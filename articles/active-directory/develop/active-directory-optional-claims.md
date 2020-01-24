---
title: Poskytněte volitelné deklarace identity aplikacím Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Jak přidat vlastní nebo další deklarace k tokenům SAML 2,0 a JSON web tokens (JWT) vydaných Azure Active Directory.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/08/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 1bc2c3a17aef232df184926dca5f70eac61b03ac
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76698760"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Postupy: poskytnutí volitelných deklarací identity vaší aplikaci Azure AD

Vývojáři aplikací můžou ve svých aplikacích Azure AD použít volitelné deklarace identity a určit, které deklarace identity se mají v tokenech odeslaných do jejich aplikace. 

Volitelné deklarace identity můžete použít k těmto akcím:

- Vyberte další deklarace identity, které chcete zahrnout do tokenů pro vaši aplikaci.
- Změna chování určitých deklarací identity, které Azure AD vrací v tokenech
- Přidejte a získejte přístup k vlastním deklaracím pro vaši aplikaci.

Seznam standardních deklarací identity najdete v dokumentaci [k tokenům](access-tokens.md) a [id_token](id-tokens.md) deklarací identity. 

I když jsou volitelné deklarace identity podporované v tokenech formátu v 1.0 i v 2.0 i v tokenech SAML, poskytují většinu jejich hodnoty při přechodu z verze 1.0 do verze 2.0. Jedním z cílů [koncového bodu Microsoft Identity Platform v 2.0](active-directory-appmodel-v2-overview.md) jsou menší velikosti tokenů, aby se zajistil optimální výkon pro klienty. V důsledku toho již v tokenech verze 2.0 nejsou k dispozici několik deklarací, které byly dříve zahrnuty v tokenech Access a ID, a je nutné, abyste byli požádáni o konkrétně na základě jednotlivých aplikací.

**Tabulka 1: použitelnost**

| Typ účtu | tokeny v 1.0 | tokeny v 2.0  |
|--------------|---------------|----------------|
| Osobní účet Microsoft  | Nevztahuje se  | Podporováno |
| Účet Azure AD      | Podporováno | Podporováno |

## <a name="v10-and-v20-optional-claims-set"></a>volitelná sada deklarací v 1.0 a v 2.0

Sada volitelných deklarací, které jsou ve výchozím nastavení k dispozici pro použití aplikací, jsou uvedeny níže. Pokud chcete přidat vlastní volitelné deklarace identity pro vaši aplikaci, přečtěte si část [rozšíření adresáře](#configuring-directory-extension-optional-claims)níže. Při přidávání deklarací do **přístupového tokenu**se deklarace vztahují na přístupové tokeny požadované *pro* aplikaci (webové rozhraní API), nikoli na deklarace identity *, které aplikace požaduje.* Bez ohledu na to, jak klient přistupuje k rozhraní API, se v přístupovém tokenu, který se používá k ověřování na rozhraní API, nachází správná data.

> [!NOTE]
> Většinu těchto deklarací lze zahrnout do JWTs pro tokeny v 1.0 a v 2.0, ale ne tokeny SAML, s výjimkou případů, kdy je uvedeno ve sloupci typ tokenu. Uživatelské účty podporují podmnožinu těchto deklarací, která je označena ve sloupci "typ uživatele".  Mnohé z uvedených deklarací se nevztahují na uživatele spotřebitele (nemají žádného tenanta, takže `tenant_ctry` nemá žádnou hodnotu).  

**Tabulka 2: volitelná sada deklarací identity v 1.0 a v 2.0**

| Name (Název)                       |  Popis   | Typ tokenu | Typ uživatele | Poznámky  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Čas posledního ověření uživatele Viz specifikace OpenID Connect.| JWT        |           |  |
| `tenant_region_scope`      | Oblast tenanta prostředků | JWT        |           | |
| `home_oid`                 | Pro uživatele typu Host, ID objektu uživatele v domovském tenantovi uživatele.| JWT        |           | |
| `sid`                      | ID relace, která se používá pro odhlášení uživatele na relaci. | JWT        |  Osobní účty a účty Azure AD.   |         |
| `platf`                    | Platforma zařízení    | JWT        |           | Omezeno na spravovaná zařízení, která můžou ověřit typ zařízení.|
| `verified_primary_email`   | Zdroj z PrimaryAuthoritativeEmail uživatele      | JWT        |           |         |
| `verified_secondary_email` | Zdroj z SecondaryAuthoritativeEmail uživatele   | JWT        |           |        |
| `enfpolids`                | Vynutila se ID zásad. Seznam ID zásad, které byly vyhodnoceny pro aktuálního uživatele. | JWT |  |  |
| `vnet`                     | Informace o specifikátoru virtuální sítě | JWT        |           |      |
| `fwd`                      | IP adresa.| JWT    |   | Přidá původní IPv4 adresu žádajícího klienta (Pokud se nachází uvnitř virtuální sítě). |
| `ctry`                     | Země uživatele | JWT |  | Služba Azure AD vrátí `ctry` volitelnou deklaraci identity, pokud je přítomná a hodnota deklarace je standardní kód země 2 – Letter, například FR, JP, SZ a tak dále. |
| `tenant_ctry`              | Země tenanta prostředku | JWT | | |
| `xms_pdl`          | Preferované umístění dat   | JWT | | V případě tenantů s více geografickými klienty je preferovaným umístěním dat kód se třemi písmeny ukazující geografickou oblast, ve které se uživatel nachází. Další informace najdete v dokumentaci k [Azure AD Connect o umístění preferovaného data](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Například: `APC` pro Asie a Tichomoří. |
| `xms_pl`                   | Preferovaný jazyk uživatele  | JWT ||Preferovaný jazyk uživatele, pokud je nastavený. Zdroj z jeho domovského tenanta ve scénářích přístupu hosta. Formát LL-CC ("en-US"). |
| `xms_tpl`                  | Preferovaný jazyk tenanta| JWT | | Preferovaný jazyk tenanta prostředků, pokud je nastavený. Naformátované vše ("en"). |
| `ztdid`                    | ID nasazení s nulovým dotykem | JWT | | Identita zařízení používaná pro [Windows autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Adresovatelné e-maily pro tohoto uživatele, pokud ho uživatel má.  | JWT, SAML | MSA, Azure AD | Tato hodnota je ve výchozím nastavení zahrnutá, pokud je uživatel hostem v tenantovi.  U spravovaných uživatelů (uživatelů uvnitř tenanta) musí být požadavek požadován prostřednictvím této volitelné deklarace identity nebo, pouze v 2.0, s oborem OpenID.  U spravovaných uživatelů musí být e-mailová adresa nastavena na [portálu pro správu Office](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Volitelné formátování pro deklarace skupin |JWT, SAML| |Používá se ve spojení s nastavením GroupMembershipClaims v [manifestu aplikace](reference-app-manifest.md), který musí být nastaven také. Podrobnosti najdete v tématu [deklarace skupin](#configuring-groups-optional-claims) níže. Další informace o deklaracích skupin najdete v tématu [Konfigurace deklarací identity skupin](../hybrid/how-to-connect-fed-group-claims.md) .
| `acct`             | Stav uživatelských účtů v tenantovi. | JWT, SAML | | Pokud je uživatel členem tenanta, hodnota je `0`. Pokud se jedná o hosta, hodnota `1`. |
| `upn`                      | Deklarace UserPrincipalName. | JWT, SAML  |           | I když je tato deklarace identity zahrnutá automaticky, můžete ji zadat jako volitelnou deklaraci identity pro připojení dalších vlastností, abyste mohli změnit její chování v případě uživatele typu Host.  |

## <a name="v20-specific-optional-claims-set"></a>sada volitelných deklarací identity v 2.0

Tyto deklarace jsou vždycky zahrnuté v tokenech Azure AD v 1.0, ale nejsou zahrnuté v tokenech v 2.0, pokud se nepožadují. Tyto deklarace se vztahují jenom na JWTs (ID tokenů a přístupové tokeny). 

**Tabulka 3: v 2.0 – jenom volitelné deklarace identity**

| JWT Claim     | Name (Název)                            | Popis                                | Poznámky |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP adresa                      | IP adresa, ze které se klient přihlásil.   |       |
| `onprem_sid`  | Místní identifikátor zabezpečení |                                             |       |
| `pwd_exp`     | Čas vypršení platnosti hesla        | Datum a čas, kdy platnost hesla vyprší |       |
| `pwd_url`     | Změnit adresu URL hesla             | Adresa URL, na kterou může uživatel přejít, aby změnila heslo.   |   |
| `in_corp`     | Uvnitř podnikové sítě        | Signalizuje, že se klient přihlašuje z podnikové sítě. Pokud nejsou, deklarace identity není zahrnutá.   |  Vychází z nastavení [důvěryhodných IP adres](../authentication/howto-mfa-mfasettings.md#trusted-ips) v MFA.    |
| `nickname`    | Přezdívka                        | Další jméno uživatele. Přezdívka je oddělená od prvního nebo posledního jména. | 
| `family_name` | Příjmení                       | Poskytuje příjmení, příjmení nebo rodinné jméno uživatele, jak je definováno v objektu User. <br>"family_name": "Miller" | Podporováno v MSA a Azure AD   |
| `given_name`  | Jméno                      | Poskytuje první nebo "předané" jméno uživatele, jak je nastaveno u objektu User.<br>"given_name": "Josef"                   | Podporováno v MSA a Azure AD  |
| `upn`         | Hlavní název uživatele | Identifikátorem pro uživatele, který lze použít s parametrem username_hint.  Nejedná se o trvalý identifikátor pro uživatele a neměl by se používat k klíčovým datům. | Konfiguraci deklarace identity najdete níže v části [Další vlastnosti](#additional-properties-of-optional-claims) . |

### <a name="additional-properties-of-optional-claims"></a>Další vlastnosti volitelných deklarací identity

Některé volitelné deklarace identity je možné nakonfigurovat tak, aby se změnil způsob, jakým se deklarace identity vrací. Tyto další vlastnosti se většinou používají k migraci místních aplikací s různými očekáváními dat (například `include_externally_authenticated_upn_without_hash` pomáhají s klienty, kteří nemůžou zpracovávat značky hash (`#`) v hlavní název uživatele (UPN).)

**Tabulka 4: hodnoty pro konfiguraci volitelných deklarací identity**

| Název vlastnosti  | Název další vlastnosti | Popis |
|----------------|--------------------------|-------------|
| `upn`          |                          | Lze použít pro odpovědi SAML i JWT i pro tokeny v 1.0 a v 2.0. |
|                | `include_externally_authenticated_upn`  | Zahrnuje hlavní název uživatele (UPN), který je uložený v tenantovi prostředků. Například `foo_hometenant.com#EXT#@resourcetenant.com`. |             
|                | `include_externally_authenticated_upn_without_hash` | Stejné jako výše, s tím rozdílem, že se značky hash (`#`) nahrazují podtržítkem (`_`), například `foo_hometenant.com_EXT_@resourcetenant.com` |

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

Tento objekt OptionalClaims způsobí, že token ID se vrátil klientovi, aby zahrnoval jiné hlavní název uživatele (UPN) s dalšími informacemi o domácím tenantovi a tenantovi prostředků. Deklarace `upn` se v tokenu změní jenom v případě, že je uživatel hostem v tenantovi (který používá pro ověřování jiný IDP). 

## <a name="configuring-optional-claims"></a>Konfigurace volitelných deklarací identity

> [!IMPORTANT]
> Přístupové tokeny jsou **vždy** generovány pomocí manifestu prostředku, nikoli klienta.  Takže v žádosti `...scope=https://graph.microsoft.com/user.read...` prostředek je graf.  Proto je přístupový token vytvořen pomocí manifestu grafu, nikoli manifestu klienta.  Změna manifestu aplikace nikdy nezpůsobí, že tokeny pro graf budou vypadat jinak.  Aby se ověřilo, že se změny `accessToken` projeví, požádejte o token pro vaši aplikaci, ne jinou aplikaci.  

Volitelné deklarace identity pro aplikaci můžete nakonfigurovat prostřednictvím uživatelského rozhraní nebo manifestu aplikace.

1. Přejděte na [portál Azure](https://portal.azure.com). Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**.
1. V seznamu vyberte aplikaci, pro kterou chcete nakonfigurovat volitelné deklarace identity.

**Konfigurace volitelných deklarací prostřednictvím uživatelského rozhraní:**

[![ukazuje, jak nakonfigurovat volitelné deklarace identity pomocí uživatelského rozhraní.](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. V části **Správa** vyberte **Konfigurace tokenu (Preview)** .
2. Vyberte **přidat volitelnou deklaraci identity**.
3. Vyberte typ tokenu, který chcete konfigurovat.
4. Vyberte volitelné deklarace, které se mají přidat.
5. Klikněte na tlačítko **Přidat**.

**Konfigurace volitelných deklarací pomocí manifestu aplikace:**

[![ukazuje, jak nakonfigurovat volitelné deklarace identity pomocí manifestu aplikace.](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. V části **Spravovat** vyberte možnost **manifest**. Otevře se webový editor manifestu, který umožňuje upravit manifest. Volitelně můžete vybrat **Stáhnout**, upravit manifest místně a potom ho **Nahrát** zpět do aplikace. Další informace o manifestu aplikace najdete v [článku Principy manifestu aplikace Azure AD](reference-app-manifest.md).

    Následující položka manifestu aplikace přidá volitelné deklarace identity auth_time, ipaddr a hlavní název uživatele (UPN) do ID, přístupu a tokenů SAML.

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

2. Jakmile budete hotoví, klikněte na **Uložit**. Nyní budou v tokenech vaší aplikace zahrnuty zadané volitelné deklarace identity.    


### <a name="optionalclaims-type"></a>Typ OptionalClaims

Deklaruje volitelné deklarace požadované aplikací. Aplikace může nakonfigurovat volitelné deklarace identity, které se budou vracet v každém ze tří typů tokenů (token ID, přístupový token, token SAML 2), který může přijímat ze služby tokenu zabezpečení. Aplikace může nakonfigurovat jinou sadu volitelných deklarací identity, které budou vráceny v každém typu tokenu. Vlastnost OptionalClaims entity Application je objekt OptionalClaims.

**Tabulka 5: vlastnosti OptionalClaims typu**

| Name (Název)        | Typ                       | Popis                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Kolekce (OptionalClaim) | Volitelné deklarace identity vrácené v tokenu JWT ID. |
| `accessToken` | Kolekce (OptionalClaim) | Volitelné deklarace identity vrácené v přístupovém tokenu JWT. |
| `saml2Token`  | Kolekce (OptionalClaim) | Volitelné deklarace identity vrácené v tokenu SAML.   |

### <a name="optionalclaim-type"></a>Typ OptionalClaim

Obsahuje volitelnou deklaraci přidruženou k aplikaci nebo instančnímu objektu. Vlastnosti idToken, accessToken a saml2Token typu [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) je kolekce OptionalClaim.
Pokud je tato možnost podporovaná konkrétní deklarací identity, můžete také změnit chování OptionalClaim pomocí pole AdditionalProperties.

**Tabulka 6: vlastnosti OptionalClaim typu**

| Name (Název)                 | Typ                    | Popis                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Název volitelné deklarace identity.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | Zdroj (objekt adresáře) deklarace identity. Z vlastností rozšíření jsou předdefinované deklarace identity a uživatelsky definované deklarace identity. Pokud má zdrojová hodnota hodnotu null, deklarace identity je předdefinovaná volitelná deklarace identity. Pokud je zdrojovou hodnotou uživatel, hodnota ve vlastnosti název je vlastnost rozšíření z objektu User. |
| `essential`            | Edm.Boolean             | Pokud je hodnota true, deklarace identity zadaná klientem je nutná k zajištění hladkého autorizačního prostředí pro konkrétní úkol požadovaný koncovým uživatelem. Výchozí hodnota je false.                                                                                                             |
| `additionalProperties` | Collection (EDM. String) | Další vlastnosti deklarace identity Pokud v této kolekci existuje vlastnost, upraví chování volitelné deklarace identity zadané ve vlastnosti název.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Konfigurace volitelných deklarací rozšíření adresáře

Kromě standardní volitelné sady deklarací identity můžete také nakonfigurovat tokeny, aby zahrnovaly rozšíření. Další informace najdete v tématu [Přidání vlastních dat do prostředků pomocí rozšíření](https://docs.microsoft.com/graph/extensibility-overview). Tato funkce je užitečná pro připojení dalších uživatelských informací, které vaše aplikace může používat – například Další identifikátor nebo důležitou možnost konfigurace, kterou uživatel nastavil. Příklad najdete v dolní části této stránky.

> [!NOTE]
> - Rozšíření schématu adresáře jsou funkcí pouze pro Azure AD, takže pokud manifest aplikace požaduje vlastní rozšíření a MSA uživatele do vaší aplikace, tato rozšíření nebudou vrácena.

### <a name="directory-extension-formatting"></a>Formátování rozšíření adresáře

Při konfiguraci volitelných deklarací rozšíření adresáře pomocí manifestu aplikace použijte úplný název rozšíření (ve formátu: `extension_<appid>_<attributename>`). `<appid>` musí odpovídat ID aplikace, která žádá o deklaraci identity. 

V rámci tokenu JWT budou tyto deklarace vygenerovány s následujícím formátem názvu: `extn.<attributename>`.

V rámci tokenů SAML budou tyto deklarace vygenerovány s následujícím formátem identifikátoru URI: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>Konfigurace volitelných deklarací skupin

   > [!NOTE]
   > Možnost generovat názvy skupin uživatelů a skupin synchronizovaných z místního prostředí je Public Preview.

Tato část se zabývá možnostmi konfigurace v části volitelné deklarace identity pro změnu atributů skupin používaných v deklaracích skupin z výchozí skupiny objectID na atributy synchronizované z místní služby Windows Active Directory. Můžete nakonfigurovat volitelné deklarace identity pro vaši aplikaci prostřednictvím uživatelského rozhraní nebo manifestu aplikace.

> [!IMPORTANT]
> Další podrobnosti včetně důležitých aspektů pro veřejnou verzi Preview deklarací identity skupin z místních atributů najdete v tématu [Konfigurace deklarací identity skupin pro aplikace s Azure AD](../hybrid/how-to-connect-fed-group-claims.md).

**Konfigurace volitelných deklarací skupin prostřednictvím uživatelského rozhraní:**
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Po ověření zvolte svého tenanta Azure AD tak, že ho vyberete v pravém horním rohu stránky.
1. V nabídce na levé straně vyberte **Azure Active Directory** .
1. V části **Spravovat** vyberte **Registrace aplikací**
1. Vyberte aplikaci, pro kterou chcete nakonfigurovat volitelné deklarace identity v seznamu.
1. V části **Spravovat** vyberte **Konfigurace tokenu (Preview)** .
2. Vyberte **přidat deklaraci identity skupin** .
3. Vyberte typy skupin, které se mají vrátit (**všechny skupiny, skupiny** **zabezpečení**nebo **DirectoryRole**). Možnost **všechny skupiny** zahrnuje skupinu **zabezpečení**, **DirectoryRole**a **DistributionList** .
4. Volitelné: kliknutím na vlastnosti konkrétního typu tokenu můžete změnit hodnotu deklarace identity skupin tak, aby obsahovala atributy místních skupin, nebo změnit typ deklarace na roli.
5. Klikněte na **Uložit**.

**Konfigurace volitelných deklarací skupin pomocí manifestu aplikace:**
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Po ověření zvolte svého tenanta Azure AD tak, že ho vyberete v pravém horním rohu stránky.
1. V nabídce na levé straně vyberte **Azure Active Directory** .
1. Vyberte aplikaci, pro kterou chcete nakonfigurovat volitelné deklarace identity v seznamu.
1. V části **Spravovat** vyberte **manifest** .
3. Přidejte následující položku pomocí editoru manifestu:

   Platné hodnoty jsou:

   - All (Tato možnost zahrnuje zabezpečení, DirectoryRole a DistributionList)
   - "Zabezpečení"
   - "DirectoryRole"

   Například:

    ```json
        "groupMembershipClaims": "SecurityGroup"
    ```

   Ve výchozím nastavení bude v hodnotě deklarace skupiny vygenerována hodnota ObjectID skupiny.  Pokud chcete změnit hodnotu deklarace identity tak, aby obsahovala atributy místních skupin, nebo změňte typ deklarace identity na role, použijte konfiguraci OptionalClaims následujícím způsobem:

3. Nastavte konfiguraci názvu skupiny volitelné deklarace identity.

   Pokud chcete, aby skupiny v tokenu obsahovaly atributy místní skupiny služby AD v části volitelné deklarace identity, určete, na který typ tokenu má být volitelná deklarace identity, název volitelné požadované deklarace identity a další požadované vlastnosti.  Seznam může obsahovat více typů tokenů:

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
   | **additionalProperties:** | Seznam dalších vlastností  Platné možnosti jsou "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   V additionalProperties se vyžadují jenom jedna z těchto "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Pokud je k dispozici více než jeden, použije se první a ostatní se ignorují.

   Některé aplikace vyžadují informace o skupině pro uživatele v deklaraci identity role.  Pokud chcete změnit typ deklarace identity na deklaraci skupiny na deklaraci identity role, přidejte do dalších vlastností emit_as_roles.  Hodnoty skupiny budou vygenerovány v deklaraci identity role.

   > [!NOTE]
   > Pokud se používá emit_as_roles, neobjeví se v deklaraci identity role žádné aplikační role nakonfigurované k přiřazení uživatele.

**Příklady:**

1) Generování skupin jako názvů skupin v přístupových tokenech OAuth ve formátu dnsDomainName\sAMAccountName

    
    **Konfigurace uživatelského rozhraní:**

    [![ukazuje, jak nakonfigurovat volitelné deklarace identity pomocí uživatelského rozhraní.](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **Položka manifestu aplikace:**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) Vygeneruje názvy skupin, které se vrátí ve formátu netbiosDomain\sAMAccountName jako deklarace identity rolí v tokenech SAML a OIDC ID.

    **Konfigurace uživatelského rozhraní:**

    [![ukazuje, jak nakonfigurovat volitelné deklarace identity pomocí uživatelského rozhraní.](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Položka manifestu aplikace:**
    
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
     

## <a name="optional-claims-example"></a>Příklad volitelných deklarací identity

V této části si můžete projít scénářem, abyste viděli, jak můžete použít funkci volitelné deklarace identity pro vaši aplikaci.
K dispozici je několik možností aktualizace vlastností konfigurace identity aplikace, aby bylo možné povolit a nakonfigurovat volitelné deklarace identity:
-    Můžete použít uživatelské rozhraní **Konfigurace tokenu (Preview)** (viz příklad níže).
-    Můžete použít **manifest** (viz příklad níže). Přečtěte si [dokument s manifestem aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) nejprve pro Úvod k manifestu.
-   Je také možné napsat aplikaci, která používá [Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) k aktualizaci aplikace. Typ [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) v referenční příručce Graph API vám pomůže nakonfigurovat volitelné deklarace identity.

**Příklad:** V následujícím příkladu použijete uživatelské rozhraní **Konfigurace tokenu (Preview)** a **manifest** , abyste přidali volitelné deklarace identity pro přístup, ID a tokeny SAML, které jsou určené pro vaši aplikaci. Do každého typu tokenu, který může aplikace získat, se přidají různé volitelné deklarace:
-    Tokeny ID teď budou obsahovat hlavní název uživatele (UPN) pro federované uživatele v úplném formátu (`<upn>_<homedomain>#EXT#@<resourcedomain>`).
-    Přístupové tokeny, které ostatní klienti požadují pro tuto aplikaci, teď budou zahrnovat auth_time deklarací identity.
-    Tokeny SAML teď budou obsahovat rozšíření schématu adresáře skypeId (v tomto příkladu je ID aplikace pro tuto aplikaci ab603c56068041afb2f6832e2a17e237). Tokeny SAML zveřejňují Skype ID jako `extension_skypeId`.

**Konfigurace uživatelského rozhraní:**

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

1. Po ověření zvolte svého tenanta Azure AD tak, že ho vyberete v pravém horním rohu stránky.

1. V nabídce na levé straně vyberte **Azure Active Directory** .

1. V části **Spravovat** vyberte **Registrace aplikací**.

1. V seznamu Najděte aplikaci, pro kterou chcete nakonfigurovat volitelné deklarace identity, a klikněte na ni.

1. V části **Spravovat** klikněte na **Konfigurace tokenu (Preview)** .

1. Vyberte **přidat volitelnou deklaraci identity**, vyberte typ tokenu **ID** , v seznamu deklarací identity vyberte **hlavní název uživatele (UPN)** a pak klikněte na **Přidat**.

1. Vyberte **přidat volitelnou deklaraci identity**, vyberte typ **přístupového** tokenu, v seznamu deklarací identity vyberte **Auth_time** a pak klikněte na **Přidat**.

1. Na obrazovce Přehled konfigurace tokenu klikněte na ikonu tužky vedle **názvu UPN**, klikněte na přepínač **externě ověřený** a pak klikněte na **Uložit**.

1. Vyberte **přidat volitelnou deklaraci identity**, vyberte typ tokenu **SAML** , v seznamu deklarací identity vyberte **EXTn. skypeID** (platí jenom v případě, že jste vytvořili objekt uživatele Azure AD s názvem SkypeID) a pak klikněte na **Přidat**.

    [![ukazuje, jak nakonfigurovat volitelné deklarace identity pomocí uživatelského rozhraní.](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Konfigurace manifestu:**
1. Přihlaste se k [Portálu Azure](https://portal.azure.com).
1. Po ověření zvolte svého tenanta Azure AD tak, že ho vyberete v pravém horním rohu stránky.
1. V nabídce na levé straně vyberte **Azure Active Directory** .
1. V seznamu Najděte aplikaci, pro kterou chcete nakonfigurovat volitelné deklarace identity, a klikněte na ni.
1. V části **Spravovat** klikněte na **manifest** a otevřete vložený editor manifestu.
1. Manifest můžete přímo upravit pomocí tohoto editoru. Manifest následuje po schématu pro [entitu aplikace](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)a po uložení automaticky zformátuje manifest. Do vlastnosti `OptionalClaims` budou přidány nové prvky.

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
