---
title: Zobrazení a konfigurace telemetrie DDoS Protection pro Azure DDoS Protection Standard
description: Přečtěte si, jak zobrazit a nakonfigurovat telemetrii DDoS Protection pro Azure DDoS Protection Standard.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 0be184921ff0bd6b98dd2975acb4e0d5c8b26ba0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716189"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>Zobrazení a konfigurace telemetrie ochrany před útoky DDoS

Azure DDoS Protection Standard poskytuje podrobné přehledy a vizualizace útoků pomocí DDoSch analýz. Zákazníci, kteří chrání své virtuální sítě před útoky DDoS, mají podrobnější přehled o útokech na útoky a akcích podniknutých za účelem zmírnění útoku prostřednictvím sestav o zmírnění útoků, & protokolů pro zmírnění rizik. Bohatá telemetrie se zveřejňuje prostřednictvím Azure Monitor včetně podrobných metrik během doby trvání útoku DDoS. Upozornění je možné nakonfigurovat pro všechny metriky služby Azure Monitor zveřejněné službou DDoS Protection. Protokolování se dá dál integrovat s [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event Hubs), OMS Log Analytics a Azure Storage pro pokročilou analýzu prostřednictvím rozhraní diagnostiky Azure monitor.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zobrazit telemetrii DDoS Protection
> * Zobrazit zásady zmírnění rizik v DDoS
> * Ověřování a testování telemetrie DDoS Protection

### <a name="metrics"></a>Metriky

> [!NOTE]
> I když se na Azure Portal zobrazuje více možností **agregace** , pro každou metriku se podporují jenom typy agregace uvedené v následující tabulce. Omlouváme se za tuto nejasnost a pracujeme na jejich řešení.

Pro Azure DDoS Protection Standard jsou k dispozici následující [metriky](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses) . Tyto metriky je také možné exportovat prostřednictvím nastavení diagnostiky (viz [zobrazení a konfigurace diagnostického protokolování DDoS](diagnostic-logging.md)).


| Metric | Zobrazovaný název metriky | Jednotka | Typ agregace | Popis |
| --- | --- | --- | --- | --- |
| BytesDroppedDDoS | Příchozí bajty vynechané DDoS | BytesPerSecond | Maximum | Příchozí bajty vynechané DDoS| 
| BytesForwardedDDoS | Příchozí bajty předané DDoS | BytesPerSecond | Maximum | Příchozí bajty předané DDoS |
| BytesInDDoS | Příchozí bajty DDoS | BytesPerSecond | Maximum | Příchozí bajty DDoS |
| DDoSTriggerSYNPackets | Příchozí pakety SYN pro aktivaci zmírnění DDoS | CountPerSecond | Maximum | Příchozí pakety SYN pro aktivaci zmírnění DDoS |
| DDoSTriggerTCPPackets | Příchozí pakety TCP pro aktivaci zmírnění DDoS | CountPerSecond | Maximum | Příchozí pakety TCP pro aktivaci zmírnění DDoS |
| DDoSTriggerUDPPackets | Příchozí pakety UDP pro aktivaci zmírnění DDoS | CountPerSecond | Maximum | Příchozí pakety UDP pro aktivaci zmírnění DDoS |
| IfUnderDDoSAttack | V části útok DDoS nebo ne | Počet | Maximum | V části útok DDoS nebo ne |
| PacketsDroppedDDoS | Vynechané příchozí pakety DDoS | CountPerSecond | Maximum | Vynechané příchozí pakety DDoS |
| PacketsForwardedDDoS | DDoS předaných příchozích paketů | CountPerSecond | Maximum | DDoS předaných příchozích paketů |
| PacketsInDDoS | DDoS příchozích paketů | CountPerSecond | Maximum | DDoS příchozích paketů |
| TCPBytesDroppedDDoS | Příchozí bajty protokolu TCP vyhozené DDoS | BytesPerSecond | Maximum | Příchozí bajty protokolu TCP vyhozené DDoS |
| TCPBytesForwardedDDoS | Příchozí DDoS předaných bajtů protokolu TCP | BytesPerSecond | Maximum | Příchozí DDoS předaných bajtů protokolu TCP |
| TCPBytesInDDoS | Příchozí bajty DDoS TCP | BytesPerSecond | Maximum | Příchozí bajty DDoS TCP |
| TCPPacketsDroppedDDoS | Zrušené příchozí pakety protokolu TCP DDoS | CountPerSecond | Maximum | Zrušené příchozí pakety protokolu TCP DDoS |
| TCPPacketsForwardedDDoS | DDoS předaných paketů příchozího protokolu TCP | CountPerSecond | Maximum | DDoS předaných paketů příchozího protokolu TCP |
| TCPPacketsInDDoS | DDoS příchozí pakety TCP | CountPerSecond | Maximum | DDoS příchozí pakety TCP |
| UDPBytesDroppedDDoS | Zrušené Příchozí bajty UDP DDoS | BytesPerSecond | Maximum | Zrušené Příchozí bajty UDP DDoS |
| UDPBytesForwardedDDoS | Příchozí DDoS předávaných bajtů UDP | BytesPerSecond | Maximum | Příchozí DDoS předávaných bajtů UDP |
| UDPBytesInDDoS | Příchozí bajty UDP DDoS | BytesPerSecond | Maximum | Příchozí bajty UDP DDoS |
| UDPPacketsDroppedDDoS | Zrušené příchozí pakety UDP DDoS | CountPerSecond | Maximum | Zrušené příchozí pakety UDP DDoS |
| UDPPacketsForwardedDDoS | Příchozí pakety UDP předané DDoS | CountPerSecond | Maximum | Příchozí pakety UDP předané DDoS |
| UDPPacketsInDDoS | Příchozí pakety UDP DDoS | CountPerSecond | Maximum | Příchozí pakety UDP DDoS |

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- Než budete moct dokončit kroky v tomto kurzu, musíte nejdřív vytvořit [plán Azure DDoS Standard](manage-ddos-protection.md) a na virtuální síti musí být povolený DDoS Protection Standard.
- DDoS sleduje veřejné IP adresy přiřazené k prostředkům v rámci virtuální sítě. Pokud ve virtuální síti nemáte žádné prostředky s veřejnými IP adresami, musíte nejprve vytvořit prostředek s veřejnou IP adresou. Pro [služby Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (včetně nástrojů pro vyrovnávání zatížení Azure, kde jsou virtuální počítače back-end ve virtuální síti), s výjimkou prostředí Azure App Service můžete monitorovat veřejnou IP adresu všech prostředků nasazených prostřednictvím Správce prostředků (ne Classic). Pokud chcete pokračovat v tomto kurzu, můžete rychle vytvořit virtuální počítač s [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linuxem](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .  

## <a name="view-ddos-protection-telemetry"></a>Zobrazit telemetrii DDoS Protection

Telemetrie k útokům je k dispozici prostřednictvím služby Azure Monitor v reálném čase. Telemetrii je dostupná jenom v případě, že je veřejná IP adresa v oblasti zmírnění rizik. 

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) a přejděte k plánu DDoS Protection.
2. V oblasti **Monitorování** vyberte **Metriky**.
3. Vyberte **Obor**. Vyberte **předplatné** , které obsahuje veřejnou IP adresu, kterou chcete protokolovat, vyberte možnost **Veřejná IP adresa** pro **typ prostředku** a pak vyberte konkrétní veřejnou IP adresu, pro kterou chcete metriky protokolovat, a pak vyberte **použít**.
4. Vyberte typ **agregace** jako **Max**.

Názvy metrik obsahují různé typy paketů a bajty vs. pakety se základní konstrukcí názvů značek na jednotlivých metrikách, a to následujícím způsobem:

- **Vynechaný název značky** (například **odhozené příchozí pakety DDoS**): počet paketů vyřazených/provedených systémem ochrany DDoS.
- **Název předané značky** (například **příchozí pakety předané DDoS**): počet paketů předaných systémem DDoS do cílové VIP – přenos, který nebyl filtrován.
- **Žádný název značky** (například **příchozí pakety DDoS**): celkový počet paketů předaných do systému čištění dat, který představuje součet vyřazených a předaných paketů.

## <a name="view-ddos-mitigation-policies"></a>Zobrazit zásady zmírnění rizik v DDoS

DDoS Protection Standard aplikuje tři automaticky optimalizované zásady pro zmírnění rizik (TCP SYN, TCP & UDP) pro každou veřejnou IP adresu chráněného prostředku ve virtuální síti, ve které je povolený DDoS. Prahové hodnoty zásad si můžete zobrazit tak, že vyberete  **příchozí pakety TCP, které aktivují DDoS** a **příchozí pakety UDP pro aktivaci DDoSch rizikových** metrik s typem **agregace** jako Max, jak je znázorněno na následujícím obrázku:

![Zobrazit zásady zmírňování](./media/manage-ddos-protection/view-mitigation-policies.png)

Prahové hodnoty zásad se automaticky konfigurují prostřednictvím profilace síťového provozu založeného na službě Azure Machine Learning. Pokud dojde k narušení prahové hodnoty zásad, DDoS se na útok v případě útoku na hrozby.

## <a name="validate-and-test"></a>Ověřit a otestovat

Chcete-li simulovat útok DDoS k ověření telemetrie DDoS Protection, přečtěte si téma [ověření DDoSho zjišťování](test-through-simulations.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

- Konfigurace výstrah pro metriky DDoS Protection
- Zobrazit telemetrii DDoS Protection
- Zobrazit zásady zmírnění rizik v DDoS
- Ověřování a testování telemetrie DDoS Protection

Pokud se chcete dozvědět, jak nakonfigurovat sestavy zmírnění útoků a protokoly toků, pokračujte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Zobrazení a konfigurace protokolování diagnostiky DDoS](diagnostic-logging.md)