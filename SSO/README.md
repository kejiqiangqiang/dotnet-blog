6.1  用户未登录时访问子站一，子站一服务器检测到用户没登录（没有本站session，因为没传过来session对应cookie），于是通知浏览器跳转到SSO服务站点，并在跳转的URL参数中带上当前页面地址，以便登录后自动跳转回本页。

6.2  SSO服务站点检测到用户没有登录，于是显示登录界面。

用户提交登录请求到服务端，服务端验证通过，创建和账号对应的用户登录凭据（token）。

然后，服务端通知浏览器把该token作为SSO服务站点的cookie存储起来，并跳转回子站一，跳回子站一的URL参数中会带上这个token。

6.3  浏览器在写SSO服务站点cookie后，跳转回子站一。

子站一服务端检测到浏览器请求的URL中带了单点登录的token，于是把这个token发到SSO服务站点验证。

SSO服务端站点拿token解密出用户账号，把账号信息中允许子站一访问的部分返回给子站一。

子站一根据返回的信息生成用户在本站的会话，把会话对应cookie写入浏览器，从而完成在本站的登入以及会话保持。之后用户访问再子站一时，都会带上这个cookie，从而保持在本站的登录状态。

6.4  用户再访问子站二。子站二服务器检测到用户没登录，于是通知浏览器跳转到SSO服务站点。

6.5  浏览器访问SSO服务站点时会带上上述6.2环节创建的token这个cookie。SSO服务站点根据该token能找到对应用户，于是通知浏览器跳转回子站二，并在跳转回去的URL参数中带上这个token。

6.6  子站二服务端检测到浏览器请求的URL中带上了单点登录的token，于是又会走上述6.3对应步骤，完成用户在本站的自动登录。