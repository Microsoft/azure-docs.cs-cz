---
title: Vytváření a modul runtime klíčů – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS používá dva klíče, vytváření a klíč modulu runtime pro dotazování koncového bodu předpovědi s uživatelským projevy.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: 5a6c87da7ae62af54990e0a1a2c62065717a201a
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256960"
---
# <a name="authoring-and-runtime-keys"></a>Vytváření a běhové klíče


>[!NOTE]
>Než budete pokračovat, [migrujte](luis-migration-authoring.md) prosím všechny aplikace, které nepoužívají prostředek pro vytváření prostředků Azure.

LUIS používá dva typy prostředků Azure, přičemž každý typ má klíče: 
 
* [Vytváření](#programmatic-key) k vytváření záměrů, entit a popisků projevy, školení a publikování. Až budete připraveni k publikování aplikace LUIS, budete potřebovat [klíč koncového bodu předpovědi pro modul runtime](luis-how-to-azure-subscription.md) přiřazený k aplikaci.
* [Klíč koncového bodu předpovědi pro modul runtime](#prediction-endpoint-runtime-key). Klientské aplikace, jako je například robota chatu, potřebují prostřednictvím tohoto klíče přístup k **koncovému bodu předpovědi dotazů** modulu runtime. 

|Klíč|Účel|Služba rozpoznávání`kind`|Služba rozpoznávání`type`|
|--|--|--|--|
|[Klíč pro tvorbu](#programmatic-key)|Vytváření, školení, publikování a testování.|`LUIS.Authoring`|`Cognitive Services`|
|[Klíč runtime koncového bodu předpovědi](#prediction-endpoint-runtime-key)| Modul předpovědi pro koncové body v rámci dotazu s uživatelem utterance k určení záměrů a entit.|`LUIS`|`Cognitive Services`|

LUIS také poskytuje [počáteční klíč](luis-how-to-azure-subscription.md#starter-key) s kvótou koncového bodu předpovědi 1000 transakcí za měsíc. 

I když nemusíte současně vytvářet obě klíče, je to mnohem jednodušší.

Je důležité vytvářet aplikace LUIS v [oblastech](luis-reference-regions.md#publishing-regions) , kde chcete publikovat a dotazovat.

<a name="programmatic-key" ></a>

## <a name="authoring-key"></a>Klíč pro tvorbu

Klíč pro vytváření obsahu se vytvoří automaticky při vytvoření účtu LUIS a je zadarmo. Po zahájení práce s LUIS máte jeden počáteční klíč pro všechny vaše aplikace LUIS pro každou [oblast](luis-reference-regions.md)vytváření obsahu. Účelem tohoto klíčového klíče je poskytnout ověřování ke správě aplikace v LUIS nebo k testování dotazů na koncový bod předpovědi. 

Vytváření vytváření klíčů v Azure Portal umožňuje řídit oprávnění k vytváření prostředků přiřazením osob k [roli Přispěvatel](#contributions-from-other-authors). Abyste mohli přidávat přispěvatele, potřebujete oprávnění na úrovni předplatného Azure. 

Pokud chcete vyhledat klíč pro vytváření, přihlaste se k [Luis](luis-reference-regions.md#luis-website) a kliknutím na název účtu v pravém horním navigačním panelu otevřete **Nastavení účtu**.

![Klíč pro tvorbu](./media/luis-concept-keys/authoring-key.png)

Pokud chcete vytvořit **běhové dotazy**, vytvořte [prostředek Azure Luis](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). 

> [!CAUTION]
> Pro usnadnění práce mnoho z ukázek používá [počáteční klíč](#starter-prediction-endpoint-runtime-key) , protože poskytuje několik bezplatných volání koncového bodu předpovědi ve své [kvótě](luis-boundaries.md#key-limits).  

<a name="endpoint-key"></a>

## <a name="prediction-endpoint-runtime-key"></a>Klíč runtime koncového bodu předpovědi 

Pokud potřebujete **dotazy na koncový bod za běhu**, vytvořte prostředek Language UNDERSTANDING (Luis) a pak ho přiřaďte do aplikace Luis. 

[!INCLUDE [Azure runtime resource creation for Language Understanding and Cognitive Service resources](../../../includes/cognitive-services-luis-azure-resource-instructions.md)]

Až se proces vytváření prostředků dokončí, [přiřaďte](luis-how-to-azure-subscription.md) k aplikaci klíč. 

* Klíč modulu runtime (koncový bod předpovědi dotazu) umožňuje kvótu přístupů koncového bodu na základě plánu využití, který jste zadali při vytváření klíče modulu runtime. Zobrazit [ceny služby Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) informace o cenách.

* Klíč za běhu se dá použít pro všechny aplikace LUIS nebo pro konkrétní aplikace LUIS. 
* Nepoužívejte klíč za běhu k vytváření aplikací LUIS. 

### <a name="starter-prediction-endpoint-runtime-key"></a>Klíč za běhu koncového bodu předpovědi počátečního prostředí

Klíč koncového bodu předpovědi **počátečního** kódu je zdarma a zahrnuje dotazy na koncový bod předpovědi 1000. Po použití těchto dotazů byste pro Language Understanding měli vytvořit vlastní prostředek koncového bodu předpovědi.  

Jedná se o speciální prostředek vytvořený za vás. Nezobrazuje se v seznamu prostředků Azure, protože je určen jako dočasný počáteční klíč. 

<a name="use-endpoint-key-in-query"></a>

### <a name="use-runtime-key-in-query"></a>Použít klíč za běhu v dotazu
Koncový bod LUIS runtime akceptuje dva styly dotazů, obě používají klíč předplatného koncového bodu pro prostředí, ale na různých místech.

Koncový bod používaný pro přístup k modulu runtime používá subdoménu, která je jedinečná pro vaši oblast prostředku, označená `{region}` v následující tabulce. 

|Příkaz|Příklad adresy url a klíč umístění|
|--|--|
|[GET](https://{region}.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?runtime-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`<br><br>Hodnota řetězce dotazu pro `runtime-key`<br><br>Změnit hodnotu dotazu váš koncový bod pro `runtime-key` z vytváření klíč (starter), a nový klíč koncového bodu, chcete-li použít míra klíče kvót koncový bod služby LUIS. Pokud vytvoříte klíč a přiřadit klíč, ale neměňte hodnotu dotazu koncový bod pro `runtime-key`, nepoužíváte kvótu klíče koncového bodu.|
|[POST](https://{region}.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`<br><br> Hodnota hlavičky pro `Ocp-Apim-Subscription-Key`<br>Pokud vytvoříte klíč za běhu a přiřadíte klíč za běhu, ale nezměníte hodnotu dotazu koncového `Ocp-Apim-Subscription-Key`bodu pro, nepoužíváte klíč za běhu.|

ID aplikace použité v předchozím adresy URL, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, je veřejná aplikace IoT používané k [interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). 

## <a name="assignment-of-the-runtime-key"></a>Přiřazení klíče za běhu

Klíč za běhu můžete [přiřadit](luis-how-to-azure-subscription.md) na [portálu Luis](https://www.luis.ai) nebo prostřednictvím odpovídajících rozhraní API. 

## <a name="key-limits"></a>Omezení klíčů

Pro každou oblast a předplatné můžete vytvořit až 10 klíčů pro vytváření obsahu. 

Podívejte se na téma [omezení klíčů](luis-boundaries.md#key-limits) a [oblasti Azure](luis-reference-regions.md). 

Publikování oblastech se liší od vytváření oblastí. Ujistěte se, že jste vytvořili aplikaci v oblasti vytváření obsahu odpovídající oblasti publikování, kterou chcete najít v klientské aplikaci.

## <a name="key-limit-errors"></a>Omezení klíče chyby
Pokud překročíte kvótu transakcí za sekundu (TPS), zobrazí se chyba HTTP 429. Pokud překročíte kvótu transakcí za měsíc (TPS), zobrazí se chyba HTTP 403. 

## <a name="contributions-from-other-authors"></a>Příspěvky od jiných autorů



Správa příspěvků od spolupracovníků závisí na aktuálním stavu aplikace.

**Pro [vytváření prostředků migrovaných](luis-migration-authoring.md) aplikací**: _přispěvatelé_ se spravují v Azure Portal pro vytváření prostředků pomocí stránky **řízení přístupu (IAM)** . Naučte [se, jak přidat uživatele](luis-how-to-collaborate.md)pomocí e-mailové adresy spolupracovníka a role _přispěvatele_ . 

**Pro aplikace, které ještě nebyly migrovány**: všechny _spolupracovníci_ jsou spravováni na portálu Luis ze stránky **spolupracovníci pro správu >** .

### <a name="contributor-roles-vs-entity-roles"></a>Role přispěvatele a role entit

[Role entit](luis-concept-roles.md) se vztahují na datový model aplikace Luis. Role spolupracovníka/Přispěvatel se vztahují na úrovně přístupu pro vytváření. 

## <a name="moving-or-changing-ownership"></a>Přesunutí nebo změna vlastnictví

Aplikace je definovaná pomocí prostředků Azure, které určuje předplatné vlastníka. 

Aplikaci LUIS můžete přesunout. V Azure Portal nebo Azure CLI použijte následující dokumentaci:

* [Přesun aplikace mezi LUIS vytváření prostředků](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Přesunout prostředek do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/resource-group-move-resources.md)
* [Přesunutí prostředku v rámci stejného předplatného nebo napříč předplatnými](../../azure-resource-manager/move-limitations/app-service-move-limitations.md)
* [Přenos vlastnictví](../../billing/billing-subscription-transfer.md) předplatného 

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

### <a name="prediction-endpoint-runtime-access"></a>Prediktivní přístup k modulu runtime koncového bodu

Přístup k dotazu na koncový bod předpovědi je řízen nastavením na stránce **informace o aplikaci** v části **Spravovat** . 

![Sada aplikací na veřejnou](./media/luis-concept-security/set-application-as-public.png)

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

**Pro [vytváření prostředků migrovaných](luis-migration-authoring.md) aplikací**: 

**Pro aplikace, které ještě nebyly migrovány**: Exportujte aplikaci jako soubor JSON. Jiný uživatel LUIS může aplikaci importovat, takže se stane vlastníkem aplikace. Nová aplikace bude mít jiné ID aplikace.  

## <a name="securing-the-endpoint"></a>Zabezpečení koncového bodu 

Můžete určit, kdo může zobrazit klíč koncového bodu LUIS předpovědi, voláním v prostředí serveru na serveru. Pokud používáte LUIS z robota, je již zabezpečené připojení mezi robotů a LUIS. Pokud koncový bod služby LUIS se volat přímo, měli byste vytvořit rozhraní API na straně serveru (jako je Azure [funkce](https://azure.microsoft.com/services/functions/)) s řízený přístup (například [AAD](https://azure.microsoft.com/services/active-directory/)). Pokud se volá rozhraní API na straně serveru a ověřování a autorizace je ověřeno, předejte volání k LUIS. Při použití této strategie není útokům man-in-the-middle, zastírá váš koncový bod od uživatelů, umožňuje sledovat přístup a slouží k přidání koncového bodu odpovědi protokolování (například [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="next-steps"></a>Další postup

* Vysvětlení [správy verzí](luis-concept-version.md) koncepty. 
* Naučte [se vytvářet klíče](luis-how-to-azure-subscription.md).
