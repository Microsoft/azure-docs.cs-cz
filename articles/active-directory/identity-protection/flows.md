---
title: Přihlášení pomocí Azure AD Identity Protection | Microsoft Docs
description: Poskytuje přehled uživatelského prostředí, když aplikace Identity Protection snižuje nebo opravuje uživatele nebo pokud zásady vyžadují službu Multi-Factor Authentication.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e513027eed44ec7649f41f8786882aed8511bc6
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335498"
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Prostředí pro přihlašování pomocí Azure AD Identity Protection

Pomocí Azure Active Directory Identity Protection můžete:

* vyžadovat, aby si uživatelé zaregistrovali službu Multi-Factor Authentication
* zpracování rizikových přihlášení a ohrožených uživatelů

Reakce systému na tyto problémy má dopad na přihlašovací prostředí uživatele, protože se přímo přihlašujete zadáním uživatelského jména a hesla už nebude možné. Aby se uživatel mohl bezpečně vrátit do firmy, je potřeba provést další kroky.

Tento článek poskytuje přehled možností přihlašování uživatelů pro všechny případy, ke kterým může dojít.

**Multi-Factor Authentication**

* Registrace Multi-Factor Authentication

**Riziko při přihlašování**

* Obnovení rizik při přihlášení
* Zakázané rizikové přihlášení
* Registrace Multi-Factor Authentication během rizikové přihlašování

**Riziko uživatele**

* Obnovení ohroženého účtu
* Napadený účet zablokován

## <a name="multi-factor-authentication-registration"></a>Registrace Multi-Factor Authentication
Nejlepší uživatelské prostředí pro obojí, tok obnovení ohroženého účtu a postup rizikového přihlašování je v případě, že se uživatel může zotavit sami. Pokud jsou uživatelé registrováni pro službu Multi-Factor Authentication, již mají k účtu přidruženo telefonní číslo, které lze použít k předávání bezpečnostních problémů. Pro obnovení z důvodu ohrožení bezpečnosti účtu není nutné provádět žádné zásahy helpdesku ani správce. Proto se důrazně doporučuje získat uživatele zaregistrovaný pro službu Multi-Factor Authentication. 

Správci můžou nastavit zásadu, která vyžaduje, aby uživatelé nastavili své účty pro další ověření zabezpečení. Tato zásada umožňuje uživatelům přeskočit registraci služby Multi-Factor Authentication po dobu až 14 dnů. Období odkladu na 14 dní není konfigurovatelné.

**Registrace Multi-Factor Authentication má tři kroky:**

1. V prvním kroku uživatel získá oznámení o požadavku na nastavení účtu pro službu Multi-Factor Authentication. 
   
    ![Náprava](./media/flows/140.png "Náprava")
2. Chcete-li nastavit vícefaktorové ověřování, je nutné, aby systém informoval o tom, jak má být kontaktován.
   
    ![Náprava](./media/flows/141.png "Náprava")
3. Systém vám odešle výzvu a vy budete potřebovat reagovat.
   
    ![Náprava](./media/flows/142.png "Náprava")

## <a name="risky-sign-in-recovery"></a>Obnovení rizik při přihlášení
Když správce nakonfiguroval zásadu pro rizika přihlášení, budou se uživatelé při pokusu o přihlášení informovat. 

**Postup rizikového přihlašování má dva kroky:** 

1. Uživatel je informován o tom, že při přihlašování byl zjištěn nějaký neobvyklý, například přihlašování z nového umístění, zařízení nebo aplikace. 
   
    ![Náprava](./media/flows/120.png "Náprava")
2. Uživatel musí prokázat svoji identitu vyřešením bezpečnostní výzvy. Pokud je uživatel zaregistrován pro službu Multi-Factor Authentication, musí si odložit bezpečnostní kód na své telefonní číslo. Vzhledem k tomu, že se jedná jenom o rizikové přihlášení a nikoli o ohrožený účet, uživatel nebude muset v tomto toku změnit heslo. 
   
    ![Náprava](./media/flows/121.png "Náprava")

## <a name="risky-sign-in-blocked"></a>Zakázané rizikové přihlášení
Správci se také můžou rozhodnout nastavit rizikové zásady pro přihlašování a zablokovat uživatelům při přihlašování, a to v závislosti na úrovni rizika. Chcete-li se odblokovat, musí se koncoví uživatelé obrátit na správce nebo Helpdesk nebo se můžou pokusit přihlásit ze známého umístění nebo zařízení. Samoobslužné obnovování pomocí řešení Multi-Factor Authentication není v tomto případě možnost.

![Náprava](./media/flows/200.png "Náprava")

## <a name="compromised-account-recovery"></a>Obnovení ohroženého účtu
Když je nakonfigurovaná zásada zabezpečení rizik uživatelů, uživatelé, kteří splňují úroveň rizika uživatele zadanou v zásadě (a jsou proto považovány za ohrožené), musí projít tokem obnovení zabezpečení uživatele, aby se mohli přihlásit. 

**Tok obnovení ohrožení uživatele má tři kroky:**

1. Uživatel je informován o ohrožení zabezpečení jejich účtu z důvodu podezřelé aktivity nebo nevrácených přihlašovacích údajů.
   
    ![Náprava](./media/flows/101.png "Náprava")
2. Uživatel musí prokázat svoji identitu vyřešením bezpečnostní výzvy. Pokud je uživatel zaregistrován pro službu Multi-Factor Authentication, může se zotavit z ohrožení bezpečnosti. Budou muset na své telefonní číslo odregistrovat bezpečnostní kód. 
   
   ![Náprava](./media/flows/110.png "Náprava")
3. Nakonec uživatel bude muset změnit heslo, protože někdo jiný mohl mít přístup ke svému účtu. 
   Snímky obrazovky tohoto prostředí jsou uvedené níže.
   
   ![Náprava](./media/flows/111.png "Náprava")

## <a name="compromised-account-blocked"></a>Napadený účet zablokován
Chcete-li získat uživatele, který byl zablokován zásadami zabezpečení rizika uživatele, musí se obrátit na správce nebo helpdesk. Samoobslužné obnovování pomocí řešení Multi-Factor Authentication není v tomto případě možnost.

![Náprava](./media/flows/104.png "Náprava")

## <a name="reset-password"></a>Resetovat heslo
Pokud se uživatelům, kteří mají ohrožení zabezpečení, zablokuje přihlášení, může správce pro ně vygenerovat dočasné heslo. Uživatelé budou muset během příštího přihlašování změnit svoje heslo.

![Náprava](./media/flows/160.png "Náprava")

## <a name="see-also"></a>Viz také:

* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) 
