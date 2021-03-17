---
title: Azure Load Balancer a Zóny dostupnosti
titleSuffix: Azure Load Balancer
description: Pomocí této cesty výukového programu Začínáme s Azure Standard Load Balancer a Zóny dostupnosti.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: 3c18b6d8dc44762649a9c07b88af348a18888fb5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699109"
---
# <a name="load-balancer-and-availability-zones"></a>Load Balancer a Zóny dostupnosti

Azure Load Balancer podporuje scénáře zón dostupnosti. Pomocí Standard Load Balancer můžete zvýšit dostupnost v celém scénáři tím, že zarovnáte prostředky k a rozmístěte mezi zónami.  Přečtěte si tento dokument, abyste pochopili tyto koncepty a základní pokyny k návrhu scénářů.

Load Balancer může být buď **redundantní zóna, oblast** nebo **jiné než oblast**. Chcete-li nakonfigurovat vlastnosti související s zónou (uvedené výše) pro nástroj pro vyrovnávání zatížení, vyberte odpovídající typ front-endu, který potřebujete.

## <a name="zone-redundant"></a>Zóna redundantní

V oblasti s Zóny dostupnosti může být Standard Load Balancer redundantní v zóně. Tento provoz se obsluhuje pomocí jediné IP adresy.

Jedna IP adresa front-endu bude zachována při selhání zóny. Front-end IP adresu můžete použít k přístupu ke všem (bez ovlivněných) členů fondu back-endu bez ohledu na zónu. Jedna nebo více zón dostupnosti můžou selhat a cesta k datům zůstane v pořádku, dokud jedna zóna v oblasti zůstane v dobrém stavu.

IP adresa front-endu je souběžně obsluhována několika nezávislými nasazeními infrastruktury v několika zónách dostupnosti. Jakékoli opakované pokusy nebo opětovné vytvoření budou úspěšné v jiných zónách, které nejsou ovlivněny selháním zóny.

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="Virtual Network NAT">
</p>

*Obrázek: redundantní služba Vyrovnávání zatížení zóny*

## <a name="zonal"></a>Zónové

Můžete zvolit, aby front-end byl zaručen pro jednu zónu, která se označuje jako *oblast*.  Tento scénář znamená, že jakýkoliv příchozí nebo odchozí tok je obsluhován jedinou zónou v oblasti.  Vaše sdílená složka front-endu sepravila se stavem zóny.  Cesta k datům není ovlivněná chybami v jiných zónách, než kde byla zaručena. K vystavení IP adresy na jednu zónu dostupnosti můžete použít oblast front-endu.  

Kromě toho se podporuje použití oblastí front-endu přímo pro koncové body s vyrovnáváním zatížení v rámci každé zóny. Tuto konfiguraci můžete použít k vystavení koncovým bodům pro vyrovnávání zatížení zóny pro samostatné monitorování každé zóny. U veřejných koncových bodů je můžete integrovat s produktem pro vyrovnávání zatížení DNS, jako je [Traffic Manager](../traffic-manager/traffic-manager-overview.md) , a používat jeden název DNS.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="Virtual Network NAT">
</p>

*Obrázek: oblast pro vyrovnávání zatížení*

U veřejného front-endu nástroje pro vyrovnávání zatížení přidejte parametr **zóny** do veřejné IP adresy. Na tuto veřejnou IP adresu odkazuje konfigurace IP adresy front-endu, kterou používá příslušné pravidlo.

U front-endu interního nástroje pro vyrovnávání zatížení přidejte parametr **zóny** do konfigurace protokolu IP front-endu interního nástroje pro vyrovnávání zatížení. Oblast s front-endu garantuje IP adresu v podsíti do konkrétní zóny.

## <a name="design-considerations"></a><a name="design"></a> Faktory návrhu

Když teď rozumíte vlastnostem souvisejícím s zónou pro Standard Load Balancer, může vám při návrhu vysoké dostupnosti pomáhat následující faktory návrhu.

### <a name="tolerance-to-zone-failure"></a>Tolerance při selhání zóny

- **Redundantní Load Balancer zóny** může sloužit k rozhraničním prostředkům v libovolné zóně s jednou IP adresou.  Tato IP adresa může přetrvat jednu nebo více selhání zóny, pokud alespoň jedna zóna zůstane v pořádku v rámci oblasti.
- **Oblast** front-end je omezení služby na jednu zónu a podílí se v ní osud v příslušné zóně. Pokud se v zóně nasazení nachází, nasazení nebude mít za následek tuto chybu.

Doporučuje se používat pro produkční úlohy redundantní Load Balancer zóny.

### <a name="control-vs-data-plane-implications"></a>Řízení a vliv na rovinu dat

Redundance zóny neznamená hitless rovinu dat nebo rovinu ovládacího prvku. Neredundantní toky v zóně můžou používat libovolnou zónu a vaše toky budou používat všechny zóny v pořádku v oblasti. V případě selhání v zóně nejsou ovlivněny přenosové toky pomocí zón v pořádku.

Může to ovlivnit přenos toků v době selhání zóny, ale může se stát, že se aplikace obnoví. Provoz pokračuje v nefunkčních zónách v oblasti po opětovném přenosu, když se Azure Sblíženo okolo selhání zóny.

Projděte si [vzory návrhu cloudu Azure](/azure/architecture/patterns/) , abyste vylepšili odolnost vaší aplikace vůči scénářům selhání.

## <a name="next-steps"></a>Další kroky
- Další informace o [zóny dostupnosti](../availability-zones/az-overview.md)
- Další informace o [Load Balanceru úrovně Standard](./load-balancer-overview.md)
- Přečtěte si, jak [vyrovnávat zatížení virtuálních počítačů v rámci zóny pomocí Standard Load Balancer oblast](./quickstart-load-balancer-standard-public-cli.md) .
- Přečtěte si, jak [vyrovnávat zatížení virtuálních počítačů napříč zónami pomocí zóny redundantního Standard Load Balancer](./quickstart-load-balancer-standard-public-cli.md)
- Přečtěte si o [vzorcích návrhu Azure pro Cloud](/azure/architecture/patterns/) , abyste vylepšili odolnost vaší aplikace vůči scénářům selhání.
