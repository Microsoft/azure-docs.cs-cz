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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740549"
---
Než začnete vytvářet položky, projdeme si koncept rozsahu. Azure poskytuje čtyři úrovně správy: skupiny pro správu, předplatné, skupina prostředků a prostředek. [Skupiny pro správu](../articles/billing/billing-enterprise-mgmt-group-overview.md) jsou ve verzi Preview. Následující obrázek ukazuje příklad těchto vrstev.

![Rozsah](./media/resource-manager-governance-scope/scope-levels.png)

Nastavení správy můžete použít na jakékoli z těchto úrovní rozsahu. Vybraná úroveň určuje rozsah použití nastavení. Nižší úrovně dědí nastavení z vyšších úrovní. Když nějaké nastavení použijete pro předplatné, použije se toto nastavení pro všechny skupiny prostředků a prostředky v tomto předplatném. Když nějaké nastavení použijete pro skupinu prostředků, použije se toto nastavení pro skupinu prostředků i všechny prostředky, které obsahuje. Jiná skupina prostředků ale toto nastavení nezíská.

Obvykle je vhodné používat důležitá nastavení na vyšších úrovních a nastavení související s požadavky konkrétního projektu na nižších úrovních. Například můžete chtít zajistit, aby se všechny prostředky vaší organizace nasazovaly do konkrétních oblastí. Tento požadavek splníte tak, že pro předplatné použijete zásadu, která udává povolená umístění. Když ostatní uživatelé ve vaší organizaci přidají nové skupiny prostředků a prostředky, automaticky se vynutí použití některého z povolených umístění. 
