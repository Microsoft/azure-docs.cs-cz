---
title: Víc vstupních souborů a vlastností komponenty s kodérem Premium – Azure | Microsoft Docs
description: V tomto tématu se dozvíte, jak používat setRuntimeProperties k použití více vstupních souborů a předání vlastních dat do procesoru Media Encoder Premium Workflowového média.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: 7fb35bdd-9891-4401-a65b-ef3cc8190e8a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: xpouyat
ms.reviewer: anilmur;juliako
ms.openlocfilehash: 27bdf82d4515678e28eadf07fe325860fe5df063
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "69015441"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Použití více vstupních souborů a vlastností komponenty s kodérem Premium
## <a name="overview"></a>Přehled
Existují scénáře, ve kterých může být nutné přizpůsobit vlastnosti komponenty, určit obsah XML seznamu klipů nebo odeslat více vstupních souborů, když odešlete úlohu s procesorem **Media Encoder Premium Workflow** Media. Tady je několik příkladů:

* Překrytí textu na video a nastavení textové hodnoty (například aktuální datum) za běhu pro každé vstupní video.
* Přizpůsobení souboru XML seznamu klipů (Chcete-li určit jeden nebo několik zdrojových souborů s oříznutím nebo bez něj atd.)
* Překrytí obrázku loga na vstupním videu při kódování videa.
* Kódování více zvukového jazyka.

Aby **Media Encoder Premium Workflow** věděli, že měníte některé vlastnosti v pracovním postupu při vytváření úlohy nebo odesílání více vstupních souborů, musíte použít konfigurační řetězec, který obsahuje **setRuntimeProperties** a/nebo  **transcodeSource**. V tomto tématu se dozvíte, jak je používat.

## <a name="configuration-string-syntax"></a>Syntaxe konfiguračního řetězce
Konfigurační řetězec, který má být nastaven v úloze kódování, používá dokument XML, který vypadá takto:

```xml
<?xml version="1.0" encoding="utf-8"?>
<transcodeRequest>
  <transcodeSource>
  </transcodeSource>
  <setRuntimeProperties>
    <property propertyPath="Media File Input/filename" value="VideoFileName.mp4" />
  </setRuntimeProperties>
</transcodeRequest>
```

Následuje C# kód, který čte konfiguraci XML ze souboru, aktualizuje ho pomocí správného souborového souboru videa a předá ho úloze v úloze:

```csharp
string premiumConfiguration = ReadAllText(@"D:\home\site\wwwroot\Presets\SetRuntime.xml").Replace("VideoFileName", myVideoFileName);

// Declare a new job.
IJob job = _context.Jobs.Create("Premium Workflow encoding job");

// Get a media processor reference, and pass to it the name of the 
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

// Create a task with the encoding details, using a string preset.
ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                              processor,
                              premiumConfiguration,
                              TaskOptions.None);

// Specify the input assets
task.InputAssets.Add(workflow); // workflow asset
task.InputAssets.Add(video); // video asset with multiple files

// Add an output asset to contain the results of the job. 
// This output is specified as AssetCreationOptions.None, which 
// means the output asset is not encrypted. 
task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
```

## <a name="customizing-component-properties"></a>Přizpůsobení vlastností komponenty
### <a name="property-with-a-simple-value"></a>Vlastnost s jednoduchou hodnotou
V některých případech je vhodné přizpůsobit vlastnost komponenty spolu se souborem pracovního postupu, který se spustí Media Encoder Premium Workflow.

Předpokládejme, že jste navrhli pracovní postup, který překrývá text na vašich videích, a text (například aktuální datum) by měl být nastaven za běhu. Můžete to provést tak, že odešlete text, který se má nastavit jako nová hodnota vlastnosti text pro překrývající komponentu z úlohy kódování. Tento mechanismus můžete použít ke změně dalších vlastností komponenty v pracovním postupu (například pozice nebo Barva překrytí, přenosové rychlosti kodéru AVC atd.).

**setRuntimeProperties** se používá k přepsání vlastnosti v součástech pracovního postupu.

Příklad:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
      <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
  </setRuntimeProperties>
</transcodeRequest>
```

### <a name="property-with-an-xml-value"></a>Vlastnost s hodnotou XML
Chcete-li nastavit vlastnost, která očekává hodnotu XML, zapouzdřte `<![CDATA[ and ]]>`pomocí.

Příklad:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

> [!NOTE]
> Ujistěte se, že nezadáte návratový znak hned po `<![CDATA[`.

### <a name="propertypath-value"></a>hodnota propertyPath
V předchozích příkladech byl propertyPath "/Media File Input/filename" nebo "/inactiveTimeout" nebo "clipListXml".
To je obecně název součásti a pak název vlastnosti. Cesta může mít více nebo méně úrovní, například "/primarySourceFile" (protože tato vlastnost je v kořenovém adresáři pracovního postupu) nebo "/video zpracování/překrytí/krytí" (protože překryv je ve skupině).    

Chcete-li zjistit název cesty a vlastnosti, použijte tlačítko akce, které je okamžitě vedle každé vlastnosti. Můžete kliknout na tlačítko akce a vybrat **Upravit**. Tím se zobrazí skutečný název vlastnosti a hned nad ní, obor názvů.

![Akce/úpravy](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Vlastnost](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Více vstupních souborů
Každý úkol, který odešlete do **Media Encoder Premium Workflow** , vyžaduje dva prostředky:

* První z nich je *prostředek pracovního postupu* , který obsahuje soubor pracovního postupu. Soubory pracovních postupů můžete navrhovat pomocí [Návrhář postupu provádění](media-services-workflow-designer.md).
* Druhým z nich je *mediální prostředek* , který obsahuje mediální soubory, které chcete kódovat.

Když odesíláte více mediálních souborů do **Media Encoder Premium Workflow** kodéru, platí následující omezení:

* Všechny mediální soubory musí být ve stejném mediálním *prostředku*. Používání více mediálních prostředků se nepodporuje.
* V tomto datovém médiu musíte nastavit primární soubor (v ideálním případě jde o hlavní videosoubor, který kodér požaduje k zpracování).
* Je nutné předat konfigurační data zahrnující **setRuntimeProperties** a/nebo **transcodeSource** element do procesoru.
  * **setRuntimeProperties** se používá k přepsání vlastnosti FileName nebo jiné vlastnosti v součástech pracovního postupu.
  * **transcodeSource** se používá k určení obsahu XML seznamu klipů.

Připojení v pracovním postupu:

* Pokud použijete jednu nebo několik komponent vstupního souboru médií a naplánujete použití **setRuntimeProperties** k zadání názvu souboru, nepřipojujte k nim kód primární součásti souboru. Ujistěte se, že mezi objektem primárního souboru a vstupním souborem média není žádné připojení.
* Pokud dáváte přednost použití prvku Clip list XML a jedné zdrojové komponenty médií, můžete společně propojit oba.

![Nepřipojeno k vstupu z primárního zdrojového souboru do mediálního souboru](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Pokud použijete setRuntimeProperties k nastavení vlastnosti FileName, neexistuje žádné připojení z primárního souboru k komponentám vstupního souboru médií.*

![Připojení ze seznamu souborů klipů XML ke zdroji seznamu klipů](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Můžete propojit soubor XML seznamu klipů se zdrojem médií a používat transcodeSource.*

### <a name="clip-list-xml-customization"></a>Přizpůsobení XML pro seznam klipů
V pracovním postupu můžete zadat XML seznam klipů za běhu pomocí **transcodeSource** v KONFIGURAČNÍM řetězci XML. To vyžaduje, aby byl kód PIN seznamu klipů připojen ke zdrojové součásti média v pracovním postupu.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <transcodeSource>
      <clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
      </clipList>
    </transcodeSource>
    <setRuntimeProperties>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Chcete-li zadat/primarySourceFile k použití této vlastnosti k pojmenování výstupních souborů pomocí výrazu ' Expressions ', pak doporučujeme předat XML seznam klipů jako vlastnost *po* vlastnosti/primarySourceFile, aby nedošlo k přepsání seznamu klipů nastavení/primarySourceFile

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

S dalším oříznutím přes rám:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Příklad 1: Překrytí obrázku nad videem

### <a name="presentation"></a>Prezentace
Vezměte v úvahu příklad, ve kterém chcete překrýt obrázek loga na vstupním videu, když je video zakódované. V tomto příkladu se vstupní video jmenuje "Microsoft_HoloLens_Possibilities_816p24. mp4" a logo má název "logo. png". Proveďte následující kroky:

* Vytvořte prostředek pracovního postupu pomocí souboru pracovního postupu (viz následující příklad).
* Vytvoření mediálního prostředku, který obsahuje dva soubory: MyInputVideo. mp4 jako primární soubor a MyLogo. png.
* Odešlete úlohu do procesoru Media Encoder Premium Workflow médií pomocí výše uvedených vstupních assetů a zadejte následující konfigurační řetězec.

Konfigurace:

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

V předchozím příkladu je název videosouboru odeslán do komponenty pro vstup mediálního souboru a do vlastnosti primarySourceFile. Název souboru loga se pošle do jiného vstupu mediálního souboru, který je připojený k komponentě překrytí grafiky.

> [!NOTE]
> Název videosouboru se pošle do vlastnosti primarySourceFile. Důvodem je použití této vlastnosti v pracovním postupu pro sestavení správného názvu výstupního souboru pomocí výrazů, například.

### <a name="step-by-step-workflow-creation"></a>Vytváření pracovních postupů krok za krokem
Tady je postup vytvoření pracovního postupu, který jako vstup používá dva soubory: video a obrázek. Obrázek se překryje nad video.

Otevřete **Návrhář postupu provádění** a vyberte **soubor** > **nový pracovní prostor** > **překódování**.

Nový pracovní postup ukazuje tři prvky:

* Primární zdrojový soubor
* XML seznam klipů
* Výstupní soubor/prostředek  

![Nový pracovní postup kódování](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Nový pracovní postup kódování*

Pokud chcete přijmout vstupní soubor média, Začněte přidáním součásti pro zadání mediálního souboru. Chcete-li přidat komponentu do pracovního postupu, vyhledejte ji v poli hledání úložiště a přetáhněte požadovanou položku do podokna návrháře.

Pak přidejte videosoubor, který se má použít k návrhu pracovního postupu. Provedete to tak, že kliknete na podokno pozadí v Návrhář postupu provádění a vyhledáte vlastnost primární zdrojový soubor v podokně vlastností na pravé straně. Klikněte na ikonu složky a vyberte vhodný videosoubor.

![Primární zdroj souborů](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Primární zdroj souborů*

V dalším kroku zadejte videosoubor do komponenty pro zadávání mediálního souboru.   

![Zdroj vstupu mediálního souboru](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Zdroj vstupu mediálního souboru*

Jakmile je to hotové, komponenta vstupu mediálního souboru zkontroluje soubor a naplní jeho výstupní PIN kód tak, aby odrážel soubor, který zkontroloval.

Dalším krokem je přidání aktualizačního datového typu videa, který určuje barevný prostor pro REC. 709. Přidejte "převaděč formátu videa", který je nastaven na možnost rozložení dat/Typ rozložení = konfigurovatelný planární. Tím se datový proud videa převede na formát, který se dá považovat za zdroj překryté komponenty.

![Aktualizační datový typ a převaděč formátu](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Aktualizační datový typ a převaděč formátu*

![Typ rozložení = konfigurovatelný planární](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Typ rozložení je konfigurovatelný planární*

Dále přidejte komponentu překrytí videa a připojte (nekomprimovaný) kód pro video s (nekomprimovaným) videem vstupu mediálního souboru.

Přidejte další vstup mediálního souboru (Pokud chcete načíst soubor loga), klikněte na tuto součást a přejmenujte ji na "logo pro zadání multimediálního souboru" a v vlastnosti soubor vyberte obrázek (například soubor. png). Připojte nekomprimovaný kód PIN obrázku k nekomprimovanému kódu PIN obrázku překrytí.

![Překryvná součást a zdroj souborů obrázku](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Překryvná součást a zdroj souborů obrázku*

Pokud chcete změnit pozici loga na videu (například můžete chtít umístit ho do 10 procent od levého horního rohu videa), zrušte zaškrtnutí políčka ruční vstup. To můžete provést, protože k poskytnutí souboru loga do překryvné komponenty slouží vstupní soubor média.

![Překryvná pozice](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Překryvná pozice*

Ke kódování streamu videa do H. 264 přidejte komponenty kodéru pro video AVC a kodér AAC na plochu návrháře. Připojte PIN kódy.
Nastavte kodér AAC a vyberte možnost Převod/přednastavení formátu zvuku: 2,0 (L, R).

![Audio a video kodéry](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Audio a video kodéry*

Teď přidejte **soubory** multiplexů a výstupů **ISO MPEG-4** a připojte PIN kódy, jak je znázorněno na obrázku.

![Multiplexor MP4 a výstup souboru](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*Multiplexor MP4 a výstup souboru*

Je nutné nastavit název výstupního souboru. Klikněte na součást **výstup souboru** a upravte výraz pro tento soubor:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Název výstupního souboru](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Název výstupního souboru*

Pracovní postup můžete spustit místně a ověřit tak, že funguje správně.

Po dokončení ji můžete spustit v Azure Media Services.

Nejprve Připravte Asset v Azure Media Services se dvěma soubory: video soubor a logo. Můžete to provést pomocí rozhraní .NET nebo REST API. Můžete to provést také pomocí Azure Portal nebo [Azure Media Services Exploreru](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

V tomto kurzu se dozvíte, jak spravovat prostředky pomocí AMSE. Existují dva způsoby, jak přidat soubory do assetu:

* Vytvořte místní složku, zkopírujte do ní dva soubory a přetáhněte ji na kartu **Asset (Asset** ).
* Odeslat videosoubor jako Asset, zobrazit informace o assetu, přejít na kartu soubory a nahrát další soubor (logo).

> [!NOTE]
> Ujistěte se, že jste v assetu (hlavní videosoubor) nastavili primární soubor.

![Soubory prostředků v AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Soubory prostředků v AMSE*

Vyberte Asset a zvolte možnost zakódovat ho pomocí kodéru Premium. Nahrajte pracovní postup a vyberte ho.

Kliknutím na tlačítko předáte data procesoru a přidejte následující kód XML pro nastavení vlastností modulu runtime:

![Kodér úrovně Premium v AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Kodér úrovně Premium v AMSE*

Pak vložte následující data XML. Je nutné zadat název souboru videa pro vstup mediálního souboru i pro primarySourceFile. Zadejte název souboru pro logo.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*

Použijete-li sadu .NET SDK k vytvoření a spuštění úkolu, musí být tato data XML předána jako konfigurační řetězec.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

Po dokončení úlohy zobrazí soubor MP4 v výstupním prostředku překryv.

![Překrytí na video](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Překrytí na video*

Ukázkový pracovní postup si můžete stáhnout z [GitHubu](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Příklad 2: Kódování více jazyků v zvukovém prostředí

V GitHubu je k dispozici příklad více pracovních postupů [](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding)pro kódování zvukového jazyka.

Tato složka obsahuje ukázkový pracovní postup, který lze použít ke kódování souboru MXF na prostředek s více soubory MP4 s více zvukovými stopami.

Tento pracovní postup předpokládá, že soubor MXF obsahuje jednu zvukovou stopu; Další zvukové stopy by se měly předat jako samostatné zvukové soubory (WAV nebo MP4...).

Pro kódování proveďte následující kroky:

* Vytvoří Media Services Asset se souborem MXF a zvukovými soubory (0 až 18 zvukových souborů).
* Ujistěte se, že je soubor MXF nastavený jako primární soubor.
* Vytvořte úlohu a úlohu pomocí procesoru Premium Workflow Encoder. Použijte zadaný pracovní postup (MultiMP4-1080p-19audio-v1. Workflow).
* Předání dat setruntime. XML do úlohy (Pokud používáte Azure Media Services Exploreru, použijte tlačítko "předat data XML do pracovního postupu").
  * Aktualizujte prosím data XML, abyste určili správné názvy souborů a značky jazyků.
  * Pracovní postup obsahuje zvukové komponenty s názvem audio 1 až zvuk 18.
  * RFC5646 se podporuje pro značku jazyka.

```xml
<?xml version="1.0" encoding="utf-16"?>
<transcodeRequest>
  <setRuntimeProperties>
    <property propertyPath="Media File Input Video/filename" value="MainVideo.mxf" />
    <property propertyPath="Language/language_code" value="en" />
    <property propertyPath="/primarySourceFile" value="MainVideo.mxf" />
    <property propertyPath="Audio 1/Media File Input/filename" value="french-audio.wav" />
    <property propertyPath="Audio 1/Language/language_code" value="fr" />
    <property propertyPath="Audio 2/Media File Input/filename" value="german-audio.wav" />
    <property propertyPath="Audio 2/Language/language_code" value="de" />
    <property propertyPath="Audio 3/Media File Input/filename" value="japanese-audio.wav" />
    <property propertyPath="Audio 3/Language/language_code" value="ja" />
  </setRuntimeProperties>
</transcodeRequest>
```

* Kódovaný prostředek bude obsahovat zvukové stopy ve více jazycích a tyto stopy by se měly vybrat v Azure Media Player.

## <a name="see-also"></a>Viz také:
* [Představujeme Premium Encoding v Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Jak používat kódování Premium v Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Kódování obsahu na vyžádání pomocí Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Formáty a kodeky Media Encoderu Premium Workflow](media-services-premium-workflow-encoder-formats.md)
* [Ukázkové soubory pracovního postupu](https://github.com/Azure/azure-media-services-samples)
* [Nástroj Azure Media Services Explorer](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
