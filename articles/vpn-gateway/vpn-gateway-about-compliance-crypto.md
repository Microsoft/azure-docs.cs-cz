---
title: O kryptografických požadavcích a Azure VPN Gateway | Microsoft Docs
description: Tento článek popisuje požadavky na šifrování a brány Azure VPN
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2017
ms.author: yushwang
ms.openlocfilehash: d2f3da47f1d4eebe1b81964790ff6612dd78155d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "23884248"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>O kryptografických požadavky a brány Azure VPN

Tento článek popisuje, jak můžete nakonfigurovat Azure VPN Gateway pro uspokojení požadavků kryptografických tunelů S2S VPN mezi různými místy a připojení VNet-to-VNet v rámci Azure. 

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>O parametry zásad protokolu IPsec a IKE pro Azure VPN Gateway
Protokol IPsec a IKE standardní podporuje širokou škálu kryptografických algoritmů v různých kombinacích. Pokud zákazníci v požadavku konkrétní kombinaci kryptografické algoritmy a parametry, Azure VPN Gateway pomocí sady výchozí návrhů. Sady výchozí zásady byly vybrány a maximalizovat tak vzájemnou spolupráci s širokou škálu zařízení VPN třetích stran v výchozí konfigurace. Zásady a počet návrhů v důsledku toho nemůže zahrnovat všechny možné kombinace dostupné kryptografické algoritmy a klíče síly.

Výchozí zásady nastavení pro bránu Azure VPN je uveden v dokumentu: [informace o zařízeních VPN a parametry protokolu IPsec/IKE pro připojení Site-to-Site VPN Gateway](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Kryptografické požadavky
Pro komunikaci, které vyžadují určité kryptografické algoritmy nebo parametry obvykle kvůli dodržování předpisů nebo požadavky na zabezpečení, zákazníci teď můžete nakonfigurovat jejich Azure VPN Gateway používat vlastní zásady protokolu IPsec/IKE službou konkrétní kryptografické algoritmy a klíče síly, nikoli nastaví Azure výchozí zásady.

Například zásady hlavního režimu IKEv2 pro Azure VPN Gateway využívat pouze Diffie-Hellman Skupina 2 (1 024 bitů), zatímco zákazníkům možná muset zadat silnější skupin, které se použijí v IKE, jako je například Skupina 14 (2048 bitů), 24 skupinu (skupiny MODP 2048 bitů) nebo ECP (elliptic curve skupiny) 256 nebo 384 bit (skupiny 19 a skupiny 20). Podobné požadavky platí pro také zásady rychlého režimu IPsec.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Vlastní zásady protokolu IPsec/IKE pomocí bran Azure VPN
Azure VPN Gateway teď podporují připojení, vlastní zásady protokolu IPsec/IKE. Site-to-Site nebo připojení VNet-to-VNet lze vybrat konkrétní kombinaci kryptografických algoritmů IPsec a IKE s požadovanou síly klíče, jak je znázorněno v následujícím příkladu:

![ipsec-ike-policy](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Můžete vytvořit zásady protokolu IPsec/IKE a použít pro nové nebo existující připojení. 

### <a name="workflow"></a>Pracovní postup

1. Vytvoření virtuální sítě, brány sítě VPN nebo brány místní sítě pro topologie připojení, jak je popsáno v dalších dokumenty s postupy
2. Vytvoření zásady protokolu IPsec/IKE
3. Zásady můžete použít při vytváření připojení S2S nebo VNet-to-VNet
4. Pokud je již vytvořili připojení, můžete použít nebo aktualizaci zásad na existující připojení


## <a name="ipsecike-policy-faq"></a>Zásady protokolu IPsec/IKE – nejčastější dotazy

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]


## <a name="next-steps"></a>Další postup
V tématu [zásady Konfigurace protokolu IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) podrobné pokyny ke konfiguraci vlastních zásad protokolu IPsec/IKE na připojení.

Viz také [připojení více zařízení sítě VPN založené na zásadách](vpn-gateway-connect-multiple-policybased-rm-ps.md) Další informace o možnost UsePolicyBasedTrafficSelectors.