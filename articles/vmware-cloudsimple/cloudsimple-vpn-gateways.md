---
title: Řešení Azure VMware od CloudSimple – brány VPN
description: Další informace o konceptech VPN mezi lokalitami cloud-to-site a point-to-site VPN
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 662fa342b3a18f726b418c496ff3fda937445301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024853"
---
# <a name="vpn-gateways-overview"></a>Přehled bran VPN

Brána VPN se používá k odesílání šifrovaného provozu mezi sítí oblasti CloudSimple v místním umístění nebo počítačem přes veřejný internet.  Každá oblast může mít jednu bránu VPN, která může podporovat více připojení. Když vytvoříte několik připojení ke stejné bráně VPN, všechny tunely VPN sdílejí dostupnou šířku pásma.

CloudSimple poskytuje dva druhy bran VPN:

* Brána VPN site-to-site
* Brána VPN point-to-site

## <a name="site-to-site-vpn-gateway"></a>Brána VPN site-to-site

Brána VPN site-to-site se používá k odesílání šifrovaného provozu mezi sítí oblasti CloudSimple a místním datovým centrem. Toto připojení slouží k definování rozsahu podsítí/CIDR pro síťový provoz mezi místní sítí a sítí cloudových oblastí.

Brána VPN umožňuje využívat služby z místního prostředí ve vašem privátním cloudu a služby ve vašem privátním cloudu z místní sítě.  CloudSimple poskytuje server VPN založený na zásadách pro navázání připojení z místní sítě.

Případy použití sítě VPN mezi lokalitami:

* Přístupnost vašeho privátního cloudu vCenter z libovolné pracovní stanice v místní síti.
* Použití místní služby Active Directory jako zdroje identity vCenter.
* Pohodlný přenos šablon virtuálních počítačů, ISO a dalších souborů z místních prostředků do vašeho privátního cloudu vCenter.
* Dostupnost úloh spuštěných ve vašem privátním cloudu z místní sítě.

![Topologie připojení VPN site-to-Site](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Kryptografické parametry

Připojení VPN mezi lokalitami používá k navázání zabezpečeného připojení následující výchozí kryptografické parametry.  Při vytváření připojení z místního zařízení VPN použijte některý z následujících parametrů, které podporuje vaše místní brána VPN.

#### <a name="phase-1-proposals"></a>Návrhy fáze 1

| Parametr | Návrh 1 | Návrh 2 | Návrh 3 |
|-----------|------------|------------|------------|
| Verze IKE | IKEv1 | IKEv1 | IKEv1 |
| Šifrování | AES 128 | AES 256 | AES 256 |
| Algoritmus hash| ŠA 256 | ŠA 256 | ŠA 1 |
| Diffie Hellman Group (skupina DH) | 2 | 2 | 2 |
| Životnost | 28 800 sekund | 28 800 sekund | 28 800 sekund |
| Velikost dat | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>Návrhy fáze 2

| Parametr | Návrh 1 | Návrh 2 | Návrh 3 |
|-----------|------------|------------|------------|
| Šifrování | AES 128 | AES 256 | AES 256 |
| Algoritmus hash| ŠA 256 | ŠA 256 | ŠA 1 |
| Perfect Forward Secrecy Group (PFS Group) | Žádný | Žádný | Žádný |
| Životnost | 1 800 sekund | 1 800 sekund | 1 800 sekund |
| Velikost dat | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> Nastavte tcp MSS upnutí na 1200 na vašem zařízení VPN. Nebo pokud vaše zařízení VPN nepodporují upínání MSS, můžete alternativně nastavit MTU na rozhraní tunelu na 1240 bajtů.

## <a name="point-to-site-vpn-gateway"></a>Brána VPN point-to-site

Síť VPN point-to-site se používá k odesílání šifrovaného provozu mezi sítí cloudsimple region a klientským počítačem.  Point-to-Site VPN je nejjednodušší způsob, jak získat přístup k privátní cloudové síti, včetně virtuálních počítačích privátního cloudu a virtuálních stránek pracovního vytížení.  Připojení VPN z bodu na místo použijte, pokud se připojujete k privátnímu cloudu na dálku.

## <a name="next-steps"></a>Další kroky

* [Nastavení brány VPN](vpn-gateway.md)
