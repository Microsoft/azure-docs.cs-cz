---
title: Nastavení klíče zabezpečení jako metody ověření – Azure AD
description: Jak nastavit stránku Bezpečnostní údaje (náhled) pro ověření vaší identity tak, aby jako metodu ověření používal klíč zabezpečení Fast Identity Online (FIDO2).
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: ee868ded0dc3a46f2ec20d0141aa2576cbf71f45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062332"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>Nastavení bezpečnostního klíče jako metody ověření

Bezpečnostní klíče můžete použít jako metodu bezhesla přihlášení v rámci vaší organizace. Bezpečnostní klíč je fyzické zařízení, které se používá s jedinečným KÓDEM PIN pro přihlášení k vašemu pracovnímu nebo školnímu účtu. Vzhledem k tomu, že bezpečnostní klíče vyžadují, abyste měli fyzické zařízení a něco, co znáte pouze vy, je to považováno za silnější metodu ověření než uživatelské jméno a heslo.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> Pokud možnost klíče zabezpečení nevidíte, je možné, že vaše organizace neumožňuje použít tuto možnost k ověření. V takovém případě budete muset zvolit jinou metodu nebo se obrátit na technickou podporu vaší organizace s žádostí o další pomoc.

## <a name="security-verification-versus-password-reset-authentication"></a>Ověření zabezpečení versus ověřování při resetování hesla

Metody bezpečnostních informací se používají jak pro dvoufaktorové ověření zabezpečení, tak pro resetování hesla. Ne všechny metody však lze použít pro obě metody.

| Metoda | Použití |
| ------ | -------- |
| Aplikace Authenticator | Dvoufaktorové ověření a ověřování heslem. |
| Textové zprávy | Dvoufaktorové ověření a ověřování heslem. |
| Telefonní hovory | Dvoufaktorové ověření a ověřování heslem. |
| Klíč zabezpečení | Dvoufaktorové ověření a ověřování heslem. |
| E-mailový účet | Pouze ověřování pro resetování hesla. Budete muset zvolit jinou metodu pro dvoufaktorové ověření. |
| Bezpečnostní otázky | Pouze ověřování pro resetování hesla. Budete muset zvolit jinou metodu pro dvoufaktorové ověření. |

## <a name="what-is-a-security-key"></a>Co je bezpečnostní klíč?

V současné době podporujeme několik návrhů a poskytovatelů bezpečnostních klíčů pomocí ověřovacích protokolů [Fast Identity Online (FIDO)](https://fidoalliance.org/fido2/) (FIDO2). Tyto klíče umožňují přihlásit se k vašemu pracovnímu nebo školnímu účtu a získat přístup ke cloudovým prostředkům vaší organizace na podporovaném zařízení a webovém prohlížeči.

Správce nebo vaše organizace vám poskytne bezpečnostní klíč, pokud ho budou potřebovat pro váš pracovní nebo školní účet. Existují různé typy bezpečnostních klíčů, které můžete použít, například klíč USB, který připojíte k zařízení, nebo klíč NFC, na který klepnete na čtečku NFC. Další informace o klíči zabezpečení, včetně jeho typu, naleznete v dokumentaci výrobce.

> [!Note]
> Pokud nemůžete použít bezpečnostní klíč FIDO2, existují i jiné metody ověření bez hesla, které můžete použít, jako je například aplikace Microsoft Authenticator nebo Windows Hello. Další informace o aplikaci Microsoft Authenticator najdete v [tématu Co je aplikace Microsoft Authenticator?](user-help-auth-app-overview.md). Další informace o Windows Hello najdete v tématu [Windows Hello overview](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Než začnete

Než budete moci zaregistrovat bezpečnostní klíč, musí být splněny následující:

- Správce tuto funkci zapnul pro použití ve vaší organizaci.

- Jste na zařízení, se kterými se používá aktualizace Windows 10 z května 2019 a používáte podporovaný prohlížeč.

- Máte fyzický bezpečnostní klíč schválený správcem nebo vaší organizací. Váš bezpečnostní klíč musí splňovat požadavky FIDO2 i Microsoftu. Máte-li jakékoli dotazy týkající se bezpečnostního klíče a kompatibility, obraťte se na oddělení technické podpory vaší organizace.

## <a name="register-your-security-key"></a>Registrace bezpečnostního klíče

Před přihlášením k pracovnímu nebo školnímu účtu pomocí klíče je nutné vytvořit bezpečnostní klíč a poskytnout mu jedinečný kód PIN. U svého účtu můžete zaregistrovat až 10 klíčů. 

1. Pokud jste tak ještě https://myprofile.microsoft.com neučinili, přejděte na stránku Můj **profil** a přihlaste se.

2. Vyberte **Bezpečnostní údaje**, vyberte **Přidat metodu**a pak v rozevíracím seznamu **Přidat metodu** vyberte **bezpečnostní klíč.**

    ![Pole Přidat metodu s vybraným bezpečnostním klíčem](media/security-info/security-info-security-key-add-method.png)

3. Vyberte **Přidat**a pak vyberte typ bezpečnostního klíče, který máte, **zařízení USB** nebo **zařízení NFC**.

    ![Zvolte, zda máte bezpečnostní klíč typu USB nebo NFC](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Pokud si nejste jisti, jaký typ bezpečnostního klíče máte, přečtěte si dokumentaci výrobce. Pokud si o výrobci nejste jisti, požádejte o pomoc oddělení technické podpory vaší organizace.

4. Mějte svůj bezpečnostní klíč fyzicky k dispozici a v poli **Schránka s klíči Zabezpečení** vyberte **Další**.

    ![Registrační pole spuštění bezpečnostního klíče](media/security-info/security-info-security-key-start-setup.png)

    Zobrazí se nové pole, které vám pomůže s nastavením nové metody přihlášení.

5. V poli **Nastavení nové metody přihlášení** vyberte **Další**a pak:

    - Pokud je vaším bezpečnostním klíčem zařízení USB, vložte jej do portu USB zařízení.

    - Pokud je vaším bezpečnostním klíčem zařízení NFC, klepněte na svůj bezpečnostní klíč do čtečky.

6. Zadejte jedinečný kód PIN bezpečnostního klíče do pole **zabezpečení systému Windows** a pak vyberte **OK**.

    Vrátíte se do pole **Nastavení nové metody přihlášení.**

7. Vyberte **další**.

8. Vraťte se na stránku **Bezpečnostní údaje,** zadejte název, který později poznáte pro nový bezpečnostní klíč, a vyberte **Další**.

    ![Stránka s bezpečnostními údaji, pojmenování bezpečnostního klíče](media/security-info/security-info-security-key-name.png)

    Bezpečnostní klíč je registrovaný a připravený k přihlášení k pracovnímu nebo školnímu účtu.

9. Chcete-li zavřít okno **s klíči zabezpečení,** **vyberte Hotovo.**

    Stránka **Informace o zabezpečení** je aktualizována informacemi o klíči zabezpečení.

    ![Stránka s bezpečnostními údaji se všemi registrovanými metodami](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Odstranění bezpečnostního klíče z bezpečnostních údajů

Pokud jste chybně nebo již nechcete použít bezpečnostní klíč, můžete jej odstranit z bezpečnostních údajů. I když to zabrání použití klíče zabezpečení s pracovním nebo školním účtem, klíč zabezpečení nadále ukládá vaše data a informace o pověření. Chcete-li odstranit data a informace o pověření ze samotného klíče zabezpečení, postupujte podle pokynů v části [Resetování klíče zabezpečení kompatibilní se společností Microsoft](#reset-your-security-key) v tomto článku.

1. Vyberte odkaz **Odstranit** z bezpečnostního klíče, který chcete odebrat.

2. V poli **Odstranit bezpečnostní klíč** vyberte **Ok.**

    Váš bezpečnostní klíč se smaže a už ho nebudete moct používat k přihlášení k pracovnímu nebo školnímu účtu.

>[!Important]
>Pokud bezpečnostní klíč odstraníte omylem, můžete jej znovu zaregistrovat pomocí pokynů v části [Jak zaregistrovat klíč zabezpečení](#register-your-security-key) v tomto článku.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Správa nastavení bezpečnostního klíče v nastavení systému Windows

Nastavení klíče zabezpečení můžete spravovat v aplikaci **Nastavení systému Windows,** včetně resetování bezpečnostního klíče a vytvoření nového kódu PIN bezpečnostního klíče.

### <a name="reset-your-security-key"></a>Obnovení bezpečnostního klíče

Pokud chcete odstranit všechny informace o účtu uložené ve vašem fyzickém klíči zabezpečení, musíte klíč vrátit zpět do výchozího nastavení výroby. Resetováním bezpečnostního klíče odstraníte vše z klíče, což vám umožní začít znovu.

>[!IMPORTANT]
>Resetováním bezpečnostního klíče odstraníte vše z klíče a resetujete ho na výchozí hodnoty z výroby.
>
> **Všechna data a pověření budou vymazána.**

#### <a name="to-reset-your-security-key"></a>Resetování bezpečnostního klíče

1. Otevřete aplikaci Nastavení Windows, vyberte **Účty**, vyberte **Možnosti přihlášení**, vyberte **Bezpečnostní klíč**a pak vyberte **Spravovat**.

2. Vložte bezpečnostní klíč do portu USB nebo klepnutím na čtečku NFC ověřte svou identitu.

3. Postupujte podle pokynů na obrazovce na základě konkrétního výrobce bezpečnostních klíčů. Pokud výrobce klíčů není uveden v pokynech na obrazovce, další informace naleznete na webu výrobce.

4. Výběrem **možnosti Zavřít** zavřete obrazovku **Správa.**

### <a name="create-a-new-security-key-pin"></a>Vytvoření nového kódu PIN bezpečnostního klíče

Pro bezpečnostní klíč můžete vytvořit nový kód PIN bezpečnostního klíče.

#### <a name="to-create-a-new-security-key-pin"></a>Vytvoření nového kódu PIN bezpečnostního klíče

1. Otevřete aplikaci Nastavení Windows, vyberte **Účty**, vyberte **Možnosti přihlášení**, vyberte **Bezpečnostní klíč**a pak vyberte **Spravovat**.

2. Vložte bezpečnostní klíč do portu USB nebo klepnutím na čtečku NFC ověřte svou identitu.
3. V části **PIN bezpečnostního klíče** vyberte **Přidat,** zadejte a potvrďte nový kód PIN bezpečnostního klíče a pak vyberte **OK**.

     Klíč zabezpečení je aktualizován novým kódem PIN bezpečnostního klíče pro použití s pracovním nebo školním účtem. Pokud se rozhodnete kód PIN znovu změnit, můžete vybrat tlačítko **Změnit.**
4. Výběrem **možnosti Zavřít** zavřete obrazovku **Správa.**

## <a name="additional-security-info-methods"></a>Další metody bezpečnostních informací

Chcete-li zaregistrovat klíč zabezpečení, musíte mít zaregistrovánalespoň jednu další metodu ověření zabezpečení. Další informace naleznete v [části Přehled.](security-info-add-update-methods-overview.md) 

## <a name="next-steps"></a>Další kroky

- Další informace o metodách ověřování bez hesla najdete v [článku Microsoft Azure AD začíná veřejná preview klíčů zabezpečení FIDO2, povolení přihlašovacích údajů bez hesla](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) blog nebo si přečtěte co je Microsoft [Authenticator app?](user-help-auth-app-overview.md) a [Windows Hello přehled](https://www.microsoft.com/windows/windows-hello) články.

- Podrobnější informace o [klíčích zabezpečení kompatibilních se společností Microsoft](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Pokud jste heslo ztratili nebo zapomněli, resetujte ho na [portálu pro resetování hesla](https://passwordreset.microsoftonline.com/) nebo postupujte podle pokynů v článku [Obnovit pracovní nebo školní heslo.](active-directory-passwords-update-your-own-password.md)

- V článku Nelze se [přihlásit k účtu Microsoft,](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) najdete tipy pro řešení potíží a nápovědu k problémům s přihlášením.
