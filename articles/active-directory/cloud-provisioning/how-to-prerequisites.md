---
title: Předpoklady pro zřizování cloudu Azure AD Connect ve službě Azure AD
description: Tento článek popisuje požadavky a požadavky na hardware, které potřebujete při zřizování cloudu.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dbdd5153186ee47e37856637eac16d6d450cc5a
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695176"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Požadavky pro zřízení cloudu Azure AD Connect
Tento článek poskytuje pokyny k výběru a používání Azure Active Directory (Azure AD) připojení cloudového zřizování jako řešení identity.



## <a name="cloud-provisioning-agent-requirements"></a>Požadavky na agenta zřizování cloudu
Pro použití Azure AD Connectho zřizování cloudu potřebujete následující:
    
- Účet správce hybridní identity pro vašeho tenanta Azure AD, který není uživatelem typu Host.
- Místní server pro zřizovacího agenta se systémem Windows 2012 R2 nebo novějším.  Tento server by měl být serverem vrstvy 0, který je založený na [modelu vrstvy správy služby Active Directory](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- Místní konfigurace brány firewall.

>[!NOTE]
>Agent zřizování se momentálně dá nainstalovat jenom na anglické jazykové servery. Instalace anglické jazykové sady na neanglickém serveru nemá platné alternativní řešení a výsledkem bude selhání agenta při instalaci. 

Ve zbývající části dokumentu najdete podrobné pokyny pro tyto požadavky.

### <a name="in-the-azure-active-directory-admin-center"></a>V centru pro správu Azure Active Directory

1. Vytvořte v tenantovi Azure AD jenom cloudový účet správce hybridní identity. Tímto způsobem můžete spravovat konfiguraci tenanta, pokud vaše místní služby selžou nebo nebudou k dispozici. Přečtěte si, jak [Přidat účet jenom pro cloudovou hybridní identitu správce](../fundamentals/add-users-azure-active-directory.md). Dokončení tohoto kroku je důležité, aby se zajistilo, že nebudete mít uzamčený přístup k vašemu tenantovi.
1. Přidejte jeden nebo více [vlastních názvů domén](../fundamentals/add-custom-domain.md) do svého tenanta služby Azure AD. Uživatelé se můžou přihlásit pomocí některého z těchto názvů domén.

### <a name="in-your-directory-in-active-directory"></a>Ve vašem adresáři ve službě Active Directory

Pro přípravu atributů adresáře pro synchronizaci spusťte [Nástroj IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) .

### <a name="in-your-on-premises-environment"></a>V místním prostředí

1. Identifikujte hostitelský server připojený k doméně, na kterém běží Windows Server 2012 R2 nebo novější, s minimálním počtem 4 GB paměti RAM a .NET 4.7.1 + runtime.

1. Zásady spouštění PowerShellu na místním serveru musí být nastavené na undefined nebo RemoteSigned.

1. Pokud je mezi vašimi servery a službou Azure AD brána firewall, nakonfigurujte následující položky:
   - Zajistěte, aby agenti mohli vytvářet *odchozí* požadavky do služby Azure AD prostřednictvím následujících portů:

        | Číslo portu | Jak se používá |
        | --- | --- |
        | **80** | Při ověřování certifikátu TLS/SSL stáhne seznamy odvolaných certifikátů (CRL).  |
        | **443** | Zpracovává veškerou odchozí komunikaci se službou. |
        | **8080** (volitelné) | Agenti hlásí svůj stav každých 10 minut přes port 8080, pokud není k dispozici port 443. Tento stav se zobrazuje na portálu Azure AD. |
     
   - Pokud brána firewall vynutila pravidla podle prvotních uživatelů, otevřete tyto porty pro provoz ze služeb systému Windows, které jsou spuštěny jako síťová služba.
   - Pokud vaše brána firewall nebo proxy server umožňují zadat bezpečné přípony, přidejte připojení k \* příponám. msappproxy.NET a \* . ServiceBus.Windows.NET. V takovém případě povolte přístup k [rozsahům IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653), které se aktualizují týdně.
   - Vaši agenti potřebují přístup k login.windows.net a login.microsoftonline.com pro počáteční registraci. Otevřete taky bránu firewall pro tyto adresy URL.
   - Pro ověření certifikátu Odblokujte následující adresy URL: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 a www \. Microsoft.com:80. Tyto adresy URL se používají pro ověřování certifikátů s jinými produkty Microsoftu, takže je možné, že už tyto adresy URL máte odblokované.

>[!NOTE]
> Instalace agenta zřizování cloudu na Windows serveru Core není podporovaná.


### <a name="additional-requirements"></a>Další požadavky
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Požadavky TLS

>[!NOTE]
>TLS (Transport Layer Security) je protokol, který zajišťuje zabezpečenou komunikaci. Změna nastavení TLS má vliv na celou doménovou strukturu. Další informace najdete v tématu [aktualizace povolení tls 1,1 a tls 1,2 jako výchozích zabezpečených protokolů v WinHTTP v systému Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

Systém Windows Server, který je hostitelem Azure AD Connect agenta zřizování cloudu, musí mít povolený protokol TLS 1,2, než ho nainstalujete.

Pokud chcete povolit TLS 1,2, postupujte podle těchto kroků.

1. Nastavte následující klíče registru:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Restartujte server.

## <a name="known-limitations"></a>Známá omezení
Toto jsou známá omezení:

### <a name="delta-synchronization"></a>Rozdílová synchronizace

- Filtrování oboru skupiny pro rozdílovou synchronizaci nepodporuje víc než 1500 členů.
- Když odstraníte skupinu, která se používá jako součást filtru oboru skupiny, uživatelé, kteří jsou členy skupiny, se neodstraní. 
- Při přejmenování organizační jednotky nebo skupiny, která je v oboru, nebude rozdílová synchronizace uživatele odebírat.

### <a name="provisioning-logs"></a>Protokoly zřizování
- Protokoly zřizování nejsou jasně odlišené mezi operacemi vytvoření a aktualizace.  Může se zobrazit operace vytvoření aktualizace a operace aktualizace pro vytvoření.

### <a name="cross-domain-references"></a>Odkazy mezi doménami
- Pokud máte uživatele s odkazy na členy v jiné doméně, nebudou synchronizovány jako součást aktuální synchronizace domény pro tohoto uživatele. 
- (Příklad: Správce uživatele, který synchronizujete, je v doméně B a uživatel je v doméně A. Pokud synchronizujete doménu A a B, budou synchronizovány, ale uživatel – správce se nebude přenášet.)

### <a name="group-re-naming-or-ou-re-naming"></a>Opětovné pojmenování skupin nebo opětovné pojmenovávání organizační jednotky
- Pokud přejmenujete skupinu nebo organizační jednotku ve službě AD, která je v rozsahu pro danou konfiguraci, úloha zřizování cloudu nebude schopna rozpoznat změnu názvu ve službě AD. Úloha se nepřejde do karantény a zůstane v pořádku.



## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)