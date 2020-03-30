---
title: Více vstupních souborů a vlastností komponent s prémiovým kodérem - Azure | Dokumenty společnosti Microsoft
description: Toto téma vysvětluje, jak pomocí funkce setRuntimeProperties používat více vstupních souborů a předávat vlastní data mediálnímu procesoru Media Encoder Premium Workflow.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250995"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Použití více vstupních souborů a vlastností komponent s prémiovým kodérem
## <a name="overview"></a>Přehled
Existují scénáře, ve kterých může být nutné přizpůsobit vlastnosti komponenty, zadat obsah XML seznamu klipů nebo odeslat více vstupních souborů při odeslání úlohy pomocí **mediálního procesoru Media Encoder Premium Workflow.** Tady je několik příkladů:

* Překrytí textu na videu a nastavení textové hodnoty (například aktuální datum) za běhu pro každé vstupní video.
* Přizpůsobení XML seznamu klipů (pro určení jednoho nebo více zdrojových souborů, s oříznutím nebo bez oříznutí atd.).
* Překrytí obrázku loga na vstupním videu, když je video zakódováno.
* Kódování více zvukových jazyků.

Chcete-li, aby **pracovní postup Premium kodéru médií** věděl, že při vytváření úlohy nebo odesílání více vstupních souborů měníte některé vlastnosti pracovního postupu, musíte použít konfigurační řetězec, který obsahuje **vlastnosti setRuntimeProperties** a/nebo **transcodeSource**. Toto téma vysvětluje, jak je používat.

## <a name="configuration-string-syntax"></a>Syntaxe konfiguračního řetězce
Konfigurační řetězec nastavený v úloze kódování používá dokument XML, který vypadá takto:

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

Následuje kód Jazyka C#, který čte konfiguraci XML ze souboru, aktualizuje ji pravým názvem souboru videa a předá jej úloze v úloze:

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
V některých případech je užitečné přizpůsobit vlastnost komponenty spolu se souborem pracovního postupu, který bude proveden pomocí pracovního postupu Media Encoder Premium.

Předpokládejme, že jste navrhli pracovní postup, který překreslování textu na vaše videa a text (například aktuální datum) má být nastaven za běhu. To lze provést odesláním textu, který má být nastaven jako nová hodnota vlastnosti textu komponenty překrytí z úlohy kódování. Tento mechanismus můžete použít ke změně dalších vlastností součásti v pracovním postupu (například umístění nebo barva překrytí, přenosový tok kodéru AVC atd.).

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
Chcete-li nastavit vlastnost, která očekává hodnotu XML, zapouzdřte pomocí `<![CDATA[ and ]]>`.

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
> Ujistěte se, že nedát `<![CDATA[`kočár vrátit těsně po .

### <a name="propertypath-value"></a>propertyPath hodnota
V předchozích příkladech propertyPath byl "/Media File Input/filename" nebo "/inactiveTimeout" nebo "clipListXml".
Toto je obecně název komponenty, pak název vlastnosti. Cesta může mít více nebo méně úrovní, jako je "/primarySourceFile" (protože vlastnost je v kořenovém adresáři pracovního postupu) nebo "/Video Processing/Graphic Overlay/Opacity" (protože překrytí je ve skupině).    

Chcete-li zkontrolovat cestu a název vlastnosti, použijte tlačítko akce, která je bezprostředně vedle každé vlastnosti. Klepněte na toto tlačítko akce a vyberte **upravit**. Zobrazí se skutečný název vlastnosti a bezprostředně nad ní obor názvů.

![Akce/Úprava](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Vlastnost](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Více vstupních souborů
Každý úkol, který odešlete do **pracovního postupu Premium kodéru médií,** vyžaduje dva datové zdroje:

* První z nich je *prostředek pracovního postupu,* který obsahuje soubor pracovního postupu. Soubory pracovního postupu můžete navrhnout pomocí [Návrháře pracovních postupů](media-services-workflow-designer.md).
* Druhý je *media asset,* který obsahuje mediální soubory, které chcete zakódovat.

Při odesílání více mediálních souborů do kodéru **pracovního postupu kodéru kodéru kodéru kmédiím** platí následující omezení:

* Všechny mediální soubory musí být ve stejném *mediálním podkladu*. Použití více datových zdrojů médií není podporováno.
* Primární soubor v tomto mediálním podkladu je nutné nastavit (v ideálním případě se jedná o hlavní soubor videa, ke kterému je kodéru vyzván ke zpracování).
* Je nutné předat konfigurační data, která obsahuje **parametr setRuntimeProperties** a/nebo **transcodeSource** do procesoru.
  * **setRuntimeProperties** se používá k přepsání vlastnosti název_souboru nebo jiné vlastnosti v součástech pracovního postupu.
  * **transcodeSource** se používá k určení obsahu XML seznamu klipů.

Připojení v pracovním postupu:

* Pokud použijete jednu nebo více součástí vstupu mediálního souboru a plánujete použít **příkaz setRuntimeProperties** k určení názvu souboru, nepřipojujte k nim připnutí primární součásti souboru. Ujistěte se, že neexistuje žádné spojení mezi objektem primárního souboru a vstupy mediálního souboru.
* Pokud dáváte přednost použití xml seznamu klipů a jedné součásti Zdroje médií, můžete se spojit.

![Žádné připojení z primárního zdrojového souboru se vstupem mediálního souboru](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Pokud použijete vlastnost setRuntimeProperties k nastavení vlastnosti název souboru, není k dispozici žádné připojení z primárního souboru ke součástem vstupu mediálního souboru.*

![Připojení z XML seznamu klipů ke zdroji seznamu klipů](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Xml seznamu klipů můžete připojit ke zdroji médií a použít transcodeSource.*

### <a name="clip-list-xml-customization"></a>Vlastní nastavení XML seznamu klipů
Xml seznamu klipů můžete určit v pracovním postupu za běhu pomocí **transcodeSource** v xml konfiguračního řetězce. To vyžaduje, aby byl pin XML seznamu klipů připojen k součásti Zdroj médií v pracovním postupu.

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

Pokud chcete zadat /primarySourceFile pro použití této vlastnosti k pojmenování výstupních souborů pomocí příkazu Expressions, doporučujeme předat xml seznamu klipů jako vlastnost *za* vlastnost /primarySourceFile, aby se zabránilo přepsání seznamu klipů nastavením /primarySourceFile.

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

S přídavným přesným oříznutím rámečku:

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

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Příklad 1 : Překrytí obrázku nad videem

### <a name="presentation"></a>Zobrazení
Vezměte v úvahu příklad, ve kterém chcete překrýt obrázek loga na vstupním videu, když je video zakódováno. V tomto příkladu má vstupní video název "Microsoft_HoloLens_Possibilities_816p24.mp4" a logo s názvem "logo.png". Měli byste provést následující kroky:

* Vytvořte datový zdroj pracovního postupu se souborem pracovního postupu (viz následující příklad).
* Vytvořte datový zdroj médií, který obsahuje dva soubory: MyInputVideo.mp4 jako primární soubor a MyLogo.png.
* Odešlete úlohu mediálnímu procesoru Media Encoder Premium Workflow s výše uvedenými vstupními prostředky a zadejte následující konfigurační řetězec.

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

Ve výše uvedeném příkladu je název souboru videa odeslán do komponenty Vstup mediálního souboru a vlastnosti primarySourceFile. Název souboru loga je odeslán jinému vstupu mediálního souboru, který je připojen ke komponentě překrytí grafiky.

> [!NOTE]
> Název souboru videa je odeslán do vlastnosti primarySourceFile. Důvodem je použití této vlastnosti v pracovním postupu pro vytváření správného názvu výstupního souboru pomocí výrazů, například.

### <a name="step-by-step-workflow-creation"></a>Podrobné vytvoření pracovního postupu
Tady jsou kroky k vytvoření pracovního postupu, který bere dva soubory jako vstup: video a obrázek. Překryje obraz v horní části videa.

Otevřete **Návrháře pracovních postupů** a vyberte **Soubor Nový** > podrobný**plán překódování****pracovního prostoru** > .

Nový pracovní postup zobrazuje tři prvky:

* Primární zdrojový soubor
* XML seznam klipů
* Výstupní soubor/datový zdroj  

![Nový pracovní postup kódování](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Nový pracovní postup kódování*

Chcete-li přijmout vstupní mediální soubor, začněte přidáním komponenty Vstup mediálního souboru. Chcete-li přidat komponentu do pracovního postupu, vyhledejte ji ve vyhledávacím poli Úložiště a přetáhněte požadovanou položku do podokna návrháře.

Dále přidejte soubor videa, který se má použít k návrhu pracovního postupu. Chcete-li tak učinit, klepněte na podokno pozadí v Návrháři pracovního postupu a vyhledejte vlastnost Primární zdrojový soubor v podokně vlastností vpravo. Klepněte na ikonu složky a vyberte příslušný soubor videa.

![Primární zdroj souboru](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Primární zdroj souboru*

Dále určete soubor videa v součásti Vstup mediálního souboru.   

![Zdroj vstupu mediálního souboru](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Zdroj vstupu mediálního souboru*

Jakmile to provedete, komponenta Vstup mediálního souboru zkontroluje soubor a naplní jeho výstupní kolíky tak, aby odrážely soubor, který kontroloval.

Dalším krokem je přidání "Video Data Type Updater" určit barevný prostor Rec.709. Přidejte "Převaděč formátu videa", který je nastaven na typ rozložení/rozvržení dat = Konfigurovatelný planár. Tím se datový proud videa převede do formátu, který lze považovat za zdroj komponenty překrytí.

![Video Datový typ Updater a Převaděč formátů](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Aktualizace datového typu videa a převaděč formátů*

![Typ rozložení = Konfigurovatelné rovinné](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Typ rozložení je konfigurovatelný rovinně*

Dále přidejte komponentu Překrytí videa a připojte (nekomprimovaný) pin videa k (nekomprimovanému) video pinu vstupu mediálního souboru.

Přidejte další vstup mediálního souboru (pro načtení souboru s logem), klikněte na tuto komponentu a přejmenujte ji na "Logo vstupu mediálního souboru" a vyberte obrázek (například soubor PNG) ve vlastnosti souboru. Připojte pin nekomprimovaného obrazu k pinu nekomprimovaného obrazu překrytí.

![Překrytí komponenty a zdroje souboru obrazu](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Překrytí komponenty a zdroje souboru obrazu*

Pokud chcete změnit polohu loga ve videu (například jej můžete umístit na 10 % mimo levý horní roh videa), zrušte zaškrtnutí políčka Ruční vstup. To lze provést, protože používáte vstup mediálního souboru k poskytnutí souboru loga součásti překrytí.

![Pozice překrytí](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Pozice překrytí*

Chcete-li kódovat datový proud videa na H.264, přidejte komponenty AVC Video Encoder a AAC encoder na povrch návrháře. Připojte kolíky.
Nastavte kodér AAC a vyberte Převod/přednastavení formátu zvuku : 2.0 (L, R).

![Kodéry zvuku a videa](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Kodéry zvuku a videa*

Nyní přidejte součásti **multiplexeru ISO Mpeg-4** a **výstup souboru** a připojte kolíky, jak je znázorněno na obrázku.

![MP4 multiplexer a výstup souboru](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplexer a výstup souboru*

Je třeba nastavit název výstupního souboru. Klepněte na **komponentu Výstup souboru** a upravte výraz souboru:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Název výstupu souboru](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Název výstupu souboru*

Pracovní postup můžete spustit místně a zkontrolovat, zda je správně spuštěn.

Po dokončení můžete spustit ve službě Azure Media Services.

Nejprve připravte datový zdroj ve službě Azure Media Services se dvěma soubory: souborem videa a logem. Můžete to provést pomocí rozhraní .NET nebo REST API. Můžete to udělat také pomocí portálu Azure nebo [Průzkumníka Médií](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Tento kurz ukazuje, jak spravovat prostředky s AMSE. Existují dva způsoby přidání souborů do datového zdroje:

* Vytvořte místní složku, zkopírujte dva soubory v ní a přetáhněte ji na kartu **Datový zdroj.**
* Nahrajte video soubor jako datový zdroj, zobrazte informace o datovém zdroji, přejděte na kartu Soubory a nahrajte další soubor (logo).

> [!NOTE]
> Ujistěte se, že jste nastavili primární soubor v datovém zdroji (hlavní video soubor).

![Soubory datových zdrojů v AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Soubory datových zdrojů v AMSE*

Vyberte datový zdroj a zvolte jeho kódování pomocí prémiového kodéru. Nahrajte pracovní postup a vyberte ho.

Klepnutím na tlačítko předejte data procesoru a přidejte následující XML, abyste nastavili vlastnosti za běhu:

![Prémiový kodér v AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Prémiový kodér v AMSE*

Potom vložte následující data XML. Je třeba zadat název souboru videa pro vstup mediálního souboru i primární zdrojový soubor. Zadejte také název názvu souboru pro logo.

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

Pokud k vytvoření a spuštění úlohy použijete sadku .NET SDK, musí být tato data XML předána jako konfigurační řetězec.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

Po dokončení úlohy se v souboru MP4 ve výstupním podkladu zobrazí překrytí!

![Překrytí ve videu](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Překrytí ve videu*

Ukázkový pracovní postup si můžete stáhnout z [GitHubu](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Příklad 2: Kódování více zvukových jazyků

Příklad více pracovních postupů kódování zvukového jazyka je k dispozici v [GitHubu](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Tato složka obsahuje ukázkový pracovní postup, který lze použít ke kódování souboru MXF do datového zdroje více souborů MP4 s více zvukovými stopami.

Tento pracovní postup předpokládá, že soubor MXF obsahuje jednu zvukovou stopu ; další zvukové stopy by měly být předány jako samostatné zvukové soubory (WAV nebo MP4...).

Chcete-li kódovat, postupujte takto:

* Vytvořte datový zdroj Mediální služby se souborem MXF a zvukovými soubory (0 až 18 zvukových souborů).
* Ujistěte se, že soubor MXF je nastaven jako primární soubor.
* Vytvořte úlohu a úkol pomocí procesoru Premium Workflow Encoder. Použijte poskytnutý pracovní postup (MultiMP4-1080p-19audio-v1.workflow).
* Předajte data setruntime.xml úloze (pokud používáte Průzkumník a provoz ovacího programu Azure Media Services, použijte tlačítko Předat data XML pracovnímu postupu).
  * Aktualizujte data XML a určete správné názvy souborů a značky jazyků.
  * Pracovní postup má zvukové součásti s názvem Audio 1 až Audio 18.
  * RFC5646 je podporován pro značku jazyka.

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

* Kódovaný datový zdroj bude obsahovat vícejazyčné zvukové stopy a tyto stopy by měly být volitelné v programu Azure Media Player.

## <a name="see-also"></a>Viz také
* [Zavedení kódování Premium ve službě Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Jak používat kódování Premium ve službě Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Kódování obsahu na vyžádání pomocí Mediálních služeb Azure](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Formáty a kodeky Media Encoderu Premium Workflow](media-services-premium-workflow-encoder-formats.md)
* [Ukázkové soubory pracovního postupu](https://github.com/Azure/azure-media-services-samples)
* [Nástroj Azure Media Services Explorer](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
