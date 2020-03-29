---
title: Co jsou cloudové služby Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, co je Cloudové služby Azure.
services: cloud-services
author: tgore03
ms.service: multiple
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: c531e02656c9f6342670024b2220386e789a2d98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386846"
---
# <a name="overview-of-azure-cloud-services"></a>Přehled cloudových služeb Azure
Azure Cloud Services je příkladem [platformy jako služby](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Stejně jako [Azure App Service](../app-service/overview.md)je tato technologie navržena tak, aby podporovala aplikace, které jsou škálovatelné, spolehlivé a levné pro provoz. Stejným způsobem, že služba App Service je hostované na virtuálních počítačích (VMs), tak je Azure Cloud Services. Máte však větší kontrolu nad virtuálními virtuálními ms. Můžete nainstalovat vlastní software na virtuální počítače, které používají Azure Cloud Services a můžete k nim přistupovat vzdáleně.

![Diagram cloudových služeb Azure](./media/cloud-services-choose-me/diagram.png)

Větší kontrola také znamená méně snadnépoužití. Pokud nepotřebujete další možnosti řízení, je obvykle rychlejší a jednodušší zprovoznit webovou aplikaci ve funkci Webové aplikace služby App Service ve srovnání s CloudServices Azure.

Existují dva typy rolí Cloudových služeb Azure. Jediný rozdíl mezi těmito dvěma je, jak je vaše role hostované na virtuálních počítačích:

* **Webová role**: Automaticky nasazuje a hostuje vaši aplikaci prostřednictvím služby IIS.

* **Role pracovního procesu**: Nepoužívá službu IIS a spouští samostatnou aplikaci.

Jednoduchá aplikace může například používat pouze jednu webovou roli, která slouží webu. Složitější aplikace může použít webovou roli ke zpracování příchozích požadavků od uživatelů a pak předat tyto požadavky na roli pracovníka pro zpracování. (Tato komunikace může používat [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) nebo úložiště fronty [Azure](../storage/common/storage-introduction.md).)

Jak naznačuje předchozí obrázek, všechny virtuální aplikace v jedné aplikaci běží ve stejné cloudové službě. Uživatelé přistupují k aplikaci prostřednictvím jedné veřejné IP adresy, přičemž požadavky se automaticky načítají napříč virtuálními počítačůmi aplikace. Platforma [škáluje a nasazuje](cloud-services-how-to-scale-portal.md) virtuální počítače v aplikaci Cloudservices Azure způsobem, který zabrání selhání hardwaru v jednom bodě.

I když aplikace běží ve virtuálních počítačích, je důležité si uvědomit, že Azure Cloud Services poskytuje PaaS, ne infrastrukturu jako službu (IaaS). Tady je jeden způsob, jak o tom přemýšlet. S IaaS, jako jsou virtuální počítače Azure, nejprve vytvořit a nakonfigurovat prostředí, ve kterých vaše aplikace běží. Potom nasadíte aplikaci do tohoto prostředí. Jste zodpovědní za správu velké části tohoto světa tím, že děláte věci, jako je nasazení nových opravených verzí operačního systému v každém virtuálním virtuálním provozu. V PaaS, naopak, je to, jako by prostředí již existuje. Jediné, co musíte udělat, je nasadit aplikaci. Správa platformy, na které běží, včetně nasazení nových verzí operačního systému, je zpracována za vás.

## <a name="scaling-and-management"></a>Škálování a správa
S Azure Cloud Services nevytváříte virtuální počítače. Místo toho zadáte konfigurační soubor, který azure sdělí, kolik z nich chcete, například "tři instance webové role" a "dvě instance role pracovního procesu". Platforma je pak vytváří pro vás. Stále si [vyberete, jakou velikost](cloud-services-sizes-specs.md) by měly mít tyto záložní virtuální počítače, ale sami je explicitně nevytvoříte. Pokud vaše aplikace potřebuje zpracovat větší zatížení, můžete požádat o další virtuální počítače a Azure vytvoří tyto instance. Pokud se zatížení sníží, můžete tyto instance vypnout a přestat za ně platit.

Aplikace Cloudových služeb Azure se obvykle zpřístupní uživatelům prostřednictvím dvoustupňového procesu. Vývojář [nejprve nahraje aplikaci](cloud-services-how-to-create-deploy-portal.md) do pracovní oblasti platformy. Když je vývojář připravený k tomu, aby aplikace byla aktivní, použije portál Azure k odhození pracovní s produkčním prostředím. Tento [přepínač mezi pracovní a produkční](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) lze provést bez prostojů, což umožňuje spuštění aplikace upgradovat na novou verzi bez narušení jeho uživatelů.

## <a name="monitoring"></a>Monitorování
Azure Cloud Services také poskytuje monitorování. Stejně jako virtuální počítače detekuje neúspěšný fyzický server a restartuje virtuální počítače, které byly spuštěny na tomto serveru v novém počítači. Azure Cloud Services ale také detekuje selhání virtuálních počítačů a aplikací, nejen selhání hardwaru. Na rozdíl od virtuálních počítačů má agenta uvnitř každé role webu a pracovního procesu, a proto je možné spustit nové virtuální počítače a instance aplikací, když dojde k selhání.

PaaS povaha Cloudových služeb Azure má také další důsledky. Jedním z nejdůležitějších je, že aplikace postavené na této technologii by měly být zapsány tak, aby správně běžely, když se nezdaří jakákoli instance role webu nebo pracovního procesu. K dosažení tohoto cíle by aplikace Cloudservices Azure neměla udržovat stav v systému souborů vlastních virtuálních počítačích. Na rozdíl od virtuálních počítačů vytvořených pomocí virtuálních počítačů nejsou zápisy provedené do virtuálních počítačů Cloudových služeb Azure trvalé. Neexistuje nic jako datový disk virtuálních počítačů. Místo toho by aplikace Cloudových služeb Azure měla explicitně zapsat veškerý stav do Azure SQL Database, objektů BLOB, tabulek nebo jiného externího úložiště. Vytváření aplikací tímto způsobem usnadňuje škálování a odolnější vůči selhání, což jsou oba důležité cíle Cloudových služeb Azure.

## <a name="next-steps"></a>Další kroky
* [Vytvoření aplikace cloudové služby v rozhraní .NET](cloud-services-dotnet-get-started.md) 
* [Vytvoření aplikace cloudové služby v souboru Node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Vytvoření aplikace cloudové služby v PHP](../cloud-services-php-create-web-role.md) 
* [Vytvoření aplikace cloudové služby v Pythonu](cloud-services-python-ptvs.md)






