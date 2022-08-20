# 1: 前書きと著者自己紹介
- このファイルでは、USI-Xエンジンを作る方法を解説します。(まだ書きかけ)
- https://github.com/YuaHyodo/USI-X_Othello_Samples にあるサンプルプログラム、
snail_reversi( https://github.com/YuaHyodo/snail_reversi )を使う予定です。
- この文章に書いてある事が原因で損害が生じても著者は責任を負いません。

# 2: 目次
- 1: 前書き
- 2: 目次
- 3: 概要・導入
- 4: USI-Xの利点
- 5: サンプルコードを使って最初の1歩を踏み出してみよう
- 6: 5章のエンジンを少し発展させる
- 7: その先へ

# 3: 概要・導入
- USI-X(正式名称: USI-X Protocol Othello Version)は、オセロAI(リバーシAI)のための通信プロトコルです。(USI-Xについての詳細はReadmeファイル、仕様書を確認してください)
- 以降、USI-Xに対応したオセロAIの事をUSI-Xエンジンと呼び、対応する利点や作り方、改良のヒントなどについて書きます。

# 4: USI-Xの利点
- 1: USIプロトコルという、将棋AIにおいて広く普及しているプロトコルをベースに置いているため、実用性が保証されている。
- 2: USIプロトコル対応のツールの再利用が簡単。
- 3: GUI, 互換性のあるオンライン用プロトコルと変換ツール, サーバープログラム, サンプルコード, 完成済みのエンジンが既に複数存在する。
- 4: 現在も関連ツールの開発が進んでおり、将来性もある。
- 5: シンプルでスマート、分かりやすくて対応も簡単。サンプルコードやドキュメントの充実にも力を入れている。

# 5: サンプルコードを使って最初の1歩を踏み出してみよう
- こちらのサンプルコード( https://github.com/YuaHyodo/USI-X_Othello_Samples/blob/main/USI_X_Engine_base_v1.py )を使ってシンプルなエンジンを作ってみます。
- 完成品はこちら: https://github.com/YuaHyodo/USI-X_Othello_Samples/blob/main/Step1.py
## 5-1: 解説
```
from snail_reversi.Board import BLACK, WHITE, DRAW
from snail_reversi.Board import Board

from USI_X_Engine_base_v1 import USIX_Engine_base as base
import random
```
- この部分では、関連ツールの読み込みをしています。
- snail_reversiはオセロのルール関連の部分担当、randomはランダム関係の部分担当、そしてUSI_X_Engine_base_v1はUSI-Xエンジンの土台です。
- snail_reversiは、リポジトリに書いてある方法で皆さんのPCにインストールする必要があります。( 詳細: https://github.com/YuaHyodo/snail_reversi )
<br>

```
class Player(base):
    def __init__(self):
        super().__init__()
        self.name = ''#エンジンの名前(なんでもいい)
        self.auther = ''#開発者の名前(なんでもいい)
```
- この部分では、Playerクラスを作り、初期化しています。
- self.nameにはAIの名前、self.autherには開発者の名前を入れましょう。(適当でいいです)
<br>

```
    def set_board(self):
        #この関数では、エンジンの脳内盤をセットする
        self.board = Board()
        if self.position_info['sfen'] != None:
            self.board.set_sfen(self.position_info['sfen'])
        for m in self.position_info['moves']:
            self.board.move_from_usix(m)
        return
```
- この部分では、エンジンの脳内盤(的なもの)をセットしています。
- 土台のUSI_X_Engine_base_v1が、self.position_infoに必要な情報を入れてくれるので、それを使ってself.boardをセットします。
<br>

```
    def think(self):
        #この関数で思考する
        #ここを改造するだけで自分好みのAIが作れて、関連ツールの恩恵も受けられる。
        legal_moves = self.board.gen_legal_moves()#打てる場所のlistを受け取る
        bestmove = random.choice(legal_moves)#random.choiceで、ランダムに1つ手を選ぶ。
        return bestmove, None#2番目はPonderという機能だが、これは使わない。使わないときはNoneにする
```
- ここがエンジンの思考を行う部分です。
- このコードでは、self.boardの中にあるgen_legal_moves関数を呼び出して合法手(legal_moves, ルールに沿った手)のlistを取得したあと、
random.choice(legal_moves)でランダムに1つ合法手を選んでいます。
- その後に、　return bestmove, None　で、選んだ手を土台に知らせています。
<br>

## 5-2: 強さ
- 適当に手を選んでいるので、強くないです。
- しかし、このコードを理解すれば自分好みのエンジンが作れます。
- 次章ではこれをベースに少し発展したものを作ります。(自分好みのエンジンを作る参考になると思います)


# 6: 5章のエンジンを少し発展させる
- この章では、著者がオセロに関して知っているほぼ唯一の知識である「角は強い」を教えてやることで、5章のエンジンを強化します。
- 完成品はこちら: https://github.com/YuaHyodo/USI-X_Othello_Samples/blob/main/Step2.py
## 6-1: 解説
- 5章との変更点をサクッと解説します。
```
    def think(self):
        #この関数で思考する
        kado = ['a1', 'a8', 'h1', 'h8']#角のマス
        legal_moves = self.board.gen_legal_moves()#打てる場所のlistを受け取る
        for sq in kado:
            if sq in legal_moves:#もし、角のマスに打てるなら
                return sq, None#その手を打つ
        #ない場合
        bestmove = random.choice(legal_moves)#random.choiceで、ランダムに1つ手を選ぶ。
        return bestmove, None#2番目はPonderという機能だが、これは使わない。使わないときはNoneにする
```
- kado = ['a1'...]で、角のマスを定義しています。
- if sq in legal_moves:で、角のマスに打てるかを確認しています。打てる場合は、角に打ちます。
- 打てない場合は今まで通りランダムに打ちます。

## 6-2: 強さ
- 6章のエンジンと5章のエンジンを黒白入れ替えて合計1000戦させて調べてみると、6章のエンジンから見て733勝224敗43分けでした。(黒番での戦績:354勝124敗22分け, 白番での戦績379勝100敗21分け)
( こちらのツールを使用: https://github.com/YuaHyodo/python-dlothello/blob/main/pydlothello/engine_invoker.py )
- 簡単な改良だったのに、結構強くなりました。
- 次章では、さらなる強さを追い求める人のための有用な情報を紹介します。

# 7: その先へ
