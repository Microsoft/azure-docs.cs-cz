---
title: Správa přístupu pro externí uživatele pomocí RBAC v Azure | Dokumentace Microsoftu
description: Zjistěte, jak spravovat přístup pro uživatele mimo organizaci, která používá řízení přístupu na základě role (RBAC) v Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 58108bd2851050e96df1b5453ce96856374b7163
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437031"
---
# <a name="manage-access-for-external-users-using-rbac"></a>Správa přístupu pro externí uživatele pomocí RBAC

Řízení přístupu na základě role (RBAC) umožňuje lepší zabezpečení správy pro velké organizace a pro SMB práce s externími spolupracovníky, dodavatelům nebo freelancers, kteří potřebují přístup ke konkrétním prostředkům ve vašem prostředí, ale ne nutně celý všechny obory, týkající se účtování nebo infrastruktury. RBAC umožňuje flexibilitu vlastnící jedno předplatné Azure, které spravuje správce účtu (role Správce služby na úrovni předplatného) a pro práci v rámci stejného předplatného, ale nemají žádné oprávnění pro správu pro něj pozvali více uživatelů .

> [!NOTE]
> Předplatná Office 365 nebo licence Azure Active Directory (například: přístup ke službě Azure Active Directory) zajištěného z center nejde použít pro pomocí RBAC správce Office 365.

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Přiřadíte role RBAC v oboru předplatného

Existují dva běžné příklady při RBAC je použít (to však není omezeno na):

* Ke správě určitých prostředků nebo předplatného celý s externím uživatelům z organizace pozvaný (není součástí uživatele správce tenanta Azure Active Directory)
* Práce s uživateli uvnitř organizace (jsou součástí tenanta Azure Active Directory uživatele), ale součást různými týmy a skupiny, které potřebují granulární přístup pro celé předplatné nebo pro určité skupiny prostředků nebo prostředek obory v prostředí

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Udělení přístupu na úrovni předplatného pro uživatele mimo službu Azure Active Directory

Role RBAC lze udělit pouze **vlastníky** předplatného. Proto správce musíte být přihlášení jako uživatel, který má tuto roli předem přiřazenou nebo vytvoření předplatného Azure.

Na webu Azure Portal vyberte po přihlášení jako správce "Předplatné" a vyberte možnost použít.
![okno předplatné na webu Azure portal](./media/role-assignments-external-users/0.png) ve výchozím nastavení, pokud uživatel s rolí správce koupil předplatné Azure, uživateli se zobrazí jako **správce účtu**, to se role předplatného. Další informace o rolích předplatné Azure, najdete v části [přidání nebo změna role Správce služby Azure, které spravují předplatné nebo služby](../billing/billing-add-change-azure-subscription-administrator.md).

V tomto příkladu, uživateli "alflanigan@outlook.com" je **vlastníka** "Bezplatné zkušební verze" tenant "Výchozí tenanta Azure" předplatného v AAD. Jelikož tento uživatel je Tvůrce předplatné Azure s počáteční Account Microsoft "Aplikace Outlook" (Account Microsoft = Outlook, Live atd.) budou mít výchozí název domény pro všechny uživatele v tomto tenantovi přidán **"\@ alflaniganuoutlook.onmicrosoft.com"**. Standardně je vytvořen syntaxe nové domény sestavení název uživatelské jméno a doménu uživatele, který vytvořil tenanta a přidáním rozšíření **". onmicrosoft.com"**.
Navíc uživatelé můžou přihlásit se pomocí vlastního názvu domény v tenantovi po přidání a ověření pro nového klienta. Další informace o tom, jak ověřit vlastní název domény v tenantovi Azure Active Directory najdete v tématu [přidání vlastního názvu domény do adresáře služby](/active-directory/active-directory-add-domain).

V tomto příkladu adresáři "Výchozímu tenantu Azure" obsahuje pouze uživatele s názvem domény "\@alflanigan.onmicrosoft.com".

Po výběru předplatného, musíte kliknout na uživatele s rolí správce **řízení přístupu (IAM)** a potom **přidat novou roli**.

![Funkce IAM řízení přístupu na webu Azure portal](./media/role-assignments-external-users/1.png)

![Přidání nového uživatele v IAM funkce řízení přístupu na webu Azure portal](./media/role-assignments-external-users/2.png)

Dalším krokem je vybrat roli, kterou chcete přiřadit a uživatel, kterému se přiřadí RBAC role. V **Role** rozevírací nabídka správce uživateli se zobrazí pouze předdefinované role RBAC, které jsou dostupné v Azure. Podrobnější vysvětlení jednotlivých rolí a jejich přiřaditelnými obory, najdete v článku [předdefinované role](built-in-roles.md).

Uživatele s rolí správce je pak potřeba přidat e-mailovou adresu externího uživatele. Chování je očekávané pro externí uživatele není uveden v existujícího tenanta. Po externí uživatel byl pozván, že se nebude zobrazovat v rámci **předplatná > řízení přístupu (IAM)** s aktuálním uživateli, které momentálně nejsou přiřazené roli RBAC v oboru předplatného.

![Přidejte oprávnění do nové role RBAC](./media/role-assignments-external-users/3.png)

![seznam rolí RBAC na úrovni předplatného](./media/role-assignments-external-users/4.png)

Uživatel "chessercarlton@gmail.com" má být pozvánku **vlastníka** pro předplatné "Bezplatné zkušební verze". Po odeslání e-mailové pozvánce, obdrží externího uživatele potvrzení e-mailu s odkazem k aktivaci.
![e-mailové pozvánky pro RBAC role](./media/role-assignments-external-users/5.png)

Jsou externí vůči organizaci, nový uživatel a nemá žádné atributy existující v adresáři "Výchozímu tenantu Azure". Budou vytvořeny po externí uživatel udělil souhlas mají být zaznamenány do adresáře, který je přidružený k předplatnému byl přiřazen k roli.

![e-mailovou pozvánku zprávu pro RBAC role](./media/role-assignments-external-users/6.png)

Zobrazuje externí uživatele v tenantovi Azure Active Directory od této chvíle jako externího uživatele, což lze zobrazit na webu Azure Portal.

![uživatelé okno azure active directory Azure portal](./media/role-assignments-external-users/7.png)

V **uživatelé** zobrazení, externí uživatele můžete rozpoznat podle typu jinou ikonu na webu Azure Portal.

Ale udělení **vlastníka** nebo **Přispěvatel** přístup k externím uživatelem na **předplatné** oboru, neumožňuje přístup k adresáři uživatele správce, není-li **Globálního správce** umožňuje. Ve vlastnosti uživatele **typ uživatele**, která má dvě společné parametry, **člen** a **hosta** lze identifikovat. Člen je uživatel, který je registrován v adresáři hosta je uživatele pozvat do adresáře z externího zdroje. Další informace najdete v tématu [jak správci služby Azure Active Directory přidat uživatele spolupráce B2B](../active-directory/active-directory-b2b-admin-add-users.md).

> [!NOTE]
> Ujistěte se, že po zadání přihlašovacích údajů na portálu, externí uživatel vybere na správný adresář pro přihlášení k aplikaci. Stejný uživatel můžete mít přístup k více adresářů můžete kliknutím na uživatelské jméno v pravém horním na webu Azure Portal vyberte jednu z nich a pak zvolte příslušného adresáře z rozevíracího seznamu.

Při zachování Host v adresáři, externí uživatel může spravovat všechny prostředky pro předplatné Azure, ale nemůže získat přístup k adresáři.

![omezit přístup pouze k azure active directory a Azure portal](./media/role-assignments-external-users/9.png)

Azure Active Directory a předplatným Azure nemají vztah podřízený nadřazený stejně jako ostatní prostředky Azure (Příklad: virtuální počítače, virtuální sítě, webových aplikací, úložiště atd.) mít s předplatným Azure. Všechny tyto je vytvořen, spravované a účtují v rámci předplatného Azure předplatné Azure se používá ke správě přístupu k adresáři služby Azure. Další informace najdete v tématu [předplatné jak Azure má vztah k Azure AD](/active-directory/active-directory-how-subscriptions-associated-directory).

Ze všech předdefinované role RBAC **vlastníka** a **Přispěvatel** nabízejí úplné správy přístup ke všem prostředkům v prostředí, rozdíl, že se přispěvatelem nelze vytvářet a odstraňovat nové role RBAC . Předdefinované role, jako jsou **Přispěvatel virtuálních počítačů** nabízejí úplné řízení přístup jenom k prostředkům označen názvem, bez ohledu na to **skupiny prostředků** jsou vytvářeny do.

Přiřazení předdefinované role RBAC **Přispěvatel virtuálních počítačů** na úrovni předplatného, znamená to, že uživatel s rolí:

* Můžete zobrazit všechny virtuální počítače bez ohledu na jejich data nasazení a skupiny prostředků, které jsou součástí
* Má úplná správu přístupu k virtuálním počítačům v rámci předplatného
* Nelze zobrazit další typy prostředků v předplatném
* Nejde použít změny z hlediska fakturace

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Přiřazení předdefinované role RBAC pro externí uživatele

Pro jiný scénář v tomto testu, externí uživatele "alflanigan@gmail.com" se přidá jako **Přispěvatel virtuálních počítačů**.

![Předdefinovaná role Přispěvatel virtuálních počítačů](./media/role-assignments-external-users/11.png)

Normálního chování pro tuto externí uživatel s touto předdefinovaných rolí je vidět a spravovat jenom virtuální počítače a jejich sousední prostředky Resource Manageru pouze nezbytné při nasazování. Návrh nabízí omezené pracovníci v těchto rolích přístup pouze k jejich příslušné prostředky vytvořené v rámci webu Azure portal.

![Přehled role Přispěvatel virtuálních počítačů na webu Azure portal](./media/role-assignments-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Udělení přístupu na úrovni předplatného pro uživatele ve stejném adresáři

Tok procesu je stejný jako přidání externího uživatele, i z pohledu správce, udělíte roli RBAC, stejně jako uživatel udělením přístupu k roli. Rozdíl spočívá v tom, že pozvaného uživatele nebude přijímat žádné e-mailové pozvánky jako všechny obory prostředků v rámci předplatného bude dostupný na řídicím panelu po přihlášení.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Přiřadíte role RBAC v oboru skupiny prostředků

Přiřazení roli RBAC v **skupiny prostředků** obor má stejné proces pro přiřazení role na úrovni předplatného, pro oba typy uživatelů – externí nebo interní (součástí stejného adresáře). Uživatelé, kteří mají přiřazenou roli RBAC je chcete zobrazit ve svém prostředí pouze pro skupiny prostředků byl klientům přiřazen přístup z **skupiny prostředků** ikonu na webu Azure Portal.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Přiřadíte role RBAC v oboru prostředků

Přiřazení roli RBAC v oboru prostředků v Azure má stejné proces pro přiřazení role na úrovni předplatného nebo na úrovni skupiny prostředků podle stejného pracovního postupu pro oba scénáře. Znovu, můžete uživatele, kteří mají přiřazenou roli RBAC se zobrazí pouze položky, které se mají přiřazený přístup k buď **všechny prostředky** kartu nebo přímo v řídicím panelu.

Důležitou pro RBAC, jak v oboru skupiny prostředků nebo prostředek rozsahu je pro uživatele, ujistěte se, že se přihlásit ke správnému adresáři.

![adresář přihlášení na webu Azure portal](./media/role-assignments-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Přiřadíte role RBAC pro skupiny služby Azure Active Directory

Všechny scénáře pomocí RBAC na tří různých oborů v Azure nabízí oprávnění aplikace, nasazení a správě různým prostředkům jako přiřazeného uživatele bez nutnosti spravovat osobní předplatného. Bez ohledu na to přiřazení RBAC role pro předplatné, skupinu prostředků nebo prostředek oboru, všechny prostředky vytvořené dále přiřazení uživatelé se účtují v rámci jednoho předplatného Azure, kde uživatelé mají přístup k. Díky tomu uživatelé, kteří mají oprávnění správce pro toto předplatné celý Azure billing má úplný přehled o spotřebě, bez ohledu na to kdo spravuje prostředky.

Pro větší organizace můžete použít role RBAC stejně jako u skupin Azure Active Directory vzhledem k tomu, že uživatel s rolí správce chce granulární přístup pro týmy nebo celé oddělení, nikoli jednotlivě pro každého uživatele, takže vzhledem k tomu perspektivy je velmi čas a správu efektivní možnost. Pro ilustraci v tomto příkladu **Přispěvatel** role je přidaný do jedné ze skupin v tenantovi na úrovni předplatného.

![Přidání role RBAC pro skupiny AAD](./media/role-assignments-external-users/14.png)

Tyto skupiny jsou skupiny zabezpečení, které jsou zřízené a spravují pouze v rámci Azure Active Directory.

