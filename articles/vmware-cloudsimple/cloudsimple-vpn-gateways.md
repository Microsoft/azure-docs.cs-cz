---
title: Řešení Azure VMware podle CloudSimple – VPN Gateway
description: Přečtěte si o konceptech sítě VPN typu Site-to-site CloudSimple a VPN typu Point-to-site.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 662fa342b3a18f726b418c496ff3fda937445301
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244771"
---
# <a name="vpn-gateways-overview"></a>Přehled bran VPN

Služba VPN Gateway slouží k posílání šifrovaného provozu mezi sítí v CloudSimple oblasti v místním umístění nebo v počítači prostřednictvím veřejného Internetu.  Každá oblast může mít jednu bránu sítě VPN, která může podporovat více připojení. Když vytvoříte několik připojení ke stejné bráně VPN, všechny tunely VPN sdílejí dostupnou šířku pásma brány.

CloudSimple poskytuje dva druhy bran sítě VPN:

* Brána VPN typu Site-to-site
* Brána sítě VPN typu Point-to-site

## <a name="site-to-site-vpn-gateway"></a>Brána VPN typu Site-to-site

Brána VPN typu Site-to-site se používá k posílání šifrovaného provozu mezi sítí CloudSimple oblasti a místním datacentrem. Pomocí tohoto připojení můžete definovat podsítě/rozsah CIDR pro síťový provoz mezi místní sítí a sítí oblasti CloudSimple.

VPN Gateway umožňuje využívat služby z místního prostředí v privátním cloudu a služby v privátním cloudu z místní sítě.  CloudSimple poskytuje server VPN založený na zásadách pro navázání připojení z vaší místní sítě.

Případy použití pro síť Site-to-Site VPN:

* Přístupnost vašeho privátního cloudu vCenter z libovolné pracovní stanice v místní síti.
* Použijte místní službu Active Directory jako zdroj identity vCenter.
* Pohodlný přenos šablon virtuálních počítačů, soubory ISO a dalších souborů z místních prostředků do vašeho privátního cloudu vCenter.
* Dostupnost úloh běžících ve vašem privátním cloudu z vaší místní sítě.

![Topologie připojení VPN typu Site-to-site](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Kryptografické parametry

Připojení VPN typu Site-to-site používá k navázání zabezpečeného připojení následující výchozí parametry šifrování.  Když vytvoříte připojení z místního zařízení VPN, použijte libovolný z následujících parametrů podporovaných místní bránou sítě VPN.

#### <a name="phase-1-proposals"></a>Návrhy 1 – fáze

| Parametr | Návrh 1 | Návrh 2 | Návrh 3 |
|-----------|------------|------------|------------|
| Verze protokolu IKE | IKEv1 | IKEv1 | IKEv1 |
| Encryption | AES 128 | AES 256 | AES 256 |
| Algoritmus hash| SHA 256 | SHA 256 | SHA 1 |
| Skupina Diffie Hellman (Skupina DH) | 2 | 2 | 2 |
| Doba života | 28 800 sekund | 28 800 sekund | 28 800 sekund |
| Velikost dat | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>Návrhy 2. fáze

| Parametr | Návrh 1 | Návrh 2 | Návrh 3 |
|-----------|------------|------------|------------|
| Encryption | AES 128 | AES 256 | AES 256 |
| Algoritmus hash| SHA 256 | SHA 256 | SHA 1 |
| Skupina PFS (Perfect Forward Secrecy) | Ne | Ne | Ne |
| Doba života | 1 800 sekund | 1 800 sekund | 1 800 sekund |
| Velikost dat | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> Na zařízení VPN nastavte připojení TCP MSS na 1200. Nebo pokud vaše zařízení VPN nepodporují svorky MSS, můžete místo toho nastavit jednotku MTU v rozhraní tunelu na 1240 bajtů.

## <a name="point-to-site-vpn-gateway"></a>Brána sítě VPN typu Point-to-site

SÍŤ VPN typu Point-to-site slouží k posílání šifrovaného provozu mezi sítí v CloudSimple oblasti a klientským počítačem.  Síť VPN typu Point-to-site je nejjednodušší způsob, jak získat přístup k síti privátního cloudu, včetně virtuálních počítačů vCenter a zatížení vašeho privátního cloudu.  Pokud se připojujete k privátnímu cloudu vzdáleně, použijte připojení VPN typu Point-to-site.

## <a name="next-steps"></a>Další kroky

* [Nastavení služby VPN Gateway](vpn-gateway.md)
