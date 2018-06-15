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
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2018
ms.locfileid: "29529975"
---
[Azure zásady](/azure/azure-policy/) nápovědy byste si ověřit, všechny prostředky v předplatném splňovat firemními standardy. Pomocí zásad můžete snížit náklady na omezení možnosti nasazení pouze tyto typy prostředků a skladových položek, které jsou schváleny. Můžete definovat pravidla a akce pro vaše prostředky a tato pravidla se vynucují automaticky během nasazení. Například můžete řídit typy prostředků, které jsou nasazeny. Nebo můžete omezit schválené umístění pro prostředky. Některé zásady Odepřít akce a některé zásady, nastavit auditování akce.

Zásady je doplňkem k řízení přístupu na základě role (RBAC). RBAC se zaměřuje na uživatelský přístup, a je výchozí Odepřít a explicitní povolit systému. Zásady se zaměřuje na vlastnosti prostředku, během a po nasazení. Jeho výchozí povolit a explicitní odepřít systému.

Existují dvě koncepty zásadám - *definice zásady* a *přiřazení zásad*. Definice zásad popisuje správu podmínek, které chcete vynutit. Přiřazení zásad převádí definici zásady do akce pro konkrétní obor.

![Přiřadit zásady](./media/resource-manager-governance-policy/policy-concepts.png)

Azure nabízí několik předdefinovaných zásad definice, které můžete použít bez nutnosti jakékoli úpravy. Můžete předat hodnoty parametrů můžete zadat hodnoty, které jsou povoleny ve vašem oboru. Pokud definice předdefinovaných zásad nemáte splnění požadavků, můžete [vytvořit vlastní zásady definice](../articles/azure-policy/create-manage-policy.md).
