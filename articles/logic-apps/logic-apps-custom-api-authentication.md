---
title: Přidání ověřování do vlastní rozhraní API – Azure Logic Apps | Microsoft Docs
description: Nastavení ověřování pro volání pro vaše vlastní rozhraní API z aplikace logiky
author: ecfan
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 705abb2a3cc25c965bdce364eb169b4e3a814bff
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298545"
---
# <a name="secure-calls-to-your-custom-apis-from-logic-apps"></a>Zabezpečená volání pro vaše vlastní rozhraní API z aplikace logiky

K zabezpečení volání pro vaše rozhraní API, můžete nastavit ověřování Azure Active Directory (Azure AD) prostřednictvím portálu Azure, nemusíte aktualizace kódu. Nebo můžete vyžádat a vynutit ověřování prostřednictvím kódu API.

## <a name="authentication-options-for-your-api"></a>Možnosti ověřování pro vaše rozhraní API

Můžete zabezpečit volání vlastního rozhraní API těmito způsoby:

* [Žádné změny kódu](#no-code): ochrana rozhraní API s [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) prostřednictvím portálu Azure, takže nemusíte aktualizace kódu nebo znovu nasadit své rozhraní API.

  > [!NOTE]
  > Ve výchozím nastavení ověřování Azure AD, které můžete zapnout na portálu Azure neposkytuje podrobné autorizace. Toto ověřování například zamkne rozhraní API k jenom konkrétní klienta, nikoli k konkrétního uživatele nebo aplikace. 

* [Aktualizujte kód vaše rozhraní API](#update-code): ochrana rozhraní API vynucením [ověřování pomocí certifikátu](#certificate), [základní ověřování](#basic), nebo [ověřování Azure AD](#azure-ad-code) prostřednictvím kód.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Ověření volání rozhraní API beze změny kódu

Zde jsou uvedeny základní kroky pro tuto metodu:

1. Vytvořte dvě identity aplikace Azure Active Directory (Azure AD): jeden pro svou aplikaci logiky a jeden pro webové aplikace (nebo aplikace API).

2. K ověření volání rozhraní API, použijte přihlašovací údaje (ID klienta a tajný klíč) pro objekt služby, který je spojen s identitou aplikace Azure AD pro svou aplikaci logiky.

3. ID aplikace zahrňte do vaší definici aplikace logiky.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Část 1: Vytvoření identity aplikací Azure AD pro svou aplikaci logiky

Aplikace logiky používá tuto identitu aplikace služby Azure AD k ověření služby Azure AD. Stačí nastavit tuto identitu jednou pro váš adresář. Například můžete použít stejnou identitu pro všechny aplikace logiky, i když můžete vytvořit jedinečné identity pro každou aplikaci logiky. Můžete nastavit tyto identit na portálu Azure nebo použít [prostředí PowerShell](#powershell).

**Vytvoření identity aplikací pro svou aplikaci logiky na portálu Azure**

1. V [portál Azure](https://portal.azure.com "https://portal.azure.com"), zvolte **Azure Active Directory**. 

2. Potvrďte, že jste ve stejném adresáři jako webovou aplikaci nebo aplikaci API.

   > [!TIP]
   > K přepnutí adresářů, zvolte svůj profil a vyberte jiný adresář. Nebo zvolte **přehled** > **přepínač directory**.

3. V adresáři nabídce v části **spravovat**, zvolte **registrace aplikace** > **nové registrace aplikace**.

   > [!TIP]
   > Ve výchozím nastavení v seznamu registrace aplikace jsou uvedeny všechny registrace aplikace ve vašem adresáři. Chcete-li zobrazit pouze registrace vaší aplikace, u pole hledání, vyberte **Moje aplikace**. 

   ![Vytvořit novou registraci aplikace](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Zadejte název vaší identity aplikace, ponechejte **typ aplikace** nastavena na **webovou aplikaci nebo rozhraní API**, zadejte jedinečný řetězec formátovaný jako doménu pro **přihlašovací adresa URL**a zvolte **vytvořit**.

   ![Zadejte název a adresu URL pro přihlašování identita aplikace](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   Identita aplikace, kterou jste vytvořili pro svou aplikaci logiky nyní se zobrazí v seznamu aplikací registrace.

   ![Identita aplikace pro svou aplikaci logiky](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. V seznamu registrace aplikace vyberte novou identitu aplikace. Zkopírujte a uložte **ID aplikace** má používat jako "ID klienta" pro svou aplikaci logiky v části 3.

   ![Zkopírujte a uložte ID aplikace pro aplikaci logiky](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Pokud nastavení identity aplikace nejsou zobrazeny, zvolte **nastavení** nebo **všechna nastavení**.

7. V části **přístup pomocí rozhraní API**, zvolte **klíče**. V části **popis**, zadejte název pro váš klíč. V části **Expires**, vyberte dobu trvání pro váš klíč.

   Klíč, který vytváříte funguje jako identita aplikace "tajný" nebo heslo pro svou aplikaci logiky.

   ![Vytvořte klíč pro identitu aplikace logiky](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Na panelu nástrojů vyberte **Uložit**. V části **hodnotu**, klíč se teď zobrazí. 
**Nezapomeňte zkopírovat a uložit klíč** pro pozdější použití vzhledem k tomu klíč je skrytý. Pokud ponecháte **klíče** stránky.

   Při konfiguraci aplikace logiky v rámci 3, zadáte tento klíč jako "tajný klíč" nebo heslo.

   ![Zkopírujte a uložte klíč pro pozdější](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Vytvoření identity aplikací pro svou aplikaci logiky v prostředí PowerShell**

Můžete provést tuto úlohu prostřednictvím Správce Azure Resource Manager pomocí prostředí PowerShell. V prostředí PowerShell spusťte tyto příkazy:

1. `Switch-AzureMode AzureResourceManager`

2. `Add-AzureAccount`

3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password "identity-password"`

4. Nezapomeňte zkopírovat **ID klienta** (identifikátor GUID pro vašeho tenanta Azure AD), **ID aplikace**a heslo, které jste použili.

Další informace, zjistěte, jak [vytvoření objektu služby pomocí prostředí PowerShell pro přístup k prostředkům](../azure-resource-manager/resource-group-authenticate-service-principal.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Část 2: Vytvoření identity aplikací Azure AD pro vaši webovou aplikaci nebo aplikace API

Pokud webovou aplikaci nebo aplikaci API je už nasazená, můžete zapnout ověření a vytvoření identity aplikace v portálu Azure. Jinak můžete [zapnout ověřování při nasazení pomocí šablony Azure Resource Manager](#authen-deploy). 

**Vytvoření identity aplikace a zapněte ověřování na portálu Azure pro nasazené aplikace**

1. V [portál Azure](https://portal.azure.com "https://portal.azure.com"), najděte a vyberte webovou aplikaci nebo aplikaci API. 

2. V části **nastavení**, zvolte **ověřování/autorizace**. V části **ověřování služby aplikace**, zapnout ověřování **na**. V části **zprostředkovatele ověřování**, zvolte **Azure Active Directory**.

   ![Zapnout ověřování](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Teď vytvořte identity aplikací pro webovou aplikaci nebo aplikaci API, jak je vidět tady. Na **nastavení Azure Active Directory** nastavte **režim správy** k **Express**. Zvolte **vytvořit novou aplikaci AD**. Zadejte název vaší identity aplikace a zvolte **OK**. 

   ![Vytvoření identity aplikací pro webovou aplikaci nebo aplikace API](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. Na stránce **Ověřování / autorizace** zvolte **Uložit**.

Nyní musí najít klienta ID a ID klienta pro identitu aplikací, který je spojen s webovou aplikaci nebo aplikaci API. Použijte tyto identifikátory v části 3. Pokračujte tak tyto kroky na portálu Azure.

**Najít ID klienta aplikace identit a ID klienta pro webovou aplikaci nebo aplikaci API na portálu Azure**

1. V části **zprostředkovatele ověřování**, zvolte **Azure Active Directory**. 

   ![Volba možnosti Azure Active Directory](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. Na **nastavení Azure Active Directory** nastavte **režim správy** k **Upřesnit**.

3. Kopírování **ID klienta**a uložte tento identifikátor GUID pro použití v části 3.

   > [!TIP] 
   > Pokud **ID klienta** a **Url vystavitele** nemáte objeví, zkuste aktualizovat na portálu Azure a zopakujte krok 1.

4. V části **Url vystavitele**, zkopírujte a uložte jenom identifikátor GUID pro část 3. Můžete také použít tento identifikátor GUID ve vaší webové aplikace nebo aplikace API šablonu nasazení, v případě potřeby.

   Tento identifikátor GUID je GUID konkrétní klienta ("ID klienta") a by se zobrazit v této adresy URL: `https://sts.windows.net/{GUID}`

5. Bez uložení změn, zavřete **nastavení Azure Active Directory** stránky.

<a name="authen-deploy"></a>

**Zapnout ověřování při nasazení pomocí šablony Azure Resource Manager**

Stále je nutné vytvořit identity aplikací Azure AD pro vaši webovou aplikaci nebo aplikaci API, která se liší od identity aplikace pro svou aplikaci logiky. Pokud chcete vytvořit identita aplikace, postupujte podle předchozí kroky v části 2 pro portál Azure. 

Můžete také postupujte podle kroků v části 1, ale nezapomeňte použít vaši webovou aplikaci nebo aplikaci API skutečné `https://{URL}` pro **přihlašovací adresa URL** a **identifikátor ID URI aplikace**. Z těchto kroků budete muset uložit ID klienta a ID klienta pro použití v šabloně nasazení vaší aplikace a také pro část 3.

> [!NOTE]
> Když vytvoříte identity aplikací Azure AD pro vaši webovou aplikaci nebo aplikaci API, musíte použít portál Azure, ne prostředí PowerShell. Prostředí PowerShell nemá nastavit požadovaná oprávnění pro přihlášení uživatelů do webu.

Po získání klienta ID a ID klienta, patří tyto identifikátory jako subresource vaší webové aplikace nebo aplikace API v šabloně nasazení:

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

Jak automaticky nasadit prázdnou webovou aplikaci a aplikace logiky společně s ověřování Azure Active Directory, [zobrazit úplnou šablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json), nebo klikněte na tlačítko **nasadit do Azure** tady:

[![Nasazení do Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Část 3: Naplnění části autorizace v aplikaci logiky

Předchozí šablona již v této části autorizace nastavit, ale vytváříte-li přímo aplikaci logiky, musí obsahovat části plnou autorizaci.

Otevřete svou definici. aplikaci logiky v zobrazení kódu, přejděte na **HTTP** části akci najít **autorizace** části a zahrnovat tento řádek:

`{"tenant": "{tenant-ID}", "audience": "{client-ID-from-Part-2-web-app-or-API app}", "clientId": "{client-ID-from-Part-1-logic-app}", "secret": "{key-from-Part-1-logic-app}", "type": "ActiveDirectoryOAuth" }`

| Element | Požaduje se | Popis | 
| ------- | -------- | ----------- | 
| tenant | Ano | Identifikátor GUID pro klienta služby Azure AD | 
| Cílová skupina | Ano | Identifikátor GUID pro cílový prostředek, který chcete získat přístup, což je ID klienta z identity aplikací pro webovou aplikaci nebo aplikace API | 
| clientId | Ano | Identifikátor GUID pro klienta žádají o přístup, což je ID klienta z aplikací identity pro svou aplikaci logiky | 
| Tajný kód | Ano | Klíč nebo heslo z identity aplikace pro klienta, který požaduje přístupový token | 
| type | Ano | Typ ověřování. Pro ověřování ActiveDirectoryOAuth hodnota je `ActiveDirectoryOAuth`. | 
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

### <a name="secure-api-calls-through-code"></a>Zabezpečená volání rozhraní API prostřednictvím kódu

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Ověřování certifikátem

K ověření příchozích požadavků z aplikace logiky webovou aplikaci nebo aplikaci API, můžete použít klientské certifikáty. Chcete-li nastavit kód, zjistěte další [konfiguraci vzájemné ověřování TLS](../app-service/app-service-web-configure-tls-mutual-auth.md).

V **autorizace** část, zahrnují tento řádek: 

`{"type": "clientcertificate", "password": "password", "pfx": "long-pfx-key"}`

| Element | Požaduje se | Popis | 
| ------- | -------- | ----------- | 
| type | Ano | Typ ověřování. Pro klientské certifikáty protokolu SSL, hodnota musí být `ClientCertificate`. | 
| heslo | Ano | Heslo pro přístup k certifikátu klienta (soubor PFX) | 
| Soubor PFX | Ano | Obsah s kódováním base64, pomocí certifikátu klienta (soubor PFX) | 
|||| 

<a name="basic"></a>

#### <a name="basic-authentication"></a>Základní ověřování

K ověření příchozích požadavků z aplikace logiky webovou aplikaci nebo aplikaci API, můžete základní ověřování, jako je například uživatelské jméno a heslo. Základní ověřování je běžný vzor, a toto ověřování můžete použít v libovolném jazyce sloužící k vytvoření webové aplikace nebo aplikace API.

V **autorizace** část, zahrnují tento řádek:

`{"type": "basic", "username": "username", "password": "password"}`.

| Element | Požaduje se | Popis | 
| ------- | -------- | ----------- | 
| type | Ano | Typ ověřování, který chcete použít. Pro základní ověřování, musí být hodnota `Basic`. | 
| uživatelské jméno | Ano | Uživatelské jméno, které chcete používat pro ověřování | 
| heslo | Ano | Heslo, které chcete použít pro ověřování | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Ověřování Azure Active Directory prostřednictvím kódu

Ve výchozím nastavení ověřování Azure AD, které můžete zapnout na portálu Azure neposkytuje podrobné autorizace. Toto ověřování například zamkne rozhraní API k jenom konkrétní klienta, nikoli k konkrétního uživatele nebo aplikace. 

Pokud chcete omezit přístup pomocí rozhraní API do aplikace logiky prostřednictvím kódu, extrahujte záhlaví, který má webového tokenu JSON (JWT). Zkontrolujte identitu volajícího a odmítnout požadavky, které se neshodují.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/app-service-authentication-overview.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Další postup

* [Nasazení a volání vlastním rozhraním API z logiky pracovní postupy aplikace](../logic-apps/logic-apps-custom-api-host-deploy-call.md)