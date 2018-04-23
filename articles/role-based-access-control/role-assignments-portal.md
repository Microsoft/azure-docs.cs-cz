---
title: Řízení přístupu na základě role na webu Azure Portal | Dokumentace Microsoftu
description: Začněte se správou přístupu pomocí řízení přístupu na základě rolí na portálu Azure. Oprávnění k prostředkům se přiřazují pomocí přiřazení rolí.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 35cb4be5c1ee24bba8026cbe66d2316753f9ed32
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-role-based-access-control-to-manage-access-to-your-azure-subscription-resources"></a>Použití řízení přístupu na základě rolí ke správě přístupu k prostředkům předplatného Azure
> [!div class="op_single_selector"]
> * [Správa přístupu podle uživatele nebo skupiny](role-assignments-users.md)
> * [Správa přístupu podle prostředku](role-assignments-portal.md)

Řízení přístupu na základě role v Azure umožňuje přesnou správu přístupu. Pomocí řízení přístupu na základě role v Azure můžete uživatelům poskytnout pouze takovou úroveň přístupu, kterou potřebují k provádění svých úloh. Tento článek vám pomůže nastavit a spustit řízení přístupu na základě role v Azure na portálu Azure. Další informace o tom, jak vám řízení přístupu na základě role v Azure pomůže spravovat přístup uživatelů najdete v článku [Co je řízení přístupu na základě role](overview.md).

V rámci každého předplatného můžete udělit až 2000 přiřazení rolí. 

## <a name="view-access"></a>Zobrazení informací o přístupu
Na [portálu Azure](https://portal.azure.com) můžete v hlavním okně sledovat, kdo má přístup k prostředku, skupině prostředků nebo předplatnému. Například byste se mohli chtít podívat, kdo má přístup k jedné ze skupin prostředků:

1. Na navigačním panelu vlevo vyberte **Skupiny prostředků**.  
    ![Skupiny prostředků – ikona](./media/role-assignments-portal/resourcegroups_icon.png)
2. V okně **Skupiny prostředků** vyberte název skupiny prostředků.
3. Z nabídky vlevo vyberte **Řízení přístupu (IAM)**.  
4. V okně Řízení přístupu se zobrazují všichni uživatelé, skupiny a aplikace, kterým byl udělen přístup ke skupině prostředků.  
   
    ![Snímek obrazovky s oknem uživatelé – zděděný a přiřazený přístup](./media/role-assignments-portal/view-access.png)

Všimněte si, že některé role mají obor nastavený na **Tento prostředek**, zatímco jiné mají obor **Zděděný** z jiného oboru. Přístup je ke skupině prostředků přiřazen přímo, nebo je zděděn od přiřazení nadřazeného předplatného.

> [!NOTE]
> Správci a pomocní správci v klasickém modelu předplatného jsou v novém modelu řízení přístupu na základě role v Azure  považováni za vlastníky.

## <a name="add-access"></a>Přidání přístupu
Přístup se uděluje z prostředku, skupiny prostředků nebo předplatného, které je v rozsahu platnosti přiřazení role.

1. V okně Řízení přístupu vyberte **Přidat**.  
2. V okně **Vyberte roli** vyberte roli, kterou chcete přiřadit.
3. Ve svém adresáři vyberte uživatele, skupinu nebo aplikaci, kterým chcete přiřadit přístup. V adresáři můžete vyhledávat pomocí zobrazovaných názvů, e-mailových adres a identifikátorů objektů.  
   
    ![Snímek obrazovky s oknem Přidat uživatele – vyhledávání](./media/role-assignments-portal/grant-access2.png)
4. Výběrem tlačítka **OK** vytvořte přiřazení. V automaticky otevřeném okně **Přidává se uživatel.** se zobrazuje průběh operace.  
    ![Ukazatel průběhu přidávání uživatele – snímek obrazovky](./media/role-assignments-portal/addinguser_popup.png)

Po úspěšném přiřazení role se přiřazení zobrazí v okně **Uživatelé**.

## <a name="remove-access"></a>Odebrání přístupu
1. Najeďte kurzorem na název přiřazení, které chcete odebrat. Vedle názvu se zobrazí zaškrtávací políčko.
2. Pomocí těchto zaškrtávacích políček vyberte jedno nebo více přiřazení rolí.
2. Vyberte **Odebrat**.  
3. Výběrem **Ano** odebrání potvrďte.

Zděděná přiřazení nelze odebrat. Pokud chcete odebrat zděděné přiřazení, je třeba to provést v oboru, ve kterém bylo přiřazení role vytvořené. Ve sloupci **Obor** vedle sloupce **Zděděný** je odkaz, pomocí kterého můžete přejít k prostředkům, kde byla role přiřazená. Poté přejděte k prostředku, který je zde uvedený, a odeberte přiřazení role v něm.

![Snímek obrazovky s oknem Uživatelé – neaktivní tlačítko odebrání u zděděného přístupu](./media/role-assignments-portal/remove-access2.png)

## <a name="other-tools-to-manage-access"></a>Další nástroje pro správu přístupu
Přiřazování rolí a správu přístupu pomocí řízení přístupu na základě role v Azure lze provádět i v jiných nástrojích než portálu Azure.  Další informace o požadavcích a základních postupech příkazů řízení přístupu na základě role v Azure najdete na následujících odkazech.

* [Azure PowerShell](role-assignments-powershell.md)
* [Rozhraní příkazového řádku Azure](role-assignments-cli.md)
* [REST API](role-assignments-rest.md)

## <a name="next-steps"></a>Další kroky
* [Vytvoření sestavy historie změn přístupu](change-history-report.md)
* Další informace najdete v článku [Vestavěné role řízení přístupu na základě role v Azure](built-in-roles.md).
* Definujte své [Vlastní role funkce řízení přístupu na základě role v Azure](custom-roles.md).

