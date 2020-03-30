---
title: Konfigurace deklarací skupin pro aplikace pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft
description: Informace o konfiguraci deklarací skupiny pro použití ve službě Azure AD.
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
ms.openlocfilehash: b8708aec1137836516852135412c4c7cec2feba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408398"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Konfigurace deklarací skupin pro aplikace pomocí služby Azure Active Directory (Public Preview)

Služba Azure Active Directory může poskytovat informace o členství ve skupině uživatelů v tokenech pro použití v aplikacích.  Podporovány jsou dva hlavní vzory:

- Skupiny identifikované atributem identifikátoru objektu služby Azure Active Directory (OID) (obecně dostupné)
- Skupiny identifikované atributy sAMAccountName nebo GroupSID pro synchronizované skupiny a uživatele služby Active Directory (AD) (Public Preview)

> [!IMPORTANT]
> Existuje několik upozornění na tuto funkci náhledu:
>
>- Podpora pro použití atributů sAMAccountName a identifikátoru zabezpečení (SID) synchronizovaných z místního prostředí je navržena tak, aby umožnila přesun existujících aplikací ze služby AD FS a dalších poskytovatelů identit. Skupiny spravované ve službě Azure AD neobsahují atributy nezbytné k vyzařují tyto deklarace identity.
>- Ve větších organizacích může počet skupin, kterých je uživatel členem, překročit limit, který služba Azure Active Directory přidá do tokenu. 150 skupin pro token SAML a 200 pro JWT. To může vést k nepředvídatelným výsledkům. Pokud mají uživatelé velký počet členství ve skupinách, doporučujeme použít možnost omezit skupiny emitované v deklaracích na příslušné skupiny pro aplikaci.  
>- Pro vývoj nových aplikací nebo v případech, kdy pro ni lze aplikaci nakonfigurovat a kde není vyžadována vnořená podpora skupiny, doporučujeme, aby autorizace v aplikaci byla založena na rolích aplikace, nikoli na skupinách.  To omezuje množství informací, které je potřeba přejít do tokenu, je bezpečnější a odděluje přiřazení uživatele od konfigurace aplikace.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Skupinové deklarace identity pro aplikace migrující ze služby AD FS a dalších poskytovatelů identit

Mnoho aplikací nakonfigurovaných k ověřování pomocí služby AD FS závisí na informacích o členství ve skupině systému Windows ve formě atributů skupiny Služby Windows AD.   Tyto atributy jsou skupina sAMAccountName, která může být kvalifikovaný název podle domény nebo identifikátor zabezpečení skupiny systému Windows (GroupSID).  Když je aplikace federována se službou AD FS, používá službu AD FS k načtení členství ve skupinách pro uživatele funkci TokenGroups.

Aplikace, která byla přesunuta ze sládky AD FS, potřebuje deklarace identity ve stejném formátu. Deklarace skupina a role mohou být emitovány ze služby Azure Active Directory obsahující doménu sAMAccountName nebo GroupSID synchronizované ze služby Active Directory, nikoli ve skupině Azure Active Directory objectID.

Podporované formáty pro deklarace skupiny jsou:

- **Azure Active Directory Group ObjectId** (k dispozici pro všechny skupiny)
- **sAMAccountName** (K dispozici pro skupiny synchronizované ze služby Active Directory)
- **NetbiosDomain\sAMAccountName** (k dispozici pro skupiny synchronizované ze služby Active Directory)
- **DNSDomainName\sAMAccountName** (k dispozici pro skupiny synchronizované ze služby Active Directory)
- **Identifikátor zabezpečení místní skupiny** (k dispozici pro skupiny synchronizované ze služby Active Directory)

> [!NOTE]
> Atributy SID sAMAccountName a Místní skupiny jsou k dispozici pouze u objektů skupiny synchronizovaných ze služby Active Directory.   Nejsou dostupné ve skupinách vytvořených ve Službě Azure Active Directory nebo Office365.   Aplikace nakonfigurované ve službě Azure Active Directory k získání synchronizovaných atributů místní skupiny je získají pouze pro synchronizované skupiny.

## <a name="options-for-applications-to-consume-group-information"></a>Možnosti, jak aplikace využívat informace o skupině

Aplikace mohou volat koncový bod skupin y MS Graph a získat informace o skupině pro ověřeného uživatele. Toto volání zajišťuje, že všechny skupiny, které uživatel je členem jsou k dispozici i v případě, že jsou zapojeny velký počet skupin.  Výčet skupiny je pak nezávislý na omezení velikosti tokenu.

Pokud však existující aplikace očekává, že bude využívat informace o skupině prostřednictvím deklarací identity, lze službu Azure Active Directory nakonfigurovat pomocí několika různých formátů deklarací identity.  Zvažte následující možnosti:

- Při použití členství ve skupině pro účely autorizace v aplikaci je vhodnější použít Group ObjectID. Id objektu skupiny je neměnné a jedinečné ve službě Azure Active Directory a je k dispozici pro všechny skupiny.
- Pokud používáte místní skupinu sAMAccountName pro autorizaci, použijte kvalifikované názvy domény;  Je tu menší šance, že se jména střetnou. sAMAccountName může být jedinečný v rámci domény služby Active Directory, ale pokud je více než jedna doména služby Active Directory synchronizována s tenantem služby Azure Active Directory, existuje možnost, že více než jedna skupina bude mít stejný název.
- Zvažte použití [role aplikace](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) poskytnout vrstvu dereference mezi členství ve skupině a aplikace.   Aplikace pak provede rozhodnutí o autorizaci na základě škeble rolí v tokenu.
- Pokud je aplikace nakonfigurována tak, aby získala atributy skupiny, které jsou synchronizovány ze služby Active Directory, a skupina tyto atributy neobsahuje, nebude zahrnuta do deklarací identity.
- Deklarace skupiny v tokenech zahrnují vnořené skupiny s výjimkou použití možnosti omezit deklarace skupiny na skupiny přiřazené k aplikaci.  Pokud je uživatel členem GroupB a GroupB je členem GroupA, pak deklarace skupiny pro uživatele bude obsahovat groupa a GroupB. Pokud uživatelé organizace mají velký počet členství ve skupinách, počet skupin uvedených v tokenu může zvětšit velikost tokenu.  Azure Active Directory omezuje počet skupin, které bude vypouštět v tokenu na 150 pro kontrolní výrazy SAML a 200 pro JWT.  Pokud je uživatel členem většího počtu skupin, skupiny jsou vynechány a místo toho je zahrnut odkaz na koncový bod grafu pro získání informací o skupině.

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>Požadavky pro použití atributů skupiny synchronizovaných ze služby Active Directory

Nároky členství ve skupině mohou být emitovány v tokenech pro libovolnou skupinu, pokud používáte formát ObjectId. Chcete-li použít deklarace skupiny v jiných formátech než objectid skupiny, skupiny musí být synchronizovány ze služby Active Directory pomocí služby Azure AD Connect.

Existují dva kroky konfigurace služby Azure Active Directory pro vyzařování názvů skupin pro skupiny služby Active Directory.

1. **Synchronizace názvů skupin ze služby Active Directory** Před Azure Active Directory můžete vyzařovat názvy skupin nebo místní skupiny SID ve skupině nebo deklarace rolí, požadované atributy je třeba synchronizovat ze služby Active Directory.  Musíte být spuštěna Azure AD Connect verze 1.2.70 nebo novější.   Dřívější verze Služby Azure AD Connect než 1.2.70 synchronizují objekty skupiny ze služby Active Directory, ale nebudou obsahovat atributy požadovaného názvu skupiny.  Upgradujte na aktuální verzi.

2. **Konfigurace registrace aplikace ve službě Azure Active Directory tak, aby zahrnovala deklarace skupiny do tokenů** Deklarace skupiny lze nakonfigurovat v části Podnikové aplikace na portálu nebo pomocí manifestu aplikace v části Registrace aplikací.  Postup konfigurace deklarací skupin v manifestu aplikace najdete v části Konfigurace registrace aplikace Azure Active Directory pro atributy skupiny níže.

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>Přidání deklarací skupiny do tokenů pro aplikace SAML pomocí konfigurace přihlašování k samozabezpečení

Chcete-li nakonfigurovat deklarace skupiny pro aplikaci SAML galerie nebo jiné galerie, otevřete **podnikové aplikace**, klepněte na aplikaci v seznamu, vyberte **konfiguraci jednotného přihlášení**a pak vyberte **Atributy uživatele & deklarace .**

Klikněte na **Přidat skupinovou deklaraci.**  

![nároky uI](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Pomocí přepínacích tlačítek vyberte, které skupiny mají být zahrnuty do tokenu.

![nároky uI](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Výběr | Popis |
|----------|-------------|
| **Všechny skupiny** | Vyzařuje skupiny zabezpečení a distribuční seznamy a role.  |
| **Skupiny zabezpečení** | Vyzařuje skupiny zabezpečení, které uživatel je členem ve skupinách nárok |
| **Role adresáře** | Pokud je uživateli přiřazeny role adresáře, jsou vyzařovány jako deklarace wids (deklarace skupin nebudou vyzařovány) |
| **Skupiny přiřazené k aplikaci** | Vyzařuje pouze skupiny, které jsou explicitně přiřazeny k aplikaci a uživatel je členem |

Chcete-li například vyzařovat všechny skupiny zabezpečení, kterých je uživatel členem, vyberte možnost Skupiny zabezpečení

![nároky uI](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Chcete-li vyzařovat skupiny pomocí atributů služby Active Directory synchronizovaných ze služby Active Directory namísto objektů Azure AD, vyberte požadovaný formát z rozevíracího seznamu. Do deklarací identity budou zahrnuty pouze skupiny synchronizované ze služby Active Directory.

![nároky uI](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Chcete-li vyzařovat pouze skupiny přiřazené k aplikaci, vyberte **položku Skupiny přiřazené k aplikaci.**

![nároky uI](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

Skupiny přiřazené k aplikaci budou zahrnuty do tokenu.  Ostatní skupiny, které je uživatel členem, budou vynechány.  S touto volbou vnořené skupiny nejsou zahrnuty a uživatel musí být přímým členem skupiny přiřazené k aplikaci.

Chcete-li změnit skupiny přiřazené k aplikaci, vyberte aplikaci ze seznamu **Podnikové aplikace** a v levé navigační nabídce aplikace klikněte na **Položku Uživatelé a skupiny.**

Podrobnosti o správě přiřazení skupiny k aplikacím najdete v dokumentu [Přiřazení uživatele nebo skupiny k podnikové aplikaci.](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)

### <a name="advanced-options"></a>Upřesnit možnosti

Způsob vyzařování deklarací skupiny lze upravit podle nastavení v části Upřesnit možnosti

Přizpůsobení názvu deklarace skupiny: Pokud je vybrána tato možnost, lze pro deklarace skupiny zadat jiný typ deklarace identity.   Zadejte typ deklarace do pole Název a volitelný obor názvů pro deklaraci v poli oboru názvů.

![nároky uI](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Některé aplikace vyžadují, aby se informace o členství ve skupině zobrazovaly v deklaraci role. Volitelně můžete vyzařovat skupiny uživatele jako role zaškrtnutím políčka Vyzařovat skupiny rolí.

![nároky uI](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Pokud se používá možnost vyzařovat data skupiny jako role, zobrazí se v deklaraci role pouze skupiny.  Všechny role aplikace, které je uživateli přiřazen, se v deklaraci role nezobrazí.

### <a name="edit-the-group-claims-configuration"></a>Úprava konfigurace deklarací skupiny

Jakmile je konfigurace deklarace skupiny přidána do konfigurace Atributy uživatele & deklarace identity, bude možnost přidat deklaraci skupiny zašedlá.  Chcete-li změnit konfiguraci deklarace skupiny, klikněte na deklaraci skupiny v seznamu **Další deklarace.**

![nároky uI](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Konfigurace registrace aplikace Azure AD pro atributy skupiny

Deklarace skupiny lze také nakonfigurovat v části [Volitelné deklarace](../../active-directory/develop/active-directory-optional-claims.md) identity [manifestu aplikace](../../active-directory/develop/reference-app-manifest.md).

1. Na portálu ->Azure Active Directory -> Registrace aplikací->Select Application->Manifest

2. Povolení deklarací členství ve skupině změnou deklarace skupiny MembershipClaim

Platné hodnoty jsou:

| Výběr | Popis |
|----------|-------------|
| **"Vše"** | Vyzařuje skupiny zabezpečení, distribuční seznamy a role |
| **"Skupina zabezpečení"** | Vyzařuje skupiny zabezpečení, které uživatel je členem ve skupinách nárok |
| **"Adresářová role** | Pokud je uživateli přiřazeny role adresáře, jsou vyzařovány jako deklarace wids (deklarace skupin nebudou vyzařovány) |
| **"Skupina aplikací** | Vyzařuje pouze skupiny, které jsou explicitně přiřazeny k aplikaci a uživatel je členem |

   Například:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Ve výchozím nastavení objekty skupiny objekty budou vyzařovány v hodnotě deklarace skupiny.  Chcete-li upravit hodnotu deklarace tak, aby obsahovala atributy skupiny v místním prostředí, nebo změnit typ deklarace identity na roli, použijte konfiguraci OptionalClaims následujícím způsobem:

3. Nastavte volitelné deklarace identity konfigurace názvu skupiny.

   Pokud chcete, aby skupiny v tokenu obsahovaly místní atributy skupiny Služby AD, určete, na který typ tokenu by měla být použita v části volitelné deklarace identity.  Můžete uvést více typů tokenů:

   - idToken pro token OIDC ID
   - accessToken pro přístupový token OAuth/OIDC
   - Saml2Token pro tokeny SAML.

   > [!NOTE]
   > Typ Saml2Token se vztahuje na tokeny formátu SAML1.1 a SAML2.0

   Pro každý příslušný typ tokenu upravte skupiny, které tvrdí, že používají oddíl OptionalClaims v manifestu. Schéma OptionalClaims je následující:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Schéma volitelných deklarací identity | Hodnota |
   |----------|-------------|
   | **Jméno:** | Musí být "skupiny" |
   | **Zdroj:** | Nepoužívá se. Vynechat nebo zadat hodnotu null |
   | **Základní:** | Nepoužívá se. Vynechat nebo specifikovat nepravdivé |
   | **additionalProperties:** | Seznam dalších vlastností.  Platné možnosti jsou "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   V additionalProperties jsou požadovány pouze jeden z "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Pokud je k dispozici více než jeden, první se používá a všechny ostatní ignorovány.

   Některé aplikace vyžadují informace o skupině o uživateli v deklaraci role.  Chcete-li změnit typ deklarace z deklarace skupiny na deklaraci role, přidejte "emit_as_roles" k dalším vlastnostem.  Hodnoty skupiny budou vyzařovány v deklaraci role.

   > [!NOTE]
   > Pokud je použit "emit_as_roles" všechny role aplikace nakonfigurované, že uživatel je přiřazen se nezobrazí v deklaraci role

### <a name="examples"></a>Příklady

Vyzařujte skupiny jako názvy skupin v přístupových tokenech OAuth ve formátu dnsDomainName\SAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Chcete-li vyzařovat názvy skupin, které mají být vráceny ve formátu netbiosDomain\samAccountName jako deklarace rolí v tokenech ID SAML a OIDC:

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

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)

[Konfigurace deklarací identity rolí](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
