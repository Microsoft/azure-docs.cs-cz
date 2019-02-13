---
title: Certifikát obnovení pro uživatele Office 365 a Azure AD | Dokumentace Microsoftu
description: Tento článek vysvětluje uživatelům Office 365, jak řešit problémy s e-mailů, které je informovat o obnovení certifikátu.
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
ms.openlocfilehash: 037c5210f73899483bebf131efce0d5f61a847c2
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56200356"
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Obnovení federačních certifikátů pro Office 365 a Azure Active Directory
## <a name="overview"></a>Přehled
Pro úspěšné federace mezi službami Azure Active Directory (Azure AD) a Active Directory Federation Services (AD FS) by měl odpovídat certifikáty používané službou AD FS k podepisování tokenů zabezpečení do služby Azure AD, co je nakonfigurováno ve službě Azure AD. Jakákoli neshoda může vést k porušení vztahu důvěryhodnosti. Azure AD zajišťuje, že tyto informace je udržovat synchronizované při nasazování služby AD FS a Proxy webových aplikací (pro přístup z extranetu).

Tento článek obsahuje další informace o správě podpisové certifikáty tokenu a udržovat synchronizované s Azure AD v následujících případech:

* Nenasazujete Proxy webových aplikací, a proto není k dispozici v extranetu federačních metadat.
* Nepoužíváte výchozí konfiguraci služby AD FS pro podpisové certifikáty tokenu.
* Jsou pomocí zprostředkovatele identity třetí strany.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Výchozí konfigurace služby AD FS pro podpisové certifikáty tokenu
Podpisové a dešifrovací certifikáty tokenu jsou obvykle certifikáty podepsané svým držitelem a jsou vhodné pro jeden rok. Standardně služba AD FS obsahuje tzv. automatické obnovení **AutoCertificateRollover**. Pokud používáte službu AD FS 2.0 nebo novější, Office 365 a Azure AD automaticky aktualizujte před vypršením platnosti vašeho certifikátu.

### <a name="renewal-notification-from-the-office-365-portal-or-an-email"></a>Obnovení oznámení z portálu Office 365 nebo e-mailu
> [!NOTE]
> Pokud jste dostali e-mailu nebo oznámení na portálu s výzvou k obnovení certifikátu pro Office naleznete v tématu [správou změn podpisové certifikáty tokenů](#managecerts) ke kontrole, pokud je potřeba provádět žádnou akci. Microsoft si uvědomuje možný problém, který může mít za následek oznámení o obnovení certifikátu, které jsou odesílány, i když není vyžadována žádná akce.
>
>

Azure AD se pokusí federačních metadat. monitorování a aktualizace je určeno tato metadata podpisových certifikátů tokenu. 30 dní před vypršením platnosti tokenu podpisové certifikáty, Azure AD ověří, pokud nové certifikáty jsou k dispozici pomocí cyklického dotazování federačních metadat.

* Pokud může úspěšně dotazování federačních metadat a načíst nové certifikáty, objeví se uživateli upozornění na portálu Office 365 ani e-mailové oznámení.
* Pokud nemůže získat nový token podpisové certifikáty, buď protože federačních metadat je nedostupná nebo není povoleno automatické certifikáty vyměnit, Azure AD vydá e-mailové oznámení a upozornění na portálu Office 365.

![Oznámení na portálu Office 365](./media/how-to-connect-fed-o365-certs/notification.png)

> [!IMPORTANT]
> Pokud používáte službu AD FS, zajištění kontinuity obchodních procesů, ověřte prosím, že vaše servery mají následující aktualizace tak, aby se nevyskytují chyby ověřování známých problémů. To snižuje riziko zneužití známých problémů služby AD FS proxy serveru pro toto obnovení a budoucí obnovování:
>
> Server 2012 R2 - [Windows serveru. května 2014 souhrn](https://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 a 2012 - [ověřování přes proxy server selže v systému Windows Server 2012 nebo Windows 2008 R2 SP1](https://support.microsoft.com/kb/3094446)
>
>

## Zaškrtněte, pokud je nutné aktualizovat certifikáty <a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Krok 1: Zkontrolujte stav AutoCertificateRollover
Na serveru služby AD FS otevřete prostředí PowerShell. Zkontrolujte, jestli je hodnota AutoCertificateRollover nastavena na hodnotu True.

    Get-Adfsproperties

![AutoCertificateRollover](./media/how-to-connect-fed-o365-certs/autocertrollover.png)

>[!NOTE] 
>Pokud používáte službu AD FS 2.0, nejprve spusťte Add-Pssnapin Microsoft.Adfs.Powershell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Krok 2: Ověřte, zda jsou synchronizace služby AD FS a Azure AD
Na serveru služby AD FS otevřete příkazový řádek MSOnline Powershellu a připojení k Azure AD.

> [!NOTE]
> Rutiny MSOL jsou součástí modulu MSOnline Powershellu.
> Modul MSOnline Powershellu si můžete stáhnout přímo z Galerie prostředí PowerShell.
> 
>

    Install-Module MSOnline

Připojení k Azure AD pomocí prostředí PowerShell – modul MSOnline.

    Import-Module MSOnline
    Connect-MsolService

Zkontrolujte nakonfigurovaných ve službě AD FS a Azure AD, vlastnosti pro zadanou doménu vztahu důvěryhodnosti certifikátů.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/how-to-connect-fed-o365-certs/certsync.png)

Pokud odpovídají kryptografickým otiskům v obou výstupy certifikáty jsou synchronizované s Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Krok 3: Zkontrolujte, jestli je váš certifikát vyprší
Ve výstupu příkazu Get-MsolFederationProperty nebo Get-AdfsCertificate zkontrolujte datum v části "Ne po." Pokud je datum méně než 30 dnů, byste měli provést akci.

| AutoCertificateRollover | Certifikáty, které jsou synchronizované s Azure AD | Federační metadata je veřejně dostupná | Platnost | Akce |
|:---:|:---:|:---:|:---:|:---:|
| Ano |Ano |Ano |- |Není nutná žádná akce. Zobrazit [obnovit token podepisování certifikátu automaticky](#autorenew). |
| Ano |Ne |- |Méně než 15 dnů |Prodlužte platnost okamžitě. Zobrazit [podepisování obnovit certifikát ručně](#manualrenew). |
| Ne |- |- |Méně než 30 dní |Prodlužte platnost okamžitě. Zobrazit [podepisování obnovit certifikát ručně](#manualrenew). |

\[-] Nezáleží.

## Obnovit automaticky podpisový certifikát tokenu (doporučeno) <a name="autorenew"></a>
Není nutné provádět jakékoli ruční kroky, pokud jsou splněny obě z následujících akcí:

* Jste nasadili Proxy webových aplikací, které zajišťují přístup k metadatům federace z extranetu.
* Používáte výchozí konfiguraci služby AD FS (AutoCertificateRollover povoleno).

Zkontrolujte následující pro potvrzení, že certifikát je automaticky aktualizovat.

**1. Vlastnost služby AD FS AutoCertificateRollover musí být nastavena na hodnotu True.** To znamená, že služba AD FS bude automaticky vygenerovat nový token podepisování a dešifrování tokenů, certifikáty, před starého těch, které jsou vyprší.

**2. Metadata federování služby AD FS je veřejně dostupná.** Zkontrolujte, jestli je váš federační metadata veřejně přístupná tak, že přejdete na následující adresu URL z počítače na veřejném Internetu (mimo firemní síť):

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

kde `(your_FS_name) `nahradí název federační služby hostitele, vaše organizace používá, třeba fs.contoso.com.  Pokud máte možnost ověřit oba z těchto nastavení úspěšně, není nutné dělat nic dalšího.  

Příklad: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml
## Obnovit ručně podpisový certifikát tokenu <a name="manualrenew"></a>
Můžete se rozhodnout obnovit ručně podpisových certifikátů tokenu. Například následující scénáře verzování mohou vyhovovat více Ruční prodloužení platnosti:

* Token, podpisové certifikáty není certifikáty podepsané svým držitelem. Nejčastější příčinou je, že vaše organizace spravuje certifikáty služby AD FS zapsané od organizace certifikační autority.
* Zabezpečení sítě nepovoluje federačních metadat veřejně dostupná.

V těchto scénářích při každé aktualizaci tokenu podpisové certifikáty, je nutné také aktualizovat vaši doménu Office 365 pomocí příkazu Powershellu Update-MsolFederatedDomain.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Krok 1: Zajistěte, aby služba AD FS nové podpisové certifikáty tokenu
**Jiné než výchozí konfigurace**

Pokud používáte jiné než výchozí konfiguraci služby AD FS (kde **AutoCertificateRollover** je nastavena na **False**), pravděpodobně používáte vlastní certifikáty (ne s podpisem držitele). Další informace o tom, jak obnovit podpisových certifikátů tokenu služby AD FS, najdete v části [pokyny pro zákazníky bez použití služby AD FS podepsané svým držitelem certifikáty](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Není veřejně dostupných federačních metadat**

Na druhé straně Pokud **AutoCertificateRollover** je nastavena na **True**, ale není veřejně přístupná federačních metadat, nejprve se ujistěte, že byly vytvořeny nové podpisové certifikáty tokenů službou AD FS. Ověřte, že máte nový token podpisových certifikátů pomocí následujících kroků:

1. Ověřte, že jste přihlášeni na primární server služby AD FS.
2. Zkontrolujte aktuální podpisové certifikáty ve službě AD FS tak, že otevřete příkazové okno prostředí PowerShell a spuštěním následujícího příkazu:

    PS C:\>Get-ADFSCertificate – CertificateType podpisové

   > [!NOTE]
   > Pokud používáte službu AD FS 2.0, měli byste nejprve spustit Add-Pssnapin Microsoft.Adfs.Powershell.
   >
   >
3. Podívejte se na výstup tohoto příkazu v žádné certifikáty. Pokud službu AD FS vygeneruje nový certifikát, měli byste vidět dva certifikáty ve výstupu: pro který **IsPrimary** hodnotu **True** a **NotAfter** datum je do 5 dnů a pro který **IsPrimary** je **False** a **NotAfter** spočívá v roce v budoucnu.
4. Pokud se zobrazí jenom jeden certifikát a **NotAfter** datum je do 5 dnů, budete muset vygenerovat nový certifikát.
5. Pokud chcete vygenerovat nový certifikát, spusťte následující příkaz na příkazovém řádku prostředí PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.
6. Ověřte aktualizace opětovným spuštěním následujícího příkazu: PS C:\>Get-ADFSCertificate – CertificateType podpisové

Dva certifikáty by měly být uvedeny nyní, z nichž jeden je **NotAfter** datum v budoucnosti přibližně jeden rok a pro které **IsPrimary** hodnotu **False**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Krok 2: Aktualizujte nové podpisové certifikáty pro vztah důvěryhodnosti služeb Office 365
Aktualizujte Office 365 nový token podpisové certifikáty, které má být použit pro vztah důvěryhodnosti, následujícím způsobem.

1. Otevřete modul Microsoft Azure Active Directory pro prostředí Windows PowerShell.
2. Run $cred=Get-Credential. Když tato rutina vás vyzve k zadání přihlašovacích údajů, zadejte přihlašovací údaje účtu správce cloudových služeb.
3. Spustit Connect-MsolService – $cred přihlašovacích údajů. Tato rutina vás připojí ke cloudové službě. Vytvoření kontextu, který vás připojí ke cloudové službě je potřeba před spuštěním další rutiny nainstalované pomocí nástroje.
4. Pokud se spuštění těchto příkazů na počítači, který není primární federační server AD FS, spusťte Set-MSOLAdfscontext-počítače &lt;primární server AD FS&gt;, kde &lt;primární server AD FS&gt; je interní plně kvalifikovaný název domény název primárního serveru AD FS. Tato rutina vytvoří kontext, který slouží jako propojení se službou AD FS.
5. Spustit Update-MSOLFederatedDomain-DomainName &lt;domény&gt;. Tato rutina aktualizuje nastavení ze služby AD FS do cloudové služby a nakonfiguruje vztah důvěryhodnosti mezi nimi.

> [!NOTE]
> Pokud potřebujete podporu více domén nejvyšší úrovně, jako je například contoso.com a fabrikam.com, je nutné použít **SupportMultipleDomain** přepínat pomocí libovolné rutiny. Další informace najdete v tématu [podpora více domén nejvyšší úrovně](how-to-connect-install-multiple-domains.md).
>


## Oprava důvěryhodnosti Azure AD pomocí služby Azure AD Connect <a name="connectrenew"></a>
Pokud jste nakonfigurovali farma služby AD FS a vztah důvěryhodnosti Azure AD pomocí služby Azure AD Connect, můžete zjistit, pokud je potřeba provádět žádnou akci pro podpisové certifikáty tokenu Azure AD Connect. Pokud je potřeba obnovit certifikáty, můžete k tomu Azure AD Connect.

Další informace najdete v tématu [opravu vztahu důvěryhodnosti](how-to-connect-fed-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>Postup aktualizace certifikátů služby AD FS a Azure AD
Token, podpisové certifikáty jsou standardní X509 certifikáty, které se používají k bezpečně podepisování všech tokenů, které vystavuje federační server. Dešifrování tokenů certifikáty jsou standardní X509 certifikáty, které se používají k dešifrování veškerých příchozích tokenů. 

Ve výchozím nastavení je služba AD FS nakonfigurováno pro generování podepisování a dešifrování tokenů certifikáty automaticky, v době počáteční konfiguraci a pokud certifikáty jsou blížícím se datem vypršení platnosti.

Azure AD, pokusí se načíst nový certifikát z vaší federační služby metadata 30 dní před vypršením platnosti aktuálního certifikátu. V případě, že v tuto chvíli není k dispozici nový certifikát, Azure AD bude nadále monitorovat metadata v pravidelných intervalech denně. Poté, co je k dispozici v metadatech nový certifikát, nastavení federace domény se aktualizují pomocí nové informace o certifikátu. Můžete použít `Get-MsolDomainFederationSettings` k ověření, pokud se zobrazí nový certifikát v NextSigningCertificate / SigningCertificate.

Další informace o podepisování tokenů certifikáty ve službě AD FS najdete v části [získat a nakonfigurovat podpisové a dešifrovací certifikáty tokenů pro službu AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
