---
title: Přehled – autorizace založená na identitách souborů Azure
description: Soubory Azure podporují ověřování na základě identity přes SMB (Server Message Block) prostřednictvím služby Azure Active Directory Domain Services (AD DS) a Služby Active Directory. Virtuální počítače windows (VM) spojené s doménou pak můžou přistupovat ke sdíleným souborům Azure pomocí přihlašovacích údajů Azure AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 737cdfaddca3a5f7532620bdafd86149e4d61f9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061073"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Přehled podpory ověřování na základě identity souborů Azure pro přístup SMB
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Informace o povolení ověřování ve službě AD pro sdílené složky Azure najdete v [tématu Povolení ověřování ve službě Active Directory přes složky SMB pro sdílené složky Azure](storage-files-identity-auth-active-directory-enable.md).

Informace o povolení ověřování Azure AD DS pro sdílené složky Azure najdete v [tématu Povolení ověřování služby Azure Active Directory Domain Services v souborech Azure](storage-files-identity-auth-active-directory-domain-service-enable.md).

## <a name="glossary"></a>Slovníček 
Je užitečné porozumět některým klíčovým pojmům týkajícím se ověřování služby Azure AD Domain Service přes SMB pro sdílené složky Azure:

-   **Ověřování protokolem Kerberos**

    Kerberos je ověřovací protokol, který se používá k ověření identity uživatele nebo hostitele. Další informace o protokolu Kerberos naleznete v [tématu Přehled ověřování pomocí protokolu Kerberos](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protokol bloku zpráv serveru (SMB)**

    SMB je standardní síťový protokol pro sdílení souborů. SMB je také známý jako společný internetový souborový systém nebo CIFS. Další informace o protokolu SMB naleznete v [tématech Microsoft SMB Protocol a CIFS Protocol Overview](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) je multitenantní cloudová služba a služba správy identit. Azure AD kombinuje základní adresářové služby, správu přístupu k aplikacím a ochranu identitdo jednoho řešení. Azure AD umožňuje vašim virtuálním počítačům (VM) s připojením k doméně přístup ke sdíleným datům Azure s vašimi přihlašovacími údaji Azure AD. Další informace najdete v tématu [Co je služba Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services (Azure AD DS)**

    Azure AD Domain Services (GA) poskytuje služby spravované domény, jako je připojení k doméně, zásady skupiny, LDAP a ověřování kerberos/NTLM. Tyto služby jsou plně kompatibilní se službou Windows Server Active Directory. Další informace naleznete v [tématu Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md).

- **Služba Active Directory Domain Services (AD DS, označovaná také jako AD)**

    Služba Active Directory (AD) (preview) poskytuje metody ukládání dat adresáře a zároveň je zpřístupňuje uživatelům sítě a správcům. Zabezpečení je integrováno se službou Active Directory prostřednictvím ověřování přihlášení a řízení přístupu k objektům v adresáři. Díky jedinému přihlášení k síti mohou správci spravovat data adresářů a organizaci v celé síti a oprávnění uživatelé sítě mohou přistupovat k prostředkům kdekoli v síti. Služba AD je běžně přijímána podniky v místním prostředí a používá přihlašovací údaje služby AD jako identitu pro řízení přístupu. Další informace naleznete v tématu [Přehled služby Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **Řízení přístupu na základě rolí Azure (RBAC)**

    Řízení přístupu na základě role v Azure umožňuje přesnou správu přístupu. Pomocí RBAC můžete spravovat přístup k prostředkům tím, že uživatelům udělíte co nejméně oprávnění potřebných k provádění jejich úloh. Další informace o RBAC najdete v [tématu Co je řízení přístupu na základě rolí (RBAC) v Azure?](../../role-based-access-control/overview.md).

## <a name="common-use-cases"></a>Běžné případy použití

Ověřování založené na identitě a podpora seznamů ACL systému Windows v souborech Azure je nejlepší využít pro následující případy použití:

### <a name="replace-on-premises-file-servers"></a>Nahrazení místních souborových serverů

Zavržení a nahrazení rozptýlených místních souborových serverů je běžný problém, se kterým se každý podnik setká při cestě modernizace IT. Sdílení souborů Azure s ověřováním ad (preview) je nejvhodnější zde, když můžete migrovat data do souborů Azure. Úplná migrace vám umožní využít výhod vysoké dostupnosti a škálovatelnosti a zároveň minimalizovat změny na straně klienta, zejména komplikovanou infrastrukturu domény služby AD. Poskytuje bezproblémové prostředí migrace koncovým uživatelům, takže mohou nadále přistupovat ke svým datům se stejnými přihlašovacími údaji pomocí svých stávajících počítačů spojených s doménou.

### <a name="lift-and-shift-applications-to-azure"></a>Zvedání a přesouvání aplikací do Azure

Když "výtah a přesunout" aplikace do cloudu, chcete zachovat stejný model ověřování pro vaše data. Jak rozšiřujeme prostředí řízení přístupu na základě identity na sdílené složky Azure, eliminuje potřebu změnit vaši aplikaci na moderní metody ověření a urychlit přechod na cloud. Sdílené složky Azure poskytuje možnost integrace s Azure AD DS (GA) nebo AD (preview) pro ověřování. Pokud váš plán má být 100% nativní pro cloud a minimalizovat úsilí o správu cloudových infrastruktur, Azure AD DS by se lépe hodilo jako plně spravovaná doménová služba. Pokud potřebujete plnou kompatibilitu s funkcemi služby AD DS (GA), můžete zvážit rozšíření prostředí služby AD do cloudu pomocí samoobslužných řadičů domény na virtuálních počítačích. Ať tak či onak, poskytujeme flexibilitu při výběru doménových služeb, které vyhovují vašim obchodním potřebám.

### <a name="backup-and-disaster-recovery-dr"></a>Zálohování a zotavení po havárii (DR)

Pokud udržujete primární úložiště souborů v místním prostředí, sdílené složky Azure mohou sloužit jako ideální úložiště pro zálohování nebo zotavení po havárii, aby se zlepšila kontinuita podnikání. Sdílené složky Azure můžete použít k zálohování dat z existujících souborových serverů při zachování seznamů DACL systému Windows. Pro scénáře zotavení po havárii můžete nakonfigurovat možnost ověřování pro podporu správného vynucení řízení přístupu při převzetí služeb při selhání.

## <a name="supported-scenarios"></a>Podporované scénáře

Následující tabulka shrnuje podporované scénáře ověřování sdílení souborů Azure pro Azure AD DS (GA) a AD (preview). Doporučujeme vybrat doménovou službu, kterou jste přijali pro klientské prostředí pro integraci se soubory Azure. Pokud už máte službu AD (preview) nastavenou místně nebo v Azure, kde jsou vaše zařízení připojená k službě AD, měli byste využít službu AD (preview) pro ověřování sdílených složek Azure. Podobně pokud jste již přijali Azure AD DS (GA), měli byste použít pro ověřování sdílených složek Azure.


|Ověřování Azure AD DS (GA)  |Ověřování ve službách AD (preview)  |
|---------|---------|
|Azure AD DS domény spojené s počítači s Windows přístup ke sdíleným souborům Azure s přihlašovacími údaji Azure AD přes SMB.     |Počítače s doménou AD se připojily k počítačům s Windows, které mají přístup ke sdíleným sfám Azure pomocí přihlašovacích údajů služby AD, které jsou synchronizovány se službou Azure AD přes SMB.         |

### <a name="unsupported-scenarios"></a>Nepodporované scénáře

- Ověřování Azure AD DS (GA) a AD (preview) nepodporují ověřování proti účtům počítačů. Místo toho můžete zvážit použití přihlašovacího účtu služby.
- Azure AD DS (GA) ověřování nepodporuje ověřování proti azure ad cloud připojená zařízení.

## <a name="advantages-of-identity-based-authentication"></a>Výhody ověřování založeného na identitě
Ověřování na základě identity pro soubory Azure nabízí několik výhod než pomocí ověřování pomocí sdíleného klíče:

-   **Rozšíření tradičního prostředí pro přístup ke sdílené složce založené na identitách do cloudu pomocí služby AD a Služby Azure AD DS**  
    Pokud máte v plánu "výtah a přesunout" aplikace do cloudu, nahrazení tradiční souborové servery s sdílené složky Azure, pak můžete chtít, aby vaše aplikace k ověření pomocí ad nebo Azure AD pověření pro přístup k datům souboru. Azure Files podporuje použití přihlašovacích údajů služby AD nebo Azure AD pro přístup ke sdíleným složkám Azure přes SMB z virtuálních počítače spojených s doménou Služby AD nebo Azure AD DS.

-   **Vynucení podrobného řízení přístupu u sdílených složek Azure**  
    Určitá identita můžete udělit oprávnění na úrovni sdílené složky, adresáře nebo souboru. Předpokládejme například, že máte několik týmů, které používají jednu sdílenou složku Azure pro spolupráci na projektu. Všem týmům můžete udělit přístup k necitlivým adresářům a zároveň omezit přístup k adresářům obsahujícím citlivá finanční data pouze vašemu finančnímu týmu. 

-   **Zálohování aklů systému Windows (označované také jako NTFS) spolu s daty**  
    Ke zálohování stávajících místních sdílených složek můžete použít sdílené složky Azure. Soubory Azure zachová vaše seznamy ACL spolu s daty při zálohování sdílené složky do sdílených složek Azure přes SMB.

## <a name="how-it-works"></a>Jak to funguje
Sdílené složky Azure podporují ověřování pomocí kerberos pro integraci s Azure AD DS (GA) nebo AD (preview). Než povolíte ověřování ve sdílených spores souborů Azure, musíte nejprve nastavit prostředí domény. Pro ověřování Azure AD DS (GA) byste měli povolit služby Azure AD Domain Services a doménu připojit k virtuálním mům, ze kterých plánujete přístup k datům souborů. Virtuální počítač spojený s doménou se musí napájet ve stejné virtuální síti (VNET) jako vaše služby Azure AD Domain Services. Podobně pro ověřování ve službě AD (preview) je třeba nastavit řadič domény služby Active Directory a doménu připojit k počítačům nebo virtuálním počítačům.

Když se identita přidružená k aplikaci spuštěné na virtuálním počítači pokusí o přístup k datům ve sdílených složek Azure, požadavek se odešle do služby Azure AD Domain Services k ověření identity. Pokud je ověření úspěšné, vrátí služba Azure AD Domain Services token Kerberos. Aplikace odešle požadavek, který obsahuje token Kerberos, a sdílené složky Azure tento token používají k autorizaci požadavku. Sdílené složky Azure obdrží token pouze a nezachová přihlašovací údaje Azure AD. Ověřování služby AD funguje podobným způsobem, kde služba AD poskytuje token protokolu Kerberos.

![Snímek obrazovky s diagramem ověřování Azure AD přes SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Povolení ověřování založeného na identitě

Ověřování založené na identitách můžete povolit buď pomocí Azure AD DS (GA) nebo AD (preview) pro sdílené složky Azure na vašich nových i stávajících účtech úložiště. Pro ověřování přístupu k souborům v účtu úložiště lze použít pouze jednu službu domény, která platí pro všechny sdílené složky v účtu. Podrobné pokyny krok za krokem k nastavení sdílených složek pro ověřování pomocí služby Azure AD DS (GA) v našem článku [Povolit ověřování azure active directory domain services na soubory Azure](storage-files-identity-auth-active-directory-domain-service-enable.md) a pokyny pro AD (preview) v našem dalším článku [Povolit ověřování služby Active Directory přes SMB pro sdílené složky Azure](storage-files-identity-auth-active-directory-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Konfigurace oprávnění na úrovni sdílení pro soubory Azure

Jakmile je povoleno ověřování Azure AD DS (GA) nebo AD (preview), můžete použít předdefinované role RBAC nebo nakonfigurovat vlastní role pro identity Azure AD a přiřadit přístupová práva k libovolným sdíleným souborům ve vašich účtech úložiště. přiřazené oprávnění umožňuje udělené identity získat přístup pouze ke sdílené položce, nic jiného, ani kořenový adresář. Stále je potřeba samostatně nakonfigurovat oprávnění adresáře nebo na úrovni souborů pro sdílené složky Azure.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Konfigurace oprávnění na úrovni adresáře nebo souboru pro soubory Azure

Sdílené složky Azure vynucují standardní oprávnění k souborům systému Windows na úrovni adresáře i souboru, včetně kořenového adresáře. Konfigurace oprávnění na úrovni adresáře nebo souboru je podporována prostředkem SMB i REST. Připojte sdílenou složku cílového souboru z virtuálního počítače a nakonfigurujte oprávnění pomocí Průzkumníka souborů Windows, [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)systému Windows nebo příkazu [Set-ACL.](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6)

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Použití klíče účtu úložiště pro oprávnění superuživatele

Uživatel, který má klíč účtu úložiště, má přístup ke sdíleným složkám Azure s oprávněními superuživatele. Oprávnění superuživatele obejdou všechna omezení řízení přístupu.

> [!IMPORTANT]
> Naším doporučeným osvědčeným postupem zabezpečení je vyhnout se sdílení klíčů účtu úložiště a kdykoli je to možné, využívat ověřování založené na identitě.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Zachování seznamů AC L adresářů a souborů při importu dat do sdílených složek Azure

Azure Files podporuje zachování seznamů ACL na úrovni adresářů nebo souborů při kopírování dat do sdílených složek Azure. Seznamy ACL v adresáři nebo souboru můžete kopírovat do sdílených složek Azure pomocí azure file sync nebo běžných sad nástrojů pro přesun souborů. Můžete například použít [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) `/copy:s` s příznakem ke kopírování dat, stejně jako ACLs do sdílené složky Azure. AcLs jsou zachovány ve výchozím nastavení, není nutné povolit ověřování na základě identity na vašem účtu úložiště zachovat ACLs.

## <a name="pricing"></a>Ceny
Neexistuje žádný další poplatek za službu, která by umožnila ověřování na základě identity přes SMB na vašem účtu úložiště. Další informace o cenách najdete v [tématu Ceny souborů Azure](https://azure.microsoft.com/pricing/details/storage/files/) a [stránky cen Azure AD Domain Services,](https://azure.microsoft.com/pricing/details/active-directory-ds/) pokud hledáte informace AAD DS.

## <a name="next-steps"></a>Další kroky
Další informace o souborech Azure a ověřování na základě identity přes SMB najdete v těchto prostředcích:

- [Plánování nasazení služby Soubory Azure](storage-files-planning.md)
- [Povolení ověřování služby Active Directory přes SMB pro sdílené složky Azure](storage-files-identity-auth-active-directory-enable.md)
- [Povolení ověřování služby Azure Active Directory Domain Services u souborů Azure](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [Nejčastější dotazy](storage-files-faq.md)
