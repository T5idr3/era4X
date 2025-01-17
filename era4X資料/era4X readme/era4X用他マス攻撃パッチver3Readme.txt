era4X用他マス攻撃パッチver3

◇パッチの目的
射程4の武器で隣のマスに攻撃できる様にする。
とりあえずは攻撃場所の指定との兼ね合いや参考に出来る指定座標への自動航行があったので、クリックして座標を指定しないと遠隔攻撃コマンドは出ない様になっています。

(無抵抗で攻撃を受け続ける理由もないので)敵艦隊が健在の場合、攻撃元へと突っ込んでくる様にしました。

NEW! 他マス攻撃を受けた敵艦が他マス攻撃能力を持っている場合、敵も他マス攻撃を行う様にしました。
(当該艦のミッションに設定すればプレイヤーからの攻撃が無くとも最初から他マス攻撃先を探す状態にもできます)


◇具体的な変更点

SLG.ERHに追加
#DIM CONST 攻撃不能 = 6

戦闘中に攻撃不能である事を示す定数です。
遠隔攻撃における反撃・移動はSLGターンを消費して行うべき行動(遠隔攻撃を受けた分だけ追加で戦闘・反撃できるのは変では？)なので、被攻撃側は攻撃の代わりに攻撃不能になります。(撤退は勝敗条件に関わるので「勝敗には関わらないけど攻撃はできない」攻撃不能状態の代替にはなりません)

#DIM OTHER_GRID_ATTACK_FLAG, MAX_SECTOR, MAP_WIDTH, MAP_HEIGHT

NEW! 各マス毎に・各ビットに対応した勢力の他マス攻撃フラグを保存する為の配列です。
NEW! @DO_SELF_MISSIONでフラグが立って、@SLG_AIでフラグが下りる様になっているので保存される必要はない(処理中にセーブされる事はない)はずです。

#DIM CONST 遠隔攻撃 = 7

NEW! 船のミッション(遠隔攻撃)を示す定数です。

@SLG_AI に追加
NEW! #DIM SHIP_ID
NEW! 他マス攻撃処理を追加しました。

@DO_SELF_MISSION
NEW! 遠隔攻撃フラグを持つ船が居るマスに各国家毎に他マス攻撃フラグを立てる様にしました。

@NEAREST_MULTIPLE_ANY_COUNTRY_SHIP_CHECK
NEW! 多勢力指定近傍船検索関数(近すぎ判定追加版)
NEW! @NEAREST_ANY_COUNTRY_SHIP_CHECKから一度に全勢力をも指定に出来るようにしつつ、近すぎ判定を入れる事で適度に離れた対象も選べる様にしたものです。

@FURTHEST_MULTIPLE_ANY_COUNTRY_SHIP_CHECK
NEW! 多勢力指定遠方船検索関数
NEW! @NEAREST_MULTIPLE_ANY_COUNTRY_SHIP_CHECKの不等号の向きを変えたりして一番遠方の船/一定の距離内で一番遠方の船を選べる様にしたものです。

@NEAREST_ANY_COUNTRY_SHIP_CHECK
NEW! 更新条件のミスを修正

追加ファイルOTHER_GRID_ATTACK.ERB

大体はコメントに書いてある通りです。
@OTHER_GRID_ATTACKは@BATTLEから移動と2番目の勢力の行動を省いて、ターン制限を加えた感じの他マスへの攻撃の本体です。攻撃などは@BATTLEと全く同じ関数を利用しています。

@OTHER_GRID_TARGETINGは@COMBATと役割は大体同じですが、攻撃側の前提条件の確認に関してはこれを呼ぶ前の段階でやってる事を前提にしています(「攻撃側の射程内に居るはず」の防御側の場所が指定されている以上、正常に動作している場合には無駄に再確認するだけになるのでは)

@OTHER_GRID_ATTACK
NEW! プレイヤー以外で遠隔攻撃の対象にされた場合に元々任務が無くて・大破もしていない船でHPが一定割合以下になっていない場合には反撃の為に移動or遠隔攻撃を任務とする様にしました。
NEW! その他表示回りの調整・修正など

@SLG_SHOPに「遠隔攻撃」の表示とその処理を追加
NEW! 他マス攻撃後にCALL RETIRE_BROKEN_SHIPを呼ぶように(「HP0の船に襲われる」のを回避するのに必要)

追加ファイルBITWISE_OPERATION.ERB
NEW! ビット演算関係の関数を入れる為のファイルとして新しく用意しました。

@ENEMY_COUNTRY_BITWISE_OPERATION
NEW! 各ビットを各勢力に対応させて・関数に与えられた勢力にとっての敵に当たるかどうかを1つの変数に纏めて返す関数です。



◇備考
@OTHER_GRID_ATTACKが距離4の入ったデータを表示用関数に送るとUIが崩れるので暫定的に距離データにUI表示用のダミーが存在しています。

暫定的に処理上は射程5以降の武器があるなら2マス先以降にも攻撃可能になる(はず)になっています。

暫定的に特に指定が無い場合には「1SLGターンを使って3戦闘ターン分の攻撃を行う」処理になっています。
(通常の戦闘から移動などがSLGターン側に移動した格好です)

暫定的に(他マスのそれを回収しに行けるとは考え難いので)報酬は手に入りません。(前回更新時には表記忘れ)

2023/01/18の更新で指揮官選択処理である@COMMANDER_ELECTIONにメッセージが追加されたので
暫定的な対応として旧処理をそのままコピーした「@OLD_COMMANDER_ELECTION」を使用する事でメッセージを迂回しています。

◇更新履歴
2023/01/28版
敵艦隊が健在かつ4射程武器を持つ場合には敵が「自身らの行動として」他マス攻撃を行おうとする様に
その他修正など

2023/01/25版
敵艦隊が健在の場合には敵が「自身らの行動として」移動して戦闘をしようとする様に
その他2023/01/18の更新で行われた戦闘周りの刷新に絡んでの細かい修正など

2023/01/20版
2023/01/18の更新で行われた戦闘周りの刷新に一応対応
最終ターンの結果を見られる様に処理を追加

2023/01/15版
初期版を公開