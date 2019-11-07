---
title: Způsoby migrace uživatelů v Azure Active Directory B2C
description: Popisuje základní a pokročilé koncepty migrace uživatelů pomocí Graph API Azure AD a volitelně také pomocí Azure AD B2C vlastních zásad.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c8e4027bd8892ff3bf5c598573b7736aea42953f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73602576"
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: Migrace uživatelů

Když migrujete poskytovatele identity na Azure Active Directory B2C (Azure AD B2C), budete možná muset taky migrovat uživatelské účty. Tento článek vysvětluje, jak migrovat existující uživatelské účty z libovolného poskytovatele identity na Azure AD B2C. Tento článek není určen k tomu, aby byl doporučen, ale je zde uveden několik scénářů. Vývojář je zodpovědný za vhodnost jednotlivých přístupů.

## <a name="user-migration-flows"></a>Toky migrace uživatelů

Pomocí Azure AD B2C můžete migrovat uživatele prostřednictvím [Graph API Azure AD][B2C-GraphQuickStart]. Proces migrace uživatelů spadá do dvou toků:

- **Před migrací**: Tento tok se používá, pokud máte buď jasný přístup k přihlašovacím údajům uživatele (uživatelské jméno a heslo), nebo jsou přihlašovací údaje šifrované, ale můžete je dešifrovat. Proces před migrací zahrnuje čtení uživatelů ze starého zprostředkovatele identity a vytváření nových účtů v adresáři Azure AD B2C.

- **Před migrací a resetováním hesla**: Tento tok se použije, když heslo uživatele není dostupné. Příklad:
  - Heslo je uloženo ve formátu HASH.
  - Heslo je uloženo ve zprostředkovateli identity, ke kterému nemůžete získat přístup. Váš starý poskytovatel identity ověřuje přihlašovací údaje uživatele voláním webové služby.

V obou tocích nejprve spustíte proces před migrací, přečtěte si uživatele ze starého zprostředkovatele identity a vytvořte nové účty v adresáři Azure AD B2C. Pokud heslo nemáte, vytvoříte účet pomocí hesla, které se vygenerovalo náhodně. Pak požádáte uživatele, aby změnil heslo, nebo když se uživatel poprvé přihlásí, Azure AD B2C požádá uživatele o jeho resetování.

## <a name="password-policy"></a>Zásady hesel

Zásady hesla Azure AD B2C (pro místní účty) jsou založené na zásadách Azure AD. Zásady pro registraci nebo přihlášení Azure AD B2C a resetování hesla využívají silné síly hesla a nevyprší žádná hesla. Další informace najdete v tématu [zásady hesel Azure AD][AD-PasswordPolicies].

Pokud účty, které chcete migrovat, používají slabší sílu hesla než silné síly, kterou [vynutila Azure AD B2C][AD-PasswordPolicies], můžete zakázat požadavek na silný heslo. Chcete-li změnit výchozí zásady hesla, nastavte vlastnost `passwordPolicies` na hodnotu `DisableStrongPassword`. Požadavek na vytvoření uživatele můžete například upravit následujícím způsobem:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>Krok 1: použití Graph API Azure AD k migraci uživatelů

Uživatelský účet Azure AD B2C vytvoříte pomocí Graph API (s heslem nebo s náhodným heslem). Tato část popisuje proces vytváření uživatelských účtů v adresáři Azure AD B2C pomocí Graph API.

### <a name="step-11-register-your-application-in-your-tenant"></a>Krok 1,1: registrace aplikace ve vašem tenantovi

Abyste mohli komunikovat s Graph API, musíte mít účet služby s oprávněními správce. V Azure AD zaregistrujete aplikaci a povolíte přístup pro zápis do adresáře. Přihlašovací údaje aplikace jsou **ID aplikace** a **tajný kód aplikace**. Aplikace funguje samostatně, nikoli jako uživatel, pro volání Graph API.

Nejdřív Zaregistrujte aplikaci, kterou můžete použít pro úlohy správy, jako je migrace uživatelů.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="step-12-grant-administrative-permission-to-your-application"></a>Krok 1,2: udělení oprávnění správce vaší aplikaci

V dalším kroku udělte aplikaci potřebná oprávnění Graph API služby Azure AD pro zápis do adresáře.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="step-13-create-the-application-secret"></a>Krok 1,3: vytvoření tajného klíče aplikace

Vytvořte tajný klíč klienta (klíč), který bude používat aplikace pro migraci uživatelů, kterou nakonfigurujete v pozdějším kroku.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Nyní máte aplikaci s oprávněními k vytváření, čtení a aktualizaci uživatelů ve vašem tenantovi Azure AD B2C.

### <a name="step-14-optional-environment-cleanup"></a>Krok 1,4: (volitelné) vyčištění prostředí

Oprávnění ke *čtení a zápisu dat adresáře* neobsahuje *práva* k odstraňování uživatelů. Aby mohla vaše aplikace odstraňovat uživatele (pro vyčištění prostředí), musíte provést další krok, který zahrnuje spuštění PowerShellu k nastavení oprávnění správce účtu uživatele. V opačném případě můžete přejít k další části.

> [!IMPORTANT]
> Musíte použít účet správce klienta B2C, který je *místní* pro tenanta B2C. Syntaxe názvu účtu je *admin\@contosob2c.onmicrosoft.com*.

V tomto skriptu PowerShellu, který vyžaduje [modul Azure AD PowerShell V2][AD-Powershell], udělejte toto:

1. Připojte se k online službě. Uděláte to tak, že na příkazovém řádku Windows PowerShellu spustíte rutinu `Connect-AzureAD` a zadáte svoje přihlašovací údaje.

1. Pomocí **ID aplikace** přiřaďte aplikaci roli správce uživatelských účtů. Tyto role mají dobře známé identifikátory, takže stačí do skriptu zadat **ID vaší aplikace** .

```powershell
# NOTE: This script REQUIRES the Azure AD PowerShell V2 module
#       https://docs.microsoft.com/powershell/azure/active-directory/install-adv2

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

Změňte hodnotu `$AppId` pomocí **ID aplikace**služby Azure AD.

## <a name="step-2-pre-migration-application-sample"></a>Krok 2: Ukázka předběžné migrace aplikace

Ukázku kódu před migrací najdete v úložišti GitHubu udržovaného komunitou `azure-ad-b2c/user-migration`:

[Azure-AD-B2C/User-Migration/pre-Migration][UserMigrationSample-code] (GitHub)

### <a name="step-21-edit-the-migration-data-file"></a>Krok 2,1: Úprava souboru dat migrace

Ukázková aplikace používá soubor JSON, který obsahuje fiktivní uživatelská data. Po úspěšném spuštění ukázky můžete změnit kód tak, aby se data spouštěla z vaší vlastní databáze. Můžete také exportovat profil uživatele do souboru JSON a pak nastavit aplikaci tak, aby používala tento soubor.

Chcete-li upravit soubor JSON, otevřete `AADB2C.UserMigration.sln` řešení sady Visual Studio. V projektu `AADB2C.UserMigration` otevřete `UsersData.json` soubor.

![Část souboru UsersData. JSON zobrazující bloky JSON dvou uživatelů](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Jak vidíte, soubor obsahuje seznam entit uživatele. Každá entita uživatele má následující vlastnosti:

- e-mail
- displayName
- firstName
- Polím
- heslo (může být prázdné)

> [!NOTE]
> V době kompilace Visual Studio zkopíruje soubor do adresáře `bin`.

### <a name="step-22-configure-the-application-settings"></a>Krok 2,2: Konfigurace nastavení aplikace

V projektu `AADB2C.UserMigration` otevřete soubor *App. config* . V následujících nastaveních aplikace nahraďte vlastními hodnotami:

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
> - Použití připojovacího řetězce tabulky Azure je popsáno v dalších částech.
> - Název tenanta B2C je doména, kterou jste zadali během vytváření tenanta, a zobrazí se v Azure Portal. Název tenanta obvykle končí příponou *. onmicrosoft.com* (například *contosob2c.onmicrosoft.com*).

### <a name="step-23-run-the-pre-migration-process"></a>Krok 2,3: spuštění procesu před migrací

Klikněte pravým tlačítkem na řešení `AADB2C.UserMigration` a potom znovu sestavte ukázku. Pokud máte úspěšné, měli byste mít teď `UserMigration.exe` spustitelný soubor umístěný v `AADB2C.UserMigration\bin\Debug\net461`. Chcete-li spustit proces migrace, použijte některý z následujících parametrů příkazového řádku:

- Chcete-li **migrovat uživatele s heslem**, použijte příkaz `UserMigration.exe 1`.

- Chcete-li **migrovat uživatele s náhodným heslem**, použijte příkaz `UserMigration.exe 2`. Tato operace také vytvoří entitu tabulka Azure. Později nakonfigurujete zásady pro volání služby REST API. Služba používá ke sledování a správě procesu migrace tabulku Azure.

![Okno příkazového řádku zobrazující výstup příkazu UserMigration. exe](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Krok 2,4: zkontrolování procesu před migrací

K ověření migrace použijte jednu z následujících dvou metod:

- Pokud chcete vyhledat uživatele podle zobrazovaného jména, použijte Azure Portal:

   1. Otevřete **Azure AD B2C**a pak vyberte **Uživatelé**.
   1. Do vyhledávacího pole zadejte zobrazované jméno uživatele a pak zobrazte profil uživatele.

- Pokud chcete načíst uživatele podle přihlašovací e-mailové adresy, použijte ukázkovou aplikaci:

   1. Spusťte následující příkaz:

      ```Console
          UserMigration.exe 3 {email address} > UserProfile.json
      ```

      > [!TIP]
      > Uživatele můžete také načíst pomocí zobrazovaného názvu pomocí následujícího příkazu: `UserMigration.exe 4 "<Display name>"`.

   1. Otevřete soubor UserProfile. JSON v editoru JSON a podívejte se na informace o uživateli.

      ![Soubor UserProfile. JSON otevřený v editoru Visual Studio Code](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>Krok 2,5: (volitelné) vyčištění prostředí

Pokud chcete vyčistit tenanta Azure AD a odebrat uživatele z adresáře Azure AD, spusťte příkaz `UserMigration.exe 5`.

> [!NOTE]
> * Pokud chcete vyčistit svého tenanta, nakonfigurujte pro svoji aplikaci oprávnění správce účtu uživatele.
> * Ukázková aplikace pro migraci vyčistí všechny uživatele, kteří jsou uvedeni v souboru JSON.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Krok 2,6: Přihlaste se pomocí migrovaných uživatelů (s heslem)

Po spuštění procesu před migrací s uživatelskými hesly jsou účty připravené k použití a uživatelé se můžou k aplikaci přihlásit pomocí Azure AD B2C. Pokud nemáte přístup k uživatelským heslům, pokračujte k další části.

## <a name="step-3-help-users-reset-their-password"></a>Krok 3: usnadnění resetování hesla uživateli

Pokud uživatele migrujete s náhodným heslem, musí heslo resetovat. Pokud jim chcete při resetování hesla pomáhat, pošlete uvítací e-mail s odkazem na resetování hesla.

Pokud chcete získat odkaz na zásady pro resetování hesla, postupujte podle těchto kroků. Tento postup předpokládá, že jste dříve vytvořili [vlastní zásadu](active-directory-b2c-get-started-custom.md)pro resetování hesla.

1. V pravém horním rohu [Azure Portal](https://portal.azure.com)vyberte adresář, který obsahuje klienta Azure AD B2C pomocí filtru **Directory + Subscription** .
1. V nabídce vlevo vyberte **Azure AD B2C** (nebo ve **všech službách**).
1. V části **zásady**vyberte **Architektura prostředí identity**.
1. Vyberte zásady pro resetování hesla. Například *B2C_1A_PasswordReset*.
1. V rozevíracím seznamu **Vybrat aplikaci** vyberte svou aplikaci.

    > [!NOTE]
    > **Spustit teď** vyžaduje, aby se ve vašem tenantovi zaregistrovala aspoň jedna aplikace. Informace o tom, jak zaregistrovat aplikace, najdete v tématu [kurz: registrace aplikace v Azure Active Directory B2C][B2C-AppRegister].

1. Zkopírujte adresu URL zobrazenou v textovém poli **Spustit nyní** a odešlete ji uživatelům.

    ![Stránka zásad resetování hesel s zvýrazněným koncovým bodem spustit](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Krok 4: (volitelné) změňte zásady tak, aby kontrolovaly a nastavily stav migrace uživatele.

> [!NOTE]
> Chcete-li zjistit a změnit stav migrace uživatele, je nutné použít vlastní zásadu. Pokyny pro nastavení z části Začínáme [s vlastními zásadami][B2C-GetStartedCustom] se musí dokončit.

Když se uživatel pokusí přihlásit, aniž by nejdřív resetoval heslo, měla by zásada vracet popisnou chybovou zprávu. Příklad:

> *Platnost vašeho hesla vypršela. Pokud ho chcete resetovat, vyberte odkaz pro resetování hesla.*

Tento volitelný krok vyžaduje použití Azure AD B2C vlastních zásad, jak je popsáno v článku [Začínáme s vlastními zásadami][B2C-GetStartedCustom] .

V této části změníte zásadu tak, aby zkontrolovala stav migrace uživatelů při přihlášení. Pokud uživatel nezměnil heslo, zobrazí se chybová zpráva HTTP 409 s výzvou, aby uživatel vybrali odkaz **zapomenuté heslo?** .

Chcete-li sledovat změnu hesla, použijte tabulku Azure. Když spustíte proces před migrací s parametrem příkazového řádku `2`, vytvoříte entitu uživatele v tabulce Azure. Vaše služba provede následující akce:

- Při přihlášení vyvolá zásada Azure AD B2C službu RESTful migrace a odešle e-mailovou zprávu jako vstupní deklaraci identity. Služba vyhledá e-mailovou adresu v tabulce Azure. Pokud adresa existuje, služba vyvolá chybovou zprávu: *musíte změnit heslo*.

- Po úspěšném provedení změny hesla uživatelem odeberte entitu z tabulky Azure.

> [!NOTE]
> K zjednodušení ukázky používáme tabulku Azure. Stav migrace můžete uložit do libovolné databáze nebo jako vlastní vlastnost v účtu Azure AD B2C.

### <a name="41-update-your-application-setting"></a>4,1: aktualizujte nastavení aplikace

1. K otestování ukázky rozhraní RESTful API otevřete `AADB2C.UserMigration.sln` v aplikaci Visual Studio.
1. V projektu `AADB2C.UserMigration.API` otevřete soubor *Web. config* . Nahraďte nastavení parametrem nakonfigurovaným v [kroku 2,2](#step-22-configure-the-application-settings):

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Krok 4,2: nasazení webové aplikace do Azure App Service

V Průzkumník řešení klikněte pravým tlačítkem myši na `AADB2C.UserMigration.API`a vyberte publikovat.... Při publikování na Azure App Service postupujte podle pokynů. Další informace najdete v tématu [nasazení aplikace do Azure App Service][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Krok 4,3: přidejte do své zásady technický profil a ověření technického profilu.

1. V Průzkumník řešení rozbalte položku položky řešení a otevřete soubor zásad *TrustFrameworkExtensions. XML* .
1. Změňte `TenantId``PublicPolicyUri` a `<TenantId>` pole z `yourtenant.onmicrosoft.com` na název vašeho tenanta.
1. V rámci elementu `<TechnicalProfile Id="login-NonInteractive">` nahraďte všechny instance `ProxyIdentityExperienceFrameworkAppId` a `IdentityExperienceFrameworkAppId` identifikátory aplikací nakonfigurovaných v části [Začínáme s vlastními zásadami][B2C-GetStartedCustom].
1. Pod uzlem `<ClaimsProviders>` vyhledejte následující fragment kódu XML. Změňte hodnotu `ServiceUrl` tak, aby odkazovala na adresu URL Azure App Service.

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

Předchozí technický profil definuje jednu vstupní deklaraci identity: `signInName` (Odeslat jako e-mail). Při přihlášení se tato deklarace posílá do vašeho koncového bodu RESTful.

Po definování technického profilu pro rozhraní API RESTful řekněte zásadám Azure AD B2C, aby volaly technický profil. Fragment kódu XML Přepisuje `SelfAsserted-LocalAccountSignin-Email`, který je definován v základních zásadách. Fragment kódu XML také přidá `ValidationTechnicalProfile`, přičemž ReferenceId odkazuje na váš technický profil `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Krok 4,4: nahrání zásady do tenanta

1. V [Azure Portal][Portal]přepněte do [kontextu vašeho tenanta Azure AD B2C][B2C-NavContext]a pak vyberte **Azure AD B2C**.
1. Vyberte **architekturu prostředí identity**.
1. Vyberte **všechny zásady**.
1. Vyberte **Odeslat zásadu**.
1. Zaškrtněte políčko **přepsat zásadu, pokud existuje** .
1. Nahrajte soubor *TrustFrameworkExtensions. XML* a ujistěte se, že projde ověřením.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Krok 4,5: Vyzkoušejte si vlastní zásady pomocí rutiny spustit hned

1. Vyberte **Azure AD B2C**a pak vyberte **Architektura prostředí identity**.
1. Otevřete *B2C_1A_signup_signin*, vlastní zásady předávající strany (RP), které jste nahráli, a pak vyberte **Spustit nyní**.
1. Zadejte přihlašovací údaje jednoho ze migrovaných uživatelů a pak vyberte **Přihlásit**se. Váš REST API by měl vyvolat následující chybovou zprávu:

    ![Přihlašovací stránka pro přihlášení ukazující chybovou zprávu změnit heslo](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Krok 4,6: (volitelné) řešení potíží s REST API

Informace o protokolování můžete zobrazit a monitorovat téměř v reálném čase.

1. V nabídce nastavení vaší aplikace RESTful v části **monitorování**vyberte **diagnostické protokoly**.
1. Nastavte **protokolování aplikace (systém souborů)** na **zapnuto**.
1. Nastavte **úroveň** na **verbose**.
1. Vyberte **Uložit**.

    ![Stránka Konfigurace diagnostických protokolů v Azure Portal](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

1. V nabídce **Nastavení** vyberte **log Stream**.
1. Podívejte se na výstup rozhraní RESTful API.

> [!IMPORTANT]
> Diagnostické protokoly používejte pouze během vývoje a testování. Výstup rozhraní API RESTful může obsahovat důvěrné informace, které by neměly být vystaveny v produkčním prostředí.

## <a name="optional-download-the-complete-policy-files"></a>Volitelné Stažení úplných souborů zásad

Po dokončení průvodce Začínáme [s vlastními zásadami][B2C-GetStartedCustom] doporučujeme sestavit svůj scénář pomocí vlastních souborů zásad. Pro váš odkaz jsme zadali [ukázkové soubory zásad][UserMigrationSample-policy].

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample-code]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/source-code
[UserMigrationSample-policy]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/policy
