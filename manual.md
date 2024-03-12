# SuperCollier　私的マニュアル

# シンセの定義
Sample:
```
(
SynthDef.new("test-synth",
 { |freq = 440　out = #[0, 1]| Out.ar(out, SinOsc.ar(freq, 0, 0.2)) }
 ).add;
)
x = Synth("test-synth",["freq",660]);
x.free;
```
`|freq = 440|`のように引数を指定する。
`arg freq = 440`の糖衣構文。
SCではリテラルの配列に#をつけることに注意。
SinOscのようなものをUnit Generator(=UGen)と呼ぶ。

## オシレータの種類


# バス

デフォルトでは、バスはオーディオ信号用に1024、コントロール信号用に16,384ある。
オーディオバスは出力、入力の順に割り当てられる。
つまり、2in2outのサーバーでは、out1,out2,in1,in2の順に0,1,2,3となる。
inに読み込まれたデータはレコーディングやプロセッシングなどのためにSCに送られる。
outに書き出されたデータはスピーカーからの再生音となる。
残りのバスはルーティングのための「プライベート」となり、エフェクトセンドのように利用される。

## バスへのデータの書き込み
Out ugenを使ってデータをオーディオバスに書き込む。
Out.arはインデックスと出力の2つの引数を持つ。

```
(
SynthDef("dataForABus", {
    Out.ar(
        0,        // write 1 channel of audio into bus 0
        Saw.ar(100, 0.1)
    )
}).add;
)

Synth("dataForABus");
```

複数のシンセを同じバスに書き込むと、それらの出力は合計される。
```
(
SynthDef("tutorial-args", { arg freq = 440, out = 0;
    Out.ar(out, SinOsc.ar(freq, 0, 0.2));
}).add;
)
// out2から660Hzと770Hzのサイン波が再生される。
x = Synth("tutorial-args", ["out", 1, "freq", 660]);
y = Synth("tutorial-args", ["out", 1, "freq", 770]);
```

## バスからのデータの読み込み
In ugenを使ってオーディオバスからデータを読み込む。
In.arはインデックスと読み込むチャンネル数の2つの引数を持つ。
```
(
SynthDef("dataFromABus", {
    Out.ar(
        0,
        [    // the left channel gets input from an audio bus
            In.ar(0, 1),
            SinOsc.ar(440, 0.2)
        ]
    )
}).add;
)

(
Synth("dataForABus");    // synthesize a sawtooth wave on channel 0
Synth("dataFromABus");    // pair it with a sine wave on channel 1
)
```

# レコーディング

`s.record(duration: 5);`
引数は
(path, bus, numChannels, node, duration)
とあるが基本的にdurationのみ指定すればok

ファイルの保存先は
`thisProcess.platform.recordingsDir`
という変数を変更する。
パスはstringで、バックスラッシュはエスケープする。

# フィルタ

ローパスフィルタの例
```
(
SynthDef("subtractive", { arg out;
    Out.ar(
        out,
        LPF.ar(
            Pulse.ar(440, 0.5, 0.1), 
            500
        )
    )
}).add;
)
```
LPF.arは、第一引数が入力信号、第二引数がカットオフする周波数となる。

