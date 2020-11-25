---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 6147dff71ff8c93c8c46b5921b37db459ea73e4a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027074"
---
> [!TIP]
> Po přihlášení se zobrazí seznam předplatných přidružených k vašemu účtu Azure. Informace o předplatném s `isDefault: true` je aktuálně aktivovaným předplatným pro příkazy rozhraní příkazového řádku Azure. Toto předplatné musí být stejné, které obsahuje váš pracovní prostor Azure Machine Learning. ID předplatného můžete najít z [Azure Portal](https://portal.azure.com) navštívíte stránku Přehled pro váš pracovní prostor. Můžete také použít sadu SDK k získání ID předplatného z objektu pracovního prostoru. Například, `Workspace.from_config().subscription_id`.
> 
> Pokud chcete vybrat jiné předplatné, použijte `az account set -s <subscription name or ID>` příkaz a zadejte název nebo ID předplatného, na které chcete přepnout. Další informace o výběru předplatného najdete v tématu [použití více předplatných Azure](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).