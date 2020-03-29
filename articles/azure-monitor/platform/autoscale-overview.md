---
title: Přehled automatického škálování ve virtuálních počítačích, cloudových službách a webových aplikacích
description: Automatické škálování v Microsoft Azure. Platí pro virtuální počítače, škálovací sady virtuálních počítačů, cloudové služby a webové aplikace.
ms.subservice: autoscale
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a60c03f1928b38c78a59edca4b5493307d7d19d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75364369"
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Přehled automatického škálování ve službách Microsoft Azure Virtual Machines, Cloud Services a Web Apps
Tento článek popisuje, co je automatické škálování Microsoft Azure, jeho výhody a jak začít používat.  

Automatické škálování Azure Monitor uplatní jenom [pro škálovací sady virtuálních strojů](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [cloudové služby](https://azure.microsoft.com/services/cloud-services/), [aplikační služby – webové aplikace](https://azure.microsoft.com/services/app-service/web/)a [služby správy rozhraní API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

> [!NOTE]
> Azure má dvě metody automatického škálování. Starší verze automatického škálování platí pro virtuální počítače (skupiny dostupnosti). Tato funkce má omezenou podporu a doporučujeme migrovat na škálovací sady virtuálních strojů pro rychlejší a spolehlivější podporu automatického škálování. Odkaz na použití starší technologie je součástí tohoto článku.  
>
>

## <a name="what-is-autoscale"></a>Co je automatické škálování?
Automatické škálování umožňuje mít správné množství prostředků spuštěných pro zpracování zatížení vaší aplikace. To vám umožní přidat prostředky pro zpracování zvýšení zatížení a také ušetřit peníze tím, že odstraní prostředky, které sedí nečinnosti. Zadáte minimální a maximální počet instancí, které mají být spuštěny a přidány nebo odebírat virtuální počítače automaticky na základě sady pravidel. Minimální zajišťuje, že vaše aplikace je vždy spuštěna i při žádném zatížení. Maximální limity jsou vaše celkové možné hodinové náklady. Mezi těmito dvěma extrémy můžete automaticky škálovat pomocí pravidel, která vytvoříte.

 ![Automatické škálování vysvětleno. Přidání a odebrání virtuálních počítačů](./media/autoscale-overview/AutoscaleConcept.png)

Pokud jsou splněny podmínky pravidla, spustí se jedna nebo více akcí automatického škálování. Můžete přidat a odebrat virtuální chod nebo provést jiné akce. Následující koncepční diagram ukazuje tento proces.  

 ![Vývojový diagram automatického škálování](./media/autoscale-overview/Autoscale_Overview_v4.png)

Následující vysvětlení platí pro části předchozího diagramu.   

## <a name="resource-metrics"></a>Metriky prostředků
Zdroje vyzařují metriky, tyto metriky jsou později zpracovány podle pravidel. Metriky přicházejí různými metodami.
Škálovací sady virtuálních strojů používají telemetrická data z diagnostických agentů Azure, zatímco telemetrie pro webové aplikace a cloudové služby pochází přímo z infrastruktury Azure. Některé běžně používané statistiky zahrnují využití procesoru, využití paměti, počty vláken, délku fronty a využití disku. Seznam telemetrických dat, která můžete použít, najdete [v tématu Automatické škálování běžných metrik](../../azure-monitor/platform/autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Vlastní metriky
Můžete také využít vlastní metriky, které vaše aplikace mohou být emitující. Pokud jste nakonfigurovali aplikace tak, aby odesílaly metriky do Application Insights, můžete tyto metriky využít k rozhodování o tom, zda je třeba škálovat nebo ne.

## <a name="time"></a>Time
Pravidla založená na plánu jsou založena na utc. Při nastavování pravidel je nutné správně nastavit časové pásmo.  

## <a name="rules"></a>Pravidla
Diagram zobrazuje pouze jedno pravidlo automatického škálování, ale mnoho z nich můžete mít. Můžete vytvořit komplexní překrývající se pravidla podle potřeby pro vaši situaci.  Typy pravidel zahrnují  

* **Na základě metrik** – například tuto akci proveďte, když je využití procesoru vyšší než 50 %.
* **Časově založené** - Například spoušť webhooku každý 8 hodin v sobotu v daném časovém pásmu.

Pravidla založená na metrikách měří zatížení aplikace a přidávají nebo odeberou virtuální počítače na základě tohoto zatížení. Pravidla založená na plánu umožňují škálovat, když vidíte časové vzory v zatížení a chcete škálovat dříve, než dojde k možnému zvýšení nebo snížení zatížení.  

## <a name="actions-and-automation"></a>Akce a automatizace
Pravidla mohou aktivovat jeden nebo více typů akcí.

* **Škálování** – škálování virtuálních mích dovnitř nebo ven
* **E-mail** – odeslání e-mailu správcům předplatného, spolusprávcům a/nebo další e-mailové adrese, kterou zadáte
* **Automatizujte pomocí webhooků** – volejte webhooky, které můžou vyvolat několik složitých akcí uvnitř nebo vně Azure. V Azure můžete spustit runbook Azure Automation, azure funkci nebo aplikaci Azure Logic. Mezi adresy URL třetích stran mimo Azure patří služby jako Slack a Twilio.

## <a name="autoscale-settings"></a>Nastavení automatického škálování
Automatické škálování používá následující terminologii a strukturu.

- **Nastavení automatického škálování** čte modul automatického škálování a určuje, zda má být vertikálně navýšit nebo snížit kapacitu. Obsahuje jeden nebo více profilů, informace o cílovém prostředku a nastavení oznámení.

  - **Profil automatického škálování** je kombinací:

    - **nastavení kapacity**, které označuje minimální, maximální a výchozí hodnoty pro počet instancí.
    - **sada pravidel**, z nichž každá obsahuje aktivační událost (čas nebo metriku) a akci škálování (nahoru nebo dolů).
    - **opakování**, což znamená, kdy by měl automatický škálování tento profil uvést v platnost.

      Můžete mít více profilů, které vám umožní postarat se o různé překrývající se požadavky. Můžete mít různé profily automatického škálování pro různé denní doby nebo dny v týdnu, například.

  - **Nastavení oznámení** definuje, jaká oznámení by měla nastat, když dojde k události automatického škálování na základě splnění kritérií jednoho z profilů nastavení automatického škálování. Automatické škálování může upozornit jednu nebo více e-mailových adres nebo volat na jeden nebo více webhooků.


![Nastavení automatického škálování Azure, profil a struktura pravidel](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

Úplný seznam konfigurovatelných polí a popisů je k dispozici v [rozhraní REST API automatického škálování](https://msdn.microsoft.com/library/dn931928.aspx).

Příklady kódu naleznete v tématu

* [Pokročilá konfigurace automatického škálování pomocí šablon Správce prostředků pro škálovací sady virtuálních počítačů](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md)  
* [Automatické škálování rozhraní REST API](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Vodorovné vs svislé měřítko
Automatické škálování pouze škálování vodorovně, což je zvýšení ("out") nebo snížení ("v") v počtu instancí virtuálních zařízení.  Horizontální je flexibilnější v cloudové situaci, protože umožňuje spustit potenciálně tisíce virtuálních počítačů pro zpracování zatížení.

Naproti tomu vertikální škálování se liší. Udržuje stejný počet virtuálních zařízení, ale virtuální chody jsou silnější ("nahoru") nebo méně (dolů). Výkon se měří v paměti, rychlost procesoru, místo na disku, atd.  Vertikální škálování má další omezení. Je závislá na dostupnosti většího hardwaru, který rychle dosáhne horního limitu a může se lišit podle oblasti. Vertikální škálování také obvykle vyžaduje virtuální počítač zastavit a restartovat.


## <a name="methods-of-access"></a>Metody přístupu
Automatické škálování můžete nastavit pomocí

* [Portál Azure](../../azure-monitor/platform/autoscale-get-started.md)
* [PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)
* [Rozhraní příkazového řádku (CLI) pro různé platformy](../../azure-monitor/platform/cli-samples.md#autoscale)
* [Rozhraní REST API služby Azure Monitor](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Podporované služby pro automatické škálování
| Služba | Dokumenty schématu & |
| --- | --- |
| Web Apps |[Změna velikosti webových aplikací](../../azure-monitor/platform/autoscale-get-started.md) |
| Cloud Services |[Automatické škálování cloudové služby](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Virtuální stroje: Classic |[Škálování klasických sad dostupnosti virtuálních strojů](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Virtuální počítače: Škálovací sady Windows |[Škálování škálovacích sad virtuálních strojů ve Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Virtuální počítače: Linuxové škálovací sady |[Škálování škálovacích sad virtuálních strojů v Linuxu](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Virtuální počítače: Příklad Windows |[Pokročilá konfigurace automatického škálování pomocí šablon Správce prostředků pro škálovací sady virtuálních počítačů](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md) |
| Služba api management|[Automatické škálování instance služby Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-autoscale)

## <a name="next-steps"></a>Další kroky
Chcete-li získat další informace o automatickém škálování, použijte dříve uvedené návody k automatickému škálování nebo se podívejte na následující zdroje:

* [Běžné metriky automatického škálování Azure Monitoru](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Osvědčené postupy pro automatické škálování služby Azure Monitor](../../azure-monitor/platform/autoscale-best-practices.md)
* [Použití akcí automatického škálování k odesílání e-mailů a upozornění webhooku](../../azure-monitor/platform/autoscale-webhook-email.md)
* [Automatické škálování rozhraní REST API](https://msdn.microsoft.com/library/dn931953.aspx)
* [Automatické škálování škálovacích sad virtuálních strojů](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

