---
title: Nejčastější dotazy týkající se převod textu na řeč služby v Azure
description: Získejte odpovědi na oblíbené otázky týkající se převod textu na řeč služby.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 2068bcb796b660847d0ea9b1126e64a9003ca871
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576846"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Převod textu na řeč – nejčastější dotazy

Pokud nemůžete najít odpovědi na své dotazy v tomto dokumentu, projděte si [další možnosti podpory](support.md).

## <a name="general"></a>Obecné

**D: Jaký je rozdíl mezi standardní hlasové model a model vlastní hlasové?**

**A**: model standardní hlasové (také nazývané *hlasového písma*) vyškolila pomocí dat ve vlastnictví společnosti Microsoft a je už nasazená v cloudu. Přizpůsobení průměrné modelu a přenos timbre a výraz stylu tohoto mluvčího hlasu nebo trénování úplné, nový model založený na trénovací data připravena uživatelem můžete použít vlastní hlasové modelu. V současné době více zákazníci chtějí značky, jeden z druhu hlasový vstup pro své roboty. Vlastní hlasové sestavuje se platforma je tou správnou volbou pro tuto možnost.

**Otázka: kde můžu začít Pokud chci používat standardní hlasové modelu?**

**A**: více než 80 standardní hlasové modely ve více než 45 jazyků, které jsou k dispozici prostřednictvím požadavky HTTP. Nejprve získejte [klíč předplatného](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started). Pro volání REST k modelům predeployed hlasové, najdete v článku [rozhraní REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

**Otázka: Pokud chci používat model přizpůsobených hlasových, má rozhraní API stejný jako ten, který se používá pro standardní hlasy?**

**A**: při vlastní hlasové modelu se vytvoří a nasadí, získáte koncový bod jedinečný pro váš model. Mluví ve svých aplikacích pomocí hlasu, je nutné zadat koncový bod v požadavcích HTTP. Stejné funkce, která je k dispozici v rozhraní REST API pro převod textu na řeč služby je k dispozici pro váš vlastní koncový bod. Zjistěte, jak [vytvořit a používat vlastní koncový bod](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint).

**Otázka: je potřeba připravit trénovací data k vytvoření vlastní hlasové modely samostatně?**

**A**: Ano, musíte připravit trénovací data sami vlastní hlasové modelu.

Kolekce dat řeči je potřebný k vytvoření přizpůsobených hlasových modelu. Tato kolekce se skládá z sadu zvukové soubory nahrávek řeči a textový soubor přepis všech zvukových souborů. Výsledek hlasu digitální spoléhá na kvalitu trénovací data. K vytvoření dobré řeč, je důležité, že nahrávky jsou provedeny v tichém místnosti s mikrofonem stálého vysoce kvalitní. Svazku konzistentní vzhledem k aplikacím mluvící rychlost a mluvený rozteč a dokonce i konzistence v výrazové zkoušky prezentace řeči jsou nezbytné pro vytváření skvělých hlasových digitální. Důrazně doporučujeme záznam hlasů v sadě studio záznam.

V současné době není poskytneme podporu online záznam nebo jste všechna doporučení studio záznam. Požadavek na formátu naleznete v tématu [postup přípravy záznamy a přepisy](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts).

**Otázka: které skripty používají k zaznamenání dat řeči pro školení způsobu vyjadřování vlastní?**

**A**: skripty pro záznam hlasu není omezený. Vlastní skripty slouží k zaznamenání řeči. Jenom zkontrolujte, že máte dostatek zapsané ve fonetické pokrytí ve vašich datech řeči. K trénování vlastní hlasové, může začínat malý objem dat řeči, což může být jiný věty 50 (přibližně 3 až 5 minut řeči). Čím více dat zadáte, tím víc fyzických bude vašeho hlasu, jak. Můžete začít tak moct trénovat úplné hlasového písma, když zadáte záznamy více než 2 000 vět (přibližně 3-4 hodin řeči). Chcete-li získat vysoce kvalitní, úplné hlas, musíte připravit záznamy více než 6 000 vět (přibližně 8 až 10 hodin řeči).

Zajišťuje další služby, které vám pomohou připravit skripty pro záznam. Kontakt [vlastní hlasové zákaznickou podporu](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) pro dotazy.

**Otázka: Co když budu potřebovat vyšší souběžnosti než výchozí hodnotu nebo co se nabízí na portálu?**

**A**: můžete vertikálně navýšit kapacitu váš model v přírůstcích po 20 souběžných požadavků. Kontakt [vlastní hlasové zákaznickou podporu](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) pro dotazy týkající se škálování na vyšší.

**Dotaz: lze stáhnout Moje modelu a spustit místně?**

**A**: modely nelze stáhnout a spustit místně.

**Otázka: jsou moje žádosti o omezený?**

**A**: rozhraní REST API omezuje požadavky na 25 na 5 sekund. Podrobnosti najdete na naší stránce pro [převod textu na řeč](text-to-speech.md). 

## <a name="next-steps"></a>Další postup

* [Řešení potíží](troubleshooting.md)
* [Poznámky k verzi](releasenotes.md)
