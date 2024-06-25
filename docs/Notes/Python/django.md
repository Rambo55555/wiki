记录一次Django的POST请求一直变成GET的原因，一开始以为是中间件，注释了不管用：

```
MIDDLEWARE = [
    "django.middleware.security.SecurityMiddleware",
    "django.contrib.sessions.middleware.SessionMiddleware",
    "django.middleware.common.CommonMiddleware",
    # #"django.middleware.csrf.CsrfViewMiddleware",
    "django.contrib.auth.middleware.AuthenticationMiddleware",
    "django.contrib.messages.middleware.MessageMiddleware",
    "django.middleware.clickjacking.XFrameOptionsMiddleware",
]
```

后面发现是url多写了一个"/"，导致重定向了，😓

```
urlpatterns = [
    path("", views.hellowWorld, name="helloWorld"),
    path("admin/", admin.site.urls),
    path("cmg/(<-就是这里)", views.generate_commit_message, name="cmg"),
    path("cmgWithHistory/", views.generate_commit_message_with_history, name="cmgWithHistory"),
]
```

