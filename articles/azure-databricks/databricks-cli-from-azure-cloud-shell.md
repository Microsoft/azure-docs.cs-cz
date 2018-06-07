---
title: Pomocí rozhraní příkazového řádku Databricks z prostředí cloudu Azure | Microsoft Docs
description: Naučte se používat rozhraní příkazového řádku Databricks z prostředí cloudu Azure.
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
ms.openlocfilehash: c20ad02f962fbee22bb16653c5eab351d9f3de17
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598721"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Použití rozhraní příkazového řádku Databricks z Azure Cloud Shellu

Naučte se používat rozhraní příkazového řádku Databricks z prostředí cloudu Azure k provádění operací na Databricks.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor Azure Databricks a cluster. Pokyny najdete v tématu [Začínáme s Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Nastavte osobní přístupový token v Databricks. Pokyny najdete v tématu [Token správu](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="use-the-azure-cloud-shell"></a>Použití prostředí cloudu Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
 
2. V pravém horním rohu klikněte na **cloudové prostředí** ikonu.

   ![Spusťte prostředí cloudu](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "spusťte ODBC z Excelu")

3. Zkontrolujte, zda jste vybrali **Bash** pro cloudové prostředí hodnotit. Můžete vybrat z rozevíracího seznamu možnost, jak je znázorněno na následujícím snímku obrazovky.

   ![Spusťte prostředí cloudu](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "spusťte ODBC z Excelu") 

4. Vytvoření virtuálního prostředí, ve kterém můžete nainstalovat rozhraní příkazového řádku Databtricks. V tomto fragmentu kódu níže, vytvořte virtuální prostředí s názvem `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Umožňuje přepnout do virtuálního prostředí, které jste vytvořili.

       source databrickscli/bin/activate

6. Nainstalujte rozhraní příkazového řádku Databricks.

       pip install databricks-cli

7. Nastavení ověřování s Databricks pomocí přístupového tokenu, který jste vytvořili musí, uvedené v rámci požadavků. Použijte následující příkaz:

       databricks configure --token

    Zobrazí se následující pokynů:

    * Zobrazí se výzva k zadání Databricks hostitele. Ente hodnota ve formátu `https://eastus2.azuredatabricks.net`. Zde **východní USA 2** je oblast Azure, kde jste vytvořili pracovní prostor Azure Databricks.

    * Zobrazí se výzva k zadání uživatelského jména. Zadejte **tokenu**.

    * Nakonec zobrazí se výzva k zadání hesla. Zadejte token, který jste vytvořili dříve.

Po dokončení těchto kroků můžete začít používat rozhraní příkazového řádku Databricks z prostředí cloudu Azure.

## <a name="use-databricks-cli"></a>Pomocí rozhraní příkazového řádku Databricks

Nyní můžete spustit pomocí rozhraní příkazového řádku Databricks. Například spusťte následující příkaz, který zobrazí seznam všech Databricks clustery, které máte v pracovním prostoru.

    databricks clusters list

Můžete také následující příkaz pro přístup k systému souborů Databricks (DBFS).

    databricks fs ls


Úplný referenční příkazy, najdete v části [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).


## <a name="next-steps"></a>Další postup

* Další informace o rozhraní příkazového řádku Azure najdete v tématu [přehled rozhraní příkazového řádku Azure](../cloud-shell/overview.md)
* Pokud chcete zobrazit seznam příkazů pro Azure CLI, najdete v části [referenční dokumentace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* Pokud chcete zobrazit seznam příkazů Databricks CLI, najdete v části [Databricks rozhraní příkazového řádku](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)


