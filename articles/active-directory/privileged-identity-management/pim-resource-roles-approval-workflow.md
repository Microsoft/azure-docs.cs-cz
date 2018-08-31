---
title: Schválit nebo zamítnout žádosti pro role prostředků Azure v PIM | Dokumentace Microsoftu
description: Naučte se schválit nebo zamítnout žádosti pro role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e5cda31af5ac95e7ebe2b858b1d7355ea3f2a6bb
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189800"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Schválit nebo zamítnout žádosti pro role prostředků Azure v PIM

Chcete-li schválit nebo odmítnout žádost, musí být členem seznamu schvalovatele. 

1. V PIM, vyberte **schvalovat žádosti o** z karty v nabídce vlevo a vyhledejte požadavku.

   ![Podokno "Schvalovat žádosti o"](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. Vyberte žádosti, uveďte její odůvodnění pro rozhodnutí a vyberte **schválit** nebo **Odepřít**. Pak vyřeší požadavek.

   ![Vybrané žádosti s podrobnými informacemi](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Oznámení pro pracovní postupy

Tady jsou některé údaje o oznámení pracovního postupu:

- Všichni členové schvalovatel seznamu se zobrazí oznámení e-mailem žádost o roli čeká na kontrolu. E-mailová oznámení zahrnují přímý odkaz na žádost, ve kterém můžete schvalující schválit nebo zamítnout.
- Požadavky jsou vyřešeny prvního člena seznamu, který potvrdí nebo odmítne. 
- Když schvalující odpoví na žádost, se zobrazí všechny členy seznamu schvalovatel oznámení akce. 
- Správci prostředků se zobrazí oznámení, když schváleného člena stane aktivní v jejich role. 

>[!Note]
>Správce prostředků, který se řídí zásadou, že by neměl být aktivní schváleného člena odebrat přiřazení active role v PIM. Přestože správci prostředků nejsou dostávají oznámení o čekajících žádostí Pokud nejsou členy seznamu schvalovatele, mohou zobrazit a zrušit čekající žádosti o pro všechny uživatele tak, že zobrazení čekající žádosti v PIM. 

## <a name="next-steps"></a>Další postup

- [Schválit nebo zamítnout žádosti pro role adresáře Azure AD v PIM](azure-ad-pim-approval-workflow.md)
- [E-mailových oznámení v PIM](pim-email-notifications.md)
