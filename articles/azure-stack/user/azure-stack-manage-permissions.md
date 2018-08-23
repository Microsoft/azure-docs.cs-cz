---
title: Správa oprávnění k prostředkům na uživatele ve službě Azure Stack | Dokumentace Microsoftu
description: Jako správce služeb nebo tenanta zjistěte, jak spravovat oprávnění RBAC.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: patricka
ms.reviewer: ''
ms.openlocfilehash: 2e36f52568d349aeecd47f90bf9431f096cc4fdc
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42059407"
---
# <a name="manage-access-to-resources-with-azure-stack-role-based-access-control"></a>Správa přístupu k prostředkům pomocí Azure Stack Role-Based řízení přístupu

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Azure Stack podporuje řízení přístupu na základě role (RBAC), stejné [model zabezpečení pro správu přístupu](https://docs.microsoft.com/azure/role-based-access-control/overview) používající Microsoft Azure. RBAC můžete použít ke správě uživatele, skupinu nebo aplikaci přístup k předplatných, prostředků a služeb.

## <a name="basics-of-access-management"></a>Základní informace o řízení přístupu

Řízení přístupu na základě rolí poskytuje jemně odstupňované řízení přístupu, který vám pomůže zabezpečit vaše prostředí. Uživatelům můžete udělit konkrétní oprávnění, které potřebují přiřazením role RBAC v určitém rozsahu. Obor přiřazení role může být předplatné, skupinu prostředků nebo jediný prostředek. Čtení [řízení přístupu na základě rolí na portálu Azure portal](https://docs.microsoft.com/azure/role-based-access-control/overview) článkem. poskytne podrobnější informace o řízení přístupu.

### <a name="built-in-roles"></a>Vestavěné role

Azure Stack má tři základní role, které můžete použít na všechny typy prostředků:

* **Vlastník** můžou spravovat všechno včetně přístupu k prostředkům.
* **Přispěvatel** můžou spravovat všechno kromě přístupu k prostředkům.
* **Čtečka** může vše zobrazit, ale nemůže provádět žádné změny.

### <a name="resource-hierarchy-and-inheritance"></a>Hierarchie prostředků a dědičnost

Azure Stack má následující hierarchie prostředků:

* Každé předplatné patří k jednomu adresáři.
* Každé skupině prostředků patří k jednomu předplatnému.
* Každý prostředek patří do jedné skupiny prostředků.

V podřízené obory dědí přístup, který udělíte v nadřazeném oboru. Příklad:

* Role Čtenář přiřadíte ke skupině Azure AD v oboru předplatného. Členové této skupiny můžou zobrazit každou skupinu prostředků a prostředků v předplatném.
* Přiřadit roli přispěvatele do aplikace v oboru skupiny prostředků. Aplikace můžete spravovat prostředky v příslušné skupině prostředků, ale ne jiné skupiny prostředků v předplatném všech typů.

### <a name="assigning-roles"></a>Přiřazení rolí

Můžete přiřadit více než jednu roli pro uživatele a každou roli je možné přidružit jiný obor. Příklad:

* Přiřadit role Čtenář the TestUser-A k předplatnému 1.
* Přiřazení role vlastníka the TestUser-A TestVM-1.

Azure [přiřazení rolí](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) článek obsahuje podrobné informace o zobrazení, přiřazování a odstranění rolí.

### <a name="resource-hierarchy-and-inheritance"></a>Hierarchie prostředků a dědičnost

Azure Stack má následující hierarchie prostředků:

* Každé předplatné patří k jednomu adresáři.
* Každé skupině prostředků patří k jednomu předplatnému.
* Každý prostředek patří do jedné skupiny prostředků.

V podřízené obory dědí přístup, který udělíte v nadřazeném oboru. Příklad:

* Můžete přiřadit **čtečky** role ke skupině Azure AD v oboru předplatného. Členové této skupiny můžou zobrazit každou skupinu prostředků a prostředků v předplatném.
* Můžete přiřadit **Přispěvatel** role do aplikace v oboru skupiny prostředků. Aplikace můžete spravovat prostředky v příslušné skupině prostředků, ale ne jiné skupiny prostředků v předplatném všech typů.

### <a name="assigning-roles"></a>Přiřazení rolí

Můžete přiřadit více než jednu roli pro uživatele a každou roli je možné přidružit jiný obor. Příklad:

* Přiřadit role Čtenář the TestUser-A k předplatnému 1.
* Přiřazení role vlastníka the TestUser-A TestVM-1.

Azure [přiřazení rolí](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) článek obsahuje podrobné informace o zobrazení, přiřazování a odstranění rolí.

## <a name="set-access-permissions-for-a-user"></a>Nastavte přístupová oprávnění pro uživatele

Následující kroky popisují, jak nakonfigurovat oprávnění pro uživatele.

1. Přihlaste se pomocí účtu, který má oprávnění vlastníka prostředku, který chcete spravovat.
2. V levém navigačním podokně zvolte **Skupiny prostředků**.
3. Zvolte název, který chcete nastavit oprávnění pro skupinu prostředků.
4. V navigačním podokně skupiny prostředků zvolte **řízení přístupu (IAM)**. **Řízení přístupu** zobrazení obsahuje seznam položek, které mají přístup do skupiny prostředků. Můžete filtrovat výsledky a použít panel nabídek k přidání nebo odebrání oprávnění.
5. Na **řízení přístupu** nabídky vyberte možnosti **+ přidat**.
6. Na **přidat oprávnění**:

   * Zvolte roli, kterou chcete přiřadit z **Role** rozevíracího seznamu.
   * Vyberte prostředek, kterou chcete přiřadit z **přiřadit přístup k** rozevíracího seznamu.
   * Ve svém adresáři vyberte uživatele, skupinu nebo aplikaci, kterým chcete přiřadit přístup. V adresáři můžete vyhledávat pomocí zobrazovaných názvů, e-mailových adres a identifikátorů objektů.

7. Vyberte **Uložit**.

## <a name="next-steps"></a>Další postup

[Vytvoření instančních objektů](azure-stack-create-service-principals.md)