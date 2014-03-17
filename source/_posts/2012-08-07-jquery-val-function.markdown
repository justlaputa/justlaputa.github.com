---
layout: post
title: "JQuery .val() function"
date: 2012-08-07
comments: true
categories: [javasctipt]
---

# JQuery 的 .val() 函数

最近开始学习[JQuery](http://jquery.com/)，用的过程中发现 [.val()](http://api.jquery.com/val/) 函数有些奇怪，于是研究了一下，收获不少。


<!--more-->

## .val() API

根据 JQuery [api文档](http://api.jquery.com) 的描述，.val() 函数有两种用法，分别用来获取或设置元素的值，这里只介绍获取值的方法。
 
文档里面说 .val 主要是用于获取元素的value，比如 [input](http://www.w3schools.com/tags/tag_input.asp), [select](http://www.w3schools.com/tags/tag_select.asp) 和 [textarea](http://www.w3schools.com/tags/tag_textarea.asp)等，首先第一个问题，"什么是元素的**value**?"

用 _select_ 标签为例，如下的代码：

    <select id="choise">
        <option>One</option>
        <option>Two</option>
        <option>Three</option>
        <option>Four</option>
    </select>
    <p id="result"></p>
    
    $('#choise').click(function(event) {
        $('#result').html($(this).val());
    });

可以在[jsfiddle](http://jsfiddle.net/)上运行[这段代码](http://jsfiddle.net/justlaputa/NGzDD/)试试。这段代码跟 jquery 官方文档上所给的例子一样，可以看到在 _select_ 标签所表示的对象上调用 **.val()** 方法，得到的是被选中的 _option_ 的值，所以在选择的时候，下面显示的是相应的option中的值，那我们看[这段代码](http://jsfiddle.net/justlaputa/NGzDD/13/):

    <select id="choise">
      <option value="1">One</option>
      <option value="2">Two</option>
      <option value="3">Three</option>
      <option value="4">Four</option>
    </select>

    <p id="result"></p>
    
    $('#choise').click(function(event) {
        $('#result').html($(this).val());
    });
    
可以看到，选中后显示的值变成了`1, 2, 3, 4`，那么什么是 _option_ 的 **value** 呢？从这两段代码就能看出来 _option_ 真正的 **value** 应该是其 value 属性中的值，而不是 _option_ 标签中间所包含的内容，这段内容实际上是 _option_ 对象的 **text** 值，将如上的 javascript 代码[换成如下](http://jsfiddle.net/justlaputa/NGzDD/17/)就能得到跟之前一样的效果了。

    $('#choise').click(function(event) {
      $('#result').html($('#choise option:selected').text());
    });
    
实际上，在 [w3school](www.w3schools.com) 的[文档](http://www.w3schools.com/tags/att_option_value.asp)中对 _option_ 的 **value** 属性的做了如下定义：

  > The value attribute specifies the value to be sent to a server when a form is submitted.
  
  > The content between the opening \<option\> and closing \</option\> tags is what the browsers will display in a drop-down list. However, the value of the value attribute is what will be sent to the server when a form is submitted.

  > **Note:** If the value attribute is not specified, the content will be passed as the value instead.

  这一点对于所有可以拥有 **value** [属性](http://www.w3.org/TR/html4/index/attributes.html)的标签都是相同的，即在对一个元素调用 **.val()** 函数时，首先取其 **value** 属性的值，如果没有的话，再使用其 **text** 值。
  
## JQuery 代码
知道了这个还不够，我们可以看看 [JQuery](http://jquery.com/) 是如何处理元素的value的，即如何实现 .val() 函数。

在 [jquery-1.7.2](http://code.jquery.com/jquery-1.7.2.js) 中对 .val() 的实现代码只有两段，如下：

    val: function( value ) {
    	var hooks, ret, isFunction,
    		elem = this[0];

    	if ( !arguments.length ) {
    		if ( elem ) {
    			hooks = jQuery.valHooks[ elem.type ] ||
    			 jQuery.valHooks[ elem.nodeName.toLowerCase() ];
    
    			if ( hooks && "get" in hooks && (ret = hooks.get( elem, "value" )) !== undefined ) {
    				return ret;
    			}
    
    			ret = elem.value;

    			return typeof ret === "string" ?
    				// handle most common string cases
    				ret.replace(rreturn, "") :
    				// handle cases where value is null/undef or number
    				ret == null ? "" : ret;
    		}

    		return;
    	}

    	isFunction = jQuery.isFunction( value );

    	return this.each(function( i ) {
    		var self = jQuery(this), val;

    		if ( this.nodeType !== 1 ) {
    			return;
    		}

    		if ( isFunction ) {
    			val = value.call( this, i, self.val() );
    		} else {
    			val = value;
    		}

    		// Treat null/undefined as ""; convert numbers to string
    		if ( val == null ) {
    			val = "";
    		} else if ( typeof val === "number" ) {
    			val += "";
    		} else if ( jQuery.isArray( val ) ) {
    			val = jQuery.map(val, function ( value ) {
    				return value == null ? "" : value + "";
    			});
    		}

    		hooks = jQuery.valHooks[ this.type ] || jQuery.valHooks[ this.nodeName.toLowerCase() ];

    		// If set returns undefined, fall back to normal setting
    		if ( !hooks || !("set" in hooks) || hooks.set( this, val, "value" ) === undefined ) {
    			this.value = val;
    		}
    	});
    }

    jQuery.extend({
    valHooks: {
    	option: {
    		get: function( elem ) {
    			// attributes.value is undefined in Blackberry 4.7 but
    			// uses .value. See #6932
    			var val = elem.attributes.value;
    			return !val || val.specified ? elem.value : elem.text;
    		}
    	},
    	select: {
    		get: function( elem ) {
    			var value, i, max, option,
    				index = elem.selectedIndex,
    				values = [],
    				options = elem.options,
    				one = elem.type === "select-one";

    			// Nothing was selected
    			if ( index < 0 ) {
    				return null;
    			}

    			// Loop through all the selected options
    			i = one ? index : 0;
    			max = one ? index + 1 : options.length;
    			for ( ; i < max; i++ ) {
    				option = options[ i ];

    				// Don't return options that are disabled or in a disabled optgroup
    				if ( option.selected && (jQuery.support.optDisabled ? !option.disabled : option.getAttribute("disabled") === null) &&
    						(!option.parentNode.disabled || !jQuery.nodeName( option.parentNode, "optgroup" )) ) {

    					// Get the specific value for the option
    					value = jQuery( option ).val();

    					// We don't need an array for one selects
    					if ( one ) {
    						return value;
    					}

    					// Multi-Selects return an array
    					values.push( value );
    				}
    			}

    			// Fixes Bug #2551 -- select.val() broken in IE after form.reset()
    			if ( one && !values.length && options.length ) {
    				return jQuery( options[ index ] ).val();
    			}

    			return values;
    		},

    		set: function( elem, value ) {
    			var values = jQuery.makeArray( value );

    			jQuery(elem).find("option").each(function() {
    				this.selected = jQuery.inArray( jQuery(this).val(), values ) >= 0;
    			});

    			if ( !values.length ) {
    				elem.selectedIndex = -1;
    			}
    			return values;
    		}
    	}
    },

可以看到 valHooks 对 _select_ 和 _option_ 标签做了处理，对于 **select** 对象，在使用 **.val()** 函数时调用 **get** 方法，找到被选中(selected)的option，并返回 option.val()，在使用 **.val(value)** 函数时，调用 **set** 方法，找到 option 中值与 _value_ 相同的，并设置其为 selected. 对 _option_ 标签的 hook 也可以同样分析。
