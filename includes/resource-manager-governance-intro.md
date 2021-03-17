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
ms.openlocfilehash: 55b545471cbe45fe35e28879270e4340cf9d3834
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027489"
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

Tento článek se zaměřuje na úlohy, pomocí kterých můžete implementovat zásady správného řízení. Podrobnější popis těchto konceptů najdete v tématu [Zásady správného řízení v Azure](../articles/governance/index.yml).