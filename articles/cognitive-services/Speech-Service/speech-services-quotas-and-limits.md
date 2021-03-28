---
title: Kvóty a omezení služeb Speech
titleSuffix: Azure Cognitive Services
description: Rychlá reference, podrobný popis a osvědčené postupy pro kvóty a omezení služby Speech pro rozpoznávání řeči v Azure
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/27/2021
ms.author: alexeyo
ms.openlocfilehash: 7fe2942250e165606175392d7749becda0cff23b
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645113"
---
# <a name="speech-services-quotas-and-limits"></a>Kvóty a omezení služeb Speech

Tento článek obsahuje stručný přehled a **podrobný popis** kvót a omezení pro rozpoznávání řeči v Azure pro všechny [cenové úrovně](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/). Obsahuje taky některé osvědčené postupy, které brání omezení požadavků. 

## <a name="quotas-and-limits-quick-reference"></a>Rychlé reference k kvótám a omezením
Přejít k [kvótám a omezením pro převod textu na řeč](#text-to-speech-quotas-and-limits-per-speech-resource)
### <a name="speech-to-text-quotas-and-limits-per-speech-resource"></a>Kvóty a omezení pro Převod řeči na text pro prostředek služby Speech
V tabulkách **níže jsou parametry bez "měnitelného** " řádku pro všechny cenové úrovně nastavitelované.

#### <a name="online-transcription"></a>Online přepis
Pro použití s [funkcí Speech SDK](speech-sdk.md) a/nebo [Speech-to text REST API pro krátký zvuk](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio).

| Kvóta | Zdarma (F0)<sup>1</sup> | Standardní (S0) |
|--|--|--|
| **Limit souběžných požadavků – základní model** | 1 | 100 (výchozí hodnota) |
| Měnitelný | Ne<sup>2</sup> | Ano<sup>2</sup> |
| **Limit souběžných požadavků – vlastní model** | 1 | 20 (výchozí hodnota) |
| Měnitelný | Ne<sup>2</sup> | Ano<sup>2</sup> |

#### <a name="batch-transcription"></a>Přepis Batch
| Kvóta | Zdarma (F0)<sup>1</sup> | Standardní (S0) |
|--|--|--|
| Omezení převodu [řeči na text REST API v 2.0 a v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | Batch přepis není pro F0 k dispozici. | 300 požadavků za minutu |
| Maximální velikost zvukového vstupního souboru | – | 1 GB |
| Maximální velikost vstupního objektu BLOB (může obsahovat více než jeden soubor, například v archivu zip, nezapomeňte si uvědomit výše uvedený limit velikosti souboru) | – | 2,5 GB |
| Maximální velikost kontejneru objektů BLOB | – | 5 GB |
| Maximální počet objektů blob na kontejner | – | 10000 |
| Maximální počet souborů na žádost o přepis (při použití více adres URL obsahu jako vstupu) | – | 1000  |
| Maximální počet souběžně spuštěných úloh | – | 2000  |

#### <a name="model-customization"></a>Přizpůsobení modelu
| Kvóta | Zdarma (F0)<sup>1</sup> | Standardní (S0) |
|--|--|--|
| Omezení REST API | 300 požadavků za minutu | 300 požadavků za minutu |
| Maximální počet datových sad pro řeč | 2 | 500 |
| Maximální velikost zvukové datové sady pro import dat | 2 GB | 2 GB |
| Maximální velikost souboru datové sady pro import dat | 200 MB | 1,5 GB |
| Maximální velikost souboru datové sady výslovnosti pro import dat | 1 kB | 1 MB |
| Maximální velikost textu při použití `text` parametru v žádosti o [Vytvoření](https://westcentralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateModel/) rozhraní API modelu | 200 KB | 500 kB |

<sup>1</sup> **bezplatná (F0)** cenová úroveň najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)také měsíční náhrady.<br/>
<sup>2</sup> . [Další](#detailed-description-quota-adjustment-and-best-practices)informace najdete v tématu vysvětlení, [osvědčené postupy](#general-best-practices-to-mitigate-throttling-during-autoscaling)a [pokyny k úpravám](#speech-to-text-increasing-online-transcription-concurrent-request-limit).<br/> 

### <a name="text-to-speech-quotas-and-limits-per-speech-resource"></a>Kvóty pro převod textu na řeč a omezení na prostředek řeči
V tabulce níže jsou parametry bez řádku " **Nastaviteled** " pro všechny cenové úrovně nastavitelované.

| Kvóta | Zdarma (F0)<sup>3</sup> | Standardní (S0) |
|--|--|--|
| **Maximální počet transakcí za sekundu (TPS) pro hlasy Standard a neuronové** | 200<sup>4</sup> | 200<sup>4</sup> |  |
| **Limit souběžných požadavků pro vlastní hlas** |  |  |
| Výchozí hodnota | 10 | 10 |
| Měnitelný | Č.<sup>5</sup> | Ano<sup>5</sup> |
| **Kvóty specifické pro protokol HTTP** |  |
| Maximální délka zvuku vytvořená na žádost | 10 min | 10 min |
| Maximální počet jedinečných `<voice>` značek v SSML | 50 | 50 |
| **Kvóty specifické pro protokol WebSocket** |  |  |
|Maximální délka zvuku vytvořená pro zapnutí | 10 min | 10 min |
|Maximální velikost zprávy SSML na hodnotu Turn |64 kB |64 kB |
| **Omezení REST API** | 20 požadavků za minutu | 300 požadavků za minutu |


<sup>3</sup> **zdarma (F0)** cenová úroveň najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)také měsíční náhrady.<br/>
<sup>4</sup> Podívejte se na [Další vysvětlení](#detailed-description-quota-adjustment-and-best-practices) a [osvědčené postupy](#general-best-practices-to-mitigate-throttling-during-autoscaling).<br/>
<sup>5</sup> Přečtěte si [Další vysvětlení](#detailed-description-quota-adjustment-and-best-practices), [osvědčené postupy](#general-best-practices-to-mitigate-throttling-during-autoscaling)a [pokyny k úpravám](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice).<br/> 

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>Podrobný popis, úprava kvóty a osvědčené postupy
Před vyžádáním zvýšení kvóty (v případě potřeby) zajistěte, aby bylo nutné. Služba Speech používá technologie automatického škálování k zajištění požadovaných výpočetních prostředků v režimu "na vyžádání" a zároveň má za následek nízké náklady zákazníků tím, že neudržuje nadměrné množství kapacity hardwaru. Pokaždé, když vaše aplikace obdrží kód odpovědi 429 ("moc velký počet požadavků"), zatímco vaše úloha je v rámci definovaných limitů (viz [kvóty a omezení rychlé odkazy](#quotas-and-limits-quick-reference)) nejpravděpodobnější vysvětlení je, že služba je škálovatelná na Vaši poptávku a ještě nedosáhla požadovaného škálování, proto nemá ihned dostatek prostředků na to, aby mohl požadavek zpracovat. Tento stav je obvykle přechodný a neměl by být naposledy dlouhý.

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>Obecné osvědčené postupy pro zmírnění omezení při automatickém škálování
K minimalizaci problémů souvisejících s omezováním (kód odpovědi 429) doporučujeme použít následující postupy:
- Implementace logiky opakování v aplikaci
- Vyhněte se ostrým změnám v úlohách. Postupně zvyšujte zatížení <br/>
*Příklad:* Vaše aplikace používá převod textu na řeč a vaše aktuální zatížení je 5 TPS (transakcí za sekundu). Další sekundu zvýšíte zatížení na 20 TPS (to je čtyřikrát více). Služba okamžitě začne škálovat až do splnění nového zatížení, ale pravděpodobně ji nebude možné provést v rámci druhé, takže některé z těchto požadavků získají kód odpovědi 429.   
- Testování různých vzorů zvýšení zatížení
  - Zobrazit [příklad převodu řeči na text](#speech-to-text-example-of-a-workload-pattern-best-practice)
- Vytváření dalších prostředků řeči ve stejných nebo různých oblastech a distribuce úloh mezi nimi pomocí techniky "kruhové dotazování". To je zvlášť důležité pro parametr převodu **textu na řeč TPS (transakcí za sekundu)** , který je nastavený jako 200 na prostředek řeči a nedá se upravit.  

V dalších oddílech jsou popsány zvláštní případy úpravy kvót.<br/>
Přejděte na [text na řeč. Zvýšení limitu souběžných požadavků na přepis pro vlastní hlas](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice)

### <a name="speech-to-text-increasing-online-transcription-concurrent-request-limit"></a>Převod řeči na text: zvýšení limitu souběžných požadavků online přepisu
Ve výchozím nastavení je počet souběžných požadavků omezený na 20 prostředků na řeč (základní model) nebo na vlastní koncový bod (vlastní model). U cenové úrovně Standard se dá tuto částku zvýšit. Před odesláním žádosti se ujistěte, že jste obeznámeni s materiálem v [této části](#detailed-description-quota-adjustment-and-best-practices) a máte na paměti tyto [osvědčené postupy](#general-best-practices-to-mitigate-throttling-during-autoscaling).

Zvýšení limitu souběžných požadavků nijak neovlivní **přímo vaše** náklady. Služba Speech Services používá "platíte jenom za to, co používáte". Limit definuje, jak vysoké může být služba škálovatelná, než začne omezovat vaše požadavky.

Souběžná omezení požadavků pro **základní** a **vlastní** modely je potřeba upravit **samostatně**.

Hodnota pro souběžný parametr limitu požadavků **se nezobrazuje prostřednictvím** Azure Portal, Command-Linech nástrojů nebo požadavků rozhraní API. Pokud chcete ověřit existující hodnotu, vytvořte žádost o podporu Azure.

>[!NOTE]
>[Kontejnery řeči](speech-container-howto.md) nevyžadují zvýšení limitu souběžných požadavků, protože kontejnery jsou omezené jenom procesory hardwaru, na kterých jsou hostované. Kontejnery řeči ale mají svá vlastní omezení kapacity, která by se měla vzít v úvahu. Přečtěte si otázku *"může vám pomáhat s plánováním kapacity a odhadem nákladů na kontejnery Prem řeči na text?"* z [kontejnerů řeči se Nejčastější dotazy](./speech-container-howto.md).

#### <a name="have-the-required-information-ready"></a>Připravte požadované informace:
- Pro **základní model**:
  - ID prostředku pro rozpoznávání řeči
  - Oblast
- Pro **vlastní model**: 
  - Oblast
  - ID vlastního koncového bodu

- **Jak získat informace (základní model)**:  
  - Přejít na [Azure Portal](https://portal.azure.com/)
  - Vyberte prostředek řeči, pro který chcete zvýšit limit počtu požadavků na souběžnost.
  - Vybrat *vlastnosti* (skupina *pro správu prostředků* ) 
  - Zkopírujte a uložte hodnoty následujících polí:
    - **ID prostředku**
    - **Umístění** (vaše koncová oblast)

- **Jak získat informace (vlastní model)**:
  - Přejít na portál [Speech Studio](https://speech.microsoft.com/)
  - V případě potřeby se přihlaste.
  - Přejít na Custom Speech
  - Vyberte svůj projekt
  - Přejít na *nasazení*
  - Vyberte požadovaný koncový bod.
  - Zkopírujte a uložte hodnoty následujících polí:
    - **Oblast služby** (vaše koncová oblast)
    - **ID koncového bodu**
  
#### <a name="create-and-submit-support-request"></a>Vytvořit a odeslat žádost o podporu
Zahajte zvýšení limitu souběžných požadavků pro váš prostředek nebo v případě potřeby kontrolujte dnešní limit tím, že odešlete žádost o podporu:

- Ujistěte se, že máte [požadované informace](#have-the-required-information-ready) .
- Přejít na [Azure Portal](https://portal.azure.com/)
- Vyberte prostředek řeči, pro který chcete zvýšit (nebo zda chcete kontrolu) limitu požadavků na souběžnost.
- Výběr *nové žádosti o podporu* (*Podpora a skupina řešení potíží* ) 
- Zobrazí se nové okno s automaticky vyplněnými informacemi o vašem předplatném Azure a prostředku Azure.
- Zadejte *Souhrn* (například zvýšení limitu požadavků na STT Concurrency).
- V části *typ problému* vyberte možnost problémy s kvótou nebo předplatným.
- V zobrazeném *podtypu problému* vyberte:
  - "Zvýšení kvóty nebo souběžných požadavků se zvyšuje" – pro žádost o zvýšení
  - "Ověření platnosti kvót nebo využití" pro kontrolu existujícího limitu
- Klikněte na *Další: řešení*
- Další postup při vytváření žádosti
- Do pole *Popis zadejte text* na kartě *Podrobnosti* :
  - Poznámka: žádost o kvótu **pro převod řeči na text**
  - **Základní** nebo **vlastní** model
  - Informace o prostředcích Azure, které jste [shromáždili před](#have-the-required-information-ready) 
  - Dokončete zadávání požadovaných informací a klikněte na tlačítko *vytvořit* v nabídce *Revize + vytvořit* kartu.
  - Poznamenejte si číslo žádosti o podporu v oznámeních Azure Portal. Brzy budete kontaktováni za účelem dalšího zpracování.

### <a name="speech-to-text-example-of-a-workload-pattern-best-practice"></a>Převod řeči na text: příklad nejvhodnějšího postupu pro vzor úlohy
Tento příklad představuje přístup, který vám doporučujeme, aby se omezilo možné omezení požadavků, protože probíhá automatické [škálování](#detailed-description-quota-adjustment-and-best-practices). Nejedná se o "přesný recept", ale pouze šablonu, kterou zveme, abychom mohli postupovat a upravovat podle potřeby.

Řekněme, že prostředek řeči má limit souběžných požadavků nastavený na 300. Spustit úlohu z 20 souběžných připojení a zvýšit zatížení o 20 souběžných připojení každých 1,5 – 2 minuty. Řízení odpovědí služby a implementace logiky, která se vrátí zpět (snižuje zatížení), pokud získáte příliš mnoho kódů odezvy 429. Pak to zkuste znovu s 1-2-4-4 minutovým vzorem. (To znamená, že se zvýšení zatížení zopakuje v 1 minutách, pokud pořád nefunguje, pak za 2 min atd.)

Obecně se doporučuje testovat úlohy a vzory úloh před tím, než budete pokračovat v produkčním prostředí.

### <a name="text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice"></a>Převod textu na řeč: zvýšení limitu počtu souběžných požadavků pro vlastní hlas
Ve výchozím nastavení je počet souběžných požadavků pro vlastní hlasový koncový bod omezený na 10. U cenové úrovně Standard se dá tuto částku zvýšit. Před odesláním žádosti se ujistěte, že jste obeznámeni s materiálem v [této části](#detailed-description-quota-adjustment-and-best-practices) a máte na paměti tyto [osvědčené postupy](#general-best-practices-to-mitigate-throttling-during-autoscaling).

Zvýšení limitu souběžných požadavků nijak neovlivní **přímo vaše** náklady. Služba Speech Services používá "platíte jenom za to, co používáte". Limit definuje, jak vysoké může být služba škálovatelná, než začne omezovat vaše požadavky.

Hodnota pro souběžný parametr limitu požadavků **se nezobrazuje prostřednictvím** Azure Portal, Command-Linech nástrojů nebo požadavků rozhraní API. Pokud chcete ověřit existující hodnotu, vytvořte žádost o podporu Azure.

>[!NOTE]
>[Kontejnery řeči](speech-container-howto.md) nevyžadují zvýšení limitu souběžných požadavků, protože kontejnery jsou omezené jenom procesory hardwaru, na kterých jsou hostované.

#### <a name="prepare-the-required-information"></a>Připravte požadované informace:
Pokud chcete vytvořit žádost o zvýšení, budete muset zadat oblast nasazení a ID vlastního koncového bodu. Pokud ho chcete získat, proveďte následující akce: 

- Přejít na portál [Speech Studio](https://speech.microsoft.com/)
- V případě potřeby se přihlaste.
- Přejít na *vlastní hlas*
- Vyberte svůj projekt
- Přejít na *nasazení*
- Vyberte požadovaný koncový bod.
- Zkopírujte a uložte hodnoty následujících polí:
    - **Oblast služby** (vaše koncová oblast)
    - **ID koncového bodu**
  
#### <a name="create-and-submit-support-request"></a>Vytvořit a odeslat žádost o podporu
Zahajte zvýšení limitu souběžných požadavků pro váš prostředek nebo v případě potřeby kontrolujte dnešní limit tím, že odešlete žádost o podporu:

- Ujistěte se, že máte [požadované informace](#prepare-the-required-information) .
- Přejít na [Azure Portal](https://portal.azure.com/)
- Vyberte prostředek řeči, pro který chcete zvýšit (nebo zda chcete kontrolu) limitu požadavků na souběžnost.
- Výběr *nové žádosti o podporu* (*Podpora a skupina řešení potíží* ) 
- Zobrazí se nové okno s automaticky vyplněnými informacemi o vašem předplatném Azure a prostředku Azure.
- Zadat *Souhrn* (například "zvýšit počet požadavků na souběžnost vlastního koncového bodu TTS")
- V části *typ problému* vyberte možnost problémy s kvótou nebo předplatným.
- V zobrazeném *podtypu problému* vyberte:
  - "Zvýšení kvóty nebo souběžných požadavků se zvyšuje" – pro žádost o zvýšení
  - "Ověření platnosti kvót nebo využití" pro kontrolu existujícího limitu
- Klikněte na *Další: řešení*
- Další postup při vytváření žádosti
- Do pole *Popis zadejte text* na kartě *Podrobnosti* :
  - Poznámka: žádost o kvótu **pro převod textu na řeč**
  - Informace o prostředcích Azure, které jste [shromáždili před](#prepare-the-required-information) 
  - Dokončete zadávání požadovaných informací a klikněte na tlačítko *vytvořit* v nabídce *Revize + vytvořit* kartu.
  - Poznamenejte si číslo žádosti o podporu v oznámeních Azure Portal. Brzy budete kontaktováni za účelem dalšího zpracování.