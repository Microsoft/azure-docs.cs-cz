---
title: Model identity
titleSuffix: An Azure Communication Services concept
description: Další informace o identitách a přístupových tokenech
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 336e708334778e107331f5f393476c4b3dbb98a3
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507452"
---
# <a name="identity-model"></a>Model identity

Komunikační služby Azure jsou služby identity nezávislá. Tento návrh má několik výhod:
- Opětovné použití existujících identit ze systému správy identit
- Flexibilita pro scénáře integrace
- Vaše identity se udržují soukromě ke komunikačním službám Azure.

Místo duplikace stávajících informací v systému zachováte vztah mapování, který je specifický pro váš obchodní případ. Například mapování identit 1:1, 1: N, N:1, N:M. Externí identifikátory (například telefonní čísla, uživatelé, zařízení, aplikace, GUID) nejde použít jako identitu komunikace Azure. Přístupové tokeny generované pro identitu služby Azure Communication Service se používají pro přístup k primitivním, jako je konverzace nebo volání. 

## <a name="identity"></a>Identita

Identity se vytvářejí pomocí knihovny pro správu služby Azure Communication Service. Identita slouží jako identifikátor v konverzacích a slouží k vytváření přístupových tokenů. Stejná identita se může účastnit několika souběžných relací napříč různými zařízeními. Identita může mít několik aktivních přístupových tokenů současně. Odstranění identity, prostředku nebo předplatného způsobí neplatnost všech přístupových tokenů a odstranění všech dat uložených pro tuto identitu. Odstraněná identita nemůže vydat nové přístupové tokeny, ani přístup k dříve uloženým datům (například zprávy chatu). 

Neúčtují se vám podle počtu identit, které máte, ale s využitím primitivních hodnot. Počet identit nemusíte omezovat, jak namapovat identity vaší aplikace na identity komunikačních služeb Azure. Pokud je volnost mapování v souladu s podmínkami ochrany osobních údajů, bude to mít za následek. Když chce uživatel vaší aplikace odstranit ze systému, je nutné odstranit všechny identity, které byly přidruženy k tomuto uživateli.

Komunikační služby Azure neposkytují speciální identity pro anonymní uživatele. Neudržuje mapování mezi uživateli a identitami, ale nerozumí, jestli je identita anonymní. Koncept můžete navrhnout tak, aby vyhovoval vašim požadavkům. Naším doporučením je vytvořit novou identitu pro anonymního uživatele aplikace. Při držení platného přístupového tokenu může kdokoli získat přístup k neodstraněnému obsahu identity. Například zprávy chatu odeslané uživatelem. Přístup je omezený jenom na obory, které jsou součástí přístupového tokenu. Další podrobnosti o oborech jsou v *přístupovém tokenu* oddílu.

### <a name="mapping-of-identities"></a>Mapování identit

Komunikační služby Azure nereplikují funkce IMS. Neposkytuje zákazníkům způsob, jak používat identity specifické pro zákazníka. Například telefonní číslo nebo e-mailová adresa. Místo toho poskytuje jedinečné identifikátory, které můžete přiřadit k identitám vaší aplikace. Komunikační služby Azure neukládají žádný druh informací, které by mohly odhalit skutečnou identitu vašich uživatelů.

Místo duplikace doporučujeme navrhnout, jak budou uživatelé z vaší domény identity namapováni na identity služby Azure Communication Service. Můžete postupovat podle jakéhokoli druhu vzoru 1:1, 1: N, N:1 nebo M:N.. Můžete rozhodnout, zda je jeden uživatel namapován na jedinou identitu nebo na více identit. Při vytvoření nové identity doporučujeme uložit mapování této identity uživateli nebo uživatelům vaší aplikace. Jelikož identity vyžadují přístupové tokeny pro použití primitivních rozhraní, musí být identita pro uživatele nebo uživatele vaší aplikace známa.

Pokud používáte relační databázi pro ukládání uživatelů, implementace se může lišit v závislosti na vašem scénáři mapování. U scénářů s mapováním 1:1 nebo N:1 můžete do tabulky přidat sloupec *CommunicationServicesId* , který bude ukládat vaši identitu služby Azure Communication Services. Ve scénářích s relací 1: N nebo N:M můžete zvážit vytvoření samostatné tabulky v relační databázi.

## <a name="access-token"></a>Přístupový token

Přístupový token je token JWT, který se dá použít k získání přístupu k primitivním adresářům služby Azure Communication. Vydaný přístupový token má ochranu integrity, jeho deklarace se po jeho vydání nedají změnit. To znamená, že ruční změna vlastností, jako jsou identity, vypršení platnosti nebo obory, nastaví přístupový token jako neplatný. Použití primitivních hodnot s neověřenými tokeny vede k odepření přístupu k primitivnímu. 

Vlastnosti přístupového tokenu jsou: *identita, vypršení platnosti* a *rozsahy*. Přístupový token je vždycky platný po dobu 24 hodin. Po skončení platnosti přístupového tokenu se nepůjde použít pro přístup k žádné primitivní. Identita musí mít způsob, jak požádat o nový přístupový token ze služby na straně serveru. *Rozsah* parametrů definuje neprázdnou sadu primitivních elementů, které lze použít. Komunikační služby Azure podporují následující obory pro přístupové tokeny:

|Název|Popis|
|---|---|
|Chat|  Udělí možnost zúčastnit se chatu.|
|VoIP|  Udělí možnost volat identity a telefonní čísla.|


Pokud chcete token přístupu odvolat před vypršením jeho platnosti, můžete k tomu použít knihovnu pro správu služby Azure Communication Service. Odvolání tokenu není okamžité a trvá až 15 minut, než se rozšíří. Odebrání identity, prostředku nebo předplatného způsobí odvolání všech přístupových tokenů. Pokud chcete odebrat možnost uživatele pro přístup k určitým funkcím, Odvolejte všechny přístupové tokeny. Pak vydejte nový přístupový token s více omezenými množinami oborů.
Rotace přístupových klíčů služby Azure Communication Service způsobí zrušení všech aktivních přístupových tokenů vytvořených pomocí předchozího přístupového klíče. Všechny identity ztratí přístup ke komunikační službě Azure a jsou potřeba k vydávání nových přístupových tokenů. 

V rámci serverové služby doporučujeme vystavovat přístupové tokeny, které se v aplikaci klienta nevyskytují. Důvodem je, že vydávání vyžaduje přístupový klíč nebo zda má být spravovaná identita. Nedoporučuje se z bezpečnostních důvodů sdílet přístupové klíče s aplikací klienta. Klientská aplikace by měla používat koncový bod důvěryhodné služby, který může ověřit vaše klienty a vystavit přístupový token jménem uživatele. Další podrobnosti o architektuře najdete [tady](./client-and-server-architecture.md).

Pokud ukládáte do mezipaměti přístupové tokeny do záložního úložiště, doporučujeme použít šifrování. Přístupový token je citlivý na data a dá se použít na škodlivou aktivitu, pokud není chráněná. Pomocí přístupového tokenu můžete inicializovat sadu SDK a získat přístup k rozhraní API. Dostupné rozhraní API je omezené jenom na základě oborů, které má přístupový token. Doporučujeme vystavovat přístupové tokeny jenom s obory, které jsou povinné.