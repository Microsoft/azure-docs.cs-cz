---
title: Grafické vytváření v Azure Automation
description: Vytváření grafických sestav umožňuje vytvářet Runbooky pro Azure Automation bez práce s kódem. Tento článek poskytuje Úvod do grafického vytváření a všechny podrobnosti potřebné k zahájení vytváření grafického Runbooku.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 82a06510bd9d1e0de2b38260773cb4848156bf12
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850291"
---
# <a name="graphical-authoring-in-azure-automation"></a>Grafické vytváření v Azure Automation

Vytváření grafických sestav umožňuje vytvářet Runbooky pro Azure Automation bez složitosti základního kódu pracovního postupu prostředí Windows PowerShell nebo PowerShell. Aktivity můžete na plátno přidat z knihovny rutin a runbooků, propojit je dohromady a nakonfigurovat tak, aby tvořily pracovní postup. Pokud jste někdy pracovali s nástrojem System Center Orchestrator nebo Service Management Automation (SMA), mělo by to vypadat dobře.

Tento článek poskytuje Úvod do grafického vytváření a konceptů, které potřebujete, abyste mohli začít s vytvářením grafického Runbooku.

## <a name="graphical-runbooks"></a>Grafické Runbooky

Všechny Runbooky v Azure Automation jsou pracovní postupy prostředí Windows PowerShell. Grafické a grafické Runbooky pracovních postupů PowerShellu generují kód PowerShellu, který běží na pracovních procesech automatizace, ale nemůžete ho zobrazit ani ho přímo upravovat. Grafický Runbook se dá převést na grafický Runbook pracovního postupu PowerShellu a naopak, ale nedá se převést na textový Runbook. Existující textový Runbook nelze importovat do grafického editoru.

## <a name="overview-of-graphical-editor"></a>Přehled grafického editoru

Grafický editor můžete otevřít v Azure Portal vytvořením nebo úpravou grafického Runbooku.

![Grafický pracovní prostor](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

V následujících částech jsou popsány ovládací prvky v grafickém editoru.

### <a name="canvas"></a>Plátno

Plátno je místo, kde můžete Runbook navrhovat. Přidáte aktivity z uzlů v ovládacím prvku knihovna do sady Runbook a připojíte je s odkazy k definování logiky sady Runbook.

V dolní části plátna můžete použít ovládací prvky pro přiblížení a oddálení.

### <a name="library-control"></a>Řízení knihovny

Ovládací prvek knihovna je místo, kde vyberete [aktivity](#activities) , které chcete přidat do Runbooku. Můžete je přidat na plátno, kde je propojíte s jinými aktivitami. Obsahuje čtyři části, které jsou popsány v následující tabulce:

| Sekce | Popis |
|:--- |:--- |
| Rutiny |Zahrnuje všechny rutiny, které se dají použít v Runbooku. Rutiny jsou uspořádány podle modulu. K dispozici jsou všechny moduly, které jste nainstalovali v účtu Automation. |
| Runbooky |Obsahuje Runbooky v účtu Automation. Tyto Runbooky lze přidat na plátno, aby je bylo možné použít jako podřízené Runbooky. Zobrazí se pouze Runbooky stejného základního typu, jako je sada Runbook upravována; pro grafické Runbooky se zobrazují jenom Runbooky založené na PowerShellu, zatímco pro grafické PowerShellové PowerShellové Runbooky se zobrazují jenom Runbooky PowerShellu založené na pracovním postupu. |
| Prostředky |Zahrnuje [prostředky služby Automation](/previous-versions/azure/dn939988(v=azure.100)) v účtu Automation, které se dají použít v Runbooku. Když přidáte Asset do Runbooku, přidá aktivitu pracovního postupu, která získá vybraný Asset. V případě variabilních prostředků můžete vybrat, zda chcete přidat aktivitu, chcete-li získat proměnnou nebo nastavit proměnnou. |
| Řízení Runbooku |Zahrnuje aktivity řízení sady Runbook, které lze použít v aktuální sadě Runbook. *Spojení* trvá několik vstupů a čeká na dokončení všech kroků před pokračováním pracovního postupu. Aktivita *kódu* v závislosti na typu grafického Runbooku spustí jeden nebo více řádků kódu pracovního postupu PowerShellu nebo PowerShellu. Tuto aktivitu můžete použít pro vlastní kód nebo pro funkce, které je obtížné dosáhnout s ostatními aktivitami. |

### <a name="configuration-control"></a>Řízení konfigurace

Ovládací prvek konfigurace je místo, kde můžete zadat podrobnosti pro objekt vybraný na plátně. Vlastnosti, které jsou k dispozici v tomto ovládacím prvku, závisí na typu vybraného objektu. Když vyberete možnost v ovládacím prvku konfigurace, otevře se další okna, aby bylo možné poskytnout další informace.

### <a name="test-control"></a>Řízení testu

Ovládací prvek test není zobrazen při prvním spuštění grafického editoru. Otevře se při interaktivním [testování grafického Runbooku](#graphical-runbook-procedures).

## <a name="graphical-runbook-procedures"></a>Procedury grafického Runbooku

### <a name="exporting-and-importing-a-graphical-runbook"></a>Export a Import grafického Runbooku

Můžete exportovat jenom publikovanou verzi grafického Runbooku. Pokud sada Runbook ještě nebyla publikována, tlačítko **exportovat** je zakázáno. Po kliknutí na tlačítko **exportovat** se sada Runbook stáhne do místního počítače. Název souboru se shoduje s názvem Runbooku s příponou *graphrunbook* .

Můžete importovat grafický nebo grafický PowerShellový soubor pracovního postupu PowerShellu tak, že při přidávání Runbooku vyberete možnost **Import** . Když vyberete soubor, který se má importovat, můžete si nechat stejný **název** nebo zadat nový. V poli Typ Runbooku se zobrazí typ Runbooku poté, co vybraný soubor vyhodnotí, a pokud se pokusíte vybrat jiný typ, který není správný, zobrazí se zpráva, která znamená, že došlo k potenciálním konfliktům a v průběhu převodu existuje syntaxe. vyskytl.

![Importovat Runbook](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>Testování grafického Runbooku

Můžete otestovat koncept sady Runbook v Azure Portal, zatímco se publikovaná verze Runbooku nezměnila, nebo můžete před publikováním otestovat novou sadu Runbook. To umožňuje ověřit, že runbook správně funguje, před nahrazením publikované verze. Při testování runbooku koncept runbooku se spustí a jsou dokončeny všechny akce, které aplikace provádí. Není vytvořena žádná historie úlohy, ale výstup se zobrazí v podokně výstup testu.

Otevřete ovládací prvek test pro sadu Runbook otevřením Runbooku pro úpravu a pak klikněte na tlačítko **testovací podokno** .

Řízení testu vyzve k zadání vstupních parametrů a sadu Runbook můžete spustit kliknutím na tlačítko **Start** .

### <a name="publishing-a-graphical-runbook"></a>Publikování grafického Runbooku

Každá sada Runbook v Azure Automation má koncept a publikovanou verzi. Je možné spustit jenom publikovaná verze a lze upravovat pouze verzi konceptu. Publikovaná verze není ovlivněn změny konceptu. Až bude verze konceptu připravena k dispozici, pak ji publikujete, čímž dojde k přepsání publikované verze verzí konceptu.

Grafický Runbook můžete publikovat tak, že otevřete Runbook pro úpravy a potom kliknete na tlačítko **publikovat** .

Pokud sada Runbook ještě nebyla publikována, má stav **nové**. Při publikování má stav **Publikováno**. Pokud sadu Runbook po publikování upravíte a verze koncept a publikace jsou odlišné, má sada Runbook stav **v úpravách**.

![Stavy Runbooku](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Máte také možnost vrátit se k publikované verzi Runbooku. To vyvolává všechny změny provedené od posledního publikování Runbooku a nahrazuje koncept verze Runbooku publikovanou verzí.

## <a name="activities"></a>Aktivity

Aktivity jsou stavebními kameny sady Runbook. Aktivitou může být rutina prostředí PowerShell, podřízená sada Runbook nebo aktivita pracovního postupu. Aktivitu přidáte do Runbooku tak, že na ni kliknete pravým tlačítkem v ovládacím prvku knihovna a vyberete **Přidat na plátno**. Pak můžete kliknout a přetáhnout aktivitu a umístit ji kamkoli na plátno, které chcete. Umístění aktivity na plátně nijak neovlivňuje provoz sady Runbook žádným způsobem. Můžete si také rozvrhnout Runbook, který vám umožní vizualizovat jeho operaci.

![Přidat na plátno](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Vyberte aktivitu na plátně pro konfiguraci vlastností a parametrů v okně konfigurace. **Popisek** aktivity můžete změnit na něco, co je pro vás popisné. Původní rutina se pořád spouští, stačí změnit její zobrazované jméno, které se používá v grafickém editoru. Popisek musí být v rámci sady Runbook jedinečný.

### <a name="parameter-sets"></a>Sady parametrů

Sada parametrů definuje povinné a volitelné parametry, které přijímají hodnoty pro konkrétní rutinu. Všechny rutiny mají alespoň jednu sadu parametrů a některé mají více. Pokud má rutina více sad parametrů, musíte před konfigurací parametrů vybrat, která z nich se má použít. Parametry, které lze konfigurovat, závisí na sadě parametrů, kterou zvolíte. Sadu parametrů, kterou používá aktivita, můžete změnit výběrem **sady parametrů** a vybráním jiné sady. V takovém případě dojde ke ztrátě všech hodnot parametrů, které jste nakonfigurovali.

V následujícím příkladu má rutina Get-AzureRmVM tři sady parametrů. Nemůžete konfigurovat hodnoty parametrů, dokud nevyberete jednu ze sad parametrů. Sada parametrů ListVirtualMachineInResourceGroupParamSet je určena pro vracení všech virtuálních počítačů ve skupině prostředků a má jeden volitelný parametr. **GetVirtualMachineInResourceGroupParamSet** je pro určení virtuálního počítače, který chcete vrátit, a má dva povinné a jeden volitelný parametr.

![Sada parametrů](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Hodnoty parametru

Když zadáte hodnotu parametru, vyberete zdroj dat, abyste určili, jak je hodnota zadaná. Zdroje dat, které jsou k dispozici pro konkrétní parametr, závisí na platných hodnotách pro daný parametr. Například hodnota null není dostupná možnost pro parametr, který nepovoluje hodnoty null.

| Zdroj dat | Popis |
|:--- |:--- |
| Hodnota konstanty |Zadejte hodnotu parametru. Tato hodnota je k dispozici pouze pro následující typy dat: Int32, Int64, String, Boolean, DateTime, Switch. |
| Výstup aktivity |Výstup z aktivity, která předchází aktuální aktivitě v pracovním postupu. V seznamu jsou uvedeny všechny platné aktivity. Vyberte pouze aktivitu, chcete-li použít její výstup pro hodnotu parametru. V případě, že výstupem aktivity je objekt s více vlastnostmi, můžete zadat název vlastnosti po výběru aktivity. |
| Vstup Runbooku |Vyberte vstupní parametr Runbooku jako vstup pro parametr Activity. |
| Variabilní prostředek |Jako vstup vyberte proměnnou automatizace. |
| Prostředek přihlašovacích údajů |Jako vstup vyberte přihlašovací údaje automatizace. |
| Prostředek certifikátu |Jako vstup vyberte certifikát Automation. |
| Prostředek připojení |Jako vstup vyberte připojení Automation. |
| Powershellový výraz |Zadejte jednoduchý [powershellový výraz](#powershell-expressions). Výraz je vyhodnocen před aktivitou a výsledkem, který je použit pro hodnotu parametru. Proměnné můžete použít k odkazování na výstup aktivity nebo vstupní parametr Runbooku. |
| Nenakonfigurováno |Vymaže všechny dříve nakonfigurované hodnoty. |

#### <a name="optional-additional-parameters"></a>Nepovinné další parametry

Všechny rutiny mají možnost zadat další parametry. Jedná se o společné parametry PowerShellu nebo jiné vlastní parametry. Zobrazí se textové pole, kde můžete zadat parametry pomocí syntaxe PowerShellu. Chcete-li například použít společný parametr **verbose** , zadejte **"-verbose: $true"** .

### <a name="retry-activity"></a>Aktivita opakování

**Chování opakování** umožňuje spuštění aktivity několikrát, dokud není splněna konkrétní podmínka, podobně jako smyčka. Tuto funkci můžete použít pro aktivity, které by se měly spouštět víckrát, jsou náchylné k chybám a může vyžadovat více než jeden pokus o úspěch nebo otestovat výstupní informace aktivity pro platná data.

Když u aktivity povolíte operaci opakovat, můžete nastavit zpoždění a podmínku. Zpoždění je čas (měřeno v sekundách nebo minutách), po který sada Runbook počká, než znovu spustí aktivitu. Pokud není zadána žádná prodleva, pak se aktivita spustí znovu ihned po dokončení.

![Zpoždění opakování aktivity](media/automation-graphical-authoring-intro/retry-delay.png)

Podmínka opakování je výraz PowerShellu, který se vyhodnocuje po každém spuštění aktivity. Pokud se výraz přeloží na hodnotu true, aktivita se znovu spustí. Pokud se výraz přeloží na false, pak se aktivita nespustí znovu a Runbook se přesune k další aktivitě.

![Zpoždění opakování aktivity](media/automation-graphical-authoring-intro/retry-condition.png)

Podmínka opakování může použít proměnnou s názvem $RetryData, která poskytuje přístup k informacím o opakovaných pokusech o aktivitu. Tato proměnná má vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| NumberOfAttempts |Počet, kolikrát byla aktivita spuštěna. |
| Výstup |Výstup z posledního spuštění aktivity |
| TotalDuration |Čas od prvního spuštění aktivity vypršel. |
| StartedAt |Čas ve formátu UTC, kdy byla aktivita poprvé spuštěna. |

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

Po nakonfigurování podmínky opakování pro aktivitu obsahuje aktivita dvě vizuální pomůcky, které vám připomínat. Jedna je prezentována v aktivitě a druhá je při kontrole konfigurace aktivity.

![Vizuální indikátory opakování aktivity](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Řízení skriptu pracovního postupu

Řízení kódu je speciální aktivita, která přijímá PowerShell nebo skript pracovního postupu PowerShellu v závislosti na typu grafické sady Runbook, který je vytvořen, aby poskytoval funkce, které nemusí být k dispozici. Nemůže přijímat parametry, ale může použít proměnné pro výstup aktivity a vstupní parametry Runbooku. Jakýkoli výstup aktivity se přidá do datové sběrnice, pokud nemá žádný odchozí odkaz, v takovém případě se přidá do výstupu Runbooku.

Například následující kód provádí výpočty data pomocí vstupní proměnné Runbooku s názvem $NumberOfDays. Pak pošle vypočítaný datum a čas jako výstup, který se použije v následných aktivitách v Runbooku.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Odkazy a pracovní postup

**Odkaz** v grafickém Runbooku spojuje dvě aktivity. Zobrazuje se na plátně jako šipka ukazující od zdrojové aktivity k cílové aktivitě. Aktivity jsou spouštěny ve směru šipky s cílovou aktivitou začínající po dokončení zdrojové aktivity.

### <a name="create-a-link"></a>Vytvořit odkaz

Vytvořte propojení mezi dvěma aktivitami tak, že vyberete zdrojovou aktivitu a kliknete na kolečko v dolní části obrazce. Přetáhněte šipku na cílovou aktivitu a vydanou verzi.

![Vytvořit odkaz](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Vyberte odkaz pro konfiguraci vlastností v okně konfigurace. To zahrnuje typ odkazu, který je popsán v následující tabulce:

| Typ odkazu | Popis |
|:--- |:--- |
| Kanál |Cílová aktivita se spustí jednou pro každý výstup objektu ze zdrojové aktivity. Cílová aktivita se nespustí, pokud výsledkem zdrojové aktivity není žádný výstup. Výstup zdrojové aktivity je k dispozici jako objekt. |
| Pořadí |Cílová aktivita se spustí jenom jednou. Přijímá pole objektů ze zdrojové aktivity. Výstup zdrojové aktivity je k dispozici jako pole objektů. |

### <a name="starting-activity"></a>Spuštění aktivity

Grafický Runbook začíná všemi aktivitami, které nemají příchozí propojení. To je často jenom jedna aktivita, která by fungovala jako počáteční aktivita Runbooku. Pokud příchozí odkaz neobsahuje více aktivit, sada Runbook se spustí paralelně. Postupuje podle odkazů ke spuštění dalších aktivit v průběhu každého dokončení.

### <a name="conditions"></a>Podmínky

Když zadáte podmínku na odkaz, spustí se cílová aktivita pouze v případě, že se podmínka vyřeší na hodnotu true. Při načítání výstupu ze zdrojové aktivity obvykle používáte $ActivityOutput proměnnou v podmínce.

Pro odkaz na kanál zadáte podmínku pro jeden objekt a podmínka se vyhodnotí pro každý výstup objektu zdrojovou aktivitou. Cílová aktivita se pak spustí pro každý objekt, který splňuje podmínku. Například pomocí zdrojové aktivity Get-AzureRmVm lze použít následující syntaxi pro odkaz podmíněného kanálu k načtení pouze virtuálních počítačů ve skupině prostředků s názvem *Group1*.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Pro propojení sekvence je podmínka vyhodnocena pouze jednou, protože je vráceno jediné pole obsahující výstup všech objektů ze zdrojové aktivity. Z tohoto důvodu se odkaz na sekvenci nedá použít pro filtrování jako propojení kanálu, ale jednoduše určí, jestli se má spustit další aktivita. Proveďte například následující sadu aktivit v sadě Runbook pro spuštění virtuálního počítače.

![Podmíněný odkaz s sekvencemi](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Existují tři různé sekvenční odkazy, které ověřují hodnoty pro dva vstupní parametry sady Runbook, které představují název virtuálního počítače a název skupiny prostředků, aby bylo možné určit, která z nich je vhodná pro spuštění jednoho virtuálního počítače, spustit všechny virtuální počítače v prostředku. skupiny nebo všech virtuálních počítačů v rámci předplatného. Pro propojení sekvence mezi připojením k Azure a získáním jednoho virtuálního počítače tady je logika podmínky:

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

Například aktivita **Start-AzureRmVm** v sadě Runbook spustí všechny virtuální počítače. Má dvě podmíněné odkazy. První podmíněný odkaz používá výraz *$ActivityOutput [' Start-AzureRmVM ']. IsSuccessStatusCode-EQ $true* k filtrování, pokud se aktivita Start-AzureRmVm úspěšně dokončila. Druhý používá výraz *$ActivityOutput [' Start-AzureRmVM ']. IsSuccessStatusCode-ne $true* k filtrování, pokud se v aktivitě Start-AzureRmVm nepovedlo spustit virtuální počítač.

![Příklad podmíněného odkazu](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Všechny aktivity, které následují za prvním odkazem a využívají výstup aktivity z rutiny Get-AzureVM, získají pouze virtuální počítače, které byly spuštěny v době spuštění rutiny Get-AzureVM. Každá aktivita, která následuje za druhým odkazem, načte jenom virtuální počítače, které se zastavily v době spuštění rutiny Get-AzureVM. Všechny aktivity, které následují po třetím odkazu, vrátí všechny virtuální počítače bez ohledu na jejich běžící stav.

### <a name="junctions"></a>Spojení

Spojení je speciální aktivita, která čeká na dokončení všech příchozích větví. Díky tomu můžete souběžně spustit více aktivit a zajistit, aby byla dokončena před pokračováním.

I když spojení může mít neomezený počet příchozích odkazů, nemůžete mít více než jeden z těchto odkazů kanál. Počet příchozích odkazů na sekvence není omezený. Je možné vytvořit spojení s více příchozími odkazy na kanály a sadu Runbook uložit, ale při spuštění dojde k chybě.

Níže uvedený příklad je součástí sady Runbook, která spouští sadu virtuálních počítačů a současně stahuje opravy, které se mají na tyto počítače použít. Spojení se používá k zajištění, že oba procesy budou dokončeny před tím, než sada Runbook pokračuje.

![Ulic](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cykly

Cyklus je v případě, že cílová aktivita odkazuje zpátky na její zdrojovou aktivitu nebo na jinou aktivitu, která nakonec odkazuje zpět na její zdroj. Cykly nejsou momentálně při vytváření grafiky povoleny. Pokud má vaše sada Runbook cyklus, uloží se správně, ale při spuštění obdrží chybu.

![Cyklické](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>Sdílení dat mezi aktivitami

Všechna data, která jsou výstupem aktivity s odchozím odkazem, se zapisují do *datové sběrnice* pro sadu Runbook. Jakékoli aktivity v sadě Runbook mohou použít data v datové sběrnice k naplnění hodnot parametrů nebo zahrnutí do kódu skriptu. Aktivita má přístup k výstupu jakékoli předchozí aktivity v pracovním postupu.

Způsob zápisu dat do datové sběrnice závisí na typu odkazu na aktivitu. V případě **kanálu**jsou data výstupem více objektů. Pro propojení **sekvence** je výstupem data jako pole. Pokud je k dispozici pouze jedna hodnota, jedná se o výstup jako jedno pole prvku.

K datům na datové sběrnice můžete přistupovat pomocí jedné ze dvou metod. Nejprve používá výstupní zdroj dat **aktivity** k naplnění parametru jiné aktivity. Pokud je výstupem objekt, můžete zadat jednu vlastnost.

![Výstup aktivity](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Výstup aktivity můžete také načíst ve zdroji dat **výrazu PowerShellu** nebo z aktivity **skriptu pracovního postupu** pomocí proměnné ActivityOutput. Pokud je výstupem objekt, můžete zadat jednu vlastnost. Proměnné ActivityOutput používají následující syntaxi.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Kontrolní body

Můžete nastavit [kontrolní body](automation-powershell-workflow.md#checkpoints) v grafickém Runbooku pracovního postupu PowerShellu výběrem *kontrolního bodu Runbooku* u jakékoli aktivity. Tím dojde k nastavení kontrolního bodu po spuštění aktivity.

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

Kontrolní body jsou povolené jenom v diagramech grafického pracovního postupu PowerShellu, které nejsou k dispozici v grafických sadách Runbook. Pokud sada Runbook používá rutiny Azure, měli byste postupovat podle jakékoli kontrolní aktivity s rutinou Connect-AzureRmAccount pro případ pozastavení sady Runbook a restartování z tohoto kontrolního bodu na jiném pracovním procesu.

## <a name="authenticating-to-azure-resources"></a>Ověřování v prostředcích Azure

Sady Runbook v Azure Automation, které spravují prostředky Azure, vyžadují ověřování do Azure. Výchozí metodou pro přístup k Azure Resource Manager prostředkům ve vašem předplatném pomocí runbooků Automation je [účet Spustit jako](automation-create-runas-account.md) (také označovaný jako instanční objekt). Tuto funkci můžete přidat do grafického Runbooku přidáním prostředku připojení **AzureRunAsConnection** , který pomocí rutiny [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) PowerShellu a rutiny [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) na plátno. To je znázorněno v následujícím příkladu:

![Aktivity ověřování spustit jako](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

Aktivita získat připojení spustit jako (tj. Get-AutomationConnection) je nakonfigurovaná se zdrojem dat s konstantní hodnotou s názvem AzureRunAsConnection.

![Konfigurace připojení spustit jako](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

Další aktivita Connect-AzureRmAccount přidá ověřený účet Spustit jako pro použití v Runbooku.

![Sada parametrů Connect-AzureRmAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

> [!IMPORTANT]
> **Add-AzureRmAccount** je teď alias pro **Connect-AzureRmAccount**. Pokud se při hledání položek knihovny nezobrazí **příkaz Connect-AzureRMAccount**, můžete použít příkaz **Add-AzureRMAccount**nebo můžete aktualizovat moduly v účtu Automation.

Pro parametry **APPLICATIONID**, **CERTIFICATETHUMBPRINT**a **TENANTID** je třeba zadat název vlastnosti pro cestu k poli, protože výstupem aktivity je objekt s více vlastnostmi. V opačném případě se při spuštění sady Runbook pokus o ověření nezdaří. To je to, co potřebujete minimálně k ověření vaší sady Runbook pomocí účtu Spustit jako.

Aby se zajistila zpětná kompatibilita pro předplatitele, kteří vytvořili účet Automation pomocí [účtu uživatele Azure AD](automation-create-aduser-account.md) ke správě nasazení Azure Classic nebo prostředků Azure Resource Manager, je metoda ověřování rutinou Add-AzureAccount s [prostředkem přihlašovacích údajů](automation-credentials.md) , který představuje uživatele služby Active Directory s přístupem k účtu Azure.

Tuto funkci můžete přidat do grafického Runbooku přidáním assetu přihlašovacích údajů na plátno následovaného aktivitou Add-AzureAccount. Add-AzureAccount používá pro svůj vstup aktivitu přihlašovacích údajů. To je znázorněno v následujícím příkladu:

![Aktivity ověřování](media/automation-graphical-authoring-intro/authentication-activities.png)

Je nutné provést ověření na začátku Runbooku a po každém kontrolním bodu. To znamená přidání aktivity přidání-AzureAccount po libovolné aktivitě kontrolního bodu a pracovního postupu. Nepotřebujete aktivitu Přidání přihlašovacích údajů, protože můžete použít stejné

![Výstup aktivity](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Vstup a výstup Runbooku

### <a name="runbook-input"></a>Vstup Runbooku

Sada Runbook může vyžadovat vstup buď od uživatele, když spustí Runbook prostřednictvím Azure Portal nebo z jiného Runbooku, pokud je aktuální použit jako podřízený.
Například pokud máte sadu Runbook, která vytvoří virtuální počítač, může být nutné zadat informace, jako je název virtuálního počítače a další vlastnosti při každém spuštění sady Runbook.

Zadáním jednoho nebo více vstupních parametrů přijmete vstup Runbooku. Hodnoty pro tyto parametry zadáte pokaždé, když se Runbook spustí. Když spustíte Runbook s Azure Portal, zobrazí se výzva k zadání hodnot pro každý vstupní parametr Runbooku.

Vstupní parametry pro sadu Runbook můžete otevřít kliknutím na tlačítko **vstup a výstup** na panelu nástrojů sady Runbook.

Tím se otevře ovládací prvek **vstup a výstup** , kde můžete upravit existující vstupní parametr nebo vytvořit nový kliknutím na **Přidat vstup**.

![Přidání vstupu](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Jednotlivé vstupní parametry jsou definovány vlastnostmi v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| Name (Název) |Jedinečný název parametru. Může obsahovat pouze alfanumerické znaky a nesmí obsahovat mezery. |
| Popis |Volitelný popis pro vstupní parametr. |
| Typ |Pro hodnotu parametru se očekával datový typ. Azure Portal poskytuje vhodný ovládací prvek pro datový typ pro každý parametr při zobrazení výzvy pro vstup. |
| Povinné |Určuje, zda musí být pro parametr zadána hodnota. Sadu Runbook nelze spustit, pokud nezadáte hodnotu pro každý povinný parametr, který nemá definovánu výchozí hodnotu. |
| Výchozí hodnota |Určuje, jaká hodnota se má použít pro parametr, pokud není k dispozici. Může to být buď null, nebo konkrétní hodnota. |

### <a name="runbook-output"></a>Výstup runbooku

Data vytvořená aktivitou, která neobsahují odchozí odkaz, se uloží do [výstupu Runbooku](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). Výstup je uložen s úlohou Runbooku a je k dispozici pro nadřazenou sadu Runbook, pokud je sada Runbook používána jako podřízená položka.

## <a name="powershell-expressions"></a>Výrazy PowerShellu

Jednou z výhod grafického vytváření je poskytování možnosti sestavit sadu Runbook s minimálními znalostmi prostředí PowerShell. V současné době potřebujete znát bitovou kopii PowerShellu, ale doplněním některých [hodnot parametrů](#activities) a nastavením [podmínek propojení](#links-and-workflow). V této části najdete rychlé představení výrazů PowerShellu pro uživatele, kteří s ním nemusí být obeznámeni. Úplné podrobnosti o PowerShellu jsou k dispozici v skriptování v prostředí [Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Zdroj dat výrazu PowerShellu
Výraz PowerShellu můžete použít jako zdroj dat k naplnění hodnoty [parametru aktivity](#activities) s výsledky některých kódů prostředí PowerShell. Může to být jeden řádek kódu, který provádí jednoduchou funkci nebo více řádků, které provádějí určitou komplexní logiku. Libovolný výstup příkazu, který není přiřazen proměnné, je výstupem hodnoty parametru.

Například následující příkaz by měl výstupem aktuálního data.

Například následující příkaz by měl výstupem aktuálního data.

```powershell-interactive
Get-Date
```

Následující příkazy sestaví řetězec z aktuálního data a přiřadí ho k proměnné. Obsah proměnné se pak pošle do výstupu.

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

### <a name="activity-output"></a>Výstup aktivity

Chcete-li použít výstup z předchozí aktivity v sadě Runbook, použijte $ActivityOutput proměnnou s následující syntaxí.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Například můžete mít aktivitu s vlastností, která vyžaduje název virtuálního počítače, v tomto případě můžete použít následující výraz:

```powershell-interactive
$ActivityOutput['Get-AzureVm'].Name
```

Pokud vlastnost, která vyžadovala objekt virtuálního počítače, místo pouze vlastnosti, pak byste vrátili celý objekt pomocí následující syntaxe.

```powershell-interactive
$ActivityOutput['Get-AzureVm']
```

Výstup aktivity můžete použít také v složitějším výrazu, jako je například následující, které zřetězí text do názvu virtuálního počítače.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVm'].Name
```

### <a name="conditions"></a>Podmínky

[Operátory porovnání](https://technet.microsoft.com/library/hh847759.aspx) použijte k porovnání hodnot nebo určení, zda hodnota odpovídá zadanému vzoru. Porovnání vrátí hodnotu buď $true, nebo $false.

Například následující podmínka Určuje, zda je virtuální počítač z aktivity s názvem *Get-AzureVM* aktuálně *zastaven*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

Následující podmínka kontroluje, zda je stejný virtuální počítač v jiném stavu než *Zastaveno*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Pomocí [logického operátoru](https://technet.microsoft.com/library/hh847789.aspx) , jako je **-a** nebo **-nebo**, se můžete spojit s několika podmínkami. Například následující podmínka kontroluje, jestli je stejný virtuální počítač v předchozím příkladu ve stavu *Zastaveno* nebo *zastavování*.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Zatřiďovacími tabulkami

[Zatřiďovacími tabulkami](https://technet.microsoft.com/library/hh847780.aspx) jsou páry název-hodnota, které jsou užitečné pro vrácení sady hodnot. Vlastnosti pro určité aktivity mohou očekávat zatřiďovací tabulku namísto jednoduché hodnoty. Může se také zobrazit jako zatřiďovací tabulka, která je označována jako slovník.

Vytvořte zatřiďovací tabulku s následující syntaxí. Zatřiďovací tabulka může obsahovat libovolný počet položek, ale každá je definována pomocí názvu a hodnoty.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Například následující výraz vytvoří zatřiďovací tabulku, která bude použita ve zdroji dat pro parametr Activity, který očekával zatřiďovací tabulku s hodnotami pro hledání v Internetu.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

Následující příklad používá výstup z aktivity s názvem *získat připojení k Twitteru* k naplnění zatřiďovací tabulky.

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
* Informace o tom, jak ověřit pomocí účtu Automation spustit jako, najdete v tématu [Konfigurace účtu spustit v Azure jako](automation-sec-configure-azure-runas-account.md) .
