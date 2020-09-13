---
title: Pojmenované entity pro osobní údaje
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: c50beef5c9c5dcae7edd487e8bf3d192ba557865
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662855"
---
> [!NOTE]
> K detekci chráněných informací o stavu (FÍ) použijte `domain=phi` parametr a verzi modelu `2020-04-01` nebo novější.
>
> Příklad: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii?domain=phi&model-version=2020-04-01`
 
Při odesílání požadavků na koncový bod se vrátí následující kategorie entit `/v3.1-preview.1/entities/recognition/pii` .

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
| Datum a čas | – | Data a denní doba. | `2019-10-01` |  | 
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
