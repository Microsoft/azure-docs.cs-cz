---
title: Přehled – ověřování na základě identity ve službě Azure Files
description: Služba soubory Azure podporuje ověřování na základě identity přes protokol SMB (Server Message Block) prostřednictvím Azure Active Directory Domain Services (služba AD DS) a služby Active Directory. Virtuální počítače s Windows připojené k doméně pak můžou přistupovat ke sdíleným složkám Azure pomocí přihlašovacích údajů Azure AD.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 673bf3be59d72b2cc62b9f92af353fee235c5ddc
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598812"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Přehled podpory ověřování na základě identity souborů Azure pro přístup přes protokol SMB
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Informace o tom, jak povolit ověřování AD pro sdílené složky Azure, najdete v tématu [Povolení ověřování služby Active Directory přes protokol SMB pro sdílené složky Azure](storage-files-identity-auth-active-directory-enable.md).

Informace o tom, jak povolit ověřování Azure služba AD DS pro sdílené složky Azure, najdete v tématu [povolení Azure Active Directory Domain Servicesho ověřování u souborů Azure](storage-files-identity-auth-active-directory-domain-service-enable.md).

## <a name="glossary"></a>Glosář 
Je užitečné pochopit některé klíčové podmínky týkající se ověřování služby Azure AD Domain Services přes SMB pro sdílené složky Azure:

-   **Ověřování protokolu Kerberos**

    Kerberos je ověřovací protokol, který se používá k ověření identity uživatele nebo hostitele. Další informace o protokolu Kerberos najdete v tématu [Přehled ověřování protokolu Kerberos](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protokol SMB (Server Message Block)**

    SMB je standardní protokol pro sdílení souborů v síti. SMB se označuje také jako Common Internet File System nebo CIFS. Další informace o protokolu SMB najdete v tématu [Přehled protokolu SMB a protokolu CIFS](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) je víceklientské cloudové služby Microsoftu a služba pro správu identit založené na víceklientské architektuře. Azure AD kombinuje základní adresářové služby, správu přístupu k aplikacím a ochranu identit do jediného řešení. Azure AD umožňuje virtuálním počítačům s Windows připojeným k doméně přistupovat ke sdíleným složkám Azure pomocí vašich přihlašovacích údajů Azure AD. Další informace najdete v tématu [co je Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services (Azure služba AD DS)**

    Azure AD Domain Services (GA) poskytuje spravované doménové služby, jako je připojení k doméně, zásady skupiny, LDAP a ověřování pomocí protokolu Kerberos/NTLM. Tyto služby jsou plně kompatibilní se službou Windows Server Active Directory. Další informace najdete v tématu [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md).

- **Active Directory Domain Services (služba AD DS, označovaný také jako AD)**

    Služba Active Directory (AD) (ve verzi Preview) poskytuje metody pro ukládání dat adresáře a jejich zpřístupnění uživatelům a správcům sítě. Zabezpečení je integrováno se službou Active Directory prostřednictvím ověřování přihlášení a řízení přístupu k objektům v adresáři. Pomocí jediného přihlášení k síti můžou správci spravovat data adresáře a organizaci v celé své síti a autorizovaní uživatelé sítě mají přístup k prostředkům kdekoli v síti. Služba AD obvykle přijímá podniky v místním prostředí a jako identitu pro řízení přístupu používají přihlašovací údaje služby AD. Další informace najdete v tématu [přehled Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **Access Control na základě rolí Azure (RBAC)**

    Řízení přístupu na základě role v Azure umožňuje přesnou správu přístupu. Pomocí RBAC můžete spravovat přístup k prostředkům tím, že uživatelům udělíte nejnižší oprávnění potřebná k provádění svých úloh. Další informace o RBAC najdete v tématu [co je řízení přístupu na základě role (RBAC) v Azure?](../../role-based-access-control/overview.md).

## <a name="common-use-cases"></a>Běžné případy použití

Ověřování na základě identity a podpora seznamů ACL pro Windows v Azure Files se nejlépe využije v následujících případech:

### <a name="replace-on-premises-file-servers"></a>Nahrazení místních souborových serverů

Vyřazení a nahrazování geograficky místních souborových serverů je běžný problém, ke kterému v každé podnikové síti dojde v cestě k modernizaci IT. Pokud chcete migrovat data do souborů Azure, je tu nejlepší, co je to pro sdílené složky Azure s ověřováním AD (Preview). Kompletní migrace vám umožní využít výhody vysoké dostupnosti a škálovatelnosti a zároveň zároveň minimalizovat změny na straně klienta, hlavně složitou infrastrukturu domén AD. Poskytuje bezproblémové možnosti migrace koncovým uživatelům, aby mohli nadále přistupovat ke svým datům se stejnými přihlašovacími údaji pomocí jejich stávajících počítačů připojených k doméně.

### <a name="lift-and-shift-applications-to-azure"></a>Nazvednutí a posunutí aplikací do Azure

Když aplikace nazvednete a posunete do cloudu, chcete zachovat stejný model ověřování pro vaše data. Jak rozšiřujeme prostředí pro řízení přístupu na základě identity na sdílené složky Azure, eliminuje nutnost změnit aplikaci na moderní metody ověřování a urychlit její přijetí. Sdílené složky Azure poskytují možnost integrace s Azure služba AD DS (GA) nebo AD (Preview) pro ověřování. Pokud má váš plán 100% Cloud Native a minimalizuje úsilí při správě cloudových infrastruktur, Azure služba AD DS by byl lépe vhodný jako plně spravovaná Doménová služba. Pokud potřebujete plnou kompatibilitu s funkcemi služba AD DS (GA), možná budete chtít zvážit rozšíření prostředí AD na cloud pomocí samoobslužného hostování řadičů domény na virtuálních počítačích. V obou případech nabízíme flexibilitu při výběru doménových služeb, které vyhovují vašim obchodním potřebám.

### <a name="backup-and-disaster-recovery-dr"></a>Zálohování a zotavení po havárii (DR)

Pokud udržujete primární místní úložiště souborů, můžou sdílené složky Azure sloužit jako ideální úložiště pro zálohování nebo zotavení po havárii, aby se zlepšila Kontinuita podnikových prostředí. Sdílené složky Azure můžete použít k zálohování dat z existujících souborových serverů při zachování seznamů DACL v systému Windows. V případě scénářů zotavení po havárii můžete nakonfigurovat možnost ověřování pro podporu správného vynucení řízení přístupu při převzetí služeb při selhání.

## <a name="supported-scenarios"></a>Podporované scénáře

Následující tabulka shrnuje podporované scénáře ověřování sdílených složek Azure pro Azure služba AD DS (GA) a AD (Preview). Pro integraci se soubory Azure doporučujeme vybrat doménovou službu, kterou jste přijali pro klientské prostředí. Pokud máte službu AD (Preview) již nastavenou místně nebo v Azure, kde jsou vaše zařízení připojená k doméně služby AD, měli byste pro ověřování sdílených složek Azure využít službu AD (Preview). Podobně platí, že pokud jste už Azure služba AD DS (GA) schválili, měli byste ho použít pro ověřování sdílených složek Azure.


|Ověřování Azure služba AD DS (GA)  |Ověřování AD (Preview)  |
|---------|---------|
|Počítače s Windows připojené k doméně Azure služba AD DS můžou přistupovat ke sdíleným složkám Azure pomocí přihlašovacích údajů Azure AD přes SMB.     |Počítače se systémem Windows připojené k doméně služby AD mají přístup ke sdíleným složkám Azure s přihlašovacími údaji služby AD, které jsou synchronizované s Azure AD přes SMB.         |

### <a name="unsupported-scenarios"></a>Nepodporované scénáře

- Ověřování Azure služba AD DS (GA) a AD (Preview) nepodporují ověřování u účtů počítačů. Místo toho můžete použít účet přihlášení služby.
- Ověřování Azure služba AD DS (GA) nepodporuje ověřování na zařízeních připojených ke cloudu Azure AD.

## <a name="advantages-of-identity-based-authentication"></a>Výhody ověřování na základě identity
Ověřování na základě identity pro soubory Azure nabízí oproti použití ověřování pomocí sdíleného klíče několik výhod:

-   **Rozšiřování klasického prostředí pro přístup ke sdíleným složkám na základě identity v cloudu pomocí AD a Azure služba AD DS**  
    Pokud plánujete, že aplikaci navedete do cloudu a nahradíte tradiční souborové servery službou Azure File Shares, můžete chtít, aby se vaše aplikace pro přístup k datům souborů ověřovala buď s přihlašovacími údaji služby AD nebo Azure AD. Služba soubory Azure podporuje použití přihlašovacích údajů AD nebo Azure AD pro přístup ke sdíleným složkám Azure přes protokol SMB z AD nebo z Azure služba AD DS virtuálních počítačů připojených k doméně.

-   **Vysazení podrobného řízení přístupu ke sdíleným složkám Azure**  
    Můžete udělit oprávnění ke konkrétní identitě na úrovni sdílených složek, adresářů nebo souborů. Předpokládejme například, že máte několik týmů s jednou sdílenou složkou Azure pro spolupráci s projekty. Všem týmům můžete udělit přístup k necitlivým adresářům a zároveň omezit přístup k adresářům obsahujícím citlivá finanční data jenom na váš finanční tým. 

-   **Zálohování seznamů řízení přístupu (ACL) systému Windows (označované také jako NTFS) společně s daty**  
    Pomocí sdílených složek Azure můžete zálohovat stávající místní sdílené složky. Soubory Azure při zálohování sdílené složky do sdílených složek Azure pomocí protokolu SMB zachovává vaše seznamy ACL spolu s Vašimi daty.

## <a name="how-it-works"></a>Jak to funguje
Sdílené složky Azure podporují ověřování pomocí protokolu Kerberos pro integraci s Azure služba AD DS (GA) nebo AD (Preview). Než budete moct povolit ověřování sdílených složek Azure, musíte nejdřív nastavit prostředí domény. Pro ověřování Azure služba AD DS (GA) byste měli povolit Azure AD Domain Services a připojení k doméně virtuálním počítačům, ze kterých plánujete přístup k datovým souborům. Váš virtuální počítač připojený k doméně se musí nacházet ve stejné virtuální síti (VNET) jako vaše Azure AD Domain Services. Podobně pro ověřování AD (ve verzi Preview) je potřeba nastavit řadič domény služby Active Directory a připojit se k virtuálním počítačům do domény.

Když se identita přidružená k aplikaci běžící na virtuálním počítači pokusí o přístup k datům ve sdílených složkách Azure, pošle se požadavek do Azure AD Domain Services k ověření identity. Pokud je ověření úspěšné, Azure AD Domain Services vrátí token protokolu Kerberos. Aplikace odešle požadavek, který obsahuje token protokolu Kerberos, a sdílené složky Azure používají tento token k autorizaci žádosti. Sdílené složky Azure obdrží jenom token a nezachovávají přihlašovací údaje Azure AD. Ověřování AD funguje podobným způsobem, kde AD poskytuje token protokolu Kerberos.

![Snímek obrazovky znázorňující diagram ověřování Azure AD přes protokol SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Povolit ověřování na základě identity

Ověřování na základě identity můžete povolit buď pomocí Azure služba AD DS (GA), nebo AD (Preview) pro sdílené složky Azure na vašich nových a existujících účtech úložiště. Pro ověřování přístupu k souborům v účtu úložiště, která platí pro všechny sdílené složky v účtu, se dá použít jenom jedna Doménová služba. Podrobný postup, jak nastavit sdílené složky pro ověřování pomocí Azure služba AD DS (GA) v našem článku [povolení Azure Active Directory Domain Servicesho ověřování v souborech Azure](storage-files-identity-auth-active-directory-domain-service-enable.md) a doprovodné materiály k AD (Preview) v našem dalším článku povolení [ověřování služby Active Directory přes protokol SMB pro sdílené složky Azure](storage-files-identity-auth-active-directory-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Konfigurace oprávnění na úrovni sdílené složky pro soubory Azure

Jakmile je povolené ověřování Azure služba AD DS (GA) nebo AD (Preview), můžete použít předdefinované role RBAC nebo nakonfigurovat vlastní role pro identity Azure AD a přiřadit přístup k libovolným sdíleným složkám v účtech úložiště. přiřazené oprávnění umožňuje udělené identitě získat přístup pouze ke sdílené složce, nic jiného, ani kořenovému adresáři. Ke sdíleným složkám Azure se pořád potřebuje samostatně nakonfigurovat oprávnění pro adresáře nebo soubory.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Konfigurace oprávnění adresářů nebo souborů pro soubory Azure

Sdílené složky Azure vysazují standardní oprávnění souborů Windows v adresáři i na úrovni souborů, včetně kořenového adresáře. Konfigurace oprávnění na úrovni adresáře nebo souborů se podporuje přes protokol SMB i REST. Připojte cílovou sdílenou složku z virtuálního počítače a nakonfigurujte oprávnění pomocí Průzkumníka souborů Windows, Windows [Icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)nebo příkazu [set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6) .

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Použijte klíč účtu úložiště pro oprávnění naduživatelem.

Uživatel, který má klíč účtu úložiště, má přístup ke sdíleným složkám Azure pomocí uživatelských oprávnění. Oprávnění naduživatelem obcházejí všechna omezení řízení přístupu.

> [!IMPORTANT]
> Doporučeným postupem z hlediska zabezpečení je vyhnout se sdílení klíčů účtu úložiště a využívat ověřování na základě identity, kdykoli je to možné.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Zachování seznamů ACL adresářů a souborů při importu dat do sdílených složek Azure

Azure Files podporuje zachovávání seznamů ACL na úrovni adresáře nebo souborů při kopírování dat do sdílených složek Azure. Seznamy řízení přístupu můžete zkopírovat do složky nebo souboru do sdílených složek Azure pomocí Azure File Sync nebo běžných nástrojů pro přesun souborů. Pomocí příkazu [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) s příznakem `/copy:s` můžete například kopírovat data i seznamy ACL do sdílené složky Azure. Seznamy řízení přístupu (ACL) jsou ve výchozím nastavení zachované, nemusíte u svého účtu úložiště povolit ověřování na základě identity, abyste zachovali seznamy ACL.

## <a name="pricing"></a>Ceny
Pro povolení ověřování na základě identity přes protokol SMB v účtu úložiště není k dispozici žádný další poplatek za službu. Další informace o cenách najdete v tématu ceny za [Azure Files](https://azure.microsoft.com/pricing/details/storage/files/) a [Azure AD Domain Services cenové](https://azure.microsoft.com/pricing/details/active-directory-ds/) stránky, pokud hledáte informace o AAD DS.

## <a name="next-steps"></a>Další kroky
Další informace o souborech Azure a ověřování na základě identity přes SMB najdete v těchto zdrojích informací:

- [Plánování nasazení služby Soubory Azure](storage-files-planning.md)
- [Povolení ověřování služby Active Directory přes protokol SMB pro sdílené složky Azure](storage-files-identity-auth-active-directory-enable.md)
- [Povolení ověřování Azure Active Directory Domain Services u souborů Azure](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [Nejčastější dotazy](storage-files-faq.md)
