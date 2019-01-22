---
title: Migrace z nástroje Orchestrator do služby Azure Automation
description: Popisuje, jak migrovat sady runbook a integrační balíčky z nástroje System Center Orchestrator do služby Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ae47cba8f8e9a7cdf914c0b3ea5dfb9fa6c259a9
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432893"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrace z nástroje Orchestrator do služby Azure Automation (beta verze)
Sady Runbook v [System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) jsou založeny na aktivit z integračních balíčků, které jsou napsané konkrétně pro Orchestrátor, zatímco runbooky ve službě Azure Automation jsou založené na prostředí Windows PowerShell.  [Grafické runbooky](automation-runbook-types.md#graphical-runbooks) ve službě Azure Automation mají podobné vzhled, aby runbooky nástroje Orchestrator pomocí jejich aktivity představující rutiny prostředí PowerShell, podřízené runbooky a prostředky.

[Sadě nástrojů System Center Orchestrator migrace](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) obsahuje nástroje, které vám pomohou při převodu sad runbook z nástroje Orchestrator do Azure Automation.  Kromě převod sady runbook, samy, je nutné převést integrační balíčky s aktivitami, které používají sady runbook na moduly integrace pomocí rutin prostředí Windows PowerShell.  

Toto je základní proces převodu runbooky nástroje Orchestrator do Azure Automation.  Každý z těchto kroků je podrobně popsány v níže uvedených částech.

1. Stáhněte si [sadě nástrojů System Center Orchestrator migrace](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) obsahující nástroje a moduly, které jsou popsané v tomto článku.
2. Import [standardní aktivity modulu](#standard-activities-module) do služby Azure Automation.  To zahrnuje převedená verze standardní aktivity Orchestratoru, které mohou být využívána převedené sady runbook.
3. Import [System Center Orchestrator integrační moduly](#system-center-orchestrator-integration-modules) do Azure Automation pro tato integrační balíčky používané vaší sady runbook, který přístup ke službě System Center.
4. Převést vlastní a integrační balíčky třetích stran pomocí [Integration Pack převaděč](#integration-pack-converter) a importovat do služby Azure Automation.
5. Pomocí sad runbook produktu Orchestrator převést [Runbook převaděč](#runbook-converter) a nainstalujte ve službě Azure Automation.
6. Ručně vytvořte požadované prostředky nástroje Orchestrator ve službě Azure Automation, protože převaděč sady Runbook nelze převést tyto prostředky.
7. Konfigurace [Hybrid Runbook Worker](#hybrid-runbook-worker) v místním datovém centru ke spuštění převedené sady runbook, který bude mít přístup k místním prostředkům.

## <a name="service-management-automation"></a>Service Management Automation
[Službě Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx) (SMA) ukládá a v místním datovém centru jako Orchestrátor spouští sady runbook a používá stejné moduly integrace jako Azure Automation. [Runbook převaděč](#runbook-converter) převede runbooky nástroje Orchestrator do grafických runbooků ale které nejsou podporované ve službě SMA.  Je možné instalovat [standardní aktivity modul](#standard-activities-module) a [System Center Orchestrator integrační moduly](#system-center-orchestrator-integration-modules) do služby SMA, ale vy musíte ručně [přepsat vašich sadách runbook](https://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker
Sady Runbook v nástroji Orchestrator jsou uloženy na serveru databáze a spusťte v serverech sady runbook, i v místním datovém centru.  Sady Runbook ve službě Azure Automation se ukládají v cloudu Azure a můžete spustit v místních dat pomocí System center [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).  To je, jak bude obvykle fungovat převést z nástroje Orchestrator, protože jsou navrženy pro spouštění na místní servery sady runbook.

## <a name="integration-pack-converter"></a>Převaděč Integration Pack
Převede integračních balíčků, které byly vytvořeny pomocí konvertoru Integration Pack [Orchestrator Integration Toolkit (OIT)](https://technet.microsoft.com/library/hh855853.aspx) moduly integrace založené na Windows Powershellu, který lze naimportovat do služby Azure Automation nebo Službě Service Management Automation.  

Když spustíte převaděč Integration Pack, zobrazí se průvodce, který vám umožní vybrat soubor integration pack (.oip).  Průvodce potom zobrazí aktivity součástí daného integračního balíčku a umožňuje vybrat, které se budou migrovat.  Po dokončení Průvodce vytvoří modulu integrace, který obsahuje odpovídající rutiny pro všechny aktivity v původní integračního balíčku.

### <a name="parameters"></a>Parametry
Všechny vlastnosti aktivity v integračním balíčku se převedou na parametry odpovídající rutiny v modulu integrace.  Rutiny prostředí Windows PowerShell mají sadu [společné parametry](https://technet.microsoft.com/library/hh847884.aspx) , který jde použít s všechny rutiny.  Například-Verbose parametr způsobí, že rutiny výstup podrobné informace o její činnosti.  Parametr se stejným názvem jako společný parametr může mít žádné rutiny.  Pokud aktivita nemá vlastnost se stejným názvem jako společný parametr, Průvodce vás vyzve k zadejte jiný název parametru.

### <a name="monitor-activities"></a>Monitorování aktivit
Sledování sad runbook v produktu Orchestrator začíná [sledovat činnost](https://technet.microsoft.com/library/hh403827.aspx) a spouštět nepřetržitě čeká se na konkrétní události vyvolat.  Azure Automation nepodporuje sady runbook monitorování, tak nebudou převedeny žádné monitorování aktivity v integračním balíčku.  V modulu integrace pro sledování aktivity místo toho se vytvoří rutina zástupný symbol.  Tato rutina nemá žádné funkce, ale umožňuje převedené sady runbook, který používá k instalaci.  Tato sada runbook nebude možné spustit ve službě Azure Automation, ale dá se nainstalovat tak, aby ho upravit.

### <a name="integration-packs-that-cannot-be-converted"></a>Integrační balíčky, které nelze převést
Integrační balíčky, které nebyly vytvořené pomocí OIT nelze převést pomocí konvertoru Integration Pack. Existují také některé integračních balíčků poskytovaných microsoftem, který momentálně nelze převést pomocí tohoto nástroje.  Převedená verze těchto integračních balíčků bylo [k dispozici ke stažení](#system-center-orchestrator-integration-modules) tak, aby se dá nainstalovat ve službě Azure Automation nebo Service Management Automation.

## <a name="standard-activities-module"></a>Standardní aktivity modulu
Nástroje Orchestrator obsahuje sadu [standardní aktivity](https://technet.microsoft.com/library/hh403832.aspx) , které nejsou součástí integračního balíčku, ale runbooků, které používají.  V modulu standardních aktivit je modul integrace, která obsahuje ekvivalentní rutinu pro každý z těchto činností.  Tento modul integrace ve službě Azure Automation je nutné nainstalovat před importem převedené sady runbook, který použít standardní aktivitu.

Kromě podpory převedené sady runbook, rutiny v modulu standardních aktivit je možné někým zkušenosti s nástrojem Orchestrator k vytvoření nové sady runbook ve službě Azure Automation.  Když funkce všech standardních aktivit, můžete to provést pomocí rutin, může pracovat jinak.  Rutiny v modulu převedený standardní aktivity bude fungovat stejně jako jejich odpovídající aktivity a použijte stejné parametry.  To může pomoct existující Autor runbooku nástroje Orchestrator v jejich přechod k sadám runbook Azure Automation.

## <a name="system-center-orchestrator-integration-modules"></a>System Center Orchestrator Integration Modules
Společnost Microsoft poskytuje [integrační balíčky](https://technet.microsoft.com/library/hh295851.aspx) pro vytváření runbooků pro automatizaci součástí produktu System Center a další produkty.  Některé z těchto integračních balíčků aktuálně vycházejí z OIT ale nejde momentálně převést na moduly integrace kvůli známé problémy.  [System Center Orchestrator integrační moduly](https://www.microsoft.com/download/details.aspx?id=49555) obsahuje převedená verze tato integrační balíčky, které se dají importovat do služby Azure Automation a Service Management Automation.  

Aktualizované verze integračních balíčků podle OIT, který lze převést pomocí konvertoru Integration Pack bude publikován ve verzi RTM tohoto nástroje.  Pokyny k také vám poskytneme vám pomohou při převádění pomocí aktivit z integračních balíčků, který není založen na OIT sady runbook.

## <a name="runbook-converter"></a>Převaděč sady Runbook
Převaděč Runbook převede runbooky nástroje Orchestrator do [grafické runbooky](automation-runbook-types.md#graphical-runbooks) , který může být importován do služby Azure Automation.  

Převaděč sady Runbook je implementovaný jako modul Powershellu pomocí rutiny volat **ConvertFrom-SCORunbook** , který provede převod.  Když nainstalujete nástroj, se vytvoří místní relaci Powershellu, který načte rutina.   

Toto je základní proces převést sady runbook Orchestrator a jeho import do Azure Automation.  Následující části obsahují další podrobnosti o používání nástroje a práci s runbooky převedený.

1. Exportujte jeden nebo více sad runbook z nástroje Orchestrator.
2. Získání modulů integrace pro všechny aktivity v sadě runbook.
3. Převeďte runbooky nástroje Orchestrator v exportovaném souboru.
4. Přečtěte si informace v protokolech ověření převod a určit všechny vyžadovaly manuální úlohy.
5. Importujte runbooků převedený do Azure Automation.
6. Vytvořte všechny požadované prostředky ve službě Azure Automation.
7. Úprava runbooku ve službě Azure Automation k úpravě jakékoli požadované aktivity.

### <a name="using-runbook-converter"></a>Použití převaděče sady Runbook
Syntaxe pro **ConvertFrom-SCORunbook** vypadá takto:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath - cestu k souboru exportu, který obsahuje sady runbook pro převod.
* Modul – čárkami oddělený seznam integrační moduly obsahující aktivity v sadách runbook.
* OutputFolder – cestu ke složce pro vytvoření převést grafické runbooky.

V tomto ukázkovém příkazu převede sady runbook do souboru exportu, názvem **MyRunbooks.ois_export**.  Tyto sady runbook pomocí integračních balíčků služby Active Directory a aplikace Data Protection Manager.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="log-files"></a>Soubory protokolu
Převaděč sady Runbook se vytvoří následující soubory protokolu ve stejném umístění jako převedené sady runbook.  Pokud soubory již existují, budou přepsány s informacemi z poslední převodu.

| File | Obsah |
|:--- |:--- |
| Sada Runbook převaděč - Progress.log |Podrobné kroky převodů, včetně informací o každé aktivitě úspěšně převedena a upozornění pro každou aktivitu, nepřevedeno |
| Sada Runbook převaděč - Summary.log |Seznam posledních převodů, včetně všechna upozornění a úlohy, které musíte provést jako je například vytváření proměnná požadovaná převedené sady runbook. |

### <a name="exporting-runbooks-from-orchestrator"></a>Export sad runbook z nástroje Orchestrator
Sada Runbook převaděče souborem exportu v nástroji Orchestrator, který obsahuje jeden nebo více sad runbook.  Vytvoří odpovídající runbook Azure Automation. pro každou sadu runbook nástroje Orchestrator do souboru exportu.  

Pokud chcete exportovat sady runbook z nástroje Orchestrator, klikněte pravým tlačítkem na název sady runbook v programu Runbook Designer a vyberte **exportovat**.  Export všech sad runbook ve složce, klikněte pravým tlačítkem na název složky a vyberte **exportovat**.

### <a name="runbook-activities"></a>Aktivity sady Runbook
Převaděč Runbook převede každé aktivity v sadě runbook nástroje Orchestrator na odpovídající aktivity ve službě Azure Automation.  Pro ty aktivity, které nelze převést je vytvořen aktivitou zástupný symbol v sadě runbook s textem upozornění.  Po importu převedené sady runbook do služby Azure Automation, je potřeba nahradit některé z těchto aktivit platný aktivit, které provádějí požadovanou funkci.

Všechny aktivity Orchestratoru v [standardní aktivity modul](#standard-activities-module) budou převedeny.  Existují některé standardní aktivity Orchestratoru, které nejsou v tomto modulu však a nepřevádějí.  Například **odeslání události platformy** nemá žádný ekvivalent Azure Automation, protože je událost specifická pro Orchestrator.

[Monitorování aktivit](https://technet.microsoft.com/library/hh403827.aspx) nejsou převést, protože neexistuje žádný ekvivalent k nim ve službě Azure Automation.  Výjimky jsou monitorování aktivity v [převést integrační balíčky](#integration-pack-converter) , který text bude převeden na aktivitu zástupný symbol.

Všechny aktivity z [převést integračního balíčku](#integration-pack-converter) bude převeden, pokud zadáte cestu k modulu integrace s **moduly** parametru.  Integrační balíčky System Center, můžete použít [System Center Orchestrator integrační moduly](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Prostředky orchestratoru
Převaděč Runbook pouze převede sady runbook, ne další prostředky nástroje Orchestrator jako jsou čítače, proměnné a připojení.  Čítače nejsou podporovány ve službě Azure Automation.  Připojení a proměnné jsou podporovány, ale musíte je vytvořit ručně.  Poznáte, sada runbook vyžaduje těchto prostředků a zadejte odpovídající prostředky, které je potřeba vytvořit ve službě Azure Automation převedené sady runbook ke správnému fungování souborů protokolu.

Sada runbook může například použít proměnnou k naplnění určitou hodnotu v nějaké aktivitě.  Převedené sady runbook se převést aktivitu a zadejte variabilní prostředek se stejným názvem jako proměnná Orchestrator ve službě Azure Automation.  To bude zaznamenán v **Runbook převaděč - Summary.log** soubor, který je vytvořen po převodu.  Je potřeba ručně vytvořit tento variabilní prostředek ve službě Azure Automation před použitím sady runbook.

### <a name="input-parameters"></a>Vstupní parametry
Vstupní parametry s přijmout sady Runbook v produktu Orchestrator **inicializovat Data** aktivity.  Pokud sada runbook převáděn obsahuje tato aktivita pak [vstupní parametr](automation-graphical-authoring-intro.md#runbook-input-and-output) ve službě Azure Automation runbook se vytvoří pro každý parametr v rámci aktivity.  A [ovládací prvek skriptu pracovního postupu](automation-graphical-authoring-intro.md#activities) vytvoření aktivity v převedené sady runbook, která načte a vrátí jednotlivé parametry.  Aktivity sady runbook, které používají jako vstupní parametr najdete ve výstupu z této aktivity.

Z důvodu, že se používá tato strategie je nejvhodnější zrcadlící funkce v sadě runbook produktu Orchestrator.  Aktivity v nové grafické runbooky by měla odkazovat přímo na vstupní parametry, které používají zdroj vstupních dat sady Runbook.

### <a name="invoke-runbook-activity"></a>Aktivitu vyvolat sadu Runbook
Sady Runbook v nástroji Orchestrator spouštět další sady runbook s **vyvolání sady Runbook** aktivity. Pokud tato aktivita obsahuje runbook převáděn a **počkat na dokončení** možnost je nastavena, pak aktivity sady runbook se vytvoří pro ni v převedené sady runbook.  Pokud **počkat na dokončení** není nastavena možnost, pak je vytvořen aktivity pracovního postupu skriptu, který používá **Start AzureAutomationRunbook** pro spuštění sady runbook.  Jakmile dokončíte import převedené sady runbook do služby Azure Automation, je třeba upravit tuto aktivitu s informacemi o určeného v aktivitě.

## <a name="related-articles"></a>Související články
* [System Center 2012 – Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Službě Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [Funkce hybrid Runbook Worker](automation-hybrid-runbook-worker.md)
* [Standardní aktivity orchestratoru](https://technet.microsoft.com/library/hh403832.aspx)
* [Stažení System Center Orchestrator Migration Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=47323)

