---
title: Více vstupních souborů a vlastnosti komponent se kodér úrovně Premium – Azure | Dokumentace Microsoftu
description: Toto téma vysvětluje, jak pomocí setRuntimeProperties využívat více vstupních souborů a předání dat vlastní procesor médií pracovní postup kodéru Media Encoder Premium.
services: media-services
documentationcenter: ''
author: xpouyat
manager: cfowler
editor: ''
ms.assetid: 7fb35bdd-9891-4401-a65b-ef3cc8190e8a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: xpouyat;anilmur;juliako
ms.openlocfilehash: 62892b8a82b05872edfb539092eebac5b4bcc05b
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50157679"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Použití několika vstupních souborů a vlastnosti komponent s kodér úrovně Premium
## <a name="overview"></a>Přehled
Existují scénáře, ve kterých možná budete muset upravit vlastnosti součásti určit obsah XML seznamu klipu nebo odeslání více vstupních souborů, když odešlete úlohu s **pracovní postup kodéru Media Encoder Premium** procesor médií. Tady je několik příkladů:

* Překryvné text na video a nastavení textové hodnoty (například aktuální datum) za běhu pro každý vstupní video.
* Přizpůsobení souboru XML seznamu klipu (můžete zadat jednu nebo více zdrojových souborů, s nebo bez ořezávání, atd.).
* Obrázek loga zakreslovat na vstupním videu, zatímco je kódování videa.
* Více zvuku kódování.

Chcete, aby **pracovní postup kodéru Media Encoder Premium** vědět, že změníte některé vlastnosti v pracovním postupu při vytvoření úkolu nebo odeslání více vstupních souborů, je nutné použít konfigurační řetězec, který obsahuje  **setRuntimeProperties** a/nebo **transcodeSource**. Toto téma vysvětluje, jak je používat.

## <a name="configuration-string-syntax"></a>Syntaxe řetězce konfigurace
Řetězec konfigurační nastavení v úlohu kódování používá dokumentu XML, který vypadá takto:

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

Tady je C# kód, který načte konfiguraci XML ze souboru, aktualizujte ji přímo videa název souboru a předá do úkolů v úloze:

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

## <a name="customizing-component-properties"></a>Úpravy vlastností součásti
### <a name="property-with-a-simple-value"></a>Vlastnost s jednoduchou hodnotu
V některých případech je užitečné pro přizpůsobení vlastnost komponenty spolu s soubor pracovního postupu, který bude provádět pracovní postup kodéru Media Encoder Premium.

Předpokládejme, že navržené pracovní postup překrytí textu videí a text (například aktuální datum) by měla být nastavena v době běhu. To můžete provést odeslání text, který má nastavit jako novou hodnotu pro vlastnost text součásti překrytí na úlohu kódování. Tento mechanismus můžete použít ke změně jiných vlastností komponenty v pracovním postupu (jako je například umístění a barvy překrytí, přenosové rychlosti AVC kodér atd.).

**setRuntimeProperties** umožňuje přepsat vlastnost v součásti pracovního postupu.

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

### <a name="property-with-an-xml-value"></a>Vlastnost hodnoty XML
Pokud chcete nastavit vlastnost, která očekává, že hodnoty XML, zapouzdření pomocí `<![CDATA[ and ]]>`.

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
> Zajistěte, aby vložit zalomení řádku vrácené hned za `<![CDATA[`.

### <a name="propertypath-value"></a>propertypath – hodnota
V předchozích příkladech propertyPath byl "/ mediálních souborů vstup/filename" nebo "/ inactiveTimeout" nebo "clipListXml".
To je, obecně platí, název komponenty, název vlastnosti. Cesta může mít více nebo méně úrovně, jako je třeba "/ primarySourceFile" (protože vlastnost je v kořenovém adresáři pracovního postupu) nebo "/ Video zpracování/obrázek překrytí/krytí" (protože překrytí je ve skupině).    

Zkontrolujte název a cesta k vlastnosti, použijte tlačítko akce, který je hned vedle jednotlivých vlastností. Můžete kliknutím na toto tlačítko akce a vybrat **upravit**. Zobrazí se skutečný název vlastnosti a okamžitě nad ním, obor názvů.

![Akce/upravit](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Vlastnost](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Více vstupních souborů
Každý úkol, který se odešle **pracovní postup kodéru Media Encoder Premium** vyžaduje dva prostředky:

* První z nich je *pracovního postupu Asset* , která obsahuje soubor pracovního postupu. Soubory pracovního postupu můžete navrhnout pomocí [návrháře postupu provádění](media-services-workflow-designer.md).
* Druhá je *Asset média* , který obsahuje soubory médií, který chcete kódovat.

Když posíláte více multimediálních souborů do **pracovní postup kodéru Media Encoder Premium** kodér, platí následující omezení:

* Mediální soubory musí být ve stejném *Asset média*. Použití více mediálních materiálů se nepodporuje.
* V tento Asset média je nutné nastavit primárního souboru (v ideálním případě by toto je hlavní video soubor, který kodér se zobrazí výzva ke zpracování).
* Je potřeba předávat konfigurační data, která zahrnuje **setRuntimeProperties** a/nebo **transcodeSource** element procesoru.
  * **setRuntimeProperties** se používá k přepsání vlastnost název souboru nebo jiné vlastnosti součástí pracovního postupu.
  * **transcodeSource** slouží k určení obsahu klip seznamu XML.

Připojení pracovního postupu:

* Pokud můžete použít jednu nebo několik součástí vstupního souboru média a v úmyslu používat **setRuntimeProperties** zadat název souboru, pak není pin součást primárního souboru k nim připojit. Ujistěte se, že neexistuje žádné připojení mezi objektem primární soubor a nahráním souboru média.
* Pokud chcete použít klip seznamu XML a jedna komponenta zdroj média, můžete se připojit i společně.

![Žádné připojení z primární zdrojového souboru do vstupního souboru média](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Pokud používáte setRuntimeProperties nastavit vlastnost filename není žádné připojení k zarovnání vstupního souboru médií z primárního souboru.*

![Připojení ze seznamu klip XML do Galerie zdroj seznamu](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Můžete připojit klip seznamu XML na zdroj média a použít transcodeSource.*

### <a name="clip-list-xml-customization"></a>Oříznout přizpůsobení souboru XML seznamu
Můžete zadat seznam XML klipu v pracovním postupu za běhu pomocí **transcodeSource** v konfiguraci řetězce XML. To vyžaduje PIN kód XML seznamu klip připojit ke komponentě zdroj média v pracovním postupu.

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

Pokud chcete zadat /primarySourceFile tuto vlastnost použít k pojmenování výstupních souborů s využitím "Výrazy", pak doporučujeme předání seznamu XML Galerie jako vlastnost *po* vlastnost /primarySourceFile, abyste se vyhnuli nutnosti klipu Přepsat nastavení /primarySourceFile seznamu.

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

S další snímek přesné oříznutí:

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

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Příklad 1: Překryv image na video

### <a name="presentation"></a>Prezentace
Vezměte v úvahu příklad, ve kterém chcete překrýt obrázek loga na vstupním videu, zatímco je kódování videa. V tomto příkladu vstupní video má název "Microsoft_HoloLens_Possibilities_816p24.mp4" a logo je pojmenován "logo.png". Měli byste provést následující kroky:

* Vytvoření prostředku pracovního postupu s soubor pracovního postupu (viz následující příklad).
* Vytvořte Asset média, který obsahuje dva soubory: MyInputVideo.mp4 jako primární soubor a MyLogo.png.
* Odeslat úlohu procesor médií pracovní postup kodéru Media Encoder Premium s výše vstupní prostředky a zadat následující řetězec konfigurace.

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

V předchozím příkladu je název souboru videa odesílat komponentu vstupního souboru médií a vlastnost primarySourceFile. Název souboru logo se odešle do jiného vstupního souboru média, která je připojena k součásti grafické překrytí.

> [!NOTE]
> Název souboru videa se pošle primarySourceFile vlastnost. Důvodem je použití této vlastnosti v pracovním postupu pro vytváření pomocí výrazů, třeba název správný výstupního souboru.

### <a name="step-by-step-workflow-creation"></a>Vytvoření podrobné pracovního postupu
Tady jsou kroky k vytvoření pracovní postup, který vezme jako vstupní údaje dva soubory: video a bitovou kopii. To bude překryv bitovou kopii na video.

Otevřít **návrháře postupu provádění** a vyberte **souboru** > **nový pracovní prostor** > **podrobného plánu překódování**.

Nový pracovní postup ukazuje tři prvky:

* Primárním zdrojovém souboru
* Seznam klip XML
* Výstupní soubor nebo prostředek  

![Nový pracovní postup kódování](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Nový pracovní postup kódování*

Aby bylo možné přijímat vstupní mediální soubor, spusťte s přidáváním komponentu vstupní soubor média. Chcete-li přidat součásti do pracovního postupu, podívejte se do vyhledávacího pole úložiště a přetáhněte na požadovanou položku na podokno návrháře.

V dalším kroku přidáte videosoubor má být použit pro návrh pracovního postupu. Uděláte to tak, klikněte na podokno na pozadí v Návrháři pracovních postupů a vyhledejte vlastnost primární zdrojový soubor v podokně napravo vlastnost. Klikněte na ikonu složky a vyberte příslušný soubor videa.

![Primární soubor zdroje](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Primární soubor zdroje*

Dále určete soubor videa v komponentě vstupního souboru média.   

![Mediální soubor vstupního zdroje](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Mediální soubor vstupního zdroje*

Jakmile to uděláte, bude součást vstupního souboru média kontrolu souboru a naplnění jeho výstupní spojky tak, aby odrážely soubor, který ho prozkoumat.

Dalším krokem je přidání "videa datový typ Updater" k určení barevný prostor pro Rec.709. Přidat "Videa formátu konvertor", který je nastaven typ rozložení a rozložení dat = konfigurovatelné planární. Datový proud videa to bude převést do formátu, dá se přenést jako zdroj součásti překrytí.

![Video Updater datový typ a formát převaděč](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Videa Data typu Updater a převaděč formátu*

![Typ rozložení = konfigurovatelné planární](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Typ rozložení je konfigurovatelná planární*

V dalším kroku přidejte komponentu videa překrytí a připojte (nekomprimovaný) video PIN kód (nekomprimovaný) kód PIN video ze vstupního souboru média.

Přidat vstup soubor jiného média (se načíst soubor loga), klikněte na tuto součást a přejmenujte jej na "Logo vstupní soubor média" a vyberte image (soubor například ve formátu PNG) ve vlastnosti souboru. PIN kód nekomprimované image se připojte k nekomprimované image PIN kód překrytí.

![Zdroj překrytí komponenty a obrázkových souborů](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Zdroj překrytí komponenty a obrázkových souborů*

Pokud chcete změnit umístění logo na video (například můžete chtít umístit na 10 % slevu na levém horním rohu video), zrušte zaškrtnutí políčka "Ruční vstup". Můžete to provést, protože poskytnout soubor logo do překrytí komponenty jsou pomocí vstupního souboru média.

![Překrytí pozice](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Překrytí pozice*

Ke kódování videa datového proudu k H.264, přidejte součásti kodér AVC grafickou kodér a AAC na plochu návrháře. Připojte kódy PIN.
Nastavit AAC kodér a vybrat přednastavení převodu formátu zvuk: 2.0 (L, R).

![Audio a Video kodérů](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Audio a Video kodérů*

Nyní přidejte **ISO Mpeg-4 multiplexor** a **výstupního souboru** součásti a připojit tyto PIN kódy, jak je znázorněno.

![MP4 multiplexor a výstup souboru](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplexor a výstup souboru*

Je nutné nastavit název výstupního souboru. Klikněte na tlačítko **výstupního souboru** komponenty a upravit výraz pro soubor:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Název výstupního souboru](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Název výstupního souboru*

Můžete spustit místně postup zkontrolujte, že je správně spuštěna.

Poté, co dokončí, můžete ji spustit v Azure Media Services.

Nejprve připravit prostředek ve službě Azure Media Services pomocí dvou souborů: soubor videa a logo. Můžete to provést pomocí rozhraní .NET nebo REST API. Můžete to také provést pomocí webu Azure portal nebo [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

V tomto kurzu se dozvíte, jak spravovat prostředky s AMSE. Existují dva způsoby, jak přidat soubory do prostředku:

* Vytvořte místní složku, zkopírujte příslušné dva soubory a přetáhněte složku, do které **Asset** kartu.
* Nahrát videosoubor, jako prostředek, se zobrazí informace o prostředku, přejděte na kartu soubory a odeslat další soubor (logo).

> [!NOTE]
> Ujistěte se, že se nastavit primární soubor v prostředku (hlavní soubor videa).

![Soubory prostředků v AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Soubory prostředků v AMSE*

Vyberte prostředek a zvolte ke kódování s kodér úrovně Premium. Odeslat pracovní postup a vyberte ji.

Kliknutím na tlačítko předat data do zpracovatele a přidejte následující kód XML pro nastavení vlastnosti modulu runtime:

![Kodér úrovně Premium ve AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Kodér úrovně Premium ve AMSE*

Vložte následující data XML. Musíte zadat název souboru videa pro vstupní soubor média a primarySourceFile. Zadejte název název souboru pro logo příliš.

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

Pokud použijete sadu .NET SDK k vytvoření a spuštění úlohy, má tato data XML mají být předány jako konfigurační řetězec.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

Po dokončení úlohy se zobrazí soubor MP4 v prostředku výstupu překrytí!

![Překrytí na video](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Překrytí na video*

Můžete si stáhnout ukázkový pracovní postup z [Githubu](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Příklad 2: Více zvukových kódování

Příklad zvuku vícejazyčné kódování workfkow je k dispozici v [Githubu](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Tato složka obsahuje ukázkový pracovní postup, který můžete použít ke kódování souboru MXF majetku s více soubory MP4 s více zvukové stopy.

Tento pracovní postup předpokládá, že soubor MXF obsahuje jedna zvuková stopa; Další zvukové stopy mají být předány jako samostatné zvukové soubory (WAV nebo MP4...).

Ke kódování, postupujte podle těchto kroků:

* Vytvořte prostředek služby Media Services pomocí souboru MXF a zvukové soubory (0 až 18 zvukové soubory).
* Ujistěte se, že soubor MXF nastavený jako primární soubor.
* Vytvoření úlohy a úkolů editoru pracovního postupu kodér úrovně Premium. Použití pracovního postupu k dispozici (MultiMP4-1080p-19audio-v1.workflow).
* Předání dat setruntime.xml úlohy (Pokud používáte Azure Media Services Explorer, použijte tlačítko "předání dat xml do pracovního postupu").
  * Aktualizujte data XML určená k určení správného souboru názvy a jazyky značky.
  * Pracovní postup obsahuje zvuku komponenty s názvem zvukového 1 až 18 zvuku.
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

* Zakódovanému assetu bude obsahovat více jazyka zvukové stopy a tyto stopy by měla být v Azure Media Player vybrat.

## <a name="see-also"></a>Další informace najdete v tématech
* [Úvod do Premium Encoding v Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Jak používat Premium Encoding v Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Kódování obsahu na vyžádání pomocí Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Kodeky a formáty Media Encoderu Premium Workflow](media-services-premium-workflow-encoder-formats.md)
* [Ukázkové soubory pracovního postupu](https://github.com/Azure/azure-media-services-samples)
* [Nástroj Azure Media Services Explorer](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
