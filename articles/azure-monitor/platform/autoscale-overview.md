---
title: Automatické škálování v Microsoft Azure
description: Automatické škálování v Microsoft Azure
ms.subservice: autoscale
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: e89cf36e59ab26ec220d1125162a841fc10f0e47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91567260"
---
# <a name="overview-of-autoscale-in-microsoft-azure"></a>Přehled automatického škálování v Microsoft Azure
Tento článek popisuje, co Microsoft Azure automatické škálování, jeho výhody a jak ho začít používat.  

Automatické škálování Azure Monitor platí jenom pro [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/), [API Management služby](../../api-management/api-management-key-concepts.md)a [clustery Azure Průzkumník dat](/azure/data-explorer/).

> [!NOTE]
> Azure má dvě metody automatického škálování. Starší verze automatického škálování se vztahuje na Virtual Machines (skupiny dostupnosti). Tato funkce má omezené podpory a doporučujeme migrovat na sady škálování virtuálních počítačů pro rychlejší a spolehlivější podporu automatického škálování. Tento článek obsahuje odkaz na použití starší technologie.  
>

## <a name="what-is-autoscale"></a>Co je automatické škálování?
Automatické škálování umožňuje mít ke zpracování zatížení vaší aplikace správné množství prostředků, na kterých běží. Umožňuje přidat prostředky pro zvýšení zátěže a také ušetřit peníze odebráním nečinných prostředků. Zadáte minimální a maximální počet instancí, které se mají spustit, a přidat nebo odebrat virtuální počítače automaticky na základě sady pravidel. Minimální je, že aplikace je vždycky spuštěná, i když bez zatížení. Maximální počet vydaných hodinových nákladů je omezený. Automatické škálování mezi těmito dvěma extrémními hodnotami se provádí pomocí pravidel, která vytvoříte.

 ![Automatické škálování je vysvětleno. Přidání a odebrání virtuálních počítačů](./media/autoscale-overview/AutoscaleConcept.png)

Při splnění podmínek pravidla se aktivuje jedna nebo víc akcí automatického škálování. Můžete přidat nebo odebrat virtuální počítače nebo provádět jiné akce. Tento proces je znázorněn v následujícím koncepčním diagramu.  

 ![Diagram toku automatického škálování](./media/autoscale-overview/Autoscale_Overview_v4.png)

Následující vysvětlení se vztahují na části předchozího diagramu.   

## <a name="resource-metrics"></a>Metriky prostředků
Prostředky generují metriky. Tyto metriky jsou později zpracovávány pravidly. Metriky přicházejí prostřednictvím různých metod.
Služba Virtual Machine Scale Sets používá data telemetrie z agentů Azure Diagnostics. vzhledem k tomu, že telemetrie pro webové aplikace a cloudové služby pochází přímo z infrastruktury Azure. Mezi běžně používané statistiky patří využití CPU, využití paměti, počty vláken, délka fronty a využití disku. Seznam dat telemetrie, která můžete použít, najdete v tématu [běžné metriky automatického škálování](autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Vlastní metriky
Můžete také využít vlastní metriky, které mohou vaše aplikace vysílat. Pokud jste nakonfigurovali své aplikace pro posílání metrik pro Application Insights můžete tyto metriky využít k rozhodování o tom, jestli se má škálovat nebo ne.

## <a name="time"></a>Čas
Pravidla založená na plánu jsou založená na standardu UTC. Při nastavování pravidel musíte nastavit časové pásmo správně.  

## <a name="rules"></a>Pravidla
Diagram zobrazuje pouze jedno pravidlo automatického škálování, ale může jich mít mnoho. V případě potřeby můžete vytvořit složitá překrývající se pravidla.  Typy pravidel zahrnují  

* Tato akce se provádí například **na základě metriky** , když je využití procesoru nad 50%.
* **Čas založený na čase** – například aktivovat Webhook každou 8:00 v sobotu v daném časovém pásmu.

Pravidla založená na metrikách měří zatížení aplikace a na základě tohoto zatížení přidávají nebo odebírají virtuální počítače. Pravidla založená na plánu umožňují škálování, když vidíte vzory času v zatížení a chcete škálovat, než dojde ke zvýšení nebo snížení možného zatížení.  

## <a name="actions-and-automation"></a>Akce a automatizace
Pravidla mohou aktivovat jeden nebo více typů akcí.

* **Škálování virtuálních** počítačů na více instancí
* **E-mail** – odeslání e-mailu správcům předplatného, spolupracovníkům a/nebo další e-mailové adresy, kterou zadáte
* **Automatizace prostřednictvím webhooků** – volání webhooků, která můžou aktivovat víc složitých akcí v rámci nebo mimo Azure. V rámci Azure můžete spustit sadu Azure Automation Runbook, funkci Azure nebo aplikaci logiky Azure. Příkladem adresy URL třetích stran mimo Azure jsou služby, jako je například časová rezerva a Twilio.

## <a name="autoscale-settings"></a>Nastavení automatického škálování
Automatické škálování používá následující terminologii a strukturu.

- **Nastavení automatického škálování** je čteno modulem automatického škálování, aby bylo možné určit, zda se má škálovat nahoru nebo dolů. Obsahuje jeden nebo více profilů, informace o cílovém prostředku a nastavení oznámení.

  - **Profil automatického škálování** je kombinací:

    - **Nastavení kapacity**, které označuje minimální, maximální a výchozí hodnoty pro počet instancí.
    - **sada pravidel**, z nichž každá zahrnuje Trigger (čas nebo metriku) a akci škálování (nahoru nebo dolů).
    - **opakování**, které indikuje, že by měl tento profil platit jako automatické škálování.

      Můžete mít několik profilů, které vám umožní pořídit různé překrývající se požadavky. Můžete mít různé profily automatického škálování pro různé denní dny nebo dny v týdnu, například.

  - **Nastavení oznámení** definuje, jaká oznámení se mají vyskytnout, když dojde k události automatického škálování na základě toho, že jsou splněna kritéria jednoho z profilů nastavení automatického škálování. Automatické škálování může upozorňovat na jednu nebo více e-mailových adres nebo volat na jeden nebo více webhooků.


![Nastavení, profil a struktura pravidel automatického škálování Azure](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

Úplný seznam konfigurovatelných polí a popisů je k dispozici v [REST API automatického škálování](/rest/api/monitor/autoscalesettings).

Příklady kódu naleznete v tématu.

* [Rozšířená konfigurace automatického škálování pomocí šablon Správce prostředků pro VM Scale Sets](autoscale-virtual-machine-scale-sets.md)  
* [REST API automatického škálování](/rest/api/monitor/autoscalesettings)

## <a name="horizontal-vs-vertical-scaling"></a>Vodorovné a svislé škálování
Automatické škálování se škáluje pouze vodorovně, což znamená zvýšení ("výstup") nebo snížení (v) v počtu instancí virtuálních počítačů.  Horizontální je větší flexibilita v cloudové situaci, protože umožňuje spuštění potenciálně tisíců virtuálních počítačů za účelem zpracování zatížení.

Naopak svislé škálování se liší. Udržuje stejný počet virtuálních počítačů, ale zpřístupňuje virtuální počítače více ("nahoru") nebo méně ("nižší"). Výkon se měří v paměti, rychlosti procesoru, místo na disku atd.  Vertikální škálování má větší omezení. Závisí na dostupnosti většího hardwaru, který rychle narazí na horní limit a může se lišit podle oblasti. Vertikální škálování také obvykle vyžaduje, aby se virtuální počítač zastavil a restartoval.

## <a name="methods-of-access"></a>Metody přístupu
Automatické škálování můžete nastavit přes

* [Azure Portal](autoscale-get-started.md)
* [PowerShell](../samples/powershell-samples.md#create-and-manage-autoscale-settings)
* [Rozhraní příkazového řádku (CLI) pro různé platformy](../samples/cli-samples.md#autoscale)
* [Rozhraní REST API služby Azure Monitor](/rest/api/monitor/autoscalesettings)

## <a name="supported-services-for-autoscale"></a>Podporované služby pro automatické škálování
| Služba | Schéma & docs |
| --- | --- |
| Web Apps |[Škálování Web Apps](autoscale-get-started.md) |
| Cloud Services |[Automatické škálování cloudové služby](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Virtual Machines: klasický |[Škálování skupin dostupnosti klasických virtuálních počítačů](/archive/blogs/kaevans/autoscaling-azurevirtual-machines) |
| Virtual Machines: Windows Scale Sets |[Škálování virtuálních počítačů s měřítkem ve Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Virtual Machines: Linux Scale Sets |[Škálování virtuálních počítačů na úrovni systému Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Virtual Machines: příklad Windows |[Rozšířená konfigurace automatického škálování pomocí šablon Správce prostředků pro VM Scale Sets](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md) |
| Azure App Service |[Horizontální navýšení kapacity aplikace v Azure App Service](../../app-service/manage-scale-up.md)|
| Služba API Management|[Automatické škálování instance služby Azure API Management](../../api-management/api-management-howto-autoscale.md)
| Clustery Azure Průzkumník dat|[Správa škálování clusterů Azure Průzkumník dat, aby se vešly měnící se požadavky](/azure/data-explorer/manage-cluster-horizontal-scaling)|
| Logic Apps |[Přidává se kapacita ISE (Integration Service Environment).](../../logic-apps/ise-manage-integration-service-environment.md#add-ise-capacity)|
| Spring Cloud |[Nastavení automatického škálování pro aplikace mikroslužeb](../../spring-cloud/spring-cloud-tutorial-setup-autoscale.md)|
| Service Bus |[Automaticky aktualizovat jednotky zasílání zpráv Azure Service Bus oboru názvů](../../service-bus-messaging/automate-update-messaging-units.md)|

## <a name="next-steps"></a>Další kroky
Další informace o automatickém škálování najdete v předchozích návodech k automatickému škálování nebo na těchto zdrojích informací:

* [Azure Monitor běžné metriky automatického škálování](autoscale-common-metrics.md)
* [Osvědčené postupy pro automatické škálování služby Azure Monitor](autoscale-best-practices.md)
* [Použití akcí automatického škálování k odesílání oznámení o výstrahách e-mailu a Webhooku](autoscale-webhook-email.md)
* [REST API automatického škálování](/rest/api/monitor/autoscalesettings)
* [Řešení potíží s Virtual Machine Scale Sets automatického škálování](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
