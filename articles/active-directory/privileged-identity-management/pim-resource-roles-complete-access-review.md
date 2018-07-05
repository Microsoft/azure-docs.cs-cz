---
title: Dokončení kontroly přístupu pro prostředky Azure s použitím Privileged Identity Management | Dokumentace Microsoftu
description: Popisuje postup při dokončení kontroly přístupu pro prostředky Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e21d0240469a9c775e610c97f98c073b8f83ce8e
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442186"
---
# <a name="complete-an-access-review-for-azure-resources-by-using-privileged-identity-management"></a>Dokončení kontroly přístupu pro prostředky Azure s použitím Privileged Identity Management
Správci privilegovaných rolí můžete zkontrolovat privilegovaný přístup po [se spustila kontrola zabezpečení](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) pro prostředky Azure automaticky odesílá e-mail, který vyzve uživatele ke kontrole jejich přístupu. Pokud uživatel e-mailu, můžete jim poslat pokyny [provádění kontroly zabezpečení](pim-resource-roles-perform-access-review.md).

Po období kontroly zabezpečení, nebo po dokončení všech uživatelů jejich svým přezkoumat, postupujte podle kroků v tomto článku můžete spravovat revize a zobrazit výsledky.

## <a name="manage-security-reviews"></a>Spravovat revize zabezpečení
1. Přejděte na [portál Azure](https://portal.azure.com/). Vyberte na řídicím panelu **prostředky Azure** aplikace.

2. Vyberte prostředek.

3. Vyberte **kontrol přístupu** část řídicího panelu.
![Kontroly přístupu](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)

4. Vyberte kontroly přístupu, kterou chcete spravovat.

V okně Podrobnosti o kontrolu přístupu existuje mnoho možností pro správu této revize. Možnosti jsou následující:

![Možnosti pro správu revize](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Zastavit
Všechny kontroly přístupu mají koncové datum, ale můžete použít **Zastavit** tlačítko pro dokončení včas. Všichni uživatelé, kteří nedokončili svoji kontrolu té doby nebude možné ji dokončit po zastavení revize. Kontrola nelze restartovat po byla zastavena.

### <a name="reset"></a>Resetovat
Kontroly přístupu k odebrání všech rozhodnutí, které jsou provedeny v něm můžete resetovat. Poté, co jste změnili kontroly přístupu, všichni uživatelé jsou označeny jako znovu neprovedla kontrola. 

### <a name="apply"></a>Použít
Po dokončení kontroly přístupu používat **použít** tlačítko k implementaci výsledek kontroly. Pokud v revizi byl odepřen přístup uživatelů, tento krok Odstraní přiřazení role.  

### <a name="delete"></a>Odstranění
Můžete se v případě zájmu revize víc, odstraňte jej. **Odstranit** tlačítko kontroly odebere z aplikace PIM.

## <a name="results"></a>Výsledky
Na **výsledky** kartu, zobrazit a stáhnout seznam výsledků kontroly. 
![Karta výsledky](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Revidující
Zobrazení a přidání revidujících k vaší existující kontrolu přístupu. Připomeňte revidujících k dokončení jejich revize.
![Přidat kontrolory](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



