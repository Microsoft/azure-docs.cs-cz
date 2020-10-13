---
title: Registrace poskytovatele prostředků řešení Azure VMware
description: Postup registrace poskytovatele prostředků řešení Azure VMware
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 96d15546c5102a69a0b19f92de33d35d2e9ab6c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575737"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Pokud chcete používat řešení Azure VMware, musíte nejdřív zaregistrovat poskytovatele prostředků s vaším předplatným.

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

>[!TIP]
>Případně můžete k registraci poskytovatele prostředků **Microsoft. AVS** použít grafické uživatelské rozhraní.  Další informace naleznete v článku o [poskytovateli a typech prostředků registrace](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) .  
