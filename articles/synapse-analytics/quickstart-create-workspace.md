---
title: Úvodní příručka – vytvoření pracovního prostoru
description: Vytvořte pracovní prostor Azure Synapse Analytics podle kroků v této příručce.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b6bf4c6a54e2f187a5751d267720f356e0c30ff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424395"
---
# <a name="quickstart-create-an-azure-synapse-analytics-workspace-preview"></a>Úvodní příručka: Vytvoření pracovního prostoru Azure Synapse Analytics (preview)

Tento rychlý start popisuje kroky k vytvoření pracovního prostoru Azure Synapse pomocí portálu Azure.

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

- [Účet úložiště Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlášení k [portálu Azure](https://portal.azure.com/)

## <a name="create-an-azure-synapse-workspace-using-the-azure-portal"></a>Vytvoření pracovního prostoru Azure Synapse pomocí portálu Azure

1. V podokně hledání Microsoft Azure zadejte **pracovní prostor Synapse** a vyberte tuto službu.
![Panel hledání na portálu Azure s pracovními](media/quickstart-create-synapse-workspace/workspace-search.png)prostory Azure Synapse zadanými v. .
2. Na stránce **Synapse workspaces** klikněte na **+ Add**.
![Příkaz k vytvoření nového pracovního prostoru Azure Synapse zvýrazněný. ](media/quickstart-create-synapse-workspace/create-workspace-02.png).
3. Vyplňte formulář **pracovního prostoru Azure Synapse** s následujícími informacemi:

    | Nastavení | Navrhovaná hodnota | Popis |
    | :------ | :-------------- | :---------- |
    | **Předplatné** | *Vaše předplatné* | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
    | **Skupina prostředků** | *Libovolná skupina prostředků* | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
    | **Název pracovního prostoru** | mysampleworkspace | Určuje název pracovního prostoru, který bude použit také pro koncové body připojení.|
    | **Oblasti** | USA – východ 2 | Určuje umístění pracovního prostoru.|
    | **Data Lake Storage Gen2** | Účet:`storage account name` </br> Souborový systém:`root file system to use` | Určuje název účtu úložiště ADLS Gen2, který má být používán jako primární úložiště, a systém souborů, který má být používán.|
    ||||

    ![Tok zřizování pracovního prostoru – karta Základy.](media/quickstart-create-synapse-workspace/create-workspace-03.png).

    Účet úložiště lze vybrat z:
    - Seznam účtů ADLS Gen2 dostupných ve vašem předplatném
    - Ručně zadané pomocí názvu účtu

    > [!IMPORTANT]
    > Pracovní prostor Azure Synapse musí být schopen číst a zapisovat do vybraného účtu ADLS Gen2.
    >
    > Pod výběrovými poli ADLS Gen2 je poznámka, že spravované identitě pracovního prostoru bude přiřazena role **přispěvatele dat objektů blob Storaqe** ve vybraném systému souborů Gen2 úložiště datového jezera, který mu uděluje úplný přístup.

4. (Nepovinné) Upravte některou z výchozích **hodnot zabezpečení + sítě:**
5. (Nepovinné) Na kartě Značky přidejte všechny **značky.**
6. Na kartě **Souhrn** se spustí potřebná ověření, aby bylo zajištěno, že pracovní prostor lze úspěšně vytvořit. Jakmile bude ověření úspěšné, stiskněte **klávesu Vytvořit** ![tok zřizování pracovního prostoru – kartu potvrzení.](media/quickstart-create-synapse-workspace/create-workspace-05.png). .
7. Po úspěšném dokončení procesu zřizování zdrojů se v seznamu pracovních prostorů synapse zobrazí položka vytvořeného pracovního prostoru. ![Výpis pracovních prostorů Synapse zobrazujících nově zřízené pracovní prostory.](media/quickstart-create-synapse-workspace/create-workspace-07.png).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Podle následujících kroků odstraňte pracovní prostor Azure Synapse.
> [!WARNING]
> Odstraněním pracovního prostoru Azure Synapse odeberete analytické moduly a data uložená v databázi obsažených fondů SQL a metadat pracovního prostoru. Už nebude možné se připojit ke koncovým bodům SQL, koncovým bodům Apache Spark. Všechny artefakty kódu budou odstraněny (dotazy, poznámkové bloky, definice úloh a kanály).
>
> Odstranění pracovního prostoru **nebude** mít vliv na data v datovém úložišti jezera Gen2 propojené s pracovním prostorem.

Pokud chcete odstranit pracovní prostor Azure Synapse, proveďte následující kroky:

1. Přejděte do pracovního prostoru Azure Synapse, který chcete odstranit.
1. Stiskněte **klávesu Delete** na panelu příkazů.
 ![Přehled pracovního prostoru Azure Synapse – zvýrazněný příkaz odstranění.](media/quickstart-create-synapse-workspace/create-workspace-10.png)
1. Potvrďte odstranění a stiskněte **tlačítko Odstranit.**
 ![Přehled pracovního prostoru Azure Synapse – dialogové okno pro potvrzení odstranění pracovního prostoru.](media/quickstart-create-synapse-workspace/create-workspace-11.png)
1. Po úspěšném dokončení procesu pracovní prostor Azure Synapse již nebude uveden v seznamu pracovních prostorů.

## <a name="next-steps"></a>Další kroky

Dále můžete [vytvořit fondy SQL](quickstart-create-sql-pool.md) nebo [vytvořit fondy Apache Spark](quickstart-create-apache-spark-pool.md) a začít analyzovat a zkoumat vaše data.
