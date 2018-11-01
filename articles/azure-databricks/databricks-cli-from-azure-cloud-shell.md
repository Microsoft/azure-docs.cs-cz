---
title: 'Použití rozhraní příkazového řádku Databricks z Azure Cloud Shellu '
description: Zjistěte, jak používat rozhraní příkazového řádku Databricks z Azure Cloud Shell.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: dae481fb477223f149404c6a09cad024bc15cd90
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50416689"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Použití rozhraní příkazového řádku Databricks z Azure Cloud Shellu

Zjistěte, jak používat rozhraní příkazového řádku Databricks z Azure Cloud Shell k provádění operací v Databricks.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor Azure Databricks a cluster. Pokyny najdete v tématu [Začínáme s Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Nastavte osobní přístupový token v Databricks. Pokyny najdete v tématu [Token správu](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="use-the-azure-cloud-shell"></a>Pomocí služby Azure Cloud Shell

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
 
2. V pravém horním rohu klikněte **Cloud Shell** ikonu.

   ![Spustit Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "spustit Azure Cloud Shell")

3. Ujistěte se, že vyberete **Bash** pro prostředí Cloud Shell. Můžete vybrat z rozevíracího seznamu možnosti, jak je znázorněno na následujícím snímku obrazovky.

   ![Vyberte Bash pro prostředí Cloud Shellu](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "vyberte Bash") 

4. Vytvoření virtuálního prostředí, ve kterém můžete nainstalovat rozhraní příkazového řádku Databricks. V následujícím fragmentu kódu, můžete vytvořit virtuální prostředí volá `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Přepnout do virtuálního prostředí, které jste vytvořili.

       source databrickscli/bin/activate

6. Instalace rozhraní příkazového řádku Databricks.

       pip install databricks-cli

7. Nastavení ověřování pomocí služby Databricks pomocí přístupového tokenu, který musíte mít vytvořený, uvedený jako součást požadavků. Použijte následující příkaz:

       databricks configure --token

    Zobrazí se tyto výzvy:

    * Nejprve se výzva k zadání hostitele Databricks. Zadejte hodnotu ve formátu `https://eastus2.azuredatabricks.net`. Tady **USA – východ 2** je oblast Azure, ve kterém jste vytvořili pracovní prostor Azure Databricks.

    * V dalším kroku budete vyzváni k zadání tokenu. Zadejte token, který jste vytvořili dříve.

Po dokončení těchto kroků můžete začít používat rozhraní příkazového řádku Databricks z Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Použití rozhraní příkazového řádku Databricks

Teď můžete začít používat rozhraní příkazového řádku Databricks. Například spusťte následující příkaz k výpisu všech clusterech Databricks, které mají ve vašem pracovním prostoru.

    databricks clusters list

Můžete také použít následující příkaz pro přístup k systému souborů Databricks (DBFS).

    databricks fs ls


Úplný popis příkazy pro probuzení, naleznete v tématu [rozhraní příkazového řádku Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).


## <a name="next-steps"></a>Další postup

* Další informace o Azure CLI najdete v tématu [přehled Azure CLI](../cloud-shell/overview.md)
* Seznam příkazů rozhraní příkazového řádku Azure najdete v tématu [referenční informace k Azure CLI](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* Seznam příkazů rozhraní příkazového řádku Databricks najdete v tématu [rozhraní příkazového řádku Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)


