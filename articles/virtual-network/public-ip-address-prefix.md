---
title: Předpona veřejných IP adres Azure
description: Přečtěte si, co je předpona veřejných IP adres Azure a jak vám může pomáhat s přiřazením předvídatelných veřejných IP adres vašim prostředkům.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2020
ms.author: allensu
ms.openlocfilehash: 0f71f845ef3209146ead79cafae2f3aa5c8c6d7d
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144506"
---
# <a name="public-ip-address-prefix"></a>Předpony veřejných IP adres

Předpona veřejných IP adres je vyhrazený rozsah IP adres pro vaše veřejné koncové body v Azure. Azure přiděluje souvislý rozsah adres k vašemu předplatnému na základě toho, kolik zadáte. Pokud nejste obeznámeni s veřejnými adresami, přečtěte si téma [veřejné IP adresy.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Veřejné IP adresy se přiřazují z fondu adres v každé oblasti Azure. Můžete [si stáhnout](https://www.microsoft.com/download/details.aspx?id=56519) Seznam rozsahů, které Azure používá pro každou oblast. Například 40.121.0.0/16 je jedním z více než 100 rozsahů, které Azure používá v oblasti Východní USA. Rozsah zahrnuje použitelné adresy 40.121.0.1-40.121.255.254.

Předponu veřejné IP adresy vytvoříte v oblasti a předplatném Azure zadáním názvu a počtu adres, které chcete zahrnout do předpony. Pokud například vytvoříte předponu veřejné IP adresy/28, Azure přidělí 16 adres z některého z jeho rozsahů za vás. Nevíte, který rozsah Azure bude přiřazovat, dokud nevytvoříte rozsah, ale adresy budou souvislé. Předpony veřejných IP adres mají poplatek. Podrobnosti najdete v tématu [ceny veřejných IP adres](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Proč vytvořit předponu veřejné IP adresy?

Když vytvoříte prostředky veřejné IP adresy, Azure přiřadí dostupnou veřejnou IP adresu z libovolného rozsahu používaného v dané oblasti. Jakmile Azure tuto adresu přiřadí, zjistíte, jaká adresa je, ale až ji Azure přiřadí, nevíte, jakou adresu je možné přiřadit. To může být problematické, když například vy nebo vaši obchodní partneři nastavíte pravidla brány firewall, která povolují konkrétní IP adresy. Pokaždé, když k prostředku přiřadíte novou veřejnou IP adresu, musí být adresa přidána do pravidla brány firewall. Když přiřazujete adresy k prostředkům z předpony veřejných IP adres, pravidla brány firewall se nemusejí aktualizovat pokaždé, když přiřadíte jednu z adres, protože do pravidla by se mohla přidat celý rozsah.

## <a name="benefits"></a>Výhody

- Prostředky veřejné IP adresy můžete vytvořit ze známého rozsahu.
- Vy nebo vaši obchodní partneři můžete vytvářet pravidla brány firewall s rozsahy, které zahrnují veřejné IP adresy, které jste právě přiřadili, a také adresy, které jste ještě nepřiřadili. Tím se eliminuje nutnost měnit pravidla brány firewall při přiřazování IP adres novým prostředkům.
- Výchozí velikost rozsahu, který můžete vytvořit, je/28 nebo 16 IP adres.
- Neexistují žádná omezení pro počet rozsahů, které můžete vytvořit. Existují však omezení maximálního počtu statických veřejných IP adres, které můžete mít v rámci předplatného Azure. V důsledku toho počet rozsahů, které vytvoříte, nemůže zahrnovat víc statických veřejných IP adres, než kolik můžete mít v rámci svého předplatného. Další informace najdete v tématu [omezení Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Adresy, které vytvoříte pomocí adres z předpony, se dají přiřadit k libovolnému prostředku Azure, ke kterému můžete přiřadit veřejnou IP adresu.
- Můžete snadno zjistit, které IP adresy se přidělují a ještě nejsou v rámci rozsahu přiděleny.

## <a name="scenarios"></a>Scénáře
K statické veřejné IP adrese můžete přidružit následující prostředky z předpony:

|Prostředek|Scénář|Kroky|
|---|---|---|
|Virtuální počítače| Přidružení veřejných IP adres od předpony k virtuálním počítačům v Azure snižuje režijní náklady na správu, pokud jde o seznam povolených IP adres v bráně firewall. Můžete jednoduše přidružit celou předponu s jedním pravidlem brány firewall. Při škálování s virtuálními počítači v Azure můžete přidružit IP adresy ze stejné předpony ukládání nákladů, času a režijních nákladů na správu.| K přidružení IP adres k virtuálnímu počítači z předpony: 1. [Vytvořte předponu.](manage-public-ip-address-prefix.md) 2. [Vytvořte IP adresu z předpony.](manage-public-ip-address-prefix.md) 3. [Přiřaďte IP adresu k síťovému rozhraní virtuálního počítače.](virtual-network-network-interface-addresses.md#add-ip-addresses) [IP adresy můžete taky přidružit k sadě škálování virtuálního počítače](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).
| Standardní nástroje pro vyrovnávání zatížení | Přidružení veřejných IP adres z předpony na konfiguraci IP adresy front-endu nebo odchozí pravidlo pro Load Balancer zajišťuje zjednodušení vašeho prostoru veřejných IP adres Azure. Můžete zjednodušit scénář vymazáním odchozích připojení z rozsahu souvislých IP adres definovaných předponou veřejné IP adresy. | K přidružení IP adresy z předpony k vašemu nástroji pro vyrovnávání zatížení: 1. [Vytvořte předponu.](manage-public-ip-address-prefix.md) 2. [Vytvořte IP adresu z předpony.](manage-public-ip-address-prefix.md) 3. Při vytváření Load Balancer vyberte nebo aktualizujte IP adresu vytvořenou v kroku 2 výše jako front-endové IP adresy vašeho Load Balancer. |
| Brána Azure Firewall | Veřejnou IP adresu můžete použít z předpony pro odchozí SNAT. To znamená, že veškerý odchozí provoz virtuální sítě se převede na [Azure firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veřejnou IP adresu. Vzhledem k tomu, že tato IP adresa pochází z předem vymezené předpony, je velmi snadné znát čas, který vaše veřejná IP adresa v Azure bude vypadat jako. | 1. [vytvořte předponu.](manage-public-ip-address-prefix.md) 2. [Vytvořte IP adresu z předpony.](manage-public-ip-address-prefix.md) 3. Když [nasadíte bránu Azure firewall](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), nezapomeňte vybrat IP adresu, kterou jste dříve přidělili z předpony.|
| Application Gateway v2 | Veřejnou IP adresu můžete použít z předpony pro vaše automatické škálování a redundantní Aplikační bránu v2. Vzhledem k tomu, že tato IP adresa pochází z předem vymezené předpony, je velmi snadné znát čas, který vaše veřejná IP adresa v Azure bude vypadat jako. | 1. [vytvořte předponu.](manage-public-ip-address-prefix.md) 2. [Vytvořte IP adresu z předpony.](manage-public-ip-address-prefix.md) 3. Když [nasadíte Application Gateway](../application-gateway/quick-create-portal.md#create-an-application-gateway), nezapomeňte vybrat IP adresu, kterou jste dříve přidělili z předpony.|

## <a name="constraints"></a>Omezení

- Nemůžete zadat IP adresy pro předponu. Azure přiděluje IP adresy pro předponu na základě velikosti, kterou zadáte.
- Ve výchozím nastavení můžete vytvořit předponu s až 16 IP adresami nebo/28. Přečtěte si o [omezeních sítě zvýšení požadavků](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) a [omezení Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) , kde najdete další informace.
- Po vytvoření předpony nemůžete rozsah změnit.
- Z rozsahu předpony lze přiřadit pouze statické veřejné IP adresy, které byly vytvořeny pomocí standardní SKU. Další informace o SKU veřejných IP adres najdete v tématu [Veřejná IP adresa](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Adresy z rozsahu lze přiřadit pouze k prostředkům Azure Resource Manager. Adresy nelze přiřadit k prostředkům v modelu nasazení Classic.
- Všechny veřejné IP adresy vytvořené z předpony musí existovat ve stejné oblasti a předplatném Azure jako předpona a musí být přiřazené k prostředkům ve stejné oblasti a předplatném.
- Pokud jsou adresy v rámci této adresy přiřazené k prostředkům veřejné IP adresy, které jsou přidružené k prostředku, nemůžete předponu odstranit. Zrušte přidružení všech prostředků veřejné IP adresy, kterým jsou nejprve přiřazeny IP adresy z předpony.


## <a name="next-steps"></a>Další kroky

- [Vytvoření](manage-public-ip-address-prefix.md) předpony veřejné IP adresy
