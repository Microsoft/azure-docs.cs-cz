---
title: Transformace a pracovní místa v mediálních službách
titleSuffix: Azure Media Services
description: Přečtěte si, jak vytvořit transformace popisující pravidla pro zpracování videí ve službě Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: ab99b974aed6f8cd5e1da2ee9b427f593b405889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73571243"
---
# <a name="transforms-and-jobs-in-media-services"></a>Transformace a pracovní místa v mediálních službách

Toto téma obsahuje podrobnosti o transformace [a úlohy](https://docs.microsoft.com/rest/api/media/jobs) a vysvětluje vztah mezi těmito entitami. [Transforms](https://docs.microsoft.com/rest/api/media/transforms)

## <a name="overview"></a>Přehled

### <a name="transformsjobs-workflow"></a>Pracovní postup Transformace/úlohy

Následující diagram znázorňuje pracovní postup transformace/úlohy:

![Pracovní postupy transformace a úlohy ve službě Azure Media Services](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Typický pracovní postup

1. Vytvořte transformaci.
2. Odeslat úlohy v rámci této transformace.
3. Seznam transformací.
4. Odstraňte transformaci, pokud neplánujete ji použít v budoucnu.

#### <a name="example"></a>Příklad

Předpokládejme, že jste chtěli extrahovat první snímek všech vašich videí jako miniaturu - kroky, které byste podnikli, jsou:

1. Definujte recept nebo pravidlo pro zpracování videí: "Použijte první snímek videa jako miniaturu".
2. U každého videa byste službě řekli:
    1. Kde najít to video.
    2. Kde napsat miniaturu výstupu.

**Transformace** vám pomůže vytvořit recept jednou (krok 1) a odeslat úlohy pomocí tohoto receptu (krok 2).

> [!NOTE]
> Vlastnosti **transformace** a **úlohy** typu Datetime jsou vždy ve formátu UTC.

## <a name="transforms"></a>Transformace

Transformace slouží ke **konfiguraci běžných** úloh pro kódování nebo analýzu videí. Každá **transformace** popisuje recept nebo pracovní postup úloh pro zpracování video nebo zvukových souborů. Jedna transformace může použít více než jedno pravidlo. Transformace může například určit, že každé video bude zakódováno do souboru MP4 s daným přenosovým tokem a že miniatura bude generována z prvního snímku videa. Byste přidat jednu položku TransformOutput pro každé pravidlo, které chcete zahrnout do transformace. Přednastavení slouží k tomu, abyste transformovat transformace, jak by měly být zpracovány vstupní mediální soubory.

### <a name="viewing-schema"></a>Zobrazení schématu

Ve službě Media Services v3 jsou přednastavení entitami silného typu v samotném rozhraní API. Definici schématu pro tyto objekty naleznete ve [specifikaci open api (nebo Swagger).](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) Můžete také zobrazit přednastavené definice (například **StandardEncoderPreset)** v [rozhraní REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)nebo jiné referenční dokumentaci sady Media Services v3 SDK.

### <a name="creating-transforms"></a>Vytváření transformací

Transformace můžete vytvořit pomocí rest, CLI nebo některé z publikovaných sad SDK. Rozhraní API Media Services v3 je řízeno Správcem prostředků Azure, takže můžete také použít šablony Správce prostředků k vytvoření a nasazení transformací v účtu Mediálních služeb. Řízení přístupu na základě rolí lze uzamknout přístup k transformace.

### <a name="updating-transforms"></a>Aktualizace transformací

Pokud potřebujete aktualizovat [transformaci](https://docs.microsoft.com/rest/api/media/transforms), použijte operaci **Aktualizace.** Je určen pro provádění změn v popisu nebo priority základní TransformOutputs. Doporučujeme, aby tyto aktualizace byly provedeny po dokončení všech probíhajících úloh. Pokud máte v úmyslu přepsat recept, musíte vytvořit novou transformaci.

### <a name="transform-object-diagram"></a>Transformovat objektový diagram

Následující diagram znázorňuje objekt **Transformace** a objekty, na které odkazuje, včetně vztahů odvození. Šedé šipky zobrazují typ, na který pracovní pozice odkazuje, a zelené šipky zobrazují vztahy odvození třídy.

Vyberte obrázek, chcete-li zobrazit jeho plnou velikost.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a>

## <a name="jobs"></a>Úlohy

**Úloha** je skutečný požadavek na mediální služby použít **transformace** na daný vstupní video nebo zvukový obsah. Po vytvoření transformace můžete odeslat úlohy pomocí api mediálních služeb nebo libovolné z publikovaných sad SDK. **Úloha** určuje informace, jako je umístění vstupního videa a umístění výstupu. Umístění vstupního videa můžete určit pomocí: ADRESY URL HTTPS, adresy URL SAS nebo [datové zdroje](https://docs.microsoft.com/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Vstup úlohy z protokolu HTTPS

[Vstup úlohy z protokolu HTTPS](job-input-from-http-how-to.md) použijte, pokud je váš obsah už přístupný přes adresu URL a nemusíte ukládat zdrojový soubor v Azure (například import z S3). Tato metoda je také vhodná, pokud máte obsah v úložišti objektů Blob Azure, ale nemáte potřebu souboru být v asset. V současné době tato metoda podporuje pouze jeden soubor pro vstup.

### <a name="asset-as-job-input"></a>Datový zdroj jako vstup úlohy

Asset použijte [jako vstup úlohy,](job-input-from-local-file-how-to.md) pokud je vstupní obsah již v assetu nebo je obsah uložen v místním souboru. Je to také dobrá volba, pokud máte v plánu publikovat vstupní datový zdroj pro streamování nebo stahování (řekněme, že chcete publikovat mp4 ke stažení, ale také chcete dělat řeč na text nebo detekci obličeje). Tato metoda podporuje vícesouborové datové zdroje (například sady streamování MBR, které byly kódovány místně).

### <a name="checking-job-progress"></a>Kontrola průběhu úlohy

Průběh a stav úloh lze získat sledováním událostí pomocí programu Event Grid. Další informace naleznete v tématu [Sledování událostí pomocí eventgridu](job-state-events-cli-how-to.md).

### <a name="updating-jobs"></a>Aktualizace úloh

Operaci Aktualizace entity [Úloha](https://docs.microsoft.com/rest/api/media/jobs) lze použít k úpravě *popisu* a vlastností *priority* po odeslání úlohy. Změna vlastnosti *priority* je účinná pouze v případě, že úloha je stále ve stavu ve frontě. Pokud úloha začala zpracovávat nebo byla dokončena, změna priority nemá žádný vliv.

### <a name="job-object-diagram"></a>Diagram objektu úlohy

Následující diagram znázorňuje objekt **Job** a objekty, na které odkazuje, včetně vztahů odvození.

Kliknutím na obrázek zobrazíte jeho plnou velikost.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a>

## <a name="configure-media-reserved-units"></a>Konfigurace rezervovaných jednotek médií

Pro úlohy analýzy zvuku a analýzy videa, které jsou spuštěny media services v3 nebo Video Indexer, je důrazně doporučeno zřídit svůj účet s 10 S3 Media rezervované jednotky (MRU). Pokud potřebujete více než 10 OBJEKTŮ MRU S3, otevřete lístek podpory pomocí [portálu Azure](https://portal.azure.com/).

Podrobnosti naleznete v [tématu Škálování zpracování médií pomocí cli](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Ptejte se, podávejte zpětnou vazbu, získejte aktualizace

Podívejte se na článek [komunity Mediálních služeb Azure](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, poskytovat zpětnou vazbu a získat aktualizace o mediálních službách.

## <a name="see-also"></a>Viz také

* [Kódy chyb](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Filtrování, řazení, stránkování entit Mediálních služeb](entities-overview.md)

## <a name="next-steps"></a>Další kroky

- Než začnete vyvíjet, [přečtěte si vývoj s media services v3 API](media-services-apis-overview.md) (obsahuje informace o přístupu k api, konvence pojmenování, atd.)
- Podívejte se na tyto výukové programy:

    - [Kurz: Zakódujte vzdálený soubor na základě adresy URL a streamujte video](stream-files-tutorial-with-rest.md)
    - [Kurz: Nahrávání, kódování a streamování videí](stream-files-tutorial-with-api.md)
    - [Kurz: Analýza videí pomocí mediálních služeb v3](analyze-videos-tutorial-with-api.md)
