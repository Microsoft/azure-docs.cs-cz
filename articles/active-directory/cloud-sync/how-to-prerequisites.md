---
title: Předpoklady pro Azure AD Connect synchronizaci cloudu v Azure AD
description: Tento článek popisuje požadavky a požadavky na hardware, které potřebujete pro cloudovou synchronizaci.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b83c9b0ece933ad71810c50e89ae296aa218ec75
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613304"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-sync"></a>Předpoklady pro Azure AD Connect synchronizaci cloudu
Tento článek poskytuje pokyny k výběru a použití Azure Active Directory (Azure AD) připojit cloudovou synchronizaci jako vaše řešení identity.

## <a name="cloud-provisioning-agent-requirements"></a>Požadavky na agenta zřizování cloudu
Pro použití Azure AD Connect synchronizace cloudu potřebujete následující:

- Přihlašovací údaje správce domény nebo správce podnikového správce k vytvoření Azure AD Connect gMSA Cloud Sync (skupinový účet spravované služby) pro spuštění služby agenta. 
- Účet správce hybridní identity pro vašeho tenanta Azure AD, který není uživatelem typu Host.
- Místní server pro zřizovacího agenta se systémem Windows 2012 R2 nebo novějším.  Tento server by měl být serverem vrstvy 0, který je založený na [modelu vrstvy správy služby Active Directory](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- Místní konfigurace brány firewall.

## <a name="group-managed-service-accounts"></a>Skupinové účty spravované služby
Skupinový účet spravované služby je účet spravované domény, který poskytuje automatickou správu hesel, zjednodušenou správu hlavního názvu služby (SPN), schopnost delegovat správu na jiné správce a také rozšiřuje tuto funkci na více serverů.  Azure AD Connect synchronizace cloudu podporuje a používá gMSA ke spuštění agenta.  Během instalace budete vyzváni k zadání přihlašovacích údajů správce, aby bylo možné tento účet vytvořit.  Účet se zobrazí jako (domain\provAgentgMSA $).  Další informace o gMSA najdete v tématu [skupinový účet spravované služby](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) . 

### <a name="prerequisites-for-gmsa"></a>Předpoklady pro gMSA:
1.  Schéma služby Active Directory v doménové struktuře domény gMSA se musí aktualizovat na Windows Server 2012.
2.  [Moduly PowerShellu pro vzdálenou správu](/windows-server/remote/remote-server-administration-tools) počítače v řadiči domény
3.  Aspoň jeden řadič domény v doméně musí používat Windows Server 2012.
4.  Server připojený k doméně, na kterém je agent nainstalovaný, musí být Windows Server 2012 nebo novější.

### <a name="custom-gmsa-account"></a>Vlastní účet gMSA
Pokud vytváříte vlastní účet gMSA, musíte zajistit, aby měl účet následující oprávnění.

|Typ |Name |Access |Platí pro| 
|-----|-----|-----|-----|
|Povolit |Účet gMSA |Číst všechny vlastnosti |Podřízené objekty zařízení| 
|Povolit |Účet gMSA|Číst všechny vlastnosti |Odvozené objekty InetOrgPerson| 
|Povolit |Účet gMSA |Číst všechny vlastnosti |Objekty potomkového počítače| 
|Povolit |Účet gMSA |Číst všechny vlastnosti |Odvozené objekty foreignSecurityPrincipal| 
|Povolit |Účet gMSA |Full control |Podřízené objekty skupiny| 
|Povolit |Účet gMSA |Číst všechny vlastnosti |Podřízené objekty uživatele| 
|Povolit |Účet gMSA |Číst všechny vlastnosti |Podřízené objekty kontaktu| 
|Povolit |Účet gMSA |Vytvoření nebo odstranění objektů uživatele|Tento objekt a všechny odvozené objekty| 

Postup upgradu stávajícího agenta tak, aby používal účet gMSA, najdete v tématu [skupinový účet spravované služby](how-to-install.md#group-managed-service-accounts).

### <a name="in-the-azure-active-directory-admin-center"></a>V centru pro správu Azure Active Directory

1. Vytvořte v tenantovi Azure AD jenom cloudový účet správce hybridní identity. Tímto způsobem můžete spravovat konfiguraci tenanta, pokud vaše místní služby selžou nebo nebudou k dispozici. Přečtěte si, jak [Přidat účet jenom pro cloudovou hybridní identitu správce](../fundamentals/add-users-azure-active-directory.md). Dokončení tohoto kroku je důležité, aby se zajistilo, že nebudete mít uzamčený přístup k vašemu tenantovi.
1. Přidejte jeden nebo více [vlastních názvů domén](../fundamentals/add-custom-domain.md) do svého tenanta služby Azure AD. Uživatelé se můžou přihlásit pomocí některého z těchto názvů domén.

### <a name="in-your-directory-in-active-directory"></a>Ve vašem adresáři ve službě Active Directory

Pro přípravu atributů adresáře pro synchronizaci spusťte [Nástroj IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) .

### <a name="in-your-on-premises-environment"></a>V místním prostředí

1. Identifikujte hostitelský server připojený k doméně, na kterém běží Windows Server 2012 R2 nebo novější, s minimálním počtem 4 GB paměti RAM a .NET 4.7.1 + runtime.

2. Zásady spouštění PowerShellu na místním serveru musí být nastavené na undefined nebo RemoteSigned.

3. Pokud je mezi vašimi servery a službou Azure AD brána firewall, nakonfigurujte následující položky:
    - Zajistěte, aby agenti mohli vytvářet *odchozí* požadavky do služby Azure AD prostřednictvím následujících portů:

      | Číslo portu | K čemu slouží |
      | --- | --- |
      | **80** | Při ověřování certifikátu TLS/SSL stáhne seznamy odvolaných certifikátů (CRL).  |
      | **443** | Zpracovává veškerou odchozí komunikaci se službou. |
      | **8080** (volitelné) | Agenti hlásí svůj stav každých 10 minut přes port 8080, pokud není k dispozici port 443. Tento stav se zobrazuje na portálu Azure AD. |

    - Pokud vaše brána firewall vynucuje pravidla v závislosti na zdroji uživatelů, otevřete tyto porty pro přenos ze služeb pro Windows, které běží jako síťové služby.
    - Pokud vaše brána firewall nebo proxy server umožňují zadat bezpečné přípony, přidejte připojení k \* příponám. msappproxy.NET a \* . ServiceBus.Windows.NET. V takovém případě povolte přístup k [rozsahům IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653), které se aktualizují týdně.
    - Vaši agenti kvůli počáteční registraci musí mít přístup k adresám login.windows.net a login.microsoftonline.com. Otevřete taky bránu firewall pro tyto adresy URL.
    - Pro ověření certifikátu Odblokujte následující adresy URL: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 a www \. Microsoft.com:80. Tyto adresy URL používají k ověřování certifikátů i další produkty Microsoftu, takže je možné, že je již máte odblokované.

    >[!NOTE]
    > Instalace agenta zřizování cloudu na Windows serveru Core není podporovaná.

### <a name="additional-requirements"></a>Další požadavky

- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Požadavky TLS

> [!NOTE]
> TLS (Transport Layer Security) je protokol, který zajišťuje zabezpečenou komunikaci. Změna nastavení TLS má vliv na celou doménovou strukturu. Další informace najdete v tématu [aktualizace povolení tls 1,1 a tls 1,2 jako výchozích zabezpečených protokolů v WinHTTP v systému Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

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

- Filtrování oboru skupiny pro rozdílovou synchronizaci nepodporuje více než 1500 členů.
- Když odstraníte skupinu, která se používá jako součást filtru oboru skupiny, uživatelé, kteří jsou členy skupiny, se neodstraní. 
- Pokud přejmenujete organizační jednotku nebo skupinu, která je v oboru, rozdílová synchronizace tyto uživatele neodebere.

### <a name="provisioning-logs"></a>Protokoly zřizování
- Protokoly zřizování nejsou jasně odlišené mezi operacemi vytvoření a aktualizace.  Může se zobrazit operace vytvoření aktualizace a operace aktualizace pro vytvoření.

### <a name="group-re-naming-or-ou-re-naming"></a>Opětovné pojmenování skupin nebo opětovné pojmenovávání organizační jednotky
- Pokud přejmenujete skupinu nebo organizační jednotku ve službě AD, která je v rozsahu pro danou konfiguraci, úloha synchronizace cloudu nebude schopna rozpoznat změnu názvu ve službě AD. Úloha se nepřejde do karantény a zůstane v dobrém stavu.


## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je Azure AD Connect synchronizace cloudu?](what-is-cloud-sync.md)