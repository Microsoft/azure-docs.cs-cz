---
title: Ochrana SPA back-endu v Azure API Management s využitím Active Directory B2C
description: Chraňte rozhraní API pomocí protokolu OAuth 2,0 pomocí Azure Active Directory B2C, Azure API Management a snadného ověření, které se má volat z JavaScript SPA pomocí PKCE s povoleným ověřováním SPA.
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
ms.date: 02/18/2021
ms.author: wieastbu
ms.custom: fasttrack-new, fasttrack-update, devx-track-js
ms.openlocfilehash: 812b54d10ea3cc3c405f534e36ac66abf3466808
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449284"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Ochrana SPA back-endu pomocí OAuth 2,0, Azure Active Directory B2C a Azure API Management

V tomto scénáři se dozvíte, jak nakonfigurovat instanci Azure API Management k ochraně rozhraní API.
K získání tokenu používáme tok Azure AD B2C SPA (PKCE Authentication Code +) a současně API Management k zabezpečení Azure Functions back-endu pomocí EasyAuth.

## <a name="aims"></a>Usiluje

Zjistíme, jak se dá API Management použít ve zjednodušeném scénáři s Azure Functions a Azure AD B2C. Vytvoříte aplikaci JavaScript (JS), která volá rozhraní API, které se přihlásí uživatelům pomocí Azure AD B2C. Pak použijete k ochraně back-endu rozhraní API API Management ověřit-JWT, CORS a omezení četnosti pomocí klíčových funkcí zásad.

V zájmu důkladné ochrany pak pomocí EasyAuth ověří token znovu uvnitř back-endu rozhraní API a zajistěte, aby služba API Management byla jedinou službou, která může volat Azure Functions back-end.

## <a name="what-will-you-learn"></a>Co se naučíte

> [!div class="checklist"]
> * Nastavení jednostránkové aplikace a back-endu rozhraní API v Azure Active Directory B2C
> * Vytvoření rozhraní API pro Azure Functions back-endu
> * Import rozhraní API Azure Functions do Azure API Management
> * Zabezpečení rozhraní API v Azure API Management
> * Volání koncových bodů autorizace Azure Active Directory B2C přes knihovny Microsoft Identity Platform Librarys (MSAL.js)
> * Uložení jednostránkové aplikace HTML/Vanilla JS a její obsluha z koncového bodu Azure Blob Storage

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat podle kroků v tomto článku, musíte mít:

* Účet úložiště Azure (StorageV2) Pro obecné účely v2, který bude hostitelem jednostránkové aplikace front-endu JS.
* Instance Azure API Management (libovolná úroveň bude fungovat, včetně "spotřeby", ale některé funkce, které platí pro celý scénář, nejsou v této vrstvě k dispozici (rychlost-limit-po-klíč a vyhrazená virtuální IP adresa). Tato omezení jsou uvedená níže v článku, kde je to vhodné).
* Prázdná aplikace Function App (s běžícím modulem runtime v 3.1 .NET Core v plánu spotřeby) pro hostování rozhraní nazývaného rozhraní API
* Azure AD B2C tenant propojený s předplatným.

I když v praxi byste mohli použít prostředky ve stejné oblasti v produkčních úlohách, pro tento článek s postupem není důležité, aby tato oblast nasazení byla důležitá.

## <a name="overview"></a>Přehled

Tady je ilustrace používaných komponent a tok mezi nimi po dokončení tohoto procesu.
![Používané komponenty a flow](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Používané komponenty a flow")

Tady je rychlý přehled kroků:

1. Vytvoření Azure AD B2C volání (front-endu, API Management) a aplikací API s rozsahy a udělení přístupu k rozhraní API
1. Vytvořte zásady registrace a přihlášení, které uživatelům umožní přihlašovat se Azure AD B2C 
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

   > [!TIP]
   > Budeme zachytit pár informací a klíčů, podobně jako v tomto dokumentu. může to být užitečné, když budete mít k dispozici textový editor, který dočasně ukládá následující položky konfigurace.  
   >
   > ID KLIENTA BACK-ENDU B2C:  
   > TAJNÝ KLÍČ KLIENTA BACK-ENDU B2C:  
   > IDENTIFIKÁTOR URI OBORU ROZHRANÍ BACK-END B2C:  
   > ID KLIENTA B2C FRONT-ENDU:  
   > IDENTIFIKÁTOR URI KONCOVÉHO BODU TOKU UŽIVATELE B2C:  
   > B2C DOBŘE ZNÁMÝ OPENID KONCOVÝ BOD:   
   > Název zásady B2C: URL funkce Frontendapp_signupandsignin:  
   > ZÁKLADNÍ ADRESA URL APIM API: ADRESA URL PRIMÁRNÍHO KONCOVÉHO BODU ÚLOŽIŠTĚ:  

## <a name="configure-the-backend-application"></a>Konfigurace back-endu aplikace

Otevřete okno Azure AD B2C na portálu a proveďte následující kroky.

1. Výběr karty **Registrace aplikací**
1. Klikněte na tlačítko Nová registrace.
1. V poli Výběr identifikátor URI přesměrování vyberte web.
1. Teď nastavte zobrazované jméno, vyberte něco jedinečného a důležitého pro vytvářenou službu. V tomto příkladu použijeme název "back-end aplikace".
1. Použijte zástupné symboly pro adresy URL odpovědí, jako je https://jwt.ms (Web dekódování tokenů vlastněných společností Microsoft), aktualizujeme tyto adresy později.
1. Ujistěte se, že jste vybrali možnost účty v jakémkoli zprostředkovateli identity nebo organizačním adresáři (pro ověřování uživatelů pomocí toků uživatelů).
1. V této ukázce zrušte výběr pole udělení souhlasu správce, protože v dnešní době nebudete muset offline_access oprávnění.
1. Klikněte na Zaregistrovat.
1. Poznamenejte si ID klienta back-endu aplikace pro pozdější použití (zobrazené v části aplikace (klienta) ID).
1. Vyberte kartu *certifikáty a tajné klíče* (v části spravovat) a pak klikněte na nový tajný klíč klienta, aby se vygeneroval ověřovací klíč (přijměte výchozí nastavení a klikněte na Přidat).
1. Po kliknutí na tlačítko Přidat Zkopírujte klíč (pod položkou "value") někam bezpečně pro pozdější použití jako "tajný klíč klienta back-end" – Všimněte si, že toto dialogové okno je jedinou pravděpodobností, že budete muset tento klíč zkopírovat.
1. Teď vyberte kartu *vystavit rozhraní API* (pod položkou spravovat).
1. Zobrazí se výzva k nastavení identifikátoru URI AppID, výběr a zaznamenání výchozí hodnoty.
1. Vytvořte a pojmenujte obor "Hello" pro rozhraní API funkce, můžete použít frázi "Hello" pro všechny možnosti, které lze zadat, zaznamenat plný rozsah hodnoty URI a potom kliknout na Přidat obor.
1. Vraťte se do kořenového okna Azure AD B2C výběrem cesty Azure AD B2C v levém horním rohu portálu.

   > [!NOTE]
   > Obory Azure AD B2C mají v rozhraní API efektivně oprávnění, která můžou jiné aplikace požádat o přístup prostřednictvím okna přístup přes rozhraní API ze svých aplikací, a efektivně jste právě vytvořili oprávnění aplikace pro vaše volané rozhraní API.

## <a name="configure-the-frontend-application"></a>Konfigurace front-endové aplikace

1. Výběr karty **Registrace aplikací**
1. Klikněte na tlačítko Nová registrace.
1. V poli Výběr identifikátoru URI přesměrování vyberte možnost aplikace s jednou stránkou (SPA).
1. Teď nastavte zobrazované jméno a identifikátor ID AppID, vyberte něco jedinečného a relevantního pro front-end aplikaci, která bude tuto AAD B2C registraci aplikace používat. V tomto příkladu můžete použít front-end aplikaci.
1. V závislosti na první registraci aplikace ponechte vybrané typy účtů nastavené na výchozí (ověřování uživatelů pomocí uživatelských toků).
1. Použijte zástupné symboly pro adresy URL odpovědí, jako je https://jwt.ms (Web dekódování tokenů vlastněných společností Microsoft), aktualizujeme tyto adresy později.
1. Ponechte pole udělit souhlas správce.
1. Klikněte na Zaregistrovat.
1. Poznamenejte si ID klienta front-endu pro pozdější použití (zobrazené v části aplikace (klienta) ID).
1. Přepněte na kartu *oprávnění rozhraní API* .
1. Udělte přístup k aplikaci back-endu kliknutím na Přidat oprávnění, pak na moje rozhraní API, vyberte back-end aplikaci, vyberte oprávnění, vyberte obor, který jste vytvořili v předchozí části, a klikněte na Přidat oprávnění.
1. Klikněte na udělit souhlas správce pro {tenant} a v automaticky otevřeném okně klikněte na Ano. Tato automaticky otevíraná okna souhlasí s tím, že aplikace front-end použila oprávnění Hello definované v části "aplikace back-endu" vytvořeného dříve.
1. Všechna oprávnění by se teď měla zobrazovat pro aplikaci jako zelená značka ve sloupci Stav.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Vytvoření uživatelského toku registrace a přihlášení

1. Kliknutím na Azure AD B2C s popisem cesty se vraťte do kořene okna B2C.
1. Přepněte na kartu Toky uživatelů (v části zásady).
1. Klikněte na nový tok uživatele.
1. Zvolte typ toku registrace a přihlášení a potom vyberte doporučené a pak klikněte na vytvořit.
1. Zadejte název zásady a nahrajte ji pro pozdější verzi. V tomto příkladu můžete použít "Frontendapp_signupandsignin". Všimněte si, že s předponou "B2C_1_" se dá "B2C_1_Frontendapp_signupandsignin".
1. V části zprostředkovatelé identity a místní účty vyhledejte v závislosti na konfiguraci vašeho tenanta B2C (nebo přihlašovací jméno uživatele) na odkaz e-mailová registrace a klikněte na OK. Tato konfigurace je způsobená tím, že budeme registrovat místní účty B2C a nemusíte odvodit jiného zprostředkovatele identity (jako je poskytovatel sociálních identit) a používat stávající účet sociální sítě pro uživatele.
1. Nastavení vícefaktorového ověřování a podmíněného přístupu ponechte ve výchozím nastavení.
1. V části atributy uživatelů a deklarace identity klikněte na Zobrazit další. pak zvolte možnosti deklarace identity, které chcete, aby vaši uživatelé zadali a vrátili v tokenu. Zkontrolujte aspoň zobrazované jméno a e-mailovou adresu, která se má vrátit, se zobrazovaným názvem a e-mailovou adresou, které se mají vrátit (věnujte pozor na skutečnost, že shromažďujete EmailAddress, jednotné číslo a žádáte o vrácení e-mailových adres, více), a klikněte na OK a pak klikněte na tlačítko vytvořit.
1. Klikněte na tok uživatele, který jste vytvořili v seznamu, a pak klikněte na tlačítko spustit tok uživatele.
1. Tato akce otevře okno spustit tok uživatele, vyberte front-end aplikaci, zkopírujte koncový bod toku uživatele a uložte ho pro pozdější použití.
1. Zkopírujte a uložte odkaz v horní části, záznam jako "dobře známý koncový bod konfigurace OpenID" pro pozdější použití.

   > [!NOTE]
   > Zásady B2C umožňují zobrazit koncové body přihlášení Azure AD B2C, aby bylo možné zachytit různé datové komponenty a přihlašovat uživatele různými způsoby.
   > 
   > V tomto případě jsme nakonfigurovali tok registrace nebo přihlašování (zásady). Tato možnost také vystavila známý koncový bod konfigurace, v obou případech byla naše vytvořená zásada identifikována v adrese URL parametrem řetězce dotazu "p =".  
   >
   > Až to uděláte, budete mít funkční firemní platformu pro identitu spotřebitelů, která bude podepisovat uživatele do více aplikací.

## <a name="build-the-function-api"></a>Sestavení rozhraní API funkcí

1. V Azure Portal přepněte zpátky na standardního tenanta Azure AD, abychom mohli znovu nakonfigurovat položky v předplatném.
1. Přejděte do okna Function Apps v Azure Portal, otevřete prázdnou aplikaci Function App, klikněte na funkce a pak klikněte na tlačítko Přidat.
1. V informačním rámečku, který se zobrazí, zvolte možnost "vývoj na portálu", v části Vybrat šablonu vyberte možnost Trigger HTTP, v části Podrobnosti šablony název zadejte "Hello" a pak vyberte Přidat.
1. Přepněte do okna Code + test a zkopírujte a vložte ukázkový kód z níže uvedeného *kódu* , který se zobrazí.
1. Vyberte Uložit.

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

   > [!TIP]
   > Kód funkce skriptu jazyka c#, který jste právě vložili, jednoduše zaznamená řádek do protokolů functions a vrátí text "Hello World" s některými dynamickými daty (datum a čas).

1. V levém okně vyberte Integration a pak klikněte na odkaz http (REQ) v poli Trigger.
1. Z rozevíracího seznamu vybrané metody HTTP zrušte výběr metody HTTP POST, ponechte jenom možnost získat a pak klikněte na Uložit.
1. Přepněte zpátky na kartu kód + test, klikněte na získat adresu URL funkce a potom zkopírujte adresu URL, která se zobrazí, a uložte ji pro pozdější verzi.

   > [!NOTE]
   > Vazby, které jste právě vytvořili, jednoduše říkají funkcím, aby reagovaly na adresu URL, kterou jste právě zkopírovali () na anonymní požadavky HTTP GET `https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey` . Teď máme škálovatelné rozhraní API HTTPS bez serveru, které dokáže vracet velmi jednoduchou datovou část.
   >
   > Nyní můžete testovat volání tohoto rozhraní API z webového prohlížeče pomocí vaší verze adresy URL výše, kterou jste právě zkopírovali a uložili. Můžete také odebrat parametry řetězce dotazu "? Code = SecretKey" část adresy URL a znovu spustit test, aby se prokázalo, že Azure Functions vrátí chybu 401.

## <a name="configure-and-secure-the-function-api"></a>Konfigurace a zabezpečení rozhraní API funkcí

1. Je potřeba nakonfigurovat dvě další oblasti v aplikaci Function App (omezení autorizace a sítě).
1. Nejdřív nakonfigurujeme ověřování/autorizaci, takže přejdete zpátky do kořenového okna aplikace Function App prostřednictvím cesty pro navigaci.
1. Pak vyberte ověřování/autorizace (v části nastavení).
1. Zapněte funkci ověřování App Service.
1. Nastavte akci, která se má provést, pokud není v rozevíracím seznamu žádost ověřená možnost přihlásit se pomocí Azure Active Directory.
1. V části zprostředkovatelé ověřování vyberte Azure Active Directory.
1. V přepínači režim správy vyberte možnost Upřesnit.
1. Do pole ID klienta Vložte ID klienta [Application] aplikace back-endu (z Azure AD B2C).
1. Do pole Adresa URL vystavitele vložte bod konfigurace známého Open-ID v zásadách registrace a přihlášení (Tato konfigurace jsme dříve zaznamenali).
1. Klikněte na Zobrazit tajný klíč a vložte tajný klíč klienta back-end aplikace do příslušného pole.
1. Vyberte OK, čímž se vrátíte zpátky na okno pro výběr zprostředkovatele identity nebo obrazovku.
1. V části Upřesnit nastavení nechejte [úložiště tokenů](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization#token-store) povolené (výchozí).
1. Klikněte na Uložit (v levém horním rohu okna).

   > [!IMPORTANT]
   > Rozhraní API funkcí je teď nasazené a mělo by se vyvolávat 401 odpovědí, pokud není zadaný správný token JWT jako autorizační hlavička a při předložení platného požadavku by měla vracet data.  
   > Do EasyAuth jste přidali další zabezpečení obrany tím, že nakonfigurujete možnost přihlášení pomocí služby Azure AD pro zpracování neověřených požadavků. Mějte na paměti, že tato akce změní chování neautorizovaných žádostí mezi back-end Function App a front-endu SPA, protože EasyAuth vydá neautorizovanou odpověď 302 na 401 AAD, a to pomocí API Management později.  
   >
   > Pořád ještě nemáte žádné použití zabezpečení IP, pokud máte platný klíč a OAuth2 token, kdokoli ho může zavolat odkudkoli – ideálně chceme vynutit, aby všechny žádosti byly pořízeny prostřednictvím API Management.  
   > 
   > Pokud používáte APIM spotřebu, pak není k [dispozici vyhrazená virtuální IP adresa Azure API Management](./api-management-howto-ip-addresses.md#ip-addresses-of-consumption-tier-api-management-service) pro seznam s omezeními přístupu k funkcím. Ve službě Azure API Management Standard SKU a nad [virtuální IP adresou je jeden tenant a za dobu života prostředku](./api-management-howto-ip-addresses.md#changes-to-the-ip-addresses). Pro úroveň využití Azure API Management můžete uzamknout volání rozhraní API prostřednictvím klíče sdíleného tajného klíče v části identifikátoru URI, který jste zkopírovali výše. Pro úroveň spotřeby platí také, že kroky 12-17 níže se nevztahují.

1. Zavřete okno ověřování/autorizace. 
1. Otevřete okno *API Management portálu* a pak otevřete *vaši instanci*.
1. Zaznamenejte si privátní VIP zobrazenou na kartě Přehled.
1. Vraťte se do okna *Azure Functions portálu* a pak znovu otevřete *instanci* .
1. Vyberte sítě a pak vyberte konfigurovat omezení přístupu.
1. Klikněte na Přidat pravidlo a zadejte virtuální IP adresu zkopírovanou v kroku 3 výše ve formátu xx. xx. xx. xx/32.
1. Pokud chcete pokračovat v interakci s portálem functions a provést volitelné kroky níže, měli byste sem přidat vlastní veřejnou IP adresu nebo rozsah CIDR.
1. Jakmile je položka Povolit v seznamu, Azure přidá implicitní pravidlo odepření pro blokování všech ostatních adres.

Na panel omezení IP adres budete muset přidat bloky s formátovaným směrováním CIDR. Pokud potřebujete přidat jednu adresu, například API Management VIP, je nutné ji přidat ve formátu xx. xx. xx. xx/32.

   > [!NOTE]
   > Rozhraní API funkcí by teď nemělo být možné volat odkudkoli, než prostřednictvím služby API Management, nebo vaší adresy.

1. Otevřete okno *API Management* a pak otevřete *vaši instanci*.
1. Vyberte okno rozhraní API (v části rozhraní API).
1. V podokně Přidat nové rozhraní API zvolte Function App a pak v horní části automaticky otevíraného okna vyberte plná.
1. Klikněte na Procházet, vyberte aplikaci Function App, do které rozhraní API hostuje, a klikněte na vybrat. V dalším kroku klikněte na vybrat znovu.
1. Dejte rozhraní API název a popis interního použití API Management a přidejte ho do produktu Unlimited.
1. Zkopírujte a zaznamenejte základní adresu URL rozhraní API a klikněte na vytvořit.
1. Klikněte na kartu nastavení a potom v části předplatné – přepněte mimo políčko předplatné je povinné, protože v tomto případě budeme v tomto případě používat token JWT OAuth k omezení přenosové rychlosti. Všimněte si, že pokud používáte úroveň spotřeby, bude to v produkčním prostředí stále potřeba. 

   > [!TIP]
   > Pokud používáte úroveň spotřeby APIM, neomezený produkt nebude k dispozici jako předem. Místo toho přejděte na "produkty" v části "rozhraní API" a stiskněte tlačítko Přidat.  
   > Jako název produktu a Popis zadejte "Unlimited" a v levém dolním rohu obrazovky vyberte rozhraní API, které jste právě přidali z popisku "+" rozhraní API "+". Zaškrtněte políčko Publikováno. Ponechte REST jako výchozí. Nakonec stiskněte tlačítko vytvořit. Tím jste vytvořili neomezený produkt a přiřadili ho k vašemu rozhraní API. Svůj nový produkt můžete přizpůsobit později.

## <a name="configure-and-capture-the-correct-storage-endpoint-settings"></a>Konfigurace a zachycení správného nastavení koncového bodu úložiště

1. Otevřete okno účty úložiště v Azure Portal 
1. Vyberte účet, který jste vytvořili, a v části nastavení vyberte okno "statický web" (Pokud nevidíte možnost "statický web"), zkontrolujte, že jste vytvořili účet v2.
1. Nastavte funkci statického webového hostování na povoleno a nastavte název dokumentu indexu na ' index.html ' a pak klikněte na Uložit.
1. Poznamenejte si obsah primárního koncového bodu pro pozdější použití v tomto umístění, kde se nachází front-end lokalita.

   > [!TIP]
   > Můžete použít přepisování Azure Blob Storage + CDN nebo Azure App Service k hostování ZABEZPEČENÉho webového obsahu, ale Blob Storage funkce hostování statického webu poskytuje výchozí kontejner pro poskytování statického webového obsahu/HTML/js/CSS od Azure Storage a odvozuje výchozí stránku pro nás pro nulovou práci.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Nastavení zásad **CORS** a **Validate-JWT**

> Následující části by měly následovat bez ohledu na použitou úroveň APIM. Adresa URL účtu úložiště je z účtu úložiště, který jste zpřístupnili od požadavků v horní části tohoto článku.
1. Přepněte do okna API Management portálu a otevřete vaši instanci.
1. Vyberte rozhraní API a pak vyberte všechna rozhraní API.
1. V části příchozí zpracování kliknutím na tlačítko Zobrazit kód </> zobrazte Editor zásad.
1. Upravte vstupní část a vložte následující kód XML, aby vypadal jako následující.
1. V zásadách nahraďte následující parametry.
1. {PrimaryStorageEndpoint} (Primární koncový bod úložiště, který jste zkopírovali v předchozí části), {b2cpolicy-dobře známé-OpenID} ("dobře známý koncový bod konfigurace OpenID", který jste zkopírovali dříve, a {back-end-API-Application-Client-ID} (aplikace/ID klienta pro **back-end rozhraní API**) se správnými hodnotami uloženými dříve.
1. Pokud používáte úroveň spotřeby API Management, pak byste měli odebrat zásadu Rate-limit-by-Key, protože tato zásada není dostupná, když používáte úroveň spotřeby Azure API Management.

   ```xml
   <inbound>
      <cors allow-credentials="true">
            <allowed-origins>
                <origin>{PrimaryStorageEndpoint}</origin>
            </allowed-origins>
            <allowed-methods preflight-result-max-age="120">
                <method>GET</method>
            </allowed-methods>
            <allowed-headers>
                <header>*</header>
            </allowed-headers>
            <expose-headers>
                <header>*</header>
            </expose-headers>
        </cors>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid." require-expiration-time="true" require-signed-tokens="true" clock-skew="300">
         <openid-config url="{b2cpolicy-well-known-openid}" />
         <required-claims>
            <claim name="aud">
               <value>{backend-api-application-client-id}</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <rate-limit-by-key calls="300" renewal-period="120" counter-key="@(context.Request.IpAddress)" />
      <rate-limit-by-key calls="15" renewal-period="60" counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
   </inbound>
   ```

   > [!NOTE]
   > Azure API Management teď dokáže reagovat na žádosti o křížové zdroje z vašich aplikací pro JavaScript SPA a provede omezení, které se před předáním požadavku na rozhraní API předají do služby tokenu JWT.
   > 
   > Blahopřejeme, nyní máte Azure AD B2C, API Management a Azure Functions spolupracujete, abyste mohli publikovat, zabezpečit a využívat rozhraní API.

   > [!TIP]
   > Pokud používáte úroveň spotřeby API Management pak, místo abyste omezili omezením podle předmětu JWT nebo příchozí IP adresy (omezení četnosti volání podle klíčových zásad není pro "spotřebu" úrovně "podporováno"), můžete omezit podle kvóty volání v [tomto umístění](./api-management-access-restriction-policies.md#LimitCallRate).  
   > Jak je v tomto příkladu jednoduchá stránková aplikace v JavaScriptu, používáme API Management klíč jenom pro volání omezování četnosti a fakturace. Vlastní autorizaci a ověřování zpracovává služba Azure AD B2C a je zapouzdřená v tokenu JWT, který se jednou ověří pomocí API Management a pak pomocí back-endu Azure Function.

## <a name="upload-the-javascript-spa-sample-to-static-storage"></a>Nahrání ukázky pro JavaScript SPA do statického úložiště

1. Stále v okně účet úložiště vyberte okno kontejnery z části Služba BLOB Service a klikněte na kontejner $web, který se zobrazí v pravém podokně.
1. Níže uvedený kód níže uložte do souboru místně na svém počítači index.html a pak soubor nahrajte index.html do kontejneru $web.

   ```html
    <!doctype html>
    <html lang="en">
    <head>
         <meta charset="utf-8">
         <meta http-equiv="X-UA-Compatible" content="IE=edge">
         <meta name="viewport" content="width=device-width, initial-scale=1">
         <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-BmbxuPwQa2lc/FVzBcNJ7UAyJxM6wuqIj61tLrc4wSX0szH/Ev+nYRRuWlolflfl" crossorigin="anonymous">
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.11.1/js/msal-browser.min.js"></script>
    </head>
    <body>
         <div class="container-fluid">
             <div class="row">
                 <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
                        <div class="container-fluid">
                            <a class="navbar-brand" href="#">Azure Active Directory B2C with Azure API Management</a>
                            <div class="navbar-nav">
                                <button class="btn btn-success" id="signinbtn"  onClick="login()">Sign In</a>
                            </div>
                        </div>
                    </nav>
                 </div>
             </div>
             <div class="row">
                 <div class="col-md-12">
                     <div class="card" >
                        <div id="cardheader" class="card-header">
                            <div class="card-text"id="message">Please sign in to continue</div>
                        </div>
                        <div class="card-body">
                            <button class="btn btn-warning" id="callapibtn" onClick="getAPIData()">Call API</a>
                            <div id="progress" class="spinner-border" role="status">
                                <span class="visually-hidden">Loading...</span>
                            </div>
                        </div>
                     </div>
                 </div>
             </div>
         </div>
         <script lang="javascript">
                // Just change the values in this config object ONLY.
                var config = {
                    msal: {
                        auth: {
                            clientId: "{CLIENTID}", // This is the client ID of your FRONTEND application that you registered with the SPA type in AAD B2C
                            authority:  "{YOURAUTHORITYB2C}", // Formatted as https://{b2ctenantname}.b2clogin.com/tfp/{b2ctenantguid or full tenant name including onmicrosoft.com}/{signuporinpolicyname}
                            redirectUri: "{StoragePrimaryEndpoint}", // The storage hosting address of the SPA, a web-enabled v2 storage account - recorded earlier as the Primary Endpoint.
                            knownAuthorities: ["{B2CTENANTDOMAIN}"] // {b2ctenantname}.b2clogin.com
                        },
                        cache: {
                            cacheLocation: "sessionStorage",
                            storeAuthStateInCookie: false 
                        }
                    },
                    api: {
                        scopes: ["{BACKENDAPISCOPE}"], // The scope that we request for the API from B2C, this should be the backend API scope, with the full URI.
                        backend: "{APIBASEURL}/hello" // The location that we will call for the backend api, this should be hosted in API Management, suffixed with the name of the API operation (in the sample this is '/hello').
                    }
                }
                document.getElementById("callapibtn").hidden = true;
                document.getElementById("progress").hidden = true;
                const myMSALObj = new msal.PublicClientApplication(config.msal);
                myMSALObj.handleRedirectPromise().then((tokenResponse) => {
                    if(tokenResponse !== null){
                        console.log(tokenResponse.account);
                        document.getElementById("message").innerHTML = "Welcome, " + tokenResponse.account.name;
                        document.getElementById("signinbtn").hidden = true;
                        document.getElementById("callapibtn").hidden = false;
                    }}).catch((error) => {console.log("Error Signing in:" + error);
                });
                function login() {
                    try {
                        myMSALObj.loginRedirect({scopes: config.api.scopes});
                    } catch (err) {console.log(err);}
                }
                function getAPIData() {
                    document.getElementById("progress").hidden = false; 
                    document.getElementById("message").innerHTML = "Calling backend ... "
                    document.getElementById("cardheader").classList.remove('bg-success','bg-warning','bg-danger');
                    myMSALObj.acquireTokenSilent({scopes: config.api.scopes, account: getAccount()}).then(tokenResponse => {
                        const headers = new Headers();
                        headers.append("Authorization", `Bearer ${tokenResponse.accessToken}`);
                        fetch(config.api.backend, {method: "GET", headers: headers})
                            .then(async (response)  => {
                                if (!response.ok)
                                {
                                    document.getElementById("message").innerHTML = "Error: " + response.status + " " + JSON.parse(await response.text()).message;
                                    document.getElementById("cardheader").classList.add('bg-warning');
                                }
                                else
                                {
                                    document.getElementById("cardheader").classList.add('bg-success');
                                    document.getElementById("message").innerHTML = await response.text();
                                }
                                }).catch(async (error) => {
                                    document.getElementById("cardheader").classList.add('bg-danger');
                                    document.getElementById("message").innerHTML = "Error: " + error;
                                });
                    }).catch(error => {console.log("Error Acquiring Token Silently: " + error);
                        return myMSALObj.acquireTokenRedirect({scopes: config.api.scopes, forceRefresh: false})
                    });
                    document.getElementById("progress").hidden = true;
             }
            function getAccount() {
                var accounts = myMSALObj.getAllAccounts();
                if (!accounts || accounts.length === 0) {
                    return null;
                } else {
                    return accounts[0];
                }
            }
        </script>
     </body>
    </html>
   ```

1. Přejděte na primární koncový bod statického webu, který jste předtím uložili v poslední části.

   > [!NOTE]
   > Gratulujeme, právě jste nasadili jednoduchou stránku JavaScriptu pro Azure Storage statického hostování obsahu.  
   > Vzhledem k tomu, že jsme aplikaci JS ještě nenakonfigurovali o Azure AD B2C Details – stránka se ještě nebude fungovat, když ji otevřete.

## <a name="configure-the-javascript-spa-for-azure-ad-b2c"></a>Konfigurace zabezpečeného hesla pro jazyk JavaScript pro Azure AD B2C

1. Teď víme, kde všechno je: můžeme nakonfigurovat zabezpečené ověřování pomocí odpovídající adresy rozhraní API API Management a správné Azure AD B2C ID aplikace nebo klienta.
1. Návrat do okna úložiště Azure Portal 
1. Vyberte ' Containers ' (v ' Settings ' ') 
1. Vyberte kontejner ' $web ' ze seznamu
1. V seznamu vyberte index.htmobjektů BLOB. 
1. Klikněte na Upravit. 
1. Aktualizujte hodnoty ověřování v konfiguračním oddílu msal tak, aby odpovídaly *front-endové* aplikaci, kterou jste předtím zaregistrovali v B2C. Použijte komentáře kódu pro tipy, jak by měly vypadat konfigurační hodnoty.
Hodnota *autority* musí být ve formátu:-https://{b2ctenantname}. b2clogin. com/TFP/{b2ctenantname}.. com}/{signupandsigninpolicyname}, pokud jste použili naše příklady názvů a váš tenant B2C se nazývá contoso, očekává se, že bude autorita https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com}/Frontendapp_signupandsignin .
1. Nastavte hodnoty rozhraní API tak, aby odpovídaly vaší adrese back-endu (základní adresa URL rozhraní API, kterou jste si poznamenali dříve, a hodnoty ' b2cScopes ' byly zaznamenány dříve pro *back-end aplikaci*).
1. Kliknutí na Uložit

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Nastavení identifikátorů URI pro přesměrování pro aplikaci Azure AD B2C front-endu

1. Otevřete okno Azure AD B2C a přejděte k registraci aplikace pro aplikaci JavaScript front-endu.
1. Klikněte na přesměrování identifikátorů URI a odstraňte zástupný symbol https://jwt.ms ' ', který jste zadali dříve.
1. Přidejte nový identifikátor URI pro primární koncový bod (úložiště) (mínus koncové lomítko).

   > [!NOTE]
   > Výsledkem této konfigurace bude klient aplikace front-end, který obdrží přístupový token s příslušnými deklaracemi identity z Azure AD B2C.  
   > Zabezpečené ověřování hesla bude možné v hlavičce HTTPS v volání rozhraní API back-endu přidat toto jako nosný token.  
   > 
   > API Management bude před předáním žádosti do přijímajícího rozhraní API Azure Functions předem ověřit token, voláním limitu četnosti na koncový bod, a to v závislosti na úrovni služby API Management, viz poznámka výše), než projde požadavek na příjem rozhraní API Azure functions a přidá klíč zabezpečení Functions.  
   > Zabezpečené ověřování hesla vygeneruje odpověď v prohlížeči.
   >
   > *Gratulujeme, nakonfigurovali jste Azure AD B2C, Azure API Management, Azure Functions, Azure App Service autorizaci pro práci v dokonalé harmonii!*

Teď máme jednoduchou aplikaci s jednoduchým zabezpečeným rozhraním API a zkusíme ji otestovat.

## <a name="test-the-client-application"></a>Testování klientské aplikace

1. Otevřete ukázkovou adresu URL aplikace, kterou jste si poznamenali z účtu úložiště, který jste vytvořili dříve.
1. V pravém horním rohu klikněte na Přihlásit se a na toto tlačítko se zobrazí okno Azure AD B2C registraci nebo přihlášení k profilu.
1. Aplikace by vás měla uvítacím názvem vašeho profilu B2C.
1. Nyní klikněte na volat rozhraní API a stránka by se měla aktualizovat o hodnoty odesílané zpět z vašeho zabezpečeného rozhraní API.
1. Pokud *opakovaně* kliknete na tlačítko rozhraní API pro volání a na úrovni pro vývojáře nebo nad API Management, měli byste si uvědomit, že vaše řešení bude začínat omezením rychlosti rozhraní API a tato funkce by měla být v aplikaci hlášena pomocí příslušné zprávy.

## <a name="and-were-done"></a>A hotovi

Výše uvedené kroky lze upravit a upravit tak, aby bylo možné v API Management použít řadu různých použití Azure AD B2C.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [Azure Active Directory a OAuth 2.0](../active-directory/develop/authentication-vs-authorization.md).
* Podívejte se na další [videa](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o API Management.
* Další způsoby zabezpečení back-endové služby najdete v tématu [vzájemné ověřování certifikátů](api-management-howto-mutual-certificates.md).
* [Vytvořte instanci služby API Management](get-started-create-service-instance.md).
* [Spravujte své první rozhraní API](import-and-publish.md).
