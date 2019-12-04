---
title: Předpoklady pro zřizování cloudu Azure AD Connect ve službě Azure AD
description: V tomto tématu jsou popsány předpoklady a hardwarové požadavky na zřízení cloudu.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 155edf72a60e079a609853e953e3cf66024cc83c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794247"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Předpoklady pro zřizování cloudu Azure AD Connect
Toto téma poskytuje pokyny k výběru a používání zřizování cloudu Azure AD Connect jako řešení identity.



## <a name="cloud-provisioning-agent-requirements"></a>Požadavky na agenta zřizování cloudu
Pro použití Azure AD Connectho zřizování cloudu budete potřebovat následující:
    
- účet globálního správce pro vašeho tenanta Azure AD
- místní server pro zřizovacího agenta se systémem Windows 2012 R2 nebo novějším
- Konfigurace místních bran firewall

Zbývající část dokumentu vám poskytne podrobné pokyny pro tyto požadavky.

### <a name="in-the-azure-active-directory-admin-center"></a>V centru pro správu Azure Active Directory

1. Vytvořte v tenantovi Azure AD jenom cloudový účet globálního správce. Tímto způsobem můžete spravovat konfiguraci vašeho tenanta, pokud vaše místní služby selžou nebo nebudou k dispozici. Seznamte [se s přidáním účtu globálního správce jenom pro Cloud](../active-directory-users-create-azure-portal.md). Dokončení tohoto kroku je důležité, aby se zajistilo, že nebudete mít uzamčený přístup k vašemu tenantovi.
2. Přidejte jeden nebo více [vlastních názvů domén](../active-directory-domains-add-azure-portal.md) do svého tenanta služby Azure AD. Uživatelé se můžou přihlásit pomocí některého z těchto názvů domén.

### <a name="in-your-on-premises-environment"></a>V místním prostředí

1. Identita hostitelského serveru připojeného k doméně, na kterém běží Windows Server 2012 R2 nebo novější, minimálně 4 GB paměti RAM a .NET 4.7.1 + runtime 

2. Pokud je mezi vašimi servery a službou Azure AD brána firewall, nakonfigurujte následující položky:
   - Zajistěte, aby agenti mohli vytvářet *odchozí* požadavky do služby Azure AD prostřednictvím následujících portů:

     | Číslo portu | Jak se používá |
     | --- | --- |
     | **80** | Stahuje seznamy odvolaných certifikátů (CRL) při ověřování certifikátu SSL. |
     | **443** | Zpracovává veškerou odchozí komunikaci se službou. |
     | **8080** (volitelné) | Agenti hlásí svůj stav každých deset minut přes port 8080, pokud není k dispozici port 443. Tento stav se zobrazuje na portálu Azure AD. Port _8080 se nepoužívá pro_ přihlášení uživatelů. |
     
     Pokud brána firewall vynutila pravidla podle prvotních uživatelů, otevřete tyto porty pro provoz ze služeb systému Windows, které jsou spuštěny jako síťová služba.
   - Pokud vaše brána firewall nebo proxy server umožňují zadat bezpečné přípony, přidejte připojení k **\*. msappproxy.NET** a **\*. ServiceBus.Windows.NET**. V takovém případě povolte přístup k [rozsahům IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653), které se aktualizují týdně.
   - Vaši agenti potřebují přístup k **Login.Windows.NET** a **Login.microsoftonline.com** pro počáteční registraci. Otevřete taky bránu firewall pro tyto adresy URL.
   - Pro ověření certifikátu Odblokujte následující adresy URL: **mscrl.Microsoft.com:80**, **CRL.Microsoft.com:80**, **OCSP.msocsp.com:80**a **www\.Microsoft.com:80**. Vzhledem k tomu, že se tyto adresy URL používají pro ověřování certifikátů s jinými produkty Microsoftu, tyto adresy URL už možná máte odblokované.

### <a name="verify-the-port"></a>Ověření portu
Pokud chcete ověřit, že Azure naslouchá na portu 443 a že s ním může komunikovat agent, můžete použít následující:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Tento test ověří, jestli můžou vaši agenti komunikovat s Azure přes port 443.  Otevřete prohlížeč a přejděte na adresu URL na serveru, na kterém je nainstalovaný agent.
![Služby](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>Další požadavky
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Požadavky TLS

>[!NOTE]
>Protokol TLS (Transport Layer Security) je protokol, který zajišťuje zabezpečenou komunikaci.  Změna nastavení TLS má vliv na celou doménovou strukturu.  Další informace najdete v tématu [aktualizace povolení tls 1,1 a tls 1,2 jako výchozích zabezpečených protokolů v WinHTTP v systému Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) .

Systém Windows Server, který bude hostitelem agenta pro zřizování Azure AD Connect cloudu, musí mít povolený protokol TLS 1,2, než ho nainstalujete.

Povolení TLS 1,2:

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
- [Co je zřizování cloudu Azure AD Connect?](what-is-cloud-provisioning.md)

