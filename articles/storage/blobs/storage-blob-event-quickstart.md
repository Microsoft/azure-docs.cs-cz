---
title: Koncový bod vlastní webové směrovat událostí Azure Blob storage | Microsoft Docs
description: Pomocí služby Azure Event Grid se můžete přihlásit k odběru událostí služby Blob Storage.
services: storage,event-grid
keywords: ''
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: f0764ebc423cfb5323f2b634ce5a5ecbe075135c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-with-azure-cli"></a>Události úložiště objektů Blob trasy pro koncový bod vlastní webové pomocí rozhraní příkazového řádku Azure

Azure Event Grid je služba zpracování událostí pro cloud. V tomto článku se pomocí Azure CLI přihlásíte k odběru událostí služby Blob Storage a aktivujete událost, abyste viděli výsledek. 

Obvykle odesíláte události do koncového bodu, který na událost reaguje například webhookem nebo funkcí Azure Functions. Pro zjednodušení příkladu v tomto článku budeme události odesílat na adresu URL, která jenom shromažďuje zprávy. Vytvořte tuto adresu URL pomocí nástroje třetích stran z [Hookbin](https://hookbin.com/).

> [!NOTE]
> **Hookbin** není určen pro použití vysoké propustnosti. Použití tohoto nástroje je čistě demonstrative. Pokud najednou nabídnete více než jednu událost, možná se v nástroji nezobrazí všechny.

Po dokončení kroků popsaných v tomto článku uvidíte, že se data událostí odeslala do koncového bodu.

[!INCLUDE [quickstarts-free-trial-note.md](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, pro účely tohoto článku musíte používat nejnovější verzi Azure CLI (2.0.24 nebo novější). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli).

Pokud nepoužíváte cloudové prostředí, musíte se nejdřív přihlásit pomocí `az login`.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Témata služby Event Grid jsou prostředky Azure a musí být umístěné ve skupině prostředků Azure. Skupina prostředků je logická kolekce, ve které se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). 

Následující příklad vytvoří skupinu prostředků `<resource_group_name>` v umístění *westcentralus*.  Nahraďte `<resource_group_name>` jedinečným názvem vaší skupiny prostředků.

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Pokud chcete použít události úložiště objektů Blob, je nutné buď [účtem služby Blob storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) nebo [účtem úložiště pro obecné účely v2](../common/storage-account-options.md#general-purpose-v2). **Obecné účely v2 (GPv2)** jsou účty úložiště, které podporují všechny funkce pro všechny služby úložiště, včetně objektů BLOB, soubory, fronty a tabulky. A **účtem služby Blob storage** je specializovaný účet úložiště pro ukládání nestrukturovaných dat jako blobů (objektů) ve službě Azure Storage. Účty úložiště BLOB jsou podobné účtům úložiště pro obecné účely a sdílejí všechny skvělé odolnost, dostupnost, škálovatelnost a výkon funkce používají. jednotlivá včetně 100 % konzistentnost rozhraní API pro objekty BLOB bloku a doplňovací objekty BLOB. V případě aplikací, které vyžadují jenom úložiště objektů blob bloku nebo objektů blob doporučujeme používat účty úložiště objektů blob. 

> [!NOTE]
> Dostupnost pro události úložiště je vázaný na události mřížky [dostupnosti](../../event-grid/overview.md) a bude dostupná v jiných oblastech, stejně jako událost mřížky.

Nahraďte `<storage_account_name>` jedinečným názvem vašeho účtu úložiště a `<resource_group_name>` skupinou prostředků, kterou jste vytvořili dříve.

```azurecli-interactive
az storage account create \
  --name <storage_account_name> \
  --location westcentralus \
  --resource-group <resource_group_name> \
  --sku Standard_LRS \
  --kind BlobStorage \
  --access-tier Hot
```

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Před přihlášením k odběru tématu vytvoříme koncový bod pro zprávy události. Místo psaní kódu, který by na událost reagoval, vytvoříme koncový bod, který bude shromažďovat zprávy, abyste je mohli zobrazit. Hookbin je nástroj třetí strany, který umožňuje vytvořit koncový bod a zobrazit požadavků, které jsou do ní poslané. Přejděte na [Hookbin](https://hookbin.com/) a klikněte na tlačítko **vytvořit nový koncový bod**.  Zkopírujte adresu URL přihrádky, protože ji budete potřebovat při přihlašování k odběru tématu.

## <a name="subscribe-to-your-storage-account"></a>Přihlásit k účtu úložiště

K odběru tématu se přihlašujete, aby služba Event Grid věděla, které události chcete sledovat. V následujícím příkladu se přihlásí k účtu úložiště, které jste vytvořili a předává adresu URL z Hookbin jako koncový bod pro oznámení o události. Nahraďte `<event_subscription_name>` jedinečným názvem vašeho odběru událostí a `<endpoint_URL>` hodnotou adresy URL z nástroje RequestBin z předchozí části. Díky zadání koncového bodu při přihlašování k odběru bude služba Event Grid zpracovávat směrování událostí do tohoto koncového bodu. Místo `<resource_group_name>` a `<storage_account_name>` použijte hodnoty názvu skupiny prostředků a názvu účtu úložiště, které jste vytvořili dříve.  

```azurecli-interactive
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

## <a name="trigger-an-event-from-blob-storage"></a>Aktivace události ze služby Blob Storage

Nyní aktivujeme událost, abychom viděli, jak služba Event Grid distribuuje zprávu do vašeho koncového bodu. Nejprve umožňuje nakonfigurujte název a klíč pro účet úložiště, pak jsme vytvořit kontejner, pak vytvořit a odeslat soubor. Znovu místo `<storage_account_name>` a `<resource_group_name>` použijte hodnoty názvu účtu úložiště a názvu skupiny prostředků, které jste vytvořili dříve.

```azurecli-interactive
export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY="$(az storage account keys list --account-name <storage_account_name> --resource-group <resource_group_name> --query "[0].value" --output tsv)"

az storage container create --name testcontainer

touch testfile.txt
az storage blob upload --file testfile.txt --container-name testcontainer --name testfile.txt
```

Právě jste aktivovali událost a služba Event Grid odeslala zprávu do koncového bodu, který jste nakonfigurovali při přihlášení k odběru. Přejděte na adresu URL koncového bodu, kterou jste vytvořili dříve. Nebo v otevřeném prohlížeči klikněte na tlačítko pro obnovení. Zobrazí se událost, kterou jste právě odeslali. 

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "text/plain",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/testcontainer/testblob1.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete pokračovat v práci s tímto účtem úložiště a odběru událostí, nevyčišťujte prostředky vytvořené v rámci tohoto článku. Pokud pokračovat nechcete, pomocí následujícího příkazu odstraňte prostředky, které jste v rámci tohoto článku vytvořili.

Nahraďte `<resource_group_name>` názvem skupiny prostředků, kterou jste vytvořili výše.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Další postup

Když teď víte, jak vytvářet témata a odběry událostí, zjistěte více o událostech služby Blob Storage a s čím vám služba Event Grid ještě může pomoct:

- [Reakce na události služby Blob Storage](storage-blob-event-overview.md)
- [Informace o službě Event Grid](../../event-grid/overview.md)
