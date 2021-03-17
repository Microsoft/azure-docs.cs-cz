---
title: Obnovení certifikátu pro Microsoft 365 a uživatele Azure AD | Microsoft Docs
description: Tento článek vysvětluje, jak Microsoft 365 uživatele řešit problémy s e-maily, které je upozorňují na obnovení certifikátu.
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
ms.topic: how-to
ms.date: 10/20/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: effdd156858caf5717aac92433e8bc5f4f6147ad
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686865"
---
# <a name="renew-federation-certificates-for-microsoft-365-and-azure-active-directory"></a>Prodloužit platnost federačních certifikátů pro Microsoft 365 a Azure Active Directory
## <a name="overview"></a>Přehled
Pro úspěšnou federaci mezi Azure Active Directory (Azure AD) a Active Directory Federation Services (AD FS) (AD FS) by se certifikáty, které používá AD FS k podepisování tokenů zabezpečení služby Azure AD, měly shodovat s tím, co je nakonfigurované ve službě Azure AD. Jakákoli neshoda může vést k porušení vztahu důvěryhodnosti. Služba Azure AD zajišťuje, že tyto informace budou při nasazení AD FS a proxy webových aplikací (pro přístup k síti extranet) udržovány synchronizované.

V tomto článku najdete další informace o správě certifikátů podepisování tokenů a jejich synchronizaci s Azure AD, a to v následujících případech:

* Neprovádíte nasazení služby Proxy webových aplikací, takže federační metadata nejsou v extranetu k dispozici.
* Nepoužíváte výchozí konfiguraci AD FS pro podpisové certifikáty tokenů.
* Používáte poskytovatele identity od jiného výrobce.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Výchozí konfigurace AD FS pro podpisové certifikáty tokenů
Certifikáty podepisování tokenů a dešifrování tokenů jsou obvykle certifikáty podepsané svým držitelem a jsou vhodné po dobu jednoho roku. Ve výchozím nastavení AD FS zahrnuje proces automatického obnovování s názvem **AutoCertificateRollover**. Pokud používáte AD FS 2,0 nebo novější, Microsoft 365 a Azure AD automaticky aktualizují svůj certifikát před jeho vypršením jeho platnosti.

### <a name="renewal-notification-from-the-microsoft-365-admin-center-or-an-email"></a>Oznámení o obnovení z centra pro správu Microsoft 365 nebo e-mailu
> [!NOTE]
> Pokud jste obdrželi e-mail nebo oznámení na portálu s výzvou k obnovení certifikátu pro Office, přečtěte si téma [Správa změn pro podpisové certifikáty tokenů](#managecerts) , abyste zkontrolovali, jestli potřebujete provést nějakou akci. Společnost Microsoft si je vědoma možného problému, který může vést k odeslání oznámení o prodloužení platnosti certifikátu, a to i v případě, že se nevyžaduje žádná akce.
>
>

Služba Azure AD se pokusí monitorovat federační metadata a aktualizovat podpisové certifikáty tokenu, jak je uvedeno v těchto metadatech. 30 dní před vypršením platnosti podpisových certifikátů tokenu Azure AD zkontroluje, jestli jsou nové certifikáty dostupné pomocí cyklického dotazování federačních metadat.

* Pokud se může úspěšně dotazovat federačních metadat a načíst nové certifikáty, nevydá se uživateli žádné e-mailové oznámení ani upozornění v centru pro správu Microsoft 365.
* Pokud se nové podpisové certifikáty tokenů nedají načíst, protože federační metadata nejsou dostupná nebo není povolená Automatická změna certifikátu, služba Azure AD vydá e-mailové oznámení a upozornění v centru pro správu Microsoft 365.

![Oznámení na portálu Office 365](./media/how-to-connect-fed-o365-certs/notification.png)

> [!IMPORTANT]
> Pokud používáte AD FS, abyste zajistili kontinuitu podnikových aplikací, ověřte, zda jsou servery následující aktualizace, aby nedocházelo k chybám ověřování pro známé problémy. To snižuje známou AD FS proxy server problémy s tímto obnovením a budoucími dobami obnovení:
>
> Server 2012 R2 – [Windows Server – aktualizace z května 2014](https://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 a 2012 – [ověřování prostřednictvím proxy serveru se nepovede v systému Windows Server 2012 nebo windows 2008 R2 SP1](https://support.microsoft.com/kb/3094446) .
>
>

## <a name="check-if-the-certificates-need-to-be-updated"></a>Ověřte, jestli se certifikáty musí aktualizovat. <a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Krok 1: ověření stavu AutoCertificateRollover
Na serveru AD FS otevřete PowerShell. Ověřte, zda je hodnota AutoCertificateRollover nastavena na hodnotu true.

```azurepowershell-interactive
Get-Adfsproperties
```

![AutoCertificateRollover](./media/how-to-connect-fed-o365-certs/autocertrollover.png)

>[!NOTE] 
>Pokud používáte AD FS 2,0, nejdřív spusťte Add-Pssnapin Microsoft. ADFS. PowerShell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Krok 2: potvrďte, že je synchronizace AD FS a Azure AD
Na serveru AD FS otevřete příkazový řádek PowerShellu MSOnline a připojte se k Azure AD.

> [!NOTE]
> MSOL-Cmdlets jsou součástí modulu PowerShellu pro MSOnline.
> Modul PowerShellu MSOnline si můžete stáhnout přímo z Galerie prostředí PowerShell.
> 
>

```azurepowershell-interactive
Install-Module MSOnline
```

Připojte se k Azure AD pomocí modulu MSOnline PowerShellu.

```azurepowershell-interactive
Import-Module MSOnline
Connect-MsolService
```

Ověřte certifikáty nakonfigurované v AD FS a ve vlastnostech vztahu důvěryhodnosti služby Azure AD pro zadanou doménu.

```azurepowershell-interactive
Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate
```

![Get-MsolFederationProperty](./media/how-to-connect-fed-o365-certs/certsync.png)

Pokud se kryptografické otisky v obou výstupech shodují, vaše certifikáty se synchronizují se službou Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Krok 3: Podívejte se, jestli váš certifikát brzy vyprší.
Ve výstupu Get-MsolFederationProperty nebo Get-AdfsCertificate ověřte, že datum není po. Pokud je datum kratší než 30 dnů, měli byste provést akci.

| AutoCertificateRollover | Synchronizace certifikátů s Azure AD | Federační metadata jsou veřejně přístupná. | Obou | Akce |
|:---:|:---:|:---:|:---:|:---:|
| Ano |Ano |Ano |- |Není vyžadována žádná akce. Přečtěte si téma [Automatické obnovení podpisového certifikátu tokenu](#autorenew). |
| Ano |Ne |- |Méně než 15 dní |Obnovte hned. Viz [Ruční obnovení podpisového certifikátu tokenu](#manualrenew). |
| Ne |- |- |Méně než 30 dní |Obnovte hned. Viz [Ruční obnovení podpisového certifikátu tokenu](#manualrenew). |

\[-] Nezáleží

## <a name="renew-the-token-signing-certificate-automatically-recommended"></a>Obnovit podpisový certifikát tokenu automaticky (doporučeno) <a name="autorenew"></a>
Pokud jsou splněné obě následující podmínky, nemusíte provádět žádné ruční kroky:

* Nasadili jste proxy webových aplikací, který může povolit přístup k federačním metadatům z extranetu.
* Používáte AD FS výchozí konfiguraci (AutoCertificateRollover je povolený).

Zkontrolujte následující a potvrďte, že se certifikát může automaticky aktualizovat.

**1. vlastnost AD FS AutoCertificateRollover musí být nastavená na hodnotu true.** To znamená, že AD FS automaticky vygeneruje nové podepisování tokenů a dešifrovací certifikáty tokenů, než staré vyprší.

**2. federační metadata AD FS jsou veřejně přístupná.** Ověřte, že vaše federační metadata jsou veřejně přístupná, a to tak, že přejdete na následující adresu URL z počítače ve veřejném Internetu (mimo podnikovou síť):

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

kde `(your_FS_name)` je nahrazen název hostitele federační služby, kterou vaše organizace používá, například FS.contoso.com.  Pokud máte možnost úspěšně ověřit obě tato nastavení, nemusíte provádět žádné další kroky.  

Příklad: `https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml`
## <a name="renew-the-token-signing-certificate-manually"></a>Ruční obnovení podpisového certifikátu tokenů <a name="manualrenew"></a>
Podpisové certifikáty tokenů si můžete obnovit ručně. Například následující scénáře mohou být vhodnější pro ruční obnovení:

* Podpisové certifikáty tokenů nejsou certifikáty podepsané svým držitelem. Nejběžnějším důvodem je to, že vaše organizace spravuje AD FS certifikátů zaregistrovaných z certifikační autority organizace.
* Zabezpečení sítě neumožňuje veřejně dostupným federačním metadatům.

V těchto scénářích je potřeba při každé aktualizaci podpisových certifikátů tokenu aktualizovat taky Microsoft 365 doméně pomocí příkazu PowerShellu Update-MsolFederatedDomain.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Krok 1: Zajistěte, aby AD FS měly nové podpisové certifikáty tokenů.
**Jiná než výchozí konfigurace**

Pokud používáte jinou než výchozí konfiguraci AD FS (kde **AutoCertificateRollover** je nastavená na **false**), budete pravděpodobně používat vlastní certifikáty (bez podpisu držitele). Další informace o tom, jak obnovit AD FS podpisové certifikáty tokenů, najdete v tématu [požadavky na certifikáty pro federované servery](/windows-server/identity/ad-fs/design/certificate-requirements-for-federation-servers).

**Federační metadata nejsou veřejně dostupná.**

Na druhé straně, pokud je **AutoCertificateRollover** nastavené na **hodnotu true**, ale vaše federační metadata nejsou veřejně přístupná, nejdřív se ujistěte, že AD FS vygenerovaly nové podpisové certifikáty tokenů. Potvrďte, že máte nové podpisové certifikáty tokenů, a to provedením následujících kroků:

1. Ověřte, zda jste přihlášeni k primárnímu AD FS serveru.
2. Ověřte aktuální podpisové certifikáty v AD FS otevřením okna příkazového řádku prostředí PowerShell a spuštěním následujícího příkazu:

    PS C: \> Get-ADFSCertificate – podepisování tokenů CertificateType

   > [!NOTE]
   > Pokud používáte AD FS 2,0, měli byste nejdřív spustit Add-Pssnapin Microsoft. ADFS. PowerShell.
   >
   >
3. Podívejte se na výstup příkazu na libovolných uvedených certifikátech. Pokud AD FS vygeneroval nový certifikát, měli byste vidět dva certifikáty ve výstupu: jeden, pro který je **primární** hodnota **true** , a datum **NotAfter** spadá do 5 dnů a jedna, pro kterou je **primární** hodnota **false** , a **NotAfter** je přibližně ročně v budoucnosti.
4. Pokud vidíte jenom jeden certifikát a datum **NotAfter** je do 5 dní, musíte vygenerovat nový certifikát.
5. Pokud chcete vygenerovat nový certifikát, spusťte na příkazovém řádku PowerShellu následující příkaz: `PS C:\Update-ADFSCertificate –CertificateType token-signing` .
6. Ověřte aktualizaci tak, že znovu spustíte následující příkaz: PS C: \> Get-ADFSCertificate – CertificateType token-signing

Nyní by měly být uvedeny dva certifikáty, z nichž jedna má **NotAfter** datum v budoucnosti, a pro kterou je **primární** hodnota **false**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-microsoft-365-trust"></a>Krok 2: aktualizujte nové podpisové certifikáty tokenů pro Microsoft 365 důvěryhodnosti.
Aktualizujte Microsoft 365 s novými podpisovým certifikátem tokenu, který se má použít pro vztah důvěryhodnosti následujícím způsobem.

1. Otevřete Modul Microsoft Azure Active Directory pro Windows PowerShell.
2. Spusťte $cred = Get-Credential. Když tato rutina vyzve k zadání přihlašovacích údajů, zadejte přihlašovací údaje účtu správce cloudové služby.
3. Spustit Connect-MsolService – přihlašovací $cred Tato rutina vás připojí ke cloudové službě. Vytvoření kontextu, který vás připojí ke cloudové službě, je nutné před spuštěním kterékoli z dalších rutin instalovaných nástrojem.
4. Pokud tyto příkazy spouštíte na počítači, který není AD FS primární federační server, spusťte Set-MSOLAdfscontext-Computer &lt; AD FS primární server &gt; , kde &lt; AD FS primární server &gt; je interní název FQDN primárního AD FS serveru. Tato rutina vytvoří kontext, který vás připojí k AD FS.
5. Spusťte Update-MSOLFederatedDomain – doména doména &lt; &gt; . Tato rutina aktualizuje nastavení z AD FS do cloudové služby a nakonfiguruje vztah důvěryhodnosti mezi nimi.

> [!NOTE]
> Pokud potřebujete podporovat více domén nejvyšší úrovně, například contoso.com a fabrikam.com, je nutné použít přepínač **SupportMultipleDomain** u všech rutin. Další informace najdete v tématu [Podpora více domén nejvyšší úrovně](how-to-connect-install-multiple-domains.md).
>


## <a name="repair-azure-ad-trust-by-using-azure-ad-connect"></a>Oprava vztahu důvěryhodnosti služby Azure AD pomocí Azure AD Connect <a name="connectrenew"></a>
Pokud jste nakonfigurovali AD FSovou farmu a vztah důvěryhodnosti služby Azure AD pomocí Azure AD Connect, můžete pomocí Azure AD Connect zjistit, jestli pro podpisové certifikáty tokenů potřebujete nějakou akci. Pokud potřebujete prodloužit platnost certifikátů, můžete k tomu použít Azure AD Connect.

Další informace najdete v tématu [Oprava vztahu důvěryhodnosti](how-to-connect-fed-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>AD FS a kroky aktualizace certifikátu Azure AD
Podpisové certifikáty tokenů jsou standardní certifikáty x509, které se používají k zabezpečenému podepisování všech tokenů, které vystavuje federační server. Certifikáty dešifrování tokenů jsou standardní certifikáty x509, které se používají k dešifrování všech příchozích tokenů. 

Ve výchozím nastavení je AD FS nakonfigurovaná tak, aby automaticky generovala certifikáty podepisování tokenů a dešifrování tokenů, a to v počátečním čase konfigurace i v případě, že se certifikáty blíží datu vypršení platnosti.

Služba Azure AD se pokusí načíst nový certifikát z metadat federační služby 30 dní před vypršením platnosti aktuálního certifikátu. V případě, že nový certifikát není v tuto chvíli k dispozici, bude služba Azure AD nadále monitorovat metadata v pravidelných denních intervalech. Jakmile je nový certifikát k dispozici v metadatech, nastavení federace pro doménu se aktualizují o nové informace o certifikátu. Můžete použít `Get-MsolDomainFederationSettings` k ověření, jestli se v NextSigningCertificate/SigningCertificate zobrazí nový certifikát.

Další informace o podpisových certifikátech tokenů v AD FS najdete v tématu [získání a konfigurace podpisů a dešifrovacích certifikátů tokenů pro AD FS](/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
