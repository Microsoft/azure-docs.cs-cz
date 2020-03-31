---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: df4735699f71256598bfd418a505ae8d3e5b816c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296881"
---
Po přihlášení se zobrazí seznam předplatných přidružených k vašemu účtu Azure. Informace o `isDefault: true` předplatném s je aktuálně aktivované předplatné pro příkazy Azure CLI. Toto předplatné musí být stejné, které obsahuje pracovní prostor Azure Machine Learning. ID předplatného najdete na [webu Azure Portal](https://portal.azure.com) na stránce s přehledem pro váš pracovní prostor. Pomocí sady SDK můžete také získat ID předplatného z objektu pracovního prostoru. Například, `Workspace.from_config().subscription_id`.
    
Chcete-li vybrat jiné předplatné, použijte příkaz [az účet nastavit](https://docs.microsoft.com/cli/azure/account#az-account-set) s ID předplatného přepnout na. Další informace o výběru předplatného najdete v tématu [Použití více předplatných Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).