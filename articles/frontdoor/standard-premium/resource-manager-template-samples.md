---
title: Ukázky šablon Správce prostředků – přední dvířka Azure
description: Informace o vzorových Azure Resource Manager šablonách poskytnutých pro přední dveře Azure.
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 03/24/2021
ms.openlocfilehash: 929adb0be948339af033d85b0dabd7e1cedf353e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561742"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Šablony Azure Resource Manager pro přední dveře Azure

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

Následující tabulka obsahuje odkazy na šablony Azure Resource Manager pro přední dveře Azure s referenčními architekturami, včetně dalších služeb Azure.

| Ukázka | Popis |
|-|-|
| [Sada pravidel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set/) | Vytvoří profil přední vrátka a sadu pravidel.  |
|**App Service zdroje**| **Popis** |
| [App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | Vytvoří aplikaci App Service s veřejným koncovým bodem a profilem front-dveří.  |
| [App Service s privátním odkazem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | Vytvoří aplikaci App Service s privátním koncovým bodem a profilem front-dveří.  |
| [App Service prostředí s privátním odkazem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-environment-internal-private-link) | Vytvoří App Service prostředí, aplikaci s privátním koncovým bodem a profil front-dveří.  |
|**Azure Functions zdroje**| **Popis** |
| [Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-function-public/) | Vytvoří aplikaci Azure Functions s veřejným koncovým bodem a profilem front-dveří.  |
| [Azure Functions s privátním odkazem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-function-private-link) | Vytvoří aplikaci Azure Functions s privátním koncovým bodem a profilem front-dveří.  |
|**API Management zdroje**| **Popis** |
| [API Management (externí)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-api-management-external) | Vytvoří instanci API Management s externí integrací virtuální sítě a profilem front-dveří.  |
|**Původ úložiště**| **Popis** |
| [Statický web úložiště](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-storage-static-website) | Vytvoří účet Azure Storage a statický web s veřejným koncovým bodem a profilem front-dveří.  |
| [Objekty blob úložiště s privátním propojením](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-storage-blobs-private-link) | Vytvoří účet Azure Storage a kontejner objektů BLOB s privátním koncovým bodem a profilem front-dveří.  |
|**Application Gateway zdroje**| **Popis** |
| [Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-application-gateway-public) | Vytvoří Application Gateway a profil front-dveří. |
|**Původ virtuálních počítačů**| **Popis** |
| [Virtuální počítač se službou privátního propojení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-vm-private-link) | Vytvoří virtuální počítač a službu privátního propojení a profil front-dveří. |
| | |
