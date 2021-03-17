---
title: Správa externího přístupu pomocí podmíněného přístupu Azure Active Directory
description: Použití Azure Active Directory zásad podmíněného přístupu k zabezpečení externího přístupu k prostředkům.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64209a4d9ca200c69783a4ae317b38beef8ded89
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222305"
---
# <a name="manage-external-access-with-conditional-access-policies"></a>Správa externího přístupu pomocí zásad podmíněného přístupu 

[Podmíněný přístup](../conditional-access/overview.md) je nástroj, který Azure AD používá k spojování signálů, vymáhání zásad a určení, jestli má mít uživatel povolený přístup k prostředkům. Podrobné informace o tom, jak vytvořit a používat zásady podmíněného přístupu (zásady podmíněného přístupu), najdete v tématu [Plánování nasazení podmíněného přístupu](../conditional-access/plan-conditional-access.md). 

![Diagram signálů a rozhodnutí podmíněného přístupu](media/secure-external-access//7-conditional-access-signals.png)



Tento článek popisuje použití zásad podmíněného přístupu u externích uživatelů a předpokládá, že nemáte přístup k funkcím pro [správu nároků](../governance/entitlement-management-overview.md) . Zásady podmíněného přístupu můžou být a použité společně s nároky na správu.

Dříve v této sadě dokumentů jste [vytvořili plán zabezpečení](3-secure-access-plan.md) , který je popsaný:

* Aplikace a prostředky mají stejné požadavky na zabezpečení a je možné je seskupit pro přístup.

* Požadavky na přihlášení pro externí uživatele.

Pomocí tohoto plánu vytvoříte zásady podmíněného přístupu pro externí přístup. 

> [!IMPORTANT]
> Vytvořte několik účtů externích uživatelských testů, abyste mohli testovat zásady, které vytvoříte, a teprve potom je použít u všech externích uživatelů.

## <a name="conditional-access-policies-for-external-access"></a>Zásady podmíněného přístupu pro externí přístup

Níže jsou uvedené osvědčené postupy týkající se řízení externího přístupu pomocí zásad podmíněného přístupu.

* Pokud nemůžete používat připojené organizace v rámci správy nároků, vytvořte skupinu zabezpečení Azure AD nebo skupinu Microsoft 365 pro každou organizaci partnera, se kterou pracujete. Přiřaďte všechny uživatele z tohoto partnera ke skupině. Tyto skupiny pak můžete použít v zásadách podmíněného přístupu.

* Vytvořte co nejvíce zásad podmíněného přístupu. U aplikací, které mají stejné požadavky na přístup, je přidejte do stejných zásad.  
‎ 
   > [!NOTE]
   > Zásady podmíněného přístupu se můžou vztahovat na maximálně 250 aplikací. Pokud mají více než 250 aplikací stejné požadavky na přístup, vytvořte duplicitní zásady. Zásada A bude platit pro aplikace 1-250, zásada B se bude vztahovat na aplikace 251-500 atd.

* Jasně pojmenujte zásady specifické pro externí přístup s konvencí pojmenování. Jedna konvence pojmenování je *ExternalAccess_actiontaken_AppGroup*. Například ExternalAccess_Block_FinanceApps.

## <a name="block-all-external-users-from-resources"></a>Blokování všech externích uživatelů z prostředků

Externím uživatelům můžete zablokovat přístup k určitým sadám prostředků se zásadami podmíněného přístupu. Jakmile určíte sadu prostředků, pro které chcete blokovat přístup, vytvořte zásadu.

Vytvoření zásady, která zablokuje přístup pro externí uživatele do sady aplikací:

1. Přejděte na **Azure Portal**, vyberte **Azure Active Directory**, vyberte **zabezpečení** a pak vyberte **podmíněný přístup**.

2. Vyberte **nové zásady** a zadejte **název**, například ExternalAccess_Block_FinanceApps

3. Vyberte **Uživatelé a skupiny** s. Na kartě zahrnout zvolte **Vybrat uživatele a skupiny** a pak vyberte **Všichni hosté a externí uživatelé**. 

4. Vyberte **vyloučit** a zadejte skupiny správců a všechny účty nouzového přístupu (pro přerušení).

5. Vyberte **cloudové aplikace nebo akce**, zvolte **vybrat aplikace**, vyberte všechny aplikace, pro které chcete blokovat externí přístup, a pak zvolte **Vybrat**.

6. Vyberte **podmínky**, vyberte **umístění**, v části konfigurace vyberte **Ano** a vyberte **libovolné umístění**.

7. V části řízení přístupu vyberte **udělit**, změňte přepínač na **blokovat** a zvolte **Vybrat**.

8. Zajistěte, aby bylo nastavení povolit zásady nastaveno na možnost **pouze sestava**, a pak vyberte **vytvořit**.

## <a name="block-external-access-to-all-except-specific-external-users"></a>Zablokovat externí přístup všem výjimkám specifických externích uživatelů

Může nastat situace, kdy budete chtít externí uživatele zablokovat s výjimkou konkrétní skupiny. Můžete například chtít blokovat všechny externí uživatele kromě těch, kteří pracují s finančním týmem z finančních aplikací. Použijte následující postup:

1. Vytvořte skupinu zabezpečení, která bude obsahovat externí uživatele, kteří mají přístup ke skupině Finance.

2. Postupujte podle kroků 1-3 v zablokování externího přístupu z výše uvedených prostředků.

3. V kroku 4 přidejte z finančních aplikací skupinu zabezpečení, kterou chcete vyloučit ze zablokované.

4. Proveďte zbývající kroky.

## <a name="implement-conditional-access"></a>Implementace podmíněného přístupu

Mnoho běžných zásad podmíněného přístupu je dokumentováno. Podívejte se na následující informace, které můžete přizpůsobit pro externí uživatele.

* [Vyžadovat Multi-Factor Authentication pro všechny uživatele](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)

* [Podmíněný přístup podle rizika uživatelů](../conditional-access/howto-conditional-access-policy-risk-user.md)

* [Vyžadovat Multi-Factor Authentication pro přístup z nedůvěryhodných sítí](../conditional-access/untrusted-networks.md) 

* [Vyžadovat použití podmínek použití](../conditional-access/terms-of-use.md)

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích o zabezpečení externího přístupu k prostředkům. Doporučujeme, abyste provedli akce v uvedeném pořadí.

1. [Určení požadovaného stav zabezpečení pro externí přístup](1-secure-access-posture.md)

2. [Zjistit aktuální stav](2-secure-access-current-state.md)

3. [Vytvoření plánu zásad správného řízení](3-secure-access-plan.md)

4. [Použití skupin pro zabezpečení](4-secure-access-groups.md)

5. [Přechod do Azure AD B2B](5-secure-access-b2b.md)

6. [Zabezpečený přístup se správou oprávnění](6-secure-access-entitlement-managment.md)

7. [Zabezpečený přístup pomocí zásad podmíněného přístupu](7-secure-access-conditional-access.md) (jste tady)

8. [Zabezpečený přístup s popisky citlivosti](8-secure-access-sensitivity-labels.md)

9. [Zabezpečený přístup k Microsoft teams, OneDrivu a SharePointu](9-secure-access-teams-sharepoint.md)
