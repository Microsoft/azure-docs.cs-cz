---
title: "Ochrana back-endu webového rozhraní API pomocí Azure Active Directory a API Management | Microsoft Docs"
description: "Zjistěte, jak k ochraně back-endu webového rozhraní API pomocí Azure Active Directory a API Management."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: b7fc48412799aea0c4bba971102b4912dbb18e05
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>Jak chránit, back-endu webového rozhraní API pomocí Azure Active Directory a API Management

Toto téma ukazuje, jak vytvářet back-end webového rozhraní API a chránit pomocí Azure Active Directory a rozhraní API správy protokolu OAuth 2.0.  

## <a name="create-an-azure-ad-directory"></a>Vytvořte adresář služby Azure AD
K zabezpečení vašeho webového rozhraní API back-end pomocí Azure Active Directory musíte nejprve mít klienta služby AAD. Vytvoření klienta služby AAD, přihlaste se do [portálu Azure Classic](https://manage.windowsazure.com) a klikněte na tlačítko **nový**->**App Services**->**služby Active Directory**->**Directory**->**vytvořit vlastní**. 

![Azure Active Directory][api-management-create-aad-menu]

V tomto příkladu adresář s názvem **APIMDemo** je vytvořena s výchozí doménu s názvem **DemoAPIM.onmicrosoft.com**. 

![Azure Active Directory][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>Vytvoření webového rozhraní API služby Zabezpečené přes Azure Active Directory
V tomto kroku se vytvoří webového rozhraní API back-end pomocí Visual Studio 2013. Vytvoření projektu webového rozhraní API back-end v sadě Visual Studio klikněte na položku **soubor**->**nový**->**projektu**a zvolte **webové aplikace ASP.NET** z **webové** seznamu šablon. 

![Visual Studio][api-management-new-web-app]

Klikněte na tlačítko **webového rozhraní API** z **vyberte seznam šablony** k vytvoření projektu webového rozhraní API. Chcete-li nakonfigurovat ověřování Azure Directory, klikněte na tlačítko **změna ověřování**.

![Nový projekt][api-management-new-project]

Klikněte na tlačítko **účty organizace**a zadejte **domény** klienta služby AAD. V tomto příkladu Doména je **DemoAPIM.onmicrosoft.com**. Můžete získat doménu adresáře **domén** svůj adresář.

![Domény][api-management-aad-domains]

Nakonfigurujte požadovaná nastavení v **změna ověřování** dialogové okno a klikněte na tlačítko **OK**.

![Změna ověřování][api-management-change-authentication]

Když kliknete na tlačítko **OK** Visual Studio se pokusí o aplikaci zaregistrovat u svého adresáře Azure AD a budete vyzváni k přihlášení pomocí sady Visual Studio. Přihlaste se pomocí účtu správce pro váš adresář.

![Přihlaste se k sadě Visual Studio][api-management-sign-in-vidual-studio]

Konfigurace tohoto projektu jako webového rozhraní API Azure, zaškrtněte políčko pro **hostitel v cloudu** a pak klikněte na **OK**.

![Nový projekt][api-management-new-project-cloud]

Můžete být vyzváni k přihlášení do Azure a pak můžete nakonfigurovat webové aplikace.

![Konfigurace][api-management-configure-web-app]

V tomto příkladu novou **plán služby App Service** s názvem **APIMAADDemo** je zadán.

Klikněte na tlačítko **OK** ke konfiguraci webové aplikace a vytvořte tak projekt.

## <a name="add-the-code-to-the-web-api-project"></a>Přidejte do projektu webového rozhraní API kód

Webové rozhraní API v tomto příkladu implementuje základní kalkulačky služby pomocí modelu a kontroler. Chcete-li přidat model pro službu, klikněte pravým tlačítkem **modely** v **Průzkumníku řešení** a zvolte **přidat**, **třída**. Název třídy `CalcInput` a klikněte na tlačítko **přidat**.

Přidejte následující `using` příkaz do horní části `CalcInput.cs` souboru.

```csharp
using Newtonsoft.Json;
```

Generovaná třída nahraďte následujícím kódem.

```csharp
public class CalcInput
{
    [JsonProperty(PropertyName = "a")]
    public int a;

    [JsonProperty(PropertyName = "b")]
    public int b;
}
```

Klikněte pravým tlačítkem na **řadiče** v **Průzkumníku řešení** a zvolte **přidat**->**řadič**. Zvolte **webové rozhraní API 2 řadiče - prázdný** a klikněte na tlačítko **přidat**. Typ **CalcController** pro řadič název a klikněte na tlačítko **přidat**.

![Přidání Kontroleru][api-management-add-controller]

Přidejte následující `using` příkaz do horní části `CalcController.cs` souboru.

```csharp
using System.IO;
using System.Web;
using APIMAADDemo.Models;
```

Třídy generované kontroleru nahraďte následujícím kódem. Tento kód implementuje `Add`, `Subtract`, `Multiply`, a `Divide` operace rozhraní API základní kalkulačky.

```csharp
[Authorize]
public class CalcController : ApiController
{
    [Route("api/add")]
    [HttpGet]
    public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/sub")]
    [HttpGet]
    public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/mul")]
    [HttpGet]
    public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/div")]
    [HttpGet]
    public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }
}
```

Stiskněte klávesu **F6** sestavení a ověřte řešení.

## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

K publikování tohoto projektu v Azure, klikněte pravým tlačítkem myši **APIMAADDemo** projektu v sadě Visual Studio a zvolte **publikovat**. Potvrďte výchozí nastavení **Publikovat Web** dialogové okno a klikněte na tlačítko **publikovat**.

![Publikování webu][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>Udělení oprávnění k back-end aplikace služby Azure AD
V adresáři služby Azure AD jako součást procesu konfigurace a publikování projektu webového rozhraní API je vytvořena nová aplikace pro back-end službu.

![Aplikace][api-management-aad-backend-app]

Klikněte na název aplikace a nakonfigurujte požadovaná oprávnění. Přejděte na **konfigurace** kartě a přejděte dolů k položce **oprávnění k ostatním aplikacím** části. Klikněte **oprávnění aplikací** rozevíracího seznamu vedle položky **Windows** **Azure Active Directory**, zaškrtněte políčko pro **čtení dat adresáře**a klikněte na tlačítko **Uložit**.

![Přidejte oprávnění.][api-management-aad-add-permissions]

> [!NOTE]
> Pokud **Windows** **Azure Active Directory** nejsou uvedené v části oprávnění k ostatním aplikacím, klikněte na tlačítko **přidat aplikaci** a přidejte ji ze seznamu.
> 
> 

Poznamenejte si **identifikátor Id URI aplikace** pro použití v následném kroku při aplikaci Azure AD je nakonfigurován pro portál pro vývojáře API Management.

![Id URI aplikace][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>Importovat webové rozhraní API do rozhraní API Management
Rozhraní API se konfigurují na rozhraní API portálu vydavatele, který je přístupný prostřednictvím portálu Azure. K dosažení ho, klikněte na tlačítko **portál vydavatele** na panelu nástrojů služby API Management. Pokud jste instanci služby API Management ještě nevytvořili, přečtěte si téma [vytvoření instance API Management] [ Create an API Management service instance] v [Správa vašeho prvního rozhraní API] [ Manage your first API] kurzu.

![Portál vydavatele][api-management-management-console]

Operace jde [ručně přidat do rozhraní API](api-management-howto-add-operations.md), nebo může být importován.

Vytvořte soubor s názvem `calcapi.json` s následující obsah a uložte ho do počítače. Ujistěte se, že `host` atribut body back-end vašeho webového rozhraní API. V tomto příkladu `"host": "apimaaddemo.azurewebsites.net"` se používá.

```json
{
  "swagger": "2.0",
  "info": {
    "title": "Calculator",
    "description": "Arithmetics over HTTP!",
    "version": "1.0"
  },
  "host": "apimaaddemo.azurewebsites.net",
  "basePath": "/api",
  "schemes": [
    "http"
  ],
  "paths": {
    "/add?a={a}&b={b}": {
      "get": {
        "description": "Responds with a sum of two numbers.",
        "operationId": "Add two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>51</code>.",
            "required": true,
            "type": "string",
            "default": "51",
            "enum": [
              "51"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>49</code>.",
            "required": true,
            "type": "string",
            "default": "49",
            "enum": [
              "49"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/sub?a={a}&b={b}": {
      "get": {
        "description": "Responds with a difference between two numbers.",
        "operationId": "Subtract two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>50</code>.",
            "required": true,
            "type": "string",
            "default": "50",
            "enum": [
              "50"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/div?a={a}&b={b}": {
      "get": {
        "description": "Responds with a quotient of two numbers.",
        "operationId": "Divide two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/mul?a={a}&b={b}": {
      "get": {
        "description": "Responds with a product of two numbers.",
        "operationId": "Multiply two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>5</code>.",
            "required": true,
            "type": "string",
            "default": "5",
            "enum": [
              "5"
            ]
          }
        ],
        "responses": { }
      }
    }
  }
}
```

Pokud chcete importovat rozhraní API kalkulačky, klikněte v nabídce **API Management** na levé straně na **Rozhraní API** a potom na **Importovat rozhraní API**.

![Tlačítko Importovat rozhraní API][api-management-import-api]

Proveďte následující kroky konfigurace rozhraní API kalkulačky.

1. Klikněte na tlačítko **ze souboru**, vyhledejte `calculator.json` soubor uložit a klikněte na tlačítko **Swagger** přepínač.
2. Typ **calc** do **přípona adresy URL webového rozhraní API** textové pole.
3. Klikněte do pole **Produkty (volitelné)** a zvolte **Starter**.
4. Kliknutím na **Uložit** naimportujte rozhraní API.

![Přidání nového rozhraní API][api-management-import-new-api]

Po importu rozhraní API se na portálu vydavatele zobrazí souhrnná stránka rozhraní.

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>Volání rozhraní API neúspěšně z portálu pro vývojáře
V tomto okamžiku rozhraní API byla naimportována do rozhraní API správy, ale nejde ještě volat úspěšně z portálu pro vývojáře protože službě back-end je chráněn pomocí ověřování Azure AD. 

Klikněte na tlačítko **portál pro vývojáře** z pravé horní části portálu vydavatele.

![Portál pro vývojáře][api-management-developer-portal-menu]

Klikněte na tlačítko **rozhraní API** a klikněte na **kalkulačky** rozhraní API.

![Portál pro vývojáře][api-management-dev-portal-apis]

Klikněte na tlačítko **vyzkoušet**.

![Vyzkoušet][api-management-dev-portal-try-it]

Klikněte na tlačítko **odeslat** a poznamenejte si stav odpovědi **401 – Neověřeno**.

![Odeslat][api-management-dev-portal-send-401]

Požadavek není autorizovaný, protože rozhraní API back-end je chráněn službou Azure Active Directory. Před úspěšně voláním rozhraní API vývojář portál musí být nakonfigurované k autorizaci vývojáře, kteří používají OAuth 2.0. Tento proces je popsán v následujících částech.

## <a name="register-the-developer-portal-as-an-aad-application"></a>Zaregistrujte se jako aplikaci AAD portál pro vývojáře
Prvním krokem při konfiguraci portálu pro vývojáře k autorizaci vývojáře, kteří používají OAuth 2.0 je k registraci portál pro vývojáře jako aplikaci AAD. 

Přejděte do klienta Azure AD. V tomto příkladu vyberte **APIMDemo** a přejděte do **aplikace** kartě.

![Nová aplikace][api-management-aad-new-application-devportal]

Klikněte **přidat** tlačítko Vytvořit novou aplikaci Azure Active Directory a vyberte **přidat aplikaci, kterou vyvíjí Moje organizace**.

![Nová aplikace][api-management-new-aad-application-menu]

Zvolte **webové aplikace nebo webové rozhraní API**, zadejte název a klikněte na šipku Další. V tomto příkladu **APIMDeveloperPortal** se používá.

![Nová aplikace][api-management-aad-new-application-devportal-1]

Pro **přihlašovací adresa URL** zadejte adresu URL služby API Management a připojit `/signin`. V tomto příkladu `https://contoso5.portal.azure-api.net/signin` se používá.

Pro **URL Id aplikace** zadejte adresu URL služby API Management a připojit některé jedinečných znaků. To může být jakékoli požadované znaky a v tomto příkladu `https://contoso5.portal.azure-api.net/dp` se používá. Pokud požadovaný **vlastností aplikace** jsou nakonfigurovaná, klikněte na tlačítko zaškrtnutí pro vytvoření aplikace.

![Nová aplikace][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>Konfigurace serveru autorizace OAuth 2.0 rozhraní API Management
Dalším krokem je konfigurace serveru autorizace OAuth 2.0 ve službě API Management. 

Klikněte na tlačítko **zabezpečení** nabídce API Management na levé straně klikněte na **OAuth 2.0**a potom klikněte na **přidat autorizační** serveru.

![Přidání serveru ověřování][api-management-add-authorization-server]

Zadejte název a volitelný popis v **název** a **popis** pole. Tato pole se používají k identifikaci serveru ověřování OAuth 2.0 v rámci instance služby API Management. V tomto příkladu **ukázkový server autorizace** se používá. Později při zadávání serveru OAuth 2.0, který se má použít pro ověření pro rozhraní API, vyberete tento název.

Pro **adresa URL stránky registrace klienta** zadejte hodnotu zástupného symbolu, jako `http://localhost`.  **Adresa URL stránky registrace klienta** odkazuje na stránku, uživatelé můžete vytvořit a nakonfigurovat svoje vlastní účty zprostředkovatelů OAuth 2.0, které podporují správu uživatelské účty. V tomto příkladu uživatele není vytvořit a nakonfigurovat svoje vlastní účty, tak se používá zástupný symbol.

![Přidání serveru ověřování][api-management-add-authorization-server-1]

Potom zadejte **adresu URL koncového bodu autorizace** a **adresu URL koncového bodu Token**.

![Autorizace serveru][api-management-add-authorization-server-1a]

Tyto hodnoty lze získat z **koncových bodů aplikace** stránky AAD aplikace, který jste vytvořili pro portál pro vývojáře. Chcete-li získat přístup k koncových bodů, přejděte na **konfigurace** AAD aplikace a klikněte na **zobrazit koncové body**.

![Aplikace][api-management-aad-devportal-application]

![Zobrazit koncové body][api-management-aad-view-endpoints]

Kopírování **koncový bod autorizace OAuth 2.0** a vložte ji do **adresu URL koncového bodu autorizace** textové pole.

![Přidání serveru ověřování][api-management-add-authorization-server-2]

Kopírování **koncový bod tokenu OAuth 2.0** a vložte ji do **adresu URL koncového bodu Token** textové pole.

![Přidání serveru ověřování][api-management-add-authorization-server-2a]

Kromě vkládání v koncový bod token, přidáte další text parametr s názvem **prostředků** a používat hodnotu **identifikátor Id URI aplikace** z AAD aplikace pro back-end službu, která byla vytvořena, když byla publikována projekt Visual Studio.

![Id URI aplikace][api-management-aad-sso-uri]

Potom zadejte pověření klienta. Jedná se o pověření pro prostředek, který má přístup, v tomto případě portál pro vývojáře.

![Přihlašovací údaje klienta][api-management-client-credentials]

Chcete-li získat **Id klienta**, přejděte na **konfigurace** AAD aplikace pro portál pro vývojáře a zkopírujte **Id klienta**.

Získat **tajný klíč klienta** klikněte na tlačítko **vyberte dobu trvání** rozevírací seznam v **klíče** části a zadat interval. V tomto příkladu se používá 1 rok.

![ID klienta][api-management-aad-client-id]

Klikněte na tlačítko **Uložit** zobrazí klíč a uložte konfiguraci. 

> [!IMPORTANT]
> Tento klíč si poznamenejte. Po zavření okna konfigurace Azure Active Directory, klíč nelze zobrazit znovu.
> 
> 

Klíč zkopírujte do schránky, přepněte zpět na portálu vydavatele, vložte klíč do **tajný klíč klienta** textovému poli a klikněte na tlačítko **Uložit**.

![Přidání serveru ověřování][api-management-add-authorization-server-3]

Okamžitě následující pověření klienta je udělení autorizačního kódu. Kopírování tento autorizační kód a přepínač zpět do aplikace Azure AD vývojáře portálu konfigurace stránky a vložte udělení autorizace do **adresa URL odpovědi** pole a klikněte na tlačítko **Uložit** znovu.

![Adresa URL odpovědi][api-management-aad-reply-url]

Dalším krokem je konfigurace oprávnění pro portál pro vývojáře AAD aplikace. Klikněte na tlačítko **oprávnění aplikací** a zaškrtněte políčko pro **čtení dat adresáře**. Klikněte na tlačítko **Uložit** uložte tuto změnu, a pak klikněte na **přidat aplikaci**.

![Přidejte oprávnění.][api-management-add-devportal-permissions]

Klikněte na ikonu hledání typu **APIM** do počáteční s poli, vyberte **APIMAADDemo**a klikněte na políčko pro uložení.

![Přidejte oprávnění.][api-management-aad-add-app-permissions]

Klikněte na tlačítko **delegovaná oprávnění** pro **APIMAADDemo** a zaškrtněte políčko pro **přístup APIMAADDemo**a klikněte na tlačítko **Uložit**. To umožňuje vývojáři aplikace portálu přístup ke službě back-end.

![Přidejte oprávnění.][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>Povolení autorizace uživatelů OAuth 2.0 pro rozhraní API kalkulačky
Teď, když server OAuth 2.0 je nakonfigurovaný, můžete v nastavení zabezpečení pro vaše rozhraní API. 

Klikněte na tlačítko **rozhraní API** v levé nabídce a klikněte na **kalkulačky** můžete zobrazit a konfigurovat jeho nastavení.

![API kalkulačky][api-management-calc-api]

Přejděte na **zabezpečení** zaškrtněte políčko **OAuth 2.0** zaškrtávací políčko, vyberte požadované autorizace server z **serveru ověřování** rozevíracího seznamu a klikněte na tlačítko **Uložit**.

![API kalkulačky][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>Rozhraní API kalkulačky úspěšně volejte z portálu pro vývojáře
Teď, když autorizace OAuth 2.0 je nakonfigurovaná na volání rozhraní API, můžete jeho operace úspěšně volat z středisku pro vývojáře. 

Přejděte zpět **přidat dvě celá čísla** operaci kalkulačky služby v portálu pro vývojáře a klikněte na **vyzkoušet**. Poznámka: novou položku **autorizace** části odpovídající serveru ověřování, který jste právě přidali.

![API kalkulačky][api-management-calc-authorization-server]

Vyberte **autorizační kód** z autorizace rozevíracího seznamu a zadejte přihlašovací údaje pro účet, který chcete použít. Pokud jste již přihlášeni pomocí účtu, nemusí se zobrazí výzva.

![API kalkulačky][api-management-devportal-authorization-code]

Klikněte na tlačítko **odeslat** a poznamenejte si **stav odpovědi** z **200 OK** a výsledky operace v obsahu odpovědi.

![API kalkulačky][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>Konfigurace aplikace pro volání rozhraní API

Nakonfigurujte jednoduché aplikace pracovní plochy pro volání rozhraní API. Prvním krokem je registrace klientů aplikace ve službě Azure AD a poskytněte přístup k adresáři a ke službě back-end. 

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Konfigurace zásad ověřování JWT předem autorizovat požadavků

Použití [ověření JWT](api-management-access-restriction-policies.md#ValidateJWT) zásad předem autorizovat požadavky ověřením přístupových tokenů každého příchozího požadavku. Pokud požadavek není ověřen pomocí zásad ověřování tokenů JWT, žádost je blokována API Management a není předají back-end.

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
        </claim>
    </required-claims>
</validate-jwt>
```

Další informace najdete v tématu [cloudu zahrnují díl 177: víc funkcí správy rozhraní API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) a rychlé převíjení vpřed na 13:50. Rychlé převinutí vpřed do 15:00 v tématu Zásady nakonfigurované v editoru zásad a potom do 18:50 pro předvedení volání operace z portálu pro vývojáře s i bez požadované autorizační token.

## <a name="next-steps"></a>Další postup
* Podívejte se na další [videa](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o službě API Management.
* Další způsoby zabezpečení back-end službu, naleznete v části [vzájemného ověření certifikátů](api-management-howto-mutual-certificates.md).

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Manage your first API]: import-and-publish.md
