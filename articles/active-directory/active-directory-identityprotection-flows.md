---
title: Přihlášení vyskytne s Azure AD Identity Protection | Microsoft Docs
description: Poskytuje přehled činnost koncového uživatele při Identity Protection má omezeny nebo opraven uživatele nebo když služby Multi-Factor authentication je potřeba zásady.
services: active-directory
keywords: ochrany identit Azure active directory, cloud app discovery,. Správa aplikací, zabezpečení, rizik, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: de5bf637-75a7-4104-b6d8-03686372a319
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 43681ce665bec088472b5c33f64d52d3d9fda5a1
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Možnosti přihlášení s Azure AD Identity Protection
S Azure Active Directory Identity Protection můžete:

* vyžaduje, aby uživatel zaregistrovat pro službu Multi-Factor authentication
* zpracování rizikové přihlášení a ohrožení zabezpečení uživatelů

Odpověď systému na tyto problémy má dopad na možnosti přihlášení pro uživatele, protože právě přímo přihlášení tím, že poskytuje uživatelské jméno a heslo nebude už možné. Další kroky jsou požadovány pro uživatele bezpečně zpět do firmy.

Toto téma poskytuje přehled možností přihlašování uživatele pro všechny případy, které se můžou vyskytnout.

**Multi-Factor Authentication**

* Registrace služby Multi-Factor authentication

**Přihlášení v ohrožení**

* Obnovení rizikové přihlášení
* Rizikové přihlášení blokován
* Registrace služby Multi-Factor authentication během rizikové přihlášení

**Uživatel v ohrožení**

* Obnovení ohrožení bezpečnosti účtu
* Ohrožení bezpečnosti účtu blokován

## <a name="multi-factor-authentication-registration"></a>Registrace služby Multi-Factor authentication
Nejlepších výsledků v obou případech tok obnovení ohrožení bezpečnosti účtu a rizikové tok přihlášení, je, když uživatel může samoobslužné obnovení. Pokud jsou uživatelé zaregistrovaní pro službu Multi-Factor authentication, už mají telefonní číslo přidružené k účtu, který slouží k předávání výzvy zabezpečení. Žádné pomoc HelpDesk nebo správce zapojení je potřebný k obnovení před ohrožením účet. Proto se důrazně doporučujeme pro vaši uživatelé zaregistrovat u služby Multi-Factor authentication. 

Správce může nastavit zásady, které vyžaduje, aby uživatelé nastavit své účty pro další ověření zabezpečení. Tato zásada umožňuje uživatelům přeskočit službu Multi-Factor authentication registrace po dobu 14 dnů. Období odkladu 14 dnů se nedá konfigurovat.

**Registrace služby Multi-Factor authentication má tři kroky:**

1. V prvním kroku uživatel obdrží oznámení o požadavku na nastavení účtu službu Multi-Factor authentication. 
   
    ![Náprava](./media/active-directory-identityprotection-flows/140.png "nápravy")
2. Pokud chcete nastavit vícefaktorové ověřování, musíte vědět, jak chcete kontaktovat systém.
   
    ![Náprava](./media/active-directory-identityprotection-flows/141.png "nápravy")
3. Odešle systému může být obtížné jste a potřebujete, aby odpovídal.
   
    ![Náprava](./media/active-directory-identityprotection-flows/142.png "nápravy")

## <a name="risky-sign-in-recovery"></a>Obnovení rizikové přihlášení
Jestliže správce konfiguroval zásady pro přihlášení rizika, ovlivnění uživatelé upozorněni při pokusu o přihlášení. 

**Rizikové toku přihlášení má dva kroky:** 

1. Uživatel je informován něco neobvyklého zjistilo o jejich přihlášení, jako je například přihlašujete z nového místa, zařízení nebo aplikace. 
   
    ![Náprava](./media/active-directory-identityprotection-flows/120.png "nápravy")
2. Uživatel musí k prokázání své identity tím řešení bezpečnostní kontroly. Pokud je uživatel zaregistrován u služby Multi-Factor authentication potřebují k odezvě na zabezpečovací kód, abyste své telefonní číslo. Vzhledem k tomu, že toto je jenom rizikové přihlášení a ohrožení bezpečnosti účtu, uživatel nebude muset změnit heslo v tomto toku. 
   
    ![Náprava](./media/active-directory-identityprotection-flows/121.png "nápravy")

## <a name="risky-sign-in-blocked"></a>Rizikové přihlášení blokován
Můžete také správci nastavit přihlášení riziko zásady pro blokování uživatele při přihlášení v závislosti na úroveň rizika. Koncoví uživatelé musí získat odblokuje, obraťte se na správce nebo odbornou pomoc nebo se mohou zkuste se přihlásit ze známé umístění nebo zařízení. Samoobslužné obnovení pomocí řešení služby Multi-Factor authentication není možné v tomto případě.

![Náprava](./media/active-directory-identityprotection-flows/200.png "nápravy")

## <a name="compromised-account-recovery"></a>Obnovení ohrožení bezpečnosti účtu
Když je nakonfigurován uživatelských zásad zabezpečení riziko, uživatele, kteří splní uživatele riziko úroveň určená v zásadách (a proto se předpokládá, že dojde k ohrožení) musí projít tok obnovení ohrožení zabezpečení uživatele, než se můžete přihlásit. 

**Tok obnovení ohrožení zabezpečení uživatele má tři kroky:**

1. Uživatel je informován, že jejich zabezpečení účtu je ohrožena kvůli podezřelé aktivity nebo úniku přihlašovacích údajů.
   
    ![Náprava](./media/active-directory-identityprotection-flows/101.png "nápravy")
2. Uživatel musí k prokázání své identity tím řešení bezpečnostní kontroly. Pokud je uživatel zaregistrován u služby Multi-Factor authentication může samoobslužné obnovení před ohrožením. Bude nutné k odezvě na zabezpečovací kód, abyste své telefonní číslo. 
   
   ![Náprava](./media/active-directory-identityprotection-flows/110.png "nápravy")
3. Nakonec uživatel bude muset změnit své heslo, protože někdo jiný měl přístup ke svému účtu. 
   Níže jsou snímky obrazovky toto prostředí.
   
   ![Náprava](./media/active-directory-identityprotection-flows/111.png "nápravy")

## <a name="compromised-account-blocked"></a>Ohrožení bezpečnosti účtu blokován
Uživatel musí získat uživatele, který byl zablokován pomocí zásad zabezpečení riziko uživatele odblokováno, obraťte se na správce nebo HelpDesk. Samoobslužné obnovení pomocí řešení služby Multi-Factor authentication není možné v tomto případě.

![Náprava](./media/active-directory-identityprotection-flows/104.png "nápravy")

## <a name="reset-password"></a>Resetování hesla
Pokud jsou ohrožené uživatelé blokováni v přihlášení, Správce může generovat dočasné heslo pro ně. Uživatelé budou muset změnit své heslo při příštím přihlášení.

![Náprava](./media/active-directory-identityprotection-flows/160.png "nápravy")

## <a name="see-also"></a>Další informace najdete v tématech
* [Ochrany identit Azure Active Directory](active-directory-identityprotection.md) 

