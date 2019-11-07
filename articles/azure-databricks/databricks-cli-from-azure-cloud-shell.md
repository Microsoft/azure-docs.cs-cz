---
title: 'Použití rozhraní příkazového řádku Databricks z Azure Cloud Shellu '
description: Naučte se používat rozhraní příkazového řádku datacihly z Azure Cloud Shell k provádění operací s Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: efb0d3222bfd98b15502163979425d47fa459e07
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605718"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Použití rozhraní příkazového řádku Databricks z Azure Cloud Shellu

Naučte se používat rozhraní příkazového řádku datacihly z Azure Cloud Shell k provádění operací s datacihly.

## <a name="prerequisites"></a>Požadavky

* Azure Databricks pracovní prostor a cluster. Pokyny najdete v tématu [Začínáme s Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Nastavení osobního přístupového tokenu v datacihlech Pokyny najdete v tématu [Správa tokenů](/azure/databricks/dev-tools/api/latest/authentication).

## <a name="use-the-azure-cloud-shell"></a>Použití Azure Cloud Shell

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
 
2. V pravém horním rohu klikněte na ikonu **Cloud Shell** .

   ![Spustit Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Spuštění služby Azure Cloud Shell")

3. Ujistěte se, že jste pro Cloud Shell prostředí vybrali možnost **bash** . Můžete vybrat možnost z rozevíracího seznamu, jak je znázorněno na následujícím snímku obrazovky.

   ![Vyberte bash pro prostředí Cloud Shell.](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Vybrat bash") 

4. Vytvořte virtuální prostředí, ve kterém můžete nainstalovat rozhraní příkazového řádku datacihly. V následujícím fragmentu kódu vytvoříte virtuální prostředí s názvem `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Přepněte do virtuálního prostředí, které jste vytvořili.

       source databrickscli/bin/activate

6. Nainstalujte rozhraní příkazového řádku datacihly.

       pip install databricks-cli

7. Nastavte ověřování pomocí datacihly pomocí přístupového tokenu, který jste museli vytvořit, jako součást požadavků. Použijte následující příkaz:

       databricks configure --token

    Zobrazí se následující výzvy:

    * Nejdřív se zobrazí výzva k zadání hostitele datacihly. Zadejte hodnotu ve formátu `https://eastus2.azuredatabricks.net`. Tady je **východní USA 2** oblast Azure, ve které jste vytvořili pracovní prostor Azure Databricks.

    * V dalším kroku se zobrazí výzva k zadání tokenu. Zadejte token, který jste vytvořili dříve.

Po dokončení těchto kroků můžete začít používat rozhraní příkazového řádku datacihly z Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Použití rozhraní příkazového řádku datacihly

Nyní můžete začít používat rozhraní příkazového řádku datacihly. Pokud například chcete zobrazit seznam všech clusterů datacihlů, které máte v pracovním prostoru, spusťte následující příkaz.

    databricks clusters list

Pro přístup k systému souborů datacihly (DBFS) můžete použít také následující příkaz.

    databricks fs ls


Úplný odkaz na příkazy najdete v tématu [datacihly CLI](/azure/databricks/dev-tools/databricks-cli).


## <a name="next-steps"></a>Další kroky

* Další informace o rozhraní příkazového řádku Azure najdete v tématu [Přehled Azure CLI](../cloud-shell/overview.md) .
* Pokud chcete zobrazit seznam příkazů pro Azure CLI, přečtěte si téma Reference k rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest) .
* Seznam příkazů pro rozhraní příkazového řádku datacihly najdete v tématu [datacihly CLI](/azure/databricks/dev-tools/databricks-cli) .


