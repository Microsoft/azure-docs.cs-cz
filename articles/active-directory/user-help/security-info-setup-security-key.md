---
title: Nastavení bezpečnostních údajů pro použití bezpečnostního klíče (Preview) – Azure Active Directory | Microsoft Docs
description: Jak nastavit bezpečnostní údaje pro ověření identity pomocí klíče zabezpečení rychlá identita online (FIDO2).
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d2f06b054e433c0320019548c56539d102beaad
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68386496"
---
# <a name="set-up-security-info-to-use-a-security-key-preview"></a>Nastavení bezpečnostních údajů pro použití bezpečnostního klíče (Preview)

Klíče zabezpečení můžete použít jako metodu přihlašování s neplatným heslem v rámci vaší organizace. Bezpečnostní klíč je fyzické zařízení, které se používá s jedinečným PIN kódem pro přihlášení ke svému pracovnímu nebo školnímu účtu. Vzhledem k tomu, že klíče zabezpečení vyžadují, abyste měli fyzické zařízení a něco znáte, považuje se za silnější metodu ověřování než uživatelské jméno a heslo.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Important]
>Tento obsah je určený pro uživatele. Pokud jste správce, najdete další informace o nastavení a správě vašeho prostředí Azure Active Directory (Azure AD) v [dokumentaci k Azure Active Directory](https://docs.microsoft.com/azure/active-directory).

## <a name="what-is-a-security-key"></a>Co je bezpečnostní klíč?

V současné době podporujeme několik návrhů a poskytovatelů klíčů zabezpečení pomocí metody ověřování pomocí služby [Fast identity online (Fido)](https://fidoalliance.org/fido2/) (FIDO2). Tato metoda umožňuje přihlásit se ke svému pracovnímu nebo školnímu účtu jednou a získat tak přístup ke všem cloudovým prostředkům vaší organizace a podporovaným prohlížečům.

Správce nebo vaše organizace vám poskytne klíč zabezpečení, pokud to vyžadují pro svůj pracovní nebo školní účet. Existují různé typy zabezpečovacích klíčů, které můžete použít, například klíč USB, který se připojuje k vašemu zařízení, nebo klíč NFC, na kterém klepnete na čtečku NFC. Další informace o klíči zabezpečení, včetně toho, co je třeba zadat, najdete v dokumentaci od výrobce.

> [!Note]
> Pokud nemůžete použít bezpečnostní klíč FIDO2, můžete použít i jiné metody ověřování bez hesla, jako je například aplikace Microsoft Authenticator nebo Windows Hello. Další informace o aplikaci Microsoft Authenticator najdete v tématu [co je Microsoft Authenticator aplikace?](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview). Další informace o Windows Hello najdete v tématu [Přehled Windows Hello](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Před zahájením

Než začnete registrovat svůj bezpečnostní klíč, poklikejte na toto:

- Správce tuto funkci zapnul pro použití v rámci vaší organizace.

- Jste na zařízení, na kterém běží aspoň Windows 10, verze 1903 a Microsoft Edge v prohlížeči.

- Obdrželi jste fyzický bezpečnostní klíč od svého správce nebo vaší organizace. Bezpečnostní klíč musí být FIDO2 i Microsoft. Pokud máte nějaké dotazy týkající se vašeho bezpečnostního klíče a jestli je kompatibilní, obraťte se na oddělení technické podpory vaší organizace.

## <a name="register-your-security-key"></a>Zaregistrovat klíč zabezpečení

Před přihlášením k pracovnímu nebo školnímu účtu pomocí klíče musíte připravit svůj bezpečnostní klíč pro práci se systémem Windows a jedinečným kódem PIN.

1. Pokud jste to ještě neudělali https://myprofile.microsoft.com , klikněte na stránku **můj profil** a přihlaste se.

2. Vyberte **informace o zabezpečení**, vyberte **Přidat metodu**a pak v rozevíracím seznamu **Přidat metodu** vyberte **klíč zabezpečení** .

    ![Přidat pole metody s vybraným klíčem zabezpečení](media/security-info/security-info-security-key-add-method.png)

3. Vyberte **Přidat**a potom vyberte typ klíče zabezpečení, který máte, buď **zařízení USB** nebo **zařízení NFC**.

    ![Vyberte, jestli máte k dispozici typ USB nebo NFC bezpečnostního klíče.](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Pokud si nejste jistí, jaký typ klíče zabezpečení máte, přečtěte si dokumentaci od výrobce. Pokud si nejste jistí o výrobci, požádejte o pomoc oddělení technické podpory vaší organizace.

4. Zabezpečíte klíč zabezpečení a pak v poli **bezpečnostní klíč** vyberte **Další**.

    ![Pole registrace spuštění klíče zabezpečení](media/security-info/security-info-security-key-start-setup.png)

    Zobrazí se nové okno, které vám umožní nastavit novou metodu přihlašování.

5. V poli **Nastavení nové metody přihlašování** vyberte možnost **Další**a potom:

    - Pokud je váš bezpečnostní klíč zařízením USB, vložte svůj bezpečnostní klíč do portu USB vašeho zařízení.

    - Pokud je váš bezpečnostní klíč zařízením NFC, klepněte na svůj bezpečnostní klíč k vašemu čtecímu zařízení.

6. Do pole **zabezpečení systému Windows** zadejte kód PIN jedinečného bezpečnostního klíče a pak vyberte **OK**.

    Vrátíte se do **nastavení nová metoda přihlašování** .

7. Vyberte **Další**.

8. Vraťte se na stránku **bezpečnostní údaje** , zadejte název, který budete později rozpoznávat pro svůj nový klíč zabezpečení, a pak vyberte **Další**.

    ![Stránka informace o zabezpečení, pojmenování klíče zabezpečení](media/security-info/security-info-security-key-name.png)

    Váš bezpečnostní klíč je zaregistrovaný a připravený, abyste se mohli použít pro přihlášení k pracovnímu nebo školnímu účtu.

9. Vyberte **Hotovo** , aby se zavřelo pole **bezpečnostní klíč** .

    Stránka **informace o zabezpečení** je aktualizována informacemi o klíčích zabezpečení.

    ![Stránka informace o zabezpečení se zobrazenými všemi registrovanými metodami](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Odstraní bezpečnostní klíč z bezpečnostních údajů.

Pokud už nechcete používat svůj bezpečnostní klíč, můžete klíč odstranit z bezpečnostních údajů. I když se tím zabrání použití klíče zabezpečení pro váš pracovní nebo školní účet, klíč zabezpečení bude dál ukládat vaše data a informace o přihlašovacích údajích. Chcete-li odstranit data a informace o přihlašovacích údajích z samotného klíče zabezpečení, je nutné postupovat podle pokynů v části [resetování bezpečnostního klíče kompatibilního](#reset-your-security-key) s Microsoftem v tomto článku.

1. Vyberte odkaz **Odstranit** z bezpečnostního klíče, který chcete odebrat.

2. V poli **Odstranit klíč zabezpečení** vyberte **OK** .

    Bezpečnostní klíč se odstraní a už ho nebude možné používat pro přihlášení k pracovnímu nebo školnímu účtu.

>[!Important]
>Pokud jste tento klíč zabezpečení odstranili omylem, budete ho muset zaregistrovat znovu podle pokynů v části [jak zaregistrovat klíč zabezpečení](#register-your-security-key) v tomto článku.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Správa nastavení klíče zabezpečení z nastavení systému Windows

Nastavení klíče zabezpečení můžete spravovat z aplikace **nastavení systému Windows** , včetně resetování klíče zabezpečení a vytvoření nového bezpečnostního kódu PIN klíče.

### <a name="reset-your-security-key"></a>Resetování klíče zabezpečení

Chcete-li odstranit všechny informace o účtu uložené ve vašem fyzickém bezpečnostním klíči, je nutné vrátit klíč zpět do výchozího továrního nastavení. Resetování klíče zabezpečení odstraní vše z klíče, což vám umožní začít znovu.

>[!IMPORTANT]
>Resetování klíče zabezpečení odstraní vše z klíče a resetuje ho do výchozího továrního nastavení.
>
> **Všechna data a přihlašovací údaje budou vymazány.**

#### <a name="to-reset-your-security-key"></a>Resetování klíče zabezpečení

1. Otevřete aplikaci nastavení Windows, vyberte **účty**, vyberte **Možnosti přihlášení**, vyberte **klíč zabezpečení**a pak vyberte **Spravovat**.

2. Vložte svůj bezpečnostní klíč do portu USB nebo klepnutím na čtečku NFC ověřte svoji identitu.

3. Postupujte podle pokynů na obrazovce na základě konkrétního výrobce bezpečnostního klíče. Pokud výrobce klíče není uvedený v pokynech na obrazovce, vyhledejte další informace v lokalitě výrobce.

4. Kliknutím na **Zavřít** zavřete obrazovku **Správa** .

### <a name="create-a-new-security-key-pin"></a>Vytvořit nový PIN klíč pro zabezpečení

Pro svůj bezpečnostní klíč můžete vytvořit nový PIN klíč pro zabezpečení.

#### <a name="to-create-a-new-security-key-pin"></a>Vytvoření nového PIN kódu pro klíč zabezpečení

1. Otevřete aplikaci nastavení Windows, vyberte **účty**, vyberte **Možnosti přihlášení**, vyberte **klíč zabezpečení**a pak vyberte **Spravovat**.

2. Vložte svůj bezpečnostní klíč do portu USB nebo klepnutím na čtečku NFC ověřte svoji identitu. 5
3. Vyberte **Přidat** z oblasti **PIN kód bezpečnostního klíče** , zadejte a potvrďte nový PIN klíč zabezpečení a pak vyberte **OK**.

    Bezpečnostní klíč se aktualizuje pomocí nového PIN klíče zabezpečení, který se má použít u svého pracovního nebo školního účtu. Pokud se rozhodnete znovu změnit kód PIN, můžete vybrat tlačítko **změnit** . 6
4. Kliknutím na **Zavřít** zavřete obrazovku **Správa** .

## <a name="additional-security-info-methods"></a>Další metody zabezpečení

Máte k dispozici další možnosti, jak vaše organizace kontaktuje vaši identitu na základě toho, co se you're snaží. Mezi možnosti patří:

- **Ověřovací aplikace** Stáhněte a použijte ověřovací aplikaci k získání oznámení o schválení nebo náhodně generovaného kódu schválení pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny k nastavení a používání aplikace Microsoft Authenticator najdete v tématu [Nastavení bezpečnostních údajů pro použití ověřovací aplikace](security-info-setup-auth-app.md).

- **Text mobilního zařízení** Zadejte číslo mobilního zařízení a získejte text jako kód, který budete používat pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny, jak ověřit identitu pomocí textové zprávy (SMS), najdete v tématu [Nastavení bezpečnostních údajů pro použití zasílání textových zpráv (SMS)](security-info-setup-text-msg.md).

- **Mobilní zařízení nebo pracovní telefonní hovor.** Zadejte číslo mobilního zařízení a získejte telefonní hovor pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny, jak ověřit identitu pomocí telefonního čísla, najdete v tématu [Nastavení bezpečnostních údajů pro použití telefonních hovorů](security-info-setup-phone-number.md).

- **E-mailová adresa** Zadejte svou pracovní nebo školní e-mailovou adresu, abyste získali e-mail pro resetování hesla. Tato možnost není k dispozici pro dvoustupňové ověřování. Podrobné pokyny k nastavení e-mailu najdete v tématu [Nastavení bezpečnostních údajů pro použití e-mailu](security-info-setup-email.md).

- **Bezpečnostní otázky.** Odpovězte na některé bezpečnostní otázky, které vytvořil správce vaší organizace. Tato možnost je k dispozici pouze pro resetování hesla a nikoli pro dvoustupňové ověřování. Podrobné pokyny k nastavení bezpečnostních otázek najdete v článku [Nastavení bezpečnostních informací pro použití bezpečnostních otázek](security-info-setup-questions.md) .

## <a name="next-steps"></a>Další postup

- Další informace o metodách ověřování bez hesla najdete na [webu Azure AD, který začíná ve verzi Public Preview klíčů zabezpečení FIDO2, povolení](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) blogu pro přihlášení bez hesla nebo Přečtěte si článek [co je Microsoft Authenticator aplikace?](https://docs.microsoft.com/azure/active-directory/user-help8user-help-auth-app-overview) [. ](https://www.microsoft.com/windows/windows-hello)Články s přehledem Windows Hello.

- Podrobnější informace o [bezpečnostních klíčích kompatibilních se společností Microsoft](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Resetujte heslo, pokud jste ho ztratili nebo zapomněli z [portálu pro resetování hesla](https://passwordreset.microsoftonline.com/) , nebo postupujte podle pokynů v článku [resetování svého pracovního nebo školního hesla](user-help-reset-password.md) .

- Získejte tipy pro řešení potíží a nápovědu k problémům s přihlášením v tématu se nemůžete [přihlásit k vašemu účet Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) .
