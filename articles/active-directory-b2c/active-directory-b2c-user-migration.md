---
title: Migrace uživatelských přístupů v Azure Active Directory B2C | Dokumentace Microsoftu
description: Informace o základních a pokročilých konceptů na migraci uživatelů pomocí rozhraní Graph API a případně můžete použít vlastní zásady Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 4d4affa0ff950aa353e11c01f3d5d5b5f2b2ccb1
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54849443"
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: Migrace uživatelů
Při migraci vašeho zprostředkovatele identity Azure Active Directory B2C (Azure AD B2C), budete pravděpodobně potřebovat k migraci uživatelský účet. Tento článek vysvětluje, jak migrovat existující uživatelské účty z libovolného poskytovatele identit do Azure AD B2C. Článek neměl být doporučený, ale místo toho popisuje několik scénářů. Vývojář je zodpovědná za vhodnost obou těchto přístupů.

## <a name="user-migration-flows"></a>Toky uživatelů pro migraci
S Azure AD B2C, můžete migrovat uživatele prostřednictvím [Azure AD Graph API][B2C-GraphQuickStart]. Proces migrace uživatel spadá do dvou toky:

- **Před migrací**: Tento tok použije při buď musíte vymazat přístup k přihlašovacím údajům uživatele (uživatelské jméno a heslo) nebo přihlašovací údaje jsou šifrované, ale lze je dešifrovat. Proces před migrací zahrnuje čtení uživatelů od předchozího poskytovatele identity a vytváření nových účtů v adresáři Azure AD B2C.

- **Před migrací a resetováním hesla**: Tento tok použije při heslo uživatele není dostupný. Příklad:
   - Heslo je uloženo ve formátu HASH.
   - Heslo je uloženo v zprostředkovatele identity, který nejde získat přístup. Vaše staré zprostředkovatele identity ověří přihlašovací údaje uživatele voláním webové služby.

V obou toků nejprve spustíte proces před migrací, čtení uživatelů ze staré zprostředkovatele identity a vytváření nových účtů v adresáři Azure AD B2C. Pokud nemáte heslo, vytvoříte účet pomocí hesla, které se vygeneruje náhodně. Poté požádat uživatele, chcete-li změnit heslo, nebo při prvním přihlášení uživatele, Azure AD B2C žádá uživatele, aby v něm obnovit.

## <a name="password-policy"></a>Zásady hesel
Zásady hesel Azure AD B2C (pro místní účty), je založen na zásady služby Azure AD. Azure AD B2C, registrace / přihlášení a hesla resetovat zásady použití síly hesla "silné" a platnost pasu nevyprší všechna hesla. Další informace najdete v tématu [zásady hesel služby Azure AD][AD-PasswordPolicies].

Pokud jsou účty, které chcete migrovat pomocí slabší síly hesla, než [sílu silné heslo, které vynucuje Azure AD B2C][AD-PasswordPolicies], můžete zakázat požadavek na silné heslo. Chcete-li změnit výchozí zásady pro hesla, nastavte `passwordPolicies` vlastnost `DisableStrongPassword`. Požadavek na vytvoření uživatele můžete například upravit následujícím způsobem:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>Krok 1: Migrace uživatelů pomocí Azure AD Graph API
Vytvoříte uživatelský účet Azure AD B2C prostřednictvím rozhraní Graph API (s heslem nebo se náhodné heslo). Tato část popisuje postup vytváření uživatelských účtů v adresáři Azure AD B2C s využitím rozhraní Graph API.

### <a name="step-11-register-your-application-in-your-tenant"></a>Krok 1.1: Registrace vaší aplikace ve vašem tenantovi
Ke komunikaci s rozhraním Graph API, nejprve musíte mít účet služby s oprávněními správce. Ve službě Azure AD registrace aplikace a ověřování do služby Azure AD. Přihlašovací údaje aplikací jsou **ID aplikace** a **tajný klíč aplikace**. Aplikace slouží jako samostatně, nikoli jako uživatel, pro volání rozhraní Graph API.

Migrace aplikace nejprve zaregistrujte ve službě Azure AD. Potom vytvořte klíč aplikace (tajný klíč aplikace) a nastavení aplikace s oprávněními pro zápis.

1. Přihlaste se na web [Azure Portal][Portal].
   
1. Zvolte vaši službu Azure AD **B2C** klienta tak, že vyberete svůj účet v horní části napravo od okna.
   
1. V levém podokně vyberte **Azure Active Directory** (ne Azure AD B2C). Pokud chcete ji najít, může být nutné vybrat **další služby**.
   
1. Vyberte **Registrace aplikací**.
   
1. Vyberte **Registrace nové aplikace**.
   
   ![Registrace nové aplikace](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)
   
1. Vytvoření nové aplikace následujícím způsobem:
   - Pro **název**, použijte **B2CUserMigration** nebo žádným jiným názvem, který chcete.
   - Pro **typ aplikace**, použijte **webové aplikace nebo rozhraní API**.
   - Pro **přihlašovací adresa URL**, použijte **https://localhost** (protože není relevantní pro tuto aplikaci).
   - Vyberte **Vytvořit**.
   
1. Po vytvoření aplikace, v **aplikací** vyberte nově vytvořený **B2CUserMigration** aplikace.
   
1. Vyberte **vlastnosti**, kopie **ID aplikace**a uložit pro pozdější použití.

### <a name="step-12-create-the-application-secret"></a>Krok 1.2: Vytvořit tajný klíč aplikace
1. Na webu Azure Portal **registrované aplikace** okně **klíče**.
   
1. Přidejte nový klíč (označované také jako tajný klíč klienta) a zkopírujte klíč pro pozdější použití.
   
   ![ID aplikace a klíčů](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)
   
### <a name="step-13-grant-administrative-permission-to-your-application"></a>Krok 1.3: Udělení oprávnění správce pro vaši aplikaci
1. Na webu Azure Portal **registrované aplikace** okně **požadovaná oprávnění**.

1. Vyberte **Windows Azure Active Directory**.
   
1. V **povolit přístup z** podokně v části **oprávnění aplikace**vyberte **pro čtení a zápis dat adresáře**a pak vyberte **Uložit**.
   
1. V **požadovaná oprávnění** vyberte **udělit oprávnění**.
   
   ![Oprávnění aplikace](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)
   
Teď máte aplikaci s oprávněním vytvářet, číst a aktualizovat uživatele z vašeho tenanta Azure AD B2C.

### <a name="step-14-optional-environment-cleanup"></a>Krok 1.4: (Volitelné) Vyčištění prostředí
Čtení a zápis adresář dat oprávnění provést *není* zahrnovat právo odstranit uživatele. K vaší aplikaci poskytuje možnost odstranit uživatele (Chcete-li vyčistit prostředí), je nutné provést další krok, který zahrnuje spuštění prostředí PowerShell pro nastavení oprávnění správce uživatelských účtů. Jinak můžete přeskočit k další části.

> [!IMPORTANT]
> Je nutné použít účet správce tenanta B2C, který je *místní* do tenanta B2C. Syntaxe názvu účtu se *admin@contosob2c.onmicrosoft.com*.

>[!NOTE]
> Následující skript prostředí PowerShell vyžaduje [Azure Active Directory PowerShell verze 2][AD-Powershell].

V tomto skriptu prostředí PowerShell postupujte takto:
1. Připojení ke službě online. Chcete-li tak učinit, spusťte `Connect-AzureAD` rutiny v prostředí Windows PowerShell, příkazový řádek a zadejte svoje přihlašovací údaje.
   
1. Použití **ID aplikace** přiřazení role správce účtu uživatele aplikace. Tyto role mají dobře známé identifikátory, takže je potřeba zadat vaše **ID aplikace** ve skriptu.
   
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

Změnit `$AppId` hodnotu službou Azure AD **ID aplikace**.

## <a name="step-2-pre-migration-application-sample"></a>Krok 2: Ukázkové aplikace před migrací
[Stažení a spuštění vzorového kódu][UserMigrationSample]. Můžete ho stáhnout jako soubor ZIP.

### <a name="step-21-edit-the-migration-data-file"></a>Krok 2.1: Upravit soubor dat migrace
Ukázková aplikace používá soubor JSON, který obsahuje data fiktivního uživatele. Poté co ukázku úspěšně spustíte můžete změnit kód, který využívají data z vlastní databázi. Nebo můžete exportovat profil uživatele do souboru JSON a pak nastavit aplikaci, aby používala tento soubor.

Chcete-li upravit soubor JSON, otevřete `AADB2C.UserMigration.sln` řešení sady Visual Studio. V `AADB2C.UserMigration` projekt, otevřete `UsersData.json` souboru.

![Soubor dat uživatele](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Jak je vidět, soubor obsahuje seznam entit uživatelů. Každá entita uživatele má následující vlastnosti:
- e-mail
- displayName
- Jméno
- Příjmení
- heslo (může být prázdné)

> [!NOTE]
> V době kompilace, Visual Studio zkopíruje soubor `bin` adresáře.

### <a name="step-22-configure-the-application-settings"></a>Krok 2.2: Konfigurace nastavení aplikace
V části `AADB2C.UserMigration` projekt, otevřete *App.config* souboru. Nahraďte následující nastavení aplikace s vlastními hodnotami:

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
> - Použití připojovacího řetězce služby Azure table je popsána v následujících částech.
> - Název tenanta B2C je doména, který jste zadali při vytváření klienta, a zobrazí se na webu Azure Portal. Název tenanta většinou končí příponou *. onmicrosoft.com* (například *contosob2c.onmicrosoft.com*).

### <a name="step-23-run-the-pre-migration-process"></a>Krok 2.3: Spustit proces před migrací
Klikněte pravým tlačítkem myši `AADB2C.UserMigration` řešení a pak znovu sestavit ukázku. Pokud budete úspěšní, teď byste měli mít `UserMigration.exe` spustitelný soubor umístěn v `AADB2C.UserMigration\bin\Debug\net461`. Pokud chcete spustit proces migrace, použijte jednu z následujících parametrů příkazového řádku:

- K **migrovat uživatele s heslem**, použijte `UserMigration.exe 1` příkazu.

- K **migrace uživatelů s náhodné heslo**, použijte `UserMigration.exe 2` příkazu. Tato operace vytvoří také entitu tabulky Azure. Později nakonfigurujete zásady tak, aby volání rozhraní REST API služby. Služba Azure table používá ke sledování a správě procesu migrace.

![Ukázka procesu migrace](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Krok 2.4: Zkontrolujte proces před migrací
Pokud chcete ověřit migraci, použijte jednu z těchto dvou metod:

- Chcete-li vyhledat uživatele podle zobrazovaného názvu, použijte na webu Azure portal:
   
   1. Otevřít **Azure AD B2C**a pak vyberte **uživatelů a skupin**.
   
   1. Do vyhledávacího pole zadejte zobrazované jméno a pak zobrazit profil uživatele.
   
- Pokud chcete načíst uživatele, že přihlašovací e-mailovou adresu, použijte této ukázkové aplikaci:
   
   1. Spusťte následující příkaz:
   
      ```Console
          UserMigration.exe 3 {email address}
      ```
      
      > [!TIP]
      > Uživatele můžete také načíst podle zobrazovaného názvu pomocí následujícího příkazu: `UserMigration.exe 4 "<Display name>"`.
      
   1. Otevřete soubor UserProfile.json v editoru JSON, který chcete zobrazit informace o uživateli.
   
      ![Soubor UserProfile.json](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)
      
### <a name="step-25-optional-environment-cleanup"></a>Krok 2.5: (Volitelné) Vyčištění prostředí
Pokud chcete vyčistit až vašeho tenanta Azure AD a odebrat uživatele z adresáře Azure AD, spusťte `UserMigration.exe 5` příkazu.

> [!NOTE]
> * Vyčistit vašeho tenanta, nakonfigurujte oprávnění správce uživatelských účtů pro vaši aplikaci.
> * Ukázková aplikace migrace vyčistí všechny uživatele, kteří jsou uvedeny v souboru JSON.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Krok 2.6: Přihlaste se pomocí migrovanými uživateli (s heslem)
Po spuštění proces před migrací s hesly uživatelské účty jsou připravené k použití a uživatelé můžou přihlásit do vaší aplikace pomocí Azure AD B2C. Pokud nemáte přístup k hesla uživatelů, pokračujte k další části.

## <a name="step-3-help-users-reset-their-password"></a>Krok 3: Pomoc uživatelům resetovat jejich hesla
Pokud provádíte migraci uživatelů s náhodné heslo, se musí resetovat své heslo. Aby věděli, resetovat heslo, odeslání Uvítacího e-mailu s odkazem k resetování hesla.

Pokud chcete získat odkaz na zásady pro resetování hesla, postupujte takto:

1. Vyberte **nastavení Azure AD B2C**a pak vyberte **resetovat heslo** vlastnosti zásad.

1. Vyberte svou aplikaci.

    > [!NOTE]
    > Spustit nyní vyžaduje aspoň jednu aplikaci do být registrované u klienta. Informace o postupu registrace aplikací, najdete v tématu Azure AD B2C [Začínáme] [ B2C-GetStarted] článku nebo [registrace aplikace] [ B2C-AppRegister] článku.

1. Vyberte **spustit nyní**a potom zkontrolujte zásady.

1. V **koncový bod pro okamžité spuštění** pole, zkopírujte adresu URL a potom ji odešlete uživatelům.

    ![Nastavení diagnostické protokoly](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Krok 4: (Volitelné) Změnit zásady ke kontrole a nastavení migrace stavu uživatele

> [!NOTE]
> Chcete-li zkontrolovat a změnit migrace stavu uživatele, je nutné použít vlastní zásady. Pokyny k instalaci z [začít pracovat s vlastními zásadami] [ B2C-GetStartedCustom] musí dokončit.
>

Když uživatelé se pokusí přihlásit bez resetování hesla poprvé, vaše zásady by měl vrátit popisná chybová zpráva. Příklad:
>*Platnost hesla vypršela. Aby to udělal, vyberte odkaz pro resetování hesla.*

Tento krok vyžaduje použití vlastních zásad Azure AD B2C, jak je popsáno v [Začínáme s vlastními zásadami] [ B2C-GetStartedCustom] článku.

V této části můžete změnit zásady ke kontrole stavu migrace uživatelů na přihlášení. Pokud uživatel nezměnili heslo, vrátí chybovou zprávu protokolu HTTP 409, který žádá uživatele, vyberte **zapomněli jste heslo?** odkaz.

Pokud chcete sledovat změnu hesla, použijete tabulku Azure. Když spustíte proces před migrací s parametrem příkazového řádku `2`, můžete vytvořit entitu uživatele v tabulce Azure. Vaše služba provede následující akce:

- Zásady Azure AD B2C na přihlášení, vyvolá migrace služba RESTful, odesílání e-mailovou zprávu jako vstupní deklarace identity. Služba hledá e-mailovou adresu v tabulce Azure. Pokud existuje adresu služby vyvolá chybovou zprávu: *Je nutné změnit heslo*.

- Poté, co uživatel úspěšně změní heslo, odeberte entity z tabulky Azure.

>[!NOTE]
>Pro zjednodušení ukázce používáme tabulku Azure. Stav migrace můžete uložit všechny databáze nebo jako vlastní vlastnost v účtu Azure AD B2C.

### <a name="41-update-your-application-setting"></a>4.1: Aktualizovat nastavení aplikace
1. Chcete-li otestovat ukázka rozhraní RESTful API, otevřete `AADB2C.UserMigration.sln` v sadě Visual Studio.

1. V `AADB2C.UserMigration.API` projekt, otevřete *appsettings.json* souboru. Nahraďte konfigurovaných v nastavení [krok 2.2](#step-22-configure-the-application-settings):

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Krok 4.2: Nasazení vaší webové aplikace do služby Azure App Service
V Průzkumníku řešení klikněte pravým tlačítkem myši na `AADB2C.UserMigration.API`, vyberte možnost "Publikovat...". Postupujte podle pokynů k publikování do služby Azure App Service. Další informace najdete v tématu [nasazení aplikace do služby Azure App Service][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Krok 4.3: Přidání technický profil a technický profil ověření zásad
1. V Průzkumníku řešení rozbalte "Položky řešení" a otevřete *TrustFrameworkExtensions.xml* soubor zásad.
1. Změna `TenantId`, `PublicPolicyUri` a `<TenantId>` pole z `yourtenant.onmicrosoft.com` na název vašeho tenanta.
1. V části `<TechnicalProfile Id="login-NonInteractive">` elementu, nahraďte všechny výskyty `ProxyIdentityExperienceFrameworkAppId` a `IdentityExperienceFrameworkAppId` s ID aplikace nakonfigurované v [Začínáme s vlastními zásadami][B2C-GetStartedCustom].
1. V části `<ClaimsProviders>` uzlu najít následující fragment kódu XML. Změňte hodnotu vlastnosti `ServiceUrl` přejděte na adresu URL aplikace služby Azure.

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

Předchozí technický profil definuje jednu vstupní deklaraci identity: `signInName` (Odeslat jako e-mailu). Přihlášení deklarace identity je odeslána koncovému bodu RESTful.

Po definování technický profil pro rozhraní RESTful API, řekněte zásady Azure AD B2C k volání technický profil. Fragment kódu XML přepíše `SelfAsserted-LocalAccountSignin-Email`, který je definován v základních zásadách. Fragment kódu XML se taky přidaly `ValidationTechnicalProfile`, se ReferenceId odkazující na technický profil `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Krok 4.4: Odeslání zásady do vašeho tenanta
1. V [webu Azure portal][Portal], přepněte [kontextu vašeho tenanta Azure AD B2C][B2C-NavContext]a pak vyberte **Azure AD B2C**.

1. Vyberte **architekturu rozhraní identit**.

1. Vyberte **všechny zásady**.

1. Vyberte **nahrát zásady**.

1. Vyberte **přepsat zásady, pokud existuje** zaškrtávací políčko.

1. Nahrát *TrustFrameworkExtensions.xml* souboru a ujistěte se, že projde úspěšně ověřovacím.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Krok 4.5: Testování vlastní zásady pomocí možnosti spustit hned
1. Vyberte **nastavení Azure AD B2C**a pak přejděte na **architekturu rozhraní identit**.

1. Otevřít **B2C_1A_signup_signin**, předávající stranu vlastní zásady, které jste nahráli a pak vyberte **spustit nyní**.

1. Pokuste se přihlaste jedním z přihlašovacích údajů migrovanými uživateli a pak vyberte **Sign In**. Rozhraní REST API by mělo vyvolat následující chybová zpráva:

    ![Nastavení diagnostické protokoly](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Krok 4.6: (Volitelné) Řešení potíží s REST API
Můžete zobrazit a sledovat informace o protokolování v téměř reálném čase.

1. V nabídce nastavení aplikace RESTful v části **monitorování**vyberte **diagnostické protokoly**.

1. Nastavte **protokolování aplikace (systém souborů)** k **na**.

1. Nastavte **úroveň** k **podrobné**.

1. Vyberte **uložit**

    ![Nastavení diagnostické protokoly](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

1. Na **nastavení** nabídce vyberte možnost **stream protokolů**.

1. Zkontrolujte výstup rozhraní RESTful API.

> [!IMPORTANT]
> Protokoly diagnostiky použijte pouze během vývoje a testování. Výstup rozhraní RESTful API můžou obsahovat důvěrné informace, které by neměly být vystaveny v produkčním prostředí.
>

## <a name="optional-download-the-complete-policy-files"></a>(Volitelné) Stažení kompletní zásad souborů
Po dokončení [začít pracovat s vlastními zásadami] [ B2C-GetStartedCustom] návodu, doporučujeme vám vytvořit váš scénář s využitím vlastních zásad pro soubory. Pro srovnání si uvádíme [ukázkové soubory zásad][UserMigrationSample].

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration
