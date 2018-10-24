---
title: Přidání ověřování do vlastních rozhraní API – Azure Logic Apps | Dokumentace Microsoftu
description: Nastavení ověřování pro volání vlastních rozhraní API z Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: 62f71f29f813cd30c7d8e3c7f37f41677cf07364
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956370"
---
# <a name="secure-calls-to-custom-apis-from-azure-logic-apps"></a>Zabezpečení volání vlastních rozhraní API z Azure Logic Apps

Pokud chcete zabezpečit volání k rozhraním API, můžete nastavit ověřování Azure Active Directory (Azure AD) na webu Azure portal proto není nutné a aktualizujte svůj kód. Nebo můžete vyžádat a vynutit ověřování prostřednictvím kódu API.

## <a name="authentication-options-for-your-api"></a>Možnosti ověřování pro vaše rozhraní API

Zabezpečení volání vlastních rozhraní API následujícími způsoby:

* [Bez jediné změny kódu](#no-code): ochrana vašeho rozhraní API s [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) prostřednictvím webu Azure portal, takže není nutné aktualizovat váš kód nebo znovu nasadit své rozhraní API.

  > [!NOTE]
  > Ve výchozím nastavení ověřování Azure AD, která můžete zapnout na webu Azure Portal neposkytuje podrobné autorizace. Například toto ověřování zamkne vašeho rozhraní API na právě konkrétního tenanta, nikoli na konkrétního uživatele nebo aplikace. 

* [Aktualizace kódu rozhraní API](#update-code): ochrana vašeho rozhraní API tím, že vynucuje [ověřování pomocí certifikátu](#certificate), [základní ověřování](#basic), nebo [ověřování Azure AD](#azure-ad-code) prostřednictvím kód.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Ověření volání rozhraní API beze změny kódu

Zde jsou uvedeny základní kroky pro tuto metodu:

1. Vytvořit dvě aplikace identity Azure Active Directory (Azure AD): jeden pro svou aplikaci logiky a jeden pro webové aplikace (nebo aplikace API).

2. K ověření volání rozhraní API, použijte přihlašovací údaje (ID klienta a tajný klíč) pro instanční objekt, který je spojen s identitou aplikace Azure AD pro vaši aplikaci logiky.

3. V definici aplikace logiky zahrnují ID aplikace.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Část 1: Vytvoření identity aplikací Azure AD pro vaši aplikaci logiky

Aplikace logiky používá tuto identitu aplikace služby Azure AD k ověřování vůči službě Azure AD. Stačí nastavit tuto identitu vždy jednou pro svůj adresář. Například můžete použít stejnou identitu pro všechny vaše aplikace logiky, i když můžete vytvořit jedinečné identity pro každou aplikaci logiky. Můžete nastavit tyto identity na webu Azure Portal nebo pomocí [Powershellu](#powershell).

**Vytvoření identity aplikace pro svou aplikaci logiky na webu Azure Portal**

1. V [webu Azure portal](https://portal.azure.com "https://portal.azure.com"), zvolte **Azure Active Directory**. 

2. Potvrďte, že jste ve stejném adresáři jako webovou aplikaci nebo aplikaci API.

   > [!TIP]
   > Chcete-li přepnout adresáře, zvolte svůj profil a vybrat jiný adresář. Nebo zvolte **přehled** > **přepnout adresář**.

3. V nabídce adresáře v rámci **spravovat**, zvolte **registrace aplikací** > **registrace nové aplikace**.

   > [!TIP]
   > Ve výchozím nastavení zobrazuje v seznamu registrací aplikace registrace všech aplikací ve vašem adresáři. Chcete-li zobrazit pouze registrace vaší aplikace, vedle do vyhledávacího pole, vyberte **Moje aplikace**. 

   ![Vytvoření registrace nové aplikace](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Pojmenujte svoji identitu aplikace, ponechejte **typ aplikace** nastavena na **webová aplikace / rozhraní API**, zadejte jedinečný řetězec formátovaný jako doména pro **přihlašovací adresa URL**a zvolte  **Vytvoření**.

   ![Zadejte název a adresu URL pro přihlašování identita aplikace](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   Identita aplikace, který jste vytvořili pro vaši aplikaci logiky se zobrazí v seznamu registrací aplikací.

   ![Identita aplikace pro svou aplikaci logiky](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. V seznamu registrací aplikací vyberte novou identitu aplikace. Zkopírujte a uložte **ID aplikace** používat jako "ID klienta" pro vaši aplikaci logiky v části 3.

   ![Zkopírujte a uložte ID aplikace pro aplikaci logiky](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Pokud nastavení identit aplikace nejsou viditelné, zvolte **nastavení** nebo **všechna nastavení**.

7. V části **přístup přes rozhraní API**, zvolte **klíče**. V části **popis**, zadejte název pro váš klíč. V části **Expires**, vyberte dobu trvání pro váš klíč.

   Klíč, který vytváříte funguje jako identita aplikace "tajné" nebo hesla pro vaši aplikaci logiky.

   ![Vytvořit klíč pro identitu aplikace logiky](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Na panelu nástrojů zvolte **Uložit**. V části **hodnota**, se nyní zobrazí váš klíč. 
**Nezapomeňte zkopírovat a uložit klíč** pro pozdější použití. protože je skrytý klíč při opuštění **klíče** stránky.

   Při konfiguraci aplikace logiky v části 3, zadáváte tento klíč jako "tajný klíč" nebo heslo.

   ![Zkopírujte a uložte klíč na později](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Vytvoření identity aplikace pro svou aplikaci logiky v prostředí PowerShell**

Můžete provést tuto úlohu prostřednictvím Azure Resource Manageru pomocí Powershellu. V prostředí PowerShell spusťte tyto příkazy:

1. `Switch-AzureMode AzureResourceManager`

2. `Add-AzureAccount`

3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password "identity-password"`

4. Nezapomeňte si zkopírovat **ID Tenanta** (identifikátor GUID pro vašeho tenanta Azure AD), **ID aplikace**a heslo, které jste použili.

Další informace, přečtěte si postup [vytvoření instančního objektu pro přístup k prostředkům pomocí prostředí PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Část 2: Vytvoření identity aplikací Azure AD pro vaši webovou aplikaci nebo aplikaci API

Pokud už je nasazená webová aplikace nebo aplikace API, můžete zapnout ověřování a vytvoření identity aplikace na webu Azure Portal. V opačném případě můžete [zapnout ověřování při nasazení pomocí šablony Azure Resource Manageru](#authen-deploy). 

**Vytvoření identity aplikace a zapnout ověřování na webu Azure Portal pro nasazené aplikace**

1. V [webu Azure portal](https://portal.azure.com "https://portal.azure.com"), vyhledejte a vyberte vaše webové aplikace nebo aplikace API. 

2. V části **nastavení**, zvolte **ověřování/autorizace**. V části **ověřování pomocí služby App Service**, zapněte ověřování **na**. V části **zprostředkovatelé ověřování**, zvolte **Azure Active Directory**.

   ![Zapnout ověřování](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Teď vytvořte identity aplikací pro webovou aplikaci nebo aplikaci API, jak je znázorněno zde. Na **nastavení služby Azure Active Directory** nastavte **režim správy** k **Express**. Zvolte **vytvořit novou aplikaci AD**. Pojmenujte svoji identitu aplikace a zvolte **OK**. 

   ![Vytvoření identity aplikace pro webové aplikace nebo aplikace API](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. Na stránce **Ověřování / autorizace** zvolte **Uložit**.

Nyní musíte najít klienta, ID a ID tenanta pro identita aplikace, který je spojen s webovou aplikaci nebo aplikaci API. Použijte tyto identifikátory v části 3. Takže můžete pokračujte podle postupu pro na webu Azure portal.

**Najít ID klienta aplikace identit a ID tenanta pro vaši webovou aplikaci nebo aplikaci API na webu Azure Portal**

1. V části **zprostředkovatelé ověřování**, zvolte **Azure Active Directory**. 

   ![Volba možnosti Azure Active Directory](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. Na **nastavení služby Azure Active Directory** nastavte **režim správy** k **Upřesnit**.

3. Kopírovat **ID klienta**a uložte tento identifikátor GUID pro použití v části 3.

   > [!TIP] 
   > Pokud **ID klienta** a **Url vystavitele** nemáte zobrazovat, zkuste aktualizovat na webu Azure portal a zopakujte krok 1.

4. V části **Url vystavitele**, zkopírujte a uložte jenom identifikátor GUID pro část 3. Tento identifikátor GUID můžete také použít ve webové aplikaci nebo aplikaci API šablonu nasazení, v případě potřeby.

   Tento identifikátor GUID je identifikátor GUID konkrétního tenanta ("ID tenanta") a by se měla zobrazit v této adrese URL: `https://sts.windows.net/{GUID}`

5. Bez uložení změn, zavřete **nastavení služby Azure Active Directory** stránky.

<a name="authen-deploy"></a>

**Zapnout ověřování při nasazení pomocí šablony Azure Resource Manageru**

Stále je nutné vytvořit identitu aplikace služby Azure AD pro vaši webovou aplikaci nebo aplikaci API, která se liší od identity aplikace pro svou aplikaci logiky. Chcete-li vytvořit identita aplikace, postupujte podle předchozí kroky v části 2 pro na webu Azure portal. 

Můžete také postupovat podle kroků v části 1, ale je nutné použít webové aplikace nebo rozhraní API aplikace skutečný `https://{URL}` pro **přihlašovací adresa URL** a **identifikátor ID URI aplikace**. Z těchto kroků budete muset uložit ID klienta a ID tenanta pro použití v šabloně nasazení vaší aplikace a také pro část 3.

> [!NOTE]
> Když vytvoříte identitu aplikace služby Azure AD pro vaši webovou aplikaci nebo aplikaci API, musíte použít na webu Azure portal, Powershellu není. Rutiny Powershellu není nastavení požadovaná oprávnění pro přihlášení uživatele na webu.

Po získání klientské ID a ID tenanta, patří tyto identifikátory jako vytváření webové aplikace nebo aplikace API v šabloně nasazení:

``` json
"resources": [ {
    "apiVersion": "2015-08-01",
    "name": "web",
    "type": "config",
    "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
    "properties": {
        "siteAuthEnabled": true,
        "siteAuthSettings": {
            "clientId": "{client-ID}",
            "issuer": "https://sts.windows.net/{tenant-ID}/",
        }
    }
} ]
```

Prázdná webová aplikace a aplikaci logiky spolu s ověřováním Azure Active Directory, automaticky nasazovat [zobrazit úplnou šablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json), nebo klikněte na tlačítko **nasadit do Azure** tady:

[![Nasazení do Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>3. část: Naplnění části autorizace v aplikaci logiky

Předchozí šablona už má tato autorizace část nastavení, ale pokud přímo autorizujete aplikaci logiky, musí obsahovat oddíl plnou autorizaci.

Otevřít definici aplikace logiky v zobrazení kódu, přejděte na **HTTP** části akci najít **autorizace** části a přidejte tento řádek:

`{"tenant": "{tenant-ID}", "audience": "{client-ID-from-Part-2-web-app-or-API app}", "clientId": "{client-ID-from-Part-1-logic-app}", "secret": "{key-from-Part-1-logic-app}", "type": "ActiveDirectoryOAuth" }`

| Element | Požaduje se | Popis | 
| ------- | -------- | ----------- | 
| tenant | Ano | Identifikátor GUID pro tenanta Azure AD | 
| Cílová skupina | Ano | Identifikátor GUID pro cílový prostředek, kterou chcete získat přístup, který je ID klienta z identity aplikace pro webové aplikace nebo aplikace API | 
| ID klienta | Ano | Identifikátor GUID pro klienta žádosti o přístup, který je ID klienta z identity aplikace pro svou aplikaci logiky | 
| Tajný kód | Ano | Klíč nebo heslo z identity aplikace pro klienta, který žádá o přístupový token | 
| type | Ano | Typ ověřování. ActiveDirectoryOAuth ověřování, je hodnota `ActiveDirectoryOAuth`. | 
|||| 

Příklad:

``` json
{
   "actions": {
      "some-action": {
         "conditions": [],
         "inputs": {
            "method": "post",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         },
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Zabezpečené volání rozhraní API prostřednictvím kódu.

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Ověřování certifikátem

K ověření příchozích požadavků z aplikace logiky k webové aplikaci nebo aplikaci API, můžete použít klientské certifikáty. K nastavení kódu, přečtěte si [konfigurace vzájemného ověřování TLS](../app-service/app-service-web-configure-tls-mutual-auth.md).

V **autorizace** oddílu, zahrňte tento řádek: 

`{"type": "clientcertificate", "password": "password", "pfx": "long-pfx-key"}`

| Element | Požaduje se | Popis | 
| ------- | -------- | ----------- | 
| type | Ano | Typ ověřování. Pro klientské certifikáty SSL, musí být hodnota `ClientCertificate`. | 
| heslo | Ano | Heslo pro přístup k certifikátu klienta (soubor PFX) | 
| PFX | Ano | Obsah s kódováním base64 klientský certifikát (soubor PFX) | 
|||| 

<a name="basic"></a>

#### <a name="basic-authentication"></a>Základní ověřování

K ověření příchozích požadavků z aplikace logiky k webové aplikaci nebo aplikaci API, můžete použít základní ověřování, jako je například uživatelské jméno a heslo. Základní ověřování je běžný vzor a toto ověření můžete použít v libovolném jazyce sloužící k sestavení webové aplikace nebo aplikace API.

V **autorizace** oddílu, zahrňte tento řádek:

`{"type": "basic", "username": "username", "password": "password"}`.

| Element | Požaduje se | Popis | 
| ------- | -------- | ----------- | 
| type | Ano | Typ ověřování, který chcete použít. Pro základní ověřování, musí být hodnota `Basic`. | 
| uživatelské jméno | Ano | Uživatelské jméno, které chcete použít pro ověřování | 
| heslo | Ano | Heslo, které chcete použít pro ověřování | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Ověřování pomocí Azure Active Directory prostřednictvím kódu

Ve výchozím nastavení ověřování Azure AD, která můžete zapnout na webu Azure Portal neposkytuje podrobné autorizace. Například toto ověřování zamkne vašeho rozhraní API na právě konkrétního tenanta, nikoli na konkrétního uživatele nebo aplikace. 

Pokud chcete omezit přístup přes rozhraní API do aplikace logiky pomocí kódu, extrahujte záhlaví, který má webový token JSON (JWT). Zkontrolujte identitu volajícího a odmítnout požadavky, které se neshodují.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/app-service-authentication-overview.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Další postup

* [Nasazení a volání vlastních rozhraní API od logiky pracovní postupy aplikace](../logic-apps/logic-apps-custom-api-host-deploy-call.md)