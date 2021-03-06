# ACV-TONECURVE

[![Build Status](https://app.travis-ci.com/Nagi-Fuyumi/acv-tonecurve.svg?branch=main)](https://app.travis-ci.com/Nagi-Fuyumi/acv-tonecurve)
[![MIT License](http://img.shields.io/badge/license-MIT-blue.svg?style=flat)](LICENSE)

[English](README.md)

## About

### Node.js で画像バイナリピクセルデータに AdobePhotoshop のトーンカーブプリセットファイルを適応するためのモジュールです

すべて TypeScript で構築されており、特殊なビルドは必要ありません  
下記のコマンドでインストール可能です

```bash
npm i acv-tonecurve
```

## Sample

別途 _node-canvas_ モジュールをインストールしてください

```bash
npm i canvas
```

利用方法は簡単です  
まずは、_AcvToneCurve_ をインスタンス化する際に引数にファイル名を渡してください  
その後、画像データから得られるバイナリデータを _doCurves_ 関数に渡してください  
（参照で渡されることに注意してください）  
_ctx_ コンテキストに対して書き換えたデータを渡し、_canvas_ を書き換えてください

**TypeScript**

```typescript
import AcvToneCurve from "acv-tonecurve";
import * as Canvas from "canvas";
import fs from "fs";

const Image = Canvas.Image;

const toneCurve = new AcvToneCurve("./test/tone.acv");

const loadImageSync = (file: string) =>
  new Promise<Canvas.Image>((resolve, reject) => {
    try {
      const img = new Image();

      img.onload = () => {
        resolve(img);
      };

      img.src = fs.readFileSync(file);
    } catch (e: any) {
      reject(e);
    }
  });

test("test", async () => {
  const canvas = new Canvas.Canvas(1000, 1000);
  const ctx = canvas.getContext("2d");
  ctx.globalAlpha = 1;
  ctx.globalCompositeOperation = "source-over";
  ctx.beginPath();
  ctx.fillStyle = "rgb( 255,255, 255)";
  ctx.fillRect(0, 0, canvas.width, canvas.height);

  const img = await loadImageSync("./test/image.png");

  ctx.drawImage(img, 0, 0);

  const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);

  toneCurve.doCurves(imageData.data);

  ctx.putImageData(imageData, 0, 0);

  console.log(canvas.toBuffer());
});
```

**JavaScript**

```javascript
const AcvToneCurve = require("acv-tonecurve");
const Canvas = require("canvas");
const fs = require("fs");

const Image = Canvas.Image;

const toneCurve = new AcvToneCurve.AcvToneCurve("./test/tone.acv");

const loadImageSync = (file) =>
  new Promise((resolve, reject) => {
    try {
      const img = new Image();

      img.onload = () => {
        resolve(img);
      };

      img.src = fs.readFileSync(file);
    } catch (e) {
      reject(e);
    }
  });

test("test", async () => {
  const canvas = new Canvas.Canvas(1000, 1000);
  const ctx = canvas.getContext("2d");
  ctx.globalAlpha = 1;
  ctx.globalCompositeOperation = "source-over";
  ctx.beginPath();
  ctx.fillStyle = "rgb( 255,255, 255)";
  ctx.fillRect(0, 0, canvas.width, canvas.height);

  const img = await loadImageSync("./test/image.png");

  ctx.drawImage(img, 0, 0);

  const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);

  toneCurve.doCurves(imageData.data);

  ctx.putImageData(imageData, 0, 0);

  console.log(canvas.toBuffer());
});
```

## Resources

acv-tonecurve を制作するきっかけになった方々です。
構想を使用をさせていただきありがとうございます。

Monotonic Spline Curves - http://blog.mackerron.com/2011/01/01/javascript-cubic-splines/  
jQuery-filter.me - https://github.com/MatthewRuddy/jQuery-filter.me

## Change

- 2022/04/28 JS として require するとエラーになる不具合の修正
