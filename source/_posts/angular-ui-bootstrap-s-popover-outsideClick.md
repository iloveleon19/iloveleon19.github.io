---
title: angular-ui-bootstrap 的 popover 的 outsideClick 觸發器
date: 2018-12-22 05:36:36
tags:
---

 angular-ui-bootstrap 在 1.0 版本之前是沒有  outsideClick 觸發器的

 所以要自己寫一個
<!--more-->
javascript 註冊一個全局自定義指令
```javascript
.directive('clickOutside', function ($parse, $timeout) {
   return {
       link: function (scope, element, attrs) {
           function handler(event) {
               if(!$(event.target).closest(element).length) {
                   scope.$apply(function () {
                       $parse(attrs.clickOutside)(scope);
                   });
               }
           }
           $timeout(function () {
               // Timeout is to prevent the click handler from immediately
               // firing upon opening the popover.
               $(document).on("click", handler);
           });
           scope.$on("$destroy", function () {
               $(document).off("click", handler);
           });
       }
   }
})
```

阻止事件冒泡
```javascript
$scope.onCancelClickOutside = function($event) {
   $event.stopPropagation(); // 避免按下分頁時觸發 clickOutside
}
```
cs 自訂 popover 的大小
```css
.popover-md {
   max-width:600px;
   width:600px;
}
```

html 部分
```html
<li ng-if="isCs" click-outside="closePopover()"> <!--看有無系統通知-->
   <button
       class="btn btn-link toolbar-message"
       style="color:white; font-weight:bold; text-decoration: none; padding-top:13px; padding-bottom:13px;"
       uib-popover-template="'app/partials/index/notify-warning.html'"
       popover-placement="bottom"
       popover-trigger="none"
       popover-class="popover-md"
       popover-is-open="getTogglePopover()"
       ng-click="setTogglePopover()">
       <div ng-class="{true: 'toolbar-has-message'}[getAlarmCount() > 0]">
           <span class="glyphicon glyphicon-info-sign"></span>&nbsp;
           系統通知
           <span class="badge" style="background-color: rgb(221,44,0);">{{ getAlarmCount() }}</span>
       </div>
   </button>
</li>
```

當 popover 包含分頁時會事件冒泡，所以要 onCancelClickOutside
```html
<ul uib-pagination
   total-items="totalItem"
   ng-model="currentPages"
   max-size="maxSize"
   ng-change="alarmPageChanged(currentPages)"
   ng-click="onCancelClickOutside($event)"
   items-per-page="perPageRows"
   class="pagination-sm"
   boundary-link-numbers="false"
   direction-links = "true"
   rotate="false"
   previous-text="上一頁"
   next-text="下一頁"
   info-text="PageInfo"
   style="margin: 0px; padding-right:15px;">
</ul>
```

