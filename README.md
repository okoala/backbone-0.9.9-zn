backbone 中文翻译文档
=================

#extend
Backbone.Model.extend(properties, [classProperties]) 

要创建自己的 **模型** 类，你可以扩展 Backbone.Model 并提供实例 **属性** ， 以及可选的可以直接注册到构造函数的 **类属性** (classProperties)。

**extend** 可以正确的设置原型链，因此通过 **extend** 创建的子类 (subclasses) 也可以被深度扩展。

	var Note = Backbone.Model.extend({
	  	initialize: function() { ... },
		author: function() { ... },
		coordinates: function() { ... },
		allowedToEdit: function(account) {
		 	return true;
		}
	});
	
	var PrivateNote = Note.extend({
	  	allowedToEdit: function(account) {
		  	return account.owns(this);
		 }
	});

**父类** 的简述：Javascript没有提供一种直接调用父类的方式， 如果你要重载原型链中上层定义的同名函数，如 `set`，或 `save` ， 并且你想调用父对象的实现，这时需要明确的调用它，类似这样：
