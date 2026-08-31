# Instagram リール運用 — 作業の前提

このリポジトリは、レシピアカウントのリール制作を管理する。
ローカルで作業を始めたら、まずこのファイルと `docs/` を読むこと。

## アカウントの前提

| 項目 | 内容 |
| --- | --- |
| ジャンル | レシピ（冷凍ストック・時短） |
| 掲げている軸 | 10分完成！平日ラクする冷凍ストック |
| フォロワー | 約5,700人／投稿47本（2026-08時点） |
| オーディエンス | 女性80.9% / 男性19.1% |
| 制作体制 | 撮影・アフレコ・編集はすべて本人。仕上げは CapCut |
| 音源 | Instagram アプリ内で付ける（**書き出し時にBGMは入れない**） |

## 分かっていること（実測）

8/27投稿「ゴマたま鶏しゃぶ」（21秒）の分析 → `docs/analysis/2026-08-27_goma-tama-torishabu.md`

- 視聴維持率 28.6%（平均6秒 ÷ 21秒）。**3秒で50%が離脱**
- 完成形が映るのが4.7秒。**その時点で58%が離脱済み**
- 保存26 / いいね9（保存はいいねの2.9倍）＝レシピの需要はある
- 閲覧数3,175。自己平均（約7,000）の4割
- アフレコの冒頭が0.1〜4.2秒の**息継ぎなし4.1秒**。離脱区間と重なる

参考リール（@maho_protein / 25.9秒）の分解 → `docs/analysis/2026-08-27_reference-teardown.md`

- **0〜6.7秒が予告編、6.7秒からが本編**の2周構造
- 0秒は動作の超ドアップ。最初のテロップは6モーラ、1.6秒で切替
- テロップは平均1.2秒ごと（25.9秒で21回）
- キャプション約1,000字。1行目に実績、コメント誘導は絵文字1つで答えられる形
- ただし「めざましTV紹介」という外部要因が数字に乗っている

## リールの標準仕様

| 項目 | 値 |
| --- | --- |
| 解像度 | 1080 × 1920（9:16） |
| コーデック | H.264 / yuv420p |
| 音声 | AAC 192kbps |
| フレームレート | 30fps |
| 尺 | 16秒前後 |
| テロップ切替 | 1.2秒ごとを目安 |
| 最初のテロップ | 10モーラ以内・1.6秒以内で切り替える |
| アフレコ | 1秒あたり7モーラ。一息で4秒以上話さない |
| 安全マージン | 上15% / 下20% には文字を置かない |

## 生肉を扱う企画での必須記載

凍った鶏むね肉をピーラーで削る手法は**生の鶏肉の調理**にあたる。
動画内とキャプションの両方に、以下を必ず入れる。

- 野菜を先に切る（器具からの菌の付着防止）
- 生肉に使ったピーラー・まな板はすぐ洗剤で洗い、熱湯消毒する
- 生肉用の箸と食事用の箸を分ける
- 中心まで完全に白くなるまで加熱する（「サッと」「さっとくぐらせて」は使わない）

## 素材フォルダの構成

```
素材/YYYYMMDD_企画名/
  ├── 01_素材/      撮影した元データ（無加工・触らない）
  ├── 02_書き出し/  作業中・完成データ
  └── 03_テロップ/  合成用のPNG
```

## 作業コマンド（検証済み）

ffmpeg が必要。無ければ macOS は `brew install ffmpeg`、Windows は `winget install Gyan.FFmpeg`。

### 素材の把握

```bash
# 尺・解像度・fps を確認
ffprobe -hide_banner FILE.mov

# 冒頭4.4秒を0.4秒刻みでコンタクトシート化（フックの確認用）
ffmpeg -i FILE.mov -vf "select='lt(t,4.4)*not(mod(n,12))',scale=300:-1,tile=6x2:padding=6:color=white" -frames:v 1 hook.png

# 全体をコンタクトシート化
ffmpeg -i FILE.mov -vf "fps=1/1.5,scale=250:-1,tile=5x3:padding=6:color=white" -frames:v 1 full.png
```

出力した PNG は Read ツールで開いて中身を目視すること。

### テロップPNGの生成（Python + Pillow）

`drawtext` が使えないビルドでも、PNGを作って `overlay` で合成すれば同じことができる。
日本語フォントは macOS なら `/System/Library/Fonts/ヒラギノ角ゴシック W6.ttc`、
Linux なら `/usr/share/fonts/truetype/fonts-japanese-gothic.ttf`。

```python
from PIL import Image, ImageDraw, ImageFont
W, H = 1080, 1920
img = Image.new("RGBA", (W, H), (0, 0, 0, 0))
d = ImageDraw.Draw(img)
f = ImageFont.truetype(FONT_PATH, 96)
txt = "解凍しないで"
w = d.textbbox((0, 0), txt, font=f)[2]
# stroke_width で黒フチを付ける（ループで描くより滑らか）
d.text(((W - w) // 2, int(H * 0.42)), txt, font=f,
       fill=(255, 255, 255, 255), stroke_width=6, stroke_fill=(0, 0, 0, 235))
img.save("telop.png")
```

文字サイズは96px以上（画面幅の1/12以上）。上15% / 下20% に文字を置かない。

### カット＋テロップ合成＋書き出し

```bash
ffmpeg -ss 1.0 -t 2.4 -i CLIP.mov -i telop.png \
  -filter_complex "[0:v]scale=1080:1920:force_original_aspect_ratio=increase,crop=1080:1920,setsar=1[v];[v][1:v]overlay=0:0[out]" \
  -map "[out]" -map 0:a \
  -c:v libx264 -preset medium -crf 20 -pix_fmt yuv420p -c:a aac -b:a 192k -r 30 \
  -y out_01.mp4
```

### 連結

```bash
# 各カットを同一仕様で書き出してから
printf "file 'out_01.mp4'\nfile 'out_02.mp4'\n" > list.txt
ffmpeg -f concat -safe 0 -i list.txt -c copy -y rough_cut.mp4
```

### 音声の確認

```bash
# 音量レベル
ffmpeg -i FILE.mov -af volumedetect -f null - 2>&1 | grep volume

# 声が乗っている区間の推定（BGMがステレオ、声がセンターの場合）
# Mid−Side比が高い区間＝中央定位の音＝声
```

## 作業の進め方

1. 素材のコンタクトシートを作り、Read で目視して、何が撮れているか把握する
2. `docs/scripts/` の台本と突き合わせ、**足りないカットを先に指摘する**
3. カットごとに書き出し → 連結してラフカットを作る
4. **BGMは入れずに書き出す**（Instagramアプリで付けるため）
5. 完成データはユーザーに渡す。CapCut で仕上げてもらう
6. 台本・キャプションの変更は `docs/` に反映してコミットする

## やらないこと

- 動画ファイルを git にコミットしない（容量が大きく、Git は動画に向かない）。
  リポジトリに置くのは台本・キャプション・分析などのテキストだけ
- 元素材（`01_素材/`）を上書きしない
- 参考にしたアカウントのテロップ文言・ナレーション・料理名をそのまま使わない
