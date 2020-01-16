---
title: Jak používat vytváření a klíče za běhu s LUIS
titleSuffix: Azure Cognitive Services
description: LUIS používá dva klíče, vytváření a klíč modulu runtime pro dotazování koncového bodu předpovědi s uživatelským projevy.
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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969343"
---
# <a name="authoring-and-runtime-keys"></a>Klíče pro vytváření obsahu a běhové klíče

Language Understanding (LUIS) má dvě služby a sady rozhraní API: 

* Vytváření obsahu (dříve nazývané _program_)
* Předpověď za běhu

Existuje několik typů klíčů v závislosti na službě, se kterou chcete pracovat, a na tom, jak s ní chcete pracovat.

## <a name="non-azure-resources-for-luis"></a>Prostředky mimo Azure pro LUIS

### <a name="starter-key"></a>Počáteční klíč

Když poprvé začnete používat LUIS, vytvoří se pro vás **počáteční klíč** . Tento prostředek poskytuje:

* bezplatné požadavky na služby vytváření obsahu prostřednictvím portálu LUIS nebo rozhraní API (včetně sad SDK)
* bezplatné požadavky na koncový bod 1 000 předpovědi za měsíc prostřednictvím prohlížeče, rozhraní API nebo sad SDK

## <a name="azure-resources-for-luis"></a>Prostředky Azure pro LUIS

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

LUIS umožňuje tři typy prostředků Azure: 
 
|Klíč|Účel|`kind` služby pro rozpoznávání|`type` služby pro rozpoznávání|
|--|--|--|--|
|[Klíč pro tvorbu](#programmatic-key)|Přístup k datům aplikací a jejich správa pomocí vytváření, školení, publikování a testování. Pokud máte v úmyslu programově vytvářet aplikace LUIS, vytvořte si klíč pro vytváření LUIS.<br><br>Účelem `LUIS.Authoring`ho klíče je, aby vám umožňoval:<br>* prostřednictvím kódu programu spravujte Language Understanding aplikace a modely, včetně školení a publikování<br> * řízení oprávnění k vytváření prostředků tím, že uživatelům přiřadíte [roli Přispěvatel](#contributions-from-other-authors).|`LUIS.Authoring`|`Cognitive Services`|
|[Klíč předpovědi](#prediction-endpoint-runtime-key)| Požadavky koncového bodu předpovědi dotazu. Vytvořte klíč předpovědi LUIS před tím, než klientská aplikace požaduje předpovědi nad požadavky 1 000, které poskytuje počáteční prostředek. |`LUIS`|`Cognitive Services`|
|[Klíč prostředku více služeb pro službu rozpoznávání](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Požadavky koncového bodu předpovědi dotazu sdílené s LUIS a dalšími podporovanými Cognitive Services.|`CognitiveServices`|`Cognitive Services`|

Až se proces vytváření prostředků dokončí, [přiřaďte klíč](luis-how-to-azure-subscription.md) k aplikaci na portálu Luis.

Je důležité vytvářet aplikace LUIS v [oblastech](luis-reference-regions.md#publishing-regions) , kde chcete publikovat a dotazovat.

> [!CAUTION]
> Pro usnadnění práce mnoho z ukázek používá [počáteční klíč](#starter-key) , protože poskytuje několik bezplatných volání koncového bodu předpovědi ve své [kvótě](luis-boundaries.md#key-limits).  


### <a name="query-prediction-resources"></a>Prostředky předpovědi dotazů

* Klíč za běhu se dá použít pro všechny aplikace LUIS nebo pro konkrétní aplikace LUIS. 
* Nepoužívejte klíč za běhu k vytváření aplikací LUIS. 

Koncový bod LUIS runtime akceptuje dva styly dotazů, obě používají klíč předplatného koncového bodu pro prostředí, ale na různých místech.

Koncový bod používaný pro přístup k modulu runtime používá subdoménu, která je jedinečná pro vaši oblast prostředku, označenou `{region}` v následující tabulce. 

## <a name="assignment-of-the-key"></a>Přiřazení klíče

Klíč za běhu můžete [přiřadit](luis-how-to-azure-subscription.md) na [portálu Luis](https://www.luis.ai) nebo prostřednictvím odpovídajících rozhraní API. 

## <a name="key-limits"></a>Omezení klíčů

Pro každou oblast a předplatné můžete vytvořit až 10 klíčů pro vytváření obsahu. 

Podívejte se na téma [omezení klíčů](luis-boundaries.md#key-limits) a [oblasti Azure](luis-reference-regions.md). 

Publikování oblastech se liší od vytváření oblastí. Ujistěte se, že jste vytvořili aplikaci v oblasti vytváření obsahu odpovídající oblasti publikování, kterou chcete najít v klientské aplikaci.

## <a name="key-limit-errors"></a>Omezení klíče chyby
Pokud překročíte kvótu transakcí za sekundu (TPS), zobrazí se chyba HTTP 429. Pokud překročíte kvótu transakcí za měsíc (TPS), zobrazí se chyba HTTP 403. 

## <a name="contributions-from-other-authors"></a>Příspěvky od jiných autorů

**Pro [vytváření prostředků migrovaných](luis-migration-authoring.md) aplikací**: _přispěvatelé_ se spravují v Azure Portal pro vytváření prostředků pomocí stránky **řízení přístupu (IAM)** . Naučte [se, jak přidat uživatele](luis-how-to-collaborate.md)pomocí e-mailové adresy spolupracovníka a role _přispěvatele_ . 

**Pro aplikace, které ještě nebyly migrovány**: všechny _spolupracovníci_ jsou spravováni na portálu Luis ze stránky **spolupracovníci pro správu >** .

## <a name="move-transfer-or-change-ownership"></a>Přesunutí, převedení nebo změna vlastnictví

Aplikace je definovaná pomocí prostředků Azure, které určuje předplatné vlastníka. 

Aplikaci LUIS můžete přesunout. V Azure Portal nebo Azure CLI použijte následující dokumentaci:

* [Přesun aplikace mezi LUIS vytváření prostředků](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Přesunout prostředek do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Přesunutí prostředku v rámci stejného předplatného nebo napříč předplatnými](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

Přenos [vlastnictví](../../cost-management-billing/manage/billing-subscription-transfer.md) předplatného: 

**Pro uživatele, kteří mají migrované [vytváření prostředků](luis-migration-authoring.md) migrované aplikace**: jako vlastník prostředku můžete přidat `contributor`.

**Pro uživatele, kteří se ještě nemigrovali**: Exportujte aplikaci jako soubor JSON. Jiný uživatel LUIS může aplikaci importovat, takže se stane vlastníkem aplikace. Nová aplikace bude mít jiné ID aplikace.  

## <a name="access-for-private-and-public-apps"></a>Přístup pro privátní a veřejné aplikace

Pro **soukromou** aplikaci je k dispozici přístup za běhu pro vlastníky a přispěvatele. V případě **veřejné** aplikace je přístup za běhu dostupný všem, kdo má vlastní prostředek [služby pro rozpoznávání](../cognitive-services-apis-create-account.md) Azure nebo [Luis](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) runtime a má ID veřejné aplikace. 

V současné době není k dispozici katalog veřejných aplikací.

### <a name="authoring-access"></a>Přístup pro vytváření
Přístup k aplikaci z portálu [Luis](luis-reference-regions.md#luis-website) nebo [rozhraní API pro vytváření obsahu](https://go.microsoft.com/fwlink/?linkid=2092087) řídí prostředek pro vytváření obsahu Azure. 

Vlastník a všichni přispěvatelé mají přístup k vytváření aplikací. 

|Vytváření přístup zahrnuje|Poznámky|
|--|--|
|Přidání nebo odebrání klíče koncového bodu||
|Export verze||
|Exportovat protokoly koncového bodu||
|Importuje se verze||
|Zveřejnit aplikaci|Pokud aplikace je veřejný, kdokoli s klíčem s vytvářením nebo koncový bod aplikace dotazu.|
|Upravit model|
|Publikování|
|Zkontrolujte projevy koncový bod pro [aktivní učení](luis-how-to-review-endpoint-utterances.md)|
|Trénování|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Prediktivní přístup k modulu runtime koncového bodu

Přístup k dotazu na koncový bod předpovědi je řízen nastavením na stránce **informace o aplikaci** v části **Spravovat** . 

|[Privátního koncového bodu](#runtime-security-for-private-apps)|[Veřejný koncový bod](#runtime-security-for-public-apps)|
|:--|:--|
|K dispozici pro vlastníka a přispěvatele|Dostupné pro vlastníka, přispěvatele a někoho jiného, kdo zná ID aplikace|

Můžete určit, kdo uvidí klíč LUIS runtime, voláním v prostředí serveru na serveru. Pokud používáte LUIS z robota, je již zabezpečené připojení mezi robotů a LUIS. Pokud koncový bod služby LUIS se volat přímo, měli byste vytvořit rozhraní API na straně serveru (jako je Azure [funkce](https://azure.microsoft.com/services/functions/)) s řízený přístup (například [AAD](https://azure.microsoft.com/services/active-directory/)). Když se zavolá rozhraní API na straně serveru a ověří se ověření a autorizaci, předejte volání do LUIS. I když tato strategie nebrání útokům prostředníkem, zařadí klíč a adresu URL koncového bodu vašim uživatelům, umožní vám sledovat přístup a umožňuje přidat protokolování odpovědí koncového bodu (například [Application Insights](https://azure.microsoft.com/services/application-insights/)).

#### <a name="runtime-security-for-private-apps"></a>Zabezpečení běhového prostředí pro privátní aplikace

Modul runtime privátní aplikace je k dispozici pouze pro následující:

|Klíč a uživatele|Vysvětlení|
|--|--|
|Klíč pro tvorbu vlastníka| Až 1000 koncový bod přístupů|
|Spolupracovníci/klíče pro vytváření přispěvatelů| Až 1000 koncový bod přístupů|
|Libovolný klíč přiřazený LUIS autorem nebo spolupracovníka/přispěvatelem|Založené na úrovni použití klíče|

#### <a name="runtime-security-for-public-apps"></a>Zabezpečení běhového prostředí pro veřejné aplikace

Jakmile aplikace je nakonfigurovaná jako veřejné, _jakékoli_ platný LUIS vytváření klíč nebo klíč koncového bodu služby LUIS můžete dotazovat vaší aplikace, tak dlouho, dokud klíč nebyl použit kvóty celý koncový bod.

Uživatel, který není vlastníkem nebo přispěvatelem, může k modulu runtime veřejné aplikace přistupovat jenom v případě, že má dané ID aplikace. Služba LUIS nemá veřejnou _trhu_ nebo jiný způsob, jak vyhledat aplikace z veřejného app.  

Aplikace z veřejného app je publikována ve všech oblastech, tak, aby uživatel s klíčem služby LUIS prostředků na základě oblasti můžete přistupovat k aplikaci v oblasti podle toho, která souvisí s klíč prostředku.

## <a name="transfer-of-ownership"></a>Převod vlastnictví

LUIS nemá na převod vlastnictví prostředku koncept. 

## <a name="securing-the-endpoint"></a>Zabezpečení koncového bodu 

Můžete určit, kdo může zobrazit klíč koncového bodu LUIS předpovědi, voláním v prostředí serveru na serveru. Pokud používáte LUIS z robota, je již zabezpečené připojení mezi robotů a LUIS. Pokud koncový bod služby LUIS se volat přímo, měli byste vytvořit rozhraní API na straně serveru (jako je Azure [funkce](https://azure.microsoft.com/services/functions/)) s řízený přístup (například [AAD](https://azure.microsoft.com/services/active-directory/)). Pokud se volá rozhraní API na straně serveru a ověřování a autorizace je ověřeno, předejte volání k LUIS. Při použití této strategie není útokům man-in-the-middle, zastírá váš koncový bod od uživatelů, umožňuje sledovat přístup a slouží k přidání koncového bodu odpovědi protokolování (například [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="next-steps"></a>Další kroky

* Vysvětlení [správy verzí](luis-concept-version.md) koncepty. 
* Naučte [se vytvářet klíče](luis-how-to-azure-subscription.md).
