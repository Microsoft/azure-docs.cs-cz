---
title: 'Azure VPN Gateway: řešení potíží s připojením Point-to-site: Klienti Mac OS X'
description: Naučte se řešit potíže s připojením typu Point-to-Site z Mac OS X pomocí nativního klienta VPN a IKEv2.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: 33e67df5ad68a78cc35f7168813716d1d794d527
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442798"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Řešení potíží s připojením VPN typu Point-to-Site z Mac OS X klientů VPN

Tento článek vám pomůže vyřešit problémy s připojením typu Point-to-Site z Mac OS X pomocí nativního klienta VPN a IKEv2. Klient VPN v Mac pro IKEv2 je velmi základní a nepovoluje mnoho úprav. Je třeba zkontrolovat pouze čtyři nastavení:

* Adresa serveru
* Vzdálené ID
* Místní ID
* Nastavení ověřování
* Verze operačního systému (10,11 nebo vyšší)


## <a name="troubleshoot-certificate-based-authentication"></a><a name="VPNClient"></a> Řešení potíží s ověřováním na základě certifikátů
1. Ověřte nastavení klienta VPN. Stisknutím příkazu + SHIFT a zadáním "VPN" **Zkontrolujte nastavení klienta** VPN. V seznamu klikněte na položku sítě VPN, kterou je třeba prozkoumat.

   ![Ověřování založené na certifikátech IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Ověřte, že **Adresa serveru** je úplný plně kvalifikovaný název domény a obsahuje cloudapp.NET.
3. **ID vzdáleného úložiště** by mělo být stejné jako adresa serveru (plně kvalifikovaný název domény brány).
4. **Místní ID** by mělo být stejné jako **Předmět** klientského certifikátu.
5. Kliknutím na **nastavení ověřování** otevřete stránku nastavení ověřování.

   ![Nastavení ověřování](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Ověřte, že je v rozevíracím seznamu vybraný **certifikát** .
7. Klikněte na tlačítko **Vybrat** a ověřte, zda je vybrán správný certifikát. Kliknutím na tlačítko **OK** uložte změny.

## <a name="troubleshoot-username-and-password-authentication"></a><a name="ikev2"></a>Řešení potíží s ověřováním uživatelského jména a hesla

1. Ověřte nastavení klienta VPN. Stisknutím příkazu + SHIFT a zadáním "VPN" **Zkontrolujte nastavení klienta** VPN. V seznamu klikněte na položku sítě VPN, kterou je třeba prozkoumat.

   ![Heslo pro uživatelské jméno IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Ověřte, že **Adresa serveru** je úplný plně kvalifikovaný název domény a obsahuje cloudapp.NET.
3. **ID vzdáleného úložiště** by mělo být stejné jako adresa serveru (plně kvalifikovaný název domény brány).
4. **Místní ID** může být prázdné.
5. Klikněte na tlačítko **nastavení ověřování** a ověřte, že je v rozevíracím seznamu vybraná možnost uživatelské jméno.

   ![Nastavení ověřování](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. Ověřte, zda jsou zadány správné přihlašovací údaje.

## <a name="additional-steps"></a><a name="additional"></a>Další kroky

Pokud se pokusíte provést předchozí kroky a všechno je správně nakonfigurované, Stáhněte si nástroj [Wireshark](https://www.wireshark.org/#download) a proveďte zachytávání paketů.

1. Vyfiltrujte protokol *ISAKMP* a podívejte se na **IKE_SA** pakety. Měli byste být schopni se podívat na podrobnosti o návrhu SA v části **datová část: přidružení zabezpečení**. 
2. Ověřte, že klient a Server mají společnou sadu.

   ![prostřednictvím](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Pokud síťové trasování neobsahuje odpověď serveru, ověřte, že jste povolili protokol IKEv2 na stránce Konfigurace brány Azure na webu Azure Portal.

## <a name="next-steps"></a>Další kroky
Další nápovědu najdete v tématu [Podpora Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
