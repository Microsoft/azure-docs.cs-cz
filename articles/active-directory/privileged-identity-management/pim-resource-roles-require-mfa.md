---
title: Vynutit ověřování Azure Multi-Factor Authentication v prostředcích Azure s použitím Privileged Identity Management | Dokumentace Microsoftu
description: Tento dokument popisuje, jak povolit vícefaktorové ověřování pro prostředky PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 21f6fef214f27630ff0eadc39e1e26c9c344f353
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444728"
---
# <a name="enforce-azure-multi-factor-authentication-in-azure-resources-by-using-privileged-identity-management"></a>Vynutit ověřování Azure Multi-Factor Authentication v prostředcích Azure s použitím Privileged Identity Management

Privileged Identity Management (PIM) pro role prostředků Azure umožňuje správci prostředků a správci identit k ochraně důležitých infrastruktury Azure s nástroji membership časově omezenou a přístup k just-in-time. Kromě toho poskytuje PIM volitelné vynucení ověřování Azure Multi-Factor Authentication pro dva různé scénáře.

## <a name="require-multi-factor-authentication-to-activate"></a>Vyžadovat Vícefaktorové ověřování k aktivaci

Správci prostředků můžete vyžadovat, aby oprávněné členy role ke spuštění ověřování Azure Multi-Factor Authentication, před aktivací. Tento proces zajišťuje, že uživatel, který žádá o tom, že se aktivace, který říká, že jsou s jistotou přiměřené. Vynucování této možnosti chrání důležité prostředky v situacích, pokud uživatelský účet může být ohrožený. 

Pokud chcete vynutit tento požadavek, vyberte prostředek ze seznamu spravovaných prostředků. Z [řídicího panelu s přehledem](pim-resource-roles-overview-dashboards.md), vyberte roli ze seznamu rolí v pravé dolní části obrazovky.

Kromě toho můžete získat nastavení role buď z **role** nebo **nastavení Role** karty v levém podokně.

>[!Note]
>Pokud se zobrazí banner v horní části stránky s oznámením "Máte oprávněných rolí, které je možné aktivovat" možnosti v levém podokně jsou zobrazena šedě, nejste správcem active. To znamená, že je potřeba [aktivovat](pim-resource-roles-activate-your-roles.md) než budete pokračovat.

!["Role" a "Nastavení Role" karty ](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

Chcete-li zobrazit členství rolí, vyberte **nastavení Role** na panelu v horní části obrazovky, chcete-li otevřít **podrobnosti nastavení Role**.

Chcete-li upravit nastavení rolí, vyberte **upravit** tlačítko v horní části.

V části v rámci **aktivovat**, zaškrtněte políčko pro **vyžadovat Vícefaktorové ověřování při aktivaci**. Potom vyberte **Uložit**.

![Vyžadovat Multi-Factor Authentication při aktivaci](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-multi-factor-authentication-on-assignment"></a>Vyžadovat Vícefaktorové ověřování při přiřazení

V některých případech může správce prostředků chcete přiřadit člena k roli na krátkou dobu (jeden den, například). V takovém případě nepotřebují přiřazené členy k vyžádání aktivace. V tomto scénáři PIM nemůže vynutit ověřování Multi-Factor Authentication, když člen používá přiřazení role, protože je již aktivní v roli od okamžiku, kdy jsou přiřazeny.

Zajistit, že je správce prostředků splnění přiřazení který říká, že jsou můžete vynutit ověřování Multi-Factor Authentication na přiřazení.

Ze stejné role nastavení obrazovku s podrobnostmi, zaškrtněte políčko **vyžadovat Vícefaktorové ověřování u přímého přiřazení**.

![Vyžadovat Vícefaktorové ověřování na přímého přiřazení](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>Další postup

[K aktivaci vyžadovat schválení](pim-resource-roles-approval-workflow.md)

[Použití protokolu auditu](pim-resource-roles-use-the-audit-log.md)



