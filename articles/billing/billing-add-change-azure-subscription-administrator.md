---
title: Přidání nebo změna role správce Azure předplatné | Microsoft Docs
description: Popisuje, jak přidat nebo změnit společné správce Azure, Správce služby a účet správce
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 09c2badf6116dd36add6cccc82486d7f5b8f8697
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34069135"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Přidat nebo změnit správce předplatného Azure

Správci předplatného služby Azure classic a Azure [řízení přístupu na základě Role (RBAC)](../role-based-access-control/overview.md) se dvěma systémy pro správu přístupu k prostředkům Azure:

* Role správce Classic předplatné nabízejí základní access managementu a zahrnují správce účtu, Správce služeb a Spolusprávci.
    * Při registraci nového předplatného Azure, je váš účet ve výchozím nastavení jako účet správce a Správce služby.
    * Spolusprávci mohou být přidány po přihlášení.
* RBAC je novější systém, který nabízí vyladění správy přístupu s mnoho předdefinovaných rolí, flexibilní oboru a vlastní role.
    * Uživatelé s pouze role RBAC a žádné role správce classic předplatné, ale nemůžete spravovat nasazení Azure classic.

K zajištění lepší řízení a zjednodušit správu přístupu, doporučujeme používat funkci RBAC pro všechny potřeby správy přístupu. Pokud je to možné doporučujeme změnit konfiguraci stávající zásady přístupu pomocí RBAC. 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>Přidat správce RBAC vlastníka pro přihlášení k odběru na portálu Azure 

Někdo přidat jako správce pro správu služby předplatného Azure, jim poskytněte roli vlastníka RBAC k předplatnému. Role vlastníka umožňuje spravovat prostředky v předplatném přiřazen a nemá oprávnění přístupu pro další odběry.

1. Navštivte [ **odběry** na portálu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Vyberte předplatné, které chcete udělit přístup.
3. Vyberte **přidat**  
   (Pokud chybí na tlačítko Přidat, nemáte oprávnění k přidání oprávnění.)
4. Vyberte **přístup k ovládacímu prvku (IAM)** v nabídce.
5. V **Role** vyberte **vlastníka**. 
6. V **přiřadit přístup** vyberte **uživatele Azure AD, skupinu nebo aplikaci**. 
7. V **vyberte** zadejte e-mailovou adresu uživatele, který chcete přidat jako vlastníka. Vyberte uživatele a pak vyberte **Uložit**.

    ![Snímek obrazovky zobrazující vybrané roli vlastníka](./media/billing-add-change-azure-subscription-administrator/add-role.png)

To dává uživatele úplný přístup ke všem prostředkům, včetně právo delegovat přístup k ostatním. Přístup na jiný rozsah, jako je skupina prostředků najdete v nabídce IAM pro tento obor. 

## <a name="add-or-change-co-administrator"></a>Přidat nebo změnit spolusprávcem

Jako správce a společné lze přidat pouze vlastníka. Ostatní uživatelé s rolí, například Přispěvatel a čtečky nelze přidat jako spolusprávce.

> [!TIP]
> Potřebujete pouze přidat účet "Vlastník" jako spolusprávce, pokud uživatel potřebuje ke správě nasazení Azure classic. Doporučujeme používat RBAC pro jiné účely.

1. Pokud jste to ještě neudělali, přidejte někdo jako vlastníka následující z výše uvedených pokynů.
2. **Klikněte pravým tlačítkem na** uživatel vlastníka jste právě přidali a pak vyberte **přidat jako spolusprávce**. Pokud se nezobrazí **přidat jako spolusprávce** možnost, aktualizujte stránku nebo zkuste jinou internetového prohlížeče. 

    ![Snímek obrazovky, který přidá společné správce](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    K odebrání oprávnění spolusprávcem **klikněte pravým tlačítkem na** uživatele "Společné správce" a pak vyberte **odebrat spolusprávcem**.

    ![Snímek obrazovky, který odebere společné správce](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Změnit správce služeb pro předplatné Azure

Pouze správce účtu může změnit správce služeb pro předplatné. Ve výchozím nastavení když se přihlásíte, Správce služby je stejný jako účet správce. Pokud správce služby se změní na jiný uživatel, správce účtu ztratí přístup k portálu Azure. Však správce účtu může vždy používat centra účtů změním Správce služeb samy na sebe.

1. Zajistěte, aby váš scénář je podporován kontrolou [limity pro změnu Správci služeb](#limits).
1. Přihlaste se k [centra účtů](https://account.windowsazure.com/subscriptions) jako správce účtu.
1. Vyberte předplatné.
1. Na pravé straně, vyberte **upravit podrobnosti o předplatném**.

    ![Snímek obrazovky tlačítka Upravit předplatného v centru účtů](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. V **Správce služeb** zadejte e-mailovou adresu z nového správce služby.

    ![Snímek obrazovky s poli, chcete-li změnit e-mailu Správce služby](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Omezení pro změnu Správci služeb

* Každé předplatné je spojeno s adresář služby Azure AD. Chcete-li najít adresář předplatné je spojeno s, přejděte na [ **odběry**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), pak vybrat odběr, který chcete zobrazit adresáři.
* Pokud jste přihlášení pomocí pracovního nebo školního účtu, můžete přidat další účty ve vaší organizaci jako správce služeb. Například abby@contoso.com můžete přidat bob@contoso.com jako správce služby, ale nemůžete přidat john@notcontoso.com Pokud john@notcontoso.com má přítomnosti v adresáři contoso.com. Uživatelé přihlášeni s použitím pracovní nebo školní účty můžete dále přidat Account Microsoft uživatele jako správce služeb.

  | Metoda přihlašování | Přidat uživatele Account Microsoft jako SA? | Přidat pracovní nebo školní účet v téže organizaci jako SA? | Přidat pracovní nebo školní účet v jiné organizaci, jako SA? |
  | --- | --- | --- | --- |
  |  Účet Microsoft |Ano |Ne |Ne |
  |  Pracovní nebo školní účet |Ano |Ano |Ne |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Změnit správce účtu pro předplatné Azure

Správce účtu je uživatel, který původně zaregistrovali do služby pro předplatné Azure a je odpovědná za fakturační vlastníka předplatného. Chcete-li změnit správce účtu předplatného, [přenos vlastnictví předplatného služby Azure na jiný účet](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**Nejste si jisti, který je správce účtu?** Postupujte následovně:

1. Navštivte [ **odběry** na portálu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Vyberte předplatné, které chcete kontrolovat, a pak hledejte v části **nastavení**.
1. Vyberte **vlastnosti**. Správce účtu předplatného se zobrazí v **správce účtu** pole.  

## <a name="types-of-classic-subscription-admins"></a>Typy správci classic předplatného

 Správce účtu, Správce služeb a spolusprávce jsou tři druhy rolí správců classic předplatného v Azure. Účet, který se používá k registraci do Azure bude automaticky nastavena jako účet správce a Správce služby. Potom můžete přidat další Spolusprávci. Následující tabulka popisuje přesný rozdíly mezi tyto tři role správce. 

> [!TIP]
> Pro lepší řízení a vyladění správy přístupu doporučujeme používat na základě Role v Azure řízení přístupu (RBAC), což umožňuje uživatelům, který se má přidat k více rolím. Další informace najdete v tématu [řízení přístupu na základě Role v Azure Active Directory](../role-based-access-control/overview.md).

| Klasický správce předplatného | Omezení | Popis |
| --- | --- | --- |
| Správce účtu (AA) |1 na účet Azure |Toto je uživatel, který se zaregistrovali do služby pro předplatné Azure a má oprávnění k přístupu [centra účtů](https://account.azure.com/Subscriptions) a provádět různé úlohy správy. Mezi ně patří schopnost vytvářet nové odběry, zrušit předplatné, změnit fakturace předplatného a změnit správce služeb. Vlastník fakturace předplatného je koncepčně, správce účtu. V RBAC není správce účtu přiřazenou roli.|
| Správce služeb (SA) |1 za předplatné Azure |Tato role je oprávnění ke správě služeb v [portál Azure](https://portal.azure.com). Ve výchozím nastavení pro nové předplatné je správce účtu také Správce služeb. V RBAC je zadána roli vlastníka pro správce služeb v předplatném oboru.|
| Spolusprávce (CA) |200 na předplatné |Tato role má stejná přístupová oprávnění jako správce služeb, ale nemůže změnit přidružení předplatných k adresářům Azure. V RBAC je uvedena role vlastníka Spolusprávcem v oboru předplatného.|

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Další informace o řízení přístupu k prostředkům a služby Active Directory

* Další informace o tom, jak je přístup k prostředkům řídí ve službě Microsoft Azure, najdete v části [Principy přístupu k prostředkům v Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).
* Další informace o službě Azure Active Directory najdete v tématu [asociování předplatných Azure se službou Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) a [přiřazení rolí správce v Azure Active Directory](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.
