---
title: Správa oprávnění k prostředkům na uživatele v Azure zásobníku | Microsoft Docs
description: Jako správce služeb nebo klienta zjistěte, jak ke správě oprávnění RBAC.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 9944f51c080da6edd89927bfd26398024c5d4de2
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="manage-access-to-resources-with-azure-stack-role-based-access-control"></a>Spravovat přístup k prostředkům pomocí řízení přístupu Azure Stack Role-Based

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Azure zásobníku podporuje řízení přístupu na základě role (RBAC), stejné [model zabezpečení pro správu přístupu](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview) používající Microsoft Azure. RBAC můžete spravovat uživatele, skupiny nebo přístup k aplikaci odběry, prostředky a službám.

## <a name="basics-of-access-management"></a>Základní informace o řízení přístupu

Řízení přístupu na základě rolí poskytuje řízení podrobných přístupu, který vám pomůže zabezpečit vaše prostředí. Uživatelům udělíte přesný oprávnění, které potřebují přiřazením role RBAC u určité oboru. Předplatné, skupinu prostředků nebo jediný zdroj, může být oboru přiřazení role. Pro čtení [řízení přístupu na základě rolí na portálu Azure](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview) článku získat podrobnější informace o správě přístupu.

### <a name="built-in-roles"></a>Vestavěné role

Sada Azure má tři základní role, které můžete použít pro všechny typy prostředků:

* **Vlastník** můžou spravovat všechno včetně přístupu k prostředkům.
* **Přispěvatel** můžou spravovat všechno kromě přístupu k prostředkům.
* **Čtečka** můžou zobrazit všechno, ale nelze provést žádné změny.

### <a name="resource-hierarchy-and-inheritance"></a>Hierarchie prostředků a dědičnost

Sada Azure má následující hierarchie prostředků:

* Každé předplatné patří do jednoho adresáře.
* Na jedno předplatné patří každé skupině prostředků.
* Každý prostředek patří do jedné skupiny prostředků.

V podřízené obory dědí přístup, který udělíte v nadřazeném oboru. Příklad:

* Můžete přiřadit **čtečky** role skupiny v oboru předplatné služby Azure AD. Členové této skupiny můžete zobrazit všechny skupiny prostředků a prostředků v předplatném.
* Můžete přiřadit **Přispěvatel** role k aplikaci v oboru skupiny prostředků. Aplikace můžete spravovat prostředky všechny typy v příslušné skupině prostředků, ale ne další skupiny zdrojů v rámci předplatného.

### <a name="assigning-roles"></a>přiřazení rolí

Můžete přiřadit více než jedné role pro uživatele a každou roli, mohou být přidruženy jiný rozsah. Příklad:

* Role čtenáře TestUser-A je přiřadit k předplatnému 1.
* Přiřadíte roli TestUser-A Owner TestVM-1.

Azure [přiřazení rolí](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-portal) článek obsahuje podrobné informace o zobrazení, přiřazení nebo odstranění rolí.

## <a name="set-access-permissions-for-a-user"></a>Nastavte přístupová oprávnění pro uživatele

Následující kroky popisují, jak nakonfigurovat oprávnění pro uživatele.

1. Přihlaste se pomocí účtu, který má oprávnění vlastníka prostředku, který chcete spravovat.
2. V levém navigačním podokně zvolte **skupiny prostředků**.
3. Vyberte název skupiny prostředků, kterou chcete nastavit oprávnění.
4. V navigačním podokně prostředku skupiny zvolte **přístup k ovládacímu prvku (IAM)**. **Řízení přístupu** zobrazení obsahuje seznam položek, které mají přístup ke skupině prostředků. Můžete filtrovat tyto výsledky a pomocí nabídky panelu Přidat nebo odebrat oprávnění.
5. Na **řízení přístupu** nabídky panel, vyberte **+ přidat**.
6. Na **přidat oprávnění**:

   * Vyberte roli, kterou chcete přiřadit z **Role** rozevíracího seznamu.
   * Vyberte prostředek, které chcete přiřadit z **přiřadit přístup** rozevíracího seznamu.
   * Ve svém adresáři vyberte uživatele, skupinu nebo aplikaci, kterým chcete přiřadit přístup. V adresáři můžete vyhledávat pomocí zobrazovaných názvů, e-mailových adres a identifikátorů objektů.

7. Vyberte **Uložit**.

## <a name="next-steps"></a>Další postup

[Vytvoření instančních objektů](azure-stack-create-service-principals.md)