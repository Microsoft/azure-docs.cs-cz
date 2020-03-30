---
title: Přidání ověřování pro zabezpečení volání vlastních api
description: Jak nastavit ověřování pro zlepšení zabezpečení pro volání vlastních api z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: 110a684cf6ad21c13411d3bc2ada84750744f00e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191412"
---
# <a name="increase-security-for-calls-to-custom-apis-from-azure-logic-apps"></a>Zvýšení zabezpečení pro volání vlastních api z Azure Logic Apps

Chcete-li zlepšit zabezpečení pro volání vašich rozhraní API, můžete nastavit ověřování Azure Active Directory (Azure AD) prostřednictvím portálu Azure, takže nemusíte aktualizovat kód. Nebo můžete vyžádat a vynutit ověřování prostřednictvím kódu API.

## <a name="authentication-options-for-your-api"></a>Možnosti ověřování pro rozhraní API

Zabezpečení volání vlastního rozhraní API můžete zlepšit následujícími způsoby:

* [Žádné změny kódu](#no-code): Chraňte své rozhraní API pomocí [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) prostřednictvím portálu Azure, takže nemusíte aktualizovat kód nebo znovu nasadit rozhraní API.

  > [!NOTE]
  > Ve výchozím nastavení ověřování Azure AD, které zapnete na webu Azure Portal, neposkytuje jemně odstupňovanou autorizaci. Toto ověřování například uzamkne vaše rozhraní API pouze na konkrétního klienta, nikoli na konkrétního uživatele nebo aplikaci. 

* [Aktualizace kódu rozhraní API:](#update-code)Chraňte rozhraní API vynucením [ověřování certifikátů](#certificate), [základním ověřováním](#basic)nebo [ověřováním Azure AD](#azure-ad-code) pomocí kódu.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Ověření volání do rozhraní API bez změny kódu

Zde jsou obecné kroky pro tuto metodu:

1. Vytvořte dvě identity aplikací Azure Active Directory (Azure AD): jednu pro aplikaci logiky a jednu pro webovou aplikaci (nebo aplikaci rozhraní API).

2. K ověření volání vašeho rozhraní API použijte přihlašovací údaje (ID klienta a tajný klíč) pro instanční objekt, který je přidružený k identitě aplikace Azure AD pro vaši aplikaci logiky.

3. Do definice aplikace logiky zahrňte ID aplikace.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Část 1: Vytvoření identity aplikace Azure AD pro aplikaci logiky

Vaše aplikace logiky používá tuto identitu aplikace Azure AD k ověření proti Azure AD. Tuto identitu je třeba nastavit pouze jednou pro váš adresář. Můžete například použít stejnou identitu pro všechny aplikace logiky, i když můžete vytvořit jedinečné identity pro každou aplikaci logiky. Tyto identity můžete nastavit na webu Azure Portal nebo použít [PowerShell](#powershell).

**Vytvoření identity aplikace pro aplikaci logiky na webu Azure Portal**

1. Na [webu Azure Portal](https://portal.azure.com "https://portal.azure.com")zvolte **Azure Active Directory**. 

2. Zkontrolujte, zda jste ve stejném adresáři jako webová aplikace nebo aplikace rozhraní API.

   > [!TIP]
   > Chcete-li přepnout adresáře, zvolte svůj profil a vyberte jiný adresář. Nebo zvolte **Přepnout přehled** > **adresáře**.

3. V nabídce adresáře v části **Manage**zvolte **Registrace** > aplikací**Nová registrace aplikace**.

   > [!TIP]
   > Ve výchozím nastavení zobrazuje seznam registrací aplikací ve vašem adresáři všechny registrace aplikací. Pokud chcete zobrazit jenom registrace aplikací, vyberte vedle vyhledávacího pole možnost **Moje aplikace**. 

   ![Vytvoření nové registrace aplikace](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Pojmenujte identitu aplikace, ponechejte **typ aplikace** nastavený na **Webovou aplikaci / ROZHRANÍ API**, poskytněte jedinečný řetězec formátovaný jako doménu pro přihlašovací adresu **URL**a zvolte **Vytvořit**.

   ![Zadejte adresu URL pro název a přihlášení pro identitu aplikace.](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   Identita aplikace, kterou jste vytvořili pro aplikaci logiky, se teď zobrazí v seznamu registrací aplikací.

   ![Identita aplikace pro aplikaci logiky](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. V seznamu registrací aplikací vyberte novou identitu aplikace. Zkopírujte a uložte **ID aplikace** pro použití jako "ID klienta" pro aplikaci logiky v části 3.

   ![Kopírování a ukládání ID aplikace pro aplikaci logiky](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Pokud nastavení identity aplikace nejsou viditelná, zvolte **Nastavení** nebo **Všechna nastavení**.

7. V části **PŘÍSTUP K ROZHRANÍ API**zvolte **Keys**. V části **Popis**zadejte název klíče. V části **Vyprší**vyberte dobu trvání klíče.

   Klíč, který vytváříte, funguje jako "tajný" nebo heslo identity aplikace pro vaši aplikaci logiky.

   ![Vytvořit klíč pro identitu aplikace logiky](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Na panelu nástrojů zvolte **Uložit**. V části **Hodnota**se nyní zobrazí klíč. 
**Nezapomeňte klíč zkopírovat a uložit** pro pozdější použití, protože klíč je skrytý, když opustíte stránku **Klíče.**

   Při konfiguraci aplikace logiky v části 3, zadáte tento klíč jako "tajný klíč" nebo heslo.

   ![Kopírovat a ukládat klíč na později](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Vytvoření identity aplikace pro aplikaci logiky v PowerShellu**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tento úkol můžete provést prostřednictvím Správce prostředků Azure pomocí PowerShellu. V PowerShellu spusťte tyto příkazy:

1. `Add-AzAccount`

1. `$SecurePassword = Read-Host -AsSecureString`

1. Zadejte heslo a stiskněte Enter.

1. `New-AzADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

1. Ujistěte se, že **zkopírujete ID klienta** (GUID pro vašeho klienta Azure AD), **ID aplikace**a heslo, které jste použili.

Další informace naleznete v informacích o tom, jak [vytvořit instanční objekt pomocí prostředí PowerShell pro přístup k prostředkům](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Část 2: Vytvoření identity aplikace Azure AD pro webovou aplikaci nebo aplikaci rozhraní API

Pokud je vaše webová aplikace nebo aplikace API už nasazená, můžete zapnout ověřování a vytvořit identitu aplikace na webu Azure Portal. V opačném případě můžete [zapnout ověřování při nasazení pomocí šablony Azure Resource Manager](#authen-deploy). 

**Vytvoření identity aplikace a zapnutí ověřování na webu Azure Portal pro nasazené aplikace**

1. Na [webu Azure Portal](https://portal.azure.com "https://portal.azure.com")najděte a vyberte svou webovou aplikaci nebo aplikaci API. 

2. V části **Nastavení**zvolte **Ověřování/Autorizace**. V části **Ověřování služby App Service**zapněte ověřování . **On** V části **Zprostředkovatelé ověřování**zvolte **Azure Active Directory**.

   ![Zapnutí ověřování](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Teď vytvořte identitu aplikace pro webovou aplikaci nebo aplikaci ROZHRANÍ API, jak je znázorněno tady. Na stránce **Nastavení služby Azure Active Directory** nastavte režim **správy** na **expresní**. Zvolte **Vytvořit novou aplikaci pro reklamy**. Pojmenujte identitu aplikace a zvolte **OK**. 

   ![Vytvoření identity aplikace pro webovou aplikaci nebo aplikaci API](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. Na stránce **Ověřování / autorizace** zvolte **Uložit**.

Teď musíte najít ID klienta a ID klienta pro identitu aplikace, která je přidružená k vaší webové aplikaci nebo aplikaci rozhraní API. Tyto ID používáte v části 3. Takže pokračujte těmito kroky pro portál Azure.

**Vyhledání ID klienta a ID klienta identity aplikace pro vaši webovou aplikaci nebo aplikaci API na webu Azure Portal**

1. V části **Zprostředkovatelé ověřování**zvolte **Azure Active Directory**. 

   ![Volba možnosti Azure Active Directory](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. Na stránce **Nastavení služby Azure Active Directory** nastavte režim **správy** na **Upřesnit**.

3. Zkopírujte **ID klienta**a uložte jej pro použití v části 3.

   > [!TIP] 
   > Pokud **se nezobrazí ID klienta** a **adresa URL vystavitele,** zkuste aktualizovat portál Azure a zopakujte krok 1.

4. V části **Url vystavitele**zkopírujte a uložte pouze identifikátor GUID pro část 3. Tento identifikátor GUID můžete v případě potřeby použít také ve webové aplikaci nebo v šabloně nasazení aplikace ROZHRANÍ API.

   Tento identifikátor GUID je identifikátor GUID vašeho konkrétního klienta ("ID klienta") a měl by se zobrazit v této adrese URL:`https://sts.windows.net/{GUID}`

5. Bez uložení změn zavřete stránku **Nastavení služby Azure Active Directory.**

<a name="authen-deploy"></a>

**Zapnutí ověřování při nasazení pomocí šablony Azure Resource Manager**

Pořád potřebujete vytvořit identitu aplikace Azure AD pro vaši webovou aplikaci nebo aplikaci rozhraní API, která se liší od identity aplikace aplikace pro vaši logiku. Pokud chcete vytvořit identitu aplikace, postupujte podle předchozích kroků v části 2 pro portál Azure. 

Můžete také postupovat podle kroků v části 1, ale ujistěte se, že používáte webovou aplikaci nebo aktuální `https://{URL}` adresu API pro přihlašovací adresu **URL** a identifikátor URI **ID aplikace**. Z těchto kroků budete muset uložit ID klienta i ID klienta pro použití v šabloně nasazení vaší aplikace a také pro část 3.

> [!NOTE]
> Když vytvoříte identitu aplikace Azure AD pro vaši webovou aplikaci nebo aplikaci api, musíte použít portál Azure, ne PowerShell. Příkaz PowerShell nenastavuje požadovaná oprávnění k přihlášení uživatelů k webu.

Po získání ID klienta a ID klienta zahrňte tato ID jako podprostředek vaší webové aplikace nebo aplikace rozhraní API do šablony nasazení:

``` json
"resources": [ 
   {
      "apiVersion": "2015-08-01",
      "name": "web",
      "type": "config",
      "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
      "properties": {
         "siteAuthEnabled": true,
         "siteAuthSettings": {
            "clientId": "<client-ID>",
            "issuer": "https://sts.windows.net/<tenant-ID>/"
         }
      }
   } 
]
```

Pokud chcete automaticky nasadit prázdnou webovou aplikaci a aplikaci logiky společně s ověřováním služby Azure Active Directory, [zobrazte kompletní šablonu tady](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json)nebo klikněte na **Nasadit do Azure** tady:

[![Nasazení do Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Část 3: Naplnění části Autorizace v aplikaci logiky

Předchozí šablona již má tuto část autorizace nastavena, ale pokud jste přímo vytváření aplikace logiky, musíte zahrnout úplné autorizační části.

Otevřete definici aplikace logiky v zobrazení kódu, přejděte na definici akce **HTTP,** vyhledejte část **Autorizace** a zahrňte tyto vlastnosti:

```json
{
   "tenant": "<tenant-ID>",
   "audience": "<client-ID-from-Part-2-web-app-or-API app>", 
   "clientId": "<client-ID-from-Part-1-logic-app>",
   "secret": "<key-from-Part-1-logic-app>", 
   "type": "ActiveDirectoryOAuth"
}
```

| Vlastnost | Požaduje se | Popis | 
| -------- | -------- | ----------- | 
| Nájemce | Ano | Identifikátor GUID pro klienta Azure AD | 
| Publikum | Ano | Identifikátor GUID cílového prostředku, ke kterému chcete získat přístup, což je ID klienta z identity aplikace pro webovou aplikaci nebo aplikaci rozhraní API | 
| clientId | Ano | Identifikátor GUID pro klienta požadující přístup, což je ID klienta z identity aplikace pro aplikaci logiky | 
| Tajný kód | Ano | Klíč nebo heslo z identity aplikace pro klienta, který požaduje přístupový token | 
| type | Ano | Typ ověřování. Pro ověřování ActiveDirectoryOAuth je `ActiveDirectoryOAuth`hodnota . | 
|||| 

Například:

``` json
{
   "actions": {
      "HTTP": {
         "inputs": {
            "method": "POST",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         }
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Zabezpečené volání rozhraní API prostřednictvím kódu

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Ověření certifikátu

Chcete-li ověřit příchozí požadavky z aplikace logiky do webové aplikace nebo aplikace rozhraní API, můžete použít klientské certifikáty. Chcete-li nastavit kód, přečtěte si, [jak nakonfigurovat vzájemné ověřování TLS](../app-service/app-service-web-configure-tls-mutual-auth.md).

V části **Autorizace** uveďte tyto vlastnosti:

```json
{
   "type": "ClientCertificate",
   "password": "<password>",
   "pfx": "<long-pfx-key>"
} 
```

| Vlastnost | Požaduje se | Popis |
| -------- | -------- | ----------- |
| `type` | Ano | Typ ověřování. U klientských certifikátů SSL `ClientCertificate`musí být hodnota . |
| `password` | Ne | Heslo pro přístup k klientském certifikátu (soubor PFX) |
| `pfx` | Ano | Obsah klientského certifikátu kódovaný na základně64 (soubor PFX) |
||||

<a name="basic"></a>

#### <a name="basic-authentication"></a>Základní ověřování

Chcete-li ověřit příchozí požadavky z aplikace logiky do webové aplikace nebo aplikace rozhraní API, můžete použít základní ověřování, jako je uživatelské jméno a heslo. Základní ověřování je běžný vzor a toto ověřování můžete použít v libovolném jazyce používaném k vytvoření webové aplikace nebo aplikace rozhraní API.

V části **Autorizace** uveďte tyto vlastnosti:

```json
{
   "type": "Basic",
   "username": "<username>",
   "password": "<password>"
}
```

| Vlastnost | Požaduje se | Popis | 
| -------- | -------- | ----------- | 
| type | Ano | Typ ověřování, který chcete použít. Pro základní ověřování musí `Basic`být hodnota . | 
| uživatelské jméno | Ano | Uživatelské jméno, které chcete použít pro ověřování | 
| heslo | Ano | Heslo, které chcete použít pro ověřování | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Ověřování azure služby Active Directory pomocí kódu

Ve výchozím nastavení ověřování Azure AD, které zapnete na webu Azure Portal, neposkytuje jemně odstupňovanou autorizaci. Toto ověřování například uzamkne vaše rozhraní API pouze na konkrétního klienta, nikoli na konkrétního uživatele nebo aplikaci. 

Chcete-li omezit přístup rozhraní API k aplikaci logiky prostřednictvím kódu, extrahujte záhlaví, které má webový token JSON (JWT). Zkontrolujte identitu volajícího a odmítnout požadavky, které se neshodují.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Další kroky

* [Nasazení a volání vlastních api z pracovních postupů aplikace logiky](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
