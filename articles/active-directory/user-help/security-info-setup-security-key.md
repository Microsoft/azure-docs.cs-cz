---
title: Nastavení bezpečnostního klíče jako metody ověřování – Azure AD
description: Jak nastavit stránku informací o zabezpečení (Preview), abyste ověřili, že vaše identita má jako metodu ověření používat klíč zabezpečení rychlá identita online (FIDO2).
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: c056e439deac71417ff14dcfc3f2c3c95db41946
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88797662"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>Nastavte bezpečnostní klíč jako metodu ověřování.

Klíče zabezpečení můžete použít jako metodu přihlašování s neplatným heslem v rámci vaší organizace. Bezpečnostní klíč je fyzické zařízení, které se používá s jedinečným PIN kódem pro přihlášení ke svému pracovnímu nebo školnímu účtu. Vzhledem k tomu, že klíče zabezpečení vyžadují, abyste měli fyzické zařízení a něco znáte, považuje se za silnější metodu ověřování než uživatelské jméno a heslo.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> Pokud nevidíte možnost bezpečnostní klíč, je možné, že vaše organizace neumožňuje použít tuto možnost k ověření. V takovém případě budete muset zvolit jinou metodu nebo požádat o další pomoc oddělení technické podpory vaší organizace.

## <a name="security-verification-versus-password-reset-authentication"></a>Ověření zabezpečení oproti ověřování hesla pro resetování hesla

Metody bezpečnostních údajů se používají pro ověřování dvou faktorů zabezpečení i pro resetování hesla. Nicméně ne všechny metody lze použít pro obojí.

| Metoda | Použití |
| ------ | -------- |
| Ověřovací aplikace | Dvojúrovňové ověřování a ověřování nového hesla. |
| Textové zprávy | Dvojúrovňové ověřování a ověřování nového hesla. |
| Telefonní hovory | Dvojúrovňové ověřování a ověřování nového hesla. |
| Bezpečnostní klíč | Dvojúrovňové ověřování a ověřování nového hesla. |
| E-mailový účet | Jenom ověřování heslem. Budete muset zvolit jinou metodu pro dvojúrovňové ověřování. |
| Bezpečnostní otázky | Jenom ověřování heslem. Budete muset zvolit jinou metodu pro dvojúrovňové ověřování. |

## <a name="what-is-a-security-key"></a>Co je bezpečnostní klíč?

V současné době podporujeme několik návrhů a poskytovatelů bezpečnostních klíčů pomocí ověřovacích protokolů [Fido (Fast identity online)](https://fidoalliance.org/fido2/) (FIDO2) s nehesly. Tyto klíče vám umožní přihlásit se ke svému pracovnímu nebo školnímu účtu a získat přístup k cloudovým prostředkům vaší organizace, když jsou na podporovaném zařízení a webovém prohlížeči.

Správce nebo vaše organizace vám poskytne klíč zabezpečení, pokud to vyžadují pro svůj pracovní nebo školní účet. Existují různé typy zabezpečovacích klíčů, které můžete použít, například klíč USB, který se připojuje k vašemu zařízení, nebo klíč NFC, na kterém klepnete na čtečku NFC. Další informace o klíči zabezpečení, včetně toho, co je třeba zadat, najdete v dokumentaci od výrobce.

> [!Note]
> Pokud nemůžete použít bezpečnostní klíč FIDO2, můžete použít i jiné metody ověřování bez hesla, jako je například aplikace Microsoft Authenticator nebo Windows Hello. Další informace o aplikaci Microsoft Authenticator najdete v tématu [co je Microsoft Authenticator aplikace?](user-help-auth-app-overview.md). Další informace o Windows Hello najdete v tématu [Přehled Windows Hello](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Než začnete

Předtím, než budete moci zaregistrovat klíč zabezpečení, musí být splněny následující podmínky:

- Správce tuto funkci zapnul pro použití v rámci vaší organizace.

- Jste na zařízení s Windows 10 Květen 2019 Update a pomocí podporovaného prohlížeče.

- Máte fyzický bezpečnostní klíč schválený vaším správcem nebo vaší organizací. Bezpečnostní klíč musí být FIDO2 i Microsoft. Pokud máte nějaké dotazy týkající se vašeho bezpečnostního klíče a jestli je kompatibilní, obraťte se na oddělení technické podpory vaší organizace.

## <a name="register-your-security-key"></a>Zaregistrujte si bezpečnostní klíč

Abyste se mohli přihlásit ke svému pracovnímu nebo školnímu účtu pomocí klíče, musíte si vytvořit svůj bezpečnostní klíč a dát mu jedinečný kód PIN. K vašemu účtu můžete mít zaregistrovaných až 10 klíčů. 

1. Pokud jste to ještě neudělali, klikněte na stránku **můj profil** https://myaccount.microsoft.com a přihlaste se.

2. Vyberte **informace o zabezpečení**, vyberte **Přidat metodu** a pak v rozevíracím seznamu **Přidat metodu** vyberte **klíč zabezpečení** .

    ![Přidat pole metody s vybraným klíčem zabezpečení](media/security-info/security-info-security-key-add-method.png)

3. Vyberte **Přidat** a potom vyberte typ klíče zabezpečení, který máte, buď **zařízení USB** nebo **zařízení NFC**.

    ![Vyberte, jestli máte k dispozici typ USB nebo NFC bezpečnostního klíče.](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Pokud si nejste jistí, jaký typ klíče zabezpečení máte, přečtěte si dokumentaci od výrobce. Pokud si nejste jistí o výrobci, požádejte o pomoc oddělení technické podpory vaší organizace.

4. Zabezpečíte klíč zabezpečení a pak v poli **bezpečnostní klíč** vyberte **Další**.

    ![Pole registrace spuštění klíče zabezpečení](media/security-info/security-info-security-key-start-setup.png)

    Zobrazí se nové okno, které vám umožní nastavit novou metodu přihlašování.

5. V poli **Nastavení nové metody přihlašování** vyberte možnost **Další** a potom:

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

Pokud přestanete nebo už nechcete používat bezpečnostní klíč, můžete klíč odstranit z bezpečnostních údajů. I když se tím zabrání použití klíče zabezpečení pro váš pracovní nebo školní účet, klíč zabezpečení bude dál ukládat vaše data a informace o přihlašovacích údajích. Chcete-li odstranit data a informace o přihlašovacích údajích z samotného klíče zabezpečení, je nutné postupovat podle pokynů v části [resetování bezpečnostního klíče kompatibilního s Microsoftem](#reset-your-security-key) v tomto článku.

1. Vyberte odkaz **Odstranit** z bezpečnostního klíče, který chcete odebrat.

2. V poli **Odstranit klíč zabezpečení** vyberte **OK** .

    Bezpečnostní klíč se odstraní a už ho nebude možné používat pro přihlášení k pracovnímu nebo školnímu účtu.

>[!Important]
>Pokud bezpečnostní klíč odstraníte omylem, můžete ho zaregistrovat znovu podle pokynů v části [jak zaregistrovat klíč zabezpečení](#register-your-security-key) v tomto článku.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Správa nastavení klíče zabezpečení z nastavení systému Windows

Nastavení klíče zabezpečení můžete spravovat z aplikace **nastavení systému Windows** , včetně resetování klíče zabezpečení a vytvoření nového bezpečnostního kódu PIN klíče.

### <a name="reset-your-security-key"></a>Resetování klíče zabezpečení

Chcete-li odstranit všechny informace o účtu uložené ve vašem fyzickém bezpečnostním klíči, je nutné vrátit klíč zpět do výchozího továrního nastavení. Resetování klíče zabezpečení odstraní vše z klíče, což vám umožní začít znovu.

>[!IMPORTANT]
>Resetování klíče zabezpečení odstraní vše z klíče a resetuje ho do výchozího továrního nastavení.
>
> **Všechna data a přihlašovací údaje budou vymazány.**

#### <a name="to-reset-your-security-key"></a>Resetování klíče zabezpečení

1. Otevřete aplikaci nastavení Windows, vyberte **účty**, vyberte **Možnosti přihlášení**, vyberte **klíč zabezpečení** a pak vyberte **Spravovat**.

2. Vložte svůj bezpečnostní klíč do portu USB nebo klepnutím na čtečku NFC ověřte svoji identitu.

3. Postupujte podle pokynů na obrazovce na základě konkrétního výrobce bezpečnostního klíče. Pokud výrobce klíče není uvedený v pokynech na obrazovce, vyhledejte další informace v lokalitě výrobce.

4. Kliknutím na **Zavřít** zavřete obrazovku **Správa** .

### <a name="create-a-new-security-key-pin"></a>Vytvořit nový PIN klíč pro zabezpečení

Pro svůj bezpečnostní klíč můžete vytvořit nový PIN klíč pro zabezpečení.

#### <a name="to-create-a-new-security-key-pin"></a>Vytvoření nového PIN kódu pro klíč zabezpečení

1. Otevřete aplikaci nastavení Windows, vyberte **účty**, vyberte **Možnosti přihlášení**, vyberte **klíč zabezpečení** a pak vyberte **Spravovat**.

2. Vložte svůj bezpečnostní klíč do portu USB nebo klepnutím na čtečku NFC ověřte svoji identitu.
3. Vyberte **Přidat** z oblasti **PIN kód bezpečnostního klíče** , zadejte a potvrďte nový PIN klíč zabezpečení a pak vyberte **OK**.

     Bezpečnostní klíč se aktualizuje pomocí nového PIN klíče zabezpečení, který se má použít u svého pracovního nebo školního účtu. Pokud se rozhodnete znovu změnit kód PIN, můžete vybrat tlačítko **změnit** .
4. Kliknutím na **Zavřít** zavřete obrazovku **Správa** .

## <a name="additional-security-info-methods"></a>Další metody zabezpečení

Aby bylo možné zaregistrovat klíč zabezpečení, musíte mít zaregistrovanou alespoň jednu metodu dalšího ověření zabezpečení. Další informace najdete v [části Přehled](./security-info-setup-auth-app.md) . 

## <a name="next-steps"></a>Další kroky

- Další informace o metodách ověřování bez hesla najdete v článku [Microsoft Azure AD začíná ve verzi Public Preview klíčů zabezpečení FIDO2, povolení](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) blogu pro přihlášení bez hesla nebo Přečtěte si článek [co je Microsoft Authenticator aplikace?](user-help-auth-app-overview.md) a [Přehled Windows Hello](https://www.microsoft.com/windows/windows-hello) .

- Podrobnější informace o [bezpečnostních klíčích kompatibilních se společností Microsoft](/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Resetujte heslo, pokud jste ho ztratili nebo zapomněli z [portálu pro resetování hesla](https://passwordreset.microsoftonline.com/) , nebo postupujte podle pokynů v článku [resetování svého pracovního nebo školního hesla](active-directory-passwords-update-your-own-password.md) .

- Získejte tipy pro řešení potíží a nápovědu k problémům s přihlášením v tématu se [nemůžete přihlásit k vašemu účet Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) .