---
title: Více vstupní soubory a vlastnosti komponenty pomocí kodéru Premium - Azure | Microsoft Docs
description: Toto téma vysvětluje, jak používat setRuntimeProperties k použití více vstupní soubory a předat vlastní data procesor médií Media Encoder Premium pracovního postupu.
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
ms.openlocfilehash: 66aec76e5af399e1909446b8ddf7a79aa1384d52
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Použití více vstupní soubory a vlastnosti komponenty s kodér úrovně Premium
## <a name="overview"></a>Přehled
Existují scénáře, ve kterých možná budete muset upravit vlastnosti komponenty, zadejte klip seznamu XML obsahu nebo odeslat více vstupních souborů při odesílání úlohy **Media Encoder Premium pracovního postupu** procesor médií. Tady je několik příkladů:

* Překrytí text na video a nastavení v době běhu pro každý vstupní video textové hodnoty (například aktuální datum).
* Přizpůsobení seznamu XML klip (tak, aby zadejte jednu nebo několik zdrojových souborů, s nebo bez oříznutí, atd.).
* Obrázek loga překrytí na vstup videa, při přehrávání videa je zakódován.
* Více kódování zvuk jazyka.

Umožníte **Media Encoder Premium pracovního postupu** vědět, že chcete změnit některé vlastnosti v pracovním postupu při vytvoření úlohy nebo odeslat více vstupní soubory, budete muset použít konfigurační řetězec, který obsahuje  **setRuntimeProperties** nebo **transcodeSource**. Toto téma vysvětluje, jak je používat.

## <a name="configuration-string-syntax"></a>Konfigurace řetězec syntaxe
Konfigurace řetězec, který nastavit v kódování úloh používá dokument XML, který vypadá takto:

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

Následuje kód C#, který čte konfigurační soubor XML ze souboru, jej aktualizovat s právo video název souboru a předává je na úlohu v rámci úlohy:

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

## <a name="customizing-component-properties"></a>Přizpůsobení vlastnosti komponenty
### <a name="property-with-a-simple-value"></a>Vlastnost s hodnotou jednoduché
V některých případech je užitečné, chcete-li přizpůsobit vlastnosti součásti společně s soubor pracovního postupu, který chcete provést, v pracovním postupu Premium kodér médií.

Předpokládejme, že určený pracovního postupu tento text překryvy videa a text (například aktuální datum) by měla být nastavena v době běhu. To provedete odesláním text, který má být nastavená jako novou hodnotu pro vlastnost text překrytí součásti z úlohy kódování. Tento mechanismus můžete změnit další vlastnosti komponenty v pracovním postupu (například pozici nebo barvu překrytí, přenosovou rychlostí AVC kodér atd.).

**setRuntimeProperties** se používá k přepsání na vlastnost ve součásti pracovního postupu.

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
Pokud chcete nastavit vlastnost, která očekává hodnoty XML, zapouzdření pomocí `<![CDATA[ and ]]>`.

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
> Ujistěte se, není pro umístění znaků CR návratový právě po `<![CDATA[`.

### <a name="propertypath-value"></a>Hodnota propertyPath
V předchozích příkladech propertyPath byla "/ souborů médií vstup/filename" nebo "/ inactiveTimeout" nebo "clipListXml".
Toto je, obecně platí, názvu součásti a potom název vlastnosti. Cesta může mít více nebo méně úrovně, jako je třeba "/ primarySourceFile" (protože vlastnost je v kořenovém adresáři pracovního postupu) nebo "/ Video zpracování nebo obrázek překrytí nebo krytí" (protože překrytí se nachází ve skupině).    

Chcete-li zkontrolovat název a cesta k vlastnosti, použijte tlačítko akce, která se nachází bezprostředně vedle každou vlastnost. Můžete kliknutím na toto tlačítko akce a vybrat **upravit**. To vám ukáže, skutečný název vlastnosti a okamžitě nad ním, obor názvů.

![Akce či upravit](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Vlastnost](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Více vstupní soubory
Každý úkol, který se odešle **Media Encoder Premium pracovního postupu** vyžaduje dva prostředky:

* První z nich je *pracovního postupu Asset* obsahující soubor pracovního postupu. Soubory pracovního postupu můžete navrhnout pomocí [Návrhář postupu provádění](media-services-workflow-designer.md).
* Je druhá *média Asset* obsahující soubory médií, který chcete kódovat.

Pokud odesíláte média soubory, které mají **Media Encoder Premium pracovního postupu** kodér, platí následující omezení:

* Všechny soubory média musí být ve stejné *média Asset*. Použití více prostředků média není podporováno.
* Je nutné nastavit primární soubor v tento prostředek média (v ideálním případě by toto je hlavní video soubor, který je kodér požadavku na zpracování).
* Je nutné předat konfigurační data, která zahrnuje **setRuntimeProperties** nebo **transcodeSource** element pro procesor.
  * **setRuntimeProperties** se používá k přepsání vlastnost název souboru nebo jinou vlastnost v součásti pracovního postupu.
  * **transcodeSource** slouží k určení obsahu klip seznamu XML.

Připojení v pracovním postupu:

* Pokud je použití jednoho nebo několika komponent vstupní soubor média a plánujete používat **setRuntimeProperties** Pokud chcete zadat název souboru, pak se nepřipojují pin součást primární soubor k nim. Ujistěte se, že neexistuje žádné připojení mezi objektem primární soubor a Input(s) soubor média.
* Pokud byste radši chtěli použít klip seznamu XML a jedna součást zdrojové médium, pak se můžete připojit i společně.

![Žádné připojení z primární zdrojový soubor pro vstupní soubor média](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Neexistuje žádné připojení z primární soubor do součásti vstupní soubor média, pokud používáte setRuntimeProperties set pro vlastnost název souboru.*

![Připojení ze seznamu klip XML tak, aby v případě potřeby upraví zdrojového seznamu](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Můžete připojit klip seznamu XML pro zdroj média a použít transcodeSource.*

### <a name="clip-list-xml-customization"></a>Oříznutí seznamu XML přizpůsobení
Můžete zadat seznam XML klip v pracovním postupu za běhu pomocí **transcodeSource** v konfiguraci řetězce XML. To vyžaduje PIN kód XML seznamu klip k připojení k komponentu zdroj média v pracovním postupu.

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

Pokud chcete určit /primarySourceFile pomocí této vlastnosti názvu výstupní soubory pomocí "Výrazy", pak doporučujeme předávání XML seznamu klip jako vlastnost *po* /primarySourceFile vlastnost, abyste nemuseli klip Seznam možné přepsat nastavení /primarySourceFile.

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

S další rámce přesné oříznutí:

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

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Příklad 1: Překrytí bitovou kopii na video

### <a name="presentation"></a>Prezentace
Zvažte příklad, ve kterém chcete překrýt obrázek loga na vstup videa při přehrávání videa je zakódován. V tomto příkladu vstupní video má název "Microsoft_HoloLens_Possibilities_816p24.mp4" a logo je s názvem "logo.png". Měli byste provést následující kroky:

* Vytvoření prostředku pracovního postupu s soubor pracovního postupu (viz následující příklad).
* Vytvoření média prostředku, který obsahuje dva soubory: MyInputVideo.mp4 jako primární soubor a MyLogo.png.
* Odeslat úlohu procesor médií Media Encoder Premium pracovního postupu s výše vstupní prostředky a zadat následující řetězec konfigurace.

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

V předchozím příkladu název souboru video posílá komponentu vstupní soubor média a vlastnost primarySourceFile. Název souboru logo se odesílají do jiné vstup souboru média, která je připojena k součásti grafické překrytí.

> [!NOTE]
> Název souboru videa se odesílají do primarySourceFile vlastnost. Důvodem je použití této vlastnosti v pracovním postupu pro vytváření pomocí výrazů, například název souboru správný výstup.

### <a name="step-by-step-workflow-creation"></a>Vytvoření pracovního postupu krok za krokem
Tady jsou kroky k vytvoření pracovního postupu, který přebírá dva soubory jako vstup: video a bitovou kopii. Ho bude překrytí bitovou kopii na video.

Otevřete **Návrhář postupu provádění** a vyberte **soubor** > **nový pracovní prostor** > **převod plán, podle kterého**.

Nový pracovní postup ukazuje tři prvky:

* Primární zdrojový soubor
* Seznam klip ve formátu XML
* Výstupní soubor nebo prostředek  

![Nový pracovní postup kódování](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Nový pracovní postup kódování*

Aby bylo možné přijímat souboru vstupní média, začněte přidáte komponentu vstupní soubor média. Chcete-li přidejte součást do pracovního postupu, podívejte se do vyhledávacího pole úložiště a přetáhněte na požadovanou položku na podokně návrháře.

V dalším kroku přidejte soubor video má být použit pro navrhování pracovního postupu. Uděláte to tak, klikněte v podokně pozadí v Návrháři pracovních postupů a vyhledejte vlastnost primární zdrojový soubor v podokně pravém vlastnost. Klikněte na ikonu složky a vyberte příslušný soubor videa.

![Primární soubor zdroje](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Primární soubor zdroje*

Potom zadejte soubor videa v komponentě vstupní soubor média.   

![Vstupní zdroj souborů médií](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Vstupní zdroj souborů médií*

Jakmile to uděláte, bude komponentu vstupní soubor média zkontrolujte soubor a naplnit její výstup PIN tak, aby odrážela soubor, který ho prověřovány.

Dalším krokem je přidání "Video datový typ aktualizační" k určení barevný prostor pro Rec.709. Přidání "Video formátu převaděč" nastavené na typ rozložení dat, rozvržení = konfigurovat planární. Datový proud videa bude převedena do formátu, který může být přijata jako zdroj pro komponentu překrytí.

![Video aktualizační datový typ a formát převaděč](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Video aktualizační datový typ a formát převaděč*

![Typ rozložení = konfigurovat planární](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Je-li konfigurovat planární typ rozložení*

V dalším kroku přidejte součást Video překrytí a připojení (nekomprimované) video kódu pin (nekomprimované) video připnete vstupní soubor média.

Přidat další vstup soubor média (se načíst soubor loga), klikněte na tuto součást a přejmenujte ji na "Logo vstupní soubor média" a vyberte bitovou kopii (soubor například .png) ve vlastnosti souboru. PIN kód nekomprimované image se připojte k nekomprimované image pin překrytí.

![Zdroj překrytí součásti a obrázkových souborů](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Zdroj překrytí součásti a obrázkových souborů*

Pokud chcete upravit pozici loga na video (například můžete chtít umístěte ho na 10 procent z levého horního rohu na video), zrušte zaškrtnutí políčka "Ruční vstup". Lze provést, protože používáte vstup soubor média můžete zadat souboru logo součást překrytí.

![Překrytí pozice](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Překrytí pozice*

Ke kódování datový proud videa na H.264, přidejte součásti kodér videa kodér AVC a AAC na plochu návrháře. Připojte kódy PIN.
Nastavit kodér AAC a vybrat přednastavení převodu formátu zvuk: 2.0 (L, R).

![Audio a Video kodéry](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Audio a Video kodéry*

Nyní přidejte **ISO Mpeg-4 multiplexor** a **výstup souboru** součásti a připojte kódy PIN, jak je znázorněno.

![MP4 multiplexor a výstupní soubor](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplexor a výstupní soubor*

Je nutné nastavit název souboru výstupního souboru. Klikněte **výstup souboru** součásti a úpravy výraz pro soubor:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Název výstupního souboru](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Název výstupního souboru*

Můžete spustit místně postup zkontrolujte, zda je správně spuštěna.

Po jejím dokončení, můžete ho spustit v Azure Media Services.

Nejprve připravte ke dvěma souborům se prostředek ve službě Azure Media Services: soubor videa a logo. To provedete pomocí .NET nebo REST API. Můžete to provést taky pomocí portálu Azure nebo [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

V tomto kurzu se dozvíte, jak spravovat prostředky s AMSE. Existují dva způsoby, jak přidat soubory do prostředek:

* Vytvořte místní složku, zkopírujte dva soubory v něm a přetažení složce **Asset** kartě.
* Nahrát videosoubor jako prostředek, se zobrazí informace o prostředku, přejděte na kartu soubory a odeslat další soubor (logo).

> [!NOTE]
> Nezapomeňte nastavit primární soubor v prostředku (hlavní soubor video).

![Soubory prostředků v AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Soubory prostředků v AMSE*

Vyberte asset a rozhodnete Kódovat pomocí kodéru Premium. Nahrát pracovního postupu a vyberte jej.

Kliknutím na tlačítko předat data do procesoru a přidejte následující kód XML a nastavte vlastnosti modulu runtime:

![Kodér úrovně Premium v AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Kodér úrovně Premium v AMSE*

Vložte následující data XML. Je třeba zadat název souboru videa pro vstupní soubor média i primarySourceFile. Zadejte název název souboru pro logo příliš.

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

Je-li používat sadu .NET SDK k vytvoření a spuštění úlohy, tato data XML musí předat jako konfigurační řetězec.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

Po dokončení úlohy soubor MP4 ve výstupní asset zobrazí překrytí!

![Překrytí na video](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Překrytí na video*

Můžete si stáhnout ukázkový pracovní postup z [Githubu](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Příklad 2: Více zvuk jazyk kódování

Příkladem zvuk vícejazyčné kódování workfkow je k dispozici v [Githubu](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Tato složka obsahuje ukázkový pracovní postup, který můžete použít ke kódování souboru MXF k více majetku soubory MP4 s více zvukových stop.

Tento pracovní postup předpokládá, že soubor MXF obsahuje jeden zvuk sledovat; Další zvukových stop mají být předány jako samostatné zvukové soubory (WAV nebo MP4...).

Ke kódování, postupujte podle těchto kroků:

* Vytvoření prostředku služby Media Services s MXF soubor a zvukové soubory (0 až 18 zvukové soubory).
* Ujistěte se, že v souboru MXF nastavena jako primární soubor.
* Vytvoření úlohy a úlohy pomocí procesoru kodér pracovního postupu úrovně Premium. Použití pracovního postupu poskytuje (MultiMP4-1080p-19audio-v1.workflow).
* Předávání dat setruntime.xml úlohy (Pokud používáte Azure Media Services Explorer, použijte tlačítko "předávání dat xml do pracovního postupu").
  * Aktualizujte data XML a určete správný soubor jazyky a názvy značek.
  * Pracovní postup obsahuje zvuk komponenty s názvem zvuk 1 na zvukové 18.
  * RFC5646 je podporována pro značku jazyka.

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

* K zakódovanému assetu bude obsahovat více jazyk zvukových stop a musí být tyto sleduje vybrat v Azure Media Player.

## <a name="see-also"></a>Další informace najdete v tématech
* [Představení Premium kódování v Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Jak používat kódování Premium ve službě Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Kódování obsahu na vyžádání pomocí Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Formáty Media Encoder Premium pracovního postupu a kodeky](media-services-premium-workflow-encoder-formats.md)
* [Ukázkové soubory pracovního postupu](https://github.com/Azure/azure-media-services-samples)
* [Nástroj pro Azure Media Services Explorer](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
