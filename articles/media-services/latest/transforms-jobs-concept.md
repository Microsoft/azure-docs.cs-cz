---
title: Transformace a úlohy v Azure Media Services | Microsoft Docs
description: Při použití Media Services je třeba vytvořit transformaci, která popisuje pravidla nebo specifikace pro zpracování videí. Tento článek poskytuje přehled o tom, co je transformace a jak ji používat.
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
ms.openlocfilehash: 466ab0737aa5af40bd1bc137b98ab57a48feafde
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637356"
---
# <a name="transforms-and-jobs"></a>Transformace a úlohy

Toto téma obsahuje podrobné informace [](https://docs.microsoft.com/rest/api/media/transforms) o transformacích a [úlohách](https://docs.microsoft.com/rest/api/media/jobs) a vysvětluje vztah mezi těmito entitami. 

## <a name="overview"></a>Přehled 

### <a name="transformsjobs-workflow"></a>Pracovní postup transformace/úloh

Následující diagram znázorňuje pracovní postup transformace/úlohy.

![Transformace](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Typický pracovní postup

1. Vytvořit transformaci 
2. Odeslat úlohy za tuto transformaci 
3. Seznam transformací 
4. Odstraňte transformaci, pokud ji neplánujete použít v budoucnu. 

#### <a name="example"></a>Příklad

Předpokládejme, že jste chtěli extrahovat první snímek všech videí jako miniaturu – kroky, které byste měli provést: 

1. Definujte recept nebo pravidlo pro zpracování videí – "jako miniaturu použijte první snímek videa". 
2. U každého videa byste službu oznámili: 
    1. Kde najít toto video,  
    2. Kam se má zapsat výstupní miniatura obrázku. 

**Transformace** pomáhá vytvořit recept jednou (krok 1) a odesílat úlohy pomocí tohoto návodu (krok 2).

> [!NOTE]
> Vlastnosti **transformace** a **úlohy** , které jsou typu DateTime, jsou vždycky ve formátu UTC.

## <a name="transforms"></a>Transformace

Pomocí **transformací** můžete nakonfigurovat běžné úlohy pro kódování a analýzu videí. Každá **transformace** popisuje recept nebo pracovní postup úloh pro zpracování vašich videosouborů nebo zvukových souborů. Jedna transformace může použít více než jedno pravidlo. Například transformace by mohla určit, že se má každé video kódovat do souboru MP4 v dané přenosové rychlosti a že se má z prvního snímku videa vygenerovat obrázek miniatury. Měli byste přidat jednu položku TransformOutput pro každé pravidlo, které chcete zahrnout do transformace. Pomocí přednastavení můžete sdělit transformaci způsobu zpracování vstupních mediálních souborů.

### <a name="viewing-schema"></a>Zobrazení schématu

V Media Services V3 jsou předvolby silně typované entity v rozhraní API. Definici "schématu" těchto objektů najdete v tématu [Open API Specification (nebo Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Přednastavené definice (například **StandardEncoderPreset**) můžete zobrazit také v [REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (nebo v jiné referenční dokumentaci sady Media Services V3 SDK).

### <a name="creating-transforms"></a>Vytváření transformací

Transformace můžete vytvořit pomocí REST, CLI nebo pomocí kterékoli z publikovaných sad SDK. Rozhraní API Media Services V3 je založené na Azure Resource Manager, takže můžete také použít šablony Správce prostředků k vytváření a nasazování transformací v účtu Media Services. K uzamčení přístupu k transformacím se dá použít řízení přístupu na základě role.

### <a name="updating-transforms"></a>Aktualizace transformací

Pokud potřebujete aktualizovat transformaci, [](https://docs.microsoft.com/rest/api/media/transforms)použijte operaci **aktualizace** . Je určena pro provádění změn v popisu nebo priorit podkladových TransformOutputs. Doporučuje se, aby se tyto aktualizace prováděly po dokončení všech probíhajících úloh. Pokud máte v úmyslu přepsat tento recept, je nutné vytvořit novou transformaci.

### <a name="transform-object-diagram"></a>Graf transformace objektu

Následující diagram znázorňuje **transformační** objekt a objekty, na které odkazuje, včetně odvozených vztahů. Šedé šipky ukazují typ, na který úloha odkazuje, a zelené šipky ukazují vztahy odvození třídy.<br/>Kliknutím na obrázek zobrazíte jeho plnou velikost.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a> 

## <a name="jobs"></a>Úlohy

**Úloha** je skutečný požadavek na Azure Media Services, jak použít transformaci na dané vstupní video nebo zvukový obsah. Po vytvoření transformace můžete odesílat úlohy pomocí rozhraní Media Services API nebo kterékoli z publikovaných sad SDK. **Úloha** určuje informace, jako je například umístění vstupního videa, a umístění výstupu. Umístění vstupního videa můžete určit pomocí: Adresy URL protokolu HTTPS, adresy URL SAS nebo [prostředky](https://docs.microsoft.com/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Vstup úlohy z HTTPS

Pokud je váš obsah již přístupný prostřednictvím adresy URL a nepotřebujete ukládat zdrojový soubor v Azure (například import ze S3), použijte [vstup úlohy z https](job-input-from-http-how-to.md) . Tato metoda je vhodná i v případě, že máte obsah v úložišti objektů BLOB v Azure, ale není potřeba, aby soubor byl v prostředku. V současné době tato metoda podporuje pouze jeden soubor pro vstup.

### <a name="asset-as-job-input"></a>Prostředek jako vstup úlohy

Pokud je vstupní obsah již v prostředku nebo je uložen v místním souboru, použijte [jako vstup úlohy prostředek](job-input-from-local-file-how-to.md) . Je to také dobrá možnost, pokud plánujete publikovat vstupní Asset pro streamování nebo stažení (řekněme, že chcete publikovat MP4 pro stažení, ale také chcete řeč na rozpoznávání textu nebo obličeje). Tato metoda podporuje více souborových prostředků (například sady streamování MBR, které byly zakódované místně).

### <a name="checking-job-progress"></a>Kontrola průběhu úlohy

Průběh a stav úloh lze získat monitorováním událostí pomocí Event Grid. Další informace najdete v tématu [monitorování událostí pomocí EventGrid](job-state-events-cli-how-to.md).

### <a name="updating-jobs"></a>Aktualizace úloh

Operaci aktualizace entity [úlohy](https://docs.microsoft.com/rest/api/media/jobs) lze použít k úpravě *popisu*a vlastností *priority* po odeslání úlohy. Změna vlastnosti *priority* je platná pouze v případě, že je úloha stále ve stavu queueed. Pokud úloha začala zpracovávat nebo skončila, změna priority nemá žádný vliv.

### <a name="job-object-diagram"></a>Diagram objektu úlohy

Následující diagram znázorňuje objekt **úlohy** a objekty, na které odkazuje, včetně odvozených vztahů.<br/>Kliknutím na obrázek zobrazíte jeho plnou velikost.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a> 

## <a name="configure-media-reserved-units"></a>Konfigurace rezervovaných jednotek médií

V případě analýz zvuku a analýz videa, které jsou aktivované Media Services V3 nebo Video Indexer, důrazně doporučujeme zřídit účet s 10 rezervovanými jednotkami médií (MRUs). Pokud potřebujete více než 10 použité položky S3, otevřete lístek podpory pomocí [webu Azure portal](https://portal.azure.com/).

Podrobnosti najdete v tématu [škálování zpracování médií pomocí](media-reserved-units-cli-how-to.md)rozhraní příkazového řádku.

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="see-also"></a>Viz také:

* [Kódy chyb](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Filtrování, řazení, stránkování Media Services entit](entities-overview.md)

## <a name="next-steps"></a>Další postup

- Než začnete s vývojem, přečtěte si téma [vývoj s Media Services V3 API](media-services-apis-overview.md) (obsahuje informace o přístupu k rozhraním API, konvencím pojmenování atd.
- Podívejte se na tyto kurzy:

    - [Kurz: Kódování vzdáleného souboru na základě adresy URL a streamu videa](stream-files-tutorial-with-rest.md)
    - [Kurz: Nahrávání, kódování a streamování videí](stream-files-tutorial-with-api.md)
    - [Kurz: Analýza videí pomocí Media Services V3](analyze-videos-tutorial-with-api.md)
