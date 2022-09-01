# 1: 前書きと著者自己紹介
## 1-1: 前書き
- このファイルでは、USI-Xエンジンを作る方法を解説します。(まだ書きかけ)
- https://github.com/YuaHyodo/USI-X_Othello_Samples にあるサンプルプログラム、
snail_reversi( https://github.com/YuaHyodo/snail_reversi )を使う予定です。
- この文章に書いてある事が原因で損害が生じても著者は責任を負いません。

## 1-2: 著者自己紹介
- 名前: 兵頭優空
- 年齢(2022年8月現在): 16歳(高校1年生)
- 将棋AIやオセロAIやその関連物、GANなどの他のAIなどの開発を行っている。
- USI-Xプロトコルのために2022年の夏休みを犠牲にしている。

# 2: 目次
- 1: 前書き
- 2: 目次
- 3: 概要・導入
- 4: USI-Xの利点
- 5: サンプルコードを使って最初の1歩を踏み出してみよう
- 6: 5章のエンジンを少し発展させる
- 7: その先へ
- 8: 終わりに

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
- 第6章の内容まで履修した皆さんの中には、「これでは弱すぎる。もっと強くしたい」と思った人も多いでしょう。
- この章では、そんな「さらなる強さを追い求める人」のために、「探索」と「評価関数」という2つの大きな技術(のグループ)の初歩的な内容を解説します。
- この章では、(シンプルなやつだけど)みんなが大好きな機械学習なども出てきます。

## 7-1: 探索と評価関数の概要

## 7-1-1: 探索
- 皆さんがオセロなどのボードゲームを本気でプレイしているときのことを思い出してください。
恐らく、「ここに打ったら、相手がこう打って、その後・・・」のような感じで「先読み」をしていると思います。
- 探索はざっくり言うと"「先読み」をAIにさせる技術"という感じの技術(のグループ)のことです。
- 「探索」と言っても様々な手法がありますが、今回はMiniMax法(ミニマックス法)という古くてシンプルで強力な手法を主に解説します。
- MiniMax法で終局までのすべての変化を読むと、常に最善の手を打つ「オセロの神様」が作れると言われています。

## 7-1-2: 評価関数
- 皆さんが先読みする際の事を思い出してください。
皆さんは、序盤～中盤あたりから終局(ゲームの終わり)まで先読みしているでしょうか？ <br>
いいえ、違うでしょう。
現在のコンピューターをもってしても、終局まで完全に先読みをするのは非常に困難です。<br>
では、どうするか？<br>
恐らく、皆さんは「こっちの局面と、この局面だと、こっちの局面のほうが良いな」という感じで手を選んでいると思います。
- 「評価関数」は、この「こっちの方が良いな」というのを判断する際の「局面の良し悪し」の数値である「評価値」を計算するための技術(のグループ)です。
- (オセロ用の)評価関数と言っても色々ありますが、今回はシンプルな「マスの重み」を使います。
- 今回の「マスの重み」は機械学習で求めます。(別に人力調整でも良いんですが、機械学習はロマンなので機械学習で求めます)
- すべての局面の正確な評価値を求められる「完全な評価関数」があれば、次の1手を読むだけで常に最善の手を打つ「オセロの神様」が作れると言われています。

## 7-2: サンプルコードの解説(探索部)
- サンプルコードをサクッと解説します。
- 完成品はこちら: https://github.com/YuaHyodo/USI-X_Othello_Samples/blob/main/MiniMax_Sample.py

## 7-2-1: 全体の解説
- MiniMax法は、「自分は自分の評価値が最大になるように行動し、相手は相手の評価値が最大になるように行動する」という考えで探索を行います。
- オセロは、二人零和有限確定完全情報ゲーム(2人でプレイし、お互いの利害を足すと0になり、有限手で勝負がつき、ランダム要素がなく、ゲームに関するすべての情報を得ることができるゲーム)なので、相手の評価値は自分の評価値を反転したものになっています。この特性を利用します。
<br>

- 具体的には、

```
1: 現局面(以降、今のノード)自分の番の場合は、
    次の1手で到達可能な局面(以降、子ノード)の中でもっとも高い評価値を今のノードの評価値にする
    
2: 今のノードが相手の番の場合は、子ノードの中で最も価値が低い評価値を今のノードの評価値にする
    
3: 今のノードで終局している場合は勝敗を調べることで、
    今のノードが評価関数を呼び出す深さ(思考を開始する局面(以降、ルートノード)からの手数)にある場合は評価関数で、
    今のノードの評価値を決定する
 
*ここでの評価値は自分視点のもの
```

というルールで評価値を決めます。
<br>

- 処理の流れは

```
1: 末端の局面(以降、葉ノード | 終局しているか、評価関数を呼び出す事になっているノード)まで移動する
2: 評価値を求める
3: 上記のルールに従って評価値をルートノードに向かって伝える
```

という感じです
<br>
- その他、MiniMax法での「調べなくても問題ないと分かっている部分」を調べないための処理(αβ枝刈り)、αβ枝刈りを多く発生させるための処理(move ordering)なども入れています。


## 7-2-2: 各部分の解説
```
class Eval:
    def __init__(self, path):
        self.model = subprocess.Popen(path, stdin=subprocess.PIPE, stdout=subprocess.PIPE,
                                      universal_newlines=True, bufsize=1)
        self.send('isready')
        self.recv_word('readyok')
```

- Evalクラスでは、subprocessというライブラリで評価関数を起動し、所定のプロトコルに沿って通信します。(プロトコル詳細はコードに書いてあります)
- 評価関数の付け替えを容易にするために評価関数を別プロセスとして起動するようにしました。
<br>

```
    def send(self, command):
        #コマンド送信用
        if k not in command:
            command += k
            pass
        self.model.stdin.write(command)
        return

    def recv(self):
        #コマンド受信用
        return self.model.stdout.readline()

    def recv_word(self, word):
        while True:
            message = self.recv()
            if word in message:
                break
        return message

    def main(self, sfen):
        self.send('eval ' + sfen)
        message = self.recv_word('score').split(' ')
        score = float(message[1])
        return score

```
- Evalクラスの本体です。
- sendはコマンド送信、recvは受信、recv_wordは引数wordが入っているコマンドを受信するまで待機を行います
- main関数は探索部で呼び出される部分です。sfenを受けとり、評価関数を呼び出して計算させ、結果を探索部に返します
<br>

```
class MiniMax(base):
    def __init__(self, path):
        super().__init__()
        self.name = ''
        self.auther = ''
        self.max_depth = 4#何手先を読むかの数字。大きいほど強いけど思考にその分に時間がかかる
        self.Max = 10000# +無限の代わり
        self.Min = self.Max * -1# -無限の代わり
        self.Draw_score = 0#引き分けの価値

        self.eval = Eval(path)#評価関数読み込み

        self.ordering_moves = {}
```
- 探索部の本体です。USI-Xエンジンとして動きます。
- init関数では、初期化を行っています。
- max_depthは何手先を読むか、Maxは勝ちの場合の評価値、Minは負けの場合の評価値、Draw_scoreは引き分けの値です。
- self.eval = Eval(path)でinitの引数のpathで指定された評価関数を呼び出します。
- ordering_movesは並び替え済みの合法手を保存しておくためのものです。
<br>

```
    def score_color_change(self, score):
        #スコアの視点を変える
        return score * -1

    def AB_change(self, AB):
        return [-AB[1], -AB[0]]
```
- これらの関数は引数の値を視点を変えるための関数です。
- 例えば、引数が黒番視点なら白番視点にして、白番視点なら黒番視点にして返します。
- もし、評価値の範囲が0 ~ 1の場合は、-xとするところを1.0 - xに変えると良いです。
<br>

```
    def Ordering(self):
        sfen = self.board.return_sfen()
        if sfen in self.ordering_moves.keys():
            return self.ordering_moves[sfen]
        legal_moves = self.board.gen_legal_moves()
        scores = {}
        for i in range(len(legal_moves)):
            self.board.move_from_usix(legal_moves[i])
            scores[legal_moves[i]] = self.eval.main(self.board.return_sfen())
            self.board.set_sfen(sfen)
        move_and_score = sorted(scores.items(), reverse=False, key=lambda x: x[1])
        moves = []
        for move in move_and_score:
            moves.append(move[0])
        self.ordering_moves[sfen] = moves
        return 
```
- ここではmove orderingを行います。
- 合法手を「パット見よさそうな順」に並び替える事でαβ枝刈りが多く発生するようにします。
<br>

```
    def minimax(self, depth, AB):
        #ゲームが終わったか？
        if self.board.is_gameover():
            #ゲームが終わったので、どっちが勝ったか調べる
            winner = self.board.return_winner()
            #手番側と非手番側のどっちが勝ったか調べる
            if winner == self.board.turn:
                return self.Max#手番側が勝った
            return self.Min#手番側が負けた
        
        if depth <= 0:#評価関数を呼ぶところに来たか？
            return self.eval.main(self.board.return_sfen())#評価関数を呼ぶ
```
- ここでは、「今のノードが葉ノードか？」という判定と、葉ノードだった場合の処理を記述しています。
- depthは残り深さ(手数)、ABは「調べるべき範囲」(以降、探索窓)を表しています。
- ABを[A, B]とすると、A以上B以下の範囲が探索窓です。
<br>
 
``` 
        
        #まだ深く調べる必要がある

        #盤面を戻すための準備
        sfen = self.board.return_sfen()
        #次調べる手を準備する
        legal_moves = self.board.gen_legal_moves()
        #最高のスコアを保存しておく変数の準備
        bestscore = self.Min

        if PASS in legal_moves:
            self.board.move_from_usix(PASS)
            score = self.minimax(depth - 1, AB)
            self.board.set_sfen(sfen)
            return score
        
        #すべての手を調べる
        for move in legal_moves:
            self.board.move_from_usix(move)#打つ
            score = self.minimax(depth - 1, self.AB_change(AB))#調べる
            score = self.score_color_change(score)#手番側の視点に変える
            self.board.set_sfen(sfen)#戻す

            if score >= AB[1]:
                return score

            #最高スコアを更新したか？
            if score >= bestscore:
                #更新した
                bestscore = score
            AB[0] = max((AB[0], score))
            
        #全て調べた後の最高スコアを返す
        return bestscore
```
- ここでは、次の全ての手を調べて今のノードの評価値を求めます。
- score = self.minimax(depth - 1, self.AB_change(AB))で、相手から見た評価値を取得します。それを次の行で自分視点に変えます。
- if score >= AB[1]:で、探索窓から外れているかチェックします。外れている場合は探索を打ち切ります。
- AB[0] = max((AB[0], score))で、探索窓の更新を行っています。
- return bestscoreで、評価値を返します
- パスの場合は手番の反転なしで探索を行います。
<br>

```
    def think(self):
        #盤面を復元する必要があるけど、snail_reversiには「1手戻る」機能がないのでsfenを使って無理やり戻す
        sfen = self.board.return_sfen()
        #次の手を取得
        legal_moves = self.Ordering()
        bestmove = None
        bestscore = self.Min
        AB = [self.Min, self.Max]
        
        if PASS in legal_moves:
            return PASS, None
```
- 初期化等の準備を行います。
- 合法手は、並べ替え済みのものを使います。基本的に合法手の並び替えは、ルートノードに近いほど「遅い代わりに精度が高い」タイプのものが使われます。
- 探索窓の幅は最大に設定します
- もし、合法手がパスしかないならパスします。
```
        #すべての手を調べる
        for move in legal_moves:
            self.board.move_from_usix(move)#打つ
            score = self.minimax(self.max_depth - 1, self.AB_change(AB))#調べる
            score = self.score_color_change(score)#自分視点にする
            self.board.set_sfen(sfen)#戻す

            #今までで1番良い手か？
            if score >= bestscore:
                #良い手だったので更新
                bestmove = move
                bestscore = score
            AB[0] = max((AB[0], score))
        #1番良い手(最善手)を返す
        return bestmove, None
```
- すべての手を調べて、最も評価値が高い手をbestmoveに入れておきます。すべての手を調べた後、bestmoveを返します。
- 探索窓の更新も行います


## 7-3: サンプルコードの解説(評価関数)
- 準備中

## 7-4: 強さ
- 準備中

## 7-5: さらに先へ
- 準備中

# 8: 終わりに
- 準備中
