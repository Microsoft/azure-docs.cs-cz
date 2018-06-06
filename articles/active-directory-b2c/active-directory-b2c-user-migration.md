---
title: Migrace uživatele blíží v Azure Active Directory B2C | Microsoft Docs
description: Informace o základní a rozšířené koncepty na migraci uživatele pomocí rozhraní Graph API a volitelně pomocí vlastních zásad Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7c78cb13f9028b2be527794751d5f8ced9bff171
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711337"
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: Migrace uživatelů
Když migrujete zprostředkovatele identity do Azure Active Directory B2C (Azure AD B2C), možná budete také muset migrovat uživatelský účet. Tento článek vysvětluje, jak migrovat existující uživatelské účty z kteréhokoli zprostředkovatele identity do Azure AD B2C. Článek by neměl být doporučený, ale místo toho popisuje několik scénářů. Vývojář je zodpovědná za vhodnosti obou těchto přístupů.

## <a name="user-migration-flows"></a>Toky migrace uživatele
S Azure AD B2C, můžete migrovat uživateli prostřednictvím [Azure AD Graph API][B2C-GraphQuickStart]. Proces migrace uživatele, které patří do dvou toky:

* **Před migrací**: Tento tok platí, pokud máte buď zrušte přístup k přihlašovacím údajům uživatele (uživatelské jméno a heslo) nebo přihlašovací údaje jsou šifrované, ale lze je dešifrovat. Proces před migrací zahrnuje čtení z původního zprostředkovatele identity uživatelů a vytváření nových účtů v adresáři Azure AD B2C.

* **Před migrací a heslo resetovat**: Tento tok platí, pokud heslo uživatele není dostupný. Příklad:
    * Je heslo uloženo ve formátu HASH.
    * Heslo je uloženo v zprostředkovatele identity, ke kterému nelze přistupovat. Původní zprostředkovatele identity ověří přihlašovací údaje uživatele při volání webové služby.

V obou toky je nejprve spustit proces před migrací, čtení uživatelů z původního zprostředkovatele identity a vytvořit nové účty v adresáři Azure AD B2C. Pokud nemáte heslo, vytvoříte účet pomocí náhodně generovaný heslo. Pak požádejte uživatele, chcete-li změnit heslo, nebo při prvním přihlášení uživatele, Azure AD B2C požádá uživatele, aby ho resetovat.

## <a name="password-policy"></a>Zásady hesel
Zásady hesel Azure AD B2C (pro lokální účty) je založena na zásady služby Azure AD. Azure AD B2C registrace nebo přihlášení a heslo resetovat zásady použití síly hesla "silné" a není vypršení platnosti hesla. Další informace najdete v tématu [zásady hesel služby Azure AD][AD-PasswordPolicies].

Pokud účty, které chcete migrovat pomocí slabší síly hesla, než [silné heslo sílu vynucováno Azure AD B2C][AD-PasswordPolicies], můžete zakázat požadavek na silné heslo. Chcete-li změnit výchozí zásady hesel, nastavte `passwordPolicies` vlastnost `DisableStrongPassword`. Požadavek na vytvoření uživatele můžete například upravit následujícím způsobem:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>Krok 1: Použití Azure AD Graph API pro migraci uživatelů
Vytváření účtu uživatele Azure AD B2C prostřednictvím rozhraní Graph API (heslo nebo s náhodné heslo). Tato část popisuje proces vytváření uživatelských účtů v adresáři Azure AD B2C pomocí rozhraní Graph API.

### <a name="step-11-register-your-application-in-your-tenant"></a>Krok 1.1: Registrace vaší aplikace ve vašem klientovi
Ke komunikaci s rozhraní Graph API, nejprve musí mít účet služby s oprávněními správce. Ve službě Azure AD je třeba zaregistrovat aplikaci a ověřování do služby Azure AD. Přihlašovací údaje aplikací jsou **ID aplikace** a **tajný klíč aplikace**. Aplikace funguje jako samostatně, nikoli jako uživatel, k volání rozhraní Graph API.

Nejprve zaregistrujte aplikaci migrace ve službě Azure AD. Pak vytvořte klíč pomocí aplikace (tajný klíč aplikace) a nastavte aplikaci s oprávnění k zápisu.

1. Přihlaste se na web [Azure Portal][Portal].

2. Zvolte služby Azure AD **B2C** klienta tak, že vyberete svůj účet v horní pravé části okna.

3. V levém podokně vyberte **Azure Active Directory** (ne Azure AD B2C). Pokud chcete ji najít, možná budete muset vybrat možnost **více služeb**.

4. Vyberte **Registrace aplikací**.

5. Vyberte **Registrace nové aplikace**.

    ![Registrace nové aplikace](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)

6. Vytvoření nové aplikace následujícím způsobem:
    * Pro **název**, použijte **B2CUserMigration** nebo jakýkoli jiný název, který chcete.
    * Pro **typ aplikace**, použijte **webové aplikace nebo rozhraní API**.
    * Pro **přihlašovací adresa URL**, použijte **https://localhost** (protože není relevantní pro tuto aplikaci).
    * Vyberte **Vytvořit**.

7. Po vytvoření aplikace, v **aplikace** seznam, vyberte nově vytvořenou **B2CUserMigration** aplikace.

8. Vyberte **vlastnosti**, kopie **ID aplikace**a uložit pro pozdější použití.

### <a name="step-12-create-the-application-secret"></a>Krok 1.2: Vytvoření tajný klíč aplikace
1. Na portálu Azure **zaregistrovat aplikaci** vyberte **klíče**.

2. Přidejte nový klíč (také označované jako tajný klíč klienta) a zkopírujte klíč pro pozdější použití.

    ![ID aplikace a klíče](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)

### <a name="step-13-grant-administrative-permission-to-your-application"></a>Krok 1.3: Udělení oprávnění pro správu do vaší aplikace
1. Na portálu Azure **zaregistrovat aplikaci** vyberte **požadovaná oprávnění**.

2. Vyberte **Windows Azure Active Directory**.

3. V **povolit přístup** podokně v části **oprávnění aplikací**, vyberte **pro čtení a zápis dat adresáře**a potom vyberte **Uložit**.

4. V **požadovaná oprávnění** podokně, vyberte **udělit oprávnění**.

    ![Oprávnění aplikací](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)

Nyní máte aplikace s oprávněními k vytváření, čtení a aktualizaci uživatele z vašeho klienta Azure AD B2C.

### <a name="step-14-optional-environment-cleanup"></a>Krok 1.4: Vyčištění prostředí (volitelné)
Čtení a zápis dat oprávnění directory *není* zahrnovat právo odstranit uživatele. Chcete-li poskytují aplikace možnost odstranit uživatele (za účelem vyčištění prostředí), musíte provést další krok, který zahrnuje prostředí PowerShell a nastavit oprávnění pro správce účtu uživatele. Jinak můžete přeskočit k další části.

> [!IMPORTANT]
> Musíte použít účet správce klienta B2C, který je *místní* klienta B2C. Syntaxe názvu účtu není *admin@contosob2c.onmicrosoft.com*.

>[!NOTE]
> Následující skript prostředí PowerShell vyžaduje [Azure Active Directory PowerShell verze 2][AD-Powershell].

V tento skript prostředí PowerShell postupujte takto:
1. Připojení k online službě. Chcete-li tak učinit, spusťte `Connect-AzureAD` rutiny v prostředí Windows PowerShell, příkazový řádek a zadejte svoje přihlašovací údaje.

2. Použití **ID aplikace** přiřadit role správce účtu uživatele aplikace. Tyto role mají dobře známé identifikátory, tak, aby všechny musíte udělat zadejte vaše **ID aplikace** ve skriptu.

```PowerShell
Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

Změna `$AppId` hodnotu s Azure AD **ID aplikace**.

## <a name="step-2-pre-migration-application-sample"></a>Krok 2: Ukázka aplikace před migrací
[Stáhnout a spustit ukázkový kód][UserMigrationSample]. Můžete ho stáhnout jako soubor ZIP.

### <a name="step-21-edit-the-migration-data-file"></a>Krok 2.1: Upravit soubor dat migrace
Ukázková aplikace používá soubor JSON, který obsahuje data fiktivní uživatele. Po vás ukázku úspěšně spustíte můžete změnit kód, který využívají data z vlastní databáze. Nebo můžete exportovat profil uživatele do souboru JSON a poté nastavte aplikaci používat tento soubor.

Chcete-li upravit soubor JSON, otevřete `AADB2C.UserMigration.sln` řešení sady Visual Studio. V `AADB2C.UserMigration` projekt, otevřete `UsersData.json` souboru.

![Soubor dat uživatele](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Jak vidíte, soubor obsahuje seznam entitami uživatelů. Každá entita uživatel má následující vlastnosti:
* e-mail
* displayName
* FirstName
* Příjmení
* heslo (nesmí být prázdné)

> [!NOTE]
> Při kompilaci, Visual Studio zkopíruje soubor do `bin` adresáře.

### <a name="step-22-configure-the-application-settings"></a>Krok 2.2: Konfigurace nastavení aplikace
V části `AADB2C.UserMigration` projekt, otevřete *App.config* souboru. Následující nastavení aplikace nahraďte vlastními hodnotami:

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> * Použití služby Azure table připojovací řetězec je popsané v následujících částech.
> * Název vašeho klienta B2C je doména, kterou jste zadali při vytváření klienta a ten se zobrazí na portálu Azure. Název klienta většinou končí příponou *. onmicrosoft.com* (například *contosob2c.onmicrosoft.com*).
>

### <a name="step-23-run-the-pre-migration-process"></a>Krok 2.3: Spusťte proces před migrací
Klikněte pravým tlačítkem myši `AADB2C.UserMigration` řešení a pak znovu sestavit ukázku. Pokud jste úspěšné, teď byste měli mít `UserMigration.exe` spustitelný soubor umístěný ve `AADB2C.UserMigration\bin\Debug\net461`. Pokud chcete spustit proces migrace, použijte jednu z následujících parametrů příkazového řádku:

* K **migrovat uživatele s heslem**, použijte `UserMigration.exe 1` příkaz.

* K **migraci uživatelů s náhodné heslo**, použijte `UserMigration.exe 2` příkaz. Tato operace vytvoří také entitu tabulky Azure. Později nakonfigurujte zásady tak, aby volání rozhraní REST API služby. Služba Azure table používá ke sledování a správě procesu migrace.

![Ukázkový proces migrace](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Krok 2.4: Kontrola proces před migrací
Ověření migrace, použijte jednu z následujících dvou metod:

* Chcete-li vyhledat uživatele podle zobrazovaného názvu, použijte portál Azure:

    a. Otevřete **Azure AD B2C**a potom vyberte **uživatelů a skupin**.

    b. Do vyhledávacího pole zadejte zobrazovaný název uživatele a zobrazte profil uživatele.

* Chcete-li načíst uživatele tak, že přihlášení e-mailovou adresu, použijte této ukázkové aplikaci:

    a. Spusťte následující příkaz:

    ```Console
        UserMigration.exe 3 {email address}
    ```

    > [!TIP]
    > Můžete také načíst uživatele podle zobrazovaného názvu pomocí následujícího příkazu: `UserMigration.exe 4 "<Display name>"`.

    b. Otevřete soubor UserProfile.json v editoru JSON zobrazíte informace o uživateli.

    ![Soubor UserProfile.json](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>Krok 2.5: Vyčištění prostředí (volitelné)
Pokud chcete vyčistit až klientovi Azure AD a odebrat uživatele z adresáře služby Azure AD, spusťte `UserMigration.exe 5` příkaz.

> [!NOTE]
> * Vyčistěte váš klient, nakonfigurujte oprávnění správce účtu uživatele pro vaši aplikaci.
> * Ukázková aplikace migrace vyčistí všichni uživatelé, kteří jsou uvedeny v souboru JSON.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Krok 2.6: Přihlaste se pomocí migrovanými uživateli (s heslem)
Po spuštění proces před migrací s hesly uživatelské účty jsou připravené k použití a moct uživatel přihlásit do vaší aplikace pomocí Azure AD B2C. Pokud nemáte přístup k uživatelská hesla, pokračujte v další části.

## <a name="step-3-help-users-reset-their-password"></a>Krok 3: Pomoc uživatelům resetovat heslo
Pokud provádíte migraci uživatelů s náhodné heslo, se musí obnovit své heslo. Aby mohl snadněji resetování hesla, odeslání Uvítacího e-mailu s odkazem k resetování hesla.

Chcete-li získat odkaz na vaše zásady resetování hesel, postupujte takto:

1. Vyberte **nastavení Azure AD B2C**a potom vyberte **resetovat heslo** vlastnosti zásad.

2. Vyberte svou aplikaci.

    > [!NOTE]
    > Spustit nyní vyžaduje alespoň jedné aplikace do být preregistered u klienta. Další postup registrace aplikace najdete v tématu Azure AD B2C [Začínáme] [ B2C-GetStarted] článek nebo [registrace aplikace] [ B2C-AppRegister] článku.

3. Vyberte **spustit nyní**a potom zkontrolujte zásady.

4. V **spustit nyní koncový bod** pole, zkopírujte adresu URL a potom ji odešlete uživatelům.

    ![Sada protokolů diagnostiky](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Krok 4: (Volitelné) změnu vaše zásady ke kontrole a nastavení migrace stavu uživatele

> [!NOTE]
> Chcete-li zkontrolovat a změnit migrace stavu uživatele, musíte použít vlastní zásadu. Pokyny k instalaci z [začít pracovat s vlastními zásadami] [ B2C-GetStartedCustom] musí být vyplněna.
>

Když uživatelé se pokusí přihlásit se bez nejprve resetuje se heslo, vaše zásady by měl vrátit popisný chybová zpráva. Příklad:
>*Vašeho hesla vypršela. Aby to udělal, vyberte odkaz resetovat heslo.*

Tento volitelný krok vyžaduje použití vlastních zásad Azure AD B2C, jak je popsáno v [Začínáme s vlastními zásadami] [ B2C-GetStartedCustom] článku.

V této části změnit zásady tak, aby zkontrolovat stav migrace uživatelů na přihlášení. Pokud uživatel nebyl změnit heslo, vrátí chybovou zprávu protokolu HTTP 409 uživateli výzvu k výběru **zapomněli jste heslo?** odkaz.

Chcete-li sledovat Změna hesla, použijte Azure table. Když spustíte proces před migrací s parametrem příkazového řádku `2`, vytvořit entitu uživatele v Azure tabulce. Služby provede následující akce:

* Na přihlášení vyvolá zásad Azure AD B2C migrace služba RESTful, odesílání e-mailovou zprávu jako vstupní deklarace identity. Služba hledá e-mailovou adresu v tabulce Azure. Pokud existuje daná adresa služby vyvolá chybovou zprávu: *je nutné změnit heslo*.

* Jakmile uživatel úspěšně změní heslo, odeberte z tabulky Azure entity.

>[!NOTE]
>Pro zjednodušení ukázce používáme Azure table. V některé z databází nebo jako vlastní vlastnost v účtu Azure AD B2C můžete uložit stav migrace.

### <a name="41-update-your-application-setting"></a>4.1: aktualizace nastavení vaší aplikace
1. Chcete-li otestovat ukázkové rozhraní RESTful API, otevřete `AADB2C.UserMigration.sln` v sadě Visual Studio.

2. V `AADB2C.UserMigration.API` projekt, otevřete *appSettings.JSON určený* souboru. Nahraďte konfigurovaných v nastavení [krok 2.2](#step-22-configure-the-application-settings):

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Krok 4.2: Nasazení webové aplikace do služby Azure App Service
V Průzkumníku řešení klikněte pravým tlačítkem na `AADB2C.UserMigration.API`, vyberte "Publikování...". Postupujte podle pokynů k publikování do služby Azure App Service. Další informace najdete v tématu [nasazení vaší aplikace do Azure App Service][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Krok 4.3: Přidejte technické profilu a technické profil ověření do vaší zásady
1. V Průzkumníku řešení rozbalte "Řešení položky" a otevřete *TrustFrameworkExtensions.xml* soubor zásad.
2. Změna `TenantId`, `PublicPolicyUri` a `<TenantId>` pole z `yourtenant.onmicrosoft.com` na název vašeho klienta.
3. V části `<TechnicalProfile Id="login-NonInteractive">` elementu, nahraďte všechny výskyty `ProxyIdentityExperienceFrameworkAppId` a `IdentityExperienceFrameworkAppId` ID aplikace nakonfigurované v [Začínáme s vlastními zásadami][B2C-GetStartedCustom].
4. V části `<ClaimsProviders>` uzlu najít následující fragment kódu XML. Změňte hodnotu `ServiceUrl` tak, aby odkazoval na adresu URL aplikace služby Azure.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="LocalAccountSignIn">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>

        <TechnicalProfile Id="LocalAccountPasswordReset">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

Předchozí technické profil definuje jeden vstupní deklaraci identity: `signInName` (Odeslat jako e-mailu). Na přihlášení deklarace identity posílá RESTful koncový bod.

Po definování technické profil pro vaše rozhraní RESTful API, řekněte vaše zásady Azure AD B2C volat technické profilu. Fragment kódu XML přepsání `SelfAsserted-LocalAccountSignin-Email`, která je definována v základní zásady. Také přidá fragment kódu XML `ValidationTechnicalProfile`, s ReferenceId odkazující na váš profil technické `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Krok 4.4: Nahrajte zásady klienta
1. V [portál Azure][Portal], přepnout [kontextu klienta služby Azure AD B2C][B2C-NavContext]a potom vyberte **Azure AD B2C**.

2. Vyberte **Identity rozhraní Framework**.

3. Vyberte **všechny zásady**.

4. Vyberte **nahrát zásady**.

5. Vyberte **přepsat zásady, pokud existuje** zaškrtávací políčko.

6. Nahrát *TrustFrameworkExtensions.xml* souboru a ujistěte se, že předává ověření.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Krok 4.5: Testování spustit pomocí vlastních zásad
1. Vyberte **nastavení Azure AD B2C**a pak přejděte na **Identity rozhraní Framework**.

2. Otevřete **B2C_1A_signup_signin**, předávající stranu vlastních zásad, které jste nahráli a potom vyberte **spustit nyní**.

3. Pokuste se přihlaste jedním z migrovaných uživatelské přihlašovací údaje a potom vyberte **přihlásit**. Rozhraní API REST vyvoláním se následující chybová zpráva:

    ![Sada protokolů diagnostiky](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Krok 4.6: (Volitelné) řešení REST API
Můžete zobrazit a sledování informací o protokolování v skoro v reálném čase.

1. V nabídce nastavení RESTful aplikace v části **monitorování**, vyberte **diagnostické protokoly**.

2. Nastavit **protokolování aplikací (systém souborů)** k **na**.

3. Nastavte **úroveň** k **podrobné**.

4. Vyberte **uložit**

    ![Sada protokolů diagnostiky](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

5. Na **nastavení** nabídce vyberte možnost **datový proud protokolu**.

6. Zkontrolujte výstup rozhraní RESTful API.

Další informace najdete v tématu [streamování protokoly a konzole][AppService-Log].

> [!IMPORTANT]
> Diagnostické protokoly můžete použijte pouze během vývoje a testování. Výstup rozhraní RESTful API může obsahovat důvěrné informace, které by neměly být vystaveny v produkčním prostředí.
>

## <a name="optional-download-the-complete-policy-files"></a>(Volitelné) Stáhnout soubory dokončení zásad
Po dokončení [začít pracovat s vlastními zásadami] [ B2C-GetStartedCustom] návod, doporučujeme vám vytvořit váš scénář pomocí vlastních zásad pro soubory. Pro vaši informaci uvádíme [ukázkové soubory zásad][UserMigrationSample].

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[AppService-Log]: https://docs.microsoft.com/azure/active-directory-b2c/app-service-web/web-sites-streaming-logs-and-console
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration