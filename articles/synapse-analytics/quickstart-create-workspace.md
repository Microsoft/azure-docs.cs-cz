---
title: Rychlý Start – vytvoření pracovního prostoru
description: Pomocí kroků v této příručce vytvořte pracovní prostor Azure synapse Analytics.
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 4d3044ec04a3306efdc3b57c1e5a3149b70fab05
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84112867"
---
# <a name="quickstart-create-an-azure-synapse-analytics-workspace-preview"></a>Rychlý Start: vytvoření pracovního prostoru Azure synapse Analytics (Preview)

Tento rychlý Start popisuje kroky pro vytvoření pracovního prostoru Azure synapse pomocí Azure Portal.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

- [Účet úložiště Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [Azure Portal](https://portal.azure.com/)

## <a name="create-an-azure-synapse-workspace-using-the-azure-portal"></a>Vytvoření pracovního prostoru Azure synapse pomocí Azure Portal

1. V podokně hledání Microsoft Azure zadejte **synapse Workspace** a pak vyberte tuto službu.
![Azure Portal panel hledání pomocí pracovních prostorů Azure synapse, které jsou zadané v.](media/quickstart-create-synapse-workspace/workspace-search.png)
2. Na stránce **pracovní prostory synapse** klikněte na **+ Přidat**.
![Příkaz pro vytvoření nového pracovního prostoru Azure synapse je zvýrazněný.](media/quickstart-create-synapse-workspace/create-workspace-02.png)
3. Do formuláře **pracovního prostoru Azure synapse** zadejte následující informace:

    | Nastavení | Navrhovaná hodnota | Description |
    | :------ | :-------------- | :---------- |
    | **Předplatné** | *Vaše předplatné* | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
    | **Skupina prostředků** | *Libovolná skupina prostředků* | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
    | **Název pracovního prostoru** | mysampleworkspace | Určuje název pracovního prostoru, který se bude používat taky pro koncové body připojení.|
    | **Věřitel** | USA – východ 2 | Určuje umístění pracovního prostoru.|
    | **Data Lake Storage Gen2** | Zohledňují`storage account name` </br> Systém souborů:`root file system to use` | Určuje název ADLS Gen2 účtu úložiště, který se má použít jako primární úložiště, a systém souborů, který se má použít.|
    ||||

    ![Karta základy zřizování pracovního prostoru](media/quickstart-create-synapse-workspace/create-workspace-03.png)

    Účet úložiště se dá vybrat z těchto:
    - Seznam ADLS Gen2 účtů dostupných v předplatném
    - Ruční zadání pomocí názvu účtu

    > [!IMPORTANT]
    > Pracovní prostor Azure synapse musí být schopný číst a zapisovat na vybraný ADLS Gen2 účet. Pro libovolný účet úložiště, který propojíte jako primární účet úložiště, je navíc nutné povolit **hierarchický obor názvů** při vytváření účtu úložiště.
    >
    > Pod poli ADLS Gen2 výběru se zobrazí Poznámka s informací o tom, že se spravované identitě pracovního prostoru přiřadí role **Přispěvatel dat objektů BLOB Storaqe** ve vybraném systému souborů Data Lake Storage Gen2 udělení úplného přístupu.

4. Volitelné Změňte kteroukoli kartu **Předvolby zabezpečení a sítě** :
5. Volitelné Přidejte libovolné značky na kartě **značky** .
6. Na kartě **Souhrn** se spustí potřebná ověření, aby se zajistilo, že pracovní prostor bude možné úspěšně vytvořit. Po úspěšném ověření stiskněte tlačítko **vytvořit** ![ tok zřizování pracovního prostoru – karta potvrzení.](media/quickstart-create-synapse-workspace/create-workspace-05.png)
7. Po úspěšném dokončení procesu zřizování prostředků se v seznamu pracovních prostorů synapse zobrazí položka vytvořeného pracovního prostoru. ![Seznam pracovních prostorů synapse zobrazující nově zřízený pracovní prostor.](media/quickstart-create-synapse-workspace/create-workspace-07.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odstranit pracovní prostor Azure synapse, postupujte podle následujících kroků.
> [!WARNING]
> Při odstranění pracovního prostoru Azure synapse se odstraní analytické moduly a data uložená v databázi obsažených fondů SQL a metadat pracovního prostoru. Již nebude možné se připojit k koncovým bodům SQL, Apache Spark koncovým bodům. Všechny artefakty kódu se odstraní (dotazy, poznámkové bloky, definice úloh a kanály).
>
> Odstranění pracovního prostoru nebude **mít vliv** na data v Data Lake Store Gen2, která jsou propojená s pracovním prostorem.

Pokud chcete odstranit pracovní prostor Azure synapse, proveďte následující kroky:

1. Přejděte do pracovního prostoru Azure synapse a odstraňte ho.
1. Na panelu příkazů stiskněte klávesu **Delete** .
 ![Přehled pracovního prostoru Azure synapse – zvýrazní se příkaz pro odstranění.](media/quickstart-create-synapse-workspace/create-workspace-10.png)
1. Potvrďte odstranění a stiskněte tlačítko **Odstranit** .
 ![Azure synapse Workspace – přehled – dialogové okno pro potvrzení odstranění pracovního prostoru](media/quickstart-create-synapse-workspace/create-workspace-11.png)
1. Po úspěšném dokončení procesu již nebude pracovní prostor Azure synapse uveden v seznamu pracovních prostorů.

## <a name="next-steps"></a>Další kroky

V dalším kroku můžete [vytvořit fondy SQL](quickstart-create-sql-pool-studio.md) nebo [vytvořit fondy Apache Spark](quickstart-create-apache-spark-pool-studio.md) , abyste mohli začít analyzovat a prozkoumat data.
