<template>
  <AngularInVue :controller-code="controllerCode">
    <link
      rel="stylesheet"
      href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    />
    <div v-html="htmlContent"></div>
  </AngularInVue>
</template>

<script setup>
import { ref } from 'vue';
import AngularInVue from './AngularinVue.vue';

const htmlContent = ref(`
  <div ng-app="myApp" ng-controller="MainController">
    <button class="btn btn-primary" ng-click="logMessage()">Click me</button>
    
    <button type="button" class="btn btn-default" ng-click="toggleCollapse()">Toggle collapse Vertically</button>
	<hr>
	<div uib-collapse="isNavCollapsed">
		<div class="well well-lg">Some content</div>
	</div>
  </div>
`);

const controllerCode = ref(`
  (function() {
    var app = angular.module('myApp', ['ui.bootstrap']);

    app.controller('MainController', function($scope) {
      $scope.logMessage = function() {
        console.log('Button clicked!');
      };
      $scope.isNavCollapsed = true;
      $scope.toggleCollapse = function() {

        console.log("Toggling collapse", $scope.isNavCollapsed);
        $scope.isNavCollapsed = !$scope.isNavCollapsed;
      }
    });
    angular.bootstrap(document.querySelector('angular-preview').shadowRoot.querySelector('slot').assignedNodes()[0], ['myApp']);
  })();
`);
</script>
