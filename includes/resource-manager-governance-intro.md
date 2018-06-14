---
title: zahrnout soubor
description: zahrnout soubor
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 2c16e82ccf259a4cc5ae8fcf35b2dd6b5d50ee2d
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2018
ms.locfileid: "29528340"
---
Při nasazování prostředků do Azure, máte značnou flexibilitu při rozhodování, jaké typy prostředků pro nasazení, tam, kde jsou umístěné a jak je nastavit. Tuto flexibilitu však může otevřít další možnosti, než byste chtěli povolit ve vaší organizaci. Jako uvažujete o nasazení prostředků do Azure, možná se ptáte:

* Jak splňují právní požadavky pro suverenity dat v některých zemích?
* Jak řídit náklady?
* Jak zjistím, že někdo nezmění nechtěně důležité systém?
* Jak sledování prostředků nákladů a účtovat pošle přesně?

Tento článek se zaměřuje na tyto otázky. Konkrétně můžete:

> [!div class="checklist"]
* Přiřadit uživatele k rolím a přiřaďte role do oboru, takže uživatelé mají oprávnění k provedení akce očekávané, ale ne další akce.
* Použít zásady, které stanovit konvence pro prostředky ve vašem předplatném.
* Zámek prostředky, které jsou důležité k vašemu systému.
* Označit prostředky, takže můžete sledovat jejich hodnoty, které dávají smysl pro vaši organizaci.

Tento článek se zaměřuje na úlohy, které je třeba provést implementovat zásady správného řízení. Širší informace koncepty, naleznete v [zásad správného řízení v Azure](../articles/security/governance-in-azure.md). 
