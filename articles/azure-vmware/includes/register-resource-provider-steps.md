---
title: Registrace poskytovatele prostředků řešení Azure VMware
description: Postup registrace poskytovatele prostředků řešení Azure VMware
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 380cb513ec389293db757e667ed4681778e29348
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254621"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Pokud chcete používat řešení Azure VMware, musíte nejdřív zaregistrovat poskytovatele prostředků s vaším předplatným.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

>[!TIP]
>Případně můžete k registraci poskytovatele prostředků **Microsoft. AVS** použít grafické uživatelské rozhraní.  Další informace naleznete v článku o [poskytovateli a typech prostředků registrace](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) .  
