---
title: Nejčastější dotazy k převodu řeči na Text služby v Azure
description: Tady najdete odpovědi na Nejoblíbenější dotazy týkající se převod řeči na Text.
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 8d70c4a359c713d6c5f46423193e9c9e7e1f3baf
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282853"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Převod textu na řeč – nejčastější dotazy

Pokud nemůžete najít odpovědi na své dotazy v tomto dokumentu, přečtěte si další možnosti podpory [tady](support.md).

## <a name="general"></a>Obecné

**Dotaz**: Jaký je rozdíl mezi standardní a vlastní hlasové modely?

**Odpověď**: standardní hlasové modely (označovaný také jako hlasová písma) byly školení s Microsoftem vlastní data a jsou už nasazené v cloudu. Vlastní hlasové modely umožní uživateli buď přizpůsobit průměrné modelu a přenést timbre a způsobem výrazu podle stylu hlasové mluvčího, nebo tak moct trénovat úplný nový model založený na trénovací data připravena uživatelem. Ještě dnes další a další zákazníci chtějí mít jeden z druhu, značkové hlas pro hodnotu své roboty. Vlastní hlasové vytváření platformy je tím správným řešením, která.

**Dotaz**: kdy začnu Pokud chci používat standardní hlasové modelu?

**Odpověď**: více než 80 standardní hlasové modely ve více než 45 jazyků, které jsou k dispozici prostřednictvím požadavky HTTP. Nejdřív je potřeba získat [klíč předplatného](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started). Při volání REST k modelům předem nasazené hlasové, prostudujte [podrobnosti tady](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

**Dotaz**: Pokud chci používat model přizpůsobených hlasových, je rozhraní API stejné jako standardní hlasy?

**Odpověď**: až budete mít vlastní hlasové modelu vytvořili a nasadili, získáte koncový bod jedinečný pro váš model. Je třeba zadat koncový bod v požadavcích HTTP, použití hlasu k mluví ve vašich aplikacích. Stejné funkce, které jsou k dispozici prostřednictvím rozhraní REST API pro převod textu na řeč služby je také k dispozici pro váš vlastní koncový bod. V tématu Jak [vytvořit a používat vlastní koncový bod](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint).

**Dotaz**: muset připravit na vytvoření vlastní hlasové modely na vlastní trénovacích dat?

**Odpověď**: budete muset připravit trénovací data sami. Kolekce dat řeči je potřebný k vytvoření přizpůsobených hlasových modelu. Tato kolekce se skládá z sadu zvukové soubory nahrávek řeči a textový soubor přepis všech zvukových souborů. Výsledek hlasu digitální silně závisí na kvalitu trénovací data. K vytvoření dobré hlasový vstup převod textu na ŘEČ, je důležité, že nahrávky se provádějí v tichém místnosti s vysoce kvalitní stálého mikrofon. Konzistentní svazku mluvený míra mluvený rozteč a dokonce i konzistence v výrazové zkoušky prezentace řeči jsou nezbytné pro vytváření skvělých hlasových digitální. Důrazně doporučujeme, že byste měli mít hlasy zaznamenané v sadě studio záznam.
V tuto chvíli jsme podporu online záznam ani mít žádná doporučení k studio záznam. Požadavek na formátu naleznete v tématu [postup přípravy záznamy a přepisy](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts)
 
**Dotaz**: jaké skripty použít pro nahrávání dat řeči pro školení způsobu vyjadřování vlastní? 

**Odpověď**: jsme neomezují skriptů pro záznam hlasu. Vlastní skripty slouží k zaznamenání řeči. Jenom zkontrolujte, že máte dostatek zapsané ve fonetické pokrytí ve vašich datech řeči. K trénování vlastní hlasové, můžete začít s malý objem dat řeči, které by mohly být 50 různých věty (o 3 až 5 minut řeči). Čím více dat zadáte, tím víc fyzických bude vašeho hlasu, jak. Můžete začít tak moct trénovat úplné hlasového písma, když zadáte záznamy více než 2000 vět (přibližně 3-4 hodin řeči). Pokud chcete získat úplné hlasový vstup vysoce kvalitní, je nutné připravit záznamy více než 6000 vět (přibližně 8 až 10 hodin řeči).  
Zajišťuje další služby, které vám pomohou připravit skripty pro záznam. Kontakt [vlastní hlasové zákaznickou podporu](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) pro dotazy.

**Dotaz**: Co když budu potřebovat vyšší souběžnosti, než je výchozí hodnota nebo co se nabízí na portálu?

**Odpověď**: můžete vertikálně navýšit kapacitu váš model v přírůstcích po 20 souběžných požadavků. Kontakt [vlastní hlasové zákaznickou podporu](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) pro dotazy na vyšší škálování.

**Dotaz**: můžu my model stáhnout a spustit místně?

**Odpověď**: modely nelze stáhnout a spustit místně.

## <a name="next-steps"></a>Další postup

* [Řešení potíží](troubleshooting.md)
* [Poznámky k verzi](releasenotes.md)
