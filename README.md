<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ガキ鉄 料金計算表</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            background-color: #f8f9fa;
            color: #333;
        }

        .container {
            background: white;
            border-radius: 10px;
            padding: 30px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
        }

        h1 {
            color: #ff69b4;
            text-align: center;
            margin-bottom: 30px;
        }

        .section {
            margin-bottom: 25px;
            padding: 20px;
            border: 1px solid #e0e0e0;
            border-radius: 8px;
            background-color: #fafafa;
        }

        .section h3 {
            color: #4a4a4a;
            margin-top: 0;
            border-bottom: 2px solid #ff69b4;
            padding-bottom: 5px;
        }

        .input-group {
            margin-bottom: 15px;
        }

        label {
            display: inline-block;
            width: 200px;
            font-weight: bold;
            color: #555;
        }

        input[type="number"] {
            width: 100px;
            padding: 5px;
            border: 1px solid #ddd;
            border-radius: 4px;
        }

        select {
            width: 220px;
            padding: 5px;
            border: 1px solid #ddd;
            border-radius: 4px;
        }

        input[type="checkbox"] {
            margin-left: 10px;
        }

        .total {
            background-color: #ff69b4;
            color: white;
            padding: 15px;
            border-radius: 8px;
            text-align: center;
            font-size: 1.2em;
            font-weight: bold;
            margin-top: 20px;
        }

        .note {
            background-color: #fff3cd;
            border: 1px solid #ffeaa7;
            padding: 10px;
            border-radius: 5px;
            margin-top: 15px;
            font-size: 0.9em;
        }

        .counting-options h4 {
            color: #333;
            margin-bottom: 10px;
            font-size: 1em;
        }

        .count-result {
            background-color: #e8f5e8;
            padding: 15px;
            border-radius: 5px;
            border: 1px solid #c3e6c3;
            margin-top: 10px;
        }

        button:hover {
            opacity: 0.8;
        }
    </style>
</head>

<body>
    <div class="container">
        <h1>🎙️ ガキ鉄 料金計算表 🎙️</h1>

        <div class="section">
            <h3>【ト書き抜き】台本文字数カウント</h3>
                        <div class="input-group">
                <label>通常：1文字</label>
                <select id="baseRate" onchange="calculate()">
                    <option value="1.4">iikoe(仲介経由): 1.4円</option>
                    <option value="1.3">メール(HP経由): 1.3円</option>
                </select>
            </div>
            <div class="input-group">
                <label>割増：1文字</label>
                <input type="number" id="premiumRate" value="10" readonly style="background-color: #f8f9fa;">
                <span>円</span>
            </div>
        <div class="total" id="totalAmount">
            台本: 0円 + オプション: 0円 = 合計: 0円
        </div>
            <!-- 台本入力エリア（2カラムレイアウト） -->
            <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-bottom: 20px;">
                <!-- 基本台本 -->
                <div>
                    <div class="input-group">
                        <div class="count-result">
                            <div style="display: flex; justify-content: space-between; margin-bottom: 10px;">
                                <span><strong>文字数:</strong> <span id="billableChars">0</span>文字</span>
                                <span><strong>通常の料金:</strong> <span id="basicAmount">0</span>円</span>
                            </div>
                        </div>
                        <br>
                        <textarea id="scriptText" placeholder="通常の台本" oninput="countCharacters()"
                            style="width: calc(100%); height: 200px; padding: 10px; border: 1px solid #ddd; border-radius: 4px; font-family: monospace; font-size: 14px; resize: vertical; box-sizing: border-box;"></textarea>
                    </div>
                </div>

                <!-- 割増演技台本 -->
                <div>
                    <div class="input-group">
                        <div class="count-result" style="background-color: #ffe6e6; border: 1px solid #ffb3b3;">
                            <div style="display: flex; justify-content: space-between; margin-bottom: 10px;">
                                <span><strong>文字数:</strong> <span id="premiumBillableChars">0</span>文字</span>
                                <span><strong>割増の料金:</strong> <span id="premiumAmount">0</span>円</span>
                            </div>
                        </div>
                        <br>
                        <textarea id="premiumScriptText" placeholder="割増演技の対象一覧
・嘔吐
・絶叫
・オホ声
・リョナ
・イマラチオ"
                            oninput="countPremiumCharacters()"
                            style="width: calc(100%); height: 200px; padding: 10px; border: 1px solid #dc3545; border-radius: 4px; font-family: monospace; font-size: 14px; resize: vertical; box-sizing: border-box;"></textarea>
                    </div>
                </div>
            </div>

            <div class="counting-options">
                <h4>ト書きの除外設定</h4>
                <div style="margin-bottom: 15px;">
                    <label style="width: auto; font-weight: bold;">囲い込み</label>
                    <div id="bracketPairsContainer"
                        style="margin-top: 5px; display: grid; grid-template-columns: repeat(auto-fill, minmax(80px, 1fr)); gap: 8px; max-height: 150px; overflow-y: auto; border: 1px solid #e0e0e0; padding: 10px; border-radius: 4px; background-color: #fafafa;">
                        <div class="bracket-pair-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="bracket-start-input" value="〈" oninput="countCharacters()"
                                style="width: 20px; padding: 2px; border: none; font-size: 12px; text-align: center;">
                            <input type="text" class="bracket-end-input" value="〉" oninput="countCharacters()"
                                style="width: 20px; padding: 2px; border: none; font-size: 12px; text-align: center;">
                            <span onclick="removeBracketPair(this)"
                                style="color: #333; cursor: pointer; padding: 2px 3px; font-weight: bold; font-size: 12px;">✕</span>
                        </div>
                        <div class="bracket-pair-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="bracket-start-input" value="《" oninput="countCharacters()"
                                style="width: 20px; padding: 2px; border: none; font-size: 12px; text-align: center;">
                            <input type="text" class="bracket-end-input" value="》" oninput="countCharacters()"
                                style="width: 20px; padding: 2px; border: none; font-size: 12px; text-align: center;">
                            <span onclick="removeBracketPair(this)"
                                style="color: #333; cursor: pointer; padding: 2px 3px; font-weight: bold; font-size: 12px;">✕</span>
                        </div>
                        <div class="bracket-pair-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="bracket-start-input" value="【" oninput="countCharacters()"
                                style="width: 20px; padding: 2px; border: none; font-size: 12px; text-align: center;">
                            <input type="text" class="bracket-end-input" value="】" oninput="countCharacters()"
                                style="width: 20px; padding: 2px; border: none; font-size: 12px; text-align: center;">
                            <span onclick="removeBracketPair(this)"
                                style="color: #333; cursor: pointer; padding: 2px 3px; font-weight: bold; font-size: 12px;">✕</span>
                        </div>
                        <div class="bracket-pair-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="bracket-start-input" value="〔" oninput="countCharacters()"
                                style="width: 20px; padding: 2px; border: none; font-size: 12px; text-align: center;">
                            <input type="text" class="bracket-end-input" value="〕" oninput="countCharacters()"
                                style="width: 20px; padding: 2px; border: none; font-size: 12px; text-align: center;">
                            <span onclick="removeBracketPair(this)"
                                style="color: #333; cursor: pointer; padding: 2px 3px; font-weight: bold; font-size: 12px;">✕</span>
                        </div>
                        <div class="bracket-pair-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="bracket-start-input" value="≪" oninput="countCharacters()"
                                style="width: 20px; padding: 2px; border: none; font-size: 12px; text-align: center;">
                            <input type="text" class="bracket-end-input" value="≫" oninput="countCharacters()"
                                style="width: 20px; padding: 2px; border: none; font-size: 12px; text-align: center;">
                            <span onclick="removeBracketPair(this)"
                                style="color: #333; cursor: pointer; padding: 2px 3px; font-weight: bold; font-size: 12px;">✕</span>
                        </div>
                        <div class="bracket-pair-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="bracket-start-input" value="（" oninput="countCharacters()"
                                style="width: 20px; padding: 2px; border: none; font-size: 12px; text-align: center;">
                            <input type="text" class="bracket-end-input" value="）" oninput="countCharacters()"
                                style="width: 20px; padding: 2px; border: none; font-size: 12px; text-align: center;">
                            <span onclick="removeBracketPair(this)"
                                style="color: #333; cursor: pointer; padding: 2px 3px; font-weight: bold; font-size: 12px;">✕</span>
                        </div>
                        <div class="bracket-pair-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="bracket-start-input" value="(" oninput="countCharacters()"
                                style="width: 20px; padding: 2px; border: none; font-size: 12px; text-align: center;">
                            <input type="text" class="bracket-end-input" value=")" oninput="countCharacters()"
                                style="width: 20px; padding: 2px; border: none; font-size: 12px; text-align: center;">
                            <span onclick="removeBracketPair(this)"
                                style="color: #333; cursor: pointer; padding: 2px 3px; font-weight: bold; font-size: 12px;">✕</span>
                        </div>
                    </div>
                    <button onclick="addBracketPair()"
                        style="background: #28a745; color: white; border: none; border-radius: 4px; padding: 8px 16px; font-size: 12px; margin-top: 8px;">
                        追加
                    </button>
                </div>
                <div style="margin-bottom: 15px;">
                    <label style="width: auto; font-weight: bold;">行ごと</label>
                    <div id="excludeLinesContainer"
                        style="margin-top: 5px; display: grid; grid-template-columns: repeat(auto-fill, minmax(110px, 1fr)); gap: 8px; max-height: 200px; overflow-y: auto; border: 1px solid #e0e0e0; padding: 10px; border-radius: 4px; background-color: #fafafa;">
                        <div class="exclude-line-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="exclude-line-input" value="◇" oninput="countCharacters()"
                                style="width: 80px; padding: 3px; border: none; font-size: 12px; flex: 1;">
                            <span onclick="removeExcludeLine(this)"
                                style="color: #333; cursor: pointer; padding: 2px 5px; font-weight: bold; font-size: 14px;">✕</span>
                        </div>
                        <div class="exclude-line-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="exclude-line-input" value="◇" oninput="countCharacters()"
                                style="width: 80px; padding: 3px; border: none; font-size: 12px; flex: 1;">
                            <span onclick="removeExcludeLine(this)"
                                style="color: #333; cursor: pointer; padding: 2px 5px; font-weight: bold; font-size: 14px;">✕</span>
                        </div>
                        <div class="exclude-line-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="exclude-line-input" value="■" oninput="countCharacters()"
                                style="width: 80px; padding: 3px; border: none; font-size: 12px; flex: 1;">
                            <span onclick="removeExcludeLine(this)"
                                style="color: #333; cursor: pointer; padding: 2px 5px; font-weight: bold; font-size: 14px;">✕</span>
                        </div>
                        <div class="exclude-line-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="exclude-line-input" value="■" oninput="countCharacters()"
                                style="width: 80px; padding: 3px; border: none; font-size: 12px; flex: 1;">
                            <span onclick="removeExcludeLine(this)"
                                style="color: #333; cursor: pointer; padding: 2px 5px; font-weight: bold; font-size: 14px;">✕</span>
                        </div>
                        <div class="exclude-line-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="exclude-line-input" value="◆" oninput="countCharacters()"
                                style="width: 80px; padding: 3px; border: none; font-size: 12px; flex: 1;">
                            <span onclick="removeExcludeLine(this)"
                                style="color: #333; cursor: pointer; padding: 2px 5px; font-weight: bold; font-size: 14px;">✕</span>
                        </div>
                        <div class="exclude-line-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="exclude-line-input" value="◆" oninput="countCharacters()"
                                style="width: 80px; padding: 3px; border: none; font-size: 12px; flex: 1;">
                            <span onclick="removeExcludeLine(this)"
                                style="color: #333; cursor: pointer; padding: 2px 5px; font-weight: bold; font-size: 14px;">✕</span>
                        </div>
                        <div class="exclude-line-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="exclude-line-input" value="□" oninput="countCharacters()"
                                style="width: 80px; padding: 3px; border: none; font-size: 12px; flex: 1;">
                            <span onclick="removeExcludeLine(this)"
                                style="color: #333; cursor: pointer; padding: 2px 5px; font-weight: bold; font-size: 14px;">✕</span>
                        </div>
                        <div class="exclude-line-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="exclude-line-input" value="□" oninput="countCharacters()"
                                style="width: 80px; padding: 3px; border: none; font-size: 12px; flex: 1;">
                            <span onclick="removeExcludeLine(this)"
                                style="color: #333; cursor: pointer; padding: 2px 5px; font-weight: bold; font-size: 14px;">✕</span>
                        </div>
                        <div class="exclude-line-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="exclude-line-input" value="//" oninput="countCharacters()"
                                style="width: 80px; padding: 3px; border: none; font-size: 12px; flex: 1;">
                            <span onclick="removeExcludeLine(this)"
                                style="color: #333; cursor: pointer; padding: 2px 5px; font-weight: bold; font-size: 14px;">✕</span>
                        </div>
                        <div class="exclude-line-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="exclude-line-input" value="//" oninput="countCharacters()"
                                style="width: 80px; padding: 3px; border: none; font-size: 12px; flex: 1;">
                            <span onclick="removeExcludeLine(this)"
                                style="color: #333; cursor: pointer; padding: 2px 5px; font-weight: bold; font-size: 14px;">✕</span>
                        </div>
                        <div class="exclude-line-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="exclude-line-input" value="ＳＥ" oninput="countCharacters()"
                                style="width: 80px; padding: 3px; border: none; font-size: 12px; flex: 1;">
                            <span onclick="removeExcludeLine(this)"
                                style="color: #333; cursor: pointer; padding: 2px 5px; font-weight: bold; font-size: 14px;">✕</span>
                        </div>
                        <div class="exclude-line-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="exclude-line-input" value="ＳＥ" oninput="countCharacters()"
                                style="width: 80px; padding: 3px; border: none; font-size: 12px; flex: 1;">
                            <span onclick="removeExcludeLine(this)"
                                style="color: #333; cursor: pointer; padding: 2px 5px; font-weight: bold; font-size: 14px;">✕</span>
                        </div>
                        <div class="exclude-line-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="exclude-line-input" value="SE" oninput="countCharacters()"
                                style="width: 80px; padding: 3px; border: none; font-size: 12px; flex: 1;">
                            <span onclick="removeExcludeLine(this)"
                                style="color: #333; cursor: pointer; padding: 2px 5px; font-weight: bold; font-size: 14px;">✕</span>
                        </div>
                        <div class="exclude-line-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="exclude-line-input" value="SE" oninput="countCharacters()"
                                style="width: 80px; padding: 3px; border: none; font-size: 12px; flex: 1;">
                            <span onclick="removeExcludeLine(this)"
                                style="color: #333; cursor: pointer; padding: 2px 5px; font-weight: bold; font-size: 14px;">✕</span>
                        </div>
                        <div class="exclude-line-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="exclude-line-input" value="トラック" oninput="countCharacters()"
                                style="width: 80px; padding: 3px; border: none; font-size: 12px; flex: 1;">
                            <span onclick="removeExcludeLine(this)"
                                style="color: #333; cursor: pointer; padding: 2px 5px; font-weight: bold; font-size: 14px;">✕</span>
                        </div>
                        <div class="exclude-line-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="exclude-line-input" value="※" oninput="countCharacters()"
                                style="width: 80px; padding: 3px; border: none; font-size: 12px; flex: 1;">
                            <span onclick="removeExcludeLine(this)"
                                style="color: #333; cursor: pointer; padding: 2px 5px; font-weight: bold; font-size: 14px;">✕</span>
                        </div>
                        <div class="exclude-line-item"
                            style="display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;">
                            <input type="text" class="exclude-line-input" value="※" oninput="countCharacters()"
                                style="width: 80px; padding: 3px; border: none; font-size: 12px; flex: 1;">
                            <span onclick="removeExcludeLine(this)"
                                style="color: #333; cursor: pointer; padding: 2px 5px; font-weight: bold; font-size: 14px;">✕</span>
                        </div>
                    </div>
                    <button onclick="addExcludeLine()"
                        style="background: #28a745; color: white; border: none; border-radius: 4px; padding: 8px 16px; font-size: 12px; margin-top: 8px;">
                        追加
                    </button>
                </div>
            </div>
        </div>

        <div class="section">
            <h3>追加オプション</h3>

            <div class="input-group">
                <label>アドリブ:</label>
                <input type="number" id="adlibSeconds" value="0" min="0" oninput="calculate()">
                <span>秒 (1秒3文字換算、5分以降は1秒5文字換算)</span>
            </div>

            <div class="input-group">
                <label>フリートーク:</label>
                <input type="number" id="freeTalkMinutes" value="0" min="0" oninput="calculate()">
                <span>分 (+400円/分)</span>
            </div>

            <div class="input-group">
                <label>クレジット非表記:</label>
                <input type="checkbox" id="noCredit" onchange="calculate()">
                <span>+5,000円</span>
            </div>

            <div class="input-group">
                <label>ファイルカット数:</label>
                <input type="number" id="fileCuts" value="0" min="0" oninput="calculate()">
                <span>カット (+30円/カット)</span>
            </div>

            <div class="input-group">
                <label>一人多役:</label>
                <input type="number" id="extraCharacters" value="0" min="0" oninput="calculate()">
                <span>役 (+1,500円/役)</span>
            </div>

            <div class="input-group">
                <label>動画合わせ(アテレコ):</label>
                <input type="checkbox" id="videoSync" onchange="calculate()">
                <span>台本料金の+25%</span>
            </div>

            <div class="input-group">
                <label>ノイズカット:</label>
                <select id="noiseCut" onchange="calculate()">
                    <option value="none">なし</option>
                    <option value="dlsite">あり：DLsite内で公開(無料)</option>
                    <option value="other">あり：DLsite外で公開(+25%)</option>
                </select>
            </div>
                    <div class="total" id="totalAmount2">
                台本: 0円 + オプション: 0円 = 合計: 0円
            </div>

            <button onclick="exportToCSV()"
                style="background: #e8f5e8; color: #2d6a4f; border: 1px solid #95d5b2; border-radius: 8px; padding: 15px; font-size: 1.2em; font-weight: bold; cursor: pointer; width: 100%; margin-top: 20px; text-align: center;">
                お見積もりをMisoca形式のcsvでダウンロード
            </button>

        </div>

            <div class="note" style="margin-top: 15px;">
                <strong>文字カウントの仕様について</strong>
                <ul>
                    <li>ト書き以外の文字のみをカウントします。</li>
                    <li>句読点「、」「。」や感情を表す記号「♡」感嘆符「！」、疑問符「？」三点リーダー「…」は息遣いの演技も含まるため見積もり計算対象とさせていただいています。</li>
                    <li>全角スペースと半角スペース及び、改行は除外しております。</li>
                    <li>2025/10/10現在スマホ版の場合はデザインが崩れるため、PC版のみ動作をサポートさせていただいています。</li>
                </ul>
            </div>
        </div>

    </div>

    <script>
        // 料金定数（簡単に変更可能）
        const RATES = {
            BASE_RATE: 1.4,           // 基本単価（円/文字）
            PREMIUM_ACTING: 10,       // 割増演技（円/文字）
            ADLIB_UNDER_5MIN_CHARS: 3, // アドリブ5分未満（文字/秒）
            ADLIB_OVER_5MIN_CHARS: 5,  // アドリブ5分以降（文字/秒）
            FREE_TALK: 400,           // フリートーク（円/分）
            NO_CREDIT: 5000,          // クレジット非表記（円）
            FILE_CUT: 30,             // ファイルカット（円/カット）
            EXTRA_CHARACTER: 1500,    // 一人多役（円/役）
            VIDEO_SYNC_PERCENT: 25,   // アテレコ/動画合わせ（%）
            NOISE_CUT_PERCENT: 25     // ノイズカット（%）
        };

        // 文字カウント機能
        function countCharacters() {
            const text = document.getElementById('scriptText').value;

            // 基本的な文字分類のカウント
            let hiragana = 0, katakana = 0, kanji = 0, alphanum = 0, symbol = 0;

            for (let char of text) {
                if (char.match(/[\u3041-\u3096]/)) {
                    hiragana++;
                } else if (char.match(/[\u30A1-\u30FA]/)) {
                    katakana++;
                } else if (char.match(/[\u4E00-\u9FAF]/)) {
                    kanji++;
                } else if (char.match(/[a-zA-Z0-9]/)) {
                    alphanum++;
                } else {
                    symbol++;
                }
            }

            // 総文字数
            const totalChars = text.length;

            // 課金対象文字数の計算
            let billableText = text;

            // 囲み文字ペアの処理
            const bracketPairItems = document.querySelectorAll('.bracket-pair-item');
            bracketPairItems.forEach(item => {
                const startChar = item.querySelector('.bracket-start-input').value;
                const endChar = item.querySelector('.bracket-end-input').value;
                if (startChar && endChar) {
                    // 特殊文字をエスケープして正規表現を作成
                    const escapedStart = startChar.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
                    const escapedEnd = endChar.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');

                    // 開始文字から終了文字までの内容を除外
                    const regex = new RegExp(escapedStart + '[^' + escapedEnd + ']*' + escapedEnd, 'g');
                    billableText = billableText.replace(regex, '');
                }
            });

            // 行ごと除外記号の処理
            const excludeLineInputs = document.querySelectorAll('.exclude-line-input');
            excludeLineInputs.forEach(input => {
                const pattern = input.value.trim();
                if (pattern) {
                    // 特殊文字をエスケープして正規表現を作成
                    const escapedPattern = pattern.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');

                    // そのパターンから始まる行全体をマッチ
                    const regex = new RegExp(escapedPattern + '[^\\r\\n]*[\\r\\n]*', 'g');
                    billableText = billableText.replace(regex, '');
                }
            });

            // 除外文字設定の処理
            const excludeCharInputs = document.querySelectorAll('.exclude-char-input');
            excludeCharInputs.forEach(input => {
                const char = input.value;
                if (char) {
                    // 特殊文字をエスケープして正規表現を作成
                    const escapedChar = char.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
                    const regex = new RegExp(escapedChar, 'g');
                    billableText = billableText.replace(regex, '');
                }
            });

            // スペースと改行を除外
            billableText = billableText.replace(/ /g, ''); // 半角スペースを除外
            billableText = billableText.replace(/　/g, ''); // 全角スペースを除外
            billableText = billableText.replace(/\r?\n/g, ''); // 改行を除外

            // 抜く文字の処理は削除されました（囲み文字除外設定と行ごと除外記号で代替）

            const billableChars = billableText.length;

            // 結果の表示
            document.getElementById('billableChars').textContent = billableChars.toLocaleString();

            // 基本料金の計算と表示
            const baseRate = parseFloat(document.getElementById('baseRate').value) || RATES.BASE_RATE;
            const basicAmount = billableChars * baseRate;
            document.getElementById('basicAmount').textContent = Math.round(basicAmount).toLocaleString();

            // 料金を再計算
            calculate();
        }

        // テキストクリア機能
        function clearText() {
            document.getElementById('scriptText').value = '';
            countCharacters();
        }

        // 文字数コピー機能
        function copyCount() {
            const billableChars = document.getElementById('billableChars').textContent;
            const text = `ト書きを除いた文字数 ： ${billableChars}文字`;

            if (navigator.clipboard) {
                navigator.clipboard.writeText(text).then(() => {
                    alert('文字数がクリップボードにコピーされました！');
                });
            } else {
                // フォールバック
                const textarea = document.createElement('textarea');
                textarea.value = text;
                document.body.appendChild(textarea);
                textarea.select();
                document.execCommand('copy');
                document.body.removeChild(textarea);
                alert('文字数がクリップボードにコピーされました！');
            }
        }

        // 行ごと除外パターンの追加機能
        function addExcludeLine() {
            const container = document.getElementById('excludeLinesContainer');
            const newItem = document.createElement('div');
            newItem.className = 'exclude-line-item';
            newItem.style.cssText = 'display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;';
            newItem.innerHTML = `
                <input type="text" class="exclude-line-input" placeholder="パターン" oninput="countCharacters(); countPremiumCharacters();"
                       style="width: 80px; padding: 3px; border: none; font-size: 12px; flex: 1;">
                <span onclick="removeExcludeLine(this)" style="color: #333; cursor: pointer; padding: 2px 5px; font-weight: bold; font-size: 14px;">✕</span>
            `;
            container.appendChild(newItem);
        }

        // 行ごと除外パターンの削除機能
        function removeExcludeLine(span) {
            const item = span.parentElement;
            item.remove();
            countCharacters(); // 削除後に再計算
            countPremiumCharacters(); // 割増演技も再計算
        }

        // 除外文字の追加機能
        function addExcludeChar() {
            const container = document.getElementById('excludeCharsContainer');
            const newItem = document.createElement('div');
            newItem.className = 'exclude-char-item';
            newItem.style.cssText = 'display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;';
            newItem.innerHTML = `
                <input type="text" class="exclude-char-input" placeholder="文字" oninput="countCharacters()"
                       style="width: 30px; padding: 2px; border: none; font-size: 12px; text-align: center; flex: 1;">
                <span onclick="removeExcludeChar(this)" style="color: #333; cursor: pointer; padding: 2px 3px; font-weight: bold; font-size: 12px;">✕</span>
            `;
            container.appendChild(newItem);
        }

        // 除外文字の削除機能
        function removeExcludeChar(span) {
            const item = span.parentElement;
            item.remove();
            countCharacters(); // 削除後に再計算
        }

        // 囲み文字ペアの追加機能
        function addBracketPair() {
            const container = document.getElementById('bracketPairsContainer');
            const newItem = document.createElement('div');
            newItem.className = 'bracket-pair-item';
            newItem.style.cssText = 'display: flex; align-items: center; background: white; border: 1px solid #ddd; border-radius: 4px; padding: 3px;';
            newItem.innerHTML = `
                <input type="text" class="bracket-start-input" placeholder="開始" oninput="countCharacters(); countPremiumCharacters();"
                       style="width: 20px; padding: 2px; border: none; font-size: 12px; text-align: center;">
                <input type="text" class="bracket-end-input" placeholder="終了" oninput="countCharacters(); countPremiumCharacters();"
                       style="width: 20px; padding: 2px; border: none; font-size: 12px; text-align: center;">
                <span onclick="removeBracketPair(this)" style="color: #333; cursor: pointer; padding: 2px 3px; font-weight: bold; font-size: 12px;">✕</span>
            `;
            container.appendChild(newItem);
        }

        // 囲み文字ペアの削除機能
        function removeBracketPair(span) {
            const item = span.parentElement;
            item.remove();
            countCharacters(); // 削除後に再計算
            countPremiumCharacters(); // 割増演技も再計算
        }

        // 割増演技文字カウント機能
        function countPremiumCharacters() {
            const text = document.getElementById('premiumScriptText').value;

            // 総文字数
            const totalChars = text.length;

            // 課金対象文字数の計算
            let billableText = text;

            // 囲み文字ペアの処理
            const bracketPairItems = document.querySelectorAll('.bracket-pair-item');
            bracketPairItems.forEach(item => {
                const startChar = item.querySelector('.bracket-start-input').value;
                const endChar = item.querySelector('.bracket-end-input').value;
                if (startChar && endChar) {
                    // 特殊文字をエスケープして正規表現を作成
                    const escapedStart = startChar.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
                    const escapedEnd = endChar.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');

                    // 開始文字から終了文字までの内容を除外
                    const regex = new RegExp(escapedStart + '[^' + escapedEnd + ']*' + escapedEnd, 'g');
                    billableText = billableText.replace(regex, '');
                }
            });

            // 行ごと除外記号の処理
            const excludeLineInputs = document.querySelectorAll('.exclude-line-input');
            excludeLineInputs.forEach(input => {
                const pattern = input.value.trim();
                if (pattern) {
                    // 特殊文字をエスケープして正規表現を作成
                    const escapedPattern = pattern.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');

                    // そのパターンから始まる行全体をマッチ
                    const regex = new RegExp(escapedPattern + '[^\\r\\n]*[\\r\\n]*', 'g');
                    billableText = billableText.replace(regex, '');
                }
            });

            // 除外文字設定の処理
            const excludeCharInputs = document.querySelectorAll('.exclude-char-input');
            excludeCharInputs.forEach(input => {
                const char = input.value;
                if (char) {
                    // 特殊文字をエスケープして正規表現を作成
                    const escapedChar = char.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
                    const regex = new RegExp(escapedChar, 'g');
                    billableText = billableText.replace(regex, '');
                }
            });

            // スペースと改行を除外
            billableText = billableText.replace(/ /g, ''); // 半角スペースを除外
            billableText = billableText.replace(/　/g, ''); // 全角スペースを除外
            billableText = billableText.replace(/\r?\n/g, ''); // 改行を除外

            const billableChars = billableText.length;

            // 結果の表示
            document.getElementById('premiumBillableChars').textContent = billableChars.toLocaleString();

            // 割増料金の計算と表示
            const premiumRate = parseFloat(document.getElementById('premiumRate').value) || RATES.PREMIUM_ACTING;
            const premiumAmount = billableChars * premiumRate;
            document.getElementById('premiumAmount').textContent = Math.round(premiumAmount).toLocaleString();

            // 料金を再計算
            calculate();
        }

        // 割増演技テキストクリア機能
        function clearPremiumText() {
            document.getElementById('premiumScriptText').value = '';
            countPremiumCharacters();
        }

        // 割増演技文字数コピー機能
        function copyPremiumCount() {
            const billableChars = document.getElementById('premiumBillableChars').textContent;
            const text = `割増演技対象文字数: ${billableChars}文字`;

            if (navigator.clipboard) {
                navigator.clipboard.writeText(text).then(() => {
                    alert('割増演技文字数がクリップボードにコピーされました！');
                });
            } else {
                const textarea = document.createElement('textarea');
                textarea.value = text;
                document.body.appendChild(textarea);
                textarea.select();
                document.execCommand('copy');
                document.body.removeChild(textarea);
                alert('割増演技文字数がクリップボードにコピーされました！');
            }
        }

        function calculate() {
            // 基本文字数と料金
            const billableCharsText = document.getElementById('billableChars').textContent.replace(/,/g, '');
            const characters = parseInt(billableCharsText) || 0;
            const baseRate = parseFloat(document.getElementById('baseRate').value) || RATES.BASE_RATE;

            // 基本料金
            let scriptTotal = characters * baseRate;

            // 割増演技文字数と料金
            const premiumBillableCharsText = document.getElementById('premiumBillableChars').textContent.replace(/,/g, '');
            const premiumCharacters = parseInt(premiumBillableCharsText) || 0;
            const premiumRate = parseFloat(document.getElementById('premiumRate').value) || RATES.PREMIUM_ACTING;
            scriptTotal += premiumCharacters * premiumRate;

            // オプション料金の初期化
            let optionTotal = 0;

            // アドリブ（文字換算）
            const adlibSeconds = parseInt(document.getElementById('adlibSeconds').value) || 0;
            if (adlibSeconds > 0) {
                const fiveMinutes = 300; // 5分 = 300秒
                let adlibChars = 0;
                if (adlibSeconds <= fiveMinutes) {
                    adlibChars = adlibSeconds * RATES.ADLIB_UNDER_5MIN_CHARS;
                } else {
                    adlibChars = fiveMinutes * RATES.ADLIB_UNDER_5MIN_CHARS;
                    adlibChars += (adlibSeconds - fiveMinutes) * RATES.ADLIB_OVER_5MIN_CHARS;
                }
                optionTotal += adlibChars * baseRate;
            }

            // フリートーク
            const freeTalkMinutes = parseInt(document.getElementById('freeTalkMinutes').value) || 0;
            optionTotal += freeTalkMinutes * RATES.FREE_TALK;

            // クレジット非表記
            if (document.getElementById('noCredit').checked) {
                optionTotal += RATES.NO_CREDIT;
            }

            // ファイルカット
            const fileCuts = parseInt(document.getElementById('fileCuts').value) || 0;
            optionTotal += fileCuts * RATES.FILE_CUT;

            // 一人多役
            const extraCharacters = parseInt(document.getElementById('extraCharacters').value) || 0;
            optionTotal += extraCharacters * RATES.EXTRA_CHARACTER;

            // 台本料金部分を保存（基本+割増、パーセント追加用）
            const scriptAmount = (characters * baseRate) + (premiumCharacters * premiumRate);
            let percentageAddition = 0;

            // アテレコ/動画合わせ
            if (document.getElementById('videoSync').checked) {
                percentageAddition += scriptAmount * (RATES.VIDEO_SYNC_PERCENT / 100);
            }

            // ノイズカット
            const noiseCut = document.getElementById('noiseCut').value;
            if (noiseCut === 'other') {
                percentageAddition += scriptAmount * (RATES.NOISE_CUT_PERCENT / 100);
            }

            optionTotal += percentageAddition;

            const total = scriptTotal + optionTotal;

            const formattedTotal = `台本: ${Math.round(scriptTotal).toLocaleString()}円 + オプション: ${Math.round(optionTotal).toLocaleString()}円 = 合計: ${Math.round(total).toLocaleString()}円`;
            document.getElementById('totalAmount').textContent = formattedTotal;
            document.getElementById('totalAmount2').textContent = formattedTotal;
        }

        // ダブルクォート・シングルクォートの初期設定
        function initializeBracketPairs() {
            const bracketItems = document.querySelectorAll('.bracket-pair-item');
            const bracketPairs = [
                ['「', '」'],
                ['『', '』'],
                ['\u201C', '\u201D'],  // ダブルクォート（Unicode）
                ['\u2018', '\u2019'],  // シングルクォート（Unicode）
                ['｢', '｣'],
                ['〈', '〉'],
                ['《', '》'],
                ['【', '】'],
                ['〔', '〕'],
                ['≪', '≫'],
                ['（', '）'],
                ['(', ')']
            ];

            bracketItems.forEach((item, index) => {
                if (bracketPairs[index]) {
                    const startInput = item.querySelector('.bracket-start-input');
                    const endInput = item.querySelector('.bracket-end-input');
                    if (startInput && endInput) {
                        startInput.value = bracketPairs[index][0];
                        endInput.value = bracketPairs[index][1];
                    }
                }
            });
        }

        // CSV用のエスケープ関数
        function escapeCSV(value) {
            if (value === null || value === undefined || value === '') {
                return '';
            }
            const str = String(value);
            // ダブルクォートを2つにエスケープ
            return str.replace(/"/g, '""');
        }

        // CSV書き出し機能
        function exportToCSV() {
            // 現在の日付を取得
            const today = new Date();
            const invoiceDate = `${today.getFullYear()}/${String(today.getMonth() + 1).padStart(2, '0')}/${String(today.getDate()).padStart(2, '0')}`;

            // 各種データを取得
            const billableCharsText = document.getElementById('billableChars').textContent.replace(/,/g, '');
            const characters = parseInt(billableCharsText) || 0;
            const premiumBillableCharsText = document.getElementById('premiumBillableChars').textContent.replace(/,/g, '');
            const premiumCharacters = parseInt(premiumBillableCharsText) || 0;
            const baseRate = parseFloat(document.getElementById('baseRate').value) || RATES.BASE_RATE;
            const premiumRate = parseFloat(document.getElementById('premiumRate').value) || RATES.PREMIUM_ACTING;
            const adlibSeconds = parseInt(document.getElementById('adlibSeconds').value) || 0;
            const freeTalkMinutes = parseInt(document.getElementById('freeTalkMinutes').value) || 0;
            const noCredit = document.getElementById('noCredit').checked;
            const fileCuts = parseInt(document.getElementById('fileCuts').value) || 0;
            const extraCharacters = parseInt(document.getElementById('extraCharacters').value) || 0;
            const videoSync = document.getElementById('videoSync').checked;
            const noiseCut = document.getElementById('noiseCut').value;

            // 品目データの配列（最大20品目）
            let items = Array(20).fill(null).map(() => ({
                name: '',
                quantity: '',
                unit: '',
                price: '',
                taxRate: '',
                taxExempt: ''
            }));

            let itemIndex = 0;

            // 品目1: 音声収録料（文字数がある場合）
            if (characters > 0) {
                // 料金タイプに応じて品目名を変更
                const itemName = baseRate === 1.4 ? '音声収録料(仲介経由)' : '音声収録料(メール経由)';
                items[itemIndex] = {
                    name: itemName,
                    quantity: characters,
                    unit: '文字',
                    price: baseRate,
                    taxRate: '',
                    taxExempt: '1'
                };
                itemIndex++;
            }

            // 品目2: 割増演技（文字数がある場合）
            if (premiumCharacters > 0) {
                items[itemIndex] = {
                    name: '割増演技',
                    quantity: premiumCharacters,
                    unit: '文字',
                    price: premiumRate,
                    taxRate: '',
                    taxExempt: '1'
                };
                itemIndex++;
            }

            // 品目3: アドリブ（秒数がある場合）
            if (adlibSeconds > 0) {
                const fiveMinutes = 300;
                let adlibChars = 0;
                if (adlibSeconds <= fiveMinutes) {
                    adlibChars = adlibSeconds * RATES.ADLIB_UNDER_5MIN_CHARS;
                } else {
                    adlibChars = fiveMinutes * RATES.ADLIB_UNDER_5MIN_CHARS;
                    adlibChars += (adlibSeconds - fiveMinutes) * RATES.ADLIB_OVER_5MIN_CHARS;
                }
                const adlibAmount = Math.round(adlibChars * baseRate);
                items[itemIndex] = {
                    name: 'アドリブ',
                    quantity: adlibSeconds,
                    unit: '秒',
                    price: Math.round(adlibAmount / adlibSeconds * 100) / 100,
                    taxRate: '',
                    taxExempt: '1'
                };
                itemIndex++;
            }

            // 品目4: フリートーク（分数がある場合）
            if (freeTalkMinutes > 0) {
                items[itemIndex] = {
                    name: 'フリートーク',
                    quantity: freeTalkMinutes,
                    unit: '分',
                    price: RATES.FREE_TALK,
                    taxRate: '',
                    taxExempt: '1'
                };
                itemIndex++;
            }

            // 品目5: クレジット非表記（チェックされている場合）
            if (noCredit) {
                items[itemIndex] = {
                    name: 'クレジット非表記',
                    quantity: 1,
                    unit: '式',
                    price: RATES.NO_CREDIT,
                    taxRate: '',
                    taxExempt: '1'
                };
                itemIndex++;
            }

            // 品目6: ファイルカット（カット数がある場合）
            if (fileCuts > 0) {
                items[itemIndex] = {
                    name: 'ファイルカット',
                    quantity: fileCuts,
                    unit: 'カット',
                    price: RATES.FILE_CUT,
                    taxRate: '',
                    taxExempt: '1'
                };
                itemIndex++;
            }

            // 品目7: 一人多役（役数がある場合）
            if (extraCharacters > 0) {
                items[itemIndex] = {
                    name: '一人多役',
                    quantity: extraCharacters,
                    unit: '役',
                    price: RATES.EXTRA_CHARACTER,
                    taxRate: '',
                    taxExempt: '1'
                };
                itemIndex++;
            }

            // 品目8: アテレコ/動画合わせ（チェックされている場合）
            if (videoSync) {
                const scriptAmount = (characters * baseRate) + (premiumCharacters * premiumRate);
                const videoSyncAmount = Math.round(scriptAmount * (RATES.VIDEO_SYNC_PERCENT / 100));
                items[itemIndex] = {
                    name: '動画合わせ(アテレコ)',
                    quantity: 1,
                    unit: '式',
                    price: videoSyncAmount,
                    taxRate: '',
                    taxExempt: '1'
                };
                itemIndex++;
            }

            // 品目9: ノイズカット（DLsite外の場合）
            if (noiseCut === 'other') {
                const scriptAmount = (characters * baseRate) + (premiumCharacters * premiumRate);
                const noiseCutAmount = Math.round(scriptAmount * (RATES.NOISE_CUT_PERCENT / 100));
                items[itemIndex] = {
                    name: 'ノイズカット(DLsite外で公開)',
                    quantity: 1,
                    unit: '式',
                    price: noiseCutAmount,
                    taxRate: '',
                    taxExempt: '1'
                };
                itemIndex++;
            }

            // 品目10: ノイズカット（DLsite内の場合）
            if (noiseCut === 'dlsite') {
                items[itemIndex] = {
                    name: 'ノイズカット(DLsite内で公開)',
                    quantity: 1,
                    unit: '式',
                    price: 0,
                    taxRate: '',
                    taxExempt: '1'
                };
                itemIndex++;
            }

            // CSVヘッダー（固定で品目1〜20まで + 旧テンプレート項目）
            let csvHeader = '"請求日","請求番号","件名","取引先管理コード","消費税設定","お支払い期限"';
            for (let i = 1; i <= 20; i++) {
                csvHeader += `,"品目${i}","数量${i}","単位${i}","単価${i}","消費税率${i}","非課税フラグ${i}"`;
            }
            // 旧テンプレート用の項目を追加
            csvHeader += ',"請求先名称","敬称","担当者","送付先郵便番号","送付先住所1","送付先住所2","送付先名前1","送付先名前2","送付先名前3","送付先名前4","送付先敬称"';

            // データ行を配列で構築
            let dataFields = [];
            dataFields.push(invoiceDate); // 請求日
            dataFields.push(''); // 請求番号
            dataFields.push('声優依頼に関する報酬のご請求につきまして'); // 件名
            dataFields.push(''); // 取引先管理コード
            dataFields.push('EXEMPT'); // 消費税設定（免税）
            dataFields.push(''); // お支払い期限

            // 品目データを追加（20品目分）
            for (let i = 0; i < 20; i++) {
                const item = items[i];
                dataFields.push(escapeCSV(item.name));
                dataFields.push(escapeCSV(item.quantity));
                dataFields.push(escapeCSV(item.unit));
                dataFields.push(escapeCSV(item.price));
                dataFields.push(escapeCSV(item.taxRate));
                dataFields.push(escapeCSV(item.taxExempt));
            }

            // 旧テンプレート用の項目を追加
            dataFields.push('依頼者名'); // 請求先名称
            dataFields.push('様'); // 敬称
            dataFields.push(''); // 担当者
            dataFields.push(''); // 送付先郵便番号
            dataFields.push(''); // 送付先住所1
            dataFields.push(''); // 送付先住所2
            dataFields.push(''); // 送付先名前1
            dataFields.push(''); // 送付先名前2
            dataFields.push(''); // 送付先名前3
            dataFields.push(''); // 送付先名前4
            dataFields.push(''); // 送付先敬称

            // データ行を作成
            let dataRow = dataFields.map(field => `"${field}"`).join(',');

            // デバッグ: 列数確認
            const headerCols = csvHeader.split(',').length;
            const dataCols = dataRow.split(',').length;
            console.log('ヘッダー列数:', headerCols);
            console.log('データ列数:', dataCols);

            if (headerCols !== dataCols) {
                alert(`エラー: ヘッダー列数(${headerCols})とデータ列数(${dataCols})が一致しません。`);
                return;
            }

            const csvContent = csvHeader + '\r\n' + dataRow;

            // UTF-8で保存(BOMなし)
            const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });

            // ダウンロード
            const link = document.createElement('a');
            const url = URL.createObjectURL(blob);
            link.setAttribute('href', url);
            link.setAttribute('download', `見積もり_${today.getFullYear()}${String(today.getMonth() + 1).padStart(2, '0')}${String(today.getDate()).padStart(2, '0')}.csv`);
            link.style.visibility = 'hidden';
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        }

        // 初期計算
        countCharacters();
        countPremiumCharacters();
        calculate();
    </script>
</body>

</html>
