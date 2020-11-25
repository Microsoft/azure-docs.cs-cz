---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 37917e0ed663675677f1d0452b5796120ca2694e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001716"
---
### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
**Propojená služba Azure Storage** umožňuje propojit účet Azure Storage s objektem pro vytváření dat Azure pomocí **klíče účtu**, který poskytuje datovou továrnu s globálním přístupem k Azure Storage. Následující tabulka uvádí popis pro prvky JSON specifické pro Azure Storage propojenou službu.

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ |Vlastnost Type musí být nastavená na: **AzureStorage** . |Yes |
| připojovací řetězec |Zadejte informace potřebné pro připojení k úložišti Azure pro vlastnost connectionString. |Yes |

Informace o tom, jak načíst přístupové klíče účtu úložiště, najdete v tématu [Správa přístupových](../articles/storage/common/storage-account-keys-manage.md)klíčů účtu úložiště.

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

### <a name="azure-storage-sas-linked-service"></a>Azure Storage propojená služba SAS
Sdílený přístupový podpis (SAS) poskytuje delegovaný přístup k prostředkům ve vašem účtu úložiště. Umožňuje klientovi udělit omezená oprávnění k objektům ve vašem účtu úložiště po určitou dobu a se zadanou sadou oprávnění bez nutnosti sdílení přístupových klíčů k účtu. SAS je identifikátor URI, který v parametrech dotazu zahrnuje všechny informace potřebné pro ověřený přístup k prostředku úložiště. Chcete-li získat přístup k prostředkům úložiště pomocí SAS, klient musí předat SAS pouze příslušnému konstruktoru nebo metodě. Další informace o SAS najdete v tématu [udělení omezeného přístupu k Azure Storage prostředkům pomocí sdílených přístupových podpisů (SAS)](../articles/storage/common/storage-sas-overview.md).

> [!IMPORTANT]
> Azure Data Factory teď podporuje jenom **SAS služby** , ale ne účet SAS. Všimněte si, že adresa URL SAS generable z Azure Portal nebo Průzkumník služby Storage je SAS účtu, který není podporovaný.

> [!TIP]
> Můžete provést následující příkazy prostředí PowerShell a vygenerovat pro svůj účet úložiště SAS služby (nahradíte držitele místa a udělí potřebné oprávnění): `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Propojená služba Azure Storage SAS umožňuje propojit účet Azure Storage s datovou továrnou Azure pomocí sdíleného přístupového podpisu (SAS). Poskytuje datovou továrnu s omezeným/časově vázaným přístupem ke všem/konkrétním prostředkům (objektům blob nebo kontejneru) v úložišti. Následující tabulka uvádí popis pro prvky JSON specifické pro Azure Storage propojenou službu SAS. 

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ |Vlastnost Type musí být nastavená na: **AzureStorageSas** . |Yes |
| sasUri |Zadejte identifikátor URI sdíleného přístupového podpisu k prostředkům Azure Storage, jako je například objekt blob, kontejner nebo tabulka.  |Yes |

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

Při vytváření **identifikátoru URI SAS** zvažte následující:  

* Nastavte vhodná **oprávnění** ke čtení a zápisu pro objekty na základě způsobu, jakým se ve vaší datové továrně používá propojená služba (čtení, zápis, čtení a zápis).
* Nastavte odpovídající **čas vypršení platnosti** . Ujistěte se, že v aktivním období kanálu není vypršet přístup k Azure Storage objektům.
* Identifikátor URI by měl být vytvořen na pravé úrovni kontejneru nebo objektu BLOB nebo tabulky podle potřeby. Identifikátor URI SAS pro objekt blob Azure umožňuje službě Data Factory získat přístup k tomuto konkrétnímu objektu BLOB. Identifikátor URI SAS pro kontejner objektů BLOB v Azure umožňuje službě Data Factory iterovat objekty BLOB v tomto kontejneru. Pokud potřebujete poskytnout přístup více nebo méně objektů později nebo aktualizovat identifikátor URI SAS, nezapomeňte aktualizovat propojenou službu novým identifikátorem URI.   

