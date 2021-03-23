---
title: Sdílení řídicích panelů Azure Portal pomocí řízení přístupu na základě role v Azure
description: Tento článek vysvětluje, jak sdílet řídicí panel v Azure Portal pomocí řízení přístupu na základě role v Azure.
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 336bfe9792c5ba4246458368e008a8c50833ed03
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771532"
---
# <a name="share-azure-dashboards-by-using-azure-role-based-access-control"></a>Sdílení řídicích panelů Azure pomocí řízení přístupu na základě role v Azure

Po nakonfigurování řídicího panelu ho můžete publikovat a sdílet s ostatními uživateli ve vaší organizaci. Ostatním uživatelům umožníte zobrazit váš řídicí panel pomocí [řízení přístupu na základě role Azure (RBAC)](../role-based-access-control/role-assignments-portal.md). Přiřazení jednoho uživatele nebo skupiny uživatelů k roli. Tato role definuje, jestli můžou uživatelé zobrazit nebo upravit publikovaný řídicí panel.

Všechny publikované řídicí panely se implementují jako prostředky Azure. Existují jako spravovatelné položky v rámci vašeho předplatného a jsou součástí skupiny prostředků. Z perspektivy řízení přístupu se řídicí panely neliší od jiných prostředků, jako je třeba virtuální počítač nebo účet úložiště. Jednotlivé dlaždice na řídicím panelu vynutily vlastní požadavky na řízení přístupu na základě zobrazovaných prostředků. Řídicí panel můžete široce sdílet a přitom chránit data v jednotlivých dlaždicích.

## <a name="understanding-access-control-for-dashboards"></a>Principy řízení přístupu pro řídicí panely

Díky řízení přístupu na základě role v Azure (Azure RBAC) můžete uživatelům přiřadit role na třech různých úrovních rozsahu:

* předplatné
* skupina prostředků
* prostředek

Oprávnění, která přiřadíte z předplatného, do prostředku. Publikovaný řídicí panel je prostředek. Je možné, že již máte přiřazeny uživatele k rolím pro předplatné, které platí pro publikovaný řídicí panel.

Řekněme, že máte předplatné Azure a různé členy týmu mají k předplatnému přiřazené role *vlastníka*, *přispěvatele* nebo *čtenáře* . Uživatelé, kteří jsou vlastníci nebo přispěvatelé, můžou v rámci předplatného vypisovat, zobrazovat, vytvářet, upravovat nebo odstraňovat řídicí panely. Uživatelé, kteří čtenáři můžou vypisovat a zobrazovat řídicí panely, ale nemůžou je upravovat ani odstraňovat. Uživatelé s přístupem ke čtenářům můžou provádět místní úpravy publikovaného řídicího panelu, například při odstraňování problému, ale nemůžou tyto změny publikovat zpátky na server. Můžou vytvořit soukromou kopii řídicího panelu pro sebe sama.

Můžete přiřadit oprávnění ke skupině prostředků, která obsahuje několik řídicích panelů nebo na jednotlivé řídicí panely. Můžete se třeba rozhodnout, že skupina uživatelů by měla mít omezená oprávnění v rámci předplatného, ale větší přístup k určitému řídicímu panelu. Přiřaďte tyto uživatele k roli pro tento řídicí panel.

## <a name="publish-a-dashboard"></a>Publikování řídicího panelu

Řekněme, že nakonfigurujete řídicí panel, který chcete sdílet se skupinou uživatelů v rámci vašeho předplatného. Následující kroky ukazují, jak sdílet řídicí panel se skupinou, která se nazývá Správce úložiště. Svou skupinu si můžete pojmenovat, ať už chcete. Další informace najdete v tématu [Správa skupin v Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Před přiřazením přístupu musíte řídicí panel publikovat.

1. Na řídicím panelu vyberte **sdílet**.

    ![Vyberte sdílení řídicího panelu.](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. V **sdílení + řízení přístupu** vyberte **publikovat**.

    ![publikování řídicího panelu](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Sdílení řídicího panelu se ve výchozím nastavení publikuje do skupiny prostředků s názvem **řídicí panely**. Chcete-li vybrat jinou skupinu prostředků, zrušte zaškrtnutí políčka.

Váš řídicí panel je teď publikovaný. Pokud jsou oprávnění zděděná z předplatného vhodná, nemusíte dělat nic dalšího. Jiní uživatelé ve vaší organizaci mají přístup k řídicímu panelu na základě role na úrovni předplatného a můžou ho upravovat.

## <a name="assign-access-to-a-dashboard"></a>Přiřazení přístupu k řídicímu panelu

Skupině uživatelů můžete přiřadit roli pro tento řídicí panel.

1. Po publikování řídicího panelu vyberte **Spravovat sdílení**.

1. V **Access Control** vyberte **přiřazení rolí** , abyste viděli existující uživatele, kteří už mají přiřazenou roli pro tento řídicí panel.

1. Chcete-li přidat nového uživatele nebo skupinu, vyberte možnost **Přidat** a **Přidat přiřazení role**.

    ![Přidat uživatele pro přístup k řídicímu panelu](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Vyberte roli, která představuje oprávnění pro udělení, jako je například **Přispěvatel**.

1. Vyberte uživatele nebo skupinu, které chcete přiřadit k roli. Pokud v seznamu nevidíte uživatele nebo skupinu, které jste hledali, použijte vyhledávací pole. Seznam dostupných skupin závisí na skupinách, které jste vytvořili ve službě Active Directory.

1. Až dokončíte přidávání uživatelů nebo skupin, vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

* Seznam rolí najdete v tématu [předdefinované role Azure](../role-based-access-control/built-in-roles.md).
* Další informace o správě prostředků najdete v tématu [Správa prostředků Azure pomocí Azure Portal](../azure-resource-manager/management/manage-resources-portal.md).