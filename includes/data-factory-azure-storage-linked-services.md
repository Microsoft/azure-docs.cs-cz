---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: e5f2afa4bc8a4b8eae523fde323d835c0c53fe8e
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571963"
---
### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
**Propojenou službu Azure Storage** umožňuje propojit účet úložiště Azure do služby Azure data factory pomocí **klíč účtu**, který nabízí služby data factory a globální přístup ke službě Azure Storage. Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro propojenou službu Azure Storage.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost type musí být nastavená na: **AzureStorage** |Ano |
| připojovací řetězec |Zadejte informace potřebné pro připojení k Azure storage pro vlastnost připojovací řetězec. |Ano |

Naleznete v následující části najdete postup zobrazení nebo kopírování klíč účtu pro službu Azure Storage: [přístupové klíče](../articles/storage/common/storage-account-manage.md#access-keys).

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
Sdílený přístupový podpis (SAS) poskytuje Delegovaný přístup k prostředkům ve vašem účtu úložiště. Umožňuje udělit že klienta omezená oprávnění k objektům v účtu úložiště na dobu určitou dobu a s konkrétní sadou oprávnění, aniž byste museli sdílet přístupové klíče vašeho účtu. PODPIS je identifikátor URI, který zahrnuje všechny informace potřebné pro ověřený přístup k prostředku úložiště v jeho parametry dotazu. Pro přístup k prostředkům úložiště pomocí sdíleného přístupového podpisu, musí klient pouze a zajistěte tak předání SAS odpovídajícího konstruktoru nebo metody. Podrobné informace o SAS najdete v tématu [sdílené přístupové podpisy: vysvětlení modelu SAS](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> Azure Data Factory podporuje nyní pouze **SAS služby** , ale ne SAS účtu. V tématu [typy z sdílené přístupové podpisy](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) podrobné informace o těchto dvou typů a jak sestavit. Poznámka: adresa URL SAS generable z webu Azure portal nebo Průzkumníka služby Storage je SAS účtu, který není podporován.

> [!TIP]
> Spuštěním následujících příkazů prostředí PowerShell k vygenerování SAS služby pro účet úložiště (nahrazení zástupné znaky a udělit příslušná oprávnění): `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Služby Azure Storage SAS propojené umožňuje propojit účet úložiště Azure do služby Azure data factory pomocí sdílený přístupový podpis (SAS). Poskytuje objekt pro vytváření dat s omezením pomocí specifikátoru/časově omezenou přístup k prostředkům all/konkrétní (objektu blob nebo kontejneru) ve službě storage. Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro SAS úložiště Azure, propojené služby. 

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost type musí být nastavená na: **AzureStorageSas** |Ano |
| sasUri |Zadejte identifikátor URI podpis sdíleného přístupu k prostředkům služby Azure Storage jako objekt blob, kontejneru nebo tabulky.  |Ano |

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

Při vytváření **identifikátor URI SAS**, vzhledem k tomu následující:  

* Nastavte odpovídající čtení/zápis **oprávnění** na objekty podle použití propojené služby (čtení, zápisu, čtení a zápis) ve službě data factory.
* Nastavte **čas vypršení platnosti** odpovídajícím způsobem. Ujistěte se, že přístup k objektům služby Azure Storage, nemá prošlou platnost do aktivního období kanálu.
* Identifikátor URI musí být vytvořené na správný kontejner nebo objekt blob nebo Table úrovni podle potřeby. Identifikátor Uri SAS do objektu blob Azure umožňuje službě Data Factory pro přístup k této konkrétní objekt blob. Identifikátor Uri SAS pro kontejner objektů blob v Azure umožňuje službě Data Factory k iteraci v rámci objektů BLOB v tomto kontejneru. Pokud potřebujete k zajištění přístupu více nebo méně objektů později nebo aktualizujte identifikátor URI SAS, nezapomeňte aktualizovat propojenou službu s nový identifikátor URI.   

