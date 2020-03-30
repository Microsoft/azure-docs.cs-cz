---
title: Požadavky na zřizování cloudu Azure AD Connect ve službě Azure AD
description: Tento článek popisuje požadavky na požadavky a požadavky na hardware, které potřebujete pro zřizování cloudu.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45648170f69d513b15e79cdd76f56e66bbc88bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332085"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Požadavky na zřizování cloudu Azure AD Connect
Tento článek obsahuje pokyny, jak vybrat a používat Azure Active Directory (Azure AD) Connect cloud zřizování jako vaše řešení identity.



## <a name="cloud-provisioning-agent-requirements"></a>Požadavky na agenta zřizování cloudu
K používání zřizování cloudu Azure AD Connect potřebujete následující:
    
- Globální účet správce pro vašeho klienta Azure AD, který není uživatelem typu Host.
- Místní server pro zřizovacího agenta se systémem Windows 2012 R2 nebo novějším.
- Místní konfigurace brány firewall.

>[!NOTE]
>Zřizovacíagent lze aktuálně nainstalovat pouze na servery v angličtině. Instalace anglické jazykové sady na neanglický server není platným zástupným řešením a bude mít za následek, že se agentovi nepodaří nainstalovat. 

Zbytek dokumentu obsahuje podrobné pokyny pro tyto požadavky.

### <a name="in-the-azure-active-directory-admin-center"></a>V Centru pro správu Služby Azure Active Directory

1. Vytvořte účet globálního správce pouze pro cloud v tenantovi Azure AD. Tímto způsobem můžete spravovat konfiguraci vašeho tenanta, pokud vaše místní služby selžou nebo se stanou nedostupnými. Přečtěte si, jak [přidat účet globálního správce pouze pro cloud](../active-directory-users-create-azure-portal.md). Dokončení tohoto kroku je důležité zajistit, že se nedostanete uzamčenz vašeho tenanta.
1. Přidejte do klienta Azure AD jeden nebo více [vlastních názvů domén.](../active-directory-domains-add-azure-portal.md) Uživatelé se mohou přihlásit pomocí jednoho z těchto názvů domén.

### <a name="in-your-directory-in-active-directory"></a>V adresáři ve službě Active Directory

Spusťte [nástroj IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) a připravte atributy adresáře pro synchronizaci.

### <a name="in-your-on-premises-environment"></a>V místním prostředí

1. Identifikujte hostitelský server spojený s doménou se systémem Windows Server 2012 R2 nebo vyšším s minimálně 4 GB paměti RAM a za běhu .NET 4.7.1+.

1. Pokud je mezi servery a službou Azure AD brána firewall, nakonfigurujte následující položky:
   - Ujistěte se, že agenti mohou provádět *odchozí* požadavky na Azure AD přes následující porty:

        | Číslo portu | Jak se používá |
        | --- | --- |
        | **80** | Stáhne seznamy odvolaných certifikátů (CRL) při ověřování certifikátu TLS/SSL.  |
        | **443** | Zpracovává veškerou odchozí komunikaci se službou. |
        | **8080** (volitelně) | Agenti hlásí svůj stav každých 10 minut přes port 8080, pokud port 443 není k dispozici. Tento stav se zobrazí na portálu Azure AD. |
     
   - Pokud brána firewall vynucuje pravidla podle původních uživatelů, otevřete tyto porty pro přenosy ze služeb systému Windows, které běží jako síťová služba.
   - Pokud brána firewall nebo proxy server umožňuje zadat bezpečné \*přípony, přidejte připojení k servicebus.windows.net .msappproxy.net a \*.servicebus.windows.net. Pokud ne, povolte přístup k [rozsahům IP adres datového centra Azure](https://www.microsoft.com/download/details.aspx?id=41653), které se aktualizují každý týden.
   - Vaši agenti potřebují přístup k login.windows.net a login.microsoftonline.com pro počáteční registraci. Otevřete také bránu firewall pro tyto adresy URL.
   - Pro ověření certifikátu odblokujte následující adresy URL: mscrl.microsoft.com:80,\.crl.microsoft.com:80, ocsp.msocsp.com:80 a www microsoft.com:80. Tyto adresy URL se používají pro ověření certifikátu s jinými produkty společnosti Microsoft, takže již je možné tyto adresy URL odblokovat.

### <a name="verify-the-port"></a>Ověření portu
Chcete-li ověřit, že Azure naslouchá na portu 443 a že s ním váš agent může komunikovat, použijte následující adresu URL:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Tento test ověří, že vaši agenti mohou komunikovat s Azure přes port 443. Otevřete prohlížeč a přejděte na předchozí adresu URL ze serveru, na kterém je agent nainstalován.

![Ověření dosažitelnosti portu](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>Dodatečné požadavky
- [Rozhraní Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Požadavky na TLS

>[!NOTE]
>Zabezpečení transportní vrstvy (TLS) je protokol, který zajišťuje zabezpečenou komunikaci. Změna nastavení TLS ovlivní celou doménovou strukturu. Další informace naleznete v [tématu Update, které povolují protokoly TLS 1.1 a TLS 1.2 jako výchozí zabezpečené protokoly ve winhttp u windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

Server Windows, který je hostitelem agenta cloudu Azure AD Connect, musí mít před instalací povolenou technologii TLS 1.2.

Chcete-li povolit TLS 1.2, postupujte takto.

1. Nastavte následující klíče registru:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Restartujte server.


## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)

