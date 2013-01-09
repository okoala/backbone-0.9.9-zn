Backbone 中文翻译文档
=================

#Backbone.Model

**模型**(Models) 是所有 Javascript 应用程序的核心，包括数据交互及与其相关的大量逻辑： 转换、验证、计算属性和访问控制。你可以用特定的方法扩展 **Backbone.Model**，Model 也提供了一组基本的管理变化的功能。

下面的示例演示了如何定义一个模型，包括自定义方法、设置属性、以及触发该属性变化的事件。一旦运行此代码后，`sidebar`在浏览器的控制台就可用，这样你就可以充分发挥了。

	var Sidebar = Backbone.Model.extend({
		 promptColor: function() {
			var cssColor = prompt("Please enter a CSS color:");
			this.set({color: cssColor});
		 }
	});
	
	window.sidebar = new Sidebar;
	
	sidebar.on('change:color', function(model, color) {
		  $('#sidebar').css({background: color});
	});
	
	sidebar.set({color: 'white'});
	
	sidebar.promptColor();

##extend
Backbone.Model.extend(properties, [classProperties]) 

要创建自己的 **Model** 类，你可以扩展 `Backbone.Model` 并提供实例 **属性**(properties) ， 以及可选的可以直接注册到构造函数的 **类属性** (classProperties)。

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

	var Note = Backbone.Model.extend({
	  	set: function(attributes, options) {
	    		Backbone.Model.prototype.set.apply(this, arguments);
	   		...
	  	}
	});

##constructor / initialize
new Model([attributes], [options])

当创建model实例时，可以传入 **属性** 初始值，这些值会被 `set` 到model。 如果定义了 **initialize** 函数，该函数会在model创建后执行。

	new Book({
	  	title: "One Thousand and One Nights",
	 	author: "Scheherazade"
	});

在极少数的情况下，你可能需要去重写 **constructor** ，这也是被允许的。

如果你传入`{collection: ...}`，这个参数表示这个model属于哪个collection，且用于计算这个model的`url`。否则`model.collection` 这个属性会在你第一次添加model到一个collection的时候被自动添加。


##get
model.set(attributes, [options])

从当前model中获取当前属性值，比如：note.get("title")
















