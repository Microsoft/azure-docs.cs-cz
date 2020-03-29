---
title: 'Brána Azure VPN: Konfigurace zachycení paketů'
description: Přečtěte si o funkcích zachytávání paketů, které můžete použít u bran VPN.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 2429a8d08baa34aed120cffa069abae1fb9a3df9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353507"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Konfigurace sběru paketů pro brány VPN

Problémy s připojením a výkonem jsou často krát složité a trvat značné množství času a úsilí jen zúžit příčinu problému. Možnost sběru paketů výrazně pomáhá zkrátit dobu při zužování rozsahu problému na určité části sítě, například zda je problém na straně zákazníka sítě, na straně Azure sítě nebo někde mezi nimi. Jakmile byl problém zúžen, je mnohem efektivnější ladit a přijmout nápravná opatření.

Existují některé běžně dostupné nástroje pro sběr paketů. Získání relevantní paket zachycení pomocí těchto nástrojů je však často krát těžkopádné zejména při práci s scénáře velkoobjemového provozu. Možnosti filtrování poskytované sběrem paketů brány VPN se stávají hlavním rozlišovacím programem. Kromě běžně dostupných nástrojů pro digitalizaci paketů můžete použít také sběr paketů brány VPN.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Možnosti filtrování sběru paketů brány VPN

Zachycení paketů brány VPN lze spustit na bráně nebo na konkrétním připojení v závislosti na potřebách zákazníka. Můžete také spustit zachycení paketů ve více tunelových propojeních současně. Můžete zachytit jednosměrový nebo obousměrný provoz, provoz Protokolu IKE a ESP a vnitřní pakety spolu s filtrováním na bráně VPN.

Použití filtru 5 n-tic (zdrojová podsíť, cílová podsíť, zdrojový port, cílový port, protokol) a příznaky TCP (SYN, ACK, FIN, URG, PSH, RST) je užitečné při izolování problémů s velkým objemem provozu.

Při spuštění sběru paketů můžete použít pouze jednu možnost pro jednu vlastnost.

## <a name="setup-packet-capture-using-powershell"></a>Nastavení zachycení paketů pomocí prostředí PowerShell

Podívejte se na příklady níže pro příkazy prostředí PowerShell pro spuštění a zastavení sběru paketů. Další informace o možnostech parametrů (například jak vytvořit filtr) najdete v tomto [dokumentu](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)prostředí PowerShell .

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Spuštění sběru paketů pro bránu VPN

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Volitelný parametr **-FilterData** lze použít k použití filtru.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Zastavit sběr paketů pro bránu VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Spuštění sběru paketů pro připojení brány VPN

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Volitelný parametr **-FilterData** lze použít k použití filtru.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Zastavit sběr paketů v připojení brány VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Klíčové aspekty

- Spuštění zachycení paketů může ovlivnit výkon. Nezapomeňte zastavit sběr paketů, když není potřeba.
- Doporučená minimální doba sběru paketů je 600 sekund. Kratší doba sběru paketů nemusí poskytovat úplná data z důvodu problémů se synchronizací mezi více součástmi na cestě.
- Datové soubory sběru paketů jsou generovány ve formátu PCAP. K otevření souborů PCAP použijte Wireshark nebo jiné běžně dostupné aplikace.

## <a name="next-steps"></a>Další kroky

Další informace o bráně VPN najdete v tématu [O bráně VPN](vpn-gateway-about-vpngateways.md)
