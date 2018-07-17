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
ms.openlocfilehash: c04514218c7ed8dfd72b94345d2deb88e663fda1
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740540"
---
[Zásady Azure](/azure/azure-policy/) pomáhají zajistit, aby všechny prostředky v předplatném splňovaly firemní standardy. Pomocí zásad můžete snížit náklady tím, že omezíte možnosti nasazení pouze na schválené typy prostředků a skladové položky. Pro prostředky můžete definovat pravidla a akce a tato pravidla se budou automaticky vynucovat během nasazení. Například můžete řídit, které typy prostředků se nasadí. Nebo můžete pro prostředky omezit schválená umístění. Některé zásady zamítnou akci a některé nastaví auditování určité akce.

Zásady představují doplněk k řízení přístupu na základě role (RBAC). RBAC se zaměřuje na uživatelský přístup a jedná se o systém, kdy je ve výchozím nastavení vše zamítnuto a explicitně se povoluje. Zásady se zaměřují na vlastnosti prostředků během a po nasazení. Jedná se o systém, kdy je ve výchozím nastavení vše povoleno a explicitně se zamítá.

V případě zásad je potřeba porozumět dvěma konceptům – *definice zásad* a *přiřazení zásad*. Definice zásady popisuje podmínky správy, které chcete vynucovat. Přiřazení zásady aplikuje definici zásady pro určitý rozsah.

![Přiřazování zásad](./media/resource-manager-governance-policy/policy-concepts.png)

Azure poskytuje několik předdefinovaných definic zásad, které můžete použít bez jakýchkoli úprav. Předáním hodnot parametrů můžete určit hodnoty, které jsou ve vašem rozsahu povolené. Pokud předdefinované definice zásad nesplňují vaše požadavky, můžete [vytvořit vlastní definice zásad](../articles/azure-policy/create-manage-policy.md).
