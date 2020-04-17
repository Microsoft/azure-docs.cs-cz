---
title: Přehled – autorizace založená na identitách souborů Azure
description: Soubory Azure podporují ověřování na základě identity přes SMB (Server Message Block) prostřednictvím služby Azure Active Directory Domain Services (AD DS) a Služby Active Directory. Virtuální počítače windows (VM) spojené s doménou pak můžou přistupovat ke sdíleným souborům Azure pomocí přihlašovacích údajů Azure AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: rogarana
ms.openlocfilehash: 7d9f8ccb4273d1378c4826dea420c4edca2f8ac3
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536572"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Přehled podpory ověřování na základě identity souborů Azure pro přístup SMB
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Informace o povolení místního ověřování služby Active Directory Domain Services pro sdílené složky Azure (preview) najdete v [tématu Povolení místního ověřování služby Active Directory Domain Services přes SMB pro sdílené složky Azure](storage-files-identity-auth-active-directory-enable.md).

Informace o povolení ověřování Azure AD DS pro sdílené složky Azure najdete v [tématu Povolení ověřování služby Azure Active Directory Domain Services v souborech Azure](storage-files-identity-auth-active-directory-domain-service-enable.md).

## <a name="glossary"></a>Glosář 
Je užitečné porozumět některým klíčovým pojmům týkajícím se ověřování služby Azure AD Domain Service přes SMB pro sdílené složky Azure:

-   **Ověřování protokolem Kerberos**

    Kerberos je ověřovací protokol, který se používá k ověření identity uživatele nebo hostitele. Další informace o protokolu Kerberos naleznete v [tématu Přehled ověřování pomocí protokolu Kerberos](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protokol bloku zpráv serveru (SMB)**

    SMB je standardní síťový protokol pro sdílení souborů. SMB je také známý jako společný internetový souborový systém nebo CIFS. Další informace o protokolu SMB naleznete v [tématech Microsoft SMB Protocol a CIFS Protocol Overview](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) je multitenantní cloudová služba a služba správy identit. Azure AD kombinuje základní adresářové služby, správu přístupu k aplikacím a ochranu identitdo jednoho řešení. Virtuální počítače s Windows s Azure AD mají přístup ke sdíleným souborům Azure pomocí přihlašovacích údajů Azure AD. Další informace najdete v tématu [Co je služba Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS poskytuje služby spravované domény, jako je připojení k doméně, zásady skupiny, LDAP a ověřování kerberos/NTLM. Tyto služby jsou plně kompatibilní se službou Active Directory Domain Services. Další informace naleznete v [tématu Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md).

- **Místní služba Active Directory Domain Services (AD DS)**

    Místní integrace služby AD DS (AD DS) se soubory Azure (preview) poskytuje metody pro ukládání dat adresáře a zároveň je zpřístupňuje síťovým uživatelům a správcům. Zabezpečení je integrováno se službou AD DS prostřednictvím ověřování přihlášení a řízení přístupu k objektům v adresáři. Díky jedinému přihlášení k síti mohou správci spravovat data adresářů a organizaci v celé síti a oprávnění uživatelé sítě mohou přistupovat k prostředkům kdekoli v síti. Služba AD DS je běžně přijímána podniky v místních prostředích a přihlašovací údaje služby AD DS se používají jako identita pro řízení přístupu. Další informace naleznete v tématu [Přehled služby Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **Řízení přístupu na základě rolí Azure (RBAC)**

    Řízení přístupu na základě role v Azure umožňuje přesnou správu přístupu. Pomocí RBAC můžete spravovat přístup k prostředkům tím, že uživatelům udělíte co nejméně oprávnění potřebných k provádění jejich úloh. Další informace o RBAC najdete v [tématu Co je řízení přístupu na základě rolí (RBAC) v Azure?](../../role-based-access-control/overview.md).

## <a name="common-use-cases"></a>Běžné případy použití

Ověřování založené na identitě a podpora seznamů ACL systému Windows v souborech Azure je nejlepší využít pro následující případy použití:

### <a name="replace-on-premises-file-servers"></a>Nahrazení místních souborových serverů

Zavržení a nahrazení rozptýlených místních souborových serverů je běžný problém, se kterým se každý podnik setká při cestě modernizace IT. Sdílení souborů Azure s místním ověřováním služby AD DS (preview) se sem nejlépe hodí, když můžete migrovat data do souborů Azure. Úplná migrace vám umožní využít výhod vysoké dostupnosti a škálovatelnosti a zároveň minimalizovat změny na straně klienta. Poskytuje bezproblémové prostředí migrace koncovým uživatelům, takže mohou nadále přistupovat ke svým datům se stejnými přihlašovacími údaji pomocí svých stávajících počítačů spojených s doménou.

### <a name="lift-and-shift-applications-to-azure"></a>Zvedání a přesouvání aplikací do Azure

Když zvednete a přesunete aplikace do cloudu, chcete zachovat stejný model ověřování pro vaše data. Jak rozšiřujeme prostředí řízení přístupu na základě identity na sdílené složky Azure, eliminuje potřebu změnit vaši aplikaci na moderní metody ověření a urychlit přechod na cloud. Sdílené složky Azure poskytují možnost integrace s Azure AD DS nebo místní službou AD DS (preview) pro ověřování. Pokud váš plán má být 100% nativní pro cloud a minimalizovat úsilí o správu cloudových infrastruktur, Azure AD DS by se lépe hodilo jako plně spravovaná doménová služba. Pokud potřebujete plnou kompatibilitu s funkcemi služby AD DS, můžete zvážit rozšíření prostředí služby AD DS do cloudu pomocí samoobslužných řadičů domény na virtuálních počítačích. Ať tak či onak, poskytujeme flexibilitu při výběru doménových služeb, které vyhovují vašim obchodním potřebám.

### <a name="backup-and-disaster-recovery-dr"></a>Zálohování a zotavení po havárii (DR)

Pokud udržujete primární úložiště souborů v místním prostředí, sdílené složky Azure mohou sloužit jako ideální úložiště pro zálohování nebo zotavení po havárii, aby se zlepšila kontinuita podnikání. Sdílené složky Azure můžete použít k zálohování dat z existujících souborových serverů při zachování seznamů DACL systému Windows. Pro scénáře zotavení po havárii můžete nakonfigurovat možnost ověřování pro podporu správného vynucení řízení přístupu při převzetí služeb při selhání.

## <a name="supported-scenarios"></a>Podporované scénáře

Následující tabulka shrnuje podporované scénáře ověřování sdílení souborů Azure pro Azure AD DS a místní službu AD DS (preview). Doporučujeme vybrat doménovou službu, kterou jste přijali pro klientské prostředí pro integraci se soubory Azure. Pokud už máte službu AD DS (preview) nastavenou místně nebo v Azure, kde jsou vaše zařízení připojená k vaší službě AD, měli byste využít službu AD DS (preview) pro ověřování sdílených složek Azure. Podobně pokud jste již přijali Azure AD DS (GA), měli byste použít pro ověřování sdílených složek Azure.


|Ověřování Azure AD DS  | místní ověřování služby AD DS (preview)  |
|---------|---------|
|Počítače s Windows s Azure AD DS můžou přistupovat ke sdíleným souborům Azure pomocí přihlašovacích údajů Azure AD přes SMB.     |Místní počítače se systémem Windows se službou AD DS mají přístup ke sdíleným souborům Azure s místními přihlašovacími údaji služby Active Directory, které jsou synchronizovány se službou Azure AD přes SMB.         |

### <a name="unsupported-scenarios"></a>Nepodporované scénáře

- Azure AD DS a místní ověřování služby AD DS nepodporují ověřování proti účtům počítačů. Místo toho můžete zvážit použití přihlašovacího účtu služby.
- Ověřování Azure AD DS nepodporuje ověřování proti zařízením spojených s Azure AD.

## <a name="advantages-of-identity-based-authentication"></a>Výhody ověřování založeného na identitě
Ověřování na základě identity pro soubory Azure nabízí několik výhod než pomocí ověřování pomocí sdíleného klíče:

-   **Rozšíření tradičního prostředí přístupu ke sdílené složce založené na identitách do cloudu pomocí místníslužby AD DS a Azure AD DS**  
    Pokud máte v plánu zvednout a přesunout aplikaci do cloudu a nahradit tradiční souborové servery sdílenými složkami Azure, můžete chtít, aby se vaše aplikace ověřovala pomocí místních přihlašovacích údajů služby AD DS nebo Azure AD DS pro přístup k datům souborů. Azure Files podporuje použití místních přihlašovacích údajů služby AD DS nebo Azure AD DS pro přístup ke sdíleným složkám Azure přes SMB z místních virtuálních aplikací Služby AD DS nebo virtuálních aplikací spojených s doménou Azure AD DS.

-   **Vynucení podrobného řízení přístupu u sdílených složek Azure**  
    Určitá identita můžete udělit oprávnění na úrovni sdílené složky, adresáře nebo souboru. Předpokládejme například, že máte několik týmů, které používají jednu sdílenou složku Azure pro spolupráci na projektu. Všem týmům můžete udělit přístup k necitlivým adresářům a zároveň omezit přístup k adresářům obsahujícím citlivá finanční data pouze vašemu finančnímu týmu. 

-   **Zálohování aklů systému Windows (označované také jako NTFS) spolu s daty**  
    Ke zálohování stávajících místních sdílených složek můžete použít sdílené složky Azure. Soubory Azure zachová vaše seznamy ACL spolu s daty při zálohování sdílené složky do sdílených složek Azure přes SMB.

## <a name="how-it-works"></a>Jak to funguje

Sdílené složky Azure podporují ověřování protokolu Kerberos pro integraci s Azure AD DS nebo místní službou AD DS (preview). Než povolíte ověřování ve sdílených spores souborů Azure, musíte nejprve nastavit prostředí domény. Pro ověřování Azure AD DS byste měli povolit služby Azure AD Domain Services a doménu připojit k virtuálním počítačům, ze kterých plánujete přístup k datům souborů. Virtuální počítač spojený s doménou se musí napájet ve stejné virtuální síti (VNET) jako váš Azure AD DS. Podobně pro místní ověřování služby AD DS (preview) je třeba nastavit řadič domény a doménu připojit k počítačům nebo virtuálním počítačům.

Když se identita přidružená k aplikaci spuštěné na virtuálním počítači pokusí o přístup k datům ve sdílených složek Azure, požadavek se odešle do Služby Azure AD DS k ověření identity. Pokud je ověření úspěšné, Azure AD DS vrátí token Kerberos. Aplikace odešle požadavek, který obsahuje token Kerberos, a sdílené složky Azure tento token používají k autorizaci požadavku. Sdílené složky Azure obdrží jenom token a nezachová azure ad DS přihlašovací údaje. Místní ověřování služby AD DS funguje podobným způsobem, kde vaše služby AD DS poskytuje token Protokolu Kerberos.

![Snímek obrazovky s diagramem ověřování Azure AD přes SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Povolení ověřování založeného na identitě

Ověřování na základě identit můžete povolit buď pomocí Azure AD DS, nebo místní služby AD DS (preview) pro sdílené složky Azure na vašich nových i stávajících účtech úložiště. Pro ověřování přístupu k souborům v účtu úložiště lze použít pouze jednu službu domény, která platí pro všechny sdílené složky v účtu. Podrobné pokyny k nastavení sdílených složek pro ověřování pomocí služby Azure AD DS v našem článku [Povolit ověřování služby Azure Active Directory Domain Services v souborech Azure](storage-files-identity-auth-active-directory-domain-service-enable.md) a pokyny pro místní službu AD DS (preview) v našem dalším článku [Povolit místní ověřování služby Active Directory Domain Services přes SMB pro sdílené složky Azure](storage-files-identity-auth-active-directory-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Konfigurace oprávnění na úrovni sdílení pro soubory Azure

Jakmile je povoleno ověřování Azure AD DS nebo místní ověřování služby AD DS (preview), můžete použít předdefinované role RBAC nebo nakonfigurovat vlastní role pro identity Azure AD a přiřadit přístupová práva ke všem sdíleným souborům ve vašich účtech úložiště. Přiřazené oprávnění umožňuje udělené identity získat přístup pouze ke sdílené položce, nic jiného, ani kořenový adresář. Stále je potřeba samostatně nakonfigurovat oprávnění adresáře nebo na úrovni souborů pro sdílené složky Azure.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Konfigurace oprávnění na úrovni adresáře nebo souboru pro soubory Azure

Sdílené složky Azure vynucují standardní oprávnění k souborům systému Windows na úrovni adresáře i souboru, včetně kořenového adresáře. Konfigurace oprávnění na úrovni adresáře nebo souboru je podporována prostředkem SMB i REST. Připojte sdílenou složku cílového souboru z virtuálního počítače a nakonfigurujte oprávnění pomocí Průzkumníka souborů Windows, [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)systému Windows nebo příkazu [Set-ACL.](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6)

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Použití klíče účtu úložiště pro oprávnění superuživatele

Uživatel s klíčem účtu úložiště může přistupovat ke sdíleným složkám Azure s oprávněními superuživatele. Oprávnění superuživatele obejdou všechna omezení řízení přístupu.

> [!IMPORTANT]
> Naším doporučeným osvědčeným postupem zabezpečení je vyhnout se sdílení klíčů účtu úložiště a využívat ověřování na základě identity, kdykoli je to možné.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Zachování seznamů AC L adresářů a souborů při importu dat do sdílených složek Azure

Azure Files podporuje zachování seznamů ACL na úrovni adresářů nebo souborů při kopírování dat do sdílených složek Azure. Seznamy ACL v adresáři nebo souboru můžete kopírovat do sdílených složek Azure pomocí azure file sync nebo běžných sad nástrojů pro přesun souborů. Můžete například použít [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) `/copy:s` s příznakem ke kopírování dat, stejně jako ACLs do sdílené složky Azure. AcLs jsou zachovány ve výchozím nastavení, není nutné povolit ověřování na základě identity na vašem účtu úložiště zachovat ACLs.

## <a name="pricing"></a>Ceny
Neexistuje žádný další poplatek za službu, která by umožnila ověřování na základě identity přes SMB na vašem účtu úložiště. Další informace o cenách najdete v [tématu Ceny souborů Azure](https://azure.microsoft.com/pricing/details/storage/files/) a [ceny služby Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Další kroky
Další informace o souborech Azure a ověřování na základě identity přes SMB najdete v těchto prostředcích:

- [Plánování nasazení služby Soubory Azure](storage-files-planning.md)
- [Povolení místního ověřování služby Active Directory Domain Services přes SMB pro sdílené složky Azure](storage-files-identity-auth-active-directory-enable.md)
- [Povolení ověřování služby Azure Active Directory Domain Services u souborů Azure](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [Nejčastější dotazy](storage-files-faq.md)
