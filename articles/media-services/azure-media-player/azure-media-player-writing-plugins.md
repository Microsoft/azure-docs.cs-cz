---
title: Psaní pluginů pro Azure Media Player
description: Naučte se psát plugin pomocí Azure Media Player u JavaScriptu
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: b7aac80b19a7c30d994f3c14e19047583d5334ac
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727109"
---
# <a name="writing-plugins-for-azure-media-player"></a>Psaní pluginů pro Azure Media Player #

Plugin je JavaScript psaný rozšířit nebo zlepšit přehrávač. Můžete psát pluginy, které mění vzhled programu Azure Media Player, jeho funkčnost nebo dokonce mají rozhraní s jinými službami. Můžete to udělat ve dvou jednoduchých krocích:

## <a name="step-1"></a>Krok 1 ##

Napište svůj JavaScript ve funkci, jako je takto:

```javascript

    (function () {
        amp.plugin('yourPluginName', function (options) {
        var myPlayer = this;
           myPlayer.addEventListener(amp.eventName.ready, function () {
        console.log("player is ready!");
            });
        });
    }).call(this);
```

Kód můžete napsat přímo na stránku HTML ve `<script>` značkách nebo v externím souboru JavaScriptu. Pokud tak učiníte, nezapomeňte za skript AMP `<head>` zahrnout soubor JavaScript u *stránky* HTML.

Příklad:

```javascript
    <!--*****START OF Azure Media Player Scripts*****-->
    <script src="//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <!--*****END OF Azure Media Player Scripts*****-->
    <!--Add Plugins-->
    <script src="yourPluginName.js"></script>
```

## <a name="step-2"></a>Krok 2 ##
Inicializovat plugin s JavaScriptem jedním ze dvou způsobů:

Metoda 1:

```javascript
    var myOptions = {
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
            yourPluginName: {
                [your plugin options]: [example options]
           }
        }
    };     
    var myPlayer = amp([videotag id], myOptions);
```

Metoda 2:

```javascript
    var video = amp([videotag id]);
    video.yourPluginName({[your plugins option]: [example option]});
```

Plugin možnosti nejsou vyžadovány, včetně nich jen umožňuje vývojářům pomocí pluginu nakonfigurovat své chování, aniž by museli měnit zdrojový kód.

Pro inspiraci a další příklady na vytvoření pluginu se podívejte na naši [galerii](azure-media-player-plugin-gallery.md)

>[!NOTE]
> Plugin kód dynamicky mění položky v DOM během životnosti diváka hráče zkušenosti, nikdy neprovede trvalé změny ve zdrojovém kódu hráče. To je místo, kde pochopení vašeho prohlížeče vývojářské nástroje přijde vhod. Chcete-li například změnit vzhled prvku v přehrávači, můžete najít jeho element HTML podle názvu třídy a pak odtud přidat nebo změnit atributy. Zde je skvělý zdroj pro [změnu atributů HTML.](http://www.w3schools.com/js/js_htmldom_html.asp)

### <a name="integrated-plugins"></a>Integrované pluginy ###

 V současné době existují dva pluginy pečené do AMP: [time-tip](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) a [klávesové zkratky](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html). Tyto pluginy byly původně vyvinuty tak, aby byly modulární pluginy pro hráče, ale jsou nyní zahrnuty do zdrojového kódu přehrávače.

### <a name="plugin-gallery"></a>Galerie pluginů ###

[Plugin galerie](http//:aka.ms/ampplugins) má několik pluginů, které komunita již přispěla pro funkce, jako jsou časové osy značky, zoom, analytika a další. Stránka poskytuje přístupy k pluginům a pokyny, jak ji nastavit, stejně jako demo, které zobrazuje plugin v akci. Pokud vytvoříte skvělý plugin, který si myslíte, že by měly být zahrnuty v naší galerii, neváhejte a předložit, takže můžeme podívat.

## <a name="next-steps"></a>Další kroky ##

- [Rychlý start přehrávače médií Azure](azure-media-player-quickstart.md)