---
title: Vkládání reklam na straně klienta | Microsoft Docs
description: Tento článek ukazuje, jak vložit reklamy do média na straně klienta.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 2d942099d0abbdfc4ddfa0276184500166250728
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014951"
---
# <a name="inserting-ads-on-the-client-side"></a>Vkládání reklam na straně klienta

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Tento článek obsahuje informace o tom, jak vložit různé typy reklam na straně klienta.

Informace o uzavřených titulkech a podpoře AD v živém streamování videí najdete v článku [podporované uzavřené titulky a standardy pro vkládání reklam](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> Azure Media Player aktuálně nepodporuje reklamy.
> 
> 

## <a name="inserting-ads-into-your-media"></a><a id="insert_ads_into_media"></a>Vkládání reklam do multimédií
Azure Media Services poskytuje podporu pro vkládání reklam prostřednictvím platformy Windows Media: rozhraní přehrávače. Pro zařízení se systémem Windows 8, Silverlight, Windows Phone 8 a iOS jsou k dispozici rozhraní přehrávače, která podporují službu AD. Každé rozhraní přehrávače obsahuje vzorový kód, který ukazuje, jak implementovat aplikaci přehrávače. Existují tři různé druhy reklam, které můžete vložit do svého média: seznam.

* **Lineární** – celé rámce, které pozastaví hlavní video.
* **Nelineární** – překryvné reklamy, které se zobrazují při přehrávání hlavního videa, obvykle se jedná o logo nebo jiný statický obrázek umístěný v přehrávači.
* **Doprovodné** – reklamy, které se zobrazují mimo přehrávač.

Reklamy lze umístit do libovolného místa v časovém øádku hlavního videa. Je nutné sdělit přehrávači, kdy má prohrát reklamu a které reklamy se mají přehrát. K tomu je potřeba použít sadu standardních souborů XML: šablona služby video AD (Velká část), digitální video (VMAP), šablony abstraktního sekvencování médií (MAST) a definice rozhraní Digital Video Player (VPAID). OBROVSKÉ soubory určují, jaké reklamy se mají zobrazit. Soubory VMAP určují, kdy se mají přehrávat různé reklamy, a obsahují velké množství XML. Soubory MAST představují jiný způsob, jak sesekvencit reklamy, které také mohou obsahovat velké množství XML. Soubory VPAID definují rozhraní mezi přehrávačem videa a serverem AD nebo AD.

Každé rozhraní přehrávače pracuje odlišně a každá z nich bude zahrnuta v jeho vlastním článku. Tento článek popisuje základní mechanismy používané pro vkládání reklam. Aplikace přehrávače videa vyžadují reklamu ze serveru AD. Server AD může reagovat několika způsoby:

* Vrátí nepřeberný soubor.
* Vrácení souboru VMAP (s vloženým OBROVSKÉm)
* Vrácení souboru MAST (s vloženým OBROVSKÉm)
* Vrácení ROZSÁHLÉho souboru s VPAID reklamami

### <a name="using-a-video-ad-service-template-vast-file"></a>Použití souboru šablony služby video AD (Velká)
OBROVSKÉ soubory určují, co AD nebo reklamy zobrazit. Následující kód XML je příkladem OBROVSKÉho souboru pro lineární službu AD:

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

Lineární služba AD je popsána <**lineárním**> elementu. Určuje dobu trvání reklamy, sledování událostí, kliknutí na tlačítko sledování a počet **MediaFile** prvků. Události sledování se zadává v rámci <**TrackingEvents**> elementu a umožňují serveru AD sledovat různé události, ke kterým dochází při prohlížení reklamy. V tomto případě jsou sledovány události spuštění, středních, úplných a rozbalených událostí. K události Start dojde při zobrazení reklamy. K události středního bodu dochází, když se zobrazila aspoň 50% časová osa služby AD. K události Complete dojde v případě, že služba AD běžela na konci. K události expand dojde, když uživatel rozbalí přehrávač videa na celou obrazovku. V rámci <**VideoClicks**> element a určuje identifikátor URI pro prostředek, který se zobrazí, když uživatel klikne na reklamu. **> <** ClickTracking je zadáno v> elementu <**ClickTracking** , a to i v rámci elementu <**VideoClicks**> a určuje prostředek sledování, který má hráč požádat, když uživatel klikne na reklamu. Prvky <**MediaFile**> určují informace o konkrétním kódování reklamy. Pokud existuje více <**MediaFile**> elementu, přehrávač videa si může vybrat nejlepší kódování pro platformu.

Lineární reklamy se dají zobrazit v zadaném pořadí. Chcete-li to provést, přidejte další `<Ad>` prvky do NEPŘEberného souboru a určete pořadí pomocí atributu Sequence. Ilustruje to následující příklad:

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

V elementu jsou také určeny nelineární reklamy `<Creative>` . Následující příklad ukazuje `<Creative>` prvek, který popisuje nelineární službu AD.

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

Element <**NonLinearAds**> může obsahovat jeden nebo více <**nelineárních**> prvků, z nichž každá může popsat nelineární službu AD. Nelineární > prvek <Určuje prostředek pro nelineární službu AD. Prostředkem může být <**StaticResource**>, <**IFrameResource**> nebo <**HTMLResource**>. \<**StaticResource**> Popisuje prostředek, který není HTML, a definuje atribut creativeType, který určuje, jak se prostředek zobrazuje:

Image/gif, obrázek/JPEG, obrázek/png – prostředek se zobrazí ve značce HTML <**img**>.

Application/x-JavaScript – prostředek se zobrazí ve značce HTML <**skriptu**>.

Application/x-Shockwave-Flash – prostředek se zobrazí v přehrávači Flash.

**IFrameResource** popisuje prostředek HTML, který lze zobrazit v prvku IFRAME. **HTMLResource** popisuje část kódu HTML, který může být vložen do webové stránky. **TrackingEvents** určete události sledování a identifikátor URI, který se má požadovat, když dojde k události. V této ukázce jsou sledovány události acceptInvitation a sbalení. Další informace o elementu **NonLinearAds** a jeho podřízených objektech naleznete v tématu IAB.NET/VAST. Všimněte si, že element **TrackingEvents** je umístěn v rámci elementu **NonLinearAds** a nikoli **nelineárním** prvkem.

Doprovodné reklamy jsou definovány v rámci `<CompanionAds>` elementu. `<CompanionAds>`Element může obsahovat jeden nebo více `<Companion>` elementů. Každý `<Companion>` prvek popisuje doprovodnou reklamu a může obsahovat `<StaticResource>` , `<IFrameResource>` , nebo, `<HTMLResource>` které jsou zadány stejným způsobem jako v nelineární službě AD. Nepřeberný soubor může obsahovat více doprovodných reklam a aplikace přehrávače může zvolit nejvhodnější reklamu, kterou je třeba zobrazit. Další informace najdete v části [obrovské 3,0](https://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Používání souboru VMAP (Digital Video Multiple AD Playlist)
Soubor VMAP umožňuje určit, kdy dojde k přerušení reklamy, jak dlouho je každé přerušení, kolik reklam se může zobrazit v rámci přerušení a jaké typy reklam se můžou zobrazit během přerušení. V ukázkovém souboru VMAP, který definuje jednu zalomení služby Active Directory, postupujte následovně:

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

Soubor VMAP začíná `<VMAP>` prvkem, který obsahuje jeden nebo více `<AdBreak>` elementů, z nichž každá definuje přerušení reklamy. Každé přerušení reklamy určuje typ přerušení, ID přerušení a časový posun. Atribut breakType určuje typ reklamy, který lze přehrát během přerušení: lineární, nelineární nebo zobrazení. Zobrazit reklamy se mapují na rozsáhlé doprovodné reklamy. V seznamu odděleném čárkou (bez mezer) lze zadat více než jeden typ AD. BreakID je nepovinný identifikátor služby AD. TimeOffset určuje, kdy se má AD zobrazit. Dá se zadat jedním z následujících způsobů:

1. Čas – ve formátu hh: mm: ss nebo hh: mm: ss. mmm, kde. mmm je milisekundy. Hodnota tohoto atributu určuje čas od začátku časové osy videa až na začátek konce reklamy.
2. Procento – ve formátu n%, kde n je procento časové osy videa, která se má přehrát před přehráním reklamy
3. Začátek/Konec – určuje, že se má AD zobrazit před nebo po zobrazení videa.
4. Pozice – Určuje pořadí konců reklam v případě, že časování konců reklamy není známé, například při živém streamování. Pořadí každého přerušení reklamy je zadáno ve formátu #n, kde n je celé číslo (Integer) 1 nebo vyšší. 1 znamená, že služba AD by měla být přehrávána první příležitostí, 2 znamená, že služba AD by měla být přehrávána při druhé příležitosti a tak dále.

V rámci `<AdBreak>` elementu může být jeden <**AdSource**> element. Element <**AdSource**> obsahuje následující atributy:

1. ID – určuje identifikátor zdroje AD
2. allowMultipleAds – logická hodnota, která určuje, jestli se dá během přerušení reklamy zobrazit víc reklam
3. followRedirects – volitelná logická hodnota, která určuje, jestli má přehrávač videa dodržovat přesměrování v rámci odpovědi AD

Prvek <> **AdSource** poskytuje přehrávači vloženou odpověď AD nebo odkaz na odpověď AD. Může obsahovat jeden z následujících prvků:

* `<VASTAdData>` indikuje, že v souboru VMAP je vložená nepřeberná odpověď AD.
* `<AdTagURI>` identifikátor URI, který odkazuje na odpověď služby AD z jiného systému
* `<CustomAdData>` -libovolný řetězec, který představuje neobrovské odpovědi

V tomto příkladu je zadána vložená odpověď AD s `<VASTAdData>` prvkem, který obsahuje obrovské odpovědi AD. Další informace o ostatních prvcích naleznete v tématu [VMAP](https://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

Element <**AdBreak**> může také obsahovat jeden prvek> <**TrackingEvents** . Element <**TrackingEvents**> umožňuje sledovat začátek nebo konec služby AD nebo to, zda během přerušení služby AD došlo k chybě. Prvek <**TrackingEvents**> obsahuje jeden nebo více <**sledovacích**> prvků, z nichž každý určuje sledovací událost a sledovací identifikátor URI. Možné sledované události:

1. breakStart – sleduje začátek přerušení reklamy.
2. breakEnd – sledování dokončení služby AD break
3. Chyba – sleduje chybu, ke které došlo během přerušení reklamy.

Následující příklad ukazuje soubor VMAP, který určuje události sledování.

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

Další informace o <prvky> **TrackingEvents** a jejích podřízených prvcích naleznete v tématu http://iab.net/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Použití souboru šablony pro abstraktní sekvencování médií (MAST)
Soubor MAST umožňuje zadat triggery, které definují, kdy se AD zobrazí. Následuje příklad souboru MAST, který obsahuje aktivační události pro předběžnou službu AD, službu AD a následnou službu AD.

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


Soubor MAST začíná elementem **mast** , který obsahuje jeden element **Triggers** . `<triggers>`Element obsahuje jeden nebo více elementů **triggeru** , které definují, kdy má být služba AD přehrána.

**Aktivační** element obsahuje element **startConditions** , který určuje, kdy má služba AD začít hrát. Element **startConditions** obsahuje jeden nebo více `<condition>` elementů. Když se každý `<condition>` vyhodnotí jako true, Trigger se iniciuje nebo odvolá v závislosti na tom, jestli `<condition>` je obsažený v **startConditions** nebo **endConditions** elementu. Pokud `<condition>` je přítomno více prvků, jsou považovány za implicitní nebo, jakákoli podmínka vyhodnocení na hodnotu true způsobí inicializaci triggeru. `<condition>` prvky mohou být vnořené. V případě `<condition>` , že jsou podřízené prvky přednastaveny, jsou považovány za implicitní a, všechny podmínky se musí vyhodnotit na hodnotu true pro inicializaci triggeru. `<condition>`Element obsahuje následující atributy definující podmínku:

1. **typ** – určuje typ podmínky, události nebo vlastnosti.
2. **název** – název vlastnosti nebo události, která se má použít při vyhodnocování
3. **Value** – hodnota, na kterou bude vlastnost vyhodnocena
4. **Operator** – operace, která se má použít během vyhodnocení: EQ (EQUAL), NEQ (nerovná se), GTR (větší), GEQ (větší nebo rovno), lt (menší než), LEQ (menší než nebo rovno), mod (modulo)

**endConditions** také obsahuje `<condition>` prvky. Když se podmínka vyhodnotí jako true, aktivační událost se resetuje. `<trigger>`Element také obsahuje `<sources>` element, který obsahuje jeden nebo více `<source>` prvků. `<source>`Elementy definují identifikátor URI pro odpověď AD a typ odpovědi AD. V tomto příkladu je identifikátor URI předána obrovské odpovědi.

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

### <a name="using-video-player-ad-interface-definition-vpaid"></a>Použití definice rozhraní video Player-Ad (VPAID)
VPAID je rozhraní API pro povolení komunikace spustitelných jednotek AD s přehrávačem videa. To umožňuje vysoce interaktivní prostředí Active Directory. Uživatel může komunikovat se službou AD a služba AD může reagovat na akce podniknuté prohlížečem. Například reklama může zobrazit tlačítka, která uživateli umožňují zobrazit další informace nebo delší verzi služby AD. Přehrávač videa musí podporovat rozhraní VPAID API a spustitelný soubor AD musí implementovat rozhraní API. Když hráč vyžádá reklamu ze serveru AD, server může reagovat s obrovské odezvy, která obsahuje VPAID AD.

V kódu, který musí být spuštěn v běhovém prostředí, jako je například Adobe Flash™ nebo JavaScript, který lze spustit ve webovém prohlížeči, je vytvořen spustitelný objekt AD. Pokud server AD vrátí nepřebernou odpověď obsahující VPAID AD, hodnota atributu apiFramework v `<MediaFile>` elementu musí být "VPAID". Tento atribut určuje, že obsažená služba AD je VPAID spustitelná služba AD. Atribut Type musí být nastaven na typ MIME spustitelného souboru, například application/x-Shockwave-Flash nebo Application/x-JavaScript. Následující fragment kódu XML ukazuje `<MediaFile>` prvek z obrovské odpovědi obsahující spustitelnou službu AD VPAID.

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type="application/x-shockwaveflash"
                  width="640" height="480" apiFramework="VPAID">
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

Spustitelný objekt AD lze inicializovat pomocí elementu v `<AdParameters>` rámci `<Linear>` `<NonLinear>` prvků nebo v obrovské odpovědi. Další informace o elementu najdete v `<AdParameters>` části [obrovské 3,0](https://www.iab.net/media/file/VASTv3.0.pdf). Další informace o rozhraní VPAID API najdete v článku [VPAID 2,0](https://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implementace přehrávače Windows nebo Windows Phone 8 s podporou služby AD
Platforma Microsoft Media: Player Framework pro Windows 8 a Windows Phone 8 obsahuje kolekci ukázkových aplikací, které ukazují, jak implementovat aplikaci pro Video Player pomocí architektury. Můžete si stáhnout rozhraní přehrávače a ukázky z [rozhraní Player pro Windows 8 a Windows Phone 8](https://playerframework.codeplex.com).

Když otevřete řešení Microsoft. PlayerFramework. XAML. Samples, zobrazí se v projektu několik složek. Složka pro inzerce obsahuje vzorový kód, který je relevantní pro vytvoření přehrávače videa s podporou služby AD. Uvnitř reklamní složky je počet souborů XAML/cs, z nichž každý ukazuje, jak vložit reklamy jiným způsobem. Jednotlivé seznamy jsou popsány v následujícím seznamu:

* AdPodPage. XAML ukazuje, jak zobrazit AD pod.
* AdSchedulingPage. XAML ukazuje, jak naplánovat reklamu.
* FreeWheelPage. XAML ukazuje, jak použít modul plug-in FreeWheel k naplánování reklam.
* MastPage. XAML ukazuje, jak naplánovat reklamu pomocí souboru MAST.
* ProgrammaticAdPage. XAML ukazuje, jak programově naplánovat reklamu do videa.
* ScheduleClipPage. XAML ukazuje, jak naplánovat reklamu bez OBROVSKÉho souboru.
* VastLinearCompanionPage. XAML ukazuje, jak vložit lineární a doprovodnou reklamu.
* VastNonLinearPage. XAML ukazuje, jak vložit nelineární službu AD.
* VmapPage. XAML ukazuje, jak zadat reklamu se souborem VMAP.

Každý z těchto ukázek používá třídu MediaPlayer definovanou rozhraním přehrávače. Většina ukázek používá moduly plug-in, které přidávají podporu pro různé formáty odpovědí AD. Ukázka ProgrammaticAdPage programově spolupracuje s instancí MediaPlayer.

### <a name="adpodpage-sample"></a>Ukázka AdPodPage
Tato ukázka používá AdSchedulerPlugin k definování, kdy se má zobrazit reklama. V tomto příkladu je naplánováno přehrání inzerce v polovině více sekund po pěti sekundách. Služba AD pod (skupina reklam, která se má zobrazit v pořadí), je určena v OBROVSKÉm souboru vráceném ze serveru AD. Identifikátor URI pro nepřeberný soubor je uveden v `<RemoteAdSource>` elementu.

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

Další informace o AdSchedulerPlugin najdete v tématu [inzerce v rozhraní přehrávače v systému Windows 8 a Windows Phone 8](https://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation) .

### <a name="adschedulingpage"></a>AdSchedulingPage
Tato ukázka také používá AdSchedulerPlugin. Plánuje tři inzeráty, předběžnou službu AD, službu AD a následnou reklamu. Identifikátor URI pro velké množství pro každou reklamu je určený v `<RemoteAdSource>` elementu.

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
Tato ukázka používá FreeWheelPlugin, který určuje zdrojový atribut, který určuje identifikátor URI, který odkazuje na soubor SmartXML, který určuje obsah služby AD a informace o plánování služby AD.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage
Tato ukázka používá MastSchedulerPlugin, který umožňuje použít soubor MAST. Zdrojový atribut určuje umístění souboru MAST.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Tato ukázka programově spolupracuje s MediaPlayer. MediaPlayer vytvoří instanci souboru ProgrammaticAdPage. XAML:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

Soubor ProgrammaticAdPage. XAML. cs vytvoří AdHandlerPlugin, přidá TimelineMarker, který určí, kdy se má AD zobrazit, a potom přidá obslužnou rutinu pro událost MarkerReached, která načte RemoteAdSource určující identifikátor URI do OBROVSKÉho souboru a potom přehraje reklamu.

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
V této ukázce se používá AdSchedulerPlugin k naplánování mezimnožinové reklamy tak, že se určí soubor. wmv, který obsahuje AD.

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

### <a name="vastlinearcompanionpage"></a>VastLinearCompanionPage
Tato ukázka ukazuje, jak použít AdSchedulerPlugin k naplánování lineárního AD v polovině, s doprovodnou reklamou. `<RemoteAdSource>`Prvek určuje umístění obrovského souboru.

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
Tato ukázka používá AdSchedulerPlugin k naplánování lineárního a nelineárního AD. Přeberné umístění souboru je určené `<RemoteAdSource>` elementem.

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

### <a name="vmappage"></a>VMAPPage
Tato ukázka používá VmapSchedulerPlugin k naplánování reklam pomocí souboru VMAP. Identifikátor URI pro soubor VMAP je zadán v atributu source `<VmapSchedulerPlugin>` elementu.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Implementace přehrávače videa iOS s podporou služby AD
Platforma Microsoft Media: Player Framework pro iOS obsahuje kolekci ukázkových aplikací, které ukazují, jak implementovat aplikaci pro Video Player pomocí architektury. Můžete stáhnout rozhraní přehrávače a ukázky z [Azure Media Player Framework](https://github.com/CloudMetal/azure-media-player-framework). Stránka GitHub obsahuje odkaz na wiki, který obsahuje další informace o rozhraní přehrávače a Úvod do ukázky přehrávače: [Azure Media Player wiki](https://github.com/CloudMetal/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Plánování reklam pomocí VMAP
Následující příklad ukazuje, jak naplánovat reklamu pomocí souboru VMAP.

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

### <a name="scheduling-ads-with-vast"></a>Plánování reklam s využitím ROZSÁHLÉho využití
Následující příklad ukazuje, jak naplánovat pozdní vazbu OBROVSKÉho AD.


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

   Následující příklad ukazuje, jak naplánovat předčasné vytvoření vazby OBROVSKÉho AD.

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

Následující příklad ukazuje, jak vložit reklamu pomocí hrubého vyjmutého úprav (RCE).

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

Následující příklad ukazuje, jak naplánovat AD pod.

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

Následující příklad ukazuje, jak naplánovat nerychlou hromadnou reklamu v polovině. Nerychlá služba AD se hraje jenom jednou bez ohledu na to, co prohlížeč provede.

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

Následující příklad ukazuje, jak naplánovat rychlé Shrnutí v rámci služby AD. Při každém dosažení zadaného bodu na časové ose videa se zobrazí rychlá reklama.

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

Následující příklad ukazuje, jak naplánovat službu po zavedení AD.

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

Následující příklad ukazuje, jak naplánovat předběžnou službu AD.

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

Následující příklad ukazuje, jak naplánovat polovině překryvných reklam.

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
