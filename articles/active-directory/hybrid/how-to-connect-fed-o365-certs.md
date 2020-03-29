---
title: Obnovení certifikátu pro uživatele Office 365 a Azure AD | Dokumenty společnosti Microsoft
description: Tento článek vysvětluje uživatelům Office 365, jak řešit problémy s e-maily, které je upozorňují na obnovení certifikátu.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 543b7dc1-ccc9-407f-85a1-a9944c0ba1be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d98a1aabef2de505e66b2127226b9e89cd791e20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60244850"
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Obnovení federačních certifikátů pro Office 365 a Azure Active Directory
## <a name="overview"></a>Přehled
Pro úspěšnou federaci mezi Azure Active Directory (Azure AD) a AD FS (AD FS) by certifikáty používané službou AD FS k podepisování tokenů zabezpečení do Azure AD měly odpovídat tomu, co je nakonfigurované ve službě Azure AD. Jakýkoli nesoulad může vést k nepřerušené důvěře. Azure AD zajišťuje, že tyto informace jsou synchronizovány při nasazení Služby AD FS a proxy webových aplikací (pro extranet přístup).

Tento článek obsahuje další informace pro správu certifikátů podpisu tokenů a jejich synchronizaci s Azure AD, v následujících případech:

* Nenasazujete proxy webové aplikace, a proto nejsou metadata federace k dispozici v extranetu.
* Nepoužíváte výchozí konfiguraci služby AD FS pro podpisové certifikáty tokenů.
* Používáte zprostředkovatele identity jiného výrobce.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Výchozí konfigurace služby AD FS pro podpisové certifikáty tokenů
Certifikáty pro podepisování tokenů a dešifrování tokenů jsou obvykle certifikáty podepsané svým držitelem a jsou vhodné po dobu jednoho roku. Ve výchozím nastavení zahrnuje službu AD FS proces automatického obnovení nazvaný **AutoCertificateRollover**. Pokud používáte službu AD FS 2.0 nebo novější, Office 365 a Azure AD automaticky aktualizují váš certifikát před vypršením jeho platnosti.

### <a name="renewal-notification-from-the-microsoft-365-admin-center-or-an-email"></a>Oznámení o obnovení z Centra pro správu Microsoftu 365 nebo e-mailu
> [!NOTE]
> Pokud jste obdrželi e-mail nebo oznámení portálu s žádostí o obnovení certifikátu pro Office, přečtěte si informace o tom, zda je třeba provést nějakou akci, najdete v [tématu Správa změn podpisových certifikátů tokenů.](#managecerts) Společnost Microsoft si je vědoma možného problému, který může vést k odeslání oznámení o obnovení certifikátu, a to i v případě, že není vyžadována žádná akce.
>
>

Azure AD se pokusí sledovat metadata federace a aktualizovat podpisové certifikáty tokenu, jak je uvedeno v těchto metadatech. 30 dní před vypršením platnosti podpisových certifikátů tokenu Azure AD zkontroluje, jestli jsou nové certifikáty dostupné dotazem na metadata federace.

* Pokud se mu podaří úspěšně zostřit metadata federace a načíst nové certifikáty, uživateli se nezobrazí žádné e-mailové oznámení ani upozornění v Centru pro správu Microsoftu 365.
* Pokud nemůže načíst nové podpisové certifikáty tokenů, protože metadata federace nejsou dostupná nebo automatické převrácení certifikátu není povoleno, Azure AD vydá e-mailové oznámení a upozornění v Centru pro správu Microsoftu 365.

![Oznámení portálu Office 365](./media/how-to-connect-fed-o365-certs/notification.png)

> [!IMPORTANT]
> Chcete-li zajistit kontinuitu provozu, ověřte, zda servery mají následující aktualizace, aby nedošlo k selhání ověřování u známých problémů. To zmírňuje známé problémy proxy serveru služby AD FS pro toto obnovení a budoucí období obnovení:
>
> Server 2012 R2 – [souhrn windows serveru květen 2014](https://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 a 2012 – [ověřování prostřednictvím serveru proxy se nezdaří v systému Windows Server 2012 nebo Windows 2008 R2 SP1](https://support.microsoft.com/kb/3094446)
>
>

## <a name="check-if-the-certificates-need-to-be-updated"></a>Zkontrolujte, zda je třeba certifikáty aktualizovat<a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Krok 1: Kontrola stavu AutoCertificateRollover
Na serveru služby AD FS otevřete powershell. Zkontrolujte, zda je hodnota AutoCertificateRollover nastavena na hodnotu True.

    Get-Adfsproperties

![Automatický převrácení certifikátů](./media/how-to-connect-fed-o365-certs/autocertrollover.png)

>[!NOTE] 
>Pokud používáte službu AD FS 2.0, spusťte nejprve add-pssnapin microsoft.adfs.powershell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Krok 2: Potvrzení synchronizace služby AD FS a Azure AD
Na serveru služby AD FS otevřete výzvu prostředí MSOnline PowerShell a připojte se ke službě Azure AD.

> [!NOTE]
> Rutiny MSOL jsou součástí modulu MSOnline PowerShell.
> Modul Prostředí MSOnline PowerShell si můžete stáhnout přímo z Galerie prostředí PowerShell.
> 
>

    Install-Module MSOnline

Připojte se k Azure AD pomocí modulu MSOnline PowerShell.

    Import-Module MSOnline
    Connect-MsolService

Zkontrolujte certifikáty nakonfigurované ve službách AD FS a Azure AD vztah důvěryhodnosti vlastnosti pro zadanou doménu.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/how-to-connect-fed-o365-certs/certsync.png)

Pokud kryptografické otisky v obou výstupech odpovídají, vaše certifikáty jsou synchronizovány s Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Krok 3: Zkontrolujte, jestli platnost certifikátu brzy vyprší
Ve výstupu Get-MsolFederationProperty nebo Get-AdfsCertificate zkontrolujte datum v části "Not After". Pokud je datum kratší než 30 dní, měli byste provést akci.

| Automatický převrácení certifikátů | Synchronizované certifikáty se službou Azure AD | Metadata federace jsou veřejně přístupná. | Platnost | Akce |
|:---:|:---:|:---:|:---:|:---:|
| Ano |Ano |Ano |- |Není vyžadována žádná akce. Viz [Obnovit podpisový certifikát tokenu automaticky](#autorenew). |
| Ano |Ne |- |Méně než 15 dní |Okamžitě obnovte. [Viz Ruční obnovení podpisového certifikátu tokenu](#manualrenew). |
| Ne |- |- |Méně než 30 dní |Okamžitě obnovte. [Viz Ruční obnovení podpisového certifikátu tokenu](#manualrenew). |

\[-] Nezáleží na tom

## <a name="renew-the-token-signing-certificate-automatically-recommended"></a>Automatické obnovení podpisového certifikátu tokenu (doporučeno)<a name="autorenew"></a>
Pokud jsou splněny obě následující skutečnosti, nemusíte provádět žádné ruční kroky:

* Nasadili jste proxy webové aplikace, které umožňují přístup k metadatům federace z extranetu.
* Používáte výchozí konfiguraci služby AD FS (funkce AutoCertificateRollover je povolena).

Zkontrolujte následující, abyste potvrdili, že certifikát lze automaticky aktualizovat.

**1. Vlastnost AD FS AutoCertificateRollover musí být nastavena na True.** To znamená, že služba AD FS automaticky vygeneruje nové certifikáty pro podepisování tokenů a dešifrování tokenů, než vyprší platnost starých certifikátů.

**2. Metadata federace služby AD FS jsou veřejně přístupná.** Zkontrolujte, zda jsou metadata federace veřejně přístupná, a to tak, že přejdete na následující adresu URL z počítače na veřejném internetu (mimo podnikovou síť):

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

kde `(your_FS_name)` je nahrazen názvem hostitele federační služby, který vaše organizace používá, například fs.contoso.com.  Pokud jste schopni ověřit obě tato nastavení úspěšně, není třeba dělat nic jiného.  

Příklad: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml
## <a name="renew-the-token-signing-certificate-manually"></a>Ruční obnovení podpisového certifikátu tokenu<a name="manualrenew"></a>
Můžete se rozhodnout obnovit podpisové certifikáty tokenu ručně. Následující scénáře mohou například fungovat lépe pro ruční obnovení:

* Podpisové certifikáty tokenů nejsou certifikáty podepsané svým držitelem. Nejčastějším důvodem je, že vaše organizace spravuje certifikáty služby AD FS zaregistrované z certifikační autority organizace.
* Zabezpečení sítě neumožňuje, aby metadata federace byla veřejně dostupná.

V těchto scénářích je nutné při každé aktualizaci podpisových certifikátů tokenů aktualizovat doménu Office 365 také pomocí příkazu PowerShell Update-MsolFederatedDomain.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Krok 1: Ujistěte se, že služby AD FS mají nové podpisové certifikáty tokenů.
**Nevýchozí konfigurace**

Pokud používáte nevýchozí konfiguraci služby AD FS (kde je **funkce AutoCertificateRollover** nastavena na **hodnotu False**), pravděpodobně používáte vlastní certifikáty (nikoli podepsané svým držitelem). Další informace o obnovení podpisových certifikátů tokenů služby AD FS naleznete v [tématu Pokyny pro zákazníky, kteří nepoužívají certifikáty podepsané samořízenými certifikáty služby AD FS](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Metadata federace nejsou veřejně dostupná.**

Na druhou stranu pokud **AutoCertificateRollover** je nastavena na **True**, ale vaše federace metadata není veřejně přístupné, nejprve se ujistěte, že nové podpisové certifikáty tokenu byly generovány službou AD FS. Potvrďte, že máte nové podpisové certifikáty tokenů, a to provedením následujících kroků:

1. Ověřte, zda jste přihlášeni k primárnímu serveru služby AD FS.
2. Otevřete příkazové okno Prostředí PowerShell a spouštějte následující příkazy, zkontrolujte aktuální podpisové certifikáty ve službě AD FS:

    PS C:\>Get-ADFSCertificate –Podpis tokenu Typu certificateType

   > [!NOTE]
   > Pokud používáte službu AD FS 2.0, měli byste nejprve spustit aplikaci Add-Pssnapin Microsoft.Adfs.Powershell.
   >
   >
3. Podívejte se na výstup příkazu na všechny uvedené certifikáty. Pokud AD FS vygenerovala nový certifikát, měli byste vidět dva certifikáty ve výstupu: jeden, pro který **isPrimary** hodnota je **true** a **NotAfter** datum je do 5 dnů a jeden, pro který **IsPrimary** je **False** a **NotAfter** je asi rok v budoucnu.
4. Pokud se zobrazí pouze jeden certifikát a **Datum NotAfter** je do 5 dnů, je třeba vygenerovat nový certifikát.
5. Chcete-li vygenerovat nový certifikát, proveďte na `PS C:\>Update-ADFSCertificate –CertificateType token-signing`příkazovém řádku prostředí PowerShell následující příkaz: .
6. Ověřte aktualizaci znovu spuštěním následujícího příkazu: PS C:\>Get-ADFSCertificate –CertificateType token-signing

Dva certifikáty by měly být uvedeny nyní, z nichž jeden má **NotAfter** datum přibližně jeden rok v budoucnosti a pro které **IsPrimary** hodnota je **False**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Krok 2: Aktualizace nových podpisových certifikátů tokenů pro důvěryhodnost Office 365
Aktualizujte Office 365 s novými podpisovými certifikáty tokenů, které se mají použít pro vztah důvěryhodnosti, následujícím způsobem.

1. Otevřete modul Microsoft Azure Active Directory module pro prostředí Windows PowerShell.
2. Spustit $cred=Získat pověření. Když vás tato rutina vyzve k zadání přihlašovacích údajů, zadejte přihlašovací údaje účtu správce cloudových služeb.
3. Spusťte Connect-MsolService – $cred pověření. Tato rutina vás připojí ke cloudové službě. Vytvoření kontextu, který vás připojí ke cloudové službě, je nutné před spuštěním některé z dalších rutin nainstalovaných nástrojem.
4. Pokud tyto příkazy používáte v počítači, který není primárním federačním serverem služby &lt;AD FS, spusťte primární server&gt;Set-MSOLAdfscontext -Computer AD FS , kde &lt;primární server&gt; služby AD FS je interní název fqdn primárního serveru Služby AD FS. Tato rutina vytvoří kontext, který vás spojí se službou AD FS.
5. Spusťte update-MSOLFederatedDomain &lt;&gt;– doménu DomainName . Tato rutina aktualizuje nastavení ze služby AD FS do cloudové služby a konfiguruje vztah důvěryhodnosti mezi těmito dvěma.

> [!NOTE]
> Pokud potřebujete podporovat více domén nejvyšší úrovně, jako je například contoso.com a fabrikam.com, musíte použít přepínač **SupportMultipleDomain** s libovolnými rutinami. Další informace naleznete v [tématu Podpora více domén nejvyšší úrovně](how-to-connect-install-multiple-domains.md).
>


## <a name="repair-azure-ad-trust-by-using-azure-ad-connect"></a>Oprava vztahu důvěryhodnosti Azure AD pomocí služby Azure AD Connect<a name="connectrenew"></a>
Pokud jste nakonfigurovali farmu služby AD FS a vztah důvěryhodnosti Azure AD pomocí Azure AD Connect, můžete pomocí Služby Azure AD Connect zjistit, jestli potřebujete provést jakoukoli akci pro vaše podpisové certifikáty tokenů. Pokud potřebujete obnovit certifikáty, můžete k tomu použít Azure AD Connect.

Další informace naleznete [v tématu Oprava vztahu důvěryhodnosti](how-to-connect-fed-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>Kroky aktualizace certifikátu Služby AD FS a Certifikátu Azure AD
Podpisové certifikáty tokenů jsou standardní certifikáty X509, které se používají k bezpečnému podepisování všech tokenů, které federační server vydává. Dešifrovací certifikáty tokenů jsou standardní certifikáty X509, které se používají k dešifrování všech příchozích tokenů. 

Ve výchozím nastavení je služba AD FS nakonfigurována tak, aby automaticky generovala podpisové certifikáty tokenů a dešifrování tokenů, a to jak v počáteční době konfigurace, tak v době, kdy se certifikáty blíží k datu vypršení platnosti.

Azure AD se pokusí načíst nový certifikát z metadat federační služby 30 dní před vypršením platnosti aktuálního certifikátu. V případě, že nový certifikát není k dispozici v té době, Azure AD bude nadále sledovat metadata v pravidelných denních intervalech. Jakmile je nový certifikát k dispozici v metadatech, nastavení federace pro doménu se aktualizují o nové informace o certifikátu. Můžete použít `Get-MsolDomainFederationSettings` k ověření, zda se zobrazí nový certifikát v NextSigningCertificate / SigningCertificate.

Další informace o certifikátech pro podepisování tokenů ve službě AD FS naleznete v [tématu Získání a konfigurace certifikátů pro podepisování tokenů a dešifrování tokenů pro službu AD FS.](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
