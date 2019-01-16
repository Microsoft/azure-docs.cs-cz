---
title: Členství v dynamické skupině přidat odebírání uživatelů – kurz – Azure Active Directory
description: V tomto kurzu použijete skupiny s pravidly členství uživatelů k automatickému přidávání nebo odebírání uživatelů.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: tutorial
ms.date: 08/07/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 6efb649142f43a98ea08c99ae853de0fc0c8ea3c
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321258"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>Kurz: Přidání nebo odebrání členů skupiny automaticky

V Azure Active Directory (Azure AD) můžete automaticky přidávat uživatele do skupin zabezpečení nebo skupin Office 365 nebo je z nich odebírat, abyste to pokaždé nemuseli dělat ručně. Při každé změně jakýchkoli vlastností uživatele nebo zařízení Azure AD vyhodnotí všechna dynamická pravidla skupin ve vašem tenantovi a zjistí, jestli má změna přidat nebo odebrat členy.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření automaticky naplněné skupiny uživatelů typu host z konkrétní partnerské společnosti
> * Přiřazení licencí ke skupině pro funkce specifické pro partnera, ke kterým budou mít uživatelé typu host přístup
> * Bonus: Zabezpečení skupiny **Všichni uživatelé** odebráním uživatelů typu host, abyste členům mohli například udělit přístup k výhradně interním webům

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Tato funkce vyžaduje, abyste jako globální správce tenanta měli jednu licenci Azure AD Premium. Pokud ji nemáte, v Azure AD vyberte **Licence** > **Produkty** > **Vyzkoušet / koupit**.

Uživatelům, kteří mají být členy dynamických skupin, licence přiřazovat nemusíte. V tenantovi potřebujete pouze minimální počet dostupných licencí Azure AD Premium P1 pro pokrytí všech takových uživatelů. 

## <a name="create-a-group-of-guest-users"></a>Vytvoření skupiny uživatelů typu host

Nejprve vytvoříte skupinu pro uživatele typu host, kteří jsou všichni z jedné partnerské společnosti. Potřebují zvláštní licencování, takže je efektivnější pro tento účel vytvořit skupinu.

1. Přihlaste se k webu Azure Portal (https://portal.azure.com)) pomocí účtu, který má k tenantovi oprávnění globálního správce.
2. Vyberte **Azure Active Directory** > **Skupiny** > **Nová skupina**.
  ![výběr příkazu Nová skupina](./media/groups-dynamic-tutorial/new-group.png)
3. V okně **Skupina**:
  
  * Jako typ skupiny vyberte **Zabezpečení**.
  * Jako název a popis skupiny zadejte `Guest users Contoso`.
  * Změňte **Typ členství** na **Dynamický uživatel**.
  * Vyberte **Přidat dynamický dotaz**.
  
4. Vyberte **Pokročilé pravidlo** a do pole **Pokročilé pravidlo** zadejte `(user.userType -eq "Guest") -and (user.companyName -eq "Contoso")`.
5. Výběrem možnosti **Přidat dotaz** zavřete okno.
6. V okně **Skupina** výběrem možnosti **Vytvořit** vytvořte skupinu.

## <a name="assign-licenses"></a>Přiřazení licencí

Teď, když máte novou skupinu, můžete použít licence, které tito uživatelé z partnerské společnosti potřebují.

1. V Azure AD vyberte **Licence**, vyberte jednu nebo několik licencí a pak vyberte **Přiřadit**.
2. Vyberte **Uživatelé a skupiny**, vyberte skupinu **Uživatelé typu host – Contoso** a uložte provedené změny.
3. **Možnosti přiřazení** umožňují zapnout nebo vypnout plány služeb zahrnuté v licencích, které jste vybrali. Když provedete nějaké změny, nezapomeňte je uložit kliknutím na **OK**.
4. Přiřazení dokončíte tak, že v dolní části podokna **Přiřadit licenci** kliknete na **Přiřadit**.

## <a name="remove-guests-from-all-users-group"></a>Odebrání hostů ze skupiny Všichni uživatelé

Možná je vaším konečným správním plánem přiřadit všechny uživatele typu host k vlastním skupinám podle společnosti. Teď můžete také změnit skupinu **Všichni uživatelé** tak, aby byla vyhrazená pouze pro členy ve vašem tenantovi. Pak ji můžete použít k přiřazování aplikací a licencí specifických pro vaši domovskou organizaci.

   ![Změna skupiny Všichni uživatelé tak, aby byla pouze pro členy](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

**Odebrání skupiny uživatelů typu host**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu, který má k tenantovi oprávnění globálního správce.
2. Vyberte **Azure Active Directory** > **Skupiny**. Vyberte skupinu **Uživatelé typu host – Contoso**, vyberte tři tečky (...) a pak vyberte **Odstranit**. Když odstraníte skupinu, odeberou se všechny přiřazené licence.

**Obnovení skupiny Všichni uživatelé**
1. Vyberte **Azure Active Directory** > **Skupiny**. Výběrem názvu skupiny **Všichni uživatelé** otevřete skupinu.
1. Vyberte **Pravidla dynamického členství**, vymažte veškerý text pravidla a vyberte **Uložit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:
> [!div class="checklist"]
> * Vytvoření skupiny uživatelů typu host
> * Přiřazení licencí k nové skupině
> * Změna skupiny Všichni uživatelé tak, aby byla pouze pro členy

Přejděte k dalšímu článku, kde najdete další základní informace o licencování na základě skupin.
> [!div class="nextstepaction"]
> [Základy licencování na základě skupin](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



