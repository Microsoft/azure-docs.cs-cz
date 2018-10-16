---
title: Řešení potíží s připojeními Azure Point-to-Site VPN z klientů se systémem Mac OS X | Dokumentace Microsoftu
description: Postup řešení potíží s připojeními P2S Mac OS X VPN klienta
services: vpn-gateway
documentationcenter: na
author: anzaman
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: c370808d06f31f9d79c99ca0f20f613d9e335d60
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339046"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Řešení potíží s připojeními Point-to-Site VPN z klientů se systémem Mac OS X VPN

Tento článek pomáhá řešit problémy s připojením Point-to-Site v Mac OS X pomocí nativního klienta VPN a IKEv2. Klient VPN v systému Mac pro IKEv2 je velmi základní a neumožňuje moc přizpůsobení. Jsou pouze čtyři nastavení, které je potřeba zkontrolovat:

* Adresa serveru
* Vzdálený ID
* Místní ID
* Nastavení ověřování
* Verze operačního systému (10.11 nebo novější)


## <a name="VPNClient"></a> Řešení potíží s ověřováním prostřednictvím certifikátů
1. Zkontrolujte nastavení klienta VPN. Přejděte **nastavení sítě** stisknutím kombinace kláves příkaz + Shift a pak zadejte "VPN" Zkontrolujte nastavení klienta VPN. Ze seznamu klikněte na položku VPN, který je nutné prozkoumat.

  ![Ověřování na základě protokolu IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Ověřte, že **adresa serveru** je plně kvalifikovaný název a obsahuje cloudapp.net.
3. **Vzdáleného ID** by měla být stejná jako adresa serveru (FQDN brány).
4. **Místní ID** by měl být stejný jako **subjektu** klientského certifikátu.
5. Klikněte na **nastavení ověřování** otevřete stránku nastavení ověřování.

  ![Nastavení ověřování](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Ověřte, že **certifikát** vybraný z rozevíracího seznamu.
7. Klikněte na tlačítko **vyberte** tlačítko a ověřte, zda je vybrána správný certifikát. Klikněte na tlačítko **OK** ukládat změny.

## <a name="ikev2"></a>Řešení potíží s ověřování uživatelským jménem a heslem.

1. Zkontrolujte nastavení klienta VPN. Přejděte **nastavení sítě** stisknutím kombinace kláves příkaz + Shift a pak zadejte "VPN" Zkontrolujte nastavení klienta VPN. Ze seznamu klikněte na položku VPN, který je nutné prozkoumat.

  ![IKEv2 uživatelského jména hesla](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Ověřte, že **adresa serveru** je plně kvalifikovaný název a obsahuje cloudapp.net.
3. **Vzdáleného ID** by měla být stejná jako adresa serveru (FQDN brány).
4. **Místní ID** může být prázdné.
5. Klikněte na tlačítko **nastavení ověřování** tlačítko a ověřte, že "Username" je vybraný z rozevíracího seznamu.

  ![Nastavení ověřování](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.jpg)
6. Ověřte, že jsou zadané správné přihlašovací údaje.

## <a name="additional"></a>Další kroky

Pokud je všechno správně nastavené, opakujte předchozí kroky Stáhnout [Wireshark](https://www.wireshark.org/#download) a proveďte zachycení paketů.

1. Filtrovat podle *isakmp* a podívejte se na **IKE_SA** paketů. Je třeba podívat na podrobnosti návrh přidružení zabezpečení v rámci **datová část: přidružení zabezpečení**. 
2. Ověřte, že klient a server mají společnou sadu.

  ![paketů](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Pokud se v trasování sítě nepřijde žádná odpověď serveru, ověřte, že jste povolili protokol IKEv2 na stránce konfigurace brány Azure na webu Azure Portal.

## <a name="next-steps"></a>Další postup
Další nápovědu najdete v tématu [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
