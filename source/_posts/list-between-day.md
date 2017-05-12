---
title: 列出两个日期之间的所有天
date: 2017-05-12 14:03:57
tags: 个人整理
categories: 个人整理
---

```
/**
  * 列出两个日期之间所有的天
  * @param  {[type]} a [description]
  * @param  {[type]} b [description]
  * @return {[Array]}   [description] ['03/16', '03/17']
  */
 function listDiffDay(start, end) {
    if (!start || !end) return;
    var resultData = [];
    // 日期格式必须是YYYY-MM-DD
    var regExp = /((((1[6-9]|[2-9]\d)\d{2})-(1[02]|0?[13578])-([12]\d|3[01]|0?[1-9]))|(((1[6-9]|[2-9]\d)\d{2})-(1[012]|0?[13456789])-([12]\d|30|0?[1-9]))|(((1[6-9]|[2-9]\d)\d{2})-0?2-(1\d|2[0-8]|0?[1-9]))|(((1[6-9]|[2-9]\d)(0[48]|[2468][048]|[13579][26])|((16|[2468][048]|[3579][26])00))-0?2-29-))/
    // console.log(regExp.test(start))
    if (regExp.test(start) && regExp.test(end)) {
    	var s = start.split("-");
    	var e = end.split("-");
    	var d = new Date();
    	
    	var i = 0;
    	var oneDay = 24 * 60 * 60 * 1000;
    	// 设置开始的日期
    	d.setDate(s[2])
    	d.setMonth(s[1] - 1);
    	d.setFullYear(s[0]);
    	while (i == 0) {
    		var c = d.getTime() + oneDay; // 增加一天的时间数
    		d.setTime(c);
    		var this_date = d.getDate();
    		var this_month = d.getMonth() + 1;
    		var this_year = d.getFullYear();
    		this_date = this_date < 10 ? ('0'+ this_date): this_date;
    		this_month = this_month < 10? ('0'+ this_month): this_month;
    		resultData.push(this_month +'/'+ this_date);
    		if (this_year == e[0] && this_month == e[1] && this_date == e[2]) {
    			i = 1;
    		}
    	}
    }
    return resultData;
 }

 调用 `console.log(listDiffDay('2017-02-20', 2017-03-10'))` 
 ```