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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740546"
---
Při nasazování prostředků do Azure máte značnou flexibilitu při rozhodování, jaké typy prostředků nasadit, do jakého umístění a jak je nastavit. Tato flexibilita však může otevírat více možností, než byste ve vaší organizaci chtěli povolit. Při zvažování nasazení prostředků do Azure si můžete klást následující otázky:

* Jak v určitých zemích zajistím splnění zákonných požadavků na suverenitu dat?
* Jak můžu řídit náklady?
* Jak zajistím, že někdo nechtěně nezmění důležitý systém?
* Jak můžu sledovat náklady na prostředky a správně je účtovat?

Těmito otázkami se zabývá tento článek. Konkrétně můžete:

> [!div class="checklist"]
* Přiřadit uživatele k rolím a tyto role přiřadit k rozsahům, aby uživatelé měli oprávnění k provádění očekávaných akcí, ale žádných dalších.
* Použít zásady, které předepisují konvence pro prostředky ve vašem předplatném.
* Uzamknout prostředky, které jsou pro váš systém klíčové.
* Označit prostředky, abyste je mohli sledovat podle hodnot, které dávají smysl pro vaši organizaci.

Tento článek se zaměřuje na úlohy, pomocí kterých můžete implementovat zásady správného řízení. Podrobnější popis těchto konceptů najdete v tématu [Zásady správného řízení v Azure](../articles/security/governance-in-azure.md). 
