---
title: Co je Project Personality Chat?
titlesuffix: Azure Cognitive Services
description: Tento článek poskytuje přehled o Azure Project Personality Chat, což je cloudové rozhraní API pro zlepšení konverzačních schopností vašeho robota.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: personality-chat
ms.topic: overview
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ms.openlocfilehash: 865815862be4a2250347f782985e0bce87780197
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220530"
---
# <a name="what-is-project-personality-chat"></a>Co je Project Personality Chat?

Project Personality Chat vylepšuje konverzační schopnosti vašeho robota díky zpracování nezávazné konverzace v souladu s jinou zvolenou osobností. Personality Chat používá k identifikaci obvyklých záměrů v nezávazné konverzaci klasifikátory záměrů a generuje odpovědi, které jsou konzistentní s osobností. Na základě záměru a skóre důvěrnosti robot zvolí nejlepší odpověď z kurátorované redakční databáze nebo vygeneruje odpověď v reálném čase pomocí hloubkových neurálních sítí (DNN). Můžete zvolit ze tří výchozích osob. Model osoby vrátí odpovědi, které se nejvíc hodí na zvolenou osobnost.

K dispozici je přizpůsobitelná redakční sada pro dotazy nezávazné konverzace. Dá se snadno integrovat pomocí sady SDK služby Microsoft Bot Framework. Tato sada SDK vám šetří čas a náklady na jejich vytvoření od nuly.

## <a name="getting-started-with-project-personality-chat"></a>Začínáme se službou Project Personality Chat

Můžete navštívit stránku se cvičeními pro Project Personality Chat a chatovat s dostupnou ukázkou a požádat o dřívější přístup, až bude služba dostupná.
V současné době můžete do svých robotů také integrovat přizpůsobitelnou knihovnu jen pro redakční účely prostřednictvím sady SDK služby Microsoft Bot Framework. <br>
[Ukázky: Integrace Personality Chatu do robota](https://github.com/Microsoft/BotBuilder-PersonalityChat/) <br>
[Vyzkoušet knihovnu Personality Chat](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp)

## <a name="generating-responses-using-neural-networks"></a>Generování odpovědí pomocí neurálních sítí

Personality Chat používá modely hloubkového učení, aby se naučil další obecné konverzační vzory a mohl generovat odpovědi. Model generování odpovědí je opakovaná neurální síť (RNN). Tento model je trénovaný na milionech konverzací, ve kterých rozumí vzorům lidské interakce a učí se je. Na základě naučených vzorů větné stavby se formují nové dotazy a generují odpovědi. Při generování této nové odpovědi hledá model v „psaném slovníku“ slov a vybere n-nejlepší první slova pro odpověď. Pomocí paprskového prohledávání hledá dál n-nejlepší druhá slova pro každé z vygenerovaných první slov. Odpověď se považuje za dokončenou, když model vybere slovo pro „konec věty“. Jakmile má všechny odpovědi, zvolí n-nejlepší odpovědi na základě skóre pravděpodobnosti pro úplnou odpověď.

Model se učí generovat kontextově odpovídající obraty, které mají správnou „strukturu“. Například dotaz jako „Chcete si teď popovídat?“ toho uvádí docela dost o struktuře přijatelné odpovědi: Pravděpodobně bude začínat nějakým vyjádřením slova „Ano“ nebo „Ne“ a slovesem v 1. osobě jednotného čísla. Odpověď „Ne“ bude pravděpodobně obsahovat zdvořilé vysvětlení a tak dále.

Systém se pokusí naučit jazykový model pro základní strukturu konverzace. Tato struktura by měla umožňovat, aby odpovědi byly částečně ovlivňovány externími omezeními, jako jsou témata, osobnost a tak dále.  Vzhledem k tomu, že tato omezení se učí z rozsáhlých vzorů, jsou vhodná mimo jiné pro aplikace pro nezávaznou konverzaci.

![Model sekvence k sekvenci pro generování odpovědí](./media/overview/sequence-to-sequence-model.png)

## <a name="personality-modeling"></a>Modelování osobnosti

 U každého konverzačního modelu založeného na datech je třeba se vypořádat s tím, aby osobnost (charakter) byla bez výkyvů soudržná. OSOBA se definuje jako charakter vnímaný během konverzačních interakcí. Na osobu se můžeme dívat jako na celek vytvořený z prvků identity, jazykového chování a stylu interakce. V aktuální verzi Personality Chatu se zaměřujeme na jazykové chování a styl interakce.

Tento model představuje jednotlivé mluvčí jako vektory nebo zakotvení. Zakóduje informace od mluvčího, které ovlivňují obsah a styl jeho odpovědí. Model se pak učí zastoupení mluvčích na základě konverzačního obsahu od různých mluvčích. Mluvčí s podobnými odpověďmi mívají podobná zakotvení a v prostoru vektoru zabírají pozice blízko u sebe. Tímto způsobem trénovací data mluvčích, kteří jsou v prostoru vektoru blízko sebe, pomáhají modelu mluvčího zvýšit schopnost generalizovat. Model efektivně shlukuje mluvčí, kteří mají podobná zastoupení v prostoru vektoru, a formuje shluk osob s „ID osoby“.

Pro výchozí osoby se používají atributy a kurátorované odpovědi, aby se našel nejbližší odpovídající shluk mluvčích. Tento shluk se pak zvolí jako ID osoby pro každou z výchozích osobností. Neustálé přizpůsobování může probíhat u každého typu osoby tak, že se vezme sada odpovědí robota nebo předpřipravených odpovědí. Pak se vytvářejí konverzace, které přesně emulují osobu tohoto jednotlivce jako lingvistické chování při odpovědi a další hlavní charakteristické rysy.

![Modelování osoby pomocí shluků mluvčích](./media/overview/persona-modeling.png)

## <a name="small-talk-intent-understanding"></a>Porozumění záměru nezávazné konverzace

Personality Chat má klasifikátory záměru, které mají zajistit odpověď na záměry nezávazné konverzace. Tyto klasifikátory nebudou bránit úlohám nebo dotazům na informace. Klasifikátor konverzace vysoké úrovně určuje, jestli je dotaz záměrem nezávazné konverzace nebo společenské konverzace. Rozhoduje se pomocí lexikálních a sémantických klasifikátorů a kombinováním jejich skóre. Tyto záměry se učí pomocí konverzačních dat (jako kladné ukázky záměru) a dotazů na hledání/úlohy (pro záporné ukázky záměru).

Další klasifikátory dílčích záměrů slouží k identifikaci podtříd nezávazné konverzace, aby se daly vymezit typy nezávazné konverzace, na které služba reaguje, například: pozdravy, lichotky, názory a tak dále. Tyto klasifikátory hloubkového učení pomocí konvolučního sémantického modelu s hloubkovou strukturou (CDSSM) převádějí všechny dotazy na vektory. Jsou natrénované pomocí desítek tisíc kurátorovaných dotazů na dílčí záměry. Klasifikátor pak vytvoří shodu s dotazem s existujícími, identifikovanými třídami záměrů po nalezení nejbližší shody v prostoru vektoru.

Přidali jsme celou řadu kontrolních mechanismů, které mají zabránit nežádoucím odpovědím na základě znalostí z inteligentních agentů jako Zo. Ve výchozím nastavení je Project Personality Chat nastavený tak, aby odpovídal výhradně na rozpoznané záměry uživatele. Je vhodné si otestovat, jestli je Project Personality Chat vhodný pro vaše podmínky. Vaše zpětná vazba je vítaná, pokud si všimnete něčeho, co je potřeba se více naučit.
