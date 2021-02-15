---
title: Konfigurace dočasného přístupového průchodu ve službě Azure AD pro registraci metod ověřování bez hesla
description: Naučte se, jak nakonfigurovat a povolit uživatelům registraci metod ověřování bez hesla pomocí dočasného přístupového průchodu (klepnutím).
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: justinha
author: inbarckms
manager: daveba
ms.reviewer: inbarckms
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56d45119fa86ab47e6a625c628d8cb9763db83bd
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520918"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>Konfigurace dočasného přístupového průchodu ve službě Azure AD pro registraci metod ověřování bez hesla (Preview)

Metody ověřování bez hesla, jako je FIDO2 nebo telefon bez hesla, se přihlásí prostřednictvím aplikace Microsoft Authenticator a umožní uživatelům, aby se bezpečně přihlásili bez hesla. Uživatelé mohou spustit metody neumožňující heslo jedním ze dvou způsobů:

- Používání stávajících metod Multi-Factor Authentication služby Azure AD 
- Použití dočasného přístupového průchodu (klepnutím) 

Klepněte na časově omezené heslo vydané správcem, které splňuje požadavky na silné ověřování a dá se použít k zaregistrování dalších metod ověřování, včetně bez hesla. Po klepnutí se také usnadňuje obnovení, když uživatel ztratí nebo zapomněl svůj silný ověřovací faktor, jako je FIDO2 bezpečnostní klíč nebo aplikace Microsoft Authenticator, ale musí se přihlásit k registraci nových metod silného ověřování.


V tomto článku se dozvíte, jak pomocí Azure Portal povolit a používat ve službě Azure AD klepnutí. Tyto akce můžete provést také pomocí rozhraní REST API. 

>[!NOTE]
>Dočasný přístupový průchod je aktuálně ve verzi Public Preview. Některé funkce nemusí být podporované nebo mají omezené možnosti. 

## <a name="enable-the-tap-policy"></a>Povolit zásadu klepnutí

Zásada klepnutím definuje nastavení, například dobu života průchodů vytvořených v tenantovi, nebo uživatele a skupiny, kteří můžou k přihlášení použít klepnutí. Než se někdo může přihlásit pomocí klepnutí, musíte povolit zásadu metody ověřování a vybrat, kteří uživatelé a skupiny se můžou přihlásit pomocí klepnutí.
I když můžete vytvořit klepnutím pro libovolného uživatele, můžou se k němu přihlašovat jenom oprávnění, která jsou součástí této zásady.

Držitelé role správce zásad globálních správců a metod ověřování můžou aktualizovat zásadu klepnutí na metodu ověřování.
Konfigurace zásady klepněte na metodu ověřování:

1. Přihlaste se k Azure Portal jako globální správce a klikněte na **Azure Active Directory**  >    >  **metody ověřování** zabezpečení  >  **dočasný přístup Pass**.
1. Klikněte na **Ano** , pokud chcete zásadu povolit, vyberte, kteří uživatelé mají zásadu použitou, a jakékoli **Obecné** nastavení.

   ![Snímek obrazovky, jak povolit zásadu pro klepnutí na metodu ověřování](./media/how-to-authentication-temporary-access-pass/policy.png)

   Výchozí hodnota a rozsah povolených hodnot jsou popsány v následující tabulce.


   | Nastavení          | Výchozí hodnoty | Povolené hodnoty               | Komentáře                                                                                                                                                                                                                                                                 |   |
   |------------------|----------------|------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    Minimální doba života | 1 hodina         | 10 – 43200 minut (30 dní) | Minimální počet minut, po který je klepnutí platný.                                                                                                                                                                                                                         |   |
   | Maximální doba života | 24 hodin       | 10 – 43200 minut (30 dní) | Maximální počet minut, po který je klepnutí platný.                                                                                                                                                                                                                         |   |
   | Výchozí doba života | 1 hodina         | 10 – 43200 minut (30 dní) | Výchozí hodnoty lze přepsat jednotlivými průchody v rámci minimální a maximální doby života nakonfigurované zásadou.                                                                                                                                                |   |
   | Jednorázové použití     | Ne          | True nebo false                 | Pokud je zásada nastavená na hodnotu false, průchody v tenantovi se dají v rámci platnosti (maximální doba života) použít buď jednou, nebo více než jednou. Když v zásadě klepnutí vynutíte jednorázové použití, všechny průchody vytvořené v tenantovi se vytvoří jako jednorázové použití. |   |
   | Délka           | 8              | 8-48 znaků              | Definuje délku hesla.                                                                                                                                                                                                                                      |   |

## <a name="create-a-tap-in-the-azure-ad-portal"></a>Vytvoření klepnutí na portálu Azure AD

Po povolení zásady klepněte na možnost vytvořit uživatele v Azure AD. Tyto role mohou provádět následující akce související s klepnutím.

- Globální správce může vytvořit, odstranit, Zobrazit klepněte na libovolného uživatele (s výjimkou samotného).
- Správci privilegovaného ověřování můžou vytvořit, odstranit, zobrazit klepnutím na správce a členy (s výjimkou samotného).
- Správci ověřování můžou vytvořit, odstranit, zobrazit klepnutím na členy (s výjimkou samotného).
- Globální správce může zobrazit podrobnosti o klepnutí na uživateli (bez čtení samotného kódu).

Chcete-li vytvořit klepnutím:

1. Přihlaste se k portálu buď jako globální správce, správce privilegovaného ověřování nebo Správce ověřování. 
1. Klikněte na **Azure Active Directory**, vyhledejte uživatele, vyberte uživatele, například *Novák zelený*, a pak zvolte **metody ověřování**.
1. V případě potřeby vyberte možnost pro **nové prostředí metod ověřování uživatelů**.
1. Vyberte možnost **Přidání metod ověřování**.
1. Pod tlačítkem **zvolit metodu** klikněte na **dočasný přístup Pass (Náhled)**.
1. Definujte vlastní čas aktivace nebo dobu trvání a klikněte na **Přidat**.

   ![Snímek obrazovky s postupem, jak vytvořit klepnutím](./media/how-to-authentication-temporary-access-pass/create.png)

1. Po přidání se zobrazí podrobnosti o klepnutí. Poznamenejte si skutečnou hodnotu klepnutí. Tuto hodnotu zadáte uživateli. Po kliknutí na tlačítko **OK** nemůžete tuto hodnotu zobrazit.
   
   ![Snímek obrazovky s informacemi o klepnutí](./media/how-to-authentication-temporary-access-pass/details.png)

## <a name="use-a-tap"></a>Použít klepněte

Nejběžnějším použitím pro klepnutí je, že uživatel může zaregistrovat ověřovací údaje během prvního přihlášení, aniž by musel dokončit další výzvy zabezpečení. Metody ověřování jsou registrovány na adrese [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Uživatelé můžou taky aktualizovat existující metody ověřování.

1. Otevřete webový prohlížeč na [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Zadejte hlavní název uživatele (UPN) účtu, pro který jste vytvořili klepnutím, například *tapuser@contoso.com* .
1. Pokud je uživatel zahrnut v zásadě klepněte, zobrazí se obrazovka, která má zadat svůj klepnutím.
1. Zadejte klepněte na tlačítko, které se zobrazilo v Azure Portal.

   ![Snímek obrazovky s postupem, jak zadat klepnutím](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>V případě federovaných domén se v rámci federace upřednostňuje klepnutí. Uživatel s klepnutím dokončí ověřování ve službě Azure AD a nebude přesměrován na zprostředkovatele federovaných identit (IdP).

Uživatel je nyní přihlášen a může aktualizovat nebo zaregistrovat metodu, jako je například FIDO2 zabezpečení klíče. Uživatelé, kteří aktualizují své metody ověřování z důvodu ztráty svých přihlašovacích údajů nebo zařízení, by se měli ujistit, že odeberou staré metody ověřování.

Uživatelé můžou také použít svůj klepnutím k registraci v případě přihlašování k telefonickému přihlášení přímo z ověřovací aplikace. Další informace najdete v tématu [Přidání pracovního nebo školního účtu do aplikace Microsoft Authenticator](../user-help/user-help-auth-app-add-work-school-account.md).

![Snímek obrazovky s postupem, jak zadat pracovní nebo školní účet](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

## <a name="delete-a-tap"></a>Odstranit klepnutím

Klepnutím na tlačítko vypršela platnost nelze použít. V části **metody ověřování** pro uživatele se ve sloupci **Detail** zobrazuje, když vypršela doba klepnutí. Tato pole s vypršenou platností můžete odstranit pomocí následujících kroků:

1. Na portálu Azure AD přejděte na **uživatele**, vyberte uživatele, například *klepněte na uživatel* a pak zvolte **metody ověřování**.
1. Na pravé straně metody ověřování **dočasná metoda Access Pass (Preview)** zobrazené v seznamu vyberte **Odstranit**.

## <a name="replace-a-tap"></a>Nahradit klepnutím 

- Uživatel může mít pouze jedno klepnutí. Heslo lze použít během počátečního a koncového času klepnutí.
- Pokud uživatel vyžaduje nové klepnutí:
  - Pokud je existující klepnutí platné, správce musí odstranit existující klepnutím a vytvořit nový průchod pro uživatele. Odstraněním platného klepnutí dojde k odvolání relací uživatele. 
  - Pokud již existujícímu klepnutím dojde k vypršení platnosti, nový klepnutím přepíše existující klepnutím a nebude odvolávat relace uživatele.

Další informace o standardech NIST pro registraci a obnovení najdete v článku [NIST Special publikace 800-63a](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4).

## <a name="limitations"></a>Omezení

Pamatujte na tato omezení:

- Když pomocí jednorázového klepnutí zaregistrujete metodu bez hesla, jako je FIDO2 nebo přihlašování telefonem, musí uživatel dokončit registraci do 10 minut od přihlášení pomocí jednorázového klepnutí. Toto omezení se nevztahuje na klepnutí, které lze použít více než jednou.
- Uživatelé typu Host se nemohou přihlásit pomocí klepnutí.
- Uživatelům v oboru pro zásady registrace samoobslužného resetování hesla (SSPR) bude nutné zaregistrovat jednu z metod SSPR po přihlášení klepnutím. Pokud uživatel bude používat jenom FIDO2 klíč, vylučte je ze zásad SSPR nebo zakažte zásady registrace SSPR. 
- Klepnutím se nedá použít s rozšířením serveru NPS (Network Policy Server) a adaptéru Active Directory Federation Services (AD FS) (AD FS).
- Pokud je v tenantovi zapnuté bezproblémové jednotné přihlašování, zobrazí se uživatelům výzva k zadání hesla. Odkaz **použít váš dočasný přístup** bude k dispozici pro uživatele, aby se přihlásil pomocí klepnutí.

![Snímek obrazovky s použitím klepnutí](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>Řešení potíží    

- Pokud se uživateli během přihlašování nenabídne klepnutím, podívejte se na následující:
  - Uživatel je v oboru pro zásadu klepnutí na metodu ověřování.
  - Uživatel má platné klepnutí, a pokud se jedná o jednorázové použití, zatím se nepoužil.
- Pokud se **přihlášení k dočasnému předávacímu průchodu zablokovalo, protože** se během přihlašování při přihlášení zobrazí zásada přihlašovací údaje uživatele, podívejte se na následující:
  - Uživatel má možnost vícenásobného použití, zatímco zásady metody ověřování vyžadují jednorázové klepnutí.
  - Jednorázový klepnutí již bylo použito.

## <a name="next-steps"></a>Další kroky

- [Plánování nasazení ověřování s neplatnými hesly v Azure Active Directory](howto-authentication-passwordless-deployment.md)

