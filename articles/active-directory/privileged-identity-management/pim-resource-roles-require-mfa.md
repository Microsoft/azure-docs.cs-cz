---
title: Privileged Identity Management pro prostředky Azure – vícefaktorové ověřování | Microsoft Docs
description: Tento dokument popisuje postup povolení vícefaktorového ověřování pro prostředky PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 8d1c05e7f61ed76c47613bfab7bb8afd9b66cbe7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---mfa"></a>Privileged Identity Management - role prostředků - MFA

PIM pro role prostředků Azure umožňuje správci prostředků a správci identity k ochraně kritické infrastruktury Azure s časově vázaných členství a přístup za běhu. Kromě toho poskytuje PIM volitelné vynucení Azure Multi-Factor Authentication (MFA) pro dva odlišné scénáře.

## <a name="require-mfa-to-activate"></a>Vyžadovalo aktivovat

Správci prostředků můžete vyžadovat, vhodné členy role úspěšné Azure MFA, před aktivací. Tento proces zajišťuje, že je aktivace žádajícího uživatele, který říká se, že jsou s dostatečnou jistotou. Vynucování tuto možnost chrání důležitých prostředků v situacích, pokud uživatelský účet ohrožení. 

Pokud chcete vynutit tento požadavek, vyberte prostředku ze seznamu spravované prostředky. Z [řídicí panel Přehled](pim-resource-roles-overview-dashboards.md), vyberte roli ze seznamu rolí v pravém dolním rohu obrazovky.

Kromě toho získáte nastavení role z buď "role" nebo "Nastavení Role" karty v levé navigační nabídce.

>[!Note]
>Pokud jsou zobrazena šedě možnosti v nabídce levé navigaci a zobrazí nápis informující o v horní části stránky s oznámením "Máte nárok role, které je možné aktivovat" nejsou aktivní správce a musí [aktivovat](pim-resource-roles-activate-your-roles.md) než budete pokračovat.

![](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

Pokud členství role zobrazení, vyberte z panelu v horní části obrazovky otevřete "podrobnosti o nastavení Role" "Nastavení Role".

Klikněte **upravit** tlačítka v horní části můžete upravit nastavení rolí.

V části v rámci **aktivovat**, klikněte na zaškrtávací políčko k **vyžadovat Vícefaktorové ověřování k aktivaci**, pak klikněte na Uložit.

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-mfa-on-assignment"></a>Vyžadovat vícefaktorové ověřování na přiřazení

V některých případech může správce prostředků chcete členem přiřadit roli krátké doby trvání (například jeden den) a není nutné přiřazené členy a požádají o aktivaci. V tomto scénáři nemůže PIM vynutit vícefaktorové ověřování, pokud člen používá přiřazení role, protože je již v roli od okamžiku, kdy jsou přiřazeny aktivní.

Zajistit, že je správce prostředků splnění přiřazení, který říká se, že jsou můžete vynutit vícefaktorové ověřování na přiřazení.

Z stejné Role nastavení obrazovky podrobnosti zaškrtněte políčko "Vyžadovat Vícefaktorové ověřování u přiřazení".

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>Další postup

[Vyžadovat schválení aktivovat](pim-resource-roles-approval-workflow.md)

[Použití protokolu auditu](pim-resource-roles-use-the-audit-log.md)



