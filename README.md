# React DRF SimpleJWT App

TL;DR: Django, DRF, DRF SimpleJWT with React Frontend sample.

The purpose of this is to get you started with a secure React-Django project.
No need to implement authentication+authorization on your frontend when it's
already done for you. You can start by pressing "Use this template"; it's not
a fork but a new repository with a fresh initial commit with all the code from
here.

Test user: `test` and pw `test`.


## Demo

django 的 `settings.py` 中对于 access_token 设置为 10 seconds 有效期, refresh_token 是 30 秒有效期.

相应的, django 的 log 则会在每 10 seconds 会出现一次 token 过期导致的报错和重连, 如下:

```
[20/Jun/2022 01:31:50] "POST /api/token/both/ HTTP/1.1" 200 438
Unauthorized: /api/ping/
[20/Jun/2022 01:31:53] "GET /api/ping/?id=PONG HTTP/1.1" 401 58
[20/Jun/2022 01:31:53] "POST /api/token/access/ HTTP/1.1" 200 218
[20/Jun/2022 01:31:53] "GET /api/ping/?id=PONG HTTP/1.1" 200 13
[20/Jun/2022 01:31:55] "GET /api/ping/?id=PONG HTTP/1.1" 200 13
[20/Jun/2022 01:31:57] "GET /api/ping/?id=PONG HTTP/1.1" 200 13
[20/Jun/2022 01:31:58] "GET /api/ping/?id=PONG HTTP/1.1" 200 13
[20/Jun/2022 01:31:59] "GET /api/ping/?id=PONG HTTP/1.1" 200 13
[20/Jun/2022 01:32:01] "GET /api/ping/?id=PONG HTTP/1.1" 200 13
[20/Jun/2022 01:32:02] "GET /api/ping/?id=PONG HTTP/1.1" 200 13
Unauthorized: /api/ping/
[20/Jun/2022 01:32:03] "GET /api/ping/?id=PONG HTTP/1.1" 401 185
[20/Jun/2022 01:32:03] "POST /api/token/access/ HTTP/1.1" 200 218
[20/Jun/2022 01:32:03] "GET /api/ping/?id=PONG HTTP/1.1" 200 13





Unauthorized: /api/ping/
[20/Jun/2022 01:32:15] "GET /api/ping/?id=PONG HTTP/1.1" 401 183
[20/Jun/2022 01:32:15] "POST /api/token/access/ HTTP/1.1" 200 218
[20/Jun/2022 01:32:15] "GET /api/ping/?id=PONG HTTP/1.1" 200 13
[20/Jun/2022 01:32:17] "GET /api/ping/?id=PONG HTTP/1.1" 200 13
[20/Jun/2022 01:32:18] "GET /api/ping/?id=PONG HTTP/1.1" 200 13
[20/Jun/2022 01:32:19] "GET /api/ping/?id=PONG HTTP/1.1" 200 13
[20/Jun/2022 01:32:19] "GET /api/ping/?id=PONG HTTP/1.1" 200 13

```


### 通过 refresh_token 更新 access_token 的处理方式

```
const errorInterceptor = (error) => {
  console.log('error: ', JSON.stringify(error))
  const originalRequest = error.config;
  const status = error.response.status;
  if (isCorrectRefreshError(status)) {
    return refreshToken().then((data)=> {
      const headerAuthorization = `Bearer ${window.localStorage.getItem(ACCESS_TOKEN)}`;
      authRequest.defaults.headers['Authorization'] = headerAuthorization;
      originalRequest.headers['Authorization'] = 10;
      return authRequest(originalRequest) // 更换了 access_token 后, 重新发 request
    }).catch((error)=> {
      // if token refresh fails, logout the user to avoid potential security risks.
      logoutUser();
      return Promise.reject(error)
    })
  }
  return Promise.reject(error)
}
```




----
# Folked original repository


---
### Example repositories

- Android: [Andrew-Chen-Wang/mobile-auth-example](https://github.com/Andrew-Chen-Wang/mobile-auth-example)
- iOS: [Andrew-Chen-Wang/mobile-auth-example](https://github.com/Andrew-Chen-Wang/mobile-auth-example)
- React: [SimpleJWT/drf-SimpleJWT-React](https://github.com/SimpleJWT/drf-SimpleJWT-React)

---
### Introduction

This repository is an example of using React on the front end comminicating with Django, Django Rest Framework and DRF SimpleJWT applications.

---
### Usage

#### Backend (Django) Instructions.


1. `cd server` to get your terminal/cmd into the server directory.
2. To run the server, create a virtual environment `virtualenv venv && source venv/bin/activate`, install packages `pip install -r requirements.txt` -- the requirements.txt file is inside the server subdirectory -- and do `python manage.py migrate && python manage.py runserver`.
    - Again, make sure when you do this, you are inside the server directory on your terminal/cmd.
    - On Windows, you should do `venv\Scripts\activate` instead of `source venv/bin/activate`
3. If you're writing for an example repository, please create
a new directory labeled with the name of the framework (e.g. jwt-ios),
and add its `.gitignore`. Please use the
[github/gitignore](https://github.com/github/gitignore) repository.
Provide detailed instructions if necessary.

A default user with the username `test` and password `test` have been created.

This repository does not come with throttling, but **it is
highly recommended that you add throttling to your entire
project.** You can use a third-party package called
Django-ratelimit or DRF's internal throttling mechanism.
Django-ratelimit is more extensive -- covering Django views,
as well -- and thus more supported by SimpleJWT.

#### Frontend (jwt-react) React instructions.

1. `cd jwt-react` to get your terminal/server into the frontend (react) folder.

2. `npm install` to install all of the dependencies for the front end application.

3. `npm start` and you should be good to go, ensure that your backend is running on port `http://localhost:8000`, if you run it on another port/ip please change the `BASE_URL` in `jwt-react/src/api/auth.js`

4. Use `npm test` if you'd like to run the test which tests the api/ folder currently.


---
### License

This repository is licensed under the 
[MIT License](https://github.com/SimpleJWT/drf-SimpleJWT-server-template/blob/master/LICENSE).
