---
title: Azure Media Player seznam funkcí
description: Odkaz na funkci pro Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 88048c3328114f17b30859efb41bb9f059b71439
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91296361"
---
# <a name="feature-list"></a>Seznam funkcí #
Tady je seznam testovaných funkcí a nepodporované funkce:

| Příznak | TESTOVAT | ČÁSTEČNĚ TESTOVÁNO | NETESTOVANÝMI | NEPLATNÉ | POZNÁMKY |
| ------- | ------ | ---------------- | -------- | ----------- | ----- |
| **Přehrávání**                                |        |                  |          |             |                                                                                                                      |
| Základní přehrávání na vyžádání                | X      |                  |          |             | Podporuje pouze datové proudy z Azure Media Services                                                                      |
| Základní přehrávání v reálném čase                     | X      |                  |          |             | Podporuje pouze datové proudy z Azure Media Services                                                                      |
| AES                                     | X      |                  |          |             | Podporuje službu doručování klíčů Azure Media Services                                                                   |
| Více DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Podporuje službu doručování klíčů Azure Media Services                                                                   |
| Widevine                                |        | X                |          |             | Podporuje Widevine PSSH pole, která jsou popsaný v manifestu.                                                                    |
| FairPlay                                |        | X                |          |             | Podporuje službu doručování klíčů Azure Media Services                                                                   |
| **Technické pracovníky**                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| Záložní verze Flash (Flash)                | X      |                  |          |             | Ne všechny funkce jsou k dispozici na tomto Techu.                                                                         |
| Silverlight – záložní Silverlight      | X      |                  |          |             | Ne všechny funkce jsou k dispozici na tomto Techu.                                                                         |
| HLS Passing Native (Html5)         |        | X                |          |             | Ne všechny funkce jsou na tomto Techu dostupné kvůli omezením platforem.                                            |
| **Funkce**                                |        |                  |          |             |                                                                                                                      |
| Podpora rozhraní API                             | X      |                  |          |             | Zobrazit seznam známých problémů                                                                                                |
| Základní uživatelské rozhraní                                | X      |                  |          |                                                                                                                                    |
| Inicializace prostřednictvím JavaScriptu       | X      |                  |          |             |                                                                                                                      |
| Inicializace prostřednictvím značky videa        |        | X                |          |             |                                                                                                                      |
| Segment adresování – na základě času         | X      |                  |          |             |                                                                                                                      |
| Adresování segmentů – na základě indexu        |        |                  |          | X           |                                                                                                                      |
| Adresování segmentů – na základě bajtů         |        |                  |          | X           |                                                                                                                      |
| Azure Media Services přepisu adresy URL       |        | X                |          |             |                                                                                                                      |
| Přístupnost – titulky a titulky  | X      |                 |          |             |  WebVTT (na vyžádání), CEA 708 (na vyžádání a živé) a IMSC1 (na vyžádání a živé)                                                       |
| Přístupnost – klávesové zkratky                 | X      |                  |          |             |                                                                                                                      |
| Usnadnění – Vysoký kontrast           |        | X                |          |             |                                                                                                                      |
| Usnadnění – fokus karty               |        | X                |          |             |                                                                                                                      |
| Zasílání zpráv o chybách                         |        | X                |          |             | Chybové zprávy nejsou v souladu s technickými pracovníky                                                                         |
| Aktivace událostí                        | X      |                  |          |             |                                                                                                                      |
| Diagnostika                             |        | X                |          |             | Diagnostické informace jsou k dispozici pouze na AzureHtml5JS Tech a částečně k dispozici na technickém programu Silverlight. |
| Přizpůsobitelný technický způsob                 |        | X                |          |             |                                                                                                                      |
| Heuristické – základní                      | X      |                  |          |             |                                                                                                                      |
| Heuristiky – přizpůsobení              |        |                  | X        |             | Vlastní nastavení je dostupné jenom u AzureHtml5JS tech.                                                          |
| Nekontinuita                         | X      |                  |          |             |                                                                                                                      |
| Výběr přenosové rychlosti                          | X      |                  |          |             | Toto rozhraní API je dostupné jenom na technikách AzureHtml5JS a Flash.                                                    |
| Datový proud s více zvuky                      |        | X                |          |             | Programový zvukový přepínač je podporován v AzureHtml5JS a technikách Flash a je dostupný prostřednictvím výběru uživatelského rozhraní v AzureHtml5JS, Flash a nativní Html5 (v Safari).  Většina platforem vyžaduje, aby se stejná privátní data kodeku přepnula na zvukové proudy (stejný kodek, kanál, vzorkovací frekvence atd.). |
| Lokalizace uživatelského rozhraní                         |        | X                |          |             |                                                                                                                      |
| Přehrávání více instancí                 |        |                  |          | X           | Tento scénář může fungovat i u některých techniků, ale v současné době není podporován a netestoval. Můžete to také využít k práci s použitím iFrames. |
| Podpora reklam                             |        | X                |          |             | AMP podporuje vkládání předběžných a následných lineárních reklam z nevyhovujících serverů AD pro VOD v AzureHtml5JS tech |
| Analýzy                               |        | X                |          |             | AMP nabízí možnost naslouchat analýzám a diagnostickým událostem, aby se daly odeslat do back-endu Analytics dle vašeho výběru.  Všechny události a vlastnosti nejsou v rámci technických důvodů k dispozici v důsledku omezení platformy.                                                                            |
| Vlastní vzhledy                            |        |                  | X        |             | Tento scénář lze dosáhnout vypnutím nastavení ovládacích prvků na hodnotu false v AMP a použitím vlastních HTML a šablon stylů CSS.           |
| Hledání v pročištění řádků                      |        |                  |          | X           |                                                                                                                      |
| Zdvih – přehrání                              |        |                  |          | X           |                                                                                                                      |
| Jenom zvuk                              | X      |                  |          |           | Podporováno v AzureHtml5JS. Progresivní přehrávání ve formátu MP3 může spolupracovat s technikou HTML5, pokud ji platforma podporuje.                                                                                                        |
| Jenom video                              | X      |                  |          |           | Podporováno v AzureHtml5JS.                                                                                                        |
| Prezentace pro více období               |        |                  |          | X                                                                                                                                  |
| Více úhlů kamery                  |        |                  |          | X           |                                                                                                                      |
| Rychlost přehrávání                          |        | X                |          |             | Rychlost přehrávání je podporovaná ve většině scénářů s výjimkou toho, že se jedná o částečnou chybu v Chrome.                 |

## <a name="next-steps"></a>Další kroky ##
- [Rychlý Start Azure Media Player](azure-media-player-quickstart.md)