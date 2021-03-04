---
title: Pojmenované entity pro osobní údaje
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: 97167485dae155670f0eb83fc3ef9cb658952251
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750236"
---
> [!NOTE]
> K detekci chráněných informací o stavu (FÍ) použijte `domain=phi` parametr a verzi modelu `2020-04-01` nebo novější.
>
> Příklad: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
Při odesílání požadavků na koncový bod se vrátí následující kategorie entit `/v3.1-preview.3/entities/recognition/pii` .


| Kategorie   |  Popis                          |
|------------|-------------|
| [Person (Osoba)](#category-person)      |  Jména lidí.  |
| [PersonType](#category-persontype) | Typy úloh nebo role držené osobou. |
| [Telefonní číslo](#category-phonenumber) |Telefonní čísla (jenom USA a telefonní čísla EU). |
| [Organizace](#category-organization) |  Společnosti, skupiny, státní instituce a jiné organizace.  |
| [Adresa](#category-address) | Úplné poštovní adresy.  |
| [E-mail](#category-email) | E-mailové adresy.   |
| [Adresa URL](#category-url) | Adresy URL webů.  |
| [IP adresa](#category-ip) | Síťové IP adresy.  |
| [Datum a čas](#category-datetime) | Data a denní doba. | 
| [Množství](#category-quantity) | Čísla a číselná množství.  |
| [Informace o Azure](#azure-information) | Identifikovatelné informace Azure, například informace o ověřování.  |
| [Identifikace](#identification) | Identifikace specifická pro finance a zemi.  |

### <a name="category-person"></a>Kategorie: osoba

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        Person (Osoba)

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Jména lidí. Také vrácen s `domain=phi` .
      
    :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>Kategorie: PersonType

Tato kategorie obsahuje následující entitu:


:::row:::
    :::column span="":::
        **Entita**

        PersonType

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Typy úloh nebo role držené osobou.
      
    :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>Kategorie: PhoneNumber

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Telefonní čísla (jenom USA a telefonní čísla EU). Také vrácen s `domain=phi` .
      
    :::column-end:::
:::row-end:::


### <a name="category-organization"></a>Kategorie: organizace

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        Organizace

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Společnosti, politické skupiny, hudební pásma, sportovní klub, státní orgány a veřejné organizace. Státní příslušníky a náboženství nejsou zahrnuté do tohoto typu entity.
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Podkategorie

Entita v této kategorii může mít následující podkategorie.

:::row:::
    :::column span="":::
        **Podkategorie entity**

        Lékař

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Lékařské společnosti a skupiny.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Burzovní Exchange

    :::column-end:::
    :::column span="2":::

        Burzovní skupiny Exchange. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sport

    :::column-end:::
    :::column span="2":::

        Organizace související s sportem.
      
    :::column-end:::

:::row-end:::


### <a name="category-address"></a>Kategorie: adresa

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        Adresa

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Úplná poštovní adresa.
      
    :::column-end:::
:::row-end:::

### <a name="category-email"></a>Kategorie: E-mail

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        E-mail

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        E-mailové adresy.
      
    :::column-end:::
:::row-end:::

### <a name="category-url"></a>Kategorie: adresa URL

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        URL

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Adresy URL webů. 
      
    :::column-end:::
:::row-end:::

### <a name="category-ip"></a>Kategorie: IP

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        IP adresa

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        síťové IP adresy. 
      
    :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>Kategorie: DateTime

Tato kategorie obsahuje následující entity:

:::row:::
    :::column span="":::
        **Entita**

        DateTime

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Data a denní doba. 
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Podkategorie

Entita v této kategorii může mít následující podkategorie.

:::row:::
    :::column span="":::
        **Podkategorie entity**

        Date (Datum)

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Kalendářní data kalendáře
      
    :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>Kategorie: množství

Tato kategorie obsahuje následující entity:

:::row:::
    :::column span="":::
        **Entita**

        Množství

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Čísla a číselná množství.
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Podkategorie

Entita v této kategorii může mít následující podkategorie.

:::row:::
    :::column span="":::
        **Podkategorie entity**

        Věk

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Ve věku.
      
    :::column-end:::
:::row-end:::

### <a name="azure-information"></a>Informace o Azure

Tyto kategorie entit obsahují identifikovatelné informace Azure, včetně ověřovacích informací a připojovacích řetězců. Nevráceno s `domain=phi` parametrem.

:::row:::
    :::column span="":::
        **Entita**

        Klíč ověření Azure DocumentDB 

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Autorizační klíč pro server Azure Cosmos DB.   
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Připojovací řetězec databáze Azure IAAS a připojovací řetězec SQL Azure

    :::column-end:::
    :::column span="2":::

        Připojovací řetězec pro databázi Azure Infrastructure as a Service (IaaS) a připojovací řetězec SQL.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Připojovací řetězec SQL Azure

    :::column-end:::
    :::column span="2":::

        Připojovací řetězec pro databázi v Azure SQL Database.
      
    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Připojovací řetězec Azure IoT  

    :::column-end:::
    :::column span="2":::

        Připojovací řetězec pro Azure IoT 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Heslo pro nastavení publikování v Azure  

    :::column-end:::
    :::column span="2":::

        Heslo pro nastavení publikování v Azure
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Připojovací řetězec Azure Redis Cache 

    :::column-end:::
    :::column span="2":::

        Připojovací řetězec pro Redis Cache
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        SAS Azure 

    :::column-end:::
    :::column span="2":::

        Připojovací řetězec pro službu Azure software as a Service (SaaS).
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Připojovací řetězec Azure Service Bus

    :::column-end:::
    :::column span="2":::

        Připojovací řetězec pro službu Azure Service Bus.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Klíč účtu Azure Storage 

    :::column-end:::
    :::column span="2":::

       Klíč účtu pro účet služby Azure Storage. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Klíč účtu Azure Storage (Obecné)

    :::column-end:::
    :::column span="2":::

       Klíč obecného účtu pro účet služby Azure Storage.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Připojovací řetězec SQL Server 

    :::column-end:::
    :::column span="2":::

       Připojovací řetězec pro počítač se systémem SQL Server.
      
    :::column-end:::
:::row-end:::

### <a name="identification"></a>Identifikace

[!INCLUDE [supported identification entities](./identification-entities.md)]
