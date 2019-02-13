---
title: Jak se zobrazují na přístupovém panelu aplikací | Dokumentace Microsoftu
description: Řešení potíží s důvod, proč se aplikace zobrazuje na přístupovém panelu
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: celested
ms.reviewr: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: a488c30f5b9438a91d2eae429cf65689af992dcb
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167078"
---
# <a name="how-applications-appear-on-the-access-panel"></a>Jak se zobrazují na přístupovém panelu aplikací

Přístupový Panel je webový portál, který umožňuje uživateli pomocí pracovního nebo školního účtu ve službě Azure Active Directory (Azure AD) k zobrazení a spuštění aplikace založené na cloudu, že správce Azure AD udělil jim přístup k. Tyto aplikace jsou konfigurovány jménem uživatele na portálu Azure AD. Správce můžete zřídit přímo aplikaci na tohoto uživatele nebo do skupiny je uživatel součástí výsledkem aplikace na přístupovém panelu uživatele.

## <a name="general-issues-to-check-first"></a>Obecné problémy a proveďte nejprve kontrolu

-   Pokud aplikace byla odebrána z uživatele nebo skupiny, které je uživatel členem, zkuste přihlásit a znovu do přístupového panelu uživatele po několika minutách zkontrolovat, jestli je aplikace odebrána.

-   Pokud licenci byla odebrána z uživatele nebo skupinu je uživatel že členem to může trvat dlouhou dobu, v závislosti na velikosti a složitosti skupiny změny má být provedeno. Povolit pro čas navíc před přihlášením na přístupovém panelu.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problémy související s přiřazením aplikace pro uživatele

Uživatel může zobrazit aplikace na přístupovém panelu protože měl byla dříve přiřazená k němu. Toto jsou některé způsoby, jak zkontrolovat:

-   [Zkontrolujte, zda uživatel je přiřazena aplikace](#check-if-a-user-is-assigned-to-the-application)

-   [Zkontrolujte, jestli je uživatel v rámci licence spojené s aplikací](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Zkontrolujte, zda uživatel je přiřazena aplikace

Pokud chcete zkontrolovat, zda uživatel je přiřazena aplikace, postupujte podle těchto kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

6.  **Hledání** pro název dané žádosti.

7.  Klikněte na tlačítko **uživatelů a skupin**.

8.  Zkontrolujte, zda uživatel je přiřazena aplikace.

  * Pokud chcete odebrat uživatele z aplikace, **klikněte na odpovídající řádek** uživatele a vyberte **odstranit**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Zkontrolujte, jestli je uživatel v rámci licence spojené s aplikací

Pokud chcete zkontrolovat přiřazené licence pro uživatele, postupujte takto:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Klikněte na tlačítko **licence** zobrazíte, která aktuálně licencí uživateli přiřadila.

   * Pokud uživateli přiřazena licence k Office, to umožňuje aplikacím Office první strany zobrazovat na přístupovém panelu uživatele.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problémy související se přiřazení aplikací do skupin

Uživatel může zobrazit aplikace na přístupovém panelu protože jsou součástí skupiny, které se přiřadila aplikace. Toto jsou některé způsoby, jak zkontrolovat:

-   [Kontrola členství uživatele ve skupinách](#check-a-users-group-memberships)

-   [Zkontrolujte, jestli je uživatel členem skupiny přiřazené k licenci](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Kontrola členství uživatele ve skupinách

Pokud chcete zkontrolovat členství, postupujte takto:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Klikněte na tlačítko **skupiny.**

8.  Zaškrtněte, pokud chcete zjistit, zda uživatel je součástí skupiny přiřazené k aplikaci.

   * Pokud chcete odebrat uživatele ze skupiny, **klikněte na odpovídající řádek** skupinu a vyberte odstranit.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Zkontrolujte, jestli je uživatel členem skupiny přiřazené k licenci

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **uživatelů a skupin** v navigační nabídce.

5.  Klikněte na tlačítko **všichni uživatelé**.

6.  **Hledání** pro uživatele, které vás zajímají a **klikněte na odpovídající řádek** k výběru.

7.  Klikněte na tlačítko **skupiny.**

8.  Klikněte na řádek konkrétní skupinu.

9.  Klikněte na tlačítko **licence** zobrazíte skupině licencí, které je přiřazeno k němu.

  * Pokud skupině je přiřazená licence k Office, tomu se některé aplikace Office první strany zobrazovat na přístupovém panelu uživatele.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Pokud tyto kroky pro řešení potíží není vyřešit problém

vytvořit lístek podpory s použitím následujících informací, pokud je k dispozici:

-   Chyba ID korelace

-   Hlavní název uživatele (uživatel e-mailová adresa)

-   ID tenanta

-   Typ prohlížeče

-   Vyvolá se časové pásmo a čas nebo časový rámec při chybě

-   Trasování Fiddler

## <a name="next-steps"></a>Další postup
[Správa aplikací pomocí Azure Active Directory](what-is-application-management.md)
