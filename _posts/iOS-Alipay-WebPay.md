
layout: Alipay
title: iOS-AlipaySDK集成支付的一种方式
date: 2018-01-01 01:18:02
tags: Alipay 
---
1. 后台提供的是一个可以使用webView加载的URL, URL的返回值为一个带有订单信息的from表单, 只有是这种类型,才能沿用以下代码

2. 如果你请求的网络接口, 返回值中直接包含订单信息串, 那么可用直接传orderInfo的方法直接来做.

<!--more-->

步骤1 集成支付宝SDK

步骤2 在加载webView的界面这样写

```

- (void)viewDidLoad {
    [super viewDidLoad];
    self.view.backgroundColor = [UIColor whiteColor];
    /// 初始化webView
    _webView = [[UIWebView alloc]initWithFrame:self.view.bounds];
    /// 设置代理
    _webView.delegate = self;
    [self.view addSubview:_webView];
    /// load
    NSURLRequest *request = [[NSURLRequest alloc]initWithURL:[NSURL URLWithString:self.strUrl]];
    [_webView loadRequest:request];
    
    [[NSNotificationCenter defaultCenter]addObserver:self selector:@selector(payCancel:) name:RP_NOTIFITION_PAYCANCEL object:nil];
}
- (void)payCancel:(NSNotification*)notifi{
    [self.navigationController popViewControllerAnimated:YES];
}
#pragma mark -
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType
{
    NSString* orderInfo = [[AlipaySDK defaultService]fetchOrderInfoFromH5PayUrl:[request.URL absoluteString]];
    if (orderInfo.length > 0) {
        // 调用支付接口进行支付
        /*
         {
         isProcessUrlPay = 1;
         resultCode = 6001;
         returnUrl = "";
         }
         */
        [[AlipaySDK defaultService]payUrlOrder:orderInfo fromScheme:@"zhandaotingche" callback:^(NSDictionary* result) {
            /*
             9000    订单支付成功
             8000    正在处理中，支付结果未知（有可能已经支付成功），请查询商户订单列表中订单的支付状态
             4000    订单支付失败
             5000    重复请求
             6001    用户中途取消
             6002    网络连接出错
             6004    支付结果未知（有可能已经支付成功），请查询商户订单列表中订单的支付状态
             其它    其它支付错误
             */
            if ([[result objectForKey:@"resultCode"] isEqualToString:@"6001"]) {
                [[NSNotificationCenter defaultCenter]postNotificationName:RP_NOTIFITION_PAYCANCEL object:nil];

            }else if ([[result objectForKey:@"resultCode"] isEqualToString:@"9000"]){
                [[NSNotificationCenter defaultCenter]postNotificationName:RP_NOTIFITION_PAYSUSSCESS object:nil];
                
            }else if ([[result objectForKey:@"resultCode"] isEqualToString:@"4000"]){
                [[NSNotificationCenter defaultCenter]postNotificationName:RP_NOTIFITION_PAYFAIL object:nil];
            }
        }];
    }
    return YES;
}
```
在AppDelegate中 中补充
```
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
{
    if ([url.host isEqualToString:@"safepay"]) {
        // 支付跳转支付宝钱包进行支付，处理支付结果
        [[AlipaySDK defaultService] processOrderWithPaymentResult:url standbyCallback:^(NSDictionary *resultDic) {
            NSLog(@"result = %@",resultDic);
            
            if ([[resultDic objectForKey:@"resultCode"] isEqualToString:@"6001"]) {
                [[NSNotificationCenter defaultCenter]postNotificationName:RP_NOTIFITION_PAYCANCEL object:nil];
                
            }else if ([[resultDic objectForKey:@"resultCode"] isEqualToString:@"9000"]){
                [[NSNotificationCenter defaultCenter]postNotificationName:RP_NOTIFITION_PAYSUSSCESS object:nil];
                
            }else if ([[resultDic objectForKey:@"resultCode"] isEqualToString:@"4000"]){
                [[NSNotificationCenter defaultCenter]postNotificationName:RP_NOTIFITION_PAYFAIL object:nil];
            }
        }];
        
    }
    return YES;
}
-(BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options{
    [WXApi handleOpenURL:url delegate:self];
    if ([url.host isEqualToString:@"safepay"]) {
        // 支付跳转支付宝钱包进行支付，处理支付结果
        [[AlipaySDK defaultService] processOrderWithPaymentResult:url standbyCallback:^(NSDictionary *resultDic) {
            NSLog(@"result = %@",resultDic);
            if ([[resultDic objectForKey:@"resultCode"] isEqualToString:@"6001"]) {
                [[NSNotificationCenter defaultCenter]postNotificationName:RP_NOTIFITION_PAYCANCEL object:nil];
                
            }else if ([[resultDic objectForKey:@"resultCode"] isEqualToString:@"9000"]){
                [[NSNotificationCenter defaultCenter]postNotificationName:RP_NOTIFITION_PAYSUSSCESS object:nil];
                
            }else if ([[resultDic objectForKey:@"resultCode"] isEqualToString:@"4000"]){
                [[NSNotificationCenter defaultCenter]postNotificationName:RP_NOTIFITION_PAYFAIL object:nil];
            }
        }];
    }
    return YES;
}
```

good!

待补充~
