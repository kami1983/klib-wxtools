# klib-wxtools

一个服务于微信开发的类库工具包

# 安装
* `composer require klib/wxtools` 即可

# 使用
* 以Larvael 框架举例，修改 `routes/web.php`
```
... 其他代码
# 在头部引用类库
use KLib\WxTools\Crypto\WXBizMsgCrypt;

# 添加测试路由
Route::get('/klib/test', function () {

    // 第三方发送消息给公众平台
    # 创建 WXBizMsgCrypt 类所需要的信息
    $encodingAesKey = "nz47o6NxDrpf4hXPoDzXUZMCYR4hhnUBB4dKR4RuJ4u";
    $token = "JWKFKKKEQBBDKFKS";
    $appId = "wxd3e6d25996c4bc5f";

    # 浏览器发送的消息
    $timeStamp = "1409304348";
    $nonce = "xxxxxx";

    # 待解密的消息
    $text = "<xml><ToUserName><![CDATA[oia2Tj我是中文jewbmiOUlr6X-1crbLOvLw]]></ToUserName><FromUserName><![CDATA[gh_7f083739789a]]></FromUserName><CreateTime>1407743423</CreateTime><MsgType><![CDATA[video]]></MsgType><Video><MediaId><![CDATA[eYJ1MbwPRJtOvIEabaxHs7TX2D-HV71s79GUxqdUkjm6Gs2Ed1KF3ulAOA9H1xG0]]></MediaId><Title><![CDATA[testCallBackReplyVideo]]></Title><Description><![CDATA[testCallBackReplyVideo]]></Description></Video></xml>";

    $pc = new WXBizMsgCrypt($token, $encodingAesKey, $appId);
    $encryptMsg = '';
    $errCode = $pc->encryptMsg($text, $timeStamp, $nonce, $encryptMsg);
    if ($errCode == 0) {
        print("加密后: " . $encryptMsg . "\n");
    } else {
        print($errCode . "\n");
    }

    $xml_tree = new \DOMDocument();
    $xml_tree->loadXML($encryptMsg);
    $array_e = $xml_tree->getElementsByTagName('Encrypt');
    $array_s = $xml_tree->getElementsByTagName('MsgSignature');
    $encrypt = $array_e->item(0)->nodeValue;
    $msg_sign = $array_s->item(0)->nodeValue;

    $format = "<xml><ToUserName><![CDATA[toUser]]></ToUserName><Encrypt><![CDATA[%s]]></Encrypt></xml>";
    $from_xml = sprintf($format, $encrypt);


    // 第三方收到公众号平台发送的消息
    $msg = '';
    $errCode = $pc->decryptMsg($msg_sign, $timeStamp, $nonce, $from_xml, $msg);
    if ($errCode == 0) {
        print("解密后: " . $msg . "\n");
    } else {
        print("出错 ：".$errCode . "\n");
    }

});
```
* 浏览器访问： `http://测试域名/klib/test` 后查看源码，加密解密成功，之后自己配置自己的信息即可。

