---
title: 'Azure VPN Gateway: řešení potíží s připojením Point-to-site: Klienti Mac OS X'
description: Postup řešení potíží s připojeními P2S Mac OS X VPN klienta
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: f88053c93884e10e46a0f7d70106bda67b057562
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425713"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Řešení potíží s připojeními Point-to-Site VPN z klientů se systémem Mac OS X VPN

Tento článek pomáhá řešit problémy s připojením Point-to-Site v Mac OS X pomocí nativního klienta VPN a IKEv2. Klient VPN v systému Mac pro IKEv2 je velmi základní a neumožňuje moc přizpůsobení. Jsou pouze čtyři nastavení, které je potřeba zkontrolovat:

* Adresa serveru
* Vzdálený ID
* Místní ID
* Nastavení ověřování
* Verze operačního systému (10.11 nebo novější)


## <a name="VPNClient"></a>Řešení potíží s ověřováním na základě certifikátů
1. Zkontrolujte nastavení klienta VPN. Stisknutím příkazu + SHIFT a zadáním "VPN" **Zkontrolujte nastavení klienta** VPN. Ze seznamu klikněte na položku VPN, který je nutné prozkoumat.

   ![Ověřování na základě protokolu IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Ověřte, že **Adresa serveru** je úplný plně kvalifikovaný název domény a obsahuje cloudapp.NET.
3. **ID vzdáleného úložiště** by mělo být stejné jako adresa serveru (plně kvalifikovaný název domény brány).
4. **Místní ID** by mělo být stejné jako **Předmět** klientského certifikátu.
5. Kliknutím na **nastavení ověřování** otevřete stránku nastavení ověřování.

   ![Nastavení ověřování](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Ověřte, že je v rozevíracím seznamu vybraný **certifikát** .
7. Klikněte na tlačítko **Vybrat** a ověřte, zda je vybrán správný certifikát. Kliknutím na tlačítko **OK** uložte změny.

## <a name="ikev2"></a>Řešení potíží s ověřováním uživatelského jména a hesla

1. Zkontrolujte nastavení klienta VPN. Stisknutím příkazu + SHIFT a zadáním "VPN" **Zkontrolujte nastavení klienta** VPN. Ze seznamu klikněte na položku VPN, který je nutné prozkoumat.

   ![IKEv2 uživatelského jména hesla](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Ověřte, že **Adresa serveru** je úplný plně kvalifikovaný název domény a obsahuje cloudapp.NET.
3. **ID vzdáleného úložiště** by mělo být stejné jako adresa serveru (plně kvalifikovaný název domény brány).
4. **Místní ID** může být prázdné.
5. Klikněte na tlačítko **nastavení ověřování** a ověřte, že je v rozevíracím seznamu vybraná možnost uživatelské jméno.

   ![Nastavení ověřování](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. Ověřte, že jsou zadané správné přihlašovací údaje.

## <a name="additional"></a>Další kroky

Pokud se pokusíte provést předchozí kroky a všechno je správně nakonfigurované, Stáhněte si nástroj [Wireshark](https://www.wireshark.org/#download) a proveďte zachytávání paketů.

1. Vyfiltrujte protokol *ISAKMP* a podívejte se na **IKE_SA** pakety. Měli byste být schopni se podívat na podrobnosti o návrhu SA v části **datová část: přidružení zabezpečení**. 
2. Ověřte, že klient a server mají společnou sadu.

   ![paketů](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Pokud síťové trasování neobsahuje odpověď serveru, ověřte, že jste povolili protokol IKEv2 na stránce Konfigurace brány Azure na webu Azure Portal.

## <a name="next-steps"></a>Další kroky
Další nápovědu najdete v tématu [Podpora Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
