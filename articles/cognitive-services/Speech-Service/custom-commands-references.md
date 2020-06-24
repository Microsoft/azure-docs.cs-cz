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
ms.openlocfilehash: 5a5e3590e0e184633760f080dfd3402ed75bd48e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307516"
---
# <a name="custom-commands-concepts-and-definitions"></a>Koncepty a definice vlastních příkazů

Tento článek slouží jako referenční informace pro koncepty a definice aplikací pro vlastní příkazy.

## <a name="commands-configuration"></a>Konfigurace příkazů
Příkazy jsou základní stavební bloky aplikace s vlastním příkazy. Příkaz je sada konfigurací potřebných k dokončení konkrétní úlohy definované uživatelem.

### <a name="example-sentences"></a>Příklady vět
Příkladem projevy jsou množiny příkladů, které může uživatel spustit konkrétní příkaz. Všimněte si, že je třeba poskytnout pouze ukázku projevy a nikoliv vyčerpávající seznam. 

### <a name="parameters"></a>Parametry
Parametry jsou informace vyžadované příkazy k dokončení úkolu. Ve složitých scénářích lze také pomocí parametrů definovat podmínky, které aktivují vlastní akce.

### <a name="completion-rules"></a>Pravidla dokončení
Série pravidel, která se mají provést, když je příkaz připravený k splnění, tj. když jsou splněné všechny podmínky pravidel.

### <a name="interaction-rules"></a>Pravidla interakce
Další pravidla, která budou zpracovávat konkrétnější nebo složitou situaci. Můžete přidat další ověření nebo konfigurovat pokročilé funkce, jako jsou potvrzení nebo oprava v jednom kroku. Můžete také vytvořit vlastní pravidla interakce.

## <a name="parameters-configuration"></a>Konfigurace parametrů

Parametry jsou informace vyžadované příkazy k dokončení úkolu. Ve složitých scénářích lze také pomocí parametrů definovat podmínky, které aktivují vlastní akce.

### <a name="name"></a>Name
Parametr je identifikován vlastností název. Parametru byste měli vždy zadat popisný název. Parametr může být uveden v různých oddílech, například v podmínkách vytváření, odpovědích řeči nebo jiných akcích.
 
### <a name="isglobal"></a>IsGlobal
Zaškrtávací políčko, které určuje, zda je obor tohoto parametru sdílen všemi příkazy v aplikaci. Pokud je parametr globální, jeho hodnota může být potenciálně poskytována z libovolného oboru příkazu a jakmile je přiřazena hodnota – může být odkazováno z kteréhokoli z příkazů. 

### <a name="required"></a>Vyžadováno
Zaškrtávací políčko označující, zda je hodnota pro tento parametr vyžadována pro splnění a dokončení příkazu. Je nutné nakonfigurovat odpovědi, aby vyžádaly uživatele k zadání hodnoty, pokud je parametr označen jako povinný.

### <a name="type"></a>Typ
Vlastní příkazy podporují následující typy parametrů:


* DateTime
* Geography
* Číslo
* Řetězec

Všechny tyto typy parametrů podporují konfiguraci výchozí hodnoty, kterou můžete nakonfigurovat z portálu.

### <a name="configuration"></a>Konfigurace
Konfigurace je vlastnost parametru definovaná pouze pro typ: řetězec. Níže jsou uvedené podporované hodnoty.
* Žádné
* Přijmout úplný vstup: povolení této možnosti, parametr akceptuje všechny vstupní utterance, což je užitečné v případě, že uživatel potřebuje parametr s úplným utterance. například poštovní adresy.
* Přijměte předdefinované vstupní hodnoty z externího katalogu: používá se ke konfiguraci parametru, který může předpokládat širokou škálu hodnot. například prodejní katalog. V tomto případě je katalog hostovaný na externím webovém koncovém bodu a dá se nakonfigurovat nezávisle.
* Přijměte předdefinované vstupní hodnoty z interního katalogu: používá se ke konfiguraci parametru, který může předpokládat několik hodnot. V tomto případě musí být hodnoty nakonfigurovány v nástroji Speech Studio.


### <a name="validation"></a>Ověřování
Ověření jsou určena pro určité typy parametrů, které umožňují konfigurovat omezení pro hodnotu parametru. V současné době vlastní příkazy podporují ověření následujících typů parametrů:
* DateTime
* Číslo

## <a name="rules-configuration"></a>Konfigurace pravidel
Pravidlo ve vlastních příkazech je definováno sadou **podmínek**, které jsou při splnění splněné, spustí sadu **akcí**. Pravidlo také umožňuje nakonfigurovat **stav následného spuštění** a **očekávání** pro další tah.

### <a name="types"></a>Typy
Vlastní příkazy podporují následující kategorie pravidel:
* Pravidla dokončení

    Seznam pravidel, která se mají provést při splnění příkazu Všechna pravidla konfigurovaná v této části, pro které jsou splněné podmínky, se spustí.
    
* Pravidla interakce

    Pravidla interakce se dají použít ke konfiguraci dalších vlastních ověření, potvrzení, opravy v jednom kroku nebo pro provedení jakékoli jiné vlastní logiky dialogových oken. Tato pravidla jsou vyhodnocována při každém zpracování a lze je použít k aktivaci pravidel dokončení.

Různé akce konfigurované jako součást pravidla jsou spouštěny v pořadí, ve kterém se zobrazují na portálu pro vytváření.
    
### <a name="conditions"></a>Podmínky
Sada podmínek, které musí být splněny, aby bylo pravidlo spuštěno. Podmínka pravidla může být následujících typů:
* Hodnota parametru Equals: hodnota konfigurovaného parametru se rovná konkrétní hodnotě.
* Žádná hodnota parametru: nakonfigurované parametry by neměly mít žádnou hodnotu.
* Požadované parametry: nakonfigurovaný parametr má hodnotu.
* Všechny požadované parametry: všechny parametry, které byly označeny jako povinné, mají hodnotu.
* Aktualizované parametry: jedna nebo více hodnot parametrů bylo aktualizováno v důsledku zpracování aktuálního vstupu (utterance/Activity).
* Potvrzení bylo úspěšné: vstupní utterance nebo aktivita byla úspěšné potvrzení (Ano).
* Potvrzení bylo odepřeno: vstupní utterance/aktivita byla úspěšné potvrzení (ne).
* Předchozí příkaz se musí aktualizovat: Tato podmínka se v instancích používá, když chcete zachytit souběžné potvrzení spolu s aktualizací. Na pozadí se nakonfiguruje, když se v dialogovém okně zjistí negativní potvrzení, že záměr je stejný jako předchozí a uživatel odpověděl s aktualizací.

### <a name="actions"></a>Akce
* Odeslat odezvu na řeč: odešlete odpověď na řeč zpátky klientovi.
* Hodnota parametru aktualizace: hodnota aktualizace parametru příkazu na zadanou hodnotu.
* Hodnota vymazat parametr vymažte z hodnoty parametru příkazu.
* Zavolat webový koncový bod: volání do webového koncového bodu.
* Odeslat aktivitu klientovi: odeslání vlastní aktivity klientovi.

### <a name="expectations"></a>Očekávání
Očekávání se používají ke konfiguraci pomocných parametrů pro zpracování dalšího vstupu uživatele. Podporovány jsou následující typy:
* Očekává se potvrzení od uživatele: Určuje, jestli má aplikace při dalším vstupu uživatele očekávat potvrzení (ano/ne).
* Byl očekáván vstup z parametru od uživatele: zadejte jeden nebo více parametrů příkazu, které aplikace očekává od vstupu uživatele.

### <a name="post-execution-state"></a>Stav po spuštění
Stav dialogového okna po zpracování aktuálního vstupu (utterance/Activity). Jedná se o následující typy:
* Příkaz byl dokončen: dokončete příkaz a nebudou zpracována žádná další pravidla příkazu.
* Spustit pravidla dokončení: proveďte všechna platná pravidla dokončování.
* Počkat na vstup uživatele: Počkejte na zadání dalšího uživatele.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zobrazit ukázky na GitHubu](https://aka.ms/speech/cc-samples)
