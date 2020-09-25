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
ms.topic: how-to
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: ce438ad0725aff677f897a635a0cd32d92bbbdbe
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91265465"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory"></a>Konfigurace deklarací identity skupin pro aplikace s Azure Active Directory

Azure Active Directory může poskytnout informace o členství skupiny uživatelů v tokenech pro použití v aplikacích.  Podporovány jsou dva hlavní vzory:

- Skupiny identifikované pomocí atributu identifikátoru objektu Azure Active Directory (OID)
- Skupiny identifikované atributy sAMAccountName nebo GroupSID pro synchronizované skupiny a uživatele služby Active Directory (AD)

> [!IMPORTANT]
> Existuje několik aspektů, které je potřeba poznamenat pro tuto funkci:
>
>- Podpora pro použití atributů sAMAccountName a identifikátoru zabezpečení (SID) synchronizovaných z místního prostředí je navržená tak, aby umožňovala přesun stávajících aplikací od AD FS a dalších zprostředkovatelů identity. Skupiny spravované ve službě Azure AD neobsahují atributy potřebné k vygenerování těchto deklarací.
>- Ve větších organizacích může počet skupin, kterých je uživatel členem, překročit limit, který Azure Active Directory do tokenu přidat. 150 skupiny pro tokeny SAML a 200 pro token JWT. To může vést k nepředvídatelným výsledkům. Pokud mají vaši uživatelé velký počet členství ve skupinách, doporučujeme použít možnost k omezení skupin vysílaných v deklaracích na příslušné skupiny pro danou aplikaci.  
>- V případě vývoje nových aplikací nebo v případech, kdy pro něj lze aplikaci nakonfigurovat a kde není podporována vnořená podpora skupin, doporučujeme, aby byla autorizace v aplikaci založena na aplikačních rolích, nikoli na skupinách.  Tím se omezí množství informací, které potřebuje přejít do tokenu, je bezpečnější a odděluje přiřazení uživatele od konfigurace aplikace.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Deklarace skupin pro aplikace, které se migrují od AD FS a dalších zprostředkovatelů identity

Mnohé aplikace nakonfigurované pro ověřování pomocí AD FS spoléhají na informace o členství ve skupině ve formě atributů skupin služby Windows AD.   Tyto atributy jsou skupiny sAMAccountName, které mohou být kvalifikovány názvem domény nebo identifikátor zabezpečení skupiny systému Windows (GroupSID).  Když je aplikace federované pomocí AD FS, AD FS pomocí funkce TokenGroups načíst členství ve skupinách pro daného uživatele.

Aplikace, která byla přesunuta z AD FS potřebuje deklarace identity ve stejném formátu. Deklarace identity skupin a rolí mohou být vygenerovány z Azure Active Directory obsahujících doménu s úplným oprávněním sAMAccountName nebo GroupSID synchronizovaném ze služby Active Directory, nikoli Azure Active Directory objectID skupiny.

Podporované formáty pro deklarace skupin jsou:

- **ObjectId Azure Active Directory skupin** (k dispozici pro všechny skupiny)
- **sAMAccountName** (k dispozici pro skupiny synchronizované ze služby Active Directory)
- **NetbiosDomain\sAMAccountName** (k dispozici pro skupiny synchronizované ze služby Active Directory)
- **DNSDomainName\sAMAccountName** (k dispozici pro skupiny synchronizované ze služby Active Directory)
- **Identifikátor zabezpečení místní skupiny** (k dispozici pro skupiny synchronizované ze služby Active Directory)

> [!NOTE]
> atributy účtu sAMAccountName a místní skupiny jsou dostupné jenom u objektů skupin synchronizovaných ze služby Active Directory.   Nejsou k dispozici ve skupinách vytvořených v Azure Active Directory nebo Office 365.   Aplikace nakonfigurované v Azure Active Directory za účelem získání synchronizovaných atributů místních skupin se získají jenom pro synchronizované skupiny.

## <a name="options-for-applications-to-consume-group-information"></a>Možnosti pro aplikace, které využívají informace o skupině

Aplikace mohou volat koncový bod skupin MS Graph a získat informace o skupině pro ověřeného uživatele. Toto volání zajistí, že všechny skupiny, kterých je uživatel členem, jsou k dispozici i v případě, že je zapojen velký počet skupin.  Výčet skupin je pak nezávislý na omezení velikosti tokenu.

Pokud však existující aplikace očekává využívání informací o skupině prostřednictvím deklarací identity, Azure Active Directory lze nakonfigurovat pomocí řady různých formátů deklarací identity.  Vezměte v úvahu následující možnosti:

- Při použití členství ve skupině pro účely autorizace v aplikaci je vhodnější použít identifikátor ObjectID skupiny. Identifikátor ObjectID skupiny je neměnný a jedinečný v Azure Active Directory a k dispozici pro všechny skupiny.
- Pokud pro autorizaci používáte místní skupinu sAMAccountName, použijte kvalifikované názvy domény;  Nejedná se o nemožnost konfliktu názvů. parametr sAMAccountName může být v rámci domény služby Active Directory jedinečný, ale pokud je více než jedna doména služby Active Directory synchronizována s Azure Active Directory tenant, existuje možnost, že více než jedna skupina bude mít stejný název.
- Zvažte použití [aplikačních rolí](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) k zajištění vrstvy nepřímých odkazů mezi členstvím ve skupině a aplikací.   Aplikace pak provede interní rozhodnutí o autorizaci na základě role Clams v tokenu.
- Pokud je aplikace nakonfigurovaná tak, aby získala atributy skupin synchronizované ze služby Active Directory, a skupina tyto atributy neobsahuje, nebude součástí deklarací identity.
- Deklarace skupiny v tokenech zahrnují vnořené skupiny s výjimkou případů, kdy se používá možnost k omezení deklarací identity skupin na skupiny přiřazené k aplikaci.  Pokud je uživatel členem skupiny GroupB a GroupB je členem skupiny, pak budou deklarace skupiny pro uživatele obsahovat obě skupiny a GroupB. Pokud mají uživatelé organizace velký počet členství ve skupinách, může počet skupin uvedených v tokenu zvětšit velikost tokenu.  Azure Active Directory omezuje počet skupin, které bude generovat v tokenu do 150 pro kontrolní výrazy SAML a 200 pro token JWT.  Pokud je uživatel členem většího počtu skupin, jsou vynechány skupiny a místo toho budou zahrnuty odkazy na koncový bod grafu pro získání informací o skupině.

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>Předpoklady pro používání atributů skupin synchronizovaných ze služby Active Directory

Deklarace identity členství ve skupině se dají vygenerovat v tokenech pro libovolnou skupinu, pokud použijete formát ObjectId. Chcete-li použít deklarace skupin ve formátech jiných než ID objektu skupiny, je nutné skupiny synchronizovat ze služby Active Directory pomocí Azure AD Connect.

Existují dva kroky ke konfiguraci Azure Active Directory k vygenerování názvů skupin pro skupiny služby Active Directory.

1. **Synchronizovat názvy skupin ze služby Active Directory** Než Azure Active Directory může v deklaracích identity skupiny nebo role generovat názvy skupin nebo místní skupiny SID, požadované atributy se musí synchronizovat ze služby Active Directory.  Musíte používat Azure AD Connect verze 1.2.70 nebo novější.   Starší verze Azure AD Connect než 1.2.70 budou synchronizovat objekty skupiny ze služby Active Directory, ale nebudou obsahovat požadované atributy názvu skupiny.  Upgradujte na aktuální verzi.

2. **Konfigurace registrace aplikace v Azure Active Directory, aby zahrnovala deklarace skupin v tokenech** Deklarace skupin je možné nakonfigurovat v části podnikové aplikace na portálu nebo pomocí manifestu aplikace v oddílu registrace aplikací.  Konfigurace deklarací skupin v manifestu aplikace naleznete níže v části "konfigurace Azure Active Directory registrace aplikací pro atributy skupiny" níže.

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>Přidání deklarací skupin do tokenů pro aplikace SAML pomocí konfigurace jednotného přihlašování

Pokud chcete nakonfigurovat deklarace skupin pro aplikaci v galerii nebo mimo galerii SAML, otevřete **podnikové aplikace**, klikněte na aplikaci v seznamu, vyberte **Konfigurace jednotného přihlašování**a pak vyberte **atributy uživatele & deklarace identity**.

Klikněte na **přidat deklaraci skupiny** .  

![Snímek obrazovky zobrazující stránku atributy uživatele & deklarace identity s vybranou možnost přidat deklaraci skupiny](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Pomocí přepínačů vyberte, které skupiny se mají do tokenu zahrnout.

![Snímek obrazovky, který zobrazuje okno deklarace skupiny se zvolenými možnostmi skupiny zabezpečení](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Výběr | Popis |
|----------|-------------|
| **Všechny skupiny** | Vygeneruje skupiny zabezpečení a distribuční seznamy a role.  |
| **Skupiny zabezpečení** | Vygeneruje skupiny zabezpečení, kterých je uživatel členem v deklaraci identity skupin. |
| **Role adresáře** | Pokud je uživateli přiřazené role adresáře, vygenerují se jako deklarace identity WIDS (deklarace identity skupin nebude vygenerována). |
| **Skupiny přiřazené k aplikaci** | Vygeneruje pouze skupiny, které jsou explicitně přiřazeny k aplikaci a uživatel je členem |

Pokud například chcete vygenerovat všechny skupiny zabezpečení, které je uživatel členem, vyberte skupiny zabezpečení.

![Snímek obrazovky, který zobrazuje okno deklarace skupiny se zvolenými možnostmi skupiny zabezpečení a otevře se rozevírací nabídka "zdrojový atribut".](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Pokud chcete vygenerovat skupiny pomocí atributů služby Active Directory synchronizovaných ze služby Active Directory místo identifikátorů objectID služby Azure AD, v rozevíracím seznamu vyberte požadovaný formát. V deklaracích budou zahrnuty pouze skupiny synchronizované ze služby Active Directory.

![Snímek obrazovky zobrazující, že je otevřená rozevírací nabídka "zdrojový atribut"](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Pokud chcete vygenerovat jenom skupiny přiřazené k aplikaci, vyberte **skupiny přiřazené k aplikaci** .

![Snímek obrazovky, který zobrazuje okno deklarace skupin s vybranými skupinami "skupiny přiřazené k aplikaci".](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

Do tokenu budou zahrnuty skupiny přiřazené k aplikaci.  Další skupiny, kterých je uživatel členem, budou vynechány.  S touto možností nejsou zahrnuty vnořené skupiny a uživatel musí být přímo členem skupiny přiřazené k aplikaci.

Chcete-li změnit skupiny přiřazené k aplikaci, vyberte aplikaci ze seznamu **podnikové aplikace** a pak klikněte na položku **Uživatelé a skupiny** v nabídce navigace na levé straně aplikace.

Podrobnosti o správě přiřazování skupin k aplikacím najdete v dokumentu [přiřazení uživatele nebo skupiny k podnikové aplikaci](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) .

### <a name="advanced-options"></a>Upřesnit možnosti

Způsob, jakým se generují deklarace skupin, se dají upravit pomocí nastavení v části Upřesnit možnosti.

Přizpůsobit název deklarace skupiny: Pokud je tato možnost vybraná, pro deklarace skupin se dá zadat jiný typ deklarace identity.   Do pole název zadejte typ deklarace identity a v poli obor názvů volitelný obor názvů pro deklaraci identity.

![Snímek obrazovky s oddílem rozšířené možnosti s názvem upravit název vybrané deklarace skupiny a zadat hodnoty název a obor názvů.](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Některé aplikace vyžadují, aby se informace o členství ve skupině zobrazovaly v deklaraci identity role. Skupiny uživatelů můžete volitelně vygenerovat jako role zaškrtnutím políčka Generovat skupiny a deklarace identity role.

![Snímek obrazovky s oddílem rozšířené možnosti s možností přizpůsobit název deklarace skupiny a vybrat skupiny pro vygenerování jako deklarace identity pro role](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Pokud se použije možnost Generovat data skupiny jako role, v deklaraci identity role se zobrazí jenom skupiny.  Všechny role aplikace, ke kterým se uživatel přiřadí, se nezobrazí v deklaraci identity role.

### <a name="edit-the-group-claims-configuration"></a>Úprava konfigurace deklarací skupin

Po přidání konfigurace deklarace skupiny do atributů uživatele & konfigurace deklarací se možnost přidat deklaraci skupiny zobrazí šedě.  Pokud chcete změnit konfiguraci deklarace identity skupiny, klikněte v seznamu **dalších deklarací** na deklaraci skupiny.

![uživatelské rozhraní deklarací identity](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Konfigurace registrace aplikace Azure AD pro atributy skupiny

Deklarace skupin lze také nakonfigurovat v části [volitelné deklarace](../../active-directory/develop/active-directory-optional-claims.md) v [manifestu aplikace](../../active-directory/develop/reference-app-manifest.md).

1. Na portálu – >Azure Active Directory > registrace aplikací – >vybrat manifest aplikace->

2. Povolení deklarací členství ve skupině změnou groupMembershipClaim

Platné hodnoty jsou:

| Výběr | Popis |
|----------|-------------|
| **Všem** | Vygeneruje skupiny zabezpečení, distribuční seznamy a role. |
| **"Zabezpečení"** | Vygeneruje skupiny zabezpečení, kterých je uživatel členem v deklaraci identity skupin. |
| **"DirectoryRole** | Pokud je uživateli přiřazené role adresáře, vygenerují se jako deklarace identity WIDS (deklarace identity skupin nebude vygenerována). |
| **"Aplikační aplikace** | Vygeneruje pouze skupiny, které jsou explicitně přiřazeny k aplikaci a uživatel je členem |

   Příklad:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Ve výchozím nastavení bude v hodnotě deklarace skupiny vygenerována hodnota ObjectID skupiny.  Pokud chcete změnit hodnotu deklarace identity tak, aby obsahovala atributy místních skupin, nebo změňte typ deklarace identity na role, použijte konfiguraci OptionalClaims následujícím způsobem:

3. Nastavte konfiguraci názvu skupiny volitelné deklarace identity.

   Pokud chcete, aby skupiny v tokenu obsahovaly atributy skupiny místních skupin služby AD, určete, na který typ tokenu má být volitelná deklarace identity použita v části volitelné deklarace.  Seznam může obsahovat více typů tokenů:

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

   | Volitelné schéma deklarací identity | Hodnota |
   |----------|-------------|
   | **Jméno:** | Musí být "skupiny" |
   | **Zdrojová** | Nepoužívá se. Vynechat nebo zadat hodnotu null |
   | **význam** | Nepoužívá se. Vynechat nebo zadat hodnotu false |
   | **AdditionalProperties** | Seznam dalších vlastností  Platné možnosti jsou "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   V additionalProperties se vyžadují jenom jedna z těchto "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Pokud je k dispozici více než jeden, použije se první a ostatní se ignorují.

   Některé aplikace vyžadují informace o skupině pro uživatele v deklaraci identity role.  Pokud chcete změnit typ deklarace identity na deklaraci skupiny na deklaraci identity role, přidejte do dalších vlastností emit_as_roles.  Hodnoty skupiny budou vygenerovány v deklaraci identity role.

   > [!NOTE]
   > Pokud se používá emit_as_roles, neobjeví se v deklaraci identity role žádné aplikační role nakonfigurované k přiřazení uživatele.

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

- [Přidejte autorizaci pomocí skupin & deklarace identity skupin do webové aplikace ASP.NET Core (ukázka kódu).](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-2-Groups/README.md)
- [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)
- [Konfigurace deklarací identity rolí](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
