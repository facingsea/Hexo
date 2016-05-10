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
