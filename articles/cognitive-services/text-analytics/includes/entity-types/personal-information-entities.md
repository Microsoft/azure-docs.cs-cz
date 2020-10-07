---
title: Pojmenované entity pro osobní údaje
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: eedfe4f24797a9b564479b2f8d3f4d04b0751272
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779439"
---
> [!NOTE]
> K detekci chráněných informací o stavu (FÍ) použijte `domain=phi` parametr a verzi modelu `2020-04-01` nebo novější.
>
> Příklad: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/pii?domain=phi&model-version=2020-07-01`
 
Při odesílání požadavků na koncový bod se vrátí následující kategorie entit `/v3.1-preview.2/entities/recognition/pii` .

| Kategorie   | Subcategory | Popis                          | Spouští se verze modelu. | Poznámky |
|------------|-------------|--------------------------------------|------------------------|---|
| Osoba     | –         | Jména lidí.  | `2019-10-01`  | Také vrácen s `domain=phi` . |
| PersonType | –         | Typy úloh nebo role držené osobou. | `2020-02-01` | |
| PhoneNumber | – | Telefonní čísla (jenom USA a telefonní čísla EU). | `2019-10-01` | Také vrácen s `domain=phi` . |
|Organizace  | – | Společnosti, politické skupiny, hudební pásma, sportovní klub, státní orgány a veřejné organizace.  | `2019-10-01` | Státní příslušníky a náboženství nejsou zahrnuté do tohoto typu entity.  |
|Organizace | Lékař | Lékařské společnosti a skupiny. | `2020-04-01` |  |
|Organizace | Burzovní Exchange | Burzovní skupiny Exchange. | `2020-04-01` |  |
| Organizace | Sport | Organizace související s sportem. | `2020-04-01` |  |
| Adresa | – | Úplné poštovní adresy.  | `2020-04-01` | Také vrácen s `domain=phi` . |
| Evropské unie GPS – souřadnice | – | Souřadnice GPS pro umístění v rámci Evropské unie.  | `2019-10-01` |  |
| E-mail | – | E-mailové adresy. | `2019-10-01` | Také vrácen s `domain=phi` .   |
| URL | – | Adresy URL webů. | `2019-10-01` | Také vrácen s `domain=phi` . |
| IP adresa | – | Síťové IP adresy. | `2019-10-01` | Také vrácen s `domain=phi` . |
| DateTime | – | Data a denní doba. | `2019-10-01` |  | 
| Datum a čas | Datum | Kalendářní data kalendáře | `2019-10-01` | Také vrácen s `domain=phi` . |
| Množství | – | Čísla a číselná množství. | `2019-10-01` |  |
| Množství | Věk | Ve věku. | `2019-10-01` | | |

## <a name="azure-information"></a>Informace o Azure

Tato kategorie entit zahrnuje identifikovatelné informace Azure, včetně ověřovacích informací a připojovacích řetězců. K dispozici počínaje verzí modelu `2019-10-01` . Nevráceno s `domain=phi` parametrem.

| Subcategory                           | Popis                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Klíč ověření Azure DocumentDB             | Autorizační klíč pro server Azure Cosmos DB.                           |
| Připojovací řetězec databáze Azure IAAS a připojovací řetězec SQL Azure | Připojovací řetězec pro databázi Azure Infrastructure as a Service (IaaS) a připojovací řetězec SQL. |
| Připojovací řetězec SQL Azure           | Připojovací řetězec pro databázi v Azure SQL Database.                                |
| Připojovací řetězec Azure IoT           | Připojovací řetězec pro Azure IoT                        |
| Heslo pro nastavení publikování v Azure        | Heslo pro nastavení publikování v Azure                                        |
| Připojovací řetězec Azure Redis Cache   | Připojovací řetězec pro Redis Cache                             |
| SAS Azure                             | Připojovací řetězec pro službu Azure software as a Service (SaaS).                     |
| Připojovací řetězec Azure Service Bus   | Připojovací řetězec pro službu Azure Service Bus.                                 |
| Klíč účtu Azure Storage             | Klíč účtu pro účet služby Azure Storage.                                   |
| Klíč účtu Azure Storage (Obecné)   | Klíč obecného účtu pro účet služby Azure Storage.                           |
| Připojovací řetězec SQL Server          | Připojovací řetězec pro počítač se systémem SQL Server.                                         |

## <a name="identification"></a>Identifikace

[!INCLUDE [supported identification entities](./identification-entities.md)]
