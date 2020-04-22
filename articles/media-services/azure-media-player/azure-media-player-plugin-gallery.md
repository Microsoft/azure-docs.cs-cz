---
title: Galerie pluginů přehrávače médií Azure
description: Tento článek obsahuje seznam pluginů dostupných pro Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 206200f3ba0757cd738439b58c8b94874cf5a938
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726477"
---
# <a name="azure-media-player-plugin-gallery"></a>Galerie pluginů přehrávače médií Azure #

## <a name="plugins"></a>Moduly plug-in ##

| Název pluginu                         | Ukázková adresa URL                    | Zdrojový kód                | Popis    |
|-------------------------------------|-----------------------------|----------------------------|----------------|
| Další funkce                 | | | |
| **Nwe!** AMP360Video                | [Demo](http://www.babylonjs.com/demos/amp360video/)                        | [GitHubu](https://github.com/BabylonJS/Extensions/tree/master/Amp360Video)                     | Plugin umožňuje vizualizovat video 360 v ampu buď na ploše, nebo v zařízeních kompatibilních s VR. Kompletní dokumentace je k dispozici [zde](https://doc\.babylonjs\.com/extensions/amp360video): |
|  Sprite Tip                         | [Demo](http://www.skymedia.tv/asset/sprite)                        | [GitHubu](https://github.com/RickShahid/SpriteTip)                    | Modul plug-in Programu Azure Media Player (AMP) pro vykreslování náhledů miniatur videa, který je generován ze standardu kodéru médií (MES) služby Azure Media Services (AMS). |
| Překrytí diagnostiky                 | [Demo](https://openidconnectweb.azurewebsites.net/Diagnoverlay.html)                        | [GitHubu](https://github.com/willzhan/diagnoverlay)                     | Tento plugin zobrazuje: Všechny klíčové parametry, statistiky videa, všechny události v životním cyklu přehrávání videa a informace o ochraně DRM, jako je ID klíče, adresy URL získávání licencí, pokud jsou chráněny.                                                                                                                                                                      |
| Kmitočet snímků a kalkulačka časového kódu | [Demo](http://mconvertitest001.blob.core.windows.net/public/example.html)                        | [GitHubu](https://github.com/mconverti/media-services-javascript-azure-media-player-framerate-timecode-calculator-plugin)                     | Tento plugin vypočítá kmitočet snímků `tfhd` / `trun` videa na základě MP4 boxů prvního fragmentu videa MPEG-DASH, analyzuje hodnotu časového měřítka z klientského manifestu MPEG-DASH a také poskytuje způsob, jak generovat časový kód pro daný absolutní čas z přehrávače (stejně jako poskytuje hráči absolutní čas daný časový kód) |
| <strike>Rychlost přehrávání</strike>                      | [Demo](https://azure-samples.github.io/media-services-javascript-Azure-Media-Player-playback-rate-plugin/)                        | [GitHubu](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Tento plugin umožňuje divákům kontrolovat, jakou rychlost videa. *Všimněte si, že tato funkce je automaticky k dispozici ve verzi AMP v2.0.0+, ale ve výchozím nastavení je zakázána.* Chcete-li se dozvědět, jak to povolit, podívejte se na naše ukázky [zde](https://github.com/Azure-Samples/azure-media-player-samples) |
| Tip na čas přilehne                      | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)                        | [GitHubu](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Zobrazí časový tip nad indikátorem průběhu myši, který je namyši, pro přesné vyhledávání času. *Poznámka: Tento plugin je již integrován do AMP,* ale pokud máte zájem vidět, jak je to naprogramované, neváhejte se podívat.                                                                                                                       |
| Překrytí nadpisů                       | [Demo](https://azure-samples.github.io/media-services-javascript-azure-media-player-title-overlay-plugin/")                        | [GitHubu](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-title-overlay-plugin)                     | Překryvy konfigurovatelný název videa přes obrazovku |
| Značky časové osy                    | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timelinemarkers/example.html)                        | [GitHubu](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-timeline-markers-plugin)                     | Tento plugin se v řadě časů a překryvy drobné značky přes indikátor průběhu v té době. |
| Analýzy                           | | | |
| Application Insights                | [Blogu](https://azure.microsoft.com/blog/player-analytics-azure-media-player-plugin/)                   | [GitHubu](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-application-insights-plugin)                     | Plugin, který sleduje metriky přehrávače a portuje je do Power BI pro intuitivní grafické znázornění hráčských zážitků vašich diváků. |
| Google Analytics                    | –                         | [GitHubu](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-google-analytics-plugin)                     | Plugin Google Analytics pro Azure Media Player |
| Diagnostika                         | | | |
| Výstup diagnostiky                  | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/diagnosticslogger/example.html)                        | [GitHubu](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-diagnostic-logger-plugin)                     | Tento plugin výstupy řadu diagnostiky z přehrávače, vidět to v akci jít na demo odkaz a otevřete si konzoli JavaScript. |
| Usnadnění přístupu                      | | | |
| Přiblížit                             | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/zoom/example.html)                        | [GitHubu](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-zoom-plugin)                     | Tento plugin zobrazuje drag-schopný zoom-in měřítku na obrazovce přehrávače, takže diváci mohou přiblížit na váš obsah |
| Živé titulky                       | [Příspěvek blogu Azure](https://azure.microsoft.com/blog/live-real-time-captions-with-azure-media-services-and-player/)[, příspěvek subply](http://www.subply.com/en/Products/AzureLiveCaptions.htm) | – | *Další informace najdete v tématu Příspěvek.* Pracovní postup End to End navržený pro živý plugin vytvořený titulky pro Azure Media Player, klikněte na lin, který je nejvíce vlevo, a přejděte na web SubPly a získejte další informace o řešení. |
| Klávesové zkratky                            | <strike>[Ukázka](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)</strike>                        | <strike>[GitHubu](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-hot-keys-plugin)</strike>                     | Klávesové zkratky plugin umožňuje divákům ovládat různé aspekty přehrávače s obecnými kombinacemi pluginů, jako je F pro celou obrazovku, M pro ztlumení a šipky pro ovládání indikátoru průběhu. *Poznámka: Tento plugin byl již integrován do AMP, ale neváhejte jej použít jako zdroj* |
| Sociální                              | | | |
| Sdílet                               | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/share/example.html)                        | [GitHubu](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-social-share-plugin)                     | Tento plugin přidá tlačítko sdílet na ovládací panel hráče, aby vaši diváci mohli sdílet video, které sledují, se svými přáteli prostřednictvím Facebooku, Twitteru nebo Linkedinu. |

## <a name="next-steps"></a>Další kroky ##

- [Rychlý start přehrávače médií Azure](azure-media-player-quickstart.md)