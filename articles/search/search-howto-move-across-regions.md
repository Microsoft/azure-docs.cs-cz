---
title: Postup přesunutí prostředku služby napříč oblastmi
titleSuffix: Azure Cognitive Search
description: Tento článek vám ukáže, jak přesunout prostředky Azure Kognitivní hledání z jedné oblasti do jiné v cloudu Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/10/2020
ms.openlocfilehash: 774b605859df41e0b71ee82c38a6b08bdf5b9c49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91629807"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Přesuňte službu Azure Kognitivní hledání do jiné oblasti Azure.

Zákazníci si občas vyžádají o přesunutí vyhledávací služby do jiné oblasti. V současné době není k dispozici žádný vestavěný mechanismus nebo nástroje, které by mohly s touto úlohou pomáhat, ale tento článek vám může porozumět ručním krokům při dosažení stejného výsledku.

> [!NOTE]
> V Azure Portal všechny služby obsahují příkaz **Exportovat šablonu** . V případě Azure Kognitivní hledání tento příkaz vytvoří základní definici služby (název, umístění, úroveň, repliku a počet oddílů), ale nerozpozná obsah vaší služby, ani nepřenáší klíče, role nebo protokoly. I když tento příkaz existuje, nedoporučujeme ho používat pro přesun vyhledávací služby.

## <a name="prerequisites"></a>Požadavky

+ Ujistěte se, že cílová oblast podporuje služby a funkce, které váš účet využívá.

+ V případě funkcí verze Preview se ujistěte, že je vaše předplatné schválené pro cílovou oblast.

## <a name="prepare-and-move"></a>Příprava a přesun

1. Identifikujte závislosti a související služby, abyste pochopili úplný dopad přemístění služby, pro případ, že budete potřebovat přesunout víc než jenom Azure Kognitivní hledání.

   Azure Storage se používá pro protokolování, vytváření znalostní báze a je běžně používaný externí zdroj dat pro obohacení a indexování AI. Cognitive Services je závislost v rozšíření AI. Pokud používáte rozšíření AI, musí být obě Cognitive Services i vaše vyhledávací služba ve stejné oblasti.

1. Vytvořte inventář všech objektů ve službě, abyste věděli, co přesunout: indexy, mapy synonym, indexery, zdroje dat, dovednosti. Pokud jste povolili protokolování, vytvořte a archivujte všechny sestavy, které můžete potřebovat pro historický záznam.

1. Zkontrolujte ceny a dostupnost v nové oblasti, abyste zajistili dostupnost služby Azure Kognitivní hledání a všech souvisejících služeb v nové oblasti. Většina funkcí je dostupná ve všech oblastech, ale některé funkce ve verzi Preview mají omezenou dostupnost.

1. Vytvořte službu v nové oblasti a znovu publikujte ze zdrojového kódu všechny existující indexy, mapy synonym, indexery, zdroje dat a dovednosti. Mějte na paměti, že názvy služeb musí být jedinečné, takže nebudete moct znovu použít stávající název. Zkontrolujte všechny dovednosti a zjistěte, jestli jsou připojení k Cognitive Services stále platná v souvislosti s požadavkem na stejný region. Pokud používáte jinou službu, Azure Storage ověřte také, jestli jsou úložiště znalostí vytvořená.

1. Znovu načtěte indexy a úložiště znalostí, pokud jsou k dispozici. Pomocí kódu aplikace buď zadáte data JSON do indexu, nebo znovu spusťte indexery pro vyžádání dokumentů z externích zdrojů. 

1. Povolit protokolování a pokud je používáte, znovu vytvořit role zabezpečení.

1. Aktualizujte klientské aplikace a sady testů na použití nového názvu služby a klíčů rozhraní API a otestujte všechny aplikace.

## <a name="discard-or-clean-up"></a>Zahození nebo vyčištění

Po úplném otestování a provozu nové služby odstraňte starou službu. Odstranění služby automaticky odstraní veškerý obsah přidružený ke službě.

## <a name="next-steps"></a>Další kroky

Následující odkazy vám pomůžou najít další informace při dokončení kroků uvedených výše.

+ [Ceny a oblasti Azure Kognitivní hledání](https://azure.microsoft.com/pricing/details/search/)
+ [Volba úrovně](search-sku-tier.md)
+ [Vytvoření vyhledávací služby](search-create-service-portal.md)
+ [Načíst dokumenty pro hledání](search-what-is-data-import.md)
+ [Povolit protokolování](search-monitor-logs.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is whitelisted for the target region. For more information about preview features, see [knowledge stores](./knowledge-store-concept-intro.md), [incremental enrichment](./cognitive-search-incremental-indexing-conceptual.md), and [private endpoint](./service-create-private-endpoint.md).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](../storage/common/storage-account-move.md?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2020-03-13",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](../storage/common/storage-account-move.md?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](./search-get-started-portal.md)

[Create a skillset](./cognitive-search-quickstart-blob.md)

[Create a knowledge store](./knowledge-store-create-portal.md) -->