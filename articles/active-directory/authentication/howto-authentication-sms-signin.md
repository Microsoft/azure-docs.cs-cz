---
title: Přihlášení uživatele založeného na SMS pro Azure Active Directory
description: Naučte se, jak nakonfigurovat a povolit uživatelům přihlášení k Azure Active Directory pomocí serveru SMS (Preview).
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: 849057ab1ccde2e0771f0c1cd52ea399ffe4cea7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964685"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory-preview"></a>Konfigurace a povolení ověřování na základě serveru SMS pomocí Azure Active Directory (Preview)

Aby bylo možné snížit složitost a bezpečnostní rizika pro uživatele, aby se přihlásili k aplikacím a službám, Azure Active Directory (Azure AD) nabízí více možností ověřování. Ověřování založené na SMS, které je aktuálně ve verzi Preview, umožňuje uživatelům přihlásit se, aniž by museli zadávat nebo dokonce znát uživatelské jméno a heslo. Po vytvoření účtu správcem identity můžou jejich telefonní číslo zadat na příkazovém řádku pro přihlášení a zadat ověřovací kód, který se jim pošle prostřednictvím textové zprávy. Tato metoda ověřování zjednodušuje přístup k aplikacím a službám, zejména pro pracovníky front-line.

V tomto článku se dozvíte, jak povolit ověřování pomocí serveru SMS pro vybrané uživatele nebo skupiny v Azure AD.

> [!NOTE]
> Ověřování pomocí serveru SMS pro uživatele je funkce verze Public Preview služby Azure Active Directory. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Aby bylo možné povolit ověřování pomocí serveru SMS, potřebujete oprávnění *globálního správce* v TENANTOVI Azure AD.
* Každý uživatel, který je povolený v zásadě metody ověřování pomocí textové zprávy, musí mít licenci, a to i v případě, že ho nepoužívají. Každý povolený uživatel musí mít jednu z následujících licencí Azure AD, EMS Microsoft 365:
    * [Azure AD Premium P1 nebo P2][azuread-licensing]
    * [Microsoft 365 (M365) F1 nebo F3][m365-firstline-workers-licensing]
    * [Enterprise mobility + Security (EMS) E3 nebo E5][ems-licensing] nebo [Microsoft 365 (M365) E3 nebo E5][m365-licensing]

## <a name="limitations"></a>Omezení

Během veřejné verze Preview ověřování založeného na serveru SMS platí následující omezení:

* Ověřování pomocí serveru SMS není aktuálně kompatibilní s Azure Multi-Factor Authentication.
* S výjimkou týmů nejsou ověřování pomocí serveru SMS v současnosti kompatibilní s nativními aplikacemi Office.
* Ověřování pomocí serveru SMS se pro účty B2B nedoporučuje.
* Federované uživatele se nebudou ověřovat v domovském tenantovi. Ověřují se jenom v cloudu.

## <a name="enable-the-sms-based-authentication-method"></a>Povolení metody ověřování založené na SMS

Existují tři hlavní kroky, jak povolit a používat ověřování pomocí serveru SMS ve vaší organizaci:

* Povolte zásadu metoda ověřování.
* Vyberte uživatele nebo skupiny, které mohou používat metodu ověřování založenou na serveru SMS.
* Přiřaďte telefonní číslo každému uživatelskému účtu.
    * Toto telefonní číslo se dá přiřadit v Azure Portal (viz tento článek) a v části *Moji zaměstnanci* nebo *můj profil*.

Nejdřív pro vašeho tenanta Azure AD povolíme ověřování pomocí SMS.

1. Přihlaste se k [Azure Portal][azure-portal] jako *globální správce*.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V navigační nabídce na levé straně okna Azure Active Directory vyberte **metody ověřování > zabezpečení > zásady metody ověřování (Preview)**.

    [![Vyhledejte a vyberte v Azure Portal okno zásady ověřování (Preview).](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png)](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. V seznamu dostupných metod ověřování vyberte **text zpráva**.
1. Nastavte **Povolit** na *Ano*.

    ![Povolit ověřování textu v okně zásady metody ověřování](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    Můžete povolit ověřování pomocí serveru SMS pro *všechny uživatele* nebo *Vybrat uživatele* a skupiny. V další části povolíte ověřování pomocí serveru SMS pro testovacího uživatele.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>Přiřazení metody ověřování uživatelům a skupinám

Při ověřování pomocí serveru SMS ve vašem tenantovi Azure AD teď vyberte nějaké uživatele nebo skupiny, kterým chcete tuto metodu ověřování povolit.

1. V okně zásady ověřování textové zprávy nastavte **cíl** na *Vybrat uživatele*.
1. Zvolte možnost **Přidat uživatele nebo skupiny**a pak vyberte testovacího uživatele nebo skupinu, například *uživatele contoso* nebo *Contoso SMS*.

    [![Vyberte možnost Uživatelé nebo skupiny a povolte ověřování pomocí serveru SMS v Azure Portal.](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png)](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. Když vyberete uživatele nebo skupiny, klikněte na **Vybrat**a **uložte** aktualizované zásady metody ověřování.

Každý uživatel, který je povolený v zásadě metody ověřování pomocí textové zprávy, musí mít licenci, a to i v případě, že ho nepoužívají. Ujistěte se, že máte příslušné licence pro uživatele, které povolíte v zásadách metody ověřování, zejména pokud tuto funkci povolíte pro velké skupiny uživatelů.

## <a name="set-a-phone-number-for-user-accounts"></a>Nastavení telefonního čísla pro uživatelské účty

Pro uživatele jsou nyní povoleny ověřování pomocí serveru SMS, ale jejich telefonní číslo musí být přidruženo k profilu uživatele ve službě Azure AD, aby se mohli přihlásit. Uživatel může [toto telefonní číslo nastavit](../user-help/sms-sign-in-explainer.md) v *mém profilu*, nebo můžete toto telefonní číslo přiřadit pomocí Azure Portal. Telefonní čísla můžou nastavit *globální správci*, *Správci ověřování*nebo *Správci privilegovaného ověřování*.

Pokud je telefonní číslo nastavené na znaménko SMS, je k dispozici také pro použití s [Azure Multi-Factor Authentication][tutorial-azure-mfa] a [Samoobslužné resetování hesla][tutorial-sspr].

1. Vyhledejte a vyberte **Azure Active Directory**.
1. V navigační nabídce na levé straně okna Azure Active Directory vyberte **Uživatelé**.
1. V předchozí části vyberte uživatele, kterého jste povolili pro ověřování pomocí serveru SMS, jako je například *uživatel společnosti Contoso*, a pak vyberte **metody ověřování**.
1. Zadejte telefonní číslo uživatele, včetně kódu země, například *+ 1 XXXXXXXXX*. Azure Portal ověří, jestli je telefonní číslo ve správném formátu.

    ![Nastavení telefonního čísla pro uživatele v Azure Portal pro použití s ověřováním pomocí serveru SMS](./media/howto-authentication-sms-signin/set-user-phone-number.png)

    Telefonní číslo musí být ve vašem tenantovi jedinečné. Pokud se pokusíte použít stejné telefonní číslo pro více uživatelů, zobrazí se chybová zpráva.

1. Chcete-li použít telefonní číslo na účet uživatele, vyberte možnost **Uložit**.

Po úspěšném zřízení se u *přihlašovacích práv SMS*zobrazí zaškrtnutí.

## <a name="test-sms-based-sign-in"></a>Testování přihlášení pomocí serveru SMS

Pokud chcete otestovat uživatelský účet, který je teď povolený pro přihlašování založené na SMS, proveďte následující kroky:

1. Otevřete nové okno webového prohlížeče InPrivate nebo anonymním. [https://www.office.com][office]
1. V pravém horním rohu vyberte **Přihlásit**se.
1. Na příkazovém řádku pro přihlášení zadejte telefonní číslo přidružené k uživateli v předchozí části a pak vyberte **Další**.

    ![Zadejte telefonní číslo na přihlašovací výzvu pro testovacího uživatele.](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. Do poskytnutého telefonního čísla se pošle textová zpráva. Chcete-li dokončit proces přihlášení, zadejte 25místný kód uvedený v textové zprávě na příkazovém řádku pro přihlášení.

    ![Zadejte potvrzovací kód odeslaný přes textovou zprávu na telefonní číslo uživatele.](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. Uživatel je nyní přihlášen, aniž by bylo nutné zadat uživatelské jméno nebo heslo.

## <a name="troubleshoot-sms-based-sign-in"></a>Řešení potíží s přihlašováním pomocí SMS

Následující scénáře a postup řešení potíží se můžou použít, pokud máte problémy s povolením a použitím přihlášení založeného na SMS.

### <a name="phone-number-already-set-for-a-user-account"></a>Telefonní číslo již bylo nastaveno pro uživatelský účet.

Pokud již byl uživatel zaregistrován pro Azure Multi-Factor Authentication nebo Samoobslužné resetování hesla (SSPR), mají již telefonní číslo přidružené ke svému účtu. Toto telefonní číslo není k dispozici automaticky pro přihlášení pomocí serveru SMS.

Uživatel s telefonním číslem, který je už nastavený pro svůj účet, zobrazuje tlačítko, které na stránce **můj profil** *povolí přihlášení ke službě SMS* . Vyberte toto tlačítko a účet je povolený pro přihlašování založené na SMS a předchozí registraci Azure Multi-Factor Authentication nebo SSPR.

Další informace o činnosti koncového uživatele najdete v tématu [uživatelské prostředí přihlášení k serveru SMS pro telefonní číslo (Preview)](../user-help/sms-sign-in-explainer.md).

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Při pokusu o nastavení telefonního čísla na účtu uživatele došlo k chybě.

Pokud se při pokusu o nastavení telefonního čísla pro uživatelský účet v Azure Portal zobrazí chyba, přečtěte si následující postup řešení potíží:

1. Ujistěte se, že jste povolili přihlášení k verzi Preview založené na SMS.
1. Ověřte, zda je uživatelský účet povolen v zásadě metoda ověřování *text zprávy* .
1. Ujistěte se, že jste nastavili telefonní číslo se správným formátováním, jak je ověřeno v Azure Portal (například *+ 1 4251234567*).
1. Ujistěte se, že se telefonní číslo nepoužívá jinde ve vašem tenantovi.
1. Ověřte, že na účtu není nastavené žádné číslo hlasu. Pokud je nastaveno číslo hlasu, odstraňte jej a pokuste se o telefonní číslo znovu.

## <a name="next-steps"></a>Další kroky

Další způsoby, jak se přihlásit ke službě Azure AD bez hesla, například Microsoft Authenticator aplikace nebo klíčů zabezpečení FIDO2, najdete v tématu [Možnosti ověřování bez hesla pro službu Azure AD][concepts-passwordless].

K [Povolení][rest-enable] nebo [zakázání][rest-disable] přihlášení pomocí serveru SMS můžete také použít Microsoft Graph REST API beta.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md
[rest-enable]: /graph/api/phoneauthenticationmethod-enablesmssignin?view=graph-rest-beta&tabs=http
[rest-disable]: /graph/api/phoneauthenticationmethod-disablesmssignin?view=graph-rest-beta&tabs=http

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
[o365-f1]: https://www.microsoft.com/microsoft-365/business/office-365-f1?market=af
[o365-f3]: https://www.microsoft.com/microsoft-365/business/office-365-f3?activetab=pivot%3aoverviewtab
