---
title: Rozhraní API pro splnění SaaS v2 na komerčním webu Microsoft Marketplace
description: Naučte se vytvářet a spravovat SaaS nabídku na Microsoft AppSource a Azure Marketplace pomocí rozhraní API pro splnění verze 2.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2acf5178e7d1cfdf907146d733150a48e9696a5e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712347"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>Rozhraní API pro splnění SaaS verze 2 na komerčním webu Marketplace

Tento článek obsahuje podrobnosti o rozhraních API, která umožňují partnerům prodávat jejich nabídky software jako služba (SaaS) v Microsoft AppSource a Azure Marketplace. Vydavatel je nutný k implementaci integrace s těmito rozhraními API k publikování nabídky SaaS s podporou transakcí v partnerském centru.

## <a name="managing-the-saas-subscription-life-cycle"></a>Správa životního cyklu předplatného SaaS

Komerční tržiště spravuje celý životní cyklus předplatného SaaS po jeho nákupu koncovým uživatelem. Používá cílovou stránku, rozhraní API pro plnění, rozhraní API pro provoz a Webhook jako mechanismus pro řízení skutečného SaaS předplatného, použití, aktualizací a zrušení. Faktura koncového uživatele vychází ze stavu předplatného SaaS, které Microsoft udržuje. 

### <a name="states-of-a-saas-subscription"></a>Stavy předplatného SaaS

Následující diagram znázorňuje stavy předplatného SaaS a příslušné akce.

![Diagram znázorňující životní cyklus softwaru jako předplatné služby na webu Marketplace.](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>Koupeno, ale ještě Neaktivováno (*PendingFulfillmentStart*)

Po zakoupení nabídky SaaS koncovým uživatelem (nebo CSP) na komerčním webu Marketplace by se Vydavatel měl informovat o nákupu. Vydavatel pak může vytvořit a nakonfigurovat nový účet SaaS na straně vydavatele pro koncového uživatele.

Vytvoření účtu, který se má vyskytnout:

1. Zákazník vybere tlačítko **Konfigurovat** , které je dostupné pro SaaS nabídku po jeho úspěšném nákupu v Microsoft AppSource nebo Azure Portal. Alternativně může zákazník použít tlačítko **Konfigurovat** v e-mailu, které budou dostávat krátce po nákupu.
2. Společnost Microsoft pak oznámí partnerovi o nákupu otevřením na kartě nový prohlížeč adresa URL cílové stránky s parametrem token (identifikační token nákupu z komerčního tržiště).

Příkladem takového volání je `https://contoso.com/signup?token=<blob>` , že adresa URL cílové stránky této nabídky SaaS v partnerském centru je nakonfigurována jako `https://contoso.com/signup` . Tento token poskytuje vydavateli s ID, které jedinečně identifikuje SaaS nákup a zákazníka.

>[!NOTE]
>Vydavatel se nebude informovat o nákupu SaaS, dokud zákazník nespustí proces konfigurace ze strany Microsoftu.

Adresa URL cílové stránky musí být spuštěná a běžet každý den, každý den a připravena přijímat nová volání od Microsoftu. Pokud cílová stránka přestane být k dispozici, zákazníci se nebudou moci zaregistrovat ke službě SaaS a začít ji používat.

Dále musí vydavatel předat *token* zpět společnosti Microsoft voláním [rozhraní SaaS Resolve API](#resolve-a-purchased-subscription)a zadáním tokenu jako hodnoty `x-ms-marketplace-token header` parametru Header. V důsledku volání rozhraní API se vyměňují tokeny, abyste získali podrobnosti o nákupu SaaS, jako je jedinečné ID nákupu, zakoupeného ID nabídky a ID zakoupeného plánu.

Na cílové stránce by měl být Zákazník přihlášený k novému nebo existujícímu účtu SaaS prostřednictvím jednotného přihlašování (SSO) Azure Active Directory (Azure AD).

Vydavatel by měl implementovat jednotné přihlašování a poskytovat tak uživatelské prostředí, které Microsoft pro tento tok vyžaduje. Při konfiguraci jednotného přihlašování se ujistěte, že používáte aplikaci Azure AD s více klienty, a povolíte jak pracovní, tak školní účty nebo osobní účty Microsoft. Tento požadavek se vztahuje pouze na cílovou stránku pro uživatele, kteří jsou přesměrováni na službu SaaS, pokud už se k ní přihlásili pomocí přihlašovacích údajů Microsoftu. Jednotné přihlašování není vyžadováno pro všechna přihlášení ke službě SaaS.

> [!NOTE]
>Pokud SSO vyžaduje, aby správce mohl udělit oprávnění k aplikaci, popis nabídky v partnerském centru musí zveřejnit tento přístup na úrovni správce. Toto zveřejnění je v rozporu se [zásadami certifikace na komerčním webu](/legal/marketplace/certification-policies#10003-authentication-options).

Po přihlášení by zákazník měl dokončit konfiguraci SaaS na straně vydavatele. Vydavatel pak musí volat [rozhraní API Aktivace předplatného](#activate-a-subscription) k odeslání signálu, který Azure Marketplace, že zřízení účtu SaaS bylo dokončeno.
Tato akce spustí fakturační cyklus zákazníka. Pokud volání rozhraní API Aktivace předplatného není úspěšné, zákazník se nebude účtovat za nákup.


![Diagram znázorňující volání A P I pro scénář zřizování.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>Aktivní (*odebírané*)

*Aktivní (odebírané)* je stálý stav zřízeného předplatného SaaS. Jakmile strana Microsoftu zpracuje volání [rozhraní API pro aktivaci předplatného](#activate-a-subscription) , je předplatné SaaS označené jako *odebírané*. Zákazník teď může používat službu SaaS na straně vydavatele a bude se účtovat.

Když je předplatné SaaS už aktivní, zákazník může vybrat **Spravovat prostředí SaaS** z Azure Portal nebo centra pro správu Microsoft 365. Tato akce také způsobí, že společnost Microsoft volá **adresu URL cílové stránky** s parametrem *token* , jak se stane v aktivovaném toku. Vydavatel by měl rozlišovat mezi novým nákupem a správou stávajících účtů SaaS a tuto adresu URL této cílové stránky zpracovat odpovídajícím způsobem.

#### <a name="being-updated-subscribed"></a>Probíhá aktualizace (*odebírané*)

Tato akce znamená, že aktualizace existujícího předplatného Active SaaS je zpracovávána společností Microsoft i vydavatelem. Tuto aktualizaci může iniciovat:

- Zákazník z komerčního tržiště.
- CSP z komerčního tržiště.
- Zákazník z webu pro SaaS vydavatele (ale ne pro nákupy vytvořené pomocí CSP).

K dispozici jsou dva typy aktualizací pro předplatné SaaS:

- Aktualizujte plán, když zákazník zvolí jiný plán pro předplatné.
- Aktualizujte množství, když zákazník změní počet zakoupených stanic pro předplatné.

Aktualizovat se dá jenom aktivní předplatné. I když se předplatné aktualizuje, zůstane jeho stav aktivní na straně Microsoftu.

##### <a name="update-initiated-from-the-commercial-marketplace"></a>Aktualizace iniciovaná z komerčního tržiště

V tomto toku zákazník změní plán předplatného nebo množství stanic z Azure Portal nebo centra pro správu Microsoft 365.

1. Po zadání aktualizace Microsoft zavolá adresu URL Webhooku vydavatele, nakonfigurovanou v poli **Webhook připojení** v partnerském centru, s příslušnou hodnotou pro *akci* a další relevantní parametry. 
1. Strana vydavatele by měla provést požadované změny ve službě SaaS a po dokončení voláním [stavu aktualizace rozhraní API provozu](#update-the-status-of-an-operation)informovat společnost Microsoft.
1. Pokud je oprava odeslána se stavem *selhání* , proces aktualizace se nedokončí na straně Microsoftu. Předplatné SaaS zachová stávající plán a množství míst.

> [!NOTE]
> Po přijetí oznámení Webhooku by měl Vydavatel vyvolat opravu pro [aktualizaci stavu rozhraní API operace](#update-the-status-of-an-operation) s odpovědí na selhání nebo úspěšnost *během 10 sekund okna* . Pokud se do 10 sekund neobdrží oprava stavu operace, plán změny se *automaticky opraví jako úspěšný*. 

Pořadí volání rozhraní API pro scénář aktualizace, který je iniciované z komerčního tržiště, je znázorněno v následujícím diagramu.

![Diagram znázorňující volání A P I pro aktualizaci iniciované na webu Marketplace](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>Aktualizace iniciovaná od vydavatele

V tomto toku zákazník změní plán předplatného nebo množství míst zakoupených ze samotné služby SaaS. 

1. Před provedením požadované změny na straně vydavatele musí kód vydavatele volat [rozhraní API pro změnu plánu](#change-the-plan-on-the-subscription) a/nebo [rozhraní API změny množství](#change-the-quantity-of-seats-on-the-saas-subscription) . 

1. Microsoft použije změnu u předplatného a pak pošle vydavateli pomocí **Webhooku připojení** , aby se projevila stejná změna.

1. Teprve potom by měl Vydavatel provést požadovanou změnu předplatného SaaS a informovat společnost Microsoft, když se změna provede voláním [stavu aktualizace rozhraní API operace](#update-the-status-of-an-operation).

Pořadí volání rozhraní API pro scénář aktualizace, který je iniciován na straně vydavatele, je znázorněno v následujícím diagramu.

![Diagram znázorňující volání pro zahájení aktualizace na straně vydavatele A P](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>Pozastaveno (*pozastaveno*)

Tento stav indikuje, že platba zákazníka za službu SaaS nebyla přijata. Vydavatel bude upozorněn na tuto změnu ve stavu předplatného SaaS od Microsoftu. Oznámení se provádí prostřednictvím volání Webhooku s parametrem *Action* nastaveným na *pozastaveno*.

Vydavatel může nebo nemusí provádět změny ve službě SaaS na straně vydavatele. Doporučujeme, aby tento Vydavatel tyto informace k dispozici pro pozastaveného zákazníka a omezení nebo zablokoval přístup zákazníka ke službě SaaS. Existuje pravděpodobnost, že platba nebude nikdy přijata.

Microsoft uděluje zákazníkovi 30denní dobu odkladu před automatickým zrušením předplatného. Když je předplatné v *pozastaveném* stavu:

* Partner nebo ISV musí ponechat účet SaaS v obnovitelném stavu, aby bylo možné obnovit všechny funkce bez ztráty dat nebo nastavení.
* Partner nebo ISV by měl očekávat požadavek na obnovení předplatného, pokud je platba přijatá během období odkladu, nebo žádost o zrušení zřízení předplatného na konci období odkladu. Oba požadavky budou odesílány pomocí mechanismu Webhooku.

Stav předplatného se změní na pozastaveno na straně Microsoftu předtím, než vydavatel provede jakoukoli akci. Pozastavit lze pouze aktivní odběry.

#### <a name="reinstated-suspended"></a>Obnovené (*pozastavené*)

Tato akce indikuje, že platební nástroj zákazníka se znovu stane platným, platba za předplatné SaaS a předplatné se obnoví. V tomto případě: 

1. Společnost Microsoft volá Webhook s parametrem *Akce* nastaveným na hodnotu *obnovit* .
1. Vydavatel se ujistěte, že odběr je na straně vydavatele opět plně funkční.
1. Vydavatel volá [rozhraní API pro operaci opravy](#update-the-status-of-an-operation) se stavem úspěch.
1. Proces obnovení je úspěšný a u předplatného SaaS se zákazníkovi účtuje znovu. 

Pokud je oprava odeslána se stavem *selhání* , proces obnovení se nedokončí na straně Microsoftu a předplatné zůstane *pozastaveno*.

Obnovit lze pouze pozastavené předplatné. Pozastavené předplatné SaaS zůstane v *pozastaveném* stavu, když ho znovu vystavíte. Po dokončení této operace se stav předplatného stane *aktivní*.

#### <a name="renewed-subscribed"></a>Obnoveno (*odebírané*)

Předplatné SaaS se od Microsoftu automaticky obnoví na konci období předplatného v měsíci nebo roce. Výchozí nastavení automatického obnovování *platí pro všechna* předplatná SaaS. Aktivní předplatná SaaS se budou obnovovat běžným tempo. Společnost Microsoft neupozorní vydavatele na obnovení předplatného. Zákazník může vypnout automatické obnovení předplatného SaaS prostřednictvím portálu pro správu Microsoft 365. V tomto případě se předplatné SaaS na konci aktuálního fakturačního období automaticky zruší. Zákazníci si také můžou předplatné SaaS kdykoli zrušit.

Automaticky se obnoví pouze aktivní odběry. Předplatná zůstávají aktivní během procesu obnovení a pokud je automatické obnovení úspěšné. Po obnovení se počáteční a koncové datum období předplatného aktualizuje na data nového období.

Pokud automatické obnovení selže kvůli problému s platbou, předplatné se *pozastaví* a pošle se vydavateli oznámení.

#### <a name="canceled-unsubscribed"></a>Zrušeno (*odhlášeno*) 

Předplatné dostanou tento stav v reakci na explicitní akci zákazníka nebo CSP zrušením předplatného z webu vydavatele, Azure Portal nebo centra pro správu Microsoft 365. Předplatné je taky možné implicitně zrušit, protože se v důsledku nedoplatku poplatků po dobu 30 dnů účtuje jako *pozastavený* stav.

Poté, co vydavatel obdrží volání Webhooku zrušení, by měl uchovávat zákaznická data pro obnovení na vyžádání po dobu alespoň sedmi dnů. Data zákazníků se pak dají odstranit jenom vy.

Předplatné SaaS se dá kdykoli zrušit v rámci svého životního cyklu. Po zrušení předplatného se nedá znovu aktivovat.

## <a name="api-reference"></a>API – referenční informace

Tato část popisuje rozhraní API pro odběr a Operations SaaS.

**Rozhraní API pro odběry** by se měla použít ke zpracování životního cyklu předplatného SaaS z nákupu na zrušení.

**Rozhraní API operací** by se měla použít pro:

* Ověřte a potvrďte zpracovaná volání Webhooku.
* Získejte seznam aplikací čekajících na operace, které čekají na potvrzení vydavatelem.

> [!NOTE]
> Verze TLS verze 1,2 bude brzy vynutila jako minimální verze komunikace pomocí protokolu HTTPS. Ujistěte se, že ve svém kódu používáte tuto verzi protokolu TLS. Protokoly TLS verze 1,0 a 1,1 budou brzy zastaralé.

### <a name="subscription-apis"></a>Rozhraní API pro předplatné

#### <a name="resolve-a-purchased-subscription"></a>Řešení zakoupeného předplatného

Koncový bod umožňuje vydavateli vyměňovat si identifikační token nákupu od komerčního tržiště (označovaného jako *token* při [nákupu, ale ještě neaktivovaný](#purchased-but-not-yet-activated-pendingfulfillmentstart)) s trvale zakoupeným ID předplatného SaaS a jeho podrobnostmi.

Když se zákazník přesměruje na adresu URL cílové stránky partnerského serveru, jako parametr *tokenu* v tomto volání adresy URL se předává identifikační token zákazníka. U partnera se očekává, že tento token použijete a vyžádáte ho k vyřešení. Odpověď rozhraní API obsahuje ID předplatného SaaS a další podrobnosti k jedinečné identifikaci nákupu. *Token* , který je k dispozici s VOLÁNÍM adresy URL cílové stránky, je obvykle platný po dobu 24 hodin. Pokud již vypršela platnost *tokenu* , který jste obdrželi, doporučujeme koncovému uživateli poskytnout následující pokyny:

"Tento nákup jsme nedokázali identifikovat. Otevřete prosím toto předplatné SaaS znovu v Azure Portal nebo v centru pro správu Microsoft 365 a pak znovu vyberte konfigurovat účet nebo spravovat účet.

Volání řešení Resolve API vrátí podrobnosti o předplatném a stav SaaS předplatných ve všech podporovaných stavech.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Spuštění `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Parametry dotazu:*

|  Parametr         | Hodnota            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Použijte 2018-08-31.   |

*Hlavičky žádosti:*

|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
|  `x-ms-correlationid` |  Jedinečná řetězcová hodnota pro operaci na klientovi. Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  `authorization`     |  Jedinečný přístupový token, který identifikuje vydavatele vytvářející toto volání rozhraní API. Formát je v případě, že je `"Bearer <accessaccess_token>"` hodnota tokenu načtena vydavatelem, jak je vysvětleno v tématu [získání tokenu založeného na aplikaci Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |
|  `x-ms-marketplace-token`  | Parametr identifikačního *tokenu* nákupu, který se má vyřešit  Token se předává do volání adresy URL cílové stránky, když se zákazník přesměruje na web partnera SaaS (například: `https://contoso.com/signup?token=<token><authorization_token>` ). <br> <br>  Všimněte si, že hodnota *tokenu* , která je kódována, je součástí adresy URL cílové stránky, takže je nutné ji dekódovat předtím, než se použije jako parametr v tomto volání rozhraní API.  <br> <br> Tady je příklad kódovaného řetězce v adrese URL: `contoso.com/signup?token=ab%2Bcd%2Fef` , kde *token* je `ab%2Bcd%2Fef` .  Stejný dekódování tokenu bude: `Ab+cd/ef` |
| | |

*Kódy odpovědí:*

Kód: 200 vrátí jedinečné identifikátory předplatného SaaS na základě `x-ms-marketplace-token` poskytnutého rozhraní.

Příklad těla odpovědi:

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased offer's plan ID
  "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
  "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
      "startDate": "2019-05-31",
      "endDate": "2019-06-29"
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": ["Delete", "Update", "Read"],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

Kód: 400 Chybný požadavek. `x-ms-marketplace-token` chybí, je poškozený, neplatný nebo vypršela jeho platnost.

Kód: 403 zakázaný. Autorizační token je neplatný, vypršela jeho platnost nebo nebyl poskytnut.  Požadavek se pokouší získat přístup k předplatnému SaaS pro nabídku, která byla publikována s jiným ID aplikace služby Azure AD, než je ta, která se používá k vytvoření autorizačního tokenu.

Tato chyba je často příznakem, že se [registrace SaaS](pc-saas-registration.md) neprovádí správně.

Kód: 500 interní chyba serveru.  Opakujte volání rozhraní API.  Pokud chyba přetrvává, obraťte se na [podporu Microsoftu](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="activate-a-subscription"></a>Aktivace předplatného

Po nakonfigurování účtu SaaS pro koncového uživatele musí vydavatel volat rozhraní API Aktivace předplatného na straně Microsoftu.  Zákazník se nebude účtovat, dokud toto volání rozhraní API neproběhne úspěšně.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Spuštění `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Parametry dotazu:*

|  Parametr         | Hodnota             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Použijte 2018-08-31.   |
| `subscriptionId` | Jedinečný identifikátor zakoupeného předplatného SaaS.  Toto ID se získá po vyřešení autorizačního tokenu komerčního tržiště pomocí [rozhraní Resolve API](#resolve-a-purchased-subscription).
 |

*Hlavičky žádosti:*

|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID.  Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
| `x-ms-correlationid` |  Jedinečná řetězcová hodnota pro operaci na klientovi.  Tento řetězec koreluje všechny události z klientské operace s událostmi na straně serveru.  Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
| `authorization`      |  Jedinečný přístupový token, který identifikuje vydavatele vytvářející toto volání rozhraní API. Formát je v případě, že je `"Bearer <access_token>"` hodnota tokenu načtena vydavatelem, jak je vysvětleno v tématu [získání tokenu založeného na aplikaci Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Příklad datové části požadavku:*

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*Kódy odpovědí:*

Kód: 200 předplatné bylo označeno jako odebírané na straně Microsoftu.

Pro toto volání není k dispozici text odpovědi.

Kód: 400 Špatný požadavek: ověření se nezdařilo.

* `planId` neexistuje v datové části požadavku.
* `planId` v datové části požadavku se neshoduje s tím, který byl zakoupen.
* `quantity` v datové části požadavku se neshoduje s tím, který byl koupen.
* Předplatné SaaS je v *odebíraném* nebo *pozastaveném* stavu.

Kód: 403 zakázaný. Autorizační token je neplatný, vypršela jeho platnost nebo nebyl poskytnut. Požadavek se pokouší získat přístup k předplatnému SaaS pro nabídku, která byla publikována s jiným ID aplikace služby Azure AD, než je ta, která se používá k vytvoření autorizačního tokenu.

Tato chyba je často příznakem, že se [registrace SaaS](pc-saas-registration.md) neprovádí správně.

Kód: 404 nebyl nalezen. Předplatné SaaS je ve stavu bez *odběru* .

Kód: 500 interní chyba serveru.  Opakujte volání rozhraní API.  Pokud chyba přetrvává, obraťte se na [podporu Microsoftu](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-list-of-all-subscriptions"></a>Získá seznam všech předplatných.

Toto rozhraní API načte seznam všech zakoupených předplatných SaaS pro všechny nabídky, které Vydavatel zveřejňuje na komerčním webu Marketplace.  Budou se vracet SaaS předplatná ve všech možných stavech. Vrátí se i nevyplacené odběry SaaS, protože tyto informace se na straně Microsoftu neodstraňují.

Rozhraní API vrací stránkované výsledky 100 na stránku.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Čtěte `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Parametry dotazu:*

|  Parametr         | Hodnota             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Použijte 2018-08-31.  |
| `continuationToken`  | Volitelný parametr. Pokud chcete načíst první stránku výsledků, ponechte prázdné.  K načtení další stránky použijte hodnotu vrácenou v `@nextLink` parametru. |

*Hlavičky žádosti:*

|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
| `x-ms-correlationid` |  Jedinečná řetězcová hodnota pro operaci na klientovi.  Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru.  Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
| `authorization`      |  Jedinečný přístupový token, který identifikuje vydavatele, který provádí toto volání rozhraní API.  Formát je v případě, že je `"Bearer <access_token>"` hodnota tokenu načtena vydavatelem, jak je vysvětleno v tématu [získání tokenu založeného na aplikaci Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Kódy odpovědí:*

Kód: 200 vrátí seznam všech stávajících předplatných pro všechny nabídky provedené tímto vydavatelem na základě autorizačního tokenu vydavatele.

*Příklad těla odpovědi:*

```json
{
  "subscriptions": [
    {
      "id": "<guid>", // purchased SaaS subscription ID
      "name": "Contoso Cloud Solution", // SaaS subscription name
      "publisherId": "contoso", // publisher ID
      "offerId": "offer1", // purchased offer ID
      "planId": "silver", // purchased plan ID
      "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
      "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": { // The period for which the subscription was purchased.
        "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
        "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
        "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
      },
      "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
      "sessionMode": "None", // not relevant
      "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
      "isTest": false, // not relevant
      "sandboxType": "None", // not relevant
      "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
    },
    // next SaaS subscription details, might be a different offer
    {
      "id": "<guid1>",
      "name": "Contoso Cloud Solution1",
      "publisherId": "contoso",
      "offerId": "offer2",
      "planId": "gold",
      "quantity": "",
      "beneficiary": {
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": {
        "emailId": "purchase@csp.com ",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": {
        "startDate": "2019-05-31",
        "endDate": "2020-04-30",
        "termUnit": "P1Y"
      },
      "allowedCustomerOperations": ["Read"],
      "sessionMode": "None",
      "isFreeTrial": false,
      "isTest": false,
      "sandboxType": "None",
      "saasSubscriptionStatus": "Suspended"
    }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

Pokud se pro tohoto vydavatele nenašly žádné koupené předplatné SaaS, vrátí se prázdný text odpovědi.

Kód: 403 zakázaný. Autorizační token je nedostupný, neplatný nebo vypršel.

Tato chyba je často příznakem, že se [registrace SaaS](pc-saas-registration.md) neprovádí správně. 

Kód: 500 interní chyba serveru. Opakujte volání rozhraní API.  Pokud chyba přetrvává, obraťte se na [podporu Microsoftu](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-subscription"></a>Získat předplatné

Toto rozhraní API načte zadané zakoupené předplatné SaaS pro nabídku SaaS, kterou Vydavatel zveřejňuje na komerčním webu Marketplace. Pomocí tohoto volání získáte všechny dostupné informace pro konkrétní předplatné SaaS podle jeho ID, nikoli voláním rozhraní API, které se používá pro získání seznamu všech předplatných.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Čtěte `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   Použijte 2018-08-31. |
| `subscriptionId`     |  Jedinečný identifikátor zakoupeného předplatného SaaS.  Toto ID se získá po vyřešení autorizačního tokenu komerčního tržiště pomocí rozhraní Resolve API. |

*Hlavičky žádosti:*

|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
|  `x-ms-correlationid` |  Jedinečná řetězcová hodnota pro operaci na klientovi.  Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru.  Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
|  `authorization`     | Jedinečný přístupový token, který identifikuje vydavatele, který provádí toto volání rozhraní API. Formát je v případě, že je `"Bearer <access_token>"` hodnota tokenu načtena vydavatelem, jak je vysvětleno v tématu [získání tokenu založeného na aplikaci Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Kódy odpovědí:*

Kód: 200 vrátí podrobnosti o předplatném SaaS na základě `subscriptionId` poskytnutého.

*Příklad těla odpovědi:*

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "name": "Contoso Cloud Solution", // SaaS subscription name
  "publisherId": "contoso", // publisher ID
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased plan ID
  "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
  "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
    "emailId": "test@contoso.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
    "emailId": "test@test.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
  "sessionMode": "None", // not relevant
  "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
  "isTest": false, // not relevant
  "sandboxType": "None", // not relevant
  "saasSubscriptionStatus": " Subscribed ", // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
  "term": { // the period for which the subscription was purchased
    "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
    "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
    "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
  }
}
```

Kód: 403 zakázaný. Autorizační token je neplatný, vypršela jeho platnost nebo nebyl poskytnut. Požadavek se pokouší získat přístup k předplatnému SaaS pro nabídku, která je publikovaná s jiným ID aplikace Azure AD, než je ta, která se používá k vytvoření autorizačního tokenu.

Tato chyba je často příznakem, že se [registrace SaaS](pc-saas-registration.md) neprovádí správně. 

Kód: 404 nebyl nalezen.  Nepovedlo se najít předplatné SaaS se zadaným parametrem `subscriptionId` .

Kód: 500 interní chyba serveru.  Opakujte volání rozhraní API.  Pokud chyba přetrvává, obraťte se na [podporu Microsoftu](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="list-available-plans"></a>Výpis dostupných plánů

Toto rozhraní API načte všechny plány pro nabídku SaaS, která je určená pro `subscriptionId` konkrétní nákup této nabídky.  Pomocí tohoto volání získáte seznam všech privátních a veřejných plánů, které může příjemce předplatného SaaS aktualizovat.  Vrácené plány budou k dispozici ve stejné zeměpisné oblasti jako již zakoupený plán.

Toto volání vrátí seznam plánů dostupných pro daného zákazníka kromě již zakoupeného.  Seznam může být prezentován koncovému uživateli na webu vydavatele.  Koncový uživatel může změnit plán předplatného na některý z plánů v vráceném seznamu.  Změna plánu na jeden, který není v seznamu, se nezdaří.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Čtěte `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Parametry dotazu:*

|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Použijte 2018-08-31.  |
|  `subscriptionId`    |  Jedinečný identifikátor zakoupeného předplatného SaaS.  Toto ID se získá po vyřešení autorizačního tokenu komerčního tržiště pomocí rozhraní Resolve API. |

*Hlavičky žádosti:*

|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID.  Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
|  `x-ms-correlationid`  |  Jedinečná řetězcová hodnota pro operaci na klientovi.  Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru.  Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
|  `authorization`     |  Jedinečný přístupový token, který identifikuje vydavatele, který provádí toto volání rozhraní API.  Formát je v případě, že je `"Bearer <access_token>"` hodnota tokenu načtena vydavatelem, jak je vysvětleno v tématu [získání tokenu založeného na aplikaci Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Kódy odpovědí:*

Kód: 200 vrátí seznam všech dostupných plánů pro stávající předplatné SaaS, včetně již zakoupeného.

Příklad těla odpovědi:

```json
{
  "plans": [
    {
      "planId": "Platinum001",
      "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
      "isPrivate": true //true or false
    },
    {
      "planId": "gold",
      "displayName": "Gold plan for Contoso",
      "isPrivate": false //true or false
    }
  ]
}
```

Pokud `subscriptionId` se nenajde, vrátí se prázdný text odpovědi.

Kód: 403 zakázaný. Autorizační token je neplatný, vypršela jeho platnost nebo nebyl poskytnut.  Požadavek se může pokusit o přístup k předplatnému SaaS pro nabídku, která je publikovaná s jiným ID aplikace Azure AD, než je ta, která se používá k vytvoření autorizačního tokenu.

Tato chyba je často příznakem, že se [registrace SaaS](pc-saas-registration.md) neprovádí správně. 

Kód: 500 interní chyba serveru.  Opakujte volání rozhraní API.  Pokud chyba přetrvává, obraťte se na [podporu Microsoftu](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="change-the-plan-on-the-subscription"></a>Změna plánu předplatného

Pomocí tohoto rozhraní API aktualizujete stávající plán zakoupený pro předplatné SaaS na nový plán (veřejný nebo soukromý).  Vydavatel musí toto rozhraní API zavolat při změně plánu na straně vydavatele pro předplatné SaaS, zakoupené na komerčním tržišti.

Toto rozhraní API se dá volat jenom pro *aktivní* odběry.  Libovolný plán se dá změnit na jakýkoliv jiný existující plán (veřejný nebo soukromý), ale ne na sám sebe.  U privátních plánů musí být tenant zákazníka definovaný jako součást cílové skupiny plánu v partnerském centru.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Použita `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Použijte 2018-08-31.  |
| `subscriptionId`     | Jedinečný identifikátor zakoupeného předplatného SaaS.  Toto ID se získá po vyřešení autorizačního tokenu komerčního tržiště pomocí rozhraní Resolve API. |

*Hlavičky žádosti:*
 
|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  `x-ms-correlationid`  | Jedinečná řetězcová hodnota pro operaci na klientovi.  Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru.  Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  `authorization`     |  Jedinečný přístupový token, který identifikuje vydavatele, který provádí toto volání rozhraní API.  Formát je v případě, že je `"Bearer <access_token>"` hodnota tokenu načtena vydavatelem, jak je vysvětleno v tématu [získání tokenu založeného na aplikaci Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Příklad datové části požadavku:*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*Kódy odpovědí:*

Kód: 202 požadavek na změnu plánu byl přijat a zpracován asynchronně.  U partnera se očekává dotaz na **adresu URL umístění operace** , aby bylo možné zjistit úspěch nebo neúspěch žádosti o změnu plánu.  Cyklické dotazování by se mělo provádět každých několik sekund, dokud se pro operaci nevrátí konečný stav *selhání*, *úspěšného* nebo *konfliktu* .  Konečný stav operace by měl být vrácen rychle, ale v některých případech může trvat několik minut.

Pokud je akce připravena k úspěšnému dokončení na straně komerčního tržiště, partner také obdrží oznámení Webhooku.  Pouze potom by měl Vydavatel provést změnu plánu na straně vydavatele.

*Hlavičky odpovědi:*

|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Adresa URL pro získání stavu operace.  Například, `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Kód: 400 Chybný požadavek: selhání ověřování.

* Nový plán neexistuje nebo není pro toto konkrétní předplatné SaaS k dispozici.
* Nový plán je stejný jako aktuální plán.
* Stav předplatného SaaS není *přihlášený k odběru*.
* Operace aktualizace pro předplatné SaaS není součástí systému `allowedCustomerOperations` .

Kód: 403 zakázaný. Autorizační token je neplatný, vypršela jeho platnost nebo nebyl poskytnut.  Požadavek se pokouší získat přístup k předplatnému SaaS pro nabídku, která je publikovaná s jiným ID aplikace Azure AD, než je ta, která se používá k vytvoření autorizačního tokenu.

Tato chyba je často příznakem, že se [registrace SaaS](pc-saas-registration.md) neprovádí správně.

Kód: 404 nebyl nalezen.  Předplatné SaaS with se `subscriptionId` nenašlo.

Kód: 500 interní chyba serveru.  Opakujte volání rozhraní API.  Pokud chyba přetrvává, obraťte se na [podporu Microsoftu](https://partner.microsoft.com/support/v2/?stage=1).

>[!NOTE]
>Plán nebo množství sedadel lze kdykoli změnit, nikoli obojí.

>[!Note]
>Toto rozhraní API se dá volat až po získání explicitního schválení změny od koncového uživatele.

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>Změna počtu míst v předplatném SaaS

Pomocí tohoto rozhraní API můžete aktualizovat (zvyšovat nebo snižovat) množství míst zakoupených pro SaaS předplatné.  Vydavatel musí toto rozhraní API zavolat, když se změní počet sedadel na straně vydavatele pro předplatné SaaS vytvořené na komerčním webu Marketplace.

Množství míst na sedadle nemůže být větší než množství povolené v aktuálním plánu.  V takovém případě by měl Vydavatel změnit plán před změnou množství míst.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Použita `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Použijte 2018-08-31.  |
|  `subscriptionId`     | Jedinečný identifikátor zakoupeného předplatného SaaS.  Toto ID se získá po vyřešení autorizačního tokenu komerčního tržiště pomocí rozhraní Resolve API.  |

*Hlavičky žádosti:*
 
|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID.  Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  `x-ms-correlationid`  | Jedinečná řetězcová hodnota pro operaci na klientovi.  Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru.  Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  `authorization`     | Jedinečný přístupový token, který identifikuje vydavatele, který provádí toto volání rozhraní API.  Formát je v případě, že je `"Bearer <access_token>"` hodnota tokenu načtena vydavatelem, jak je vysvětleno v tématu [získání tokenu založeného na aplikaci Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Příklad datové části požadavku:*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*Kódy odpovědí:*

Kód: 202 požadavek na změnu množství byl přijat a zpracován asynchronně. U partnera se očekává dotaz na **adresu URL umístění operace** , aby bylo možné zjistit úspěch nebo neúspěch žádosti o změnu množství.  Cyklické dotazování by se mělo provádět každých několik sekund, dokud se pro operaci nevrátí konečný stav *selhání*, *úspěšného* nebo *konfliktu* .  Konečný stav operace by měl být vrácen rychle, ale v některých případech může trvat několik minut.

Pokud je akce připravena k úspěšnému dokončení na straně komerčního tržiště, partner také obdrží oznámení Webhooku.  Pouze potom by měl Vydavatel provést změnu množství na straně vydavatele.

*Hlavičky odpovědi:*

|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Připojte se k prostředku, abyste získali stav operace.  Například, `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`.  |

Kód: 400 Chybný požadavek: selhání ověřování.

* Nové množství je větší nebo nižší než aktuální limit plánu.
* Nové množství chybí.
* Nové množství je stejné jako aktuální množství.
* Stav předplatného SaaS není přihlášený k odběru.
* Operace aktualizace pro předplatné SaaS není součástí `allowedCustomerOperations` .

Kód: 403 zakázaný.  Autorizační token je neplatný, vypršela jeho platnost nebo nebyl poskytnut.  Požadavek se pokouší o přístup k předplatnému, které nepatří k aktuálnímu vydavateli.

Tato chyba je často příznakem, že se [registrace SaaS](pc-saas-registration.md) neprovádí správně. 

Kód: 404 nebyl nalezen.  Předplatné SaaS with se `subscriptionId` nenašlo.

Kód: 500 interní chyba serveru.  Opakujte volání rozhraní API.  Pokud chyba přetrvává, obraťte se na [podporu Microsoftu](https://partner.microsoft.com/support/v2/?stage=1).

>[!Note]
>V jednom okamžiku se dá změnit jenom plán nebo množství, ne obojí.

>[!Note]
>Toto rozhraní API se dá volat až po získání explicitního schválení od koncového uživatele ke změně.

#### <a name="cancel-a-subscription"></a>Zrušení předplatného

Pomocí tohoto rozhraní API můžete zrušit odběr zadaného předplatného SaaS.  Vydavatel nemusí používat toto rozhraní API a doporučujeme, aby zákazníci, kteří budou přesměrováni na komerční tržiště, zrušili SaaS předplatných.

Pokud se Vydavatel rozhodne implementovat zrušení předplatného SaaS zakoupeného na komerčním webu Marketplace na straně vydavatele, musí volat toto rozhraní API.  Po dokončení tohoto volání se stav předplatného stane na straně Microsoftu *odhlášený* .

Zákazníkovi se nebude účtovat, pokud se předplatné zruší v rámci těchto odkladních období:

* Dvacet čtyři hodiny pro měsíční předplatné po aktivaci.
* Čtrnáct dní ročního předplatného po aktivaci.

Zákazníkům se bude fakturovat, pokud se předplatné zruší po uplynutí předchozích odkladních období.  Zákazník ztratí přístup k předplatnému SaaS na straně Microsoftu hned po zrušení. 

##### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Dstranit `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Použijte 2018-08-31.  |
|  `subscriptionId`     | Jedinečný identifikátor zakoupeného předplatného SaaS.  Toto ID se získá po vyřešení autorizačního tokenu komerčního tržiště pomocí rozhraní Resolve API.  |

*Hlavičky žádosti:*
 
|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID.  Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  `x-ms-correlationid`  | Jedinečná řetězcová hodnota pro operaci na klientovi.  Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru.  Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  `authorization`     |  Jedinečný přístupový token, který identifikuje vydavatele vytvářející toto volání rozhraní API.  Formát je v případě, že je `"Bearer <access_token>"` hodnota tokenu načtena vydavatelem, jak je vysvětleno v tématu [získání tokenu založeného na aplikaci Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Kódy odpovědí:*

Kód: 202 žádost o zrušení odběru byla přijata a zpracována asynchronně.  U partnera se očekává dotaz na **adresu URL umístění operace** , aby bylo možné určit úspěch nebo neúspěch této žádosti.  Cyklické dotazování by se mělo provádět každých několik sekund, dokud se pro operaci nevrátí konečný stav *selhání*, *úspěšného* nebo *konfliktu* .  Konečný stav operace by měl být vrácen rychle, ale v některých případech může trvat několik minut.

Partner taky obdrží oznámení Webhooku, když se akce úspěšně dokončí na komerční straně Marketplace.  Pouze potom by měl Vydavatel zrušit předplatné na straně vydavatele.

*Hlavičky odpovědi:*

|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Připojte se k prostředku, abyste získali stav operace.  Například, `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Kód: 400 Chybný požadavek.  Odstranění není v `allowedCustomerOperations` seznamu pro toto předplatné SaaS.

Kód: 403 zakázaný.  Autorizační token je neplatný, vypršela jeho platnost nebo není k dispozici. Požadavek se pokouší získat přístup k předplatnému SaaS pro nabídku, která je publikovaná s jiným ID aplikace Azure AD, než je ta, která se používá k vytvoření autorizačního tokenu.

Tato chyba je často příznakem, že se [registrace SaaS](pc-saas-registration.md) neprovádí správně.

Kód: 404 nebyl nalezen.  Předplatné SaaS with se `subscriptionId` nenašlo.

Kód: 500 interní chyba serveru. Opakujte volání rozhraní API.  Pokud chyba přetrvává, obraťte se na [podporu Microsoftu](https://partner.microsoft.com/support/v2/?stage=1).

### <a name="operations-apis"></a>Rozhraní API operací

#### <a name="list-outstanding-operations"></a>Výpis nezpracovaných operací 

Získá seznam nevyřízených operací pro zadané předplatné SaaS.  Vydavatel by měl potvrdit vrácené operace voláním [rozhraní API opravy operace](#update-the-status-of-an-operation).

V současné době se jako odpověď pro toto volání rozhraní API vrátí jenom **operace obnovit** .

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Čtěte `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Parametry dotazu:*

|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  Použijte 2018-08-31.         |
|    `subscriptionId` | Jedinečný identifikátor zakoupeného předplatného SaaS.  Toto ID se získá po vyřešení autorizačního tokenu komerčního tržiště pomocí rozhraní Resolve API.  |

*Hlavičky žádosti:*
 
|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID.  Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  `x-ms-correlationid` |  Jedinečná řetězcová hodnota pro operaci na klientovi.  Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru.  Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  `authorization`     |  Formát je v případě, že je `"Bearer <access_token>"` hodnota tokenu načtena vydavatelem, jak je vysvětleno v tématu [získání tokenu založeného na aplikaci Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Kódy odpovědí:*

Kód: 200 vrátí nedokončenou operaci obnovení u zadaného předplatného SaaS.

*Příklad datové části odpovědi:*

```json
{
  "operations": [
    {
      "id": "<guid>", //Operation ID, should be provided in the operations patch API call
      "activityId": "<guid>", //not relevant
      "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
      "offerId": "offer1", // purchased offer ID
      "publisherId": "contoso",
      "planId": "silver", // purchased plan ID
      "quantity": "20", // purchased amount of seats, will be empty is not relevant
      "action": "Reinstate",
      "timeStamp": "2018-12-01T00:00:00", // UTC
      "status": "InProgress" // the only status that can be returned in this case
    }
  ]
}
```

Vrátí prázdný kód JSON, pokud nečekají žádné operace obnovení.

Kód: 400 Chybný požadavek: selhání ověřování.

Kód: 403 zakázaný. Autorizační token je neplatný, vypršela jeho platnost nebo nebyl poskytnut.  Požadavek se pokouší získat přístup k předplatnému SaaS pro nabídku, která je publikovaná s jiným ID aplikace Azure AD, než je ta, která se používá k vytvoření autorizačního tokenu.

Tato chyba je často příznakem, že se [registrace SaaS](pc-saas-registration.md) neprovádí správně. 

Kód: 404 nebyl nalezen.  Předplatné SaaS with se `subscriptionId` nenašlo.

Kód: 500 interní chyba serveru. Opakujte volání rozhraní API.  Pokud chyba přetrvává, obraťte se na [podporu Microsoftu](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-operation-status"></a>Získat stav operace

Toto rozhraní API umožňuje vydavateli sledovat stav zadané asynchronní operace:  **zrušit odběr**, **ChangePlan** nebo **ChangeQuantity**.

`operationId`Pro toto volání rozhraní API se dá načíst z hodnoty vrácené **umístěním Operations-Location**, volání rozhraní API pro získání nevyřízených operací nebo `<id>` hodnoty parametru přijatého ve volání Webhooku.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Čtěte `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Použijte 2018-08-31.  |
|  `subscriptionId`    |  Jedinečný identifikátor zakoupeného předplatného SaaS.  Toto ID se získá po vyřešení autorizačního tokenu komerčního tržiště pomocí rozhraní Resolve API. |
|  `operationId`       |  Jedinečný identifikátor operace, která se načítá. |

*Hlavičky žádosti:*

|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID.  Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
|  `x-ms-correlationid` |  Jedinečná řetězcová hodnota pro operaci na klientovi.  Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru.  Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  `authorization`     |  Jedinečný přístupový token, který identifikuje vydavatele vytvářející toto volání rozhraní API.  Formát je v případě, že je `"Bearer <access_token>"` hodnota tokenu načtena vydavatelem, jak je vysvětleno v tématu [získání tokenu založeného na aplikaci Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Kódy odpovědí:*

Kód: 200 Získá podrobnosti pro zadanou operaci SaaS. 

*Příklad datové části odpovědi:*

```json
Response body:
{
  "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
  "activityId": "<guid>", //not relevant
  "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
  "offerId": "offer1", // purchased offer ID
  "publisherId": "contoso",
  "planId": "silver", // purchased plan ID
  "quantity": "20", // purchased amount of seats
  "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
  "timeStamp": "2018-12-01T00:00:00", // UTC
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeeded, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

Kód: 403 zakázaný. Autorizační token je neplatný, vypršela jeho platnost nebo nebyl poskytnut.  Požadavek se pokouší získat přístup k předplatnému SaaS pro nabídku, která je publikovaná s jiným ID aplikace Azure AD, než je ta, která se používá k vytvoření autorizačního tokenu.

Tato chyba je často příznakem, že se [registrace SaaS](pc-saas-registration.md) neprovádí správně. 

Kód: 404 nebyl nalezen.  

* Předplatné se `subscriptionId` nenašlo.
* Operace se `operationId` nenašla.

Kód: 500 interní chyba serveru.  Opakujte volání rozhraní API.  Pokud chyba přetrvává, obraťte se na [podporu Microsoftu](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="update-the-status-of-an-operation"></a>Aktualizace stavu operace

Pomocí tohoto rozhraní API můžete aktualizovat stav operace, která čeká na dokončení operace, a určit tak úspěch nebo neúspěch operace na straně vydavatele.

`operationId`Pro toto volání rozhraní API se dá načíst z hodnoty vrácené **umístěním Operations-Location**, volání rozhraní API pro získání nevyřízených operací nebo `<id>` hodnoty parametru přijatého ve volání Webhooku.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Použita `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  Použijte 2018-08-31.  |
|   `subscriptionId`   |  Jedinečný identifikátor zakoupeného předplatného SaaS.  Toto ID se získá po vyřešení autorizačního tokenu komerčního tržiště pomocí rozhraní Resolve API.  |
|   `operationId`      |  Jedinečný identifikátor operace, která je dokončena. |

*Hlavičky žádosti:*

|  Parametr         | Hodnota             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID.  Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
|   `x-ms-correlationid` |  Jedinečná řetězcová hodnota pro operaci na klientovi.  Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru.  Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
|  `authorization`     |  Jedinečný přístupový token, který identifikuje vydavatele, který provádí toto volání rozhraní API.  Formát je v případě, že je `"Bearer <access_token>"` hodnota tokenu načtena vydavatelem, jak je vysvětleno v tématu [získání tokenu založeného na aplikaci Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Příklad datové části požadavku:*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*Kódy odpovědí:*

Kód: 200 volání k informování o dokončení operace na straně partnera.  Tato odpověď by například mohla signalizovat dokončení změny sedadel nebo plánů na straně vydavatele.

Kód: 403
- Zakázán.  Autorizační token není k dispozici, je neplatný nebo vypršela jeho platnost. Požadavek se může pokusit o přístup k předplatnému, které nepatří k aktuálnímu vydavateli.
- Zakázán.  Autorizační token je neplatný, vypršela jeho platnost nebo nebyl poskytnut.  Požadavek se pokouší získat přístup k předplatnému SaaS pro nabídku, která je publikovaná s jiným ID aplikace Azure AD, než je ta, která se používá k vytvoření autorizačního tokenu.

Tato chyba je často příznakem, že se [registrace SaaS](pc-saas-registration.md) neprovádí správně.

Kód: 404 nebyl nalezen.

* Předplatné se `subscriptionId` nenašlo.
* Operace se `operationId` nenašla.

Kód: 409 konflikt.  Například novější aktualizace je již splněna.

Kód: 500 interní chyba serveru.  Opakujte volání rozhraní API.  Pokud chyba přetrvává, obraťte se na [podporu Microsoftu](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementace Webhooku ve službě SaaS

Při vytváření nabídky s podporou SaaS v partnerském centru poskytuje partner adresu URL **Webhooku připojení** , která se má použít jako koncový bod HTTP.  Tento Webhook vyvolala společnost Microsoft pomocí volání POST HTTP, které oznamuje straně vydavatele o následujících událostech, ke kterým dochází na straně Microsoftu:

* Když je předplatné SaaS ve stavu *odebíraného* :
    * ChangePlan 
    * ChangeQuantity
    * Suspend
    * Přestat odebírat
* Když je předplatné SaaS v *pozastaveném* stavu:
    * Obnovit vyřazenou aplikaci
    * Přestat odebírat

Vydavatel musí ve službě SaaS implementovat Webhook, aby se stav předplatného SaaS v souladu se stranou Microsoftu.  Služba SaaS se vyžaduje k volání rozhraní API operace get pro ověření a autorizaci volání Webhooku a dat datové části před provedením akce na základě oznámení Webhooku.  Vydavatel by měl vrátit HTTP 200 společnosti Microsoft, jakmile bude zpracováno volání Webhooku.  Tato hodnota potvrzuje, že volání Webhooku bylo úspěšně přijato vydavatelem.

>[!Note]
>Služba adresy URL Webhooku musí být v nepřetržitém provozu a je připravena přijímat nová volání od společnosti Microsoft po celou dobu.  Microsoft má zásady opakování pro volání Webhooku (500 opakování za 8 hodin), ale pokud Vydavatel hovor nepřijme a vrátí odpověď, operace Webhooku, o které dojde, nakonec selže na straně Microsoftu.

*Příklady datové části Webhooku:*

```json
// end user changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": "<guid>", // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": " 25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success
}
```

```json
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold",
  "quantity": " 20",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "In Progress"
}
```

## <a name="development-and-testing"></a>Vývoj a testování

Pro zahájení procesu vývoje doporučujeme na straně vydavatele vytvořit fiktivní odezvy rozhraní API.  Tyto odpovědi můžou být založené na ukázkových odpovědích uvedených v tomto článku.

Když je Vydavatel připravený na koncové testování:

* Publikujte nabídku SaaS k omezené cílové skupině Preview a nechte ji ve fázi Preview.
* Nastavte cenu plánu na 0, abyste se vyhnuli aktivaci skutečných fakturačních výdajů při testování.  Další možností je nastavit nenulovou cenu a zrušit všechny nákupy testů během 24 hodin.
* Zajistěte, aby všechny toky byly vyvolány na konci, aby se simuloval scénář reálného zákazníka.
* Pokud chce partner testovat plný nákup a fakturační tok, udělejte to s nabídkou, která je vyšší než $0.  Nákup se bude účtovat a bude vygenerována faktura.

Tok nákupu se dá aktivovat z Azure Portal nebo Microsoft AppSourcech lokalit v závislosti na tom, kde se nabídka publikuje.

Akce *změnit plán*, *změnit množství* a *zrušit odběr* jsou testovány na straně vydavatele.  Na straně Microsoftu se může *zrušit odběr* z Azure Portal i z centra pro správu (portál, ve kterém se spravují nákupy Microsoft AppSource).  *Změna množství a plánu* se dá aktivovat jenom z centra pro správu.

## <a name="get-support"></a>Získání podpory

Možnosti podpory pro vydavatele najdete v tématu [Podpora programu komerčního tržiště v partnerském centru](../support.md) .

## <a name="next-steps"></a>Další kroky

Další možnosti pro SaaS nabídky na komerčním webu Marketplace najdete v tématu [rozhraní API služby měření v komerčním tržišti](marketplace-metering-service-apis.md) .

Zkontrolujte a používejte [klienty pro různé programovací jazyky a ukázky](https://github.com/microsoft/commercial-marketplace-samples).
