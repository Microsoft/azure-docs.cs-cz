---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81616834"
---
> [!TIP]
> Po přihlášení se zobrazí seznam předplatných přidružených k vašemu účtu Azure. Informace o předplatném s `isDefault: true` je aktuálně aktivovaným předplatným pro příkazy rozhraní příkazového řádku Azure. Toto předplatné musí být stejné, které obsahuje váš pracovní prostor Azure Machine Learning. ID předplatného můžete najít z [Azure Portal](https://portal.azure.com) navštívíte stránku Přehled pro váš pracovní prostor. Můžete také použít sadu SDK k získání ID předplatného z objektu pracovního prostoru. Například, `Workspace.from_config().subscription_id`.
> 
> Pokud chcete vybrat jiné předplatné, použijte `az account set -s <subscription name or ID>` příkaz a zadejte název nebo ID předplatného, na které chcete přepnout. Další informace o výběru předplatného najdete v tématu [použití více předplatných Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).