---
title: Přehled automatického škálování virtuálních počítačů, cloudové služby a webové aplikace
description: Škálování v Microsoft Azure. Platí pro virtuální počítače, virtuální počítač sadách škálování, cloudové služby a webové aplikace.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/02/2016
ms.author: robb
ms.component: autoscale
ms.openlocfilehash: 4eeca81e08a0ecae9ba41ccdd2bf8a2f395f579c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264666"
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Přehled automatického škálování ve virtuálních počítačích Microsoft Azure, cloudové služby a webové aplikace
Tento článek popisuje, jaké škálování Microsoft Azure je, její výhody a jak začít používat.  

Azure monitorování škálování se vztahují pouze na [sady škálování virtuálního počítače](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [cloudové služby](https://azure.microsoft.com/services/cloud-services/), a [služby App Service – webové aplikace](https://azure.microsoft.com/services/app-service/web/).

> [!NOTE]
> Azure má dvě metody automatického škálování. Starší verze škálování platí pro virtuální počítače (skupiny dostupnosti). Tato funkce má omezenou podporu a doporučujeme migraci do sady škálování virtuálního počítače pro podporu automatického škálování rychlejší a spolehlivější. Odkaz na tom, jak pomocí technologie starší je obsažena v tomto článku.  
>
>

## <a name="what-is-autoscale"></a>Co je automatické škálování?
Škálování umožňuje mít správného množství prostředků, které jsou spuštěné zvládání zatížení ve vaší aplikaci. Umožňuje přidat prostředky pro zpracování nárůst zatížení a také ušetřit peníze odebráním prostředky, které jsou uložený nečinnosti. Můžete zadat minimální a maximální počet instancí spustit a přidat nebo odebrat virtuální počítače automaticky na základě sady pravidel. S minimální díky, že aplikace je vždy spuštěna ani v žádné zatížení. S maximální omezuje vaše náklady možné každou hodinu. Automatické škálování mezi těmito dvěma hranicemi pomocí pravidel, které vytvoříte.

 ![Škálování vysvětlené. Přidání a odebrání virtuálních počítačů](./media/monitoring-overview-autoscale/AutoscaleConcept.png)

Pokud jsou splněny podmínky pravidla, vyvolají se jeden nebo víc akcí škálování. Můžete přidat a odebrat virtuální počítače nebo provádět další akce. Následující koncepční diagram znázorňuje tento proces.  

 ![Vývojový Diagram škálování](./media/monitoring-overview-autoscale/Autoscale_Overview_v4.png)

Vysvětlení níže se vztahuje na údaje předchozímu diagramu.   

## <a name="resource-metrics"></a>Metrika prostředků
Emitování metriky prostředky, tyto metriky později zpracovává pravidla. Metriky pocházet pomocí různých metod.
Sady škálování virtuálního počítače pomocí telemetrická data z Azure diagnostics agentů, zatímco telemetrie pro webové aplikace a cloudové služby pochází přímo z infrastruktury Azure. Běžně používané statistikami zahrnují využití procesoru, využití paměti, počet vláken, délka fronty a využití disku. Seznam co telemetrická data, můžete použít, naleznete v části [běžné metriky automatického škálování](insights-autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Vlastní metriky
Můžete využít i vlastní vlastní metriky, které vaše aplikace může být generování. Pokud jste nakonfigurovali vaše aplikace k odeslání metriky do Application Insights můžete využít tyto metriky při rozhodování na tom, zda škálování nebo ne. 

## <a name="time"></a>Čas
Pravidla na základě plánu jsou založené na UTC. Časové pásmo je nutné nastavit správně při nastavování pravidel.  

## <a name="rules"></a>Pravidla
Diagram zobrazuje pouze jedno pravidlo automatického škálování, ale může mít mnoho z nich. Můžete vytvořit komplexní překrývající se pravidla, podle potřeby pro vaši situaci.  Zahrnout typy pravidel  

* **Na základě metrika** – například tuto akci proveďte, pokud využití CPU je vyšší než 50 %.
* **Založené na čase** – například aktivační události webhooku každých 8: 00 na sobotu v daném časovém pásmu.

Na základě metrika pravidla měření zatížení aplikace a přidat nebo odebrat podle tohoto zatížení virtuálních počítačů. Na základě plánu pravidla umožňují škálování při najdete v části vzory čas v zatížení a chcete škálovat před možné zatížení zvýšení nebo snížení.  

## <a name="actions-and-automation"></a>Akce a automatizace
Pravidla můžete aktivovat jeden nebo více typů akcí.

* **Škálování** -škálování virtuálních počítačů příchozí nebo odchozí
* **E-mailu** – poslat správci předplatného, spolusprávci nebo další e-mailovou adresu, zadáte e-mailu
* **Automatizovat pomocí webhooků** -volání webhooků, které můžete aktivovat více komplexní akcí uvnitř nebo mimo Azure. Uvnitř Azure můžete spustit runbook automatizace Azure, funkce Azure nebo Azure Logic Apps. Příklad adresy URL třetích stran mimo Azure zahrnují služby, jako je Slacku a Twilio.

## <a name="autoscale-settings"></a>Nastavení automatického škálování
Škálování použijte následující terminologie a struktura.

- **Nastavení automatického škálování** je pro čtení pomocí modulu škálování a určí, jestli se škálovat nahoru nebo dolů. Obsahuje jeden nebo více profilů, informace o cílový prostředek a nastavení oznámení.

    - **Škálování profil** je kombinací a:

        - **Nastavení kapacity**, která určuje minimální, maximální a výchozí hodnoty pro počet instancí.
        - **Sada pravidel**, z nichž každý obsahuje aktivační událost (čas nebo metrika) a akce škálování (nahoru nebo dolů).
        - **opakování**, což naznačuje, když put tento profil platit škálování.

        Může mít několik profilů, které umožňují postará o různé překrývající se požadavky. Různé škálování profilů pro různé časy den nebo dny v týdnu, může mít například.

    - A **nastavení oznámení** definuje, k jaké oznámení by mělo dojít, když dojde k události škálování podle které splňují kritéria jeden z profilů nastavení automatického škálování. Škálování můžete oznámit jeden nebo více e-mailové adresy nebo volat minimálně jeden webhook.


![Nastavení automatického škálování Azure, profil a strukturu pravidla](./media/monitoring-overview-autoscale/AzureResourceManagerRuleStructure3.png)

Úplný seznam konfigurovat polí a popisy je k dispozici v [škálování REST API](https://msdn.microsoft.com/library/dn931928.aspx).

Příklady kódu najdete v tématu

* [Upřesňující konfigurace automatického škálování pro škálovatelné sady virtuálních počítačů pomocí šablony Resource Manageru](insights-advanced-autoscale-virtual-machine-scale-sets.md)  
* [Škálování REST API](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Svislé škálování vodorovné vs
Škálování pouze škáluje vodorovně, což je zvýšení ("na") nebo zmenšit ("v") v počtu instancí virtuálních počítačů.  Vodorovný je flexibilnější v situaci, cloud jako umožňuje spustit potenciálně tisíce virtuálních počítačů pro zpracování zátěže.

Naproti tomu svislé škálování se liší. Zachová stejný počet virtuálních počítačů, ale další ("nahoru") nebo méně ("dolů") výkonné díky virtuálních počítačů. Výkon se měří v paměť, rychlost procesoru, místo na disku atd.  Svislé škálování má další omezení. Je závislá na dostupnosti větší hardware, který rychle dotkne horní limit a můžete se liší podle oblasti. Svislé škálování také obvykle vyžaduje virtuální počítač zastavit a restartovat.

Další informace najdete v tématu [svisle škálování virtuální počítač Azure s Azure Automation](../virtual-machines/linux/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="methods-of-access"></a>Metody přístupu
Můžete nastavit automatické škálování prostřednictvím

* [Azure Portal](insights-how-to-scale.md)
* [PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)
* [Napříč platformami rozhraní příkazového řádku (CLI)](insights-cli-samples.md#autoscale)
* [Rozhraní API REST Azure monitorování](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Podporované služby pro škálování
| Služba | Schéma & dokumentace |
| --- | --- |
| Web Apps |[Škálování webové aplikace](insights-how-to-scale.md) |
| Cloud Services |[Škálování cloudové služby](../cloud-services/cloud-services-how-to-scale-portal.md) |
| Virtuální počítače: Classic |[Škálování sady dostupnosti Classic virtuálního počítače](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Virtuálních počítačů: Sady škálování Windows |[Škálování virtuálního počítače nastaví v systému Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md) |
| Virtuálních počítačů: Nastaví Linux škálování |[Škálování virtuálního počítače nastaví v systému Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) |
| Virtuálních počítačů: Příklad Windows |[Upřesňující konfigurace automatického škálování pro škálovatelné sady virtuálních počítačů pomocí šablony Resource Manageru](insights-advanced-autoscale-virtual-machine-scale-sets.md) |

## <a name="next-steps"></a>Další postup
Další informace o škálování, použijte postupy škálování uvedených výše nebo naleznete v následujících materiálech:

* [Azure monitorování běžné metriky automatického škálování](insights-autoscale-common-metrics.md)
* [Osvědčené postupy pro monitorování Azure škálování](insights-autoscale-best-practices.md)
* [Akce škálování používat k odesílání e-mailu a webhooku oznámení výstrah](insights-autoscale-to-webhook-email.md)
* [Škálování REST API](https://msdn.microsoft.com/library/dn931953.aspx)
* [Řešení potíží škálování sady škálování virtuálního počítače](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
