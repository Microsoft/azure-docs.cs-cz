---
title: Konfigurace deklarací skupin pro aplikace s Azure Active Directory | Microsoft Docs
description: Informace o tom, jak nakonfigurovat deklarace identity skupin pro použití s Azure AD.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 3cb53656adb1dbeb5e5597d02edfe5be4dbec6a8
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170482"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Konfigurace deklarací identity skupin pro aplikace s Azure Active Directory (Public Preview)

Azure Active Directory může poskytnout informace o členství skupiny uživatelů v tokenech pro použití v aplikacích.  Podporovány jsou dva hlavní vzory:

- Skupiny identifikované podle jejich atributu identifikátoru objektu Azure Active Directory (všeobecně dostupné)
- Skupiny identifikované atributy sAMAccountName nebo GroupSID pro synchronizované skupiny a uživatele služby Active Directory (Public Preview)

> [!IMPORTANT]
> Pro tuto funkci verze Preview si pamatujte na řadu aspektů:
>
>- Podpora pro použití atributů sAMAccountName a identifikátoru zabezpečení (SID) synchronizovaných z místního prostředí je navržená tak, aby umožňovala přesun stávajících aplikací od AD FS a dalších zprostředkovatelů identity. Skupiny spravované ve službě Azure AD neobsahují atributy potřebné k vygenerování těchto deklarací.
>- Ve větších organizacích může počet skupin, kterých je uživatel členem, překročit limit, který Azure Active Directory do tokenu přidat. 150 skupiny pro tokeny SAML a 200 pro token JWT. To může vést k nepředvídatelným výsledkům. Pokud se jedná o potenciální problém, doporučujeme testování a v případě potřeby počkat až do přidání vylepšení, které vám umožní omezit deklarace identity na příslušné skupiny pro danou aplikaci.  
>- V případě vývoje nových aplikací nebo v případech, kdy pro něj lze aplikaci nakonfigurovat a kde není podporována vnořená podpora skupin, doporučujeme, aby byla autorizace v aplikaci založena na aplikačních rolích, nikoli na skupinách.  Tím se omezí množství informací, které potřebuje přejít do tokenu, je bezpečnější a odděluje přiřazení uživatele od konfigurace aplikace.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Deklarace skupin pro aplikace, které se migrují od AD FS a dalších zprostředkovatelů identity

Mnoho aplikací, které jsou nakonfigurovány pro ověřování pomocí AD FS, využívají informace o členství ve skupině ve formě atributů skupin služby Windows AD.   Tyto atributy jsou skupiny sAMAccountName, které mohou být kvalifikovány názvem domény nebo identifikátor zabezpečení skupiny systému Windows (GroupSID).  Když je aplikace federované pomocí AD FS, AD FS pomocí funkce TokenGroups načíst členství ve skupinách pro daného uživatele.

Aby se shodovalo s tokenem, který aplikace obdržela z AD FS, můžou se vysílat deklarace identity skupiny a role, které budou obsahovat kvalifikovaný identifikátor sAMAccountName domény, ne Azure Active Directory objectID skupiny.

Podporované formáty pro deklarace skupin jsou:

- **ObjectId Azure Active Directory skupiny** (K dispozici pro všechny skupiny)
- **sAMAccountName** (K dispozici pro skupiny synchronizované ze služby Active Directory)
- **NetbiosDomain\sAMAccountName** (K dispozici pro skupiny synchronizované ze služby Active Directory)
- **DNSDomainName\sAMAccountName** (K dispozici pro skupiny synchronizované ze služby Active Directory)
- **Identifikátor zabezpečení místní skupiny** (K dispozici pro skupiny synchronizované ze služby Active Directory)

> [!NOTE]
> atributy účtu sAMAccountName a místní skupiny jsou dostupné jenom u objektů skupin synchronizovaných ze služby Active Directory.   Nejsou k dispozici ve skupinách vytvořených v Azure Active Directory nebo Office 365.   Aplikace nakonfigurované v Azure Active Directory za účelem získání synchronizovaných atributů místních skupin se získají jenom pro synchronizované skupiny.

## <a name="options-for-applications-to-consume-group-information"></a>Možnosti pro aplikace, které využívají informace o skupině

Jedním ze způsobů, jak aplikace získat informace o skupině, je zavolat koncový bod skupin grafu, aby bylo možné získat členství pro ověřeného uživatele ve skupině. Toto volání zajistí, že všechny skupiny, kterých je uživatel členem, jsou k dispozici i v případě, že existuje velký počet skupin a aplikace musí vytvořit výčet všech skupin, kterých je uživatel členem.  Výčet skupin je pak nezávislý na omezení velikosti tokenu.

Pokud ale existující aplikace už očekává využívání informací o skupině prostřednictvím deklarací v tokenu, který obdrží, Azure Active Directory dá nakonfigurovat několik různých možností deklarace identity, aby vyhovovaly potřebám aplikace.  Vezměte v úvahu následující možnosti:

- Při použití členství ve skupině pro účely autorizace v aplikaci je vhodnější použít identifikátor ObjectID skupiny, který je neměnný a jedinečný v Azure Active Directory a k dispozici pro všechny skupiny.
- Pokud pro autorizaci používáte místní skupinu sAMAccountName, použijte kvalifikované názvy domény;  neexistují situace, kdy došlo k konfliktu názvů. název sAMAccountName sám o sobě může být v rámci domény služby Active Directory jedinečný, ale pokud je více než jedna doména služby Active Directory synchronizovaná s Azure Active Directory tenant, existuje možnost, že více než jedna skupina bude mít stejný název.
- Zvažte použití [aplikačních rolí](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) k zajištění vrstvy nepřímých odkazů mezi členstvím ve skupině a aplikací.   Aplikace pak provede interní rozhodnutí o autorizaci na základě role Clams v tokenu.
- Pokud je aplikace nakonfigurovaná tak, aby získala atributy skupin synchronizované ze služby Active Directory, a skupina tyto atributy neobsahuje, nebude součástí deklarací identity.
- Deklarace skupin v tokenech zahrnují vnořené skupiny.   Pokud je uživatel členem skupiny GroupB a GroupB je členem skupiny, pak budou deklarace skupiny pro uživatele obsahovat obě skupiny a GroupB. Pro organizace, které mají velké využití vnořených skupin a uživatelů s velkým počtem členství ve skupině, může velikost tokenu zvětšit počet skupin uvedených v tokenu.   Azure Active Directory omezuje počet skupin, které vygeneruje v tokenu, do 150 pro kontrolní výrazy SAML a 200 pro token JWT, aby se zabránilo příliš velkému počtu tokenů.  Pokud je uživatel členem většího počtu skupin, než je limit, vygenerují se skupiny spolu s odkazem na koncový bod grafu, aby bylo možné získat informace o skupině.

> Předpoklady pro používání atributů skupin synchronizovaných ze služby Active Directory:   Skupiny je třeba synchronizovat ze služby Active Directory pomocí Azure AD Connect.

Existují dva kroky ke konfiguraci Azure Active Directory k vygenerování názvů skupin pro skupiny služby Active Directory.

1. **Synchronizovat názvy skupin ze služby Active Directory** Než Azure Active Directory může v deklaracích identity skupiny nebo role generovat názvy skupin nebo místní skupiny SID, požadované atributy se musí synchronizovat ze služby Active Directory.  Musíte používat Azure AD Connect verze 1.2.70 nebo novější.   Před verzí 1.2.70 Azure AD Connect provede synchronizaci objektů skupin ze služby Active Directory, ale ve výchozím nastavení nezahrne požadované atributy názvu skupiny.  Měli byste upgradovat na aktuální verzi.

2. **Konfigurace registrace aplikace v Azure Active Directory, aby zahrnovala deklarace skupin v tokenech** Deklarace skupin je možné nakonfigurovat buď v sekci podnikové aplikace portálu pro galerii, nebo mimo galerii aplikací jednotného přihlašování SAML nebo pomocí manifestu aplikace v oddílu registrace aplikací.  Konfigurace deklarací skupin v manifestu aplikace naleznete níže v části "konfigurace Azure Active Directory registrace aplikací pro atributy skupiny" níže.

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>Konfigurace deklarací identity skupin pro aplikace SAML pomocí konfigurace jednotného přihlašování

Pokud chcete nakonfigurovat deklarace skupin pro aplikaci Galerie nebo jinou aplikaci SAML, otevřete podnikové aplikace, klikněte na aplikaci v seznamu a vyberte konfigurace jednotného přihlašování.

Vyberte ikonu Upravit vedle možnosti "skupiny vracené v tokenu".

![uživatelské rozhraní deklarací identity](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Pomocí přepínačů vyberte, které skupiny se mají do tokenu zahrnout.

![uživatelské rozhraní deklarací identity](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Výběr | Popis |
|----------|-------------|
| **Všechny skupiny** | Vygeneruje skupiny zabezpečení a distribuční seznamy.   Také způsobí, že role adresáře, ke kterým je uživatel přiřazen, je vygenerována v deklaraci ' WIDS ', a všechny role aplikace, které je uživatel přiřazen k vygenerování v deklaraci identity rolí. |
| **Skupiny zabezpečení** | Vygeneruje skupiny zabezpečení, kterých je uživatel členem v deklaraci identity skupin. |
| **Distribuční seznamy** | Vygeneruje distribuční seznamy, kterých je uživatel členem. |
| **Role adresáře** | Pokud je uživateli přiřazené role adresáře, vygenerují se jako deklarace identity WIDS (deklarace identity skupin nebude vygenerována). |

Pokud například chcete vygenerovat všechny skupiny zabezpečení, které je uživatel členem, vyberte skupiny zabezpečení.

![uživatelské rozhraní deklarací identity](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Pokud chcete vygenerovat skupiny pomocí atributů služby Active Directory synchronizovaných ze služby Active Directory místo identifikátorů objectID služby Azure AD, v rozevíracím seznamu vyberte požadovaný formát.  Tím se nahradí ID objektu v deklaracích s řetězcovými hodnotami, které obsahují názvy skupin.   V deklaracích budou zahrnuty pouze skupiny synchronizované ze služby Active Directory.

![uživatelské rozhraní deklarací identity](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

### <a name="advanced-options"></a>Upřesnit možnosti

Způsob, jakým se generují deklarace skupin, se dají upravit pomocí nastavení v části Upřesnit možnosti.

Přizpůsobení názvu deklarace skupiny:  Pokud je tato možnost vybrána, lze pro deklarace skupin zadat jiný typ deklarace identity.   Do pole název zadejte typ deklarace identity a v poli obor názvů volitelný obor názvů pro deklaraci identity.

![uživatelské rozhraní deklarací identity](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Některé aplikace vyžadují, aby se informace o členství ve skupině zobrazovaly v deklaraci identity role. Skupiny uživatelů můžete volitelně vygenerovat jako role zaškrtnutím políčka Generovat skupiny a deklarace identity role.

![uživatelské rozhraní deklarací identity](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Pokud se použije možnost Generovat data skupiny jako role, v deklaraci identity role se zobrazí jenom skupiny.  Všechny role aplikace, ke kterým se uživatel přiřadí, se nezobrazí v deklaraci identity role.

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Konfigurace registrace aplikace Azure AD pro atributy skupiny

Deklarace skupin lze také nakonfigurovat v části [volitelné deklarace](../../active-directory/develop/active-directory-optional-claims.md) v [manifestu aplikace](../../active-directory/develop/reference-app-manifest.md).

1. Na portálu – > Azure Active Directory > Registrace aplikací – > vybrat manifest aplikace->

2. Povolení deklarací členství ve skupině změnou groupMembershipClaim

   Platné hodnoty jsou:

   - Všem
   - "Zabezpečení"
   - "DistributionList"
   - "DirectoryRole"

   Příklad:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Ve výchozím nastavení bude v hodnotě deklarace skupiny vygenerována hodnota ObjectID skupiny.  Pokud chcete změnit hodnotu deklarace identity tak, aby obsahovala atributy místních skupin, nebo změňte typ deklarace identity na role, použijte konfiguraci OptionalClaims následujícím způsobem:

3. Nastavte konfiguraci názvu skupiny volitelné deklarace identity.

   Pokud chcete, aby skupiny v tokenu obsahovaly atributy místní skupiny AD v části volitelné deklarace identity, určete, na který typ tokenu má být volitelná deklarace identity, název volitelné požadované deklarace identity a další požadované vlastnosti.  Seznam může obsahovat více typů tokenů:

   - idToken pro token ID OIDC
   - accessToken pro přístupový token OAuth/OIDC
   - Saml2Token pro tokeny SAML

   > [!NOTE]
   > Typ Saml2Token se vztahuje na formátovací tokeny SAML 1.1 i SAML 2.0.

   U každého relevantního typu tokenu Změňte deklaraci identity skupin na použití oddílu OptionalClaims v manifestu. Schéma OptionalClaims je následující:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Volitelné schéma deklarací identity | Value |
   |----------|-------------|
   | **Jméno:** | Musí být "skupiny" |
   | **Zdrojová** | Nepoužívá se. Vynechat nebo zadat hodnotu null |
   | **význam** | Nepoužívá se. Vynechat nebo zadat hodnotu false |
   | **additionalProperties:** | Seznam dalších vlastností  Platné možnosti jsou "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   V additionalProperties se vyžadují jenom jedna z těchto "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Pokud je k dispozici více než jeden, použije se první a ostatní se ignorují.

   Některé aplikace vyžadují informace o skupině pro uživatele v deklaraci identity role.  Pokud chcete změnit typ deklarace identity na deklaraci skupiny na deklaraci identity role, přidejte do dalších vlastností "emit_as_roles".  Hodnoty skupiny budou vygenerovány v deklaraci identity role.

   > [!NOTE]
   > Pokud se používá "emit_as_roles", budou se v deklaraci identity role zobrazovat i role aplikace, které jsou nakonfigurované tak, že se uživatel přiřadí.

### <a name="examples"></a>Příklady

Generování skupin jako názvů skupin v přístupových tokenech OAuth ve formátu dnsDomainName\SAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Chcete-li generovat názvy skupin, které mají být vráceny ve formátu netbiosDomain\samAccountName jako deklarace identit rolí v tokenech SAML a OIDC ID:

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

## <a name="next-steps"></a>Další kroky

[Co je hybridní identita?](whatis-hybrid-identity.md)
