---
title: Zlepšení modelu služby Custom Speech-Speech
titleSuffix: Azure Cognitive Services
description: Konkrétní typy přepisů a souvisejících textů na základě popisku můžou zlepšit přesnost rozpoznávání pro model řeči a text založený na scénáři speaking.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: v-demjoh
ms.openlocfilehash: bb904482f3cb5900b724803816269f1b10ab3720
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83727875"
---
# <a name="improve-custom-speech-accuracy"></a>Vylepšení přesnosti služby Custom Speech

V tomto článku se dozvíte, jak vylepšit kvalitu vlastního modelu přidáním zvukového záznamu, přepisu s popiskem a souvisejícího textu.

## <a name="accuracy-in-different-scenarios"></a>Přesnost v různých scénářích

Scénáře rozpoznávání řeči se liší podle kvality zvuku a jazyka (slovníku slovního a mluveného stylu). Následující tabulka prověřuje čtyři běžné scénáře:

| Scénář | Kvalita zvuku | Slovník | Styl speaking |
|----------|---------------|------------|----------------|
| Call center | Nízká, 8 kHz, možná 2 lidi na 1 zvukovém kanálu, mohli byste je zkomprimovat | Zúžené a jedinečné pro doménu a produkty | Konverzace, volně strukturovaná |
| Hlasový asistent (například Cortana nebo okno jednotky) | Vysoká, 16 kHz | Silná entita (názvy skladeb, produkty, umístění) | Jasně uvedená slova a fráze |
| Diktování (rychlá zpráva, poznámky, hledání) | Vysoká, 16 kHz | Řadu | Poznámka – převzetí |
| Skryté titulky videa | Různé, včetně proměnlivého používání mikrofonu, přidaná hudba | Různé, ze schůzek, recitováného rozpoznávání řeči, hudebních textů | Číst, připravit nebo volně strukturované |

Různé scénáře vedou k různým výsledkům kvality. V následující tabulce najdete informace o tom, jak obsah z těchto čtyř scénářů se [podílí na chybovosti slov (WER)](how-to-custom-speech-evaluate-data.md). Tabulka uvádí, které typy chyb jsou v každém scénáři nejběžnější.

| Scénář | Kvalita rozpoznávání řeči | Chyby vkládání | Chyby při odstraňování | Chyby nahrazení |
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

Další související textové věty mohou primárně snižovat chyby při nahrazování související s chybnou rozpoznáním běžných slov a slov specifických pro doménu jejich zobrazením v kontextu. Slova specifická pro doménu můžou být neobvyklá nebo vytvořená slova, ale jejich výslovnost musí být jednoduše rozpoznaná.

> [!NOTE]
> Nepoužívejte související textové věty, které zahrnují šum, například nerozpoznatelné znaky nebo slova.

### <a name="add-audio-with-human-labeled-transcripts"></a>Přidat zvuk s přepisy s lidským popiskem

Zvuk s přepisy s lidskými štítky nabízí nejlepší vylepšení přesnosti, pokud zvuk pochází z cílového případu použití. Vzorky musí pokrývat plný rozsah řeči. Například centrum volání pro maloobchodní obchod získá většinu volání swimwear a Sunglasses během letních měsíců. Zajistěte, aby vaše ukázka obsahovala plný rozsah řeči, který chcete detekovat.

Vezměte v úvahu tyto podrobnosti:

* Custom Speech může zachytit kontext slova, aby se snížily chyby při nahrazování, ne chyby vložení nebo odstranění.
* Vyhněte se ukázkám, které obsahují chyby přepisu, ale zahrnují různorodost kvality zvuku.
* Vyhněte se vět, které nesouvisí s vaší doménou problému. Nesouvisející věty můžou poškodit váš model.
* V případě, že se kvalita přepisů liší, je možné duplikovat nevyhovující věty (například Skvělé přepisy, které obsahují klíčové fráze) ke zvýšení jejich váhy.

### <a name="add-new-words-with-pronunciation"></a>Přidat nová slova s výslovností

Slova, která jsou tvořená nebo vysoce specializovaná, mohou mít jedinečné výslovnost. Tato slova je možné rozpoznat, pokud je možné slovo rozdělit na menší slova a vyslovit. Například pro rozpoznání pole **Xbox**, vyslovení jako **X**. Tento přístup nezvýší celkovou přesnost, ale může zvýšit rozpoznávání těchto klíčových slov.

> [!NOTE]
> Tato technika je v tuto chvíli dostupná jenom pro některé jazyky. Podrobnosti najdete v tématu přizpůsobení výslovnosti v [tabulce převod řeči na text](language-support.md) .

## <a name="sources-by-scenario"></a>Zdroje podle scénáře

Následující tabulka uvádí scénáře rozpoznávání hlasu a uvádí zdrojové materiály, které je potřeba zvážit v třech kategoriích výukového obsahu uvedených výše.

| Scénář | Související textové věty | Audio + přepisy s popiskem | Nová slova s výslovností |
|----------|------------------------|------------------------------|------------------------------|
| Call center             | marketingové dokumenty, weby, recenze produktů související s aktivitou centra volání | volání Center volá přepisu podle člověka | výrazy s nejednoznačnými výslovnostmi (viz Xbox výše) |
| Hlasový asistent         | Vypsat věty pomocí všech kombinací příkazů a entit | zaznamenávat hlasy, které mluví do zařízení, a přepisovat je do textu | názvy (filmy, skladby, produkty), které mají jedinečnou výslovnost |
| Diktování               | psaný vstup, jako jsou rychlé zprávy nebo e-maily | Podobně jako v předchozím příkladu | Podobně jako v předchozím příkladu |
| Skryté titulky videa | Televizní pořady – skripty, filmy, marketingový obsah, souhrny videí | přesný přepis videí | Podobně jako v předchozím příkladu |

## <a name="next-steps"></a>Další kroky

- [Trénování modelu](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Další zdroje

- [Příprava a testování dat](how-to-custom-speech-test-data.md)
- [Kontrola dat](how-to-custom-speech-inspect-data.md)