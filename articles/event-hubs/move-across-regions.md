---
title: Přesunutí oboru názvů Centra událostí Azure do jiné oblasti | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak přesunout obor názvů Centra událostí Azure z aktuální oblasti do jiné oblasti.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 04/14/2020
ms.author: spelluru
ms.reviewer: shvija
ms.openlocfilehash: 2dfc9c517605bbb48bee0b306fb275464cfebe39
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606805"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Přesunutí oboru názvů Centra událostí Azure do jiné oblasti
Existují různé scénáře, ve kterých byste chtěli přesunout existující obor názvů Event Hubs z jedné oblasti do druhé. Můžete například vytvořit obor názvů se stejnou konfigurací pro testování. Můžete také vytvořit sekundární obor názvů v jiné oblasti jako součást [plánování zotavení po havárii](event-hubs-geo-dr.md#setup-and-failover-flow).

> [!NOTE]
> Tento článek ukazuje, jak exportovat šablonu Azure Resource Manager pro existující obor názvů Event Hubs a potom pomocí šablony vytvořit obor názvů se stejným nastavením konfigurace v jiné oblasti. Tento proces však nepřesune události, které ještě nejsou zpracovány. Před odstraněním je třeba zpracovat události z původního oboru názvů.

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že služby a funkce, které váš účet používá, jsou podporovány v cílové oblasti.
- U funkcí náhledu se ujistěte, že vaše předplatné je na seznamu povolených pro cílovou oblast.
- Pokud máte **povolenou funkci pro zachycení** pro centra událostí v oboru názvů, přesuňte azure storage nebo Azure Data Lake Store Gen [2](../storage/common/storage-account-move.md) nebo Azure Data Lake Store [Gen 1](../data-lake-store/data-lake-store-migration-cross-region.md) před přesunutím oboru názvů Event Hubs. Skupinu prostředků, která obsahuje obory názvů Služby úložiště a centra událostí, můžete také přesunout do jiné oblasti podle následujících kroků podobných těm, které jsou popsány v tomto článku. 
- Pokud je obor názvů Event Hubs v **clusteru Event Hubs**, vytvořte před procházením kroků v tomto článku [vyhrazený cluster](event-hubs-dedicated-cluster-create-portal.md) v **cílové oblasti.** 

## <a name="prepare"></a>Příprava
Chcete-li začít, exportujte šablonu Správce prostředků. Tato šablona obsahuje nastavení, která popisují obor názvů Centra událostí.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyberte **Všechny prostředky** a pak vyberte obor názvů Centra událostí.

3. Vyberte > **nastavení** > **exportovat šablonu**.

4. Na stránce **exportovat šablonu** zvolte **Stáhnout.**

    ![Stáhnout šablonu Správce prostředků](./media/move-across-regions/download-template.png)

5. Vyhledejte soubor ZIP, který jste stáhli z portálu, a rozbalte jej do složky podle vašeho výběru.

   Tento soubor zip obsahuje soubory JSON, které obsahují šablonu a skripty pro nasazení šablony.


## <a name="move"></a>Přesunout

Nasadte šablonu a vytvořte obor názvů Event Hubs v cílové oblasti. 


1. Na webu Azure Portal vyberte **Vytvořit prostředek**.

2. Do pole **Hledat na Marketplace** zadejte **template deployment** a stiskněte **ENTER**.

3. Vyberte **Template deployment**.

4. Vyberte **Vytvořit**.

5. Vyberte **Vytvořit vlastní šablonu v editoru**.

6. Vyberte **Načíst soubor**a podle pokynů načtěte soubor **template.json,** který jste stáhli v poslední části.

7. Vyberte **Uložit,** chcete-li šablonu uložit. 

8. Na stránce **Vlastní nasazení** postupujte takto: 

    1. Vyberte **předplatné**Azure . 

    2. Vyberte existující **skupinu prostředků** nebo ji vytvořte. Pokud byl zdrojový obor názvů v clusteru Event Hubs, vyberte skupinu prostředků, která obsahuje cluster v cílové oblasti. 

    3. Vyberte cílové **umístění** nebo oblast. Pokud jste vybrali existující skupinu prostředků, bude toto nastavení jen pro čtení. 

    4. V části **NASTAVENÍ** postupujte takto:
    
        1. zadejte nový **název oboru názvů**. 

            ![Nasazení šablony Správce prostředků](./media/move-across-regions/deploy-template.png)

        2. Pokud byl zdrojový obor názvů v **clusteru Event Hubs**, zadejte názvy **skupin y prostředků** a **clusteru Event Hubs** jako součást **externího ID**. 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. Pokud centrum událostí ve vašem oboru názvů používá účet úložiště pro zachycení událostí, `StorageAccounts_<original storage account name>_external` zadejte název skupiny prostředků a účet úložiště pro pole. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. Zaškrtněte políčko **Souhlasím s výše uvedenými podmínkami.** 
    
    6. Nyní vyberte **Vybrat nákup** a spusťte proces nasazení. 

## <a name="discard-or-clean-up"></a>Zlikvidovat nebo vyčistit
Po nasazení, pokud chcete začít znovu, můžete odstranit **cílový obor názvů Event Hubs**a zopakovat kroky popsané v části [Příprava](#prepare) a [přesunutí](#move) v tomto článku.

Chcete-li potvrdit změny a dokončit přesun oboru názvů Event Hubs, odstraňte **zdrojový obor názvů Event Hubs**. Před odstraněním oboru názvů zkontrolujte, zda jste zpracovali všechny události v oboru názvů. 

Odstranění oboru názvů Event Hubs (zdroj nebo cíl) pomocí portálu Azure:

1. V okně hledání v horní části portálu Azure zadejte **Centra událostí**a z výsledků hledání vyberte **Centra událostí.** V seznamu se zobrazí obory názvů Centra událostí.

2. Vyberte cílový obor názvů, který chcete odstranit, a na panelu nástrojů vyberte **Odstranit.** 

    ![Odstranit obor názvů - tlačítko](./media/move-across-regions/delete-namespace-button.png)

3. Na stránce **Odstranit zdroje*** ověřte vybrané prostředky a potvrďte odstranění zadáním **ano**a pak vyberte **Odstranit**. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli obor názvů Centra událostí Azure z jedné oblasti do druhé a vyčistili zdrojové prostředky.  Další informace o přesunu prostředků mezi oblastmi a zotavení po havárii v Azure najdete v tématu:


- [Přesun prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Přesun virtuálních počítačů Azure do jiné oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
