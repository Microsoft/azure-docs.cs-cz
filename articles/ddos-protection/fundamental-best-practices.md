---
title: Azure DDoS Protection základní osvědčené postupy
description: Seznamte se s osvědčenými postupy zabezpečení pomocí DDoS Protection.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 66c1ab1cb5ed478aa34825fb6903e4d06f834097
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94989469"
---
# <a name="fundamental-best-practices"></a>Základní osvědčené postupy

V následujících částech najdete doporučené pokyny k sestavování DDoS odolných služeb v Azure.

## <a name="design-for-security"></a>Navrhování s důrazem na zabezpečení

Zajistěte, aby zabezpečení bylo prioritní v celém životním cyklu aplikace, od návrhu a implementace po nasazení a provoz. Aplikace mohou mít chyby, které umožňují relativně nízké množství požadavků na použití nadměrné množství prostředků, což vede k výpadku služby. 

Aby byla zajištěna ochrana služby spuštěné v Microsoft Azure, měli byste mít dobrou představu o architektuře vaší aplikace a soustředit se na [pět pilířů kvality softwaru](/azure/architecture/guide/pillars).
Měli byste znát typické objemy přenosů dat, model připojení mezi aplikací a dalšími aplikacemi a koncové body služby, které jsou zpřístupněny veřejnému Internetu.

Důležité je zajistit, aby byla aplikace dostatečně odolná pro zpracování odepření služby, která je zaměřená na samotnou aplikaci, je nejdůležitější. Zabezpečení a ochrana osobních údajů jsou integrované v platformě Azure, počínaje nástrojem [SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl/default.aspx). SDL řeší zabezpečení v každé vývojové fázi a zajišťuje průběžnou aktualizaci Azure, aby bylo ještě bezpečnější.

## <a name="design-for-scalability"></a>Návrh pro škálovatelnost

Škálovatelnost je to, jak dobře dokáže systém zvládnout zvýšené zatížení. Navrhněte své aplikace pro [horizontální škálování](/azure/architecture/guide/design-principles/scale-out) tak, aby splňovaly požadavky na zesilovací zatížení, konkrétně v případě útoku DDoS. Pokud vaše aplikace závisí na jedné instanci služby, vytvoří se v jednom bodě selhání. Zřizování více instancí zajistí, že bude váš systém pružnější a lépe škálovatelný.

V případě [Azure App Service](../app-service/overview.md)vyberte [plán App Service](../app-service/overview-hosting-plans.md) , který nabízí více instancí. Pro Azure Cloud Services nakonfigurujte jednotlivé role tak, aby používaly [víc instancí](../cloud-services/cloud-services-choose-me.md). V případě [Azure Virtual Machines](../virtual-machines/index.yml)zajistěte, aby architektura virtuálních počítačů (VM) zahrnovala více než jeden virtuální počítač a aby každý virtuální počítač byl součástí [skupiny dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md). Pro možnosti automatického škálování doporučujeme používat [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md) .

## <a name="defense-in-depth"></a>Hloubková ochrana

Cílem důkladné obrany je spravovat rizika pomocí různých obrannou linií strategií. Vrstvení zabezpečení v aplikaci omezuje možnost úspěšného útoku. Doporučujeme, abyste pro své aplikace implementovali zabezpečené návrhy pomocí integrovaných možností platformy Azure.

Například riziko útoku se zvyšuje s velikostí (*oblastí Surface*) aplikace. Oblast povrchu můžete zmenšit pomocí seznamu schválení a uzavřít tak vystavený adresní prostor IP adres a naslouchající porty, které nejsou potřebné v nástrojích pro vyrovnávání zatížení ([Azure Load Balancer](../load-balancer/quickstart-load-balancer-standard-public-portal.md) a [Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)). [Skupiny zabezpečení sítě (skupin zabezpečení sítě)](../virtual-network/network-security-groups-overview.md) představují jiný způsob, jak omezit plochu pro útok.
[Značky služeb](../virtual-network/network-security-groups-overview.md#service-tags) a [skupiny zabezpečení aplikací](../virtual-network/network-security-groups-overview.md#application-security-groups) můžete použít k minimalizaci složitosti při vytváření pravidel zabezpečení a konfiguraci zabezpečení sítě, jako přirozené rozšíření struktury aplikace.

Služby Azure ve [virtuální síti](../virtual-network/virtual-networks-overview.md) byste měli nasazovat, kdykoli to bude možné. Tento postup umožňuje prostředkům služby komunikovat prostřednictvím privátních IP adres. Provoz služeb Azure z virtuální sítě ve výchozím nastavení používá veřejné IP adresy jako zdrojové IP adresy. Použití [koncových bodů služby](../virtual-network/virtual-network-service-endpoints-overview.md) přepne provoz služby na používání privátních adres virtuální sítě jako zdrojových IP adres při přístupu ke službě Azure z virtuální sítě.

Často se v Azure zobrazují místní prostředky zákazníků, které získávají útok na jejich prostředky. Pokud připojujete místní prostředí k Azure, doporučujeme, abyste minimalizovali expozici místních prostředků veřejnému Internetu. V Azure můžete využít možnosti škálování a rozšířené ochrany DDoS, a to nasazením známých veřejných entit v Azure. Vzhledem k tomu, že tyto veřejně přístupné entity jsou často cílem útoků DDoS, jejich vložení do Azure snižuje dopad na vaše místní prostředky.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit plán DDoS Protection](manage-ddos-protection.md).