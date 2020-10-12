---
title: Pomocí Azure Active Directory B2C a Azure API Management Chraňte back-end s protokolem OAuth 2,0.
description: Chraňte rozhraní API pomocí protokolu OAuth 2,0 pomocí Azure Active Directory B2C, Azure API Management a snadného ověření, které se má volat z JavaScript SPA.
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: wieastbu
ms.custom: fasttrack-new
ms.openlocfilehash: 60177dd00dc6326aae4cfdc0b658c85f2635f8c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86253690"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Ochrana SPA back-endu pomocí OAuth 2,0, Azure Active Directory B2C a Azure API Management

V tomto scénáři se dozvíte, jak nakonfigurovat instanci Azure API Management k ochraně rozhraní API.
Protokol OAuth 2,0 s Azure AD B2C společně API Management k zabezpečení Azure Functions back-endu pomocí EasyAuth.

## <a name="aims"></a>Usiluje
Zjistíme, jak se dá API Management použít ve zjednodušeném scénáři s Azure Functions a Azure AD B2C. Vytvoříte aplikaci JavaScript (JS), která volá rozhraní API, které se přihlásí uživatelům pomocí Azure AD B2C. Pak použijete funkce zásad Validate-JWT API Management k ochraně back-endu API.

Pro důkladnou obranu pak používáme EasyAuth k ověření tokenu v back-endu rozhraní API.

## <a name="prerequisites"></a>Požadavky
Pokud chcete postupovat podle kroků v tomto článku, musíte mít:
* Účet úložiště Azure (StorageV2) Pro obecné účely v2 pro hostování jednostránkové aplikace front-end JS
* Instance Azure API Management 
* Prázdná aplikace služby Azure Functions (s využitím modulu runtime v2 .NET Core v plánu spotřeby systému Windows) pro hostování rozhraní nazývaného rozhraní API
* Tenant Azure AD B2C, který je propojený s předplatným 

I když v praxi byste mohli použít prostředky ve stejné oblasti v produkčních úlohách, pro tento článek s postupem není důležité, aby tato oblast nasazení byla důležitá.

## <a name="overview"></a>Přehled
Tady je ilustrace používaných komponent a tok mezi nimi po dokončení tohoto procesu.
![Používané komponenty a flow](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Používané komponenty a flow")

Tady je rychlý přehled kroků:

1. Vytvoření Azure AD B2C volání (front-endu, API Management) a aplikací API s rozsahy a udělení přístupu k rozhraní API
1. Vytvořte zásady registrace nebo přihlašování a Umožněte uživatelům, aby se přihlásili pomocí Azure AD B2C 
1. Konfigurace API Management s novými Azure AD B2C ID klientů a klíčů, aby bylo možné povolit autorizaci uživatelů OAuth2 v konzole pro vývojáře
1. Sestavení rozhraní API funkcí
1. Nakonfigurujte rozhraní API funkce tak, aby povolovalo EasyAuth s Azure AD B2C novým ID a klíči klienta a aby se APIM VIP. 
1. Sestavení definice rozhraní API v API Management
1. Nastavení Oauth2 pro konfiguraci rozhraní API Management API
1. Nastavte zásadu **CORS** a přidejte zásadu **Validate-JWT** k ověření tokenu OAuth pro každý příchozí požadavek.
1. Sestavení volající aplikace pro využívání rozhraní API
1. Nahrání ukázky pro adresu JS SPA
1. Konfigurovat klientskou aplikaci Sample JS pomocí nového ID Azure AD B2C klienta a klíčů 
1. Testování klientské aplikace

## <a name="configure-azure-ad-b2c"></a>Konfigurace Azure AD B2C 
Otevřete okno Azure AD B2C na portálu a proveďte následující kroky.
1. Vyberte kartu **aplikace** . 
1. Klikněte na tlačítko Přidat a vytvořte tři aplikace pro následující účely.
   * Klient front-end.
   * Rozhraní API funkce back-end.
   * Volitelné Portál pro vývojáře API Management (Pokud nepoužíváte Azure API Management v úrovni spotřeby, další informace v tomto scénáři najdete později).
1. Nastavte WebApp/Web API pro všechny 3 aplikace a nastavte možnost ' Allow implicitní tok ' na Ano jenom pro klienta front-endu.
1. Teď nastavte identifikátor URI ID aplikace, vyberte něco jedinečného a důležitého pro vytvářenou službu.
1. Použijte zástupné symboly pro adresy URL odpovědí, pro které teď například https://localhost aktualizujeme tyto adresy URL později.
1. Klikněte na tlačítko vytvořit a opakujte kroky 2-5 pro každou ze tří výše uvedených aplikací, záznam identifikátoru URI, názvu a ID aplikace pro pozdější použití pro všechny tři aplikace.
1. Otevřete API Management aplikaci Portál pro vývojáře ze seznamu aplikací a vyberte kartu *klíče* (v části Obecné) a vygenerujte ověřovací klíč kliknutím na vygenerovat klíč.
1. Když kliknete na Uložit, poznamenejte si klíč někam do trezoru pro pozdější použití – mějte na paměti, že toto místo je jedinou pravděpodobností, abyste se dostali k zobrazení a zkopírování tohoto klíče.
1. Teď vyberte kartu *publikované obory* (v části přístup k rozhraní API).
1. Vytvořte a pojmenujte obor rozhraní API funkce a zaznamenejte obor a naplněnou hodnotu rozsahu a pak klikněte na Uložit.
   > [!NOTE]
   > Obory Azure AD B2C mají v rozhraní API efektivně oprávnění, která můžou jiné aplikace požádat o přístup prostřednictvím okna přístup přes rozhraní API ze svých aplikací, a efektivně jste právě vytvořili oprávnění aplikace pro vaše volané rozhraní API.
1. Otevřete další dvě aplikace a pak se podívejte na kartu *přístup přes rozhraní API* .
1. Udělte jim přístup k oboru rozhraní API back-endu a výchozímu serveru, který už má ("přístup k profilu uživatele").
1. Vygenerujte klíč tak, že vyberete kartu *klíče* v části Obecné, vygenerujete klíč ověření a tyto klíče si poznamenejte v bezpečí později.

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Vytvoření uživatelského toku pro registraci nebo přihlášení
1. Vraťte se do kořenového okna (nebo přehledu) okna Azure AD B2C 
1. Pak vyberte Toky uživatelů (zásady) a klikněte na nový tok uživatele.
1. Vyberte typ uživatelského toku registrace a přihlášení.
1. Zadejte název zásady a nahrajte ji pro pozdější verzi.
1. Pak v části zprostředkovatelé identity zaškrtněte políčko registrace uživatele (to může říci, že se jedná o E-mail) a klikněte na OK. 
1. V části atributy uživatelů a deklarace identity klikněte na Zobrazit další. pak zvolte možnosti deklarace identity, které chcete, aby vaši uživatelé zadali a vrátili v tokenu. Zkontrolujte aspoň zobrazované jméno a e-mailovou adresu, abyste mohli shromažďovat a vracet údaje, klikněte na OK a pak klikněte na vytvořit.
1. Vyberte zásadu, kterou jste vytvořili v seznamu, a pak klikněte na tlačítko spustit tok uživatele.
1. Tato akce otevře okno spustit tok uživatele, vyberte front-end aplikaci a potom si poznamenejte adresu domény b2clogin.com, která je zobrazená pod rozevíracím seznamem pro možnost Vybrat doménu.
1. Kliknutím na odkaz v horní části otevřete "dobře známý koncový bod konfigurace OpenID" a zaznamenejte hodnoty authorization_endpoint a token_endpoint a také hodnotu samotného odkazu, jako je známý koncový bod konfigurace OpenID.

   > [!NOTE]
   > Zásady B2C umožňují zobrazit koncové body přihlášení Azure AD B2C, aby bylo možné zachytit různé datové komponenty a přihlašovat uživatele různými způsoby. V tomto případě jsme nakonfigurovali koncový bod registrace nebo přihlášení, který vystavil známou známku konfigurace, konkrétně naše vytvořená zásada byla v adrese URL identifikována parametrem p =.
   > 
   > Až to bude hotové – teď máte funkční firemní platformu pro identitu spotřebitelů, která bude podepisovat uživatele do více aplikací. 
   > Pokud chcete, můžete kliknout na tlačítko spustit tok uživatele (Pokud chcete projít proces registrace nebo přihlášení) a získat dojem, co v praxi provede, ale krok přesměrování na konci selže, protože aplikace ještě není nasazená.

## <a name="build-the-function-api"></a>Sestavení rozhraní API funkcí
1. V Azure Portal přepněte zpátky na standardního tenanta Azure AD, abychom mohli znovu nakonfigurovat položky v předplatném. 
1. V okně funkce aplikace v Azure Portal otevřete prázdnou aplikaci funkcí a pak vytvořte novou In-Portal funkci Webhook + API pomocí rychlého startu.
1. Vložte ukázkový kód níže do souboru run. csx, který se zobrazí v existujícím kódu.

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!NOTE]
   > Kód funkce skriptu jazyka c#, který jste právě vložili, jednoduše zaznamená řádek do protokolů functions a vrátí text "Hello World" s některými dynamickými daty (datum a čas).

3. V levém okně vyberte "Integration" a potom v pravém horním rohu podokna vyberte ' Rozšířený editor '.
4. Vložte vzorový kód uvedený níže přes existující formát JSON.

   ```json
   {
      "bindings": [
       {
        "authLevel": "function",
        "name": "req",
        "type": "httpTrigger",
        "direction": "in",
        "methods": [
           "get"
        ],
        "route": "hello"
       },
       {
         "name": "$return",
         "type": "http",
         "direction": "out"
       }
     ]
   }
   ```

5. Přepněte zpátky na kartu HttpTrigger1, klikněte na získat adresu URL funkce a potom zkopírujte adresu URL, která se zobrazí.

   > [!NOTE]
   > Vazby, které jste právě vytvořili, jednoduše říkají funkcím, aby odpovídaly anonymním žádostem HTTP GET na adresu URL, kterou jste právě zkopírovali. ( `https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey` ) Teď máme škálovatelné rozhraní API HTTPS bez serveru, které dokáže vracet velmi jednoduchou datovou část.
   > Nyní můžete testovat volání tohoto rozhraní API z webového prohlížeče pomocí výše uvedené adresy URL, můžete také přesměrovat "kód = tajná část adresy URL a prokázat, že Azure Functions vrátí chybu 401.

## <a name="configure-and-secure-the-function-api"></a>Konfigurace a zabezpečení rozhraní API funkcí
1. Je třeba nakonfigurovat dvě další oblasti v aplikaci Function App (omezení ověření a sítě).
1. Nejdřív nakonfigurujeme ověřování/autorizaci, takže kliknete na název aplikace Function App (vedle &lt; &gt; ikony funkce Z) a zobrazí se stránka s přehledem.
1. Dále vyberte kartu funkce platformy a vyberte možnost ověřování/autorizace.
1. Zapněte funkci ověřování App Service.
1. V části zprostředkovatelé ověřování vyberte možnost Azure Active Directory a v přepínači režim správy vyberte možnost Upřesnit.
1. Vložte ID aplikace rozhraní API funkce back-end (z Azure AD B2C do pole ID klienta).
1. Do pole Adresa URL vystavitele vložte bod konfigurace známého Open-ID v zásadách registrace nebo přihlašování (Tato konfigurace jsme dříve zaznamenali).
1. Vyberte OK.
1. Nastavte akci, která se má provést, když se v rozevíracím seznamu požadavek neověřuje – Přihlaste se pomocí Azure Active Directory a pak klikněte na Uložit.

   > [!NOTE]
   > Rozhraní API funkcí je teď nasazené a má vyvolat 401 odpovědí, pokud není zadaný správný klíč, a měl by vracet data při předložení platné žádosti.
   > Do EasyAuth jste přidali další zabezpečení obrany tím, že nakonfigurujete možnost přihlášení pomocí služby Azure AD pro zpracování neověřených požadavků. Mějte na paměti, že tato akce změní chování neautorizovaných žádostí mezi back-end Function App a front-endu SPA, protože EasyAuth vydá neautorizovanou odpověď 302 na 401 AAD, a to pomocí API Management později.
   > Pořád ještě nemáte žádné použití zabezpečení IP, pokud máte platný klíč a OAuth2 token, kdokoli ho může zavolat odkudkoli – ideálně chceme vynutit, aby všechny žádosti byly pořízeny prostřednictvím API Management.
   > Pokud používáte úroveň spotřeby API Management, nebudete moct toto uzamčení vytvořit pomocí VIP, protože pro tuto vrstvu neexistuje žádná vyhrazená statická IP adresa, budete se muset spolehnout na metodu, kterou volání rozhraní API zamkne prostřednictvím klíče sdíleného tajného klíče, takže kroky 11-13 nebudou možné.

1. Zavřete okno ověřování/autorizace. 
1. Vyberte síť a pak vyberte omezení přístupu.
1. V dalším kroku podržíte povolené IP adresy aplikace Function App na VIP instance API Management. Tato virtuální IP adresa se zobrazí v části Správa rozhraní API – přehled na portálu.
1. Pokud chcete pokračovat v interakci s portálem functions a provést volitelné kroky níže, měli byste sem přidat vlastní veřejnou IP adresu nebo rozsah CIDR.
1. Jakmile je položka Povolit v seznamu, Azure přidá implicitní pravidlo odepření pro blokování všech ostatních adres. 

Na panel omezení IP adres budete muset přidat bloky s formátovaným směrováním CIDR. Pokud potřebujete přidat jednu adresu, například API Management VIP, je nutné ji přidat ve formátu xx. xx. xx. xx.

   > [!NOTE]
   > Rozhraní API funkcí by teď nemělo být možné volat odkudkoli, než prostřednictvím služby API Management, nebo vaší adresy.
   
## <a name="import-the-function-app-definition"></a>Import definice aplikace Function App
1. Otevřete okno *API Management*a pak otevřete *vaši instanci*.
1. V části API Management vaší instance vyberte okno rozhraní API.
1. V podokně Přidat nové rozhraní API zvolte Function App a pak v horní části automaticky otevíraného okna vyberte plná.
1. Klikněte na Procházet, vyberte aplikaci Function App, do které rozhraní API hostuje, a klikněte na vybrat.
1. Dejte rozhraní API název a popis interního použití API Management a přidejte ho do produktu Unlimited.
1. Ujistěte se, že jste si zaznamenali základní adresu URL pro pozdější použití, a pak klikněte na vytvořit.

## <a name="configure-oauth2-for-api-management"></a>Konfigurace Oauth2 pro API Management

1. Pak na kartě zabezpečení vyberte okno OAuth 2,0 a klikněte na Přidat.
1. Zadejte hodnoty pro *zobrazované jméno* a *Popis* pro přidaný koncový bod OAuth (tyto hodnoty se budou zobrazovat v dalším kroku jako Oauth2 koncový bod).
1. Do adresy URL stránky pro registraci klienta můžete zadat libovolnou hodnotu, protože tato hodnota nebude použita.
1. Ověřte typ udělení *implicitního ověřování* a ponechte zaškrtnuté políčko typ udělení autorizačního kódu.
1. Přejděte do pole *autorizace* a koncový bod *tokenu* a zadejte hodnoty, které jste si poznamenali v dřívějším známém konfiguračním dokumentu XML.
1. Posuňte se dolů a naplňte *parametr těla* s názvem Resource pomocí ID klienta rozhraní API funkce back-end z registrace aplikace Azure AD B2C.
1. Vyberte možnost pověření klienta, nastavte ID klienta na ID aplikace konzoly pro vývojáře – tento krok přeskočte, pokud používáte API Management model spotřeby.
1. Nastavte tajný klíč klienta na klíč, který jste si poznamenali dříve – tento krok přeskočte, pokud používáte API Management model spotřeby.
1. Nakonec nyní zaznamenejte redirect_uri udělení ověřovacího kódu z API Management pro pozdější použití.

## <a name="set-up-oauth2-for-your-api"></a>Nastavení Oauth2 pro vaše rozhraní API
1. Vaše rozhraní API se zobrazí na levé straně portálu v části všechna rozhraní API, otevřete své rozhraní API kliknutím na něj.
1. Vyberte kartu nastavení.
1. Aktualizujte nastavení tak, že v přepínači autorizace uživatele vyberete OAuth 2,0.
1. Vyberte server OAuth, který jste definovali dříve.
1. Zaškrtněte políčko Přepsat rozsah a zadejte obor, který jste si poznamenali pro volání rozhraní API back-endu dříve v.

   > [!NOTE]
   > Teď máme instanci API Management, která ví, jak získat přístupové tokeny z Azure AD B2C k autorizaci požadavků a pochopení naší konfigurace Oauth2 Azure Active Directory B2C.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Nastavení zásad **CORS** a **Validate-JWT**

> Následující části by měly následovat bez ohledu na použitou úroveň APIM. 

1. Přepněte zpátky na kartu Návrh a zvolte všechna rozhraní API a potom kliknutím na tlačítko zobrazení kódu zobrazte Editor zásad.
1. Upravte vstupní část a vložte následující kód XML, aby vypadal jako následující.

   ```xml
   <inbound>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
         <openid-config url="https://tenant.b2clogin.com/tenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_MyDefaultPolicy" />
         <required-claims>
            <claim name="aud">
               <value>your-backend-api-application-client-id</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <cors>
         <allowed-origins>
            <origin>*</origin>
         </allowed-origins>
         <allowed-methods>
           <method>GET</method>
         </allowed-methods>
         <allowed-headers>
            <header>*</header>
         </allowed-headers>
         <expose-headers>
           <header>*</header>
         </expose-headers>
       </cors>
   </inbound>
   ```
1. Upravte adresu URL OpenID-config tak, aby se shodovala se známým Azure AD B2Cm koncovým bodem pro zásady registrace nebo přihlašování.
1. Upravte hodnotu deklarace identity tak, aby odpovídala platnému ID aplikace, označované také jako ID klienta pro aplikaci API back-endu a uložte.

   > [!NOTE]
   > Teď může funkce API Management reagovat na žádosti mezi požadavky na různé zdroje i na aplikace na úrovni JS, a až do předání požadavku na rozhraní API, bude se předávat omezení četnosti a předběžného ověření tokenu JWT.

   > [!NOTE]
   > Následující část je volitelná a nevztahuje se na úroveň **spotřeby** , která nepodporuje portál pro vývojáře.
   > Pokud nemáte v úmyslu používat portál pro vývojáře, nebo ho nemůžete použít, protože používáte úroveň spotřeby, přeskočte tento krok a přejděte rovnou na ["sestavení JavaScriptu pro použití rozhraní API"](#build-the-javascript-spa-to-consume-the-api).

## <a name="optional-configure-the-developer-portal"></a>Volitelné Konfigurace portálu pro vývojáře

1. Otevřete okno Azure AD B2C a přejděte k registraci aplikace portálu pro vývojáře.
1. Nastavte položku Adresa URL odpovědi na tu, kterou jste si poznamenali, když jste nakonfigurovali redirect_uri udělení ověřovacího kódu v API Management dříve.

   Když je teď v konzole povolená autorizace uživatele OAuth 2,0 `Echo API` , před voláním rozhraní API získá Konzola pro vývojáře přístupový token pro uživatele.

1. Přejděte na libovolnou operaci na `Echo API` portálu pro vývojáře a vyberte **vyzkoušet** , abyste vás mohli přenést do konzoly pro vývojáře.
1. Poznamenejte si novou položku v sekci **autorizace** , která odpovídá autorizačnímu serveru, který jste právě přidali.
1. V rozevíracím seznamu autorizace vyberte **autorizační kód** a zobrazí se výzva, abyste se přihlásili do TENANTA Azure AD. Pokud jste již k účtu přihlášeni, pravděpodobně nebudete vyzváni.
1. Po úspěšném přihlášení se `Authorization: Bearer` do žádosti přidá hlavička s přístupovým tokenem z Azure AD B2C kódovaným v kódování Base64. 
1. Vyberte **Odeslat** a můžete volat rozhraní API úspěšně.

   > [!NOTE]
   > API Management teď dokáže získat tokeny pro portál pro vývojáře, aby otestovali vaše rozhraní API a bylo schopné pochopit jeho definici a na portálu pro vývojáře vykreslit příslušnou zkušební stránku.

1. V okně Přehled portálu API Management klikněte na portál pro vývojáře, abyste se přihlásili jako správce rozhraní API.
1. Tady můžete vy a ostatní Vybraní uživatelé vašeho rozhraní API testovat a volat je z konzoly.
1. Vyberte ' Products ', pak zvolte ' neomezeno ', pak vyberte rozhraní API, které jsme vytvořili dříve, a klikněte na tlačítko ' vyzkoušet '.
1. Zobrazte si klíč předplatného rozhraní API a zkopírujte ho někam do trezoru spolu s adresou URL požadavku, kterou později budete potřebovat.
1. V rozevíracím seznamu ověření OAuth vyberte taky možnost implicitní, takže se tady budete muset ověřit pomocí automaticky otevíraného okna.
1. Klikněte na Odeslat, pokud jsou všechny správné, Function App by měl odpovědět se zprávou Hello prostřednictvím služby API Management pomocí zprávy 200 OK a nějakého formátu JSON.

   > [!NOTE]
   > Blahopřejeme, nyní máte Azure AD B2C, API Management a Azure Functions spolupracujete, abyste mohli publikovat, zabezpečit a využívat rozhraní API. Možná jste si všimli, že rozhraní API je ve skutečnosti zabezpečeno dvakrát pomocí této metody, jednou s hlavičkou API Management OCP-předplatné-Key a jednou s autorizací: nosič JWT.
   > Byli jste správný, protože tento příklad je aplikace s jednoduchou stránkou JavaScriptu, používáme API Management klíč jenom pro volání omezování četnosti a fakturace.
   > Vlastní autorizaci a ověřování zpracovává Azure AD B2C a je zapouzdřeno v tokenu JWT, který je ověřen dvakrát, jednou pomocí API Management a poté podle Azure Functions.

## <a name="build-the-javascript-spa-to-consume-the-api"></a>Sestavení JavaScriptu pro použití rozhraní API
1. Otevřete okno účty úložiště v Azure Portal 
1. Vyberte účet, který jste vytvořili, a v části nastavení vyberte okno "statický web" (Pokud nevidíte možnost "statický web"), zkontrolujte, že jste vytvořili účet v2.
1. Nastavte funkci statického webového hostování na povoleno a nastavte název dokumentu indexu na ' index.html ' a pak klikněte na Uložit.
1. Poznamenejte si obsah primárního koncového bodu, jako je to místo, kde se nachází front-end lokalita. 

   > [!NOTE]
   > Můžete použít přepisování Azure Blob Storage + CDN nebo Azure App Service, ale funkce hostování statického webu v Blob Storage poskytuje výchozí kontejner pro poskytování statického webového obsahu/HTML/js/CSS od Azure Storage a odvozuje výchozí stránku pro nás pro nulovou práci.

## <a name="upload-the-js-spa-sample"></a>Nahrání ukázky pro adresu JS SPA
1. Pořád v okně účet úložiště vyberte okno objekty BLOB z části Služba BLOB Service a klikněte na kontejner $web, který se zobrazí v pravém podokně.
1. Níže uvedený kód níže uložte do souboru místně na svém počítači index.html a pak soubor nahrajte index.html do kontejneru $web.

   ```html
   <!doctype html>
   <html lang="en">
   <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Sample JS SPA</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
   </head>
   <body>
        <div class="container-fluid">
            <div class="row">
                <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-light bg-light navbar-dark bg-dark">
                        <a class="navbar-brand" href="#">Sample Code</a>
                        <ul class="navbar-nav ml-md-auto">
                            <li class="nav-item dropdown">
                                <a class="btn btn-large btn-success" onClick="login()">Sign In</a>
                            </li>
                        </ul>
                    </nav>
                </div>
            </div>
            <div class="row">
                <div class="col-md-12">
                    <div class="jumbotron">
                        <h2>
                    <div id="message">Hello, world!</div>
                    </h2>
                        <p>
                            <a class="btn btn-primary btn-large" onClick="GetAPIData()">Call API</a>
                        </p>
                    </div>
                </div>
            </div>
        </div>
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
        <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
        <script lang="javascript">
            var applicationConfig = {
                clientID: "clientidgoeshere",
                authority: "https://tenant.b2clogin.com/tfp/tenant/policy",
                b2cScopes: ["https://tenant/app/scope"],
                webApi: 'http://functionurl',
                subKey: 'apimkeygoeshere'
            };
            var msalConfig = {
                auth: {
                        clientId: applicationConfig.clientID, 
                        authority:  applicationConfig.authority,
                        validateAuthority: false
                },
                cache: {
                        cacheLocation: "localStorage",
                        storeAuthStateInCookie: true
                }
            };
            var clientApplication = new Msal.UserAgentApplication(msalConfig);
            function login() {
                var loginRequest = {
                    scopes: applicationConfig.b2cScopes
                };
                clientApplication.loginPopup(loginRequest).then(function (loginResponse) {
                    var tokenRequest = {
                        scopes: applicationConfig.b2cScopes
                    };
                    clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                        document.getElementById("signinbtn").innerHTML = "Logged in as: " + clientApplication.account.name;
                        document.getElementById("callapibtn").hidden = false
                        }).catch(function (error) {
                            clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                                }).catch (function (error) {
                                    console.log("Error acquiring the popup:\n" + error);
                                });
                        })
                    }).catch (function (error) {
                        console.log("Error during login:\n" + error);
                });
            }
            function GetAPIData() {
                var tokenRequest = {
                    scopes: applicationConfig.b2cScopes
                }
                clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                    callApiWithAccessToken(tokenResponse.accessToken);
                    }).catch(function (error) {
                        clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                            callApiWithAccessToken(tokenResponse.accessToken);
                            
                        }).catch(function (error) {
                            console.log("Error acquiring the access token to call the Web api:\n" + error);
                        });
                    })
            }
            function callApiWithAccessToken(token)
            {
                console.log("calling "  + applicationConfig.webApi +  " with " + token);
                    // Make the api call here
                $.ajax({
                    type: "get",
                    headers: {'Authorization': 'Bearer ' + token, 'Ocp-Apim-Subscription-Key': applicationConfig.subKey},                   url: applicationConfig.webApi
                }
                ).done(function (body) {
                    document.getElementById("message").innerHTML = "The API Said " + body;
                });
            }
        </script>
    </body>
   </html>
   
   ```

1. Přejděte na primární koncový bod statického webu, který jste předtím uložili v poslední části.

   > [!NOTE]
   > Gratulujeme, právě jste nasadili jednoduchou stránku JavaScriptu na Azure Storage, protože jsme aplikaci JS nenakonfigurovali pomocí vašich klíčů pro rozhraní API nebo nakonfigurovali aplikaci JS pomocí vašich Azure AD B2C detailů – stránka se ještě nepracuje, pokud ji otevřete.

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>Konfigurace zabezpečeného hesla pro Azure AD B2C JS
1. Teď víme, kde všechno je: můžeme nakonfigurovat zabezpečené ověřování pomocí odpovídající adresy rozhraní API API Management a správné Azure AD B2C ID aplikace/klienta.
1. Vraťte se do okna úložiště Azure Portal a klikněte na index.html a pak zvolte Upravit objekt BLOB. 
1. Aktualizujte podrobnosti ověření tak, aby odpovídaly front-endové aplikaci, kterou jste předtím zaregistrovali v B2C a zaznamenali, že hodnoty "b2cScopes" jsou pro back-end rozhraní API.
1. Klíč webApi a adresu URL API najdete v API Management testovacím podokně pro operaci rozhraní API.
1. Vytvořte klíč předplatného APIM podle nadpisu API Management zpátky do okna API Management, vyberte předplatné a pak klikněte na Přidat předplatné a pak záznam uložte. Kliknutím na tlačítko se třemi tečkami (...) vedle vytvořeného řádku umožníte zobrazení klíčů, abyste mohli primární klíč zkopírovat.
1. Měl by vypadat nějak jako v následujícím kódu:-  

   ```javascript
   var applicationConfig =
      clientID: "{aadb2c-clientid-goeshere}",
      authority: "https://{tenant}.b2clogin.com/{tenant}/{policy}",
      b2cScopes: ["https://{tenant}/{app}/{scope}"], 
      webApi: 'http://{apim-url-for-your-function}',
      subKey: '{apim-subscription-key-goes-here}'
   };
   ```

1. Kliknutí na Uložit

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Nastavení identifikátorů URI pro přesměrování pro aplikaci Azure AD B2C front-endu
1. Otevřete okno Azure AD B2C a přejděte k registraci aplikace pro aplikaci JavaScript front-endu.
1. Nastavte adresu URL pro přesměrování na tu, kterou jste si poznamenali dříve, kdy jste předtím nastavili primární koncový bod statického webu.

   > [!NOTE] 
   > Výsledkem této konfigurace bude klient aplikace front-end, který obdrží přístupový token s příslušnými deklaracemi identity z Azure AD B2C.
   > Zabezpečené ověřování hesla bude možné v hlavičce HTTPS v volání rozhraní API back-endu přidat toto jako nosný token.
   > API Management před předáním žádosti do přijímajícího rozhraní API Azure Functions předá požadavek na koncový bod pro volání do koncového bodu.
   > Zabezpečené ověřování hesla vygeneruje odpověď v prohlížeči.

   > *Gratulujeme, nakonfigurovali jste Azure AD B2C, Azure API Management, Azure Functions, Azure App Service autorizaci pro práci v dokonalé harmonii!*

   > [!NOTE]
   > Teď máme jednoduchou aplikaci s jednoduchým zabezpečeným rozhraním API a zkusíme ji otestovat.

## <a name="test-the-client-application"></a>Testování klientské aplikace
1. Otevřete ukázkovou adresu URL aplikace, kterou jste si poznamenali z účtu úložiště, který jste vytvořili dříve.
1. V pravém horním rohu klikněte na Přihlásit se a na toto tlačítko se zobrazí okno Azure AD B2C registraci nebo přihlášení k profilu.
1. Možnost po přihlášení v části přihlášený jako na obrazovce bude naplněna z tokenu JWT.
1. Nyní klikněte na zavolat webové rozhraní API a stránka by se měla aktualizovat o hodnoty odesílané zpátky ze zabezpečeného rozhraní API.

## <a name="and-were-done"></a>A hotovi
Výše uvedené kroky lze upravit a upravit tak, aby bylo možné v API Management použít řadu různých použití Azure AD B2C.

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [Azure Active Directory a OAuth 2.0](../active-directory/develop/authentication-vs-authorization.md).
* Podívejte se na další [videa](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o API Management.
* Další způsoby zabezpečení back-endové služby najdete v tématu [vzájemné ověřování certifikátů](api-management-howto-mutual-certificates.md).
* [Vytvořte instanci služby API Management](get-started-create-service-instance.md).
* [Spravujte své první rozhraní API](import-and-publish.md).
