---
title: Postup přesunutí prostředku služby napříč oblastmi
titleSuffix: Azure Cognitive Search
description: Tento článek vám ukáže, jak přesunout prostředky Azure Kognitivní hledání z jedné oblasti do jiné v cloudu Azure.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/18/2020
ms.openlocfilehash: 392c86d8ea24e59d388926d4df581305ea2b531d
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599298"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Přesuňte službu Azure Kognitivní hledání do jiné oblasti Azure.

Pokud chcete přesunout účet služby pro rozpoznávání Azure z jedné oblasti do druhé, vytvoříte šablonu exportu pro přesun vašich předplatných. Po přesunu předplatného budete muset přesunout vaše data a znovu vytvořit službu.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Exportujte šablonu.
> * Úprava šablony: Přidání cílové oblasti, hledání a názvů účtů úložiště.
> * Nasaďte šablonu pro vytvoření nových účtů hledání a úložiště.
> * Ověřte stav služby v nové oblasti.
> * Vyčistěte prostředky ve zdrojové oblasti.

## <a name="prerequisites"></a>Předpoklady

- Zajistěte, aby služby a funkce používané vaším účtem byly podporovány v cílové oblasti.

- V případě funkcí verze Preview se ujistěte, že je vaše předplatné na seznamu povolených pro cílovou oblast. Další informace o funkcích verze Preview najdete v [článcích znalostní báze](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [přírůstkové obohacení](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual)a [soukromý koncový bod](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## <a name="assessment-and-planning"></a>Posouzení a plánování

Když přesunete vyhledávací službu do nové oblasti, budete muset [přesunout data do nové služby úložiště](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) a pak znovu sestavit své indexy, dovednosti a znalostní obchody. Měli byste nahrávat aktuální nastavení a kopírovat soubory JSON, aby bylo možné snadněji a rychleji sestavovat vaše služby.

## <a name="moving-your-search-services-resources"></a>Přesun prostředků služby Search

Pokud chcete začít, exportujte a pak upravte šablonu Správce prostředků.

### <a name="export-a-template"></a>Exportovat šablonu

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).

2. Přejít na stránku skupiny prostředků.

> [!div class="mx-imgBorder"]
> Příklad stránky ![skupiny prostředků](./media/search-move-resource/export-template-sample.png)

3. Vyberte **Všechny prostředky**.

3. V navigační nabídce vlevo vyberte **Exportovat šablonu**.

4. Na stránce **Exportovat šablonu** vyberte **Stáhnout** .

5. Vyhledejte soubor. zip, který jste stáhli z portálu, a rozbalte tento soubor do složky podle vašeho výběru.

Soubor zip obsahuje soubory. JSON, které tvoří šablonu a skripty pro nasazení šablony.

### <a name="modify-the-template"></a>Úprava šablony

Šablonu upravíte změnou názvů a oblastí hledání a účtu úložiště. Názvy musí splňovat pravidla pro všechny zásady vytváření názvů služeb a oblastí. 

Pokud chcete získat kódy umístění oblastí, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kód oblasti je název oblasti bez mezer **Střed USA** = **centralus**.

1. Na webu Azure Portal vyberte **Vytvořit prostředek**.

2. Do pole **Hledat na Marketplace** zadejte **template deployment** a stiskněte **ENTER**.

3. Vyberte **Template deployment**.

4. Vyberte **Vytvořit**.

5. Vyberte **Vytvořit vlastní šablonu v editoru**.

6. Vyberte **načíst soubor**a potom podle pokynů načtěte soubor **template. JSON** , který jste stáhli a rozčetli v předchozí části.

7. V souboru **template. JSON** pojmenujte cílové vyhledávání a účty úložiště nastavením výchozí hodnoty názvů hledání a účtu úložiště. 

8. Upravte vlastnost **umístění** v souboru **template. JSON** do cílové oblasti pro službu Search a službu úložiště. Tento příklad nastaví cílovou oblast na `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2015-08-19",
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

### <a name="deploy-the-template"></a>Nasazení šablony

1. Uložte soubor **template. JSON** .

2. Zadejte nebo vyberte hodnoty vlastností:

- **Předplatné:** Vyberte předplatné Azure.

- **Skupina prostředků**: Vyberte **Vytvořit novou** a zadejte název nové skupiny prostředků.

- **Umístění**: vyberte umístění Azure.

3. Klikněte na **Souhlasím s výše uvedenými podmínkami a ujednáními** a pak klikněte na tlačítko **Vybrat nákup** .

## <a name="verifying-your-services-status-in-new-region"></a>Ověřování stavu služeb v nové oblasti

Pokud chcete tento přesun ověřit, otevřete novou skupinu prostředků a vaše služby se zobrazí v seznamu s novou oblastí.

Pokud chcete přesunout data ze zdrojové oblasti do cílové oblasti, přečtěte si pokyny v tomto článku pro [přesun dat do nového účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## <a name="clean-up-resources-in-your-original-region"></a>Vyčištění prostředků v původní oblasti

Pokud chcete potvrdit změny a dokončit přesunutí účtu služby, odstraňte účet zdrojové služby.

## <a name="next-steps"></a>Další kroky

[Vytvoření indexu](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Vytvoření dovednosti](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Vytvoření úložiště znalostí](https://docs.microsoft.com/azure/search/knowledge-store-create-portal)

