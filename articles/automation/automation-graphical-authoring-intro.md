---
title: Vytváření grafiky v Azure Automation
description: Vytváření grafiky umožňuje vytvářet runbooky pro Azure Automation bez práce s kódem. Tento článek obsahuje úvod do grafického vytváření a všechny podrobnosti potřebné k zahájení vytváření grafického runbooku.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: cf8ced05066923c94e80628651d8983560601d69
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406055"
---
# <a name="graphical-authoring-in-azure-automation"></a>Vytváření grafiky v Azure Automation

Vytváření grafiky umožňuje vytvářet runbooky pro Azure Automation bez složitosti základního kódu pracovního postupu Prostředí Windows PowerShell nebo PowerShell. Aktivity můžete na plátno přidat z knihovny rutin a sad Runbook, propojit je a nakonfigurovat tak, aby vytvořily pracovní postup. Pokud jste někdy pracovali s System Center Orchestrator nebo Service Management Automation (SMA), grafické vytváření by mělo vypadat povědomě. Tento článek obsahuje úvod do konceptů, které potřebujete začít vytvářet grafické runbook.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="graphical-runbooks"></a>Grafické runbooky

Všechny runbooky v Azure Automation jsou pracovní postupy Windows PowerShellu. Grafické sady runbooků a grafické runbooky pracovního postupu prostředí PowerShell generují kód prostředí PowerShell, který spouštějí pracovníci automatizace, ale který nelze zobrazit ani upravit. Grafickou runbook můžete převést na grafickou runbook pracovního postupu prostředí PowerShell a naopak. Tyto runbooky však nelze převést na textovou runbook. Grafický editor automatizace navíc nelze importovat textovou runbook.

## <a name="overview-of-graphical-editor"></a>Přehled grafického editoru

Grafický editor můžete otevřít na webu Azure Portal vytvořením nebo úpravou grafické sady Runbook.

![Grafický pracovní prostor](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

Následující části popisují ovládací prvky v grafickém editoru.

### <a name="canvas-control"></a>Ovládací prvek Plátno

Ovládací prvek Plátno umožňuje navrhnout runbook. Aktivity můžete přidat z uzlů v ovládacím prvku Knihovna do runbooku a propojit je s odkazy pro definování logiky runbooku. V dolní části plátna jsou ovládací prvky, které umožňují přiblížení a oddálení.

### <a name="library-control"></a>Ovládací prvek Knihovna

Ovládací prvek Knihovna umožňuje vybrat [aktivity, které](#activities) chcete přidat do runbooku. Přidáte je na plátno, kde je můžete připojit k jiným aktivitám. Ovládací prvek Knihovna obsahuje oddíly definované v následující tabulce.

| Sekce | Popis |
|:--- |:--- |
| Rutiny |Všechny rutiny, které lze použít v runbooku. Rutiny jsou uspořádány podle modulu. Všechny moduly, které jste nainstalovali do účtu Automation, jsou k dispozici. |
| Runbooky |Runbooky ve vašem účtu Automation. Tyto runbooky můžete přidat na plátno, které se použije jako podřízené runbooky. Zobrazí se pouze runbooky stejného základního typu jako upravovaný runbook. Pro grafické sady Runbook jsou zobrazeny pouze sady Runbook založené na prostředí PowerShell. Pro grafické runbooky pracovního postupu prostředí PowerShell jsou zobrazeny pouze sady Runbook založené na pracovním postupu prostředí PowerShell. |
| Prostředky |[Prostředky automatizace](/previous-versions/azure/dn939988(v=azure.100)) ve vašem účtu Automation, které můžete použít v runbooku. Přidání datového zdroje do runbooku přidá aktivitu pracovního postupu, která získá vybraný datový zdroj. V případě proměnných datových zdrojů můžete vybrat, zda chcete přidat aktivitu, chcete-li proměnnou získat, nebo nastavit proměnnou. |
| Řízení runbooku |Řídit aktivity, které lze použít v aktuální množiny runbooku. Aktivita křižovatky trvá více vstupů a čeká, dokud všechny byly dokončeny před pokračováním pracovního postupu. Aktivita kódu spouští jeden nebo více řádků kódu pracovního postupu prostředí PowerShell nebo PowerShell v závislosti na typu grafického spuštění. Tuto aktivitu můžete použít pro vlastní kód nebo pro funkce, které je obtížné dosáhnout s jinými aktivitami. |

### <a name="configuration-control"></a>Řízení konfigurace

Ovládací prvek Konfigurace umožňuje zadat podrobnosti o objektu, který je vybrán na plátně. Vlastnosti dostupné v tomto ovládacím prvku závisí na typu vybraného objektu. Pokud zvolíte možnost v ovládacím prvku Konfigurace, otevře se další listy poskytnout další informace.

### <a name="test-control"></a>Kontrola zkoušek

Ovládací prvek Test se nezobrazí při prvním spuštění grafického editoru. Otevře se při interaktivním testování grafické sady Runbook.

## <a name="activities"></a>Aktivity

Aktivity jsou stavebními kameny runbooku. Aktivitou může být rutina prostředí PowerShell, podřízená runbook nebo pracovní postup. Aktivitu můžete do runbooku přidat tak, že na ní kliknete pravým tlačítkem myši v ovládacím prvku Knihovna a vyberete **Přidat na plátno**. Potom můžete na aktivitu a přetáhněte ji umístit kamkoli na plátno, které se vám líbí. Umístění aktivity na plátně nemá vliv na provoz runbooku. Runbook můžete rozkládat jakýmkoli způsobem, který je nejvhodnější pro vizualizaci jejího provozu.

![Přidat na plátno](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Vyberte aktivitu na plátně a nakonfigurujte její vlastnosti a parametry v okně Konfigurace. Popisek aktivity můžete změnit na název, který považujete za popisný. Runbook stále spouští původní rutinu. Jednoduše měníte zobrazovaný název, který používá grafický editor. Všimněte si, že popisek musí být jedinečný v rámci runbooku.

### <a name="parameter-sets"></a>Sady parametrů

Sada parametrů definuje povinné a volitelné parametry, které přijímají hodnoty pro určitou rutinu. Všechny rutiny mají alespoň jednu sadu parametrů a některé mají několik sad. Pokud má rutina více sad parametrů, musíte před konfigurací parametrů vybrat tu, která má být používána. Sadu parametrů používanou aktivitou můžete změnit výběrem **parametru Set** a výběrem jiné sady. V tomto případě budou ztraceny všechny hodnoty parametrů, které jste již nakonfigurovali.

V následujícím příkladu má rutina [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) tři sady parametrů. Příklad používá jednu sadu nazvanou **ListVirtualMachineInResourceGroupParamSet**s jedním volitelným parametrem pro vrácení všech virtuálních počítačů ve skupině prostředků. Příklad také používá sadu parametrů **GetVirtualMachineInResourceGroupParamSet** pro určení virtuálního počítače, který má být vrácen. Tato sada má dva povinné parametry a jeden volitelný parametr.

![Sada parametrů](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Hodnoty parametrů

Když zadáte hodnotu parametru, vyberete zdroj dat, abyste určili, jak bude hodnota zadána. Zdroje dat, které jsou k dispozici pro konkrétní parametr, závisí na platných hodnotách pro daný parametr. Například Null není k dispozici možnost pro parametr, který neumožňuje hodnoty null.

| Zdroj dat | Popis |
|:--- |:--- |
| Konstantní hodnota |Zadejte hodnotu parametru. Tento zdroj dat je k dispozici pouze pro následující datové typy: Int32, Int64, String, Boolean, DateTime, Switch. |
| Výstup aktivity |Použijte výstup z aktivity, která předchází aktuální aktivitě v pracovním postupu. Všechny platné aktivity jsou uvedeny. Pro hodnotu parametru použijte pouze aktivitu, která vytváří výstup. Pokud aktivita vypíše objekt s více vlastnostmi, můžete po výběru aktivity zadat název určité vlastnosti. |
| Vstup runbooku |Vyberte vstup runbooku jako vstup pro parametr aktivity. |
| Variabilní majetek |Jako vstup vyberte proměnnou Automation. |
| Datový zdroj pověření |Jako vstup vyberte pověření automatizace. |
| Majetek certifikátu |Jako vstup vyberte certifikát automatizace. |
| Datový zdroj připojení |Vyberte připojení automatizace jako vstup. |
| Powershellový výraz |Zadejte jednoduchý [výraz prostředí PowerShell](#powershell-expressions). Výraz je vyhodnocen před aktivitou a výsledek se použije pro hodnotu parametru. Proměnné můžete použít k odkazování na výstup aktivity nebo vstupního parametru runbooku. |
| Nenakonfigurováno |Zrušte všechny hodnoty, které byly dříve nakonfigurovány. |

#### <a name="optional-additional-parameters"></a>Volitelné dodatečné parametry

Všechny rutiny mají možnost poskytnout další parametry. Jedná se o běžné parametry prostředí PowerShell nebo jiné vlastní parametry. Grafický editor představuje textové pole, do kterého můžete zadat parametry pomocí syntaxe prostředí PowerShell. Chcete-li například `Verbose` použít společný parametr, měli byste zadat `-Verbose:$True`.

### <a name="retry-activity"></a>Opakovat aktivitu

Funkce opakování aktivity umožňuje její spuštění vícekrát, dokud není splněna určitá podmínka, podobně jako smyčka. Tuto funkci můžete použít pro aktivity, které by měly být spuštěny vícekrát, jsou náchylné k chybám, mohou vyžadovat více než jeden pokus o úspěch nebo otestovat výstupní informace o aktivitě pro platná data.

Pokud povolíte opakování aktivity, můžete nastavit zpoždění a podmínku. Zpoždění je čas (měřeno v sekundách nebo minutách), který runbook čeká před spuštěním aktivity znovu. Pokud nezadáte zpoždění, aktivita se spustí znovu ihned po dokončení.

![Zpoždění opakování aktivity](media/automation-graphical-authoring-intro/retry-delay.png)

Podmínka opakování je výraz prostředí PowerShell, který je vyhodnocen po každém spuštění aktivity. Pokud se výraz překládá na True, aktivita se spustí znovu. Pokud se výraz překládá na False, aktivita se znovu nespustí a runbook se přesune na další aktivitu.

![Zpoždění opakování aktivity](media/automation-graphical-authoring-intro/retry-condition.png)

Podmínka opakování můžete použít proměnnou s názvem, `RetryData` který poskytuje přístup k informacím o opakování aktivity. Tato proměnná má vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| `NumberOfAttempts` |Počet spuštění aktivity. |
| `Output` |Výstup z posledního spuštění aktivity. |
| `TotalDuration` |Časovaná doba od prvního spuštění aktivity. |
| `StartedAt` |Čas (ve formátu UTC), kdy byla aktivita poprvé spuštěna. |

Následují příklady podmínek opakování aktivity.

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

Po konfiguraci podmínky opakování aktivity aktivita obsahuje dvě vizuální podněty, které vám připomene. Jeden je uveden v aktivitě a druhý je zobrazen při kontrole konfigurace aktivity.

![Vizuální indikátory opakování aktivity](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Ovládací prvek Skript pracovního postupu

Ovládací prvek Skript pracovního postupu je speciální aktivita, která přijímá skript pracovního postupu prostředí PowerShell nebo Prostředí PowerShell v závislosti na typu vytvářeného grafického souboru runbook. Tento ovládací prvek poskytuje funkce, které nemusí být k dispozici jinými prostředky. Nemůže přijímat parametry, ale může použít proměnné pro výstup aktivity a vstupní parametry runbooku. Všechny výstup aktivity je přidán do databus. Výjimkou je výstup bez odchozího propojení, v takovém případě je výstup přidán do výstupu runbooku.

Například následující kód provádí výpočty data pomocí vstupní `NumberOfDays`proměnné runbooku s názvem . Potom odešle vypočtenou hodnotu DateTime jako výstup, který má být použit následnými aktivitami v runbooku.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Odkazy a pracovní postup

Odkaz v grafickém runbooku spojuje dvě aktivity. Zobrazí se na plátně jako šipka směřující ze zdrojové aktivity na cílovou aktivitu. Aktivity spustit ve směru šipky s cílovou aktivitou začíná po dokončení zdrojové aktivity.

### <a name="link-creation"></a>Vytvoření odkazu

Propojení mezi dvěma aktivitami můžete vytvořit výběrem zdrojové aktivity a kliknutím na kruh v dolní části obrazce. Přetáhněte šipku na cílovou aktivitu a uvolněte ji.

![Vytvoření odkazu](media/automation-graphical-authoring-intro/create-link-options.png)

Vyberte odkaz pro konfiguraci jeho vlastností v okně Konfigurace. Vlastnosti zahrnují typ propojení, který je popsán v následující tabulce.

| Typ propojení | Popis |
|:--- |:--- |
| Kanál |Cílová aktivita je spuštěna jednou pro každý výstup objektu ze zdrojové aktivity. Cílová aktivita se nespustí, pokud zdrojová aktivita nevede k žádnému výstupu. Výstup ze zdrojové aktivity je k dispozici jako objekt. |
| Sequence |Cílová aktivita se spustí pouze jednou, když obdrží výstup ze zdrojové aktivity. Výstup ze zdrojové aktivity je k dispozici jako pole objektů. |

### <a name="start-of-activity"></a>Zahájení činnosti

Grafický runbook začíná všechny aktivity, které nemají příchozí odkaz. Často existuje pouze jedna aktivita, která funguje jako počáteční aktivita pro runbook. Pokud více aktivit nemají příchozí propojení, spuštění runbooku jejich spuštěním paralelně. Následuje odkazy spustit další aktivity jako každý dokončí.

### <a name="link-conditions"></a>Podmínky propojení

Když zadáte podmínku pro propojení, cílová aktivita se spustí pouze v případě, že podmínka přelože na True. Obvykle používáte `ActivityOutput` proměnnou v podmínce k načtení výstupu ze zdrojové aktivity.

Pro propojení potrubí je nutné zadat podmínku pro jeden objekt. Soubor Runbook vyhodnotí podmínku pro každý výstup objektu zdrojovou aktivitou. Potom spustí cílovou aktivitu pro každý objekt, který splňuje podmínku. Například se zdrojovou `Get-AzVM`aktivitou aplikace můžete použít následující syntaxi pro odkaz podmíněného kanálu k načtení pouze virtuálních počítačů ve skupině prostředků s názvem Group1.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Pro sekvenční propojení runbook vyhodnotí podmínku pouze jednou, protože je vráceno jedno pole obsahující všechny objekty ze zdrojové aktivity. Z tohoto důvodu sada Runbook nemůže použít sekvenční odkaz pro filtrování, stejně jako s odkazem na kanál. Sekvenční propojení můžete jednoduše určit, zda je spuštěna další aktivita.

Vezměte například následující sadu aktivit v našem **runbooku Start VM:**

![Podmíněné propojení s sekvencemi](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Runbook používá tři různé sekvenční odkazy, `VMName` `ResourceGroupName` které ověřují hodnoty vstupních parametrů a určují příslušnou akci, kterou je třeba provést. Možné akce jsou spuštění jednoho virtuálního virtuálního aplikace, spuštění všech virtuálních aplikací ve skupině prostředků nebo spuštění všech virtuálních aplikací v předplatném. Pro sekvenční propojení mezi `Connect to Azure` a `Get single VM`, zde je logika podmínky:

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

Při použití podmíněného propojení jsou data dostupná ze zdrojové aktivity na jiné aktivity v této větvi filtrována podmínkou. Pokud je aktivita zdrojem více propojení, data dostupná pro aktivity v každé větvi závisí na podmínce v propojení, které se připojuje k této větvi.

Například aktivita `Start-AzVM` v níže uvedeném runbooku spustí všechny virtuální počítače. Má dva podmíněné odkazy. První podmíněný odkaz používá `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` výraz k `Start-AzVM` filtrování, pokud se aktivita úspěšně dokončí. Druhý podmíněný odkaz používá `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` výraz k `Start-AzVm` filtrování, pokud aktivita nespustí virtuální počítač.

![Příklad podmíněného odkazu](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Všechny aktivity, které následuje první odkaz `Get-AzureVM` a používá výstup aktivity pouze z `Get-AzureVM` načte virtuální počítače, které byly spuštěny v době spuštění. Všechny aktivity, které následuje druhý odkaz získá pouze virtuální `Get-AzureVM` počítače, které byly zastaveny v době, kdy byla spuštěna. Všechny aktivity následující třetí odkaz získá všechny virtuální počítače bez ohledu na jejich spuštěný stav.

### <a name="junctions"></a>Křižovatek

Křižovatka je speciální aktivita, která čeká, dokud nebudou dokončeny všechny příchozí větve. To umožňuje runbook uvolnovat více aktivit paralelně a zajistit, že všechny byly dokončeny před přechodem na.

Zatímco křižovatka může mít neomezený počet příchozích spojení, pouze jeden z těchto spojů může být kanál. Počet příchozích sekvenčních odkazů není omezen. Můžete vytvořit spojení s více příchozími propojeními kanálu a uložit runbook, ale při spuštění se nezdaří.

Následující příklad je součástí sady Runbook, která spustí sadu virtuálních počítačů a současně stahuje opravy, které se mají použít na tyto počítače. Používá spojení k zajištění, že oba procesy jsou dokončeny před spuštěním knihy runbook pokračuje.

![Spojení](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cykly

Cyklus je vytvořen, když cílová aktivita odkazuje zpět na jeho zdrojové aktivity nebo jiné aktivity, které nakonec odkazy zpět na jeho zdroj. Vytváření grafiky v současné době nepodporuje cykly. Pokud má vaše runbook cyklus, uloží správně, ale při spuštění se zobrazí chyba.

![Cyklické](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="data-sharing-between-activities"></a>Sdílení dat mezi aktivitami

Všechna data, která výstupy aktivity s odchozí propojení je zapsána do datové sběrnice pro runbook. Jakákoli aktivita v runbooku může použít data na datové sběrnici k naplnění hodnot parametrů nebo zahrnout do kódu skriptu. Aktivita může přistupovat k výstupu jakékoli předchozí aktivity v pracovním postupu.

Způsob zápisu dat do datové sběrnice závisí na typu propojení na aktivitě. Pro propojení kanálu jsou data výstupem jako více objektů. Pro sekvenční propojení jsou data výstupem jako pole. Pokud existuje pouze jedna hodnota, je výstup jako jednoprvkové pole.

Vaše runbook má dva způsoby přístupu k datům na datové sběrnici: 
* Použijte zdroj dat výstupu aktivity.
* Použijte zdroj dat výrazu PowerShell.

První mechanismus používá zdroj dat výstupu aktivity k naplnění parametru jiné aktivity. Pokud je výstupem objekt, runbook může určit jednu vlastnost.

![výstup aktivity](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Druhý mechanismus přístupu k datům načte výstup aktivity ve zdroji dat `ActivityOutput` výrazu PowerShell nebo aktivity skriptu pracovního postupu s proměnnou pomocí syntaxe uvedené níže. Pokud je výstupem objekt, může vaše runbook určit jednu vlastnost.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Kontrolní body

[Kontrolní body](automation-powershell-workflow.md#checkpoints) můžete nastavit v grafické sadě Runbook workflow prostředí PowerShell výběrem **sady Runbook pro** kontrolní bod pro libovolnou aktivitu. To způsobí, že kontrolní bod nastavit po spuštění aktivity.

![CheckPoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

Kontrolní body jsou povoleny pouze v grafických runbookech pracovního postupu prostředí PowerShell a nejsou k dispozici v grafických sadách Runbook. Pokud runbook používá rutiny Azure, měl by sledovat všechny `Connect-AzAccount` aktivity s kontrolními body s aktivitou. Operace připojení se používá v případě, že je runbook pozastaven a musí se restartovat z tohoto kontrolního bodu na jiném pracovníkovi.

## <a name="runbook-input-and-output"></a>Vstup a výstup runbooku

### <a name="runbook-input"></a>Vstup runbooku<a name="runbook-input"></a>

Runbook vyžaduje vstup od uživatele, který spouští runbook prostřednictvím portálu Azure, nebo z jinésady Runbook, pokud se aktuální používá jako podřízený. Například pro runbook, který vytvoří virtuální počítač, může uživatel muset poskytnout takové informace, jako je název virtuálního počítače a další vlastnosti při každém spuštění runbooku.

Runbook přijímá vstup definováním jednoho nebo více vstupních parametrů. Uživatel poskytuje hodnoty pro tyto parametry při každém spuštění runbooku. Když uživatel spustí runbook pomocí portálu Azure, uživatel je vyzván k zadání hodnot pro každý vstupní parametr podporovaný runbook.

Při vytváření sady Runbook můžete přistupovat k jejím vstupním parametrům kliknutím na **Vstup a výstup** na panelu nástrojů sady Runbook. Tím se otevře ovládací prvek Vstup a Výstup, kde můžete upravit existující vstupní parametr nebo vytvořit nový klepnutím na tlačítko **Přidat vstup**.

![Přidání vstupu](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Každý vstupní parametr je definován vlastnostmi v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| Název | Povinná hodnota. Název parametru Název musí být jedinečný v rámci runbooku. Musí začínat písmenem a může obsahovat pouze písmena, číslice a podtržítka. Název nesmí obsahovat mezeru. |
| Popis |Nepovinný parametr. Popis účelu vstupního parametru. |
| Typ | Nepovinný parametr. Pro hodnotu parametru byl očekáván datový typ. Portál Azure poskytuje odpovídající ovládací prvek pro datový typ pro každý parametr při zobrazení výzvy pro vstup. Podporované typy parametrů jsou String, Int32, Int64, Decimal, Boolean, DateTime a Object. Pokud datový typ není vybrán, je výchozí String.|
| Povinné | Nepovinný parametr. Nastavení, které určuje, zda musí být pro parametr poskytnuta hodnota. Pokud zvolíte `yes`, musí být při spuštění runbooku poskytnuta hodnota. Pokud zvolíte `no`, hodnota není vyžadována při spuštění runbooku a lze použít výchozí hodnotu. Runbook nelze spustit, pokud nezadáte hodnotu pro každý povinný parametr, který nemá výchozí hodnotu definována. |
| Výchozí hodnota | Nepovinný parametr. Hodnota použitá pro parametr, pokud není předán při spuštění runbooku. Chcete-li nastavit výchozí `Custom`hodnotu, zvolte . Tuto `None` možnost vyberte, pokud nechcete zadat žádnou výchozí hodnotu. |

### <a name="runbook-output"></a>Výstup runbooku

Grafické vytváření ukládá data vytvořená jakoukoli aktivitou, která nemá odchozí odkaz na [výstup runbooku](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). Výstup se uloží s úlohou runbooku a je k dispozici pro nadřazenou runbook, když se runbook používá jako podřízený.

## <a name="powershell-expressions"></a>Výrazy prostředí PowerShell

Jednou z výhod vytváření grafických je, že umožňuje vytvořit runbook s minimální znalostí prostředí PowerShell. V současné době však potřebujete vědět trochu PowerShell pro vyplnění určitých [hodnot parametrů](#activities) a pro nastavení [podmínek propojení](#links-and-workflow). Tato část obsahuje rychlý úvod do výrazů prostředí PowerShell. Úplné podrobnosti o prostředí PowerShell jsou k dispozici na [skriptování s prostředím Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Zdroj dat výrazu PowerShell

Výraz Prostředí PowerShell můžete použít jako zdroj dat k naplnění hodnoty [parametru aktivity](#activities) s výsledky kódu prostředí PowerShell. Výraz může být jeden řádek kódu, který provádí jednoduchou funkci nebo více řádků, které provádějí některé složité logiky. Jakýkoli výstup z příkazu, který není přiřazen proměnné, je výstupem na hodnotu parametru.

Například následující příkaz vyvodí aktuální datum.

```powershell-interactive
Get-Date
```

Další fragment kódu vytvoří řetězec od aktuálního data a přiřadí jej proměnné. Kód odešle obsah proměnné do výstupu.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Následující příkazy vyhodnotí aktuální datum a vrátí řetězec označující, zda je aktuální den víkend nebo den v týdnu.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Výstup aktivity

Chcete-li použít výstup z předchozí aktivity `ActivityOutput` v aplikaci Runbook, použijte proměnnou s následující syntaxí.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Můžete mít například aktivitu s vlastností, která vyžaduje název virtuálního počítače. V takovém případě může vaše runbook použít následující výraz.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Pokud vlastnost vyžaduje objekt virtuálního počítače namísto pouze názvu, vrátí aplikace Runbook celý objekt pomocí následující syntaxe.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Runbook můžete použít výstup aktivity ve složitější výraz, jako je například následující. Tento výraz zřetězí text s názvem virtuálního počítače.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="conditions"></a>Podmínky

Pomocí [operátorů porovnání](https://technet.microsoft.com/library/hh847759.aspx) můžete porovnat hodnoty nebo určit, zda hodnota odpovídá zadanému vzoru. Porovnání vrátí hodnotu True nebo False.

Například následující podmínka určuje, zda je virtuální `Get-AzureVM` počítač z aktivity s názvem aktuálně zastaven.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

Následující podmínka určuje, zda je stejný virtuální počítač v jiném stavu než zastaveno.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

V aplikaci Runbook můžete připojit více podmínek `-and` `-or`pomocí [logického operátoru](https://technet.microsoft.com/library/hh847789.aspx), například nebo . Například následující podmínka zkontroluje, zda je virtuální počítač v předchozím příkladu ve stavu Zastaveno nebo Zastaveno.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Hashtables

[Hashtables](https://technet.microsoft.com/library/hh847780.aspx) jsou dvojice název hodnota, které jsou užitečné pro vrácení sadu hodnot. Může se také zobrazit hashtable označovány jako slovník. Vlastnosti pro určité aktivity očekávat hashtable namísto jednoduchou hodnotu.

Vytvořte hashtable pomocí následující syntaxe. Může obsahovat libovolný počet položek, ale každá je definována názvem a hodnotou.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Například následující výraz vytvoří hodnotitelnou hodnotu, která se použije jako zdroj dat pro parametr aktivity, který očekává hodnot hashtable hodnot pro vyhledávání na internetu.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

Následující příklad používá výstup z `Get Twitter Connection` aktivity volané k naplnění hashtable.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticating-to-azure-resources"></a>Ověřování prostředků Azure

Runbooky v Azure Automation, které spravují prostředky Azure, vyžadují ověření do Azure. [Účet Spustit jako](automation-create-runas-account.md), označovaný také jako instanční objekt, je výchozí mechanismus, který runbook automatizace používá pro přístup k prostředkům Azure Resource Manager ve vašem předplatném. Tuto funkci můžete přidat do grafického souboru Runbook přidáním prostředku `AzureRunAsConnection` připojení, který používá rutinu [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) prostředí PowerShell na plátno. Můžete také přidat rutinu [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Tento scénář je znázorněno v následujícím příkladu.

![Spustit jako aktivity ověřování](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

Aktivita `Get Run As Connection` nebo `Get-AutomationConnection`, je nakonfigurována s `AzureRunAsConnection`názvem zdroje dat s konstantní hodnotou .

![Spustit jako konfiguraci připojení](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

Další aktivita `Connect-AzAccount`, přidá ověřený účet Spustit jako pro použití v runbooku.

![Sada parametrů Connect-AzAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>Pro sady Runbook `Add-AzAccount` `Add-AzureRMAccount` prostředí PowerShell `Connect-AzAccount`a jsou aliasy pro . Všimněte si, že tyto aliasy nejsou k dispozici pro grafické sady Runbook. Grafická kniha runbook `Connect-AzAccount` může používat pouze samostatně.

Pro pole parametrů **APPLICATIONID**, **CERTIFICATETHUMBPRINT**a **TENANTID**zadejte název vlastnosti cesty pole, protože aktivita vyvede objekt s více vlastnostmi. V opačném případě při spuštění runbooku se nezdaří při pokusu o ověření. To je to, co potřebujete minimálně k ověření runbooku pomocí účtu Spustit jako.

Někteří předplatitelé vytvořit účet Automatizace pomocí [uživatelského účtu Azure AD](automation-create-aduser-account.md) pro správu klasické nasazení Azure nebo pro prostředky Azure Resource Manager. Chcete-li zachovat zpětnou kompatibilitu pro tyto předplatitele, `Add-AzureAccount` mechanismus ověřování pro použití v aplikaci Runbook je rutina s [datovým zdrojem pověření](automation-credentials.md). Prostředek představuje uživatele služby Active Directory s přístupem k účtu Azure.

Tuto funkci můžete povolit pro grafickou runbook přidáním datového `Add-AzureAccount` zdroje přihlašovacích údajů na plátno, následovaný matnou aktivitou, která používá datový zdroj pověření pro jeho vstup. Prohlédněte si následující příklad.

![Autentizační aktivity](media/automation-graphical-authoring-intro/authentication-activities.png)

Runbook se musí ověřit na začátku a po každém kontrolním bodu. Proto musíte použít `Add-AzureAccount` aktivitu `Checkpoint-Workflow` po jakékoli aktivitě. Není nutné použít další aktivitu pověření.

![Výstup aktivity](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="exporting-and-importing-a-graphical-runbook"></a>Export a import grafické sady Runbook

Exportovat lze pouze publikovanou verzi grafického seznamu runbook. Pokud runbook ještě nebyl publikován, tlačítko **Export** je zakázáno. Po klepnutí na tlačítko **Exportovat** se runbook stáhne do místního počítače. Název souboru odpovídá názvu sady Runbook s příponou **Graphrunbook.**

Grafický nebo grafický soubor sady runbook pracovního postupu prostředí PowerShell můžete importovat výběrem možnosti **Importovat** při přidávání sady Runbook. Když vyberete soubor, který chcete importovat, můžete zachovat stejný název nebo zadat nový. Pole **Typ knihy Runbook** zobrazuje typ runbooku poté, co vyhodnotí vybraný soubor. Pokud se pokusíte vybrat jiný typ, který není správný, grafický editor zobrazí zprávu s upozorněním, že existují potenciální konflikty a mohou se během převodu projevit chyby syntaxe.

![Import runbooku](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="testing-a-graphical-runbook"></a>Testování grafického runbooku

Každý grafický runbook v Azure Automation má koncept verze a publikované verze. Můžete spustit pouze publikovanou verzi, zatímco verzi Koncept můžete upravovat pouze. Jakékoliv změny konceptu nemají vliv na publikovanou verzi. Když je verze Koncept připravena k použití, publikujete ji, která přepíše aktuální publikovanou verzi s verzí Konceptu.

Koncept verze runbooku můžete otestovat na webu Azure Portal a publikovanou verzi ponechat beze změny. Případně můžete otestovat novou runbook před publikováním, abyste mohli ověřit, zda sada Runbook funguje správně před nahrazením jakékoli verze. Testování runbooku provede koncept verze a zajistí, že všechny akce, které provede, jsou dokončeny. Není vytvořena žádná historie úloh, ale podokno Výstup testu zobrazuje výstup.

Otevřete ovládací prvek Test pro grafickou runbook otevřením runbooku pro úpravy a klepnutím na **tlačítko Testovací podokno**. Ovládací prvek Test vyzve k zadání vstupních parametrů a spuštění runbooku můžete spustit klepnutím na tlačítko **Start**.

## <a name="publishing-a-graphical-runbook"></a>Publikování grafické sady Runbook

Publikujte grafickou knihu runbook otevřením runbooku pro úpravy a klepnutím na tlačítko **Publikovat**. Možné stavy pro runbook jsou:

* Nový -- runbook ještě nebyl publikován. 
* Publikováno – byl publikován runbook.
* V úpravách – runbook byl po publikování upraven a verze Koncept a Publikovaná se liší.

![Stavy runbooků](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Máte možnost vrátit se k publikované verzi sady Runbook. Tato operace zahodí všechny změny provedené od posledního publikování runbooku. Nahradí koncept verze runbooku publikovanou verzí.

## <a name="next-steps"></a>Další kroky

* Pokud chcete začít s runbooky pracovního postupu prostředí PowerShell, [přečtěte si první runbook pracovního postupu prostředí PowerShell](automation-first-runbook-textual.md).
* Informace o tom, jak začít s grafickými runbooky, najdete [v tématu Moje první grafická kniha runbook](automation-first-runbook-graphical.md).
* Další informace o typech runbooků a jejich výhodách a omezeních najdete v [tématu Typy runbooků Azure Automation](automation-runbook-types.md).
* Informace o tom, jak se ověřit pomocí účtu Automat spustit jako, najdete v [tématu Konfigurace účtu Azure Run As](automation-sec-configure-azure-runas-account.md).
* Odkaz na rutinu prostředí PowerShell naleznete v tématu [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
