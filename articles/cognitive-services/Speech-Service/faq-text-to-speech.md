---
title: Nejčastější dotazy k převodu textu na řeč
titleSuffix: Azure Cognitive Services
description: Získejte odpovědi na nejčastější dotazy týkající se služby Převod textu na řeč.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 19b8be83a3678164197ec0650b07091e941a04d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110505"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Nejčastější dotazy k převodu textu na řeč

Pokud v těchto nejčastějších dotazech nemůžete najít odpovědi na své otázky, podívejte se na [další možnosti podpory](support.md).

## <a name="general"></a>Obecné

**Otázka: Jaký je rozdíl mezi standardním hlasovým modelem a vlastním hlasovým modelem?**

**A**: Standardní hlasový model (také nazývaný _hlasové písmo_) byl trénován pomocí dat vlastněných společností Microsoft a je již nasazen v cloudu. Vlastní hlasový model můžete použít buď k přizpůsobení průměrného modelu a přenosu zabarvení a vyjádření hlasového stylu mluvčího, nebo trénování úplného nového modelu na základě trénovacích dat připravených uživatelem. Dnes, stále více a více zákazníků chce jeden-z-a-druhu, značkové hlas pro své roboty. Vlastní platforma pro vytváření hlasu je pro tuto možnost tou správnou volbou.

**Otázka: Kde mám začít, pokud chci použít standardní hlasový model?**

**A**: Více než 80 standardních hlasových modelů ve více než 45 jazycích je k dispozici prostřednictvím požadavků HTTP. Nejprve získejte [klíč předplatného](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started). Chcete-li volání REST na předem nasazené hlasové modely, naleznete v [tématu ROZHRANÍ REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

**Otázka: Pokud chci použít přizpůsobený hlasový model, je rozhraní API stejné jako rozhraní API, které se používá pro standardní hlasy?**

**A**: Při vytvoření a nasazení vlastního hlasového modelu získáte jedinečný koncový bod pro váš model. Chcete-li použít hlas mluvit ve vašich aplikacích, musíte zadat koncový bod v požadavcích HTTP. Stejné funkce, které jsou k dispozici v rozhraní REST API pro službu převod textu na řeč je k dispozici pro vlastní koncový bod. Přečtěte si, jak [vytvořit a používat vlastní koncový bod](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-voice-endpoint).

**Otázka: Musím připravit trénovací data pro vlastní vytvoření vlastních hlasových modelů?**

**A:** Ano, musíte připravit trénovací data sami pro vlastní hlasový model.

K vytvoření přizpůsobeného hlasového modelu je vyžadována kolekce řečových dat. Tato kolekce se skládá ze sady zvukových souborů hlasových nahrávek a textového souboru přepisu každého zvukového souboru. Výsledek vašeho digitálního hlasu do značné míry závisí na kvalitě vašich tréninkových dat. Chcete-li vytvořit dobrý hlas mezi převody textu na řeč, je důležité, aby nahrávky byly pořízeny v tiché místnosti s vysoce kvalitním stojícím mikrofonem. Konzistentní objem, rychlost mluvení a řečnická hřiště, a dokonce i konzistence v expresivních manýrách řeči jsou nezbytné pro budování skvělého digitálního hlasu. Důrazně doporučujeme nahrávat hlasy v nahrávacím studiu.

V současné době neposkytujeme podporu online nahrávání ani nemáme žádná doporučení nahrávacího studia. Požadavek na formát naleznete v tématu [příprava nahrávek a přepisů](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice-create-voice).

**Otázka: Jaké skripty mám použít k záznamu řečových dat pro vlastní hlasové školení?**

**A**: Neomezujeme skripty pro nahrávání hlasu. K záznamu řeči můžete použít vlastní skripty. Jen se ujistěte, že máte dostatečné fonetické pokrytí v datech řeči. Chcete-li trénovat vlastní hlas, můžete začít s malým objemem řečových dat, což může být 50 různých vět (asi 3-5 minut řeči). Čím více dat poskytnete, tím přirozenější bude váš hlas. Můžete začít trénovat plné hlasové písmo, když zadáte nahrávky více než 2000 vět (asi 3-4 hodiny řeči). Chcete-li získat vysoce kvalitní, plný hlas, musíte připravit nahrávky více než 6 000 vět (asi 8-10 hodin řeči).

Poskytujeme další služby, které vám pomohou připravit skripty pro nahrávání. Obraťte [se na zákaznickou podporu Custom Voice](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) pro dotazy.

**Otázka: Co když potřebuji vyšší souběžnost než výchozí hodnotu nebo co je nabízeno na portálu?**

**A**: Můžete vertikálně navýšit kapacitu modelu v krocích po 20 souběžných požadavků. Obraťte [se na zákaznickou podporu custom voice](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) s dotazy týkajícími se vyššíškálování.

**Otázka: Mohu stáhnout model a spustit jej místně?**

**A**: Modely nelze stáhnout a spustit místně.

**Otázka: Jsou mé požadavky omezeny?**

**A**: Rozhraní REST API omezuje požadavky na 25 za 5 sekund. Podrobnosti naleznete na našich stránkách pro [text na řeč](text-to-speech.md).

## <a name="next-steps"></a>Další kroky

- [Řešení potíží](troubleshooting.md)
- [Poznámky k verzi](releasenotes.md)
