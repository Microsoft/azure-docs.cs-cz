---
title: Poskytování volitelných deklarací identity aplikacím Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Jak přidat vlastní nebo další deklarace identity do tokenů SAML 2.0 a JSON Web Tokens (JWT) vydaných službou Azure Active Directory.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 3/11/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 23d83b59c510f2565b2f66f78dad56c9c9592dd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136513"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Postup: Poskytněte volitelné deklarace identity v aplikaci Azure AD

Vývojáři aplikací můžete použít volitelné deklarace identity ve svých aplikacích Azure AD určit, které deklarace, které chtějí v tokenech odeslaných do jejich aplikace. 

Volitelné deklarace identity můžete použít k:

- Vyberte další deklarace identity, které chcete zahrnout do tokenů pro vaši aplikaci.
- Změňte chování určitých deklarací, které Azure AD vrátí v tokenech.
- Přidejte a získejte přístup k vlastním deklaracím identity pro vaši aplikaci.

Seznamy standardních deklarací naleznete v [dokumentaci k přístupovému tokenu](access-tokens.md) a [id_token](id-tokens.md) deklarací. 

Zatímco volitelné deklarace identity jsou podporovány v tokenech formátu v1.0 a v2.0, stejně jako tokeny SAML, poskytují většinu své hodnoty při přechodu z v1.0 na v2.0. Jedním z cílů [koncového bodu platformy identity Microsoftu v2.0](active-directory-appmodel-v2-overview.md) je menší velikost tokenu pro zajištění optimálního výkonu klientů. V důsledku toho několik deklarací dříve zahrnuty v přístupu a ID tokeny již nejsou k dispozici v tokeny v2.0 a musí být požádáni konkrétně na základě pro aplikaci.

**Tabulka 1: Použitelnost**

| Typ účtu | tokeny v1.0 | tokeny v2.0  |
|--------------|---------------|----------------|
| Osobní účet Microsoft  | Není dostupné.  | Podporuje se |
| Účet Azure AD      | Podporuje se | Podporuje se |

## <a name="v10-and-v20-optional-claims-set"></a>sada volitelných deklarací v1.0 a v2.0

Sada volitelných deklarací, které jsou k dispozici ve výchozím nastavení pro aplikace, které mají být používány, je uvedena níže. Pokud chcete přidat vlastní volitelné deklarace identity pro vaši aplikaci, přečtěte si níže informace [o rozšířeních adresářů](#configuring-directory-extension-optional-claims). Při přidávání deklarací identity k **přístupovému tokenu**se deklarace identity vztahují na přístupové tokeny požadované *pro* aplikaci (webové rozhraní API), nikoli na deklarace *požadované* aplikací. Bez ohledu na to, jak klient přistupuje k rozhraní API, správná data je k dispozici v přístupový token, který se používá k ověření proti rozhraní API.

> [!NOTE]
> Většina těchto deklarací identity může být zahrnuta do jwts pro tokeny v1.0 a v2.0, ale ne tokeny SAML, s výjimkou případů, kdy je uvedeno ve sloupci Typ tokenu. Spotřebitelské účty podporují podmnožinu těchto deklarací identity, která je označena ve sloupci Typ uživatele.  Mnoho uvedených deklarací se nevztahuje na uživatele příjemce `tenant_ctry` (nemají žádného klienta, takže nemá žádnou hodnotu).  

**Tabulka 2: v1.0 a v2.0 volitelná sada deklarací**

| Name (Název)                       |  Popis   | Typ tokenu | Typ uživatele | Poznámky  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Čas, kdy byl uživatel naposledy ověřen. Viz specifikace OpenID Connect.| JWT        |           |  |
| `tenant_region_scope`      | Oblast klienta prostředků | JWT        |           | |
| `home_oid`                 | Pro uživatele typu Host ID objektu uživatele v domovském tenantovi uživatele.| JWT        |           | |
| `sid`                      | ID relace, které se používá pro odhlášení uživatele relace. | JWT        |  Osobní účty a účty Azure AD.   |         |
| `platf`                    | Platforma zařízení    | JWT        |           | Omezeno na spravovaná zařízení, která můžou ověřit typ zařízení.|
| `verified_primary_email`   | Pochází z primárního autoritativního e-mailu uživatele      | JWT        |           |         |
| `verified_secondary_email` | Pochází z sekundárního autoritativního e-mailu uživatele   | JWT        |           |        |
| `enfpolids`                | Vynucená ID zásad. Seznam ID zásad, které byly vyhodnoceny pro aktuálního uživatele. | JWT |  |  |
| `vnet`                     | Informace specifikátoru virtuální sítě. | JWT        |           |      |
| `fwd`                      | IP adresa.| JWT    |   | Přidá původní adresu IPv4 žádajícího klienta (když je uvnitř virtuální sítě). |
| `ctry`                     | Země uživatele | JWT |  | Azure AD `ctry` vrátí volitelné deklarace, pokud je k dispozici a hodnota deklarace je standardní dvoupísmenný kód země, jako je fr, JP, SZ a tak dále. |
| `tenant_ctry`              | Země klienta prostředků | JWT | | |
| `xms_pdl`          | Upřednostňované umístění dat   | JWT | | Pro vícegeoklienty je upřednostňovaným umístěním dat třípísmenný kód zobrazující zeměpisnou oblast, ve které se uživatel nachází. Další informace najdete v [dokumentaci k Azure AD Connect o umístění upřednostňovaných dat](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Například: `APC` pro Asii a Tichomoří. |
| `xms_pl`                   | Preferovaný jazyk uživatele  | JWT ||Pokud je nastaven, je preferovaný jazyk uživatele. Pochází z jejich domácího tenanta, ve scénářích přístupu hosta. Formátovaný LL-CC ("en-us"). |
| `xms_tpl`                  | Preferovaný jazyk klienta| JWT | | Upřednostňovaný jazyk klienta prostředku, pokud je nastaven. Formátováno LL ("en"). |
| `ztdid`                    | ID nasazení bez dotykového ovládání | JWT | | Identita zařízení používaná pro [systém Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Adresovatelný e-mail pro tohoto uživatele, pokud uživatel má jeden.  | JWT, SAML | MSA, Azure AD | Tato hodnota je zahrnuta ve výchozím nastavení, pokud je uživatel hostem v tenantovi.  Pro spravované uživatele (uživatele uvnitř klienta) je nutné si ji vyžádat prostřednictvím této volitelné deklarace nebo pouze v 2.0 s oborem OpenID.  Pro spravované uživatele musí být e-mailová adresa nastavena na [portálu pro správu Office](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Volitelné formátování deklarací skupin |JWT, SAML| |Používá se ve spojení s groupmembershipclaims nastavení v [manifestu aplikace](reference-app-manifest.md), který musí být nastaventaké. Podrobnosti viz [Tvrzení skupiny](#configuring-groups-optional-claims) níže. Další informace o deklaracích skupin naleznete v tématu [Konfigurace deklarací skupiny](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Stav účtu uživatelů v tenantovi. | JWT, SAML | | Pokud je uživatel členem klienta, hodnota `0`je . Pokud jsou hostem, hodnota `1`je . |
| `upn`                      | Deklarace userPrincipalName. | JWT, SAML  |           | Přestože je toto tvrzení automaticky zahrnuto, můžete jej zadat jako volitelnou deklaraci pro připojení dalších vlastností a upravit jeho chování v případě uživatele typu Host.  |

## <a name="v20-specific-optional-claims-set"></a>sada volitelných deklarací specifických pro specifické 2.0

Tyto deklarace identity jsou vždy zahrnuty v 1.0 Azure AD tokeny, ale nejsou zahrnuty v v2.0 tokeny, pokud není požadováno. Tyto deklarace identity platí pouze pro jwts (ID tokeny a přístupové tokeny). 

**Tabulka 3: Volitelné deklarace pouze v2.0**

| JWT Reklamace     | Name (Název)                            | Popis                                | Poznámky |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP adresa                      | Adresa IP, ze které se klient přihlásil.   |       |
| `onprem_sid`  | Identifikátor místního zabezpečení |                                             |       |
| `pwd_exp`     | Čas vypršení platnosti hesla        | Datum, kdy vyprší platnost hesla. |       |
| `pwd_url`     | Změnit adresu URL hesla             | Adresa URL, kterou může uživatel navštívit a změnit heslo.   |   |
| `in_corp`     | Uvnitř podnikové sítě        | Signalizuje, pokud se klient přihlašuje z podnikové sítě. Pokud tomu tak není, nárok není zahrnut.   |  Na základě [nastavení důvěryhodných IP serverů](../authentication/howto-mfa-mfasettings.md#trusted-ips) v mfa.    |
| `nickname`    | Přezdívka                        | Další název pro uživatele. Přezdívka je oddělená od křestního jména nebo příjmení. Vyžaduje `profile` obor.| 
| `family_name` | Příjmení                       | Obsahuje příjmení, příjmení nebo příjmení uživatele, jak je definováno v objektu uživatele. <br>"family_name Millerová"" | Podporované v MSA a Azure AD. Vyžaduje `profile` obor.   |
| `given_name`  | Jméno                      | Poskytuje první nebo "dané" jméno uživatele, jak je nastaveno na objekt uživatele.<br>"given_name": "Frank"                   | Podporováno v MSA a Azure AD .  Vyžaduje `profile` obor. |
| `upn`         | Hlavní název uživatele | Identifer pro uživatele, který lze použít s parametrem username_hint.  Není trvalý identifikátor pro uživatele a by neměl být používán pro klíčová data. | Viz [další vlastnosti](#additional-properties-of-optional-claims) níže pro konfiguraci deklarace. Vyžaduje `profile` obor.|

### <a name="additional-properties-of-optional-claims"></a>Další vlastnosti volitelných deklarací

Některé volitelné deklarace identity lze nakonfigurovat tak, aby změnily způsob vrácení deklarace identity. Tyto další vlastnosti se většinou používají k migraci místních `include_externally_authenticated_upn_without_hash` aplikací s různými očekáváními dat`#`(například pomáhá klientům, kteří nemohou zpracovat značky hash ( ) v hlavní známce)

**Tabulka 4: Hodnoty pro konfiguraci volitelných deklarací identity**

| Název vlastnosti  | Další název vlastnosti | Popis |
|----------------|--------------------------|-------------|
| `upn`          |                          | Lze použít pro odpovědi SAML a JWT a pro tokeny v1.0 a v2.0. |
|                | `include_externally_authenticated_upn`  | Zahrnuje hlavní název paměti ZABEZPEČENÍ hosta uložený v tenantovi prostředku. Například `foo_hometenant.com#EXT#@resourcetenant.com`. |             
|                | `include_externally_authenticated_upn_without_hash` | Stejné jako výše, s tím`#`rozdílem, že značky`_`hash ( ) jsou nahrazeny například podtržítky ( ),`foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Příklad dalších vlastností

    ```json
        "optionalClaims": 
         {
             "idToken": [ 
            { 
                      "name": "upn", 
                      "essential": false,
                  "additionalProperties": [ "include_externally_authenticated_upn"]  
                    }
                 ]
        }
    ```

Tento objekt OptionalClaims způsobí, že token ID vrácený klientovi bude obsahovat deklaraci upn s dalšími informacemi o domovském klientovi a klientovi prostředků. Deklarace `upn` se změní pouze v tokenu, pokud je uživatel hostem v tenantovi (který používá jiný IDP pro ověřování). 

## <a name="configuring-optional-claims"></a>Konfigurace volitelných deklarací identity

> [!IMPORTANT]
> Přístupové tokeny jsou **vždy** generovány pomocí manifestu prostředku, nikoli klienta.  Takže v `...scope=https://graph.microsoft.com/user.read...` žádosti je prostředek rozhraní Microsoft Graph API.  Přístupový token je tedy vytvořen pomocí manifestu rozhraní API aplikace Microsoft Graph, nikoli manifestu klienta.  Změna manifestu pro vaši aplikaci nikdy nezpůsobí, že tokeny pro rozhraní API Microsoft Graph budou vypadat jinak.  Chcete-li ověřit, `accessToken` že vaše změny jsou v platnosti, požádejte o token pro vaši aplikaci, ne pro jinou aplikaci.  


Můžete nakonfigurovat volitelné deklarace identity pro vaši aplikaci prostřednictvím ui nebo manifestu aplikace.

1. Přejděte na [portál Azure](https://portal.azure.com). Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**.
1. V seznamu vyberte aplikaci, pro kterou chcete konfigurovat volitelné deklarace identity.

**Konfigurace volitelných deklarací prostřednictvím hlavního než vuového nástroje:**

[![Ukazuje, jak nakonfigurovat volitelné deklarace identity pomocí rozhraní.](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. V části **Spravovat** vyberte **konfigurace tokenu (náhled).**
2. Vyberte **Přidat volitelnou deklaraci .**
3. Vyberte typ tokenu, který chcete konfigurovat.
4. Vyberte volitelné deklarace identity, které chcete přidat.
5. Klikněte na **Přidat**.

**Konfigurace volitelných deklarací prostřednictvím manifestu aplikace:**

[![Ukazuje, jak nakonfigurovat volitelné deklarace identity pomocí manifestu aplikace.](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. V části **Spravovat** vyberte **Manifest**. Otevře se webový editor manifestu, který umožňuje upravovat manifest. Volitelně můžete vybrat **Stáhnout**, upravit manifest místně a potom ho **Nahrát** zpět do aplikace. Další informace o manifestu aplikace naleznete [v článku Principy manifestu aplikace Azure AD](reference-app-manifest.md).

    Následující položka manifestu aplikace přidá auth_time, ipaddr a upn volitelné deklarace identity id, přístup a saml tokeny.

    ```json
        "optionalClaims":  
           {
              "idToken": [
                    {
                          "name": "auth_time", 
                          "essential": false
                     }
              ],
              "accessToken": [
                     {
                            "name": "ipaddr", 
                            "essential": false
                      }
              ],
              "saml2Token": [
                      {
                            "name": "upn", 
                            "essential": false
                       },
                       {
                            "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                            "source": "user", 
                            "essential": false
                       }
               ]
           }
    ```

2. Jakmile budete hotoví, klikněte na **Uložit**. Nyní zadané volitelné deklarace budou zahrnuty do tokenů pro vaši aplikaci.    


### <a name="optionalclaims-type"></a>Typ OptionalClaims

Deklaruje volitelné deklarace požadované aplikací. Aplikace může nakonfigurovat volitelné deklarace identity, které mají být vráceny v každém ze tří typů tokenů (Token ID, přístupový token, TOKEN SAML 2), který může přijímat ze služby tokenů zabezpečení. Aplikace můžete nakonfigurovat jinou sadu volitelných deklarací identity, které mají být vráceny v každém typu tokenu. Vlastnost OptionalClaims entity Application je objekt OptionalClaims.

**Tabulka 5: Vlastnosti typu OptionalClaims**

| Name (Název)        | Typ                       | Popis                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Kolekce (OptionalClaim) | Volitelné deklarace vrácené v tokenu ID JWT. |
| `accessToken` | Kolekce (OptionalClaim) | Volitelné deklarace vrácené v přístupovém tokenu JWT. |
| `saml2Token`  | Kolekce (OptionalClaim) | Volitelné deklarace vrácené v tokenu SAML.   |

### <a name="optionalclaim-type"></a>Typ OptionalClaim

Obsahuje volitelnou deklaraci přidruženou k aplikaci nebo instančnímu objektu. Vlastnosti idToken, accessToken a saml2Token typu [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) jsou kolekcí Vlastností OptionalClaim.
Pokud je podporována konkrétní deklarace, můžete také upravit chování OptionalClaim pomocí AdditionalProperties pole.

**Tabulka 6: Vlastnosti typu OptionalClaim**

| Name (Název)                 | Typ                    | Popis                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Název volitelné deklarace.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | Zdroj (objekt adresáře) deklarace. Existují předdefinované deklarace identity a uživatelem definované deklarace identity z vlastností rozšíření. Pokud je zdrojová hodnota null, deklarace je předdefinovaná nepovinná deklarace. Pokud je zdrojová hodnota uživatel, hodnota ve vlastnosti name je vlastnost rozšíření z objektu uživatele. |
| `essential`            | Edm.Boolean             | Pokud je hodnota true, deklarace určená klientem je nezbytná k zajištění hladkého autorizačního prostředí pro konkrétní úlohu požadovanou koncovým uživatelem. Výchozí hodnota je False.                                                                                                             |
| `additionalProperties` | Kolekce (Edm.String) | Další vlastnosti deklarace. Pokud vlastnost existuje v této kolekci, upraví chování volitelné deklarace zadané ve vlastnosti name.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Konfigurace volitelných deklarací rozšíření adresáře

Kromě standardní volitelné sady deklarací identity můžete také nakonfigurovat tokeny tak, aby zahrnovaly rozšíření. Další informace naleznete [v dokumentaci k rozšíření Microsoft GraphProperty](https://docs.microsoft.com/graph/api/resources/extensionproperty?view=graph-rest-1.0) – všimněte si, že schémata a otevřená rozšíření nejsou podporovány volitelnými deklaracemi identity, pouze rozšíření adresáře stylu AAD-Graph. Tato funkce je užitečná pro připojení dalších informací o uživateli, které může vaše aplikace použít – například další identifikátor nebo důležitá možnost konfigurace, kterou uživatel nastavil. Příklad najdete v dolní části této stránky.

> [!NOTE]
> - Rozšíření schématu adresáře jsou funkce pouze pro Azure AD, takže pokud manifest aplikace požaduje vlastní rozšíření a uživatel MSA se přihlásí do vaší aplikace, tato rozšíření nebudou vrácena.

### <a name="directory-extension-formatting"></a>Formátování rozšíření adresáře

Při konfiguraci volitelných deklarací rozšíření adresáře pomocí manifestu aplikace použijte `extension_<appid>_<attributename>`úplný název rozšíření (ve formátu: ). Musí `<appid>` odpovídat ID aplikace požadující deklaraci. 

V rámci JWT budou tyto deklarace vyzařovány s následujícím názvem: `extn.<attributename>`.

V rámci tokenů SAML budou tyto deklarace vyzařovány v následujícím formátu URI:`http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>Konfigurace volitelných deklarací skupin

   > [!NOTE]
   > Možnost vyzařovat názvy skupin pro uživatele a skupiny synchronizované z místního prostředí je Public Preview.

Tato část popisuje možnosti konfigurace v části volitelné deklarace identity pro změnu atributů skupiny používaných v deklaracích skupiny z výchozího identifikátoru objektu skupiny na atributy synchronizované z místní služby Windows Active Directory. Můžete nakonfigurovat skupiny volitelné deklarace identity pro vaši aplikaci prostřednictvím ui nebo manifestu aplikace.

> [!IMPORTANT]
> Další podrobnosti, včetně důležitých upozornění pro public preview deklarací skupiny z místních atributů, [najdete v tématu Konfigurace deklarací skupiny pro aplikace s Azure AD](../hybrid/how-to-connect-fed-group-claims.md).

**Konfigurace volitelných deklarací skupin prostřednictvím hlavního i nového než ui:**
1. Přihlášení k [portálu Azure](https://portal.azure.com)
1. Po ověření zvolte klienta Azure AD tak, že ho vyberete v pravém horním rohu stránky.
1. V levém menu vyberte **službu Azure Active Directory.**
1. V části **Správa** vyberte **Registrace aplikací.**
1. V seznamu vyberte aplikaci, pro kterou chcete konfigurovat volitelné deklarace identity.
1. V části **Správa** vyberte **Konfigurace tokenu (náhled).**
2. Vybrat **přidat nárok na skupiny**
3. Vyberte typy skupin, které chcete vrátit (**Všechny skupiny**, **SecurityGroup**nebo **DirectoryRole**). Možnost **Všechny skupiny** zahrnuje **securitygroup**, **directoryrole**a **distribuční seznam.**
4. Volitelné: kliknutím na vlastnosti konkrétního typu tokenu můžete upravit hodnotu deklarace skupin tak, aby obsahovala atributy skupiny v místním prostředí, nebo změnit typ deklarace na roli.
5. Klikněte na **Uložit.**

**Konfigurace volitelných deklarací skupin prostřednictvím manifestu aplikace:**
1. Přihlášení k [portálu Azure](https://portal.azure.com)
1. Po ověření zvolte klienta Azure AD tak, že ho vyberete v pravém horním rohu stránky.
1. V levém menu vyberte **službu Azure Active Directory.**
1. V seznamu vyberte aplikaci, pro kterou chcete konfigurovat volitelné deklarace identity.
1. V části **Správa** vyberte **Manifest.**
3. Pomocí editoru manifestu přidejte následující položku:

   Platné hodnoty jsou:

   - "Vše" (tato možnost zahrnuje SecurityGroup, DirectoryRole a DistributionList)
   - "Skupina zabezpečení"
   - "DirectoryRole"

   Například:

    ```json
        "groupMembershipClaims": "SecurityGroup"
    ```

   Ve výchozím nastavení objekty skupiny objekty budou vyzařovány v hodnotě deklarace skupiny.  Chcete-li upravit hodnotu deklarace tak, aby obsahovala atributy skupiny v místním prostředí, nebo změnit typ deklarace identity na roli, použijte konfiguraci OptionalClaims následujícím způsobem:

3. Nastavte volitelné deklarace identity konfigurace názvu skupiny.

   Pokud chcete, aby skupiny v tokenu obsahovat místní atributy skupiny Služby ad v části volitelné deklarace identity určit, který typ tokenu volitelné deklarace by měla být použita, název volitelné deklarace požadované a všechny další požadované vlastnosti.  Můžete uvést více typů tokenů:

   - idToken pro token OIDC ID
   - accessToken pro přístupový token OAuth
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

   | Volitelné schéma deklarací identity | Hodnota |
   |----------|-------------|
   | **Jméno:** | Musí být "skupiny" |
   | **Zdroj:** | Nepoužívá se. Vynechat nebo zadat hodnotu null |
   | **Základní:** | Nepoužívá se. Vynechat nebo specifikovat nepravdivé |
   | **additionalProperties:** | Seznam dalších vlastností.  Platné možnosti jsou "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   V additionalProperties jsou požadovány pouze jeden z "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Pokud je k dispozici více než jeden, první se používá a všechny ostatní ignorovány.

   Některé aplikace vyžadují informace o skupině o uživateli v deklaraci role.  Chcete-li změnit typ deklarace z deklarace skupiny na deklaraci role, přidejte "emit_as_roles" k dalším vlastnostem.  Hodnoty skupiny budou vyzařovány v deklaraci role.

   > [!NOTE]
   > Pokud je použit "emit_as_roles" všechny role aplikace nakonfigurované, že uživatel je přiřazen se nezobrazí v deklaraci role

**Příklady:**

1) Vyzařujte skupiny jako názvy skupin v tokenech přístupu OAuth ve formátu dnsDomainName\sAMAccountName

    
    **Konfigurace ui:**

    [![Ukazuje, jak nakonfigurovat volitelné deklarace identity pomocí rozhraní.](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **Zápis manifestu přihlášky:**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) Vyzařovat názvy skupin, které mají být vráceny ve formátu netbiosDomain\sAMAccountName jako deklarace rolí v tokenech ID SAML a OIDC

    **Konfigurace ui:**

    [![Ukazuje, jak nakonfigurovat volitelné deklarace identity pomocí rozhraní.](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Zápis manifestu přihlášky:**
    
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
    ``` 
     

## <a name="optional-claims-example"></a>Příklad volitelných deklarací

V této části můžete projít scénář a zjistit, jak můžete použít volitelnou funkci deklarací identity pro vaši aplikaci.
Existuje několik možností, které jsou k dispozici pro aktualizaci vlastností konfigurace identity aplikace pro povolení a konfiguraci volitelných deklarací identity:
-    Můžete použít **konfiguraci tokenu (náhled)** UI (viz příklad níže)
-    Manifest můžete použít **(viz** příklad níže). Přečtěte si [principy dokumentu manifestu aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) nejprve pro úvod do manifestu.
-   Je také možné napsat aplikaci, která používá [rozhraní Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2F1.0&view=graph-rest-1.0) k aktualizaci aplikace. Typ [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) v referenční příručce rozhraní MICROSOFT Graph API vám může pomoci s konfigurací volitelných deklarací identity.

**Příklad:** V níže uvedeném příkladu použijete **konfiguraci tokenu (náhled)** a **manifest** k přidání volitelných deklarací tokenů přístupu, ID a SAML určených pro vaši aplikaci. Ke každému typu tokenu, který může aplikace získat, budou přidány různé volitelné deklarace identity:
-    Tokeny ID budou nyní obsahovat hlavní kdo nese hlavní`<upn>_<homedomain>#EXT#@<resourcedomain>`stránka uživatele pro federované uživatele v úplné podobě ( ).
-    Přístupové tokeny, které ostatní klienti požadují pro tuto aplikaci, budou nyní obsahovat deklaraci auth_time
-    Tokeny SAML budou nyní obsahovat rozšíření schématu adresáře skypeId (v tomto příkladu je ID aplikace pro tuto aplikaci ab603c56068041afb2f6832e2a17e237). Tokeny SAML zpřístupní Skype `extension_skypeId`ID jako .

**Konfigurace ui:**

1. Přihlášení k [portálu Azure](https://portal.azure.com)

1. Po ověření zvolte svého klienta Azure AD tak, že ho vyberete v pravém horním rohu stránky.

1. V levé nabídce vyberte **službu Azure Active Directory.**

1. V části **Správa** vyberte **Registrace aplikací**.

1. Vyhledejte aplikaci, pro kterou chcete nakonfigurovat volitelné deklarace identity, v seznamu a klikněte na ni.

1. V části **Správa** klikněte na **konfigurace tokenu (preview).**

1. Vyberte **Přidat volitelnou deklaraci**, vyberte typ tokenu **ID,** ze seznamu deklarací identity vyberte **upn** a klepněte na tlačítko **Přidat**.

1. Vyberte **Přidat volitelnou deklaraci**, vyberte typ tokenu **Přístupu,** vyberte **auth_time** ze seznamu deklarací identity a klikněte na **Přidat**.

1. Na obrazovce Přehled konfigurace tokenu klikněte na ikonu tužky vedle **upn**, klikněte na přepínač **Externě ověřené** a potom klikněte na **Uložit**.

1. Vyberte **Přidat volitelnou deklaraci**identity , vyberte typ tokenu **SAML,** ze seznamu deklarací identity vyberte **extn.skypeID** (platí jenom v případě, že jste vytvořili objekt uživatele Azure AD s názvem skypeID) a klikněte na **Přidat**.

    [![Ukazuje, jak nakonfigurovat volitelné deklarace identity pomocí rozhraní.](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Konfigurace manifestu:**
1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Po ověření zvolte svého klienta Azure AD tak, že ho vyberete v pravém horním rohu stránky.
1. V levé nabídce vyberte **službu Azure Active Directory.**
1. Vyhledejte aplikaci, pro kterou chcete nakonfigurovat volitelné deklarace identity, v seznamu a klikněte na ni.
1. V části **Spravovat** klikněte na **Manifest** a otevřete editor vřádkových manifestů.
1. Manifest můžete přímo upravit pomocí tohoto editoru. Manifest následuje schéma entity [Aplikace](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)a automaticky zformátuje manifest po uložení. Do vlastnosti budou `OptionalClaims` přidány nové prvky.

    ```json
            "optionalClaims": {
                "idToken": [ 
                     { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                     }
                     ],
                "accessToken": [ 
                      {
                        "name": "auth_time", 
                        "essential": false
                      }
                     ],
            "saml2Token": [ 
                  { 
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": true
                  }
                 ]
        ``` 


1. When you're finished updating the manifest, click **Save** to save the manifest.

## Next steps

Learn more about the standard claims provided by Azure AD.

- [ID tokens](id-tokens.md)
- [Access tokens](access-tokens.md)
