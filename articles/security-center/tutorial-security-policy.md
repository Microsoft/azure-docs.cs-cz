---
title: Práce s bezpečnostními politikami | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak pracovat se zásadami zabezpečení v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: c98ae7c95ac3fc186786612dd3d8d8bd55fa816f
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024876"
---
# <a name="working-with-security-policies"></a>Práce se zásadami zabezpečení

Tento článek vysvětluje, jak jsou konfigurovány zásady zabezpečení a jak je zobrazit v Centru zabezpečení. 

## <a name="introduction-to-security-policies"></a>Úvod do zásad zabezpečení

Zásady zabezpečení definují požadovanou konfiguraci vašich úloh a pomáhají zajistit, že splňujete požadavky na zabezpečení vaší společnosti nebo regulačních orgánů.

Azure Security Center vydává svá doporučení zabezpečení na základě vybraných zásad. Zásady Centra zabezpečení jsou založené na iniciativách zásad vytvořených v zásadách Azure. [Zásady Azure](../governance/policy/overview.md) můžete použít ke správě zásad a k nastavení zásad napříč skupinami pro správu a napříč několika předplatnými.

Centrum zabezpečení nabízí následující možnosti pro práci se zásadami zabezpečení:

* **Zobrazení a úprava předdefinovaných výchozích zásad** – Pokud povolíte Centrum zabezpečení, bude předdefinovaná iniciativa s názvem VÝCHOZÍ ASC automaticky přiřazena všem předplatným registrovaným v Centru zabezpečení (úrovně Free nebo Standard). Chcete-li tuto iniciativu přizpůsobit, můžete povolit nebo zakázat jednotlivé zásady v ní. Podívejte se na seznam [integrovaných zásad zabezpečení,](security-center-policy-definitions.md) abyste pochopili možnosti, které jsou k dispozici předem.

* **Přidejte vlastní zásady** – Pokud chcete přizpůsobit iniciativy zabezpečení použité pro vaše předplatné, můžete tak učinit v centru zabezpečení. Pak obdržíte doporučení, pokud vaše počítače nedodržují zásady, které vytvoříte. Pokyny k vytváření a přiřazování vlastních zásad naleznete [v tématu Použití vlastních zásad zabezpečení](custom-security-policies.md).

* **Přidejte zásady dodržování předpisů** – řídicí panel dodržování předpisů centra zabezpečení zobrazuje stav všech hodnocení ve vašem prostředí v kontextu konkrétního standardu nebo regulace (například Azure CIS, NIST SP 800-53 R4, SWIFT CSP CSCF-v2020). Další informace naleznete v [tématu Zlepšení dodržování předpisů](security-center-compliance-dashboard.md).


## <a name="managing-your-security-policies"></a>Správa zásad zabezpečení

Zobrazení zásad zabezpečení ve službě Security Center:

1. Na řídicím panelu **Centra zabezpečení** vyberte **zásady zabezpečení**.

    ![Okno Správa zásad](./media/security-center-policies/security-center-policy-mgt.png)

   Na obrazovce **Správa zásad** uvidíte počet skupin pro správu, předplatných a pracovních prostorů a také strukturu skupinpro správu.

1. Vyberte předplatné nebo skupinu pro správu, jejíž zásady chcete zobrazit.

1. Zobrazí se stránka zásad zabezpečení pro toto předplatné nebo skupinu pro správu. Zobrazuje dostupné a přiřazené zásady.

   ![obrazovka zásad](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Pokud je vedle výchozí zásady popisek "MG Inherited", znamená to, že zásada byla přiřazena skupině pro správu a zděděna podle předplatného, které si prohlížíte.


1. Vyberte si z dostupných možností na této stránce:

    1. Chcete-li pracovat s oborovými zásadami, vyberte **Přidat další standardy**. Další informace naleznete v [tématu Aktualizace dynamických balíčků dodržování předpisů](update-regulatory-compliance-packages.md).

    1. Chcete-li přiřadit a spravovat vlastní iniciativy, vyberte **Přidat vlastní iniciativy**. Další informace naleznete [v tématu Použití vlastních zásad zabezpečení](custom-security-policies.md).

    1. Chcete-li zobrazit a upravit výchozí zásady, vyberte **Zobrazit účinnou zásadu** a postupujte podle níže uvedeného. 

       ![obrazovka zásad](./media/security-center-policies/policy-screen.png)
       
       Tato obrazovka **zásad zabezpečení** odráží akci přijatou zásadami přiřazenými ve vybrané mase předplatného nebo pro správu.
       
       * Pomocí odkazů v horní části otevřete **přiřazení** zásad, které platí pro předplatné nebo skupinu pro správu. Tyto odkazy umožňují přístup k přiřazení a upravit nebo zakázat zásady. Pokud například zjistíte, že konkrétní přiřazení zásad účinně odepře ochranu koncového bodu, použijte odkaz k úpravě nebo zakázání zásady.
       
       * V seznamu zásad se zobrazí efektivní uplatnění zásad ve vaší skupině předplatného nebo správy. Nastavení každé zásady, které se vztahují k oboru jsou vzaty v úvahu a kumulativní výsledek akcí přijatých zásady se zobrazí. Například pokud v jednom přiřazení zásady je zakázáno, ale v jiném je nastavena na AuditIfNotExist, pak kumulativní efekt použije AuditIfNotExist. Aktivnější účinek má vždy přednost.
       
       * Efekt zásad může být: Append, Audit, AuditIfNotExists, Deny, DeployIfNotExists, Disabled. Další informace o použití efektů naleznete v [tématu Principy efektů zásad](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > Při zobrazení přiřazených zásad můžete zobrazit více přiřazení a uvidíte, jak je každé přiřazení nakonfigurováno samostatně.


## <a name="who-can-edit-security-policies"></a>Kdo může upravovat zásady zabezpečení?

Zásady zabezpečení můžete upravit prostřednictvím portálu zásad Azure, prostřednictvím rozhraní REST API nebo pomocí prostředí Windows PowerShell.

Security Center používá řízení přístupu na základě rolí (RBAC). To poskytuje předdefinované role, které se dají v Azure přiřadit uživatelům, skupinám a službám. Když uživatelé otevřou Centrum zabezpečení, zobrazí se jim pouze informace související s prostředky, ke kterým mají přístup. To znamená, že uživatelům je přiřazena role *vlastníka*, *přispěvatele*nebo *čtenáře* k předplatnému prostředku. Kromě těchto rolí existují dvě konkrétní role Centra zabezpečení:

- **Čtečka zabezpečení**: Mají práva k zobrazení Centra zabezpečení, která obsahují doporučení, výstrahy, zásady a stav, ale nemohou provádět změny.
- **Správce zabezpečení**: Mají stejná práva k zobrazení jako *čtečka zabezpečení*a mohou také aktualizovat zásady zabezpečení a zrušit doporučení a výstrahy.


## <a name="disable-security-policies"></a>Zakázání zásad zabezpečení
Pokud výchozí zásady zabezpečení generují doporučení, které není relevantní pro vaše prostředí, můžete jej zastavit zakázáním definice zásady, která odešle doporučení.
Další informace o doporučeních naleznete v [tématu Správa doporučení zabezpečení](security-center-recommendations.md).

1. V Centru zabezpečení vyberte v části **Zásady & dodržování předpisů** **zásady zabezpečení**.

   ![správa politik](./media/tutorial-security-policy/policy-management.png)

2. Vyberte předplatné nebo skupinu pro správu, pro kterou chcete zakázat doporučení.

   > [!NOTE]
   > Pamatujte, že skupina pro správu uplatňuje zásady na svá předplatná. Pokud tedy zakážete zásady předplatného, přičemž toto předplatné patří do skupiny pro správu, která pořád používá stejné zásady, budete doporučení k zásadám nadále dostávat. Tyto zásady budou nadále uplatňovány z úrovně správy a doporučení se budou pořád generovat.

1. Vyberte **možnost Zobrazit účinnou zásadu**.

   ![zakázat zásady](./media/tutorial-security-policy/view-effective-policy.png)

1. Vyberte přiřazenou zásadu.

   ![zakázat zásady](./media/tutorial-security-policy/security-policy.png)

1. V části **PARAMETRY** vyhledejte zásadu, která vyvolá doporučení, které chcete zakázat, a v rozevíracím seznamu vyberte **Zakázáno.**

   ![zakázat zásady](./media/tutorial-security-policy/disable-policy.png)

1. Vyberte **Uložit**.

   > [!NOTE]
   > Změny zásad zakázání se mohou projevit až 12 hodin.



## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli o zásadách zabezpečení. Související informace naleznete v následujících článcích:

* Pokyny k nastavení zásad pomocí PowerShellu najdete v [tématu Úvodní příručka: Vytvoření přiřazení zásad k identifikaci nekompatibilních prostředků pomocí modulu Azure PowerShell.](../governance/policy/assign-policy-powershell.md)

* Pokyny k úpravám zásad zabezpečení v zásadách Azure najdete v tématu [Vytváření a správa zásad k vynucení dodržování předpisů](../governance/policy/tutorials/create-and-manage.md).

* Pokyny, jak nastavit zásady napříč předplatnými nebo skupinami pro správu pomocí zásad Azure, najdete v [tématu Co je zásady Azure?](../governance/policy/overview.md)