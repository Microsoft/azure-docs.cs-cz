---
title: Registrace poskytovatele prostředků řešení Azure VMware
description: Postup registrace poskytovatele prostředků řešení Azure VMware
ms.topic: include
ms.date: 09/04/2020
ms.openlocfilehash: 08741181b778b44289868a9f3fb1c2368955248c
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512354"
---
Pokud chcete používat řešení Azure VMware, musíte nejdřív zaregistrovat poskytovatele prostředků s vaším předplatným.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Další způsoby registrace poskytovatele prostředků najdete v tématu [poskytovatelé a typy prostředků Azure](../../azure-resource-manager/management/resource-providers-and-types.md).