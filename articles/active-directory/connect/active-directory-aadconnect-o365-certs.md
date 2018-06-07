---
title: Certifikát obnovení pro uživatele služeb Office 365 a Azure AD | Microsoft Docs
description: Tento článek vysvětluje uživatelům služeb Office 365, jak řešit problémy s e-mailů, které je informovat o obnovení certifikátu.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 543b7dc1-ccc9-407f-85a1-a9944c0ba1be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: e8f6b30bb7cbe82159e86fa48721afce3f9477d8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591493"
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Obnovení federačních certifikátů pro Office 365 a Azure Active Directory
## <a name="overview"></a>Přehled
Pro úspěšné federaci mezi Azure Active Directory (Azure AD) a služby Active Directory Federation Services (AD FS) by měl odpovídat certifikátů používaných službou AD FS k podepisování tokenů zabezpečení do služby Azure AD, co je nakonfigurováno ve službě Azure AD. Jakákoli neshoda může dojít k porušení vztahu důvěryhodnosti. Azure AD zajistí, že tyto informace jsou uchovávány v synchronizaci při nasazení služby AD FS a Proxy webových aplikací (pro přístup z extranetu).

Tento článek obsahuje další informace ke správě vašeho podpisových certifikátů tokenu a udržovat synchronizované s Azure AD, v následujících případech:

* Nejsou nasazení Proxy webových aplikací, a proto není k dispozici v extranetu federačních metadat.
* Nepoužíváte výchozí konfiguraci služby AD FS pro podpisových certifikátů tokenu.
* Používáte poskytovatel identity jiného výrobce.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Výchozí konfiguraci služby AD FS pro podpisových certifikátů tokenu
Podpisové a dešifrovací certifikáty tokenu jsou obvykle certifikáty podepsané svým držitelem a jsou vhodné pro jeden rok. Ve výchozím nastavení, služba AD FS obsahuje procesu automatického obnovení označovaného jako **AutoCertificateRollover**. Pokud používáte službu AD FS 2.0 nebo novější, Office 365 a Azure AD automaticky aktualizujte certifikát, než vyprší její platnost.

### <a name="renewal-notification-from-the-office-365-portal-or-an-email"></a>Obnovení oznámení z portálu Office 365 nebo e-mailu
> [!NOTE]
> Pokud jste dostali e-mailu nebo portálu oznámení s žádostí o obnovení certifikátu pro Office, najdete v části [Správa změn podpisových certifikátů tokenu](#managecerts) ke kontrole, pokud je třeba provádět žádnou akci. Microsoft má informace o potenciální problém, který může vést k oznámení o obnovení certifikátu, které jsou odesílány, i když není vyžadována žádná akce.
>
>

Azure AD se pokusí monitorování federačních metadat a aktualizovat podle tato metadata podpisových certifikátů tokenu. 30 dní před vypršením platnosti tokenu podpisové certifikáty, Azure AD ověří, zda nové certifikáty jsou k dispozici pomocí cyklického dotazování federačních metadat.

* Pokud se můžete úspěšně dotazování federačních metadat a načíst nové certifikáty, je si uživatel zařídit žádné e-mailových oznámení nebo upozornění na portálu Office 365.
* Pokud ho nelze načíst nové podpisových certifikátů tokenu, buď protože federačních metadat je nedostupná nebo není povoleno automatické certifikáty vyměnit, Azure AD vydá e-mailových oznámení a upozornění na portálu Office 365.

![Oznámení portálu Office 365](./media/active-directory-aadconnect-o365-certs/notification.png)

> [!IMPORTANT]
> Pokud používáte službu AD FS, zajištění kontinuity obchodních procesů, ověřte, že vaše servery mají následující aktualizace tak, že nedojde k selhání ověřování známých problémů. To snižuje známé problémy serveru proxy služby AD FS pro toto období budoucí obnovení a obnovení:
>
> Server 2012 R2 - [systému Windows Server kumulativní může 2014](http://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 a 2012 – [nezdaří ověřování prostřednictvím proxy serveru v systému Windows Server 2012 nebo Windows 2008 R2 SP1](http://support.microsoft.com/kb/3094446)
>
>

## Zkontrolujte, pokud je nutné aktualizovat certifikáty <a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Krok 1: Kontrola stavu AutoCertificateRollover
Na serveru služby AD FS otevřete prostředí PowerShell. Zkontrolujte, jestli je hodnota AutoCertificateRollover nastavena na hodnotu True.

    Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

>[!NOTE] 
>Pokud používáte službu AD FS 2.0, spusťte nejprve přidat-Pssnapin Microsoft.Adfs.Powershell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Krok 2: Ověřte, jestli jsou synchronizované služby AD FS a Azure AD
Na serveru služby AD FS otevřete příkazového řádku Azure AD PowerShell a připojte ke službě Azure AD.

> [!NOTE]
> Můžete si stáhnout Azure AD PowerShell [zde](https://technet.microsoft.com/library/jj151815.aspx).
>
>

    Connect-MsolService

Zkontrolujte nakonfigurovaných v AD FS a Azure AD vlastnosti pro zadanou doménu vztahu důvěryhodnosti certifikátů.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

Pokud se v obou výstupy kryptografické otisky shodují, jsou certifikáty synchronizované s Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Krok 3: Kontrola, zda váš certifikát je vyprší
Ve výstupu Get-MsolFederationProperty nebo Get-AdfsCertificate zkontrolujte datum v části "Není po." Pokud je datum rychle méně než 30 dní, můžete provést akce.

| AutoCertificateRollover | Certifikáty, které jsou synchronizované s Azure AD | Federační metadata jsou veřejně přístupné | Platnosti | Akce |
|:---:|:---:|:---:|:---:|:---:|
| Ano |Ano |Ano |- |Není vyžadována žádná akce. V tématu [podepisování obnovit certifikát automaticky](#autorenew). |
| Ano |Ne |- |Méně než 15 dní |Obnovte okamžitě. V tématu [podepisování obnovit certifikát ručně](#manualrenew). |
| Ne |- |- |Méně než 30 dní |Obnovte okamžitě. V tématu [podepisování obnovit certifikát ručně](#manualrenew). |

\[-] Nezáleží.

## Obnovit automaticky podpisový certifikát tokenu (doporučeno) <a name="autorenew"></a>
Nemusíte provádět žádné ruční kroky, pokud jsou splněny obě z následujících akcí:

* Nasazení Proxy webových aplikací, které můžete povolit přístup k metadatům federace z extranetu.
* Používáte výchozí konfiguraci služby AD FS (AutoCertificateRollover je povoleno).

Zkontrolujte následující potvrďte, že certifikát automaticky aktualizován.

**1. Vlastnost služby AD FS AutoCertificateRollover musí být nastavená na hodnotu True.** To znamená, že služby AD FS automaticky vygeneruje nový token podepisování a dešifrování tokenů, certifikáty, před starý těch, které jsou vyprší.

**2. Federační metadata služby AD FS je veřejně přístupná.** Zkontrolujte, jestli federačních metadat veřejně přístupná tak, že přejdete na tuto adresu URL z počítače v veřejného Internetu (mimo firemní síť):

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

kde `(your_FS_name) `se nahradí název federační služby hostitele vaše organizace používá, třeba fs.contoso.com.  Pokud budete moci ověřit oba z těchto nastavení úspěšně, můžete nemusíte dělat žádné další kroky.  

Příklad: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml
## Obnovte ručně podpisový certifikát tokenu <a name="manualrenew"></a>
Můžete se rozhodnout obnovit ručně podpisových certifikátů tokenu. Následující scénáře mohou například fungují lépe pro ruční obnovení:

* Token, podpisové certifikáty jsou certifikáty podepsané držitelem. Nejčastější příčinou je, že vaše organizace spravuje certifikáty služby AD FS zapsané od organizace certifikační autority.
* Zabezpečení sítě neumožňuje federačních metadat být veřejně dostupné.

V těchto scénářích při každé aktualizaci token podpisové certifikáty, je nutné také aktualizovat doménu Office 365 pomocí příkazu prostředí PowerShell, MsolFederatedDomain aktualizace.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Krok 1: Zajistěte, aby služba AD FS nové podpisových certifikátů tokenu
**Jiné než výchozí konfigurace**

Pokud používáte jiné než výchozí konfiguraci služby AD FS (kde **AutoCertificateRollover** je nastaven na **False**), pravděpodobně používáte vlastní certifikáty (není podepsaný). Další informace o tom, jak obnovit podpisových certifikátů tokenu služby AD FS najdete v tématu [pokyny pro zákazníky bez použití služby AD FS certifikátů podepsaných svým držitelem](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Federační metadata nejsou veřejně dostupné**

Na druhé straně Pokud **AutoCertificateRollover** je nastaven na **True**, ale není veřejně přístupná federačních metadat, nejprve se ujistěte, že byla vygenerována nové podpisové certifikáty tokenu služby AD FS. Potvrďte, že máte nový token podpisové certifikáty provedením následujících kroků:

1. Ověřte, že jste přihlášeni na primární server služby AD FS.
2. Zkontrolujte aktuální podpisových certifikátů ve službě AD FS Otevřete příkazové okno prostředí PowerShell a spuštěním následujícího příkazu:

    PS C:\>Get-ADFSCertificate – CertificateType podpisové

   > [!NOTE]
   > Pokud používáte službu AD FS 2.0, byste měli nejdřív spustit Add-Pssnapin Microsoft.Adfs.Powershell.
   >
   >
3. Podívejte se na výstupu příkazu v jakékoli certifikáty uvedené. Pokud službu AD FS vygeneruje nový certifikát, měli byste vidět dva certifikáty ve výstupu: pro který **IsPrimary** hodnota je **True** a **neplatí po** datum je do 5 dnů a pro který **IsPrimary** je **False** a **neplatí po** spočívá v roce v budoucnu.
4. Pokud se zobrazí pouze jeden certifikát a **neplatí po** datum je do 5 dní, budete muset vygenerovat nový certifikát.
5. Pokud chcete vygenerovat nový certifikát, spusťte následující příkaz na příkazovém řádku prostředí PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.
6. Zkontrolujte aktualizace opětovným spuštěním následujícího příkazu: PS C:\>Get-ADFSCertificate – CertificateType podpisové

Teď by měl být uvedený dva certifikáty, z nichž jeden je **neplatí po** datum v budoucnosti přibližně jeden rok a pro které **IsPrimary** hodnota je **False**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Krok 2: Aktualizace nový token podpisové certifikáty pro vztah důvěryhodnosti Office 365
Aktualizace Office 365 s nový token podepisování certifikátů, které se použije pro vztah důvěryhodnosti, následujícím způsobem.

1. Otevřete modul Microsoft Azure Active Directory pro prostředí Windows PowerShell.
2. Spustit $cred = Get-Credential. Když tato rutina vás vyzve k zadání přihlašovacích údajů, zadejte přihlašovací údaje účtu správce cloudové služby.
3. Spustit Connect-MsolService – $cred na přihlašovací údaje. Tato rutina je připojí ke cloudové službě. Vytváření kontextu, která vás spojí se Cloudová služba se vyžaduje před spuštěním další rutiny nainstalován nástrojem.
4. Pokud spustíte tyto příkazy v počítači, který není primární federační server služby AD FS, spusťte sadu MSOLAdfscontext-počítače &lt;primární server služby AD FS&gt;, kde &lt;primární server služby AD FS&gt; je interní plně kvalifikovaný název domény název primárního serveru služby AD FS. Tato rutina vytvoří kontext, který vás spojí se služby AD FS.
5. Spuštění aktualizace MSOLFederatedDomain – DomainName &lt;domény&gt;. Tato rutina aktualizuje nastavení ze služby AD FS do cloudové služby a nakonfiguruje vztah důvěryhodnosti mezi nimi.

> [!NOTE]
> Pokud potřebujete podporu více domén nejvyšší úrovně, jako je například contoso.com a fabrikam.com, musíte použít **SupportMultipleDomain** přepínač s rutiny. Další informace najdete v tématu [podpory pro víc domén nejvyšší úrovně](active-directory-aadconnect-multiple-domains.md).
>


## Opravte vztah důvěryhodnosti služby Azure AD pomocí Azure AD Connect <a name="connectrenew"></a>
Pokud jste nakonfigurovali farma služby AD FS a vztah důvěryhodnosti služby Azure AD pomocí Azure AD Connect, můžete zjistit, pokud je třeba provádět žádnou akci podpisové certifikáty tokenu Azure AD Connect. Pokud potřebujete obnovit certifikáty, můžete to udělat Azure AD Connect.

Další informace najdete v tématu [opravu vztahu důvěryhodnosti](active-directory-aadconnect-federation-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>Postup aktualizace certifikátů pro služby AD FS a Azure AD
Token, podpisové certifikáty jsou standardní X509 certifikáty, které se používají k bezpečně podepisování všech tokenů, které vystavuje federační server. Dešifrování tokenů certifikáty jsou standardní X509 certifikáty, které slouží k dešifrování veškerých příchozích tokenů. 

Ve výchozím nastavení je služby AD FS nakonfigurováno pro generování podepisování a dešifrování tokenů certifikáty automaticky, v době počáteční konfiguraci a pokud certifikáty se blíží datum ukončení platnosti.

Azure AD, pokusí se načíst nový certifikát z vaší služby federačních metadat, 30 dní před vypršením platnosti aktuálního certifikátu. V případě, že v této chvíli není k dispozici nový certifikát, Azure AD budou nadále sledovat metadata v pravidelných intervalech denně. Jakmile je k dispozici v metadatech nový certifikát, nastavení federace pro doménu jsou aktualizované o nové informace o certifikátu. Můžete použít `Get-MsolDomainFederationSettings` k ověření, pokud se zobrazí nový certifikát v NextSigningCertificate nebo SigningCertificate.

Další informace o podepisování tokenů certifikáty ve službě AD FS najdete v části [získat konfiguraci podepisování tokenů a tokenu dešifrovací certifikáty pro službu AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
