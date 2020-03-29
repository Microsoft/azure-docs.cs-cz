---
title: 'Brána Azure VPN: Poradce při potížích s připojením point-to-site: klienti Mac OS X'
description: Postup řešení potíží s připojením klientů VPN P2S Mac OS X
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: f88053c93884e10e46a0f7d70106bda67b057562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425713"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Poradce při potížích s připojením VPN z bodu na místo z klientů MAC OS X VPN

Tento článek vám pomůže vyřešit problémy s připojením bodu na web z Mac OS X pomocí nativního klienta VPN a IKEv2. Vpn klient v Macu pro IKEv2 je velmi základní a neumožňuje mnoho přizpůsobení. Existují pouze čtyři nastavení, která je třeba zkontrolovat:

* Adresa serveru
* ID vzdáleného id
* Místní ID
* Nastavení ověřování
* Verze operačního systému (10.11 nebo vyšší)


## <a name="troubleshoot-certificate-based-authentication"></a><a name="VPNClient"></a>Poradce při potížích s ověřováním založeným na certifikátu
1. Zkontrolujte nastavení klienta VPN. Stisknutím kláves Command + Shift přejděte na **nastavení sítě** a zadejte příkaz VPN a zkontrolujte nastavení klienta VPN. V seznamu klikněte na položku VPN, kterou je třeba prozkoumat.

   ![Ověřování založené na certifikátech IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Ověřte, zda je **adresa serveru** úplným úplným názevový název souboru A obsahuje cloudapp.net.
3. **Vzdálené ID** by mělo být stejné jako adresa serveru (brána FQDN).
4. **Místní ID** by mělo být stejné jako **předmět** klientského certifikátu.
5. Kliknutím na **Nastavení ověřování** otevřete stránku Nastavení ověřování.

   ![Nastavení ověřování](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Ověřte, zda je v rozevíracím souboru vybrán **certifikát.**
7. Klepněte na tlačítko **Vybrat** a ověřte, zda je vybrán správný certifikát. Klepnutím na **tlačítko OK** uložíte všechny změny.

## <a name="troubleshoot-username-and-password-authentication"></a><a name="ikev2"></a>Poradce při potížích s ověřováním uživatelského jména a hesla

1. Zkontrolujte nastavení klienta VPN. Stisknutím kláves Command + Shift přejděte na **nastavení sítě** a zadejte příkaz VPN a zkontrolujte nastavení klienta VPN. V seznamu klikněte na položku VPN, kterou je třeba prozkoumat.

   ![IKEv2 uživatelské jméno heslo](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Ověřte, zda je **adresa serveru** úplným úplným názevový název souboru A obsahuje cloudapp.net.
3. **Vzdálené ID** by mělo být stejné jako adresa serveru (brána FQDN).
4. **Místní ID** může být prázdné.
5. Klepněte na tlačítko **Nastavení ověřování** a ověřte, zda je v rozevíracím souboru vybráno "Uživatelské jméno".

   ![Nastavení ověřování](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. Ověřte, zda jsou zadána správná pověření.

## <a name="additional-steps"></a><a name="additional"></a>Další kroky

Pokud se pokusíte předchozí kroky a vše je správně nakonfigurováno, stáhněte [wireshark](https://www.wireshark.org/#download) a proveďte zachycení paketů.

1. Filtrujte *na isakmp* a podívejte se na **IKE_SA** pakety. Měli byste být schopni se podívat na podrobnosti návrhu přidružení zabezpečení v části **Datové části: Přidružení zabezpečení**. 
2. Ověřte, zda mají klient a server společnou sadu.

   ![Paket](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Pokud na trasování sítě není žádná odezva serveru, ověřte, zda jste povolili protokol IKEv2 na stránce Konfigurace brány Azure na webu portálu Azure.

## <a name="next-steps"></a>Další kroky
Další nápovědu naleznete v [tématu Podpora společnosti Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
