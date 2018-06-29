---
title: Nejčastější dotazy pro převod řeči na Text služby v Azure | Microsoft Docs
description: Tady najdete odpovědi na otázky nejoblíbenější o převod řeči na Text.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 4a29435c0ace79fc3a5d3a5a42a0e91bdbc8da5e
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082820"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Převod textu na řeč, nejčastější dotazy

Pokud nenajdete odpovědi na otázky v tohoto článku, zkuste žádostí služby řeči vlastní komunity na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) a [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Obecné

**Otázka**: Jaký je rozdíl mezi modely standardní a vlastní hlasové?

**Odpověď**: standardní hlasu modelů (také známa jako hlasové písma) byla trénink na data ve vlastnictví společnosti Microsoft a jsou už nasazené v cloudu. Vlastní hlasové modely povolí uživateli přizpůsobit průměrná modelu a přenést timbre a výraz způsobem podle mluvčího hlasové styl nebo ke cvičení úplné nový model na základě dat školení připravený uživatelem. Dnes více zákazníci chtějí mít značky, jeden z typu hlasu pro jejich robotů. Vlastní hlasové vytváření platformy je správná volba pro tento.

**Otázka**: kde mám začít Pokud chcete používat standardní hlasové modelu?

**Odpověď**: více než 80 standardní hlasové modely v více než 45 jazyky jsou k dispozici prostřednictvím požadavky HTTP. Nejprve je nutné získat [klíč předplatného](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started). Volání REST modely předem nasazené hlasové, najdete [podrobnosti zde](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

**Otázka**: Pokud chcete použít model přizpůsobených hlasových, je rozhraní API stejný jako standardní hlasy?

**Odpověď**: Pokud máte vlastní hlasové modelu, vytvoření a nasazení, zobrazí se koncový bod jedinečný pro váš model. Je třeba zadat koncový bod ve své žádosti HTTP, řeči ve svých aplikacích pomocí hlasu. Stejné funkce, která je k dispozici prostřednictvím rozhraní REST API pro službu Převod textu na řeč je také k dispozici pro svůj vlastní koncový bod. V tématu Jak [vytvořit a používat svůj vlastní koncový bod](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint).

**Otázka**: je nutné připravit Cvičná data pro vytvoření vlastní hlasové modely na vlastní?

**Odpověď**: budete muset připravit Cvičná data sami. Kolekce řečových dat je potřeba vytvořit přizpůsobených hlasových model. Tato kolekce se skládá z sadu zvukových souborů řeči záznamy a textového souboru přepis zvukové soubory. Výsledek hlasu digitální výraznou spoléhá na kvalitu Cvičná data. Chcete-li hlas dobrý převod textu na ŘEČ, je důležité, že záznamy se provádějí v tiché místnosti s mikrofon stálé vysoké kvality. Konzistentní svazku hovořícího míra, hovořícího výšky a i konzistence v výrazovou zkoušky prezentace řeči jsou nezbytné pro vytváření kvalitních digitální hlasu. Důrazně doporučujeme, že byste měli mít hlasy zaznamenaná v studio záznam.
V tuto chvíli nemůžeme není podporují online záznam nebo žádná doporučení studio záznam. Požadavek na formát najdete v části [postup přípravy záznamy a přepisy](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts)
 
**Otázka**: co skripty použít pro záznam dat řeči pro vlastní hlasové školení? 

**Odpověď**: jsme Neomezovat skripty pro záznam hlasu. Zaznamenání řeč, můžete použít vlastní skripty. Jenom zkontrolujte, zda že máte dostatek výslovnosti pokrytí ve vašich datech řeči. Při školení vlastní hlasové, můžete spustit v malém objemu řeči data, která může být 50 různých věty (o 3 až 5 minut řeči). Zadejte další data, tím více fyzických bude váš hlas. Můžete začít cvičení úplné hlasové písmo, když zadáte záznamy více než 2000 vět (přibližně 3 – 4 hodiny řeči). Chcete-li získat úplné hlasu vysoké kvality, je nutné připravit záznamy více než 6000 vět (asi 8-10 hodin řeči).  
Poskytujeme další služby, které vám pomohou připravit skripty pro záznam. Obraťte se na [vlastní hlasové zákaznickou podporu](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) pro dotazy.

**Otázka**: Co když je potřeba souběžnosti vyšší než je výchozí hodnota nebo co je k dispozici v portálu?

**Odpověď**: můžete postupně škálovat modelu v přírůstcích po 20 souběžnými požadavky. Obraťte se na [vlastní hlasové zákaznickou podporu](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) pro dotazy na vyšší škálování.

**Otázka**: lze stáhnout Moje modelu a spustit místně?

**Odpověď**: modely nelze stáhnout a spustit místně.

## <a name="next-steps"></a>Další postup

* [Řešení potíží](troubleshooting.md)
* [Poznámky k verzi](releasenotes.md)
