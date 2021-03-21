---
title: Pokyny pro efektivní prostředí ukotvení
description: Pokyny a požadavky pro efektivní vytváření a hledání kotev pomocí prostorových kotev Azure.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: a6bc500516de7e554c38a335ea57519a39c8f602
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95487499"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Vytvoření efektivního prostředí pro kotvu pomocí prostorových kotev Azure

Tento článek poskytuje pokyny a požadavky, které vám pomůžou efektivně vytvářet a vyhledávat kotvy pomocí prostorových kotev Azure.

## <a name="anchor-improvement-over-time"></a>Vylepšení ukotvení v průběhu času

Pomocí prostorových kotev Azure se snažíme vylepšit kvalitu budoucích operací hledání. Provedeme to pomocí shromážděných dat prostředí k rozšíření vizuálních informací o kotvách, které hledáte. Tento proces běží pod digestoří a je offline optimalizace spuštěná službou prostorových kotev Azure pro optimalizaci pro vaše prostředí. Další data shromážděná během každé operace vytváří silnější porozumění prostředí. To zlepšuje kvalitu a umožňuje lépe vyhledávat kotvy prostřednictvím změny prostředí, předávání času a uživatelů, kteří hledají kotvy z různých úhlů a perspektiv.

## <a name="good-anchors"></a>Dobré kotvy

I když se prostorové kotvy Azure snaží vylepšit kvalitu kotev v čase, je také důležité investovat čas při vytváření dobrých kotev buď při pedagogování, nebo při vytváření identifikátorů uživatelů v uživatelském prostředí (UX). Díky investicím do předplatného vytváření dobrých kotev umožníte koncovým uživatelům spolehlivě najít kotvy:

- V různých zařízeních.
- V různou dobu.
- V různých světelných podmínkách.
- Z požadovaných perspektiv v prostoru.

## <a name="static-and-dynamic-locations"></a>Statická a dynamická umístění

Součástí navrhování možností ukotvení je výběr umístění. Budou umístění statická a definovaná správcem prostoru? Nebo budou tyto uživatele dynamické a definované uživatelem?

Manažer maloobchodního obchodu může chtít, aby uživatelé mohli přesvědčit uživatele, aby mohli navštěvovat. Vývojář herní hry s jednou realitou ale by nejspíš chtěl dovolit uživatelům zvolit, kde se má přehrát.

U statických umístění můžete správcům naučit čas strávený prostorem s dobrými kotvami.

V případě dynamických umístění byste si měli myslet, jak naučíte uživatele v uživatelském prostředí a chcete vytvářet dobré kotvy.

## <a name="stable-visual-features"></a>Stabilní vizuální funkce

Systémy vizuálního sledování používané na hybridních a rozšířených zařízeních se spoléhají na vizuální funkce prostředí. Chcete-li získat nejspolehlivější možnosti:

- *Vytvořte* kotvy v umístěních, která mají stabilní vizuální funkce (tj. funkce, které se často nemění).

-  Nevytvářejte kotvy na velkých prázdných površích, které nemají žádné rozlišující charakteristiky.

-  Nevytvářejte kotvy na vysoce odrážejících materiálech.

-  Nevytvářejte kotvy na površích, kde se vzor opakuje, například koberc nebo Wallpaper.

![Příklady dobré prostředí pro kotvy a chybné prostředí pro kotvy](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Různé perspektivy zobrazení

Při vytváření kotvy si myslíte o lidech, kteří se později pokusí kotvu najít.

Zvažte, například kotvu uprostřed místnosti, která má dvě dveře. Pravděpodobně budete chtít uživatelům dovolit zadat místnost z obou dveří. Při vytváření kotvy budete muset zkontrolovat jeho polohu z obou dveří. Provedete změnu perspektivy pro zachycení dat prostředí kolem kotvy, aby uživatelé mohli lokalizovat kotvy z obou dveří.

Obecně platí, že při vytváření kotvy je možné ji vyhledat z perspektiv osob, které se pokusí najít. Takže pokud umísťujete virtuální obsah do venkovního sculptureu, je při vytváření kotvy vhodné protékat Sculpture a při jeho procházení. Pokud je vaše kotva v rohu místnosti, existuje pouze jeden směr, od kterého se k ní přiblíží. Při vytváření tohoto kotva ho můžete prověřit přímo z této perspektivy.

## <a name="multiple-anchors"></a>Více kotev

Osvětlení může v vizuálních funkcích, které aplikace detekuje, udělat rozdíl. Kotvy vytvořené silně přirozeným světlem mohou být obtížné najít uměle světla a naopak.

Pokud máte tento problém, může vám pomohlo vytvořit dvě kotvy. Na stejném místě vytvořte jednu kotvu v letním a jiném v uměle světle. Vaše aplikace se pak může dotazovat na oba kotvy. V případě, že je umístění ukotveno, aplikace bude mít pro kotvu možnost pozice.

Podobně v prostředích, kde se vizuální funkce mění, protože většina objektů přesouvá, může vám více kotev přispět. V případě, že dojde k příliš obtížnému nalezení kotvy z důvodu významných změn v prostředí, můžete kotvu nahradit novým. Můžete to udělat například v maloobchodě, kde se rozložení aktualizuje každých několik měsíců.

## <a name="targets-and-rooms"></a>Cíle a místnosti

V mnoha případech je kotva vstupním bodem pro prostředí vaší aplikace. Budete chtít rychle a spolehlivě projít tento krok, aby mohli uživatelé zadat své prostředí. Doba útraty, jak budou uživatelé najít vaše kotvy, je důležitým krokem pro návrh. Je vhodné si představit, jak najít kotvy v rámci dvou hlavních scénářů: *cílů* a *místností*.

### <a name="targets"></a>Targets

V cílovém scénáři je umístění kotvy dobře známé. Například v aplikaci pro malování s fiktivní hybridní realitou si jeden uživatel umístí virtuální plátno na zeď. Dává ostatním uživatelům v místnosti pokyn, aby svá zařízení odkazovali na stejné místo na zdi, aby nalezla kotvu a zahájila prostředí.

Dalším příkladem cílového scénáře může být přihlášení k kavárně, které čte "vyhledávání obchodů". Kavárna zde umístila kotvu. Když uživatelé prohledají znaménko, naleznou kotvu a najdou na to, aby našli obchody v kávě.

V cílovém scénáři můžou fotky pomáhat. Pokud zobrazíte uživatelům fotografii zamýšleného cíle na svém zařízení, můžou rychle zjistit, co se má kontrolovat v reálném světě. Můžete například pomoci vašim uživatelům v rámci všeobecné oblasti zamýšleného cíle pomocí GPS. Když uživatel dorazí, aplikace zobrazí fotografii cíle. Uživatel vyhledá místo, vyhledá cíl a vyhledá kotvu.

![Ilustrace kotvy ukazující fotografii cíle na mobilním zařízení uživatele](./media/start-here-edit.png)

### <a name="rooms"></a>Prostory

Ve scénáři místnosti uživatelé zadají místo jednoduše vědět, že tady je kotva. Uživatelé prohledají místo na svém zařízení a rychle vyhledají kotvu.

Toto prostředí obvykle vyžaduje, abyste vytvořili dobře uspořádané kotvy, jak je popsáno v různých perspektivách zobrazení. Pokud jste při vytváření kotvy prohledali místnost z řady pohledů, uživatelé budou moci při pokusu o její vyhledání téměř kdekoli Hledat.

![Obrázek, jak může uživatel naskenovat místnost pro vyhledání kotvy](./media/scan-room.png)

V podstatě se při vytváření kotvy tráví více času, aby bylo možné později vyhledat kotvu a najít kotvu rychleji. Při vytváření prostředí budete muset vzít v úvahu tento důležitý obchod.

Příklad aplikace pro malování ve smíšené realitě, kterou jsme dříve poznamenali, nefunguje dobře ve scénáři místnosti. Tady se uživatel, který umístí kotvu, chce rychle připojit k prostředí. Uživatelé nechtějí čekat na spuštění prostředí, dokud nedojde k dobře prověření místnosti. Vzhledem k tomu, že všichni uživatelé znají přesně, kde si zařízení nasměrují, aby vyhledali kotvy, tento příklad funguje lépe jako cílový scénář.

## <a name="anchor-location"></a>Umístění kotvy

Systémy vizuálního sledování spoléhají na vizuální funkce v prostředí. Čím více vizuálních funkcí vyhledávání zahrnuje, tím větší je pravděpodobnost nalezení kotvy.

Při vytváření uživatelského rozhraní, které podporuje užitečnou kontrolu prostředí, postupujte podle obecných pokynů v této části.

Za prvé, pokud uživatel nenalezne kotvu během několika sekund, aplikace by měla uživatelům povzbudit, aby si zařízení přesunuli, aby se zaznamenalo více perspektiv. Aplikace může také uživatelům povzbudit, aby se přesunuli kolem prostředí a kontrolovali kotvu z dalších perspektiv. Větší perspektiva funkcí, kterou zařízení vidí, je lepší, protože se tím zvyšuje pravděpodobnost, že se kotva nachází, a také shromáždí více dat prostředí, která budou použita ke zlepšení kvality kotvy.

V případě cílových scénářů požádejte uživatele, aby se přesunul kolem cíle, aby ho mohli zobrazit z různých perspektiv. Jinými slovy, požádejte uživatele, aby zachytával cíl z nových perspektiv, dokud se kotva neumístí.

V případě scénářů pro místnosti požádejte uživatele, aby pomaleji skenování místnosti. Například požádejte uživatele, aby zachytí 180 stupňů nebo dokonce 360 stupňů místnosti. Nebo požádejte uživatele o zobrazení místnosti z nové perspektivy.

Nejužitečnější způsob je kontrolovat v místnosti. Prověřování v místnosti zachytí více vizuálních funkcí prostředí než skenování blízké stěny, například. Skenování blízké stěny nezachycuje tolik užitečných vizuálních funkcí prostředí.

Při hledání kotvy není vhodné opakovaně přesouvat zařízení ze strany na stranu. Stačí zachytit stejné body ze stejné perspektivy.

## <a name="experience-tests"></a>Testy zkušeností

V tomto článku jsme probrali obecné pokyny. S prostorovými kotvami píšete aplikace, které komunikují se skutečným světem. Z tohoto důvodu byste měli věnovat čas testování scénářů ukotvení vaší aplikace v reálných prostředích. To platí hlavně pro prostředí, která reprezentují, kde očekáváte, aby vaši uživatelé aplikaci používali.
