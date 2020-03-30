---
title: Přehled tapu virtuální sítě Azure | Dokumenty společnosti Microsoft
description: Přečtěte si o virtuální síti TAP. Nástroj TAP ve virtuální síti poskytuje hlubokou kopii síťového provozu virtuálních strojů, které lze streamovat do kolektoru paketů.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279608"
---
# <a name="virtual-network-tap"></a>Naslouchací zařízení virtuální sítě

Azure virtual network TAP (Terminal Access Point) umožňuje nepřetržitě streamovat provoz sítě virtuálních strojů do kolektoru síťových paketů nebo analytického nástroje. Kolektor nebo analytický nástroj je poskytován partnerem [síťového virtuálního zařízení.](https://azure.microsoft.com/solutions/network-appliances/) Seznam partnerských řešení, která jsou ověřena pro práci s technologií TAP ve virtuální síti, naleznete [v tématu partnerská řešení](#virtual-network-tap-partner-solutions).

> [!IMPORTANT]
> Virtuální síť TAP je momentálně ve verzi preview ve všech oblastech Azure. Chcete-li používat virtuální síť TAP, musíte se <azurevnettap@microsoft.com> zaregistrovat do náhledu odesláním e-mailu s ID předplatného. Jakmile se vaše předplatné zaregistruje, dostanete e-mail s potvrzením. Tuto funkci nemůžete používat, dokud neobdržíte potvrzovací e-mail. Tato předběžná verze je k dispozici bez smlouvy o úrovni služeb a neměla by být použita pro produkční úlohy. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [doplňkových podmínkách použití pro Microsoft Azure Preview.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 

## <a name="virtual-network-tap-partner-solutions"></a>Partnerská řešení virtuální sítě TAP

### <a name="network-packet-brokers"></a>Zprostředkovatelé síťových paketů

- [Big Switch Big Monitoring Fabric](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Hranoly](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>Analýza zabezpečení, správa výkonu sítě/aplikací

- [Zabezpečení probuzení](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch Cloud](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis Kybernetická bezpečnost](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [RsA NetWitness® platforma](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

Následující obrázek ukazuje, jak funguje tap virtuální sítě. Konfiguraci TAP můžete přidat v [síťovém rozhraní](virtual-network-network-interface.md) připojeném k virtuálnímu počítači nasazenému ve vaší virtuální síti. Cílem je IP adresa virtuální sítě ve stejné virtuální síti jako monitorované síťové rozhraní nebo [partnerská virtuální](virtual-network-peering-overview.md) síť. Kolektorové řešení pro virtuální síť TAP lze nasadit za nástroj Pro vyrovnávání interního zatížení Azure pro vysokou dostupnost. Informace o možnostech nasazení pro individuální řešení naleznete [v tématu partnerská řešení](#virtual-network-tap-partner-solutions).

![Jak funguje tap virtuální sítě](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Požadavky

Před vytvořením virtuální sítě TAP, musíte obdržet potvrzovací e-mail, který jste zaregistrovaní ve verzi Preview a mít jeden nebo více virtuálních počítačů vytvořených pomocí modelu nasazení [Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a partnerské řešení pro agregaci provozu TAP ve stejné oblasti Azure. Pokud nemáte partnerské řešení ve vaší virtuální síti, podívejte se na [partnerská řešení](#virtual-network-tap-partner-solutions) pro nasazení jednoho. Stejný prostředek TAP virtuální sítě můžete použít k agregaci provozu z více síťových rozhraní ve stejném nebo jiném předplatná. Pokud jsou monitorovaná síťová rozhraní v různých předplatných, musí být předplatná přidružena ke stejnému tenantovi služby Azure Active Directory. Monitorovaná síťová rozhraní a cílový koncový bod pro agregaci provozu TAP mohou být navíc ve virtuálních sítích partnerského vztahu ve stejné oblasti. Pokud používáte tento model nasazení, ujistěte se, že [partnerský vztah virtuální sítě](virtual-network-peering-overview.md) je povolen před konfigurací virtuální sítě TAP.

## <a name="permissions"></a>Oprávnění

Účty, které používáte k použití konfigurace tap v síťových rozhraních, musí být přiřazeny roli [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [vlastní roli,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) které jsou přiřazeny potřebné akce z následující tabulky:

| Akce | Name (Název) |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Vyžadováno k vytvoření, aktualizaci, čtení a odstranění prostředku TAP ve virtuální síti |
| Microsoft.Network/networkInterfaces/read | Je nutné číst prostředek síťového rozhraní, na kterém bude tap konfigurován |
| Microsoft.Network/tapKonfigurace/* | Vyžadováno vytvoření, aktualizace, čtení a odstranění konfigurace TAP v síťovém rozhraní |

## <a name="next-steps"></a>Další kroky

- Přečtěte [si,](tutorial-tap-virtual-network-cli.md)jak vytvořit virtuální síť TAP .
