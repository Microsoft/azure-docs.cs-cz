---
title: Přihlašovací prostředí v Azure AD Identity Protection | Dokumentace Microsoftu
description: Obsahuje přehled uživatelského rozhraní, když Identity Protection zmírnit, nebo opraven uživatele nebo při ověřování službou Multi-Factor Authentication se vyžaduje zásada.
services: active-directory
keywords: Azure active directory identity protection, zjišťování cloudových aplikací, Správa aplikací, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: de5bf637-75a7-4104-b6d8-03686372a319
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 397aa000da7ea70c12883622b60c0e3e6dded811
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025293"
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Možnosti přihlašování s Azure AD Identity Protection
S Azure Active Directory Identity Protection můžete:

* vyžadovat registraci k vícefaktorovému ověřování uživatelů
* riziková přihlášení a ohrožených uživatelů

Odpověď systém na těchto problémů má dopad na uživatele přihlašovací prostředí, protože přímo přihlášení zadáním uživatelského jména a hesla není možné zobrazovat. Další kroky jsou požadovány pro uživatele bez obav zpět o firmě.

Tento článek obsahuje přehled uživatele přihlašovací prostředí pro všechny případy, které se mohou vyskytnout.

**Multi-Factor Authentication**

* Registrace služby Multi-Factor authentication

**Přihlaste se na rizika**

* Obnovení riziková přihlášení
* Riziková přihlášení blokováno
* Registrace služby Multi-Factor authentication během riziková přihlášení

**Riziko uživatele**

* Obnovení ohrožení bezpečnosti účtu
* Zablokuje ohrožení bezpečnosti účtu

## <a name="multi-factor-authentication-registration"></a>Registrace služby Multi-Factor authentication
Nejlepší uživatelské prostředí pro obě, tok obnovení ohrožení bezpečnosti účtu a rizikových toku přihlášení je, když uživatel může samoobslužné obnovení. Pokud jsou uživatelé zaregistrovaní ověřování službou Multi-Factor Authentication, už mají telefonní číslo přidružené ke svému účtu, který slouží k předání bezpečnostním hrozbám. Žádné pomoc HelpDesk nebo správce zapojení je potřeba provést obnovení při ohrožení bezpečnosti účtu. Proto se důrazně doporučujeme získat vaši uživatelé zaregistrovali služby Multi-Factor authentication. 

Správci můžou nastavit zásadu, která vyžaduje, aby uživatelé nastavit jejich účty pro dodatečné ověření zabezpečení. Tyto zásady umožňují uživatelům vynechání registrace služby Multi-Factor authentication po dobu 14 dnů. Období odkladu za 14 dní se nedá konfigurovat.

**Registrace služby Multi-Factor authentication má tři kroky:**

1. V prvním kroku uživatel obdrží oznámení o požadavku na nastavení účtu službu Multi-Factor authentication. 
   
    ![Náprava](./media/flows/301.png "nápravy")

2. Chcete-li nastavení služby Multi-Factor authentication, nechejte systém vědět, jak chcete kontaktovat.
   
    ![Náprava](./media/flows/302.png "nápravy")

3. Poskytuje systému číslo telefonní číslo a způsob, jak mají být kontaktován.

    ![Náprava](./media/flows/303.png "nápravy")

K dokončení registrace, musíte buď odpovědět na telefonní hovor nebo zadejte kód. 


## <a name="risky-sign-in-recovery"></a>Obnovení riziková přihlášení
Jestliže správce nakonfiguroval zásady rizik přihlašování, ovlivněných uživatelů: upozorněni při pokusu o přihlášení. 

**Tok rizikových přihlášení má dva kroky:** 

1. Uživatel je informován, že se něco neobvyklého zjistil o přihlášení, jako je například přihlašujete z nového místa, zařízení nebo aplikace. 
   
    ![Náprava](./media/flows/120.png "nápravy")
2. Uživatel musí k prokázání své identity podle řešení bezpečnostní kontroly. Pokud je uživatel zaregistrován u služby Multi-Factor authentication, které potřebují k Zakulacení dojít k jejich telefonní číslo bezpečnostní kód. Protože se jedná jenom rizikových přihlášení a ohrožení bezpečnosti účtu, uživatel nebude mít ke změně hesla v tomto toku. 
   
    ![Náprava](./media/flows/121.png "nápravy")

## <a name="risky-sign-in-blocked"></a>Riziková přihlášení blokováno
Můžete také správci nastavení zásad rizika přihlašování k blokování uživatelů při přihlášení v závislosti na úrovni rizika. Získáte odblokované musí koncoví uživatelé kontaktovat správce nebo technickou podporu, nebo můžete, zkuste se přihlásit ze známého umístění nebo zařízení. Samoobslužné obnovení pomocí služby Multi-Factor authentication platformy řešení není možné v tomto případě.

![Náprava](./media/flows/200.png "nápravy")

## <a name="compromised-account-recovery"></a>Obnovení ohrožení bezpečnosti účtu
Pokud byly nakonfigurované zásady zabezpečení rizik uživatelů, uživatelé, kteří splňují uživatel rizika úroveň uveden v zásadách (a proto se předpokládá, že dojde k ohrožení bezpečnosti) musí procházet přes tok obnovení ohrožení zabezpečení uživatele předtím, než se mohl přihlásit. 

**Tok uživatele ohrožení obnovení má tři kroky:**

1. Uživatel informován, že jejich zabezpečení účtu ohroženy z důvodu podezřelé aktivity nebo úniku přihlašovacích údajů.
   
    ![Náprava](./media/flows/101.png "nápravy")
2. Uživatel musí k prokázání své identity podle řešení bezpečnostní kontroly. Pokud je uživatel zaregistrován u služby Multi-Factor authentication můžete samoobslužné obnovení z jejichž zabezpečení je ohrožené. Musí se zaokrouhlí dojít k jejich telefonní číslo bezpečnostní kód. 
   
   ![Náprava](./media/flows/110.png "nápravy")
3. A konečně uživatel musí změnit heslo, protože někdo jiný může mít přístup ke svému účtu. 
   Snímky obrazovek pro toto prostředí jsou uvedené níže.
   
   ![Náprava](./media/flows/111.png "nápravy")

## <a name="compromised-account-blocked"></a>Zablokuje ohrožení bezpečnosti účtu
Uživatel musí získat jako uživatel, který je zablokovaný kvůli odblokování zásady zabezpečení rizik uživatelů, obraťte se na správce nebo HelpDesk. Samoobslužné obnovení pomocí služby Multi-Factor authentication platformy řešení není možné v tomto případě.

![Náprava](./media/flows/104.png "nápravy")

## <a name="reset-password"></a>Resetování hesla
Pokud z přihlášení jsou blokovány ohrožených uživatelů, Správce může vygenerovat dočasné heslo pro ně. Uživatelé nemají změnit své heslo při příštím přihlášení.

![Náprava](./media/flows/160.png "nápravy")

## <a name="see-also"></a>Další informace najdete v tématech
* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) 

