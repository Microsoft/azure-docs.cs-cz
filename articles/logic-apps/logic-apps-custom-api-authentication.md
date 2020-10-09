---
title: Přidání ověřování pro zabezpečení volání vlastních rozhraní API
description: Jak nastavit ověřování pro zlepšení zabezpečení pro volání vlastních rozhraní API z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: 5e0dcd478c6eb6696a0e07d35d4dccddac68ac1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80656236"
---
# <a name="increase-security-for-calls-to-custom-apis-from-azure-logic-apps"></a>Zvýšení zabezpečení pro volání vlastních rozhraní API z Azure Logic Apps

Pro zvýšení zabezpečení volání rozhraní API můžete nastavit ověřování Azure Active Directory (Azure AD) prostřednictvím Azure Portal, takže nemusíte aktualizovat kód. Nebo můžete vyžádat a vynutit ověřování prostřednictvím kódu API.

## <a name="authentication-options-for-your-api"></a>Možnosti ověřování pro vaše rozhraní API

V těchto způsobech můžete zlepšit zabezpečení volání vlastních rozhraní API:

* [Žádné změny kódu](#no-code): pomocí Azure Portal chránit rozhraní API pomocí služby [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) , takže nemusíte aktualizovat kód ani znovu nasazovat rozhraní API.

  > [!NOTE]
  > Ve výchozím nastavení ověřování Azure AD, které zapnete v Azure Portal, neposkytuje jemně odstupňovanou autorizaci. Toto ověřování například uzamkne vaše rozhraní API pouze na konkrétního tenanta, nikoli na konkrétního uživatele nebo aplikaci. 

* [Aktualizujte kód rozhraní API](#update-code): Chraňte své rozhraní API vynucením [ověřování pomocí certifikátů](#certificate), [základního ověřování](#basic)nebo [ověřování Azure AD](#azure-ad-code) prostřednictvím kódu.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Ověřování volání rozhraní API beze změny kódu

Tady jsou obecné kroky pro tuto metodu:

1. Vytvořte dvě Azure Active Directory (Azure AD) identity aplikace: jednu pro vaši aplikaci logiky a jednu pro vaši webovou aplikaci (nebo aplikaci API).

2. Pokud chcete ověřit volání rozhraní API, použijte přihlašovací údaje (ID klienta a tajný kód) pro instanční objekt, který je přidružený k identitě aplikace služby Azure AD pro vaši aplikaci logiky.

3. Do definice aplikace logiky zahrňte ID aplikací.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Část 1: vytvoření identity aplikace služby Azure AD pro vaši aplikaci logiky

Vaše aplikace logiky používá tuto identitu aplikace Azure AD k ověřování vůči službě Azure AD. Tuto identitu musíte nastavit pro svůj adresář jednou jenom jednou. Můžete se třeba rozhodnout, že chcete použít stejnou identitu pro všechny vaše aplikace logiky, i když můžete pro každou aplikaci logiky vytvořit jedinečné identity. Tyto identity můžete nastavit v Azure Portal nebo pomocí [PowerShellu](#powershell).

**Vytvořte identitu aplikace pro vaši aplikaci logiky v Azure Portal**

1. V [Azure Portal](https://portal.azure.com "https://portal.azure.com")vyberte možnost **Azure Active Directory**. 

2. Potvrďte, že jste ve stejném adresáři jako vaše webová aplikace nebo aplikace API.

   > [!TIP]
   > Chcete-li přepnout adresáře, zvolte svůj profil a vyberte jiný adresář. Případně vyberte možnost **Přehled**  >  **adresáře Switch**.

3. V nabídce adresář v části **Spravovat**vyberte **Registrace aplikací**  >  **Nová registrace aplikace**.

   > [!TIP]
   > Ve výchozím nastavení zobrazuje seznam registrací aplikací všechny registrace aplikací ve vašem adresáři. Chcete-li zobrazit pouze registrace aplikací, vyberte vedle pole hledání možnost **Moje aplikace**. 

   ![Vytvoření nové registrace aplikace](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Přiřaďte vaší aplikaci název, ponechte **Typ aplikace** nastavenou na **Webová aplikace/rozhraní API**, zadejte jedinečný řetězec formátovaný jako doména pro **přihlašovací adresu URL**a klikněte na **vytvořit**.

   ![Zadejte název a přihlašovací adresu URL pro identitu aplikace.](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   Identita aplikace, kterou jste vytvořili pro vaši aplikaci logiky, se teď zobrazí v seznamu registrace aplikací.

   ![Identita aplikace pro vaši aplikaci logiky](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. V seznamu registrace aplikací vyberte novou identitu aplikace. Zkopírujte a uložte **ID aplikace** pro použití jako ID klienta pro vaši aplikaci logiky v části 3.

   ![Zkopírování a uložení ID aplikace pro aplikaci logiky](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Pokud není nastavení identity vaší aplikace viditelné, vyberte **Nastavení** nebo **všechna nastavení**.

7. V části **přístup k rozhraní API**vyberte **klíče**. V části **Popis**zadejte název klíče. Pod položkou **konec platnosti**vyberte dobu trvání pro svůj klíč.

   Klíč, který vytváříte, funguje jako klíčová slova pro identitu aplikace nebo heslo pro vaši aplikaci logiky.

   ![Vytvořit klíč pro identitu aplikace logiky](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Na panelu nástrojů klikněte na tlačítko **Uložit**. Pod položkou **hodnota**se teď klíč zobrazuje. 
**Nezapomeňte si klíč zkopírovat a uložit** pro pozdější použití, protože při opuštění stránky **klíče** je klíč skrytý.

   Když nakonfigurujete aplikaci logiky v části 3, zadáte tento klíč jako tajný klíč nebo heslo.

   ![Zkopírovat a uložit klíč pro pozdější](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Vytvoření identity aplikace pro vaši aplikaci logiky v PowerShellu**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tuto úlohu můžete provést prostřednictvím Azure Resource Manager s využitím PowerShellu. V PowerShellu spusťte tyto příkazy:

1. `Add-AzAccount`

1. `$SecurePassword = Read-Host -AsSecureString`

1. Zadejte heslo a stiskněte klávesu ENTER.

1. `New-AzADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

1. Nezapomeňte zkopírovat **ID tenanta** (GUID vašeho TENANTA Azure AD), **ID aplikace**a heslo, které jste použili.

Další informace najdete v tématu [Vytvoření instančního objektu s prostředím PowerShell pro přístup k prostředkům](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Část 2: vytvoření identity aplikace Azure AD pro webovou aplikaci nebo aplikaci API

Pokud je vaše webová aplikace nebo aplikace API už nasazená, můžete zapnout ověřování a vytvořit identitu aplikace v Azure Portal. V opačném případě můžete [ověřování zapnout při nasazení pomocí šablony Azure Resource Manager](#authen-deploy). 

**Vytvoření identity aplikace a zapnutí ověřování v Azure Portal pro nasazené aplikace**

1. V [Azure Portal](https://portal.azure.com "https://portal.azure.com")vyhledejte a vyberte webovou aplikaci nebo aplikaci API. 

2. V části **Nastavení**vyberte **ověřování/autorizace**. V části **ověřování App Service**zapněte **ověřování.** V části **Zprostředkovatelé ověřování**vyberte **Azure Active Directory**.

   ![Zapnout ověřování](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Nyní vytvořte identitu aplikace pro webovou aplikaci nebo aplikaci API, jak je znázorněno zde. Na stránce **nastavení Azure Active Directory** nastavte **režim správy** na hodnotu **expresní**. Vyberte **vytvořit novou aplikaci AD**. Pojmenujte identitu vaší aplikace a klikněte na **tlačítko OK**. 

   ![Vytvoření identity aplikace pro webovou aplikaci nebo aplikaci API](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. Na stránce **Ověřování / autorizace** zvolte **Uložit**.

Teď musíte najít ID klienta a ID tenanta pro identitu aplikace, která je přidružená k vaší webové aplikaci nebo aplikaci API. Tato ID použijete v části 3. Proto pokračujte podle těchto kroků Azure Portal.

**Vyhledejte ID klienta identity aplikace a ID tenanta webové aplikace nebo aplikace API v Azure Portal**

1. V části **Zprostředkovatelé ověřování**vyberte **Azure Active Directory**. 

   ![Volba možnosti Azure Active Directory](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. Na stránce **nastavení Azure Active Directory** nastavte **režim správy** na **Upřesnit**.

3. Zkopírujte **ID klienta**a uložte tento identifikátor GUID pro použití v části 3.

   > [!TIP] 
   > Pokud se **ID klienta** a **Adresa URL vystavitele** nezobrazí, zkuste aktualizovat Azure Portal a opakujte krok 1.

4. V části **Adresa URL vystavitele**zkopírujte a uložte jenom identifikátor GUID pro část 3. V případě potřeby můžete také použít tento identifikátor GUID v šabloně nasazení webové aplikace nebo aplikace API.

   Toto GUID je váš konkrétní identifikátor GUID vašeho tenanta ("ID tenanta") a měl by se zobrazovat v této adrese URL: `https://sts.windows.net/{GUID}`

5. Bez uložení změn zavřete stránku **nastavení Azure Active Directory** .

<a name="authen-deploy"></a>

**Zapnout ověřování při nasazení pomocí šablony Azure Resource Manager**

Stále potřebujete vytvořit identitu aplikace Azure AD pro webovou aplikaci nebo aplikaci API, která se liší od identity aplikace pro vaši aplikaci logiky. Chcete-li vytvořit identitu aplikace, postupujte podle předchozích kroků v části 2 Azure Portal. 

Můžete také postupovat podle části 1, ale ujistěte se, že používáte svou webovou aplikaci nebo aplikaci API skutečnou `https://{URL}` pro **přihlašovací adresu URL** a **identifikátor URI ID aplikace**. Z těchto kroků musíte uložit ID klienta i ID tenanta pro použití v šabloně nasazení vaší aplikace a také pro část 3.

> [!NOTE]
> Když vytvoříte identitu aplikace Azure AD pro webovou aplikaci nebo aplikaci API, musíte použít Azure Portal, ne PowerShell. Prostředí PowerShell rutiny nenastavuje požadovaná oprávnění k podepisování uživatelů na webu.

Po získání ID klienta a ID tenanta uveďte tato ID jako podprostředek vaší webové aplikace nebo aplikace API v šabloně nasazení:

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

K automatickému nasazení prázdné webové aplikace a aplikace logiky spolu s ověřováním Azure Active Directory si můžete [Zobrazit úplnou šablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json)nebo kliknout na **nasadit do Azure** tady:

[![Nasazení do Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Část 3: naplnění autorizačního oddílu ve vaší aplikaci logiky

Pro předchozí šablonu už je nastavený tento autorizační oddíl, ale pokud přímo vytváříte aplikaci logiky, musíte zahrnout část s úplným autorizací.

Otevřete definici aplikace logiky v zobrazení kódu, vyhledejte část definice akce **http** , najděte oddíl **autorizace** a zahrňte tyto vlastnosti:

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
| tenant | Ano | Identifikátor GUID pro tenanta Azure AD | 
| osoby | Ano | Identifikátor GUID cílového prostředku, ke kterému chcete získat přístup, což je ID klienta z identity aplikace pro webovou aplikaci nebo aplikaci API. | 
| clientId | Ano | Identifikátor GUID pro klienta požadujícího přístup, což je ID klienta z identity aplikace pro vaši aplikaci logiky. | 
| Tajný kód | Ano | Klíč nebo heslo z identity aplikace pro klienta, který žádá o přístupový token | 
| typ | Ano | Typ ověřování. Pro ověřování ActiveDirectoryOAuth je hodnota `ActiveDirectoryOAuth` . | 
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

### <a name="secure-api-calls-through-code"></a>Zabezpečená volání rozhraní API prostřednictvím kódu

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Ověření certifikátu

Pokud chcete ověřit příchozí požadavky z aplikace logiky do vaší webové aplikace nebo aplikace API, můžete použít klientské certifikáty. Chcete-li nastavit kód, zjistěte, [jak nakonfigurovat vzájemné ověřování TLS](../app-service/app-service-web-configure-tls-mutual-auth.md).

V části **autorizace** zahrňte tyto vlastnosti:

```json
{
   "type": "ClientCertificate",
   "password": "<password>",
   "pfx": "<long-pfx-key>"
} 
```

| Vlastnost | Požaduje se | Popis |
| -------- | -------- | ----------- |
| `type` | Ano | Typ ověřování. Pro klientské certifikáty TLS/SSL musí být hodnota `ClientCertificate` . |
| `password` | No | Heslo pro přístup k klientskému certifikátu (soubor PFX) |
| `pfx` | Ano | Obsah klientského certifikátu kódovaný v Base64 (soubor PFX) |
||||

<a name="basic"></a>

#### <a name="basic-authentication"></a>Základní ověřování

Pokud chcete ověřit příchozí žádosti z aplikace logiky do vaší webové aplikace nebo aplikace API, můžete použít základní ověřování, například uživatelské jméno a heslo. Základní ověřování je běžným vzorem a můžete ho použít v jakémkoli jazyce, který se používá k sestavení webové aplikace nebo aplikace API.

V části **autorizace** zahrňte tyto vlastnosti:

```json
{
   "type": "Basic",
   "username": "<username>",
   "password": "<password>"
}
```

| Vlastnost | Požaduje se | Popis | 
| -------- | -------- | ----------- | 
| typ | Ano | Typ ověřování, který chcete použít. Pro základní ověřování musí být hodnota `Basic` . | 
| username | Ano | Uživatelské jméno, které chcete použít pro ověřování. | 
| heslo | Ano | Heslo, které chcete použít pro ověřování. | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Azure Active Directory ověřování prostřednictvím kódu

Ve výchozím nastavení ověřování Azure AD, které zapnete v Azure Portal, neposkytuje jemně odstupňovanou autorizaci. Toto ověřování například uzamkne vaše rozhraní API pouze na konkrétního tenanta, nikoli na konkrétního uživatele nebo aplikaci. 

Pokud chcete omezit přístup rozhraní API k vaší aplikaci logiky prostřednictvím kódu, rozbalte hlavičku, která má token JSON web token (JWT). Zkontroluje identitu volajícího a zamítne žádosti, které se neshodují.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Další kroky

* [Nasazení a volání vlastních rozhraní API z pracovních postupů aplikací logiky](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
