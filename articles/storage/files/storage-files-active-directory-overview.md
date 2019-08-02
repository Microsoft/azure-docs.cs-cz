---
title: Přehled ověřování Azure Active Directory přes protokol SMB pro soubory Azure (Preview) – Azure Storage
description: Služba soubory Azure podporuje ověřování na základě identity přes protokol SMB (Server Message Block) (Preview) prostřednictvím služby Domain Services Azure Active Directory (Azure AD). Virtuální počítače s Windows připojené k doméně pak můžou přistupovat ke sdíleným složkám Azure pomocí přihlašovacích údajů Azure AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: rogarana
ms.openlocfilehash: b1bc7385751fbd1829b4aee2713621448f8aa505
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699723"
---
# <a name="overview-of-azure-files-azure-active-directory-domain-service-aad-ds-authentication-support-for-smb-access-preview"></a>Přehled podpory ověřování služby Azure Files Azure Active Directory Domain Service (AAD DS) pro přístup přes protokol SMB (Preview)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Další informace o povolení ověřování AAD DS pro soubory Azure najdete v tématu [Povolení ověřování služby Azure Active Directory Domain Services přes SMB pro soubory Azure (Preview)](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Glosář 
Je užitečné pochopit některé klíčové podmínky týkající se ověřování služby Azure AD Domain Service prostřednictvím protokolu SMB pro soubory Azure:

-   **Azure Active Directory (Azure AD)**  
    Azure Active Directory (Azure AD) je víceklientské cloudové služby Microsoftu a služba pro správu identit založené na víceklientské architektuře. Azure AD kombinuje základní adresářové služby, správu přístupu k aplikacím a ochranu identit do jediného řešení. Další informace najdete v tématu [co je Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services**  
    Azure AD Domain Services poskytuje spravované doménové služby, jako je připojení k doméně, zásady skupiny, LDAP a ověřování pomocí protokolu Kerberos/NTLM. Tyto služby jsou plně kompatibilní se službou Windows Server Active Directory. Další informace najdete v tématu [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md).

-   **Access Control na základě rolí Azure (RBAC)**  
    Řízení přístupu na základě role v Azure umožňuje přesnou správu přístupu. Pomocí RBAC můžete spravovat přístup k prostředkům tím, že uživatelům udělíte nejnižší oprávnění potřebná k provádění svých úloh. Další informace o RBAC najdete v tématu [co je řízení přístupu na základě role (RBAC) v Azure?](../../role-based-access-control/overview.md)

-   **Ověřování protokolu Kerberos**

    Kerberos je ověřovací protokol, který se používá k ověření identity uživatele nebo hostitele. Další informace o protokolu Kerberos najdete v tématu [Přehled ověřování protokolu Kerberos](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protokol SMB (Server Message Block)**  
    SMB je standardní protokol pro sdílení souborů v síti. SMB se označuje také jako Common Internet File System nebo CIFS. Další informace o protokolu SMB najdete v tématu [Přehled protokolu SMB a protokolu CIFS](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## <a name="advantages-of-azure-ad-domain-service-authentication"></a>Výhody ověřování Azure AD Domain Service
Ověřování služby Azure AD Domain Services pro soubory Azure nabízí několik výhod oproti použití ověřování pomocí sdíleného klíče:

-   **Rozšiřování klasického prostředí pro přístup ke sdíleným složkám na základě identity v cloudu pomocí Azure AD a služby Azure AD Domain Service**  
    Pokud plánujete, že se vaše aplikace navede do cloudu, nahradíme tradiční souborové servery pomocí služby soubory Azure a pak budete chtít, aby se vaše aplikace ověřovala s přihlašovacími údaji Azure AD pro přístup k datům souborů. Služba soubory Azure podporuje použití přihlašovacích údajů Azure AD pro přístup k souborům Azure přes SMB z virtuálních počítačů s Windows připojenými k doméně AAD. Můžete také zvolit synchronizaci všech místních objektů služby Active Directory s Azure AD, abyste zachovali uživatelská jména, hesla a další přiřazení skupin.

-   **Vysazení podrobného řízení přístupu ke sdíleným složkám Azure**  
    Můžete udělit oprávnění ke konkrétní identitě na úrovni sdílených složek, adresářů nebo souborů. Předpokládejme například, že máte několik týmů s jednou sdílenou složkou Azure pro spolupráci s projekty. Všem týmům můžete udělit přístup k necitlivým adresářům a zároveň omezit přístup k adresářům obsahujícím citlivá finanční data jenom na váš finanční tým. 

-   **Zálohování seznamů ACL spolu s Vašimi daty**  
    Pomocí služby soubory Azure můžete zálohovat stávající místní sdílené složky. Soubory Azure při zálohování sdílené složky do souborů Azure pomocí protokolu SMB zachovají vaše seznamy ACL spolu s Vašimi daty.

## <a name="how-it-works"></a>Jak to funguje
Služba soubory Azure používá Azure AD Domain Services k podpoře ověřování pomocí protokolu Kerberos s přihlašovacími údaji služby Azure AD z virtuálních počítačů připojených k doméně. Než budete moct Azure AD používat se soubory Azure, musíte nejdřív povolit Azure AD Domain Services a připojit se k doméně z virtuálních počítačů, ze kterých plánujete přístup k datům souborů. Váš virtuální počítač připojený k doméně se musí nacházet ve stejné virtuální síti (VNET) jako Azure AD Domain Services. 

Když se identita přidružená k aplikaci běžící na virtuálním počítači pokusí o přístup k datům v souborech Azure, pošle se požadavek do Azure AD Domain Services k ověření identity. Pokud je ověření úspěšné, Azure AD Domain Services vrátí token protokolu Kerberos. Aplikace odešle požadavek, který obsahuje token protokolu Kerberos, a soubory Azure tento token schválí k autorizaci žádosti. Soubory Azure obdrží jenom token a nezachovávají přihlašovací údaje Azure AD.

![Snímek obrazovky znázorňující diagram ověřování Azure AD přes protokol SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-domain-service-authentication-for-smb-access"></a>Povolení ověřování Azure AD Domain Service pro přístup přes protokol SMB
Můžete povolit ověřování služby Azure AD Domain Services pro soubory Azure na vašich nových a existujících účtech úložiště vytvořených po 24. září 2018. 

Než povolíte tuto funkci, ověřte, jestli je nasazený Azure AD Domain Services pro primárního tenanta Azure AD, ke kterému je přidružený váš účet úložiště. Pokud jste ještě nenastavili Azure AD Domain Services, postupujte podle podrobných pokynů uvedených v části [povolení Azure Active Directory Domain Services pomocí Azure Portal](../../active-directory-domain-services/create-instance.md).

Nasazení Azure AD Domain Services obvykle trvá 10 až 15 minut. Po nasazení Azure AD Domain Services můžete povolit ověřování Azure AD přes SMB pro soubory Azure. Další informace najdete v tématu [Povolení ověřování služby Azure Active Directory Domain Services přes protokol SMB pro soubory Azure (Preview)](storage-files-active-directory-enable.md). 

### <a name="configure-share-level-permissions-for-azure-files"></a>Konfigurace oprávnění na úrovni sdílené složky pro soubory Azure
Po povolení ověřování služby Azure AD Domain Service můžete nakonfigurovat vlastní role RBAC pro identity Azure AD a přiřazovat přístupová práva ke všem sdíleným složkám v účtu úložiště.

Když se aplikace spuštěná na virtuálním počítači připojeném k doméně pokusí připojit sdílenou složku Azure nebo získat přístup k adresáři nebo souboru, ověřují se přihlašovací údaje služby Azure AD aplikace, aby se zajistilo správné oprávnění na úrovni sdílené složky a oprávnění NTFS. Informace o konfiguraci oprávnění na úrovni sdílené složky najdete v tématu [Povolení ověřování služby Azure Active Directory Domain Services přes protokol SMB (Preview)](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Konfigurace oprávnění na úrovni adresáře nebo souborů pro soubory Azure 
Soubory Azure vynutily standardní oprávnění souborů NTFS na úrovni adresářů a souborů, včetně kořenového adresáře. Konfigurace oprávnění na úrovni adresáře nebo souborů se podporuje jenom přes protokol SMB. Připojte cílovou sdílenou složku z virtuálního počítače a nakonfigurujte oprávnění pomocí příkazu Windows [Icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) nebo [set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) . 

> [!NOTE]
> Konfigurace oprávnění NTFS prostřednictvím Průzkumníka souborů Windows není ve verzi Preview podporovaná.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Použijte klíč účtu úložiště pro oprávnění naduživatelem. 
Uživatel, který má klíč účtu úložiště, má přístup k souborům Azure s oprávněními uživatele. Oprávnění naduživatelem překročí všechna omezení řízení přístupu nakonfigurovaná na úrovni sdílené složky pomocí RBAC a vynutila Azure AD. Aby bylo možné připojit sdílenou složku Azure, musí být k oprávnění naduživatelem. 

> [!IMPORTANT]
> V rámci osvědčených postupů pro zabezpečení nepoužívejte sdílení klíčů účtu úložiště a využijte oprávnění služby Azure AD, kdykoli je to možné.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Zachování seznamů ACL adresářů a souborů pro import dat do sdílených složek Azure
Soubory Azure teď při kopírování dat do sdílených složek Azure podporují zachovávání seznamů ACL adresáře nebo souborů. Do souborů Azure můžete zkopírovat seznamy řízení přístupu (ACL) do adresáře nebo souboru. Pomocí příkazu [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) s příznakem `/copy:s` můžete například zkopírovat data a seznamy ACL do sdílené složky Azure. Uchovávání seznamů ACL je ve výchozím nastavení zapnuté a není nutné explicitně povolit funkci ověřování služby Azure AD Domain Service v účtu úložiště. 

## <a name="pricing"></a>Ceny
Pro povolení ověřování Azure AD přes protokol SMB v účtu úložiště není k dispozici žádný další poplatek za službu. Další informace o cenách najdete v tématu ceny za [Azure Files](https://azure.microsoft.com/pricing/details/storage/files/) a [Azure AD Domain Services cenové](https://azure.microsoft.com/pricing/details/active-directory-ds/) stránky.

## <a name="next-steps"></a>Další kroky
Další informace o souborech Azure a ověřování Azure AD přes protokol SMB najdete v těchto zdrojích informací:

- [Seznámení se soubory Azure](storage-files-introduction.md)
- [Povolení Azure Active Directory ověřování pomocí protokolu SMB pro soubory Azure (Preview)](storage-files-active-directory-enable.md)
- [Nejčastější dotazy](storage-files-faq.md)
