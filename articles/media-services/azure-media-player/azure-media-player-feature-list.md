---
title: Azure Media Player seznam funkcí
description: Odkaz na funkci pro Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: 4b0666b439c284fd402b3f6e04bbaed14d6aa358
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448759"
---
# <a name="feature-list"></a>Seznam funkcí #
Tady je seznam testovaných funkcí a nepodporované funkce:

| Funkce | TESTOVAT | ČÁSTEČNĚ TESTOVÁNO | NETESTOVANÝMI | NEPLATNÉ | POZNÁMKY |
| ------- | ------ | ---------------- | -------- | ----------- | ----- |
| **Přehrávání**                                |        |                  |          |             |                                                                                                                      |
| Základní přehrávání na vyžádání                | ×      |                  |          |             | Podporuje pouze datové proudy z Azure Media Services                                                                      |
| Základní přehrávání v reálném čase                     | ×      |                  |          |             | Podporuje pouze datové proudy z Azure Media Services                                                                      |
| AES                                     | ×      |                  |          |             | Podporuje službu doručování klíčů Azure Media Services                                                                   |
| Více DRM                               |        | ×                |          |             |                                                                                                                      |
| PlayReady                               | ×      |                  |          |             | Podporuje službu doručování klíčů Azure Media Services                                                                   |
| Widevine                                |        | ×                |          |             | Podporuje Widevine PSSH pole, která jsou popsaný v manifestu.                                                                    |
| FairPlay                                |        | ×                |          |             | Podporuje službu doručování klíčů Azure Media Services                                                                   |
| **Technické pracovníky**                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | ×      |                  |          |             |                                                                                                                      |
| Záložní verze Flash (Flash)                | ×      |                  |          |             | Ne všechny funkce jsou k dispozici na tomto Techu.                                                                         |
| Silverlight – záložní Silverlight      | ×      |                  |          |             | Ne všechny funkce jsou k dispozici na tomto Techu.                                                                         |
| HLS Passing Native (Html5)         |        | ×                |          |             | Ne všechny funkce jsou na tomto Techu dostupné kvůli omezením platforem.                                            |
| **Funkce**                                |        |                  |          |             |                                                                                                                      |
| Podpora rozhraní API                             | ×      |                  |          |             | Zobrazit seznam známých problémů                                                                                                |
| Základní uživatelské rozhraní                                | ×      |                  |          |                                                                                                                                    |
| Inicializace prostřednictvím JavaScriptu       | ×      |                  |          |             |                                                                                                                      |
| Inicializace prostřednictvím značky videa        |        | ×                |          |             |                                                                                                                      |
| Segment adresování – na základě času         | ×      |                  |          |             |                                                                                                                      |
| Adresování segmentů – na základě indexu        |        |                  |          | ×           |                                                                                                                      |
| Adresování segmentů – na základě bajtů         |        |                  |          | ×           |                                                                                                                      |
| Azure Media Services přepisu adresy URL       |        | ×                |          |             |                                                                                                                      |
| Přístupnost – titulky a titulky  | ×      |                 |          |             |  WebVTT (na vyžádání), CEA 708 (na vyžádání a živé) a IMSC1 (na vyžádání a živé)                                                       |
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
| Podpora reklam                             |        | ×                |          |             | AMP podporuje vkládání předběžných a následných lineárních reklam z nevyhovujících serverů AD pro VOD v AzureHtml5JS tech |
| Analýzy                               |        | ×                |          |             | AMP nabízí možnost naslouchat analýzám a diagnostickým událostem, aby se daly odeslat do back-endu Analytics dle vašeho výběru.  Všechny události a vlastnosti nejsou v rámci technických důvodů k dispozici v důsledku omezení platformy.                                                                            |
| Vlastní vzhledy                            |        |                  | ×        |             | Tento scénář lze dosáhnout vypnutím nastavení ovládacích prvků na hodnotu false v AMP a použitím vlastních HTML a šablon stylů CSS.           |
| Hledání v pročištění řádků                      |        |                  |          | ×           |                                                                                                                      |
| Trick-Play                              |        |                  |          | ×           |                                                                                                                      |
| Jenom zvuk                              | ×      |                  |          |           | Podporováno v AzureHtml5JS. Progresivní přehrávání ve formátu MP3 může spolupracovat s technikou HTML5, pokud ji platforma podporuje.                                                                                                        |
| Jenom video                              | ×      |                  |          |           | Podporováno v AzureHtml5JS.                                                                                                        |
| Prezentace pro více období               |        |                  |          | ×                                                                                                                                  |
| Více úhlů kamery                  |        |                  |          | ×           |                                                                                                                      |
| Rychlost přehrávání                          |        | ×                |          |             | Rychlost přehrávání je podporovaná ve většině scénářů s výjimkou toho, že se jedná o částečnou chybu v Chrome.                 |

## <a name="next-steps"></a>Další kroky ##
- [Rychlý Start Azure Media Player](azure-media-player-quickstart.md)