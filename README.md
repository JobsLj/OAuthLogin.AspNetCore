# OAuthLogin.AspNetCore [![Build status](https://ci.appveyor.com/api/projects/status/7oa50i7lt9fboq5m?svg=true)](https://ci.appveyor.com/project/seven1986/oauthlogin-aspnetcore) [![NuGet](https://img.shields.io/nuget/v/OAuthLogin.AspNetCore.svg)](https://www.nuget.org/packages/OAuthLogin.AspNetCore)  [![Join the chat at https://gitter.im/OAuthLogin/OAuthLogin](https://img.shields.io/gitter/room/OAuthLogin/OAuthLogin.svg?style=flat-square)](https://gitter.im/OAuthLogin/OAuthLogin?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge) [![Average time to resolve an issue](http://isitmaintained.com/badge/resolution/seven1986/OAuthLogin.AspNetCore.svg)](http://isitmaintained.com/project/seven1986/OAuthLogin.AspNetCore "Average time to resolve an issue") [![GitHub license](https://img.shields.io/badge/license-Apache%202-blue.svg)](https://raw.githubusercontent.com/seven1986/OAuthLogin/master/LICENSE)

Installation
-------------

OAuthLogin.AspNetCore is available as a NuGet package. You can install it using the NuGet Package Console window:


```
PM> Install-Package OAuthLogin.AspNetCore
```


Usage
------

��һ�����½�����ʵ���ࣺ
```csharp
    /// <summary>
    /// ������ƽ̨APP��Ϣ����ʵ����
    /// </summary>
    public class CredentialsSetting {
        public CredentialSetting QQ { get; set; }
        public CredentialSetting Wechat { get; set; }
        public CredentialSetting Weibo { get; set; }
        public CredentialSetting FaceBook { get; set; }
        public CredentialSetting KaKao { get; set; }
    }

    public class CredentialSetting {
        /// <summary>
        /// AppKey
        /// </summary>
        public string client_id { get; set; }

        /// <summary>
        /// AppSecret
        /// </summary>
        public string client_secret { get; set; }
    }
```

�ڶ���������appsettings.json�ļ�
```json
{
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    }
  },
  "Credentials": {
    "QQ": {
      "client_id": "",
      "client_secret": ""
    },
    "Wechat": {
      "client_id": "",
      "client_secret": ""
    },
    "Weibo": {
      "client_id": "",
      "client_secret": ""
    },
    "FaceBook": {
      "client_id": "",
      "client_secret": ""
    },
    "KaKao": {
      "client_id": "",
      "client_secret": ""
    }
  },
}
```

����������Startup.cs����΢����΢�š�QQ��facebook��Kakao��client_id��client_secret

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
        {
            // ..ʡ����������

            #region ������ƽ̨����
            var Credentials = Configuration.GetSection("Credentials").Get<CredentialsSetting>();
            LoginProvider.UseWeibo(Credentials.Weibo.client_id, Credentials.Weibo.client_secret);
            LoginProvider.UseQQ(Credentials.QQ.client_id, Credentials.QQ.client_secret);
            LoginProvider.UseFaceBook(Credentials.FaceBook.client_id, Credentials.FaceBook.client_secret);
            LoginProvider.UseWechat(Credentials.Wechat.client_id, Credentials.Wechat.client_secret);
            LoginProvider.UseKakao(Credentials.KaKao.client_id); 
            #endregion
        }
```

���Ĳ������Views/OAuth�ļ��У����5�����ļ���QQ.cshtml��Facebook.cshtml��Wechat.cshtml��Webo.cshtml��Facebook.cshtml


���岽�����Controllers/OAuthController.cs
```csharp
public class OAuthController : Controller
    {
        IHttpContextAccessor _contextAccessor;

        public OAuthController(IHttpContextAccessor
                  contextAccessor) { this._contextAccessor = contextAccessor; }

        public IActionResult QQ()
        {
            var res = new QQ(_contextAccessor).Authorize();

            if (res != null && res.code == 0)
            {
                return RedirectToLogin(new
                {
                    channel = "qq",
                    code = 0,
                    user = new
                    {
                        uid = res.result.Value<string>("openid"),
                        name = res.result.Value<string>("nickname"),
                        img = res.result.Value<string>("figureurl"),
                        token = res.token
                    }
                });
            }

            return View();
        }

        public IActionResult Wechat()
        {
            var res = new Wechat(_contextAccessor).Authorize();

            if (res != null && res.code == 0)
            {
                return RedirectToLogin(new
                {
                    channel = "wechat",
                    code = 0,
                    user = new
                    {
                        uid = res.result.Value<string>("uid"),
                        name = res.result.Value<string>("nickname"),
                        img = res.result.Value<string>("headimgurl"),
                        token = res.token
                    }
                });
            }

            return View();
        }

        public IActionResult Weibo()
        {
            var res = new Weibo(_contextAccessor).Authorize();

            if (res != null && res.code == 0)
            {
                return RedirectToLogin(new
                {
                    channel = "weibo",
                    code = 0,
                    user = new
                    {
                        uid = res.result.Value<string>("idstr"),
                        name = res.result.Value<string>("name"),
                        img = res.result.Value<string>("profile_image_url"),
                        token = res.token
                    }
                });
            }

            return View();
        }

        public IActionResult Facebook()
        {
            var res = new Facebook(_contextAccessor).Authorize();

            if (res != null && res.code == 0)
            {
                return RedirectToLogin(new
                {
                    channel = "facebook",
                    code = 0,
                    user = new
                    {
                        uid = res.result.Value<string>("id"),
                        name = res.result.Value<string>("name"),
                        img = res.result["picture"]["data"].Value<string>("url"),
                        token = res.token
                    }
                });
            }

            return View();
        }

        public IActionResult Kakao()
        {
            var res = new Kakao(_contextAccessor).Authorize();

            if (res != null && res.code == 0)
            {
                return RedirectToLogin(new
                {
                    channel = "kakao",
                    code = 0,
                    user = new
                    {
                        uid = res.result.Value<string>("uid"),
                        name = res.result.Value<string>("nickname"),
                        img = res.result.Value<string>("thumbnail_image"),
                        token = res.token
                    }
                });
            }

            return View();
        }

        RedirectResult RedirectToLogin(object _entity)
        {
            var OAuthResult = JsonConvert.SerializeObject(_entity);

            // ��ת��ҳ�棬union���������Ǳ������û�����
            var url = "/login?union=" + WebUtility.UrlEncode(OAuthResult);

            return Redirect(url);
        }
    }
```