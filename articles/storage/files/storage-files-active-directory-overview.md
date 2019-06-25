---
title: Přehled ověřování pomocí Azure Active Directory přes protokol SMB pro soubory Azure (preview) – služby Azure Storage
description: Služba soubory Azure podporuje ověřování na základě identity přes protokol SMB (Server Message Block) (preview) prostřednictvím služby Azure Active Directory (Azure AD) Domain Services. Připojené k doméně Windows virtuálních počítačů (VM) pak můžou sdílenými složkami Azure pomocí přihlašovacích údajů Azure AD.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/18/2019
ms.author: rogarana
ms.openlocfilehash: 21087424be1a7a3edfe2dddcbec830bd74559b23
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269362"
---
# <a name="overview-of-azure-files-azure-active-directory-domain-service-aad-ds-authentication-support-for-smb-access-preview"></a>Přehled podpory ověřování soubory Azure Azure Active Directory Domain Service (AAD DS) pro přístup k protokolu SMB (Preview)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Zjistěte, jak povolit ověřování pomocí AAD DS pro soubory Azure, najdete v článku [povolit ověřování Azure Active Directory Domain Service přes protokol SMB pro soubory Azure (Preview)](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Glosář 
Je dobré znát několik klíčových pojmů týkajících se ověřování pomocí služby Azure AD Domain Service přes protokol SMB pro soubory Azure:

-   **Azure Active Directory (Azure AD)**  
    Azure Active Directory (Azure AD) je od Microsoftu založené na cloudu adresáři a identitami správy službou s více tenanty. Azure AD kombinuje základní adresářové služby, správu přístupu k aplikacím a ochranu identity v rámci jednoho řešení. Další informace najdete v tématu [co je Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services**  
    Azure AD Domain Services poskytuje služby spravované domény, jako je například připojení k doméně, zásady skupiny, LDAP a ověřování protokolu Kerberos nebo NTLM ověřování. Tyto služby jsou plně kompatibilní s Windows Server Active Directory. Další informace najdete v tématu [domény služby Azure Active Directory (AD)](../../active-directory-domain-services/overview.md).

-   **Řízení přístupu (RBAC) na základě Azure Role**  
    Řízení přístupu na základě role v Azure umožňuje přesnou správu přístupu. Pomocí RBAC, můžete spravovat přístup k prostředkům tak, že udělíte uživatelům co nejnižší oprávnění potřebná k provádění svých úloh. Další informace o RBAC najdete v tématu [co je řízení přístupu na základě rolí (RBAC) v Azure?](../../role-based-access-control/overview.md)

-   **Ověřování pomocí protokolu Kerberos**

    Pomocí protokolu Kerberos je ověřovací protokol, který se používá k ověření identity uživatele nebo hostitele. Další informace o protokolu Kerberos najdete v tématu [Přehled ověřování protokolem Kerberos](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Server protokolu Message Block (SMB)**  
    Protokol SMB je standardní síťového sdílení souborů protokolu. Protokol SMB je označované také jako Common Internet File System nebo CIFS. Další informace o protokolu SMB najdete v tématu [Microsoft protokolu SMB a přehled protokolu CIFS](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## <a name="advantages-of-azure-ad-domain-service-authentication"></a>Výhody ověřování pomocí služby Azure AD Domain Service
Ověřování Azure AD Domain Services pro Azure Files nabízí několik výhod oproti použití ověřování pomocí sdíleného klíče:

-   **Rozšiřují prostředí tradiční založené na identitě soubor sdílené složce přístup do cloudu s Azure AD a Azure AD Domain Services**  
    Pokud máte v úmyslu metodou "lift and shift" přihlašovací údaje pro přístup k datům souboru aplikace do cloudu, která nahrazuje tradiční souborové servery se soubory Azure, můžete aplikaci k ověřování ve službě Azure AD. Azure podporuje soubory pomocí přihlašovacích údajů Azure AD pro přístup k službě soubory Azure přes protokol SMB ze služby AAD DS připojených k doméně virtuální počítače s Windows. Můžete také synchronizace všech objektů v místním Active Directory do služby Azure AD, chcete-li zachovat uživatelská jména, hesla a další přiřazení skupiny.

-   **Vynutit detailní řízení přístupu ke sdílené složky Azure**  
    Můžete udělit oprávnění na konkrétní identitu na sdílenou složku, adresáře nebo úrovni souboru. Předpokládejme například, že máte několik týmů pro projekt spolupráce pomocí jedné sdílené složky Azure. Všechny týmy přístup k adresářům, kteří nejsou citliví, můžete udělit zároveň omezit přístup k adresářům obsahující citlivá finanční data pouze finanční tým. 

-   **Zálohování seznamy ACL spolu s daty**  
    Soubory Azure můžete použít k zálohování vaší stávající místní sdílené složky. Služba soubory Azure chrání vaše seznamy ACL spolu s daty při zálohování souboru sdílení do služby soubory Azure přes protokol SMB.

## <a name="how-it-works"></a>Jak to funguje
Služba soubory Azure používá Azure AD Domain Services pro podporu ověřování protokolu Kerberos pomocí přihlašovacích údajů Azure AD z virtuálních počítačů připojených k doméně. Předtím, než budete moct použít Azure AD se soubory Azure, musíte povolit Azure AD Domain Services a připojte se k doméně z virtuálních počítačů, ze kterých plánujete k souborovým datům. Váš virtuální počítač připojený k doméně se musí nacházet ve stejné virtuální síti (VNET) jako Azure AD Domain Services. 

Když identity přidružené k aplikaci spuštěné na virtuálním počítači pokusí o přístup k datům ve službě soubory Azure, je žádost odeslána k ověření identity do služby Azure AD Domain Services. Pokud je ověření úspěšné, Azure AD Domain Services vrátí token protokolu Kerberos. Aplikace odešle požadavek, který obsahuje token protokolu Kerberos a souborů Azure používá tento token k ověření požadavku. Služba soubory Azure přijme token pouze a není zachována přihlašovacích údajů Azure AD.

![Snímek obrazovky znázorňující diagram ověřování Azure AD prostřednictvím protokolu SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-domain-service-authentication-for-smb-access"></a>Povolit ověřování Azure AD Domain Services pro přístup k protokolu SMB
Můžete povolit ověřování pomocí služby Azure AD Domain Service pro soubory Azure pro vaše účty úložiště. nové i stávající vytvořené po 24. září 2018. 

Před povolením této funkce, ověřte, že byla nasazena služba Azure AD Domain Services pro primární tenant Azure AD, ke kterému je přidružena účtu úložiště. Pokud jste ještě Azure AD Domain Services, postupujte podle podrobných pokynů v [povolit Azure Active Directory Domain Services pomocí webu Azure portal](../../active-directory-domain-services/create-instance.md).

Nasazení služby Azure AD Domain Services obvykle trvá 10 až 15 minut. Po nasazení služby Azure AD Domain Services, můžete povolit ověřování Azure AD prostřednictvím protokolu SMB pro soubory Azure. Další informace najdete v tématu [povolit Azure Active Directory Domain Services ověřování prostřednictvím protokolu SMB pro soubory Azure (Preview)](storage-files-active-directory-enable.md). 

### <a name="configure-share-level-permissions-for-azure-files"></a>Konfigurovat oprávnění na úrovni sdílené složky pro soubory Azure
Jakmile bylo povoleno ověřování pomocí služby Azure AD Domain Service, můžete nakonfigurovat vlastní role RBAC pro Azure AD identity a přiřadit přístupová práva k žádné sdílené složky v účtu úložiště.

Když se aplikace běžící na virtuálním počítači připojeném k doméně se pokusí o připojení sdílené složky Azure nebo přístup k adresáři nebo souboru, přihlašovacích údajů Azure AD. aplikace ověřeni zajistit správné oprávnění na úrovni sdílené složky a oprávnění systému souborů NTFS. Informace o konfiguraci oprávnění na úrovni sdílené složky najdete v tématu [povolit Azure Active Directory Domain Services ověřování přes protokol SMB (Preview)](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Konfigurovat oprávnění na úrovni adresáře nebo souboru pro soubory Azure 
Služba soubory Azure vynucuje standardní oprávnění systému souborů NTFS na úrovni adresáře a souboru, včetně v kořenovém adresáři. Konfigurace oprávnění na úrovni adresář nebo soubor je podporována pouze prostřednictvím protokolu SMB. Připojit cílové sdílené složce z vašeho virtuálního počítače a konfigurovat oprávnění Windows s použitím [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) nebo [nastavení seznamu ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) příkazu. 

> [!NOTE]
> Konfigurace systému souborů NTFS oprávnění pomocí Průzkumníka souborů Windows se nepodporuje ve verzi preview.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Použijte klíč účtu úložiště pro oprávnění superuživatele 
Uživatel má klíč účtu úložiště můžete přístup ke službě soubory Azure s oprávněními superuživatele. Toto oprávnění superuživatele všechna omezení řízení přístupu nakonfigurována na úrovni sdílené složky pomocí RBAC a vynucuje Azure AD. Superuživatel oprávnění jsou vyžadována pro připojení sdílené složky Azure. 

> [!IMPORTANT]
> V rámci osvědčených postupů pro zabezpečení vyhnout sdíleli své klíče účtu úložiště a využívat oprávnění Azure AD, kdykoli je to možné.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Zachovat adresáře a souboru seznamy ACL pro import dat do sdílené složky Azure
Služba soubory Azure teď podporuje zachování seznamy ACL pro adresář nebo soubor při kopírování dat do sdílené složky Azure. Seznamy ACL na adresář nebo soubor můžete zkopírovat do služby soubory Azure. Například můžete použít [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) s příznakem `/copy:s` ke kopírování dat a seznamů ACL do sdílené složky Azure. Seznam ACL a zachovávání s rozlišením je ve výchozím a není potřeba explicitně povolit funkci ověřování Azure AD Domain Services na vašem účtu úložiště. 

## <a name="pricing"></a>Ceny
Neplatí žádné další poplatky službu povolit ověřování Azure AD prostřednictvím protokolu SMB na vašem účtu úložiště. Další informace o cenách najdete v tématu [cenám služby soubory Azure](https://azure.microsoft.com/pricing/details/storage/files/) a [ceny služby Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/) stránky.

## <a name="next-steps"></a>Další kroky
Další informace o službě soubory Azure a Azure AD authentication přes protokol SMB naleznete v následujících zdrojích:

- [Úvod do služby soubory Azure](storage-files-introduction.md)
- [Povolit ověřování Azure Active Directory přes protokol SMB pro soubory Azure (Preview)](storage-files-active-directory-enable.md)
- [Nejčastější dotazy](storage-files-faq.md)
