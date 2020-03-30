---
title: Předpona veřejné IP adresy Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, co je předpona veřejné IP adresy Azure a jak vám může pomoct přiřadit předvídatelné veřejné IP adresy k vašim prostředkům.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: anavin
ms.openlocfilehash: 12fb7e03062600745cd8511d37b439ce44f2ef78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75640716"
---
# <a name="public-ip-address-prefix"></a>Předpony veřejných IP adres

Předpona veřejné IP adresy je rezervovaný rozsah IP adres pro vaše veřejné koncové body v Azure. Azure přiděluje souvislý rozsah adres do vašeho předplatného na základě toho, kolik zadáte. Pokud nejste obeznámeni s veřejnými adresami, přečtěte [si informace o veřejných IP adresách.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Veřejné IP adresy se přiřazují z fondu adres v každé oblasti Azure. Můžete [si stáhnout](https://www.microsoft.com/download/details.aspx?id=56519) seznam rozsahů, které Azure používá pro každou oblast. Například 40.121.0.0/16 je jedním z více než 100 rozsahů, které Azure používá v oblasti USA – východ. Rozsah zahrnuje použitelné adresy 40.121.0.1 - 40.121.255.254.

Předponu veřejné IP adresy v oblasti Azure a předplatné můžete vytvořit zadáním názvu a kolik adres chcete předponu zahrnout. Například Pokud vytvoříte předponu veřejné IP adresy /28, Azure přidělí 16 adres z jednoho z jeho rozsahů pro vás. Nevíte, který rozsah Azure přiřadí, dokud nevytvoříte rozsah, ale adresy jsou souvislé. Předpony veřejných IP adres mají poplatek. Podrobnosti naleznete v tématu [ceny za veřejnou IP adresu](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Proč vytvářet předponu veřejné IP adresy?

Když vytvoříte prostředky veřejné IP adresy, Azure přiřadí dostupnou veřejnou IP adresu z libovolnéoblasti používané v oblasti. Jakmile Azure přiřadí adresu, víte, co je adresa, ale dokud Azure přiřadí adresu, nevíte, která adresa může být přiřazena. To může být problematické, když například vy nebo vaši obchodní partneři nastavujete pravidla brány firewall, která umožňují určité IP adresy. Pokaždé, když zdroji přiřadíte novou veřejnou IP adresu, musí být adresa přidána do pravidla brány firewall. Při přiřazení adres zdrojům z předpony veřejné IP adresy není nutné pravidla brány firewall aktualizovat pokaždé, když přiřadíte jednu z adres, protože celý rozsah může být přidán do pravidla.

## <a name="benefits"></a>Výhody

- Prostředky veřejné IP adresy můžete vytvořit ze známého rozsahu.
- Vy nebo vaši obchodní partneři můžete vytvářet pravidla brány firewall s rozsahy, které obsahují veřejné IP adresy, které jste aktuálně přiřadili, a adresy, které jste ještě nepřiřadili. To eliminuje potřebu měnit pravidla brány firewall při přiřazování adres IP novým prostředkům.
- Výchozí velikost rozsahu, který můžete vytvořit, je /28 nebo 16 IP adres.
- Neexistují žádná omezení, kolik rozsahů můžete vytvořit, ale existují omezení na maximální počet statických veřejných IP adres, které můžete mít v předplatném Azure. V důsledku toho počet rozsahů, které vytvoříte nemůže zahrnovat více statické veřejné IP adresy, než můžete mít ve vašem předplatném. Další informace najdete v tématu [Limity Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Adresy, které vytvoříte pomocí adres z předpony, lze přiřadit k libovolnému prostředku Azure, ke kterému můžete přiřadit veřejnou IP adresu.
- Můžete snadno zjistit, které IP adresy jsou přiděleny a ještě nejsou přiděleny v rozsahu.

## <a name="scenarios"></a>Scénáře
Ke statické veřejné IP adrese můžete z předpony přidružit následující prostředky:

|Prostředek|Scénář|Kroky|
|---|---|---|
|Virtuální počítače| Připojování veřejných IP adresy z předpony k virtuálním počítačům v Azure snižuje režii na správu, pokud jde o seznamy IP služeb v bráně firewall. Můžete jednoduše zařadit celou předponu pomocí jediného pravidla brány firewall. Při škálování s virtuálními počítači v Azure můžete přidružit IP adresy ze stejné předpony, která šetří náklady, čas a režii na správu.| Přidružení IP adresy z předpony k virtuálnímu počítači: 1. [Vytvořte předponu.](manage-public-ip-address-prefix.md) 2. [Vytvořte IP adresu z předpony.](manage-public-ip-address-prefix.md) 3. [Přidružte IP adresu k síťovému rozhraní virtuálního počítače.](virtual-network-network-interface-addresses.md#add-ip-addresses) Ip adresy můžete také [přidružit k škálovací sadě virtuálních strojů](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).
| Standardní vykladače zatížení | Přisuzování veřejných IP adres z předpony do front-endové konfigurace IP nebo odchozího pravidla nástroje pro vyrovnávání zatížení zajišťuje zjednodušení vašeho veřejného IP adresového prostoru Azure. Scénář můžete zjednodušit úpravou odchozích připojení, která mají být pocházet z rozsahu sousedících adres IP definovaných veřejnou předponou IP. | Chcete-li přidružit IP adresy z předpony k vyrovnávání zatížení: 1. [Vytvořte předponu.](manage-public-ip-address-prefix.md) 2. [Vytvořte IP adresu z předpony.](manage-public-ip-address-prefix.md) 3. Při vytváření systému vyrovnávání zatížení vyberte nebo aktualizujte ip adresu vytvořenou v kroku 2 výše jako ip adresu front-endu vašeho zařízení pro vyrovnávání zatížení. |
| Brána Azure Firewall | Veřejnou IP adresu můžete použít z předpony pro odchozí SNAT. To znamená, že veškerý odchozí provoz virtuální sítě se přeloží do veřejné IP adresy [Azure Firewall.](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Vzhledem k tomu, že tato IP adresa pochází z předurčené předpony, je velmi snadné předem zjistit, jak bude vypadat vaše veřejná IP stopa v Azure. | 1. [Vytvořte předponu.](manage-public-ip-address-prefix.md) 2. [Vytvořte IP adresu z předpony.](manage-public-ip-address-prefix.md) 3. Při [nasazování brány firewall Azure](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)nezapomeňte vybrat IP adresu, kterou jste dříve přidělili z předpony.|
| Aplikační brána v2 | Veřejnou IP adresu můžete použít z předpony pro automatické škálování a zónově redundantní aplikační bránu v2. Vzhledem k tomu, že tato IP adresa pochází z předurčené předpony, je velmi snadné předem zjistit, jak bude vypadat vaše veřejná IP stopa v Azure. | 1. [Vytvořte předponu.](manage-public-ip-address-prefix.md) 2. [Vytvořte IP adresu z předpony.](manage-public-ip-address-prefix.md) 3. Při [nasazování aplikační brány](../application-gateway/quick-create-portal.md#create-an-application-gateway)nezapomeňte vybrat IP adresu, kterou jste dříve přidělili z předpony.|

## <a name="constraints"></a>Omezení

- Pro předponu nelze zadat adresy IP. Azure přiděluje IP adresy pro předponu na základě zadané velikosti.
- Můžete vytvořit předponu až 16 IP adres nebo /28. Další informace najdete v tématu [Limity Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Po vytvoření předpony nelze rozsah změnit.
- Z rozsahu předpony lze přiřadit pouze statické veřejné IP adresy vytvořené pomocí standardní skladové položky. Další informace o veřejných sadech SKU ip adres naleznete v [tématu Veřejná IP adresa](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Adresy z oblasti lze přiřadit jenom k prostředkům Azure Resource Manageru. Adresy nelze přiřadit k prostředkům v klasickém modelu nasazení.
- Všechny veřejné IP adresy vytvořené z předpony musí existovat ve stejné oblasti Azure a předplatné jako předpona a musí být přiřazeny k prostředkům ve stejné oblasti a předplatné.
- Předponu nelze odstranit, pokud jsou všechny adresy v ní přiřazeny k veřejným prostředkům IP adres přidruženým k prostředku. Nejprve oddělte všechny prostředky veřejných IP adres, kterým jsou přiřazeny adresy IP, od předpony.


## <a name="next-steps"></a>Další kroky

- [Vytvoření](manage-public-ip-address-prefix.md) předpony veřejné IP adresy
