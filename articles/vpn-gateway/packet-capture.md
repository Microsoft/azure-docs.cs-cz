---
title: 'Azure VPN Gateway: Konfigurace zachycení paketů'
description: Přečtěte si o funkcích zachytávání paketů, které můžete používat na branách VPN.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 41c36d302605bb619899131a8ace649b0f1439b2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151856"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Konfigurace zachycení paketů pro brány VPN

Problémy související s připojením a výkonem jsou často složité a využívají velký čas a úsilí jenom k zúžení příčiny problému. Schopnost zachytávání paketů významně pomáhá snižovat čas při zúžení rozsahu problému na určité části sítě, třeba na tom, jestli je problém na straně zákazníka sítě, na straně Azure v síti nebo někde mezi. Po zúžení problému je mnohem efektivnější ladit a provádět nápravné akce.

K dispozici jsou některé běžně dostupné nástroje pro zachytávání paketů. Získání relevantních zachycení paketů pomocí těchto nástrojů je ale často náročné na čas, zejména při práci s vysokými scénáři provozu. Funkce filtrování, které poskytuje zachytávání paketů brány VPN, se staly významnými odlišnostmi. Kromě běžně dostupných nástrojů pro zachytávání paketů můžete použít také zachytávání paketů brány VPN.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Možnosti filtrování zachytávání paketů brány VPN

Zachytávání paketů služby VPN Gateway můžete v bráně nebo konkrétním připojení spustit v závislosti na potřebách zákazníka. Zachytávání paketů můžete také spouštět na více tunelech současně. Můžete zachytit provoz s jedním nebo obousměrným směrováním, provoz IKE a ESP a vnitřní pakety spolu s filtrováním v bráně VPN.

Použití filtrů 5 řazených kolekcí členů (zdrojová podsíť, cílová podsíť, zdrojový port, cílový port, protokol) a příznaky TCP (SYN, ACK, FIN, URG, PSH, RST) je užitečné při izolaci problémů s vysokým objemem provozu.

## <a name="setup-packet-capture-using-powershell"></a>Nastavení zachytávání paketů pomocí prostředí PowerShell

Pokud chcete spustit a zastavit zachytávání paketů, podívejte se na příklady níže pro příkazy prostředí PowerShell. Další informace o možnostech parametrů (například vytváření filtrů) najdete v tomto [dokumentu](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)PowerShellu.

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Spustit zachytávání paketů pro bránu sítě VPN

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Nepovinný parametr **-fulltextových** lze použít k aplikování filtrů.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Zastavení zachytávání paketů pro bránu sítě VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Spustit zachytávání paketů pro připojení brány VPN

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Nepovinný parametr **-fulltextových** lze použít k aplikování filtrů.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Zastavit zachytávání paketů u připojení brány sítě VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Klíčové aspekty

- Spuštění zachycení paketů může mít vliv na výkon. Nezapomeňte zachytávání paketů zastavit, pokud není potřeba.
- Navrhovaná minimální doba zachytávání paketů je 600 sekund. Pokud má kratší doba trvání zachytávání paketů, nesmí doplňovat kompletní data kvůli synchronizaci problémů mezi několika součástmi v cestě.
- Soubory dat zachytávání paketů jsou generovány ve formátech PCAP nebo ETL. Pro pochopení dat možná budete potřebovat analyzátor Netmon.

## <a name="next-steps"></a>Další kroky

Další informace o VPN Gateway najdete v tématu [o VPN Gateway](vpn-gateway-about-vpngateways.md)