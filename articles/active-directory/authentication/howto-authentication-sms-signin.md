---
title: Přihlášení uživatele pomocí SMS pro Službu Azure Active Directory
description: Přečtěte si, jak nakonfigurovat a povolit uživatelům přihlášení ke službě Azure Active Directory pomocí SMS (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: 845948d9aec28ee79a11fb11aaef4cfbf1b263fa
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770553"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory-preview"></a>Konfigurace a povolení uživatelů pro ověřování pomocí služby Azure Active Directory (preview)

Chcete-li snížit složitost a bezpečnostní rizika pro uživatele k přihlášení k aplikacím a službám, Azure Active Directory (Azure AD) poskytuje několik možností ověřování. Ověřování pomocí sms, které je v současné době ve verzi Preview, umožňuje uživatelům přihlásit se, aniž by museli poskytovat nebo dokonce znát své uživatelské jméno a heslo. Po vytvoření účtu správcem identity mohou zadat své telefonní číslo na výzvu k přihlášení a zadat ověřovací kód, který je jim odeslán prostřednictvím textové zprávy. Tato metoda ověřování zjednodušuje přístup k aplikacím a službám, zejména pro pracovníky v první linii.

Tento článek ukazuje, jak povolit ověřování na základě SMS pro vybrané uživatele nebo skupiny ve službě Azure AD.

|     |
| --- |
| Ověřování pomocí sms pro uživatele je funkce veřejného náhledu služby Azure Active Directory. Další informace o náhledech najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="before-you-begin"></a>Než začnete

Chcete-li tento článek dokončit, potřebujete následující zdroje a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* K povolení ověřování pomocí serveru SMS potřebujete oprávnění *globálního správce* v tenantovi Azure AD.
* Každý uživatel, který je povolen v zásadách metody ověřování textových zpráv, musí mít licenci, i když ji nepoužívá. Každý povolený uživatel musí mít jednu z následujících licencí Azure AD nebo Microsoft 365:
    * [Azure AD Premium P1 nebo P2][azuread-licensing]
    * [Microsoft 365 (M365) F1 nebo F3][m365-firstline-workers-licensing]
    * [Podniková mobilita + zabezpečení (EMS) E3 nebo E5][ems-licensing] nebo [Microsoft 365 (M365) E3 nebo E5][m365-licensing]

## <a name="limitations"></a>Omezení

Během veřejné ho ukončování pravosti pomocí serveru SMS platí následující omezení:

* Ověřování pomocí SMS není momentálně kompatibilní s azure multifaktorovým ověřováním.
* S výjimkou Teams není ověřování pomocí SMS aktuálně kompatibilní s nativními aplikacemi Office.
* Ověřování pomocí SMS se nedoporučuje pro účty B2B.
* Federovaní uživatelé se v domovském tenantovi neověřují. Ověřují se pouze v cloudu.

## <a name="enable-the-sms-based-authentication-method"></a>Povolení metody ověřování založené na serveru SMS

Existují tři hlavní kroky k povolení a používání ověřování pomocí serveru SMS ve vaší organizaci:

* Povolte zásadu metody ověřování.
* Vyberte uživatele nebo skupiny, které mohou používat metodu ověřování založenou na serveru SMS.
* Přiřaďte každému uživatelskému účtu telefonní číslo.
    * Toto telefonní číslo lze přiřadit na webu Azure Portal (který je uveden v tomto článku) a v části *Můj zaměstnanec* nebo *Můj profil*.

Nejprve povolte ověřování na základě SMS pro vašeho klienta Azure AD.

1. Přihlaste se k [portálu Azure][azure-portal] jako *globální správce*.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V navigační nabídce na levé straně okna služby Azure Active Directory vyberte **metody zabezpečení > ověřování > zásady metody ověřování (preview).**

    [![](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png "Browse to and select the Authentication method policy (preview) window in the Azure portal")](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. V seznamu dostupných metod ověřování vyberte **textová zpráva**.
1. Nastavte **možnost Povolit** na *ano*.

    ![Povolení textového ověřování v okně zásad metody ověřování](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    Můžete povolit ověřování pomocí serveru SMS pro *všechny uživatele* nebo *Vybrat uživatele* a skupiny. V další části povolíte ověřování na základě SMS pro testovacího uživatele.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>Přiřazení metody ověřování uživatelům a skupinám

Když je ověřování založené na SMS ve vašem tenantovi Azure AD povoleno, vyberte teď některé uživatele nebo skupiny, kterým bude povoleno používat tuto metodu ověřování.

1. V okně zásad ověřování textových zpráv nastavte **možnost Cíl** na *Vybrat uživatele*.
1. Zvolte, zda chcete **přidat uživatele nebo skupiny**, a pak vyberte testovacího uživatele nebo skupinu, například *Uživatele contoso* nebo *uživatele contoso SMS*.

    [![](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png "Choose users or groups to enable for SMS-based authentication in the Azure portal")](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. Po výběru uživatelů nebo skupin zvolte **Vybrat**a **pak Uložit** aktualizovanou zásadu metody ověřování.

Každý uživatel, který je povolen v zásadách metody ověřování textových zpráv, musí mít licenci, i když ji nepoužívá. Ujistěte se, že máte příslušné licence pro uživatele, které povolíte v zásadách metody ověřování, zejména pokud povolíte funkci pro velké skupiny uživatelů.

## <a name="set-a-phone-number-for-user-accounts"></a>Nastavení telefonního čísla pro uživatelské účty

Uživatelé jsou teď povolená pro ověřování pomocí SMS, ale jejich telefonní číslo musí být přidruženo k profilu uživatele ve službě Azure AD, než se budou moci přihlásit. Uživatel si [toto telefonní číslo](../user-help/sms-sign-in-explainer.md) může nastavit sám v části Můj *profil*nebo můžete telefonní číslo přiřadit pomocí portálu Azure. Telefonní čísla mohou nastavit *globální správci*, *správci ověřování*nebo *správci privilegovaného ověřování*.

Pokud je telefonní číslo nastavené pro sms-sign, je také k dispozici pro použití s [Azure Multi-Factor Authentication][tutorial-azure-mfa] a [samoobslužné resetování hesla][tutorial-sspr].

1. Vyhledejte a vyberte **Azure Active Directory**.
1. V navigační nabídce na levé straně okna služby Azure Active Directory vyberte **možnost Uživatelé**.
1. V předchozí části vyberte uživatele, které jste povolili pro ověřování pomocí sms, například *Uživatel contoso*, a vyberte **metody ověřování**.
1. Zadejte telefonní číslo uživatele, včetně kódu země, například *+1 xxxxxxxxx*. Portál Azure ověří, že telefonní číslo je ve správném formátu.

    ![Nastavení telefonního čísla pro uživatele na webu Azure Portal, které se má používat s ověřováním pomocí SMS](./media/howto-authentication-sms-signin/set-user-phone-number.png)

    Telefonní číslo musí být ve vašem tenantovi jedinečné. Pokud se pokusíte použít stejné telefonní číslo pro více uživatelů, zobrazí se chybová zpráva.

1. Pokud chcete telefonní číslo použít u uživatelského účtu, vyberte **Uložit**.

Při úspěšném zřízení se zobrazí zaškrtnutí pro *povolenou službu SMS*.

## <a name="test-sms-based-sign-in"></a>Testování přihlášení pomocí sms

Chcete-li otestovat uživatelský účet, který je nyní povolen pro přihlášení pomocí serveru SMS, proveďte následující kroky:

1. Otevření nového okna webového prohlížeče InPrivate nebo Inkognito[https://www.office.com][office]
1. V pravém horním rohu vyberte **Přihlásit se**.
1. Na výzvu k přihlášení zadejte telefonní číslo přidružené k uživateli v předchozí části a vyberte **další**.

    ![Zadání telefonního čísla na výzvu k přihlášení pro testovacího uživatele](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. Na uvedené telefonní číslo je odeslána textová zpráva. Chcete-li dokončit proces přihlášení, zadejte šestimístný kód uvedený v textové zprávě na výzvu k přihlášení.

    ![Zadejte potvrzovací kód odeslaný prostřednictvím textové zprávy na telefonní číslo uživatele](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. Uživatel je nyní přihlášen bez nutnosti zadejte uživatelské jméno nebo heslo.

## <a name="troubleshoot-sms-based-sign-in"></a>Poradce při potížích s přihlášením pomocí serveru SMS

Následující scénáře a postupy řešení potíží lze použít, pokud máte problémy s povolením a používáním přihlášení na základě SMS.

### <a name="phone-number-already-set-for-a-user-account"></a>Telefonní číslo již nastaveno pro uživatelský účet

Pokud se uživatel už zaregistroval pro Azure Multi-Factor Authentication a/nebo samoobslužné resetování hesla (SSPR), už má k účtu přidružené telefonní číslo. Toto telefonní číslo není automaticky k dispozici pro použití s přihlášením pomocí serveru SMS.

Uživateli, který má pro svůj účet již nastavené telefonní číslo, se na stránce **Můj profil** zobrazí tlačítko *Povolit pro přihlášení pomocí SMS.* Vyberte toto tlačítko a účet je povolený pro použití s přihlášením pomocí SMS a předchozí registrace Azure Multi-Factor Authentication nebo SSPR.

Další informace o prostředí pro koncové uživatele naleznete v tématu [SMS přihlašovací uživatelské prostředí pro telefonní číslo (náhled)](../user-help/sms-sign-in-explainer.md).

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Při pokusu o nastavení telefonního čísla v uživatelském účtu došlo k chybě.

Pokud se při pokusu o nastavení telefonního čísla pro uživatelský účet na webu Azure Portal zobrazí chyba, přečtěte si následující kroky pro řešení potíží:

1. Ujistěte se, že jste povoleni pro náhled přihlášení pomocí sms.
1. Zkontrolujte, zda je uživatelský účet povolen v zásadách metody ověřování *textovou zprávou.*
1. Ujistěte se, že jste nastavili telefonní číslo s vlastním formátováním, jak je ověřeno na webu Azure Portal (například *+1 4251234567).*
1. Ujistěte se, že telefonní číslo není použito jinde ve vašem tenantovi.
1. Zkontrolujte, zda na účtu není nastaveno žádné hlasové číslo. Pokud je hlasové číslo nastaveno, odstraňte ho a zkuste ho znovu.

## <a name="next-steps"></a>Další kroky

Další způsoby přihlášení k Azure AD bez hesla, jako je microsoft ověřovací aplikace nebo klíče zabezpečení FIDO2, najdete v [tématu možnosti ověřování bez hesla pro Azure AD][concepts-passwordless].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
