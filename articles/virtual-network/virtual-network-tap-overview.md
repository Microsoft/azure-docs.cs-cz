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
ms.openlocfilehash: 47db03460ad3c5194a5445f0b25cb8e742e60c21
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358924"
---
# <a name="virtual-network-tap"></a>Naslouchací zařízení virtuální sítě

KLEPNUTÍ na virtuální síť Azure (terminálový přístupový bod) umožňuje nepřetržitě streamovat síťový provoz virtuálního počítače do nástroje pro shromažďování síťových paketů nebo pro analýzu. Sběrač nebo nástroj pro analýzu poskytuje síťový partner pro [síťové virtuální zařízení](https://azure.microsoft.com/solutions/network-appliances/) . Seznam partnerských řešení, která jsou ověřená pro práci s virtuální sítí, najdete v tématu [Partnerská řešení](#virtual-network-tap-partner-solutions).

> [!IMPORTANT]
> Klepnutí na virtuální síť je v tuto chvíli ve všech oblastech Azure ve verzi Preview. Pokud chcete použít virtuální síť, musíte se zaregistrovat ve verzi Preview odesláním e-mailu <azurevnettap@microsoft.com> s vaším ID předplatného. Jakmile se vaše předplatné zaregistruje, dostanete e-mail s potvrzením. Možnost nemůžete použít, dokud nedostanete potvrzovací e-mail. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro produkční úlohy. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v tématu [dodatečné informace o použití Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview .

## <a name="virtual-network-tap-partner-solutions"></a>Virtuální síť – klepněte na Partnerská řešení

### <a name="network-packet-brokers"></a>Zprostředkovatelé síťových paketů

- [Prostředky infrastruktury pro velké monitorování velkých přepínačů](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>Analýza zabezpečení, Správa výkonu sítě a aplikace

- [Zabezpečení spánku](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cloud Cisco Stealthwatch](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop se odhalí (x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis kyberbezpečnosti](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [® Platforma RSA NetWitness](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

Následující obrázek ukazuje, jak funguje virtuální síť. Můžete přidat konfiguraci klepnutím na [síťové rozhraní](virtual-network-network-interface.md) , které je připojené k virtuálnímu počítači nasazenému ve vaší virtuální síti. Cíl je IP adresa virtuální sítě ve stejné virtuální síti jako monitorované síťové rozhraní nebo navázání [partnerské virtuální](virtual-network-peering-overview.md) sítě. Řešení kolektoru pro klepnutí na virtuální síť můžete nasadit za využití interního nástroje pro vyrovnávání zatížení Azure pro zajištění vysoké dostupnosti. Pokud chcete vyhodnotit možnosti nasazení pro jednotlivá řešení, přečtěte si téma [Partnerská řešení](#virtual-network-tap-partner-solutions).

![Jak funguje na virtuální síti](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Požadavky

Předtím, než vytvoříte virtuální síť, musíte přijmout potvrzovací e-mail, který jste si zaregistrovali ve verzi Preview, a mít jeden nebo víc virtuálních počítačů vytvořených pomocí modelu nasazení [Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a partnerského řešení pro agregaci provozu klepnutí ve stejné oblasti Azure. Pokud ve vaší virtuální síti nemáte Partnerské řešení, přečtěte si článek o [partnerských řešeních](#virtual-network-tap-partner-solutions) k nasazení. K agregaci provozu z více síťových rozhraní ve stejných nebo různých předplatných můžete použít stejnou virtuální síť. klepněte na prostředek. Pokud jsou monitorovaná síťová rozhraní v různých předplatných, musí být odběry přidruženy ke stejnému Azure Active Directory tenantovi. Monitorovaná síťová rozhraní a cílový koncový bod pro agregaci přenosů po klepnutí se navíc můžou nacházet v partnerských virtuálních sítích ve stejné oblasti. Pokud používáte tento model nasazení, zajistěte, aby bylo povoleno vytváření [partnerských vztahů virtuálních sítí](virtual-network-peering-overview.md) před konfigurací klepnutí na virtuální síť.

## <a name="permissions"></a>Oprávnění

Účty, které použijete k použití konfigurace na síťových rozhraních, musí být přiřazeny roli [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která je přiřazená k potřebným akcím z následující tabulky:

| Akce | Název |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Vyžaduje se pro vytvoření, aktualizaci, čtení a odstranění virtuální sítě klepnutím na prostředek. |
| Microsoft.Network/networkInterfaces/read | Vyžaduje se ke čtení prostředku síťového rozhraní, na kterém se bude konfigurovat klepnutím. |
| Microsoft. Network/tapConfigurations/* | Vyžaduje se pro vytvoření, aktualizaci, čtení a odstranění konfigurace klepnutí na síťovém rozhraní. |

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit virtuální síť klepnutím](tutorial-tap-virtual-network-cli.md).
