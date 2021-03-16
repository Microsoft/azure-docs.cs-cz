---
title: Model identit
titleSuffix: An Azure Communication Services concept
description: Další informace o identitách a přístupových tokenech
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 5e53f9e89dd9718111da4f8016bc3e5a7f791627
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496571"
---
# <a name="identity-model"></a>Model identit

Komunikační služby Azure jsou služby identity – nezávislá. Tento návrh nabízí několik výhod:

- Znovu používat stávající identity ze systému správy identit
- Poskytuje flexibilitu pro scénáře integrace
- Vaše identity jsou v komunikačních službách Azure soukromé.

Místo duplikace informací v systému zachováte vztah mapování, který váš obchodní případ vyžaduje. Můžete například mapovat identity 1:1, 1: N, N:1, N:M. Externí identifikátory, jako jsou telefonní čísla, uživatelé, zařízení, aplikace a identifikátory GUID, se nedají použít k identifikaci v komunikačních službách Azure. Přístupové tokeny, které se generují pro identitu komunikačních služeb Azure, se používají pro přístup k primitivním objektům, jako je konverzace nebo volání.

## <a name="identity"></a>Identita

Identity můžete vytvořit pomocí knihovny identity Communications Services Azure. Identita slouží jako identifikátor v konverzacích. Používá se k vytváření přístupových tokenů. Stejná identita se může účastnit několika souběžných relací napříč různými zařízeními. Identita může mít několik aktivních přístupových tokenů současně.

Odstranění identity, prostředku nebo předplatného zruší platnost všech přístupových tokenů. Tato akce také odstraní všechna data uložená pro danou identitu. Odstraněná identita nemůže vytvářet nové přístupové tokeny nebo získávat přístup k dříve uloženým datům (například zprávy chatu).

Neúčtují se vám poplatky za počet identit. Místo toho se vám účtuje použití primitivních hodnot. Počet vašich identit nemusí omezovat, jak namapovat identity aplikace na identity komunikačních služeb Azure.

Díky svobodě mapování se dostane odpovědnost za ochranu osobních údajů. Pokud se chce uživatel z vašeho systému odstranit, musíte odstranit všechny identity přidružené k tomuto uživateli.

Komunikační služby Azure neposkytují speciální identity pro anonymní uživatele. Neudržuje mapování mezi uživateli a identitami a nemůže určit, jestli je identita anonymní. Koncept identity můžete navrhnout tak, aby vyhovoval vašim potřebám. Naším doporučením je vytvořit novou identitu pro každého anonymního uživatele v každé aplikaci.

Kdokoli, kdo má platný přístupový token, má přístup k aktuálnímu obsahu identity. Uživatelé můžou například získat přístup k odeslaným zprávám chatu. Přístup je omezený jenom na obory, které jsou součástí přístupového tokenu. Další informace najdete v části [přístupové tokeny](#access-tokens) v tomto článku.

### <a name="identity-mapping"></a>Mapování identit

Komunikační služby Azure nereplikují funkce systému Azure Identity Management. Neposkytuje zákazníkům způsob, jak používat identity specifické pro zákazníka. Zákazníci například nemohou použít telefonní číslo nebo e-mailovou adresu. Místo toho poskytují komunikační služby Azure jedinečné identifikátory. K identitám vaší aplikace můžete přiřadit tyto jedinečné identifikátory. Komunikační služby Azure neukládají žádné typy informací, které by mohly odhalit skutečnou identitu vašich uživatelů.

Abyste se vyhnuli duplicitě informací v systému, naplánujte, jak namapovat uživatele z vaší domény identity na identity komunikačních služeb Azure. Můžete postupovat podle jakéhokoli druhu vzoru. Můžete například použít 1:1, 1: N, N:1 nebo M:N. Rozhodněte, zda je jeden uživatel namapován na jedinou identitu nebo na více identit.

Při vytvoření nové identity uložte své mapování na uživatele nebo uživatele vaší aplikace. Vzhledem k tomu, že identity vyžadují přístupové tokeny, aby používaly primitivní prvky, musí být identita známa uživateli nebo uživatelům vaší aplikace.

Pokud k ukládání informací o uživatelích používáte relační databázi, můžete upravit návrh podle vašeho scénáře mapování. V případě scénářů, které mapují 1:1 nebo N:1, můžete chtít do `CommunicationServicesId` tabulky přidat sloupec pro uložení vaší identity služby Azure Communication Services. Ve scénářích používajících relaci 1: N nebo N:M můžete zvážit vytvoření samostatné tabulky v relační databázi.

## <a name="access-tokens"></a>Přístupové tokeny

Přístupový token je JSON Web Token (JWT), který se dá použít k získání přístupu k primitivám komunikační služby Azure. Přístupový token, který je vydaný, má ochranu integrity. To znamená, že jeho deklarace se po jeho vydání nedají změnit. V důsledku ruční změny vlastností, jako jsou identity, vypršení platnosti nebo obory, dojde k zrušení platnosti přístupového tokenu. Pokud se pro neověřené tokeny používají primitivní prvky, přístup k primitivním elementům se odmítne.

Vlastnosti přístupového tokenu jsou:
* Odcizen.
* Vypršení platnosti.
* Oboru.

Přístupový token je vždycky platný po dobu 24 hodin. Po vypršení platnosti je přístupový token neověřený a nedá se použít pro přístup k žádné primitivní.

Identita vyžaduje způsob, jak požádat o nový přístupový token ze služby na straně serveru. Parametr *Scope* definuje neprázdnou sadu primitivních elementů, které lze použít. Komunikační služby Azure podporují následující obory pro přístupové tokeny.

|Název|Popis|
|---|---|
|Chat|  Udělí možnost zúčastnit se chatu.|
|VoIP|  Udělí možnost volat identity a telefonní čísla.|


K odvolání přístupového tokenu před jeho časem vypršení platnosti použijte knihovnu identity Communications Services Azure. Odvolání tokenu není okamžité. Rozšíření bude trvat až 15 minut. Odebrání identity, prostředku nebo předplatného odvolá všechny přístupové tokeny.

Pokud chcete odebrat možnost uživatele pro přístup k určitým funkcím, Odvolejte všechny přístupové tokeny. Pak vydejte nový přístupový token, který má více omezené sady oborů.

V komunikačních službách Azure zavolá rotace přístupových klíčů všechny aktivní přístupové tokeny, které byly vytvořené pomocí předchozího přístupového klíče. Všechny identity ztratí přístup ke komunikačním službám Azure a musí vydávat nové přístupové tokeny.

V rámci serverové služby doporučujeme vystavovat přístupové tokeny, které se v aplikaci klienta nevyskytují. Důvodem je, že vydávání vyžaduje přístupový klíč nebo spravovanou identitu. Z bezpečnostních důvodů se nedoporučuje sdílení přístupových klíčů s aplikací klienta.

Klientská aplikace by měla používat koncový bod důvěryhodné služby, který může ověřit vaše klienty. Koncový bod by měl svým jménem vystavovat přístupové tokeny. Další informace najdete v tématu [Architektura klienta a serveru](./client-and-server-architecture.md).

Pokud ukládáte do mezipaměti přístupové tokeny do záložního úložiště, doporučujeme použít šifrování. Přístupový token je citlivý na data. Dá se použít pro škodlivou aktivitu, pokud není chráněná. Uživatel, který má přístupový token, může sadu SDK spustit a získat přístup k rozhraní API. Dostupné rozhraní API je omezené jenom na základě oborů, které má přístupový token. Doporučujeme vystavovat přístupové tokeny, které mají pouze požadované obory.

## <a name="next-steps"></a>Další kroky

* Úvod do správy přístupových tokenů najdete v tématu [vytváření a Správa přístupových tokenů](../quickstarts/access-tokens.md).
* Úvodní informace k ověřování najdete v tématu [ověřování ve službě Azure Communication Services](./authentication.md).
* Úvod k zaregistrování a ochraně osobních údajů najdete v tématu [dostupnost oblasti a](./privacy.md)zaregistrování dat.