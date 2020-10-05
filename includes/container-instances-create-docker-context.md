---
title: zahrnout soubor
description: zahrnout soubor
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 3221c4f3e196cf1573bd7c0424fa3b4530c0b2ca
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90709654"
---
## <a name="create-azure-context"></a>Vytvořit kontext Azure

Pokud chcete použít příkazy Docker ke spuštění kontejnerů v Azure Container Instances, nejdřív se přihlaste do Azure:

```bash
docker login azure
```

Po zobrazení výzvy zadejte nebo vyberte svoje přihlašovací údaje Azure.


Vytvořte ACI kontext spuštěním `docker context create aci` . Tento kontext přidruží Docker k předplatnému Azure a skupině prostředků, abyste mohli vytvářet a spravovat instance kontejnerů. Například pro vytvoření kontextu s názvem *myacicontext*:

```
docker context create aci myacicontext
```

Po zobrazení výzvy vyberte ID vašeho předplatného Azure a pak vyberte existující skupinu prostředků nebo **vytvořte novou skupinu prostředků**. Pokud zvolíte novou skupinu prostředků, vytvoří se se systémem generovaným názvem. Instance kontejnerů Azure, stejně jako všechny prostředky Azure, se musí nasadit do skupiny prostředků. Skupiny prostředků vám umožňují organizaci a správu souvisejících prostředků Azure.


Spusťte `docker context ls` a potvrďte, že jste přidali kontext ACI do kontextů Docker:

```
docker context ls
```