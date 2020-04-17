---
title: Migrace z Orchestratoru do Azure Automation
description: Popisuje, jak migrovat sady Runbook a integrační balíčky z Orchestrator System Center do Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: c7df6e31cd021fc61129131f9bd02acc7b96e2ad
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457548"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrace z Orchestratoru do Azure Automation (beta)

Runbooky v [System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) jsou založeny na aktivitách z integračních balíčků, které jsou napsané speciálně pro Orchestrator, zatímco runbooky v Azure Automation jsou založené na Windows PowerShellu.  [Grafické runbooky](automation-runbook-types.md#graphical-runbooks) v Azure Automation mají podobný vzhled jako runbooky Orchestrator s jejich aktivitami představujícími rutiny Prostředí PowerShell, podřízené runbooky a datové zdroje.

Sada [nástrojů pro migraci Orchestrator system center obsahuje](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) nástroje, které vám pomohou při převodu runbooků z Orchestratoru na Azure Automation.  Kromě převodu samotných sad Runbook je nutné převést integrační balíčky s aktivitami, které sady Runbook používají, na integrační moduly s rutinami prostředí Windows PowerShell.  

Následuje základní proces pro převod runbooků Orchestrator na Azure Automation.  Každý z těchto kroků je podrobně popsán v následujících částech.

1. Stáhněte si [sadu nástrojů pro migraci Orchestrator system center,](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) která obsahuje nástroje a moduly popsané v tomto článku.
2. Importujte [modul standardních aktivit](#standard-activities-module) do Azure Automation.  To zahrnuje převedené verze standardních aktivit Orchestrator, které mohou být používány převedenými runbooky.
3. Importujte [moduly integrace System Center Orchestrator](#system-center-orchestrator-integration-modules) do Azure Automation pro integrační balíčky používané vašimi runbooky, které přistupují k System Center.
4. Převeďte vlastní balíčky a balíčky integrace třetích stran pomocí [převaděče integration pack](#integration-pack-converter) a importujte do Azure Automation.
5. Převeďte runbooky Orchestrator pomocí [převaděče Runbook](#runbook-converter) a nainstalujte je v Azure Automation.
6. Ručně vytvořit požadované prostředky Orchestrator v Azure Automation, protože Runbook Converter nepřevede tyto prostředky.
7. Nakonfigurujte [hybridní pracovník sady Runbook](#hybrid-runbook-worker) v místním datovém centru pro spouštění převedených sad Runbook, které budou mít přístup k místním prostředkům.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="service-management-automation"></a>Service Management Automation

[Automatizace správy služeb](https://technet.microsoft.com/library/dn469260.aspx) (SMA) ukládá a spouští runbooky ve vašem místním datovém centru, jako je Orchestrator, a používá stejné integrační moduly jako Azure Automation. [Runbook Converter](#runbook-converter) převádí Orchestrator runbooky na grafické runbooky, které nejsou podporovány v SMA.  Stále můžete nainstalovat [modul pro standardní aktivity](#standard-activities-module) a [moduly integrace orchestrátoru system center](#system-center-orchestrator-integration-modules) do sma, ale musíte ručně přepsat sady [Runbook](https://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Sady Runbook v Orchestrator jsou uloženy na databázovém serveru a spouštěny na serverech sady Runbook, a to jak v místním datovém centru.  Runbooky v Azure Automation se ukládají v cloudu Azure a můžou běžet ve vašem místním datovém centru pomocí [hybridního runbookového pracovníka](automation-hybrid-runbook-worker.md).  Tímto způsobem obvykle spustíte runbooky převedené z Orchestrator, protože jsou navrženy tak, aby běžely na místních serverech.

## <a name="integration-pack-converter"></a>Integrační balíček Converter

Integrační balíček Converter převádí integrační balíčky, které byly vytvořeny pomocí [Orchestrator Integration Toolkit (OIT)](https://technet.microsoft.com/library/hh855853.aspx) na integrační moduly založené na prostředí Windows PowerShell, které lze importovat do Azure Automation nebo Service Management Automation.  

Při spuštění integračního převaděče sady Pack se zobrazí průvodce, který vám umožní vybrat soubor integračního balíčku (.oip).  Průvodce pak zobrazí seznam aktivit zahrnutých v tomto integračním balíčku a umožní vám vybrat, které budou migrovány.  Po dokončení průvodce vytvoří integrační modul, který obsahuje odpovídající rutinu pro každou z aktivit v původním integračním balíčku.

### <a name="parameters"></a>Parametry

Všechny vlastnosti aktivity v integračním balíčku jsou převedeny na parametry odpovídající rutiny v integračním modulu.  Rutiny prostředí Windows PowerShell mají sadu [běžných parametrů,](https://technet.microsoft.com/library/hh847884.aspx) které lze použít se všemi rutinami. Například parametr -Verbose způsobí, že rutina výstup podrobné informace o jeho provozu.  Žádná rutina může mít parametr se stejným názvem jako společný parametr. Pokud aktivita má vlastnost se stejným názvem jako společný parametr, průvodce vás vyzve k zadání jiného názvu parametru.

### <a name="monitor-activities"></a>Monitorování aktivit

Sledování runbooků v Orchestrátoru začíná [aktivitou monitoru](https://technet.microsoft.com/library/hh403827.aspx) a běží nepřetržitě a čeká na vyvolání konkrétní událostí.  Azure Automation nepodporuje monitorování runbooků, takže žádné aktivity monitorování v integračním balíčku nebudou převedeny.  Místo toho je v integračním modulu pro aktivitu monitoru vytvořena zástupná rutina.  Tato rutina nemá žádné funkce, ale umožňuje instalaci libovolného převedeného runbooku, který ji používá.  Tato runbook nebude možné spustit v Azure Automation, ale může být nainstalována tak, že můžete upravit.

### <a name="integration-packs-that-cannot-be-converted"></a>Integrační balíčky, které nelze převést

Integrační balíčky, které nebyly vytvořeny pomocí OIT nelze převést pomocí převaděče integračního balíčku. Existují také některé integrační balíčky poskytované společností Microsoft, které nelze v současné době převést pomocí tohoto nástroje.  Převedené verze těchto integračních balíčků byly [k dispozici ke stažení,](#system-center-orchestrator-integration-modules) aby je bylo možné nainstalovat do Azure Automation nebo Service Management Automation.

## <a name="standard-activities-module"></a>Modul standardních aktivit

Orchestrator obsahuje sadu [standardních aktivit,](https://technet.microsoft.com/library/hh403832.aspx) které nejsou zahrnuty v integračním balíčku, ale používají je mnoho runbooků.  Modul Standardní aktivity je integrační modul, který obsahuje ekvivalent rutiny pro každou z těchto aktivit.  Tento integrační modul je nutné nainstalovat v Azure Automation před importem všech převedených runbooků, které používají standardní aktivitu.

Kromě podpory převedených runbooků může rutiny v modulu standardních aktivit používat někdo obeznámený s Orchestrator k vytváření nových runbooků v Azure Automation.  Zatímco funkce všech standardních činností lze provádět pomocí rutin, mohou pracovat odlišně.  Rutiny v modulu převedených standardních aktivit budou fungovat stejně jako jejich odpovídající aktivity a budou používat stejné parametry.  To může pomoci existující orchestrator autor a runbook v jejich přechodu na runbooky Azure Automation.

## <a name="system-center-orchestrator-integration-modules"></a>Moduly integrace Orchestrátoru systémového centra

Společnost Microsoft poskytuje [integrační balíčky](https://technet.microsoft.com/library/hh295851.aspx) pro vytváření runbooků pro automatizaci součástí System Center a dalších produktů.  Některé z těchto integračních balíčků jsou v současné době založeny na OIT, ale v současné době nemohou být převedeny na integrační moduly z důvodu známých problémů.  [Moduly integrace System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) obsahují převedené verze těchto integračních balíčků, které lze importovat do azure automatizace a automatizace správy služeb.  

Podle RTM verze tohoto nástroje budou publikovány aktualizované verze integračních balíčků založených na OIT, které lze převést pomocí integračního převaděče.  Budou také poskytnuty pokyny, které vám pomohou při převodu runbooků pomocí aktivit z integračních balíčků, které nejsou založeny na OIT.

## <a name="runbook-converter"></a>Převaděč stek

Převaděč Runbook převádí Orchestrator runbooky na [grafické runbooky,](automation-runbook-types.md#graphical-runbooks) které lze importovat do Azure Automation.  

Runbook Converter je implementován jako modul Prostředí `ConvertFrom-SCORunbook` PowerShell s rutinou volanou, která provádí převod.  Při instalaci nástroje vytvoří zástupce relace prostředí PowerShell, který načte rutinu.   

Následuje základní proces převodu runbooku Orchestrator a jeho importu do Azure Automation.  V následujících částech jsou uvedeny další podrobnosti o používání nástroje a práci s převedenými sadami runbooků.

1. Exportujte jeden nebo více runbooků z Orchestratoru.
2. Získejte integrační moduly pro všechny aktivity v runbooku.
3. Převeďte runbooky Orchestrator v exportovaném souboru.
4. Zkontrolujte informace v protokolech k ověření převodu a určit všechny požadované ruční úkoly.
5. Importujte převedené runbooky do Azure Automation.
6. Vytvořte všechny požadované prostředky v Azure Automation.
7. Upravte runbook v Azure Automation a upravte všechny požadované aktivity.

### <a name="using-runbook-converter"></a>Použití převaděče runbooku

Syntaxe pro **ConvertFrom-SCORunbook** je následující:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath - Cesta k exportnímu souboru obsahujícímu sady Runbook, které chcete převést.
* Modul - Čárka oddělený seznam integračních modulů obsahujících aktivity v runbookech.
* OutputFolder - Cesta ke složce k vytvoření převedených grafických sad Runbook.

Následující příkaz příkladpřevede sady Runbook v exportním souboru s názvem **MyRunbooks.ois_export**.  Tyto sady Runbook používají integrační balíčky Active Directory a Data Protection Manager.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="log-files"></a>Soubory protokolu

Převaděč runbookvytvoří následující soubory protokolu ve stejném umístění jako převedený runbook.  Pokud soubory již existují, budou přepsány informacemi z posledního převodu.

| File | Obsah |
|:--- |:--- |
| Převaděč runbooku – průběh.log |Podrobné kroky převodu včetně informací o každé aktivitě, která byla úspěšně převedena, a upozornění pro každou aktivitu, která nebyla převedena. |
| Převaděč runbook - Summary.log |Souhrn posledního převodu včetně všech upozornění a následných úloh, které je třeba provést, jako je vytvoření proměnné požadované pro převedenou runbook. |

### <a name="exporting-runbooks-from-orchestrator"></a>Export runbooků z Orchestrátoru

Převaděč sady Runbook pracuje s exportním souborem z Orchestratoru, který obsahuje jednu nebo více sad Runbook.  Vytvoří odpovídající runbook Azure Automation pro každý runbook Orchestrator v souboru exportu.  

Chcete-li exportovat runbook z Orchestrator, klepněte pravým tlačítkem myši na název runbooku v návrháři runbooku a vyberte **exportovat**.  Chcete-li exportovat všechny sady Runbook ve složce, klepněte pravým tlačítkem myši na název složky a vyberte příkaz **Exportovat**.

### <a name="runbook-activities"></a>Aktivity sady Runbook

Převaděč runbook převádí každou aktivitu v runbooku Orchestrator na odpovídající aktivitu v Azure Automation.  Pro aktivity, které nelze převést, je v runbooku vytvořena zástupná aktivita s výstražným textem.  Po importu převedené runbook do Azure Automation, musíte nahradit některé z těchto aktivit s platnými aktivitami, které provádějí požadované funkce.

Všechny aktivity Orchestrator v [modulu standardní aktivity](#standard-activities-module) budou převedeny.  Existují některé standardní Orchestrator aktivity, které nejsou v tomto modulu i když a nejsou převedeny.  Například `Send Platform Event` nemá žádný ekvivalent Azure Automation, protože událost je specifická pro Orchestrator.

[Aktivity monitorování](https://technet.microsoft.com/library/hh403827.aspx) se nepřevedou, protože v Azure Automation není žádný ekvivalent.  Výjimkou jsou aktivity monitorování v [převedených integračních balíčcích,](#integration-pack-converter) které budou převedeny na zástupnou aktivitu.

Všechny aktivity z [převedeného integračního balíčku](#integration-pack-converter) budou převedeny, `modules` pokud poskytnete cestu k integračnímu modulu s parametrem. Pro sady System Center Integration Pack můžete použít [moduly integrace System Center Orchestrator](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Zdroje orchestrator

Převaděč sady Runbook převádí pouze runbooky, nikoli jiné prostředky Orchestrator, jako jsou čítače, proměnné nebo připojení.  Čítače nejsou podporovány v Azure Automation.  Proměnné a připojení jsou podporovány, ale je nutné je vytvořit ručně.  Soubory protokolu vás budou informovat, pokud runbook vyžaduje takové prostředky a určit odpovídající prostředky, které je třeba vytvořit v Azure Automation pro převedené runbook správně fungovat.

Runbook může například použít proměnnou k naplnění určité hodnoty v aktivitě.  Převedený runbook převede aktivitu a určí proměnnou aktivum v Azure Automation se stejným názvem jako proměnná Orchestrator.  To bude zaznamenáno v souboru **Runbook Converter - Summary.log,** který je vytvořen po převodu.  Před použitím sady Runbook budete muset ručně vytvořit tento datový zdroj proměnných v Azure Automation.

### <a name="input-parameters"></a>Vstupní parametry

Runbooky v Orchestrator přijímají `Initialize Data` vstupní parametry s aktivitou.  Pokud převáděný runbook zahrnuje tuto aktivitu, vytvoří se [vstupní parametr](automation-graphical-authoring-intro.md#runbook-input-and-output) v runbooku Azure Automation pro každý parametr v aktivitě.  Aktivita [ovládacího prvku Skript pracovního postupu](automation-graphical-authoring-intro.md#activities) je vytvořena v převedeném runbooku, který načítá a vrací každý parametr.  Všechny aktivity v runbooku, které používají vstupní parametr odkazují na výstup z této aktivity.

Důvod, proč se tato strategie používá, je co nejlépe zrcadlit funkce v runbooku Orchestrator.  Aktivity v nových grafických sadách runbooků by měly odkazovat přímo na vstupní parametry pomocí vstupního zdroje dat sady Runbook.

### <a name="invoke-runbook-activity"></a>Vyvolat aktivitu runbooku

Runbooky v Orchestrátoru spouštějí s aktivitou další runbooky. `Invoke Runbook` Pokud převáděný soubor Runbook zahrnuje tuto aktivitu `Wait for completion` a možnost je nastavena, vytvoří se pro ni aktivita sady Runbook v převedené sadě Runbook.  Pokud `Wait for completion` tato možnost není nastavena, vytvoří se aktivita skriptu pracovního postupu, která ke spuštění sady runbook používá [start-azautomationrunbook.](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) Po importu převedeného runbooku do Azure Automation je nutné tuto aktivitu upravit informacemi zadanými v aktivitě.

## <a name="related-articles"></a>Související články

* [System Center 2012 - Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)
* [Standardní aktivity Orchestrator](https://technet.microsoft.com/library/hh403832.aspx)
* [Stáhnout sadu nástrojů pro migraci nástroje System Center Orchestrator](https://www.microsoft.com/en-us/download/details.aspx?id=47323)

