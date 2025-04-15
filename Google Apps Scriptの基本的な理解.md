# Google Apps Scriptの基本的な理解

Google Apps Script (GAS) は JavaScript をベースにしていますが、Google独自の機能が多くあります。以下に基本的な概念を説明します。

## GASの基本的な構造

```javascript
// 1. 関数定義 - これがエントリーポイントになる
function myFunction() {
  // ここにコードを書く
}

// 2. Google Sheetsへのアクセス
function accessSheet() {
  // アクティブなスプレッドシートを取得
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  
  // 特定のシートを名前で取得
  const sheet = ss.getSheetByName('シート1');
  
  // セルの値を取得
  const value = sheet.getRange('A1').getValue();
  
  // セルに値を設定
  sheet.getRange('B1').setValue('こんにちは');
}

// 3. UIとの対話
function showAlert() {
  const ui = SpreadsheetApp.getUi();
  ui.alert('メッセージ', '表示するテキスト', ui.ButtonSet.OK);
}
```

## 重要なGoogle Apps Scriptのクラス

1. **SpreadsheetApp**: スプレッドシートにアクセスするためのメインクラス
2. **Spreadsheet**: スプレッドシート全体を表すクラス
3. **Sheet**: 個々のシートを表すクラス
4. **Range**: セル範囲を表すクラス
5. **Ui**: ユーザーインターフェースを操作するクラス

## スプレッドシートの操作例

```javascript
// 行や列の操作
function manipulateRowsAndColumns() {
  const sheet = SpreadsheetApp.getActiveSheet();
  
  // 行を挿入
  sheet.insertRowAfter(3); // 3行目の後に新しい行を挿入
  
  // 列を挿入
  sheet.insertColumnBefore(2); // 2列目の前に新しい列を挿入
  
  // セル範囲の値を一度に設定
  const values = [
    ['名前', '年齢', '都道府県'],
    ['田中', 30, '東京'],
    ['佐藤', 25, '大阪']
  ];
  sheet.getRange(1, 1, values.length, values[0].length).setValues(values);
  
  // 書式設定
  sheet.getRange('A1:C1').setBackground('#D9EAD3').setFontWeight('bold');
}
```

## 主な操作と構文

### 1. シートの取得と作成

```javascript
// 現在アクティブなスプレッドシートを取得
const ss = SpreadsheetApp.getActiveSpreadsheet();

// スプレッドシートをIDで取得
const ss = SpreadsheetApp.openById('スプレッドシートのID');

// スプレッドシートをURLで取得
const ss = SpreadsheetApp.openByUrl('スプレッドシートのURL');

// シートを名前で取得
const sheet = ss.getSheetByName('シート名');

// 現在アクティブなシートを取得
const sheet = ss.getActiveSheet();

// 新しいシートを作成
const newSheet = ss.insertSheet('新しいシート名');
```

### 2. セルの操作

```javascript
// セル範囲を取得（A1表記）
const range = sheet.getRange('A1:C3');

// セル範囲を取得（行、列、行数、列数）
const range = sheet.getRange(1, 1, 3, 3); // A1:C3と同じ

// 単一セルの値を取得
const value = sheet.getRange('A1').getValue();

// セル範囲の値を取得（二次元配列で返される）
const values = sheet.getRange('A1:C3').getValues();

// 単一セルに値を設定
sheet.getRange('A1').setValue('こんにちは');

// セル範囲に値を設定
const data = [
  ['名前', '年齢', '都道府県'],
  ['田中', 30, '東京']
];
sheet.getRange(1, 1, data.length, data[0].length).setValues(data);
```

### 3. 書式設定

```javascript
// 背景色を設定
range.setBackground('#D9EAD3');

// フォントの太さを設定
range.setFontWeight('bold');

// フォントサイズを設定
range.setFontSize(12);

// セルの水平方向の配置を設定
range.setHorizontalAlignment('center'); // 'left', 'center', 'right'

// セルの垂直方向の配置を設定
range.setVerticalAlignment('middle'); // 'top', 'middle', 'bottom'

// 数値フォーマットを設定
range.setNumberFormat('0.00'); // 小数点以下2桁
range.setNumberFormat('yyyy/mm/dd'); // 日付
range.setNumberFormat('¥#,##0'); // 通貨（日本円）
```

### 4. データ検証（ドロップダウンリスト等）

```javascript
// ドロップダウンリストの作成
const rule = SpreadsheetApp.newDataValidation()
  .requireValueInList(['選択肢1', '選択肢2', '選択肢3'], true)
  .build();
sheet.getRange('A1:A10').setDataValidation(rule);

// 数値の範囲制限
const numRule = SpreadsheetApp.newDataValidation()
  .requireNumberBetween(1, 100)
  .build();
sheet.getRange('B1:B10').setDataValidation(numRule);
```

### 5. フィルターの設定

```javascript
// 指定した範囲にフィルターを設定
sheet.getRange('A1:E10').createFilter();
```

### 6. イベントトリガー

```javascript
// スプレッドシートが開かれたときに実行される関数
function onOpen() {
  const ui = SpreadsheetApp.getUi();
  ui.createMenu('カスタムメニュー')
    .addItem('機能1', 'function1')
    .addSeparator()
    .addItem('機能2', 'function2')
    .addToUi();
}

// 編集が行われたときに実行される関数
function onEdit(e) {
  // eにはイベント情報が含まれる
  const range = e.range; // 編集されたセル範囲
  const value = e.value; // 新しい値
  const oldValue = e.oldValue; // 古い値
  const sheet = e.source.getActiveSheet(); // 編集されたシート
}
```

### 7. その他の便利な操作

```javascript
// 列の幅を設定
sheet.setColumnWidth(1, 150); // 1列目の幅を150ピクセルに設定

// 行の高さを設定
sheet.setRowHeight(1, 30); // 1行目の高さを30ピクセルに設定

// 検索と置換
sheet.createTextFinder('検索文字列').replaceAllWith('置換文字列');

// 条件付き書式設定
const range = sheet.getRange('A1:C10');
const rule = SpreadsheetApp.newConditionalFormatRule()
  .whenNumberGreaterThan(100)
  .setBackground('#FF9999')
  .build();
const rules = sheet.getConditionalFormatRules();
rules.push(rule);
sheet.setConditionalFormatRules(rules);

// シートの保護
const protection = sheet.protect().setDescription('編集禁止');
protection.setWarningOnly(true); // 警告のみ（実際には編集可能）
```

## 高度な使用例

### 1. HTMLサービスを使用したウェブインターフェース

```javascript
// HTMLを表示する関数
function showWebInterface() {
  const html = HtmlService.createHtmlOutputFromFile('WebInterface')
    .setWidth(800)
    .setHeight(600);
  SpreadsheetApp.getUi().showModalDialog(html, 'ウェブインターフェース');
}

// WebInterface.htmlファイル内でJavaScriptからサーバー側の関数を呼び出す例
// google.script.run.withSuccessHandler(onSuccess).withFailureHandler(onFailure).serverFunction(data);
```

### 2. トリガーの設定

```javascript
// 時間駆動型トリガーを設定する（例：毎日午前9時に実行）
function createTimeDrivenTrigger() {
  ScriptApp.newTrigger('myFunction')
    .timeBased()
    .atHour(9)
    .everyDays(1)
    .create();
}
```

## デバッグとエラー処理

```javascript
// ログを出力（スクリプトエディタの「実行」→「ログを表示」で確認）
console.log('デバッグ情報:', variableName);

// try-catchでエラーを捕捉
try {
  // エラーが発生する可能性のあるコード
  const value = sheet.getRange('A1').getValue();
  // 処理
} catch (error) {
  // エラー処理
  console.error('エラーが発生しました:', error);
  SpreadsheetApp.getUi().alert('エラー', 'エラーが発生しました: ' + error.toString(), SpreadsheetApp.getUi().ButtonSet.OK);
}
```

## 注意事項とベストプラクティス

1. **実行時間制限**: スクリプトの実行時間は最大6分（タイムアウト）
2. **クォータ制限**: 1日あたりの実行回数や外部APIへのリクエスト数に制限あり
3. **非同期処理**: `google.script.run`はHTMLからサーバー側の関数を呼び出す際に非同期で動作
4. **APIキーの管理**: 外部サービスのAPIキーはスクリプトプロパティに保存（Properties Service）
5. **権限管理**: 必要最小限の権限を要求（スコープ）
6. **バージョン管理**: 重要な変更前にはバージョンを作成しておく
7. **パフォーマンス**: 大量のデータを処理する際はバッチ処理を検討

Google Apps Scriptを使いこなすことで、Google Sheetsを単なる表計算ソフトから強力なビジネスアプリケーションに変えることができます。
