---
title: Nejčastější dotazy (FAQ) – LUIS
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: troubleshooting
ms.date: 05/06/2020
ms.openlocfilehash: b5e25e9ed25ced96d38994928bcb6275ce79420f
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612792"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Nejčastější dotazy ke službě Language Understanding

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Language Understanding (LUIS).

## <a name="whats-new"></a>Novinky

[Přečtěte si další](whats-new.md) informace o novinkách v Language UNDERSTANDING (Luis).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Vytváření obsahu

### <a name="what-are-the-luis-best-practices"></a>Jaké jsou osvědčené postupy pro LUIS?
Začněte s [cyklem vytváření](luis-concept-app-iteration.md)a pak si přečtěte [osvědčené postupy](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Jaký je nejlepší způsob, jak začít sestavovat aplikaci v LUIS?

Nejlepším způsobem, jak sestavit aplikaci, je [přírůstkový proces](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Co je dobrým zvykem modelování záměrů mojí aplikace? Mám vytvořit konkrétnější nebo obecnější záměry?

Vyberte záměry, které nejsou tak obecné, aby se překrývaly, ale ne tak specifické, aby bylo obtížné LUIS rozlišovat mezi podobnými záměry. Jedním z osvědčených postupů pro modelování LUIS je vytvoření diskriminačního konkrétního záměru.

### <a name="is-it-important-to-train-the-none-intent"></a>Je důležité naučit se žádné záměry?

Ano, je dobré naučit svůj záměr **none** s více projevy při přidávání dalších popisků k ostatním záměrům. Dobrý poměr je 1 nebo 2 přidaných popisků k **žádnému** pro každých 10 popisků přidaných k záměru. Tento poměr zvyšuje diskriminační sílu LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Jak mohu opravit pravopisné chyby v projevy?

Přečtěte si kurz [rozhraní API Bingu pro kontrolu pravopisu v7](luis-tutorial-bing-spellcheck.md) . LUIS vynucuje omezení vyplývající z rozhraní API Bingu pro kontrolu pravopisu v7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Návody aplikaci LUIS programově upravit?
Pokud chcete aplikaci LUIS upravit programově, použijte [rozhraní API pro vytváření obsahu](https://go.microsoft.com/fwlink/?linkid=2092087). Příklady volání rozhraní API pro vytváření najdete v tématu [volání rozhraní API pro Luis](./get-started-get-model-rest-apis.md) a vytváření [aplikací Luis pomocí Node.js](./luis-tutorial-node-import-utterances-csv.md) . Rozhraní API pro vytváření obsahu vyžaduje použití [klíčového obsahu](luis-how-to-azure-subscription.md#azure-resources-for-luis) místo klíče koncového bodu. Programový vytváření obsahu umožňuje až 1 000 000 volání za měsíc a pět transakcí za sekundu. Další informace o klíčích, které používáte se službou LUIS, najdete v tématu [Správa klíčů](./luis-how-to-azure-subscription.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Kde je funkce vzoru, která poskytuje porovnávání regulárních výrazů?
Předchozí **funkce vzoru** je aktuálně zastaralá, Nahrazená **[vzorci](luis-concept-patterns.md)**.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Návody použít entitu pro stažení správných dat?
Viz [entity](luis-concept-entity-types.md) a [extrakce dat](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Měly by variace ukázkového utterance zahrnovat interpunkční znaménka?
Použijte jedno z následujících řešení:
* Ignorovat [interpunkční znaménka](luis-reference-application-settings.md#punctuation-normalization)
* Přidejte různé variace jako příklad projevy k záměru.
* Přidejte vzor příkladu utterance s [syntaxí pro ignorování](luis-concept-patterns.md#pattern-syntax) interpunkce.

### <a name="does-luis-currently-support-cortana"></a>Podporuje LUIS aktuálně podporu Cortany?

Předem připravené aplikace Cortana byly zastaralé v 2017. Již nejsou podporovány.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Návody přenést vlastnictví aplikace LUIS?
Pokud chcete přenést aplikaci LUIS do jiného předplatného Azure, exportujte aplikaci LUIS a importujte ji pomocí nového účtu. Aktualizujte ID aplikace LUIS v klientské aplikaci, která je volá. Nová aplikace může vracet mírně odlišnou LUIS skóre od původní aplikace.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Předem vytvořená entita je označena jako utterance jako místo vlastní entity. Návody opravit?

Na portálu LUIS můžete označit text pro přesně entitu, které vás zajímá při extrakci. Pokud na portálu LUIS není zobrazená správná předpověď entity, možná budete muset přidat další projevy a označit entitu v rámci textu nebo přidat funkci.

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Pokusili jste se importovat soubor aplikace nebo verze, ale zobrazila se chyba, co se stalo?

Přečtěte si další informace o [chybách importu verzí](luis-how-to-manage-versions.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Spolupráce a přispívání

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-azure-role-based-access-control-azure-rbac"></a>Návody dát spolupracovníkům přístup k LUIS pomocí Azure Active Directory (Azure AD) nebo řízení přístupu na základě role Azure (RBAC)?

Informace o tom, jak dát spolupracovníkům přístup, najdete v tématu [Azure Active Directory prostředky](luis-how-to-collaborate.md#azure-active-directory-resources)  a [Azure Active Directory uživatele klienta](luis-how-to-collaborate.md#azure-active-directory-tenant-user) .

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Koncový bod

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Obdržel (a) jsem stavový kód chyby HTTP 403. Jak ho mám opravit?

Stavové kódy chyb 403 a 429 získáte, když překročíte transakce za sekundu nebo transakce za měsíc pro vaši cenovou úroveň. Zvyšte svou cenovou úroveň nebo použijte Language Understanding [kontejnery](luis-container-howto.md).

Když použijete všechny tyto bezplatné dotazy na koncový bod 1000 nebo překročíte kvótu měsíčních transakcí vaší cenové úrovně, obdržíte kód stavu chyby HTTP 403.

Pokud chcete tuto chybu opravit, musíte [změnit svou cenovou úroveň](luis-how-to-azure-subscription.md#change-the-pricing-tier) na vyšší úroveň nebo [vytvořit nový prostředek](get-started-portal-deploy-app.md#create-the-endpoint-resource) a [přiřadit ho k aplikaci](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Mezi řešení této chyby patří:

* V [Azure Portal](https://portal.azure.com)v prostředku Language Understanding na **cenové úrovni správa prostředků – >** změňte cenovou úroveň na vyšší úroveň TPS. Pokud je váš prostředek už přiřazený k aplikaci Language Understanding, nemusíte dělat nic na Language Understandingovém portálu.
*  Pokud vaše využití přesáhne nejvyšší cenovou úroveň, přidejte další Language Understanding prostředky s nástrojem pro vyrovnávání zatížení před nimi. K tomu může pomáhat [kontejner Language Understanding](luis-container-howto.md) s Kubernetes nebo Docker Compose.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Obdržel (a) jsem stavový kód chyby HTTP 429. Jak ho mám opravit?

Stavové kódy chyb 403 a 429 získáte, když překročíte transakce za sekundu nebo transakce za měsíc pro vaši cenovou úroveň. Zvyšte svou cenovou úroveň nebo použijte Language Understanding [kontejnery](luis-container-howto.md).

Tento stavový kód se vrátí, když vaše transakce za sekundu překročí vaši cenovou úroveň.

Mezi řešení patří:

* Pokud nejste na nejvyšší úrovni, můžete [zvýšit svou cenovou úroveň](luis-how-to-azure-subscription.md#change-the-pricing-tier).
* Pokud vaše využití přesáhne nejvyšší cenovou úroveň, přidejte další Language Understanding prostředky s nástrojem pro vyrovnávání zatížení před nimi. K tomu může pomáhat [kontejner Language Understanding](luis-container-howto.md) s Kubernetes nebo Docker Compose.
* Pomocí [zásady opakování](/azure/architecture/best-practices/transient-faults#general-guidelines) , kterou sami implementujete, můžete své požadavky na klientskou aplikaci vymezit tak, že se zobrazí tento stavový kód.

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Dotaz na koncový bod vrátil neočekávané výsledky. Co bych měl/a dělat?

Neočekávané výsledky předpovědi dotazu jsou založené na stavu publikovaného modelu. Pokud chcete model opravit, možná budete muset model, vlak a publikování znovu změnit.

Oprava modelu začíná [aktivním učením](luis-how-to-review-endpoint-utterances.md).

Nedeterministické školení můžete odebrat tak, že aktualizujete [rozhraní API pro nastavení verze aplikace](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) , aby se mohla používat všechna školicí data.

Projděte si [osvědčené postupy](luis-concept-best-practices.md) pro další tipy.

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Proč aplikace LUIS přidá do dotazu mezery kolem slov nebo za ně?
LUIS [tokenizes](luis-glossary.md#token) utterance na základě [jazykové verze](luis-language-support.md#tokenization). Pro [extrakci dat](luis-concept-data-extraction.md#tokenized-entity-returned)jsou k dispozici jak původní hodnota, tak i hodnota s tokeny.

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Návody vytvořit a přiřadit klíč koncového bodu LUIS?
[Vytvořte v Azure klíč koncového bodu](luis-how-to-azure-subscription.md) pro vaši úroveň [služeb](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) . [Přiřaďte klíč](luis-how-to-azure-subscription.md) na stránce **[prostředky Azure](luis-how-to-azure-subscription.md)** . Pro tuto akci není k dispozici žádné odpovídající rozhraní API. Pak musíte požadavek HTTP změnit na koncový bod, aby [používal nový klíč koncového bodu](luis-how-to-azure-subscription.md).

### <a name="how-do-i-interpret-luis-scores"></a>Návody interpretovat skóre LUIS?
Váš systém by měl používat nejvyšší záměr bodování bez ohledu na jeho hodnotu. Například skóre nižší než 0,5 (méně než 50%) nemusí nutně znamenat, že LUIS má nízkou důvěru. Poskytování více školicích dat může přispět ke zvýšení [skóre](luis-concept-prediction-score.md) nejpravděpodobnějšího záměru.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Proč na řídicím panelu Moje aplikace nevidím svoje přístupy ke koncovému bodu?
Celkový počet přístupů koncového bodu na řídicím panelu vaší aplikace se pravidelně aktualizuje, ale metriky přidružené k vašemu klíči LUIS koncového bodu v Azure Portal se aktualizují častěji.

Pokud na řídicím panelu nevidíte aktualizované přístupy ke koncovému bodu, přihlaste se k Azure Portal a vyhledejte prostředek přidružený ke klíči koncového bodu LUIS a otevřete **metriky** pro výběr metriky **Celkový počet volání** . Pokud se klíč koncového bodu používá pro více než jednu aplikaci LUIS, metrika v Azure Portal zobrazuje agregovaný počet volání ze všech aplikací LUIS, které ji používají.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Je k dispozici příkaz prostředí PowerShell k dosažení kvóty koncového bodu?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

K zobrazení kvóty koncového bodu můžete použít příkaz prostředí PowerShell:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Moje aplikace LUIS fungovala včera, ale dnes jsem 403 chyb. Nezměnil (a) jsem aplikaci. Jak ho mám opravit?
Podle těchto [pokynů](#how-do-i-create-and-assign-a-luis-endpoint-key) vytvořte klíč koncového bodu Luis a přiřaďte ho k aplikaci. Pak je nutné změnit požadavek HTTP klientské aplikace na koncový bod tak, aby [používal nový klíč koncového bodu](luis-how-to-azure-subscription.md). Pokud jste vytvořili nový prostředek v jiné oblasti, změňte také oblast žádosti klienta HTTP.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Návody zabezpečený koncový bod LUIS?
Viz [zabezpečení koncového bodu](luis-how-to-azure-subscription.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Práce v rámci omezení LUIS

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Jaký je maximální počet záměrů a entit, které může aplikace LUIS podporovat?
Podívejte se na odkaz [hranice](luis-limits.md) .

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Chci vytvořit aplikaci LUIS s více než maximálním počtem záměrů. Co bych měl/a dělat?

Podívejte se [na osvědčené postupy pro záměry](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Chci v LUIS vytvořit aplikaci s více než maximálním počtem entit. Co bych měl/a dělat?

Zobrazit [osvědčené postupy pro entity](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Jaká jsou omezení počtu a velikosti seznamů frází?
Maximální délku [seznamu frází](./luis-concept-feature.md)najdete v odkazu na [hranice](luis-limits.md) .

### <a name="what-are-the-limits-on-example-utterances"></a>Jaká jsou omezení, například projevy?
Podívejte se na odkaz [hranice](luis-limits.md) .

## <a name="testing-and-training"></a>Testování a školení

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>V podokně dávkového testování se zobrazují nějaké chyby pro některé modely v aplikaci Jak můžu vyřešit tento problém?

Chyby označují, že mezi štítky a předpovědi z vašich modelů dochází k nejistému rozdílu. Chcete-li vyřešit tento problém, proveďte jednu nebo obě následující úlohy:
* Chcete-li pomoci LUIS zdokonalit diskriminaci mezi záměry, přidejte další popisky.
* Pokud chcete LUIS rychleji, přidejte funkce seznamu frází, které zavádějí slovníky specifické pro doménu.

Podívejte se na kurz [dávkového testování](./luis-how-to-batch-test.md) .

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Po exportu aplikace se znovu naimportuje do nové aplikace (s novým ID aplikace) se výsledky předpovědi LUIS liší. Proč k tomu dochází?

Podívejte se [na rozdíly předpovědi mezi kopiemi stejné aplikace](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Některé projevy po provedení změn v aplikaci přejdou na nesprávný záměr. Zdá se, že problém zmizí náhodně. Jak ho mám opravit?

Podívejte [se na téma výuka se všemi daty](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Publikování aplikace

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Jaké je ID tenanta v okně Přidat klíč do aplikace?
V Azure tenant představuje klienta nebo organizaci, která je přidružená ke službě. V Azure Portal v poli **ID adresáře** Najděte ID tenanta, a to tak, že vyberete **Azure Active Directory**  >  **Spravovat**  >  **vlastnosti**.

![ID tenanta v Azure Portal](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Proč jsou k naší aplikaci přiřazeny další klíče koncového bodu, než jsem přiřadil (a)?
Každá aplikace LUIS má v seznamu koncových bodů k dispozici modul pro vytváření/počáteční klíč a pohodlí. Tento klíč umožňuje jenom pár přístupů do koncového bodu, takže si můžete vyzkoušet LUIS.

Pokud vaše aplikace existovala ještě dřív, než LUIS byla všeobecně dostupná (GA), přiřadí se klíče koncových bodů LUIS ve vašem předplatném automaticky. To bylo provedeno, aby byla migrace GA jednodušší. Všechny nové klíče koncového bodu LUIS v Azure Portal _nejsou automaticky přiřazeny_ k Luis.

## <a name="key-management"></a>Správa klíčů

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Návody vědět, jakou klávesu potřebuji, jak ji získám a co s nimi mám dělat?

Další informace o rozdílech mezi klíčovým slovem pro tvorbu a klíčovým modulem runtime najdete [v tématu klíče koncových bodů pro vytváření a předpovědi dotazů v Luis](luis-how-to-azure-subscription.md) .

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Zobrazila se mi chyba při nedostatku kvóty. Jak ho mám opravit?

Další informace najdete v tématu opravení stavového kódu HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) a [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) .

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Potřebuji zpracovat další dotazy koncového bodu. Jak to mám udělat?

Další informace najdete v tématu opravení stavového kódu HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) a [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) .

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Vytvořili jsem klíč pro vytváření obsahu, ale nezobrazuje se na portálu LUIS. Co se stalo?

Vytváření klíčů je k dispozici na portálu LUIS po [migraci na prostředí pro tvorbu kódu](luis-migration-authoring.md).

## <a name="app-management"></a>Správa aplikací

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Návody stáhnout protokol uživatele projevy?
Ve výchozím nastavení vaše aplikace LUIS zaznamená projevy od uživatelů. Pokud si chcete stáhnout protokol projevy, který uživatelé odesílají do vaší aplikace LUIS, pokračujte na **Moje aplikace** a vyberte aplikaci. Na panelu nástrojů kontext vyberte **exportovat protokoly koncových bodů**. Protokol je formátovaný jako textový soubor s oddělovači (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Jak můžu zakázat protokolování projevy?
Protokolování uživatele projevy můžete vypnout nastavením `log=false` adresy URL koncového bodu, kterou klientská aplikace používá pro dotazování Luis. Vypnutí protokolování ale zakáže schopnost vaší aplikace LUIS navrhovat projevy nebo zdokonalovat výkon, který je založený na [aktivním učení](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Pokud jste nastavili `log=false` ochranu proti soukromí dat, nemůžete stáhnout záznam o uživatelích projevy z Luis ani použít tyto projevy k vylepšení vaší aplikace.

Protokolování je jediné úložiště projevy.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Proč nechci zaprotokolovat všechny projevyy koncového bodu?
Pokud používáte protokol pro analýzu předpovědi, Zachyťte test projevy do protokolu.

## <a name="data-management"></a>Správa dat

### <a name="can-i-delete-data-from-luis"></a>Můžu odstranit data z LUIS?

* Můžete vždycky odstranit příklad projevy, který se používá pro školení LUIS. Pokud odstraníte příklad utterance z aplikace LUIS, odebere se z webové služby LUIS a nebude k dispozici pro export.
* Projevy je možné odstranit ze seznamu uživatelských projevy, který LUIS navrhuje na stránce **zkontrolovat koncový bod pro projevy** . Odstraněním projevy z tohoto seznamu zabráníte jejich navržení, ale neodstraníte je z protokolů.
* Pokud odstraníte účet, ododstraňují se všechny aplikace spolu s příklady projevy a protokolů. Data se uchovávají na serverech po dobu 60 dnů, než se trvale odstraní.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Jak Microsoft spravuje data, která se odesílají do LUIS?

[Centrum zabezpečení](https://www.microsoft.com/trustcenter) vysvětluje naše závazky a možnosti pro správu a přístup k datům ve službách Azure.

## <a name="language-and-translation-support"></a>Podpora jazyků a překladů

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Mám aplikaci v jednom jazyce a chcete vytvořit paralelní aplikaci v jiném jazyce. Jaký je nejjednodušší způsob, jak to udělat?
1. Exportujte aplikaci.
2. Přeloží označený projevy v souboru JSON exportované aplikace do cílového jazyka.
3. Možná budete muset změnit názvy záměrů a entit nebo je nechat v takovém případě.
4. Nakonec importujte aplikaci tak, aby měla aplikaci LUIS v cílovém jazyce.

## <a name="app-notification"></a>Oznámení aplikace

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Proč se mi zobrazil e-mail s oznámením, že mám skoro nekvótu?
Váš kód pro vytváření/počáteční klíč je povolený jenom 1000 dotazování koncového bodu na měsíc. Vytvořte klíč koncového bodu LUIS (bezplatný nebo placený) a použijte tento klíč při vytváření dotazů na koncový bod. Pokud provádíte dotazy koncového bodu z robota nebo jiné klientské aplikace, je třeba změnit klíč koncového bodu LUIS.

## <a name="bots"></a>Roboti

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>LUIS robot nepracuje. Co mám udělat?

Prvním problémem je izolovat, jestli problém souvisí s LUIS nebo nastane mimo middleware LUIS.

#### <a name="resolve-issue-in-luis"></a>Řešení potíží v LUIS
Předejte stejný utterance do LUIS z [koncového bodu Luis](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint). Pokud se zobrazí chyba, vyřešte problém v LUIS, dokud nebude chyba nadále vrácena. Mezi běžné chyby patří:

* `Out of call volume quota. Quota will be replenished in <time>.` – Tento problém znamená, že buď potřebujete změnit z klíčového obsahu na [klíč koncového bodu](luis-how-to-azure-subscription.md) , nebo potřebujete změnit [úrovně služeb](luis-how-to-azure-subscription.md#change-the-pricing-tier).

#### <a name="resolve-issue-in-azure-bot-service"></a>Řešení potíží v Azure Bot Service

Pokud používáte Azure Bot Service a problém je, že se **test ve webovém chatu** vrátí `Sorry, my bot code is having an issue` , zkontrolujte protokoly:

1. V Azure Portal pro robota v části **Správa robota** vyberte **Build (sestavit**).
1. Otevřete Editor kódu online.
1. V horním, modrém navigačním panelu vyberte název robota (druhá položka napravo).
1. V rozevíracím seznamu výsledek vyberte možnost **otevřít konzolu Kudu**.
1. Vyberte **soubory protokolu** a pak vyberte **aplikace**. Zkontrolujte všechny soubory protokolu. Pokud se ve složce aplikace nezobrazuje chyba, zkontrolujte všechny soubory protokolu v **souboru** protokolu.
1. Nezapomeňte projekt znovu sestavit, pokud používáte zkompilovaný jazyk, jako je C#.

> [!Tip]
> Konzolu nástroje může nainstalovat i balíčky.

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Vyřešte problém při ladění na místním počítači s rozhraním bot Framework.

Další informace o místním ladění robota najdete v tématu věnovaném [ladění robota](/azure/bot-service/bot-service-debug-bot).

## <a name="integrating-luis"></a>Integrace LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Kde je moje aplikace LUIS vytvořená během procesu předplatného služby Azure Web App bot?
Pokud vyberete šablonu LUIS a v podokně šablona vyberete tlačítko **Vybrat** , podokno vlevo se změní tak, aby obsahovalo typ šablony, a zobrazí dotaz, v jaké oblasti se má vytvořit šablona Luis. Proces robota webové aplikace nevytváří předplatné LUIS, i když.

![Oblast bot webové aplikace LUIS Template](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Jaké oblasti LUIS podporují pro řeči pro robot Framework?
Dočistění [řeči](/bot-framework/bot-service-manage-speech-priming) je podporované jenom pro aplikace Luis v centrální instanci (US).

## <a name="api-programming-strategies"></a>Strategie programování API

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Návody programově získat oblast LUIS prostředku?

Pomocí ukázky LUIS můžete [najít oblast](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) programově pomocí C# nebo Node.Js.

## <a name="luis-service"></a>Služba LUIS

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Je Language Understanding (LUIS) k dispozici místně nebo v privátním cloudu?

Ano, [kontejner](luis-container-howto.md) Luis můžete použít pro tyto scénáře, pokud máte nezbytné připojení k využití měřiče.

## <a name="migrating-to-the-next-version"></a>Migrace na další verzi

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Návody migrujete na verzi Preview V3 API?

Viz [Průvodce migrací rozhraní API v2 na verzi v3 pro aplikace Luis](luis-migration-api-v3.md) .

## <a name="build-2019-conference-announcements"></a>Oznámení o konferenci Build 2019

Následující funkce byly vydány na konferenci Build 2019:

* [Verze Preview Průvodce migrací rozhraní V3 API](luis-migration-api-v3.md)
* [Vylepšený řídicí panel analýzy](luis-how-to-use-dashboard.md)
* [Vylepšené předem připravené domény](luis-reference-prebuilt-domains.md)
* [Dynamické seznam entit](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Externí entity](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Videa:

* [Jak používat konverzační službu Azure ke škálování vašeho podnikání pro novou generaci](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Další kroky

Další informace o LUIS najdete v následujících zdrojích informací:
* [Stack Overflow dotazy označené pomocí LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Microsoft Q&Stránka s otázkou pro MSDN Language Understanding Intelligent Services (LUIS)](/answers/topics/azure-language-understanding.html)