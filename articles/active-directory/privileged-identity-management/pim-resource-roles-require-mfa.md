---
title: Vynutit ověřování Azure Multi-Factor Authentication prostředků Azure pomocí Privileged Identity managementu | Microsoft Docs
description: Tento dokument popisuje postup povolení vícefaktorového ověřování pro prostředky PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 0c50a17b651fc1fb5d49915da5da4a37d40a0d3e
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260059"
---
# <a name="enforce-azure-multi-factor-authentication-in-azure-resources-by-using-privileged-identity-management"></a>Vynutit ověřování Azure Multi-Factor Authentication prostředků Azure pomocí Privileged Identity Management

Privileged Identity Management (PIM) pro role prostředků Azure umožňuje správci prostředků a správci identity k ochraně kritické infrastruktury Azure s časově vázaných členství a přístup za běhu. Kromě toho poskytuje PIM volitelné vynucení ověřování Azure Multi-Factor Authentication pro dva odlišné scénáře.

## <a name="require-multi-factor-authentication-to-activate"></a>Vyžadovat Vícefaktorové ověřování k aktivaci

Správci prostředků může vyžadovat vhodné členy role spuštění Azure Multi-Factor Authentication před aktivací. Tento proces zajišťuje, že uživatel, který požaduje se, že je aktivace, který říká se, že jsou s dostatečnou jistotou. Vynucování tuto možnost chrání důležitých prostředků v situacích, pokud uživatelský účet může být ohrožena. 

Pokud chcete vynutit tento požadavek, vyberte prostředku ze seznamu spravované prostředky. Z [řídicí panel Přehled](pim-resource-roles-overview-dashboards.md), vyberte roli ze seznamu rolí v pravé dolní části obrazovky.

Kromě toho můžete získat nastavení rolí z buď **role** nebo **nastavení Role** karty v levém podokně.

>[!Note]
>Pokud jsou zobrazena šedě možnosti v levém podokně a hlavičky v horní části stránky, která uvádí, "Máte nárok role, které je možné aktivovat", nejste správcem aktivní. To znamená, že je potřeba [aktivovat](pim-resource-roles-activate-your-roles.md) než budete pokračovat.

!["Role" a "Nastavení Role" karty ](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

Pokud chcete zobrazit členství role, vyberte **nastavení Role** z panelu v horní části obrazovky, otevřete **podrobné nastavení Role**.

Chcete-li upravit nastavení rolí, vyberte **upravit** tlačítka v horní části.

V části v rámci **aktivovat**, zaškrtněte políčko **vyžadovat Vícefaktorové ověřování u aktivace**. Potom vyberte **Uložit**.

![Vyžadovat Multi-Factor Authentication při aktivaci](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-multi-factor-authentication-on-assignment"></a>Požadovat použití vícefaktorového ověřování na přiřazení

V některých případech může správce prostředků chcete členem přiřadit roli po krátkou dobu (jeden den, např.). V takovém případě nemusí přiřazené členy a požádají o aktivaci. V tomto scénáři nemůže PIM vynutit ověřování Multi-Factor Authentication, pokud člen používá přiřazení role, protože je již v roli od okamžiku, kdy jsou přiřazeny aktivní.

Pro zajištění správce prostředků splnění přiřazení kdo říká se, že jsou, můžete vynutit ověřování Multi-Factor Authentication na přiřazení.

Z stejné role nastavení obrazovky podrobnosti, zaškrtněte políčko **vyžadovat Vícefaktorové ověřování u přímé přiřazení**.

![Požadovat použití vícefaktorového ověřování na přímé přiřazení](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>Další postup

[Vyžadovat schválení aktivovat](pim-resource-roles-approval-workflow.md)

[Použití protokolu auditu](pim-resource-roles-use-the-audit-log.md)



