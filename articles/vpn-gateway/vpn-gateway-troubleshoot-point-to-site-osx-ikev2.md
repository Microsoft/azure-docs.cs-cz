---
title: Řešení potíží s Azure Point-to-Site VPN připojení z klientů se systémem Mac OS X | Microsoft Docs
description: Postup řešení potíží s P2S Mac OS X klienta sítě VPN
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
ms.openlocfilehash: 1cf8195cbf65f27c71a4db18c0c61c8a25673acd
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Řešení potíží s připojení Point-to-Site VPN z klientů se systémem Mac OS X VPN

Tento článek vám pomůže vyřešit potíže s připojením Point-to-Site z Mac OS X pomocí nativní klient VPN a IKEv2. Klient VPN ve Mac pro IKEv2 je velmi základní a není povolena pro mnoho přizpůsobení. Existují pouze čtyři nastavení, které je třeba ověřit:

* Adresa serveru
* Vzdálené ID
* Místní ID
* Nastavení ověřování
* Verze operačního systému (10.11 nebo vyšší)


## <a name="VPNClient"></a> Řešení potíží s ověřování pomocí certifikátů
1. Zkontrolujte nastavení klienta VPN. Přejděte na **nastavení sítě** stisknutím příkaz + Shift a pak zadejte "VPN" Zkontrolujte nastavení klienta VPN. Ze seznamu klikněte na položku VPN, musí být prozkoumat.

  ![Ověřování pomocí certifikátů IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Ověřte, zda **adresu serveru** je plně kvalifikovaný název a obsahuje cloudapp.net.
3. **Vzdáleného ID** by měla být stejná jako adresa serveru (brány FQDN).
4. **Místní ID** by měla být stejná jako **subjektu** certifikátu klienta.
5. Klikněte na **nastavení ověřování** otevřete stránku nastavení ověřování.

  ![Nastavení ověřování](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Ověřte, že **certifikát** je vybraný z rozevíracího seznamu.
7. Klikněte **vyberte** tlačítko a ověřte, zda je vybrán správný certifikát. Klikněte na tlačítko **OK** uložte provedené změny.

## <a name="ikev2"></a>Řešení potíží s ověřování uživatelským jménem a heslem.

1. Zkontrolujte nastavení klienta VPN. Přejděte na **nastavení sítě** stisknutím příkaz + Shift a pak zadejte "VPN" Zkontrolujte nastavení klienta VPN. Ze seznamu klikněte na položku VPN, musí být prozkoumat.

  ![Uživatelské jméno heslo IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Ověřte, zda **adresu serveru** je plně kvalifikovaný název a obsahuje cloudapp.net.
3. **Vzdáleného ID** by měla být stejná jako adresa serveru (brány FQDN).
4. **Místní ID** může být prázdné.
5. Klikněte **nastavení ověřování** tlačítko a ověřte, zda je vybrán "Username" z rozevíracího seznamu.

  ![Nastavení ověřování](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.jpg)
6. Ověřte, zda jsou zadali správné přihlašovací údaje.

## <a name="additional"></a>Další kroky

Pokud je všechno správně nastavené, opakujte předchozí kroky Stáhnout [Wireshark](https://www.wireshark.org/#download) a provádět zachytáváním paketů.

1. Filtrovat *iskmp* a podívejte se na **IKE_SA** paketů. Nyní byste měli mít podívejte se na podrobnosti návrhu SA v části **datové části: přidružení zabezpečení**. 
2. Ověřte, zda klient a server mají společnou sadu.

  ![Paketu](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg)

## <a name="next-steps"></a>Další postup
Další nápovědu najdete v tématu [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
