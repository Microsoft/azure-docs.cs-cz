---
title: Pojmenované entity pro osobní údaje
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/30/2020
ms.author: aahi
ms.openlocfilehash: dd7a8b94aefbf389afef30b327ffaa367a30dd51
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108484"
---
> [!NOTE]
> Chcete-li zjistit `PHI` (chráněné informace o stavu), použijte `domain=phi` parametr a verzi modelu `2020-04-01` nebo novější.
>
> Příklad: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii?domain=phi&model-version=2020-04-01`
 
Při odesílání požadavků do koncového bodu se vrátí následující kategorie entit `/v3.1-preview.1/entities/recognition/pii` .

| Kategorie   | Subcategory | Description                          | Spouští se verze modelu. | Poznámky |
|------------|-------------|--------------------------------------|------------------------|---|
| Person (Osoba)     | Není k dispozici         | Jména lidí.  | `2019-10-01`  | Také vrácen s `domain=phi` . |
| PersonType | Není k dispozici         | Typy úloh nebo role držené osobou. | `2020-02-01` | |
| PhoneNumber | Není k dispozici | Telefonní čísla (jenom USA a telefonní čísla EU). | `2019-10-01` | Také vrácen s`domain=phi` |
|Organizace  | Není k dispozici | Společnosti, politické skupiny, hudební pásma, sportovní klub, státní orgány a veřejné organizace.  | `2019-10-01` | Státní příslušníky a náboženství nejsou zahrnuté do tohoto typu entity.  |
|Organizace | Lékař | Lékařské společnosti a skupiny. | `2020-04-01` | Také vrácen s `domain=phi` . |
|Organizace | Burzovní Exchange | Burzovní skupiny Exchange. | `2020-04-01` | Také vrácen s `domain=phi` . |
| Organizace | Sport | Organizace související s sportem. | `2020-04-01` | Také vrácen s `domain=phi` . |
| Adresa | Není k dispozici | Úplné poštovní adresy.  | `2020-04-01` | Také vrácen s `domain=phi` . |
| Evropské unie GPS – souřadnice | Není k dispozici | Souřadnice GPS pro umístění v rámci Evropské unie.  | `2019-10-01` |  |
| E-mail | Není k dispozici | E-mailové adresy. | `2019-10-01` | Také vrácen s `domain=phi` .   |
| URL | Není k dispozici | Adresy URL webů. | `2019-10-01` | Také vrácen s `domain=phi` . |
| IP adresa | Není k dispozici | Síťové IP adresy. | `2019-10-01` | |
| DateTime | Není k dispozici | Data a denní doba. | `2019-10-01` |  | 
| DateTime | Datum | Kalendářní data kalendáře | `2019-10-01` | Také vrácen s `domain=phi` . |
| Množství | Není k dispozici | Čísla a číselná množství. | `2019-10-01` |  |
| Množství | Věk | Ve věku. | `2019-10-01` | | |
| Mezinárodní klasifikace nemocí (ICD – 9-CM) | Není k dispozici | Entity týkající se mezinárodní klasifikace nemocí, deváté revize.   | `2020-04-01` | |
| Mezinárodní klasifikace nemocí (ICD – 10 – CM) | Není k dispozici | Entity týkající se mezinárodní klasifikace nemocí a desáté revize.    | `2020-04-01` | |

## <a name="azure-information"></a>Informace o Azure

Tato kategorie entit zahrnuje identifikovatelné informace Azure včetně ověřovacích informací a připojovacích řetězců. K dispozici počínaje verzí modelu `2019-10-01` . Nevráceno s `domain=phi` parametrem.

| Subcategory                           | Description                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Klíč ověření Azure DocumentDB             | Autorizační klíč pro server Azure DocumentDB                           |
| Připojovací řetězec databáze Azure IAAS a připojovací řetězec SQL Azure | Připojovací řetězec pro databázi Azure Infrastructure as a Service (IaaS) a připojovací řetězec SQL. |
| Připojovací řetězec SQL Azure           | Připojovací řetězec pro databázi v Azure SQL Database.                                |
| Připojovací řetězec Azure IoT           | Připojovací řetězec pro Azure Internet věcí (IoT)                        |
| Heslo pro nastavení publikování v Azure        | Heslo pro nastavení publikování v Azure                                        |
| Připojovací řetězec Azure Redis Cache   | Připojovací řetězec pro službu Azure cache pro Redis.                             |
| SAS Azure                             | Připojovací řetězec pro službu Azure software as a Service (SAS).                     |
| Připojovací řetězec Azure Service Bus   | Připojovací řetězec pro službu Azure Service Bus.                                 |
| Klíč účtu Azure Storage             | Klíč účtu pro účet služby Azure Storage.                                   |
| Klíč účtu Azure Storage (Obecné)   | Klíč obecného účtu pro účet služby Azure Storage.                           |
| Připojovací řetězec SQL Server          | Připojovací řetězec pro systém SQL Server.                                         |

## <a name="identification"></a>Identifikace

[!INCLUDE [supported identification entities](./identification-entities.md)]
