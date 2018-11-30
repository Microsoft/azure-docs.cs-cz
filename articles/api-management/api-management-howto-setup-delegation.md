---
title: Jak udělit oprávnění registrace a produktu předplatné uživatele
description: Naučíte se delegovat předplatné uživatele registrace a produktu nepředáme žádné třetí straně ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: f0050a91ca8ed380c838c96cf1e485a80a0c9297
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52445391"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Jak udělit oprávnění registrace a produktu předplatné uživatele
Delegování můžete použít svůj stávající web pro zpracování developer přihlašování – v registrace/přihlášení a odběru produktů na rozdíl od použití integrované funkce na portálu pro vývojáře. To umožňuje webu na vlastní uživatelská data a provést ověření z těchto kroků vlastním způsobem.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"> </a>Delegování pro vývojáře přihlášení a registraci
Na delegáta pro vývojáře přihlásit a zaregistrovat se do existujícího webu, je potřeba vytvořit koncový bod speciální delegování na vašem webu, který funguje jako vstupní bod pro takové žádosti iniciované z portálu pro vývojáře API Management.

Poslední pracovní postup bude následujícím způsobem:

1. Pro vývojáře kliknutí na odkaz přihlášení nebo registraci na portálu pro správu rozhraní API pro vývojáře
2. Prohlížeč přesměrován do koncového bodu delegace.
3. Na oplátku přesměruje nebo uvede uživatelského rozhraní uživatele s žádostí pro přihlášení v nebo registrace koncového bodu delegace.
4. V případě úspěchu bude uživatel přesměrován zpět na stránku portálu API Management pro vývojáře, spuštění z

Pokud chcete začít, Pojďme první nastavení API Management pro směrování požadavků prostřednictvím vašeho koncového bodu delegace. V portálu pro vydavatele API Management, klikněte na **zabezpečení** a potom klikněte na tlačítko **delegování** kartu. Klikněte na zaškrtávací políčko umožníte delegáta přihlášení a registraci.

![Stránku delegování][api-management-delegation-signin-up]

* Rozhodněte, co bude adresa URL koncového bodu delegace speciální být a zadejte ho v **adresa URL koncového bodu delegace** pole. 
* V rámci delegování ověřování pole klíče zadejte tajný kód, který se použije k výpočtu podpisu přesnost je potřeba pro ověřování, ujistěte se, že žádost pochází skutečně ze služby Azure API Management. Můžete kliknout **generovat** tlačítko rozhraní API správy náhodně generovat klíče pro vás.

Teď je potřeba vytvořit **koncového bodu delegace**. Je třeba provést několik akcí:

1. Zobrazí se žádost o ve tvaru:
   
   > *http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL zdrojové stránky} & řetězce salt = {řetězec} & sig = {řetězec}*
   > 
   > 
   
    Dotazování parametry pro přihlášení / registrace případ:
   
   * **operace**: Určuje typ žádosti o delegování je – lze pouze **SignIn** v tomto případě
   * **returnUrl**: adresa URL stránky, kde uživatel kliknul na odkaz, přihlaste se nebo zaregistrujte se
   * **řetězce Salt**: speciální řetězec salt používaný pro výpočet hodnoty hash zabezpečení
   * **SIG**: hash vypočítané zabezpečení se použije k porovnání s vlastním vypočtená hodnota hash
2. Ověřte, že žádost pochází ze služby Azure API Management (volitelný, ale důrazně doporučujeme pro zabezpečení)
   
   * Výpočetní HMAC SHA512 hash řetězce na základě **returnUrl** a **řetězce salt** parametrů dotazu ([ukázkového kódu uvedeného níže]):
     
     > Metoda HMAC (**řetězce salt** + '\n' + **returnUrl**)
     > 
     > 
   * Porovnání nad vypočítat hodnotu hash na hodnotu **sig** parametr dotazu. Pokud se tyto dvě hodnoty hash shodují, přejít k dalšímu kroku, jinak zamítnutí žádosti.
3. Ověřte, že vám posíláme, žádost o přihlášení v/sign nahoru: **operace** parametr dotazu bude nastavena na "**SignIn**".
4. Uživateli zprostředkovali uživatelského rozhraní pro nebo registrace
5. Pokud je uživatel registraci musíte vytvořit odpovídající účet pro ně ve službě API Management. [Vytvoření uživatele] pomocí REST API služby API Management. Pokud tak učiníte, ujistěte se nastavit ID uživatele do stejného, který je ve vašem úložišti uživatele nebo ID, které je možné, mějte přehled o.
6. Po úspěšném ověření uživatele:
   
   * [požádat o token jednotného přihlašování (SSO)] přes REST API služby API Management
   * Připojte parametr returnUrl dotazu na adresu URL jednotného přihlašování, obdržíte od volání rozhraní API výše:
     
     > Například https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * přesměruje uživatele na výše uvedenou adresu URL vyprodukované

Kromě **SignIn** operace, můžete také provádět správu účtů podle předchozích kroků a použijte jednu z následujících operací:

* **Metodu ChangePassword**
* **ChangeProfile**
* **CloseAccount**

Tyto parametry dotazu pro operace správy účtů, musíte předat.

* **operace**: Určuje, jaký typ žádosti o delegování je (ChangePassword, ChangeProfile nebo CloseAccount)
* **ID uživatele**: id uživatele účtu pro správu
* **řetězce Salt**: speciální řetězec salt používaný pro výpočet hodnoty hash zabezpečení
* **SIG**: hash vypočítané zabezpečení se použije k porovnání s vlastním vypočtená hodnota hash

## <a name="delegate-product-subscription"> </a>Delegování předplatné produktu
Delegování odběrem funguje podobně delegování uživatele přihlášení/nahoru. Poslední pracovní postup by měl vypadat takto:

1. Pro vývojáře vybere produktu na portálu pro vývojáře API Management a klikne na tlačítko přihlásit k odběru
2. Prohlížeč přesměrován do koncového bodu delegace.
3. Koncového bodu delegace. provede kroky předplatné požadovaný produkt – jedná se jenom na vás, může mít za následek přesměrování na jinou stránku požádat o fakturační údaje, s dotazem, máte další otázky, nebo jednoduše uložení informací a nevyžaduje žádné akce uživatele

K povolení funkcí, na **delegování** stránce klikněte na **delegovat předplatné produktu**.

Zajistěte, že koncového bodu delegace provede následující akce:

1. Zobrazí se žádost o ve tvaru:
   
   > *http://www.yourwebsite.com/apimdelegation?operation={operation}&productId={product Přihlaste se k odběru} & userId = {{uživatel provádění požadavku} & řetězce salt = {řetězec} & sig = {řetězec}*
   > 
   > 
   
    Parametry dotazu pro případ předplatné produktu:
   
   * **operace**: Určuje, jaký typ žádosti o delegování je. Pro předplatné produktu požadavky na platné možnosti jsou:
     * "Odběr upozornění": Zadaná žádost o předplatné pro uživatele pro daný produkt s ID (viz níže)
     * "Odhlásit": požadavek na odhlášení uživatele z produktu
     * "Obnovit": žádost o obnovení předplatného (pro příklad, který může být vypršení platnosti)
   * **productId**: ID produktů, uživatel si vyžádal přihlásit k odběru
   * **ID uživatele**: ID uživatele, pro kterého byla podána žádost
   * **řetězce Salt**: speciální řetězec salt používaný pro výpočet hodnoty hash zabezpečení
   * **SIG**: hash vypočítané zabezpečení se použije k porovnání s vlastním vypočtená hodnota hash
2. Ověřte, že žádost pochází ze služby Azure API Management (volitelný, ale důrazně doporučujeme pro zabezpečení)
   
   * COMPUTE SHA512 HMAC řetězce na základě **productId**, ** ID uživatele, a **řetězce salt** parametrů dotazu:
     
     > Metoda HMAC (**řetězce salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * Porovnání nad vypočítat hodnotu hash na hodnotu **sig** parametr dotazu. Pokud se tyto dvě hodnoty hash shodují, přejít k dalšímu kroku, jinak zamítnutí žádosti.
3. Provádět žádné zpracovávání předplatné produktu na základě typu operaci v **operace** – například fakturace, další otázky, atd.
4. Na úspěšně přihlášení k odběru produktu na vaší straně uživatele, předplatné pro uživatele do produktu API Management [volání rozhraní REST API pro předplatné produktu].

## <a name="delegate-example-code"> </a> Příklad kódu
Tyto ukázky kódu ukazují, jak provést *ověřovací klíč delegace*, který je nastaven na obrazovce delegování z portálu pro vydavatele, k vytvoření HMAC, které se pak použije k ověření podpisu, prokázání platnosti předané returnUrl. Stejný kód funguje pro productId a ID uživatele s drobné změny.

**C#kód ke generování hodnoty hash returnUrl**

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

**Pro vytvoření hodnoty hash returnUrl kód NodeJS**

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

## <a name="next-steps"></a>Další postup
Další informace o delegování najdete v následujícím videu:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[požádat o token jednotného přihlašování (SSO)]: https://docs.microsoft.com/rest/api/apimanagement/User/GenerateSsoUrl
[Vytvoření uživatele]: https://docs.microsoft.com/rest/api/apimanagement/user/createorupdate
[volání rozhraní REST API pro předplatné produktu]: https://docs.microsoft.com/rest/api/apimanagement/productsubscriptions
[Next steps]: #next-steps
[ukázkového kódu uvedeného níže]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
