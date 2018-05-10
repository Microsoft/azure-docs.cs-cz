---
title: Smooth streamování modul plug-in pro rozhraní médií s otevřeným zdrojem
description: Další informace o použití modulu plug-in Azure Media Services technologie Smooth Streaming pro rozhraní Adobe otevřený zdroj média.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
ms.openlocfilehash: 6a4ae0697e59eb00e216fc29b154408e4d7a99b8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Postup použití Microsoft funkce Smooth Streaming modulu plug-in pro Media Framework Adobe Open Source
## <a name="overview"></a>Přehled
Modul plug-in Microsoft technologie Smooth Streaming pro otevřený zdroj Media Framework 2.0 (SS pro OSMF) rozšiřuje možnosti výchozí OSMF a přidá do nových nebo stávajících OSMF přehrávače Microsoft Smooth Streaming přehrávání obsahu. Tento modul plug-in také přidá funkce Smooth Streaming přehrávání k přehrávání zábleskové média (SMP).

SS pro OSMF zahrnuje dvě verze modulu plug-in:

* Statické technologie Smooth Streaming modul plug-in pro OSMF (.swc)
* Dynamické technologie Smooth Streaming modul plug-in pro OSMF (SWF)

Tento dokument předpokládá, že má čtečka obecné praktické znalosti OSMF a OSMF moduly plug-in. Další informace o OSMF, naleznete v dokumentaci na [oficiální web OSMF](http://osmf.org/).

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Modul plug-in technologie Smooth Streaming pro OSMF 2.0
Tento modul plug-in podporuje načítání a přehrávání obsahu na vyžádání funkce Smooth Streaming s následující funkce:

* Přehrávání technologie Smooth Streaming na vyžádání (Play, pozastavení, hledání, zastavte)
* Přehrávání živé vysílání funkce Smooth Streaming (Play)
* Funkce formátu DVR (pozastavení, hledání, přehrávání formátu DVR, přejděte to-Live) za provozu
* Podpora pro video kodeky - H.264
* Podpora pro zvuk kodeky - AAC
* Přepínání s rozhraními API předdefinované OSMF více jazyků zvuk
* Maximální počet přehrávání kvality výběr OSMF integrované rozhraní API
* Něho uzavřený titulky s titulky OSMF modulu plug-in
* Adobe&reg; Flash&reg; Player 11.4 nebo vyšší.
* Tato verze podporuje pouze OSMF 2.0.

## <a name="supported-features-and-known-issues"></a>Podporované funkce a známé problémy
Úplný seznam podporovaných funkcích, nepodporované funkce a známé problémy, najdete v části [tento dokument](http://download.microsoft.com/download/3/1/B/31B63D97-574E-4A8D-BF8D-170744181724/Smooth_Streaming_Plugin_for_OSMF.pdf).

## <a name="loading-the-plugin"></a>Načítání modulu plug-in
Moduly plug-in OSMF mohou být načteny staticky (v době kompilace) nebo dynamicky (za běhu). Modul plug-in technologie Smooth Streaming pro stažení OSMF včetně dynamických a statických verzí.

* Statické načítání: načtení staticky, statické knihovny (SWC) vyžaduje se soubor. Statické modulů plug-in jsou přidány jako odkaz na projekty a sloučení do konečné výstupní soubor v době kompilace.
* Dynamické načtení: K načtení dynamicky předkompilovaných (SWF) vyžaduje se soubor. Dynamických modulů plug-in jsou načtena v modulu runtime a nejsou zahrnuty ve výstupu projektu. (Kompilované výstup) Dynamických modulů plug-in můžete načíst pomocí protokolů HTTP a souboru.

Další informace o statické a dynamické načítání, najdete v oficiální [stránka modulů plug-in webu OSMF](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

### <a name="ss-for-osmf-static-loading"></a>SS pro statické načítání OSMF
Následující fragment kódu ukazuje, jak načíst modul plug-in SS pro OSMF staticky- and -play základní video používání OSMF MediaFactory třídy. Před včetně SS pro OSMF kód, ujistěte se prosím, že odkaz na projekt obsahuje statické modulu plug-in "MSAdaptiveStreamingPlugin v1.0.3 osmf2.0.swc".

```
package 
{

    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;



    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;

            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")

        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;

            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```


### <a name="ss-for-osmf-dynamic-loading"></a>SS pro dynamické načítání OSMF
Následující fragment kódu ukazuje, jak načíst modul plug-in SS pro OSMF dynamicky- and -play základní videa pomocí třídy OSMF MediaFactory. Před zahrnutím SS pro OSMF kód, zkopírujte do složky projektu dynamických modulů plug-in "MSAdaptiveStreamingPlugin v1.0.3 osmf2.0.swf", pokud chcete načíst pomocí souboru protokolu, nebo zkopírujte pod webový server pro načtení protokolu HTTP. Je potřeba zahrnout "MSAdaptiveStreamingPlugin v1.0.3 osmf2.0.swc" do odkazů projektu.

{balíčku

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;


    //Sets the size of the SWF

    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Přehrávání zábleskové médií s dynamických modulů plug-in SS ODMF
Technologie Smooth Streaming pro OSMF dynamických modulů plug-in není kompatibilní s [zábleskové média přehrávání (SMP)](http://osmf.org/strobe_mediaplayback.html). SS pro modul plug-in OSMF můžete použít pro přidání do SMP technologie Smooth Streaming přehrávání obsahu. K tomu, zkopírujte "MSAdaptiveStreamingPlugin v1.0.3 osmf2.0.swf" v části webový server, pro zatížení HTTP pomocí následujících kroků:

1. Přejděte [stránce instalace přehrávání médií zábleskové](http://osmf.org/dev/2.0gm/setup.html). 
2. Nastavit src ke zdroji technologie Smooth Streaming (např.) http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3. Proveďte změny požadované konfigurace a klikněte na položku Preview a aktualizace.
   
   **Poznámka:** obsahu webového serveru musí platný crossdomain.xml. 
4. Zkopírujte a vložte kód do jednoduchá stránka HTML pomocí svém oblíbeném textovém editoru, například v následujícím příkladu:

        <html>
        <body>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
        </embed>
        </object>
        </body>
        </html>



1. Přidat modul plug-in technologie Smooth Streaming OSMF kód pro vložení a uložit.
   
        <html>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
        </embed>
        </object>
        </html>
2. Uložte stránku HTML a publikovat na webový server. Přejděte do publikované webové stránce pomocí vašeho oblíbeného Flash&reg; Player povoleno internetového prohlížeče (Internet Explorer, Chrome, Firefox, atd.).
3. Získejte technologie Smooth Streaming obsah uvnitř aplikace Adobe&reg; Flash&reg; přehrávač.

Další informace o obecné OSMF vývoj, najdete v oficiální [OSMF vývoj stránky](http://osmf.org/resources.html).

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Microsoft adaptivní datové proudy modulu plug-in pro aktualizaci OSMF](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

