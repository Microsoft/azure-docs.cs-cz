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
ms.openlocfilehash: 0b3af1bd7b3ab48074432d9e39552d72c6664b98
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124727"
---
Při nasazování prostředků do Azure máte značnou flexibilitu při rozhodování, jaké typy prostředků nasadit, do jakého umístění a jak je nastavit. Tato flexibilita však může otevírat více možností, než byste ve vaší organizaci chtěli povolit. Při zvažování nasazení prostředků do Azure si můžete klást následující otázky:

* Jak v určitých zemích zajistím splnění zákonných požadavků na suverenitu dat?
* Jak můžu řídit náklady?
* Jak zajistím, že někdo nechtěně nezmění důležitý systém?
* Jak můžu sledovat náklady na prostředky a správně je účtovat?

Těmito otázkami se zabývá tento článek. Konkrétně můžete:

> [!div class="checklist"]
> * Přiřadit uživatele k rolím a tyto role přiřadit k rozsahům, aby uživatelé měli oprávnění k provádění očekávaných akcí, ale žádných dalších.
> * Použít zásady, které předepisují konvence pro prostředky ve vašem předplatném.
> * Uzamknout prostředky, které jsou pro váš systém klíčové.
> * Označit prostředky, abyste je mohli sledovat podle hodnot, které dávají smysl pro vaši organizaci.

Tento článek se zaměřuje na úlohy, pomocí kterých můžete implementovat zásady správného řízení. Podrobnější popis těchto konceptů najdete v tématu [Zásady správného řízení v Azure](../articles/security/governance-in-azure.md). 
