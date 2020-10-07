---
title: Řešení potíží – přizpůsobení
description: Tento článek obsahuje odpovědi na nejčastější dotazy k řešení potíží týkajících se přizpůsobení.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.openlocfilehash: b5f7ed1f2ded8f6ec0320d417b59bab016d75028
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777271"
---
# <a name="personalizer-troubleshooting"></a>Řešení potíží s přizpůsobením

Tento článek obsahuje odpovědi na nejčastější dotazy k řešení potíží týkajících se přizpůsobení.

## <a name="configuration-issues"></a>Problémy s konfigurací

### <a name="i-changed-a-configuration-setting-and-now-my-loop-isnt-performing-at-the-same-learning-level-what-happened"></a>Změnil (a) jsem nastavení konfigurace a teď moje smyčka neprovádí na stejné úrovni učení. Co se stalo?

Některá nastavení konfigurace [resetují váš model](how-to-settings.md#settings-that-include-resetting-the-model). Změny konfigurace by měly být pečlivě plánovány.

### <a name="when-configuring-personalizer-with-the-api-i-received-an-error-what-happened"></a>Při konfiguraci přizpůsobeného objektu pomocí rozhraní API jsem zobrazila chyba. Co se stalo?

Pokud ke konfiguraci služby a změně chování výuky použijete jednu žádost API, zobrazí se chyba. Musíte udělat dvě samostatná volání rozhraní API: nejdřív, pokud chcete službu nakonfigurovat, a pak přepnout chování učení.

## <a name="transaction-errors"></a>Chyby transakce

### <a name="i-get-an-http-429-too-many-requests-response-from-the-service-what-can-i-do"></a>Od služby se zobrazí odpověď HTTP 429 (příliš mnoho požadavků). Co mám udělat?

Pokud jste při vytváření instance přizpůsobené úrovně ceny vybrali zdarma, existuje omezení kvóty pro počet povolených požadavků na řazení. Zkontrolujte míru volání rozhraní API pro rozhraní API pro řazení (v podokně metriky v Azure Portal pro prostředek přizpůsobeného) a upravte cenovou úroveň (v podokně s cenovou úrovní), pokud se očekává, že se váš volání zvýší nad prahovou hodnotou zvolené cenové úrovně.

### <a name="im-getting-a-5xx-error-on-rank-or-reward-apis-what-should-i-do"></a>Zobrazuje se 5xx chyba na rozhraních API pro řazení a měnu. Co bych měl/a dělat?

Tyto problémy by měly být transparentní. Pokud budou pokračovat, obraťte se na podporu výběrem **nové žádosti o podporu** v části **Podpora a řešení potíží** v Azure Portal pro váš prostředek pro přizpůsobení.

## <a name="learning-loop"></a>Výuková smyčka

### <a name="the-learning-loop-doesnt-attain-a-100-match-to-the-system-without-personalizer-how-do-i-fix-this"></a>Výuková smyčka nedosahuje 100% systému bez přizpůsobeného přizpůsobování. Návody opravit?

Důvody, které nedosahují vašeho cíle, pomocí výukového cyklu:
* S voláním rozhraní API řazení není dost funkcí odeslaných.
* Chyby v odeslaných funkcích – například posílání neagregovaných dat funkcí, jako například časová razítka rozhraní API pro řazení
* Chyby se zpracováním smyčky – například neodesílají data o nepracovních odměnuch do API pro události

Chcete-li opravit, je nutné změnit zpracování buď změnou funkcí poslaných do smyčky, nebo ověřit, že je tato měna správného vyhodnocení kvality odpovědi pořadí.

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Výuková smyčka se zřejmě neučí. Návody opravit?

Výuková smyčka potřebuje několik tisíc volání odměna před tím, než volání pořadí zařadí přednost.

Pokud si nejste jistí, jak se vaše výuková smyčka právě chová, spusťte [testování offline](concepts-offline-evaluation.md)a použijte opravené zásady učení.

### <a name="i-keep-getting-rank-results-with-all-the-same-probabilities-for-all-items-how-do-i-know-personalizer-is-learning"></a>Stále Získávám výsledky pořadí se všemi stejnými pravděpodobnostmi pro všechny položky. Návody víte, že se přizpůsobuje učení?

Přizpůsobování vrátí stejné pravděpodobnosti ve výsledku rozhraní API řazení, pokud se právě spustí a má _prázdný_ model, nebo když resetujete smyčku přizpůsobeného, a váš model se pořád nachází v období **Frekvence aktualizace vašeho modelu** .

Po zahájení nového období aktualizace se použije aktualizovaný model a dojde ke změně pravděpodobností.

### <a name="the-learning-loop-was-learning-but-seems-to-not-learn-anymore-and-the-quality-of-the-rank-results-isnt-that-good-what-should-i-do"></a>Výuková smyčka se naučila, ale zdá se, že už se neučí, a kvalita výsledků je Nedobrá. Co bych měl/a dělat?

* Ujistěte se, že jste dokončili a použili jedno vyhodnocení v Azure Portal pro tento prostředek přidaných prostředků (výuková smyčka).
* Zajistěte, aby byly všechny ceny odesílány prostřednictvím rozhraní API pro odměnu a zpracovány.

### <a name="how-do-i-know-that-the-learning-loop-is-getting-updated-regularly-and-is-used-to-score-my-data"></a>Návody víte, že výuková smyčka se pravidelně aktualizuje a používá se k vyhodnocení mých dat?

Čas poslední aktualizace modelu můžete najít na stránce **Nastavení modelu a učení** Azure Portal. Pokud se zobrazí staré časové razítko, je pravděpodobně způsobeno tím, že neposíláte volání pořadí a odměňování. Pokud služba neobsahuje žádná příchozí data, neaktualizuje výuku. Pokud vidíte, že výuková smyčka není často aktualizována, můžete upravit **Četnost aktualizace modelu**smyčky.

## <a name="offline-evaluations"></a>Offline vyhodnocení

### <a name="an-offline-evaluations-feature-importance-returns-a-long-list-with-hundreds-or-thousands-of-items-what-happened"></a>Důležitost funkcí pro offline vyhodnocení vrátí dlouhý seznam se stovkami nebo tisíci položek. Co se stalo?

Obvykle je to způsobeno časovými razítky, ID uživatele nebo některými dalšími dodanými funkcemi, které jsou odesílány v.

### <a name="i-created-an-offline-evaluation-and-it-succeeded-almost-instantly-why-is-that-i-dont-see-any-results"></a>Vytvořil (a) jsem se offline zkušební období a skoro okamžitě uspělo. Proč tomu tak je? Nevidím žádné výsledky?

Testování v režimu offline používá z událostí v daném časovém období data z vyškolených modelů. Pokud jste neodeslali žádná data v časovém období mezi počátečním a koncovým časem vyhodnocení, bude dokončena bez jakýchkoli výsledků. Odešlete nové online vyhodnocení tak, že vyberete časový rozsah s událostmi, které jste věděli, že jste ho přizpůsobili.

## <a name="learning-policy"></a>Zásady učení

### <a name="how-do-i-import-a-learning-policy"></a>Návody Importovat zásady učení?

Přečtěte si další informace o [konceptech zásad učení](concept-active-learning.md#understand-learning-policy-settings) a [o tom, jak používat](how-to-manage-model.md) nové zásady učení. Pokud nechcete vybrat zásady učení, můžete použít [testování v režimu offline](how-to-offline-evaluation.md) k návrhu zásad učení na základě aktuálních událostí.


## <a name="security"></a>Zabezpečení

### <a name="the-api-key-for-my-loop-has-been-compromised-what-can-i-do"></a>Klíč rozhraní API pro moji smyčku byl zneužit. Co mám udělat?

Po záměně klientů na použití jiného klíče můžete znovu vygenerovat jeden klíč. Použití dvou klíčů vám umožní s opožděným šířením klíče bez nutnosti jakéhokoli výpadku. Tento postup doporučujeme provést v pravidelných cyklech jako bezpečnostní opatření.


## <a name="next-steps"></a>Další kroky

[Konfigurace frekvence aktualizace modelu](how-to-settings.md#model-update-frequency)