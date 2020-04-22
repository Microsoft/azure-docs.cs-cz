---
title: Seznam funkcí přehrávače Azure Media Player
description: Odkaz na funkci pro Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e5595620a2f888b06ad5b35d2e8a008f23861463
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727226"
---
# <a name="feature-list"></a>Seznam funkcí #
Zde je seznam testovaných funkcí a nepodporovaných funkcí:

|                                         | Testovány | ČÁSTEČNĚ TESTOVÁNO | Nevyzkoušené | Nepodporovaný | POZNÁMKY                                                                                                                |
|:----------------------------------------|--------|------------------|----------|-------------|:---------------------------------------------------------------------------------------------------------------------|
| Přehrávání                                |        |                  |          |             |                                                                                                                      |
| Základní přehrávání na vyžádání                | ×      |                  |          |             | Podporuje jenom datové proudy z Azure Media Services                                                                      |
| Základní živé přehrávání                     | ×      |                  |          |             | Podporuje jenom datové proudy z Azure Media Services                                                                      |
| AES                                     | ×      |                  |          |             | Podporuje službu doručování klíčů mediálních služeb Azure                                                                   |
| MultiDRM                               |        | ×                |          |             |                                                                                                                      |
| PlayReady                               | ×      |                  |          |             | Podporuje službu doručování klíčů mediálních služeb Azure                                                                   |
| Widevine                                |        | ×                |          |             | Podporuje Widevine PSSH boxy uvedené v manifestu                                                                    |
| FairPlay                                |        | ×                |          |             | Podporuje službu doručování klíčů mediálních služeb Azure                                                                   |
| Technici                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | ×      |                  |          |             |                                                                                                                      |
| Flash Záložní (FlashSS)                | ×      |                  |          |             | Ne všechny funkce jsou k dispozici na této technologii.                                                                         |
| Silverlight záložní SilverlightSS      | ×      |                  |          |             | Ne všechny funkce jsou k dispozici na této technologii.                                                                         |
| Nativní HLS Pass-through (Html5)         |        | ×                |          |             | Ne všechny funkce jsou k dispozici na této technologii z důvodu omezení platformy.                                            |
| Funkce                                |        |                  |          |             |                                                                                                                      |
| Podpora rozhraní API                             | ×      |                  |          |             | Zobrazit seznam známých problémů                                                                                                |
| Základní ui                                | ×      |                  |          |                                                                                                                                    |
| Inicializace pomocí JavaScriptu       | ×      |                  |          |             |                                                                                                                      |
| Inicializace pomocí značky videa        |        | ×                |          |             |                                                                                                                      |
| Adresování segmentu – časově         | ×      |                  |          |             |                                                                                                                      |
| Segmentové adresování – založené na indexu        |        |                  |          | ×           |                                                                                                                      |
| Segmentové adresování – založené na bajtu         |        |                  |          | ×           |                                                                                                                      |
| Vyzápis adres URL služby Azure Media Services       |        | ×                |          |             |                                                                                                                      |
| Usnadnění přístupu - titulky a titulky  |        | ×                |          |             |  WebVTT podporován na vyžádání, live CEA 708 částečně testováno                                                       |
| Usnadnění přístupu - klávesové zkratky                 | ×      |                  |          |             |                                                                                                                      |
| Přístupnost – vysoký kontrast           |        | ×                |          |             |                                                                                                                      |
| Usnadnění přístupu – fokus tabulátoru               |        | ×                |          |             |                                                                                                                      |
| Zasílání zpráv při chybách                         |        | ×                |          |             | Chybové zprávy jsou mezi technikami nekonzistentní                                                                         |
| Aktivační událost                        | ×      |                  |          |             |                                                                                                                      |
| Diagnostika                             |        | ×                |          |             | Diagnostické informace jsou k dispozici jenom v technologii AzureHtml5JS a částečně dostupné v technologii SilverlightSS. |
| Přizpůsobitelná technická objednávka                 |        | ×                |          |             |                                                                                                                      |
| Heuristika - Základní                      | ×      |                  |          |             |                                                                                                                      |
| Heuristika - Přizpůsobení              |        |                  | ×        |             | Přizpůsobení je k dispozici pouze v technologii AzureHtml5JS.                                                          |
| Nespojitosti                         | ×      |                  |          |             |                                                                                                                      |
| Vybrat bitovou rychlost                          | ×      |                  |          |             | Toto rozhraní API je k dispozici jenom na technikech AzureHtml5JS a FlashSS.                                                    |
| Vícezvukový datový proud                      |        | ×                |          |             | Programový zvukový přepínač je podporovaný na technikách AzureHtml5JS a FlashSS a je k dispozici prostřednictvím výběru ui na AzureHtml5JS, FlashsS a nativním Html5 (v Safari).  Většina platforem vyžaduje pro přepínání zvukových proudů stejná soukromá data kodeku (stejný kodek, kanál, vzorkovací frekvence atd.). |
| Lokalizace ui                         |        | ×                |          |             |                                                                                                                      |
| Přehrávání ve více instancích                 |        |                  |          | ×           | Tento scénář může fungovat pro některé techniky, ale je v současné době nepodporované a netestované. Můžete také získat to do práce pomocí iframes |
| Podpora reklam                             |        | x                |          |             | AMP podporuje vkládání lineárních reklam před polovinou a po rolování z prodejních a režijních a režijních reklam pro VOD v technologii AzureHtml5JS |
| Analýzy                               |        | ×                |          |             | AMP poskytuje možnost naslouchat analýzám a diagnostickým událostem, aby bylo možné je odeslat do back-endu Analytics podle vašeho výběru.  Všechny události a vlastnosti nejsou k dispozici napříč technikami z důvodu omezení platformy.                                                                            |
| Vlastní vzhledy                            |        |                  | ×        |             | V AMP a pomocí vlastního HTML a CSS přepněte ovládací prvky nastavení na false.           |
| Hledat bar drhnutí                      |        |                  |          | ×           |                                                                                                                      |
| Trik-Play                              |        |                  |          | ×           |                                                                                                                      |
| Pouze zvuk                              |        |                  |          | ×           | Může fungovat v některých techniků pro adaptivní streamování, ale není aktuálně podporována a nefunguje v AzureHtml5JS. Progresivní přehrávání MP3 může pracovat s technologií HTML5, pokud ji platforma podporuje.                                                                                                        |
| Pouze video                              |        |                  |          | ×           | Může fungovat v některých techniků pro adaptivní streamování, ale není aktuálně podporována a nefunguje v AzureHtml5JS.      |
| Vícedobá prezentace               |        |                  |          | ×                                                                                                                                  |
| Více úhlů kamery                  |        |                  |          | ×           |                                                                                                                      |
| Rychlost přehrávání                          |        | ×                |          |             | Rychlost přehrávání je podporována ve většině scénářů s výjimkou mobilního případu kvůli částečné chybě v Chromu                 |

## <a name="next-steps"></a>Další kroky ##
- [Rychlý start přehrávače médií Azure](azure-media-player-quickstart.md)