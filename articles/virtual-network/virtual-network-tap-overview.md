---
title: Virtuální síť Azure – Přehled klepněte na přehled | Microsoft Docs
description: Přečtěte si informace o KLEPNUTÍch virtuální sítě. Klepnutím na virtuální síť získáte hloubkovou kopii provozu sítě virtuálních počítačů, která se dá streamovat do sběrače paketů.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: ceafbacb4cfc99b5327423ff1a50b82292030893
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92479251"
---
# <a name="virtual-network-tap"></a>Naslouchací zařízení virtuální sítě
> [!IMPORTANT]
> V současné době se ve všech oblastech Azure podrží ve službě Virtual Network možnost je ve verzi Preview. Můžete nás poslat e-mailem <azurevnettap@microsoft.com> s vaším ID předplatného a budeme vás informovat o budoucí aktualizace verze Preview. V provizorním případě můžete použít řešení založená na agentech nebo síťové virtuální zařízení, která poskytují funkce viditelnosti sítě a sítě prostřednictvím našich [řešení partnerů pro zprostředkovatele paketů](#virtual-network-tap-partner-solutions) dostupných v [Azure Marketplace nabídky](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/networking?page=1&subcategories=appliances%3Ball&search=Network%20Traffic&filters=partners).

KLEPNUTÍ na virtuální síť Azure (terminálový přístupový bod) umožňuje nepřetržitě streamovat síťový provoz virtuálního počítače do nástroje pro shromažďování síťových paketů nebo pro analýzu. Sběrač nebo nástroj pro analýzu poskytuje síťový partner pro [síťové virtuální zařízení](https://azure.microsoft.com/solutions/network-appliances/) . Seznam partnerských řešení, která jsou ověřená pro práci s virtuální sítí, najdete v tématu [Partnerská řešení](#virtual-network-tap-partner-solutions).
Následující obrázek ukazuje, jak funguje virtuální síť. Můžete přidat konfiguraci klepnutím na [síťové rozhraní](virtual-network-network-interface.md) , které je připojené k virtuálnímu počítači nasazenému ve vaší virtuální síti. Cíl je IP adresa virtuální sítě ve stejné virtuální síti jako monitorované síťové rozhraní nebo navázání [partnerské virtuální](virtual-network-peering-overview.md) sítě. Řešení kolektoru pro klepnutí na virtuální síť můžete nasadit za využití interního nástroje pro vyrovnávání zatížení Azure pro zajištění vysoké dostupnosti.
![Jak funguje na virtuální síti](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Předpoklady

Předtím, než vytvoříte virtuální síť, musíte přijmout potvrzovací e-mail, který jste si zaregistrovali ve verzi Preview, a mít jeden nebo víc virtuálních počítačů vytvořených pomocí modelu nasazení [Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a partnerského řešení pro agregaci provozu klepnutí ve stejné oblasti Azure. Pokud ve vaší virtuální síti nemáte Partnerské řešení, přečtěte si článek o [partnerských řešeních](#virtual-network-tap-partner-solutions) k nasazení. K agregaci provozu z více síťových rozhraní ve stejných nebo různých předplatných můžete použít stejnou virtuální síť. klepněte na prostředek. Pokud jsou monitorovaná síťová rozhraní v různých předplatných, musí být odběry přidruženy ke stejnému Azure Active Directory tenantovi. Monitorovaná síťová rozhraní a cílový koncový bod pro agregaci přenosů po klepnutí se navíc můžou nacházet v partnerských virtuálních sítích ve stejné oblasti. Pokud používáte tento model nasazení, zajistěte, aby bylo povoleno vytváření [partnerských vztahů virtuálních sítí](virtual-network-peering-overview.md) před konfigurací klepnutí na virtuální síť.

## <a name="permissions"></a>Oprávnění

Účty, které použijete k použití konfigurace na síťových rozhraních, musí být přiřazeny roli [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která je přiřazená k potřebným akcím z následující tabulky:

| Akce | Name |
|---|---|
| Microsoft. Network/virtualNetworkTaps/* | Vyžaduje se pro vytvoření, aktualizaci, čtení a odstranění virtuální sítě klepnutím na prostředek. |
| Microsoft. Network/networkInterfaces/Read | Vyžaduje se ke čtení prostředku síťového rozhraní, na kterém se bude konfigurovat klepnutím. |
| Microsoft. Network/tapConfigurations/* | Vyžaduje se pro vytvoření, aktualizaci, čtení a odstranění konfigurace klepnutí na síťovém rozhraní. |


## <a name="virtual-network-tap-partner-solutions"></a>Virtuální síť – klepněte na Partnerská řešení

### <a name="network-packet-brokers"></a>Zprostředkovatelé síťových paketů

- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)
- [Prostředky infrastruktury pro velké monitorování velkých přepínačů](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)

### <a name="security-analytics-networkapplication-performance-management"></a>Analýza zabezpečení, Správa výkonu sítě a aplikace

- [Zabezpečení spánku](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cloud Cisco Stealthwatch](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis kyberbezpečnosti](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/marketplace-azure)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [® Platforma RSA NetWitness](https://www.rsa.com/content/dam/en/solution-brief/rsa-netwitness-platform-overview-for-federal-agencies.pdf)
- [Vectra Cognito](https://vectra.ai/microsoftazure)



## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit virtuální síť klepnutím](tutorial-tap-virtual-network-cli.md).
