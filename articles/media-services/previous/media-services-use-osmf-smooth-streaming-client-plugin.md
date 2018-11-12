---
title: Modul plug-in Smooth Streaming pro Open Source Media Framework
description: Zjistěte, jak používat modul plug-in Azure Media Services technologie Smooth Streaming pro Adobe Open Source Media Framework.
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
ms.openlocfilehash: 1664dc16e93fa825962690ec3ed5681478005d59
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229624"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Jak používat Microsoft Smooth Streaming modulu plug-in pro Adobe Open Source Media Framework
## <a name="overview"></a>Přehled
Modul plug-in Microsoft Smooth Streaming pro otevřený zdroj Media Framework 2.0 (SS pro OSMF) rozšiřuje možnosti výchozí OSMF a přidá přehrávání obsahu Microsoft Smooth Streaming OSMF hráči nové i stávající. Modul plug-in také přidává funkce Smooth Streaming přehrávání pro přehrávání médií zábleskové (SMP).

SS pro OSMF zahrnuje dvě verze modulu plug-in:

* Statické modulu plug-in Smooth Streaming pro OSMF (.swc)
* Dynamický modul plug-in Smooth Streaming pro OSMF (SWF)

Tento dokument předpokládá, že má čtečka obecné praktické znalosti OSMF a OSMF moduly plug-in. Další informace o OSMF, najdete v dokumentaci na [oficiální web OSMF](http://osmf.org/).

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Technologie Smooth Streaming modul plug-in pro OSMF 2.0
Modul plug-in podporuje načítání a přehrávání obsahu Smooth Streaming na vyžádání s následujícími funkcemi:

* Přehrávání technologie Smooth Streaming na vyžádání (přehrát, pozastavit, hledání, Stop)
* Živé vysílání funkce Smooth Streaming přehrávání (Přehrát)
* Živá funkce DVR (pozastavení, hledání, přehrávání DVR, Go-to-Live)
* Podpora pro video kodeků - H.264
* Podpora pro zvuk kodeky - AAC
* Vícejazyčné zvuku přímé přepnutí s OSMF integrovaná rozhraní API
* Maximální počet přehrávání kvality výběr OSMF integrovaná rozhraní API
* Sajdkára skryté titulky s modulem plug-in titulky OSMF
* Adobe&reg; Flash&reg; Player 11.4 nebo novější.
* Tato verze podporuje pouze OSMF 2.0.

## <a name="supported-features-and-known-issues"></a>Podporované funkce a známé problémy
Úplný seznam podporovaných funkcí, nepodporované funkce a známých problémů najdete v tématu [tento dokument](https://download.microsoft.com/download/3/1/B/31B63D97-574E-4A8D-BF8D-170744181724/Smooth_Streaming_Plugin_for_OSMF.pdf).

## <a name="loading-the-plugin"></a>Načtení modulu plug-in
Moduly plug-in OSMF můžete načíst staticky (v době kompilace) nebo dynamicky (za běhu). Modul plug-in Smooth Streaming pro OSMF stažení obsahuje statické i dynamické verze.

* Statické načítání: Pokud chcete načíst staticky, je potřeba soubor statické knihovny (SWC). Statické moduly plug-in jsou přidány jako odkaz na projekty a sloučení do výsledného výstupního souboru v době kompilace.
* Dynamické načítání: Pokud chcete dynamicky načíst, je potřeba předkompilovaného souboru (SWF). Dynamické moduly plug-in jsou načteny v modulu runtime a nejsou zahrnuty do výstupu projektu. (Kompilovaný výstup) Dynamické moduly plug-in můžete načíst pomocí protokolů HTTP a soubor.

Další informace o statické a dynamické načítání, najdete v oficiální [stránka modulů plug-in OSMF](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

### <a name="ss-for-osmf-static-loading"></a>SS pro statické načítání OSMF
Následující fragment kódu ukazuje, jak načíst modul plug-in SS pro OSMF staticky a přehrát základní videa pomocí OSMF MediaFactory třídy. Před zahrnutím SS pro OSMF kódu, ujistěte se, že odkaz na projekt obsahuje statické modulu plug-in "MSAdaptiveStreamingPlugin v1.0.3 osmf2.0.swc".

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
Následující fragment kódu ukazuje, jak dynamicky načíst modul plug-in SS pro OSMF a přehrát základní videa pomocí OSMF MediaFactory třídy. Před SS pro OSMF kódu, včetně dynamických modulů plug-in "MSAdaptiveStreamingPlugin v1.0.3 osmf2.0.swf" zkopírujte do složky projektu, pokud chcete načíst pomocí souboru protokolu nebo zkopírujte v části webového serveru pro zatížení protokolu HTTP. Není nutné zahrnout "MSAdaptiveStreamingPlugin v1.0.3 osmf2.0.swc" do odkazů projektu.

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

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Zábleskové přehrání média pomocí dynamických modulů plug-in SS ODMF
Technologie Smooth Streaming pro OSMF dynamických modulů plug-in je kompatibilní s [přehrávání médií zábleskové (SMP)](http://osmf.org/strobe_mediaplayback.html). SS pro modul plug-in OSMF slouží k přidání přehrávání obsahu Smooth Streaming na SMP. K tomuto účelu zkopírujte "MSAdaptiveStreamingPlugin v1.0.3 osmf2.0.swf" v části webového serveru pro zatížení protokolu HTTP pomocí následujících kroků:

1. Přejděte [stránku nastavení přehrávání médií zábleskové](http://osmf.org/dev/2.0gm/setup.html). 
2. Nastavte src ke zdroji technologie Smooth Streaming (např.) http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3. Proveďte změny požadované konfigurace a klikněte na tlačítko ve verzi Preview a aktualizace.
   
   **Poznámka:** potřebuje platný crossdomain.xml obsahu webového serveru. 
4. Zkopírujte a vložte kód pro jednoduché stránky HTML pomocí oblíbeného textového editoru, jako v následujícím příkladu:

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



1. Přidat modul plug-in Smooth Streaming OSMF kód pro vložení a uložit.
   
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
2. Stránku HTML uložit a publikovat na webový server. Přejděte na publikovanou webovou stránku pomocí oblíbených Flash&reg; Player povolené internetového prohlížeče (Internet Explorer, Chrome, Firefox, tak dále).
3. Užijte si obsah Smooth Streaming uvnitř Adobe&reg; Flash&reg; přehrávače.

Další informace o obecný vývoj OSMF prosím najdete v oficiální [OSMF vývoj stránky](http://osmf.org/resources.html).

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Microsoft s adaptivním streamováním modulu plug-in pro aktualizaci OSMF](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

