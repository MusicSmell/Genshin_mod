# Help generator
namespace_merge.py (by [SilentNightSound](https://github.com/SilentNightSound/GI-Model-Importer/blob/main/Tools/genshin_merge_mods.py))로 통합된 모드들의 토글키를 설명하는 도움말 창 자동 생성기
<br/>
<br/>

## 간단 사용법
1. namespace_merge.py로 모드 통합하기. 이 때 이름을 '꼭' 올바른 캐릭터 이름([링크의 폴더 이름](https://github.com/leotorrez/GI-Model-Importer-Assets/tree/main/PlayerCharacterData))으로 입력하기.
2. help_generator.py 다운로드
3. namespace_merge.py로 통합된 폴더로 파일 복사
4. 해당 폴더에서 아래 코드 실행 
```bash
python namespace_merge.py
```
5. 해당 캐릭터가 보이는 창에서, `/?`키 누르기
<br/>

## 상세 사용법
```bash
python namespace_merge.py 
```
<br/>

## 도움말 설명
인게임에서 도움말은 `/?` 또는 지정한 키로 키고 끌 수 있습니다.<br/>
도움말의 경우 아래 규칙으로 자동 생성됩니다.
```bash
모드 이름


    [키1] 기능1

    [키2|키3] Back키가 있는 경우. 2가 앞으로, 3이 뒤로
```
<br/>

## 커스터마이징
해당 코드는 help.txt의 내용을 인게임에 띄우는 help.ini를 자동 생성하는 코드입니다.<br/>
따라서 내가 원하는 스타일대로 help.txt와 help.ini를 직접 만들면 원하는 도움말 창을 띄울 수 있습니다.
#### 1. 파일 복사하기
Repository에 제공되는 샘플 help.txt와 샘플 help.ini를 다운받고, 모드 폴더로 옮깁니다.<br/>
namespace_merge로 통합한 폴더가 아닌, 각각의 모드 폴더여야 합니다.
#### 2. help.txt 수정하기
해당 파일의 텍스트를 그대로 불러와서 인게임에 띄우기 때문에 이 텍스트를 수정하면 원하는 내용을 띄울 수 있습니다.<br/>
한글이나 일부 특수문자는 인게임에서 출력되지 않을 수 있습니다.
#### 3. help.ini 수정하기
알맞게 help.ini를 직접 수정해야 합니다. 아래 주석을 참고해주세요.
```ini
global $active = 0


[Present]
post $active = 0


[KeyHelp]
condition = $active == 1
key = /   ; 도움말을 띄우는 토글키 입니다. 원하는 키로 수정하세요.
type = toggle
; {master}에는 namespace_merge.py로 통합 파일을 생성할 때 입력한 이름을 넣어야 합니다. Master{master}.py <- 이 {master}입니다.
; {index}에는 namespace_merge.py로 통합된 해당 모드의 index를 넣어야 합니다.
; 통합된 모드가 아닌, 모드 하나에 도움말 창을 띄우고 싶다면 run = CommandListHelp만 남기고 if ~ endif까지 지우면 됩니다.
if $\\{master}\\Master\\swapvar=={index}
	run = CommandListHelp
endif 


[TextureOverrideCharacterPosition]
; {hash}에는 캐릭터의 postion hash값을 넣어야 합니다.
; https://github.com/leotorrez/GI-Model-Importer-Assets/tree/main/PlayerCharacterData/{Character}/hash.json의 "position_vb"값을 찾거나, 모드.ini에서 직접 찾으시면 됩니다.
hash = {hash}
match_priority = 100
; 위와 동일한 {master}, {index}
; 통합된 모드가 아닌, 모드 하나에 도움말 창을 띄우고 싶다면 $active = 1만 남기고 if ~ endif까지 지우면 됩니다.
if $\\{master}\\Master\\swapvar=={index}
	$active = 1
endif 


[CommandListHelp]
pre Resource\\ShaderFixes\\help.ini\\Help = ref ResourceNotesFull
pre Resource\\ShaderFixes\\help.ini\\Params = ref ResourceParamsFull
pre run = CustomShader\\ShaderFixes\\help.ini\\FormatText
pre Resource\\ShaderFixes\\help.ini\\HelpShort = null
post Resource\\ShaderFixes\\help.ini\\Help = null


[ResourceNotesFull]
type = buffer
format = R8_UINT
filename = help.txt   ;만약 help.txt가 아닌 다른 이름으로 도움말 텍스트를 저장했거나, 파일의 위치가 다르다면 filename을 올바를 위치로 수정해주세요.


[ResourceParamsFull]
type = StructuredBuffer
array = 1
; 인게임 내에 help.txt의 내용을 어떻게 띄울지 그 스타일을 결정합니다.
data = R32_FLOAT   -0.93 -1 1 1   1 1 1 1   0 0 0 0.5   0.025 0.025   1 2   0   1.0
```
<br/>

## Q & A
#### 이 오류 왜 뜨나요
>오류의 경우의 수는 매우 많기 때문에, 일단 chatgpt나 gemini등 여러 AI에게 물어보세요. 아래는 일반적인 경우입니다.
>namespace_merge.py가 생성한 Master.ini와 실제 모드 정보가 일치하지 않으면 오류가 뜹니다. 내가 Master.ini를 수정했거나, 모드 폴더나 파일명을 통합 이후 수정했는지 확인해주세요.
#### 왜 토글키 설명이 이따구인가요
>이 코드는 '자동생성기' 입니다. 모드 제작자가 명명한 토글키 이름을 그대로 불러오기 때문에 제작자에 따라 토글키 설명이 불친절할 수 있습니다.<br/>
>불만족스럽다면 해당 모드의 help.txt를 직접 수정해주세요.
#### 다른 캐릭터나 다른 모드의 도움말이 떠요
>동시에 여러 도움말을 불러오면 다른 하나가 무시됩니다. [커스터마이징/3. help.ini 수정하기](#3-helpini-수정하기)를 참고하여 hash, master, index가 겹치는 다른 help.ini가 있는지 확인해보세요.<br/>
>서로 다른 캐릭터가 동시에 존재할 경우 (선계, 스토리 등) 다른 캐릭터의 도움말이 뜰 수 있습니다.<br/>
>일부 캐릭터를 같이 파티에 편성할 경우 (저의 경우 푸리나 + 마비카) 고장나는데 (통합 자체의 문제라고 추정되나) 원인은 잘 모르겠습니다. 재접하면 해당 파티를 불러오기 전까지 정상 작동합니다.
#### 도움말이 안 떠요
>토글키가 존재하지 않는 모드의 경우 도움말을 생성하지 않습니다.<br/>
>hash가 잘못된 경우일 수 있습니다. 
