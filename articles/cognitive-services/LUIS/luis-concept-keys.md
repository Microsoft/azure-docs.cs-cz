---
title: Použití klíčů pro vytváření a runtime s luisem
titleSuffix: Azure Cognitive Services
description: Služba LUIS používá dva klíče, klíč pro vytváření k vytvoření modelu a klíč runtime pro dotazování koncového bodu předpovědi s projevy uživatele.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9d213c8fa03ad2ca5e5fd7e620e52aa502749be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220960"
---
# <a name="authoring-and-runtime-keys"></a>Klíče pro vytváření obsahu a běhové klíče

Language Understanding (LUIS) má dvě služby a sady rozhraní API: 

* Vytváření (dříve známé jako _programové_)
* Doba běhu předpovědi

Existuje několik typů klíčů v závislosti na tom, s jakou službou chcete pracovat a jak s ní chcete pracovat.

## <a name="non-azure-resources-for-luis"></a>Prostředky mimo Azure pro službu LUIS

### <a name="starter-key"></a>Startovací klávesa

Při prvním spuštění pomocí LUIS, **startovací klíč** je vytvořen pro vás. Tento zdroj poskytuje:

* bezplatné požadavky na služby vytváření prostřednictvím portálu LUIS nebo nástrojů API (včetně sad SDK)
* zdarma 1 000 požadavků na koncový bod predikce za měsíc prostřednictvím prohlížeče, rozhraní API nebo sad SDK

## <a name="azure-resources-for-luis"></a>Prostředky Azure pro LUIS

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

Služba LUIS povoluje tři typy prostředků Azure: 
 
|Klíč|Účel|Kognitivní služby`kind`|Kognitivní služby`type`|
|--|--|--|--|
|[Klíč pro vytváření](#programmatic-key)|Přístup k datům aplikace a správa pomocí vytváření, školení, publikování a testování. Pokud máte v úmyslu programově vytvářet aplikace LUIS, vytvořte klíč pro vytváření luis.<br><br>Účelem `LUIS.Authoring` klíče je umožnit vám:<br>* programově spravovat jazyk porozumění aplikací a modelů, včetně školení, a publikování<br> * oprávnění k řízení zdrojového zdroje přiřazením osob k [roli přispěvatele](#contributions-from-other-authors).|`LUIS.Authoring`|`Cognitive Services`|
|[Predikční klíč](#prediction-endpoint-runtime-key)| Požadavky koncového bodu předpovědi dotazu. Vytvořte klíč předpověď LUIS před vaše klientská aplikace požaduje předpovědi nad rámec 1 000 požadavků poskytnutých počáteční prostředek. |`LUIS`|`Cognitive Services`|
|[Klíč prostředků služby Cognitive Service pro více služeb](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Požadavky koncového bodu předpovědi dotazu sdílené s LUIS a dalšími podporovanými službami Cognitive Services.|`CognitiveServices`|`Cognitive Services`|

Po dokončení procesu vytváření prostředků [přiřaďte klíč](luis-how-to-azure-subscription.md) k aplikaci na portálu LUIS.

Je důležité vytvářet aplikace LUIS v [oblastech,](luis-reference-regions.md#publishing-regions) kde chcete publikovat a dotazovat.

> [!CAUTION]
> Pro větší pohodlí mnoho ukázek používá [klíč Starter,](#starter-key) protože poskytuje několik volání koncového bodu free prediction ve své [kvótě](luis-boundaries.md#key-limits).  


### <a name="query-prediction-resources"></a>Prostředky předpovědi dotazu

* Klíč runtime lze použít pro všechny vaše aplikace LUIS nebo pro konkrétní aplikace LUIS. 
* Nepoužívejte klíč runtime pro vytváření aplikací LUIS. 

Koncový bod runtime LUIS přijímá dva styly dotazu, oba používají klíč běhu koncového bodu předpověď, ale na různých místech.

Koncový bod používaný pro přístup k běhu používá subdoménu, která je jedinečná pro oblast vašeho prostředku, označená `{region}` v následující tabulce. 

## <a name="assignment-of-the-key"></a>Přiřazení klíče

Klíč runtime můžete [přiřadit](luis-how-to-azure-subscription.md) na [portálu LUIS](https://www.luis.ai) nebo prostřednictvím odpovídajících api. 

## <a name="key-limits"></a>Klíčové limity

Můžete vytvořit až 10 klíčů pro vytváření na oblast na předplatné. 

Viz [Omezení klíčů](luis-boundaries.md#key-limits) a oblasti [Azure](luis-reference-regions.md). 

Oblasti publikování se liší od oblastí vytváření. Ujistěte se, že vytvoříte aplikaci v oblasti vytváření odpovídající oblasti publikování, kterou má být klientská aplikace umístěna.

## <a name="key-limit-errors"></a>Chyby limitu klíče
Pokud překročíte kvótu transakcí za sekundu (TPS), zobrazí se chyba HTTP 429. Pokud překročíte kvótu transakce za měsíc (TPS), zobrazí se chyba HTTP 403. 

## <a name="contributions-from-other-authors"></a>Příspěvky od jiných autorů

**Pro [vytváření prostředků migrovaných](luis-migration-authoring.md) aplikací**: _přispěvatelé_ se spravují na webu Azure Portal pro vytváření prostředků pomocí stránky **Řízení přístupu (IAM).** Přečtěte [si, jak přidat uživatele](luis-how-to-collaborate.md)pomocí e-mailové adresy spolupracovníka a role _přispěvatele._ 

**Pro aplikace, které ještě nebyly migrovány**: všichni _spolupracovníci_ jsou spravovány na portálu LUIS ze stránky **Spravovat -> spolupracovníky.**

## <a name="move-transfer-or-change-ownership"></a>Přesunutí, převod nebo změna vlastnictví

Aplikace je definovaná svými prostředky Azure, které jsou určeny podle předplatného vlastníka. 

Aplikaci LUIS můžete přesunout. Použijte následující prostředky dokumentace na webu Azure Portal nebo Azure CLI:

* [Přesunutí aplikace mezi autorizací prostředků LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Přesunutí prostředku do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Přesunutí prostředků v rámci stejného předplatného nebo mezi předplatnými](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

Převod [vlastnictví](../../cost-management-billing/manage/billing-subscription-transfer.md) předplatného: 

**Pro uživatele, kteří migrovali – [vytváření prostředků migrovaných](luis-migration-authoring.md) aplikací**: Jako vlastník prostředku můžete přidat . `contributor`

**Pro uživatele, kteří ještě nemigrovali**: Exportujte aplikaci jako soubor JSON. Jiný uživatel LUIS můžete importovat aplikaci, a tím se stát vlastníkem aplikace. Nová aplikace bude mít jiné ID aplikace.  

## <a name="access-for-private-and-public-apps"></a>Přístup k soukromým a veřejným aplikacím

Pro **soukromou** aplikaci je přístup za běhu k dispozici vlastníkům a přispěvatelům. Pro **veřejnou** aplikaci je přístup za běhu k dispozici všem, kdo mají vlastní prostředek Azure [Cognitive Service](../cognitive-services-apis-create-account.md) nebo [LUIS](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) runtime a mají ID veřejné aplikace. 

V současné době neexistuje katalog veřejných aplikací.

### <a name="authoring-access"></a>Vytváření přístupu
Přístup k aplikaci z portálu [LUIS](luis-reference-regions.md#luis-website) nebo [vytváření api](https://go.microsoft.com/fwlink/?linkid=2092087) je řízen a autorské prostředku Azure. 

Vlastník a všichni přispěvatelé mají přístup k vytváření aplikace. 

|Přístup k vytváření zahrnuje|Poznámky|
|--|--|
|Přidání nebo odebrání klíčů koncového bodu||
|Export verze||
|Export protokolů koncových bodů||
|Import verze||
|Zveřejněte aplikaci|Když je aplikace veřejná, může se na ni dotazovat každý, kdo má klíč pro vytváření nebo koncový bod.|
|Upravit model|
|Publikování|
|Kontrola projevy koncového bodu pro [aktivní učení](luis-how-to-review-endpoint-utterances.md)|
|Trénování|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Přístup ke spuštění koncového bodu předpověď

Přístup k dotazu koncový bod předpověď je řízen nastavení na stránce **Informace o aplikaci** v části **Spravovat.** 

|[Soukromý koncový bod](#runtime-security-for-private-apps)|[Veřejný koncový bod](#runtime-security-for-public-apps)|
|:--|:--|
|K dispozici pro vlastníky a přispěvatele|K dispozici pro vlastníka, přispěvatele a všechny ostatní, kdo zná ID aplikace|

Můžete určit, kdo vidí váš klíč runtime LUIS voláním v prostředí server-server. Pokud používáte LUIS z robota, připojení mezi robotem a LUIS je již zabezpečené. Pokud voláte koncový bod LUIS přímo, měli byste vytvořit rozhraní API na straně serveru (například [funkci](https://azure.microsoft.com/services/functions/)Azure) s řízeným přístupem (například [AAD).](https://azure.microsoft.com/services/active-directory/) Při volání a ověření rozhraní API na straně serveru a autorizace je ověřena, předejte volání luis. I když tato strategie nebrání útokům man-in-the-middle, zamlžuje váš klíč a adresu URL koncového bodu od uživatelů, umožňuje sledovat přístup a umožňuje přidat protokolování odpovědí koncového bodu (například [Application Insights](https://azure.microsoft.com/services/application-insights/)).

#### <a name="runtime-security-for-private-apps"></a>Zabezpečení runtime pro soukromé aplikace

Za běhu soukromé aplikace je k dispozici pouze následující:

|Klíč a uživatel|Vysvětlení|
|--|--|
|Klíč pro vytváření vlastníka| Až 1000 přístupů koncového bodu|
|Vývojové klíče spolupracovníka/přispěvatele| Až 1000 přístupů koncového bodu|
|Libovolný klíč přiřazený luis autornebo spolupracovník/přispěvatel|Na základě úrovně využití klíče|

#### <a name="runtime-security-for-public-apps"></a>Zabezpečení za běhu pro veřejné aplikace

Jakmile je aplikace nakonfigurována jako veřejná, _může se na vás dotazovat libovolný_ platný klíč pro vytváření LUIS nebo koncový bod LUIS, pokud klíč nepoužil celou kvótu koncového bodu.

Uživatel, který není vlastníkem nebo přispěvatelem, má přístup k běhu veřejné aplikace pouze v případě, že mu bude přiděleno ID aplikace. Služba LUIS nemá veřejný _trh_ ani jiný způsob vyhledávání veřejné aplikace.  

Veřejná aplikace se publikuje ve všech oblastech, takže uživatel s klíčem prostředku LUIS založené na oblasti má přístup k aplikaci v kterékoli oblasti, která je přidružena ke klíči prostředku.

## <a name="transfer-of-ownership"></a>Převod vlastnictví

Služba LUIS nemá koncept převodu vlastnictví prostředku. 

## <a name="securing-the-endpoint"></a>Zabezpečení koncového bodu 

Můžete určit, kdo může zobrazit váš klíč koncového bodu prognózy LUIS voláním v prostředí server-server. Pokud používáte LUIS z robota, připojení mezi robotem a LUIS je již zabezpečené. Pokud voláte koncový bod LUIS přímo, měli byste vytvořit rozhraní API na straně serveru (například [funkci](https://azure.microsoft.com/services/functions/)Azure) s řízeným přístupem (například [AAD).](https://azure.microsoft.com/services/active-directory/) Při volání rozhraní API na straně serveru a ověřování a autorizace jsou ověřeny, předejte volání luis. I když tato strategie nebrání útokům man-in-the-middle, zamlžuje koncový bod od uživatelů, umožňuje sledovat přístup a umožňuje přidat protokolování odezvy koncového bodu (například [Application Insights).](https://azure.microsoft.com/services/application-insights/)  

## <a name="next-steps"></a>Další kroky

* Principy konceptů [správy verzí.](luis-concept-version.md) 
* Přečtěte [si, jak vytvořit klíče](luis-how-to-azure-subscription.md).
