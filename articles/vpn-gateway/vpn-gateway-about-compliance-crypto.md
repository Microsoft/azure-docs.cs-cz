---
title: Informace o kryptografických požadavcích a branách Azure VPN Gateway | Dokumentace Microsoftu
description: Tento článek pojednává o kryptografických požadavcích a branách Azure VPN Gateway
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
ms.openlocfilehash: 060e647badcc3bad7b44d7cef3530c36b8ecdf57
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "23884248"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Informace o kryptografických požadavcích a branách Azure VPN Gateway

Tento článek popisuje, jak nakonfigurovat bran Azure VPN Gateway pro splnění požadavků na šifrování pro tunely S2S VPN mezi různými místy a připojení VNet-to-VNet v rámci Azure. 

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>O parametrech zásad protokolu IPsec a IKE pro brány Azure VPN Gateway
Protokol IPsec a IKE standard podporuje širokou škálu kryptografických algoritmů v různých kombinacích. Pokud zákazníci neuvede v požadavku konkrétní kombinaci kryptografických algoritmů a parametrů, pomocí Azure VPN Gateway sadu výchozí návrhy. Výchozí sady zásad byly vybrány pro maximalizaci vzájemná funkční spolupráce s širokou škálu zařízení VPN třetích stran ve výchozí konfiguraci. V důsledku toho se zásadami a počet návrhů pokrýt všechny možné kombinace dostupné kryptografické algoritmy a silami klíče.

Výchozí zásady pro Azure VPN gateway je uvedené v tomto dokumentu: [O zařízeních VPN a parametry protokolu IPsec/IKE pro připojení typu Site-to-Site VPN Gateway](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Kryptografických požadavcích
Pro komunikaci, které vyžadují konkrétní kryptografických algoritmů nebo parametrů obvykle kvůli dodržování předpisů nebo požadavky na zabezpečení, zákazníci teď můžou nakonfigurovat jejich bran Azure VPN k použití vlastních zásad IPsec/IKE s konkrétními kryptografických algoritmy a síly klíče, spíše než výchozí sady zásad Azure.

Například zásady hlavního režimu IKEv2 pro Azure VPN Gateway využívat pouze 2 skupiny Diffie-Hellman (1 024 bitů), zatímco zákazníci možná muset zadat silnější skupin, které se použijí v protokolu IKE, jako je například Skupina 14 (2 048 bitů), skupinu 24 (skupina MODP 2048 bitů) nebo ECP (elliptic křivka skupiny) 256 nebo 384 bitů (skupině 19 a 20 skupiny). Podobné požadavky platí pro také zásady rychlý režim protokolu IPsec.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Vlastní zásady IPsec/IKE s bránami Azure VPN Gateway
Azure VPN Gateway teď podporují připojení, vlastní zásady IPsec/IKE. Pro Site-to-Site a připojení VNet-to-VNet můžete vybrat konkrétní kombinaci kryptografické algoritmy pro protokol IPsec a IKE s požadovanou sílu klíče, jak je znázorněno v následujícím příkladu:

![zásady protokolu ike IPSec](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Můžete vytvořit zásady IPsec/IKE a použít na novou nebo existující připojení. 

### <a name="workflow"></a>Pracovní postup

1. Vytvoření virtuální sítě, VPN Gateway nebo brány místní sítě pro vaší topologie připojení, jak je popsáno v další dokumenty s postupy
2. Vytvoření zásady IPsec/IKE
3. Zásady můžete použít při vytváření připojení S2S nebo VNet-to-VNet
4. Pokud se připojení vytvoří, můžete použít nebo aktualizovat zásady na existující připojení


## <a name="ipsecike-policy-faq"></a>Nejčastější dotazy o zásadách IPsec/IKE

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]


## <a name="next-steps"></a>Další postup
Zobrazit [zásady Konfigurace protokolu IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) podrobné pokyny ke konfiguraci vlastních zásad IPsec/IKE na připojení.

Viz také [připojení několika zařízení VPN založené na zásadách](vpn-gateway-connect-multiple-policybased-rm-ps.md) Další informace týkající se možnosti UsePolicyBasedTrafficSelectors.