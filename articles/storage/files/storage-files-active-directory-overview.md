---
title: Přehled – ověřování na základě identity ve službě Azure Files
description: Služba soubory Azure podporuje ověřování na základě identity přes protokol SMB (Server Message Block) prostřednictvím Azure Active Directory Domain Services (služba AD DS) a služby Active Directory. Virtuální počítače s Windows připojené k doméně pak můžou přistupovat ke sdíleným složkám Azure pomocí přihlašovacích údajů Azure AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: rogarana
ms.openlocfilehash: 93dd73b5a9149474be771537f47b448c5c4d7d8f
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629321"
---
# <a name="overview-of-azure-files-identity-based-authentication-options-for-smb-access"></a>Přehled možností ověřování na základě identity souborů Azure pro přístup přes protokol SMB
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Informace o tom, jak povolit místní ověřování Active Directory Domain Services pro sdílené složky Azure, najdete v tématu [Povolení ověřování místní Active Directory Domain Services pomocí protokolu SMB pro sdílené složky Azure](storage-files-identity-auth-active-directory-enable.md).

Informace o tom, jak povolit ověřování Azure služba AD DS pro sdílené složky Azure, najdete v tématu [povolení Azure Active Directory Domain Servicesho ověřování u souborů Azure](storage-files-identity-auth-active-directory-domain-service-enable.md).

## <a name="glossary"></a>Slovníček 
Je užitečné pochopit některé klíčové podmínky týkající se ověřování služby Azure AD Domain Services přes SMB pro sdílené složky Azure:

-   **Ověřování protokolu Kerberos**

    Kerberos je ověřovací protokol, který se používá k ověření identity uživatele nebo hostitele. Další informace o protokolu Kerberos najdete v tématu [Přehled ověřování protokolu Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protokol SMB (Server Message Block)**

    SMB je standardní protokol pro sdílení souborů v síti. SMB se označuje také jako Common Internet File System nebo CIFS. Další informace o protokolu SMB najdete v tématu [Přehled protokolu SMB a protokolu CIFS](/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) je víceklientské cloudové služby Microsoftu a služba pro správu identit založené na víceklientské architektuře. Azure AD kombinuje základní adresářové služby, správu přístupu k aplikacím a ochranu identit do jediného řešení. Virtuální počítače s Windows připojené k Azure AD mají přístup ke sdíleným složkám Azure pomocí vašich přihlašovacích údajů Azure AD. Další informace najdete v tématu [co je Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure Active Directory Domain Services (Azure služba AD DS)**

    Azure služba AD DS poskytuje spravované doménové služby, jako je připojení k doméně, zásady skupiny, LDAP a ověřování pomocí protokolu Kerberos/NTLM. Tyto služby jsou plně kompatibilní s Active Directory Domain Services. Další informace najdete v tématu [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md).

- **Místní Active Directory Domain Services (služba AD DS)**

    Integrace s místními Active Directory Domain Services (služba AD DS) se službou Azure Files poskytuje metody pro ukládání dat adresáře a jejich zpřístupnění uživatelům a správcům sítě. Zabezpečení je integrováno do služba AD DS prostřednictvím ověřování přihlášení a řízení přístupu k objektům v adresáři. Pomocí jediného přihlášení k síti můžou správci spravovat data adresáře a organizaci v celé své síti a autorizovaní uživatelé sítě mají přístup k prostředkům kdekoli v síti. Služba AD DS obvykle přijímá podniky v místních prostředích a služba AD DS přihlašovací údaje se používají jako identita pro řízení přístupu. Další informace najdete v tématu [přehled Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **Řízení přístupu na základě role Azure (Azure RBAC)**

    Řízení přístupu na základě role Azure (Azure RBAC) umožňuje jemně odstupňovanou správu přístupu pro Azure. Pomocí Azure RBAC můžete spravovat přístup k prostředkům tím, že uživatelům udělíte nejnižší oprávnění potřebná k provádění svých úloh. Další informace o službě Azure RBAC najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)?](../../role-based-access-control/overview.md).

## <a name="common-use-cases"></a>Běžné případy použití

Ověřování na základě identity a podpora seznamů ACL pro Windows v Azure Files se nejlépe využije v následujících případech:

### <a name="replace-on-premises-file-servers"></a>Nahrazení místních souborových serverů

Vyřazení a nahrazování geograficky místních souborových serverů je běžný problém, ke kterému v každé podnikové síti dojde v cestě k modernizaci IT. Sdílené složky Azure s ověřováním v místním služba AD DS jsou nejlépe vyhovující tomuto umístění, když můžete migrovat data do souborů Azure. Kompletní migrace vám umožní využít výhody vysoké dostupnosti a škálovatelnosti a zároveň minimalizovat změny na straně klienta. Poskytuje bezproblémové možnosti migrace koncovým uživatelům, aby mohli nadále přistupovat ke svým datům se stejnými přihlašovacími údaji pomocí jejich stávajících počítačů připojených k doméně.

### <a name="lift-and-shift-applications-to-azure"></a>Nazvednutí a posunutí aplikací do Azure

Při nazvednutí a posunutí aplikací do cloudu chcete zachovat stejný model ověřování pro vaše data. Jak rozšiřujeme prostředí pro řízení přístupu na základě identity na sdílené složky Azure, eliminuje nutnost změnit aplikaci na moderní metody ověřování a urychlit její přijetí. Sdílené složky Azure poskytují možnost integrace s Azure služba AD DS nebo místní služba AD DS pro ověřování. Pokud má váš plán 100% Cloud Native a minimalizuje úsilí při správě cloudových infrastruktur, Azure služba AD DS by byl lépe vhodný jako plně spravovaná Doménová služba. Pokud potřebujete plnou kompatibilitu s funkcemi služba AD DS, možná budete chtít zvážit rozšíření služba AD DS prostředí do cloudu pomocí samoobslužného hostování řadičů domény na virtuálních počítačích. V obou případech nabízíme flexibilitu při výběru doménových služeb, které vyhovují vašim obchodním potřebám.

### <a name="backup-and-disaster-recovery-dr"></a>Zálohování a zotavení po havárii (DR)

Pokud udržujete primární místní úložiště souborů, můžou sdílené složky Azure sloužit jako ideální úložiště pro zálohování nebo zotavení po havárii, aby se zlepšila Kontinuita podnikových prostředí. Sdílené složky Azure můžete použít k zálohování dat z existujících souborových serverů při zachování seznamů DACL v systému Windows. V případě scénářů zotavení po havárii můžete nakonfigurovat možnost ověřování pro podporu správného vynucení řízení přístupu při převzetí služeb při selhání.

## <a name="supported-scenarios"></a>Podporované scénáře

Následující tabulka shrnuje podporované scénáře ověřování sdílených složek Azure pro Azure služba AD DS a místní služba AD DS. Pro integraci se soubory Azure doporučujeme vybrat doménovou službu, kterou jste přijali pro klientské prostředí. Pokud jste už služba AD DS místně nebo v Azure, kde jsou vaše zařízení připojená k vaší službě AD, měli byste pro ověřování sdílených složek Azure využít služba AD DS. Podobně pokud jste už služba AD DS Azure, měli byste použít k ověřování sdílených složek Azure.


|Ověřování Azure služba AD DS  | Ověřování místní služba AD DS  |
|---------|---------|
|Počítače s Windows připojenými k Azure služba AD DS mají přístup ke sdíleným složkám Azure pomocí přihlašovacích údajů Azure AD přes SMB.     |Místní počítače s Windows připojené k služba AD DS nebo Azure připojené k služba AD DS Azure mají přístup ke sdíleným složkám Azure pomocí místních přihlašovacích údajů služby Active Directory, které jsou synchronizované s Azure AD přes SMB. Váš klient musí mít přehled o vašem služba AD DS.        |

### <a name="restrictions"></a>Omezení

- Ověřování Azure služba AD DS a místní služba AD DS nepodporují ověřování u účtů počítačů. Místo toho můžete použít účet přihlášení služby.
- U zařízení připojených k Azure AD nebo zařízení registrovaných v Azure AD se nepodporuje ověřování pomocí Azure služba AD DS ani místní ověřování služba AD DS.
- Sdílené složky Azure podporují ověřování na základě identity jenom v jedné z následujících služeb domény, a to buď [Azure Active Directory Domain Services (Azure služba AD DS)](#azure-ad-ds) , nebo [místní Active Directory Domain Services (služba AD DS)](#ad-ds).
- V systému souborů NFS (Network File System), který je ve verzi Preview, není podporována metoda ověřování na základě identity.

## <a name="advantages-of-identity-based-authentication"></a>Výhody ověřování na základě identity
Ověřování na základě identity pro soubory Azure nabízí oproti použití ověřování pomocí sdíleného klíče několik výhod:

-   **Rozšiřování klasického prostředí pro přístup ke sdíleným složkám na základě identity v cloudu s místními služba AD DS a Azure služba AD DS**  
    Pokud máte v úmyslu aplikaci zastoupit do cloudu a nahradit tradiční souborové servery službou Azure File Shares, můžete chtít, aby se aplikace ověřovala buď pomocí místních služba AD DS, nebo v Azure služba AD DS pověření pro přístup k datům souborů. Soubory Azure podporuje použití místních služba AD DS nebo přihlašovacích údajů Azure služba AD DS pro přístup ke sdíleným složkám Azure přes protokol SMB z místního prostředí služba AD DS nebo Azure služba AD DS virtuálních počítačů připojených k doméně.

-   **Vysazení podrobného řízení přístupu ke sdíleným složkám Azure**  
    Můžete udělit oprávnění ke konkrétní identitě na úrovni sdílených složek, adresářů nebo souborů. Předpokládejme například, že máte několik týmů s jednou sdílenou složkou Azure pro spolupráci s projekty. Všem týmům můžete udělit přístup k necitlivým adresářům a zároveň omezit přístup k adresářům obsahujícím citlivá finanční data jenom na váš finanční tým. 

-   **Zálohování seznamů řízení přístupu (ACL) systému Windows (označované také jako NTFS) společně s daty**  
    Pomocí sdílených složek Azure můžete zálohovat stávající místní sdílené složky. Soubory Azure při zálohování sdílené složky do sdílených složek Azure pomocí protokolu SMB zachovává vaše seznamy ACL spolu s Vašimi daty.

## <a name="how-it-works"></a>Jak to funguje

Sdílené složky Azure využívají protokol Kerberos k ověřování pomocí místních služba AD DS nebo Azure služba AD DS. Když se identita přidružená k uživateli nebo aplikaci spuštěné v klientovi pokusí o přístup k datům ve sdílených složkách Azure, pošle se požadavek do doménové služby, buď služba AD DS nebo Azure služba AD DS, a ověří identitu. Pokud je ověření úspěšné, vrátí token protokolu Kerberos. Klient pošle požadavek, který obsahuje token protokolu Kerberos a sdílené složky Azure, k autorizaci žádosti používá tento token. Sdílené složky Azure přijímají pouze tokeny protokolu Kerberos, nikoli přihlašovací údaje pro přístup.

Než budete moct povolit ověřování na sdílených složkách Azure na základě identity, musíte nejdřív nastavit prostředí domény.

### <a name="ad-ds"></a>AD DS

Pro místní ověřování služba AD DS musíte nastavit řadiče domény AD a připojit se k doméně nebo virtuální počítače. Řadiče domény můžete hostovat na virtuálních počítačích Azure nebo v místním prostředí. V obou případech musí mít klienti připojení k doméně pohled na doménovou službu, takže se musí nacházet v rámci podnikové sítě nebo virtuální sítě (VNET) vaší doménové služby.

Následující diagram znázorňuje místní služba AD DS ověřování sdílených složek Azure pomocí protokolu SMB. Služba AD DS Prem musí být synchronizované s Azure AD pomocí Azure AD Connect synchronizace. Pro přístup ke sdílené složce Azure můžete ověřovat a autorizovat jenom hybridní uživatelé, kteří existují v místních služba AD DS i v Azure AD. Důvodem je to, že oprávnění na úrovni sdílené složky jsou nakonfigurovaná proti identitě reprezentované ve službě Azure AD, kde se oprávnění na úrovni adresáře nebo souboru vynutilo v služba AD DS. Ujistěte se, že jste správně nakonfigurovali oprávnění proti stejnému hybridnímu uživateli.

:::image type="content" source="media/storage-files-active-directory-overview/Files-on-premises-AD-DS-Diagram.png" alt-text="Diagram, který znázorňuje místní ověřování služba AD DS pro sdílené složky Azure přes protokol SMB.":::

### <a name="azure-ad-ds"></a>Azure AD DS

Pro ověřování Azure služba AD DS byste měli povolit Azure AD Domain Services a připojení k doméně virtuálním počítačům, ze kterých plánujete přístup k datovým souborům. Váš virtuální počítač připojený k doméně se musí nacházet ve stejné virtuální síti (VNET) jako vaše služba AD DS Azure. 

Následující diagram představuje pracovní postup pro ověřování Azure služba AD DS ke sdíleným složkám souborů Azure přes protokol SMB. Postupuje podobně jako při ověřování Prem služba AD DS do sdílených složek Azure. Existují dva hlavní rozdíly:

- Nejdřív nemusíte vytvářet identitu ve službě Azure služba AD DS, která by představovala účet úložiště. To se provádí v procesu povolení na pozadí.

- Za druhé, všichni uživatelé, kteří existují v Azure AD, můžou být ověřeni a autorizováni. Uživatel může být pouze Cloud nebo hybridní. Synchronizace z Azure AD do Azure služba AD DS spravovaná platformou bez nutnosti konfigurace uživatele. Klient musí být ale připojený k doméně Azure služba AD DS, ale nemůže být připojený k Azure AD ani zaregistrován. 

:::image type="content" source="media/storage-files-active-directory-overview/Files-Azure-AD-DS-Diagram.png" alt-text="Diagram":::

### <a name="enable-identity-based-authentication"></a>Povolit ověřování na základě identity

Ověřování na základě identity můžete povolit buď pomocí Azure služba AD DS, nebo místního služba AD DS pro sdílené složky Azure na vašich nových a existujících účtech úložiště. Pro ověřování přístupu k souborům v účtu úložiště, která platí pro všechny sdílené složky v účtu, se dá použít jenom jedna Doménová služba. Podrobné pokyny k nastavení sdílených složek pro ověřování pomocí Azure služba AD DS v našem článku [povolení Azure Active Directory Domain Servicesho ověřování v Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md) a doprovodnéch materiálech k místnímu služba AD DS v našem dalším článku [povolíte místní Active Directory Domain Services ověřování pomocí protokolu SMB pro sdílené složky Azure](storage-files-identity-auth-active-directory-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Konfigurace oprávnění na úrovni sdílené složky pro soubory Azure

Jakmile povolíte Azure služba AD DS nebo místní služba AD DS ověřování, můžete použít předdefinované role Azure nebo nakonfigurovat vlastní role pro identity Azure AD a přiřazovat přístupová práva ke všem sdíleným složkám v účtech úložiště. Přiřazené oprávnění umožňuje udělené identitě získat přístup pouze ke sdílené složce, nic jiného, ani kořenovému adresáři. Ke sdíleným složkám Azure se pořád potřebuje samostatně nakonfigurovat oprávnění pro adresáře nebo soubory.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Konfigurace oprávnění adresářů nebo souborů pro soubory Azure

Sdílené složky Azure vynutila standardní oprávnění souborů Windows v adresáři i na úrovni souborů, včetně kořenového adresáře. Konfigurace oprávnění na úrovni adresáře nebo souborů se podporuje přes protokol SMB i REST. Připojte cílovou sdílenou složku z virtuálního počítače a nakonfigurujte oprávnění pomocí Průzkumníka souborů Windows, Windows [Icacls](/windows-server/administration/windows-commands/icacls)nebo příkazu [set-ACL](/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6) .

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Použijte klíč účtu úložiště pro oprávnění naduživatelem.

Uživatel s klíčem účtu úložiště má přístup ke sdíleným složkám Azure pomocí uživatelských oprávnění. Oprávnění naduživatelem obcházejí všechna omezení řízení přístupu.

> [!IMPORTANT]
> Doporučeným postupem zabezpečení je vyhnout se sdílení klíčů účtu úložiště a kdykoli je to možné, využití ověřování na základě identity.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Zachování seznamů ACL adresářů a souborů při importu dat do sdílených složek Azure

Azure Files podporuje zachovávání seznamů ACL na úrovni adresáře nebo souborů při kopírování dat do sdílených složek Azure. Seznamy řízení přístupu můžete zkopírovat do složky nebo souboru do sdílených složek Azure pomocí Azure File Sync nebo běžných nástrojů pro přesun souborů. Pomocí příkazu [Robocopy](/windows-server/administration/windows-commands/robocopy) s `/copy:s` příznakem můžete například kopírovat data i seznamy ACL do sdílené složky Azure. Seznamy řízení přístupu (ACL) jsou ve výchozím nastavení zachované, nemusíte u svého účtu úložiště povolit ověřování na základě identity, abyste zachovali seznamy ACL.

## <a name="pricing"></a>Ceny
Pro povolení ověřování na základě identity přes protokol SMB v účtu úložiště není k dispozici žádný další poplatek za službu. Další informace o cenách najdete v tématu ceny za [Azure Files](https://azure.microsoft.com/pricing/details/storage/files/) a [ceny Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Další kroky
Další informace o souborech Azure a ověřování na základě identity přes SMB najdete v těchto zdrojích informací:

- [Plánování nasazení Azure Files](storage-files-planning.md)
- [Povolit místní Active Directory Domain Services ověřování pomocí protokolu SMB pro sdílené složky Azure](storage-files-identity-auth-active-directory-enable.md)
- [Povolení ověřování Azure Active Directory Domain Services u souborů Azure](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [Nejčastější dotazy](storage-files-faq.md)