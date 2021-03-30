---
title: Přesunout Azure Event Grid systémová témata do jiné oblasti
description: V tomto článku se dozvíte, jak přesunout Azure Event Grid systémová témata z jedné oblasti do jiné oblasti.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: eb6029b206e7d47789371ee81e75c4e05c69ee65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89084530"
---
# <a name="move-azure-event-grid-system-topics-to-another-region"></a>Přesunout Azure Event Grid systémová témata do jiné oblasti
Prostředky můžete chtít přesunout do jiné oblasti z řady důvodů. Například pro využití nové oblasti Azure, abyste splnili požadavky na interní zásady a požadavky zásad správného řízení nebo v reakci na požadavky na plánování kapacity. 

Tady je postup vysoké úrovně, který je popsaný v tomto článku: 

- **Exportujte skupinu prostředků** , která obsahuje Azure Storage účet a související systémové téma, do šablony Správce prostředků. Můžete také exportovat šablonu pouze pro systémové téma. Pokud přejdete do této trasy, nezapomeňte před přesunutím systémového tématu přesunout zdroj události Azure (v tomto příkladu účet Azure Storage) do jiné oblasti. Potom v části vyexportovaná šablona pro systém aktualizujte externí ID účtu úložiště v cílové oblasti. 
- **Upravte šablonu** a přidejte `endpointUrl` vlastnost, která bude ukazovat na Webhook, který se přihlásí k odběru systému. Když je vyexportováno systémové téma, jeho předplatné (v tomto případě je Webhook) je také exportováno do šablony, ale `endpointUrl` vlastnost není součástí. Takže ho budete muset aktualizovat tak, aby odkazoval na koncový bod, který se přihlašuje k odběru tématu. Také aktualizujte hodnotu `location` vlastnosti na nové umístění nebo oblast. Pro jiné typy obslužných rutin událostí musíte aktualizovat pouze umístění. 
- **Použijte šablonu k nasazení prostředků** do cílové oblasti. Zadáte názvy pro účet úložiště a systémové téma, které se má vytvořit v cílové oblasti. 
- **Ověřte nasazení**. Ověřte, jestli je Webhook vyvolaný při nahrávání souboru do úložiště objektů BLOB v cílové oblasti. 
- Chcete-li **Dokončit přesunutí**, odstraňte prostředky (zdrojové události a systémové téma) ze zdrojové oblasti. 

## <a name="prerequisites"></a>Požadavky
- Dokončete [rychlé zprovoznění: směrování událostí služby Blob Storage do webového koncového bodu pomocí Azure Portal](blob-event-quickstart-portal.md) ve zdrojové oblasti. Tento krok je **nepovinný**. Proveďte test kroků v tomto článku. Ponechte účet úložiště v samostatné skupině prostředků z plánu App Service a App Service. 
- Ujistěte se, že je služba Event Grid v cílové oblasti dostupná. Zobrazit [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Příprava
Začněte tím, že vyexportujete šablonu Správce prostředků pro skupinu prostředků, která obsahuje zdroj systémové události (Azure Storage účet) a její přidružené systémové téma. 

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V nabídce vlevo vyberte **skupiny prostředků** . Pak vyberte skupinu prostředků, která obsahuje zdroj události, pro který se vytvořilo systémové téma. V následujícím příkladu je to účet **Azure Storage** . Skupina prostředků obsahuje účet úložiště a jeho přidružené systémové téma. 

    :::image type="content" source="./media/move-system-topics-across-regions/resource-group-page.png" alt-text="Stránka skupiny prostředků":::        
3. V nabídce vlevo vyberte v části **Nastavení** položku **Exportovat šablonu** a pak na panelu nástrojů vyberte **Stáhnout** . 

    :::image type="content" source="./media/move-system-topics-across-regions/export-template-menu.png" alt-text="Nákladnost účet – exportovat stránku šablony":::        
5. Vyhledejte soubor **. zip** , který jste stáhli z portálu, a rozbalte tento soubor do složky podle vašeho výběru. Tento soubor zip obsahuje šablony a parametry soubory JSON. 
1. Otevřete **template.js** v editoru podle svého výběru. 
1. Adresa URL Webhooku není exportována do šablony. Provedete to tak, že provedete následující kroky:
    1. V souboru šablony vyhledejte **Webhook**. 
    1. V části **Properties (vlastnosti** `,` ) na konci posledního řádku přidejte znak čárky (). V tomto příkladu je to `"preferredBatchSizeInKilobytes": 64` . 
    1. Přidejte `endpointUrl` vlastnost s hodnotou nastavenou na adresu URL Webhooku, jak je znázorněno v následujícím příkladu. 

        ```json
        "destination": {
            "properties": {
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64,
                "endpointUrl": "https://mysite.azurewebsites.net/api/updates"
            },
            "endpointType": "WebHook"
        }
        ```

        > [!NOTE]
        > Pro jiné typy obslužných rutin událostí jsou všechny vlastnosti exportovány do šablony. Stačí aktualizovat `location` vlastnost pouze na cílovou oblast, jak je znázorněno v následujícím kroku. 
7. Aktualizujte `location` prostředek **účtu úložiště** na cílovou oblast nebo umístění. Pokud chcete získat kódy umístění, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kód oblasti je název oblasti bez mezer, například `West US` je rovno `westus` .

    ```json
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccounts_spegridstorage080420_name')]",
    "location": "westus",
    ```
8. Opakujte krok, abyste aktualizovali `location` prostředek **tématu systému** v šabloně. 

    ```json
    "type": "Microsoft.EventGrid/systemTopics",
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('systemTopics_spegridsystopic080420_name')]",
    "location": "westus",
    ```
1. **Uložte** šablonu. 

## <a name="recreate"></a>Znovu vytvořit 
Nasaďte šablonu k vytvoření účtu úložiště a systémového tématu pro účet úložiště v cílové oblasti. 

1. V Azure Portal vyberte **vytvořit prostředek**.
2. V **části Hledat na Marketplace** zadejte **šablonu Deployment** a potom stiskněte **ENTER**.
3. Vyberte **template Deployment**.
4. Vyberte **Vytvořit**.
5. **V editoru vyberte vytvořit vlastní šablonu**.
6. Vyberte **načíst soubor** a potom podle pokynů načtěte **template.js** do souboru, který jste stáhli v poslední části.
7. Vyberte **Uložit** a šablonu uložte. 
8. Na stránce **vlastní nasazení** postupujte podle těchto kroků. 
    1. Vyberte **předplatné** Azure. 
    1. Vyberte existující **skupinu prostředků** v cílové oblasti nebo ji vytvořte. 
    1. V poli **oblast** vyberte cílovou oblast. Pokud jste vybrali existující skupinu prostředků, toto nastavení je jen pro čtení.
    1. Do pole **Název systémového tématu** zadejte název systémového tématu, které bude přidruženo k účtu úložiště.  
    1. Jako **název účtu úložiště** zadejte název účtu úložiště, který se má vytvořit v cílové oblasti. 

        :::image type="content" source="./media/move-system-topics-across-regions/deploy-template.png" alt-text="Nasazení šablony Správce prostředků":::
    5. V dolní části stránky vyberte **zkontrolovat + vytvořit** . 
    1. Na stránce **Revize + vytvořit** zkontrolujte nastavení a vyberte **vytvořit**. 

## <a name="verify"></a>Ověření
1. Po úspěšném nasazení vyberte Přejít na **skupinu prostředků**. 
1. Na stránce **Skupina prostředků** ověřte, že se vytvořil zdroj události (v tomto příkladu Azure Storage účet) a systémové téma. 
1. Nahrajte soubor do kontejneru v úložišti objektů BLOB v Azure a ověřte, jestli Webhook přijal událost. Další informace najdete v tématu [odeslání události do koncového bodu](blob-event-quickstart-portal.md#send-an-event-to-your-endpoint).

## <a name="discard-or-clean-up"></a>Zahození nebo vyčištění
Pokud chcete dokončit přesunutí, odstraňte skupinu prostředků, která obsahuje účet úložiště a příslušné systémové téma ve zdrojové oblasti.  

Pokud chcete začít znovu, odstraňte skupinu prostředků v cílové oblasti a opakujte kroky v částech [Příprava](#prepare) a [opětovné vytvoření](#recreate) v tomto článku.

Odstranění skupiny prostředků (zdroje nebo cíle) pomocí Azure Portal:

1. V okně hledání v horní části Azure Portal zadejte **skupiny prostředků** a z výsledků hledání vyberte **skupiny prostředků** . 
2. Vyberte skupinu prostředků, kterou chcete odstranit, a vyberte **Odstranit** z panelu nástrojů. 

    :::image type="content" source="./media/move-system-topics-across-regions/delete-resource-group-button.png" alt-text="Odstranění skupiny prostředků":::
3. Na stránce potvrzení zadejte název skupiny prostředků a vyberte **Odstranit**.  

## <a name="next-steps"></a>Další kroky
Zjistili jste, jak přesunout zdroj události Azure a jeho přidružené systémové téma z jedné oblasti do jiné oblasti. Přečtěte si následující články, které vám posouvají vlastní témata, domény a obory názvů partnerů v různých oblastech.

- [Přesuňte vlastní témata napříč oblastmi](move-custom-topics-across-regions.md). 
- [Přesune domény mezi oblasti](move-domains-across-regions.md). 
- [Přesuňte obory názvů partnerů mezi oblasti](move-partner-namespaces-across-regions.md). 

Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete v následujícím článku: [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).
