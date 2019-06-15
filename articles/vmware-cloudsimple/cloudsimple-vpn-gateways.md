---
title: V řešení VMware podle CloudSimple - Azure VPN Gateway
description: Další informace o konceptech VPN point-to-site a site-to-site VPN CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c9689a468e8784eb4ec3590011e02a37d92d6b9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083403"
---
# <a name="vpn-gateways-overview"></a>Přehled bran VPN

VPN gateway slouží k posílání šifrovaného provozu mezi síť CloudSimple oblast v místním umístění nebo počítač prostřednictvím veřejného Internetu.  Každá oblast může mít pouze jednu bránu VPN. K jedné bráně VPN však můžete vytvořit několik připojení. Když vytvoříte několik připojení ke stejné bráně VPN, všechny tunely VPN sdílejí dostupnou šířku pásma.

CloudSimple nabízí dva typy brány sítě VPN:

* Brány VPN typu Site-to-site
* Point-to-site VPN Gateway

## <a name="site-to-site-vpn-gateway"></a>Brány VPN typu Site-to-site

Brány VPN typu site-to-site slouží k posílání šifrovaného provozu mezi CloudSimple oblasti sítí a místním datacentrem. Pro účely definování rozsahu podsítě/CIDR, pro síťový provoz mezi vaší místní sítí a sítí oblasti CloudSimple použijte toto připojení.

VPN gateway umožňuje využívat služby z místně na vašem privátním cloudu a služby v privátním cloudu, z místní sítě.  CloudSimple poskytuje serveru VPN na základě zásad pro vytvoření připojení z vaší místní sítě.

Případy použití pro síť VPN site-to-site zahrnují:

* Usnadnění vCenter privátního cloudu z libovolné pracovní stanice ve vaší místní síti.
* Použití místní Active Directory jako zdroj identity serveru vCenter.
* Pohodlné přenos šablony virtuálního počítače, soubory ISO a další soubory z vašich místních prostředků do privátního cloudu vCenter.
* Usnadnění úloh běžících na privátní cloud z vaší místní sítě.

![Topologie připojení VPN typu Site-to-Site](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Kryptografickým parametrům

Připojení VPN typu site-to-site používá následující kryptografické parametry výchozí vytvořit zabezpečené připojení.  Když vytvoříte připojení z místního zařízení VPN, musí odpovídat parametry.

Připojení VPN typu site-to-site používá následující kryptografické parametry výchozí vytvořit zabezpečené připojení.  Když vytvoříte připojení z místního zařízení VPN, použijte některý z následujících parametrů podporuje bránu místní sítě VPN.

#### <a name="phase-1-proposals"></a>Fáze 1 návrhy

| Parametr | Návrh 1 | Návrh 2 | Návrh 3 |
|-----------|------------|------------|------------|
| Verze IKE | IKEv1 | IKEv1 | IKEv1 |
| Šifrování | AES 128 | AES 256 | AES 256 |
| Hashovací algoritmus| SHA 256 | SHA 256 | SHA 1 |
| Skupina Diffie Hellman (skupiny DH) | 2 | 2 | 2 |
| Doba životnosti | 28 800 sekund | 28 800 sekund | 28 800 sekund |
| Velikost dat | 4 GB | 4 GB | 4 GB |


#### <a name="phase-2-proposals"></a>Fáze 2 návrhy 

| Parametr | Návrh 1 | Návrh 2 | Návrh 3 |
|-----------|------------|------------|------------|
| Šifrování | AES 128 | AES 256 | AES 256 |
| Hashovací algoritmus| SHA 256 | SHA 256 | SHA 1 |
| Ideální vpřed Secrecy (PFS skupiny) | Žádný | Žádný | Žádný |
| Doba životnosti | 1800 sekund | 1800 sekund | 1800 sekund |
| Velikost dat | 4 GB | 4 GB | 4 GB |

## <a name="point-to-site-vpn-gateway"></a>Brány VPN typu Point-to-site

Síť point-to-site VPN se používá k posílání šifrovaného provozu mezi CloudSimple oblasti sítí a klientský počítač.  Point-to-site VPN je nejjednodušší způsob, jak přistupovat k vaší síti privátního cloudu, včetně privátního cloudu vCenter a úloh virtuálních počítačů.  Připojení k síti VPN point-to-site použijte, pokud se připojujete k privátní cloud vzdáleně.

## <a name="next-steps"></a>Další postup

* [Nastavení brány sítě VPN](https://docs.azure.cloudsimple.com/vpn-gateway/)