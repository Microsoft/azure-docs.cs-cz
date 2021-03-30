---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: cephalin
ms.openlocfilehash: c3fa57dd162fbbfbf0d46f73bffc78f279ef2968
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "83649121"
---
* **Přihlašovací údaje na úrovni uživatele**: jedna sada přihlašovacích údajů pro celý účet Azure. Dá se použít k nasazení App Service pro libovolnou aplikaci v libovolném předplatném, ke které má účet Azure oprávnění pro přístup. Je to výchozí sada, která je v grafickém uživatelském rozhraní na portálu (například **Přehled** a **vlastnosti** [stránky prostředku](../articles/azure-resource-manager/management/manage-resources-portal.md#manage-resources)aplikace). Když je uživateli udělen přístup k aplikaci prostřednictvím Role-Based Access Control (RBAC) nebo oprávnění spolusprávce, může tento uživatel použít vlastní přihlašovací údaje na úrovni uživatele, dokud přístup nevrátí. Nesdílejte tyto přihlašovací údaje s ostatními uživateli Azure.

* **Přihlašovací údaje na úrovni aplikace**: jedna sada přihlašovacích údajů pro každou aplikaci. Dá se použít jenom k nasazení do této aplikace. Přihlašovací údaje pro jednotlivé aplikace se generují automaticky při vytvoření aplikace. Nedají se nakonfigurovat ručně, ale můžete je kdykoli resetovat. Aby mohl uživatel udělit přístup k přihlašovacím údajům na úrovni aplikace přes (RBAC), musí být tento uživatel přispěvatel nebo vyšší v aplikaci (včetně předdefinované role Přispěvatel webu). Čtenáři nemají oprávnění k publikování a nemají přístup k těmto přihlašovacím údajům.