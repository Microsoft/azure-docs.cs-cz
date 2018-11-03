---
title: Přehled automatického škálování virtuálních počítačů, cloudových služeb a webových aplikací
description: Automatické škálování v Microsoft Azure. Platí pro virtuální počítače, Škálovací sady virtuálních počítačů, cloudových služeb a webových aplikací.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: robb
ms.component: autoscale
ms.openlocfilehash: 7a3083bd757a7aa5625799724d601f99fd7e2b14
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50961292"
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Přehled automatického škálování v Microsoft Azure Virtual Machines, Cloud Services a webových aplikací
Tento článek popisuje, co Microsoft Azure při automatickém škálování je, jeho výhody a jak začít používat.  

Automatické škálování služby Azure Monitor se týká pouze [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service – Web Apps](https://azure.microsoft.com/services/app-service/web/), a [služby API Management](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

> [!NOTE]
> Azure nabízí dvě metody automatického škálování. Starší verzi automatického škálování se vztahuje na virtuální počítače (skupiny dostupnosti). Tato funkce má omezenou podporu a doporučujeme migrovat na škálovací sady virtuálních počítačů pro podporu automatického škálování rychlejší a spolehlivější. Odkaz na tom, jak používat starší technologie je zahrnuta v tomto článku.  
>
>

## <a name="what-is-autoscale"></a>Co je automatické škálování?
Automatické škálování umožňuje mít správného množství prostředků systémem pro zpracování zátěže ve své aplikaci. Umožňuje přidat prostředky do zpracovat nárůst zatížení a také prostředky, které jsou sedí ušetříte peníze nečinnosti. Můžete zadat minimální a maximální počet instancí ke spuštění a přidat nebo odebrat virtuální počítače automaticky na základě sady pravidel. S minimální díky že vaše aplikace vždycky běží i bez zatížení. S maximální omezuje vaše možné celkové hodinové náklady. Automatické škálování mezi těmito dvěma extrémy pomocí pravidel, které vytvoříte.

 ![Vysvětlení automatického škálování. Přidání a odebrání virtuálních počítačů](./media/monitoring-overview-autoscale/AutoscaleConcept.png)

Pokud se splní podmínky pravidel, se aktivují jednu nebo více akcí automatického škálování. Můžete přidat a odebrat virtuální počítače nebo provádět jiné akce. Následující koncepční diagram znázorňuje tento proces.  

 ![Vývojový Diagram automatického škálování](./media/monitoring-overview-autoscale/Autoscale_Overview_v4.png)

Vysvětlení níže se vztahuje na, které byly na předchozím obrázku.   

## <a name="resource-metrics"></a>Metriky prostředků
Prostředky generovat metriky, tyto metriky jsou zpracovány později pravidla. Metriky jsou prostřednictvím různých metod.
Škálovací sady virtuálních počítačů použít telemetrická data diagnostiky Azure agenty, zatímco telemetrie pro webové aplikace a cloudové služby pochází přímo z infrastruktury Azure. Některé běžně používané statistiky zahrnovat využití CPU, využití paměti, počtu vláken, délka fronty a využití disku. Seznam jaké telemetrická data, můžete použít, najdete v části [běžné metriky automatického škálování](insights-autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Vlastní metriky
Můžete taky využít vlastní vlastní metriky, které vaše aplikace může výstupu. Pokud jste nakonfigurovali vaší aplikace k odeslání metrik do Application Insights můžete využít tyto metriky pro rozhodování na, jestli se má změnit velikost nebo ne.

## <a name="time"></a>Čas
Pravidla podle plánu jsou založeny na standardu UTC. Při nastavování pravidel musí správně nastavené časové pásmo.  

## <a name="rules"></a>Pravidla
Diagram znázorňuje pouze jedno pravidlo automatického škálování, ale může mít mnoho z nich. Můžete vytvořit komplexní překrývající se pravidla, podle potřeby pro konkrétní situaci.  Zahrnují typy pravidel  

* **Na základě metrik** – například tuto akci proveďte, pokud využití CPU je vyšší než 50 %.
* **Podle času** – například aktivační událost webhooku každých 8: 00 v sobotu v daném časovém pásmu.

Pravidla podle metriky měření zatížení aplikace a přidat nebo odebrat virtuálních počítačů založených na zátěž. Pravidla podle plánu bylo možné vertikálně až vidět času zatížení a chcete vertikálně navýšit před možné zatížení zvýšení nebo snížení dojde.  

## <a name="actions-and-automation"></a>Akce a automatizace
Pravidla může aktivovat jeden nebo více typů akcí.

* **Škálování** – škálování virtuálních počítačů, snížení nebo navýšení kapacity
* **E-mailu** – poslat e-mail správci předplatného, spolusprávci a/nebo další e-mailovou adresu
* **Automatizace prostřednictvím webhooků** -volání webhooků, které může aktivovat více akcí komplexní uvnitř nebo mimo Azure. V Azure můžete spustit runbook Azure Automation, funkce Azure Functions nebo aplikace logiky Azure. Příklad adresy URL třetích stran mimo Azure zahrnují služby, jako je Slack a Twilio.

## <a name="autoscale-settings"></a>Nastavení automatického škálování
Automatické škálování použít následující terminologie a struktura.

- **Nastavení automatického škálování** přečte modul automatického škálování k určení, jestli chcete vertikálně navýšit nebo snížit kapacitu. Obsahuje jeden nebo více profilů, informace o cílový prostředek a nastavení oznámení.

    - **Profil automatického škálování** je kombinací a:

        - **Nastavení kapacity**, která určuje minimální, maximální a výchozí hodnoty pro počet instancí.
        - **Sada pravidel**, z nichž každý obsahuje aktivační událost (čas nebo metriky) a akce škálování (nahoru nebo dolů).
        - **opakování**, což znamená, že při vložení tohoto profilu platit automatického škálování.

        Můžete mít více profilů, které umožňují postará o jinou překrývající se požadavky. Profily automatického škálování různých pro různou den nebo dny v týdnu, můžete mít třeba.

    - A **nastavení oznámení** definuje, jaká oznámení se budou objevovat při výskytu události automatického škálování podle toho, které splňují kritéria jeden z profilů nastavení automatického škálování. Automatické škálování může upozornit jeden nebo více e-mailové adresy nebo volat minimálně jeden webhook.


![Zadané nastavení automatického škálování Azure, profil a pravidla struktury](./media/monitoring-overview-autoscale/AzureResourceManagerRuleStructure3.png)

Úplný seznam konfigurovatelných pole a popisy je k dispozici v [rozhraní REST API pro automatické škálování](https://msdn.microsoft.com/library/dn931928.aspx).

Příklady kódu naleznete v tématu

* [Pokročilá konfigurace automatického škálování pro Škálovací sady virtuálních počítačů pomocí šablon Resource Manageru](insights-advanced-autoscale-virtual-machine-scale-sets.md)  
* [Rozhraní REST API pro automatické škálování](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Vertikální škálování vodorovné vs
Automatické škálování pouze škáluje vodorovně, což je zvýšení ("out") nebo snížení ("") v počtu instancí virtuálních počítačů.  Vodorovné je flexibilnější v situaci, cloud jako umožňuje potenciálně spouštíme tisíce virtuálních počítačů pro zpracování zátěže.

Naproti tomu vertikální škálování se liší. Udržuje stejný počet virtuálních počítačů, ale budou virtuální počítače ("nahoru") méně nebo více ("výpadek") výkonné. Měřený v paměti, rychlost procesoru, místo na disku atd.  Vertikální škálování má další omezení. Záleží na dostupnosti větší hardwaru, která rychle narazí na horní mez a jednotlivých oblastech můžou lišit. Vertikální škálování obvykle vyžaduje virtuální počítač zastavit a restartovat.

Další informace najdete v tématu [vertikální škálování virtuálních počítačů Azure s využitím Azure Automation](../virtual-machines/linux/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="methods-of-access"></a>Metody přístupu
Můžete nastavit automatické škálování prostřednictvím

* [Azure Portal](monitoring-autoscale-get-started.md)
* [PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)
* [Rozhraní příkazového řádku (CLI) pro různé platformy](insights-cli-samples.md#autoscale)
* [Rozhraní REST API služby Azure Monitor](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Podporované služby pro automatické škálování
| Služba | Schéma a dokumentace |
| --- | --- |
| Web Apps |[Škálování webové aplikace](monitoring-autoscale-get-started.md) |
| Cloud Services |[Automatické škálování cloudové služby](../cloud-services/cloud-services-how-to-scale-portal.md) |
| Virtual Machines: Classic |[Škálovací sady dostupnosti klasický virtuální počítač](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Virtuálních počítačů: Škálovací sady Windows |[Škálování virtuálního počítače škálovací sady v Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-powershell.md) |
| Virtuálních počítačů: Linux škálovacích sad |[Škálování virtuálního počítače škálovací sady v Linuxu](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-cli.md) |
| Virtual Machines: Příklad Windows |[Pokročilá konfigurace automatického škálování pro Škálovací sady virtuálních počítačů pomocí šablon Resource Manageru](insights-advanced-autoscale-virtual-machine-scale-sets.md) |
| Služba API Management|[Automatické škálování instance služby Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-autoscale)

## <a name="next-steps"></a>Další postup
Další informace o automatické škálování, použití automatického škálování návody výše uvedenými nebo odkazovat na následujících odkazech:

* [Azure Monitor běžné metriky automatického škálování](insights-autoscale-common-metrics.md)
* [Osvědčené postupy pro automatické škálování služby Azure Monitor](insights-autoscale-best-practices.md)
* [Pomocí akcí automatického škálování můžete odeslat emailová a webhooková oznámení výstrah](insights-autoscale-to-webhook-email.md)
* [Rozhraní REST API pro automatické škálování](https://msdn.microsoft.com/library/dn931953.aspx)
* [Řešení potíží virtuální počítač automatické škálování Škálovací sady](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
