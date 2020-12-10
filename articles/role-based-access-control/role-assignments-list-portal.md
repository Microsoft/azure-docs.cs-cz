---
title: Vypsání přiřazení rolí Azure pomocí Azure Portal – Azure RBAC
description: Naučte se určit, k jakým prostředkům uživatelé, skupiny, instanční objekty nebo spravované identity mají přístup pomocí Azure Portal a řízení přístupu na základě role Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/09/2020
ms.author: rolyon
ms.openlocfilehash: 06a9a955e8a963f94b1885abf5a920cb96c01940
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007427"
---
# <a name="list-azure-role-assignments-using-the-azure-portal"></a>Vypsání přiřazení rolí Azure pomocí Azure Portal

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] Tento článek popisuje, jak zobrazit seznam přiřazení rolí pomocí Azure Portal.

> [!NOTE]
> Pokud má vaše organizace samoobslužné funkce správy pro poskytovatele služeb, který používá [správu delegovaných prostředků Azure](../lighthouse/concepts/azure-delegated-resource-management.md), tady se nezobrazí přiřazení rolí autorizovaných tímto poskytovatelem služeb.

## <a name="list-role-assignments-for-a-user-or-group"></a>Seznam přiřazení rolí pro uživatele nebo skupinu

Rychlý způsob, jak zobrazit role přiřazené uživateli nebo skupině v rámci předplatného, je použít podokno **přiřazení role Azure** .

1. V Azure Portal v nabídce Azure Portal vyberte **všechny služby** .

1. Vyberte **Azure Active Directory** a pak vyberte **Uživatelé** nebo **skupiny**.

1. Klikněte na uživatele nebo skupinu, pro kterou chcete zobrazit seznam přiřazení rolí.

1. Klikněte na **přiřazení rolí Azure**.

    Zobrazí se seznam rolí přiřazených vybranému uživateli nebo skupině v různých oborech, jako je skupina pro správu, předplatné, skupina prostředků nebo prostředek. Tento seznam obsahuje všechna přiřazení rolí, ke kterým máte oprávnění číst.

    ![Přiřazení rolí pro uživatele](./media/role-assignments-list-portal/azure-role-assignments-user.png)    

1. Chcete-li změnit předplatné, klikněte na seznam **předplatných** .

## <a name="list-owners-of-a-subscription"></a>Seznam vlastníků předplatného

Uživatelé, kterým byla přiřazena role [vlastníka](built-in-roles.md#owner) pro předplatné, můžou spravovat všechno v rámci předplatného. Pomocí těchto kroků můžete zobrazit seznam vlastníků předplatného.

1. V Azure Portal klikněte na **všechny služby** a pak na **předplatná**.

1. Klikněte na předplatné, pro které chcete zobrazit seznam vlastníků.

1. Klikněte na **Řízení přístupu (IAM)** .

1. Kliknutím na kartu **Přiřazení rolí** zobrazte všechna přiřazení rolí pro toto předplatné.

1. Přejděte do části **vlastníci** a zobrazte všechny uživatele, kterým byla přiřazena role vlastníka pro toto předplatné.

   ![Řízení přístupu k předplatnému – karta přiřazení rolí](./media/role-assignments-list-portal/sub-access-control-role-assignments-owners.png)

## <a name="list-role-assignments-at-a-scope"></a>Seznam přiřazení rolí v oboru

1. V Azure Portal klikněte na **všechny služby** a pak vyberte obor. Můžete například vybrat **skupiny pro správu**, **předplatná**, **skupiny prostředků** nebo prostředek.

1. Klikněte na konkrétní prostředek.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Kliknutím na kartu **přiřazení rolí** zobrazíte všechna přiřazení rolí v tomto oboru.

   ![Řízení přístupu – karta přiřazení rolí](./media/role-assignments-list-portal/rg-access-control-role-assignments.png)

   Na kartě přiřazení rolí vidíte, kdo má v tomto oboru přístup. Všimněte si, že některé role mají obor nastavený na **Tento prostředek**, zatímco jiné mají obor **(Zděděný)** z jiného oboru. Přístup je buď přiřazen konkrétně tomuto prostředku, nebo zděděn z přiřazení do nadřazeného oboru.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Seznam přiřazení rolí pro uživatele v oboru

Pokud chcete vypsat přístup pro uživatele, skupinu, instanční objekt nebo spravovanou identitu, uveďte jejich přiřazení rolí. Pomocí těchto kroků můžete zobrazit seznam přiřazení rolí pro jednoho uživatele, skupinu, instanční objekt nebo spravovanou identitu v konkrétním oboru.

1. V Azure Portal klikněte na **všechny služby** a pak vyberte obor. Můžete například vybrat **skupiny pro správu**, **předplatná**, **skupiny prostředků** nebo prostředek.

1. Klikněte na konkrétní prostředek.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Klikněte na kartu **kontrolovat přístup** .

    ![Řízení přístupu ke skupině prostředků – kontrola přístupu na kartě přístup](./media/role-assignments-list-portal/rg-access-control-check-access.png)

1. V seznamu **Najít** vyberte uživatele, skupinu, instanční objekt nebo spravovanou identitu, pro kterou chcete ověřit přístup.

1. Do vyhledávacího pole zadejte řetězec, ve kterém budou v adresáři hledána zobrazovaná jména, e-mailové adresy nebo identifikátory objektů.

    ![Zaškrtněte seznam pro výběr přístupu.](./media/shared/rg-check-access-select.png)

1. Kliknutím na objekt zabezpečení otevřete podokno **přiřazení** .

    V tomto podokně můžete zobrazit přístup pro vybraný objekt zabezpečení v tomto oboru a zdědit ho do tohoto oboru. Přiřazení v podřízených oborech nejsou uvedena. Zobrazí se následující přiřazení:

    - Přiřazení rolí se přidala do Azure RBAC.
    - Odmítnutí přiřazení přidaných pomocí Azure modrotisky nebo spravovaných aplikací Azure
    - Klasický správce služeb nebo Co-Administrator přiřazení pro nasazení v klasických prostředích. 

    ![podokno přiřazení](./media/shared/rg-check-access-assignments-user.png)

## <a name="list-role-assignments-for-a-managed-identity"></a>Seznam přiřazení rolí pro spravovanou identitu

Přiřazení rolí pro spravované identity přiřazené systémem a uživatelem v konkrétním oboru můžete vypsat pomocí okna **řízení přístupu (IAM)** , jak je popsáno výše. Tato část popisuje, jak zobrazit seznam přiřazení rolí jenom pro spravovanou identitu.

### <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

1. V Azure Portal otevřete spravovanou identitu přiřazenou systémem.

1. V nabídce vlevo klikněte na možnost **Identita**.

    ![Spravovaná identita přiřazená systémem](./media/shared/identity-system-assigned.png)

1. V části **oprávnění** klikněte na **přiřazení rolí Azure**.

    Zobrazí se seznam rolí přiřazených k vybrané spravované identitě přiřazené systémem v různých oborech, jako je skupina pro správu, předplatné, skupina prostředků nebo prostředek. Tento seznam obsahuje všechna přiřazení rolí, ke kterým máte oprávnění číst.

    ![Přiřazení rolí pro spravovanou identitu přiřazenou systémem](./media/shared/role-assignments-system-assigned.png)

1. Chcete-li změnit předplatné, klikněte na seznam **předplatných** .

### <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

1. V Azure Portal otevřete uživatelem přiřazenou spravovanou identitu.

1. Klikněte na **přiřazení rolí Azure**.

    Zobrazí se seznam rolí přiřazených k vybrané spravované identitě přiřazené uživatelem v různých oborech, jako je skupina pro správu, předplatné, skupina prostředků nebo prostředek. Tento seznam obsahuje všechna přiřazení rolí, ke kterým máte oprávnění číst.

    ![Snímek obrazovky zobrazující přiřazení rolí pro spravovanou identitu přiřazenou uživatelem](./media/shared/role-assignments-user-assigned.png)

1. Chcete-li změnit předplatné, klikněte na seznam **předplatných** .

## <a name="list-number-of-role-assignments"></a>Výpis počtu přiřazení rolí

V každém předplatném můžete mít až **2000** přiřazení rolí. Toto omezení zahrnuje přiřazení rolí v rámci předplatného, skupiny prostředků a oborů prostředků. Aby bylo možné tento limit sledovat, karta **přiřazení rolí** obsahuje graf, který uvádí počet přiřazení rolí pro aktuální předplatné.

![Řízení přístupu – počet grafů přiřazení rolí](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Pokud se blížíte k maximálnímu počtu a pokusíte se přidat další přiřazení role, zobrazí se upozornění v podokně **Přidat přiřazení role** . Způsob, jakým můžete snížit počet přiřazení rolí, najdete v tématu [řešení potíží s Azure RBAC](troubleshooting.md#azure-role-assignments-limit).

![Řízení přístupu – upozornění přidání přiřazení role](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="download-role-assignments"></a>Stažení přiřazení rolí

Přiřazení rolí si můžete stáhnout v oboru ve formátu CSV nebo JSON. To může být užitečné v případě, že při migraci předplatného potřebujete zkontrolovat seznam v tabulce nebo přebírat inventář.

Když si stáhnete přiřazení rolí, měli byste mít na paměti následující kritéria:

- Pokud nemáte oprávnění ke čtení adresáře, jako je například role čtenáři adresáře, sloupce DisplayName, SignInName a ObjectType budou prázdné.
- Nezahrnují se přiřazení rolí, jejichž objekt zabezpečení byl odstraněn.
- Není zahrnutý přístup povolený pro klasické správce.

Pomocí těchto kroků si můžete stáhnout přiřazení rolí v oboru.

1. V Azure Portal klikněte na **všechny služby** a pak vyberte obor, ve kterém chcete stáhnout přiřazení rolí. Můžete například vybrat **skupiny pro správu**, **předplatná**, **skupiny prostředků** nebo prostředek.

1. Klikněte na konkrétní prostředek.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Kliknutím na **Stáhnout přiřazení role** otevřete podokno pro stažení rolí přiřazení.

    ![Řízení přístupu – stažení přiřazení rolí](./media/role-assignments-list-portal/download-role-assignments.png)

1. Pomocí zaškrtávacích políček vyberte přiřazení rolí, která chcete zahrnout do staženého souboru.

    - **Zděděné** – zahrňte zděděné přiřazení rolí pro aktuální obor.
    - **V aktuálním oboru** – zahrňte přiřazení rolí pro aktuální obor.
    - **Podřízené** – zahrnuje přiřazení rolí na úrovních pod aktuálním oborem. Toto zaškrtávací políčko je pro obor skupiny pro správu zakázané.

1. Vyberte formát souboru, který může obsahovat hodnoty oddělené čárkami (CSV) nebo JavaScript Object Notation (JSON).

1. Zadejte název souboru.

1. Kliknutím na **Start** zahájíte stahování.

    Následující příklad ukazuje výstup pro jednotlivé formáty souborů.

    ![Stažení přiřazení rolí jako sdíleného svazku clusteru](./media/role-assignments-list-portal/download-role-assignments-csv.png)

    ![Snímek obrazovky se staženými přiřazeními rolí ve formátu JSON.](./media/role-assignments-list-portal/download-role-assignments-json.png)

## <a name="next-steps"></a>Další kroky

- [Přidání nebo odebrání přiřazení rolí Azure pomocí portálu Azure Portal](role-assignments-portal.md)
- [Řešení potíží s Azure RBAC](troubleshooting.md)
