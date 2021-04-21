---
title: Postup plánování a nasazení serverů s podporou ARC Azure
description: Naučte se, jak povolit velké množství počítačů pro servery s podporou ARC Azure a zjednodušit tak konfiguraci základních funkcí zabezpečení, správy a monitorování v Azure.
ms.date: 04/21/2021
ms.topic: conceptual
ms.openlocfilehash: e3f8fe410da56f627ceab5f17c980f2daa1a262c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831973"
---
# <a name="plan-and-deploy-arc-enabled-servers"></a>Plánování a nasazení serverů s povoleným ARC

Nasazení služby IT infrastruktury nebo obchodní aplikace je výzvou k jakékoli společnosti. Aby se zajistilo, že bude dobře fungovat a nemusíte mít žádné nedokončené překvapením a neplánované náklady, budete ho muset důkladně naplánovat, abyste měli jistotu, že jste připravení. Chcete-li naplánovat nasazení serverů s podporou ARC Azure v jakémkoli měřítku, měl by pokrývat kritéria návrhu a nasazení, která je potřeba splnit, aby bylo možné úlohy úspěšně dokončit.

Aby nasazení mohlo pokračovat plynule, měl by váš plán vytvořit jasné porozumění:

* Role a odpovědnosti.
* Inventář fyzických serverů nebo virtuálních počítačů, aby bylo možné ověřit, že splňují požadavky na síť a systém.
* Sada dovedností a školení potřebné k povolení úspěšného nasazení a průběžné správy.
* Kritéria přijetí a způsob, jakým sledujete jeho úspěch.
* Nástroje nebo metody, které se mají použít k automatizaci nasazení.
* Zjištěná rizika a plány zmírnění omezení zabraňující prodlevám, přerušením atd.
* Jak se vyhnout přerušení během nasazování.
* Jaká je cesta k eskalaci při výskytu významného problému?

Účelem tohoto článku je zajistit úspěšné nasazení serverů s podporou ARC Azure v různých produkčních fyzických serverech nebo virtuálních počítačích ve vašem prostředí.

## <a name="prerequisites"></a>Požadavky

* V počítačích je spuštěný [podporovaný operační systém](agent-overview.md#supported-operating-systems) pro agenta připojeného počítače.
* Vaše počítače mají připojení z vaší místní sítě nebo jiného cloudového prostředí k prostředkům v Azure, a to buď přímo, nebo prostřednictvím proxy server.
* Chcete-li nainstalovat a nakonfigurovat servery s podporou ARC připojené počítačového agenta, účet se zvýšenými oprávněními (tj. správcem nebo jako root) na počítačích.
* Pokud chcete připojit počítače, jste členem role **připojení počítače připojeného k Azure** .
* Pokud chcete číst, upravovat a odstraňovat počítač, jste členem role **Správce prostředků počítače připojeného k Azure** .

## <a name="pilot"></a>Pilotní nasazení

Před nasazením na všechny produkční počítače začněte vyhodnocením tohoto procesu nasazení, než ho budete mít ve svém prostředí v podstatě. V případě pilotního projektu Identifikujte reprezentativní vzorkování počítačů, které nepostradatelné pro vaše společnosti schopnost podnikání. Budete chtít mít jistotu, že máte dostatek času na spuštění pilotního projektu a posoudit jeho dopad: doporučujeme minimálně 30 dnů.

Vytvořte formální plán, který popisuje rozsah a podrobnosti pilotního projektu. Následuje ukázka toho, co by měl plán zahrnovat, abyste mohli začít.

* Cíle – popisuje obchodní a technické ovladače, které vedly k rozhodnutí, že pilotní nasazení je nezbytné.
* Kritéria výběru – určuje kritéria pro výběr, které aspekty řešení budou prokazatelně prostřednictvím pilotního projektu.
* Rozsah – popisuje rozsah pilotního projektu, který zahrnuje mimo jiné součásti řešení, předpokládaný plán, dobu trvání pilotního projektu a počet počítačů, které se mají cílit.
* Kritéria a metriky úspěchu – definujte kritéria úspěšnosti pilotního nasazení a konkrétní míry, které se používají k určení úrovně úspěchu.
* Plán školení – popisuje plán pro školení systémových techniků, správců atd., kteří jsou během pilotního nasazení noví v Azure a IT službách.
* Plán přechodu – popisuje strategii a kritéria, pomocí kterých je možné řídit přechod z pilotního projektu do produkčního prostředí.
* Vrácení zpět – popisuje postupy vrácení pilotního projektu do stavu před nasazením.
* Rizika – Vypíše všechna zjištěná rizika pro vedení pilotního nasazení a přidružená k produkčnímu nasazení.

## <a name="phase-1-build-a-foundation"></a>Fáze 1: sestavení základu

V této fázi mohou systémové technici nebo správci povolit základní funkce v jejich předplatném Azure, aby spouštěli základ před tím, než umožní počítačům správu prostřednictvím serverů s podporou ARC a dalších služeb Azure.

|Úkol |Podrobnosti |Doba trvání |
|-----|-------|---------|
| [Vytvoření skupiny prostředků](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Vyhrazená skupina prostředků, která zahrnuje jenom servery s podporou ARC a centralizaci správy a monitorování těchto prostředků. | Jedna hodina |
| Použijte [značky](../../azure-resource-manager/management/tag-resources.md) , které vám pomůžou organizovat počítače. | Vyhodnoťte a sestavte [strategii označování](/azure/cloud-adoption-framework/decision-guides/resource-tagging/) zarovnaná na základě IT, která může přispět ke snížení složitosti správy serverů s povoleným obloukem a zjednodušení rozhodování o správě. | Jeden den |
| Návrh a nasazení [protokolů Azure monitor](../../azure-monitor/logs/data-platform-logs.md) | Vyhodnoťte [požadavky na návrh a nasazení](../../azure-monitor/logs/design-logs-deployment.md) , abyste zjistili, jestli by vaše organizace měla používat existující nebo implementovat jiný Log Analytics pracovní prostor k ukládání dat protokolů z hybridních serverů a počítačů. <sup>1</sup> | Jeden den |
| Vývoj plánu zásad správného řízení [v Azure Policy](../../governance/policy/overview.md) | Určete, jak budete implementovat zásady správného řízení pro hybridní servery a počítače v oboru předplatného nebo skupiny prostředků s Azure Policy. | Jeden den |
| Konfigurace [řízení přístupu na základě role](../../role-based-access-control/overview.md) (RBAC) | Vytvořte plán přístupu pro řízení, kdo má přístup ke správě serverů s podporou ARC a možnost zobrazit svá data z jiných služeb a řešení Azure. | Jeden den |
| Identifikace počítačů s již nainstalovaným agentem Log Analytics | Spusťte následující dotaz protokolu v [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md) , který podporuje převod stávajících nasazení agenta Log Analytics na agenta spravovaného rozšířením:<br> Tep <br> &#124;, kde před > TimeGenerated (30d) <br> &#124;, kde ResourceType = = "Machines" a (ComputerEnvironment = = "non-Azure") <br> &#124; Shrnutí podle počítačů, ResourceProvider, ResourceType, ComputerEnvironment | Jedna hodina |

<sup>1</sup> důležitým aspektem při vyhodnocení log Analyticsho návrhu pracovního prostoru je integrace s Azure Automationem v podpoře funkcí Update Management a Change Tracking a inventarizace a také Azure Security Center a Azure Sentinel. Pokud už vaše organizace má účet Automation a povolila funkce správy propojené s pracovním prostorem Log Analytics, vyhodnoťte, jestli můžete centralizovat a zefektivnit operace správy, a také minimalizovat náklady pomocí těchto existujících prostředků a vytvořit duplicitní účet, pracovní prostor atd.

## <a name="phase-2-deploy-arc-enabled-servers"></a>Fáze 2: nasazení serverů s podporou ARC

V dalším kroku přidáme do základu ve fázi 1, když připravujete a nasadíte servery s podporou ARC připojeného agenta počítače.

|Úkol |Podrobnosti |Doba trvání |
|-----|-------|---------|
| Stažení předem definovaného instalačního skriptu | Přečtěte si a přizpůsobte předem definovaný instalační skript pro nasazení připojeného strojového agenta v rámci škálování, aby se podporovaly požadavky na automatizované nasazení.<br><br> Ukázka prostředků připojování v měřítku:<br><br> <ul><li> [Základní skript pro nasazení ve velkém měřítku](onboard-service-principal.md)</ul></li> <ul><li>[Připojování VMware vSphere virtuálních počítačů s Windows serverem v měřítku](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_win/_index.md)</ul></li> <ul><li>[Připojování virtuálních počítačů se systémem VMware vSphere Linux v měřítku](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_linux/_index.md)</ul></li> <ul><li>[Připojování instancí AWS EC2 pomocí Ansible v měřítku](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/aws_scaled_ansible/_index.md)</ul></li> <ul><li>[Nasazení během škálování pomocí vzdálené komunikace PowerShellu](./onboard-powershell.md) (jenom Windows)</ul></li>| Jeden nebo více dní v závislosti na požadavcích, organizační procesy (například změna a Release Management) a použitá metoda automatizace. |
| [Vytvoření instančního objektu](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |Vytvořte instanční objekt pro připojení počítačů neinteraktivně pomocí Azure PowerShell nebo na portálu.| Jedna hodina |
| Nasazení agenta připojeného počítače na cílové servery a počítače |Pomocí nástroje pro automatizaci nasaďte skripty na vaše servery a připojte je k Azure.| Jeden nebo více dní v závislosti na plánu vydání a při dvoufázové zavedení. |

## <a name="phase-3-manage-and-operate"></a>Fáze 3: Správa a provoz

Fáze 3 vidí správcům nebo systémovým technikům automatizaci ručních úloh pro správu a provoz agenta připojeného počítače a počítače během jejich životního cyklu.

|Úkol |Podrobnosti |Doba trvání |
|-----|-------|---------|
|Vytvoření výstrahy Resource Health |Pokud server zastaví odesílání prezenčních signálů do Azure déle než 15 minut, může to znamenat, že je offline, síťové připojení je blokované nebo agent neběží. Vytvořte plán, jak budete reagovat a prozkoumat tyto incidenty, a pomocí [výstrah Resource Health dostávat upozornění](../..//service-health/resource-health-alert-monitor-guide.md) na jejich spuštění.<br><br> Při konfiguraci výstrahy zadejte následující:<br> **Typ prostředku**  =  **Servery s podporou ARC Azure**<br> **Aktuální stav prostředku**  =  **Nedostupné**<br> **Stav**  =  předchozího prostředku **K dispozici** | Jedna hodina |
|Vytvoření výstrahy Azure Advisor | Pro zajištění nejlepšího prostředí a nejnovějších oprav zabezpečení a chyb doporučujeme udržovat agenta serverů s podporou ARC Azure v aktuálním stavu. Neaktuální agenti budou identifikováni pomocí [výstrahy Azure Advisor](../../advisor/advisor-alerts-portal.md).<br><br> Při konfiguraci výstrahy zadejte následující:<br> **Typ doporučení**  =  **Upgradujte na nejnovější verzi agenta připojeného počítače Azure** . | Jedna hodina |
|[Přiřazení zásad Azure](../../governance/policy/assign-policy-portal.md) ke svému předplatnému nebo rozsahu skupiny prostředků |Přiřaďte k oboru předplatného nebo rozsahu skupiny prostředků [zásadu](../../azure-monitor/vm/vminsights-enable-policy.md) **Povolit Azure monitor pro virtuální počítače** (a další, které vyhovují vašim potřebám). Azure Policy vám umožní přiřadit definice zásad, které budou instalovat požadované agenty pro přehledy pro virtuální počítače v rámci vašeho prostředí.| Různé |
|[Povolit Update Management pro servery s povoleným obloukem](../../automation/update-management/enable-from-automation-account.md) |Nakonfigurujte Update Management v Azure Automation ke správě aktualizací operačního systému pro virtuální počítače s Windows a Linux zaregistrované u serverů s podporou ARC. | 15 minut |

## <a name="next-steps"></a>Další kroky

* Informace o řešení potíží najdete v části [Poradce při potížích s agentem připojeného počítače](troubleshoot-agent-onboard.md).

* Naučte se, jak zjednodušit nasazení s ostatními službami Azure, jako je [Konfigurace stavu](../../automation/automation-dsc-overview.md) Azure Automation a další podporovaná [rozšíření virtuálních počítačů Azure](manage-vm-extensions.md).