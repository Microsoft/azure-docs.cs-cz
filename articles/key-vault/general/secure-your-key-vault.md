---
title: Zabezpečení přístupu k trezoru klíčů
description: Model přístupu pro Azure Key Vault, včetně ověřování služby Active Directory a koncových bodů prostředků.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sudbalas
ms.openlocfilehash: 94034edfa1a5c6ffccd022b4cbf7bae42cc0bae3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212463"
---
# <a name="secure-access-to-a-key-vault"></a>Zabezpečení přístupu k trezoru klíčů

Azure Key Vault je cloudová služba, která chrání šifrovací klíče a tajné kódy, jako jsou certifikáty, připojovací řetězce a hesla. Vzhledem k tomu, že tato data jsou citlivá a důležitá pro podnikání, je nutné zabezpečený přístup k vašim trezorům klíčů povolit pouze autorizovaným aplikacím a uživatelům. Tento článek poskytuje přehled modelu přístupu Key Vault. Vysvětluje ověřování a autorizaci a popisuje, jak zabezpečit přístup k vašim trezorům klíčů.

Další informace o Key Vault najdete v tématu [o Azure Key Vault](overview.md). Další informace o tom, co je možné uložit v trezoru klíčů, najdete v tématu [informace o klíčích, tajných klíčích a certifikátech](about-keys-secrets-certificates.md).

## <a name="access-model-overview"></a>Přehled modelu přístupu

Přístup k trezoru klíčů se ovládá prostřednictvím dvou rozhraní: **rovina správy** a **rovina dat**. Rovina správy je místo, kde spravujete Key Vault sebe sama. Mezi operace v této rovině patří vytváření a odstraňování trezorů klíčů, načítání vlastností Key Vault a aktualizace zásad přístupu. Rovina dat je místo, kde pracujete s daty uloženými v trezoru klíčů. Můžete přidávat, odstraňovat a upravovat klíče, tajné klíče a certifikáty.

Obě roviny používají pro ověřování [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) . Pro autorizaci rovina správy používá [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) a rovina dat používá [zásady přístupu Key Vault](./assign-access-policy-portal.md) a [Azure RBAC pro Key Vault operace roviny dat](./rbac-guide.md).

Pro přístup k trezoru klíčů v kterékoli rovině musí mít všichni volající (uživatelé nebo aplikace) správné ověřování a autorizaci. Ověřování vytváří identitu volajícího. Autorizace určuje, které operace může volající spustit. Ověřování pomocí Key Vault funguje ve spojení s [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md), která zodpovídá za ověřování identity libovolného **objektu zabezpečení**.

Objekt zabezpečení je objekt, který představuje uživatele, skupinu, službu nebo aplikaci požadující přístup k prostředkům Azure. Azure přiřadí jedinečné **ID objektu** každému objektu zabezpečení.

* Objekt zabezpečení **uživatele** identifikuje jednotlivce, který má profil v Azure Active Directory.

* Objekt zabezpečení **skupiny** identifikuje sadu uživatelů vytvořených v Azure Active Directory. Všechny role nebo oprávnění přiřazených ke skupině jsou udělena všem uživatelům v rámci dané skupiny.

* **Instanční objekt** je typ objektu zabezpečení, který identifikuje aplikaci nebo službu, což znamená, že je místo uživatele nebo skupiny slovní část kódu. ID objektu instančního objektu se označuje jako jeho **ID klienta** a funguje jako jeho uživatelské jméno. **Tajný klíč klienta** instančního objektu nebo **certifikát** funguje jako heslo. Řada služeb Azure podporuje přiřazování [spravované identity](../../active-directory/managed-identities-azure-resources/overview.md) pomocí automatizované správy **ID klienta** a **certifikátu**. Spravovaná identita představuje nejbezpečnější a doporučenou možnost ověřování v rámci Azure.

Další informace o ověřování pro Key Vault najdete v tématu [ověření pro Azure Key Vault](authentication.md)

## <a name="key-vault-authentication-options"></a>Možnosti ověřování Key Vault

Když vytvoříte Trezor klíčů v rámci předplatného Azure, automaticky se přiřadí k tenantovi Azure AD daného předplatného. Všichni volající v obou rovinách se musí zaregistrovat v tomto tenantovi a ověřit pro přístup k trezoru klíčů. V obou případech můžou aplikace získat přístup k Key Vault třemi způsoby:

- **Pouze aplikace**: aplikace představuje instanční objekt nebo spravovanou identitu. Tato identita je nejběžnější scénář pro aplikace, které pravidelně potřebují přistupovat k certifikátům, klíčům nebo tajným klíčům z trezoru klíčů. Aby tento scénář fungoval, `objectId` musí být aplikace zadaná v zásadách přístupu a `applicationId` nesmí být zadána nebo musí být zadaná  `null` .
- **Pouze uživatel**: uživatel přistupuje k trezoru klíčů z jakékoli aplikace zaregistrované v tenantovi. Příklady tohoto typu přístupu zahrnují Azure PowerShell a Azure Portal. Aby tento scénář fungoval, `objectId` musí být uživatel uveden v zásadách přístupu a `applicationId` nesmí být zadán nebo musí být zadán  `null` .
- **Aplikace-plus – uživatel** (někdy označovaný jako _složená identita_): uživatel je vyžadován pro přístup k trezoru klíčů z konkrétní aplikace _a_ aplikace musí k zosobnění uživatele používat tok spouštěný jménem ověřování (OBO). Aby tento scénář fungoval, `applicationId` `objectId` musí být v zásadách přístupu zadány obě i. `applicationId`Identifikuje požadovanou aplikaci a `objectId` identifikuje uživatele. V současné době tato možnost není k dispozici pro rovinu dat Azure RBAC (Preview).

Ve všech typech přístupu se aplikace ověřuje pomocí Azure AD. Aplikace používá jakoukoli [podporovanou metodu ověřování](../../active-directory/develop/authentication-vs-authorization.md) založenou na typu aplikace. Aplikace získá token pro prostředek v rovině pro udělení přístupu. Prostředek je koncový bod v rovině pro správu nebo data na základě prostředí Azure. Aplikace použije token a pošle REST API žádost o Key Vault. Pokud se chcete dozvědět víc, Projděte si [celý tok ověřování](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Model jednoho mechanismu ověřování do obou rovin má několik výhod:

- Organizace můžou centrálně řídit přístup ke všem trezorům klíčů ve své organizaci.
- Pokud uživatel opustí, okamžitě ztratí přístup ke všem trezorům klíčů v organizaci.
- Organizace můžou přizpůsobit ověřování pomocí možností ve službě Azure AD, jako je například povolení služby Multi-Factor Authentication pro zvýšení zabezpečení.

## <a name="resource-endpoints"></a>Koncové body prostředků

Aplikace přistupují k rovinám prostřednictvím koncových bodů. Ovládací prvky přístupu pro tyto dvě roviny pracují nezávisle. Abyste aplikaci udělili přístup k používání klíčů v trezoru klíčů, udělíte přístup k rovině dat pomocí zásad přístupu Key Vault nebo Azure RBAC (Preview). Pokud chcete uživateli udělit oprávnění ke čtení Key Vault vlastností a značek, ale ne přístup k datům (klíčům, tajným klíčům nebo certifikátům), udělíte přístup k rovině správy pomocí Azure RBAC.

V následující tabulce jsou uvedeny koncové body pro řídicí a datové roviny.

| &nbsp;Rovina přístupu | Koncové body přístupu | Operace | &nbsp;Mechanismus řízení přístupu |
| --- | --- | --- | --- |
| Rovina správy | **Globální**<br> management.azure.com:443<br><br> **Azure Čína 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Státní správa USA Azure:**<br> management.usgovcloudapi.net:443<br><br> **Azure Německo:**<br> management.microsoftazure.de:443 | Vytváření, čtení, aktualizace a odstraňování trezorů klíčů<br><br>Nastavení zásad přístupu Key Vault<br><br>Nastavení značek Key Vault | Azure RBAC |
| Rovina dat | **Globální**<br> &lt;název_trezoru&gt;.vault.azure.net:443<br><br> **Azure Čína 21Vianet:**<br> &lt;název_trezoru&gt;.vault.azure.cn:443<br><br> **Státní správa USA Azure:**<br> &lt;název_trezoru&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Německo:**<br> &lt;název_trezoru&gt;.vault.microsoftazure.de:443 | Klíče: šifrování, dešifrování, wrapKey, unwrapKey, podpis, ověření, získání, vypsání, vytvoření, aktualizace, import, odstranění, obnovení, zálohování, obnovení, vyprázdnění<br><br> Certifikáty: managecontacts, getissuer, listissuers, setissuers, deleteissuers, manageissuers, získat, seznam, vytvořit, importovat, aktualizovat, odstranit, obnovit, zálohovat, obnovit, vymazat<br><br>  Tajné kódy: získání, seznam, nastavení, odstranění, obnovení, zálohování, obnovení, vyprázdnění | Zásada přístupu Key Vault nebo Azure RBAC (Preview)|

## <a name="management-plane-and-azure-rbac"></a>Rovina správy a Azure RBAC

Na rovině správy můžete pomocí [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) autorizovat operace, které volající může spustit. V modelu Azure RBAC má každé předplatné Azure instanci Azure AD. Přístup k uživatelům, skupinám a aplikacím udělíte z tohoto adresáře. Přístup se uděluje pro správu prostředků v předplatném Azure, které používají model nasazení Azure Resource Manager.

V rámci skupiny prostředků můžete vytvořit Trezor klíčů a spravovat přístup pomocí Azure AD. Uživatelům nebo skupinám udělíte možnost spravovat trezory klíčů ve skupině prostředků. Přístup na konkrétní úroveň oboru udělíte tak, že jim přiřadíte příslušné role Azure. Chcete-li uživateli udělit přístup ke správě trezorů klíčů, přiřaďte uživatele předdefinované [Key Vault role přispěvatele](../../role-based-access-control/built-in-roles.md#key-vault-contributor) v určitém oboru. K roli Azure se dají přiřadit tyto úrovně oborů:

- **Předplatné**: role Azure přiřazená na úrovni předplatného se vztahuje na všechny skupiny prostředků a prostředky v rámci daného předplatného.
- **Skupina prostředků**: role Azure přiřazená na úrovni skupiny prostředků se vztahuje na všechny prostředky v této skupině prostředků.
- **Konkrétní prostředek**: na tento prostředek se vztahuje role Azure přiřazená pro konkrétní prostředek. V tomto případě je prostředkem konkrétní Trezor klíčů.

Existuje několik předdefinovaných rolí. Pokud předdefinovaná role nevyhovuje vašim potřebám, můžete definovat vlastní roli. Další informace najdete v tématu [Předdefinované role v Azure](../../role-based-access-control/built-in-roles.md). 

Musíte mít `Microsoft.Authorization/roleAssignments/write` `Microsoft.Authorization/roleAssignments/delete` oprávnění a, jako je například správce nebo [vlastník](../../role-based-access-control/built-in-roles.md#owner) [přístupu uživatele](../../role-based-access-control/built-in-roles.md#user-access-administrator) .

> [!IMPORTANT]
> Pokud má uživatel `Contributor` oprávnění k rovině správy trezoru klíčů, uživatel může udělit přístup k rovině dat nastavením zásad přístupu Key Vault. Měli byste přesně řídit, kdo má `Contributor` roli přístup k vašim trezorům klíčů. Ujistěte se, že k vašim trezorům klíčů, klíčům, tajným klíčům a certifikátům mají přístup jenom autorizovaní uživatelé.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Rovina dat a zásady přístupu

Nastavením zásad přístupu Key Vault pro Trezor klíčů můžete udělit přístup k rovině dat. Aby bylo možné nastavit tyto zásady přístupu, musí mít uživatel, skupina nebo aplikace `Key Vault Contributor` oprávnění pro rovinu správy pro daný Trezor klíčů.

Uživateli, skupině nebo aplikaci udělíte přístup k provádění konkrétních operací pro klíče nebo tajné klíče v trezoru klíčů. Key Vault podporuje až 1 024 záznamů zásad přístupu pro Trezor klíčů. Pokud chcete udělit přístup k rovině dat několika uživatelům, vytvořte skupinu zabezpečení Azure AD a přidejte do této skupiny uživatele.

Úplný seznam operací trezoru a tajné operace můžete zobrazit tady: [Key Vault odkaz na operaci](/rest/api/keyvault/#vault-operations)

<a id="key-vault-access-policies"></a> Zásady přístupu Key Vault udělují oprávnění nezávisle na klíčích, tajných klíčích a certifikátech.  Přístupová oprávnění pro klíče, tajné klíče a certifikáty jsou na úrovni trezoru. 

Další informace o použití zásad přístupu trezoru klíčů najdete v tématu [přiřazení zásad Key Vault přístupu](assign-access-policy-portal.md) .

> [!IMPORTANT]
> Zásady přístupu Key Vault platí na úrovni trezoru. Když je uživateli udělené oprávnění k vytváření a odstraňování klíčů, můžou tyto operace provádět u všech klíčů v tomto trezoru klíčů.
Zásady přístupu Key Vault nepodporují podrobné oprávnění na úrovni objektu, jako je konkrétní klíč, tajný klíč nebo certifikát. 
>

## <a name="data-plane-and-azure-rbac-preview"></a>Rovina dat a Azure RBAC (Preview)

Řízení přístupu na základě role v Azure je alternativním modelem oprávnění pro řízení přístupu k Azure Key Vault rovině dat, která se dají povolit pro jednotlivé trezory klíčů. Model oprávnění Azure RBAC je exkluzivní a nastavený na nastavení, zásady přístupu k trezoru se staly neaktivními. Azure Key Vault definuje sadu předdefinovaných rolí Azure, které zahrnují společné sady oprávnění používané pro přístup k klíčům, tajným klíčům nebo certifikátům.

Když je role Azure přiřazená k objektu zabezpečení Azure AD, poskytuje Azure přístup k těmto prostředkům pro daný objekt zabezpečení. Přístup může být vymezen na úrovni předplatného, skupiny prostředků, trezoru klíčů nebo jednotlivého klíče, tajného klíče nebo certifikátu. Objekt zabezpečení Azure AD může být uživatelem, skupinou, instančním objektem služby nebo [spravovanou identitou pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Klíčové výhody použití oprávnění Azure RBAC pro zásady přístupu do trezoru jsou Centralizovaná správa řízení přístupu a její integrace s [Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md). Privileged Identity Management poskytuje aktivaci rolí na základě času a schválení, která vám umožní zmírnit rizika nadměrných, zbytečných nebo nepoužívaných přístupových oprávnění k prostředkům, o kterých se zajímáte.

Další informace o Key Vault rovině dat pomocí Azure RBAC najdete v tématu [Key Vault klíče, certifikáty a tajné klíče pomocí řízení přístupu na základě role Azure](rbac-guide.md) .

## <a name="firewalls-and-virtual-networks"></a>Brány firewall a virtuální sítě

Pro další vrstvu zabezpečení můžete nakonfigurovat brány firewall a pravidla virtuální sítě. Ve výchozím nastavení můžete nakonfigurovat Key Vault brány firewall a virtuální sítě tak, aby odepřely přístup k provozu ze všech sítí (včetně internetového provozu). Můžete udělit přístup k provozu z konkrétních [virtuálních sítí Azure](../../virtual-network/virtual-networks-overview.md) a rozsahů veřejných IP adres, což vám umožní vytvořit zabezpečenou hranici sítě pro vaše aplikace.

Tady je několik příkladů použití koncových bodů služby:

* Používáte Key Vault k ukládání šifrovacích klíčů, tajných klíčů aplikací a certifikátů a chcete zablokovat přístup k trezoru klíčů z veřejného Internetu.
* Chcete uzamknout přístup k trezoru klíčů, aby se k vašemu trezoru klíčů mohli připojit jenom vaše aplikace nebo krátký seznam určených hostitelů.
* Máte aplikaci spuštěnou ve službě Azure Virtual Network a tato virtuální síť je u všech příchozích a odchozích přenosů uzamčena. Vaše aplikace se pořád potřebuje připojit k Key Vault k načtení tajných klíčů nebo certifikátů nebo k používání kryptografických klíčů.

Další informace o Key Vault brány firewall a virtuálních sítích najdete v tématu [konfigurace Azure Key Vault bran firewall a virtuálních sítí](network-security.md) .

> [!NOTE]
> Brány firewall Key Vault a pravidla virtuální sítě se vztahují jenom na rovinu dat Key Vault. Brány firewall a pravidla virtuální sítě neovlivní Key Vault operace roviny řízení (například operace vytvoření, odstranění a úpravy, nastavení zásad přístupu, nastavení brány firewall a pravidel virtuální sítě).

## <a name="private-endpoint-connection"></a>Připojení privátního koncového bodu

V případě potřeby úplného blokování Key Vault expozici veřejnosti lze použít [privátní koncový bod Azure](../../private-link/private-endpoint-overview.md) . Privátní koncový bod Azure je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě využívající privátní propojení Azure. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně ho přinášejí do vaší virtuální sítě. Veškerý provoz do služby se dá směrovat prostřednictvím privátního koncového bodu, takže se nevyžadují žádné brány, zařízení NAT, ExpressRoute, připojení VPN ani veřejné IP adresy. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Můžete se připojit k instanci prostředku Azure, která poskytuje nejvyšší úroveň členitosti v řízení přístupu.

Běžné scénáře použití privátního odkazu pro služby Azure:

- **Služby soukromého přístupu na platformě Azure**: Připojte svoji virtuální síť ke službám v Azure bez veřejné IP adresy ve zdroji nebo cíli. Poskytovatelé služeb mohou vykreslovat své služby ve své vlastní virtuální síti a příjemci mají přístup k těmto službám ve své místní virtuální síti. Platforma privátního propojení bude zpracovávat připojení mezi příjemcem a službami přes páteřní síť Azure. 
 
- **Místní a partnerské sítě**: přístup ke službám, které běží v Azure, prostřednictvím privátního partnerského vztahu ExpressRoute, tunelových propojení VPN a partnerských virtuálních sítí s použitím privátních koncových bodů. Není nutné nastavovat veřejný partnerský vztah ani procházet internetem, aby bylo možné službu kontaktovat. Privátní odkaz poskytuje zabezpečený způsob migrace úloh do Azure.
 
- **Ochrana před únikem dat**: privátní koncový bod je namapován na instanci prostředku PaaS namísto celé služby. Příjemci se můžou připojit jenom ke konkrétnímu prostředku. Přístup k jakémukoli jinému prostředku ve službě je blokovaný. Tento mechanismus zajišťuje ochranu před riziky úniku dat. 
 
- **Globální dosah**: Připojte soukromě ke službám běžícím v jiných oblastech. Virtuální síť příjemce může být v oblasti A a může se připojit ke službám za soukromým odkazem v oblasti B.  
 
- **Rozšiřování na vlastní služby**: umožňuje stejné prostředí a funkce pro vlastní vygenerování služby pro uživatele v Azure. Umístěním služby za standardní Azure Load Balancer můžete povolit pro privátní propojení. Příjemce se pak může připojit přímo k vaší službě pomocí privátního koncového bodu ve své vlastní virtuální síti. Žádosti o připojení můžete spravovat pomocí toku volání schválení. Privátní propojení Azure funguje pro zákazníky a služby patřící různým klientům Azure Active Directory. 

Další informace o privátních koncových bodech najdete v tématu [Key Vault s privátním odkazem Azure](./private-link-service.md) .

## <a name="example"></a>Příklad

V tomto příkladu vyvíjíme aplikaci, která používá certifikát pro TLS/SSL, Azure Storage k ukládání dat a šifrovací klíč RSA 2 048 pro šifrování dat v Azure Storage. Naše aplikace běží na virtuálním počítači Azure (nebo v sadě škálování virtuálního počítače). K ukládání tajných klíčů aplikací můžeme použít Trezor klíčů. Pro ověření pomocí Azure AD můžeme uložit spouštěcí certifikát, který používá aplikace.

Potřebujeme přístup k následujícím uloženým klíčům a tajným klíčům:
- **Certifikát TLS/SSL**: používá se pro TLS/SSL.
- **Klíč úložiště**: používá se pro přístup k účtu úložiště.
- **RSA 2 048-bitový klíč**: používá se k zabalení nebo rozbalení šifrovacího klíče dat Azure Storage.
- **Spravovaná identita aplikace**: používá se k ověřování ve službě Azure AD. Po udělení přístupu k Key Vault může aplikace načíst klíč úložiště a certifikát.

Abychom určili, kdo může spravovat, nasazovat a auditovat naši aplikaci, je potřeba definovat následující role:
- **Bezpečnostní tým**: zaměstnanci oddělení IT od kanceláře CSO (hlavní bezpečnostní důstojník) nebo podobné přispěvatelé. Bezpečnostní tým je zodpovědný za řádné zabezpečení tajných kódů. Tajné klíče můžou zahrnovat certifikáty TLS/SSL, klíče RSA pro šifrování, připojovací řetězce a klíče účtu úložiště.
- **Vývojáři a operátoři**: zaměstnanci, kteří aplikaci vyvíjejí a nasazují v Azure. Členové tohoto týmu nejsou součástí bezpečnostních pracovníků. Nemají přístup k citlivým datům, jako jsou certifikáty TLS/SSL a klíče RSA. Pouze aplikace, kterou nasazují, by měli mít přístup k citlivým datům.
- **Auditori**: Tato role je určena pro přispěvatele, kteří nejsou členy vývoje nebo obecných zaměstnanců IT. Kontrolují použití a údržbu certifikátů, klíčů a tajných klíčů, aby bylo zajištěno dodržování standardů zabezpečení.

Existuje jiná role, která je mimo rozsah naší aplikace: Správce předplatného (nebo skupiny prostředků). Správce předplatného nastavuje počáteční přístupová oprávnění pro bezpečnostní tým. Poskytují přístup k bezpečnostnímu týmu pomocí skupiny prostředků, která má prostředky požadované aplikací.

Pro naše role musíme autorizovat tyto operace:

**Bezpečnostní tým**
- Vytvořte trezory klíčů.
- Zapněte protokolování Key Vault.
- Přidejte klíče a tajné kódy.
- Vytvořte zálohy klíčů pro zotavení po havárii.
- Nastavte zásady přístupu Key Vault a přiřaďte role pro udělení oprávnění uživatelům a aplikacím pro konkrétní operace.
- Pravidelně převeďte klíče a tajné kódy.

**Vývojáři a operátoři**
- Získejte odkazy od týmu zabezpečení pro certifikáty Bootstrap a TLS/SSL (kryptografické otisky), klíč úložiště (tajný identifikátor URI) a klíč RSA (identifikátor URI klíče) pro zabalení a rozbalení.
- Vývoj a nasazení aplikace pro přístup k certifikátům a tajným klíčům prostřednictvím kódu programu

**Auditoři**
- Zkontrolujte protokoly Key Vault a potvrďte správné použití klíčů a tajných kódů a dodržování standardů zabezpečení dat.

Následující tabulka shrnuje přístupová oprávnění pro naše role a aplikace.

| Role | Oprávnění k rovině správy | Oprávnění k rovině dat – zásady přístupu k trezoru | Oprávnění k rovině dat – Azure RBAC  |
| --- | --- | --- | --- |
| Bezpečnostní tým | [Přispěvatel Key Vault](../../role-based-access-control/built-in-roles.md#key-vault-contributor) | Certifikáty: všechny operace <br> Klíče: všechny operace <br> Tajné kódy: všechny operace | [Správce Key Vault](../../role-based-access-control/built-in-roles.md#key-vault-administrator) |
| Vývojáři a &nbsp; operátoři | Oprávnění k nasazení Key Vault<br><br> **Poznámka**: Toto oprávnění umožňuje nasazeným virtuálním počítačům načíst tajné kódy z trezoru klíčů. | Žádné | Žádné |
| Auditoři | Žádné | Certifikáty: seznam <br> Klíče: vypsat (list)<br>Tajné klíče: vypsat (list)<br><br> **Poznámka**: Toto oprávnění umožňuje auditorům kontrolovat atributy (značky, data aktivace, data vypršení platnosti) pro klíče a tajné klíče, které nejsou v protokolech vygenerovány. | [Čtecí modul Key Vault](../../role-based-access-control/built-in-roles.md#key-vault-reader) |
| Účet služby Azure Storage | Žádné | Klíče: Get, list, wrapKey, unwrapKey <br> | [Uživatel šifrování Key Vault šifrovací služby](../../role-based-access-control/built-in-roles.md#key-vault-crypto-service-encryption-user) |
| Aplikace | Žádné | Tajné kódy: získat, seznam <br> Certifikáty: získat, vypsat | [Key Vault čtenář](../../role-based-access-control/built-in-roles.md#key-vault-reader) [Key Vault tajného uživatele](../../role-based-access-control/built-in-roles.md#key-vault-secrets-user) |

Tři role týmu potřebují přístup k dalším prostředkům spolu s oprávněními Key Vault. Pokud chcete nasadit virtuální počítače (nebo funkci Web Apps Azure App Service), vývojáři a operátory potřebují přístup k nasazení. Auditoři potřebují přístup pro čtení k účtu úložiště, ve kterém jsou uložené protokoly Key Vault.

Náš příklad popisuje jednoduchý scénář. Scénáře reálného života můžou být složitější. V závislosti na vašich potřebách můžete upravit oprávnění k vašemu trezoru klíčů. Předpokládali jsme, že tým zabezpečení poskytuje klíče a tajné odkazy (identifikátory URI a kryptografické otisky), které jsou používány DevOps zaměstnanci ve svých aplikacích. Vývojáři a operátoři nevyžadují přístup k rovině dat. Zaměřili jsme se na to, jak váš Trezor klíčů zabezpečit.

> [!NOTE]
> Tento příklad ukazuje, jak je přístup Key Vault uzamčen v produkčním prostředí. Vývojáři by měli mít vlastní předplatné nebo skupinu prostředků s úplnými oprávněními ke správě trezorů, virtuálních počítačů a účtu úložiště, kde aplikace vyvíjí.

## <a name="resources"></a>Zdroje informací

- [Informace o službě Azure Key Vault](overview.md)
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
- [Azure RBAC](../../role-based-access-control/overview.md)
- [Privátní propojení](../../private-link/private-link-overview.md)

## <a name="next-steps"></a>Další kroky

[Ověřování ve službě Azure Key Vault](authentication.md)

[Přiřazení zásady přístupu Key Vault](assign-access-policy-portal.md)

[Přiřazení role Azure pro přístup k klíčům, tajným klíčům a certifikátům](rbac-guide.md)

[Konfigurace bran firewall a virtuálních sítí pro Key Vault](network-security.md)

[Navázání připojení privátního propojení k Key Vault](private-link-service.md)
