---
title: Azure Media Player seznam funkcí
description: Odkaz na funkci pro Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e5595620a2f888b06ad5b35d2e8a008f23861463
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81727226"
---
# <a name="feature-list"></a>Seznam funkcí #
Tady je seznam testovaných funkcí a nepodporované funkce:

|                                         | TESTOVAT | ČÁSTEČNĚ TESTOVÁNO | NETESTOVANÝMI | NEPLATNÉ | POZNÁMKY                                                                                                                |
|:----------------------------------------|--------|------------------|----------|-------------|:---------------------------------------------------------------------------------------------------------------------|
| Přehrávání                                |        |                  |          |             |                                                                                                                      |
| Základní přehrávání na vyžádání                | ×      |                  |          |             | Podporuje pouze datové proudy z Azure Media Services                                                                      |
| Základní přehrávání v reálném čase                     | ×      |                  |          |             | Podporuje pouze datové proudy z Azure Media Services                                                                      |
| AES                                     | ×      |                  |          |             | Podporuje službu doručování klíčů Azure Media Services                                                                   |
| Více DRM                               |        | ×                |          |             |                                                                                                                      |
| PlayReady                               | ×      |                  |          |             | Podporuje službu doručování klíčů Azure Media Services                                                                   |
| Widevine                                |        | ×                |          |             | Podporuje Widevine PSSH pole, která jsou popsaný v manifestu.                                                                    |
| FairPlay                                |        | ×                |          |             | Podporuje službu doručování klíčů Azure Media Services                                                                   |
| Technické pracovníky                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | ×      |                  |          |             |                                                                                                                      |
| Záložní verze Flash (Flash)                | ×      |                  |          |             | Ne všechny funkce jsou k dispozici na tomto Techu.                                                                         |
| Silverlight – záložní Silverlight      | ×      |                  |          |             | Ne všechny funkce jsou k dispozici na tomto Techu.                                                                         |
| HLS Passing Native (Html5)         |        | ×                |          |             | Ne všechny funkce jsou na tomto Techu dostupné kvůli omezením platforem.                                            |
| Funkce                                |        |                  |          |             |                                                                                                                      |
| Podpora rozhraní API                             | ×      |                  |          |             | Zobrazit seznam známých problémů                                                                                                |
| Základní uživatelské rozhraní                                | ×      |                  |          |                                                                                                                                    |
| Inicializace prostřednictvím JavaScriptu       | ×      |                  |          |             |                                                                                                                      |
| Inicializace prostřednictvím značky videa        |        | ×                |          |             |                                                                                                                      |
| Segment adresování – na základě času         | ×      |                  |          |             |                                                                                                                      |
| Adresování segmentů – na základě indexu        |        |                  |          | ×           |                                                                                                                      |
| Adresování segmentů – na základě bajtů         |        |                  |          | ×           |                                                                                                                      |
| Azure Media Services přepisu adresy URL       |        | ×                |          |             |                                                                                                                      |
| Přístupnost – titulky a titulky  |        | ×                |          |             |  Na na vyžádání je podporována WebVTT, Live CEA 708 – částečně testováno                                                       |
| Přístupnost – klávesové zkratky                 | ×      |                  |          |             |                                                                                                                      |
| Usnadnění – Vysoký kontrast           |        | ×                |          |             |                                                                                                                      |
| Usnadnění – fokus karty               |        | ×                |          |             |                                                                                                                      |
| Zasílání zpráv o chybách                         |        | ×                |          |             | Chybové zprávy nejsou v souladu s technickými pracovníky                                                                         |
| Aktivace událostí                        | ×      |                  |          |             |                                                                                                                      |
| Diagnostika                             |        | ×                |          |             | Diagnostické informace jsou k dispozici pouze na AzureHtml5JS Tech a částečně k dispozici na technickém programu Silverlight. |
| Přizpůsobitelný technický způsob                 |        | ×                |          |             |                                                                                                                      |
| Heuristické – základní                      | ×      |                  |          |             |                                                                                                                      |
| Heuristiky – přizpůsobení              |        |                  | ×        |             | Vlastní nastavení je dostupné jenom u AzureHtml5JS tech.                                                          |
| Nekontinuita                         | ×      |                  |          |             |                                                                                                                      |
| Výběr přenosové rychlosti                          | ×      |                  |          |             | Toto rozhraní API je dostupné jenom na technikách AzureHtml5JS a Flash.                                                    |
| Datový proud s více zvuky                      |        | ×                |          |             | Programový zvukový přepínač je podporován v AzureHtml5JS a technikách Flash a je dostupný prostřednictvím výběru uživatelského rozhraní v AzureHtml5JS, Flash a nativní Html5 (v Safari).  Většina platforem vyžaduje, aby se stejná privátní data kodeku přepnula na zvukové proudy (stejný kodek, kanál, vzorkovací frekvence atd.). |
| Lokalizace uživatelského rozhraní                         |        | ×                |          |             |                                                                                                                      |
| Přehrávání více instancí                 |        |                  |          | ×           | Tento scénář může fungovat i u některých techniků, ale v současné době není podporován a netestoval. Můžete to také využít k práci s použitím iFrames. |
| Podpora reklam                             |        | x                |          |             | AMP podporuje vkládání předběžných a následných lineárních reklam z nevyhovujících serverů AD pro VOD v AzureHtml5JS tech |
| Analýzy                               |        | ×                |          |             | AMP nabízí možnost naslouchat analýzám a diagnostickým událostem, aby se daly odeslat do back-endu Analytics dle vašeho výběru.  Všechny události a vlastnosti nejsou v rámci technických důvodů k dispozici v důsledku omezení platformy.                                                                            |
| Vlastní vzhledy                            |        |                  | ×        |             | V AMP nastavte ovládací prvky na false a použijte vlastní HTML a CSS.           |
| Hledání v pročištění řádků                      |        |                  |          | ×           |                                                                                                                      |
| Zdvih – přehrání                              |        |                  |          | ×           |                                                                                                                      |
| Jenom zvuk                              |        |                  |          | ×           | Může v některých technikách pracovat s adaptivním streamování, ale v současné době není podporovaná a nefunguje v AzureHtml5JS. Progresivní přehrávání ve formátu MP3 může spolupracovat s technikou HTML5, pokud ji platforma podporuje.                                                                                                        |
| Jenom video                              |        |                  |          | ×           | Může v některých technikách pracovat s adaptivním streamování, ale v současné době není podporovaná a nefunguje v AzureHtml5JS.      |
| Prezentace pro více období               |        |                  |          | ×                                                                                                                                  |
| Více úhlů kamery                  |        |                  |          | ×           |                                                                                                                      |
| Rychlost přehrávání                          |        | ×                |          |             | Rychlost přehrávání je podporovaná ve většině scénářů s výjimkou toho, že se jedná o částečnou chybu v Chrome.                 |

## <a name="next-steps"></a>Další kroky ##
- [Rychlý Start Azure Media Player](azure-media-player-quickstart.md)