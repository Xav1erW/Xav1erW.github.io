---
layout: page      # 必须
title: Web Tool  # 必须，页面名称
description: 常用哈希工具       # 页面二级标题，描述性文字
comments: false     # 禁用评论，可选，默认开启
reward: false       # 禁用打赏，可选，默认开启
---

<style>
body {
font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
font-size: 14px;
line-height: 20px;
}
#raw-input {
display: flex;
flex-direction: column;
align-items: center;
}
.output {
width: 50%;
overflow: auto;
margin-bottom: 20px;
}
.output>span:nth-child(1) {
font-weight: bold;
}
</style>



<div>
<div id="raw-input">
<h1>Tool</h1>
<label for="">原始输入
<input type="text" id="input" value="">
</label>
</div>
<br>
<div id="output" style="display:flex; flex-direction: column; align-items: center;">
<h2>输出</h2>
<p class="output">
<span>md5</span>
<span id="md5"></span>
</p>
<p class="output">
<span>sha1</span>
<span id="sha1"></span>
</p>
<p class="output">
<span>sha256</span>
<span id="sha256"></span>
</p>
<p class="output">
<span>base64</span>
<span id="base64"></span>
</p>
</div>
</div>



<script src="./crypto-js.min.js"></script>
<script>
    var input = document.getElementById('input');
    var output = document.getElementById('output');
    var md5 = document.getElementById('md5');
    var sha1 = document.getElementById('sha1');
    var sha256 = document.getElementById('sha256');
    var base64 = document.getElementById('base64');
    input.addEventListener('input', function (e) {
        var value = e.target.value;
        md5.innerText = md5_encode(value);
        sha1.innerText = sha1_encode(value);
        sha256.innerText = sha256_encode(value);
        base64.innerText = base64_encode(value);
    });
    function md5_encode(value) {
        return CryptoJS.MD5(value).toString();
    }
    function sha1_encode(value) {
        return CryptoJS.SHA1(value).toString();
    }    
    function sha256_encode(value) {
        return CryptoJS.SHA256(value).toString();
    }   
    function base64_encode(value) {
        return CryptoJS.enc.Base64.stringify(CryptoJS.enc.Utf8.parse(value));
    }
</script>