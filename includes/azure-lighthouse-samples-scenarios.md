---
title: zahrnout soubor
description: zahrnout soubor
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 04/24/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 4a6d9ef04a33c84d68dff1429fb39a193a249280
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82204488"
---
Tyto ukázky ukazují různé úlohy, které je možné provádět ve scénářích správy pro více tenantů.

| **Šablona** | **Popis** |
|---------|---------|
| [create-keyvault-secret](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret) | Vytvoří službu Key Vault v tenantovi zákazníka a vytvoří zásady přístupu.
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-rg-deployment) | Nasadí účty úložiště do dvou různých skupin prostředků.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-mgmt-services) | Vytvoření služeb pro správu Azure, jejich propojení a nasazení dalších řešení Ke kompletnímu nasazení použijte šablonu **rgWithAzureMgmt.json**. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-security-center) | Povolení a konfigurace služby Azure Security Center v rámci cílového předplatného Azure |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-sentinel) | Nasadí a povolí Azure Sentinel pro stávající pracovní prostor Log Analytics v delegovaném předplatném. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-log-analytics-vm-extensions) | Tyto šablony umožňují nasadit rozšíření Log Analytics do virtuálních počítačů s Windows a Linuxem a připojit je k určenému pracovnímu prostoru Log Analytics. |
