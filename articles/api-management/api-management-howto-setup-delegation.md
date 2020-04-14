---
title: Jak delegovat registraci uživatelů a předplatné produktu
description: Zjistěte, jak delegovat registraci uživatelů a předplatné produktu na třetí stranu ve správě rozhraní Azure API.
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
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: c28872e6cffa973f01b3f5a87c423d9dd93a2aa5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259098"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Jak delegovat registraci uživatelů a předplatné produktu

Delegování umožňuje používat stávající web pro zpracování vývojáře přihlášení nebo registraci a předplatné produktů, na rozdíl od použití integrované funkce na portálu pro vývojáře. Umožňuje vašim webovým stránkám vlastnit uživatelská data a provádět ověření těchto kroků vlastním způsobem.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>Delegování přihlášení a registrace vývojáře

Chcete-li delegovat vývojáře, přihlásit se a přihlásit se na svůj stávající web, budete muset na svém webu vytvořit speciální koncový bod delegování. Musí fungovat jako vstupní bod pro všechny takové požadavky iniciované z portálu pro vývojáře správy rozhraní API.

Konečný pracovní postup bude následující:

1. Vývojář klikne na přihlašovací nebo zaregistrovací odkaz na portálu pro vývojáře api managementu.
2. Prohlížeč je přesměrován na koncový bod delegování.
3. Koncový bod delegování na oplátku přesměruje na nebo představuje uživatelské rozhraní s žádostí o přihlášení nebo registraci uživatele
4. Pokud je uživatel úspěšný, je přesměrován zpět na stránku portálu pro vývojáře pro správu rozhraní API, ze které začal

Chcete-li začít, pojďme nejprve nastavit správu rozhraní API pro směrování požadavků prostřednictvím koncového bodu delegování. Na webu Azure Portal vyhledejte **zabezpečení** ve vašem prostředku správy rozhraní API a klikněte na položku **Delegování.** Kliknutím na zaškrtávací políčko povolíte možnost Přihlásit se & se zaregistrovat.Click the check box to enable 'Delegate sign in & sign up'.

![Stránka delegování][api-management-delegation-signin-up]

* Rozhodněte, jaká bude adresa URL vašeho zvláštního koncového bodu delegování, a zadejte ji do pole **URL koncového bodu delegování.** 
* V rámci pole ověřovacíklíč delegování zadejte tajný klíč, který se použije k výpočtu podpisu, který vám byl poskytnut k ověření, aby bylo zajištěno, že požadavek skutečně pochází ze správy rozhraní Azure API. Kliknutím na tlačítko **generovat** můžete mít api management náhodně generovat klíč pro vás.

Nyní je třeba vytvořit **koncový bod delegování**. Musí provést řadu akcí:

1. Obdržíte žádost v následujícím formuláři:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL zdrojové stránky}&salt={string}&sig={string}*
   > 
   > 
   
    Parametry dotazu pro případ přihlášení nebo registrace:
   
   * **operace**: určuje, o jaký typ žádosti o delegování se jedná - v tomto případě může být **pouze Přihlášení**
   * **returnUrl**: URL stránky, na které uživatel kliknul na přihlašovací nebo registrační odkaz
   * **sůl**: speciální solný řetězec používaný pro výpočet bezpečnostního hašiše
   * **sig**: vypočítaný hash zabezpečení, který má být použit pro srovnání s vlastní vypočítanou hash
2. Ověřte, že požadavek pochází ze správy rozhraní AZURE API (volitelné, ale vysoce doporučeno pro zabezpečení)
   
   * Vypočítejte hodnotu hash HMAC-SHA512 řetězce na základě parametrů dotazu **returnUrl** a **salt** [(příklad kódu uvedeného níže]):
     
     > HMAC(**sůl** + '\n' + **returnUrl**)
     > 
     > 
   * Porovnejte výše vypočítaný hash s hodnotou parametru **sig** dotazu. Pokud se dva hashy shodují, přejděte k dalšímu kroku, jinak požadavek zamítnete.
3. Ověřte, zda přijímáte požadavek na přihlášení/registraci: parametr **operačního** dotazu bude nastaven na "**SignIn**".
4. Představit uživateli uživatelské rozhraní pro přihlášení nebo registraci
5. Pokud se uživatel přihlašuje, musíte pro ně vytvořit odpovídající účet ve správě rozhraní API. [Vytvořte uživatele] pomocí rozhraní REST API pro správu rozhraní API. Přitom se ujistěte, že jste nastavili ID uživatele na stejnou hodnotu jako v úložišti uživatelů nebo na ID, které můžete sledovat.
6. Když je uživatel úspěšně ověřen:
   
   * [vyžádání jednotného přihlašování (SSO) token] prostřednictvím rozhraní API pro správu rozhraní API
   * Připojit parametr dotazu returnUrl k adrese URL služby SSO, kterou jste obdrželi z výše uvedeného volání rozhraní API:
     
     > napříkladhttps://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * přesměrovat uživatele na výše vyprodukovanou adresu URL

Kromě operace **Přihlášení** můžete také provádět správu účtu podle předchozích kroků a pomocí jedné z následujících operací:

* **Changepassword**
* **Změnit profil**
* **Zavřítúčet**

Pro operace správy účtů je nutné předat následující parametry dotazu.

* **operace**: určuje, jaký typ požadavku na delegování je (ChangePassword, ChangeProfile nebo CloseAccount)
* **userId**: ID uživatele účtu pro správu
* **sůl**: speciální solný řetězec používaný pro výpočet bezpečnostního hašiše
* **sig**: vypočítaný hash zabezpečení, který má být použit pro srovnání s vlastní vypočítanou hash

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>Delegování předplatného produktu
Delegování předplatného produktu funguje podobně jako delegování přihlášení/přístupu uživatele. Konečný pracovní postup by byl následující:

1. Vývojář vybere produkt na portálu pro vývojáře správy rozhraní API a klikne na tlačítko Přihlásit se k odběru.
2. Prohlížeč je přesměrován na koncový bod delegování.
3. Koncový bod delegování provádí požadované kroky předplatného produktu. Je na vás, abyste navrhli kroky. Mohou zahrnovat přesměrování na jinou stránku, která požaduje fakturační údaje, kladení dalších otázek nebo pouhé ukládání informací a nevyžaduje žádnou akci uživatele.

Chcete-li tuto funkci povolit, klikněte na stránce **Delegování** na **příkaz Delegovat předplatné produktu**.

Dále zajistěte, aby koncový bod delegování provádí následující akce:

1. Obdržíte žádost v následujícím formuláři:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={operation}&productId={product to subscribe to}&userId={user making request}&salt={string}&sig={string}*
   >
   
    Parametry dotazu pro případ předplatného produktu:
   
   * **operace**: určuje, o jaký typ žádosti o delegování se jedná. Pro požadavky na předplatné produktu jsou platné možnosti:
     * "Přihlásit se": žádost o přihlášení uživatele k odběru daného produktu s poskytnutým ID (viz níže)
     * "Odhlásit se": žádost o odhlášení uživatele z produktu
     * "Obnovit": žádost o obnovení předplatného (například, která může být vypršení platnosti)
   * **ProductId**: ID produktu, o jehož přihlášení uživatel požádal, aby se přihlásil
   * **subscriptionID**: on *Unsubscribe* and *Renew* - ID předplatného produktu
   * **userId**: ID uživatele, pro které je žádost podána
   * **sůl**: speciální solný řetězec používaný pro výpočet bezpečnostního hašiše
   * **sig**: vypočítaný hash zabezpečení, který má být použit pro srovnání s vlastní vypočítanou hash

2. Ověřte, že požadavek pochází ze správy rozhraní AZURE API (volitelné, ale vysoce doporučeno pro zabezpečení)
   
   * Vypočítat HMAC-SHA512 řetězce na základě **productId**, **userId**a **salt** query parametry:
     
     > HMAC(**sůl** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * Porovnejte výše vypočítaný hash s hodnotou parametru **sig** dotazu. Pokud se dva hashy shodují, přejděte k dalšímu kroku, jinak požadavek zamítnete.
3. Zpracovat předplatné produktu na základě typu operace požadované v **provozu** - například fakturace, další otázky atd.
4. Při úspěšném přihlášení uživatele k odběru produktu na vaší straně se přihlaste k odběru produktu API Management voláním rozhraní [REST API pro odběry].

## <a name="example-code"></a><a name="delegate-example-code"> </a> Ukázkový kód

Tyto ukázky kódu ukazují, jak:

* Vezměte *ověřovací klíč delegování*, který je nastaven na obrazovce Delegování na portálu pro vydavatele.
* Vytvořte HMAC, který se pak používá k ověření podpisu, prokazující platnost předané returnUrl.

Stejný kód funguje pro productId a userId s mírnou úpravou.

**C# kód pro generování hash returnUrl**

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

**NodeJS kód pro generování hash returnUrl**

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
> Je třeba [znovu publikovat portál pro vývojáře](api-management-howto-developer-portal-customize.md#publish) pro delegování se projeví.

## <a name="next-steps"></a>Další kroky
Další informace o delegování najdete v následujícím videu:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[vyžádání tokenu jednotného přihlašování (SSO)]: https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/User/GenerateSsoUrl
[vytvoření uživatele]: https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/user/createorupdate
[volání rozhraní REST API pro odběry]: https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/subscription/createorupdate
[Next steps]: #next-steps
[příklad kódu uvedeného níže]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
