---
title: Psaní modulů plug-in pro Azure Media Player
description: Naučte se psát modul plug-in s Azure Media Player pomocí JavaScriptu
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: c1a64bff8b81735d9c4c9a14d2c1e12bd0bfe57e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91296157"
---
# <a name="writing-plugins-for-azure-media-player"></a>Psaní modulů plug-in pro Azure Media Player #

Modul plug-in je zapsaný do JavaScriptu pro rozšíření nebo vylepšení přehrávače. Můžete psát moduly plug-in, které mění vzhled Azure Media Player, jeho funkce nebo dokonce mají rozhraní pro jiné služby. Můžete to udělat ve dvou snadných krocích:

## <a name="step-1"></a>Krok 1 ##

Napište svůj JavaScript do funkce, například takto:

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

Svůj kód můžete napsat přímo na stránce HTML v rámci `<script>` značek nebo v externím souboru JavaScriptu. Pokud to uděláte, nezapomeňte zahrnout soubor JavaScriptu do `<head>` stránky HTML *za* skriptem amp.

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
Inicializujte modul plug-in pomocí JavaScriptu jedním ze dvou způsobů:

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

Možnosti modulu plug-in nejsou vyžadovány, včetně těch, které umožňují vývojářům použít modul plug-in ke konfiguraci chování bez nutnosti změny zdrojového kódu.

Inspiraci a další příklady vytváření modulů plug-in najdete v naší [galerii](azure-media-player-plugin-gallery.md) .

>[!NOTE]
> Kód modulu plug-in dynamicky mění položky v modelu DOM během životnosti přehrávače prohlížeče, nikdy neprovádí trvalé změny ve zdrojovém kódu přehrávače. Tady jsou informace o vývojářských nástrojích prohlížeče, které jsou užitečné. Například pokud chcete změnit vzhled prvku v přehrávači, můžete najít jeho element HTML jeho názvem třídy a potom přidat nebo změnit atributy. Tady je skvělý prostředek pro [změnu atributů HTML.](http://www.w3schools.com/js/js_htmldom_html.asp)

### <a name="integrated-plugins"></a>Integrované moduly plug-in ###

 V současné době jsou dva moduly plug-in vloženými do AMP: [časový popis](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) a [klávesové zkratky](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html). Tyto moduly plug-in se původně vyvinuly jako modulární moduly plug-in pro přehrávač, ale teď jsou součástí zdrojového kódu přehrávače.

### <a name="plugin-gallery"></a>Galerie modulů plug-in ###

[Galerie modulů plug-in](https://aka.ms/ampplugins) má několik modulů plug-in, které už komunita přispěla k funkcím, jako jsou značky časových řádků, přiblížení, analýzy a další. Stránka poskytuje přístup k modulům plug-in a pokyny, jak je nastavit, a ukázku, která zobrazuje modul plug-in v akci. Pokud vytvoříte modul plug-in, který se domníváte, že byste si myslíte, že byste se měli v naší galerii začlenit, můžete ho kdykoli odeslat.

## <a name="next-steps"></a>Další kroky ##

- [Rychlý Start Azure Media Player](azure-media-player-quickstart.md)
