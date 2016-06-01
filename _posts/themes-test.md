---
title: themes-test
date: 2016-05-08 13:04:31
description: 测试主题highlight
categories: 
- theme
- test
tags:
- theme
- test
---

## ActionScript

``` actionscript
package org.example.dummy {
    import org.dummy.*;

    /*define package inline interface*/
    public interface IFooBarzable {
        public function foo(... pairs):Array;
    }

    public class FooBar implements IFooBarzable {
        static private var cnt:uint = 0;
        private var bar:String;

        //constructor
        public function TestBar(bar:String):void {
            bar = bar;
            ++cnt;
        }

        public function foo(... pairs):Array {
            pairs.push(bar);
            return pairs;
        }
    }
}
```



{% codeblock Javascript Array Syntax lang:js http://j.mp/pPUUmW MDN Documentation %}
var arr1 = new Array(arrayLength);
var arr2 = new Array(element0, element1, ..., elementN);
{% endcodeblock %}
