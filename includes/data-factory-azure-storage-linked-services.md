---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 37917e0ed663675677f1d0452b5796120ca2694e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75468626"
---
### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
**Propojená služba Azure Storage** umožňuje propojit účet úložiště Azure s továrně dat Azure pomocí **klíče účtu**, který poskytuje datové továrně globální přístup k Úložišti Azure. Následující tabulka obsahuje popis prvků JSON specifických pro propojenou službu Azure Storage.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost type musí být nastavena na: **AzureStorage.** |Ano |
| připojovací řetězec |Zadejte informace potřebné pro připojení k úložišti Azure pro vlastnost connectionString. |Ano |

Informace o tom, jak načíst přístupové klíče účtu úložiště, najdete v [tématu Správa přístupových klíčů účtu úložiště](../articles/storage/common/storage-account-keys-manage.md).

**Příklad:**  

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="azure-storage-sas-linked-service"></a>Propojená služba Azure Storage Sas
Sdílený přístupový podpis (SAS) poskytuje delegovaný přístup k prostředkům ve vašem účtu úložiště. Umožňuje udělit klientovi omezená oprávnění k objektům v účtu úložiště po určitou dobu a se zadanou sadou oprávnění, aniž byste museli sdílet přístupové klíče účtu. SAS je identifikátor URI, který ve svých parametrech dotazu zahrnuje všechny informace potřebné pro ověřený přístup k prostředku úložiště. Pro přístup k prostředkům úložiště s SAS, klient potřebuje pouze předat v SAS příslušného konstruktoru nebo metody. Další informace o SAS najdete [v tématu udělení omezeného přístupu k prostředkům Úložiště Azure pomocí sdílených přístupových podpisů (SAS).](../articles/storage/common/storage-sas-overview.md)

> [!IMPORTANT]
> Azure Data Factory teď podporuje **jenom službu SAS,** ale ne účet SAS. Všimněte si, že adresa URL SAS generable z portálu Azure nebo Průzkumníka úložiště je účet SAS, který není podporován.

> [!TIP]
> Můžete spustit pod příkazy Prostředí PowerShell generovat služby SAS pro váš účet úložiště (nahradit zástupné symboly a udělit potřebná oprávnění):`$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Propojená služba Azure Storage SAS umožňuje propojit účet úložiště Azure s totodatovou továrně dat Azure pomocí sdíleného přístupového podpisu (SAS). Poskytuje datové továrny s omezený/časově vázaný přístup ke všem/konkrétní prostředky (objekt blob/kontejner) v úložišti. Následující tabulka obsahuje popis prvků JSON specifických pro propojenou službu Azure Storage SAS. 

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost type musí být nastavena na: **AzureStorageSas** |Ano |
| sasUri |Zadejte identifikátor URI sdíleného přístupového podpisu pro prostředky úložiště Azure, jako je objekt blob, kontejner nebo tabulka.  |Ano |

**Příklad:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"
        }
    }
}
```

Při vytváření **identifikátoru URI SAS**zvažte následující:  

* Nastavte příslušná **oprávnění** pro čtení a zápis u objektů na základě toho, jak se propojená služba (čtení, zápis, čtení a zápis) používá ve vaší datové továrně.
* Nastavte **dobu expirace** odpovídajícím způsobem. Ujistěte se, že přístup k objektům Služby Azure Storage nevyprší během aktivní období kanálu.
* Uri by měl být vytvořen na pravé úrovni kontejneru/objektu blob nebo tabulky na základě potřeby. Identifikátor SAS Uri pro objekt blob Azure umožňuje službě Data Factory přístup k tomuto konkrétnímu objektu blob. Identifikátor SAS Uri pro kontejner objektů blob Azure umožňuje službě Data Factory iterate prostřednictvím objektů BLOB v tomto kontejneru. Pokud potřebujete později poskytnout přístup k více/méně objektům nebo aktualizovat identifikátor URI SAS, nezapomeňte aktualizovat propojenou službu pomocí nového identifikátoru URI.   

