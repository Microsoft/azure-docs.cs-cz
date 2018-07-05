---
title: Grafické vytváření obsahu v Azure Automation
description: Vytváření grafického obsahu umožňuje vytváření runbooků Azure Automation bez práce s kódem. Tento článek obsahuje úvod do vytváření grafického obsahu a všechny informace potřebné k vytvoření grafického runbooku.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b0517af9f8066d2d5849b0ffe3d4a0d00afdad44
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437871"
---
# <a name="graphical-authoring-in-azure-automation"></a>Grafické vytváření obsahu v Azure Automation.

Vytváření grafického obsahu umožňuje vytváření runbooků bez složitosti základního kódu Windows Powershellu nebo pracovním postupu Powershellu pro Azure Automation. Přidání aktivit do plátna z knihovny, rutiny a sady runbook, společně propojují a nakonfigurovat tak, aby formulář pracovního postupu. Pokud jste někdy pracovali s System Center Orchestrator nebo Service Management Automation (SMA), potom to by měla vypadat povědomě vám

Tento článek obsahuje úvod do vytváření grafického obsahu a koncepty potřebné k zahájení práce během vytváření grafického runbooku.

## <a name="graphical-runbooks"></a>Grafické runbooky

Všechny sady runbook ve službě Azure Automation jsou pracovní postupy prostředí Windows PowerShell. Grafické runbooky grafický Powershellový pracovní postup generování kódu Powershellu, který je spuštěn Worker služby Automation, ale nemůžete zobrazit nebo přímo upravovat. Grafický runbook lze převést na sadu runbook Graphical PowerShell Workflow a naopak, ale jejich nelze převést na textový sady runbook. Existujícího textového runbooku se nedají importovat do grafický editor.

## <a name="overview-of-graphical-editor"></a>Přehled grafický editor

Vytvořením nebo úpravou grafického runbooku můžete otevřete grafický editor na webu Azure Portal.

![Grafické pracovního prostoru](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

Následující části popisují ovládacích prvků v grafickém editoru.

### <a name="canvas"></a>Plátno

Na plátně je, kde můžete navrhnout vaše sada runbook. Přidání aktivity do sady runbook z uzlů v ovládacím prvku knihovna a spojujeme je s odkazy na definování logiky sady runbook.

Ovládací prvky můžete použít v dolní části plátna pro přiblížení a oddálení.

### <a name="library-control"></a>Ovládací prvek knihovna

Ovládacím prvku knihovna je, kde můžete vybrat [aktivity](#activities) přidáte do runbooku. Můžete je přidat na plátno, kde je připojení k jiné aktivity. Obsahuje čtyři části jsou popsané v následující tabulce:

| Sekce | Popis |
|:--- |:--- |
| Rutiny |Zahrnuje všechny rutiny, které lze použít ve své sadě runbook. Rutiny jsou uspořádané podle modulu. Všechny moduly nainstalované ve vašem účtu automation jsou k dispozici. |
| Runbooky |Obsahuje sady runbook ve vašem účtu automation. Tyto sady runbook lze přidat na plátno a použít jako podřízené sady runbook. Zobrazují se jenom runbooky stejného typu jako sady runbook, který právě upravujete core; pro grafický jsou uvedeny pouze pomocí prostředí PowerShell runbooky sady runbook, zatímco pro grafický Powershellový pracovní postup sady runbook jsou uvedeny pouze pomocí prostředí PowerShell pracovního postupu-sady runbook. |
| Prostředky |Zahrnuje [prostředky služby automation](http://msdn.microsoft.com/library/dn939988.aspx) ve vašem účtu automation, který je možné v sadě runbook. Když přidáte prostředek k sadě runbook, přidá aktivitu pracovního postupu, který získá vybraný prostředek. V případě proměnných assetů můžete vybrat, jestli se má přidat aktivitu získat proměnnou nebo nastavit proměnnou. |
| Řízení sady Runbook |Obsahuje ovládací prvek aktivity sady runbook, které lze použít v aktuální sadě runbook. A *spojení* přijímá více vstupů a počká, dokud nebudou všechny dokončila před pokračováním pracovní postup. A *kód* aktivita spustí jeden nebo více řádků kódu Powershellu nebo pracovním postupu Powershellu v závislosti na typu grafický runbook. Tuto aktivitu lze použít pro vlastní kód nebo pro funkce, které je obtížné dosáhnout s ostatními aktivitami. |

### <a name="configuration-control"></a>Řízení konfigurace

Ovládací prvek konfigurace je, kde zadáte podrobnosti pro objekt vybraný na plátně. K dispozici v tomto ovládacím prvku vlastnosti závisí na typu vybraného objektu. Při výběru možnosti v ovládacím prvku konfigurace, otevře se další okna aby bylo možné poskytnout další informace.

### <a name="test-control"></a>Testování ovládacího prvku

Ovládací prvek testu se nezobrazí při prvním spuštění grafický editor. Při otevření můžete interaktivně [testování grafický runbook](#graphical-runbook-procedures).

## <a name="graphical-runbook-procedures"></a>Postupy grafického runbooku

### <a name="exporting-and-importing-a-graphical-runbook"></a>Export a import grafického runbooku

Jde exportovat jenom publikované verze grafický runbook. Pokud sada runbook ještě nebyla publikována, pak bude **exportovat** je tlačítko neaktivní. Když kliknete **exportovat** tlačítko, sada runbook se stáhne do místního počítače. Název souboru odpovídá názvu sady runbook s *graphrunbook* rozšíření.

Importujete soubor sady runbook grafický nebo grafický Powershellový pracovní postup tak, že vyberete **importovat** možnost při přidávání sady runbook. Když vyberete soubor k importu, abyste mohli stejné **název** nebo zadejte nový. V poli Typ Runbooku se zobrazí typu runbook po vyhodnocuje vybraný soubor a pokud budete chtít vybrat jiný typ, který není správný, zpráva se zobrazí zmínku neexistují potenciální konflikty a během převodu, může být syntaxe chyby.

![Import runbooku](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>Testování grafického runbooku

Na webu Azure Portal můžete otestovat verzi konceptu sady runbook, při opuštění publikovanou verzi runbooku beze změny, nebo nový runbook můžete otestovat předtím, než se publikoval. To umožňuje ověřit, že runbook správně funguje, před nahrazením publikované verze. Při testování runbooku koncept runbooku se spustí a jsou dokončeny všechny akce, které aplikace provádí. Nevytvoří se žádná historie úlohy, ale výstup se zobrazí v podokně výstup testu.

Otevřete ovládací prvek testu pro sadu runbook tak, že otevřete sadu runbook pro úpravy a potom klikněte na **testovací podokno** tlačítko.

Ovládací prvek testu zobrazí výzvu pro žádné vstupní parametry a spustíte runbook po kliknutí na **Start** tlačítko.

### <a name="publishing-a-graphical-runbook"></a>Publikování grafického runbooku

Každá sada runbook ve službě Azure Automation má koncept a publikovanou verzi. Je možné spustit jenom publikovaná verze a lze upravovat pouze verzi konceptu. Publikovaná verze není ovlivněn změny konceptu. Když koncept je připraven k dispozici, pak ji publikujete, která publikovaná verze přepíše verzi konceptu.

Grafický runbook můžete publikovat tak, že otevřete sadu runbook pro úpravy a potom kliknutím na **publikovat** tlačítko.

Když runbook ještě nebyla publikována, je ve stavu **nový**. Když se publikuje, je ve stavu **publikováno**. Pokud sada runbook upravíte, poté, co byla publikována a koncept a publikovaný verze se liší, sada runbook je ve stavu **v režimu úpravy**.

![Stavy sady Runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Máte také možnost vrátit na publikovanou verzi runbooku. Vyvolá okamžitě všechny změny provedené od posledního publikování sady runbook a nahradí koncept runbooku publikovanou verzi.

## <a name="activities"></a>Aktivity

Aktivity jsou stavební bloky sady runbook. Aktivita může být rutiny Powershellu, podřízené sady runbook nebo aktivita pracovního postupu. Přidání aktivity do sady runbook pravým tlačítkem myši v ovládacím prvku knihovna a výběrem **přidat na plátno**. Potom můžete kliknout a přetáhněte aktivitu umístit kamkoli na plátno, který vás zajímá. Umístění na plátně aktivity nemá vliv na operace sady runbook žádným způsobem. Můžete rozložit sadě runbook, ale pro vás nejvhodnější vizualizaci jeho operace.

![Přidat na plátno](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Vyberte aktivitu na plátně konfigurovat její vlastnosti a parametry v okně konfigurace. Můžete změnit **popisek** aktivity na něco, která je pro vás popisný. Původní rutina je pořád spuštěný, jednoduše změníte její zobrazovaný název, který se používá v grafickém editoru. Popisek musí být jedinečný v rámci sady runbook.

### <a name="parameter-sets"></a>Sady parametrů

Definuje sadu parametrů povinných a volitelných parametrů, které přijímají hodnoty pro konkrétní rutiny. Všechny rutiny mají nastavený aspoň jeden parametr, a některé mají více. Pokud nějaká rutina obsahuje několik sad parametrů, je nutné vybrat který z nich je použít, než budete moct nakonfigurovat parametry. Parametry, které můžete nakonfigurovat, závisí na sadu parametrů, kterou zvolíte. Můžete změnit sadu parametrů, používá aktivitu tak, že vyberete **sady parametrů** a vyberete jiné sady. V takovém případě všechny hodnoty parametrů, které jste nakonfigurovali, se ztratí.

V následujícím příkladu rutiny Get-AzureRmVM má tři sady parametrů. Hodnoty parametrů nelze nakonfigurovat, dokud nevyberete některou sady parametrů. Sada parametrů ListVirtualMachineInResourceGroupParamSet je pro vrácení všech virtuálních počítačů ve skupině prostředků a má jeden volitelný parametr. **GetVirtualMachineInResourceGroupParamSet** slouží k určení virtuálního počítače chcete vrátit a má dvě povinné a jeden volitelný parametr.

![Sada parametrů](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Hodnoty parametrů

Pokud zadáte hodnotu pro parametr, vyberte zdroj dat k určení, jak je zadaná hodnota. Zdroje dat, které jsou k dispozici pro konkrétní parametr závisí na platné hodnoty pro tento parametr. Například hodnota Null není k dispozici možnost pro parametr, který nepovoluje hodnoty null.

| Zdroj dat | Popis |
|:--- |:--- |
| Konstanta |Zadejte hodnotu pro parametr. To je k dispozici pouze pro následující typy dat: datový typ Int32, Int64, řetězec, logická hodnota, data a času, přepínač. |
| Výstup aktivity |Výstupem z aktivity, které předchází aktuální aktivitu v pracovním postupu. Všechny aktivity. Platné jsou uvedeny. Vyberte aktivitu používat svůj výstup pro hodnotu parametru. V případě, že výstupem aktivity je objekt s více vlastnostmi, můžete zadat název vlastnosti po výběru aktivity. |
| Vstup z Runbooku |Vyberte vstupní parametr runbooku s jako vstup pro parametr aktivity. |
| Variabilní prostředek |Proměnná služby Automation vyberte jako vstup. |
| Asset přihlašovacích údajů |Vyberte přihlašovací údaje služby Automation jako vstup. |
| Asset certifikátu |Vyberte certifikát Automation jako vstup. |
| Asset připojení |Vyberte připojení služby Automation jako vstup. |
| Powershellový výraz |Zadat jednoduchý [Powershellový výraz](#powershell-expressions). Výraz je vyhodnocen před aktivity a výsledek použité pro hodnotu parametru. Proměnné můžete použít k odkazování na výstupu aktivity nebo vstupní parametr runbooku. |
| Není nakonfigurované |Vymaže libovolnou hodnotu, která byla dříve nakonfigurovaná. |

#### <a name="optional-additional-parameters"></a>Nepovinné další parametry

Všechny rutiny máte možnost zadat další parametry. Jedná se o běžné parametry Powershellu nebo jiné vlastní parametry. Zobrazí se textové pole, ve kterém můžete zadat parametry s využitím syntaxe Powershellu. Chcete-li například použít **Verbose** společný parametr, zadali byste **"-Verbose: $True"**.

### <a name="retry-activity"></a>Opakování aktivity

**Chování opakování** umožňuje aktivity spouštět opakovaně, dokud není splněna konkrétní podmínka, podobně jako smyčky. Tuto funkci můžete použít pro aktivity, které by měly být spuštěny více než jednou, jsou náchylné k chybám, a může potřebovat více než jeden pokus k dosažení úspěchu, nebo testovat ve výstupních informacích aktivity platná data.

Když povolíte opakování pro aktivitu, můžete nastavit zpoždění a podmínku. Zpoždění je čas (v řádu sekund nebo minut), sada runbook čeká předtím, než se aktivita spouští znovu. Pokud žádné zpoždění není zadána, aktivita bude spusťte znovu ihned po jeho dokončení.

![Zpoždění opakování aktivity](media/automation-graphical-authoring-intro/retry-delay.png)

Podmínky opakování je Powershellový výraz, který je vyhodnocen po každém spuštění aktivity. Pokud výraz se přeloží na hodnotu True, pak aktivita spouští znovu. Pokud výraz se přeloží na hodnotu False, pak aktivity nelze spustit znovu a runbook přesune další aktivitu.

![Zpoždění opakování aktivity](media/automation-graphical-authoring-intro/retry-condition.png)

Podmínky opakování můžete použít proměnnou s názvem $RetryData, který poskytuje přístup k informacím o opakování aktivity. Tato proměnná má vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| NumberOfAttempts |Počet pokusů, které nebyly spuštěny aktivity. |
| Výstup |Výstup z poslední spuštění aktivity. |
| TotalDuration |Vypršel časový limit, které uplynuly od spuštění první aktivity. |
| StartedAt |Čas ve formátu UTC aktivity byl prvním spuštění. |

Následují příklady aktivity opakování podmínky.

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

Po dokončení konfigurace podmínky opakování pro aktivitu aktivity zahrnuje dva vizuální upozornění na to upozorní. Jeden je uveden v aktivity a druhý je při kontrole konfigurace aktivity.

![Indikátory Visual opakování aktivity](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Řízení pracovního postupu skriptu

Ovládací prvek kódu je speciální aktivitě, která přijímá skript prostředí PowerShell nebo pracovního postupu Powershellu v závislosti na typu grafický runbook, aby bylo možné poskytují funkce, které jinak možná není k dispozici právě vytvořené. Nepřijímá parametry, ale může použít proměnné pro aktivitu výstup a runbook vstupní parametry. Všechny výstupy aktivity se přidá do datové sběrnice, pokud nemá žádná odchozí propojení v takovém případě je přidána do výstupní sady runbook.

Například následující kód provede výpočty data pomocí sady runbook vstupní proměnnou s názvem $NumberOfDays. Vypočítané datum čas pak odešle jako výstup pro následné aktivity v sadě runbook.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Odkazy a pracovního postupu

A **odkaz** v grafický runbook připojí dvě aktivity. Na plátně se zobrazí jako šipky směřující od zdroje aktivity. na cílovou aktivitu. Aktivity spuštění směrem šipky se cílová aktivita spouští se po dokončení zdrojové aktivity.

### <a name="create-a-link"></a>Vytvořit odkaz

Vytvořte propojení mezi dvěma aktivitami zdrojová aktivita výběrem a kliknutím na kruh v dolní části obrazce. Přetáhněte šipku na cílová aktivita a vydání.

![Vytvořit odkaz](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Vyberte odkaz a nakonfigurujte její vlastnosti v okně konfigurace. Jedná se o typ odkazu, která je popsána v následující tabulce:

| Typ odkazu | Popis |
|:--- |:--- |
| Kanál |Cílová aktivita spustí jednou pro každý objekt výstup ze zdrojové aktivity. Cílová aktivita se nespustí, pokud zdrojová aktivita výsledkem žádný výstup. Výstup ze zdrojové aktivity je k dispozici jako objekt. |
| Sekvence |Cílová aktivita se spustí pouze jednou. Přijímá pole objektů ze zdrojové aktivity. Výstup ze zdrojové aktivity je k dispozici jako pole objektů. |

### <a name="starting-activity"></a>Spuštění aktivit

Grafický runbook začíná veškeré aktivity, které nemají příchozí propojení. To je často jenom jednu aktivitu, která bude fungovat jako počáteční aktivita sady runbook. Pokud více aktivit nemají příchozí propojení, sada runbook spustí spuštěním paralelně. Postupuje odkazy na spouštěly ostatní aktivity, každé dokončení.

### <a name="conditions"></a>Podmínky

Pokud zadáte podmínku na propojení, cílová aktivita se spustí jenom v případě kdy bude podmínka přeložená na hodnotu true. Obvykle použijete proměnnou $ActivityOutput v podmínce pro načtení výstupu ze zdrojové aktivity

Odkaz na kanál určíte podmínku pro jediný objekt a podmínka se vyhodnocuje pro každý objekt výstup podle zdroje aktivity. Cílová aktivita se pak spusťte pro každý objekt, který splňuje podmínku. Například s aktivitou zdroj Get-AzureRmVm následující syntaxi může použít pro propojení podmíněné kanálu k načtení jenom virtuální počítače ve skupině prostředků s názvem *Group1*.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Odkaz na pořadí podmínka se jenom vyhodnotí jednou od jednoho pole se vrátí, obsahuje všechny objekty výstup ze zdrojové aktivity. Z tohoto důvodu pořadí propojení nelze použít pro filtrování jako propojení kanálu, ale stačí určit, zda je další aktivita spuštěna. Vezměme si jako příklad následující sadu aktivit v naší sadě runbook spustit virtuální počítač.

![Podmíněné propojení s knihovnou pořadí](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Existují tři různé sekvence odkazy, které jsou ověřuje hodnoty poskytnuté pro dva vstupní parametry runbooku představující název virtuálního počítače a název skupiny prostředků, aby bylo možné určit, což je požadované akce trvat - start jednoho virtuálního počítače, spusťte všechny virtuální počítače v prostředku Skupina, nebo všechny virtuální počítače v rámci předplatného. Pořadí propojení mezi připojit se k Azure a jeden virtuální počítač Get tady je podmíněné logiky:

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

Při použití podmíněného propojení podmínka filtrování dat ze zdrojové aktivity k dispozici pro další aktivity v této větvi. Pokud aktivita je zdroj, který má několik odkazů, aby byla data dostupná pro aktivity v každé větvi závisí na podmínky v propojeních do této větve.

Například **Start-AzureRmVm** spuštěním aktivity v sadě runbook pod všechny virtuální počítače. Má dva podmíněných propojení. První podmíněného propojení používá výraz *$ActivityOutput ['Start-AzureRmVM']. IsSuccessStatusCode - eq $true* k filtrování, pokud aktivita Start-AzureRmVm byla úspěšně dokončena. Druhý výraz používá *$ActivityOutput ['Start-AzureRmVM']. IsSuccessStatusCode - ne $true* k filtrování, pokud aktivita Start-AzureRmVm nepovedlo se spustit virtuální počítač.

![Příklad podmíněného propojení](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Všechny aktivity, který následuje první odkaz a používá výstup aktivity z Get-AzureVM se zobrazí jenom virtuální počítače, které byly spuštěny v době spuštění Get-AzureVM. Všechny aktivity, který následuje na druhý odkaz získá jenom virtuální počítače, které jste zastavili v době spuštění Get-AzureVM. Následující odkaz na třetí aktivity bez získá všechny virtuální počítače bez ohledu na jejich stavu spuštěno.

### <a name="junctions"></a>Spojení

Spojení je speciální aktivitě, která čeká na dokončení všech příchozích větví. To umožňuje spustit paralelně několik aktivit a ujistěte se, že všechny dokončila před přechodem na.

Přestože spojení může mít neomezený počet příchozí odkazy, ne více než jeden z těchto odkazů může být kanálu. Počet odkazů na příchozí pořadí není omezen. Budou moci vytvořit spojení s více příchozí propojení kanálu a uložit sady runbook, ale to není úspěšné při jejím spuštění.

Následující příklad je součástí sady runbook, který se spustí sadu virtuálních počítačů při stahování současně opravy u těchto počítačů. Spojení se používá k zajištění, že oba tyto procesy jsou dokončeny před pokračuje v sadě runbook.

![Spojení](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cykly

Cyklus je, když aktivita odkazy cíl, zpět jeho zdrojová aktivita nebo jiné aktivity, který nakonec odkazuje zpět na svůj zdroj. Cykly není aktuálně povoleno ve vytváření grafického obsahu. Pokud vaše sada runbook obsahuje cyklus, uloží správně, ale při spuštění se zobrazí chyba.

![Cyklus](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>Sdílení dat mezi aktivitami

Všechna data, která je výstupem aktivitu odchozího odkazu je zapsána do *datové sběrnice* pro sadu runbook. Všechny aktivity v sadě runbook můžete použít data na datové sběrnice k naplnění hodnoty parametru nebo zahrnout kód skriptu. Aktivita může přistupovat k výstupu jakékoli předchozí aktivity v pracovním postupu.

Jak budou data zapsána do datové sběrnice závisí na typu odkazu na aktivity. Pro **kanálu**, data se výstup jako násobky objekty. Pro **pořadí** odkaz, data se výstup jako pole. Pokud existuje pouze jedna hodnota, je výstup jako jediný prvek pole.

Můžou k datům v datové sběrnice pomocí jedné ze dvou způsobů. Nejprve se pomocí **výstup aktivity** zdroj dat k naplnění parametr jiné aktivity. Pokud výstupem je objekt, můžete určit jednu vlastnost.

![Výstup aktivity](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Můžete také načíst výstup aktivity v **Powershellový výraz** zdroj dat nebo z **pracovního postupu skriptu** aktivita s proměnnou ActivityOutput. Pokud výstupem je objekt, můžete určit jednu vlastnost. Proměnné ActivityOutput použijte následující syntaxi.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Kontrolní body

Můžete nastavit [kontrolní body](automation-powershell-workflow.md#checkpoints) v sadě runbook grafický Powershellový pracovní postup tak, že vyberete *runbook kontrolního bodu* u všech aktivit. To způsobí, že kontrolní bod nastavit po spuštění aktivit.

![Kontrolní bod](media/automation-graphical-authoring-intro/set-checkpoint.png)

Kontrolní body jsou povolené jenom v sadách runbook grafický Powershellový pracovní postup, není k dispozici v grafických runboocích. Pokud sada runbook používá rutiny Azure, měli byste postupovat podle všechny kontrolní aktivity s Connect-AzureRmAccount v případě, je runbook pozastavený a restartuje od tohoto kontrolního bodu na jinému pracovnímu procesu.

## <a name="authenticating-to-azure-resources"></a>Ověřované pro prostředky Azure

Runbooky ve službě Azure Automation, které spravují prostředky Azure vyžadují ověřování do Azure. [Účet Spustit jako](automation-create-runas-account.md) (také označované jako objekt služby) je výchozí metodou pro přístup k prostředkům Azure Resource Manageru ve vašem předplatném pomocí runbooků Automation. Tuto funkci můžete přidat do grafický runbook tak, že přidáte **AzureRunAsConnection** asset připojení, která je pomocí Powershellu [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) rutiny a [ Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) rutiny na plátno. To je znázorněno v následujícím příkladu:

![Spustit jako ověřování aktivit](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

Aktivita Get Run As Connection (to znamená, Get-AutomationConnection), je konfigurována s konstantní hodnotou zdroje dat s názvem AzureRunAsConnection.

![Konfigurace připojení spustit jako](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

Další aktivity Connect-AzureRmAccount, přidá ověření účtu spustit jako pro použití v sadě runbook.

![Sada parametrů Connect-AzureRmAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

> [!IMPORTANT]
> **Add-AzureRmAccount** je nyní alias pro **Connect-AzureRMAccount**. Při vyhledávání knihovny položky, pokud se nezobrazí **Connect-AzureRMAccount**, můžete použít **Add-AzureRmAccount**, nebo ve vašem účtu Automation můžete aktualizovat moduly.

Pro parametry **APPLICATIONID**, **CERTIFICATETHUMBPRINT**, a **TENANTID** je třeba zadat název vlastnosti pro cestu pole, protože aktivity výstupem je objekt s více vlastnostmi. Jinak po spuštění sady runbook, dojde k chybě při pokusu o ověření. Je to, co je potřeba minimálně ověření runbooku pomocí účtu spustit jako.

Pro zachování zpětné kompatibility pro předplatitele, kteří vytvořili účet Automation pomocí [uživatelského účtu Azure AD](automation-create-aduser-account.md) ke správě nasazení Azure classic nebo pro prostředky Azure Resource Manageru, je metoda ověřování Přidat-AzureAccount rutinu s [asset přihlašovacích údajů](automation-credentials.md) , která představuje uživatele služby Active Directory s přístupem k účtu Azure.

Tuto funkci můžete přidat do grafický runbook tak, že přidáte na plátno, za nímž následuje aktivita Add-AzureAccount asset přihlašovacích údajů. Přidat-AzureAccount používá aktivitu přihlašovacích údajů pro vstup. To je znázorněno v následujícím příkladu:

![Ověřování aktivit](media/automation-graphical-authoring-intro/authentication-activities.png)

Budete muset ověřit při spuštění sady runbook a za každý existující kontrolní bod. To znamená, že přidáte aktivita přidání Add-AzureAccount po žádnou aktivitu Checkpoint-Workflow. Vzhledem k tomu můžete použít stejný nepotřebujete aktivita přidání přihlašovacích údajů

![Výstup aktivity](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Sada Runbook vstupu a výstupu

### <a name="runbook-input"></a>Vstup z Runbooku

Sady runbook může vyžadovat vstup od uživatele při spuštění sady runbook na portálu Azure nebo z jiného runbooku, pokud se tu používá jako podřízený objekt.
Pokud máte sadu runbook, která vytvoří virtuální počítač, budete muset poskytnout informace, jako je název virtuálního počítače a další vlastnosti pokaždé, když, spuštění runbooku.

Přijměte vstup pro sadu runbook tak, že definujete jeden nebo více vstupních parametrů. Můžete zadat hodnoty pro tyto parametry pokaždé, když spuštění runbooku. Při spuštění sady runbook pomocí portálu Azure, budete vyzváni k poskytnutí hodnot pro každý vstupní parametry runbooku.

Vstupní parametry pro sady runbook se zpřístupní po kliknutí **vstup a výstup** tlačítko na panelu nástrojů sady runbook.

Tím se otevře **vstupní a výstupní** ovládací prvek, ve kterém můžete upravit existující vstupní parametr nebo vytvořte novou kliknutím **přidat vstup**.

![Přidat vstup](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Každý vstupní parametr je definován vlastností v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| Název |Jedinečný název parametru. To může obsahovat jenom alfanumerické znaky a nesmí obsahovat mezery. |
| Popis |Volitelný popis pro vstupní parametr. |
| Typ |Datový typ, očekávání pro hodnotu parametru. Na webu Azure portal poskytuje vhodný ovládací prvek pro datový typ pro každý parametr při zobrazení výzvy ke vstupu. |
| Povinné |Určuje, zda je nutné zadat hodnotu pro parametr. Sadu runbook nelze spustit, pokud nezadáte hodnotu pro každý povinný parametr, který nemá výchozí hodnotu definovanou. |
| Výchozí hodnota |Určuje, jaká hodnota se používá pro parametr, pokud není zadáno. To může být buď Null, nebo konkrétní hodnotu. |

### <a name="runbook-output"></a>Výstup runbooku

Uložená data vytvořená žádnou aktivitu, která nemá odchozího odkazu [výstup runbooku](http://msdn.microsoft.com/library/azure/dn879148.aspx). Výstup je uložen pod úlohy runbooku a je k dispozici nadřízený runbook, když sada runbook slouží jako podřízený objekt.

## <a name="powershell-expressions"></a>Výrazy prostředí PowerShell

Jednou z výhod vytváření grafického obsahu vám poskytuje možnost vytvoření sady runbook s minimálními znalostmi prostředí PowerShell. V současné době je potřeba vědět hodně prostředí PowerShell i když k naplnění některých [hodnoty parametrů](#activities) a pro nastavení [odkaz podmínky](#links-and-workflow). Tato část obsahuje stručný úvod do prostředí PowerShell výrazy pro uživatele, kteří nemusí být obeznámeni s ním. Úplné podrobnosti o Powershellu najdete na adrese [skriptování v prostředí Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Zdroj dat výraz prostředí PowerShell
Můžete použít Powershellový výraz jako zdroj dat k naplnění hodnoty [parametr aktivity](#activities) s výsledky kódu Powershellu. Může to být jediný řádek kódu, který provádí některé jednoduché funkce nebo více řádků, které provádějí některé komplexní logiku. Žádný výstup z příkazu, který není přiřazen k proměnné se výstup hodnotu parametru.

Například následující příkaz by výstup aktuální datum.

Například následující příkaz by výstup aktuální datum.

```powershell-interactive
Get-Date
```

Následující příkazy vytvořit řetězec od aktuálního data a přiřaďte ho k proměnné. Obsah proměnné se pak odešlou do výstupu

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Následující příkazy vyhodnotí na aktuální datum a vrátí řetězec určující, zda je aktuální den víkendu nebo den v týdnu.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Výstup aktivity

Pokud chcete použít výstup z předchozí aktivity v sadě runbook, použijte proměnnou $ActivityOutput s následující syntaxí.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Například může mít aktivita, jejíž vlastnost, která vyžaduje název virtuálního počítače v takovém případě můžete použít následující výraz:

```powershell-interactive
$ActivityOutput['Get-AzureVm'].Name
```

Pokud vlastnost, která vyžaduje virtuální počítač objekty namísto pouze vlastnosti, by vrátí celý objekt pomocí následující syntaxe.

```powershell-interactive
$ActivityOutput['Get-AzureVm']
```

Můžete také použít výstup aktivity v mnohem složitější výraz například následující, který zřetězí text, který má název virtuálního počítače.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVm'].Name
```

### <a name="conditions"></a>Podmínky

Použití [operátory porovnání](https://technet.microsoft.com/library/hh847759.aspx) porovnat hodnoty nebo zjistit, jestli hodnota odpovídá zadanému vzoru. Porovnání vrací hodnotu $true nebo $false.

Například následující podmínka Určuje, zda virtuální počítač z aktivity s názvem *Get-AzureVM* je aktuálně *zastavena*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

Následující podmínky kontroly, jestli je stejný virtuální počítač v libovolném stavu jiné než *zastavena*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Toho se můžete zapojit několika podmínek použití [logického operátoru](https://technet.microsoft.com/library/hh847789.aspx) jako **– a** nebo **– nebo**. Například následující podmínky kontroly, jestli stejný virtuální počítač v předchozím příkladu je ve stavu *zastavena* nebo *zastavení*.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Zatřiďovací tabulky

[Zatřiďovacích tabulkách](http://technet.microsoft.com/library/hh847780.aspx) jsou páry název/hodnota, které jsou užitečné pro vrácení sady hodnot. Vlastnosti pro určité aktivity může očekávat, že zatřiďovací tabulku místo jednoduchých hodnot. Také může zobrazit jako zatřiďovací tabulky označovány jako slovník.

Vytvoření zatřiďovací tabulku s následující syntaxí. Zatřiďovací tabulka může obsahovat libovolný počet položek, které se ale každý je definován tak, že název a hodnotu.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Například následující výraz vytvoří tabulku hash pro ve zdroji dat použít pro parametr aktivity, který byl očekáván zatřiďovací tabulku s hodnotami pro vyhledávání na Internetu.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

Následující příklad používá výstupem z aktivity volá *získat připojení služby Twitter* k naplnění zatřiďovací tabulku.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="next-steps"></a>Další kroky

* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).
* První kroky s grafickými runbooky najdete v článku [Můj první grafický runbook](automation-first-runbook-graphical.md).
* Další informace o typech runbooků, jejich výhodách a omezeních najdete v článku [Typy runbooků ve službě Azure Automation](automation-runbook-types.md).
* Jak provést ověření pomocí účtu Automation spustit jako najdete v tématu [nakonfigurovat účet Spustit jako](automation-sec-configure-azure-runas-account.md)