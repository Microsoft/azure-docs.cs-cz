---
title: "Migrace z nástroje Orchestrator do služby Azure Automation"
description: "Popisuje, jak migrovat sady runbook a integrační balíčky z produktu System Center Orchestrator pro Azure Automation."
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: b50f9973ef7ea5f884930d4f1ee67f886cc36ad0
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrace z nástroje Orchestrator do služby Azure Automation (Beta)
Sady Runbook v [System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) jsou založené na aktivit z integračních balíčků, které jsou napsané konkrétně pro Orchestrator, zatímco runbooky ve službě Azure Automation jsou založené na prostředí Windows PowerShell.  [Grafické runbooky](automation-runbook-types.md#graphical-runbooks) ve službě Azure Automation mít podobný vzhled do sady runbook nástroje Orchestrator s jejich aktivity představující rutiny prostředí PowerShell, podřízené runbooky a prostředky.

[Nástrojů System Center Orchestrator migrace](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) obsahuje nástroje, které vám pomohou při převádění sady runbook z nástroje Orchestrator pro Azure Automation.  Kromě převod sady runbook, sami, je nutné převést integrační balíčky s aktivitami, které používají sady runbook na moduly integrace pomocí rutin prostředí Windows PowerShell.  

Toto je základní proces převodu sady runbook nástroje Orchestrator na Azure Automation.  Každý z těchto kroků je podrobně popsány v následujících částech.

1. Stažení [nástrojů System Center Orchestrator migrace](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) obsahující nástroje a moduly, které jsou popsané v tomto článku.
2. Import [standardní aktivity modulu](#standard-activities-module) do služby Azure Automation.  To zahrnuje převedený verzích standardní Orchestrator aktivity, které může být používán převedené sady runbook.
3. Import [System Center Orchestrator integrační moduly](#system-center-orchestrator-integration-modules) do Azure Automation pro tyto integrační balíčky používané vaší sady runbook, které přístup k produktu System Center.
4. Převést vlastní a třetích stran integrační balíčky pomocí [Integration Pack převaděč](#integration-pack-converter) a importovat do Azure Automation.
5. Převedení Orchestrator sady runbook pomocí [Runbook převaděč](#runbook-converter) a nainstalujte ve službě Azure Automation.
6. Ručně vytvořte požadované prostředky Orchestrator ve službě Azure Automation, protože převaděč Runbook nepřevádět tyto prostředky.
7. Konfigurace [hybridní pracovní proces Runbooku](#hybrid-runbook-worker) ve vašem místním datacentru ke spuštění převedené sady runbook, které budou přistupovat k místním prostředkům.

## <a name="service-management-automation"></a>Service Management Automation
[Službě Service Management Automation](http://technet.microsoft.com/library/dn469260.aspx) (SMA) ukládá a spouští sady runbook ve vašem místním datovém centru jako Orchestrator a používá stejný moduly integrace jako Azure Automation. [Runbook převaděč](#runbook-converter) převede sady runbook nástroje Orchestrator na grafické runbooky ale které nejsou podporovány ve službě SMA.  Můžete nainstalovat [standardní aktivity modulu](#standard-activities-module) a [System Center Orchestrator integrační moduly](#system-center-orchestrator-integration-modules) do služby SMA, ale vy musíte ručně [přepisování runbooky](http://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker
Sady Runbook v nástroji Orchestrator jsou uložené na serveru databáze a spusťte v serverech sady runbook, i v místním datovém centru.  Runbooky ve službě Azure Automation se ukládají v cloudu Azure a můžete spustit v místní data pomocí center [hybridní pracovní proces Runbooku](automation-hybrid-runbook-worker.md).  Toto je, jak bude obvykle spuštění sad runbook převést z nástroje Orchestrator, protože jsou určená ke spuštění na místní servery.

## <a name="integration-pack-converter"></a>Převaděč Integration Pack
Převaděč Integration Pack převede integrační balíčky, které byly vytvořené pomocí [Orchestrator Integration Toolkit (OIT)](http://technet.microsoft.com/library/hh855853.aspx) moduly integrace založené na prostředí Windows PowerShell, který lze importovat do Azure Automation, nebo Službě Service Management Automation.  

Když spustíte Integration Pack převaděč, máte k ruce průvodce, který vám umožní vybrat soubor integračního balíčku (.oip).  Průvodce potom zobrazí aktivity součástí daného integračního balíčku a umožňuje vybrat, které se budou migrovat.  Po dokončení Průvodce vytvoří modul integrace, která obsahuje odpovídající rutiny pro všechny aktivity v původní integračního balíčku.

### <a name="parameters"></a>Parametry
Všechny vlastnosti aktivitu v integračního balíčku se převedou na parametry odpovídajících rutin v modulu integrace.  Rutiny prostředí Windows PowerShell obsahují sadu [společné parametry](http://technet.microsoft.com/library/hh847884.aspx) který lze použít s všechny rutiny.  Například-Verbose parametr způsobí, že rutiny výstup podrobné informace o své činnosti.  Žádné rutiny může mít parametr se stejným názvem jako společný parametr.  Pokud aktivita nemá vlastnost se stejným názvem jako společný parametr, Průvodce zobrazí výzvu k zadání jiného názvu parametru.

### <a name="monitor-activities"></a>Monitorování aktivit
Monitorování sady runbook v produktu Orchestrator začněte s [sledovat činnost](http://technet.microsoft.com/library/hh403827.aspx) a spouštět nepřetržitě čekání na vyvolat určitá událost.  Služby Azure Automation nepodporuje sady runbook monitorování, takže všechny aktivity monitorování v integračního balíčku nebudou převedeny.  Místo toho se vytvoří zástupný symbol rutiny v modulu integrace pro aktivity monitorování.  Tato rutina má používat žádné funkce, ale umožňuje převedené sady runbook, který používá k instalaci.  Tato sada runbook nebude možné spustit v Azure Automation, ale dá se nainstalovat tak, aby ho upravit.

### <a name="integration-packs-that-cannot-be-converted"></a>Integrační balíčky, které nelze převést
Integrační balíčky, které nebyly vytvořené s OIT nelze převést pomocí převaděče Integration Pack. Existují také některé integrační balíčky od společnosti Microsoft, který momentálně nelze převést pomocí tohoto nástroje.  Převedený verzích tyto integrační balíčky byly [poskytnout ke stažení](#system-center-orchestrator-integration-modules) tak, aby mohou být nainstalovány v Azure Automation nebo Service Management Automation.

## <a name="standard-activities-module"></a>Standardní aktivity modulu
Orchestrator obsahuje sadu [standardní aktivity](http://technet.microsoft.com/library/hh403832.aspx) které nejsou součástí integračního balíčku, ale jsou používány kolik sad runbook.  Standardní aktivity modul je modul integrace, která zahrnuje ekvivalentní rutiny pro každou z těchto aktivit.  Tento modul integrace ve službě Azure Automation nainstalujte před importem převedený sady runbook, který použít standardní aktivitu.

Vedle podpory převedené sady runbook, rutiny v modulu standardních aktivit lze někdo obeznámeni s nástrojem Orchestrator k vytvoření nové sady runbook ve službě Azure Automation.  Když funkce všech standardních aktivit lze provést pomocí rutin, může fungovat různě.  Rutiny v modulu převedený standardní aktivity bude fungovat stejně jako jejich odpovídající aktivity a používat stejné parametry.  To může pomoct existující Autor sady runbook Orchestrator jejich přechází do Azure Automation runbook.

## <a name="system-center-orchestrator-integration-modules"></a>Moduly integrace produktu System Center Orchestrator
Společnost Microsoft poskytuje [integrační balíčky](http://technet.microsoft.com/library/hh295851.aspx) pro vytváření runbooků pro automatizaci součástí produktu System Center a další produkty.  Některé z těchto integrační balíčky jsou aktuálně založené na OIT ale nemůže aktuálně převedeny na moduly integrace z důvodu známé problémy.  [Moduly System Center Orchestrator Integration](https://www.microsoft.com/download/details.aspx?id=49555) zahrnuje převedený verzích tyto integrační balíčky, které lze importovat do Azure Automation a Service Management Automation.  

Ve verzi RTM tohoto nástroje bude publikována aktualizovaných verzí integračních balíčků podle OIT, který lze převést pomocí převaděče Integration Pack.  Pokyny bude poskytnuta také pomoci při převádění pomocí aktivit z integračních balíčků, které nejsou založené na OIT sady runbook.

## <a name="runbook-converter"></a>Převaděč sady Runbook
Převaděč Runbook převede sady runbook nástroje Orchestrator do [grafické runbooky](automation-runbook-types.md#graphical-runbooks) který lze importovat do Azure Automation.  

Převaděč Runbook je implementovaný jako modul prostředí PowerShell pomocí rutiny volat **ConvertFrom SCORunbook** který provede převod.  Když nainstalujete nástroj, vytvoří zástupce do relace prostředí PowerShell, který načítá rutinu.   

Toto je základní proces převést sady runbook Orchestrator a jeho import do Azure Automation.  Následující části obsahují další podrobnosti o používání nástroje a práce s převedené sady runbook.

1. Jedna nebo více sad runbook exportujte z nástroje Orchestrator.
2. Získáte moduly integrace pro všechny aktivity v sadě runbook.
3. Převeďte sady Orchestrator runbook v exportovaném souboru.
4. Zkontrolujte informace v protokolech ověření převod a k určení žádné vyžadovaly manuální úlohy.
5. Importování sad runbook převedený do Azure Automation.
6. Vytvořte všechny požadované prostředky ve službě Azure Automation.
7. Úprava runbooku ve službě Azure Automation k úpravě všech požadovaných aktivit.

### <a name="using-runbook-converter"></a>Pomocí převaděče sady Runbook
Syntaxe **ConvertFrom SCORunbook** vypadá takto:

    ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>

* RunbookPath – cesta k souboru exportu, který obsahuje sady runbook pro převod.
* Modul – čárkami oddělený seznam moduly integrace obsahující aktivity v sadách runbook.
* OutputFolder - cestu ke složce pro vytvoření převést grafické runbooky.

Následující ukázkový příkaz převede sady runbook do souboru exportu, názvem **MyRunbooks.ois_export**.  Tyto sady runbook pomocí integračních balíčků služby Active Directory a Data Protection Manager.

    ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"


### <a name="log-files"></a>Soubory protokolu
Převaděč Runbook vytvoří následující soubory protokolů ve stejném umístění jako převedené sady runbook.  Pokud soubory již existují, budou přepsány informace z poslední převod.

| File | Obsah |
|:--- |:--- |
| Převaděč Runbook - Progress.log |Podrobné kroky převodu, včetně informací pro každou aktivitu úspěšně převést a upozornění pro každou aktivitu nehodí pro převod. |
| Převaděč Runbook - Summary.log |Souhrn poslední převod včetně všechna upozornění a následnou akci úlohy, které je třeba provést jako je například vytváření proměnné požadované převedené sady runbook. |

### <a name="exporting-runbooks-from-orchestrator"></a>Export sad runbook z nástroje Orchestrator
Převaděč Runbook pracuje s souboru exportu z nástroje Orchestrator, který obsahuje jeden nebo více sad runbook.  Vytvoří odpovídající runbook automatizace Azure. pro každou sadu runbook Orchestrator do souboru exportu.  

Pokud chcete exportovat sady runbook z nástroje Orchestrator, klikněte pravým tlačítkem na název sady runbook v programu Runbook Designer a vyberte **exportovat**.  Pokud chcete exportovat všechny sady runbook ve složce, klikněte pravým tlačítkem na název složky a vyberte **exportovat**.

### <a name="runbook-activities"></a>Aktivity sady Runbook
Převaděč Runbook převede každou aktivitu v sadě runbook Orchestrator na odpovídající aktivita ve službě Azure Automation.  Pro ty aktivity, které nelze převést je vytvořen aktivitou zástupný symbol v sadě runbook s textem upozornění.  Po importu do Azure Automation převedené sady runbook, je potřeba nahradit některé z těchto aktivit platný aktivit, které provádějí požadované funkce.

Žádné aktivity Orchestrator v [standardní aktivity modulu](#standard-activities-module) bude převeden.  Existují některé standardní Orchestrator aktivity, které nejsou v tomto modulu ale a nejsou převést.  Například **odeslání události platformy** neobsahuje ekvivalent Azure Automation. vzhledem k tomu, že je událost specifická pro Orchestrator.

[Monitorování aktivit](https://technet.microsoft.com/library/hh403827.aspx) se nepřevádějí vzhledem k tomu, že není žádný ekvivalent k nim ve službě Azure Automation.  Výjimka je monitorování aktivit v [převést integrační balíčky](#integration-pack-converter) který text bude převeden na zástupný symbol aktivity.

Všechny aktivity z [převést integračního balíčku](#integration-pack-converter) převede Pokud zadáte cestu k modulu integrace s **moduly** parametr.  Pro System Center integračních balíčků, můžete použít [System Center Orchestrator integrační moduly](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Prostředky nástroje Orchestrator
Převaděč Runbook pouze převede sady runbook, není další prostředky nástroje Orchestrator například čítače, proměnné nebo připojení.  Čítače nejsou podporovány ve službě Azure Automation.  Připojení a proměnné jsou podporované, ale je třeba vytvořit ručně.  Soubory protokolu bude vás informovat, pokud sada runbook vyžaduje takových prostředků a zadejte odpovídající prostředky, které je potřeba vytvořit ve službě Azure Automation převedené sady runbook ke správnému fungování.

Sada runbook může například použít proměnné k naplnění konkrétní hodnotu v aktivitě.  Převedený runbook bude převést aktivity a zadejte variabilní prostředek se stejným názvem jako proměnnou Orchestrator ve službě Azure Automation.  To bude zaznamenán v **Runbook převaděč - Summary.log** souboru, který je vytvořen po převodu.  Musíte ručně vytvořit tento variabilní prostředek ve službě Azure Automation před použitím sady runbook.

### <a name="input-parameters"></a>Vstupní parametry
Sady Runbook v nástroji Orchestrator přijmout vstupní parametry s **inicializovat Data** aktivity.  Pokud sada runbook převáděné zahrnuje tato aktivita pak [vstupní parametr](automation-graphical-authoring-intro.md#runbook-input-and-output) ve službě Azure Automation runbook se vytvoří pro každý parametr v aktivitě.  A [řízení pracovního postupu skriptu](automation-graphical-authoring-intro.md#activities) vytvoření aktivity v převedený runbooku, který načte a vrátí jednotlivé parametry.  Všechny aktivity v sadě runbook, které používají vstupní parametr odkazovat na výstup z této aktivity.

Z důvodu, že se používá tato strategie je nejlépe zrcadlení funkce v sadě Orchestrator runbook.  Aktivity v nové grafické runbooky by měl naleznete přímo vstupní parametry pomocí zdroj vstupních dat sady Runbook.

### <a name="invoke-runbook-activity"></a>Aktivitu vyvolat sadu Runbook
Sady Runbook v nástroji Orchestrator spouštět další sady runbook s **vyvolání sady Runbook** aktivity. Pokud sada runbook převáděné obsahuje tuto aktivitu a **čekání na dokončení** je možnost nastavena, tak aktivity sady runbook je pro něj vytvořit v sadě runbook převedený.  Pokud **čekání na dokončení** není nastavena možnost a potom vytvoří aktivita pracovního postupu skriptu, který používá **Start-AzureAutomationRunbook** pro spuštění sady runbook.  Po importu převedené sady runbook do Azure Automation, musíte upravit tuto aktivitu s informacemi o určeného v aktivitě.

## <a name="related-articles"></a>Související články
* [System Center 2012 – Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
* [Službě Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [Hybridní pracovní proces Runbooku](automation-hybrid-runbook-worker.md)
* [Standardní aktivity Orchestrator](http://technet.microsoft.com/library/hh403832.aspx)
* [Stažení System Center Orchestrator Migration Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=47323)
