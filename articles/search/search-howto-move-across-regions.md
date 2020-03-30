---
title: Jak přesunout prostředek služby napříč oblastmi
titleSuffix: Azure Cognitive Search
description: Tento článek vám ukáže, jak přesunout prostředky Azure Cognitive Search z jedné oblasti do druhé v cloudu Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 03/24/2020
ms.openlocfilehash: 00f16d11f7a9cd276772eda5e91d6e117ada8c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246297"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Přesunutí služby Azure Cognitive Search do jiné oblasti Azure

V některých případě se zákazníci ptají na přesunutí vyhledávací služby do jiné oblasti. V současné době neexistuje žádný vestavěný mechanismus nebo nástroje, které by vám pomohly s tímto úkolem, ale tento článek vám může pomoci pochopit ruční kroky pro dosažení stejného výsledku.

> [!NOTE]
> Na webu Azure Portal mají všechny služby příkaz **Exportovat šablonu.** V případě Azure Cognitive Search tento příkaz vytváří základní definici služby (název, umístění, vrstva, replika a počet oddílů), ale nerozpozná obsah vaší služby, ani nepřenáší klíče, role nebo protokoly. Přestože příkaz existuje, nedoporučujeme jej používat pro přesunutí vyhledávací služby.

## <a name="guidance-for-moving-a-service"></a>Pokyny pro přesun služby

1. Identifikujte závislosti a související služby, abyste pochopili úplný dopad přemístění služby v případě, že potřebujete přesunout víc než jen Azure Cognitive Search.

   Azure Storage se používá pro protokolování, vytváření úložiště znalostí a je běžně používaný externí zdroj dat pro obohacení a indexování AI. Cognitive Services je závislost v obohacení AI. Kognitivní služby a vyhledávací služba musí být ve stejné oblasti, pokud používáte obohacení AI.

1. Vytvořte soupis všech objektů ve službě, abyste věděli, co přesunout: indexy, mapy synonym, indexery, zdroje dat, dovednosti. Pokud jste povolili protokolování, vytvořte a archivujte všechny sestavy, které budete potřebovat pro historický záznam.

1. Zkontrolujte ceny a dostupnost v nové oblasti, abyste zajistili dostupnost Azure Cognitive Search a všech souvisejících služeb v nové oblasti. Většina funkcí je k dispozici ve všech oblastech, ale některé funkce náhledu mají omezenou dostupnost.

1. Vytvořte službu v nové oblasti a znovu publikovat ze zdrojového kódu všechny existující indexy, synonyma mapy, indexery, zdroje dat a dovednosti. Nezapomeňte, že názvy služeb musí být jedinečné, takže existující název nelze znovu použít. Zkontrolujte každou sadu dovedností a zjistěte, zda jsou připojení ke službám Cognitive Services stále platná z hlediska požadavku na stejnou oblast. Pokud se také vytvoří úložiště znalostí, zkontrolujte připojovací řetězce pro Azure Storage, pokud používáte jinou službu.

1. Znovu načtěte indexy a úložiště znalostí, pokud je to možné. Budete buď použít kód aplikace k nabízení dat JSON do indexu, nebo znovu spusťte indexery pro vyžádat dokumenty z externích zdrojů. 

1. Povolte protokolování a pokud je používáte, znovu vytvořte role zabezpečení.

1. Aktualizujte klientské aplikace a testovací sady tak, aby používaly nový název služby a klíče rozhraní API, a otestujte všechny aplikace.

1. Odstraňte starou službu, jakmile je nová služba plně testována a funkční.

## <a name="next-steps"></a>Další kroky

Následující odkazy vám mohou pomoci najít další informace při dokončení výše uvedených kroků.

+ [Ceny a oblasti Azure cognitive search](https://azure.microsoft.com/pricing/details/search/)
+ [Volba úrovně](search-sku-tier.md)
+ [Vytvoření vyhledávací služby](search-create-service-portal.md)
+ [Načtení vyhledávacích dokumentů](search-what-is-data-import.md)
+ [Povolení protokolování](search-monitor-logs.md)


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

- For preview features, ensure that your subscription is whitelisted for the target region. For more information about preview features, see [knowledge stores](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [incremental enrichment](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual), and [private endpoint](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

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

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Create a skillset](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Create a knowledge store](https://docs.microsoft.com/azure/search/knowledge-store-create-portal) -->