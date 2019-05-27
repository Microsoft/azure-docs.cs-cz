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
ms.openlocfilehash: d1e7fa1ed1649508f0d4923db8817d17ad556ca1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66164589"
---
Při nasazování prostředků do Azure máte značnou flexibilitu při rozhodování, jaké typy prostředků nasadit, do jakého umístění a jak je nastavit. Tato flexibilita však může otevírat více možností, než byste ve vaší organizaci chtěli povolit. Při zvažování nasazení prostředků do Azure si můžete klást následující otázky:

* Jak můžu splňovat právní požadavky na suverenitu dat v určitých zemích nebo oblastech?
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
