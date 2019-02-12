---
title: Vkládání reklam na straně klienta | Dokumentace Microsoftu
description: Toto téma ukazuje, jak vkládání reklam na straně klienta.
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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: cc5f3f729acca1f7aa23a7714300c1b581c6f7f8
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993890"
---
# <a name="inserting-ads-on-the-client-side"></a>Vkládání reklam na straně klienta
Tento článek obsahuje informace o tom, jak vložit různé typy reklam na straně klienta.

Informace o podpoře uzavřené titulky a ad v živé streamování videa najdete v tématu [podporované titulky a standardy vložení Ad](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> Azure Media Player v současné době nepodporuje reklamy.
> 
> 

## <a id="insert_ads_into_media"></a>Vkládání reklam na média
Azure Media Services poskytuje podporu pro vkládání reklam prostřednictvím mediální platformu Windows: Přehrávačů. Přehrávačů s podporou ad jsou k dispozici pro zařízení s Windows 8, Silverlight, Windows Phone 8 a iOS. Každý player framework obsahuje ukázkový kód, který ukazuje, jak implementovat aplikace přehrávače. Existují tři různé druhy reklamy, které lze vložit do seznamu média: seznam.

* **Lineární** – úplné rámce reklamy, které pozastavit hlavní video.
* **Nelineárních** – překrytí reklamy, které jsou zobrazeny, protože přehrávání videa hlavní, obvykle logo nebo jiný statický obrázek umístěny v přehrávači.
* **Doprovodná** – reklamy, které se zobrazují mimo přehrávač.

Služby Active Directory je možné použít v libovolném bodě hlavní video časové ose. Hráč musí zjistit, kdy se má přehrát ad a které reklamy přehrávání. To se provádí pomocí sady standardních souborů založený na formátu XML: Video Ad šablona (VAST), digitální Video služby, více Ad stop (VMAP), Media abstraktní sekvencování šablony (STOŽÁRŮ) a digitální videopřehrávače Ad definice rozhraní (VPAID). ROZSÁHLÉ soubory určují jaké služby Active Directory k zobrazení. Soubory VMAP určují, kdy přehrát různých reklamy a obsahovat OBROVSKÉ XML. Dalším způsobem, jak pořadí reklamy, které také mohou obsahovat OBROVSKÉ XML jsou STOŽÁRŮ soubory. Soubory VPAID definují rozhraní mezi přehrávače videa a ad nebo serveru služby ad.

Každý player framework funguje jinak a každá se budeme v jeho vlastní článku. Tento článek popisuje základní mechanismus používaný k vkládání reklam. Aplikací pro přehrávání videa požadavku služby Active Directory ze serveru služby ad. Serveru služby ad může reagovat v několika způsoby:

* Vrátit ROZSÁHLÉ soubor
* Vrátit VMAP soubor (pomocí vložených VAST)
* Vrátit STOŽÁRŮ soubor (pomocí vložených VAST)
* Vrátit ROZSÁHLÉ soubor s VPAID reklamy

### <a name="using-a-video-ad-service-template-vast-file"></a>Pomocí souboru šablony (VAST) služby Ad videa
ROZSÁHLÉ soubor Určuje, jaké ad nebo zobrazit reklamy. Následující kód XML je příkladem souboru ROZSÁHLÉ pro lineární ad:

```xml
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
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

Lineární ad je popsán <**lineární**> element. Určuje dobu trvání AD, sledování událostí, klikněte na tlačítko prostřednictvím sledování kliknutí a několik **MediaFile** elementy. Sledování události jsou uvedeny v rámci <**TrackingEvents**> element a povolit serveru služby ad pro sledování různých událostí, ke kterým dochází při zobrazování reklamy. V tomto případě start, střední, dokončeno a rozbalte události jsou sledovány. Událost zahájení nastane, pokud se zobrazí ad. Střední událost nastane, pokud alespoň, že se nezobrazil 50 % ad časové osy. Událost dokončení vyvolá se po spuštění služby ad na konec. Když je uživatel rozbalí na celou obrazovku přehrávač videa, dojde k události rozbalit. Clickthroughs zadávají se <**interaktivní**> element v rámci <**VideoClicks**> element a určuje identifikátor URI pro prostředek má zobrazit, když uživatel klikne na ad. ClickTracking je zadán v <**ClickTracking**> element, také v rámci <**VideoClicks**> prvku a určuje prostředek sledování pro přehrávač požadovat, když uživatel klikne na ad . <**MediaFile**> elementy zadejte informace o konkrétním kódováním reklamu. Když je více než jeden <**MediaFile**> elementu videopřehrávač můžete zvolit nejvhodnější kódování pro platformu.

Lineární reklamy lze zobrazit v uvedeném pořadí. Chcete-li to provést, přidejte další <Ad> prvků, které mají VAST souboru a určete pořadí pomocí atributu pořadí. Následující příklad ukazuje toto:

```xml
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
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

Nelineárních reklamy jsou určené v <Creative> i element. Následující příklad ukazuje <Creative> element, který popisuje nelineárních ad.

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

<**NonLinearAds**> element může obsahovat jeden nebo více <**NonLinear**> elementy, z nichž každý lze popsat nelineárních ad. <**NonLinear**> prvek určuje prostředek pro nelineárních ad. Prostředek může být <**StaticResource**>, <**IFrameResource**>, nebo <**HTMLResource**>. <**StaticResource**> popisuje prostředků jiného typu než HTML a definuje atribut creativeType, který určuje způsob zobrazení zdroje:

Image/gif, image/jpeg, image/png – prostředku se zobrazí v HTML <**img**> značky.

Application/x-javascript – prostředku se zobrazí v HTML <**skript**> značky.

Application/x-shockwave-flash – prostředku se zobrazí v přehrávače pro Flash.

**IFrameResource** popisuje prostředek ve formátu HTML, který lze zobrazit v elementu IFrame. **HTMLResource** popisuje část kódu HTML, který může být vložen do webové stránky. **TrackingEvents** zadejte sledování událostí a identifikátor URI pro žádosti při výskytu události. V této ukázce jsou sledovány události acceptInvitation a sbalení. Další informace o **NonLinearAds** elementu a jejích potomků, najdete v části IAB.NET/VAST. Všimněte si, že **TrackingEvents** se nachází v rámci elementu **NonLinearAds** element místo **NonLinear** elementu.

Doprovodná reklamy jsou definována v rámci <CompanionAds> elementu. <CompanionAds> Element může obsahovat jeden nebo více <Companion> elementy. Každý <Companion> element popisuje doprovodná ad a může obsahovat <StaticResource>, <IFrameResource>, nebo <HTMLResource> které je určené stejným způsobem jako v nelineárních ad. ROZSÁHLÉ soubor může obsahovat více doprovodných reklamy a aplikace přehrávače můžete vybrat nejvhodnější ad k zobrazení. Další informace o VAST najdete v tématu [ROZSÁHLÉ 3.0](http://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Použití více souboru se seznamem testů (VMAP) Ad digitální Video
Soubor VMAP umožňuje zadat, pokud dojde k zalomení ad, jak dlouho trvá každé přerušení, kolik reklamy lze zobrazit v rámci přerušení a co mohou být typy reklamy při přerušení. Následující příklad VMAP souboru, který definuje jeden ad přerušení:

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[http://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
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

Soubor VMAP začíná <VMAP> element, který obsahuje jeden nebo více <AdBreak> prvky nichž každý definuje přerušení ad. Konec každého ad určuje typu break, přerušení ID a časovým posunem. Atribut breakType Určuje typ služby ad, která může být přehrávané během přerušení: lineární, nelineárních, nebo zobrazení. Zobrazit mapu reklamy ROZSÁHLÉ doprovodná reklamy. V seznamu odděleném čárkami (bez mezer) lze zadat více než jeden typ ad. BreakID je volitelný identifikátor služby ad. TimeOffset Určuje, kdy má být zobrazena ad. To se dá nastavit v jednom z následujících způsobů:

1. Čas – ve formátu hh: mm: nebo hh:mm:ss.mmm kde .mmm je milisekund. Hodnota tohoto atributu určuje čas začátku pozastavení ad od začátku časové osy videa.
2. Procento – ve formátu n %, kde n je procento časové osy videa k přehrání před přehrávání ad
3. Začátek/konec – Určuje, že ad mají být zobrazeny před nebo po byla zobrazena videa
4. Umístění – určuje pořadí ad konce, když načasování konce ad je neznámý, například v živého streamování. Pořadí každý konec ad se zadává v #n formát, kde n je celé číslo 1 nebo větší. 1 znamená ad by možné přehrát, při první příležitosti, 2 označuje, že ad by měl být přehrán při druhém příležitosti a tak dále.

V rámci <AdBreak> elementu, může dojít k jednomu <**AdSource**> element. <**AdSource**> prvek obsahuje následující atributy:

1. ID – Určuje identifikátor zdroje služby ad
2. allowMultipleAds – logická hodnota, která určuje, zda při přerušení ad může zobrazovat více reklamy
3. followRedirects – přesměruje volitelná logická hodnota určující, pokud by měl případném dalším sdílení dodržovat přehrávač videa v rámci ad odpovědi

<**AdSource**> element poskytuje hráč odpověď ad vložené nebo odkaz na odpověď ad. Může obsahovat jednu z následujících elementů:

* <VASTAdData> indikuje, že odpověď ROZSÁHLÉ ad je vložený v souboru VMAP
* <AdTagURI> identifikátor URI, který odkazuje na ad odpověď z jiného systému
* <CustomAdData> -libovolný řetězec, který představuje-ROZSÁHLÉ odpovědi

V tomto příkladu se zadaným odpověď ad v řádku <VASTAdData> element, který obsahuje odpověď ROZSÁHLÉ ad. Další informace o dalších prvků, naleznete v tématu [VMAP](http://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

<**AdBreak**> element může obsahovat také jeden <**TrackingEvents**> element. <**TrackingEvents**> element umožňuje sledovat začátek nebo konec přerušení ad nebo zda došlo k chybě během pozastavení ad. <**TrackingEvents**> element obsahuje jeden nebo více <**sledování**> elementy, z nichž každý určuje sledování událostí a sledování identifikátoru URI. Jsou možné sledování události:

1. breakStart – sleduje začátku pozastavení ad
2. breakEnd – sledování dokončení přerušení ad
3. Chyba – sleduje chybu, ke které došlo během pozastavení ad

Následující příklad ukazuje VMAP soubor, který specifikuje události sledování

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

Další informace o <**TrackingEvents**> element a jeho podřízené položky, naleznete v tématu http://iab.net/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Pomocí Media abstraktní klasifikace soubor šablony (STOŽÁRŮ)
Soubor STOŽÁRŮ můžete k určení aktivačních událostí, které definují, když se objeví reklamu. Následuje příklad STOŽÁRŮ souboru, který obsahuje aktivační události pro ad vrácení pre, střední vrácení ad a ad po vrácení.

```xml
    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
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


Soubor STOŽÁRŮ začíná **STOŽÁRŮ** element, který obsahuje jeden **aktivační události** elementu. <triggers> Element obsahuje jeden nebo více **aktivační událost** prvky, které definují, kdy by měl ad přehrána.

**Aktivační událost** obsahuje element **startConditions** element, který zadejte, kdy by měla začít přehrávat ad. **StartConditions** element obsahuje jeden nebo více <condition> elementy. Při každé <condition> vyhodnotí jako true aktivační události je zahájeno nebo odvolat, v závislosti na, jestli se <condition> je součástí **startConditions** nebo **endConditions** – element v uvedeném pořadí. Když více <condition> prvky jsou k dispozici, jsou považovány za implicitní nebo všechny podmínky vyhodnotí na hodnotu true způsobí, že trigger pro zahájení. <condition> mohou být vnořené elementy. Pokud podřízený <condition> jsou přednastaveny elementy, jsou považovány za implicitní a, všechny podmínky se musí vyhodnotit na hodnotu true pro trigger pro zahájení. <condition> Prvek obsahuje následující atributy, které definují podmínky:

1. **typ** – Určuje typ podmínky, události nebo vlastnosti
2. **název** – název vlastnosti nebo události, které se použijí při vyhodnocení
3. **Hodnota** – hodnota, která vlastnost se vyhodnotí oproti
4. **operátor** – operace má použít při vyhodnocení: EQ (rovná), NEQ (není rovno), GTR (větší), GEQ (větší nebo rovnou hodnotě), LT (menší než), LEQ (menší než nebo rovno), MOD (modulo)

**endConditions** také obsahovat <condition> elementy. Pokud je podmínka vyhodnocena jako true aktivační událost se vynuluje. <trigger> Také obsahuje element <sources> element, který obsahuje jeden nebo více <source> elementy. <source> Elementy definovat identifikátor URI odpovědi ad a typ odpovědi ad. V tomto příkladu je uveden identifikátoru URI k ROZSÁHLÉ odpovědi.

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

### <a name="using-video-player-ad-interface-definition-vpaid"></a>Pomocí definice rozhraní Video přehrávač Ad (VPAID)
VPAID je rozhraní API umožňující použití jednotky spustitelného souboru ad ke komunikaci s přehrávač videa. To umožňuje ad vysoce interaktivní prostředí. Uživatelé můžou komunikovat se službou ad a ad můžou reagovat na akce provedené v prohlížeči. Ad může například zobrazit tlačítka, která umožní uživateli zobrazit další informace nebo delší verzi ad. Přehrávač videa musí podporovat rozhraní API VPAID a spustitelné ad musí implementovat rozhraní API. Když hráč požadavky že ze serveru služby ad serveru ad může reagovat obrovského množství odpovědí, který obsahuje VPAID ad.

Spustitelný soubor ad se vytvoří v kódu, který je třeba spustit v běhovém prostředí, jako je například Adobe Flash™ nebo JavaScript, které mohou být provedeny ve webovém prohlížeči. ROZSÁHLÉ odpověď obsahující VPAID ad návratu server služby ad hodnotu apiFramework atribut <MediaFile> element musí být "VPAID". Tento atribut určuje, že omezením ad je spustitelný soubor ad VPAID. Atribut type musí být nastaven na typ MIME spustitelného souboru, například "application/x-shockwave-flash" nebo "application/x-javascript". Následující fragment kódu ukazuje XML <MediaFile> element z ROZSÁHLÉ odpověď obsahující spustitelný soubor ad VPAID.

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type=”application/x-shockwaveflash”
                  width=”640” height=”480” apiFramework=”VPAID”>
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

Spustitelný soubor ad mohou být inicializovány pomocí <AdParameters> element v rámci <Linear> nebo <NonLinear> prvky v odpovědi na velké. Další informace o <AdParameters> prvku, naleznete v tématu [ROZSÁHLÉ 3.0](http://www.iab.net/media/file/VASTv3.0.pdf). Další informace o rozhraní API VPAID najdete v tématu [VPAID 2.0](http://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implementace Windows nebo Windows Phone 8 Player s podporou Ad
Platforma Microsoft Media: Player Framework pro systém Windows 8 a Windows Phone 8 obsahuje kolekci z ukázkových aplikací, které ukazují, jak implementovat aplikace přehrávače videa pomocí rozhraní. Architekturu přehrávače a ukázky od si můžete stáhnout [Player Framework pro systém Windows 8 a Windows Phone 8](https://playerframework.codeplex.com).

Když otevřete řešení Microsoft.PlayerFramework.Xaml.Samples, zobrazí se počet složek v rámci projektu. Inzerování složka obsahuje ukázkový kód, který je relevantní pro vytváření přehrávač videa s podporou ad. Uvnitř reklama složka je počet XAML/cs soubory, které ukazují, jak vkládání reklam jiným způsobem. Následující seznam popisuje všechny:

* AdPodPage.xaml ukazuje, jak zobrazit pod ad.
* AdSchedulingPage.xaml ukazuje, jak k naplánování služby Active Directory.
* FreeWheelPage.xaml ukazuje, jak používat modul plug-in FreeWheel naplánování služby Active Directory.
* MastPage.xaml ukazuje, jak k naplánování služby Active Directory pomocí souboru STOŽÁRŮ.
* ProgrammaticAdPage.xaml ukazuje, jak prostřednictvím kódu programu naplánování služby Active Directory do videa.
* ScheduleClipPage.xaml ukazuje, jak naplánovat ad bez ROZSÁHLÉ souboru.
* VastLinearCompanionPage.xaml ukazuje, jak vložit lineární a doprovodné ad.
* VastNonLinearPage.xaml ukazuje, jak vložit nelineárních ad.
* VmapPage.xaml ukazuje, jak určit reklamy souborem VMAP.

Každá ukázka používá MediaPlayer – třída definována v rámci rozhraní přehrávače. Většina ukázek používat moduly plug-in, které přidává funkce pro různé formáty reklam odpovědi. Ukázka ProgrammaticAdPage programově komunikuje instanci MediaPlayer.

### <a name="adpodpage-sample"></a>Ukázka AdPodPage
Tento příklad používá AdSchedulerPlugin k definování, kdy se má zobrazit reklamu. V tomto příkladu je naplánované ohlášení uprostřed vrácení přehrána po pěti sekundách. Pod ad (skupiny služby Active Directory pro zobrazení v pořadí) je zadáno v souboru ROZSÁHLÉ vrácená ze serveru služby ad. Identifikátor URI pro ROZSÁHLÉ souboru je zadaná v <RemoteAdSource> elementu.

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

Další informace o AdSchedulerPlugin najdete v tématu [reklamy v rámci Playeru ve Windows 8 a Windows Phone 8](http://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
Tato ukázka také používá AdSchedulerPlugin. Naplánuje tři reklamy, před ad, střední role ad a ad po vrácení. Identifikátor URI pro VAST u každé je zadán v <RemoteAdSource> elementu.

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
Tato ukázka používá FreeWheelPlugin, který určuje zdrojový atribut, který určuje identifikátor URI, který odkazuje na soubor SmartXML určující ad obsahu, jakož i informace o plánování ad.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage
Tato ukázka používá MastSchedulerPlugin, která umožňuje použít soubor STOŽÁRŮ. Zdrojový atribut určuje umístění souboru STOŽÁRŮ.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Tato ukázka programově komunikuje MediaPlayer. Soubor ProgrammaticAdPage.xaml vytvoří instanci MediaPlayer:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

Soubor ProgrammaticAdPage.xaml.cs vytvoří AdHandlerPlugin, přidá TimelineMarker zadat při ad by se mělo zobrazit a pak přidá obslužnou rutinu pro událost MarkerReached, který načte RemoteAdSource určující identifikátor URI pro ROZSÁHLÉ souboru a pak hraje ad.

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
Tento příklad používá AdSchedulerPlugin naplánování uprostřed vrácení ad tak, že zadáte soubor .wmv, který obsahuje ad.

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
Tento příklad ukazuje, jak použít AdSchedulerPlugin k plánování střední vrácení lineární ad se službou companion ad. <RemoteAdSource> Prvek určuje umístění souboru obrovského množství.

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
Tato ukázka používá AdSchedulerPlugin naplánování lineární a nelineárních ad. Umístění souboru ROZSÁHLÉ zadán s parametrem <RemoteAdSource> elementu.

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
Tento příklad používá VmapSchedulerPlugin naplánování služby Active Directory pomocí souboru VMAP. Identifikátor URI souboru VMAP je zadaný v atributu zdrojové <VmapSchedulerPlugin> elementu.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Implementace přehrávače videa s podporou Ad pro iOS
Platforma Microsoft Media: Architekturu přehrávače pro iOS obsahuje kolekci z ukázkových aplikací, které ukazují, jak implementovat aplikace přehrávače videa pomocí rozhraní. Architekturu přehrávače a ukázky od si můžete stáhnout [Azure Media Player Framework](https://github.com/Azure/azure-media-player-framework). Na stránce Githubu má odkaz na Wiki, který obsahuje další informace o architekturu přehrávače a úvod do ukázkové player: [Azure Media Player Wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Plánování služby Active Directory s VMAP
Následující příklad ukazuje, jak k naplánování služby Active Directory pomocí souboru VMAP.

```csharp
    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
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

### <a name="scheduling-ads-with-vast"></a>Plánování služby Active Directory s VAST
Následující příklad ukazuje, jak naplánovat pozdní ROZSÁHLÉ ad vazby.


```csharp
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
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

   Následující příklad ukazuje, jak naplánovat předčasné ROZSÁHLÉ ad vazby.

```csharp
    //Example:4 Schedule an early binding VAST ad
    //Download the VAST file
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]])
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

Následující příklad ukazuje, jak vložit ad pomocí hrubý vyjmout úpravy (zdrojový)

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

Následující příklad ukazuje, jak naplánovat pod ad.

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

Následující příklad ukazuje, jak naplánovat – rychlé ad uprostřed vrácení. Rychlé ad pouze přehrání po bez ohledu na jakékoli hledání v prohlížeči provede.

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

Následující příklad ukazuje, jak naplánovat rychlé ad uprostřed vrácení. Jako vždy navrchu ad se zobrazí pokaždé, když je dosaženo Zadaný bod v časové osy videa.

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

Následující příklad ukazuje, jak naplánovat po vrácení ad.

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

Následující příklad ukazuje, jak plánovat před ad.

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

Následující příklad ukazuje, jak naplánovat ad uprostřed vrácení překrytí.

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


## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Vývoj aplikací videopřehrávače](media-services-develop-video-players.md)

