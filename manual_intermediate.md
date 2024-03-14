# SuperCollider 中級
クラスとメソッドの詳細を理解して自由にコーディングするために。


## バス

デフォルトでは、バスはオーディオ信号用に1024、コントロール信号用に16,384ある。
オーディオバスは出力、入力の順に割り当てられる。
つまり、2in2outのサーバーでは、out1,out2,in1,in2の順に0,1,2,3となる。
inに読み込まれたデータはレコーディングやプロセッシングなどのためにSCに送られる。
outに書き出されたデータはスピーカーからの再生音となる。
残りのバスはルーティングのための「プライベート」となり、エフェクトセンドのように利用される。

### バスへのデータの書き込み
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

### バスからのデータの読み込み
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