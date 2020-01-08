---
title: Migrace z nástroje Orchestrator na Azure Automation
description: Popisuje, jak migrovat Runbooky a integrační balíčky z nástroje System Center Orchestrator na Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 528b961ca07ec86ad502ee1b589772e354564a3d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421678"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrace z nástroje Orchestrator na Azure Automation (beta verze)
Sady Runbook v [nástroji System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) jsou založené na aktivitách z integračních balíčků, které jsou vytvořené speciálně pro nástroj Orchestrator, zatímco runbooky v Azure Automation jsou založené na prostředí Windows PowerShell.  [Grafické Runbooky](automation-runbook-types.md#graphical-runbooks) v Azure Automation mají podobný vzhled pro Runbooky Orchestrator s jejich aktivitami, které představují rutiny prostředí PowerShell, podřízené Runbooky a prostředky.

[Sada nástrojů pro migraci System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) obsahuje nástroje, které vám pomůžou při převodu runbooků z nástroje Orchestrator na Azure Automation.  Kromě převádění sad Runbook, je nutné převést integrační balíčky s aktivitami, které Runbooky používají pro moduly integrace s rutinami prostředí Windows PowerShell.  

Následuje základní proces převodu runbooků nástroje Orchestrator na Azure Automation.  Každý z těchto kroků je podrobně popsán v následujících částech.

1. Stáhněte si nástroj [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) , který obsahuje nástroje a moduly popsané v tomto článku.
2. Importuje [modul standardní aktivity](#standard-activities-module) do Azure Automation.  To zahrnuje převedené verze standardních aktivit nástroje Orchestrator, které mohou být používány převedenými sadami Runbook.
3. Importujte [moduly integrace System Center Orchestrator](#system-center-orchestrator-integration-modules) do Azure Automation pro tyto integrační balíčky, které používají vaše Runbooky pro přístup k produktu System Center.
4. Pomocí [převaděče integračního balíčku](#integration-pack-converter) převeďte vlastní integrační balíčky a sady třetích stran a importujte je do Azure Automation.
5. Převeďte Runbooky Orchestrator pomocí [převaděče runbooků](#runbook-converter) a nainstalujte nástroj do Azure Automation.
6. Ruční vytvoření požadovaných assetů Orchestrator v Azure Automation, protože převaděč sady Runbook tyto prostředky nepřevede.
7. Nakonfigurujte [Hybrid Runbook Worker](#hybrid-runbook-worker) v místním datovém centru, aby se spouštěly převedené Runbooky, které budou mít přístup k místním prostředkům.

## <a name="service-management-automation"></a>Service Management Automation
[Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx) (SMA) ukládá a spouští Runbooky v místním datovém centru jako Orchestrator a používá stejné moduly Integration jako Azure Automation. [Převaděč sady Runbook](#runbook-converter) převede Runbooky produktu Orchestrator na grafické Runbooky, a to i v případě, že SMA není podporován.  Do SMA můžete i nadále instalovat [modul pro standardní aktivity](#standard-activities-module) a [integrační moduly nástroje System Center Orchestrator](#system-center-orchestrator-integration-modules) , ale musíte ručně [přepsat Runbooky](https://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Hybridní pracovní proces runbooku
Sady Runbook v nástroji Orchestrator jsou uloženy na databázovém serveru a spuštěny na serverech Runbook Server v místním datovém centru.  Runbooky v Azure Automation se ukládají do cloudu Azure a můžou běžet v místním datovém centru pomocí [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).  Tím se obvykle spouští Runbooky převedené z nástroje Orchestrator, protože jsou navržené pro spouštění na místních serverech.

## <a name="integration-pack-converter"></a>Převaděč integračního balíčku
Konvertor integračního balíčku převede integrační balíčky, které byly vytvořeny pomocí [Orchestrator Integration Toolkit (OIT)](https://technet.microsoft.com/library/hh855853.aspx) , do integračních modulů založených na prostředí Windows PowerShell, které lze importovat do Azure Automation nebo Service Management Automation.  

Při spuštění převaděče integračního balíčku se zobrazí průvodce, který vám umožní vybrat soubor integračního balíčku (. OIP).  Průvodce pak zobrazí seznam aktivit zahrnutých do tohoto integračního balíčku a umožní vám vybrat, které budou migrovány.  Po dokončení Průvodce vytvoří modul Integration Module, který obsahuje odpovídající rutinu pro každou z aktivit v původním integračním balíčku.

### <a name="parameters"></a>Parametry
Všechny vlastnosti aktivity v integračním balíčku jsou převedeny na parametry odpovídající rutiny v modulu integrace.  Rutiny prostředí Windows PowerShell mají sadu [společných parametrů](https://technet.microsoft.com/library/hh847884.aspx) , které lze použít se všemi rutinami.  Například parametr-verbose způsobí, že rutina zaznamená výstup podrobných informací o jeho provozu.  Žádná rutina nemůže mít parametr se stejným názvem jako společný parametr.  Pokud aktivita má vlastnost se stejným názvem jako společný parametr, Průvodce vás vyzve k zadání dalšího názvu pro parametr.

### <a name="monitor-activities"></a>Monitorování aktivit
Monitorujte Runbooky v produktu Orchestrator pomocí [monitorovací aktivity](https://technet.microsoft.com/library/hh403827.aspx) a spouštějte nepřetržitě na vyvolání konkrétní událostí.  Azure Automation nepodporuje monitorování runbooků, takže nebudou převedeny žádné aktivity monitorování v integračním balíčku.  Místo toho je v modulu integrace pro aktivitu monitorování vytvořena zástupná rutina.  Tato rutina nemá žádné funkce, ale umožňuje převedenou sadu Runbook, která používá k instalaci.  Tento Runbook nebude možné spustit v Azure Automation, ale je možné ho nainstalovat, abyste ho mohli upravit.

### <a name="integration-packs-that-cannot-be-converted"></a>Integrační balíčky, které nelze převést
Integrační balíčky, které nebyly vytvořeny pomocí OIT, nelze převést pomocí převaděče integračního balíčku. K dispozici jsou také některé integrační balíčky poskytované společností Microsoft, které nelze v současnosti pomocí tohoto nástroje převádět.  [K dispozici](#system-center-orchestrator-integration-modules) jsou převedené verze těchto integračních balíčků ke stažení, aby je bylo možné nainstalovat do Azure Automation nebo Service Management Automation.

## <a name="standard-activities-module"></a>Modul standardní aktivity
Nástroj Orchestrator zahrnuje sadu [standardních aktivit](https://technet.microsoft.com/library/hh403832.aspx) , které nejsou součástí integračního balíčku, ale jsou používány mnoha sadami Runbook.  Modul standardní aktivity je modul Integration Module, který obsahuje ekvivalent rutiny pro každou z těchto aktivit.  Před importem všech převedených runbooků, které používají standardní aktivitu, je nutné tento integrační modul nainstalovat do Azure Automation.

Kromě podpory převedených runbooků můžou být rutiny v modulu standardní aktivity používané někým známým s nástrojem Orchestrator, aby mohli vytvářet nové Runbooky v Azure Automation.  I když se funkce všech standardních aktivit dají provádět s rutinami, můžou fungovat různě.  Rutiny v modulu převedené standardní aktivity budou fungovat stejně jako jejich odpovídající aktivity a použít stejné parametry.  To může pomohlo existujícímu autorovi Runbooku nástroje Orchestrator v jejich přechodu na Azure Automation Runbooky.

## <a name="system-center-orchestrator-integration-modules"></a>Integrační moduly nástroje System Center Orchestrator
Společnost Microsoft poskytuje [integrační balíčky](https://technet.microsoft.com/library/hh295851.aspx) pro vytváření runbooků pro automatizaci součástí produktu System Center a dalších produktů.  Některé z těchto integračních balíčků jsou aktuálně založené na OIT, ale nedají se v současnosti převést na integrační moduly kvůli známým problémům.  [Integrační moduly nástroje System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) obsahují převedené verze těchto integračních balíčků, které lze importovat do Azure Automation a Service Management Automation.  

Pomocí verze RTM tohoto nástroje se publikují aktualizované verze integračních balíčků založené na OIT, které je možné převést pomocí převaděče integračního balíčku.  Budou vám také poskytnuty doprovodné materiály, které vám pomůžou při převodu runbooků pomocí aktivit z integračních balíčků, které nejsou založené na OIT.

## <a name="runbook-converter"></a>Převaděč Runbooku
Převaděč Runbooku převede Runbooky Orchestrator na [grafické Runbooky](automation-runbook-types.md#graphical-runbooks) , které se dají importovat do Azure Automation.  

Převaděč sady Runbook je implementován jako modul prostředí PowerShell s rutinou s názvem **ConvertFrom-SCORunbook** , která provádí převod.  Při instalaci nástroje se vytvoří zástupce relace PowerShellu, která načte rutinu.   

Následuje základní proces převodu Runbooku nástroje Orchestrator a jeho import do Azure Automation.  Následující oddíly poskytují další podrobnosti o používání nástroje a práci s převedenými Runbooky.

1. Exportujte jednu nebo více sad Runbook z nástroje Orchestrator.
2. Získejte moduly pro integraci pro všechny aktivity v sadě Runbook.
3. Převeďte Runbooky nástroje Orchestrator v exportovaném souboru.
4. Zkontrolujte informace v protokolech a ověřte převod a určete všechny požadované ruční úlohy.
5. Importujte převedené Runbooky do Azure Automation.
6. Vytvořte všechny požadované prostředky v Azure Automation.
7. Upravte Runbook v Azure Automation pro úpravu požadovaných aktivit.

### <a name="using-runbook-converter"></a>Použití převaděče Runbooku
Syntaxe pro **ConvertFrom-SCORunbook** je následující:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath-cesta k souboru exportu obsahující Runbooky, které se mají převést.
* Modul – seznam modulů integrace, které obsahují aktivity v sadách Runbook, oddělený čárkami.
* OutputFolder – cesta ke složce pro vytvoření převedených grafických runbooků.

Následující příklad příkazu převede Runbooky v souboru exportu s názvem **MyRunbooks. ois_export**.  Tyto Runbooky používají integrační balíčky služby Active Directory a Data Protection Manager.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="log-files"></a>Soubory protokolu
Převaděč Runbooku vytvoří následující soubory protokolu ve stejném umístění jako převedený Runbook.  Pokud již soubory existují, budou přepsány informacemi z posledního převodu.

| Soubor | Obsah |
|:--- |:--- |
| Převaděč Runbooku-průběh. log |Podrobné kroky převodu zahrnující informace pro každou aktivitu byly úspěšně převedeny a upozornění pro každou aktivitu, která není převedena. |
| Runbook Converter-Summary. log |Souhrn posledního převodu, včetně všech upozornění a následných úkolů, které je třeba provést, například vytvoření proměnné požadované pro převedený Runbook. |

### <a name="exporting-runbooks-from-orchestrator"></a>Exportování runbooků z nástroje Orchestrator
Převaděč sady Runbook funguje se souborem exportu z nástroje Orchestrator, který obsahuje jednu nebo více sad Runbook.  Vytvoří odpovídající sadu Runbook Azure Automation pro každou sadu Runbook nástroje Orchestrator v souboru exportu.  

Pokud chcete Runbook z nástroje Orchestrator exportovat, klikněte pravým tlačítkem na název Runbooku v Runbook Designer a vyberte **exportovat**.  Chcete-li exportovat všechny Runbooky ve složce, klikněte pravým tlačítkem myši na název složky a vyberte položku **exportovat**.

### <a name="runbook-activities"></a>Aktivity sady Runbook
Převaděč Runbooku převede každou aktivitu v Runbooku nástroje Orchestrator na odpovídající aktivitu v Azure Automation.  Pro aktivity, které se nedají převést, se v Runbooku vytvoří zástupná aktivita s textem upozornění.  Po importu převedeného Runbooku do Azure Automation musíte nahradit libovolnou z těchto aktivit platnými aktivitami, které provádějí požadované funkce.

Budou převedeny všechny aktivity produktu Orchestrator v [modulu standardní aktivity](#standard-activities-module) .  Existují některé standardní aktivity nástroje Orchestrator, které nejsou v tomto modulu, ale nejsou převedeny.  Například **událost Send Platform** nemá žádný Azure Automation ekvivalent, protože událost je specifická pro nástroj Orchestrator.

[Aktivity monitorování](https://technet.microsoft.com/library/hh403827.aspx) se nepřevádí, protože v Azure Automation nejsou ekvivalentní.  Tato výjimka monitoruje aktivity v [převedených integračních balíčcích](#integration-pack-converter) , které budou převedeny na zástupné aktivity.

Pokud zadáte cestu k modulu integrace s parametrem **modules** , dojde k převodu jakékoli aktivity z [převedeného integračního balíčku](#integration-pack-converter) .  Pro integrační balíčky produktu System Center můžete použít [integrační moduly nástroje System Center Orchestrator](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Prostředky nástroje Orchestrator
Konvertor sady Runbook převádí pouze Runbooky, nikoli jiné prostředky nástroje Orchestrator, například čítače, proměnné nebo připojení.  V Azure Automation nejsou podporovány čítače.  Proměnné a připojení jsou podporovány, ale je nutné je vytvořit ručně.  Soubory protokolu vás budou informovat, pokud sada Runbook vyžaduje tyto prostředky a určí odpovídající prostředky, které je třeba vytvořit v Azure Automation pro převedenou sadu Runbook tak, aby fungovala správně.

Sada Runbook může například použít proměnnou k naplnění konkrétní hodnoty v aktivitě.  Převedený Runbook převede aktivitu a určí v Azure Automation variabilní prostředek se stejným názvem, jako má proměnná Orchestrator.  Tato zpráva bude zaznamenána v souboru **. log sady Runbook Converter-Summary** , který je vytvořen po převodu.  Před použitím sady Runbook budete muset tento variabilní prostředek ručně vytvořit v Azure Automation.

### <a name="input-parameters"></a>Vstupní parametry
Runbooky v Orchestrator akceptují vstupní parametry s aktivitou **inicializovat data** .  Pokud tento Runbook obsahuje tuto aktivitu, pak se pro každý parametr v aktivitě vytvoří [vstupní parametr](automation-graphical-authoring-intro.md#runbook-input-and-output) v sadě Azure Automation Runbook.  V převedené sadě Runbook se vytvoří aktivita [řízení skriptu pracovního postupu](automation-graphical-authoring-intro.md#activities) , která načte a vrátí každý parametr.  Všechny aktivity v sadě Runbook, které používají vstupní parametr, odkazují na výstup z této aktivity.

Důvodem, proč se tato strategie používá, je nejlepší zrcadlit funkce v Runbooku produktu Orchestrator.  Aktivity v nových grafických sadách Runbook by měly odkazovat přímo na vstupní parametry pomocí zdroje vstupních dat Runbooku.

### <a name="invoke-runbook-activity"></a>Vyvolat aktivitu Runbooku
Sady Runbook ve službě Orchestrator spouštějí další Runbooky s aktivitou **vyvolání sady Runbook** . Pokud sada Runbook, která se převádí, zahrnuje tuto aktivitu a je nastavená možnost **čekání na dokončení** , vytvoří se pro ni v převedené sadě Runbook aktivita Runbooku.  Pokud není nastavená možnost **čekání na dokončení** , vytvoří se aktivita skriptu pracovního postupu, která pomocí příkazu **Start-AzureAutomationRunbook** spustí sadu Runbook.  Po importu převedeného Runbooku do Azure Automation musíte tuto aktivitu upravit s informacemi zadanými v aktivitě.

## <a name="related-articles"></a>Související články
* [System Center 2012 – Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)
* [Standardní aktivity nástroje Orchestrator](https://technet.microsoft.com/library/hh403832.aspx)
* [Stáhnout nástroj System Center Orchestrator Migration Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=47323)

