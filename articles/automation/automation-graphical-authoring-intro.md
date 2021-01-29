---
title: Vytváření grafických runbooků v Azure Automation
description: V tomto článku se dozvíte, jak vytvořit grafický Runbook bez práce s kódem.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: ec74ca19978a4164289276d44b34eb14b694687f
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051577"
---
# <a name="author-graphical-runbooks-in-azure-automation"></a>Vytváření grafických runbooků v Azure Automation

Všechny Runbooky v Azure Automation jsou pracovní postupy prostředí Windows PowerShell. Grafické Runbooky a runbooky grafického pracovního postupu PowerShellu generují kód PowerShellu, který spouštějí pracovní procesy, ale nemůžete je zobrazit ani upravit. Grafický Runbook můžete převést na grafický Runbook PowerShellového pracovního postupu a naopak. Tyto Runbooky ale nemůžete převést na textový Runbook. Kromě toho grafický editor automatizace nemůže importovat textový Runbook.

Vytváření grafických sestav umožňuje vytvářet Runbooky pro Azure Automation bez složitosti základního kódu pracovního postupu prostředí Windows PowerShell nebo PowerShell. Aktivity můžete na plátno přidat z knihovny rutin a sad Runbook, propojit je dohromady a nakonfigurovat je tak, aby tvořily pracovní postup. Pokud jste někdy pracovali s nástrojem System Center Orchestrator nebo Service Management Automation (SMA), měl by vzhled grafiky vypadat dobře. Tento článek poskytuje Úvod k konceptům, které potřebujete, abyste mohli začít vytvářet grafický Runbook.

## <a name="overview-of-graphical-editor"></a>Přehled grafického editoru

Grafický editor můžete otevřít v Azure Portal vytvořením nebo úpravou grafického Runbooku.

![Grafický pracovní prostor](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

V následujících částech jsou popsány ovládací prvky v grafickém editoru.

### <a name="canvas-control"></a>Ovládací prvek plátna

Ovládací prvek plátna umožňuje návrh sady Runbook. Do sady Runbook můžete přidat aktivity z uzlů v ovládacím prvku knihovna a propojit je s odkazy k definování logiky sady Runbook. V dolní části plátna jsou ovládací prvky, které umožňují přiblížení a oddálení.

### <a name="library-control"></a>Řízení knihovny

Ovládací prvek knihovna umožňuje vybrat [aktivity](#use-activities) , které se mají přidat do Runbooku. Můžete je přidat na plátno, kde je můžete propojit s dalšími aktivitami. Ovládací prvek knihovna obsahuje oddíly, které jsou definovány v následující tabulce.

| Sekce | Description |
|:--- |:--- |
| Rutiny |Všechny rutiny, které lze použít v sadě Runbook. Rutiny jsou uspořádány podle modulu. Všechny moduly, které jste nainstalovali v účtu Automation, jsou k dispozici. |
| Runbooky |Runbooky v účtu Automation. Tyto Runbooky můžete přidat na plátno, které se použijí jako podřízené Runbooky. Zobrazují se jenom Runbooky stejného základního typu jako upravované sady Runbook. Pro grafické Runbooky se zobrazí pouze Runbooky založené na PowerShellu. Pro grafické Runbooky pracovních postupů PowerShellu se zobrazí jenom Runbooky založené na pracovním postupu PowerShellu. |
| Prostředky |[Prostředky služby Automation](/previous-versions/azure/dn939988(v=azure.100)) v účtu Automation, které můžete použít v Runbooku. Přidání assetu do Runbooku přidá aktivitu pracovního postupu, která získá vybraný Asset. V případě variabilních prostředků můžete vybrat, zda chcete přidat aktivitu, chcete-li získat proměnnou nebo nastavit proměnnou. |
| Řízení runbooku |Kontrolní aktivity, které lze použít v aktuální sadě Runbook. Aktivita spojení přebírá několik vstupů a čeká na dokončení všech kroků před pokračováním pracovního postupu. Aktivita kódu v závislosti na typu grafického Runbooku spouští jeden nebo několik řádků kódu pracovního postupu PowerShellu nebo PowerShellu. Tuto aktivitu můžete použít pro vlastní kód nebo pro funkce, které je obtížné dosáhnout s ostatními aktivitami. |

### <a name="configuration-control"></a>Řízení konfigurace

Ovládací prvek konfigurace umožňuje zadat podrobnosti o objektu, který je vybrán na plátně. Vlastnosti, které jsou k dispozici v tomto ovládacím prvku, závisí na typu vybraného objektu. Když zvolíte možnost v ovládacím prvku konfigurace, otevře se další okno, kde najdete další informace.

### <a name="test-control"></a>Řízení testu

Ovládací prvek test není zobrazen při prvním spuštění grafického editoru. Otevře se při interaktivním testování grafického Runbooku.

## <a name="use-activities"></a>Aktivity použití

Aktivity jsou stavebními kameny sady Runbook. Aktivitou může být rutina prostředí PowerShell, podřízená sada Runbook nebo pracovní postup. Do sady Runbook můžete přidat aktivitu kliknutím na ni pravým tlačítkem v ovládacím prvku knihovna a výběrem možnosti **Přidat na plátno**. Pak můžete kliknout a přetáhnout aktivitu a umístit ji kamkoli na plátno, které chcete. Umístění aktivity na plátně nemá vliv na operaci sady Runbook. Svůj Runbook můžete rozvrhnout jakýmkoli způsobem, který můžete najít nejlépe, aby bylo možné vizualizovat jeho činnost.

![Přidat na plátno](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Vyberte aktivitu na plátně pro konfiguraci vlastností a parametrů v okně konfigurace. Popisek aktivity můžete změnit na název, který najdete s popisným názvem. Sada Runbook stále spouští původní rutinu. Jednoduše změníte zobrazované jméno, které používá grafický editor. Všimněte si, že popisek musí být v rámci sady Runbook jedinečný.

### <a name="parameter-sets"></a>Sady parametrů

Sada parametrů definuje povinné a volitelné parametry, které přijímají hodnoty pro konkrétní rutinu. Všechny rutiny mají alespoň jednu sadu parametrů a některé mají několik sad. Pokud má rutina více sad parametrů, je nutné před konfigurací parametrů vybrat, která se má použít. Sadu parametrů použitou v aktivitě můžete změnit tak, že vyberete **sadu parametrů** a vyberete jinou sadu. V takovém případě ztratí všechny hodnoty parametrů, které jste již nakonfigurovali.

V následujícím příkladu má rutina [Get-AzVM](/powershell/module/az.compute/get-azvm?view=azps-3.5.0&preserve-view=true) tři sady parametrů. V příkladu se používá jedna sada s názvem **ListVirtualMachineInResourceGroupParamSet** s jedním volitelným parametrem pro vrácení všech virtuálních počítačů ve skupině prostředků. V příkladu se používá také sada parametrů **GetVirtualMachineInResourceGroupParamSet** k zadání virtuálního počítače, který se má vrátit. Tato sada má dva povinné parametry a jeden volitelný parametr.

![Sada parametrů](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Hodnoty parametrů

Když zadáte hodnotu parametru, vyberete zdroj dat, abyste určili, jak je hodnota zadaná. Zdroje dat, které jsou k dispozici pro konkrétní parametr, závisí na platných hodnotách pro daný parametr. Například hodnota null není dostupná možnost pro parametr, který nepovoluje hodnoty null.

| Zdroj dat | Description |
|:--- |:--- |
| Hodnota konstanty |Zadejte hodnotu parametru. Tento zdroj dat je k dispozici pouze pro následující datové typy: Int32, Int64, String, Boolean, DateTime, Switch. |
| Výstup aktivity |Použijte výstup aktivity, která předchází aktuální aktivitě v pracovním postupu. V seznamu jsou uvedeny všechny platné aktivity. Pro hodnotu parametru použijte pouze aktivitu, která vytváří výstup. V případě, že výstupem aktivity je objekt s více vlastnostmi, můžete po výběru aktivity zadat název určité vlastnosti. |
| Vstup Runbooku |Vyberte vstup Runbooku jako vstup pro parametr Activity. |
| Variabilní prostředek |Jako vstup vyberte proměnnou automatizace. |
| Prostředek přihlašovacích údajů |Jako vstup vyberte přihlašovací údaje automatizace. |
| Prostředek certifikátu |Jako vstup vyberte certifikát Automation. |
| Prostředek připojení |Jako vstup vyberte připojení Automation. |
| Powershellový výraz |Zadejte jednoduchý [powershellový výraz](#work-with-powershell-expressions). Výraz je vyhodnocen před aktivitou a výsledek je použit pro hodnotu parametru. Proměnné můžete použít k odkazování na výstup aktivity nebo vstupní parametr Runbooku. |
| Nenakonfigurováno |Vymažte všechny dříve nakonfigurované hodnoty. |

#### <a name="optional-additional-parameters"></a>Nepovinné další parametry

Všechny rutiny mají možnost zadat další parametry. Jedná se o běžné parametry PowerShellu nebo jiné vlastní parametry. Grafický editor zobrazí textové pole, kde můžete zadat parametry pomocí syntaxe prostředí PowerShell. Chcete-li například použít `Verbose` společný parametr, je třeba zadat `-Verbose:$True` .

### <a name="retry-activity"></a>Aktivita opakování

Funkce opakování pro aktivitu umožňuje, aby ji bylo možné spustit několikrát, dokud není splněna konkrétní podmínka, podobně jako smyčka. Tuto funkci můžete použít pro aktivity, které by se měly spouštět víckrát, jsou náchylné k chybám, může vyžadovat více než jeden pokus o úspěch nebo otestovat výstupní informace aktivity pro platná data.

Když u aktivity povolíte operaci opakovat, můžete nastavit zpoždění a podmínku. Zpoždění je čas (měřeno v sekundách nebo minutách), po který sada Runbook počká, než znovu spustí aktivitu. Pokud nezadáte prodlevu, aktivita se spustí znovu ihned po dokončení.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-delay.png" alt-text="Snímek obrazovky s nastavením funkcí povolit opakování":::

Podmínka opakování je výraz PowerShellu, který se vyhodnocuje po každém spuštění aktivity. Pokud se výraz přeloží na hodnotu true, aktivita se znovu spustí. Pokud se výraz přeloží na false, aktivita se znovu nespustí a Runbook se přesune na další aktivitu.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-condition.png" alt-text="Snímek obrazovky znázorňující opakování, dokud tato podmínka není pravdivá, a příklady výrazů PowerShellu, které lze použít v podmínce opakování.":::

Podmínka opakování může použít proměnnou s názvem `RetryData` , která poskytuje přístup k informacím o opakovaných pokusech o aktivitu. Tato proměnná má vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| `NumberOfAttempts` |Počet, kolikrát byla aktivita spuštěna. |
| `Output` |Výstup z posledního spuštění aktivity |
| `TotalDuration` |Čas od prvního spuštění aktivity vypršel. |
| `StartedAt` |Čas (ve formátu UTC) při prvním spuštění aktivity. |

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

Po nakonfigurování podmínky opakování pro aktivitu obsahuje aktivita dvě vizuální pomůcky, které vám připomínat. Jedna je prezentována v aktivitě a druhá se zobrazí při kontrole konfigurace aktivity.

![Vizuální indikátory opakování aktivity](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Řízení skriptu pracovního postupu

Řízení skriptu pracovního postupu je speciální aktivita, která přijímá PowerShell nebo skript pracovního postupu PowerShellu v závislosti na typu vytvářeného grafického Runbooku. Tento ovládací prvek poskytuje funkce, které nemusí být k dispozici jiným způsobem. Nemůže přijímat parametry, ale může použít proměnné pro výstup aktivity a vstupní parametry Runbooku. Do datové sběrnice se přidá jakýkoliv výstup aktivity. Výjimka je výstup bez odchozího propojení. v takovém případě se výstup přidá do výstupu Runbooku.

Například následující kód provádí výpočty data pomocí vstupní proměnné Runbooku s názvem `NumberOfDays` . Pak pošle vypočítanou hodnotu DateTime jako výstup, který se použije v následných aktivitách v Runbooku.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="use-links-for-workflow"></a>Použít odkazy pro pracovní postup

Odkaz v grafickém Runbooku spojuje dvě aktivity. Zobrazuje se na plátně jako šipka ukazující od zdrojové aktivity k cílové aktivitě. Aktivity jsou spouštěny ve směru šipky s cílovou aktivitou začínající po dokončení zdrojové aktivity.

### <a name="create-a-link"></a>Vytvořit odkaz

Propojení dvou aktivit můžete vytvořit tak, že vyberete zdrojovou aktivitu a kliknete na kolečko v dolní části obrazce. Přetáhněte šipku na cílovou aktivitu a vydanou verzi.

![Vytvořit odkaz](media/automation-graphical-authoring-intro/create-link-options.png)

Vyberte odkaz pro konfiguraci vlastností v okně konfigurace. Vlastnosti obsahují typ odkazu, který je popsán v následující tabulce.

| Typ odkazu | Description |
|:--- |:--- |
| Kanál |Cílová aktivita se spustí jednou pro každý výstup objektu ze zdrojové aktivity. Cílová aktivita se nespustí, pokud výsledkem zdrojové aktivity není žádný výstup. Výstup zdrojové aktivity je k dispozici jako objekt. |
| Sequence |Cílová aktivita se spustí pouze jednou při přijetí výstupu ze zdrojové aktivity. Výstup zdrojové aktivity je k dispozici jako pole objektů. |

### <a name="start-runbook-activity"></a>Aktivita spuštění Runbooku

Grafický Runbook začíná všemi aktivitami, které nemají příchozí propojení. Často je k dispozici pouze jedna aktivita, která funguje jako počáteční aktivita pro sadu Runbook. Pokud příchozí odkaz neobsahuje více aktivit, sada Runbook se spustí paralelně. Postupuje podle odkazů ke spuštění dalších aktivit v průběhu každého dokončení.

### <a name="specify-link-conditions"></a>Zadat podmínky propojení

Když zadáte podmínku na odkaz, cílová aktivita bude spuštěna pouze v případě, že se podmínka vyřeší na hodnotu true. Při `ActivityOutput` načítání výstupu ze zdrojové aktivity obvykle používáte proměnnou v podmínce.

Pro propojení kanálu je nutné zadat podmínku pro jeden objekt. Sada Runbook vyhodnotí podmínku pro každý výstup objektu zdrojovou aktivitou. Potom spustí cílovou aktivitu pro každý objekt, který splňuje podmínku. Například u zdrojové aktivity nástroje `Get-AzVM` můžete použít následující syntaxi pro odkaz podmíněného kanálu k načtení pouze virtuálních počítačů ve skupině prostředků s názvem Group1.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

V případě odkazu na sekvenci sada Runbook vyhodnotí podmínku pouze jednou, protože je vráceno jedno pole obsahující všechny objekty ze zdrojové aktivity. Z tohoto důvodu sada Runbook nemůže použít odkaz na sekvenci pro filtrování, jako by to mohlo mít odkaz na kanál. Odkaz sekvence může jednoduše určit, jestli se má spustit další aktivita.

Například proveďte následující sadu aktivit v sadě Runbook **spuštění virtuálního počítače** :

![Podmíněný odkaz s sekvencemi](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Sada Runbook používá tři různé sekvenční odkazy, které ověřují hodnoty vstupních parametrů `VMName` a `ResourceGroupName` určují, jakou akci chcete provést. Možné akce jsou spouštěny jediným virtuálním počítačem, spustí všechny virtuální počítače ve skupině prostředků nebo spustí všechny virtuální počítače v rámci předplatného. Pro propojení sekvence mezi `Connect to Azure` a `Get single VM` tady je logika podmínky:

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

Když použijete podmíněné propojení, data dostupná ze zdrojové aktivity do jiných aktivit v této větvi se filtrují podle podmínky. Pokud je aktivita zdrojem více odkazů, data dostupná pro aktivity v jednotlivých větvích závisí na stavu odkazu připojení k této větvi.

Například `Start-AzVM` aktivita v sadě Runbook spustí všechny virtuální počítače. Má dvě podmíněné odkazy. První podmíněný odkaz používá výraz `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` k filtrování, pokud se `Start-AzVM` aktivita úspěšně dokončí. Druhý podmíněný odkaz používá výraz `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` k filtrování, pokud se `Start-AzVm` aktivitě nepodařilo spustit virtuální počítač.

![Příklad podmíněného odkazu](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Všechny aktivity, které následují za prvním odkazem a využívají výstup aktivity `Get-AzureVM` pouze načte virtuální počítače, které byly spuštěny v době `Get-AzureVM` spuštění. Všechny aktivity, které následují za druhým odkazem, získávají pouze virtuální počítače, které byly zastaveny v době `Get-AzureVM` spuštění. Všechny aktivity, které následují po třetím odkazu, vrátí všechny virtuální počítače bez ohledu na jejich běžící stav.

### <a name="use-junctions"></a>Použití spojení

Spojení je speciální aktivita, která čeká na dokončení všech příchozích větví. To umožňuje, aby sada Runbook spouštěla více aktivit paralelně a před přechodem na službu dokončila vše.

V případě, že spojení může mít neomezený počet příchozích odkazů, může být pouze jedním z těchto odkazů kanál. Počet příchozích odkazů na sekvence není omezený. Můžete vytvořit spojení s více příchozími odkazy na kanály a sadu Runbook uložit, ale při spuštění dojde k chybě.

Níže uvedený příklad je součástí sady Runbook, která spouští sadu virtuálních počítačů a současně stahuje opravy, které se mají na tyto počítače použít. Pomocí spojení se ujistěte, že jsou oba procesy dokončeny před tím, než sada Runbook pokračuje.

![Spojení](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="work-with-cycles"></a>Práce s cykly

Cyklus se vytvoří, když cílová aktivita odkazuje zpátky na svou zdrojovou aktivitu nebo na jinou aktivitu, která nakonec odkazuje zpět na svůj zdroj. Vytváření grafického obsahu v současné době nepodporuje cykly. Pokud má vaše sada Runbook cyklus, uloží se správně, ale při spuštění obdrží chybu.

![Cyklické](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="share-data-between-activities"></a>Sdílení dat mezi aktivitami

Všechna data, která jsou výstupem aktivity s odchozím odkazem, jsou zapsána do datové sběrnice pro sadu Runbook. Jakékoli aktivity v sadě Runbook mohou použít data v datové sběrnice k naplnění hodnot parametrů nebo zahrnutí do kódu skriptu. Aktivita má přístup k výstupu jakékoli předchozí aktivity v pracovním postupu.

Způsob zápisu dat do datové sběrnice závisí na typu odkazu na aktivitu. Pro propojení kanálu jsou data ve výstupu jako více objektů. Pro propojení sekvence je výstupem data jako pole. Pokud je k dispozici pouze jedna hodnota, jedná se o výstup jako pole s jedním prvkem.

Sada Runbook má dva způsoby, jak získat přístup k datům na datové sběrnice: 
* Použijte výstupní zdroj dat aktivity.
* Použijte zdroj dat výrazu PowerShellu.

První mechanismus používá výstupní zdroj dat aktivity k naplnění parametru jiné aktivity. Pokud je výstupem objekt, sada Runbook může určit jednu vlastnost.

![výstup aktivity](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Druhý mechanismus pro přístup k datům načte výstup aktivity ve zdroji dat výrazu PowerShellu nebo aktivity skriptu pracovního postupu s `ActivityOutput` proměnnou pomocí syntaxe uvedené níže. Pokud je výstupem objekt, sada Runbook může určit jednu vlastnost.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="use-checkpoints"></a>Použít kontrolní body

Můžete nastavit [kontrolní body](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) v grafickém Runbooku pracovního postupu PowerShellu výběrem **kontrolního bodu Runbooku** u jakékoli aktivity. Tím dojde k nastavení kontrolního bodu po spuštění aktivity.

![CheckPoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

Kontrolní body jsou povolené jenom v diagramech grafického pracovního postupu PowerShellu a nejsou k dispozici v grafických sadách Runbook. Pokud sada Runbook používá rutiny Azure, měla by následovat po všech kontrolních aktivitách s `Connect-AzAccount` aktivitou. Operace připojení se používá v případě, že je Runbook pozastaven a musí se restartovat z tohoto kontrolního bodu na jiném pracovním procesu.

## <a name="handle-runbook-input"></a>Zpracování vstupu Runbooku

Sada Runbook vyžaduje vstup od uživatele, který spouští Runbook, prostřednictvím Azure Portal nebo z jiné sady Runbook, pokud je aktuální použit jako podřízený. Například pro sadu Runbook, která vytvoří virtuální počítač, může uživatel zadat takové informace jako název virtuálního počítače a další vlastnosti při každém spuštění sady Runbook.

Sada Runbook přijímá vstup definováním jednoho nebo více vstupních parametrů. Uživatel poskytne hodnoty pro tyto parametry pokaždé, když se sada Runbook spustí. Když uživatel spustí Runbook pomocí Azure Portal, zobrazí se uživateli výzva k zadání hodnot pro každý vstupní parametr podporovaný sadou Runbook.

Při vytváření sady Runbook můžete ke svým vstupním parametrům přistupovat kliknutím na **vstup a výstup** na panelu nástrojů sady Runbook. Tím se otevře ovládací prvek vstup a výstup, kde můžete upravit existující vstupní parametr nebo vytvořit nový kliknutím na **Přidat vstup**.

![Přidání vstupu](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Jednotlivé vstupní parametry jsou definovány vlastnostmi v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| Název | Povinná hodnota. Název parametru Název musí být v rámci sady Runbook jedinečný. Musí začínat písmenem a může obsahovat jenom písmena, číslice a podtržítka. Název nesmí obsahovat mezery. |
| Description |Nepovinný parametr. Popis účelu pro vstupní parametr |
| Typ | Nepovinný parametr. Pro hodnotu parametru se očekával datový typ. Azure Portal poskytuje vhodný ovládací prvek pro datový typ pro každý parametr při zobrazení výzvy pro vstup. Podporované typy parametrů jsou String, Int32, Int64, Decimal, Boolean, DateTime a Object. Pokud není vybraný datový typ, použije se výchozí hodnota String (řetězec).|
| Povinné | Nepovinný parametr. Nastavení, které určuje, zda musí být pro parametr zadána hodnota. Zvolíte-li `yes` tuto možnost, musí být při spuštění sady Runbook zadána hodnota. Zvolíte-li možnost `no` , není při spuštění sady Runbook požadována hodnota a je možné použít výchozí hodnotu. Sadu Runbook nelze spustit, pokud nezadáte hodnotu pro každý povinný parametr, který nemá definovánu výchozí hodnotu. |
| Výchozí hodnota | Nepovinný parametr. Hodnota použitá pro parametr, pokud není předána při spuštění Runbooku. Pokud chcete nastavit výchozí hodnotu, vyberte `Custom` . Tuto možnost vyberte `None` , pokud nechcete zadat žádnou výchozí hodnotu. |

## <a name="handle-runbook-output"></a>Zpracovat výstup Runbooku

Vytváření grafického obsahu ukládá data vytvořená aktivitou, která nemá odchozí odkaz na [Výstup runbooku](./automation-runbook-output-and-messages.md). Výstup je uložen s úlohou Runbooku a je k dispozici pro nadřazenou sadu Runbook, pokud je sada Runbook používána jako podřízená položka.

## <a name="work-with-powershell-expressions"></a>Práce s výrazy PowerShellu

Jednou z výhod grafického vytváření je to, že vám umožní vytvořit Runbook s minimálními znalostmi prostředí PowerShell. V současné době ale potřebujete znát bitovou kopii PowerShellu pro naplnění určitých [hodnot parametrů](#use-activities) a pro nastavení [podmínek propojení](#use-links-for-workflow). V této části najdete stručný úvod do PowerShellových výrazů. Úplné podrobnosti o PowerShellu jsou k dispozici v skriptování v prostředí [Windows PowerShell](/powershell/scripting/overview).

### <a name="use-a-powershell-expression-as-a-data-source"></a>Použití výrazu PowerShellu jako zdroje dat

Výraz PowerShellu můžete použít jako zdroj dat k naplnění hodnoty [parametru aktivity](#use-activities) s výsledky kódu prostředí PowerShell. Výraz může být jeden řádek kódu, který provádí jednoduchou funkci nebo více řádků, které provádějí určitou komplexní logiku. Libovolný výstup příkazu, který není přiřazen proměnné, je výstupem hodnoty parametru.

Například následující příkaz vypíše aktuální datum.

```powershell-interactive
Get-Date
```

Další fragment kódu vytvoří řetězec z aktuálního data a přiřadí ho k proměnné. Kód pošle obsah proměnné do výstupu.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Následující příkazy vyhodnotí aktuální datum a vrátí řetězec, který označuje, zda je aktuální den víkend nebo den v týdnu.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="use-activity-output"></a>Použít výstup aktivity

Chcete-li použít výstup z předchozí aktivity v sadě Runbook, použijte `ActivityOutput` proměnnou s následující syntaxí.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Například můžete mít aktivitu s vlastností, která vyžaduje název virtuálního počítače. V takovém případě může sada Runbook použít následující výraz.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Pokud vlastnost vyžaduje objekt virtuálního počítače namísto pouze názvu, sada Runbook vrátí celý objekt pomocí následující syntaxe.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Sada Runbook může použít výstup aktivity ve složitějším výrazu, například následující. Tento výraz zřetězí text k názvu virtuálního počítače.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="compare-values"></a>Porovnat hodnoty

[Operátory porovnání](/powershell/module/microsoft.powershell.core/about/about_comparison_operators) použijte k porovnání hodnot nebo určení, zda hodnota odpovídá zadanému vzoru. Porovnání vrátí hodnotu buď true, nebo false.

Například následující podmínka Určuje, jestli je virtuální počítač z aktivity s názvem `Get-AzureVM` aktuálně zastavený.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

Následující podmínka Určuje, zda je stejný virtuální počítač v jiném stavu než zastaveno.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Pomocí [logického operátoru](/powershell/module/microsoft.powershell.core/about/about_logical_operators), například nebo, můžete ve svém Runbooku spojit několik `-and` podmínek `-or` . Například následující podmínka kontroluje, jestli je virtuální počítač v předchozím příkladu ve stavu zastaveno nebo zastavování.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="use-hashtables"></a>Použití zatřiďovacími tabulkami

[Zatřiďovacími tabulkami](/powershell/module/microsoft.powershell.core/about/about_hash_tables) jsou páry název-hodnota, které jsou užitečné pro vrácení sady hodnot. Může se také zobrazit zatřiďovací tabulka, na kterou se odkazuje jako na slovník. Vlastnosti pro určité aktivity očekávají zatřiďovací tabulku namísto jednoduché hodnoty.

Pomocí následující syntaxe vytvořte zatřiďovací tabulku. Může obsahovat libovolný počet položek, ale každá je definována pomocí názvu a hodnoty.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Například následující výraz vytvoří zatřiďovací tabulku, která bude použita jako zdroj dat pro parametr aktivity, který očekává hodnotu hash hodnot pro hledání v Internetu.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

Následující příklad používá výstup z aktivity volané `Get Twitter Connection` k naplnění zatřiďovací tabulky.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticate-to-azure-resources"></a>Ověřování v prostředcích Azure

Sady Runbook v Azure Automation, které spravují prostředky Azure, vyžadují ověřování do Azure. [Účet Spustit jako](./automation-security-overview.md), označovaný také jako instanční objekt, je výchozím mechanismem, který Runbook služby Automation používá pro přístup k Azure Resource Manager prostředkům ve vašem předplatném. Tuto funkci můžete přidat do grafického Runbooku přidáním `AzureRunAsConnection` assetu připojení, který na plátně používá rutinu PowerShellu [Get-AutomationConnection](/system-center/sma/manage-global-assets) . Můžete také přidat rutinu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Tento scénář je znázorněn v následujícím příkladu.

![Aktivity ověřování spustit jako](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

`Get Run As Connection`Aktivita nebo `Get-AutomationConnection` je nakonfigurována pomocí datového zdroje s konstantní hodnotou s názvem `AzureRunAsConnection` .

![Konfigurace připojení spustit jako](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

Další aktivita `Connect-AzAccount` přidá ověřený účet Spustit jako pro použití v Runbooku.

![Sada parametrů Connect-AzAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>Pro PowerShellové Runbooky `Add-AzAccount` a `Add-AzureRMAccount` jsou aliasy pro `Connect-AzAccount` . Všimněte si, že tyto aliasy nejsou k dispozici pro vaše grafické Runbooky. Grafická sada Runbook může používat pouze `Connect-AzAccount` sebe sama.

Pro pole parametrů **APPLICATIONID**, **CERTIFICATETHUMBPRINT** a **TENANTID** zadejte název vlastnosti pro cestu k poli, protože výstupem aktivity je objekt s více vlastnostmi. V opačném případě, když se sada Runbook spustí, při pokusu o ověření dojde k chybě. To je to, co potřebujete minimálně k ověření vaší sady Runbook pomocí účtu Spustit jako.

Někteří předplatitelé vytvoří účet Automation pomocí [uživatelského účtu Azure AD](./shared-resources/credentials.md) ke správě nasazení Azure Classic nebo k Azure Resource Manager prostředkům. Aby se zajistila zpětná kompatibilita pro tyto předplatitele, je mechanismus ověřování, který se má použít ve vašem Runbooku, `Add-AzureAccount` rutina s [Assetem přihlašovacích údajů](./shared-resources/credentials.md). Asset představuje uživatele služby Active Directory s přístupem k účtu Azure.

Tuto funkci můžete pro svůj grafický Runbook povolit přidáním assetu přihlašovacích údajů na plátno následovaný `Add-AzureAccount` aktivitou, která pro svůj vstup používá prostředek přihlašovacích údajů. Prohlédněte si následující příklad.

![Aktivity ověřování](media/automation-graphical-authoring-intro/authentication-activities.png)

Runbook se musí ověřit při spuštění a za každým kontrolním bodem. Proto je nutné použít `Add-AzureAccount` aktivitu po `Checkpoint-Workflow` aktivitě. Nemusíte používat aktivitu další přihlašovací údaje.

![Výstup aktivity](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="export-a-graphical-runbook"></a>Export grafického Runbooku

Můžete exportovat jenom publikovanou verzi grafického Runbooku. Pokud sada Runbook ještě nebyla publikována, tlačítko **exportovat** je zakázáno. Po kliknutí na tlačítko **exportovat** se sada Runbook stáhne do místního počítače. Název souboru se shoduje s názvem sady Runbook s příponou **. graphrunbook** .

## <a name="import-a-graphical-runbook"></a>Import grafického Runbooku

Můžete importovat grafický nebo grafický PowerShellový soubor pracovního postupu PowerShellu tak, že při přidávání Runbooku vyberete možnost **Import** . Když vyberete soubor, který se má importovat, můžete si nechat stejný název nebo zadat nový. V poli **typ Runbooku** se zobrazí typ Runbooku poté, co vybraný soubor vyhodnotí. Pokud se pokusíte vybrat jiný typ, který není správný, grafický editor zobrazí zprávu, že došlo k potenciálním konfliktům a že při převodu dojde k chybám syntaxe.

![Importovat Runbook](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="test-a-graphical-runbook"></a>Test grafického Runbooku

Každá Grafická sada Runbook v Azure Automation má verzi konceptu a publikovanou verzi. Můžete spustit jenom publikovanou verzi, ale můžete upravit jenom koncept verze. Jakékoliv změny konceptu nemají vliv na publikovanou verzi. Když je koncept konceptu připravený k použití, publikujete ho a přepíše aktuální publikovanou verzi pomocí vaší verze konceptu.

Verzi konceptu sady Runbook můžete testovat v Azure Portal, zatímco publikovaná verze zůstane beze změny. Alternativně můžete otestovat novou sadu Runbook před tím, než bude publikována, abyste mohli ověřit, že sada Runbook funguje správně ještě před nahrazením verzí. Testování Runbooku spustí koncept verze a zajistí, že se dokončí všechny akce, které provádí. Nevytvoří se žádná historie úlohy, ale zobrazí se výstup v podokně výstup testu.

Otevřete ovládací prvek test pro grafický Runbook tak, že otevřete Runbook pro úpravy a potom kliknete na **testovací podokno**. Řízení testu vyzve k zadání vstupních parametrů a sadu Runbook můžete spustit kliknutím na tlačítko **Start**.

## <a name="publish-a-graphical-runbook"></a>Publikování grafického Runbooku

Publikování grafického Runbooku otevřením Runbooku pro úpravy a následným kliknutím na **publikovat**. Možné stavy pro Runbook:

* New--sada Runbook ještě nebyla publikována. 
* Publikováno – sada Runbook byla publikována.
* V části Upravit – sada Runbook byla upravena po publikování a verze konceptu a publikovaných verzí se liší.

![Stavy Runbooku](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Máte možnost vrátit se k publikované verzi Runbooku. Tato operace vyvolává všechny změny provedené od posledního publikování Runbooku. Nahrazuje verzi konceptu Runbooku publikovanou verzí.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak začít s grafickými Runbooky, najdete v tématu [kurz: Vytvoření grafického Runbooku](learn/automation-tutorial-runbook-graphical.md).
* Další informace o typech runbooků a jejich výhodách a omezeních najdete v tématu [Azure Automation typy runbooků](automation-runbook-types.md).
* Informace o ověřování pomocí účtu Spustit jako pro automatizaci najdete v tématu [účet Spustit jako](automation-security-overview.md#run-as-account).
* Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation/?view=azps-3.7.0&preserve-view=true#automation).
