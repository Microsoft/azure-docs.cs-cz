---
title: zahrnout soubor
description: zahrnout soubor
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 4f5dbbd1da8a221fb5f8b9641e016829987db538
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2018
ms.locfileid: "29529958"
---
Před vytvořením všechny položky, pojďme si shrnout koncept oboru. Azure nabízí čtyři úrovně správy: skupin pro správu, předplatné, skupinu prostředků a prostředků. [Skupiny pro správu](../articles/billing/billing-enterprise-mgmt-group-overview.md) jsou ve verzi preview. Následující obrázek ukazuje příklad tyto vrstvy.

![Rozsah](./media/resource-manager-governance-scope/scope-levels.png)

Můžete použít nastavení pro správu na všech těchto úrovních oboru. Vybraná úroveň určuje, jak často se projeví nastavení. Nižších úrovních dědit nastavení z vyšší úrovně. Když použijete nastavení pro předplatné, toto nastavení platí pro všechny skupiny prostředků a prostředky v rámci vašeho předplatného. Při aplikování nastavení na skupinu prostředků, které nastavení se použijí skupinu prostředků a všechny její prostředky. Jiné skupině prostředků, ale nemá tohoto nastavení.

Obvykle má smysl použít důležitých nastavení na vyšší úrovně a projekt specifické požadavky na nižších úrovních. Například můžete chtít zkontrolujte, zda že všechny prostředky ve vaší organizaci se nasadí do určitých oblastí. Aby bylo možné tento požadavek, použijte pro předplatné, které určuje povolených umístění zásady. Jako ostatní uživatelé ve vaší organizaci přidat nové skupiny prostředků a prostředků, vynutí se automaticky povolených umístění. 
