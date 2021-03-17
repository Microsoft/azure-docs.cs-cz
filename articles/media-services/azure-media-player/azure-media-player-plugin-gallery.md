---
title: Galerie modulů plug-in Azure Media Player
description: Tento článek obsahuje seznam modulů plug-in, které jsou dostupné pro Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 71fa79cb8847d16ac0890f9aba647cb8f5e2e444
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089338"
---
# <a name="azure-media-player-plugin-gallery"></a>Galerie modulů plug-in Azure Media Player #

## <a name="plugins"></a>Moduly plug-in ##

| Název modulu plug-in                         | Ukázková adresa URL                    | Zdrojový kód                | Description    |
|-------------------------------------|-----------------------------|----------------------------|----------------|
| Další funkce                 | | | |
| **New!** AMP360Video                | [Demo](http://www.babylonjs.com/demos/amp360video/)                        | [GitHub](https://github.com/BabylonJS/Extensions/tree/master/Amp360Video)                     | Modul plug-in umožňuje vizualizovat 360 video v amp buď na vašem počítači, nebo na zařízeních kompatibilních s VR. Úplná dokumentace je k dispozici [zde](https://doc.babylonjs.com/extensions/amp360video): |
|  Popis Sprite                         | [Demo](https://www.smwcentral.net/?p=section&a=details&id=10301)                        | [GitHub](https://github.com/RickShahid/SpriteTip)                    | Azure Media Player (AMP) modul plug-in pro vykreslování zobrazení časové osy miniatury obrazu, který je vygenerovaný z Azure Media Services (AMS) Media Encoder Standard (status) |
| Překryvná Diagnostika                 | [Demo](https://openidconnectweb.azurewebsites.net/Diagnoverlay.html)                        | [GitHub](https://github.com/willzhan/diagnoverlay)                     | Tento modul plug-in zobrazí všechny klíčové parametry, statistiky videa, všechny události v životním cyklu přehrávání videa a informace o ochraně DRM, jako je ID klíče, adresy URL pro získání licence, pokud jsou chráněné.                                                                                                                                                                      |
| Snímková frekvence a timecode kalkulačka | Není dostupná žádná ukázka. | [GitHub](https://github.com/mconverti/media-services-javascript-azure-media-player-framerate-timecode-calculator-plugin)                     | Tento modul plug-in vypočítá snímkový kmitočet videa na základě `tfhd` / `trun` polí MP4 prvního fragmentu videa MPEG-pomlčky, analyzuje hodnotu časového měřítka z manifestu klienta MPEG-spojovník a poskytuje způsob, jak vygenerovat timecode pro daný absolutní čas v přehrávači (a také poskytuje pro daný timecode absolutní čas). |
| <strike>Rychlost přehrávání</strike>                      | [Demo](https://azure-samples.github.io/media-services-javascript-Azure-Media-Player-playback-rate-plugin/)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Tento modul plug-in umožňuje čtenářům řídit rychlost videa. *Tato funkce je k dispozici automaticky ve verzi AMP v 2.0.0 +, ale ve výchozím nastavení zakázána.* Pokud se chcete dozvědět, jak ho povolit, podívejte se na [naše ukázky.](https://github.com/Azure-Samples/azure-media-player-samples) |
| Časový Tip při najetí myší                      | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Zobrazuje časový hrot nad indikátorem průběhu při najetí myší na přesný čas hledání. *Poznámka: Tento modul plug-in je už integrovaný do amp* , ale pokud vás zajímá, jak se v programu klidně nebudete moct podívat.                                                                                                                 |
| Překryv názvů                       | [Demo](https://azure-samples.github.io/media-services-javascript-azure-media-player-title-overlay-plugin)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-title-overlay-plugin)                     | Překrývá konfigurovatelný název videa po obrazovce. |
| Značky časové osy                    | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timelinemarkers/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-timeline-markers-plugin)                     | Tento modul plug-in přebírá v poli čas a překrývá v něm nemalé značky v průběhu indikátoru průběhu. |
| Analýzy                           | | | |
| Application Insights                | [Příspěvek na blogu](https://azure.microsoft.com/blog/player-analytics-azure-media-player-plugin/)                   | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-application-insights-plugin)                     | Modul plug-in, který sleduje metriky a porty přehrávače, Power BI pro intuitivní grafické znázornění prostředí přehrávače vašich prohlížečů. |
| Google Analytics                    | –                         | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-google-analytics-plugin)                     | Modul plug-in Google Analytics pro Azure Media Player |
| Diagnostika                         | | | |
| Výstup diagnostiky                  | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/diagnosticslogger/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-diagnostic-logger-plugin)                     | Tento modul plug-in z přehrávače vypíše pole diagnostiky, aby se zobrazil v akci, přejděte na ukázkový odkaz a otevřete konzolu jazyka JavaScript. |
| Usnadnění přístupu                      | | | |
| Přiblížit                             | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/zoom/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-zoom-plugin)                     | Tento modul plug-in na obrazovce hráčů zobrazuje škálu přiblížení umožňující přetahování, takže se můžou čtenáři přiblížit k obsahu. |
| Živé titulky                       | Příspěvek na [blogu Azure](https://azure.microsoft.com/blog/live-real-time-captions-with-azure-media-services-and-player/),[SubPly](http://www.subply.com/en/Products/AzureLiveCaptions.htm) | – | *Další informace najdete v příspěvku.* Konečný pracovní postup, který je určený pro živý modul plug-in pro Azure Media Player, klikněte na odkaz vlevo a přejděte k webu SubPly a zjistěte další informace o řešení. |
| Klávesové zkratky                            | <strike>[Ukázka](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)</strike>                        | <strike>[GitHubu](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-hot-keys-plugin)</strike>                     | Modul plug-in klávesové zkratky umožňuje čtenářům řídit různé aspekty přehrávače s obecnými kombinacemi modulů plug-in, jako je F pro celou obrazovku, pro funkci ztlumení a šipky pro řízení indikátoru průběhu. *Poznámka: Tento modul plug-in už je integrovaný do AMP, ale můžete ho použít jako prostředek.* |
| Sociální sítě                              | | | |
| Sdílení                               | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/share/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-social-share-plugin)                     | Tento modul plug-in přidá na řídicí panel přehrávače tlačítko sdílet, aby mohli vaši uživatelé sdílet video, které sleduje s přáteli přes Facebook, Twitter nebo LinkedIn. |

## <a name="next-steps"></a>Další kroky ##

- [Rychlý Start Azure Media Player](azure-media-player-quickstart.md)
