---
title: Vyhodnocení a vylepšení Custom Speech přesnosti – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: V tomto dokumentu se dozvíte, jak kvantitativní měření a zlepšení kvality cloudového modelu pro převod řeči a textu nebo vlastního modelu. Pro testování přesnosti se vyžadují data přepisu a přepisu, které by měly být k dispozici po dobu 30 až 5 hodin reprezentativního zvuku.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.openlocfilehash: b7e4ea586098ea3eb0dfd684650f798d7988e18b
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634579"
---
# <a name="evaluate-and-improve-custom-speech-accuracy"></a>Vyhodnocení a vylepšení přesnosti služby Custom Speech

V tomto článku se dozvíte, jak změřit a zdokonalit přesnost modelů řeči a textu od Microsoftu nebo vašich vlastních modelů. Pro testování přesnosti se vyžadují data přepisu a přepisu, které by měly být k dispozici po dobu 30 až 5 hodin reprezentativního zvuku.

## <a name="evaluate-custom-speech-accuracy"></a>Vyhodnocení přesnosti služby Custom Speech

Standardní hodnota pro měření přesnosti modelu je [počet chyb v aplikaci](https://en.wikipedia.org/wiki/Word_error_rate) (WER). Služba WER počítá počet nesprávných slov zjištěných během rozpoznávání a pak je vydělí celkovým počtem slov poskytnutých v rámci přepisu popisku (viz obrázek N). Nakonec se toto číslo vynásobí 100% za účelem výpočtu WER.

![Vzorec WER](./media/custom-speech/custom-speech-wer-formula.png)

Nesprávně identifikovaná slova spadají do tří kategorií:

* Vložení (I): slova, která jsou nesprávně přidána do přepisu hypotéz
* Odstranění (D): slova, která jsou v přepisu hypotéza nerozpoznána
* Nahrazování: slova, která byla nahrazena mezi odkazem a hypotézou

Tady je příklad:

![Příklad nesprávně identifikovaných slov](./media/custom-speech/custom-speech-dis-words.png)

Pokud chcete místní výsledky služby WER replikovat, můžete použít sclite z [SCTK](https://github.com/usnistgov/SCTK).

## <a name="resolve-errors-and-improve-wer"></a>Řešení chyb a vylepšení WER

Pomocí služby WER z výsledků rozpoznávání počítačů můžete vyhodnotit kvalitu modelu, který používáte, pro aplikaci, nástroj nebo produkt. WER 5% až 10% se považuje za dobrou kvalitu a je připravená k použití. Je přijatelné akceptovat 20%, ale možná budete chtít zvážit další školení. WER na více než 30% signalizuje špatné kvality a vyžaduje přizpůsobení a školení.

Důležité je, jak jsou distribuované chyby. V případě, že dojde k mnoha chybám při odstraňování, většinou se jedná o slabý signál zvukové signalizace. Pokud chcete tento problém vyřešit, budete muset shromáždit zvuková data blíž ke zdroji. Chyby vkládání znamenají, že zvuk byl zaznamenán v prostředí s vysokou úrovní šumu a Crosstalk může být k dispozici, což způsobuje problémy s rozpoznáváním. K chybám nahrazení dochází často v případě, že se nedostatečná ukázka podmínek specifických pro doménu poskytla jako přepisy nebo související text.

Analýzou jednotlivých souborů můžete určit, jaký typ chyb existuje a které chyby jsou jedinečné pro určitý soubor. Porozumění problémům na úrovni souborů vám pomůže zaměřit se na vylepšení.

## <a name="create-a-test"></a>Vytvořit test

Pokud chcete testovat kvalitu modelu standardních hodnot řeči od Microsoftu nebo vlastního modelu, který jste si vyzkoušeli, můžete porovnat dva modely vedle sebe a vyhodnotit tak přesnost. Porovnávání zahrnuje výsledky WER a rozpoznávání. Vlastní model se obvykle porovnává se základními modely Microsoftu.

Chcete-li vyhodnotit modely vedle sebe:

1. Přihlaste se k [portálu Custom Speech](https://speech.microsoft.com/customspeech).
2. Přejít na **text > řeči Custom Speech > [název projektu] > testování**.
3. Klikněte na **Přidat test**.
4. Vyberte možnost **vyhodnotit přesnost**. Dejte testu název, popis a vyberte si zvukovou datovou sadu přepisu a s popiskem.
5. Vyberte až dva modely, které chcete testovat.
6. Klikněte na **Vytvořit**.

Po úspěšném vytvoření testu můžete porovnat výsledky vedle sebe.

### <a name="side-by-side-comparison"></a>Souběžné porovnání

Po dokončení testu, který je označen změnou stavu na *úspěch*, najdete číslo wer pro oba modely zahrnuté v testu. Kliknutím na název testu zobrazíte stránku s podrobnostmi o testování. Tato stránka podrobností obsahuje seznam všech projevy ve vaší datové sadě, které označují výsledky rozpoznávání dvou modelů společně s přepisem z odeslané datové sady. Chcete-li zkontrolovat souběžné porovnání, můžete přepínat různé typy chyb včetně vložení, odstranění a nahrazování. Díky poslechu zvuku a porovnávání výsledků rozpoznávání v každém sloupci, který ukazuje přepis a výsledky pro dva modely řeči, můžete rozhodnout, který model vyhovuje vašim potřebám a kde je potřeba další školení a vylepšení.

## <a name="improve-custom-speech-accuracy"></a>Vylepšení přesnosti služby Custom Speech

Scénáře rozpoznávání řeči se liší podle kvality zvuku a jazyka (slovníku slovního a mluveného stylu). Následující tabulka prověřuje čtyři běžné scénáře:

| Scenario | Kvalita zvuku | Slovník | Styl speaking |
|----------|---------------|------------|----------------|
| Call center | Nízká, 8 kHz, možná 2 lidi na 1 zvukovém kanálu, mohli byste je zkomprimovat | Zúžené a jedinečné pro doménu a produkty | Konverzace, volně strukturovaná |
| Hlasový asistent (například Cortana nebo okno jednotky) | Vysoká, 16 kHz | Silná entita (názvy skladeb, produkty, umístění) | Jasně uvedená slova a fráze |
| Diktování (rychlá zpráva, poznámky, hledání) | Vysoká, 16 kHz | Řadu | Poznámka – převzetí |
| Skryté titulky videa | Různé, včetně proměnlivého používání mikrofonu, přidaná hudba | Různé, ze schůzek, recitováného rozpoznávání řeči, hudebních textů | Číst, připravit nebo volně strukturované |

Různé scénáře vedou k různým výsledkům kvality. V následující tabulce najdete informace o tom, jak obsah z těchto čtyř scénářů se [podílí na chybovosti slov (WER)](how-to-custom-speech-evaluate-data.md). Tabulka uvádí, které typy chyb jsou v každém scénáři nejběžnější.

| Scenario | Kvalita rozpoznávání řeči | Chyby vkládání | Chyby při odstraňování | Chyby nahrazení |
|----------|----------------------------|------------------|-----------------|---------------------|
| Call center | Střední (< 30% WER) | Nízká, s výjimkou případů, kdy ostatní lidé mluví na pozadí | Může být vysoké. Centra volání můžou mít vysokou úroveň šumu a překrývající se reproduktory můžou model Zaměňujte. | Střední. Tyto chyby můžou způsobovat i názvy produktů a lidí. |
| Hlasový asistent | Vysoká (může být < 10% WER) | Nízká | Nízká | Střední z důvodu názvů písní, názvů produktů nebo míst |
| Diktování | Vysoká (může být < 10% WER) | Nízká | Nízká | Vysoká |
| Skryté titulky videa | Závisí na typu videa (může být < 50% WER). | Nízká | Může být vysoké kvůli hudbě, hluku, kvalitě mikrofonu | Žargonu můžou tyto chyby způsobit. |

Určení součástí WER (počet chyb vložení, odstranění a nahrazení) pomáhá určit, jaký typ dat se má přidat pro zlepšení modelu. K zobrazení kvality směrného modelu použijte [portál Custom Speech](https://speech.microsoft.com/customspeech) . Portál sestaví počty chyb vložení, nahrazení a odstranění, které jsou kombinovány s sazbou kvality WER.

## <a name="improve-model-recognition"></a>Vylepšení rozpoznávání modelu

Přidáním školicích dat na [portálu Custom Speech](https://speech.microsoft.com/customspeech)můžete zkrátit chyby rozpoznávání. 

Naplánujte si udržování vlastního modelu tím, že se pravidelně přidávají zdrojové materiály. Váš vlastní model potřebuje další školení, abyste měli přehled o změnách vašich entit. Můžete například potřebovat aktualizace názvů produktů, názvů skladeb nebo nových umístění služby.

Následující části popisují, jak může každý druh dalších školicích dat snižovat chyby.

### <a name="add-related-text-sentences"></a>Přidat související textové věty

Při učení nového vlastního modelu Začněte přidáním souvisejícího textu, který vylepšuje rozpoznávání slov a frází specifických pro doménu. Související textové věty mohou primárně snižovat chyby při nahrazování související s nerozpoznáním běžných slov a slov specifických pro doménu jejich zobrazením v kontextu. Slova specifická pro doménu můžou být neobvyklá nebo vytvořená slova, ale jejich výslovnost musí být jednoduše rozpoznaná.

> [!NOTE]
> Nepoužívejte související textové věty, které zahrnují šum, například nerozpoznatelné znaky nebo slova.

### <a name="add-audio-with-human-labeled-transcripts"></a>Přidat zvuk s přepisy s lidským popiskem

Zvuk s přepisy s lidskými štítky nabízí nejlepší vylepšení přesnosti, pokud zvuk pochází z cílového případu použití. Vzorky musí pokrývat plný rozsah řeči. Například centrum volání pro maloobchodní obchod získá většinu volání swimwear a Sunglasses během letních měsíců. Zajistěte, aby vaše ukázka obsahovala plný rozsah řeči, který chcete detekovat.

Vezměte v úvahu tyto podrobnosti:

* Školení se zvukem přináší nejvíc výhod, pokud je zvuk také obtížné pochopit pro lidi. Ve většině případů byste měli začít školení jenom pomocí souvisejícího textu.
* Pokud používáte jeden z nejčastěji používaných jazyků, jako je například USA – angličtina, je velmi pravděpodobné, že nebudete muset vlakovat se zvukovými daty. V takových jazycích základní modely nabízejí ve většině scénářů již velmi dobré výsledky rozpoznávání. je pravděpodobně dostačující ke školení se souvisejícím textem.
* Custom Speech může zachytit kontext slova, aby se snížily chyby při nahrazování, ne vkládání nebo chyby při odstraňování.
* Vyhněte se ukázkám, které obsahují chyby přepisu, ale zahrnují různorodost kvality zvuku.
* Vyhněte se vět, které nesouvisí s vaší doménou problému. Nesouvisející věty můžou poškodit váš model.
* V případě, že se kvalita přepisů liší, je možné duplikovat nevyhovující věty (například Skvělé přepisy, které obsahují klíčové fráze) ke zvýšení jejich váhy.
* Služba Speech automaticky použije přepisy k vylepšení rozpoznávání slov specifických pro doménu a frází, jako kdyby byly přidány jako související text.
* Dokončení školicí operace může trvat několik dní. Pro zlepšení rychlosti školení nezapomeňte vytvořit předplatné služby Speech Service v [oblasti s vyhrazeným hardwarem](custom-speech-overview.md#set-up-your-azure-account) pro školení.

> [!NOTE]
> Ne všechny základní modely podporují školení se zvukem. Pokud základní model ho nepodporuje, služba řeči bude používat jenom text z přepisů a zvuk bude ignorovat. Seznam základních modelů, které podporují školení se zvukovými daty, najdete v tématu [jazyková podpora](language-support.md#speech-to-text) . I v případě, že základní model podporuje školení se zvukovými daty, může služba použít jenom část zvuku. Pořád bude používat všechny přepisy.

> [!NOTE]
> V případech, kdy změníte základní model používaný pro školení a máte zvuk v datové sadě školení, *vždy* ověřte, zda nový vybraný základní model [podporuje školení se zvukovými daty](language-support.md#speech-to-text). Pokud dřív použitý základní model nepodporoval školení se zvukovými daty a datová sada pro školení obsahuje zvuk, může se výrazně zvýšit doba školení s novým základním modelem a může se stát, **že budete moct** snadno přejít z několika hodin na několik dní. To platí hlavně v **případě, že** vaše předplatné služby Speech není v [oblasti s vyhrazeným hardwarem](custom-speech-overview.md#set-up-your-azure-account) pro školení.
>
> Pokud se setkáte s problémem popsaným v předchozím odstavci, můžete rychle zkrátit dobu školení tím, že snížíte velikost zvuku v datové sadě nebo zcela odeberete a necháte jenom text. Tato možnost se důrazně doporučuje, pokud vaše předplatné služby Speech **není v** [oblasti s vyhrazeným hardwarem](custom-speech-overview.md#set-up-your-azure-account) pro školení.

### <a name="add-new-words-with-pronunciation"></a>Přidat nová slova s výslovností

Slova, která jsou tvořená nebo vysoce specializovaná, mohou mít jedinečné výslovnost. Tato slova je možné rozpoznat, pokud je možné slovo rozdělit na menší slova a vyslovit. Například pro rozpoznání pole **Xbox**, vyslovení jako **X**. Tento přístup nezvýší celkovou přesnost, ale může zvýšit rozpoznávání těchto klíčových slov.

> [!NOTE]
> Tato technika je v tuto chvíli dostupná jenom pro některé jazyky. Podrobnosti najdete v tématu přizpůsobení výslovnosti v [tabulce převod řeči na text](language-support.md) .

## <a name="sources-by-scenario"></a>Zdroje podle scénáře

Následující tabulka uvádí scénáře rozpoznávání hlasu a uvádí zdrojové materiály, které je potřeba zvážit v třech kategoriích výukového obsahu uvedených výše.

| Scenario | Související textové věty | Audio + přepisy s popiskem | Nová slova s výslovností |
|----------|------------------------|------------------------------|------------------------------|
| Call center             | marketingové dokumenty, weby, recenze produktů související s aktivitou centra volání | volání Center volá přepisu podle člověka | výrazy s nejednoznačnými výslovnostmi (viz Xbox výše) |
| Hlasový asistent         | Vypsat věty pomocí všech kombinací příkazů a entit | zaznamenávat hlasy, které mluví do zařízení, a přepisovat je do textu | názvy (filmy, skladby, produkty), které mají jedinečnou výslovnost |
| Diktování               | psaný vstup, jako jsou rychlé zprávy nebo e-maily | Podobně jako v předchozím příkladu | Podobně jako v předchozím příkladu |
| Skryté titulky videa | Televizní pořady – skripty, filmy, marketingový obsah, souhrny videí | přesný přepis videí | Podobně jako v předchozím příkladu |

## <a name="next-steps"></a>Další kroky

* [Trénování a nasazení modelu](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Další zdroje informací

* [Příprava a testování dat](./how-to-custom-speech-test-and-train.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)