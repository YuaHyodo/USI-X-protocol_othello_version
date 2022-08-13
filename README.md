# USI-X-protocol_othello_version

# 概要
- ここは、やねうらお氏(世界トップクラスの将棋AI、やねうら王の開発者)が提案するUSI-Xプロトコルをオセロに対応させたものに関する文書などを置いておくリポジトリである。
- かつてはUOIプロトコルという類似のプロトコルに関するリポジトリだったが、変更された。

# 説明
- USI-Xプロトコルはやねうらお氏が提案する、将棋AIで普及しているUSIプロトコルをベースにした「対局ゲーム標準通信プロトコル」である。
- このリポジトリでは、USI-Xプロトコルの問題点の改善と、オセロに対応させる際の詳細な仕様を定義を行っている。

# 利点
- 将棋AIなどで使われているコードを利用するのが簡単。
- Online Othello Protocolというオンライン対局用プロトコルとの変換ツールも存在するため、USI-Xプロトコルに対応していればオンライン対局も行える。
- 様々な関連ツールが既に開発済みで、将来性がある。

# 今後の予定
- プロトコルと関連ツールの更なる改良を行う。(可能な限り続ける)
- プロトコルの本格的な普及を行う。(目標: 2022年9月末までに開始)
- Online Othello Protocolでオセロ版floodgateを開発し公開する。(目標: 2022年末まで)
- Online Othello Protocolで大会を開催する。(目標: 2022年末まで)

# その他
- 意見・提案などは常時募集中である。気軽に送っていただきたい。

# 関連リポジトリ
- USI-Xプロトコル(オセロ版)に関するサンプルプログラムの置き場: https://github.com/YuaHyodo/USI-X_Othello_Samples

- オンライン対局用プロトコル、Online Othello Protocolのリポジトリ: https://github.com/YuaHyodo/online_othello_protocol

- Online Othello Protocol対応の簡易対局サーバー、Ari-Othello-Serverのリポジトリ: https://github.com/YuaHyodo/Ari-Othello-Server

- USI-XエンジンとOnline Othello Protocolサーバーを中継するGUIを持ったツール、Azisai_Othello_GUIのリポジトリ: https://github.com/YuaHyodo/Azisai_Othello_GUI

- USI-Xエンジンであるpython-dlothello、Azisai、random_kunとその関連物のリポジトリ: https://github.com/YuaHyodo/python-dlothello

- sfenをグラフィカルに表示してくれるツール、Othello sfen viewerのリポジトリ: https://github.com/YuaHyodo/Othello_sfen_viewer

- USI-XエンジンをPythonプログラムから呼び出すためのツール: https://github.com/YuaHyodo/USI_X_Engine_Bridge

# 関連リンク
- USI-Xプロトコルを提案している、やねうらお氏のブログ記事: http://yaneuraou.yaneu.com/2022/06/07/standard-communication-protocol-for-games/
- USIプロトコルの仕様が書いてあるページ: http://shogidokoro.starfree.jp/usi.html
