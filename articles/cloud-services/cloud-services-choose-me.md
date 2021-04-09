---
title: Co je Azure Cloud Services (Classic) | Microsoft Docs
description: Přečtěte si, co je Azure Cloud Services, konkrétně je navržená tak, aby podporovala aplikace, které jsou škálovatelné, spolehlivé a nenákladné pro provoz.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: cbb9aae57b952c05aa722f81309158a11aef826b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742807"
---
# <a name="overview-of-azure-cloud-services-classic"></a>Přehled služby Azure Cloud Services (Classic)

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

Azure Cloud Services je příkladem [platformy jako služby](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Stejně jako [Azure App Service](../app-service/overview.md)je tato technologie navržena tak, aby podporovala aplikace, které jsou škálovatelné, spolehlivé a nenákladné pro provoz. Stejným způsobem, jako hostuje App Service na virtuálních počítačích, je to také Cloud Services Azure. Máte ale větší kontrolu nad virtuálními počítači. Na virtuální počítače, které používají Azure Cloud Services, můžete nainstalovat vlastní software a k nim můžete přistupovat vzdáleně.

![Diagram Cloud Services Azure](./media/cloud-services-choose-me/diagram.png)

Další ovládací prvky také znamenají méně snadné použití. Pokud nepotřebujete další možnosti ovládacího prvku, je obvykle rychlejší a jednodušší získat webovou aplikaci v Web Apps funkce App Service v porovnání se službou Azure Cloud Services.

Existují dva typy rolí Azure Cloud Services. Jediným rozdílem mezi těmito dvěma způsoby je to, jak je vaše role hostovaná na virtuálních počítačích:

* **Webová role**: automaticky nasadí a hostuje vaši aplikaci prostřednictvím služby IIS.

* **Role pracovního procesu**: nepoužívá službu IIS a spouští samostatnou aplikaci.

Například jednoduchá aplikace může používat jenom jednu webovou roli, která obsluhuje Web. Složitější aplikace může použít webovou roli ke zpracování příchozích požadavků od uživatelů a pak tyto požadavky předat do role pracovního procesu ke zpracování. (Tato komunikace může používat [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) nebo [Azure Queue Storage](../storage/common/storage-introduction.md).)

Jak ukazuje předchozí obrázek, všechny virtuální počítače v jedné aplikaci běží ve stejné cloudové službě. Uživatelé přistupují k aplikaci prostřednictvím jedné veřejné IP adresy, přičemž požadavky se automaticky vyrovnávají napříč virtuálními počítači aplikace. Platforma se [škáluje a nasadí](cloud-services-how-to-scale-portal.md) virtuální počítače v aplikaci Azure Cloud Services tak, aby nedocházelo k jednomu bodu selhání hardwaru.

I když aplikace běží na virtuálních počítačích, je důležité pochopit, že Azure Cloud Services poskytuje PaaS, ne infrastrukturu jako službu (IaaS). Tady je jeden ze způsobů, jak si ho představit. V IaaS, jako je Azure Virtual Machines, nejprve vytvoříte a nakonfigurujete prostředí, ve kterém běží aplikace. Pak nasadíte aplikaci do tohoto prostředí. Zodpovídáte za správu mnoha z těchto světových věcí, jako je například nasazení nových opravených verzí operačního systému v každém virtuálním počítači. V PaaS naproti tomu je to, jako kdyby prostředí již existovalo. Stačí provést nasazení aplikace. Správa platformy, na které běží, včetně nasazení nových verzí operačního systému, je zpracována za vás.

## <a name="scaling-and-management"></a>Škálování a Správa
S Azure Cloud Services nevytváříte virtuální počítače. Místo toho zadáte konfigurační soubor, který oznamuje, kolik každého z nich chcete, například "tři instance webové role" a "dvě instance role pracovního procesu". Platforma je pak vytvoří za vás. Pořád si zvolíte, [jakou velikost](cloud-services-sizes-specs.md) záložních virtuálních počítačů by měly být, ale nebudete je explicitně vytvářet sami. Pokud vaše aplikace potřebuje zvládnout větší zatížení, můžete požádat o další virtuální počítače a Azure tyto instance vytvoří. Pokud se zatížení sníží, můžete tyto instance vypnout a začlenit jejich platby.

Aplikace Azure Cloud Services je obvykle k dispozici uživatelům prostřednictvím procesu se dvěma kroky. Vývojář nejdřív [nahraje aplikaci](cloud-services-how-to-create-deploy-portal.md) do pracovní oblasti platformy. Když je vývojář připravený k zajištění provozu aplikace, používá Azure Portal k prohození pracovní části s produkčním prostředím. Tento [přepínač mezi příchodem a výrobou](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) se dá udělat bez výpadků, což umožňuje upgradování běžící aplikace na novou verzi bez narušení jejich uživatelů.

## <a name="monitoring"></a>Monitorování
Azure Cloud Services také nabízí monitorování. Stejně jako Virtual Machines detekuje neúspěšný fyzický server a restartuje virtuální počítače, které na tomto serveru běžely na novém počítači. Ale Azure Cloud Services také detekuje neúspěšné virtuální počítače a aplikace, nikoli jenom selhání hardwaru. Na rozdíl od Virtual Machines má agent uvnitř každé webové a pracovní role, takže při selhání může začít nové virtuální počítače a instance aplikace.

PaaS povaha Azure Cloud Services má i jiné důsledky. Jedním z nejdůležitějších z nich je, že aplikace postavené na této technologii by se měly zapsat ke správnému spuštění, když dojde k chybě jakékoli instance webové nebo pracovní role. Aby to bylo možné dosáhnout, aplikace Cloud Services Azure by neměla udržovat stav v systému souborů svých vlastních virtuálních počítačů. Na rozdíl od virtuálních počítačů vytvořených pomocí Virtual Machines nejsou zápisy do virtuálních počítačů Azure Cloud Services trvalé. K dispozici není žádný podobný Virtual Machines datový disk. Místo toho by měla aplikace Cloud Services v Azure explicitně zapisovat všechny stavy do Azure SQL Database, objektů blob, tabulek nebo některých jiných externích úložišť. Sestavování aplikací tímto způsobem usnadňuje škálování a odolnější selhání, což jsou důležité cíle Azure Cloud Services.

## <a name="next-steps"></a>Další kroky
* [Vytvoření aplikace cloudové služby v .NET](cloud-services-dotnet-get-started.md) 
* [Vytvoření aplikace cloudové služby v Node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Vytvoření aplikace cloudové služby v PHP](../cloud-services-php-create-web-role.md) 
* [Vytvoření aplikace cloudové služby v Pythonu](cloud-services-python-ptvs.md)






