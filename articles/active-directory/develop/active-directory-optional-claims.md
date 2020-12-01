---
title: Poskytněte volitelné deklarace identity aplikacím Azure AD.
titleSuffix: Microsoft identity platform
description: Jak přidat vlastní nebo další deklarace k tokenům SAML 2,0 a JSON webtokens (JWT) vydaným platformou Microsoft Identity Platform.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/30/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 7eedb9ce30be236e8d47152f0e114b7bc5ae2304
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348088"
---
# <a name="how-to-provide-optional-claims-to-your-app"></a>Postupy: poskytnutí volitelných deklarací identity vaší aplikaci

Vývojáři aplikací můžou ve svých aplikacích Azure AD použít volitelné deklarace identity a určit, které deklarace identity se mají v tokenech odeslaných do jejich aplikace.

Volitelné deklarace identity můžete použít k těmto akcím:

- Vyberte další deklarace identity, které chcete zahrnout do tokenů pro vaši aplikaci.
- Změna chování určitých deklarací identity, které platforma Microsoft Identity Platform vrací v tokenech
- Přidejte a získejte přístup k vlastním deklaracím pro vaši aplikaci.

Seznam standardních deklarací identity najdete v dokumentaci [k tokenům](access-tokens.md) a [id_token](id-tokens.md) deklarací identity.

I když jsou volitelné deklarace identity podporované v tokenech formátu v 1.0 i v 2.0 i v tokenech SAML, poskytují většinu jejich hodnoty při přechodu z verze 1.0 do verze 2.0. Jedním z cílů [koncového bodu Microsoft Identity Platform v 2.0](./v2-overview.md) jsou menší velikosti tokenů, aby se zajistil optimální výkon pro klienty. V důsledku toho již v tokenech verze 2.0 nejsou k dispozici několik deklarací, které byly dříve zahrnuty v tokenech Access a ID, a je nutné, abyste byli požádáni o konkrétně na základě jednotlivých aplikací.

**Tabulka 1: použitelnost**

| Typ účtu               | tokeny v 1.0 | tokeny v 2.0 |
|----------------------------|-------------|-------------|
| Osobní účet Microsoft | –         | Podporováno   |
| Účet Azure AD           | Podporováno   | Podporováno   |

## <a name="v10-and-v20-optional-claims-set"></a>volitelná sada deklarací v 1.0 a v 2.0

Sada volitelných deklarací, které jsou ve výchozím nastavení k dispozici pro použití aplikací, jsou uvedeny níže. Pokud chcete přidat vlastní volitelné deklarace identity pro vaši aplikaci, přečtěte si část [rozšíření adresáře](#configuring-directory-extension-optional-claims)níže. Při přidávání deklarací do **přístupového tokenu** se deklarace vztahují na přístupové tokeny požadované *pro* aplikaci (webové rozhraní API), nikoli na deklarace identity *, které aplikace požaduje.* Bez ohledu na to, jak klient přistupuje k rozhraní API, se v přístupovém tokenu, který se používá k ověřování na rozhraní API, nachází správná data.

> [!NOTE]
> Většinu těchto deklarací lze zahrnout do JWTs pro tokeny v 1.0 a v 2.0, ale ne tokeny SAML, s výjimkou případů, kdy je uvedeno ve sloupci typ tokenu. Uživatelské účty podporují podmnožinu těchto deklarací, která je označena ve sloupci "typ uživatele".  Mnohé z uvedených deklarací se nevztahují na uživatele typu uživatel (nemají žádného tenanta `tenant_ctry` ).

**Tabulka 2: volitelná sada deklarací identity v 1.0 a v 2.0**

| Název                       |  Popis   | Typ tokenu | Typ uživatele | Poznámky  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Čas posledního ověření uživatele Viz specifikace OpenID Connect.| TOKEN        |           |  |
| `tenant_region_scope`      | Oblast tenanta prostředků | TOKEN        |           | |
| `sid`                      | ID relace, která se používá pro odhlášení uživatele na relaci. | TOKEN        |  Osobní účty a účty Azure AD.   |         |
| `verified_primary_email`   | Zdroj z PrimaryAuthoritativeEmail uživatele      | TOKEN        |           |         |
| `verified_secondary_email` | Zdroj z SecondaryAuthoritativeEmail uživatele   | TOKEN        |           |        |
| `vnet`                     | Informace o specifikátoru virtuální sítě | TOKEN        |           |      |
| `fwd`                      | IP adresa.| TOKEN    |   | Přidá původní IPv4 adresu žádajícího klienta (Pokud se nachází uvnitř virtuální sítě). |
| `ctry`                     | Země nebo oblast uživatele | JWT, SAML |  | Služba Azure AD vrátí `ctry` volitelnou deklaraci identity, pokud je přítomná a hodnota pole je standardní kód země/oblasti se dvěma písmeny, například FR, JP, SZ a tak dále. |
| `tenant_ctry`              | Země tenanta prostředku | TOKEN | | Stejné jako `ctry` s výjimkou nastavenou na úrovni tenanta správcem.  Musí být také standardní hodnota se dvěma písmeny. |
| `xms_pdl`             | Preferované umístění dat   | TOKEN | | V případě tenantů s více geografickými klienty je preferovaným umístěním dat kód se třemi písmeny ukazující geografickou oblast, ve které se uživatel nachází. Další informace najdete v dokumentaci k [Azure AD Connect o umístění preferovaného data](../hybrid/how-to-connect-sync-feature-preferreddatalocation.md).<br/>Například: `APC` pro Asie a Tichomoří. |
| `xms_pl`                   | Preferovaný jazyk uživatele  | TOKEN ||Preferovaný jazyk uživatele, pokud je nastavený. Zdroj z jeho domovského tenanta ve scénářích přístupu hosta. Formát LL-CC ("en-US"). |
| `xms_tpl`                  | Preferovaný jazyk tenanta| TOKEN | | Preferovaný jazyk tenanta prostředků, pokud je nastavený. Naformátované vše ("en"). |
| `ztdid`                    | ID nasazení s nulovým dotykem | TOKEN | | Identita zařízení používaná pro [Windows autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Adresovatelné e-maily pro tohoto uživatele, pokud ho uživatel má.  | JWT, SAML | MSA, Azure AD | Tato hodnota je ve výchozím nastavení zahrnutá, pokud je uživatel hostem v tenantovi.  U spravovaných uživatelů (uživatelů uvnitř tenanta) musí být požadavek požadován prostřednictvím této volitelné deklarace identity nebo, pouze v 2.0, s oborem OpenID.  U spravovaných uživatelů musí být e-mailová adresa nastavena na [portálu pro správu Office](https://portal.office.com/adminportal/home#/users).|
| `acct`                | Stav uživatelských účtů v tenantovi | JWT, SAML | | Pokud je uživatel členem tenanta, hodnota je `0` . Pokud se jedná o hosta, hodnota je `1` . |
| `groups`| Volitelné formátování pro deklarace skupin |JWT, SAML| |Používá se ve spojení s nastavením GroupMembershipClaims v [manifestu aplikace](reference-app-manifest.md), který musí být nastaven také. Podrobnosti najdete v tématu [deklarace skupin](#configuring-groups-optional-claims) níže. Další informace o deklaracích skupin najdete v tématu [Konfigurace deklarací identity skupin](../hybrid/how-to-connect-fed-group-claims.md) .
| `upn`                      | UserPrincipalName | JWT, SAML  |           | Identifikátorem pro uživatele, který lze použít s parametrem username_hint.  Nejedná se o trvalý identifikátor pro uživatele a neměl by se používat k jedinečným informacím identity uživatele (například jako klíč databáze). Místo toho použijte ID objektu uživatele ( `oid` ) jako klíč databáze. Uživatelům přihlašování pomocí [alternativního přihlašovacího ID](../authentication/howto-authentication-use-email-signin.md) by se neměl zobrazovat hlavní název uživatele (UPN). Místo toho použijte následující deklarace tokenu ID pro zobrazení stavu přihlášení uživateli: `preferred_username` nebo `unique_name` pro tokeny V1 a `preferred_username` pro tokeny v2. I když je tato deklarace identity zahrnutá automaticky, můžete ji zadat jako volitelnou deklaraci identity pro připojení dalších vlastností, abyste mohli změnit její chování v případě uživatele typu Host.  |
| `idtyp`                    | Typ tokenu   | Přístupové tokeny JWT | Speciální: jenom v tokenech přístupu jenom pro aplikace |  Hodnota je `app` v případě, že se jedná o token pouze pro aplikaci. Toto je nejpřesnější způsob, jak rozhraní API určit, jestli je token aplikace nebo token aplikace + uživatel.|

## <a name="v20-specific-optional-claims-set"></a>sada volitelných deklarací identity v 2.0

Tyto deklarace jsou vždycky zahrnuté v tokenech Azure AD v 1.0, ale nejsou zahrnuté v tokenech v 2.0, pokud se nepožadují. Tyto deklarace se vztahují jenom na JWTs (ID tokenů a přístupové tokeny).

**Tabulka 3: v 2.0 – jenom volitelné deklarace identity**

| Deklarace JWT     | Název                            | Popis                                | Poznámky |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP adresa                      | IP adresa, ze které se klient přihlásil.   |       |
| `onprem_sid`  | Místní identifikátor zabezpečení |                                             |       |
| `pwd_exp`     | Čas vypršení platnosti hesla        | Datum a čas, kdy platnost hesla vyprší |       |
| `pwd_url`     | Změnit adresu URL hesla             | Adresa URL, na kterou může uživatel přejít, aby změnila heslo.   |   |
| `in_corp`     | Uvnitř podnikové sítě        | Signalizuje, že se klient přihlašuje z podnikové sítě. Pokud nejsou, deklarace identity není zahrnutá.   |  Vychází z nastavení [důvěryhodných IP adres](../authentication/howto-mfa-mfasettings.md#trusted-ips) v MFA.    |
| `family_name` | Příjmení                       | Poskytuje příjmení, příjmení nebo rodinné jméno uživatele, jak je definováno v objektu User. <br>"family_name": "Miller" | Podporováno v MSA a Azure AD. Vyžaduje `profile` obor.   |
| `given_name`  | Jméno                      | Poskytuje první nebo "předané" jméno uživatele, jak je nastaveno u objektu User.<br>"given_name": "Josef"                   | Podporováno v MSA a Azure AD.  Vyžaduje `profile` obor. |
| `upn`         | Hlavní název uživatele | Identifikátorem pro uživatele, který lze použít s parametrem username_hint.  Nejedná se o trvalý identifikátor pro uživatele a neměl by se používat k jedinečným informacím identity uživatele (například jako klíč databáze). Místo toho použijte ID objektu uživatele ( `oid` ) jako klíč databáze. Uživatelům přihlašování pomocí [alternativního přihlašovacího ID](../authentication/howto-authentication-use-email-signin.md) by se neměl zobrazovat hlavní název uživatele (UPN). Místo toho použijte následující deklarace tokenu ID pro zobrazení stavu přihlášení uživateli: `preferred_username` nebo `unique_name` pro tokeny V1 a `preferred_username` pro tokeny v2. | Konfiguraci deklarace identity najdete níže v části [Další vlastnosti](#additional-properties-of-optional-claims) . Vyžaduje `profile` obor.|

### <a name="additional-properties-of-optional-claims"></a>Další vlastnosti volitelných deklarací identity

Některé volitelné deklarace identity je možné nakonfigurovat tak, aby se změnil způsob, jakým se deklarace identity vrací. Tyto další vlastnosti se většinou používají k migraci místních aplikací s různými očekáváními dat. Například `include_externally_authenticated_upn_without_hash` pomáhá s klienty, kteří nemůžou zpracovávat značky hash ( `#` ) v hlavní název uživatele (UPN).

**Tabulka 4: hodnoty pro konfiguraci volitelných deklarací identity**

| Název vlastnosti  | Název další vlastnosti | Popis |
|----------------|--------------------------|-------------|
| `upn`          |                          | Lze použít pro odpovědi SAML i JWT i pro tokeny v 1.0 a v 2.0. |
|                | `include_externally_authenticated_upn`  | Zahrnuje hlavní název uživatele (UPN), který je uložený v tenantovi prostředků. Například `foo_hometenant.com#EXT#@resourcetenant.com`. |
|                | `include_externally_authenticated_upn_without_hash` | Stejné jako výše, s tím rozdílem, že značky hash ( `#` ) jsou nahrazeny podtržítkem ( `_` ), například `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Příklad dalších vlastností

```json
"optionalClaims": {
    "idToken": [
        {
            "name": "upn",
            "essential": false,
            "additionalProperties": [
                "include_externally_authenticated_upn"
            ]
        }
    ]
}
```

Tento objekt OptionalClaims způsobí, že token ID se vrátil klientovi, aby zahrnoval `upn` deklaraci identity s dalšími informacemi o domácím tenantovi a tenantovi prostředků. `upn`Deklarace se v tokenu změní jenom v případě, že je uživatel hostem v tenantovi (který používá pro ověřování jiný IDP).

## <a name="configuring-optional-claims"></a>Konfigurace volitelných deklarací identity

> [!IMPORTANT]
> Přístupové tokeny jsou **vždy** generovány pomocí manifestu prostředku, nikoli klienta.  Takže v žádosti `...scope=https://graph.microsoft.com/user.read...` je prostředek Microsoft Graph rozhraní API.  Proto je přístupový token vytvořený pomocí manifestu Microsoft Graph API, nikoli manifestu klienta.  Změna manifestu aplikace nikdy nezpůsobí, že tokeny pro rozhraní API pro Microsoft Graph se budou lišit.  Aby bylo možné ověřit, že `accessToken` jsou vaše změny v platnosti, požádejte o token pro vaši aplikaci, ne jinou aplikaci.

Volitelné deklarace identity pro aplikaci můžete nakonfigurovat prostřednictvím uživatelského rozhraní nebo manifestu aplikace.

1. Přejděte na web [Azure Portal](https://portal.azure.com). Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**.
1. V seznamu vyberte aplikaci, pro kterou chcete nakonfigurovat volitelné deklarace identity.

**Konfigurace volitelných deklarací prostřednictvím uživatelského rozhraní:**

[![Konfigurace volitelných deklarací v uživatelském rozhraní](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. V části **Správa** vyberte **Konfigurace tokenu**.
1. Vyberte **přidat volitelnou deklaraci identity**.
1. Vyberte typ tokenu, který chcete konfigurovat.
1. Vyberte volitelné deklarace, které se mají přidat.
1. Vyberte **Přidat**.

> [!NOTE]
> Okno **Konfigurace tokenu** možností uživatelského rozhraní není k dispozici pro aplikace zaregistrované v klientovi Azure AD B2C. U aplikací zaregistrovaných v tenantovi B2C lze volitelné deklarace identity nakonfigurovat úpravou manifestu aplikace. Další informace najdete v tématu [Přidání deklarací identity a přizpůsobení uživatelského vstupu pomocí vlastních zásad v Azure Active Directory B2C](../../active-directory-b2c/custom-policy-configure-user-input.md) 

**Konfigurace volitelných deklarací pomocí manifestu aplikace:**

[![Ukazuje, jak nakonfigurovat volitelné deklarace identity pomocí manifestu aplikace.](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. V části **Spravovat** vyberte možnost **manifest**. Otevře se webový editor manifestu, který umožňuje upravit manifest. Volitelně můžete vybrat **Stáhnout**, upravit manifest místně a potom ho **Nahrát** zpět do aplikace. Další informace o manifestu aplikace najdete v [článku Principy manifestu aplikace Azure AD](reference-app-manifest.md).

    Následující položka manifestu aplikace přidá volitelné deklarace identity auth_time, ipaddr a hlavní název uživatele (UPN) do ID, přístupu a tokenů SAML.

    ```json
    "optionalClaims": {
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

2. Po dokončení vyberte **Uložit**. Nyní budou v tokenech vaší aplikace zahrnuty zadané volitelné deklarace identity.


### <a name="optionalclaims-type"></a>Typ OptionalClaims

Deklaruje volitelné deklarace požadované aplikací. Aplikace může nakonfigurovat volitelné deklarace identity, které se vrátí v každém ze tří typů tokenů (token ID, přístupový token, token SAML 2), které může přijímat ze služby tokenu zabezpečení. Aplikace může nakonfigurovat jinou sadu volitelných deklarací identity, které budou vráceny v každém typu tokenu. Vlastnost OptionalClaims entity Application je objekt OptionalClaims.

**Tabulka 5: vlastnosti OptionalClaims typu**

| Název          | Typ                       | Popis                                           |
|---------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Kolekce (OptionalClaim) | Volitelné deklarace identity vrácené v tokenu JWT ID.     |
| `accessToken` | Kolekce (OptionalClaim) | Volitelné deklarace identity vrácené v přístupovém tokenu JWT. |
| `saml2Token`  | Kolekce (OptionalClaim) | Volitelné deklarace identity vrácené v tokenu SAML.       |

### <a name="optionalclaim-type"></a>Typ OptionalClaim

Obsahuje volitelnou deklaraci přidruženou k aplikaci nebo instančnímu objektu. Vlastnosti idToken, accessToken a saml2Token typu [OptionalClaims](/graph/api/resources/optionalclaims) je kolekce OptionalClaim.
Pokud je tato možnost podporovaná konkrétní deklarací identity, můžete také změnit chování OptionalClaim pomocí pole AdditionalProperties.

**Tabulka 6: vlastnosti OptionalClaim typu**

| Název                   | Typ                    | Popis                                                                                                                                                                                                                                                                                                   |
|------------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Název volitelné deklarace identity.                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | Zdroj (objekt adresáře) deklarace identity. Z vlastností rozšíření jsou předdefinované deklarace identity a uživatelsky definované deklarace identity. Pokud má zdrojová hodnota hodnotu null, deklarace identity je předdefinovaná volitelná deklarace identity. Pokud je zdrojovou hodnotou uživatel, hodnota ve vlastnosti název je vlastnost rozšíření z objektu User. |
| `essential`            | Edm.Boolean             | Pokud je hodnota true, deklarace identity zadaná klientem je nutná k zajištění hladkého autorizačního prostředí pro konkrétní úkol požadovaný koncovým uživatelem. Výchozí hodnota je False.                                                                                                                 |
| `additionalProperties` | Collection (EDM. String) | Další vlastnosti deklarace identity Pokud v této kolekci existuje vlastnost, upraví chování volitelné deklarace identity zadané ve vlastnosti název.                                                                                                                                                   |

## <a name="configuring-directory-extension-optional-claims"></a>Konfigurace volitelných deklarací rozšíření adresáře

Kromě standardní volitelné sady deklarací identity můžete také nakonfigurovat tokeny, aby zahrnovaly rozšíření. Další informace najdete [v dokumentaci ke službě Microsoft Graph extensionProperty](/graph/api/resources/extensionproperty).

Rozšíření schématu a Open nejsou podporovaná v volitelných deklaracích, ale jenom v rozšířeních adresářů stylů AAD-Graph. Tato funkce je užitečná pro připojení dalších uživatelských informací, které vaše aplikace může používat – například Další identifikátor nebo důležitou možnost konfigurace, kterou uživatel nastavil. Příklad najdete v dolní části této stránky.

> [!NOTE]
> Rozšíření schématu adresáře jsou funkcí jenom pro Azure AD. Pokud váš manifest aplikace požaduje vlastní rozšíření a uživatel MSA se do vaší aplikace přihlásí, tato rozšíření nebudou vrácena.

### <a name="directory-extension-formatting"></a>Formátování rozšíření adresáře

Při konfiguraci volitelných deklarací rozšíření adresáře pomocí manifestu aplikace použijte úplný název rozšíření (ve formátu: `extension_<appid>_<attributename>` ). `<appid>`Musí odpovídat ID aplikace, která žádá o deklaraci identity.

V rámci tokenu JWT budou tyto deklarace vygenerovány s následujícím formátem názvu:  `extn.<attributename>` .

V rámci tokenů SAML budou tyto deklarace vygenerovány s následujícím formátem identifikátoru URI: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>Konfigurace volitelných deklarací skupin

   > [!NOTE]
   > Možnost generovat názvy skupin uživatelů a skupin synchronizovaných z místního prostředí je Public Preview.

Tato část se zabývá možnostmi konfigurace v části volitelné deklarace identity pro změnu atributů skupin používaných v deklaracích skupin z výchozí skupiny objectID na atributy synchronizované z místní služby Windows Active Directory. Můžete nakonfigurovat volitelné deklarace identity pro vaši aplikaci prostřednictvím uživatelského rozhraní nebo manifestu aplikace.

> [!IMPORTANT]
> Další podrobnosti včetně důležitých aspektů pro veřejnou verzi Preview deklarací identity skupin z místních atributů najdete v tématu [Konfigurace deklarací identity skupin pro aplikace s Azure AD](../hybrid/how-to-connect-fed-group-claims.md).

**Konfigurace volitelných deklarací skupin prostřednictvím uživatelského rozhraní:**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Po ověření zvolte svého tenanta Azure AD tak, že ho vyberete v pravém horním rohu stránky.
1. V nabídce na levé straně vyberte **Azure Active Directory** .
1. V části **Spravovat** vyberte **Registrace aplikací**.
1. V seznamu vyberte aplikaci, pro kterou chcete nakonfigurovat volitelné deklarace identity.
1. V části **Spravovat** vyberte **Konfigurace tokenu**.
1. Vyberte **přidat deklaraci identity skupin**.
1. Vyberte typy skupin, které se mají vrátit (**skupiny zabezpečení** nebo **role adresáře**, **všechny skupiny** a/nebo **skupiny přiřazené k aplikaci**). Možnost **skupiny přiřazené k aplikaci** zahrnuje jenom skupiny přiřazené k aplikaci. Možnost **všechny skupiny** zahrnuje skupinu **zabezpečení**, **DirectoryRole** a **DistributionList**, ale ne **skupiny přiřazené k aplikaci**. 
1. Volitelné: vyberte vlastnosti konkrétního typu tokenu, abyste mohli upravit hodnotu deklarace skupiny, aby obsahovala atributy místních skupin, nebo změňte typ deklarace identity na roli.
1. Vyberte **Uložit**.

**Konfigurace volitelných deklarací skupin pomocí manifestu aplikace:**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Po ověření zvolte svého tenanta Azure AD tak, že ho vyberete v pravém horním rohu stránky.
1. V nabídce na levé straně vyberte **Azure Active Directory** .
1. V seznamu vyberte aplikaci, pro kterou chcete nakonfigurovat volitelné deklarace identity.
1. V části **Spravovat** vyberte možnost **manifest**.
1. Přidejte následující položku pomocí editoru manifestu:

   Platné hodnoty jsou:

   - All (Tato možnost zahrnuje zabezpečení, DirectoryRole a DistributionList)
   - "Zabezpečení"
   - "DirectoryRole"
   - "Skupina aplikací" (Tato možnost zahrnuje jenom skupiny, které jsou přiřazené aplikaci)

   Příklad:

    ```json
    "groupMembershipClaims": "SecurityGroup"
    ```

   Ve výchozím nastavení bude v hodnotě deklarace skupiny vygenerována hodnota ObjectID skupiny.  Pokud chcete změnit hodnotu deklarace identity tak, aby obsahovala atributy místních skupin, nebo změňte typ deklarace identity na role, použijte konfiguraci OptionalClaims následujícím způsobem:

1. Nastavte konfiguraci názvu skupiny volitelné deklarace identity.

   Pokud chcete, aby skupiny v tokenu obsahovaly atributy místní skupiny služby AD v části volitelné deklarace identity, zadejte, na který typ tokenu má být volitelná deklarace identity, název volitelné požadované deklarace identity a další požadované vlastnosti.  Seznam může obsahovat více typů tokenů:

   - idToken pro token ID OIDC
   - accessToken pro přístupový token OAuth
   - Saml2Token pro tokeny SAML

   > [!NOTE]
   > Typ Saml2Token se vztahuje i na tokeny formátu SAML 1.1 i SAML 2.0.

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

   Některé aplikace vyžadují informace o skupině pro uživatele v deklaraci identity role.  Pokud chcete změnit typ deklarace identity ze skupiny na deklaraci identity role, přidejte do dalších vlastností emit_as_roles.  Hodnoty skupiny budou vygenerovány v deklaraci identity role.

   > [!NOTE]
   > Pokud se použije emit_as_roles, nebudou se v deklaraci identity role zobrazovat všechny role aplikace nakonfigurované k přiřazení uživatele.

**Příklady:**

1) Generování skupin jako názvů skupin v přístupových tokenech OAuth ve formátu dnsDomainName\sAMAccountName

    **Konfigurace uživatelského rozhraní:**

    [![Konfigurace volitelných deklarací identity](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)

    **Položka manifestu aplikace:**

    ```json
    "optionalClaims": {
        "accessToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "dns_domain_and_sam_account_name"
                ]
            }
        ]
    }
    ```

2) Vygeneruje názvy skupin, které se vrátí ve formátu netbiosDomain\sAMAccountName jako deklarace identity rolí v tokenech SAML a OIDC ID.

    **Konfigurace uživatelského rozhraní:**

    [![Volitelné deklarace identity v manifestu](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Položka manifestu aplikace:**

    ```json
    "optionalClaims": {
        "saml2Token": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ],
        "idToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ]
    }
    ```

## <a name="optional-claims-example"></a>Příklad volitelných deklarací identity

V této části si můžete projít scénářem, abyste viděli, jak můžete použít funkci volitelné deklarace identity pro vaši aplikaci.
K dispozici je několik možností aktualizace vlastností konfigurace identity aplikace, aby bylo možné povolit a nakonfigurovat volitelné deklarace identity:

- Můžete použít uživatelské rozhraní **Konfigurace tokenu** (viz příklad níže).
- Můžete použít **manifest** (viz příklad níže). Přečtěte si [dokument s manifestem aplikace Azure AD](./reference-app-manifest.md) nejprve pro Úvod k manifestu.
- Je také možné napsat aplikaci, která používá [rozhraní Microsoft Graph API](/graph/use-the-api) k aktualizaci aplikace. Typ [OptionalClaims](/graph/api/resources/optionalclaims) v referenční příručce rozhraní API pro Microsoft Graph vám může pomáhat s konfigurací volitelných deklarací identity.

**Příklad:**

V následujícím příkladu použijete uživatelské rozhraní **Konfigurace tokenu** a **manifest** k přidání volitelných deklarací identity pro přístup, ID a tokeny SAML, které jsou určené pro vaši aplikaci. Do každého typu tokenu, který může aplikace získat, se přidají různé volitelné deklarace:

- Tokeny ID teď budou obsahovat hlavní název uživatele (UPN) pro federované uživatele v úplném formátu ( `<upn>_<homedomain>#EXT#@<resourcedomain>` ).
- Přístupové tokeny, které ostatní klienti požadují pro tuto aplikaci, teď budou zahrnovat auth_time deklaraci identity.
- Tokeny SAML teď budou obsahovat rozšíření schématu adresáře skypeId (v tomto příkladu je ID aplikace pro tuto aplikaci ab603c56068041afb2f6832e2a17e237). Tokeny SAML vystaví Skype ID jako `extension_skypeId` .

**Konfigurace uživatelského rozhraní:**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Po ověření zvolte svého tenanta Azure AD tak, že ho vyberete v pravém horním rohu stránky.

1. V nabídce na levé straně vyberte **Azure Active Directory** .

1. V části **Spravovat** vyberte **Registrace aplikací**.

1. V seznamu Najděte aplikaci, pro kterou chcete nakonfigurovat volitelné deklarace identity, a vyberte ji.

1. V části **Spravovat** vyberte **Konfigurace tokenu**.

1. Vyberte **přidat volitelnou deklaraci identity**, vyberte typ tokenu **ID** , v seznamu deklarací identity vyberte **hlavní název uživatele (UPN)** a pak vyberte **Přidat**.

1. Vyberte **přidat volitelnou deklaraci identity**, vyberte typ **přístupového** tokenu, v seznamu deklarací identity vyberte **auth_time** a pak vyberte **Přidat**.

1. Na obrazovce Přehled konfigurace tokenu vyberte ikonu tužky vedle **hlavního názvu uživatele**, vyberte přepínač **externě ověřený** a pak vyberte **Uložit**.

1. Vyberte **přidat volitelnou deklaraci identity**, vyberte typ tokenu **SAML** , v seznamu deklarací identity vyberte **EXTn. skypeID** (platí jenom v případě, že jste vytvořili objekt uživatele Azure AD s názvem skypeID) a pak vyberte **Přidat**.

    [![Volitelné deklarace identity pro token SAML](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Konfigurace manifestu:**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Po ověření zvolte svého tenanta Azure AD tak, že ho vyberete v pravém horním rohu stránky.
1. V nabídce na levé straně vyberte **Azure Active Directory** .
1. V seznamu Najděte aplikaci, pro kterou chcete nakonfigurovat volitelné deklarace identity, a vyberte ji.
1. V části **Spravovat** vyberte **manifest** a otevřete tak vložený editor manifestu.
1. Manifest můžete přímo upravit pomocí tohoto editoru. Manifest následuje po schématu pro [entitu aplikace](./reference-app-manifest.md)a po uložení automaticky zformátuje manifest. Do vlastnosti budou přidány nové prvky `OptionalClaims` .

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "upn",
                "essential": false,
                "additionalProperties": [
                    "include_externally_authenticated_upn"
                ]
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
    }
    ```

1. Až skončíte s aktualizací manifestu, vyberte **Uložit** a uložte manifest.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o standardních deklaracích poskytovaných službou Azure AD.

- [Tokeny ID](id-tokens.md)
- [Přístupové tokeny](access-tokens.md)