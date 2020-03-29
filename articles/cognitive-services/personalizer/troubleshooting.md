---
title: Poradce při potížích – personalizátor
description: Tento článek obsahuje odpovědi na často kladené otázky týkající se řešení potíží týkající se personalisty.
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: 904953f028eb31afe42cf477ac05be43e8b72a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336033"
---
# <a name="personalizer-troubleshooting"></a>Poradce při potížích s personalizátky

Tento článek obsahuje odpovědi na často kladené otázky týkající se řešení potíží týkající se personalisty.

## <a name="transaction-errors"></a>Chyby transakcí

<details>
<summary><b>Dostanu odpověď HTTP 429 (Příliš mnoho požadavků) ze služby. Co mám dělat?</b></summary>

**Odpověď**: Pokud jste při vytváření instance Personalistu vybrali bezplatnou cenovou úroveň, existuje maximální kvóta počtu povolených požadavků pořadí. Zkontrolujte rychlost volání rozhraní API pro rozhraní API hodnocení (v podokně Metriky na portálu Azure pro prostředek personalisty) a upravte cenovou úroveň (v podokně Cenová úroveň), pokud se očekává, že se objem volání zvýší nad prahovou hodnotu pro vybranou cenovou úroveň.

</details>

<details>
<summary><b>Dostávám chybu 5xx na hodnosti nebo odměny API. Co mám dělat?</b></summary>

**Odpověď**: Tyto otázky by měly být transparentní. Pokud budou pokračovat, obraťte se na podporu výběrem **nové žádosti o podporu** v části Podpora + řešení **potíží,** na portálu Azure pro prostředek personalika.

</details>

## <a name="learning-loop"></a>Výukové smyčky

<details>
<summary>
<b>Učení smyčka nedosahuje 100% shoda se systémem bez Personalista. Jak to mohu opravit?</b></summary>

**Odpověď**: Důvody, proč nedosáhnete svého cíle s učení smyčky:
* Nedostatek funkcí odeslaných pomocí volání Rozhraní API hodnocení
* Chyby v odeslaných funkcích – například odesílání neagregovaných dat funkcí, jako jsou časová razítka do rozhraní Rank API
* Chyby se zpracováním smyčky - například neodesílání údajů o odměnách do Bonus API pro události

Chcete-li opravit, musíte změnit zpracování buď změnou funkcí odeslaných do smyčky, nebo se ujistěte, že odměna je správné hodnocení kvality odpovědi hodnosti.

</details>

<details>
<summary>
<b>Učení smyčky Nezdá se, že učit. Jak to mohu opravit?</b></summary>

**Odpověď**: Vzdělávací smyčka potřebuje několik tisíc volání odměny, než rank hovory efektivně upřednostňují.

Pokud si nejste jisti, jak se vaše studijní smyčka aktuálně chová, spusťte [offline hodnocení](concepts-offline-evaluation.md)a použijte opravené zásady učení.

</details>

<details>
<summary><b>Dostávám výsledky hodnosti se všemi stejnými pravděpodobnostmi pro všechny položky. Jak poznám, že se personalizátor učí?</b></summary>

**Odpověď**: Personalizáta vrátí stejné pravděpodobnosti ve výsledku rozhraní API pořadí, když právě začal a má _prázdný_ model, nebo při resetování personalizační smyčky a váš model je stále v rámci období **frekvence aktualizace modelu.**

Když začíná nové období aktualizace, použije se aktualizovaný model a pravděpodobnosti se změní.

</details>

<details>
<summary><b>Učení smyčka byla učení, ale zdá se, že se učit už, a kvalita výsledků Rank není tak dobrý. Co mám dělat?</b></summary>

**Odpověď**:
* Ujistěte se, že jste dokončili a použili jedno vyhodnocení na webu Azure Portal pro tento prostředek personalikár (výukové smyčky).
* Ujistěte se, že všechny odměny jsou odesílány prostřednictvím rozhraní Bonus API a zpracovány.

</details>


<details>
<summary><b>Jak poznám, že se učící smyčka se pravidelně aktualizuje a používá se ke získávání mých dat?</b></summary>

**Odpověď**: Čas, kdy byl model naposledy aktualizován, najdete na stránce **Model a nastavení učení** na webu Azure Portal. Pokud vidíte staré časové razítko, je to pravděpodobně proto, že neodesíláte volání hodnosti a odměny. Pokud služba nemá žádná příchozí data, neaktualizuje učení. Pokud se zobrazí učení smyčky není aktualizace dostatečně často, můžete upravit smyčky **Model Update frekvence**.

</details>

## <a name="offline-evaluations"></a>Offline vyhodnocení

<details>
<summary><b>Důležitost funkce offline hodnocení vrátí dlouhý seznam se stovkami nebo tisíci položek. Co se přihodilo?</b></summary>

**Odpověď**: To je obvykle způsobeno časová razítka, ID uživatele nebo některé jiné jemně odstupňované funkce odeslané.

</details>

<details>
<summary><b>Vytvořil jsem offline hodnocení a to se podařilo téměř okamžitě. Proč? Nevidím žádné výsledky?</b></summary>

**Odpověď**: Offline vyhodnocení používá trénovaná data modelu z událostí v tomto časovém období. Pokud jste neodeslali žádná data v časovém období mezi počátečním a koncovým časem vyhodnocení, bude dokončena bez jakýchkoli výsledků. Odešlete nové offline hodnocení výběrem časového rozsahu s událostmi, o kterých víte, že byly odeslány personalistovi.

</details>


## <a name="learning-policy"></a>Zásady učení

<details>
<summary><b>Jak importuji zásady učení?</b></summary>

**Odpověď**: Přečtěte si další informace o [konceptech zásad učení](concept-active-learning.md#understand-learning-policy-settings) a [o tom, jak použít](how-to-manage-model.md) nové zásady učení. Pokud nechcete vybrat zásady učení, můžete pomocí [offline hodnocení](how-to-offline-evaluation.md) navrhnout zásady učení na základě aktuálních událostí.

</details>

## <a name="security"></a>Zabezpečení

<details>
<summary><b>Klíč rozhraní API pro moji smyčku byl prozrazen. Co mám dělat?</b></summary>

**Odpověď**: Po výměně klientů můžete znovu vygenerovat jeden klíč za použití druhého klíče. S dvěma klíči umožňuje šířit klíč v líné způsobem bez nutnosti mít žádné prostoje. Doporučujeme to v pravidelném cyklu jako bezpečnostní opatření.

</details>

## <a name="next-steps"></a>Další kroky

[Konfigurace četnosti aktualizací modelu](how-to-settings.md#model-update-frequency)