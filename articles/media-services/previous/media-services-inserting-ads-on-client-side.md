---
title: Vkládání reklam na straně klienta | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak vložit reklamy do médií na straně klienta.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 274ee09ae98dd229b255e58261f462e322be9f89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565736"
---
# <a name="inserting-ads-on-the-client-side"></a>Vkládání reklam na straně klienta
Tento článek obsahuje informace o tom, jak vložit různé typy reklam na straně klienta.

Informace o skrytých titulkech a podpoře reklam ve videích živého streamování najdete v [tématu Podporované standardy skrytých titulků a vkládání reklam](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> Azure Media Player momentálně nepodporuje reklamy.
> 
> 

## <a name="inserting-ads-into-your-media"></a><a id="insert_ads_into_media"></a>Vkládání reklam do médií
Azure Media Services poskytuje podporu pro vkládání reklam prostřednictvím platformy Windows Media Platform: Player Frameworks. Rozhraní pro hráče s podporou reklam jsou k dispozici pro zařízení s Windows 8, Silverlight, Windows Phone 8 a iOS. Každý hráč rámec obsahuje ukázkový kód, který ukazuje, jak implementovat aplikaci hráče. Existují tři různé druhy reklam, které můžete vložit do seznamu médií:

* **Lineární** – celorámové reklamy, které pozastavují hlavní video.
* **Nelineární** – překryvné reklamy, které se zobrazují jako hlavní video, obvykle logo nebo jiný statický obrázek umístěný v přehrávači.
* **Companion** – reklamy, které se zobrazují mimo přehrávač.

Reklamy mohou být umístěny kdykoli v časové linii hlavního videa. Musíte hráči sdělit, kdy má reklamu přehrát a které reklamy se mají hrát. To se provádí pomocí sady standardních souborů založených na formátu XML: Šablona služby video reklamy (VAST), Seznam digitálních video více násobných reklam (VMAP), Šablona pro výběr médií (MAST) a Definice rozhraní ad rozhraní digitálního přehrávače videa (VPAID). Soubory VAST určují, jaké reklamy se mají zobrazovat. VMAP soubory určují, kdy hrát různé reklamy a obsahují VAST XML. MAST soubory jsou dalším způsobem, jak sekvencovat reklamy, které také mohou obsahovat VAST XML. Soubory VPAID definují rozhraní mezi přehrávačem videa a reklamou nebo reklamním serverem.

Každý hráč rámec funguje jinak a každý bude zahrnuta ve svém vlastním článku. Tento článek popisuje základní mechanismy používané k vkládání reklam. Aplikace přehrávače videa požadují reklamy z reklamního serveru. Na ad server může reagovat několika způsoby:

* Vrácení souboru VAST
* Vrácení souboru VMAP (s vloženým vastem)
* Vrácení souboru MAST (s vloženým vast)
* Vrácení souboru VAST s reklamami VPAID

### <a name="using-a-video-ad-service-template-vast-file"></a>Použití souboru šablony služby videoreklamy (VAST)
Soubor VAST určuje, jakou reklamu nebo reklamy se mají zobrazovat. Následující kód XML je příkladem souboru VAST pro lineární reklamu:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
```

Lineární ad je popsána prvkem <**lineární**>. Určuje dobu trvání reklamy, sledování událostí, proklik, sledování kliknutí a řadu prvků **MediaFile.** Události sledování jsou určeny v rámci <**prvku SledováníUdálosti**> a umožňují serveru reklamy sledovat různé události, ke kterým dochází při prohlížení reklamy. V tomto případě jsou sledovány události start, midpoint, complete a expand. Událost zahájení nastane, když se zobrazí ad. K události středního bodu dojde, když se zobrazilo alespoň 50 % časové osy reklamy. K události dokončení dojde, když se reklama spustí až do konce. K události Rozbalit dojde, když uživatel rozbalí přehrávač videa na celou obrazovku. Prokliky jsou určeny pomocí prvku <**ClickThrough**> v rámci> prvku <**VideoClicks** a určuje identifikátor URI pro prostředek, který se zobrazí, když uživatel klikne na ad. ClickTracking je zadán v elementu <**ClickTracking**>, také v rámci> prvku <**VideoClicks** a určuje měřicí zdroj, o který má hráč požádat, když uživatel klikne na ad. Prvky> <**MediaFile** určují informace o konkrétním kódování reklamy. Pokud existuje více než jeden <**MediaFile**> element, přehrávač videa můžete zvolit nejlepší kódování pro platformu.

Lineární reklamy lze zobrazit v určeném pořadí. Chcete-li to `<Ad>` provést, přidejte další prvky do souboru VAST a určete pořadí pomocí atributu sekvence. Ilustruje to následující příklad:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
```

Nelineární reklamy jsou `<Creative>` určeny také v prvku. Následující příklad ukazuje `<Creative>` prvek, který popisuje nelineární ad.

```xml
    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>
```

<> element **nelinearads** může obsahovat jeden nebo více <**nelineárních**> prvků, z nichž každý může popisovat nelineární ad. Prvek <**nelineární**> určuje zdroj pro nelineární ad. Prostředek může být <**StaticResource**>, <**> IFrameResource** nebo <> **HTMLResource.** \<**StaticResource**> popisuje prostředek, který není ve formátu HTML, a definuje atribut creativeType, který určuje způsob zobrazení prostředku:

Image/gif, image/jpeg, image/png – zdroj je zobrazen v html <**img**> tag.

Aplikace/x-javascript – prostředek je zobrazen ve skriptu HTML **<**> tag.

Aplikace/x-shockwave-flash – zdroj se zobrazí v přehrávači Flash.

**IFrameResource** popisuje prostředek HTML, který lze zobrazit v prvku IFrame. **HTMLResource** popisuje část kódu HTML, který lze vložit do webové stránky. **TrackingEvents** zadat sledování událostí a URI požadovat, když dojde k události. V této ukázce jsou sledovány události acceptInvitation a collapse. Další informace o prvku **NonLinearAds** a jeho podřízených dětech naleznete v tématu IAB.NET/VAST. Všimněte si, že **TrackingEvents** prvek je umístěn v **NonLinearAds** prvek spíše než **nonLinear** prvek.

Doprovodné reklamy `<CompanionAds>` jsou definovány v rámci prvku. Prvek `<CompanionAds>` může obsahovat `<Companion>` jeden nebo více prvků. Každý `<Companion>` prvek popisuje doprovodnou ad `<StaticResource>`a `<IFrameResource>`může `<HTMLResource>` obsahovat , , nebo které jsou určeny stejným způsobem jako v nelineární reklamě. Soubor VAST může obsahovat více doprovodných reklam a aplikace přehrávače si může vybrat nejvhodnější zobrazování reklam. Další informace o vast, viz [VAST 3.0](https://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Použití souboru seznamu digitálních videovíce násobných reklam (VMAP)
Soubor VMAP umožňuje určit, kdy dojde k reklamním přestávkám, jak dlouho je každá přestávka, kolik reklam se může během přestávky zobrazit a jaké typy reklam se mohou během přestávky zobrazovat. Následující v příkladu souboru VMAP, který definuje jednu přestávku reklamy:

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[https://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scalable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

Soubor VMAP začíná elementem, `<VMAP>` který obsahuje `<AdBreak>` jeden nebo více prvků, z nichž každý definuje konec reklamy. Každá přestávka reklamy určuje typ přerušení, ID přerušení a časový posun. Atribut breakType určuje typ reklamy, kterou lze během přestávky přehrát: lineární, nelineární nebo zobrazenou. Obsahové reklamy jsou mapovány na doprovodné reklamy VAST. V seznamu odděleném čárkami (bez mezer) lze zadat více typů reklam. BreakID je volitelný identifikátor reklamy. TimeOffset určuje, kdy má být ad zobrazena. Může být specifikován jedním z následujících způsobů:

1. Čas – ve formátu hh:mm:ss nebo hh:mm:ss.mmm, kde .mmm je milisekund. Hodnota tohoto atributu určuje čas od začátku časové osy videa do začátku přestávky reklamy.
2. Procento – ve formátu n, kde n je procento časové osy videa, které se má přehrát před přehráním reklamy
3. Začátek/konec – určuje, že se má zobrazit ad před nebo po zobrazení videa.
4. Pozice – určuje pořadí reklamních přestávek, když není časování reklamních přestávek neznámé, například v živém vysílání. Pořadí jednotlivých zalomení reklamy je určeno ve #n formátu, kde n je celé číslo 1 nebo vyšší. 1 znamená, že by se měla hrát při první příležitosti, 2 znamená, že by se měla hrát při druhé příležitosti a tak dále.

V `<AdBreak>` rámci prvku může být jeden <**AdSource**> element. Prvek> <**AdSource** obsahuje následující atributy:

1. Id – určuje identifikátor zdroje reklamy.
2. allowMultipleAds – logická hodnota, která určuje, zda lze během reklamní přestávky zobrazit více reklam
3. followRedirects – volitelná logická hodnota, která určuje, zda by přehrávač videa měl respektovat přesměrování v rámci odpovědi na reklamu

Prvek <**AdSource**> poskytuje hráči vkládanou odpověď na reklamu nebo odkaz na odpověď na reklamu. Může obsahovat jeden z následujících prvků:

* `<VASTAdData>`označuje, že odpověď reklamy VAST je vložena do souboru VMAP
* `<AdTagURI>`Identifikátor URI, který odkazuje na odpověď reklamy z jiného systému
* `<CustomAdData>`-libovolný řetězec, který představuje odpověď bez vast

V tomto příkladu je in-line odpověď `<VASTAdData>` na reklamu zadána s prvkem, který obsahuje odpověď reklamy VAST. Další informace o ostatních prvcích naleznete v [tématu VMAP](https://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

<**AdBreak**> prvek může také obsahovat jeden <**TrackingEvents**> element. Prvek <**TrackingEvents**> umožňuje sledovat začátek nebo konec zarážky reklamy nebo to, zda během zarážky reklamy došlo k chybě. <**TrackingEvents**> prvek obsahuje jeden nebo více <**sledování**> prvky, z nichž každý určuje události sledování a sledování URI. Možné události sledování jsou:

1. breakStart – sleduje začátek přestávky na reklamy
2. breakEnd – sledujte dokončení přestávky na reklamu
3. chyba – sleduje chybu, ke které došlo během přerušení reklamy

Následující příklad ukazuje soubor VMAP, který určuje sledování událostí

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

Další informace o elementu <**TrackingEvents**> a jeho podřízených akcí naleznete v tématuhttp://iab.net/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Použití souboru šablony sekvenování abstraktních médií (MAST)
Soubor MAST umožňuje určit aktivační události, které definují, kdy se zobrazí ad. Následuje příklad souboru MAST, který obsahuje aktivační události pro předběžnou reklamu, reklamu v polovině role a reklamu po složce.

```xml
    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>
```


Soubor MAST začíná **prvkem MAST,** který obsahuje jeden **prvek aktivační události.** Prvek `<triggers>` obsahuje jeden nebo více **prvků aktivační události,** které definují, kdy má být hrana reklamy.

Element **aktivační události** obsahuje prvek **startConditions,** který určuje, kdy by se měla událost reklamy začít přehrávat. Prvek **startConditions** obsahuje jeden `<condition>` nebo více prvků. Když `<condition>` každý vyhodnotí true aktivační událost je iniciována nebo odvolána v závislosti na tom, `<condition>` zda je obsažen v rámci **startConditions** nebo **endConditions** element. Pokud `<condition>` je k dispozici více prvků, jsou považovány za implicitní NEBO, všechny podmínky vyhodnocující true způsobí aktivační událost zahájit. `<condition>`prvky mohou být vnořeny. Pokud `<condition>` jsou podřízené prvky přednastaveny, jsou považovány za implicitní and, všechny podmínky musí vyhodnotit true pro aktivační událost zahájit. Prvek `<condition>` obsahuje následující atributy, které definují podmínku:

1. **type** – určuje typ podmínky, události nebo vlastnosti
2. **název** – název nemovitosti nebo události, která má být použita při hodnocení
3. **value** – hodnota, proti které bude vlastnost vyhodnocena
4. **operátor** – operace pro použití během hodnocení: EQ (rovná se), NEQ (není stejný), GTR (větší), GEQ (větší nebo roven), LT (Menší než), LEQ (menší než nebo roven), MOD (modulo)

**endConditions** také `<condition>` obsahují prvky. Když se podmínka vyhodnotí jako true, aktivační událost se resetuje. Prvek `<trigger>` také obsahuje `<sources>` prvek, který `<source>` obsahuje jeden nebo více prvků. Prvky `<source>` definují identifikátor URI pro odpověď reklamy a typ odpovědi na reklamu. V tomto příkladu je identifikátor URI dán odpovědi VAST.

```xml
    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
```

### <a name="using-video-player-ad-interface-definition-vpaid"></a>Použití definice rozhraní přehrávače a reklamy videa (VPAID)
VPAID je rozhraní API pro povolení komunikace spustitelných reklam s přehrávačem videa. To umožňuje vysoce interaktivní prostředí reklam. Uživatel může s reklamou pracovat a reklama může reagovat na akce provedené divákem. V reklamě se například mohou zobrazovat tlačítka, která uživateli umožňují zobrazit více informací, nebo delší verzi reklamy. Přehrávač videa musí rozhraní API VPAID API podporovat a spustitelná ad musí rozhraní API implementovat. Když hráč požádá o reklamu z reklamního serveru, může server odpovědět odpovědí VAST, která obsahuje vpaid reklamu.

Spustitelná ad je vytvořena v kódu, který musí být spuštěn v prostředí runtime, jako je Adobe Flash™ nebo JavaScript, který lze spustit ve webovém prohlížeči. Pokud ad server vrátí odpověď VAST obsahující vpaid reklamy, hodnota apiFramework `<MediaFile>` atribut v elementu musí být "VPAID". Tento atribut určuje, že obsažená ada je spustitelná ad VPAID. Atribut typu musí být nastaven na typ mime spustitelného souboru, například "application/x-shockwave-flash" nebo "application/x-javascript". Následující fragment XML zobrazuje `<MediaFile>` prvek z odpovědi VAST obsahující spustitelnou reklamu VPAID.

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type="application/x-shockwaveflash"
                  width="640" height="480" apiFramework="VPAID">
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

Spustitelnou adlze lze inicializovat pomocí `<AdParameters>` prvku v rámci elementu `<Linear>` or `<NonLinear>` v odpovědi VAST. Další informace o `<AdParameters>` prvku naleznete v tématu [VAST 3.0](https://www.iab.net/media/file/VASTv3.0.pdf). Další informace o rozhraní VPAID API naleznete v tématu [VPAID 2.0](https://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implementace Windows nebo Windows Phone 8 Player s podporou reklam
Microsoft Media Platform: Player Framework pro Windows 8 a Windows Phone 8 obsahuje kolekci ukázkových aplikací, které ukazují, jak implementovat aplikaci přehrávače videa pomocí rozhraní. Můžete si stáhnout rozhraní Player Framework a ukázky z [rozhraní Player Framework pro Windows 8 a Windows Phone 8](https://playerframework.codeplex.com).

Při otevření řešení Microsoft.PlayerFramework.Xaml.Samples se v rámci projektu zobrazí řada složek. Složka Reklama obsahuje ukázkový kód relevantní pro vytvoření přehrávače videa s podporou reklam. Uvnitř složky Reklama je řada XAML / cs soubory, z nichž každý ukazuje, jak vložit reklamy jiným způsobem. Následující seznam popisuje každý z nich:

* AdPodPage.xaml Zobrazuje způsob zobrazení reklamního podu.
* AdSchedulingPage.xaml Zobrazuje způsob plánování reklam.
* FreeWheelPage.xaml Ukazuje, jak používat freewheel plugin pro plánování reklam.
* MastPage.xaml Ukazuje, jak naplánovat reklamy se souborem MAST.
* ProgrammaticAdPage.xaml Ukazuje, jak programově naplánovat reklamy do videa.
* ScheduleClipPage.xaml Ukazuje, jak naplánovat reklamu bez souboru VAST.
* Soubor VastLinearCompanionPage.xaml Ukazuje, jak vložit lineární a doprovodnou a doprovodnou ařitnou reklamu.
* Soubor VastNonLinearPage.xaml Ukazuje, jak vložit nelineární reklamu.
* VmapPage.xaml Ukazuje, jak určit reklamy pomocí souboru VMAP.

Každý z těchto ukázek používá třídu MediaPlayer definovanou rozhraním přehrávače. Většina ukázek používá pluginy, které přidávají podporu pro různé formáty odpovědí na reklamy. Ukázka ProgrammaticAdPage programově spolupracuje s instancí MediaPlayer.

### <a name="adpodpage-sample"></a>Ukázka aplikace AdPodPage
Tato ukázka používá AdSchedulerPlugin k definování, kdy se má zobrazit ad. V tomto příkladu je naplánováno přehrávání reklamy v polovině role po pěti sekundách. Reklamní pod (skupina reklam, které se mají zobrazovat v pořadí) je určen v souboru VAST vráceném z reklamního serveru. Identifikátor URI do souboru VAST `<RemoteAdSource>` je určen v elementu.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">

        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>

                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>

                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>
```

Další informace o adSchedulerPlugin, najdete [v tématu Inzerce v rozhraní Player Framework na Windows 8 a Windows Phone 8](https://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>Stránka AdSchedulingPage
Tato ukázka také používá AdSchedulerPlugin. Naplánuje tři reklamy, předběžnou reklamu, reklamu v polovině role a reklamu po sazbě. Identifikátor URI pro každou položku je `<RemoteAdSource>` pro každou ad specifikován v prvku.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>

                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="freewheelpage"></a>FreeWheelPage
Tato ukázka používá FreeWheelPlugin, který určuje atribut Source, který určuje identifikátor URI, který odkazuje na soubor SmartXML, který určuje obsah reklamy a informace o plánování reklam.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage
Tato ukázka používá MastSchedulerPlugin, který umožňuje použít soubor MAST. Atribut Source určuje umístění souboru MAST.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Tato ukázka programově spolupracuje s MediaPlayer. Soubor ProgrammaticAdPage.xaml vytvoří instanci mediaplayeru:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

Soubor ProgrammaticAdPage.xaml.cs vytvoří AdHandlerPlugin, přidá TimelineMarker určit, kdy má být zobrazena ad a pak přidá obslužnou rutinu pro MarkerReached událost, která načte RemoteAdSource zadání URI do souboru VAST a pak přehraje ad.

```csharp
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;

            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }

            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }
```

### <a name="scheduleclippage"></a>ScheduleClipPage
Tato ukázka používá AdSchedulerPlugin k naplánování reklamy v polovině role zadáním souboru WMV, který ji obsahuje.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearcompanionpage"></a>Stránka VastLinearCompanionPage
Tato ukázka ukazuje, jak pomocí modulu AdSchedulerPlugin naplánovat lineární reklamu uprostřed role s doprovodnou reklamou. Prvek `<RemoteAdSource>` určuje umístění souboru VAST.

```xml
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage
Tato ukázka používá AdSchedulerPlugin k naplánování lineární a nelineární reklamy. Umístění souboru VAST je `<RemoteAdSource>` určeno s prvkem.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vmappage"></a>Stránka VMAPPage
Tato ukázka používá VmapSchedulerPlugin k plánování reklam pomocí souboru VMAP. Identifikátor URI do souboru VMAP je určen `<VmapSchedulerPlugin>` v atributu Source prvku.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Implementace přehrávače videí pro iOS s podporou reklam
Microsoft Media Platform: Player Framework pro iOS obsahuje kolekci ukázkových aplikací, které ukazují, jak implementovat aplikaci přehrávače videa pomocí rozhraní. Rozhraní Player Framework a ukázky si můžete stáhnout z [rozhraní Azure Media Player Framework](https://github.com/Azure/azure-media-player-framework). Stránka GitHub obsahuje odkaz na Wiki, který obsahuje další informace o rozhraní přehrávače a úvod do ukázky přehrávače: [Azure Media Player Wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Plánování reklam pomocí VMAP
Následující příklad ukazuje, jak naplánovat reklamy pomocí souboru VMAP.

```csharp
    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }
            }
```

### <a name="scheduling-ads-with-vast"></a>Plánování reklam s VAST
Následující ukázka ukazuje, jak naplánovat pozdní závaznou reklamu VAST.


```csharp
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

   Následující ukázka ukazuje, jak naplánovat včasnou závaznou reklamu VAST.

```csharp
    //Example:4 Schedule an early binding VAST ad
    //Download the VAST file
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]])
    {
        [self logFrameworkError];
    }
    else
    {
        adLinearTime.startTime = 7;
        adLinearTime.duration = 0;

        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }
```

Následující ukázka ukazuje, jak vložit reklamu pomocí úprav hrubého řezu (RCE)

```csharp
    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }
```

Následující příklad ukazuje, jak naplánovat pod reklamy.

```csharp
    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;

    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Následující příklad ukazuje, jak naplánovat nelepivou reklamu v polovině role. Nelepivá reklama se hraje pouze jednou bez ohledu na to, jak se divák bude snažit.

```csharp
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Následující příklad ukazuje, jak naplánovat lepkavou reklamu v polovině role. Při každém dosažení zadaného bodu na časové ose videa se zobrazí lepkavá ad.

```csharp
    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Následující ukázka ukazuje, jak naplánovat reklamu po roli.

```csharp
    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Následující ukázka ukazuje, jak naplánovat předroložovací reklamu.

```csharp
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Následující ukázka ukazuje, jak naplánovat překryvnou reklamu v polovině převrácení.

```csharp
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```


## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
