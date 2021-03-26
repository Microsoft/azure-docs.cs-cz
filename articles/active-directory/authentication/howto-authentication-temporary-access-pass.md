---
title: Konfigurace dočasného přístupového průchodu ve službě Azure AD pro registraci metod ověřování bez hesla
description: Naučte se konfigurovat a povolit uživatelům registraci metod ověřování bez hesla pomocí dočasného průchodu Accessu.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarckms
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0805ac84318a4fee98c30127ac80c0dac2b96309
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558257"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>Konfigurace dočasného přístupového průchodu ve službě Azure AD pro registraci metod ověřování bez hesla (Preview)

Metody ověřování bez hesla, jako je FIDO2 nebo telefon bez hesla, se přihlásí prostřednictvím aplikace Microsoft Authenticator a umožní uživatelům, aby se bezpečně přihlásili bez hesla. Uživatelé mohou spustit metody neumožňující heslo jedním ze dvou způsobů:

- Používání stávajících Multi-Factor Authenticationch metod Azure AD 
- Použití dočasného přístupového průchodu (klepnutím) 

Dočasný přístupový průchod je časově omezený přístupový kód vydaný správcem, který splňuje požadavky na silné ověřování a dá se použít k zaregistrování dalších metod ověřování, včetně bez hesla. Dočasná metoda přístupu také usnadňuje obnovení, když uživatel ztratí nebo zapomněl svůj faktor ověřování, jako je FIDO2 bezpečnostní klíč nebo aplikace Microsoft Authenticator, ale musí se přihlásit k registraci nových metod silného ověřování.


V tomto článku se dozvíte, jak ve službě Azure AD povolit a používat dočasný přístupový průchod pomocí Azure Portal. Tyto akce můžete provést také pomocí rozhraní REST API. 

>[!NOTE]
>Dočasný přístupový průchod je aktuálně ve verzi Public Preview. Některé funkce nemusí být podporované nebo mají omezené možnosti. 

## <a name="enable-the-temporary-access-pass-policy"></a>Povolit zásadu dočasného předání přístupu

Zásada dočasného předání přístupu definuje nastavení, například dobu života průchodů vytvořených v tenantovi, nebo uživatele a skupiny, kteří můžou k přihlášení použít dočasný přístupový průchod. Než se někdo může přihlásit pomocí dočasného přístupového průchodu, musíte povolit zásadu metody ověřování a vybrat, kteří uživatelé a skupiny se můžou přihlásit pomocí dočasného přístupového průchodu.
I když můžete vytvořit dočasný přístup pro každého uživatele, můžou se k němu přihlásit jenom oprávnění, která jsou součástí této zásady.

Držitelé role správce zásad globálního správce a metody ověřování můžou aktualizovat zásady dočasného přístupu k metodě ověřování.
Postup konfigurace zásady dočasného přístupu k metodě ověřování:

1. Přihlaste se k Azure Portal jako globální správce a klikněte na **Azure Active Directory**  >    >  **metody ověřování** zabezpečení  >  **dočasný přístup Pass**.
1. Klikněte na **Ano** , pokud chcete zásadu povolit, vyberte, kteří uživatelé mají zásadu použitou, a jakékoli **Obecné** nastavení.

   ![Snímek obrazovky s postupem, jak povolit zásady dočasného přístupu Pass over](./media/how-to-authentication-temporary-access-pass/policy.png)

   Výchozí hodnota a rozsah povolených hodnot jsou popsány v následující tabulce.


   | Nastavení | Výchozí hodnoty | Povolené hodnoty | Komentáře |
   |---|---|---|---|
   | Minimální doba života | 1 hodina | 10 – 43200 minut (30 dní) | Minimální počet minut, po který je dočasný přístupový průchod platný. |
   | Maximální doba života | 24 hodin | 10 – 43200 minut (30 dní) | Maximální počet minut, po který je dočasný přístupový průchod platný. |
   | Výchozí doba života | 1 hodina | 10 – 43200 minut (30 dní) | Výchozí hodnoty lze přepsat jednotlivými průchody v rámci minimální a maximální doby života nakonfigurované zásadou. |
   | Jednorázové použití | Ne | True nebo false | Pokud je zásada nastavená na hodnotu false, průchody v tenantovi se dají v rámci platnosti (maximální doba života) použít buď jednou, nebo více než jednou. Když vynutíte jednorázové použití v zásadách dočasného přístupového průchodu, všechny průchody vytvořené v tenantovi se vytvoří jako jednorázové použití. |
   | Délka | 8 | 8-48 znaků | Definuje délku hesla. |

## <a name="create-a-temporary-access-pass-in-the-azure-ad-portal"></a>Vytvoření dočasného přístupového průchodu na portálu Azure AD

Po povolení zásady můžete vytvořit dočasný přístupový průchod pro uživatele v Azure AD. Tyto role mohou provádět následující akce týkající se dočasného přístupového průchodu.

- Globální správce může vytvořit, odstranit a zobrazit dočasný přístup k předávání u libovolného uživatele (s výjimkou samotného).
- Správci privilegovaného ověřování můžou vytvořit, odstranit, zobrazit dočasný přístup k předávání u správců a členů (s výjimkou samotných).
- Správci ověřování můžou vytvořit, odstranit, zobrazit dočasný přístup k předávání u členů (s výjimkou samotných).
- Globální správce může zobrazit podrobnosti o dočasném přístupu pro uživatele (bez čtení samotného kódu).

Vytvoření dočasného přístupového průchodu:

1. Přihlaste se k portálu buď jako globální správce, správce privilegovaného ověřování nebo Správce ověřování. 
1. Klikněte na **Azure Active Directory**, vyhledejte uživatele, vyberte uživatele, například *Novák zelený*, a pak zvolte **metody ověřování**.
1. V případě potřeby vyberte možnost pro **nové prostředí metod ověřování uživatelů**.
1. Vyberte možnost **Přidání metod ověřování**.
1. Pod tlačítkem **zvolit metodu** klikněte na **dočasný přístup Pass (Náhled)**.
1. Definujte vlastní čas aktivace nebo dobu trvání a klikněte na **Přidat**.

   ![Snímek obrazovky s postupem vytvoření dočasného přístupového průchodu](./media/how-to-authentication-temporary-access-pass/create.png)

1. Po přidání se zobrazí podrobnosti o dočasném přístupovém průchodu. Poznamenejte si skutečný údaj hodnoty úspěšnosti dočasného přístupu. Tuto hodnotu zadáte uživateli. Po kliknutí na tlačítko **OK** nemůžete tuto hodnotu zobrazit.
   
   ![Snímek obrazovky s podrobnostmi o dočasném přístupu](./media/how-to-authentication-temporary-access-pass/details.png)

## <a name="use-a-temporary-access-pass"></a>Použít dočasný přístupový průchod

Nejběžnějším použitím dočasného přístupového průchodu je, že uživatel může zaregistrovat ověřovací údaje při prvním přihlášení, aniž by musel dokončit další výzvy zabezpečení. Metody ověřování jsou registrovány na adrese [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Uživatelé můžou taky aktualizovat existující metody ověřování.

1. Otevřete webový prohlížeč na [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Zadejte hlavní název uživatele (UPN) účtu, pro který jste vytvořili dočasný přístupový průchod, například *tapuser@contoso.com* .
1. Pokud je uživatel zahrnut v zásadách dočasného přístupu, zobrazí se obrazovka, která má zadat dočasný přístupový průchod.
1. Zadejte dočasný přístupový průchod, který se zobrazil v Azure Portal.

   ![Snímek obrazovky s postupem, jak zadat dočasný přístupový průchod](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>U federovaných domén se v rámci federace upřednostňuje dočasný přístupový průchod. Uživatel s dočasným přístupovým průchodem dokončí ověřování ve službě Azure AD a nebude přesměrován na zprostředkovatele federovaných identit (IdP).

Uživatel je nyní přihlášen a může aktualizovat nebo zaregistrovat metodu, jako je například FIDO2 zabezpečení klíče. Uživatelé, kteří aktualizují své metody ověřování z důvodu ztráty svých přihlašovacích údajů nebo zařízení, by se měli ujistit, že odeberou staré metody ověřování.

Uživatelé můžou k registraci telefonického přihlášení bez hesla přímo z ověřovací aplikace použít taky jejich dočasný přístupový průchod. Další informace najdete v tématu [Přidání pracovního nebo školního účtu do aplikace Microsoft Authenticator](../user-help/user-help-auth-app-add-work-school-account.md).

![Snímek obrazovky s postupem, jak zadat dočasný přístupový průchod pomocí pracovního nebo školního účtu](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

## <a name="delete-a-temporary-access-pass"></a>Odstranění dočasného přístupového průchodu

Neplatnost dočasného přístupového průchodu s vypršenou platností nelze použít. V části **metody ověřování** pro uživatele se ve sloupci **Detail** zobrazuje, kdy vypršela platnost dočasného průchodu Accessu. Dočasného přístupového průchodu s vypršenou platností můžete odstranit pomocí následujících kroků:

1. Na portálu Azure AD přejděte na **uživatele**, vyberte uživatele, například *klepněte na uživatel* a pak zvolte **metody ověřování**.
1. Na pravé straně metody ověřování **dočasná metoda Access Pass (Preview)** zobrazené v seznamu vyberte **Odstranit**.

## <a name="replace-a-temporary-access-pass"></a>Nahrazení dočasného přístupového průchodu 

- Uživatel může mít pouze jeden dočasný přístupový průchod. Heslo lze použít při počátečním a koncovém čase dočasného přístupového průchodu.
- Pokud uživatel vyžaduje nový dočasný přístup:
  - Pokud je existující dočasný přístupový průchod platný, správce musí odstranit stávající dočasný přístup a vytvořit nový průchod pro uživatele. Odstraněním platného dočasného přístupového průchodu se odvolají relace uživatele. 
  - Pokud platnost stávajícího dočasného přístupového průchodu vypršela, nová dočasná přístupová Pass přepíše stávající dočasný přístup a nebude odvolávat relace uživatele.

Další informace o standardech NIST pro registraci a obnovení najdete v článku [NIST Special publikace 800-63a](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4).

## <a name="limitations"></a>Omezení

Pamatujte na tato omezení:

- Pokud k registraci metody bez hesla, jako je FIDO2 nebo přihlašování telefonem, uživatel používá jednorázové dočasné přístupy, musí dokončit registraci během 10 minut od přihlášení s jednorázovým dočasným přístupem. Toto omezení se nevztahuje na dočasný přístupový průchod, který lze použít více než jednou.
- Uživatelé typu Host se nemůžou přihlásit pomocí dočasného přístupového průchodu.
- Uživatelům v oboru pro zásady registrace samoobslužného resetování hesla (SSPR) bude nutné zaregistrovat jednu z metod SSPR po přihlášení pomocí dočasného přístupového průchodu. Pokud uživatel bude používat jenom FIDO2 klíč, vylučte je ze zásad SSPR nebo zakažte zásady registrace SSPR. 
- Dočasný přístupový průchod nelze použít s adaptérem rozšíření serveru NPS (Network Policy Server) a Active Directory Federation Services (AD FS) (AD FS).
- Pokud je v tenantovi zapnuté bezproblémové jednotné přihlašování, zobrazí se uživatelům výzva k zadání hesla. Odkaz **použít váš dočasný přístup** bude k dispozici pro uživatele, aby se přihlásil pomocí dočasného přístupového průchodu.

  ![Snímek obrazovky s použitím dočasného předávacího přístupu](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>Řešení potíží    

- Pokud uživateli během přihlašování nenabídne dočasný přístupový průchod, ověřte následující:
  - Uživatel je v oboru pro zásady dočasného přístupu k metodě ověřování.
  - Uživatel má platný dočasný přístupový průchod, a pokud se jedná o jednorázové použití, zatím se nepoužil.
- Pokud se **přihlášení k dočasnému předávacímu průchodu zablokovalo, protože** se během přihlašování pomocí dočasného přístupového průchodu zobrazí zásady pro přihlašovací údaje uživatele, ověřte následující:
  - Uživatel má k disnásobení dočasnou metodu přístupu, zatímco zásady metody ověřování vyžadují jednorázový dočasný přístup.
  - Jednorázový dočasný přístupový průchod byl již použit.

## <a name="next-steps"></a>Další kroky

- [Plánování nasazení ověřování s neplatnými hesly v Azure Active Directory](howto-authentication-passwordless-deployment.md)

