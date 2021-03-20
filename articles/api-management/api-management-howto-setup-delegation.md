---
title: Delegování registrace uživatele a předplatného produktu
description: Přečtěte si, jak delegovat registraci uživatelů a předplatné produktů na třetí stranu v Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: 54193c9333c75fd8b973ebe33470fca3617e2f2d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93341837"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Delegování registrace uživatele a předplatného produktu

Delegování vám umožňuje používat stávající web ke zpracování přihlašování a předplatného pro vývojáře k produktům, a to na rozdíl od používání integrované funkce portálu pro vývojáře. Umožňuje vašemu webu vlastnit uživatelská data a provádět ověřování těchto kroků vlastním způsobem.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>Delegování přihlašování a registrace vývojářů

Abyste mohli delegovat vývojáře, přihlaste se a přihlaste se k existujícímu webu, budete muset na svém webu vytvořit speciální koncový bod delegování. Musí fungovat jako vstupní bod pro všechny takové žádosti iniciované z API Management portálu pro vývojáře.

Konečný pracovní postup bude následující:

1. Vývojář klikne na odkaz Přihlásit se nebo zaregistrovat na portálu pro vývojáře API Management
2. Prohlížeč se přesměruje na koncový bod delegování.
3. Koncový bod delegování v vratcích se přesměrovává na nebo prezentuje uživatelské rozhraní žádající uživatele o přihlášení nebo registraci.
4. Po úspěšném dokončení se uživatel přesměruje zpátky na stránku API Management portálu pro vývojáře, ze které začala.

Začněte tím, že nejprve nastavíte API Management pro směrování požadavků prostřednictvím vašeho koncového bodu delegování. V Azure Portal vyhledejte v prostředku API Management **zabezpečení** a pak klikněte na položku **delegování** . Kliknutím na zaškrtávací políčko povolíte delegáta & přihlásit se.

![Stránka delegování][api-management-delegation-signin-up]

* Rozhodněte, jaká adresa URL vašeho speciálního koncového bodu delegování bude a zadejte ji do pole **Adresa URL koncového bodu delegování** . 
* V poli klíč pro ověření delegování zadejte tajný klíč, který se použije k výpočtu podpisu, který jste dostali k ověření, aby se zajistilo, že požadavek bude skutečně přijít z Azure API Management. Kliknutím na tlačítko **Generovat** můžete API Management pro vás vygenerovat náhodně klíč.

Nyní je třeba vytvořit **koncový bod delegování**. Je potřeba provést několik akcí:

1. Příjem žádosti v následujícím formátu:
   
   > *http: \/ /www.yourwebsite.com/apimdelegation?operation=SignIn&ReturnUrl = {adresa URL zdrojové stránky} &Salt = {String} &SIG = {String}*
   
    Parametry dotazu pro případ přihlášení/přihlášení:
   
   * **operace**: Určuje, který typ požadavku na delegování je. v takovém případě se může **Přihlásit** pouze v tomto případě.
   * **ReturnUrl**: adresa URL stránky, na které se uživatel kliknul na přihlášení nebo odkaz pro registraci
   * **Salt**: speciální řetězec Salt používaný k výpočtu hodnoty hash zabezpečení
   * **SIG**: vypočítaná hodnota hash zabezpečení, která se má použít pro porovnání s vámi vypočítanou hodnotou hash
2. Ověřte, že požadavek přichází z Azure API Management (volitelné, ale důrazně se doporučuje pro zabezpečení).
   
   * Vypočítat hodnotu hash HMAC-SHA512 řetězce na základě parametrů dotazu **ReturnUrl** a **Salt** ([vzorový kód uvedený níže]):
     
     > HMAC (**sůl** + ' \n ' + **ReturnUrl**)

   * Porovná výše vypočítanou hodnotu hash s hodnotou parametru dotazu **SIG** . Pokud se dvě hodnoty hash shodují, přejděte k dalšímu kroku, jinak zakažte požadavek.
3. Ověřte, že jste obdrželi žádost o přihlášení nebo přihlášení: parametr dotazu **operace** bude nastaven na "Signing" (přihlásit **se).**
4. Prezentovat uživatele s uživatelským ROZHRANÍm pro přihlášení nebo registraci
5. Pokud se uživatel přihlásí, musíte pro ně vytvořit odpovídající účet v API Management. [Vytvoří uživatele] s REST APIem API Management. Když to uděláte, ujistěte se, že jste nastavili ID uživatele na stejnou hodnotu jako ve vašem úložišti uživatelů nebo na ID, které můžete sledovat.
6. Po úspěšném ověření uživatele:
   
   * [Vyžádání sdíleného přístupového tokenu] přes API Management REST API
   * Přidejte parametr dotazu returnUrl k adrese URL jednotného přihlašování, kterou jste dostali z volání rozhraní API výše:
     
     > například `https://<developer portal domain, for example: contoso.developer.azure-api.net>/signin-sso?token=<URL-encoded token>&returnUrl=<URL-encoded URL, for example: %2Freturn%2Furl>` 
     
   * Přesměruje uživatele na výše vytvořenou adresu URL.

Kromě operace **přihlášení** můžete také provádět správu účtů podle předchozích kroků a pomocí jedné z následujících operací:

* **Metodu ChangePassword**
* **ChangeProfile**
* **CloseAccount**
* **SignOut**

Pro operace správy účtů musíte předat následující parametry dotazu.

* **operace**: Určuje, který typ požadavku na delegování je (ChangePassword, ChangeProfile nebo CloseAccount).
* **userId**: ID uživatele účtu, který se má spravovat
* **Salt**: speciální řetězec Salt používaný k výpočtu hodnoty hash zabezpečení
* **SIG**: vypočítaná hodnota hash zabezpečení, která se má použít pro porovnání s vámi vypočítanou hodnotou hash

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>Delegování předplatného produktu

Delegování předplatného produktu funguje podobně jako delegování přihlášení uživatele. Konečný pracovní postup by byl následující:

1. Vývojář vybere produkt na portálu pro vývojáře API Management a klikne na tlačítko přihlásit k odběru.
2. Prohlížeč je přesměrován na koncový bod delegování.
3. Koncový bod delegování provádí požadované kroky odběru produktu. Pro návrh kroků je to na vás. Mohou zahrnovat přesměrování na jinou stránku, aby vyžadovaly informace o fakturaci, požadovali další otázky nebo jednoduše ukládali informace a nevyžadovali akci uživatele.

Pokud chcete tuto funkci povolit, na stránce **delegování** klikněte na **delegovat předplatné produktu**.

Dále zajistěte, aby koncový bod delegování provede následující akce:

1. Příjem žádosti v následujícím formátu:
   
   > *http: \/ /www.yourwebsite.com/apimdelegation? operace = {Operation} &ProductID = {produkt pro přihlášení k odběru} &userId = {usering Request} &Salt = {String} &SIG = {String}*
   >
   
    Parametry dotazu pro případ odběru produktu:
   
   * **operace**: Určuje, jaký typ požadavku na delegování je. U předplatného produktu si vyžádá platné možnosti:
     * "Předplatné": žádost o přihlášení uživatele k danému produktu se zadaným ID (viz níže)
     * "Zrušit odběr": požadavek na zrušení odběru uživatele z produktu
     * "Prodloužit": požadavek na obnovení předplatného (například může vypršet platnost)
   * **ProductID**: po *přihlášení k odběru* – ID produktu, který uživatel požádal o přihlášení k odběru
   * **SubscriptionId**: při *zrušení odběru* a *obnovení* – ID předplatného produktu
   * **userId**: on *předplatné* – ID uživatele, pro který se žádost vytvořila
   * **Salt**: speciální řetězec Salt používaný k výpočtu hodnoty hash zabezpečení
   * **SIG**: vypočítaná hodnota hash zabezpečení, která se má použít pro porovnání s vámi vypočítanou hodnotou hash

2. Ověřte, že požadavek přichází z Azure API Management (volitelné, ale důrazně se doporučuje pro zabezpečení).
   
   * Vypočítat HMAC-SHA512 řetězce na základě parametrů dotazu **ProductID**, **userId** a **Salt** :
     
     > HMAC (**sůl** + ' \n ' + **ProductID** + ' \n ' + **userId**)
     > 
     > 
   * Porovná výše vypočítanou hodnotu hash s hodnotou parametru dotazu **SIG** . Pokud se dvě hodnoty hash shodují, přejděte k dalšímu kroku, jinak zakažte požadavek.
3. Zpracuje předplatné produktu na základě typu operace požadované v **operaci** – například fakturace, další otázky atd.
4. Po úspěšném přihlášení uživatele k produktu na vaši stranu se přihlaste k odběru API Management produktu tím, že [zavoláte REST API pro předplatná].

## <a name="example-code"></a><a name="delegate-example-code"></a> Příklad kódu

Tyto ukázky kódu ukazují, jak:

* Pořídit *klíč pro ověření delegování*, který je nastavený na obrazovce delegování na portálu vydavatele
* Vytvořte HMAC, který se pak použije k ověření podpisu a určení platnosti úspěšného returnUrl.

Stejný kód funguje pro productId a userId s mírnou úpravou.

**Kód jazyka C# pro generování hodnoty hash returnUrl**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**NodeJS kód pro vygenerování hodnoty hash returnUrl**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> Aby se změny delegování projevily, je potřeba [znovu publikovat portál pro vývojáře](api-management-howto-developer-portal-customize.md#publish) .

## <a name="next-steps"></a>Další kroky
Další informace o delegování najdete v následujícím videu:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[Vyžádání sdíleného přístupového tokenu]: /rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken
[Vytvoření uživatele]: /rest/api/apimanagement/2019-12-01/user/createorupdate
[volání REST API pro odběry]: /rest/api/apimanagement/2019-12-01/subscription/createorupdate
[Next steps]: #next-steps
[příklad kódu poskytnutý níže]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
