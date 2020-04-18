---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616834"
---
> [!TIP]
> Po přihlášení se zobrazí seznam předplatných přidružených k vašemu účtu Azure. Informace o `isDefault: true` předplatném s je aktuálně aktivované předplatné pro příkazy Azure CLI. Toto předplatné musí být stejné, které obsahuje pracovní prostor Azure Machine Learning. ID předplatného najdete na [webu Azure Portal](https://portal.azure.com) na stránce s přehledem pro váš pracovní prostor. Pomocí sady SDK můžete také získat ID předplatného z objektu pracovního prostoru. Například, `Workspace.from_config().subscription_id`.
> 
> Chcete-li vybrat jiné `az account set -s <subscription name or ID>` předplatné, použijte příkaz a zadejte název předplatného nebo ID, na které chcete přepnout. Další informace o výběru předplatného najdete v tématu [Použití více předplatných Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).