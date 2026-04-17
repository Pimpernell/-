# 进销存出入库管理系统
#### 本进销存出入库管理系统，以基础信息表为数据核心，整合商品出入库全流程管理、数据查询、库存监控与账务对账功能，实现商品流转数据规范化管理，方便快速查询业务信息、实时把控库存状态。

### 核心模块功能介绍
系统涵盖表单录入、出入库明细、表单查询、对账单查询、实时库存、基础信息表六大核心模块：
先在基础信息表录入产品、客户、供应商、业务类型等基础数据，再通过表单录入完成出入库单据提交，数据自动同步至出入库明细；支持按产品、日期、往来单位精准查询业务数据与对账信息，实时库存可查看商品结存、成本及盘盈亏情况，全程实现业务数据可录、可查、可追溯。

### 技术栈与工具
- 核心开发平台 |Microsoft Excel
- 数据自动化处理 |Excel内置函数 + 宏命令操作
- 核心开发语言 |VBA \宏命令
- 交互界面构建 |表单控件 + 数据验证 + 条件格式

### 界面功能效果图展示
#### 首页
<img width="2559" height="1305" alt="image" src="https://github.com/user-attachments/assets/4b6cb7ab-f0b3-48c7-9a27-f15036aff687" />

#### 表单录入
<img width="2454" height="1266" alt="image" src="https://github.com/user-attachments/assets/fc6e857a-3b68-4bce-bf8d-baf1b3275ec4" />

#### 出入库明细
<img width="2472" height="1274" alt="image" src="https://github.com/user-attachments/assets/5b180976-fce3-420f-9ca5-4ace2b21c5bc" />

#### 单品查询
<img width="2451" height="1296" alt="image" src="https://github.com/user-attachments/assets/54a44420-cb34-48c5-a5bf-c3ddb0ce5d29" />

#### 对账单查询
<img width="2445" height="1305" alt="image" src="https://github.com/user-attachments/assets/fad52142-ff98-4c6d-9999-50a9ed369265" />

#### 实时库存
<img width="2448" height="1302" alt="image" src="https://github.com/user-attachments/assets/5bdc9206-73ec-4fba-97aa-1af5671efbc4" />

#### 基础信息
<img width="2451" height="1299" alt="image" src="https://github.com/user-attachments/assets/d6b4b036-918c-43eb-baf1-b3eceb1e4c2f" />

#### 操作视频
https://github.com/user-attachments/assets/988501fe-af81-4690-b73c-4a825f9fc9c0

#### 宏命令
```python
/**
 * 圆角矩形1_Click Macro
 * 保存按钮的功能
 */
function 圆角矩形1_Click()
{
	
	Application.ScreenUpdating = false; // 关闭屏幕刷新，提升性能
    
    try {
    	// 源表
		let sourceSheet1 = Application.Worksheets.Item("表单录入");
		// 目标存档表
		let storeSheet= Application.Worksheets.Item("出入库明细");
		
		var rE, i;
		let arr = ["",undefined,null]
		if(!arr.includes(sourceSheet1.Range("E9").Value2) && !arr.includes(sourceSheet1.Range("M9").Value2) && !arr.includes(sourceSheet1.Range("E22").Value2)){
		    for (i = 11; i <= 20; i++) {
		        if (sourceSheet1.Cells(i, 5).Value2 != "" && sourceSheet1.Cells(i, 5).Value2 !=undefined && sourceSheet1.Cells(i, 5).Value2 !=null) {
		            // 获取出入库明细表的最后非空行
		            rE = storeSheet.Range("d" + storeSheet.Rows.Count).End(xlUp).Row;
		            console.log(rE)
		            if (rE <= 8) rE = 8;
		            
		            // 写入基础信息
		            storeSheet.Cells(rE + 1, 4).Value2 = sourceSheet1.Range("M9").Value2;  // 日期
		            storeSheet.Cells(rE + 1, 5).Value2 = sourceSheet1.Range("N8").Value2;  // 单号
		            storeSheet.Cells(rE + 1, 6).Value2 = sourceSheet1.Range("E9").Value2; // 供货
		            storeSheet.Cells(rE + 1, 7).Value2 = sourceSheet1.Cells(i, 5).Value2;  // 产品编号
		            storeSheet.Cells(rE + 1, 8).Value2 = sourceSheet1.Cells(i, 6).Value2;  // 名称
		            storeSheet.Cells(rE + 1, 9).Value2 = sourceSheet1.Cells(i, 7).Value2;  // 规格
		            storeSheet.Cells(rE + 1, 10).Value2 = sourceSheet1.Cells(i, 8).Value2; // 型号
		            storeSheet.Cells(rE + 1, 11).Value2 = sourceSheet1.Cells(i, 9).Value2; // 单位
		            storeSheet.Cells(rE + 1, 12).Value2 = sourceSheet1.Cells(i, 10).Value2; // 库位
		            
		            // 根据入库/出库类型写入不同列
		            if (sourceSheet1.Range("Q10").Value2 == "入库") {
		                storeSheet.Cells(rE + 1, 13).Value2 = sourceSheet1.Cells(i, 11).Value2; // 入库数量
		                storeSheet.Cells(rE + 1, 14).Value2 = sourceSheet1.Cells(i, 12).Value2; // 单价
		                storeSheet.Cells(rE + 1, 15).Value2 = sourceSheet1.Cells(i, 13).Value2; // 金额
		            }
		            
		            if (sourceSheet1.Range("Q10").Value2 == "出库") {
		                storeSheet.Cells(rE + 1, 16).Value2 = sourceSheet1.Cells(i, 11).Value2; // 出库数量
		                storeSheet.Cells(rE + 1, 17).Value2 = sourceSheet1.Cells(i, 12).Value2; // 单价
		                storeSheet.Cells(rE + 1, 18).Value2 = sourceSheet1.Cells(i, 13).Value2; // 金额
		            }
		            
		            // 写入经办人和备注
		            storeSheet.Cells(rE + 1, 19).Value2 = sourceSheet1.Range("E22").Value2; // 经办人
		            storeSheet.Cells(rE + 1, 20).Value2 = sourceSheet1.Cells(i, 14).Value2; // 备注
		        }
		    }
		    alert("已完成！");
	    } else {
	    	 alert("请填入：" + sourceSheet1.Range("D9").Value2 + "," + sourceSheet1.Range("L9").Value2+ "," + sourceSheet1.Range("S9").Value2);
	    }
	   
    }
	catch (err) {
        alert("错误：" + err.message);
    } finally {
        Application.ScreenUpdating = true;
    }
	
}
/**
 * 圆角矩形2_ClickReset Macro、
 * 清除按钮的功能
 */
function 圆角矩形2_ClickReset()
{
  // 源表
	let sourceSheet1 = Application.Worksheets.Item("表单录入");
	// E9 供应商
	sourceSheet1.Range("E9").Value2 = ""
	// 日期
	sourceSheet1.Range("M9").Value2 = ""
    //	产品编码
	sourceSheet1.Range("E11:E20").Value2 = ""
	//	数量
	sourceSheet1.Range("K11:K20").Value2 = ""
	//	备注
	sourceSheet1.Range("N11:N20").Value2 = ""
	//	经办人
	sourceSheet1.Range("E22").Value2 = ""
	alert("已完成！");
}
```
#### 核心公式
```python
查找数据匹配
=IFERROR(VLOOKUP($E11,基础信息!$K:$U,2,0),"")
辅助查询，日期辅助
=IF(AND($G9=单品查询!$N$3,$D9>=单品查询!$P$4,$D9<=单品查询!$R$4),1,0)+N($W8)
=IF(AND($F9=对账单查询!$N$3,$D9>=对账单查询!$P$4,$D9<=对账单查询!$R$4),1,0)+N($X8)
数量统计公式
=VLOOKUP($N$3,基础信息!$K:$U,7,0)+SUMIFS(出入库明细!$M:$M,出入库明细!$G:$G,$N$3,出入库明细!$D:$D,"<"&$P$4)-SUMIFS(出入库明细!$P:$P,出入库明细!$G:$G,$N$3,出入库明细!$D:$D,"<"&$R$4)
入库数量公式
=SUMIFS(出入库明细!$M:$M,出入库明细!$G:$G,$N$3,出入库明细!$D:$D,">="&$P$4,出入库明细!$D:$D,"<="&$R$4)
总数量公式
=SUMIFS(出入库明细!$M:$M,出入库明细!$F:$F,$N$3,出入库明细!$D:$D,">="&$P$4,出入库明细!$D:$D,"<="&$R$4)-SUMIFS(出入库明细!$P:$P,出入库明细!$F:$F,$N$3,出入库明细!$D:$D,">="&$P$4,出入库明细!$D:$D,"<="&$R$4)
```

#### 名称管理器
<img width="999" height="798" alt="image" src="https://github.com/user-attachments/assets/bd700089-f621-41e4-94b6-f6f1f3673d4c" />
#### 数据有效性
<img width="557" height="599" alt="image" src="https://github.com/user-attachments/assets/54cb21c9-0e91-4305-a0b9-649d8db0b31e" />

