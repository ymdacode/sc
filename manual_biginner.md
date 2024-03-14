# SuperCollier　初級

とりあえず音を出して、パラメータを変えて遊べるようになるために。

## シンセの定義
```
(
SynthDef.new("test-synth",
    { 
        |freq = 440　out = #[0, 1]|
        Out.ar(out, SinOsc.ar(freq, 0, 0.2))
    }
).add;
)
x = Synth("test-synth",["freq",660]);
x.free;
```
`|freq = 440|`のように引数を指定する。
`arg freq = 440`の糖衣構文。
SCではリテラルの配列に#をつけることに注意。
SinOscのようなものをUnit Generator(=UGen)と呼ぶ。


## レコーディング

`s.record(duration: 5);`
引数は
(path, bus, numChannels, node, duration)
とあるが基本的にdurationのみ指定すればok

ファイルの保存先は
`thisProcess.platform.recordingsDir`
という変数を変更する。
パスはstringで、バックスラッシュはエスケープする。

## フィルタ

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

## エンヴェロープ

EnvGenの中でエンヴェロープを記述する。
```

env = EnvGen.kr(
        Env(
            levels: [0, 0.1, 0.2, 0.3], 
            times: [0.1, 0.1, 0.1],
            curve: 8
        )
    );
```

EnvGenは音声信号そのものではないので、処理の軽いkrを用いる。
EnvGenの引数は
EnvGen.kr(envelope, gate: 1.0, levelScale: 1.0, levelBias: 0.0, timeScale: 1.0, doneAction: 0)

ここに適用するエンヴェロープはEnvを使って指定する。
最も基本的なものは
Env.new(levels: [ 0, 1, 0 ], times: [ 1, 1 ], curve: 'lin', releaseNode, loopNode, offset: 0)

その他にも便利なメソッドが用意されている。
.plotでエンヴェロープを確認できる。