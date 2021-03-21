---
title: Pojmenované entity pro osobní údaje
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/15/2021
ms.author: aahi
ms.openlocfilehash: 19586c09cca9a0dc74ba9ee4ef9da459964f9b7e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599296"
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

        Jména lidí. 

        Chcete-li získat tuto kategorii entit, přidejte `Person` do `pii-categories` parametru. `Person` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
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

        Chcete-li získat tuto kategorii entit, přidejte `PersonType` do `pii-categories` parametru. `PersonType` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::

    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
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

        Chcete-li získat tuto kategorii entit, přidejte `PhoneNumber` do `pii-categories` parametru. `PhoneNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::

    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
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

        Chcete-li získat tuto kategorii entit, přidejte `Organization` do `pii-categories` parametru. `Organization` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::

    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
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

        Chcete-li získat tuto kategorii entit, přidejte `OrganizationMedical` do `pii-categories` parametru. `OrganizationMedical` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::

    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Burzovní Exchange

    :::column-end:::
    :::column span="2":::

        Burzovní skupiny Exchange. 

        Chcete-li získat tuto kategorii entit, přidejte `OrganizationStockExchange` do `pii-categories` parametru. `OrganizationStockExchange` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Sport

    :::column-end:::
    :::column span="2":::

        Organizace související s sportem.

        Chcete-li získat tuto kategorii entit, přidejte `OrganizationSports` do `pii-categories` parametru. `OrganizationSports` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
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

        Chcete-li získat tuto kategorii entit, přidejte `Address` do `pii-categories` parametru. `Address` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::

    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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
      
        Chcete-li získat tuto kategorii entit, přidejte `Email` do `pii-categories` parametru. `Email` Vrátí se v odpovědi rozhraní API, pokud se zjistí.

    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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

        Chcete-li získat tuto kategorii entit, přidejte `URL` do `pii-categories` parametru. `URL` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::

    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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

        Chcete-li získat tuto kategorii entit, přidejte `IP` do `pii-categories` parametru. `IP` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::

    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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

        Chcete-li získat tuto kategorii entit, přidejte `DateTime` do `pii-categories` parametru. `DateTime` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
:::column span="":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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

        Chcete-li získat tuto kategorii entit, přidejte `Date` do `pii-categories` parametru. `Date` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**
      
      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
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

        Chcete-li získat tuto kategorii entit, přidejte `Quantity` do `pii-categories` parametru. `Quantity` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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

        Chcete-li získat tuto kategorii entit, přidejte `Age` do `pii-categories` parametru. `Age` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="2":::
        **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
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

        Chcete-li získat tuto kategorii entit, přidejte `AzureDocumentDBAuthKey` do `pii-categories` parametru. `AzureDocumentDBAuthKey` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Připojovací řetězec databáze Azure IAAS a připojovací řetězec služby Azure SQL.
        

    :::column-end:::
    :::column span="2":::

        Připojovací řetězec pro databázi Azure Infrastructure as a Service (IaaS) a připojovací řetězec SQL.

        Chcete-li získat tuto kategorii entit, přidejte `AzureIAASDatabaseConnectionAndSQLString` do `pii-categories` parametru. `AzureIAASDatabaseConnectionAndSQLString` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Připojovací řetězec Azure IoT  

    :::column-end:::
    :::column span="2":::

        Připojovací řetězec pro Azure IoT 
      
        Chcete-li získat tuto kategorii entit, přidejte `AzureIoTConnectionString` do `pii-categories` parametru. `AzureIoTConnectionString` Vrátí se v odpovědi rozhraní API, pokud se zjistí.

    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Heslo pro nastavení publikování v Azure  

    :::column-end:::
    :::column span="2":::

        Heslo pro nastavení publikování v Azure

        Chcete-li získat tuto kategorii entit, přidejte `AzurePublishSettingPassword` do `pii-categories` parametru. `AzurePublishSettingPassword` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Připojovací řetězec Azure Redis Cache 

    :::column-end:::
    :::column span="2":::

        Připojovací řetězec pro Redis Cache

        Chcete-li získat tuto kategorii entit, přidejte `AzureRedisCacheString` do `pii-categories` parametru. `AzureRedisCacheString` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        SAS Azure 

    :::column-end:::
    :::column span="2":::

        Připojovací řetězec pro službu Azure software as a Service (SaaS).

        Chcete-li získat tuto kategorii entit, přidejte `AzureSAS` do `pii-categories` parametru. `AzureSAS` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Připojovací řetězec Azure Service Bus

    :::column-end:::
    :::column span="2":::

        Připojovací řetězec pro službu Azure Service Bus.

        Chcete-li získat tuto kategorii entit, přidejte `AzureServiceBusString` do `pii-categories` parametru. `AzureServiceBusString` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Klíč účtu Azure Storage 

    :::column-end:::
    :::column span="2":::

        Klíč účtu pro účet služby Azure Storage. 

        Chcete-li získat tuto kategorii entit, přidejte `AzureStorageAccountKey` do `pii-categories` parametru. `AzureStorageAccountKey` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Klíč účtu Azure Storage (Obecné)

    :::column-end:::
    :::column span="2":::

        Klíč obecného účtu pro účet služby Azure Storage.

        Chcete-li získat tuto kategorii entit, přidejte `AzureStorageAccountGeneric` do `pii-categories` parametru. `AzureStorageAccountGeneric` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Připojovací řetězec SQL Server 

    :::column-end:::
    :::column span="2":::

        Připojovací řetězec pro počítač se systémem SQL Server.

        Chcete-li získat tuto kategorii entit, přidejte `SQLServerConnectionString` do `pii-categories` parametru. `SQLServerConnectionString` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::

### <a name="identification"></a>Identifikace

[!INCLUDE [supported identification entities](./identification-entities.md)]
