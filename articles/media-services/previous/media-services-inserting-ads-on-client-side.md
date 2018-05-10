---
title: Vkládání reklam na straně klienta | Microsoft Docs
description: Toto téma ukazuje, jak vložit reklamy na straně klienta.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
ms.openlocfilehash: 57cb06fcee029f72dd1cf81ef086985fdda3f45f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="inserting-ads-on-the-client-side"></a>Vkládání reklam na straně klienta
Tento článek obsahuje informace o tom, jak vložit různých typů reklamy na straně klienta.

Informace o podpoře uzavřené přidávání titulků a ad v živé streamování videa najdete v tématu [podporované titulky a standardy vložení Ad](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> Azure Media Player v současné době nepodporuje reklamy.
> 
> 

## <a id="insert_ads_into_media"></a>Vkládání reklamy do vašeho média
Azure Media Services poskytuje podporu pro vkládání reklam prostřednictvím platformu Windows Media: architektury přehrávačů. Architektury přehrávačů s podporou ad jsou dostupné pro zařízení s Windows 8, Silverlight, Windows Phone 8 a iOS. Každý player framework obsahuje ukázkový kód, který ukazuje, jak implementovat aplikace přehrávače. Existují tři různé druhy služby Active Directory, které lze vložit do seznamu média: seznam.

* **Lineární** – úplné rámce služby Active Directory, které pozastavit hlavní video.
* **Nelineární** – reklamy překrytí, které se zobrazují jako přehrávání videa hlavní, obvykle logo nebo jiné statický obrázek umístit přehrávač.
* **Doprovodná** – reklamu, které se zobrazují mimo přehrávač.

Služby Active Directory mohou být umístěny v libovolném bodě hlavní video časovou osu. Přehrávač musí zjistit, kdy přehrávání ad a které reklamy přehrávání. To se provádí pomocí sady standardní soubory formátu XML: Video Ad služby šablony (VAST), více Ad seznam stop (VMAP) ve digitální Video, šablony abstraktní sekvencování na média (STOŽÁRŮ) a digitální Video Player Ad rozhraní definice (VPAID). VELKÁ soubory zadejte co zobrazit reklamy. Soubory VMAP určete, kdy k přehrávání různých reklamy a obsahují velká XML. STOŽÁRŮ soubory jsou jiný způsob, jak pořadí služby Active Directory, které také může obsahovat velká XML. Soubory VPAID definovat rozhraní mezi přehrávání videa a ad nebo serveru služby ad.

Každý player framework funguje jinak, každý bude uvedena v jeho vlastní článku. Tento článek popisuje základní mechanismus používaný k vkládání reklam. Aplikací pro přehrávání videa žádost služby Active Directory ze serveru služby ad. Serveru služby ad může reagovat v mnoha různými způsoby:

* Vrátí soubor velká
* Vrátí VMAP soubor (s embedded VAST)
* Vrátit STOŽÁRŮ soubor (s embedded VAST)
* Vrátí velká soubor s VPAID služby Active Directory

### <a name="using-a-video-ad-service-template-vast-file"></a>Pomocí souboru šablony (VAST) služby Video Ad
Soubor velká Určuje, jaké ad nebo zobrazit reklamy. Následující kód XML je příkladem soubor velká pro lineární ad:

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
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
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

Lineární ad je popsán <**lineární**> elementu. Určuje dobu trvání ad, sledování událostí, klikněte na tlačítko prostřednictvím, klikněte na sledování a několika **MediaFile** elementy. Sledování události jsou určené v rámci <**TrackingEvents**> elementu a povolit serveru služby ad sledovat různé události, ke kterým došlo při prohlížení ad. V tomto případě začátku středový, dokončení a rozbalte události se sledují. Událost spuštění nastane, když se zobrazí ad. Středový událost nastane, když alespoň, že byl zobrazen 50 % osy ad. Událost complete nastane, když ad byla spuštěna na konec. Rozbalte událost nastane, když uživatel rozšíří přehrávání videa na celou obrazovku. Se zadaným Clickthroughs <**interaktivní**> v rámci <**VideoClicks**> elementu a určuje identifikátoru URI prostředku zobrazíte, když uživatel klikne na ad. ClickTracking je uveden v <**ClickTracking**> elementu, také uvnitř <**VideoClicks**> elementu a určuje sledování prostředků pro player k vyžádání, když uživatel klikne na ad . <**MediaFile**> elementy zadejte informace o konkrétní kódování ad. Když je více než jedna <**MediaFile**> elementu přehrávání videa můžete zvolit nejvhodnější kódování pro platformu. 

Lineární reklamy lze zobrazit v zadaném pořadí. Chcete-li to provést, přidejte další <Ad> elementy na VAST souboru a určit pořadí, pomocí pořadí atributu. Následující příklad ilustruje toto:

```xml
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResouce]]></Impression>
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
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResouce]]></Impression>
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

Nelineární reklamy jsou určené v <Creative> také element. Následující příklad ukazuje <Creative> element, který popisuje nelineární ad.

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

<**NonLinearAds**> element může obsahovat jednu nebo více <**NonLinear**> prvky, z nichž každý lze popsat nelineární ad. <**NonLinear**> element určuje prostředek pro nelineární ad. Prostředek může být <**StaticResouce**>, <**IFrameResource**>, nebo <**HTMLResouce**>. <**StaticResource**> popisuje prostředků jiného typu než HTML a definuje creativeType atribut, který určuje, jak se zobrazí prostředku:

Bitové kopie nebo gif, image nebo jpeg, image nebo png – prostředku se zobrazí v kódu HTML <**img**> značky.

Application/x-javascript – prostředku se zobrazí v kódu HTML <**skriptu**> značky.

Application/x-shockwave-flash – prostředku se zobrazí v Flash player.

**IFrameResource** popisuje prostředek HTML, který lze zobrazit v elementu IFrame. **HTMLResource** popisuje kód HTML, který lze vložit do webové stránky. **TrackingEvents** zadejte sledování událostí a identifikátor URI požadavku dojde k události. V této ukázce jsou sledovány acceptInvitation a sbalit události. Další informace o **NonLinearAds** elementu a jeho podřízených položek, najdete v části IAB.NET/VAST. Všimněte si, že **TrackingEvents** se nachází v rámci elementu **NonLinearAds** element místo **NonLinear** element.

Doprovodná reklamy jsou definovány v rámci <CompanionAds> elementu. <CompanionAds> Element může obsahovat jednu nebo více <Companion> elementy. Každý <Companion> element popisuje doprovodné ad a může obsahovat <StaticResource>, <IFrameResource>, nebo <HTMLResource> které je určené stejným způsobem jako nelineární ad. VELKÁ soubor může obsahovat více doprovodných reklamy a aplikace přehrávače můžete zvolit nejvhodnější ad k zobrazení. Další informace o VAST najdete v tématu [velká 3.0](http://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Použití více soubor seznamu stop (VMAP) Ad digitální Video
Soubor VMAP umožňuje zadat, když dojde k zalomení ad, jak dlouho je každý konec, kolik reklamy, může se zobrazit v rámci zalomení a jaké typy služby Active Directory může být při zalomení zobrazovat. Následující soubor VMAP příklad, který definuje zalomení jeden ad:

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
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scaleable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
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

Soubor VMAP začíná <VMAP> element, který obsahuje jeden nebo více <AdBreak> elementy, každý definování přerušení služby ad. Každý ad zalomení Určuje typ ukončení, pozastavení ID a posun času. Atribut breakType Určuje typ ad, která může být přehráván během rozdělení: lineární, nelineární, nebo zobrazení. Pro velká doprovodné reklamy zobrazit reklamy mapy. Více než jeden typ ad můžete zadat seznam oddělený čárkami (bez mezer). BreakID je volitelné identifikátor pro služby ad. TimeOffset Určuje, kdy má být zobrazena ad. Určit lze v jednom z následujících způsobů:

1. Čas – ve formátu hh: mm: nebo hh:mm:ss.mmm kde .mmm je milisekundách. Hodnota tohoto atributu určuje dobu od začátku video časovou osu na začátek rozdělení ad.
2. Procento – ve formátu n % tam, kde n je procento video časovou osu přehrávání před přehrávání ad
3. Počáteční nebo koncové – Určuje, že ad by měla zobrazit před nebo po video byla zobrazena.
4. Pozice – určuje pořadí zalomení ad, když načasování zalomení ad je neznámý, například v živé vysílání datového proudu. Pořadí od konce každé ad je zadána ve formátu #n, kde n je celé číslo větší nebo 1. 1 znamená, že neexistuje ad by měla být přehráván při první příležitosti 2 označuje, že ad by měla být přehráván na druhou možnost a tak dále.

V rámci <AdBreak> elementu, může být jedna <**AdSource**> elementu. <**AdSource**> element obsahuje následující atributy:

1. ID – Určuje identifikátor zdroje ad
2. allowMultipleAds – logická hodnota, která určuje, zda lze zobrazit více reklamy během pozastavení ad
3. followRedirects – přesměruje volitelné logickou hodnotu, která určuje, pokud by měl respektovat přehrávání videa v rámci odpověď ad

<**AdSource**> element poskytuje přehrávač odpověď ad vložené nebo odkaz na odpověď ad. Může obsahovat jeden z následujících elementů:

* <VASTAdData> Označuje, že odpověď velká ad vložené v rámci tohoto souboru VMAP
* <AdTagURI> identifikátor URI, který odkazuje na odpověď ad z jiného systému
* <CustomAdData> -libovolný řetězec, který reprezentuje-velká odpověď

V tomto příkladu je definován odpověď ad v řádku s <VASTAdData> element, který obsahuje odpověď velká ad. Další informace o dalších prvků najdete v tématu [VMAP](http://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

<**AdBreak**> element může také obsahovat jednu <**TrackingEvents**> elementu. <**TrackingEvents**> elementu umožňuje sledovat počáteční nebo koncový přerušení služby ad nebo zda došlo k chybě během pozastavení ad. <**TrackingEvents**> element obsahuje jeden nebo více <**sledování**> prvky, z nichž každý určuje sledování událostí a sledování identifikátor URI. Jsou možné sledování události:

1. breakStart – sleduje začátku přerušení služby ad
2. breakEnd – sledování dokončení přerušení služby ad
3. Chyba – sleduje chybu, ke které došlo k chybě během pozastavení ad

Následující příklad ukazuje VMAP soubor, který určuje sledování událostí

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

Další informace o <**TrackingEvents**> elementu a jeho podřízených položek, najdete v tématu http://iab.org/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Pomocí médií abstraktní, pořadí úloh souboru šablony (STOŽÁRŮ)
Soubor STOŽÁRŮ umožňuje zadat aktivační události, které definují, kdy se má zobrazit ad. Následující příklad je STOŽÁRŮ soubor obsahující aktivačních událostí pro před kumulativní ad, střední kumulativní ad a po vrácení ad.

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


Soubor STOŽÁRŮ začíná **STOŽÁRŮ** element, který obsahuje jeden **aktivační události** elementu. <triggers> Element obsahuje jeden nebo více **aktivační událost** prvky, které definují, kdy by měly být přehrány ad. 

**Aktivační událost** obsahuje element **startConditions** element, který zadejte, kdy by měl začínat ad přehrávání. **StartConditions** element obsahuje jeden nebo více <condition> elementy. Při každé <condition> vyhodnotí jako true aktivační událost se zahájí nebo odebrán, podle toho, jestli <condition> je obsažena v **startConditions** nebo **endConditions** element v uvedeném pořadí. Když více <condition> elementy jsou přítomny, jsou považovány za implicitní nebo, aktivační událost zahájíte způsobí, že všechny podmínky vyhodnocení na hodnotu true. <condition> elementy mohou být použity. Když podřízené <condition> jsou přednastavení elementy, jsou považovány za implicitní a, všechny podmínky musí vyhodnotit na hodnotu true pro aktivační událost k zahájení. <condition> Prvek obsahuje následující atributy, které definují podmínky: 

1. **typ** – Určuje typ podmínky, události nebo vlastnosti
2. **název** – název vlastnosti nebo událost, která má použít při vyhodnocení
3. **Hodnota** – hodnota, která vlastnost vyhodnotí proti
4. **operátor** – operaci má použít při vyhodnocení: EQ (stejná), NEQ (není rovno), GTR (větší), GEQ (větší nebo rovna), LT (menší než), LEQ (je menší než nebo rovno), MOD (modulo)

**endConditions** také obsahovat <condition> elementy. Pokud je podmínka vyhodnocena jako true aktivační událost se resetuje. <trigger> Také obsahuje element <sources> element, který obsahuje jeden nebo více <source> elementy. <source> Elementy zadejte identifikátor URI odpovědi ad a typ odpovědi ad. V tomto příkladu je identifikátor URI zadána odpověď na velká. 

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

### <a name="using-video-player-ad-interface-definition-vpaid"></a>Pomocí definice rozhraní Video Player ve službě Active Directory (VPAID)
VPAID je rozhraní API pro povolení spustitelné ad jednotky ke komunikaci s přehrávání videa. To umožňuje prostředí vysoce interaktivní ad. Uživatel může komunikovat s ad a služby ad může reagovat na akce provedené v prohlížeči. Například může ad zobrazení tlačítka, která umožňují uživateli zobrazit další informace nebo delší verze služby ad. Přehrávání videa musí podporovat rozhraní API VPAID a spustitelné ad musí implementovat rozhraní API. Když přehrávač požadavky že ze serveru služby ad serveru ad může být použit velká odpovědi, která obsahuje VPAID ad.

Spustitelný soubor ad se vytvoří v kódu, který je třeba spustit v prostředí runtime například Adobe Flash™ nebo JavaScript, může být spuštěn ve webovém prohlížeči. Po návratu velká odpověď obsahující VPAID ad serveru služby ad hodnotu apiFramework atribut <MediaFile> element musí být "VPAID". Tento atribut určuje, že obsahují ad je spustitelný soubor ad VPAID. Atribut type musí být nastaven na typ MIME ke spustitelnému souboru, například "application/x-shockwave-flash" nebo "application/x-javascript". Následující fragment kódu ukazuje XML <MediaFile> element z velká odpověď obsahující spustitelné ad VPAID. 

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type=”application/x-shockwaveflash”
                  width=”640” height=”480” apiFramework=”VPAID”>
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

Spustitelný soubor ad může být inicializována pomocí <AdParameters> v rámci <Linear> nebo <NonLinear> elementy v odpovědi na velká. Další informace o <AdParameters> elementu, najdete v části [velká 3.0](http://www.iab.net/media/file/VASTv3.0.pdf). Další informace o rozhraní API VPAID najdete v tématu [VPAID 2.0](http://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implementace systému Windows nebo Windows Phone 8 hráče s podpora služby Ad
Platforma Microsoft média: Player Framework pro Windows 8 a Windows Phone 8 obsahuje kolekci ukázkové aplikace, které ukazují, jak implementovat přehrávání videa aplikace pomocí rozhraní. Můžete si stáhnout přehrávač Framework a ukázky z [Player Framework pro Windows 8 a Windows Phone 8](https://playerframework.codeplex.com).

Když otevřete řešení Microsoft.PlayerFramework.Xaml.Samples, zobrazí se počet složek v projektu. Inzerování složka obsahuje ukázkový kód, který je důležité pro vytvoření přehrávání videa s podpora služby ad. Uvnitř reklama složka je počet XAML nebo cs soubory, které ukazují, jak vložit reklamy jiným způsobem. Následující seznam popisuje všechny:

* AdPodPage.xaml ukazuje způsob zobrazení pod služby ad.
* AdSchedulingPage.xaml ukazuje, jak při plánování služby Active Directory.
* FreeWheelPage.xaml ukazuje, jak naplánování služby Active Directory pomocí modulu plug-in FreeWheel.
* MastPage.xaml ukazuje, jak při plánování služby Active Directory pomocí souboru STOŽÁRŮ.
* ProgrammaticAdPage.xaml ukazuje, jak programově naplánování služby Active Directory do video.
* ScheduleClipPage.xaml ukazuje, jak naplánovat ad bez velká souboru.
* VastLinearCompanionPage.xaml ukazuje, jak k vložení lineární a doprovodné ad.
* VastNonLinearPage.xaml ukazuje, jak vložit – lineární ad.
* VmapPage.xaml ukazuje, jak zadat soubor VMAP služby Active Directory.

Všechny tyto ukázky používá třídu Media Player definované rozhraní přehrávač. Většina ukázek pomocí modulů plug-in, který přidat podporu pro různými formáty odpovědi ad. Ukázka ProgrammaticAdPage prostřednictvím kódu programu komunikuje s instancí Media Player.

### <a name="adpodpage-sample"></a>Ukázka AdPodPage
Tato ukázka používá AdSchedulerPlugin definovat, kdy se mají zobrazit ad. V tomto příkladu inzerování střední kumulativní oprava má být přehráván po pět sekund. Pod ad (skupina služby Active Directory pro zobrazení v pořadí) je zadána v velká souboru, kterou vrátil server služby ad. Identifikátor URI pro velká souboru je uveden v <RemoteAdSource> elementu.

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

Další informace o AdSchedulerPlugin najdete v tématu [inzerování v rámci Player ve Windows 8 a Windows Phone 8](http://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
Tato ukázka používá také AdSchedulerPlugin. Naplánuje tři služby Active Directory, ad před, střední kumulativní ad a po vrácení ad. Zadaný identifikátor URI pro VAST u každé reklamy v <RemoteAdSource> elementu.

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
Tato ukázka používá FreeWheelPlugin, která určuje zdrojový atribut, který určuje identifikátor URI, který ukazuje na soubor SmartXML, který určuje ad obsahu a také informace o plánování ad.

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
Tato ukázka prostřednictvím kódu programu komunikuje s Media Player. Soubor ProgrammaticAdPage.xaml vytvoří Media Player:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

Soubor ProgrammaticAdPage.xaml.cs vytvoří AdHandlerPlugin, přidá TimelineMarker zadat při ad mají být zobrazeny a potom přidá obslužné rutiny pro MarkerReached událost, která načte RemoteAdSource, určující identifikátor URI pro velká soubor a pak hraje ad.

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
Tato ukázka používá AdSchedulerPlugin k naplánování střední kumulativní ad zadáním soubor .wmv, který obsahuje ad.

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
Tato ukázka znázorňuje, jak naplánovat střední kumulativní lineární ad s ad doprovodné pomocí AdSchedulerPlugin. <RemoteAdSource> Element určuje umístění souboru velká.

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
Tato ukázka používá AdSchedulerPlugin při plánování lineární a -lineární ad. Umístění souboru velká zadaný <RemoteAdSource> elementu.

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
Tato ukázka používá k naplánování služby Active Directory pomocí souboru VMAP VmapSchedulerPlugin. Zadaný identifikátor URI k souboru VMAP v zdrojový atribut <VmapSchedulerPlugin> elementu.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Implementace iOS Video hráče s podpora služby Ad
Platforma Microsoft média: Architektura Player pro iOS obsahuje kolekci ukázkové aplikace, které ukazují, jak implementovat přehrávání videa aplikace pomocí rozhraní. Můžete si stáhnout přehrávač Framework a ukázky z [Azure Media Player Framework](https://github.com/Azure/azure-media-player-framework). Stránku githubu obsahuje odkaz na Wiki, který obsahuje další informace o rozhraní player a úvod do ukázka player: [Wiki přehrávač médií Azure](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Plánování služby Active Directory s VMAP
Následující příklad ukazuje, jak při plánování služby Active Directory pomocí souboru VMAP.

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
Následující příklad ukazuje, jak naplánovat pozdní vazby velká ad.


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

   Následující příklad ukazuje, jak naplánovat ad pro velká časné vazby.

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

Následující příklad ukazuje, jak vložit ad pomocí hrubý vyjmout úpravy (RCE)

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

Následující příklad ukazuje, jak naplánovat pod služby ad.

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

Následující příklad ukazuje, jak naplánovat-rychlých ad střední vrácení. Rychlých ad pouze přehraje po bez ohledu na jakékoli vyhledávání se provádí v prohlížeči.

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

Následující příklad ukazuje, jak naplánovat rychlých ad střední vrácení. Rychlých ad se zobrazí pokaždé, když je dosaženo Zadaný bod na video časovou osu.

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

Následující příklad ukazuje, jak naplánovat před ad.

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

Následující příklad ukazuje, jak naplánovat ad střední kumulativní překrytí.

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

