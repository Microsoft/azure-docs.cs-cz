---
title: Grafické vytváření obsahu v Azure Automation.
description: Vytváření grafického obsahu umožňuje vytváření runbooků pro automatizaci Azure bez práce s kódem. Tento článek obsahuje úvod do vytváření grafického obsahu a všechny podrobnosti, které jsou potřebné k zahájení vytváření grafický runbook.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 42187def32ea0f0605b50e0e25b7f78ebf0c10ac
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="graphical-authoring-in-azure-automation"></a>Grafické vytváření obsahu v Azure Automation.
## <a name="introduction"></a>Úvod
Vytváření grafického obsahu umožňuje vytváření runbooků bez složitosti kód základní prostředí Windows PowerShell nebo pracovní postup prostředí PowerShell pro Azure Automation. Přidání aktivit do plátna z knihovny rutin a sady runbook, odkaz je společně a konfiguraci k vytvoření pracovního postupu. Pokud jste již někdy se System Center Orchestrator nebo Service Management Automation (SMA), pak to by měla vypadat povědomě vám

Tento článek obsahuje úvod do vytváření grafického obsahu a koncepty, že potřebujete začít vytváření grafický runbook.

## <a name="graphical-runbooks"></a>Grafické runbooky
Všechny runbooky ve službě Azure Automation jsou pracovní postupy prostředí Windows PowerShell. Grafické pracovní postup prostředí PowerShell a grafický runbook generování kódu prostředí PowerShell, který se spouští zaměstnanci automatizace, ale nemůžete zobrazit nebo přímo upravovat. Grafický runbook lze převést na runbook pracovního postupu grafické prostředí PowerShell a naopak, ale je nelze převést na textový. Existující textovou sadu runbook nelze importovat do grafický editor.

## <a name="overview-of-graphical-editor"></a>Přehled grafického editoru
Na portálu Azure můžete otevřete grafický editor vytvořením nebo úpravou grafický runbook.

![Grafické prostoru](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

Následující části popisují ovládací prvky v grafickém editoru.

### <a name="canvas"></a>Plátno
Na plátno je, kde můžete navrhnout vaše sada runbook. Přidání aktivity do sady runbook z uzlů v ovládacím prvku knihovna a připojte je s odkazy na definovat logiky sady runbook.

Pomocí ovládacích prvků v dolní části plátna přiblížení a oddálení.

### <a name="library-control"></a>Knihovna řízení
Ovládacím prvku knihovna slouží k výběru [aktivity](#activities) přidat do runbooku. Je je přidat na plátno, kde je připojit k jiné aktivity. Obsahuje čtyři oddíly, které jsou popsané v následující tabulce:

| Sekce | Popis |
|:--- |:--- |
| Rutiny |Zahrnuje všechny rutiny, které lze použít v sadě runbook. Rutiny jsou uspořádané podle modulu. Všechny moduly, které jste nainstalovali v účtu automation jsou k dispozici. |
| Runbooky |Obsahuje sady runbook ve vašem účtu automation. Tyto sady runbook lze přidat na plátno, který se má použít jako podřízené sady runbook. Jenom runbooky stejného typu jádro jako runbook upravovaný jsou uvedeny; pro grafický jsou zobrazeny pouze pomocí prostředí PowerShell runbooky sady runbook, zatímco pro runbooky pracovních postupů grafické prostředí PowerShell jsou zobrazeny pouze PowerShell založené na pracovním postupu sady runbook. |
| Prostředky |Zahrnuje [prostředky automation](http://msdn.microsoft.com/library/dn939988.aspx) ve vašem účtu automation, který lze použít v sadě runbook. Když přidáte prostředek k sadě runbook, přidá aktivitu pracovního postupu, který získá vybrané asset. V případě proměnné prostředků můžete vybrat, jestli chcete přidat aktivitu získat proměnnou nebo nastavit proměnnou. |
| Řízení sady Runbook |Zahrnuje řízení aktivity sady runbook, které lze použít v aktuální sadě runbook. A *spojení* trvá více vstupů a čeká na všechny dokončili před pokračováním pracovního postupu. A *kód* aktivita běží jeden nebo více řádků kódu prostředí PowerShell nebo pracovního postupu Powershellu v závislosti na typ grafický runbook. Tuto aktivitu můžete použít pro vlastní kód nebo pro funkce, které je obtížné dosáhnout s ostatními aktivitami. |

### <a name="configuration-control"></a>Řízení konfigurace
Řízení konfigurace je, kde zadáte podrobnosti pro objekt vybrané na plátně. Vlastnosti dostupné u tohoto ovládacího prvku závisí na typu vybraného objektu. Když vyberete možnost v ovládacím prvku konfigurace, otevře se další oken s cílem poskytnout další informace.

### <a name="test-control"></a>Testování ovládacího prvku
Ovládací prvek testu se nezobrazí při prvním spuštění grafický editor. Když je otevřen můžete interaktivně [testování grafický runbook](#graphical-runbook-procedures).

## <a name="graphical-runbook-procedures"></a>Postupy grafický runbook
### <a name="exporting-and-importing-a-graphical-runbook"></a>Export a import grafický runbook
Exportovat lze pouze publikované verze grafický runbook. Pokud sada runbook ještě nebyla publikována, pak se **exportovat** tlačítko k dispozici. Když kliknete **exportovat** tlačítko runbook se stáhne do místního počítače. Název souboru odpovídá názvu sady runbook s *graphrunbook* rozšíření.

Soubor sady runbook grafický nebo grafické prostředí PowerShell pracovního postupu můžete importovat tak, že vyberete **importovat** možnost při přidávání sady runbook. Když vyberete soubor k importu, můžete zachovat stejné **název** nebo zadejte nový. V poli Typ Runbooku se zobrazí typ runbooku, až se vyhodnocuje vybraný soubor a pokud se pokusíte vyberte jiný typ, který není správný, zpráva zobrazí poznamenat existují potenciální konflikty a při převodu, může být syntaxe došlo k chybám.

![Import sady runbook](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>Testování grafický runbook
Při publikované verze sady runbook ponechána beze změny, nebo můžete otestovat nové sady runbook před publikování můžete otestovat verzi konceptu sady runbook na portálu Azure. To umožňuje ověřit, že runbook správně funguje, před nahrazením publikované verze. Při testování runbooku koncept runbooku se spustí a všechny akce, které provádí se dokončí. Historie úlohy je vytvořen, avšak výstupu se zobrazí v podokně výstup testu.

Otevřete ovládací prvek testů pro sadu runbook tak, že otevřete sadu runbook pro úpravy a potom klikněte na **testovací podokno** tlačítko.

Ovládací prvek testovací zobrazí výzvu pro všechny vstupní parametry runbooku můžete spustit kliknutím na **spustit** tlačítko.

### <a name="publishing-a-graphical-runbook"></a>Publikování grafický runbook
Každá sada runbook ve službě Azure Automation má koncept a publikovanou verzi. Je možné spustit jenom publikovaná verze a lze upravovat pouze verzi konceptu. Publikovaná verze neovlivní změny na verzi konceptu. Když koncept je připraven k dispozici, pak ji publikujete, která publikovaná verze přepíše konceptem.

Můžete publikovat grafický runbook tak, že otevřete sadu runbook pro úpravy a potom kliknutím na **publikovat** tlačítko.

Když runbook ještě nebyla publikována, má stav **nový**. Při publikování, má stav **publikováno**. Pokud chcete upravit sadu runbook po publikování a koncept a publikovaný verze se liší, sada runbook je ve stavu **v upravit**.

![Stavy, které jsou sady Runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Máte také možnost vrátit na publikovanou verzi runbooku. Vyvolá rychle všechny změny, protože sadu runbook naposledy publikován a nahradí koncept runbooku na publikovanou verzi.

## <a name="activities"></a>Aktivity
Aktivity jsou stavební kameny sad runbook. Aktivita může být rutiny prostředí PowerShell, podřízené sady runbook nebo aktivit pracovního postupu. Přidání aktivity do sady runbook v ovládacím prvku knihovna pravým tlačítkem a výběrem **přidat na plátno**. Můžete pak klikněte na tlačítko a přetáhněte ji na aktivitu, abyste umístit kamkoli na plátně, který chcete. Umístění aktivity na plátně neovlivní operace sady runbook žádným způsobem. Rozložení lze runbookem ale pro vás nejvhodnější k vizualizaci své činnosti.

![Přidat na plátno](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Vyberte aktivitu na plátno konfigurovat její vlastnosti a parametry v okně konfigurace. Můžete změnit **popisek** aktivity na jinou hodnotu, která je pro vás popisný. Původní rutiny je stále spuštěn, jednoduše měníte jeho zobrazovaný název, který se používá v grafickém editoru. Popisek musí být jedinečný v rámci sady runbook.

### <a name="parameter-sets"></a>Sady parametrů
Definuje sadu parametrů povinné a nepovinné parametry, které přijímá pouze hodnoty pro konkrétní rutiny. Všechny rutiny mít minimálně jeden parametr nastavit a některé mají více. Pokud rutina obsahuje několik sad parametrů, je třeba které z nich používat před konfigurací parametry vybrat. Parametry, které můžete nakonfigurovat, závisí na sadu parametrů, který zvolíte. Zadaná sada parametrů používaná aktivitou tak, že vyberete, můžete změnit **nastavený parametr** a vyberte jinou sadu. V takovém případě budou ztraceny všechny hodnoty parametrů, které jste nakonfigurovali.

V následujícím příkladu má rutinu Get-AzureRmVM tři sady parametrů. Hodnoty parametru nelze nakonfigurovat, dokud vyberete jednu z sady parametrů. Zadaná sada parametrů ListVirtualMachineInResourceGroupParamSet je pro návrat všechny virtuální počítače ve skupině prostředků a má jeden volitelný parametr. **GetVirtualMachineInResourceGroupParamSet** je pro zadání virtuálního počítače chcete vrátit a má dva povinné a jeden volitelný parametr.

![Sada parametrů](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Hodnoty parametru
Pokud zadáte hodnotu pro parametr, můžete vybrat zdroj dat k určení, jak je zadána hodnota. Zdroje dat, které jsou k dispozici pro konkrétní parametr závisí na platné hodnoty pro tento parametr. Například hodnota Null není k dispozici možnost pro parametr, který nepovoluje hodnoty null.

| Zdroj dat | Popis |
|:--- |:--- |
| Konstanta |Zadejte hodnotu pro parametr. To je dostupná jenom pro následující typy dat: Int32, Int64, řetězec, logická hodnota, datum a čas, přepínač. |
| Výstup aktivity |Výstup z aktivity, která předchází aktuální aktivity v pracovním postupu. Jsou uvedeny všechny platné aktivity. Vyberte aktivitu používat jeho výstup pro hodnotu parametru. Pokud aktivita výstupy objekt s více vlastností, potom můžete zadat název vlastnosti po výběru aktivity. |
| Vstup z Runbooku |Vyberte vstupní parametr runbooku jako vstup pro parametr aktivity. |
| Variabilní prostředek |Vyberte proměnné Automation jako vstup. |
| Asset přihlašovacích údajů |Vyberte pověření Automation jako vstup. |
| Asset certifikátu |Vyberte certifikát Automation jako vstup. |
| Asset připojení |Vyberte připojení Automation jako vstup. |
| Powershellový výraz |Zadat jednoduchý [Powershellový výraz](#powershell-expressions). Vyhodnotí výraz se před aktivity a výsledek použít pro hodnotu parametru. Proměnné můžete použít k odkazování na výstup aktivity nebo vstupní parametr runbooku. |
| Není nakonfigurované |Vymaže hodnotu, která byla nastavena. |

#### <a name="optional-additional-parameters"></a>Další volitelné parametry
Všechny rutiny mít možnost zadat další parametry. Jedná se o běžné parametry Powershellu nebo jiné vlastní parametry. Se zobrazí v textovém poli, ve kterém můžete zadat parametry pomocí syntaxe Powershellu. Chcete-li například použít **podrobné** společný parametr, zadali byste **"-Verbose: $True"**.

### <a name="retry-activity"></a>Opakujte aktivity
**Postup opakování** umožňuje aktivity ke spuštění více než jednou. dokud nebude splněna určitá podmínka, podobně jako smyčku. Tuto funkci můžete použít pro aktivity, které musí spustit vícekrát, jsou náchylné na chyby, a může potřebovat více než jeden pokus pro úspěch, nebo testovat ve výstupních informacích aktivity platná data.

Když povolíte opakování pro aktivitu, můžete nastavit zpoždění a podmínku. Zpoždění je čas (měřeno v několika sekund nebo minut), sada runbook bude čekat, než znovu spuštění aktivity. Pokud žádné zpoždění není zadaný, pak aktivita se spustí znovu ihned po jeho dokončení.

![Zpoždění při opakování aktivity](media/automation-graphical-authoring-intro/retry-delay.png)

Podmínka opakování je Powershellový výraz, který se vyhodnotí po každém spuštění aktivity. Pokud výraz přeloží na hodnotu True, pak aktivity spouští znovu. Pokud výraz přeloží na hodnotu False, pak aktivity nejde spustit znovu a runbook přesune další aktivitu.

![Zpoždění při opakování aktivity](media/automation-graphical-authoring-intro/retry-condition.png)

Opakování podmínku můžete použít proměnné s názvem $RetryData poskytující přístup k informacím o opakování aktivity. Tato proměnná má vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| NumberOfAttempts |Počet pokusů, které aktivity byl spuštěn. |
| Výstup |Výstup z posledního spuštění aktivity. |
| Agent |Vypršel časový limit aktivity od spuštění poprvé. |
| StartedAt |Čas ve formátu UTC aktivity byl prvním spuštění. |

Následují příklady aktivity opakování podmínky.

    # Run the activity exactly 10 times.
    $RetryData.NumberOfAttempts -ge 10

    # Run the activity repeatedly until it produces any output.
    $RetryData.Output.Count -ge 1

    # Run the activity repeatedly until 2 minutes has elapsed.
    $RetryData.TotalDuration.TotalMinutes -ge 2

Po dokončení konfigurace podmínku opakování pro aktivitu, aktivity obsahuje dva vizuální upozornění s upozorněním. Jeden se zobrazí v rámci aktivity a druhá je při kontrole konfigurace aktivity.

![Indikátory Visual opakování aktivity](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Řízení pracovního postupu skriptu
Ovládací prvek kód je speciální aktivitě, který přijímá skript prostředí PowerShell nebo pracovního postupu Powershellu v závislosti na typ grafický runbook právě vytvořené za účelem poskytování funkcí, které nemusí být jinak k dispozici. Nemůže přijmout parametry, ale může použít proměnné pro aktivitu výstup a runbook vstupní parametry. Žádný výstup aktivity se přidá do datové sběrnice, pouze pokud má žádná odchozí propojení v takovém případě je přidán do výstupní sady runbook.

Například následující kód provede výpočty data pomocí sady runbook vstupní proměnné názvem $NumberOfDays. Pak odešle počítané datum čas jako výstup má být používána následné aktivity v sadě runbook.

    $DateTimeNow = (Get-Date).ToUniversalTime()
    $DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
    $DateTimeStart


## <a name="links-and-workflow"></a>Odkazy a pracovní postup
A **odkaz** v grafický runbook připojení dvě aktivity. Na plátně se zobrazí jako šipka ze zdrojové aktivity k cílové aktivitě. Aktivity spustit ve směru šipku s cílová aktivita spouští po dokončení zdrojové aktivity.

### <a name="create-a-link"></a>Vytvoření odkazu
Vytvořte propojení mezi dvěma aktivitami výběrem zdrojové aktivity a kliknutím na kruh v dolní části obrazce. Přetáhněte šipku na cílová aktivita a verzi.

![Vytvoření odkazu](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Vyberte propojení na nakonfigurujte její vlastnosti v okně konfigurace. To zahrnuje typu odkaz, který je popsaný v následující tabulce:

| Typ propojení | Popis |
|:--- |:--- |
| Kanál |Cílová aktivita se spustit jednou pro každý objekt výstup ze zdrojové aktivity. Cílová aktivita se nespustí, pokud zdrojové aktivity, které jsou výsledkem žádný výstup. Výstup ze zdrojové aktivity k dispozici jako objekt. |
| Sekvence |Cílová aktivita se spustí jenom jednou. Obdrží pole objektů ze zdrojové aktivity. Výstup ze zdrojové aktivity k dispozici jako pole objektů. |

### <a name="starting-activity"></a>Spuštění aktivity
Grafický runbook začíná veškeré aktivity, které nemají příchozí propojení. Toto je často jenom jedna aktivita, která bude fungovat jako počáteční aktivita sady runbook. Pokud více aktivit nemají příchozí propojení, sada runbook spustí spuštěním paralelně. Postupuje odkazy ke spuštění jiných aktivit každý dokončení.

### <a name="conditions"></a>Podmínky
Když zadáte podmínku na propojení, cílová aktivita se spustí jenom v případě, kdy bude podmínka přeložená na hodnotu true. Obvykle můžete pomocí proměnné $ActivityOutput v podmínce načíst výstup ze zdrojové aktivity.

Pro kanál odkaz zadejte podmínku pro jediný objekt a je podmínka vyhodnocena pro každý objekt výstup zdrojové aktivity. Cílová aktivita se pak spusťte pro každý objekt, který splňuje podmínku. Například s aktivitou zdroj Get-AzureRmVm syntaxi může použít pro kanál podmíněného propojení k načtení jenom virtuální počítače ve skupině prostředků s názvem *Group1*.

    $ActivityOutput['Get Azure VMs'].Name -match "Group1"

Pro odkaz pořadí podmínka je Vyhodnocená jenom jednou vzhledem k tomu, že do jednoho pole se vrátí, obsahuje všechny objekty výstup ze zdrojové aktivity. Z toho důvodu pořadí odkaz nelze použít pro filtrování jako propojení kanálu, ale jednoduše určí, zda je další aktivita běžet. Proveďte například následující sadu aktivit v náš runbook spustit virtuální počítač.<br> ![Podmíněného propojení s pořadí](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)<br>
Existují tři různé pořadí propojení, které jsou ověření hodnoty byly poskytnuty dva vstupní parametry runbooku představující název virtuálního počítače a název skupiny prostředků, aby bylo možné určit, což je příslušné akce provést - spustit jeden virtuální počítač, spuštění všech virtuálních počítačů v prostředku Skupina, nebo všechny virtuální počítače v předplatném. Pořadí propojení mezi připojit k Azure a jeden virtuální počítač Get zde je logice podmínku:

    <#
    Both VMName and ResourceGroupName runbook input parameters have values
    #>
    (
    (($VMName -ne $null) -and ($VMName.Length -gt 0))
    ) -and (
    (($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
    )

Při použití podmíněného propojení podmínka filtrování dat ze zdrojové aktivity k dispozici pro ostatní aktivity uvedené pobočky. Pokud aktivita zdroj k více odkazů, data, která je k dispozici pro aktivity v každé větve závisí na stavu v odkazu propojíte uvedené pobočky.

Například **Start-AzureRmVm** aktivity v sadě runbook níže spustí všechny virtuální počítače. Má dva podmíněných propojení. První podmíněného propojení používá výraz *$ActivityOutput ['Start-AzureRmVM']. IsSuccessStatusCode - eq $true* k filtrování, pokud aktivitu Start-AzureRmVm úspěšně dokončit. Druhá používá výraz *$ActivityOutput ['Start-AzureRmVM']. IsSuccessStatusCode - ne $true* k filtrování, pokud aktivita Start-AzureRmVm spuštění virtuálního počítače se nezdařilo.

![Příklad podmíněného propojení](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Všechny aktivity, které následuje první odkaz a používá výstup aktivity z Get-AzureVM získají pouze virtuální počítače, které byly spuštěny v době, Get-AzureVM byla spuštěna. Všechny aktivity, která odpovídá na druhý odkaz získá pouze virtuální počítače, které jste zastavili v době, Get-AzureVM byla spuštěna. Všechny aktivity následující odkaz třetí získá všechny virtuální počítače bez ohledu na jejich stavu spuštěno.

### <a name="junctions"></a>Spojovacích bodech
Spojení je speciální aktivitě, dokud není byly dokončeny všechny příchozí větve. To umožňuje souběžně více aktivit a ujistěte se, že všechny dokončili než budete pokračovat.

Když spojení, může mít neomezený počet příchozí odkazy, více než jeden z těchto odkazů může být kanálu. Počet příchozích odkazů na pořadí není omezené. Jsou povoleny vytvoříte spojení s více příchozí propojení kanálu a uložíte sadu runbook, ale selže při spuštění.

Následující příklad je součástí sady runbook, která se spouští sada virtuálních počítačů při stahování současně opravy, které má být použita pro tyto počítače. Spojení slouží k zajištění, že oba tyto procesy jsou dokončeny před runbook pokračuje.

![Spojení](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cykly
Cyklus je, když aktivita odkazy cílové, zpět do jeho zdrojové aktivity nebo pro jinou aktivitu, který nakonec odkazy Zpět na její zdroj. Cykly nejsou aktuálně povolená ve vytváření grafického obsahu. Pokud vaše sada runbook obsahuje cyklus, uloží správně, ale obdrží chybu, když je spuštěna.

![Cyklus](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>Sdílení dat mezi aktivitami
Všechna data, která je výstupní aktivitou s odchozí propojení je zapsána do *datové sběrnice* pro sadu runbook. Všechny aktivity v sadě runbook data můžete použít na datové sběrnice k naplnění hodnoty parametru nebo zahrnout kód skriptu. Aktivity mají přístup k výstupu všechny předchozí aktivity v pracovním postupu.

Jak budou data zapsána do datové sběrnice závisí na typu odkazu na aktivity. Pro **kanálu**, data je výstup jako objekty násobky. Pro **pořadí** odkaz, data je výstup jako pole. Pokud existuje pouze jedna hodnota, je výstup jako pole jediným elementem.

Můžete přistupovat k datům v datové sběrnice pomocí jedné ze dvou způsobů. Nejprve používá **výstup aktivity** zdroj dat k naplnění parametr jiné aktivity. Je-li výstup objekt, můžete zadat vlastnosti jediné.

![Výstup aktivity](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Můžete také načíst výstup aktivity v **Powershellový výraz** zdroj dat nebo z **Workflow je skript** aktivitu se proměnná ActivityOutput. Je-li výstup objekt, můžete zadat vlastnosti jediné. Proměnné ActivityOutput použijte následující syntaxi.

    $ActivityOutput['Activity Label']
    $ActivityOutput['Activity Label'].PropertyName

### <a name="checkpoints"></a>Kontrolní body
Můžete nastavit [kontrolní body](automation-powershell-workflow.md#checkpoints) v sadě runbook pracovního postupu grafické prostředí PowerShell tak, že vyberete *kontrolního bodu runbook* na žádnou aktivitu. To způsobí, že vytvoření kontrolního bodu nastavit po spuštění aktivity.

![Kontrolní bod](media/automation-graphical-authoring-intro/set-checkpoint.png)

Kontrolní body jsou povoleny pouze v runboocích pracovního postupu grafické prostředí PowerShell, není k dispozici v grafické runbooky. Pokud sada runbook používá rutiny Azure, postupujte podle žádnou aktivitu kontrolní bod s Connect-AzureRmAccount v případě, že sada runbook je pozastavené a restartuje z této kontrolního bodu na jiný pracovní.

## <a name="authenticating-to-azure-resources"></a>Ověřované pro prostředky Azure
Runbooky ve službě Azure Automation, které spravují prostředky Azure vyžadují ověřování do Azure. [Účet Spustit jako](automation-offering-get-started.md#creating-an-automation-account) (také označované jako objekt služby) je výchozí metodou pro přístup k prostředkům Azure Resource Manager ve vašem předplatném pomocí runbooků Automation. Tuto funkci můžete přidat do grafický runbook tak, že přidáte **AzureRunAsConnection** asset připojení, která je pomocí prostředí PowerShell [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) rutiny a [ Připojit-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) rutiny na plátno. To je znázorněno v následujícím příkladu:<br>![Spustit jako ověřování aktivity](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)<br>
Aktivity získat připojení spustit jako (tedy Get-AutomationConnection), je nakonfigurovaný s konstantní hodnotou zdroj dat s názvem AzureRunAsConnection.<br>![Konfigurace připojení spustit jako](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)<br>
Na další aktivitu, Connect-AzureRmAccount, přidá k ověření účtu spustit jako pro použití v sadě runbook.<br>
![Sada parametrů připojení AzureRmAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)<br>
Pro parametry **APPLICATIONID**, **CERTIFICATETHUMBPRINT**, a **TENANTID** je třeba zadat název vlastnosti pro cestu pole, protože aktivity výstupy objekt s více vlastnostmi. V opačném případě při spuštění sady runbook, dojde k chybě při pokusu o ověření. Je to, co je potřeba minimálně ověření runbooku pomocí účtu spustit jako.

Pro zachování zpětné kompatibility pro odběratele, kteří vytvořili účet Automation pomocí [účtu uživatele Azure AD](automation-create-aduser-account.md) ke správě nasazení Azure classic nebo pro prostředky Azure Resource Manager metodu k ověření Přidat-AzureAccount rutiny s [asset přihlašovacích údajů](automation-credentials.md) představující uživatele služby Active Directory s přístupem k účtu Azure.

Tuto funkci můžete přidat do grafický runbook přidáním asset přihlašovacích údajů na plátno, za nímž následuje Add-AzureAccount aktivitu. Přidat-AzureAccount používá aktivitu přihlašovacích údajů pro vstupní. To je znázorněno v následujícím příkladu:

![Ověřování aktivity](media/automation-graphical-authoring-intro/authentication-activities.png)

Budete muset ověřit při spuštění sady runbook a po každém kontrolního bodu. To znamená, přidání aktivity přidání Add-AzureAccount po žádnou aktivitu Checkpoint-Workflow. Vzhledem k tomu, že můžete použít stejné nepotřebujete aktivitu přidání přihlašovacích údajů

![Výstup aktivity](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Runbook vstup a výstup
### <a name="runbook-input"></a>Vstup z Runbooku
Sada runbook může vyžadovat vstup od uživatele při spuštění runbooku prostřednictvím portálu Azure nebo z jiného runbooku, pokud aktuální slouží jako podřízený.
Například pokud máte sadu runbook, která vytvoří virtuální počítač, můžete zadat informace, jako je název virtuálního počítače a další vlastnosti pokaždé, když, že spuštění runbooku.

Přijmout vstup pro sadu runbook tak, že definujete jeden nebo více vstupních parametrů. Zadejte hodnoty pro tyto parametry při každém spuštění runbooku. Při spuštění sady runbook pomocí portálu Azure, budete vyzváni k zadání hodnot pro každý vstupní parametry runbooku.

Vstupní parametry pro sady runbook můžete přístup kliknutím **vstup a výstup** tlačítka na panelu nástrojů runbook.

Tím se otevře **vstup a výstup** řízení, kde můžete upravit existující vstupní parametr nebo vytvořte novou kliknutím **přidat vstup**.

![Přidat vstup](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Každý vstupního parametru je definováno vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| Název |Jedinečný název parametru. To může obsahovat pouze alfanumerické znaky a nesmí obsahovat mezery. |
| Popis |Volitelný popis pro vstupní parametr. |
| Typ |Datový typ pro hodnotu parametru. Portál Azure poskytuje vhodný ovládací prvek pro datový typ pro každý parametr při zobrazení výzvy ke vstupu. |
| Povinné |Určuje, zda musí být zadána hodnota pro parametr. Runbook nejde spustit, pokud nezadáte hodnotu pro každý povinný parametr, který nemá výchozí hodnotu definovanou. |
| Výchozí hodnota |Určuje, jaká hodnota se používá pro parametr, pokud není zadáno. To může být buď hodnotu Null nebo konkrétní hodnotu. |

### <a name="runbook-output"></a>Výstup runbooku
Data vytvořená aktivitou, která nemá odchozí odkaz je [výstup runbooku](http://msdn.microsoft.com/library/azure/dn879148.aspx). Výstup bude uložen s úloha sady runbook a je k dispozici pro nadřazený runbook, když runbook slouží jako podřízený.

## <a name="powershell-expressions"></a>Výrazy prostředí PowerShell
Jednou z výhod vytváření grafického obsahu vám poskytuje možnost vytvoření sady runbook s minimálními znalostmi prostředí PowerShell. V současné době potřebujete vědět, když pro sestavování určité verze prostředí PowerShell [hodnoty parametrů](#activities) a pro nastavení [odkaz podmínky](#links-and-workflow). Tato část obsahuje rychlý úvod do prostředí PowerShell výrazy pro uživatele, kteří nemusí být obeznámeni s ním. Podrobnosti o prostředí PowerShell jsou k dispozici na [skriptování v prostředí Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Zdroj dat výraz prostředí PowerShell
Výraz prostředí PowerShell můžete použít jako zdroj dat k naplnění hodnotu [parametr aktivity](#activities) s výsledky nějaký kód prostředí PowerShell. To může být jediný řádek kódu, který provádí některé jednoduché funkce nebo více řádků, které provádějí některé komplexní logiku. Všechny výstupy z příkaz, který není přiřazený k proměnné je výstup do hodnotu parametru.

Například následující příkaz by výstup aktuální datum.

    Get-Date

Následující příkazy vytvoření řetězce z aktuální datum a přiřaďte ho k proměnné. Obsah proměnné pak posílají se na výstup

    $string = "The current date is " + (Get-Date)
    $string

Následující příkazy vyhodnotit aktuální datum a vrátí řetězec určující, zda je aktuální den víkendu nebo den v týdnu.

    $date = Get-Date
    if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
    else { "Weekday" }


### <a name="activity-output"></a>Výstup aktivity
Chcete-li použít výstup z předchozí aktivity v sadě runbook, použijte proměnnou $ActivityOutput s následující syntaxí.

    $ActivityOutput['Activity Label'].PropertyName

Například může mít aktivita, jejíž vlastnosti, která vyžaduje název virtuálního počítače v takovém případě můžete použít následující výraz:

    $ActivityOutput['Get-AzureVm'].Name

Pokud vlastnost, která vyžaduje virtuální počítač objekt místo pouze vlastnosti, by vrátit celý objekt pomocí následující syntaxe.

    $ActivityOutput['Get-AzureVm']

Také můžete použít výstup aktivity ve složitější výrazu například tato zřetězí text, který má název virtuálního počítače.

    "The computer name is " + $ActivityOutput['Get-AzureVm'].Name


### <a name="conditions"></a>Podmínky
Použití [operátory porovnání](https://technet.microsoft.com/library/hh847759.aspx) porovnat hodnoty nebo zjistit, jestli hodnota odpovídá zadanému vzoru. Porovnání vrací hodnotu $true nebo $false.

Například následující podmínka Určuje, zda virtuální počítač z aktivity s názvem *Get-AzureVM* právě *zastavena*.

    $ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"

Následující podmínky kontroly, zda na stejný virtuální počítač je v libovolném stavu jiné než *zastavena*.

    $ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"

Toho se můžete zapojit více podmínek použití [logický operátor](https://technet.microsoft.com/library/hh847789.aspx) , jako **- a** nebo **- nebo**. Například následující podmínky kontroly, zda na stejný virtuální počítač v předchozím příkladu je ve stavu *zastavena* nebo *zastavení*.

    ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")


### <a name="hashtables"></a>Zatřiďovacích tabulkách
[Zatřiďovacích tabulkách](http://technet.microsoft.com/library/hh847780.aspx) jsou páry název/hodnota, které jsou užitečné pro vrácení sadu hodnot. Vlastnosti pro určité aktivity se očekává, že zatřiďovací tabulku namísto jednoduché hodnoty. Může se také zobrazit jako zatřiďovací tabulky uvedené jako slovník.

Vytvořit tabulku hash pomocí následující syntaxe. Zatřiďovací tabulky může obsahovat libovolný počet položek, ale každý je definována podle názvu a hodnoty.

    @{ <name> = <value>; [<name> = <value> ] ...}

Například následující výraz vytvoří tabulku hash pro použije ve zdroji dat pro parametr aktivity, který očekává zatřiďovací tabulku s hodnotami pro hledání v Internetu.

    $query = "Azure Automation"
    $count = 10
    $h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
    $h

Následující příklad používá výstupem z aktivity volá *získat připojení služby Twitter* k naplnění zatřiďovací tabulku.

    @{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
      'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
      'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
      'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}



## <a name="next-steps"></a>Další kroky
* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).
* První kroky s grafickými runbooky najdete v článku [Můj první grafický runbook](automation-first-runbook-graphical.md).
* Další informace o typech runbooků, jejich výhodách a omezeních najdete v článku [Typy runbooků ve službě Azure Automation](automation-runbook-types.md).
* Chcete-li pochopit, jak ověřit pomocí účtu Automation spustit jako, přečtěte si téma [konfigurace Azure účet Spustit jako](automation-sec-configure-azure-runas-account.md)

