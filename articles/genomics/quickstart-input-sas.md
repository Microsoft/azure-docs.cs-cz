---
title: Pracovní postup využívající signatury sdíleného přístupu
titleSuffix: Microsoft Genomics
description: Tento článek ukazuje, jak odeslat pracovní postup do služby Microsoft Genomics pomocí sdílených přístupových podpisů (SAS) místo klíčů účtu úložiště.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 03/02/2018
ms.openlocfilehash: 82f5e8b4a0c06517381857f0d914bcb65ba41d35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93394607"
---
# <a name="submit-a-workflow-to-microsoft-genomics-using-a-sas-instead-of-a-storage-account-key"></a>Odeslání pracovního postupu do služby Microsoft Genomics pomocí SAS místo klíče účtu úložiště 

Tento článek popisuje, jak odeslat pracovní postup do služby Microsoft Genomics pomocí config.txt souboru, který obsahuje [sdílené přístupové podpisy (SAS)](../storage/common/storage-sas-overview.md) místo klíčů účtu úložiště. Tato funkce může být užitečná v případě, že existují obavy ohledně zabezpečení kvůli viditelnému klíči účtu úložiště v souboru config.txt. 

Tento článek předpokládá, že jste už nainstalovali a spustili klienta `msgen` a že víte, jak používat službu Azure Storage. Pokud jste úspěšně odeslali pracovní postup pomocí poskytnutých ukázkových dat, budete připraveni pokračovat v tomto článku. 

## <a name="what-is-a-sas"></a>Co je SAS?
[Sdílený přístupový podpis (SAS)](../storage/common/storage-sas-overview.md) poskytuje delegovaný přístup k prostředkům ve vašem účtu úložiště. Pomocí SAS můžete udělit přístup k prostředkům ve vašem účtu úložiště, aniž byste sdíleli své klíče účtu. Toto je zásadní aspekt používání sdílených přístupových podpisů v aplikacích – SAS představuje bezpečný způsob sdílení prostředků úložiště, aniž byste ohrozili své klíče účtu.

SAS odeslaný do služby Microsoft Genomics musí být [SAS služby](/rest/api/storageservices/Constructing-a-Service-SAS), který deleguje přístup pouze k objektu blob nebo kontejneru, ve kterém jsou uložené vstupní a výstupní soubory. 

Identifikátor URI tokenu sdíleného přístupového podpisu (SAS) na úrovni služby se skládá z identifikátoru URI prostředku, ke kterému bude SAS delegovat přístup, následovaného tokenem SAS. Token SAS je řetězec dotazu, který zahrnuje všechny informace potřebné k ověření SAS a také určuje prostředek, dostupná oprávnění pro přístup, časový interval platnosti podpisu, podporovanou IP adresu nebo rozsah adres, ze kterých můžou přicházet požadavky, podporovaný protokol, přes který je možné požadavky provádět, volitelný identifikátor zásad přístupu přidružený k požadavku a samotný podpis. 

## <a name="sas-needed-for-submitting-a-workflow-to-the-microsoft-genomics-service"></a>SAS potřebný pro odeslání pracovního postupu do služby Microsoft Genomics
Každý pracovní postup odeslaný do služby Microsoft Genomics vyžaduje dva nebo více tokenů SAS – jeden pro každý vstupní soubor a jeden pro výstupní kontejner.

SAS pro vstupní soubory musí mít následující vlastnosti:
 - Rozsah (účet, kontejner, objekt blob): objekt blob
 - Vypršení platnosti: odteď za 48 hodin
 - Oprávnění: čtení

SAS pro výstupní kontejner musí mít následující vlastnosti:
 - Rozsah (účet, kontejner, objekt blob): kontejner
 - Vypršení platnosti: odteď za 48 hodin
 - Oprávnění: čtení, zápis, odstranění


## <a name="create-a-sas-for-the-input-files-and-the-output-container"></a>Vytvoření SAS pro vstupní soubory a výstupní kontejner
Existují dva způsoby, jak vytvořit token SAS – pomocí Průzkumníka služby Azure Storage nebo prostřednictvím kódu programu.  Pokud píšete kód, můžete SAS vytvořit sami nebo použít sadu SDK služby Azure Storage ve vašem upřednostňovaném jazyce.


### <a name="set-up-create-a-sas-using-azure-storage-explorer"></a>Nastavení: Vytvoření SAS pomocí Průzkumníka služby Azure Storage

[Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) je nástroj pro správu prostředků uložených ve službě Azure Storage.  Další informace o používání Průzkumníka služby Azure Storage najdete [tady](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Rozsah SAS pro vstupní soubory musí být omezený na konkrétní vstupní soubor (objekt blob). Pokud chcete vytvořit token SAS, postupujte podle [těchto pokynů](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). Po vytvoření SAS se na obrazovce zobrazí úplná adresa URL s řetězcem dotazu i samotný řetězec dotazu, které je možné zkopírovat.

 ![Genomika SAS Průzkumník služby Storage](./media/quickstart-input-sas/genomics-sas-storageexplorer.png "Genomika SAS Průzkumník služby Storage")


### <a name="set-up-create-a-sas-programmatically"></a>Nastavení: vytvoření SAS prostřednictvím kódu programu

Informace o vytvoření SAS pomocí sady SDK služby Azure Storage najdete ve stávající dokumentaci v různých jazycích, včetně [.NET](../storage/common/storage-sas-overview.md), [Pythonu](../storage/blobs/storage-quickstart-blobs-python.md) a [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md). 

Pokud chcete vytvořit SAS bez sady SDK, můžete řetězec dotazu SAS včetně všech informací potřebných k ověření SAS vytvořit přímo. Podrobný popis součástí řetězce dotazu SAS a způsobu jeho vytvoření najdete v těchto [pokynech](/rest/api/storageservices/constructing-a-service-sas). Požadovaný podpis SAS se vytvoří vygenerováním kódu HMAC s použitím ověřovacích informací o objektu blob nebo kontejneru, jak je popsáno v těchto[ pokynech](/rest/api/storageservices/service-sas-examples).


## <a name="add-the-sas-to-the-configtxt-file"></a>Přidání SAS do souboru config.txt
Pokud chcete spustit pracovní postup prostřednictvím služby Microsoft Genomics pomocí řetězce dotazu SAS, upravte soubor config.txt a odeberte z něj klíče. Pak připojte řetězec dotazu SAS (který začíná na `?`) k názvu výstupního kontejneru, jak je vidět níže. 

![Konfigurace SAS pro genomiku](./media/quickstart-input-sas/genomics-sas-config.png "Konfigurace SAS pro genomiku")

Pomocí pythonového klienta Microsoft Genomics odešlete svůj pracovní postup následujícím příkazem, ve kterém k názvům všech vstupních objektů blob připojíte odpovídající řetězec dotazu SAS:

```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read file, SAS query string appended] -b2 [name of your second paired end read file, SAS query string appended]
```

### <a name="if-adding-the-input-file-names-to-the-configtxt-file"></a>Přidávání názvů vstupních souborů do souboru config.txt
Alternativně je možné názvy souborů sekvencí paired-end s připojenými tokeny dotazů SAS přidat přímo do souboru config.txt, jak je vidět níže:

![Genomika SAS konfigurace blobnames](./media/quickstart-input-sas/genomics-sas-config-blobnames.png "Genomika SAS konfigurace blobnames")

V tomto případě pomocí pythonového klienta Microsoft Genomics odešlete svůj pracovní postup následujícím příkazem, ve kterém vynecháte příkazy `-b1` a `-b2`:

```python
msgen submit -f [full path to your config file] 
```

## <a name="next-steps"></a>Další kroky
V tomto článku jste odeslali pracovní postup do služby Microsoft Genomics pomocí pythonového klienta `msgen` s použitím tokenů SAS místo klíčů účtu. Další informace týkající se odesílání pracovních postupů a dalších příkazů, které můžete použít se službou Microsoft Genomics, najdete v [nejčastějších dotazech](frequently-asked-questions-genomics.md).