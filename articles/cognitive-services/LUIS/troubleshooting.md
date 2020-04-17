---
title: Nejčastější dotazy (FAQ) – LUIS
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se porozumění jazykům (LUIS).
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: 59d9b1f21e1936b7d03293ec3d338677380a7c99
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530228"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Nejčastější dotazy týkající se porozumění jazykům

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se porozumění jazykům (LUIS).

## <a name="whats-new"></a>Co je nového

[Další informace](whats-new.md) o tom, co je nového v jazyce Porozumění (LUIS).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Vytváření obsahu

### <a name="what-are-the-luis-best-practices"></a>Jaké jsou doporučené postupy luis?
Začněte s [cyklem vytváření](luis-concept-app-iteration.md)a přečtěte si [doporučené postupy](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Jaký je nejlepší způsob, jak začít vytvářet svou aplikaci v LUIS?

Nejlepší způsob, jak vytvořit aplikaci, je [prostřednictvím přírůstkového procesu](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Co je vhodné modelovat záměry mé aplikace? Mám vytvořit konkrétnější nebo obecnější záměry?

Vyberte záměry, které nejsou tak obecné, aby se překrývaly, ale ne tak specifické, že je obtížné pro LUIS rozlišovat mezi podobnými záměry. Vytváření diskriminačních specifických záměrů je jedním z osvědčených postupů pro modelování luis.

### <a name="is-it-important-to-train-the-none-intent"></a>Je důležité trénovat žádný záměr?

Ano, je dobré trénovat **žádný** záměr s více projevy při přidávání další popisky do jiných záměrů. Dobrý poměr je 1 nebo 2 popisky přidané do **žádné** pro každých 10 popisků přidaných k záměru. Tento poměr zvyšuje diskriminativní výkon služby LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Jak lze opravit pravopisné chyby v projevech?

Podívejte se na kurz [Rozhraní API pro kontrolu pravopisu Bingu V7.](luis-tutorial-bing-spellcheck.md) Služba LUIS vynucuje omezení vynucená rozhraním API pro kontrolu pravopisu Bingu V7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Jak můžu programově upravit aplikaci LUIS?
Chcete-li aplikaci LUIS upravovat programově, použijte [rozhraní API pro vytváření .](https://go.microsoft.com/fwlink/?linkid=2092087) Viz [Volání LUIS vytváření rozhraní API](./get-started-get-model-rest-apis.md) a sestavení aplikace LUIS [programově pomocí Node.js](./luis-tutorial-node-import-utterances-csv.md) příklady, jak volat vytváření rozhraní API. Rozhraní API pro vytváření vyžaduje, abyste místo koncového klíče používali klíč [pro vytváření.](luis-concept-keys.md#azure-resources-for-luis) Programatická authoring umožňuje až 1 000 000 hovorů za měsíc a pět transakcí za sekundu. Další informace o klíčích, které používáte s luis, najdete v [tématu Správa klíčů](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Kde je funkce Vzor, která poskytla odpovídající regulární výraz?
Předchozí **funkce Pole** je aktuálně zastaralá, nahrazená **[vzorky](luis-concept-patterns.md)**.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Jak lze použít entitu k vytažení správných dat?
Viz [entity](luis-concept-entity-types.md) a [extrakce dat](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Měly by varianty příkladu utterance zahrnovat interpunkci?
Použijte jeden z následujících řešení:
* Ignorovat [interpunkci](luis-reference-application-settings.md#punctuation-normalization)
* Přidejte různé varianty jako příklad projevy záměru
* Přidejte vzor promluvy příkladu se [syntaxí, která ignoruje](luis-concept-patterns.md#pattern-syntax) interpunkci.

### <a name="does-luis-currently-support-cortana"></a>Podporuje služba LUIS aktuálně Cortanu?

Předdefinované aplikace Cortany byly v roce 2017 zastaralé. Již nejsou podporovány.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Jak převádím vlastnictví aplikace LUIS?
Chcete-li přenést aplikaci LUIS do jiného předplatného Azure, exportujte aplikaci LUIS a importujte ji pomocí nového účtu. Aktualizujte ID aplikace LUIS v klientské aplikaci, která ji volá. Nová aplikace může vrátit mírně odlišné skóre LUIS z původní aplikace.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Předem sestavená entita je označena v příkladu utterance namísto mé vlastní entity. Jak to mohu opravit?

Na portálu LUIS můžete označit text pro přesnou entitu, která vás zajímá extrahování. Pokud portál LUIS nezobrazuje správnou předpověď entity, budete muset přidat další projevy a označit entitu v textu nebo přidat popisovač (například funkci).

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Snažil jsem se importovat aplikaci nebo soubor verze, ale došlo k chybě, co se stalo?

Přečtěte si další informace o [chybách importu verze](luis-how-to-manage-versions.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Spolupráce a přispívání

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-role-based-access-control-rbac"></a>Jak udělit spolupracovníkům přístup k LUIS pomocí Azure Active Directory (Azure AD) nebo řízení přístupu na základě rolí (RBAC)?

Informace o tom, jak spolupracovníkům poskytnout přístup, najdete v tématu [Prostředky Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-resources) a uživatel [klienta Azure Active Directory.](luis-how-to-collaborate.md#azure-active-directory-tenant-user)

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Koncový bod

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Obdržel(a) jsem stavový kód chyby HTTP 403. Jak ho mám opravit?

Pokud překročíte transakce za sekundu nebo transakce za měsíc pro cenovou úroveň, zobrazí se 403 a 429 kódů chyb. Zvyšte svou cenovou úroveň nebo použijte [kontejnery](luis-container-howto.md)Language Understanding .

Při použití všech těchto volných 1000 koncový bod dotazy nebo překročíte kvótu měsíčních transakcí cenové úrovně, obdržíte kód stavu chyby HTTP 403.

Chcete-li tuto chybu opravit, musíte buď [změnit cenovou úroveň](luis-how-to-azure-subscription.md#change-pricing-tier) na vyšší úroveň, nebo [vytvořit nový prostředek](get-started-portal-deploy-app.md#create-the-endpoint-resource) a přiřadit jej [k aplikaci](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Řešení pro tuto chybu zahrnují:

* Na [webu Azure Portal](https://portal.azure.com)na vašem prostředku language understanding na **cenové úrovni Resource Management ->** změňte cenovou úroveň na vyšší úroveň TPS. Pokud je váš prostředek už přiřazen k aplikaci Language Understanding, nemusíte na portálu pro porozumění jazykům nic dělat.
*  Pokud vaše využití překročí nejvyšší cenovou úroveň, přidejte další prostředky language understanding s vyvažovačem zatížení před nimi. Kontejner [Language Understanding](luis-container-howto.md) s Kubernetes nebo Docker Compose vám může pomoci.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Obdržel(a) jsem stavový kód chyby HTTP 429. Jak ho mám opravit?

Pokud překročíte transakce za sekundu nebo transakce za měsíc pro cenovou úroveň, zobrazí se 403 a 429 kódů chyb. Zvyšte svou cenovou úroveň nebo použijte [kontejnery](luis-container-howto.md)Language Understanding .

Tento stavový kód je vrácen, když vaše transakce za sekundu překročí cenovou úroveň.

Řešení zahrnují:

* Pokud nejste na nejvyšší úrovni , můžete [zvýšit cenovou úroveň](luis-how-to-azure-subscription.md#change-pricing-tier).
* Pokud vaše využití překročí nejvyšší cenovou úroveň, přidejte další prostředky language understanding s vyvažovačem zatížení před nimi. Kontejner [Language Understanding](luis-container-howto.md) s Kubernetes nebo Docker Compose vám může pomoci.
* Můžete gate vaše požadavky klientské aplikace s [zásady opakování](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) implementovat sami, když se dostanete tento stavový kód.

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Dotaz na koncový bod vrátil neočekávané výsledky. Co bych měl/a dělat?

Neočekávané výsledky předpovědi dotazu jsou založeny na stavu publikovaného modelu. Chcete-li opravit model, bude pravděpodobně nutné změnit model, trénování a publikování znovu.

Oprava modelu začíná [aktivním učením](luis-how-to-review-endpoint-utterances.md).

Nedeterministické školení můžete odebrat aktualizací [rozhraní API pro nastavení verze aplikace,](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) abyste mohli používat všechna trénovací data.

Přečtěte si [doporučené postupy](luis-concept-best-practices.md) pro další tipy.

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Proč luis přidat mezery do dotazu kolem nebo uprostřed slov?
Služba LUIS [tokenizuje](luis-glossary.md#token) utterance na základě [jazykové verze](luis-language-support.md#tokenization). Původní hodnota i tokenizovaná hodnota jsou k dispozici pro [extrakci dat](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Jak vytvořím a přiřadím klíč koncového bodu LUIS?
[Vytvořte klíč koncového bodu](luis-how-to-azure-subscription.md) v Azure pro vaši úroveň [služeb.](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) [Přiřaďte klíč](luis-how-to-azure-subscription.md) na stránce **[Prostředky Azure.](luis-how-to-azure-subscription.md)** Pro tuto akci neexistuje žádné odpovídající rozhraní API. Potom je nutné změnit požadavek HTTP na koncový bod, aby [bylo nutné použít nový klíč koncového bodu](luis-concept-keys.md).

### <a name="how-do-i-interpret-luis-scores"></a>Jak interpretuji skóre LUIS?
Váš systém by měl používat nejvyšší bodovací záměr bez ohledu na jeho hodnotu. Například skóre nižší než 0,5 (méně než 50 %) nemusí nutně znamenat, že LUIS má nízkou důvěru. Poskytnutí dalších trénovacích dat může pomoci zvýšit [skóre](luis-concept-prediction-score.md) nejpravděpodobnějšího záměru.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Proč nevidím své požadavky na koncový bod na řídicím panelu aplikace?
Celkový počet přístupů koncovýbod v řídicím panelu vaší aplikace se pravidelně aktualizuje, ale metriky přidružené ke klíči koncového bodu LUIS na webu Azure Portal se aktualizují častěji.

Pokud na řídicím panelu nevidíte aktualizované požadavky na koncový bod, přihlaste se k portálu Azure a najděte prostředek přidružený ke klíči koncového bodu LUIS a **otevřete metriky** a vyberte metriku **Celkový počet volání.** Pokud se klíč koncového bodu používá pro více než jednu aplikaci LUIS, metrika na portálu Azure zobrazuje souhrnný počet volání ze všech aplikací LUIS, které ho používají.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Existuje příkaz Prostředí PowerShell, který se dostane do kvóty koncového bodu?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ke kvótě koncového bodu můžete zobrazit příkaz Prostředí PowerShell:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Moje aplikace LUIS pracovalvčera, ale dnes jsem dostat 403 chyb. Nezměnil jsem aplikaci. Jak ho mám opravit?
Podle těchto [pokynů](#how-do-i-create-and-assign-a-luis-endpoint-key) vytvořte klíč koncového bodu LUIS a přiřaďte ho k aplikaci. Potom je nutné změnit požadavek HTTP klientské aplikace na koncový bod, aby [bylo nutné použít nový klíč koncového bodu](luis-concept-keys.md). Pokud jste vytvořili nový prostředek v jiné oblasti, změňte také oblast požadavku klienta HTTP.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Jak zabezpečit koncový bod LUIS?
Viz [Zabezpečení koncového bodu](luis-concept-keys.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Práce v rámci limitů služby LUIS

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Jaký je maximální počet záměrů a entit, které může aplikace LUIS podporovat?
Viz odkaz na [hranice.](luis-boundaries.md)

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Chci vytvořit aplikaci LUIS s více než maximální počet záměrů. Co bych měl/a dělat?

Informace o záměrech naleznete [v tématu Doporučené postupy](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Chci vytvořit aplikaci v LUIS s více než maximální počet entit. Co bych měl/a dělat?

Podívejte se na [osvědčené postupy pro entity](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Jaká jsou omezení počtu a velikosti seznamů frází?
Maximální délku [seznamu frází](./luis-concept-feature.md)naleznete v odkazu na [hranice.](luis-boundaries.md)

### <a name="what-are-the-limits-on-example-utterances"></a>Jaké jsou limity na příklad projevy?
Viz odkaz na [hranice.](luis-boundaries.md)

## <a name="testing-and-training"></a>Testování a školení

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>U některých modelů v aplikaci se zobrazují chyby v podokně dávkového testování. Jak mohu tento problém vyřešit?

Chyby označují, že existuje nějaký nesoulad mezi štítky a předpovědi z modelů. Chcete-li tento problém vyřešit, proveďte jeden nebo oba následující úkoly:
* Chcete-li službě LUIS pomoci zlepšit diskriminaci mezi záměry, přidejte další popisky.
* Chcete-li pomoci LUIS učit rychleji, přidejte funkce seznamu frází, které zavádějí slovní zásobu specifickou pro doménu.

Podívejte se na kurz [testování dávky.](luis-tutorial-batch-testing.md)

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Když se aplikace exportuje a pak se znovu importuje do nové aplikace (s novým ID aplikace), skóre předpovědi LUIS se liší. Proč se to děje?

Viz [Predikce rozdíly mezi kopiemi stejné aplikace](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Některé projevy přejít na nesprávný záměr po provedené změny v aplikaci. Zdá se, že problém zmizí náhodně. Jak ho mám opravit?

Viz [Vlak se všemi daty](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Publikování aplikací

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Co je ID klienta v okně "Přidat klíč do aplikace"?
V Azure představuje klient klientklienta nebo organizaci, která je přidružená ke službě. Najděte své ID klienta na webu Azure Portal v poli **ID adresáře** tak, že vyberete**Služby Správy** > **vlastních služeb** **Azure Active Directory** > .

![ID klienta na webu Azure Portal](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Proč je v mé aplikaci přiřazeno víc klíčů koncového bodu, než jsem přiřadil?
Každá aplikace LUIS má pro usnadnění v seznamu koncových bodů klíč pro vytváření a spuštění. Tento klíč umožňuje pouze několik přístupů koncového bodu, takže si můžete vyzkoušet LUIS.

Pokud vaše aplikace existovala před LUIS byl obecně k dispozici (GA), luis klíče koncového bodu ve vašem předplatném jsou přiřazeny automaticky. To bylo provedeno, aby se migrace GA jednodušší. Všechny nové klíče koncového bodu LUIS na portálu Azure _nejsou_ automaticky přiřazeny luis.

## <a name="key-management"></a>Správa klíčů

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Jak poznám, jaký klíč potřebuji, kde ho získám a co s ním udělám?

Viz [Vytváření a předpověď dotazu koncového bodu klíče v LUIS](luis-concept-keys.md) se dozvíte o rozdílech mezi klíč vytváření a klíč běhu předpověď.

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Došlo k chybě v tom, že jsem nebyl akvóta. Jak ho mám opravit?

Další informace najdete v tématu Oprava stavového kódu HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) a [429.](#i-received-an-http-429-error-status-code-how-do-i-fix-it)

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Potřebuji zpracovat další dotazy koncového bodu. Jak to mám udělat?

Další informace najdete v tématu Oprava stavového kódu HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) a [429.](#i-received-an-http-429-error-status-code-how-do-i-fix-it)

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Vytvořil i autorský klíč, ale nezobrazuje se na portálu LUIS. Co se stalo?

Vývojové klíče jsou k dispozici na portálu LUIS po [migraci do prostředí s klíči pro vytváření](luis-migration-authoring.md).

## <a name="app-management"></a>Správa aplikací

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Jak lze stáhnout protokol projevy uživatelů?
Ve výchozím nastavení aplikace LUIS protokoly projevy od uživatelů. Chcete-li stáhnout protokol promluv, které uživatelé odesílají do vaší aplikace LUIS, přejděte na **Moje aplikace**a vyberte aplikaci. Na kontextovém panelu nástrojů vyberte **Exportovat protokoly koncových bodů**. Protokol je formátován jako soubor csv (oddělený od čárek).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Jak lze zakázat protokolování projevy?
Protokolování projevů uživatelů můžete vypnout `log=false` nastavením adresy URL koncového bodu, kterou klientská aplikace používá k dotazování služby LUIS. Vypnutíprotokolování však zakáže možnost aplikace LUIS navrhovat projevy nebo zlepšit výkon založený na [aktivním učení](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Pokud nastavíte `log=false` z důvodu ochrany osobních údajů obavy, nelze stáhnout záznam těchto projevy uživatelů z LUIS nebo použít tyto projevy ke zlepšení vaší aplikace.

Protokolování je pouze úložiště projevy.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Proč nechci zaznamenávat všechny projevy koncových bodů?
Pokud používáte protokol pro analýzu předpověď, nezachytávejte testovací projevy v protokolu.

## <a name="data-management"></a>Správa dat

### <a name="can-i-delete-data-from-luis"></a>Můžu odstranit data z LUIS?

* Vždy můžete odstranit příklad projevy používané pro školení LUIS. Pokud odstraníte příklad utterance z aplikace LUIS, je odebrán z webové služby LUIS a není k dispozici pro export.
* Můžete odstranit projevy ze seznamu projevy uživatelů, které LUIS navrhuje na stránce **Zkontrolovat projevy koncového bodu.** Odstranění projevy z tohoto seznamu zabraňuje jejich navržení, ale neodstraní je z protokolů.
* Pokud odstraníte účet, budou odstraněny všechny aplikace spolu s jejich příklady projevy a protokoly. Data jsou na serverech uchovávána po dobu 60 dnů, než budou trvale odstraněna.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Jak microsoft spravuje data odesílaná službě LUIS?

[Centrum zabezpečení](https://www.microsoft.com/trustcenter) vysvětluje naše závazky a možnosti správy dat a přístupu ve službách Azure.

## <a name="language-and-translation-support"></a>Podpora jazyků a překladů

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Mám aplikaci v jednom jazyce a chci vytvořit paralelní aplikaci v jiném jazyce. Jaký je nejjednodušší způsob, jak to udělat?
1. Exportujte aplikaci.
2. Přeložte popisované projevy v souboru JSON exportované aplikace do cílového jazyka.
3. Možná budete muset změnit názvy záměrů a entit nebo je ponechat tak, jak jsou.
4. Nakonec importujte aplikaci, abyste měli aplikaci LUIS v cílovém jazyce.

## <a name="app-notification"></a>Oznámení o aplikaci

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Proč jsem dostal e-mail s upozorněním, že jsem téměř mimo kvótu?
Váš klíč pro vytváření a startování je povolen pouze 1000 dotazů koncového bodu za měsíc. Vytvořte klíč koncového bodu LUIS (zdarma nebo placené) a použít tento klíč při vytváření dotazů koncového bodu. Pokud provádíte dotazy koncového bodu z robota nebo jiné klientské aplikace, je třeba změnit klíč koncového bodu LUIS.

## <a name="bots"></a>Roboti

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>Můj LUIS bot nefunguje. Co mám udělat?

První problém je izolovat, pokud problém souvisí s LUIS nebo se stane mimo middleware LUIS.

#### <a name="resolve-issue-in-luis"></a>Řešení problému v luis
Předat stejnou promluvu luis z [koncového bodu LUIS](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint). Pokud se zobrazí chyba, vyřešte problém v LUIS, dokud se chyba již nevrátí. Běžné chyby zahrnují:

* `Out of call volume quota. Quota will be replenished in <time>.`- Tento problém znamená, že je třeba buď změnit z vývojového klíče na [klíč koncového bodu,](luis-how-to-azure-subscription.md) nebo je třeba změnit [úrovně služby](luis-how-to-azure-subscription.md#change-pricing-tier).

#### <a name="resolve-issue-in-azure-bot-service"></a>Řešení problému ve službě Azure Bot Service

Pokud používáte službu Azure Bot service a problém je, že **test ve webovém chatu** vrátí `Sorry, my bot code is having an issue`, zkontrolujte protokoly:

1. Na webu Azure Portal včásti správa **botů** vyberte **build**.
1. Otevřete online editor kódu.
1. V horní části modrého navigačního panelu vyberte název robota (druhá položka vpravo).
1. Ve výsledném rozevíracím seznamu vyberte **Otevřít konzolu Kudu**.
1. Vyberte **LogFiles**, pak vyberte **Aplikace**. Zkontrolujte všechny soubory protokolu. Pokud chybu nevidíte ve složce aplikace, zkontrolujte všechny soubory protokolu v části **LogFiles**.
1. Nezapomeňte znovu sestavit projekt, pokud používáte zkompilovaný jazyk, jako je například C#.

> [!Tip]
> Konzola může také instalovat balíčky.

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Vyřešte problém při ladění v místním počítači pomocí rozhraní Bot Framework.

Další informace o místním ladění robota najdete v [tématu Ladění robota](https://docs.microsoft.com/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0).

## <a name="integrating-luis"></a>Integrace služby LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Kde se moje aplikace LUIS vytvořila během procesu předplatného robota pro webovou aplikaci Azure?
Pokud vyberete šablonu LUIS a v podokně předlohy vyberete tlačítko **Vybrat,** levé podokno se změní tak, aby zahrnovalo typ šablony, a zeptá se, v jaké oblasti má být šablona LUIS vytvořit. Proces robota webové aplikace však nevytvoří předplatné LUIS.

![Oblast robota pro webové aplikace před šablony LUIS](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Jaké oblasti LUIS podporují přípravu řeči bot frameworku?
[Priming řeči](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) je podporován a podporuje se pouze pro aplikace LUIS v centrální (US) instanci.

## <a name="api-programming-strategies"></a>Strategie programování rozhraní API

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Jak programově získám oblast LUIS prostředku?

Pomocí ukázky SLUŽBY LUIS [vyhledejte oblast](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) programově pomocí jazyka C# nebo Node.Js.

## <a name="luis-service"></a>Služba LUIS

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Je language understanding (LUIS) k dispozici místně nebo v privátním cloudu?

Ano, můžete použít [kontejner](luis-container-howto.md) LUIS pro tyto scénáře, pokud máte potřebné připojení k využití měřiče.

## <a name="migrating-to-the-next-version"></a>Migrace na další verzi

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Jak se migruje do náhledu rozhraní API V3?

Viz [ROZHRANÍ API v2 až v3 Průvodce migrací pro aplikace LUIS](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>Sestavení oznámení konference 2019

Na konferenci Build 2019 byly vydány následující funkce:

* [Náhled průvodce migrací rozhraní API V3](luis-migration-api-v3.md)
* [Vylepšený řídicí panel analýzy](luis-how-to-use-dashboard.md)
* [Vylepšené předem vyestavěné domény](luis-reference-prebuilt-domains.md)
* [Entity dynamického seznamu](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Externí subjekty](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Videa:

* [Jak používat Azure Conversational AI k škálování vašeho podnikání pro příští generaci](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Další kroky

Další informace o službě LUIS najdete v následujících zdrojích:
* [Otázky přetečení zásobníku označené službou LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Fórum pro porozumění jazykům MSDN inteligentních služeb (LUIS)](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)
