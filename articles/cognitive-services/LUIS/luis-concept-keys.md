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
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 70e58077fa40ce685324cd24b447886ec3411034
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703183"
---
# <a name="authoring-and-runtime-keys"></a>Vytváření a běhové klíče


>[!NOTE]
>Než budete pokračovat, [migrujte](luis-migration-authoring.md) prosím všechny aplikace, které nepoužívají prostředek pro vytváření prostředků Azure.

LUIS používá dva typy prostředků Azure, přičemž každý typ má klíče: 
 
* [Vytváření](#programmatic-key) k vytváření záměrů, entit a popisků projevy, školení a publikování. Až budete připraveni k publikování aplikace LUIS, budete potřebovat [klíč koncového bodu předpovědi pro modul runtime](luis-how-to-azure-subscription.md) přiřazený k aplikaci.
* [Klíč koncového bodu předpovědi pro modul runtime](#prediction-endpoint-runtime-key). Klientské aplikace, jako je například robota chatu, potřebují prostřednictvím tohoto klíče přístup k **koncovému bodu předpovědi dotazů** modulu runtime. 

|Key|Účel|Služba rozpoznávání `kind`|Služba rozpoznávání `type`|
|--|--|--|--|
|[Vytváření klíče](#programmatic-key)|Vytváření, školení, publikování a testování.|`LUIS.Authoring`|`Cognitive Services`|
|[Klíč runtime koncového bodu předpovědi](#prediction-endpoint-runtime-key)| Modul předpovědi pro koncové body v rámci dotazu s uživatelem utterance k určení záměrů a entit.|`LUIS`|`Cognitive Services`|

LUIS také poskytuje [počáteční klíč](luis-how-to-azure-subscription.md#starter-key) s kvótou koncového bodu předpovědi 1000 transakcí za měsíc. 

I když nemusíte současně vytvářet obě klíče, je to mnohem jednodušší.

Je důležité vytvářet aplikace LUIS v [oblastech](luis-reference-regions.md#publishing-regions) , kde chcete publikovat a dotazovat.

<a name="programmatic-key" ></a>

## <a name="authoring-key"></a>Vytváření klíče

Klíč pro vytváření obsahu se vytvoří automaticky při vytvoření účtu LUIS a je zadarmo. Po zahájení práce s LUIS máte jeden počáteční klíč pro všechny vaše aplikace LUIS pro každou [oblast](luis-reference-regions.md)vytváření obsahu. Účelem tohoto klíčového klíče je poskytnout ověřování ke správě aplikace v LUIS nebo k testování dotazů na koncový bod předpovědi. 

Vytváření vytváření klíčů v Azure Portal umožňuje řídit oprávnění k vytváření prostředků přiřazením osob k [roli Přispěvatel](#contributions-from-other-authors). Abyste mohli přidávat přispěvatele, potřebujete oprávnění na úrovni předplatného Azure. 

Pokud chcete vyhledat klíč pro vytváření, přihlaste se k [Luis](luis-reference-regions.md#luis-website) a kliknutím na název účtu v pravém horním navigačním panelu otevřete **Nastavení účtu**.

![vytváření klíče](./media/luis-concept-keys/authoring-key.png)

Pokud chcete vytvořit **běhové dotazy**, vytvořte [prostředek Azure Luis](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). 

> [!CAUTION]
> Pro usnadnění práce mnoho z ukázek používá [počáteční klíč](#starter-prediction-endpoint-runtime-key) , protože poskytuje několik bezplatných volání koncového bodu předpovědi ve své [kvótě](luis-boundaries.md#key-limits).  

<a name="endpoint-key"></a>

## <a name="prediction-endpoint-runtime-key"></a>Klíč runtime koncového bodu předpovědi 

Pokud potřebujete **dotazy na koncový bod za běhu**, vytvořte prostředek Language UNDERSTANDING (Luis) a pak ho přiřaďte do aplikace Luis. 

[!INCLUDE [Azure runtime resource creation for Language Understanding and Cognitive Service resources](../../../includes/cognitive-services-luis-azure-resource-instructions.md)]

Až se proces vytváření prostředků dokončí, [přiřaďte](luis-how-to-azure-subscription.md) k aplikaci klíč. 

* Klíč modulu runtime (koncový bod předpovědi dotazu) umožňuje kvótu přístupů koncového bodu na základě plánu využití, který jste zadali při vytváření klíče modulu runtime. Informace o cenách najdete v tématu [Cognitive Services ceny](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) .

* Klíč za běhu se dá použít pro všechny aplikace LUIS nebo pro konkrétní aplikace LUIS. 
* Nepoužívejte klíč za běhu k vytváření aplikací LUIS. 

### <a name="starter-prediction-endpoint-runtime-key"></a>Klíč za běhu koncového bodu předpovědi počátečního prostředí

Klíč koncového bodu předpovědi **počátečního** kódu je zdarma a zahrnuje dotazy na koncový bod předpovědi 1000. Po použití těchto dotazů byste pro Language Understanding měli vytvořit vlastní prostředek koncového bodu předpovědi.  

Jedná se o speciální prostředek vytvořený za vás. Nezobrazuje se v seznamu prostředků Azure, protože je určen jako dočasný počáteční klíč. 

<a name="use-endpoint-key-in-query"></a>

### <a name="use-runtime-key-in-query"></a>Použít klíč za běhu v dotazu
Koncový bod LUIS runtime akceptuje dva styly dotazů, obě používají klíč předplatného koncového bodu pro prostředí, ale na různých místech.

Koncový bod používaný pro přístup k modulu runtime používá subdoménu, která je pro vaši oblast prostředku jedinečná, označená pomocí `{region}` v následující tabulce. 


#### <a name="v2-prediction-endpointtabv2"></a>[Koncový bod pro předpověď v2](#tab/V2)

|Operace|Příklad adresy URL a umístění klíče|
|--|--|
|[Čtěte](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?runtime-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`|
|[SPUŠTĚNÍ](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`|

#### <a name="v3-prediction-endpointtabv3"></a>[Prediktivní koncový bod V3](#tab/V3)

|Operace|Příklad adresy URL a umístění klíče|
|--|--|
|[Čtěte](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5cb0a91e54c9db63d589f433)|`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?runtime-key=your-endpoint-key-here&query=turn%20on%20the%20lights`|
|[SPUŠTĚNÍ](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5cb0a5830f741b27cd03a061)| `https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict`| 

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

* * * 

**Get**: Změňte hodnotu dotazu koncového bodu pro `runtime-key` z klíče pro vytváření obsahu (počáteční) na nový klíč koncového bodu, aby se použila míra kvót klíčů koncového bodu Luis. Pokud vytvoříte klíč a přiřadíte klíč, ale nezměníte hodnotu dotazu koncového bodu pro `runtime-key`, nepoužíváte kvótu klíče koncového bodu.

**Post**: Změňte hodnotu hlavičky pro `Ocp-Apim-Subscription-Key`.<br>Pokud vytvoříte klíč za běhu a přiřadíte klíč za běhu, ale nezměníte hodnotu dotazu koncového bodu pro `Ocp-Apim-Subscription-Key`, nepoužíváte klíč za běhu.

ID aplikace použité v předchozích adresách URL, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, je veřejná aplikace IoT, která se používá pro [interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). 

## <a name="assignment-of-the-runtime-key"></a>Přiřazení klíče za běhu

Klíč za běhu můžete [přiřadit](luis-how-to-azure-subscription.md) na [portálu Luis](https://www.luis.ai) nebo prostřednictvím odpovídajících rozhraní API. 

## <a name="key-limits"></a>Omezení klíčů

Pro každou oblast a předplatné můžete vytvořit až 10 klíčů pro vytváření obsahu. 

Podívejte se na téma [omezení klíčů](luis-boundaries.md#key-limits) a [oblasti Azure](luis-reference-regions.md). 

Oblasti publikování se liší od oblastí vytváření obsahu. Ujistěte se, že jste vytvořili aplikaci v oblasti vytváření obsahu odpovídající oblasti publikování, kterou chcete najít v klientské aplikaci.

## <a name="key-limit-errors"></a>Chyby omezení klíčů
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

|Přístup pro vytváření obsahu zahrnuje|Poznámky|
|--|--|
|Přidat nebo odebrat klíče koncového bodu||
|Export verze||
|Exportovat protokoly koncových bodů||
|Import verze||
|Nastavit aplikaci jako veřejnou|Když je aplikace veřejná, může se uživatel dotazovat na aplikaci s vytvářením nebo klíčovým bodem.|
|Upravit model|
|Opětovn|
|Kontrola služby Endpoint projevy pro [aktivní učení](luis-how-to-review-endpoint-utterances.md)|
|Průřez|

### <a name="prediction-endpoint-runtime-access"></a>Prediktivní přístup k modulu runtime koncového bodu

Přístup k dotazu na koncový bod předpovědi je řízen nastavením na stránce **informace o aplikaci** v části **Spravovat** . 

![Nastavení aplikace na veřejné](./media/luis-concept-security/set-application-as-public.png)

|[Soukromý koncový bod](#runtime-security-for-private-apps)|[Veřejný koncový bod](#runtime-security-for-public-apps)|
|:--|:--|
|K dispozici pro vlastníka a přispěvatele|Dostupné pro vlastníka, přispěvatele a někoho jiného, kdo zná ID aplikace|

Můžete určit, kdo uvidí klíč LUIS runtime, voláním v prostředí serveru na serveru. Pokud používáte LUIS z robota, propojení mezi robotem a LUIS je už zabezpečené. Pokud voláte koncový bod LUIS přímo, měli byste vytvořit rozhraní API na straně serveru (například [funkci](https://azure.microsoft.com/services/functions/)Azure) s řízeným přístupem (například [AAD](https://azure.microsoft.com/services/active-directory/)). Když se zavolá rozhraní API na straně serveru a ověří se ověření a autorizaci, předejte volání do LUIS. I když tato strategie nebrání útokům prostředníkem, zařadí klíč a adresu URL koncového bodu vašim uživatelům, umožní vám sledovat přístup a umožňuje přidat protokolování odpovědí koncového bodu (například [Application Insights](https://azure.microsoft.com/services/application-insights/)).

#### <a name="runtime-security-for-private-apps"></a>Zabezpečení běhového prostředí pro privátní aplikace

Modul runtime privátní aplikace je k dispozici pouze pro následující:

|Klíč a uživatel|Vysvětlení|
|--|--|
|Klíč pro vytváření vlastníka| Až 1000 přístupů do koncového bodu|
|Spolupracovníci/klíče pro vytváření přispěvatelů| Až 1000 přístupů do koncového bodu|
|Libovolný klíč přiřazený LUIS autorem nebo spolupracovníka/přispěvatelem|Na základě úrovně použití klíče|

#### <a name="runtime-security-for-public-apps"></a>Zabezpečení běhového prostředí pro veřejné aplikace

Jakmile je aplikace nakonfigurovaná jako veřejná, _jakýkoli_ platný LUISový klíč nebo klíč koncového bodu Luis se může dotazovat na vaši aplikaci, pokud klíč nepoužil celou kvótu koncového bodu.

Uživatel, který není vlastníkem nebo přispěvatelem, může k modulu runtime veřejné aplikace přistupovat jenom v případě, že má dané ID aplikace. LUIS nemá veřejný _trh_ ani jiný způsob hledání veřejné aplikace.  

Veřejná aplikace je publikovaná ve všech oblastech, aby uživatel s klíčem prostředků LUIS založeným na oblasti měl přístup k aplikaci v jakékoli oblasti, která je spojená s klíčem prostředku.

## <a name="transfer-of-ownership"></a>Přenos vlastnictví

**Pro [vytváření prostředků migrovaných](luis-migration-authoring.md) aplikací**: jako vlastník prostředku můžete přidat `contributor`.

**Pro aplikace, které ještě nebyly migrovány**: Exportujte aplikaci jako soubor JSON. Jiný uživatel LUIS může aplikaci importovat, takže se stane vlastníkem aplikace. Nová aplikace bude mít jiné ID aplikace.  

## <a name="securing-the-endpoint"></a>Zabezpečení koncového bodu 

Můžete určit, kdo může zobrazit klíč koncového bodu LUIS předpovědi, voláním v prostředí serveru na serveru. Pokud používáte LUIS z robota, propojení mezi robotem a LUIS je už zabezpečené. Pokud voláte koncový bod LUIS přímo, měli byste vytvořit rozhraní API na straně serveru (například [funkci](https://azure.microsoft.com/services/functions/)Azure) s řízeným přístupem (například [AAD](https://azure.microsoft.com/services/active-directory/)). Když se zavolá rozhraní API na straně serveru a ověří se ověřování a autorizace, předejte volání do LUIS. I když tato strategie nebrání útokům prostředníkem, zařadí váš koncový bod vašim uživatelům, umožní vám sledovat přístup a umožňuje přidat protokolování odpovědí koncových bodů (například [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="next-steps"></a>Další kroky

* Porozumění konceptům [správy verzí](luis-concept-version.md) . 
* Naučte [se vytvářet klíče](luis-how-to-azure-subscription.md).
