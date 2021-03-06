<!--top-->
# 5分で分かるVS Code拡張開発

-----
## 今作っている拡張機能のデモ

-----
## Whimsy

- [https://github.com/kubosho/whimsy](https://github.com/kubosho/whimsy)
- [vimのcolorschemeで勝手にドキドキするvimrc \- DRYな備忘録](http://otiai10.hatenablog.com/entry/2017/05/11/134057)
- 面白そうだし、ポモドーロっぽくなりそうなのでVS Codeで実現したかった

-----
## 作るのに必要なもの

- `npm i -g yo generator-code`
- VS Code

-----
## 注意点

- `registerCommand()` の第一引数に書いた文字列をpackage.jsonにも書く

-----
<!--code-->
```javascript
export function activate(context: ExtensionContext) {
  const activate = commands.registerCommand('extension.activate', () => {
    console.log('hello, world');
  });
}
```

-----
<!--code-->
```json
"contributes": {
    "commands": [
        {
            "command": "extension.activate",
            "title": "Hello world: activate"
        }
    ]
}
```

-----
## 注意点

- extension.ts内では `activate()` を必ずexportしないといけない
- `deactivate()` もなるべくexportしておいたほうがいい
  - 非同期でクリーンアップを実行する場合はPromiseを返す

-----
## 感想

- 検索が面倒
  - ドキュメントだけを見た場合は目的のページにたどり着きづらい
  - ドキュメント自体にページ内検索がない
  - site:https://code.visualstudio.com/docs/extensionAPI/ <検索語>
  - 実現したい機能を実装している拡張機能のコードを読んだほうが早い気がする

-----
## でも楽しいならOKです

- [元ネタ](http://dic.nicovideo.jp/a/%E3%81%A7%E3%82%82%E5%B9%B8%E3%81%9B%E3%81%AA%E3%82%89ok%E3%81%A7%E3%81%99)

