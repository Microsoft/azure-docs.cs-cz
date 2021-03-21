---
title: Vlastní příkazy koncepty a definice – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte o konceptech a definicích aplikací pro vlastní příkazy.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 98510132b2341736664dfafa52e9567df95652be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94561093"
---
# <a name="custom-commands-concepts-and-definitions"></a>Koncepty a definice vlastních příkazů

Tento článek slouží jako referenční informace pro koncepty a definice aplikací pro vlastní příkazy.

## <a name="commands-configuration"></a>Konfigurace příkazů
Příkazy jsou základní stavební bloky aplikace s vlastním příkazy. Příkaz je sada konfigurací potřebných k dokončení konkrétní úlohy definované uživatelem.

### <a name="example-sentences"></a>Příklady vět
Příkladem projevy jsou množiny příkladů, které může uživatel spustit konkrétní příkaz. Musíte poskytnout jenom ukázku projevy a ne vyčerpávající seznam. 

### <a name="parameters"></a>Parametry
Parametry jsou informace vyžadované příkazy k dokončení úkolu. Ve složitých scénářích lze také pomocí parametrů definovat podmínky, které spouštějí vlastní akce.

### <a name="completion-rules"></a>Pravidla dokončení
Pravidla dokončování představují řadu pravidel, která se mají provést, když je příkaz připravený k splnění, například když jsou splněné všechny podmínky pravidel.

### <a name="interaction-rules"></a>Pravidla interakce
Pravidla interakce jsou další pravidla, která budou zpracovávat konkrétnější nebo složitou situaci. Můžete přidat další ověření nebo konfigurovat pokročilé funkce, jako jsou potvrzení nebo opravy v jednom kroku. Můžete také vytvořit vlastní pravidla interakce.

## <a name="parameters-configuration"></a>Konfigurace parametrů

Parametry jsou informace vyžadované příkazy k dokončení úkolu. Ve složitých scénářích lze také pomocí parametrů definovat podmínky, které spouštějí vlastní akce.

### <a name="name"></a>Name
Parametr je identifikován vlastností název. Parametru byste měli vždy zadat popisný název. Parametr může být uveden v různých oddílech, například při vytváření podmínek, odezvy řeči nebo jiných akcí.
 
### <a name="isglobal"></a>IsGlobal
Toto zaškrtávací políčko označuje, zda je obor tohoto parametru sdílen ve všech příkazech aplikace. Pokud je parametr globální, jeho hodnota může být potenciálně poskytnuta z libovolného oboru příkazu. Po přiřazení hodnoty je možné ji vyřadit z libovolného z příkazů. 

### <a name="required"></a>Vyžadováno
Toto zaškrtávací políčko označuje, zda je pro tento parametr požadována hodnota pro splnění nebo dokončení příkazu. Je nutné nakonfigurovat odpovědi, aby vyzvat uživatele k zadání hodnoty, pokud je parametr označen jako povinný.

Všimněte si, že pokud jste nakonfigurovali **povinný parametr** tak, aby měl **výchozí hodnotu**, systém bude stále explicitně vyzvat k zadání hodnoty parametru.

### <a name="type"></a>Typ
Vlastní příkazy podporují následující typy parametrů:

* DateTime
* Geografie
* Číslo
* Řetězec

Všechny tyto typy parametrů s výjimkou geografického nastavení podporují konfiguraci výchozí hodnoty, kterou můžete nakonfigurovat z portálu.

### <a name="configuration"></a>Konfigurace
Konfigurace je vlastnost parametru definovaná pouze pro řetězec typu. Podporovány jsou následující hodnoty:

* **Žádné**.
* **Přijmout úplný vstup**: Pokud je povoleno, parametr akceptuje všechny vstupní utterance. Tato možnost je užitečná v případě, že uživatel potřebuje parametr s úplným utterance. Příkladem jsou poštovní adresy.
* **Přijměte předdefinované vstupní hodnoty z externího katalogu**: Tato hodnota se používá ke konfiguraci parametru, který může předpokládat širokou škálu hodnot. Příkladem je prodej v katalogu. V tomto případě je katalog hostovaný na externím webovém koncovém bodu a dá se nakonfigurovat nezávisle.
* **Přijměte předdefinované vstupní hodnoty z interního katalogu**: Tato hodnota se používá ke konfiguraci parametru, který může předpokládat několik hodnot. V takovém případě musí být hodnoty nakonfigurovány v studiu řeči.


### <a name="validation"></a>Ověřování
Ověření jsou určena pro určité typy parametrů, které umožňují konfigurovat omezení pro hodnotu parametru. V současné době vlastní příkazy podporují ověřování na následujících typech parametrů:

* DateTime
* Číslo

## <a name="rules-configuration"></a>Konfigurace pravidel
Pravidlo ve vlastních příkazech je definováno sadou *podmínek* , které při splnění splní sadu *akcí*. Pravidla také umožňují nakonfigurovat *stav po spuštění* a *očekávání* pro další tah.

### <a name="types"></a>Typy
Vlastní příkazy podporují následující kategorie pravidel:

* **Pravidla dokončování**: Tato pravidla se musí provést při splnění příkazu. Všechna pravidla konfigurovaná v této části, pro které jsou splněné podmínky, se spustí. 
* **Pravidla interakce**: Tato pravidla se dají použít ke konfiguraci dalších vlastních ověření, potvrzení a krokového opravy nebo k provedení jakékoli jiné vlastní logiky dialogových oken. Pravidla interakce jsou vyhodnocována při každém zapínání zpracování a lze je použít k aktivaci pravidel dokončení.

Různé akce nakonfigurované jako součást pravidla jsou spouštěny v pořadí, ve kterém se zobrazují na portálu pro vytváření.

### <a name="conditions"></a>Podmínky
Podmínky jsou požadavky, které musí být splněny, aby bylo pravidlo spuštěno. Podmínky pravidel mohou být následujícího typu:

* **Hodnota parametru Equals**: hodnota konfigurovaného parametru se rovná konkrétní hodnotě.
* **Žádná hodnota parametru**: nakonfigurované parametry by neměly mít žádnou hodnotu.
* **Požadované parametry**: nakonfigurovaný parametr má hodnotu.
* **Všechny požadované parametry**: všechny parametry, které byly označeny jako povinné, mají hodnotu.
* **Aktualizované parametry**: jedna nebo více hodnot parametrů bylo aktualizováno v důsledku zpracování aktuálního vstupu (utterance nebo aktivita).
* **Potvrzení bylo úspěšné**: vstupní utterance nebo aktivita byla úspěšné potvrzení (Ano).
* **Potvrzení bylo odepřeno**: vstupní utterance nebo aktivita nebyla úspěšné (ne).
* **Předchozí příkaz se musí aktualizovat**: Tato podmínka se v instancích používá, když chcete zachytit souběžné potvrzení spolu s aktualizací. Na pozadí je tato podmínka nakonfigurovaná pro případ, že se modul dialogového okna detekuje negativní potvrzení, kde je záměr stejný jako u předchozího tahu, a uživatel odpověděl s aktualizací.

### <a name="actions"></a>Akce
* **Odeslat odezvu na řeč**: odešlete odpověď na řeč zpátky klientovi.
* **Hodnota parametru aktualizace**: Aktualizujte hodnotu parametru příkazu na zadanou hodnotu.
* **Vymazat parametr hodnota**: Vymažte hodnotu parametru příkazu.
* **Zavolat webový koncový bod**: provede volání do webového koncového bodu.
* **Odeslat aktivitu klientovi**: odeslání vlastní aktivity klientovi.

### <a name="expectations"></a>Očekávání
Očekávání se používají ke konfiguraci pomocných parametrů pro zpracování dalšího vstupu uživatele. Jsou podporovány následující typy:

* **Očekává se potvrzení od uživatele**: tato očekávaná hodnota určuje, že aplikace očekává potvrzení (ano/ne) pro další uživatelský vstup.
* **Byl očekáván vstup z parametru od uživatele**: Toto očekávání Určuje jeden nebo více parametrů příkazu, které aplikace od vstupu uživatele očekává.

### <a name="post-execution-state"></a>Stav po spuštění
Stav po spuštění je stav dialogového okna po zpracování aktuálního vstupu (utterance nebo aktivita). Jedná se o následující typy:

* **Zachovat aktuální stav**: zachovat pouze aktuální stav.
* **Dokončete příkaz**: dokončete příkaz a nebudou zpracována žádná další pravidla příkazu.
* **Spustit pravidla dokončení**: proveďte všechna platná pravidla dokončování.
* **Počkat na vstup uživatele**: Počkejte na zadání dalšího uživatele.



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zobrazit ukázky na GitHubu](https://aka.ms/speech/cc-samples)
