# TodoApp1

参照にあるGitHubリポジトリを参照して、再度開発し直した。しかし、**現時点ではログインページより後ろに進むことができない。**

![](screenshot.png)

# ディレクトリ構造

## Flutter側(フロントエンド)

```powershell
│  main.dart # アプリのコア部分
│  
├─bloc
│  ├─blocs
│  │      user_bloc_provider.dart #状態管理。rxdartパッケージで実装。
│  │      
│  └─resources
│          api.dart #Flaskで開発したREST APIとの連携
│          repository.dart #アプリ内で行う処理の関数をまとめたもの
│
├─models
│  │  global.dart #アプリ内で使う色に変数をつけたファイル
│  │
│  ├─authentication
│  │      authorize.dart #認可システム
│  │
│  ├─classes
│  │      task.dart #タスクのモデル
│  │      user.dart #ユーザのモデル
│  │
│  └─widgets
│          intray_todo_widget.dart #おそらく完了済みのタスクを表示するWidget
│
└─UI
    ├─Intray
    │      intray_page.dart #完了済みのタスクを表示するページ
    │
    └─Login
            loginscreen.dart #ログインページ
```

## Flask側(バックエンド)

```powershell
C:.
│  app.py
│  config.py
│  migrate.py
│  models.py
│  requirements.txt
│  run.py
│  __init__.py
│
├─migrations
│  │  alembic.ini
│  │  env.py
│  │  README
│  │  script.py.mako
│  │
│  └─versions
│          274b68063340_.py
│          b03849ed06fd_.py
│
├─resources
│  │  project.py
│  │  Register.py
│  │  Signin.py
│  │  supportfile.py
│  │  task.py
│  │  tasks
│  │  User.py
│  │  __init__.py
│  │
│  └─__pycache__
│          Register.cpython-310.pyc
│          Signin.cpython-310.pyc
│          task.cpython-310.pyc
│          __init__.cpython-310.pyc
│
└─__pycache__
        app.cpython-310.pyc
        config.cpython-310.pyc
        models.cpython-310.pyc
```

# エラーが発生している箇所

`lib/UI/Login/loginscreen.dart` 

```dart
import 'package:flutter/material.dart';
import 'package:todoapp/bloc/blocs/user_bloc_provider.dart';
import 'package:shared_preferences/shared_preferences.dart';
import 'package:todoapp/models/classes/user.dart';
import 'package:todoapp/models/global.dart';

class LoginPage extends StatefulWidget {
  final VoidCallback login;
  final bool newUser;

  const LoginPage({Key key, this.login, this.newUser}) : super(key: key);
  @override
  _LoginPageState createState() => _LoginPageState();
}

class _LoginPageState extends State<LoginPage> {
  TextEditingController emailController = new TextEditingController();
  TextEditingController usernameController = new TextEditingController();
  TextEditingController firstNameController = new TextEditingController();
  TextEditingController passwordController = new TextEditingController();
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: darkGreyColor,
      body: Center(
        child: widget.newUser ? getSignupPage() : getSigninPage(),
      ),
    );
  }

  Widget getSigninPage() {
    TextEditingController usernameText = new TextEditingController();
    TextEditingController passwordText = new TextEditingController();
    return Container(
      margin: EdgeInsets.only(top: 100, left: 20, right: 20, bottom: 100),
      child: Column(
        mainAxisAlignment: MainAxisAlignment.spaceBetween,
        children: <Widget>[
          Text("Welcome!", style: bigLightBlueTitle),
          Container(
            height: 200,
            child: Column(
              mainAxisAlignment: MainAxisAlignment.spaceAround,
              children: <Widget>[
                Theme(
                  data: Theme.of(context)
                      .copyWith(splashColor: Colors.transparent),
                  child: TextField(
                    controller: usernameText,
                    autofocus: false,
                    style: TextStyle(fontSize: 22.0, color: darkGreyColor),
                    decoration: InputDecoration(
                      filled: true,
                      fillColor: Colors.white,
                      hintText: 'Username',
                      contentPadding: const EdgeInsets.only(
                          left: 14.0, bottom: 8.0, top: 8.0),
                      focusedBorder: OutlineInputBorder(
                        borderSide: BorderSide(color: Colors.white),
                        borderRadius: BorderRadius.circular(25.7),
                      ),
                      enabledBorder: UnderlineInputBorder(
                        borderSide: BorderSide(color: Colors.white),
                        borderRadius: BorderRadius.circular(25.7),
                      ),
                    ),
                  ),
                ),
                Theme(
                  data: Theme.of(context)
                      .copyWith(splashColor: Colors.transparent),
                  child: TextField(
                    controller: passwordText,
                    autofocus: false,
                    style: TextStyle(fontSize: 22.0, color: darkGreyColor),
                    decoration: InputDecoration(
                      filled: true,
                      fillColor: Colors.white,
                      hintText: 'Password',
                      contentPadding: const EdgeInsets.only(
                          left: 14.0, bottom: 8.0, top: 8.0),
                      focusedBorder: OutlineInputBorder(
                        borderSide: BorderSide(color: Colors.white),
                        borderRadius: BorderRadius.circular(25.7),
                      ),
                      enabledBorder: UnderlineInputBorder(
                        borderSide: BorderSide(color: Colors.white),
                        borderRadius: BorderRadius.circular(25.7),
                      ),
                    ),
                  ),
                ),
                TextButton(
                  child: Text(
                    "Sign in",
                    style: redTodoTitle,
                  ),
                  onPressed: () {
                    if (usernameText.text != null ||
                        passwordText.text != null) {
                      userBloc
                          .signinUser(usernameText.text, passwordText.text, "")
                          .then((_) {
                        widget.login();
                      });
                    }
                  },
                )
              ],
            ),
          ),
          Container(
            child: Column(
              children: <Widget>[
                Text(
                  "Don't you even have an account yet?!",
                  style: redText,
                  textAlign: TextAlign.center,
                ),
                TextButton(
                    child: Text("create one", style: redBoldText),
                    onPressed: () {
                      getSignupPage();
                    }) // おそらくonPressedがうまく機能していない可能性がある
              ],
            ),
          )
        ],
      ),
    );
  }

  Widget getSignupPage() {
    return Container(
      margin: EdgeInsets.all(20),
      child: Column(
        children: <Widget>[
          TextField(
            controller: emailController,
            decoration: InputDecoration(hintText: "Email"),
          ),
          TextField(
            controller: usernameController,
            decoration: InputDecoration(hintText: "Username"),
          ),
          TextField(
            controller: firstNameController,
            decoration: InputDecoration(hintText: "First name"),
          ),
          TextField(
            controller: passwordController,
            decoration: InputDecoration(hintText: "Password"),
          ),
          TextButton(
            child: Text("Sign up for gods sake"),
            onPressed: () {
              if (usernameController.text != null ||
                  passwordController.text != null ||
                  emailController.text != null) {
                userBloc
                    .registerUser(
                        usernameController.text,
                        firstNameController.text ?? "",
                        "",
                        passwordController.text,
                        emailController.text)
                    .then((_) {
                  widget.login();
                });
              }
            },
          )
        ],
      ),
    );
  }
}

```

▼エラーが発生している部分

```dart
TextButton(
    child: Text("create one", style: redBoldText),
    onPressed: () {
        getSignupPage();
    }) // おそらくonPressedがうまく機能していない可能性がある
```

▼エラーメッセージ。`Error: XMLHttpRequest error.`と出力された

```
Error: XMLHttpRequest error.
C:/b/s/w/ir/cache/builder/src/out/host_debug/dart-sdk/lib/_internal/js_dev_runtime/private/ddc_runtime/errors.dart
299:10  createErrorWithStack
C:/b/s/w/ir/cache/builder/src/out/host_debug/dart-sdk/lib/_internal/js_dev_runtime/patch/core_patch.dart 341:28
_throw
C:/b/s/w/ir/cache/builder/src/out/host_debug/dart-sdk/lib/core/errors.dart 116:5
throwWithStackTrace
C:/b/s/w/ir/cache/builder/src/out/host_debug/dart-sdk/lib/async/zone.dart 1378:11
callback
C:/b/s/w/ir/cache/builder/src/out/host_debug/dart-sdk/lib/async/schedule_microtask.dart 40:11
_microtaskLoop
C:/b/s/w/ir/cache/builder/src/out/host_debug/dart-sdk/lib/async/schedule_microtask.dart 49:5
_startMicrotaskLoop
C:/b/s/w/ir/cache/builder/src/out/host_debug/dart-sdk/lib/_internal/js_dev_runtime/patch/async_patch.dart 166:15
<fn>
```

# 開発環境

* Windows 11
* Flask
* Flutter 3
* Visual Studio Code

# 参照

* [TodoApp - Github](https://github.com/KalleHallden/TodoApp)