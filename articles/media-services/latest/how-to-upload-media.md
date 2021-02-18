---
title: nahrání multimédií: Azure Media Services popis: Naučte se nahrávat multimédia pro streamování nebo kódování.
služby: Media-Services documentationcenter: ' ' Author: IngridAtMicrosoft Manager: femila Editor: ' '

MS. Service: Media-Services MS. rebavování: MS. téma: How-to MS. Date: 08/31/2020 MS. Author: inhenkel
---

# <a name="upload-media-for-streaming-or-encoding"></a>Nahrání média pro streamování nebo kódování

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V Media Services nahrajete digitální soubory (Media) do kontejneru objektů BLOB přidruženého k assetu. Entita [assetu](/rest/api/media/operations/asset) může obsahovat video, zvuk, obrázky, kolekce miniatur, textové stopy a soubory titulků (a metadata o těchto souborech). Po nahrání souborů do kontejneru assetu se váš obsah bezpečně uloží do cloudu pro další zpracování a streamování.

Než začnete, budete muset shromáždit několik hodnot nebo si je představit.

1. Místní cesta k souboru, který chcete nahrát.
1. ID assetu (kontejneru)
1. Název, který chcete použít pro nahraný soubor, včetně rozšíření
1. Název účtu úložiště, který používáte
1. Přístupový klíč pro účet úložiště

## <a name="portal"></a>[Azure Portal](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[Rozhraní příkazového řádku](#tab/cli/)

[!INCLUDE [Upload files with the CLI](./includes/task-upload-file-to-asset-cli.md)]

## <a name="python"></a>[Python](#tab/python)

Za předpokladu, že váš kód již navázal ověřování a již jste vytvořili vstupní Asset, můžete k odeslání místních souborů do tohoto prostředku (in_container) použít následující fragment kódu.

```python
#The storage objects
from azure.storage.blob import BlobServiceClient, BlobClient

#Establish storage variables
storage_account_name = '<your storage account name'
storage_account_key = '<your storage account key'
storage_blob_url = 'https://<your storage account name>.blob.core.windows.net/'

in_container = 'asset-' + inputAsset.asset_id

#The file path of local file you want to upload
source_file = "ignite.mp4"

# Use the Storage SDK to upload the video
blob_service_client = BlobServiceClient(account_url=storage_blob_url, credential=storage_account_key)
blob_client = blob_service_client.get_blob_client(in_container,source_file)

# Upload the video to storage as a block blob
with open(source_file, "rb") as data:
    blob_client.upload_blob(data, blob_type="BlockBlob")
```

---
<!-- add these to the tabs when available -->
Další metody naleznete v [dokumentaci Azure Storage](../../storage/blobs/index.yml) pro práci s objekty BLOB v [jazycích .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md), [Java](../../storage/blobs/storage-quickstart-blobs-java.md), [Python](../../storage/blobs/storage-quickstart-blobs-python.md)a [JavaScript (Node.js)](../../storage/blobs/storage-quickstart-blobs-nodejs.md).

## <a name="next-steps"></a>Další kroky

> [Přehled Media Services](media-services-overview.md)
