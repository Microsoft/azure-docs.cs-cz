---
title: Azure předpona veřejné IP adresy | Dokumentace Microsoftu
description: Přečtěte si o jaké Azure veřejné IP předponu adresy a jak se vám může pomoct že přiřazení předvídatelné veřejné IP adresy k vašim prostředkům.
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
ms.openlocfilehash: 5bbe0709f89ca198b0571526291f700c99e9e59f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966771"
---
# <a name="public-ip-address-prefix"></a>Předpona veřejné IP adresy

Předponu veřejné IP adresy je rezervovaný rozsah IP adres pro veřejné koncové body v Azure. Platforma Azure přiřadí souvislý rozsah adres pro vaše předplatné založené na tom, kolik je zadat. Pokud nejste obeznámeni s veřejnými adresami, přečtěte si téma [veřejné IP adresy.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Veřejné IP adresy jsou přiřazené, z fondu adres v každé oblasti Azure. Je možné [Stáhnout](https://www.microsoft.com/download/details.aspx?id=41653) seznam oblastí Azure používá pro každou oblast. Například 40.121.0.0/16 je jedním z více než 100 rozsahů, které Azure používá v oblasti USA – východ. Rozsah zahrnuje použitelné adresy 40.121.0.1 - 40.121.255.254.

Vytvoření veřejné předpona IP adresy v oblasti Azure a předplatné tak, že zadáte název a kolik adres chcete předpona, kterou chcete zahrnout. Například pokud vytvoříte veřejnou předpona IP adresy velikosti/28, Azure přiděluje 16 adres z jednoho z jeho rozsahy za vás. Si nejste jisti, které oblasti Azure přiřadí, dokud nevytvoříte rozsahu, ale tyto adresy byly souvislé. Předpony veřejných IP adres mají poplatek. Podrobnosti najdete v tématu [ceny veřejných IP adres](https://azure.microsoft.com/pricing/details/ip-addresses).

> [!IMPORTANT]
> Předpony veřejných IP je ve verzi public preview v oblasti omezená. Je možné [zjistěte, co to znamená, že bude ve verzi preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Předpony veřejných IP je v tuto chvíli k dispozici: střed USA – Západ, USA – Západ, USA – západ 2, střed USA, Severní Evropa, západní Evropa a jihovýchodní Asie. Aktualizovaný seznam oblastí, navštivte prosím [aktualizace Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="why-create-a-public-ip-address-prefix"></a>Proč vytvářet předponu veřejné IP adresy?

Když vytvoříte prostředky veřejné IP adresy, Azure k dispozici veřejnou IP adresu přiřadit z žádným z rozsahů, které jsou použity v jiné oblasti. Jakmile Azure přiřadí adresu, budete vědět, co je adresa, ale až do Azure ho přiřadí adresu, si nejste jisti, jakou adresu může být přiřazeno. To může být problematické, pokud například vám nebo vaší obchodní partnery nastavení pravidel brány firewall, které umožňují konkrétní IP adresy. Pokaždé, když je novou veřejnou IP adresu přiřadit prostředku, adresa musí být přidán do pravidla brány firewall. Při přiřazování adres k prostředkům z předponu veřejné IP adresy, pravidla brány firewall nemusíte aktualizovat pokaždé, když je jednou z adres, přiřadit, protože celá řada může být přidán do pravidla.

## <a name="benefits"></a>Výhody

- Prostředky veřejné IP adresy můžete vytvořit ze známých rozsahu.
- Vy nebo vaše obchodní partnery můžete vytvořit pravidla brány firewall s rozsahy adres, které zahrnují veřejné IP adresy, které jste aktuálně přiřazená, jakož i adresy, které ještě nebyly přiřazeny. Tím se eliminuje potřeba změnit pravidla brány firewall na nové prostředky přiřazení IP adresy.
- Výchozí velikost oblasti, které můžete vytvořit je/28 nebo 16 IP adres.
- Neplatí žádné limity jde o tom, kolik rozsahy můžete vytvořit, ale existují omezení maximálního počtu statické veřejné IP adresy, které máte v rámci předplatného Azure. Počet rozsahů, které vytvoříte v důsledku toho nemůže zahrnovat více statických veřejných IP adres než může mít v rámci vašeho předplatného. Další informace najdete v tématu [omezení Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Adresy, které vytvoříte pomocí adresy z předpony je možné přiřadit k jakémukoli prostředku Azure, která přiřadíte veřejnou IP adresu, která.
- Můžete snadno zobrazit, které IP adresy, které jsou přiděleny a ještě není přidělena v rozsahu.

## <a name="scenarios"></a>Scénáře
Statické veřejné IP adresy můžete přiřadit následující prostředky z předpony:

|Prostředek|Scénář|Kroky|
|---|---|---|
|Virtuální počítače| Přidružení veřejné IP adresy z předpony k vašim virtuálním počítačům v Azure snižuje režie na správu, když se na seznam povolených IP adres v bráně firewall. Můžete jednoduše seznam povolených celý předponu jediné pravidlo firewallu. Při škálování s virtuálními počítači v Azure, můžete přiřadit IP adresy z stejnou předponu šetří náklady, čas a režie na správu.| Přidružení IP adresy z předpony k virtuálnímu počítači: 1. [Vytvoří předponu.](manage-public-ip-address-prefix.md) 2. [Vytvořte integrační balíček z předpony.](manage-public-ip-address-prefix.md) 3. [Přidružení IP adresy k síťovému rozhraní virtuálního počítače.](virtual-network-network-interface-addresses.md#add-ip-addresses)
| Nástroje pro vyrovnávání zatížení | Přidružení veřejné IP adresy z předpony pro vaše IP adresa front-endová konfigurace nebo odchozí pravidlo nástroje pro vyrovnávání zatížení zajišťuje zjednodušení vaše Azure veřejný prostor IP adres. Vašemu scénáři můžete zjednodušit výmaz dat odchozí připojení k pochází z rozsahu souvislých IP adres podle předpony veřejných IP. | Přidružení IP adresy z předpony pro nástroj pro vyrovnávání zatížení: 1. [Vytvoří předponu.](manage-public-ip-address-prefix.md) 2. [Vytvořte integrační balíček z předpony.](manage-public-ip-address-prefix.md) 3. Při vytváření nástroje pro vyrovnávání zatížení, vyberte nebo aktualizovat IP adresu vytvořenou v kroku 2 výše jako front-endovou IP adresu vašeho nástroje pro vyrovnávání zatížení. |
| Brána Azure Firewall | Můžete použít veřejnou IP adresu z předpony pro odchozí SNAT. To znamená, že veškerý odchozí provoz virtuální sítě se přeloží na [Brána Firewall služby Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veřejnou IP adresu. Protože tato IP adresa pochází z předem předponu, je velmi snadné předem vědět, jak bude vypadat vaše veřejné IP nároky na místo v Azure. | 1. [Vytvoří předponu.](manage-public-ip-address-prefix.md) 2. [Vytvořte integrační balíček z předpony.](manage-public-ip-address-prefix.md) 3. Pokud jste [nasazení brány Azure](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), je nutné vybrat můžete previosuly přidělenou IP adresu z předpony.|

## <a name="constraints"></a>Omezení

- Nelze zadat IP adresy pro předponu. Platforma Azure přiřadí IP adresy pro předponu, v závislosti na velikosti, který zadáte.
- Rozsah, nelze změnit po vytvoření předponu.
- Rozsah určený jenom adresy IPv4. Rozsah IPv6 adres neobsahuje.
- Z rozsahu předpona, která lze přiřadit pouze statické veřejné IP adresy vytvořené pomocí standardního SKU. Další informace o veřejné IP adresy skladové položky, naleznete v tématu [veřejnou IP adresu](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Adresy z rozsahu je možné přiřadit pouze na prostředky Azure Resource Manageru. Adresy nelze přiřadit k prostředkům v modelu nasazení classic.
- Všechny veřejné IP adresy vytvořené z předpony musí existovat ve stejné oblasti Azure a předplatné jako předpona a musíte být přiřazeni k prostředkům ve stejné oblasti a předplatném.
- Předponu nejde odstranit, pokud všechny adresy v něm jsou přiřazeny k prostředky adres veřejné IP adresy, které jsou přidružené k prostředku. Zrušit přidružení všechny prostředky veřejné IP adresy adresy, které jsou přiřazeny IP adresy z předpony nejprve.


## <a name="next-steps"></a>Další postup

- [Vytvoření](manage-public-ip-address-prefix.md) předponu veřejné IP adresy
