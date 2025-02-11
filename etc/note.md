
```C
struct SDT_Label
{
	uint8_t[??] aLabelName; // end with '\0', enc-> xor 0xFF
	uint32_t uiLableRVA; // FOA = uiHeaderSize + uiLableRVA :004521B7
};

struct SDT_HDR_Info
{
	uint32_t uiHDRSize;
	uint32_t uiUn0;
	uint32_t uiUn1;
	uint32_t uiLabelCount;
	uint32_t uiCheckDataFOA;
};

struct SDT_HDR
{
	SDT_HDR_Info
	SDT_Label[SDT_HDR_Info.uiLabelCount]
	uint8_t aCheckData[9]; // end with '\0'
};

struct SDT
{
	SDT_HDR
	// Code
}


entry_ptr = [this + 4] -> the first sdt entry

#pragma pack(push, 1)
struct VAL_Script
{
  uint32_t *pNextSDT;
  uint16_t usScriptID;
  uint16_t usUn0;
  uint32_t *pData;
  uint32_t uiSize;
  uint32_t *pHdrSize;
  uint32_t *pLabelCount;
  uint32_t *pLabelIndex;
  uint32_t *pUn0;
  uint32_t *pUnThis0;
  uint32_t *pUn1;
  uint32_t *pUnThis1;
  uint32_t uiNull;
  uint32_t uiUnThis2;
  uint32_t uiUnThis3;
};
#pragma pack(pop)

#pragma pack(push, 1)
struct VAL_Context
{
  uint16_t usScriptID;
  uint16_t usUn0;
  VAL_Script *pScript;
  uint32_t uiPC;
  uint32_t uiLine;
  uint32_t uiUn0;
};
#pragma pack(pop)

struct VAL_Context_Ptr
{
  VAL_Context *Ptr;
  uint32_t Un0;
  uint32_t Un1;
};

struct VAL_VM
{
  uint32_t uiUn0;
  VAL_Script *pScriptList;
  uint8_t aUn1[4188];
  uint16_t uiContextPtrIndex;
  uint8_t aUn2[262];
  VAL_Context_Ptr aContextPtr[255];
};

```



```
label_index_offset -> VAL::VM::GetCallOffset 
VAL::VM::GetCallOffset
	从标签索引开始偏移，并获取新的文件偏移

OP: 0x0A44 
Name: FarCallLabel 
Address: 0x00468AE1
Formate: [word:op][byte:type][dword:script_id][byte:type][str(xor(0xFF)):label_name]
Example: 440A 00 01000000 08 B39E9D9A93A0B89E929AAC8B9E8D8BFF

// 跳转到指定脚本
OP: 0x0A48
Name: JMP
Address: 0x0046886D
Formate: [word:op][byte:un][dword:script_id]
Example: 480A 00 02000000

// 依据label_index_offset在标签索引表中找到要跳转的FOA，设置给PC
OP: 0x0A42
Name: GOTO
Address: 0x0046886D
Formate: [word:op][dword:label_index_offset]
Example: 0A42 61000000

OP: 0x0A43
Name: Call
Address: 0x004688EB
Formate: [word:op][dword:label_index_offset]

OP: 0x0A3D
Name: if{
Address: 0x004684A4
Formate: 

OP: 0x0A17
Name: SubProcLabel
Address: 0x00465330
Formate: [word:op][byte:type][dword:][byte:type][dword:script_id][byte:type][str(xor(0xFF)):proc_name]

// 文本框对话字符串
OP: 0x0E01
Name: MSGWNDTEXT
Address: 
Formate: [word:op][wdord:un][dword:line_number][str(xor(0xFF)):msg_text]
Example: 010E 1111 4C040000 7EBF7E9C7E9C692575907D207D16716167B17EBDFF

// 文本框人物名字符串
OP: 0x0E00
Name: MSGWNDNAME
Address: 
Formate: [word:op][str(xor(0xFF)):NameString]
Example: 000E 7E86716167B17E85FF

// 设置分支选项
OP: 0x0E1C
Name: SelEXSet
Address: 0x0046E612
Formate: [word:op][dword:un(always 0x00000000)][byte:type][str(xor(0xFF)):select_text][byte:type][str(xor(0xFF)):select_text][byte:end_flag]
Example: 1C0E 00000000 08 7E8A7CBE7C747C927D336A1D724D7D367D377D167E89FF 08 7E8A7CBE7C747C927D336A1D724D7D367D377D187D377D5D7E89FF FF

// 换行，停留在文本框，等待用户输入事件
OP: 0x0E04
Name: R
Address: 
Formate: [word:op][wdord:un][dword:line_number]
Example: 040E 1111 D2000000
```



