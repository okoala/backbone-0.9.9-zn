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
**Backbone.Model.extend(properties, [classProperties]) **

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
**new Model([attributes], [options])**

当创建model实例时，可以传入 **属性** (attributes)初始值，这些值会被 `set` 到model。 如果定义了 **initialize** 函数，该函数会在model创建后执行。

	new Book({
	  	title: "One Thousand and One Nights",
	 	author: "Scheherazade"
	});

在极少数的情况下，你可能需要去重写 **constructor** ，这也是被允许的。

如果你传入`{collection: ...}`，这个参数表示这个model属于哪个collection，且用于计算这个model的`url`。否则`model.collection` 这个属性会在你第一次添加model到一个collection的时候被自动添加。


##get
**model.set(attributes, [options])**

从当前model中获取当前属性(attributes)值，比如：note.get("title")

##set
**model.set(attributes, [options]) **

向model设置一个或多个hash属性(attributes)。如果任何一个属性改变了model的状态，在不传入 `{silent: true}` 选项参数的情况下，会触发 `"change"` 事件，更改特定属性的事件也会触发。 可以绑定事件到某个属性，例如：change:title，及 change:content。

	note.set({title: "March 20", content: "In his eyes she eclipses..."});
	
	book.set("title", "A Scandal in Bohemia");

如果model拥有 `validate` 方法， 那么属性验证会在 set 之前执行，如果验证失败，模型不会发生变化，这时 `set` 会返回 `false`， 否则 `set` 返回一个到这个model的引用。 也可以在选项中传入 `error` 回调函数，此时验证失败时会执行它而不触发 `"error"` 事件。

传入`{silent: true}`会延迟事件，这在临时或迅速改变属性会非常有用，而且不会通过系统的其余部分进行传播。也就是说`silent`并不意味着改变(和事件)不会发生，只是说在下一个`change`前都是沉默的。

##escape
**model.escape(attribute)**

与 [get](#get) 类似，只是返回的是HTML转义后版本的model属性值。如果从model插入数据到HTML，使用 `escape` 取数据可以避免 [XSS](http://en.wikipedia.org/wiki/Cross-site_scripting) 攻击。

	var hacker = new Backbone.Model({
		name: "<script>alert('xss')</script>"
	});
	
	alert(hacker.escape('name'));

##has
**model.has(attribute)**

属性值为非 null 或非 undefined 时返回 true

	if (note.has("title")) {
		...
	}

##unset
**model.unset(attribute, [options])**

从内部属性散列表中删除指定属性(attribute)。 如果未设置 `silent` 选项，会触发 `"change"` 事件。

##clear
**model.clear([options])**

从model中删除所有属性， 包括[id](#id)属性。 如果未设置 silent 选项，会触发 "change" 事件。

##id
**model.id**

**id**是model的特殊属性，可以是任意字符串（整型 id 或 UUID）。在属性中设置的 id 会被直接拷贝到model属性上。 我们可以从集合（collections）中通过 id 获取model，另外 id 通常用于生成model的 URLs。

##idAttribute
**model.idAttribute**

一个model的唯一标示符，被储存在`id`属性下。如果使用一个不同的唯一的key直接和后端通信。可以设置Model的`idAttribute`到一个从key到`id`的一个透明映射中。

	var Meal = Backbone.Model.extend({
		idAttribute: "_id"
	});
	
	var cake = new Meal({ _id: 1, name: "Cake" });
	alert("Cake id: " + cake.id);
	
##cid
**model.cid**

model的特殊属性，cid 或客户 id 是当所有model创建时自动产生的唯一标识符。 客户 ids 在model尚未保存到服务器之前便存在，此时model可能仍不具有最终的 id， 客户 ids 的形式为：`c1, c2, c3 ...`







