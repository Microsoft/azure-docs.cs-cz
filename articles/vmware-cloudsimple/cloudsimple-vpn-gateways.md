---
title: Řešení Azure VMware (AVS) – brány VPN
description: Přečtěte si o konceptech sítě typu Site-to-Site VPN a VPN typu Point-to-site.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 73171e2c46bdf6c934db5777efe36ba51153a686
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024853"
---
# <a name="vpn-gateways-overview"></a>Přehled bran VPN

Služba VPN Gateway slouží k posílání šifrovaného provozu mezi sítí služby AVS v místním umístění nebo v počítači přes veřejný Internet. Každá oblast může mít jednu bránu sítě VPN, která může podporovat více připojení. Když vytvoříte několik připojení ke stejné bráně VPN, všechny tunely VPN sdílejí dostupnou šířku pásma.

Brána služby AVS nabízí dva druhy bran sítě VPN:

* Brána VPN typu Site-to-site
* Brána sítě VPN typu Point-to-site

## <a name="site-to-site-vpn-gateway"></a>Brána VPN typu Site-to-site

Brána VPN typu Site-to-site se používá k posílání šifrovaného provozu mezi sítí služby AVS a místním datacentrem. Pomocí tohoto připojení můžete definovat podsítě/rozsah CIDR pro síťový provoz mezi místní sítí a sítí oblastí služby AVS.

Služba VPN Gateway umožňuje využívat služby z místního prostředí v privátním cloudu služby AVS a služby v privátním cloudu služby AVS z místní sítě. AVS poskytuje server VPN založený na zásadách pro navázání připojení z vaší místní sítě.

Případy použití pro síť Site-to-Site VPN:

* Přístupnost vašeho privátního cloudu služby AVS z libovolné pracovní stanice ve vaší místní síti.
* Použijte místní službu Active Directory jako zdroj identity vCenter.
* Pohodlný přenos šablon virtuálních počítačů, soubory ISO a dalších souborů z místních prostředků do vašeho privátního cloudového vCenter služby AVS
* Dostupnost úloh běžících na privátním cloudu služby AVS z vaší místní sítě.

![Topologie připojení VPN typu Site-to-site](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Kryptografické parametry

Připojení VPN typu Site-to-site používá k navázání zabezpečeného připojení následující výchozí parametry šifrování. Když vytvoříte připojení z místního zařízení VPN, použijte libovolný z následujících parametrů podporovaných místní bránou sítě VPN.

#### <a name="phase-1-proposals"></a>Návrhy 1 – fáze

| Parametr | Návrh 1 | Návrh 2 | Návrh 3 |
|-----------|------------|------------|------------|
| Verze IKE | IKEv1 | IKEv1 | IKEv1 |
| Šifrování | AES 128 | AES 256 | AES 256 |
| Algoritmus hash| SHA 256 | SHA 256 | SHA 1 |
| Skupina Diffie Hellman (Skupina DH) | 2 | 2 | 2 |
| Doba života | 28 800 sekund | 28 800 sekund | 28 800 sekund |
| Velikost dat | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>Návrhy 2. fáze

| Parametr | Návrh 1 | Návrh 2 | Návrh 3 |
|-----------|------------|------------|------------|
| Šifrování | AES 128 | AES 256 | AES 256 |
| Algoritmus hash| SHA 256 | SHA 256 | SHA 1 |
| Skupina PFS (Perfect Forward Secrecy) | Žádné | Žádné | Žádné |
| Doba života | 1 800 sekund | 1 800 sekund | 1 800 sekund |
| Velikost dat | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> Na zařízení VPN nastavte připojení TCP MSS na 1200. Nebo pokud vaše zařízení VPN nepodporují svorky MSS, můžete místo toho nastavit jednotku MTU v rozhraní tunelu na 1240 bajtů.

## <a name="point-to-site-vpn-gateway"></a>Brána sítě VPN typu Point-to-site

SÍŤ VPN typu Point-to-site slouží k posílání šifrovaného provozu mezi sítí služby AVS a klientským počítačem. VPN typu Point-to-site je nejjednodušší způsob, jak získat přístup k síti privátního cloudu služby AVS, včetně vaší virtuální sítě služby AVS pro vCenter a zatížení úloh. Připojení VPN typu Point-to-site použijte v případě, že se připojujete k privátnímu cloudu služby AVS vzdáleně.

## <a name="next-steps"></a>Další kroky

* [Nastavení služby VPN Gateway](vpn-gateway.md)
