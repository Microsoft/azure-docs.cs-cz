---
title: Použití rozhraní příkazového řádku Databricks z Azure Cloud Shell | Dokumentace Microsoftu
description: Zjistěte, jak používat rozhraní příkazového řádku Databricks z Azure Cloud Shell.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 3ea4ebbd95237b50054fb0e344f260120d597ab5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225230"
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


