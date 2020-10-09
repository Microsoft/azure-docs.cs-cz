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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67174937"
---
Při nasazování prostředků do Azure máte značnou flexibilitu při rozhodování, jaké typy prostředků nasadit, do jakého umístění a jak je nastavit. Tato flexibilita však může otevírat více možností, než byste ve vaší organizaci chtěli povolit. Při zvažování nasazení prostředků do Azure si můžete klást následující otázky:

* Návody splňovat právní požadavky na svrchovanost dat v určitých zemích nebo oblastech?
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
