### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
**Propojená služba Azure Storage** umožňuje propojení účtu úložiště Azure s objektem pro vytváření dat Azure pomocí **klíč účtu**, který poskytuje objekt pro vytváření dat s globálním přístupem do úložiště Azure. Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro propojenou službu úložiště Azure.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost typu musí být nastavena na: **azurestorage.** |Ano |
| připojovací řetězec |Zadejte informace potřebné pro připojení k úložišti Azure pro vlastnost connectionString. |Ano |

Najdete v následujícím článku postup zobrazení nebo zkopírování klíč účtu pro Azure Storage: [zobrazení, kopírování a opětovné vytváření úložiště přístupové klíče](../articles/storage/common/storage-create-storage-account.md#manage-your-storage-account).

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

### <a name="azure-storage-sas-linked-service"></a>Propojená služba úložiště Azure Sas
Sdílený přístupový podpis (SAS) poskytuje Delegovaný přístup k prostředkům ve vašem účtu úložiště. Umožňuje udělit, že klient omezené oprávnění k objektům v účtu úložiště v zadaném časovém intervalu a zadanou sadu oprávnění, aniž by museli sdílet klíče pro přístup k účtu. SAS je identifikátor URI, který zahrnuje všechny informace potřebné pro ověřený přístup k prostředku úložiště v jeho parametry dotazu. Pro přístup k prostředkům úložiště s SAS, klient pouze musí předat SAS metodu, nebo odpovídající konstruktor. Podrobné informace o tokenu SAS naleznete v tématu [sdílené přístupové podpisy: vysvětlení modelu SAS](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> Azure Data Factory podporuje nyní pouze **SAS služby** , ale není SAS účtu. V tématu [typy z sdílené přístupové podpisy](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) podrobné informace o těchto dvou typů a postup vytvoření. Poznamenejte si adresu SAS URL generable z portálu Azure nebo Storage Explorer je SAS účtu, který není podporován.

> [!TIP]
> Můžete spustit následující příkazy prostředí PowerShell ke generování SAS služby pro účet úložiště (nahraďte zástupného a udělení potřebných oprávnění):`$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Služba Azure úložiště SAS propojené umožňuje propojení účet úložiště Azure do Azure data factory pomocí sdíleného přístupového podpisu (SAS). Poskytuje objekt pro vytváření dat omezený nebo časově vázaných přístup k prostředkům všechna nebo konkrétní (kontejner nebo objektů blob) v úložišti. Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro SAS úložiště Azure, propojené služby. 

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost typu musí být nastavena na: **AzureStorageSas** |Ano |
| sasUri |Zadejte identifikátor URI podpis sdíleného přístupu k prostředkům Azure Storage jako objekt blob, kontejneru nebo tabulky.  |Ano |

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

Při vytváření **identifikátor URI pro SAS**, vzhledem k tomu následující:  

* Nastavit vhodné pro čtení a zápis **oprávnění** na objekty podle použití propojené služby (pro čtení, zápisu, čtení/zápis) ve službě data factory.
* Nastavit **čas vypršení platnosti** správně. Ujistěte se, že přístup k objektům Azure Storage, nemá prošlou platnost do aktivního období kanálu.
* Identifikátor URI by měl být vytvořen na úrovni tabulky podle potřeby nebo správné kontejner nebo objekt blob. Identifikátor Uri SAS do objektu blob Azure umožňuje služba Data Factory pro přístup k tomuto konkrétní objektu blob. Identifikátor Uri SAS pro kontejner objektů blob v Azure umožňuje služba Data Factory k iteraci v rámci objektů BLOB v kontejneru. Pokud potřebujete poskytovat přístup více nebo méně objektů později, nebo aktualizovat identifikátor URI SAS, nezapomeňte aktualizovat propojenou službu s nový identifikátor URI.   

