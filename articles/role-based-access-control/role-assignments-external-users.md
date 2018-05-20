---
title: Spravovat přiřazení rolí pro externí uživatele v Azure | Microsoft Docs
description: Správa řízení přístupu na základě role (RBAC) pro uživatele mimo organizaci v Azure
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 084594b637f813c110e4e0b2e9df2b9103d58efc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="manage-role-assignments-for-external-users"></a>Správa přiřazení rolí pro externí uživatele

Řízení přístupu na základě role (RBAC) umožňuje lepší zabezpečení správy pro velké organizace a pro SMB práce s externími spolupracovníky, dodavatele nebo freelancers, kteří potřebují přístup ke konkrétním prostředkům ve vašem prostředí, ale nemusí nutně prokázat na celou infrastruktura nebo všechny obory, týkající se fakturace. RBAC umožňuje flexibilitu vlastnící jedno předplatné, které spravuje správce účtu (role Správce služby na úrovni předplatného) a pro práci v rámci stejného předplatného, ale bez jakékoli práva správce pro ni pozvali více uživatelů .

> [!NOTE]
> Předplatná Office 365 nebo Azure Active Directory licence (například: přístup k Azure Active Directory) zajištěného z správu Office 365 není pro použití RBAC kvalifikaci center.

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Přiřadit role RBAC v oboru předplatného

Existují dvě běžných příkladů, když RBAC je použít (ale mimo jiné):

* Že externí uživatelé organizací pozvat (není součástí uživatele správce klienta Azure Active Directory) ke správě určitých prostředků nebo celý předplatného
* Práce s uživateli uvnitř organizace (jsou součástí klienta Azure Active Directory uživatele), ale součást různé týmy nebo skupiny, které potřebují granulární přístup k celé předplatné nebo pro určité skupiny prostředků nebo prostředek oborů v prostředí

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Udělení přístupu na úrovni předplatného pro uživatele mimo Azure Active Directory

Role RBAC lze udělit pouze systémem **vlastníky** předplatného. Proto správce, musíte být přihlášení jako uživatel, který má tato role předběžně zařazená nebo vytvořil předplatné Azure.

Z portálu Azure vyberte po přihlášení jako správce "Odběry" a vyberte požadované.
![okno odběru na portálu Azure](./media/role-assignments-external-users/0.png) ve výchozím nastavení, pokud uživatel s oprávněními správce koupil předplatné Azure, uživateli se zobrazí jako **správce účtu**, tím se roli předplatného. Další informace o rolích předplatného Azure najdete v tématu [přidání nebo změna role Správce služby Azure, které spravují předplatné nebo služby](../billing/billing-add-change-azure-subscription-administrator.md).

V tomto příkladu uživatel "alflanigan@outlook.com" je **vlastníka** z "Bezplatnou zkušební verzi" předplatné v AAD klienta "Výchozí klienta Azure". Vzhledem k tomu, že je tento uživatel Tvůrce předplatného Azure se počáteční Account Microsoft "Outlook" (Account Microsoft = Outlook, Live atd.) bude výchozí název domény pro všechny uživatele přidán do tohoto klienta **"@alflaniganuoutlook.onmicrosoft.com"**. Návrh syntaxe nové domény je tvořen uvedení společně název uživatelské jméno a doménu uživatele, který vytvořil klienta a přidání rozšíření **". onmicrosoft.com"**.
Kromě toho uživatelé mohou přihlásit pomocí vlastního názvu domény v klientovi po přidání a ověření pro nového klienta. Další informace o tom, jak ověřit vlastní název domény v klienta služby Azure Active Directory najdete v tématu [přidání vlastního názvu domény do adresáře](/active-directory/active-directory-add-domain).

V tomto příkladu adresáři "Výchozí klient Azure" obsahuje pouze uživatele s názvem domény "@alflanigan.onmicrosoft.com".

Po výběru předplatného, musíte kliknout na uživatel s oprávněními správce **řízení přístupu (IAM)** a potom **přidat novou roli**.

![Funkce IAM řízení přístupu na portálu Azure](./media/role-assignments-external-users/1.png)

![Přidání nového uživatele v IAM funkce řízení přístupu na portálu Azure](./media/role-assignments-external-users/2.png)

Dalším krokem je vybrat role, kterou chcete přiřadit a uživatel, kterému se přiřadí RBAC role. V **Role** rozevírací nabídce správce uživateli se zobrazí pouze integrované RBAC role, které jsou dostupné v Azure. Podrobné vysvětlení jednotlivých rolí a jejich přiřaditelnými obory, najdete v části [předdefinované role pro řízení přístupu](built-in-roles.md).

Pak musí přidat e-mailovou adresu externího uživatele, uživatel s oprávněními správce. Očekávané chování je externí uživatel není zobrazena v existujícího klienta. Po pozval externí uživatel zadá budou viditelné v rámci **odběry > řízení přístupu (IAM)** s aktuálního uživatele, kteří jsou přiřazeny role RBAC v obor předplatného.

![Přidejte oprávnění do nové role RBAC](./media/role-assignments-external-users/3.png)

![seznam rolí RBAC na úrovni předplatného](./media/role-assignments-external-users/4.png)

Uživatel "chessercarlton@gmail.com" pozval být **vlastníka** pro předplatné "Bezplatnou zkušební verzi". Po odeslání pozvánky, obdrží externího uživatele potvrzení e-mailu s odkazem k aktivaci.
![e-mailová pozvánka pro RBAC role](./media/role-assignments-external-users/5.png)

Probíhá mimo organizaci, nový uživatel nemá žádné existující atributy v adresáři "Výchozí klient Azure". Budou vytvořeny po externího uživatele poskytl souhlas zaznamenávají v adresáři, který je přidružený k předplatnému byl přiřazen k roli.

![e-mailové zprávě pozvánky pro RBAC role](./media/role-assignments-external-users/6.png)

Zobrazuje externí uživatel v klientovi Azure Active Directory od této chvíle jako externí uživatel a tato lze zobrazit na portálu Azure.

![uživatelé okno azure active directory portálu Azure](./media/role-assignments-external-users/7.png)

V **uživatelé** zobrazení, externí uživatele umožňuje rozpoznat typ vlastní ikonu na portálu Azure.

Ale udělení **vlastníka** nebo **Přispěvatel** přístup k externím uživatelem v **předplatné** obor, neumožňuje přístup k adresáři uživatele správce, pokud **Globálního správce** to umožňuje. Ve vlastnosti uživatele **typ uživatele**, který má dvě společné parametry, **člen** a **hosta** lze identifikovat. Člen je uživatel, který je zaregistrován v adresáři, zatímco hosta je uživatel vyzván k adresáři z externího zdroje. Další informace najdete v tématu [jak správci Azure Active Directory přidat uživatele spolupráce B2B](../active-directory/active-directory-b2b-admin-add-users.md).

> [!NOTE]
> Ujistěte se, že po zadání přihlašovacích údajů na portálu, externí uživatel vybere správný adresář pro přihlášení k aplikaci. Stejný uživatel můžete mít přístup k více adresářů a můžete vybrat některý z nich kliknutím uživatelské jméno v vpravo nahoře na portálu Azure a potom z rozevíracího seznamu vyberte příslušného adresáře.

Při se hostovaného v adresáři, externího uživatele můžete spravovat všechny prostředky pro předplatné Azure, ale nemůže získat přístup k adresáři.

![přístup omezen na portálu Azure azure active directory](./media/role-assignments-external-users/9.png)

Azure Active Directory a předplatné Azure, nemají vztah nadřazený podřízený jako ostatní prostředky služby Azure (například: virtuálních počítačů, virtuálních sítí, webové aplikace, úložiště atd.) s předplatné Azure. Všechny pozdější vytvořit, spravovat a účtují pod předplatným Azure, zatímco předplatné služby Azure se používá ke správě přístupu ke službě Azure directory. Další informace najdete v tématu [předplatné jak Azure souvisí s Azure AD](/active-directory/active-directory-how-subscriptions-associated-directory).

Ze všech předdefinovaných rolí RBAC **vlastníka** a **Přispěvatel** nabízejí úplné správy přístup ke všem prostředkům v prostředí, rozdíl, že Přispěvatel nelze vytvářet a odstraňovat nové role RBAC . Mezi integrované role jako **Přispěvatel virtuálních počítačů** nabízejí úplné správy přístup jen k prostředkům uvádí název, bez ohledu na to **skupiny prostředků** během vytváření do.

Přiřazení předdefinované role RBAC **Přispěvatel virtuálních počítačů** na úrovni předplatného, znamená, že uživatel přiřazenou roli:

* Můžete zobrazit všechny virtuální počítače bez ohledu na to datum jejich nasazení a skupiny prostředků, které jsou součástí
* Má úplné správy přístup k virtuálním počítačům v rámci předplatného
* Nelze zobrazit u jiných typů prostředků v předplatném
* Všechny změny z hlediska fakturační nemůže pracovat.

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Předdefinovaná role RBAC přiřadit externího uživatele

Pro různé scénáře v tomto testu, externí uživatele "alflanigan@gmail.com" se přidá jako **Přispěvatel virtuálních počítačů**.

![předdefinované role Přispěvatel virtuálního počítače](./media/role-assignments-external-users/11.png)

Normální chování pro tento externí uživatele s tato předdefinovaná role je chcete zobrazit a spravovat pouze virtuální počítače a jejich přiléhající Resource Manager pouze prostředky potřebné při nasazování. Návrh nabízí tyto role omezený přístup jenom k jejich příslušné prostředky, které jsou vytvořené na portálu Azure.

![Přehled role Přispěvatel virtuálních počítačů na portálu Azure](./media/role-assignments-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Udělení přístupu na úrovni předplatného pro uživatele ve stejném adresáři

Tok procesu je stejný jako při přidávání externího uživatele, z pohledu správce udělení RBAC role, jakož i uživatele i udělení přístupu k roli. Rozdíl je, že pozvané uživatele neobdrží žádné pozvánek e-mailu jako všechny obory prostředků v rámci předplatného. bude k dispozici v řídicím panelu po přihlášení.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Přiřazení role RBAC v oboru skupiny prostředků

Přiřazení na role RBAC **skupiny prostředků** oboru má identické proces pro přiřazení role na úrovni předplatného, pro oba typy uživatelů - externí nebo interní (součást stejný adresář). Uživatelé, které jsou přiřazené RBAC role je zobrazíte ve svém prostředí pouze skupinu prostředků mají přiřazený přístup z **skupiny prostředků** ikonu na portálu Azure.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Přiřadit role RBAC v oboru prostředků

Přiřazení role RBAC v oboru prostředků v Azure má identické proces pro přiřazení role na úrovni předplatného nebo na úrovni skupiny prostředků, následující témže pracovním postupu pro oba scénáře. Znovu, můžete uživatele, kteří jsou přiřazeny RBAC role zobrazení pouze těch položek, které mají přiřazený přístup k, buď v **všechny prostředky** kartě nebo přímo v jejich řídicího panelu.

Důležitým aspektem pro RBAC jak v oboru skupiny prostředků nebo prostředek oboru je pro uživatele a ujistěte se, zda jste přihlášení k správném adresáři.

![přihlášení Directory na portálu Azure](./media/role-assignments-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Přiřazení role RBAC pro skupinu služby Azure Active Directory

Všechny scénáře pomocí RBAC na tři různé rozsahy v Azure nabízí oprávnění, nasazení a správě různé prostředky jako uživatel s přiřazenou bez nutnosti správy osobních předplatné. Bez ohledu na to je přiřazena RBAC role pro předplatné, skupinu prostředků nebo prostředek oboru, všechny prostředky, které jsou přiřazené uživatelé vytvořili na další se fakturují v rámci jednoho předplatného Azure, kde mají uživatelé přístup k. Tímto způsobem, uživatelů, kteří mají oprávnění správce pro toto předplatné celý Azure fakturace má úplný přehled o spotřebě, bez ohledu na to kdo spravuje prostředky.

Stejným způsobem jako pro skupiny Azure Active Directory s perspektivy, že uživatel s oprávněními správce chce zajistit granulární přístup pro týmy nebo celý oddělení, není jednotlivě pro každého uživatele, takže vzhledem k tomu lze použít pro větší organizace role RBAC jej jako velmi čas a správu efektivní možnost. Pro ilustraci v tomto příkladu **Přispěvatel** role je přidaný do jedné ze skupin v klientovi na úrovni předplatného.

![Přidání role RBAC pro skupiny AAD](./media/role-assignments-external-users/14.png)

Tyto skupiny jsou skupiny zabezpečení, které jsou zřizovat a spravovat pouze v rámci Azure Active Directory.

