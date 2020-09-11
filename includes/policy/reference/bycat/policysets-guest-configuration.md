---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 70503b8914310d205582e93e92ed7c51dc7c54d2
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90022458"
---
|Název |Popis |Zásady |Verze |
|---|---|---|---|
|[Auditovat počítače s nezabezpečeným nastavením zabezpečení hesla](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |Tento iniciativa nasadí požadavky zásad a Audituje počítače s nezabezpečeným nastavením zabezpečení hesla. Pokud chcete získat další informace o zásadách konfigurace hostů, navštivte prosím. [https://aka.ms/gcpol](https://aka.ms/gcpol) |9 |1.0.0 |
|[Nasazení požadavků pro povolení zásad konfigurace hostů na virtuálních počítačích](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |V této iniciativě se přidá spravovaná identita přiřazená systémem a nasadí se pro virtuální počítače, které mají nárok na monitorování pomocí zásad konfigurace hosta, nasadí příslušné rozhraní konfigurace hosta platformy. Je to předpoklad pro všechny zásady konfigurace hostů a před použitím zásad konfigurace hosta musí být přiřazený k oboru přiřazení zásad. Další informace o konfiguraci hosta najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . |4 |1.0.0 – Preview |
|[Počítače s Windows by měly splňovat požadavky na základní hodnoty zabezpečení Azure](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |Tento iniciativa provádí audit počítačů s Windows s nastaveními, která nesplňují základní hodnoty zabezpečení Azure. Podrobnosti najdete na stránce [https://aka.ms/gcpol](https://aka.ms/gcpol) |29 |2.0.0 – Preview |
