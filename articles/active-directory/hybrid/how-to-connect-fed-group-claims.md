---
title: Konfigurace deklarace skupiny pro aplikace pomocí Azure Active Directory | Dokumentace Microsoftu
description: Informace o tom, jak nakonfigurovat deklarace skupiny pro použití s Azure AD.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.component: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 622a3ce0f80bd09bd09fa7ff097f68155318142d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58080352"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Konfigurace deklarace skupiny pro aplikace pomocí Azure Active Directory (Public Preview)

Informace o uživatelé skupiny členství v tokeny pro použití v rámci aplikací můžete poskytuje Azure Active Directory.  Podporovány jsou dva hlavní způsoby:

- Skupiny identifikovaný jejich Azure Active Directory identifikátor objektu (OID) (obecně dostupná verze)
- Skupiny identifikovaný SAMAccountName nebo GroupSID pro synchronizaci služby Active Directory (AD), skupiny a uživatele (Public Preview)

> [!Note]
> Podpora pro použití názvů a místní identifikátory zabezpečení (SID) je navržená k umožnění přesun stávajících aplikací ze služby AD FS.    Skupiny spravované ve službě Azure AD neobsahují atributy nezbytné pro generování tyto deklarace.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-idps"></a>Deklarace skupiny pro migrace ze služby AD FS a jiných zprostředkovatelů identity aplikace

Mnoho aplikací, které jsou nakonfigurovány pro ověřování pomocí služby AD FS spoléhá na informace o členství ve skupině ve formuláři Windows AD skupiny atributů.   Tyto atributy jsou skupiny SAMAccountName, který může být název kvalifikovaný podle domény nebo identifikátor SID skupiny Windows.  Když je aplikace federované se službou AD FS, AD FS používá funkci TokenGroups načíst členství ve skupině pro daného uživatele.

Tak, aby odpovídaly token, který bude aplikace přijímat ze služby AD FS, měl vyzařovaného deklarace skupiny a role obsahující doménu kvalifikovaný SAMAccountName, místo ID objektu skupiny Azure Active Directory.

Podporované formáty pro deklarace skupiny jsou:

- **Azure Active Directory GroupObjectId** (k dispozici pro všechny skupiny).
- **SAMAccountName** (k dispozici pro skupiny synchronizované z Active Directory).
- **NetbiosDomain\samAccountName** (k dispozici pro skupiny synchronizované z Active Directory).
- **DNSDomainName\samAccountName** (k dispozici pro skupiny synchronizované z Active Directory).

> [!NOTE]
> Atributy SAMAccountName a OnPremisesGroupSID jsou dostupné jenom pro objekty skupiny synchronizované z Active Directory.   Nejsou k dispozici na skupiny vytvořené v Azure Active Directory nebo Office 365.   Aplikace, které jsou závislé na místní skupiny atributů je získat synchronizovaných jenom pro skupiny.

## <a name="options-for-applications-to-consume-group-information"></a>Možnosti pro aplikace pro zpracování informací o skupině

Jedním ze způsobů pro aplikace k získání informací o skupině je volání koncového bodu skupiny grafu aby bylo možné načíst členství ve skupině pro ověřeného uživatele. Toto volání zajišťuje, že všechny skupiny, kterou je uživatel členem skupiny jsou k dispozici i v případě, že se využívá řada velký počet skupin a aplikace je potřeba vytvořit výčet všech skupin, jejichž je uživatel členem.  Výčet skupiny je pak nezávisle na omezení velikosti tokenu.

Pokud již existující aplikace se očekává, že využívat informace o skupině prostřednictvím deklarací identity v tokenu, které že obdrží, Azure Active Directory můžete nakonfigurovat s různými možnostmi různé deklarace podle potřeb aplikace.  Zvažte následující možnosti:

- Pokud používáte členství ve skupině pro účely autorizace aplikace (Určuje, zda členství ve skupině je získané z tokenu nebo graf), je vhodnější použít ID objektu skupiny, což je neměnná a jedinečný v Azure Active Directory a k dispozici pro všechny skupiny .
- Pokud používáte skupiny SAMAccountName pro autorizaci, použijte názvy domén kvalifikovaný;  má menší riziko situací, byly konflikt názvů. SAMAccountName sama o sobě může být jedinečné v rámci domény služby Active Directory, ale pokud více než jedné domény služby Active Directory se synchronizuje s tenantem Azure Active Directory je možné pro více než jedna skupina mít stejný název.
- Zvažte použití [aplikační role](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) zajistit určitou úroveň dereference mezi členství ve skupině a aplikací.   Aplikace pak provede interní autorizační rozhodnutí založené na clams role v tokenu.
- Pokud je aplikace nakonfigurována k získání skupiny atributů, které jsou synchronizované z Active Directory a neobsahuje skupinu těchto atributů nebudou zahrnuty v deklaracích.
- Skupinu deklarací identity v tokenech obsahovat vnořené skupiny.   Pokud je uživatel členem GroupB a GroupB je členem skupiny GroupA, bude obsahovat deklarace skupiny pro uživatele, GroupA a GroupB. Pro organizace s velkým využití vnořené skupiny a uživatele s velkým počtem členství ve skupinách můžete počet skupin, které jsou uvedené v tokenu zvětšit velikost tokenu.   Azure Active Directory omezuje počet skupin, které bude generovat token 150 pro kontrolní výrazy SAML a 200 pro token JWT.

> Předpoklady pro použití atributů skupiny synchronizované z Active Directory:   Skupiny musí být synchronizovány ze služby Active Directory pomocí služby Azure AD Connect.

Existují dva kroky pro konfiguraci služby Azure Active Directory a vygenerovat názvy skupiny pro skupiny služby Active Directory.

1. **Názvy skupin ze služby Active Directory synchronizovat** před Azure Active Directory můžete generovat názvy skupin nebo v místní skupině deklarace identifikátoru SID skupiny nebo role, povinné atributy musí být synchronizovány ze služby Active Directory.  Musíte používat Azure AD Connect verze 1.2.70 nebo novější.   Starší než verze 1.2.70 Azure AD Connect synchronizuje objekty th skupiny ze služby Active Directory, ale neobsahuje ve výchozím nastavení atributů názvu požadované skupiny.  Měli byste upgradovat na aktuální verzi.

2. **Konfigurace registrace aplikace v Azure Active Directory a zahrnují skupinu deklarací identity v tokenech** deklarace skupiny může být nakonfigurována buď v oddílu podnikové aplikace na portálu pro aplikaci Galerie nebo mimo Galerii SAML SSO, nebo v části registrace aplikací pomocí Manifest aplikace.  Konfigurace deklarace skupiny v manifestu naleznete v aplikaci "Konfigurace registrace Azure Active Directory aplikace pro skupinu atributů" níže.

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>Konfigurace deklarace skupiny pro aplikace SAML pomocí konfigurace jednotného přihlašování

Konfigurace deklarace skupiny pro aplikaci Galerie nebo mimo Galerii SAML, otevřete podnikové aplikace, klikněte na aplikaci v seznamu a vyberte položku Konfigurace jednotného přihlašování.

Vyberte ikonu úprav vedle "Skupiny vrátil token"

![deklarace identity uživatelského rozhraní](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Pomocí přepínačů vyberte skupiny, které by měl být součástí tokenu

![deklarace identity uživatelského rozhraní](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Výběr | Popis |
|----------|-------------|
| **Všechny skupiny** | Vysílá skupin zabezpečení a distribuční seznamy.   Navíc způsobí, že role adresáře má uživatel přiřazenou emitování v deklaraci "wids" a všechny aplikační role, které má uživatel přiřazenou emitování v deklarace identity rolí. |
| **Skupiny zabezpečení** | Vysílá skupiny zabezpečení, které uživatel je členem v deklaraci identity skupiny |
| **Distribuční seznam** | Vysílá distribuční skupiny, jejichž je uživatel členem |
| **Role adresáře** | Pokud má uživatel přiřazené role adresáře jsou emitovány jako "wids deklarace identity (skupiny, které deklarace identity, nebude se emitovat) |

Například ke generování všechny uživatel je členem skupiny zabezpečení, vyberte skupiny zabezpečení

![deklarace identity uživatelského rozhraní](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

### <a name="advanced-options"></a>Pokročilá nastavení

Způsob, jakým jsou emitovány deklarace skupiny lze upravit pomocí nastavení v části Upřesnit možnosti

Upravit název deklarace skupiny:  Pokud je zaškrtnuto, jiný typ deklarací dá nastavit pro deklarace skupiny.   Zadejte typ deklarace identity v poli Název a volitelný obor názvů pro deklarace identity v oboru názvů polí.

![deklarace identity uživatelského rozhraní](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Vygenerovat skupiny pomocí služby Active Directory atributech namísto v Azure AD objectid zaškrtněte políčko, které nevrací skupiny jako názvy místo ID"a z rozevíracího seznamu vyberte formát.  Toto ID objektu v deklaracích nahradí řetězcové hodnoty obsahující názvy skupin.   Deklarace identity se zahrne jenom skupiny, které jsou synchronizované z Active Directory.

![deklarace identity uživatelského rozhraní](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Některé aplikace vyžadují informace o členství ve skupině se zobrazí v deklaraci "role". Zaškrtnutím políčka "Generování skupiny, které deklarace identity role" mohou volitelně vysílat skupin uživatele jako role.

![deklarace identity uživatelského rozhraní](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Pokud se používá možnost generování dat skupiny jako role, jenom skupiny se zobrazí v deklarace role.  Všechny aplikační role přiřazené uživateli se nezobrazí v deklarace role.

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Konfigurace registrace aplikace Azure AD pro skupinu atributů

Deklarace skupiny může být rovněž konfigurována ve [nepovinných deklarací identity](../../active-directory/develop/active-directory-optional-claims.md) část [Manifest aplikace](../../active-directory/develop/reference-app-manifest.md).

1. Na portálu -> Azure Active Directory -> aplikace registrací -> vyberte aplikace -> manifestu

2. Povolit deklarace členství skupiny tak, že změníte groupMembershipClaim

   Platné hodnoty jsou:

   - "Vše"
   - "Skupiny SecurityGroup"
   - "DistributionList"
   - "DirectoryRole"

   Příklad:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Ve výchozím nastavení skupiny objectid bude vygenerován ve skupině hodnoty deklarace identity.  Chcete-li změnit hodnotu deklarace identity tak, aby obsahovala místní skupinu atributů, nebo chcete změnit typ deklarace identity do role, použijte konfiguraci OptionalClaims následujícím způsobem:

3. Nastavit nepovinné deklarace skupiny název konfigurace.

   Pokud budete chtít do skupin v tokenu obsahovat atributy skupin AD v části nepovinných deklarací identity určit, které typ tokenu volitelnou deklaraci má být použit pro místní, název požadované volitelnou deklaraci a další požadované vlastnosti.  Výpis je možný víc typy tokenů:

   - idToken pro token OIDC ID
   - accessToken pro přístupový token OAuth/OIDC
   - Saml2Token pro tokeny SAML.

   > [!NOTE]
   > Typ Saml2Token platí pro SAML1.1 i SAML2.0 tokenů formátu

   Pro každý odpovídající typ tokenu upravte deklaraci skupiny oddílů OptionalClaims v manifestu. Schéma OptionalClaims vypadá takto:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Schéma nepovinných deklarací identity | Hodnota |
   |----------|-------------|
   | **Jméno:** | Musí být "groups" |
   | **Zdroj:** | Nepoužívá se. Vynechat nebo zadat hodnotu null |
   | **essential:** | Nepoužívá se. Vynechat nebo zadat hodnotu false |
   | **additionalProperties:** | Seznam dalších vlastností.  Platné možnosti jsou "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   V additionalProperties pouze jeden "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" jsou vyžadovány.  Pokud se nachází více než jeden, slouží první a všechny ostatní ignorovat.

   Některé aplikace vyžadují skupiny informace o uživateli v deklarace role.  Chcete-li změnit typ deklarace identity ze skupiny uplatnit na deklarace role, přidejte do další vlastnosti "emit_as_roles".  Skupinové hodnoty budou zaznamenávány do deklarace role.

   > [!NOTE]
   > Pokud se používá "emit_as_roles" všechny aplikační role nakonfigurované, že se uživateli přiřadila se nezobrazí v deklarace role

### <a name="examples"></a>Příklady

Generování skupiny jako názvy skupin v přístupových tokenů OAuth ve formátu dnsDomainName\SAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Ke generování názvů skupin, které se mají vrátit ve formátu netbiosDomain\samAccountName jako SAML a OIDC ID tokeny deklarací identity rolí:

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }
 ```

## <a name="next-steps"></a>Další postup

[Co je hybridní identita?](whatis-hybrid-identity.md)