---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 5e0cff7bde6e80a776d694820ca7b69dafa7c0d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83648818"
---
Přístup k soukromému webu znamená, že vaše aplikace bude přístupná jenom z privátní sítě, jako je třeba virtuální síť Azure.

* Přístup k privátní lokalitě je k dispozici v plánech [Premium](../articles/azure-functions/functions-premium-plan.md), [Spotřeba](../articles/azure-functions/functions-scale.md#consumption-plan)a [App Service](../articles/azure-functions/functions-scale.md#app-service-plan) , když jsou nakonfigurované koncové body služby.
    * Koncové body služby je možné konfigurovat pro jednotlivé aplikace v části **funkce platformy**  >  **sítě**  >  **Konfigurace omezení přístupu**  >  **Přidat pravidlo**. Virtuální sítě se teď dají vybrat jako typ pravidla.
    * Další informace najdete v tématu [koncové body služby virtuální sítě](../articles/virtual-network/virtual-network-service-endpoints-overview.md).
    * Mějte na paměti, že u koncových bodů služby má vaše funkce stále plný odchozí přístup k Internetu, a to i s nakonfigurovanou integrací virtuální sítě.
* Přístup k privátní lokalitě je také k dispozici v rámci App Service Environment, který je nakonfigurován s interním nástrojem pro vyrovnávání zatížení (interního nástroje). Další informace najdete v tématu [Vytvoření a použití interního nástroje pro vyrovnávání zatížení s App Service Environment](../articles/app-service/environment/create-ilb-ase.md).

Informace o tom, jak nastavit přístup k privátní lokalitě, najdete v tématu věnovaném [vytvoření Azure Functions přístupu k privátní lokalitě](../articles/azure-functions/functions-create-private-site-access.md).