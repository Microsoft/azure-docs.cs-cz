---
title: Chraňte back-end spa pomocí OAuth 2.0 pomocí Azure Active Directory B2C a Azure API Management.
description: Chraňte rozhraní API pomocí OAuth 2.0 pomocí Azure Active Directory B2C, Azure API Management a Easy Auth, které mají být volány z JavaScript SPA.
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
ms.openlocfilehash: 55acea360de11c5fcc699d65daf92cf24dfd691d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475472"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Ochrana backendu SPA pomocí OAuth 2.0, Azure Active Directory B2C a Azure API Management

Tento scénář ukazuje, jak nakonfigurovat instanci Azure API Management k ochraně rozhraní API.
Protokol OAuth 2.0 s Azure AD B2C spolu se správou rozhraní API použijeme k zabezpečení back-endu Azure Functions pomocí EasyAuth.

## <a name="aims"></a>Cíle
Uvidíme, jak se správa rozhraní API dá používat ve zjednodušeném scénáři s funkcemi Azure a Azure AD B2C. Vytvoříte aplikaci JavaScript (JS), která volá rozhraní API, které se přihlásí k uživatelům pomocí Azure AD B2C. Pak budete používat funkce zásad ověření-jwt správy rozhraní API k ochraně rozhraní BACKEND API.

Pro obranu do hloubky pak použijeme EasyAuth k ověření tokenu znovu uvnitř rozhraní API back-end.

## <a name="prerequisites"></a>Požadavky
Chcete-li postupovat podle kroků v tomto článku, musíte mít:
* Účet úložiště Azure (StorageV2) pro obecné účely V2 pro hostování aplikace JS Single Page App pro front-end
* Instance správy rozhraní API Azure 
* Prázdná aplikace Azure Function (spuštění runtime Jádra V2 .NET, v plánu spotřeby windows) pro hostování volavovaného rozhraní API
* Tenant Azure AD B2C propojený s předplatným 

I když v praxi byste používat prostředky ve stejné oblasti v produkčních úlohách, pro tento článek s postupy oblast nasazení není důležité.

## <a name="overview"></a>Přehled
Zde je ilustrace součásti v provozu a tok mezi nimi po dokončení tohoto procesu.
![Komponenty v provozu a toku](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Komponenty v provozu a toku")

Zde je stručný přehled kroků:

1. Vytvoření volání Azure AD B2C (front-end, správa rozhraní API) a aplikace rozhraní API s obory a udělení přístupu rozhraní API
1. Vytvoření zásad registrace nebo přihlášení, které uživatelům umožní přihlásit se pomocí Azure AD B2C 
1. Konfigurace správy rozhraní API s novými ID klienta Azure AD B2C a klíči pro povolení autorizace uživatelů OAuth2 v konzole pro vývojáře
1. Vytvoření rozhraní API funkcí
1. Konfigurace rozhraní API funkcí pro povolení easyauth s novým ID a klíči klienta Azure AD B2C a uzamčení na APIM VIP 
1. Vytvoření definice rozhraní API ve správě rozhraní API
1. Nastavení Oauth2 pro konfiguraci rozhraní API API pro správu rozhraní API
1. Nastavte zásady **CORS** a přidejte zásady **validate-jwt** k ověření tokenu OAuth pro každý příchozí požadavek
1. Vytvoření volající aplikace pro využití rozhraní API
1. Nahrát ukázku JS SPA
1. Konfigurace ukázkové klientské aplikace JS s novým ID a klíči klienta Azure AD B2C 
1. Testování klientské aplikace

## <a name="configure-azure-ad-b2c"></a>Konfigurace Azure AD B2C 
Otevřete okno Azure AD B2C na portálu a proveďte následující kroky.
1. Výběr karty **Aplikace** 
1. Klikněte na tlačítko "Přidat" a vytvořte tři aplikace pro následující účely
   * Frontend klienta.
   * Rozhraní API back-endové funkce.
   * [Nepovinné] Portál pro vývojáře správy rozhraní API (pokud nespouštěte azure api management v vrstvě spotřeby, další informace o tomto scénáři později).
1. Nastavte webapp / webové rozhraní API pro všechny 3 aplikace a nastavte "Povolit implicitní tok" na ano pouze pro front-end klienta.
1. Teď nastavte identifikátor URI ID aplikace, zvolte něco jedinečného a relevantního pro vytvářenou službu.
1. Použijte zástupné symboly pro odpovědi https://localhosturl pro tuto chvíli, jako je například , budeme aktualizovat tyto adresy URL později.
1. Klikněte na tlačítko Vytvořit a opakujte kroky 2 až 5 pro každou ze tří výše uvedených aplikací a zaznadejte identifikátor URI, název a ID aplikace pro pozdější použití pro všechny tři aplikace.
1. Otevřete aplikaci Portál pro správu vývojářů rozhraní API ze seznamu aplikací a vyberte kartu *Klíče* (v části Obecné) a kliknutím na tlačítko Generovat klíč vygenerujte auth klíč
1. Po kliknutí uložit, zaznamenat klíč někde bezpečné pro pozdější použití - všimněte si, že toto místo je jediná šance se dostanete k zobrazení a zkopírovat tento klíč.
1. Nyní vyberte kartu *Publikované obory* (v části Přístup k rozhraní API)
1. Vytvořte a pojmenujte obor pro rozhraní API funkcí a zaznamenejte obor a vyplněnou hodnotu celého rozsahu a klikněte na tlačítko Uložit.
   > [!NOTE]
   > Obory Azure AD B2C jsou efektivně oprávnění v rámci vašeho rozhraní API, které ostatní aplikace mohou požádat o přístup prostřednictvím přístupového okna rozhraní API z jejich aplikací, efektivně jste právě vytvořili oprávnění aplikací pro vaše nazývané rozhraní API.
1. Otevřete další dvě aplikace a pak se podívejte na kartu *Přístup k rozhraní API.*
1. Udělit jim přístup k oboru rozhraní API back-endu a výchozí, který již byl k dispozici ("Přístup k profilu uživatele").
1. Vygenerujte je klíč každý výběrem *klíče* kartu pod 'Obecné' generovat auth klíč a zaznamenat tyto klíče někde v bezpečí na později.

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Vytvoření toku uživatele "Zaregistrovat se nebo Přihlásit"
1. Návrat do kořenového adresáře (nebo "Přehled") blade Azure AD B2C 
1. Pak vyberte "Toky uživatelů (zásady)" a klikněte na "Tok nového uživatele"
1. Zvolte typ toku uživatele "Zaregistrovat se a přihlásit"
1. Pojmenujte zásadu a zaznamenejte ji na později.
1. Pak v části "Poskytovatelé identit" zaškrtněte políčko "Registrace id uživatele" (může se stát "Registrace e-mailu") a klikněte na OK. 
1. V části Atributy a deklarace identity uživatelů klikněte na zobrazit další. pak zvolte možnosti deklarace, které mají uživatelé zadat a vrátili v tokenu. Zkontrolujte alespoň 'Zobrazované jméno' a 'E-mailová adresa' shromažďovat a vracet, a klikněte na 'OK', pak klikněte na 'Vytvořit'.
1. Vyberte zásadu, kterou jste vytvořili v seznamu, a klikněte na tlačítko Spustit tok uživatele.
1. Tato akce otevře spustit okno toku uživatele, vyberte front-endovou aplikaci a zaznamenejte adresu domény b2clogin.com, která je zobrazena v rozevíracím řádku pro možnost Vybrat doménu.
1. Kliknutím na odkaz v horní části otevřete "známý koncový bod openid configuration" a zaznamenejte authorization_endpoint a token_endpoint hodnoty hodnoty samotného odkazu jako známého openid konfiguračního koncového bodu.

   > [!NOTE]
   > Zásady B2C umožňují vystavit koncové body přihlášení Azure AD B2C, abyste mohli zachytit různé datové součásti a přihlásit uživatele různými způsoby. V tomto případě jsme nakonfigurovali zaregistrovat nebo přihlásit koncový bod, který odhalil známý koncový bod konfigurace, konkrétně naše vytvořené zásady byly identifikovány v adrese URL parametrem p=.
   > 
   > Jakmile je to hotovo - nyní máte funkční platformu identity business to consumer, která přihlašuje uživatele do více aplikací. 
   > Pokud chcete, můžete kliknout na tlačítko "Spustit tok uživatele" zde (projít zaregistrovat nebo přihlásit proces) a získat pocit, co to bude dělat v praxi, ale krok přesměrování na konci se nezdaří, protože aplikace dosud nebyla nasazena.

## <a name="build-the-function-api"></a>Vytvoření rozhraní API funkcí
1. Přepněte zpět na svého standardního klienta Azure AD na webu Azure Portal, abychom mohli znovu nakonfigurovat položky ve vašem předplatném. 
1. Přejděte do okna Funkce aplikace na portálu Azure, otevřete prázdnou aplikaci funkcí a vytvořte novou funkci Na portálu "Webhook + API" prostřednictvím rychlého startu.
1. Vložte ukázkový kód zespodu do souboru Run.csx přes existující kód, který se zobrazí.

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
   > Kód funkce skriptu jazyka c#, který jste právě vložili, jednoduše zaznamená řádek do protokolů funkcí a vrátí text "Hello World" s některými dynamickými daty (datum a čas).

3. Z levého nože vyberte "Integrovat" a v pravém horním rohu podokna vyberte "Rozšířený editor".
4. Vložte ukázkový kód níže přes existující json.

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

5. Přepněte zpět na kartu HttpTrigger1, klikněte na "Získat adresu URL funkce" a zkopírujte ji zkopírovat.

   > [!NOTE]
   > Vazby, které jste právě vytvořili, jednoduše řekněte funkce reagovat na anonymní http GET požadavky na adresu URL, kterou jste právě zkopírovali. (Nyníhttps://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey) máme škálovatelné https API bez serveru, které je schopné vrátit velmi jednoduchou datovou část.
   > Nyní můžete otestovat volání tohoto rozhraní API z webového prohlížeče pomocí výše uvedené adresy URL, můžete také strip ?code=secret část adresy URL a dokázat, že Funkce Azure vrátí chybu 401.

## <a name="configure-and-secure-the-function-api"></a>Konfigurace a zabezpečení rozhraní API funkcí
1. Dvě další oblasti v aplikaci funkce je třeba nakonfigurovat (auth a omezení sítě).
1. Za prvé Pojďme nakonfigurovat ověřování / autorizace, takže klikněte &lt;na&gt; název aplikace funkce (vedle ikony funkcí Z) pro zobrazení stránky přehledu.
1. Dále Vyberte záložku "Funkce platformy" a vyberte "Ověřování / Autorizace".
1. Zapněte funkci Ověřování služby App Service.
1. V části Poskytovatelé ověřování zvolte "Azure Active Directory" a z volte "Upřesnit" z přepínače režimu správy.
1. Vložit ID aplikace rozhraní API back-endové funkce (z Azure AD B2C do pole ID klienta)
1. Vložte známý koncový bod konfigurace open-id z zásady registrace nebo přihlášení do pole URL vystavitisty (tuto konfiguraci jsme zaznamenali dříve).
1. Vyberte OK.
1. Nastavte akci, která má být v případě, že požadavek není ověřen, rozevírací seznam na "Přihlásit se pomocí služby Azure Active Directory" a klikněte na Uložit.

   > [!NOTE]
   > Nyní je vaše rozhraní API funkce nasazena a měla by vyvolat odpovědi 401, pokud není zadán správný klíč, a měla by vrátit data, když je předložen platný požadavek.
   > Přidali jste další zabezpečení hloubky ochrany v EasyAuth konfigurací možnosti "Přihlásit se pomocí služby Azure AD" pro zpracování neověřených požadavků. Uvědomte si, že to změní chování neoprávněného požadavku mezi aplikací Backend Function App a Frontend SPA, protože EasyAuth vydá přesměrování 302 na AAD namísto 401 Neautorizované odpovědi, opravíme to pomocí api managementu později.
   > Stále nemáme žádné ip zabezpečení, pokud máte platný klíč a token OAuth2, kdokoli to může volat odkudkoliv - v ideálním případě chceme vynutit všechny požadavky, aby přišly přes API Management.
   > Pokud používáte úroveň spotřeby správy rozhraní API, nebudete moci provést toto uzamčení pomocí programu VIP, protože pro tuto úroveň neexistuje žádná vyhrazená statická IP adresa, budete se muset spolehnout na způsob uzamčení volání rozhraní API prostřednictvím sdíleného klíče funkce tajného klíče , takže kroky 11-13 nebude možné.

1. Zavření okna Ověřování / Autorizace 
1. Vyberte možnost Síť a potom možnost "Přístupová omezení"
1. Dále uzamkněte IP adresy aplikací s povolenými funkcemi na vip instanci správy rozhraní API. Tento VIP je zobrazen v sekci API management - přehled na portálu.
1. Pokud chcete pokračovat v interakci s portálem funkcí a provést níže uvedené volitelné kroky, měli byste zde také přidat vlastní veřejnou IP adresu nebo rozsah CIDR.
1. Jakmile je povolená položka v seznamu, Azure přidá implicitní pravidlo odepřít blokovat všechny ostatní adresy. 

Budete muset přidat bloky adres ve formátu CIDR do panelu omezení IP. When you need to add a single address such as the API Management VIP, you need to add it in the format xx.xx.xx.xx.

   > [!NOTE]
   > Nyní vaše rozhraní API funkce by neměla být volatelné z libovolného místa než prostřednictvím správy rozhraní API nebo vaše adresa.
   
## <a name="import-the-function-app-definition"></a>Import definice aplikace funkce
1. Otevřete *okno Správa rozhraní API a* *otevřete instanci*.
1. Vyberte okno rozhraní API v části Správa rozhraní API ve vaší instanci.
1. V podokně "Přidat nové rozhraní API" zvolte "Aplikace funkcí" a v horní části vyskakovacího oken vyberte možnost "Plná".
1. Klikněte na Procházet, zvolte aplikaci funkcí, ve které rozhraní API hostujete, a klikněte na vybrat.
1. Pojmenujte a popis rozhraní API pro interní použití správy rozhraní API a přidejte jej do "neomezeného" produktu.
1. Ujistěte se, že jste zaznamenali základní adresu URL pro pozdější použití a potom klikněte na vytvořit.

## <a name="configure-oauth2-for-api-management"></a>Konfigurace Oauth2 pro správu rozhraní API

1. Dále vyberte okno Oauth 2.0 na kartě Zabezpečení a klepněte na tlačítko Přidat.
1. Zadejte hodnoty pro *zobrazovaný název* a *popis* pro přidaný koncový bod Oauth (tyto hodnoty se zobrazí v dalším kroku jako koncový bod Oauth2).
1. Do adresy URL registrační stránky klienta můžete zadat libovolnou hodnotu, protože tuto hodnotu nelze použít.
1. Zkontrolujte typ *implicitního udělovaného* ověření a ponechte zaškrtnutý typ udělení autorizačního kódu.
1. Přesuňte se do polí Koncového bodu *autorizace* a *Tokenu* a zadejte hodnoty, které jste dříve zachytili ze známého dokumentu XML konfigurace.
1. Posuňte se dolů a naplňte *další parametr těla* s názvem "prostředek" pomocí ID klienta rozhraní API back-endu z registrace aplikace Azure AD B2C
1. Vyberte "Pověření klienta", nastavte ID klienta na ID aplikace pro vývojářskou konzoli – tento krok přeskočte, pokud používáte model správy rozhraní API pro využití spotřeby.
1. Nastavte tajný klíč klienta na klíč, který jste zaznamenali dříve – tento krok přeskočte, pokud používáte model správy rozhraní API pro využití spotřeby.
1. Nakonec nyní zaznamenejte redirect_uri udělení kódu auth od api managementu pro pozdější použití.

## <a name="set-up-oauth2-for-your-api"></a>Nastavení Oauth2 pro vaše rozhraní API
1. Vaše rozhraní API se zobrazí na levé straně portálu v části Všechna rozhraní API, otevřete rozhraní API kliknutím na něj.
1. Vyberte kartu Nastavení.
1. Aktualizujte nastavení výběrem "Oauth 2.0" z přepínače autorizace uživatele.
1. Vyberte server Oauth, který jste definovali dříve.
1. Zaškrtněte políčko Přepsat obor a zadejte obor, který jste zaznamenali pro volání rozhraní API back-endu dříve.

   > [!NOTE]
   > Teď máme instanci správy rozhraní API, která ví, jak získat přístupové tokeny z Azure AD B2C autorizovat požadavky a rozumí naší konfiguraci Oauth2 Azure Active Directory B2C.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Nastavení zásad **CORS** a **validate-jwt**

> Následující části by měly být dodržovány bez ohledu na úroveň APIM se používá. 

1. Přepněte zpět na kartu návrhu a zvolte "Všechna rozhraní API" a kliknutím na tlačítko zobrazení kódu zobrazte editor zásad.
1. Upravte vstupní část a vložte níže uvedený xml tak, aby přečte následující.

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
1. Upravte adresu URL openid-config tak, aby odpovídala vašemu známému koncovému bodu Azure AD B2C pro zásady registrace nebo přihlášení.
1. Upravte hodnotu deklarace tak, aby odpovídala platnému ID aplikace, označované také jako ID klienta pro aplikaci back-endového rozhraní API a uložení.

   > [!NOTE]
   > Nyní api správa je schopna reagovat na požadavky napříč původem aplikací JS SPA a bude provádět omezení, omezení rychlosti a předběžné ověření tokenu ověření JWT předaným před předáním požadavku na rozhraní API funkce.

   > [!NOTE]
   > Následující část je volitelná a nevztahuje se na úroveň **Consumption,** která nepodporuje portál pro vývojáře.
   > Pokud nemáte v úmyslu používat portál pro vývojáře nebo jej nemůžete použít, protože používáte úroveň Consumption, přeskočte tento krok a přejděte rovnou na ["Build the JavaScript SPA to consume the API"](#build-the-javascript-spa-to-consume-the-api).

## <a name="optional-configure-the-developer-portal"></a>[Nepovinné] Konfigurace portálu pro vývojáře

1. Otevřete okno Azure AD B2C a přejděte k registraci aplikace pro portál pro vývojáře
1. Nastavte položku "Adresa URL odpovědi" na položku, kterou jste si poznamenali, když jste dříve nakonfigurovali redirect_uri udělení kódu auth ve správě rozhraní API.

   Nyní, když je povolena autorizace uživatele OAuth 2.0 na `Echo API`, vývojářská konzola získá přístupový token pro uživatele před voláním rozhraní API.

1. Přejděte na libovolnou `Echo API` operaci v části Na portálu pro vývojáře a vyberte **Vyzkoušet,** abyste se dostali do vývojářské konzole.
1. Poznámka: nová položka v části **Autorizace** odpovídající autorizačnímu serveru, který jste právě přidali.
1. V rozevíracím seznamu autorizace vyberte **Autorizační kód** a budete vyzváni k přihlášení k tenantovi Azure AD. Pokud jste již přihlášeni pomocí účtu, nemusí být výzva.
1. Po úspěšném `Authorization: Bearer` přihlášení se do požadavku přidá záhlaví s přístupovým tokenem z Azure AD B2C kódovaným v Base64. 
1. Vyberte **Odeslat** a můžete úspěšně volat rozhraní API.

   > [!NOTE]
   > Nyní správa rozhraní API je schopna získat tokeny pro portál pro vývojáře k testování vašeho rozhraní API a je schopen pochopit jeho definici a vykreslit příslušnou testovací stránku na portálu pro vývoj.

1. V okně přehledu portálu pro správu rozhraní API klikněte na portál pro vývojáře a přihlaste se jako správce rozhraní API.
1. Zde můžete vy a další vybraní uživatelé rozhraní API testovat a volat je z konzoly.
1. Vyberte "Produkty", pak zvolte "Neomezený", pak zvolte rozhraní API, které jsme vytvořili dříve, a klikněte na tlačítko "TRY IT"
1. Odkrýt klíč odběru rozhraní API a zkopírujte jej na bezpečné místo spolu s adresou URL požadavku, kterou budete později potřebovat.
1. Také vyberte Implicitní, z oauth ověření rozevírací rozbalit a budete muset ověřit zde s vyskakovací okno.
1. Klikněte na tlačítko "Odeslat" a pokud je vše v pořádku, vaše aplikace funkce by měla odpovědět zpět s hello message prostřednictvím správy rozhraní API se zprávou 200 OK a některými JSON.

   > [!NOTE]
   > Gratulujeme, teď máte Azure AD B2C, API Management a Azure Funkce spolupracují na publikování, zabezpečení a využívat rozhraní API. Možná jste si všimli, že rozhraní API je ve skutečnosti zabezpečeno dvakrát pomocí této metody, jednou s hlavičkou API Management Ocp-Subscription-Key header a jednou s autorizací: Bearer JWT.
   > Měli byste mít pravdu, protože tento příklad je jednostránková aplikace JavaScriptu, používáme klíč pro správu rozhraní API pouze pro omezení rychlosti a fakturační hovory.
   > Skutečné autorizace a ověřování je zpracována Azure AD B2C a je zapouzdřena v JWT, který získá ověřena dvakrát, jednou pomocí api managementa a pak funkce Azure.

## <a name="build-the-javascript-spa-to-consume-the-api"></a>Vytvoření javascriptového spa využívat API
1. Otevření okna účtů úložiště na webu Azure Portal 
1. V části Nastavení vyberte účet, který jste vytvořili, a vyberte okno "Statická webová stránka" (pokud nevidíte možnost "Statický web", zkontrolujte, zda jste si vytvořili účet V2).
1. Nastavte statickou webhostingovou funkci na "povoleno" a nastavte název dokumentu indexu na 'index.html', a pak klikněte na 'uložit'.
1. Poznamenejte si obsah primárního koncového bodu, protože toto umístění je místo, kde bude hostovaný web front-endu. 

   > [!NOTE]
   > Můžete použít buď přepsání Azure Blob Storage + CDN, nebo Azure App Service – ale funkce statického hostování webových stránek úložiště blob nám poskytuje výchozí kontejner, který slouží statickému webovému obsahu / html / js / css z Azure Storage a odvodí nám výchozí stránku pro nulovou práci.

## <a name="upload-the-js-spa-sample"></a>Nahrání ukázky JS SPA
1. Stále v okně účtu úložiště vyberte okno "Objekty blobů" z části Služba blob a klikněte na $web kontejner, který se zobrazí v pravém podokně.
1. Uložte níže uvedený kód do souboru místně v počítači jako index.html a potom nahrajte soubor index.html do kontejneru $web.

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

1. Přejděte na statický primární koncový bod webu, který jste uložili dříve v poslední části.

   > [!NOTE]
   > Gratulujeme, právě jste nasadili javascriptovou aplikaci na jednu stránku do úložiště Azure Vzhledem k tomu, že jsme nenakonfigurovali aplikaci JS s klíči pro rozhraní API nebo nenakonfigurovali aplikaci JS s podrobnostmi o Azure AD B2C – stránka ještě nebude fungovat, pokud ji otevřete.

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>Konfigurace js spa pro Azure AD B2C
1. Teď víme, kde všechno je: můžeme nakonfigurovat SPA s příslušnou adresou API API pro správu rozhraní API a správnou aplikací / ID klienta Azure AD B2C
1. Vraťte se do okna úložiště portálu Azure a klikněte na index.html a pak zvolte Upravit objekt Blob. 
1. Aktualizujte auth podrobnosti tak, aby odpovídaly vaše front-endaplikace, kterou jste zaregistrovali v B2C dříve, s tím, že 'b2cScopes' hodnoty jsou pro back-end rozhraní API.
1. Klíč webApi a adresa URL rozhraní API najdete v testovacím podokně api management pro operaci rozhraní API.
1. Vytvořte klíč předplatného APIM tak, že přejdete do správy rozhraní API zpět do okna Správa rozhraní API, vyberete "Předplatná" a kliknete na "Přidat předplatné" a pak uložte záznam. Kliknutím na tři tečky (...) vedle vytvořeného řádku můžete zobrazit klíče, abyste mohli zkopírovat primární klíč.
1. Mělo by to vypadat něco jako níže kód:-  

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

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Nastavení identifikátorů URI přesměrování pro front-endovou aplikaci Azure AD B2C
1. Otevřete okno Azure AD B2C a přejděte k registraci aplikace pro aplikaci JavaScript Frontend
1. Nastavte adresu URL přesměrování na adresu, kterou jste si poznamenali, když jste dříve nastavili primární koncový bod statického webu výše

   > [!NOTE] 
   > Výsledkem této konfigurace bude mít klient front-endové aplikace přijetí přístupového tokenu s příslušnými deklaracemi identity z Azure AD B2C.
   > Spa bude moci přidat jako nosný token v záhlaví https ve volání back-endapi.
   > Správa rozhraní API předem ověří token, omezení rychlosti volání koncového bodu pomocí klíče odběratele, před předáním požadavku do přijímajícího rozhraní API funkce Azure.
   > Spa vykreslí odpověď v prohlížeči.

   > *Gratulujeme, nakonfigurovali jste Azure AD B2C, Azure API Management, Azure Functions, Azure App Service Authorization pro perfektní harmonii!*

   > [!NOTE]
   > Nyní máme jednoduchou aplikaci s jednoduchým zabezpečeným rozhraním API, otestujeme ji.

## <a name="test-the-client-application"></a>Testování klientské aplikace
1. Otevřete ukázkovou adresu URL aplikace, kterou jste si poznamenali z účtu úložiště, který jste vytvořili dříve
1. Klikněte na "Přihlásit se" v pravém horním rohu, toto kliknutí se objeví vaše Registrace Azure AD B2C nebo přihlásit profil.
1. Příspěvek Přihlásit se do "Přihlášen jako" část obrazovky bude naplněna z vašeho JWT.
1. Nyní klikněte na "Call Web Api" a stránka by se měla aktualizovat s hodnotami odeslanými zpět z zabezpečeného rozhraní API.

## <a name="and-were-done"></a>A skončili jsme.
Výše uvedené kroky lze upravit a upravit tak, aby umožnily mnoho různých použití Azure AD B2C se správou rozhraní API.

## <a name="next-steps"></a>Další kroky
* Další informace o [Azure Active Directory a OAuth2.0](../active-directory/develop/authentication-scenarios.md).
* Podívejte se na další [videa](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o správě rozhraní API.
* Další způsoby zabezpečení back-endové služby naleznete v [tématu Vzájemné ověřování certifikátů](api-management-howto-mutual-certificates.md).
* [Vytvořte instanci služby Správa rozhraní API](get-started-create-service-instance.md).
* [Spravujte své první rozhraní API](import-and-publish.md).
