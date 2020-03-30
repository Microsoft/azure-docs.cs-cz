---
title: 'Použití rozhraní příkazového řádku Databricks z Azure Cloud Shellu '
description: Zjistěte, jak používat cli datového zonace Databricks z Azure Cloud Shell u provádění operací s Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: efb0d3222bfd98b15502163979425d47fa459e07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73605718"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Použití rozhraní příkazového řádku Databricks z Azure Cloud Shellu

Zjistěte, jak používat příkazové příkazové příkazy Databricks z Prostředí Azure Cloud Shell k provádění operací s datovými cihlami.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor a cluster Azure Databricks. Pokyny najdete [v tématu Začínáme s Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Nastavte osobní přístupový token v Databricks. Pokyny naleznete v tématu [Správa tokenů](/azure/databricks/dev-tools/api/latest/authentication).

## <a name="use-the-azure-cloud-shell"></a>Použití cloudového prostředí Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
 
2. V pravém horním rohu klikněte na ikonu **Cloud Shell.**

   ![Spuštění prostředí Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Spuštění služby Azure Cloud Shell")

3. Ujistěte se, že jste vybrali **Bash** pro prostředí Cloud Shell. Můžete si vybrat z rozbalovací možnosti, jak je znázorněno na následujícím snímku obrazovky.

   ![Výběr bash pro prostředí Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Vybrat Bash") 

4. Vytvořte virtuální prostředí, ve kterém můžete nainstalovat databricks CLI. Ve fragmentu níže vytvoříte virtuální prostředí `databrickscli`s názvem .

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Přepněte do virtuálního prostředí, které jste vytvořili.

       source databrickscli/bin/activate

6. Nainstalujte cli datového zpozíka.Install the Databricks CLI.

       pip install databricks-cli

7. Nastavte ověřování pomocí Databricks pomocí přístupového tokenu, který musíte vytvořit, uvedené jako součást předpokladů. Použijte následující příkaz:

       databricks configure --token

    Zobrazí se následující výzvy:

    * Nejprve budete vyzváni k zadání databricks hostitele. Zadejte hodnotu `https://eastus2.azuredatabricks.net`ve formátu . Tady je **Východní US 2** oblast Azure, kde jste vytvořili pracovní prostor Azure Databricks.

    * Dále budete vyzváni k zadání tokenu. Zadejte token, který jste vytvořili dříve.

Po dokončení těchto kroků můžete začít používat Databricks CLI z Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Použití cli datových cihel

Nyní můžete začít používat databricks CLI. Spusťte například následující příkaz a seznam všech clusterů Databricks, které máte v pracovním prostoru.

    databricks clusters list

Můžete také použít následující příkaz pro přístup k souborovésoustavy Databricks (DBFS).

    databricks fs ls


Úplný odkaz na příkazy naleznete v tématu [Databricks CLI](/azure/databricks/dev-tools/databricks-cli).


## <a name="next-steps"></a>Další kroky

* Další informace o Azure CLI najdete v [tématu Přehled k onomu azure cli](../cloud-shell/overview.md)
* Seznam příkazů pro Azure CLI najdete v tématu [odkaz na azure cli](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* Seznam příkazů pro příkazcli Datbricks naleznete v tématu [Databricks CLI](/azure/databricks/dev-tools/databricks-cli)


