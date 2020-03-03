---
title: Řešení potíží – přizpůsobení
description: Tento článek obsahuje odpovědi na nejčastější dotazy k řešení potíží týkajících se přizpůsobení.
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: 39db920b663ace12f2fc6ca35f9082a791da1541
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228033"
---
# <a name="personalizer-troubleshooting"></a>Řešení potíží s přizpůsobením

Tento článek obsahuje odpovědi na nejčastější dotazy k řešení potíží týkajících se přizpůsobení.

## <a name="transaction-errors"></a>Chyby transakce

<details>
<summary><b>Od služby se zobrazí odpověď HTTP 429 (příliš mnoho požadavků). Co mohu udělat?</b></summary>

**Odpověď**: Pokud jste při vytváření instance přizpůsobené úrovně ceny vybrali zdarma, existuje omezení kvóty pro počet povolených požadavků na řazení. Zkontrolujte míru volání rozhraní API pro rozhraní API pro řazení (v podokně metriky v Azure Portal pro prostředek přizpůsobeného) a upravte cenovou úroveň (v podokně s cenovou úrovní), pokud se očekává, že se váš volání zvýší nad prahovou hodnotou zvolené cenové úrovně.

</details>

<details>
<summary><b>Zobrazuje se 5xx chyba na rozhraních API pro řazení a měnu. Co mám dělat?</b></summary>

**Odpověď**: tyto problémy by měly být transparentní. Pokud budou pokračovat, obraťte se na podporu výběrem **nové žádosti o podporu** v části **Podpora a řešení potíží** v Azure Portal pro váš prostředek pro přizpůsobení.

</details>

## <a name="learning-loop"></a>Výuková smyčka

<details>
<summary>
<b>Výuková smyčka nedosahuje 100% systému bez přizpůsobeného přizpůsobování. Návody opravit?</b></summary>

**Odpověď**: důvody, které nedosáhnete vašeho cíle, pomocí výukového cyklu:
* S voláním rozhraní API řazení není dost funkcí odeslaných.
* Chyby v odeslaných funkcích – například posílání neagregovaných dat funkcí, jako například časová razítka rozhraní API pro řazení
* Chyby se zpracováním smyčky – například neodesílají data o nepracovních odměnuch do API pro události

Chcete-li opravit, je nutné změnit zpracování buď změnou funkcí poslaných do smyčky, nebo ověřit, že je tato měna správného vyhodnocení kvality odpovědi pořadí.

</details>

<details>
<summary>
<b>Výuková smyčka se zřejmě neučí. Návody opravit?</b></summary>

**Odpověď**: výuková smyčka potřebuje několik tisíc hovorů na nevýhodu, než vyvolají prioritní volání.

Pokud si nejste jistí, jak se vaše výuková smyčka právě chová, spusťte [testování offline](concepts-offline-evaluation.md)a použijte opravené zásady učení.

</details>

<details>
<summary><b>Stále Získávám výsledky pořadí se všemi stejnými pravděpodobnostmi pro všechny položky. Návody víte, že se přizpůsobuje učení?</b></summary>

**Odpověď**: přizpůsobování vrátí stejné pravděpodobnosti ve výsledku rozhraní API řazení, když se právě spustí a má _prázdný_ model, nebo když resetujete smyčku přizpůsobeného, a váš model se pořád nachází v období **Frekvence aktualizace vašeho modelu** .

Po zahájení nového období aktualizace se použije aktualizovaný model a dojde ke změně pravděpodobností.

</details>

<details>
<summary><b>Výuková smyčka se naučila, ale zdá se, že už se neučí, a kvalita výsledků je Nedobrá. Co mám dělat?</b></summary>

**Odpověď**:
* Ujistěte se, že jste dokončili a použili jedno vyhodnocení v Azure Portal pro tento prostředek přidaných prostředků (výuková smyčka).
* Zajistěte, aby byly všechny ceny odesílány prostřednictvím rozhraní API pro odměnu a zpracovány.

</details>


<details>
<summary><b>Návody víte, že výuková smyčka se pravidelně aktualizuje a používá se k vyhodnocení mých dat?</b></summary>

**Odpověď**: můžete najít čas poslední aktualizace modelu na stránce **Nastavení modelu a učení** Azure Portal. Pokud se zobrazí staré časové razítko, je pravděpodobně způsobeno tím, že neposíláte volání pořadí a odměňování. Pokud služba neobsahuje žádná příchozí data, neaktualizuje výuku. Pokud vidíte, že výuková smyčka není často aktualizována, můžete upravit **Četnost aktualizace modelu**smyčky.

</details>

## <a name="offline-evaluations"></a>Offline vyhodnocení

<details>
<summary><b>Důležitost funkcí pro offline vyhodnocení vrátí dlouhý seznam se stovkami nebo tisíci položek. Co se přihodilo?</b></summary>

**Odpověď**: obvykle se jedná o časová razítka, ID uživatele nebo některé další jemně odstupňované funkce, které jsou odesílány v.

</details>

<details>
<summary><b>Vytvořil (a) jsem se offline zkušební období a skoro okamžitě uspělo. Proč je to? Nevidím žádné výsledky?</b></summary>

**Odpověď**: testování offline využívá z událostí v tomto časovém období proučená data modelu. Pokud jste neodeslali žádná data v časovém období mezi počátečním a koncovým časem vyhodnocení, bude dokončena bez jakýchkoli výsledků. Odešlete nové online vyhodnocení tak, že vyberete časový rozsah s událostmi, které jste věděli, že jste ho přizpůsobili.

</details>


## <a name="learning-policy"></a>Zásady učení

<details>
<summary><b>Návody Importovat zásady učení?</b></summary>

**Odpověď**: Přečtěte si další informace o [konceptech zásad učení](concept-active-learning.md#understand-learning-policy-settings) a [o tom, jak používat](how-to-manage-model.md) nové zásady učení. Pokud nechcete vybrat zásady učení, můžete použít [testování v režimu offline](how-to-offline-evaluation.md) k návrhu zásad učení na základě aktuálních událostí.

</details>

## <a name="security"></a>Zabezpečení

<details>
<summary><b>Klíč rozhraní API pro moji smyčku byl zneužit. Co mohu udělat?</b></summary>

**Odpověď**: po záměně klientů na použití jiného klíče můžete znovu vygenerovat jeden klíč. Použití dvou klíčů vám umožní s opožděným šířením klíče bez nutnosti jakéhokoli výpadku. Tento postup doporučujeme provést v pravidelných cyklech jako bezpečnostní opatření.

</details>

## <a name="next-steps"></a>Další kroky

[Konfigurace frekvence aktualizace modelu](how-to-settings.md#model-update-frequency)