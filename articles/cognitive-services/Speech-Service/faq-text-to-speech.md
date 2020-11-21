---
title: Převod textu na řeč nejčastějších dotazech
titleSuffix: Azure Cognitive Services
description: Získejte odpovědi na nejčastější dotazy týkající se služby Převod textu na řeč.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.openlocfilehash: 89ddbca7a216421aad7240890a697f861d14fd1f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026348"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Převod textu na řeč nejčastějších dotazech

Pokud nemůžete najít odpovědi na vaše otázky v těchto nejčastějších dotazech, podívejte se na [Další možnosti podpory](../cognitive-services-support-options.md?context=%252fazure%252fcognitive-services%252fspeech-service%252fcontext%252fcontext%253fcontext%253d%252fazure%252fcognitive-services%252fspeech-service%252fcontext%252fcontext).

## <a name="general"></a>Obecné

**Otázka: Jaký je rozdíl mezi standardním a vlastním modelem hlasu?**

Odpověď **: standardní** hlasový model (označovaný také jako _hlasový Font_) byl vyškolený pomocí dat vlastněných společností Microsoft a je již nasazen v cloudu. Pomocí vlastního modelu hlasu můžete přizpůsobit průměrný model a přenést Timbre a výraz ve stylu hlasu mluvčího nebo naučit plný a nový model založený na školicích datech, která uživatel připravil. Dnes, více a více zákazníků chceme, aby se pro své robotyy vytvářely jeden, značkový hlas. Vlastní platforma pro vytváření hlasu je správná volba pro tuto možnost.

**Otázka: kde se mám začít používat standardní hlasový model?**

**O**: k dispozici jsou více než 80 standardních hlasových modelů ve více než 45 jazycích. Nejprve získejte [klíč předplatného](./overview.md#try-the-speech-service-for-free). Chcete-li provést volání REST pro přednasazené hlasové modely, přečtěte si [REST API](./overview.md#reference-docs).

**Otázka: Pokud chci použít přizpůsobený hlasový model, je rozhraní API stejné jako pro standardní hlasy?**

Odpověď **: když se vytvoří a** nasadí vlastní hlasový model, získáte pro svůj model jedinečný koncový bod. Pokud chcete hlas ve svých aplikacích mluvit pomocí hlasu, musíte v požadavcích HTTP zadat koncový bod. K dispozici jsou stejné funkce, které jsou k dispozici ve REST API pro službu Převod textu na řeč pro váš vlastní koncový bod. Naučte se [vytvářet a používat vlastní koncový bod](./how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint).

**Otázka: Potřebuji připravit školicí data a vytvořit vlastní modely hlasu sami?**

Odpověď **: Ano**, je nutné připravit školicí údaje sami pro vlastní model hlasu.

K vytvoření přizpůsobeného hlasového modelu se vyžaduje kolekce dat řeči. Tato kolekce obsahuje sadu zvukových souborů záznamů řeči a textový soubor přepisu každého zvukového souboru. Výsledek digitálního hlasu se spoléhá na kvalitu školicích dat. Aby bylo možné vytvořit dobrý hlas pro převod textu na řeč, je důležité, aby se nahrávky vytvořily v tiché místnosti s vysoce kvalitním a stálým mikrofonem. Pro sestavování skvělého digitálního hlasu jsou nezbytné konzistentní objemy, míra projevení a rozteč a dokonce i konzistence v mannerisms řeči. Důrazně doporučujeme nahrávat hlasy do aplikace recordation Studio.

V současné době neposkytujeme podporu online nahrávání ani nahrávání doporučení studia. Požadavky na formát najdete v tématu [Příprava nahrávek a přepisů](./how-to-custom-voice-create-voice.md).

**Otázka: Jaké skripty mám použít k nahrání dat řeči pro vlastní hlasové školení?**

**A** Odpověď: neomezujeme skripty pro záznam hlasu. Pro nahrání řeči můžete použít vlastní skripty. Stačí se ujistit, že máte v datech řeči dostatečné pokrytí foneticky. Pro výuku vlastního hlasu můžete začít s malým objemem dat řeči, což může být 50 různých vět (asi 3-5 minut řeči). Další data, která zadáte, bude přirozený váš hlas. V případě, že zadáte nahrávky o více než 2 000 vět (asi 3-4 hodin řeči), můžete začít zajišťovat všechna hlasová písma. Abyste získali vysoce kvalitní a plný hlas, musíte připravit nahrávky o více než 6 000 vět (asi 8-10 hodin řeči).

Poskytujeme další služby, které vám pomůžou připravit skripty pro nahrávání. Kontaktujte [zákaznickou podporu vlastního hlasu](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) pro dotazy.

**Otázka: Co když potřebuji vyšší souběžnost, než je výchozí hodnota nebo co se nabízí na portálu?**

Odpověď **: model** můžete škálovat v přírůstcích po 20 souběžných požadavcích. Kontaktujte [zákaznickou podporu vlastního hlasu](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) pro dotazy týkající se většího škálování.

**Otázka: můžu stáhnout svůj model a spustit ho místně?**

**A**: modely nelze stáhnout a spustit místně.

**Otázka: jsou omezeny moje požadavky?**

**A** Odpověď: viz [kvóty a limity služby Speech Services](speech-services-quotas-and-limits.md).

## <a name="next-steps"></a>Další kroky

- [Řešení potíží](troubleshooting.md)
- [Zpráva k vydání verze](releasenotes.md)