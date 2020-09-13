---
title: Migrace z Orchestratoru na Azure Automation (beta verze)
description: V tomto článku se dozvíte, jak migrovat Runbooky a integrační balíčky z nástroje Orchestrator na Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: a47f720344a16d0f77559d6aabfb2b0245e62976
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89426329"
---
# <a name="migrate-from-orchestrator-to-azure-automation-beta"></a>Migrace z Orchestratoru na Azure Automation (beta verze)

Sady Runbook v [produktu System Center 2012 – Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)) jsou založené na aktivitách z integračních balíčků, které jsou napsané speciálně pro nástroj Orchestrator, zatímco sady runbook v Azure Automation jsou založené na prostředí Windows PowerShell. [Grafické Runbooky](automation-runbook-types.md#graphical-runbooks) v Azure Automation mají podobný vzhled pro Runbooky se systémem Orchestrator a jejich aktivity, které představují rutiny prostředí PowerShell, podřízené Runbooky a prostředky. Kromě převádění sad Runbook, je nutné převést integrační balíčky s aktivitami, které Runbooky používají pro moduly integrace s rutinami prostředí Windows PowerShell. 

[Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)) (SMA) ukládá a spouští Runbooky v místním datovém centru, jako je Orchestrator, a používá stejné moduly integrace jako Azure Automation. Převaděč Runbooku převede Runbooky Orchestrator na grafické Runbooky, které se v SMA nepodporují. Do SMA můžete i nadále instalovat modul pro standardní aktivity a integrační moduly nástroje System Center Orchestrator, ale musíte ručně [přepsat Runbooky](/system-center/sma/authoring-automation-runbooks).

## <a name="download-the-orchestrator-migration-toolkit"></a>Stažení sady nástrojů pro migraci softwaru Orchestrator

Prvním krokem migrace je stažení sady [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323). Tato sada nástrojů obsahuje nástroje, které vám pomohou při převodu sad Runbook z nástroje Orchestrator na Azure Automation.  

## <a name="import-the-standard-activities-module"></a>Import modulu standardní aktivity

Importujte [modul standardní aktivity](/system-center/orchestrator/standard-activities?view=sc-orch-2019) do Azure Automation. To zahrnuje převedené verze standardních aktivit produktu Orchestrator, které mohou používat převedené grafické Runbooky.

## <a name="import-orchestrator-integration-modules"></a>Importovat integrační moduly Orchestrator

Společnost Microsoft poskytuje [integrační balíčky](/previous-versions/system-center/packs/hh295851(v=technet.10)) pro vytváření runbooků pro automatizaci součástí produktu System Center a dalších produktů. Některé z těchto integračních balíčků jsou aktuálně založené na OIT, ale nedají se v současnosti převést na integrační moduly kvůli známým problémům. Naimportujte [integrační moduly nástroje System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) do Azure Automation pro integrační balíčky používané vašimi Runbooky, které mají přístup k produktu System Center. Tento balíček zahrnuje převedené verze integračních balíčků, které lze importovat do Azure Automation a Service Management Automation.  

## <a name="convert-integration-packs"></a>Převést integrační balíčky

Pomocí [převaděče integračního balíčku](/system-center/orchestrator/orch-integration-toolkit/integration-pack-wizard?view=sc-orch-2019) můžete převést všechny integrační balíčky vytvořené pomocí [Orchestrator Integration Toolkit (OIT)](/previous-versions/system-center/developer/hh855853(v=msdn.10)) do modulů integrace založeného na prostředí PowerShell, které se dají importovat do Azure Automation nebo Service Management Automation. Při spuštění převaděče integračního balíčku se zobrazí průvodce, který umožňuje vybrat soubor integračního balíčku (. OIP). Průvodce pak zobrazí seznam aktivit zahrnutých do tohoto integračního balíčku a umožní vám vybrat aktivity, které chcete migrovat. Po dokončení Průvodce vytvoří modul Integration Module, který obsahuje odpovídající rutinu pro každou z aktivit v původním integračním balíčku.

> [!NOTE]
> Nelze použít převaděč integračního balíčku k převodu integračních balíčků, které nebyly vytvořeny pomocí nástroje OIT. Existují taky některé integrační balíčky poskytované Microsoftem, které se v tuto chvíli nedají pomocí tohoto nástroje převést. Převedené verze těchto integračních balíčků jsou k dispozici ke stažení, aby je bylo možné nainstalovat do Azure Automation nebo Service Management Automation.

### <a name="parameters"></a>Parametry

Všechny vlastnosti aktivity v integračním balíčku jsou převedeny na parametry odpovídající rutiny v modulu integrace.  Rutiny prostředí Windows PowerShell mají sadu [společných parametrů](/powershell/module/microsoft.powershell.core/about/about_commonparameters) , které lze použít se všemi rutinami. Například parametr-verbose způsobí, že rutina zaznamená výstup podrobných informací o jeho provozu.  Žádná rutina nemůže mít parametr se stejným názvem jako společný parametr. Pokud aktivita má vlastnost se stejným názvem jako společný parametr, Průvodce vás vyzve k zadání dalšího názvu parametru.

### <a name="monitor-activities"></a>Monitorování aktivit

Monitorujte Runbooky v produktu Orchestrator pomocí [monitorovací aktivity](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) a spouštějte nepřetržitě na vyvolání konkrétní událostí. Azure Automation nepodporuje monitorování runbooků, takže se nepřevádí žádné aktivity monitorování v integračním balíčku. Místo toho je v modulu integrace pro aktivitu monitorování vytvořena zástupná rutina.  Tato rutina nemá žádné funkce, ale umožňuje převedenou sadu Runbook, která používá k instalaci. Tuto sadu Runbook není možné spustit v Azure Automation, ale je možné ji nainstalovat, abyste ji mohli upravovat.

Nástroj Orchestrator zahrnuje sadu [standardních aktivit](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)) , které nejsou součástí integračního balíčku, ale jsou používány mnoha sadami Runbook.  Modul standardní aktivity je modul Integration Module, který obsahuje ekvivalent rutiny pro každou z těchto aktivit. Před importem všech převedených runbooků, které používají standardní aktivitu, je nutné tento integrační modul nainstalovat do Azure Automation.

Kromě podpory převedených runbooků můžou být rutiny v modulu standardní aktivity používané někým známým s nástrojem Orchestrator, aby mohli vytvářet nové Runbooky v Azure Automation. I když se funkce všech standardních aktivit dají provádět s rutinami, můžou fungovat různě. Rutiny v modulu převedené standardní aktivity fungují stejným způsobem jako odpovídající aktivity a používají stejné parametry. To vám může přispět k přechodu na Azure Automation Runbooky.

## <a name="convert-orchestrator-runbooks"></a>Převést Runbooky Orchestrator

Převaděč sady Runbook nástroje Orchestrator převede Runbooky nástroje Orchestrator na [grafické Runbooky](automation-runbook-types.md#graphical-runbooks) , které lze importovat do Azure Automation. Převaděč sady Runbook je implementován jako modul prostředí PowerShell s rutinou `ConvertFrom-SCORunbook` , která provádí převod. Když nainstalujete převaděč, vytvoří se zástupce relace PowerShellu, která načte rutinu.   

Zde jsou základní kroky pro převod sady Runbook a jejich import do Azure Automation. Podrobnosti o používání rutiny najdete dál v této části.

1. Exportujte jednu nebo více sad Runbook z nástroje Orchestrator.
2. Získejte moduly pro integraci pro všechny aktivity v sadě Runbook.
3. Převeďte Runbooky nástroje Orchestrator v exportovaném souboru.
4. Zkontrolujte informace v protokolech a ověřte převod a určete všechny požadované ruční úlohy.
5. Importujte převedené Runbooky do Azure Automation.
6. Vytvořte všechny požadované prostředky v Azure Automation.
7. Upravte Runbook v Azure Automation pro úpravu požadovaných aktivit.

Syntaxe pro `ConvertFrom-SCORunbook` je:

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

### <a name="use-runbook-converter-log-files"></a>Použít soubory protokolů převaděče Runbooku

Převaděč Runbooku vytvoří následující soubory protokolu ve stejném umístění jako převedený Runbook.  Pokud již soubory existují, budou přepsány informacemi z posledního převodu.

| Soubor | Obsah |
|:--- |:--- |
| Převaděč Runbooku-průběh. log |Podrobné kroky převodu zahrnující informace pro každou aktivitu byly úspěšně převedeny a upozornění pro každou aktivitu, která není převedena. |
| Runbook Converter-Summary. log |Souhrn posledního převodu, včetně všech upozornění a následných úkolů, které je třeba provést, například vytvoření proměnné požadované pro převedený Runbook. |

### <a name="export-runbooks-from-orchestrator"></a>Exportovat Runbooky z nástroje Orchestrator

Převaděč sady Runbook funguje se souborem exportu z nástroje Orchestrator, který obsahuje jednu nebo více sad Runbook.  Vytvoří odpovídající sadu Runbook Azure Automation pro každou sadu Runbook nástroje Orchestrator v souboru exportu.  

Pokud chcete Runbook z nástroje Orchestrator exportovat, klikněte pravým tlačítkem na název Runbooku v Runbook Designer a vyberte **exportovat**.  Chcete-li exportovat všechny Runbooky ve složce, klikněte pravým tlačítkem myši na název složky a vyberte položku **exportovat**.

### <a name="convert-runbook-activities"></a>Převést aktivity Runbooku

Převaděč Runbooku převede každou aktivitu v Runbooku nástroje Orchestrator na odpovídající aktivitu v Azure Automation.  Pro aktivity, které se nedají převést, se v Runbooku vytvoří zástupná aktivita s textem upozornění.  Po importu převedeného Runbooku do Azure Automation musíte nahradit libovolnou z těchto aktivit platnými aktivitami, které provádějí požadované funkce.

Všechny aktivity produktu Orchestrator v modulu standardní aktivity jsou převedeny. Existují některé standardní aktivity nástroje Orchestrator, které nejsou v tomto modulu, ale nejsou převedeny. Například `Send Platform Event` nemá žádný Azure Automation ekvivalent, protože událost je specifická pro nástroj Orchestrator.

[Aktivity monitorování](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) se nepřevádí, protože v Azure Automation nejsou ekvivalentní. Výjimky jsou sledovány aktivity v převedených integračních balíčcích, které jsou převedeny na zástupné aktivity.

Pokud zadáte cestu k modulu integrace s parametrem, dojde k převodu jakékoli aktivity z převedeného integračního balíčku `modules` . Pro integrační balíčky produktu System Center můžete použít integrační moduly nástroje System Center Orchestrator.

### <a name="manage-orchestrator-resources"></a>Správa prostředků nástroje Orchestrator

Konvertor sady Runbook převádí pouze Runbooky, nikoli jiné prostředky nástroje Orchestrator, například čítače, proměnné nebo připojení.  V Azure Automation nejsou podporovány čítače.  Proměnné a připojení jsou podporovány, ale je nutné je vytvořit ručně. Soubory protokolu vás informují o tom, jestli sada Runbook vyžaduje tyto prostředky, a určete odpovídající prostředky, které je třeba vytvořit v Azure Automation pro převedenou sadu Runbook tak, aby fungovala správně.

Sada Runbook může například použít proměnnou k naplnění konkrétní hodnoty v aktivitě.  Převedený Runbook převede aktivitu a určí variabilní prostředek v Azure Automation se stejným názvem jako má proměnná Orchestrator. Tato akce je zaznamenána v souboru **. log sady Runbook Converter-Summary** , který je vytvořen po převodu. Před použitím sady Runbook je nutné ručně vytvořit tuto proměnnou Asset v Azure Automation.

### <a name="work-with-orchestrator-input-parameters"></a>Práce se vstupními parametry nástroje Orchestrator

Runbooky v nástroji Orchestrator akceptují vstupní parametry `Initialize Data` aktivity.  Pokud tento Runbook obsahuje tuto aktivitu, pak se pro každý parametr v aktivitě vytvoří [vstupní parametr](automation-graphical-authoring-intro.md#handle-runbook-input) v sadě Azure Automation Runbook.  V převedené sadě Runbook se vytvoří aktivita [řízení skriptu pracovního postupu](automation-graphical-authoring-intro.md#use-activities) , která načte a vrátí každý parametr. Všechny aktivity v sadě Runbook, které používají vstupní parametr, odkazují na výstup z této aktivity.

Důvodem, proč se tato strategie používá, je nejlepší zrcadlit funkce v Runbooku produktu Orchestrator.  Aktivity v nových grafických sadách Runbook by měly odkazovat přímo na vstupní parametry pomocí zdroje vstupních dat Runbooku.

### <a name="invoke-runbook-activity"></a>Vyvolat aktivitu Runbooku

Runbooky v Orchestrator spouštějí další Runbooky s `Invoke Runbook` aktivitou. Pokud je sada Runbook převáděná na tuto aktivitu a `Wait for completion` možnost nastavená, vytvoří se pro ni v převedené sadě Runbook aktivita sady Runbook.  Pokud `Wait for completion` možnost není nastavena, vytvoří se aktivita skriptu pracovního postupu, která pomocí příkazu [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) spustí sadu Runbook. Po importu převedeného Runbooku do Azure Automation musíte tuto aktivitu upravit s informacemi zadanými v aktivitě.

## <a name="create-orchestrator-assets"></a>Vytvoření assetů Orchestrator

Převaděč Runbooku nepřevádí prostředky Orchestrator. V Azure Automation musíte ručně vytvořit všechny požadované prostředky Orchestrator.

## <a name="configure-hybrid-runbook-worker"></a>Konfigurace Hybrid Runbook Worker

Produkt Orchestrator ukládá Runbooky na databázový server a spouští je na serverech sad Runbook, a to v místním datovém centru. Runbooky v Azure Automation se ukládají do cloudu Azure a můžou běžet v místním datovém centru pomocí [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Nakonfigurujte pracovní proces tak, aby spouštěl vaše Runbooky převedené z nástroje Orchestrator, protože je navržený tak, aby běžel na místních serverech a měl přístup k místním prostředkům.

## <a name="related-articles"></a>Související články

* Podrobnosti o nástroji Orchestrator najdete v tématu [System Center 2012 – Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)).
* Přečtěte si další informace o automatizaci správy služeb v [Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)).
* Podrobnosti o aktivitách nástroje Orchestrator najdete v části [Standardní aktivity nástroje Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)).
* Chcete-li získat sadu nástrojů pro migraci nástroje Orchestrator, přečtěte si téma [Stažení sady System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323).
* Přehled Hybrid Runbook Worker Azure Automation najdete v tématu [přehled Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).
