---
title: Změna metody a nastavení dvou faktorů ověřování – Azure AD
description: Přečtěte si, jak změnit metodu ověření zabezpečení a nastavení pro svůj pracovní nebo školní účet na stránce další ověření zabezpečení.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e557ca1bfc7587b6a65957177440041bf4e902f
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2020
ms.locfileid: "75681323"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>Změna metody a nastavení dvou faktorů ověřování

Po nastavení metod ověřování zabezpečení pro svůj pracovní nebo školní účet můžete aktualizovat libovolné související podrobnosti, včetně:

- Výběr výchozí metody ověřování zabezpečení.

- Přidání nebo aktualizace podrobností metody ověření zabezpečení, jako je vaše telefonní číslo.

- Nastavení nové ověřovací aplikace nebo odstranění zařízení z ověřovací aplikace.

## <a name="using-the-additional-security-verification-page"></a>Použití stránky další ověření zabezpečení

Pokud vám vaše organizace poskytne konkrétní postup, jak zapnout a spravovat dvojúrovňové ověřování, měli byste postupovat podle těchto pokynů. V opačném případě můžete k nastavení metody ověření zabezpečení získat přístup ze stránky [Další ověření zabezpečení](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) .

>[!Note]
>Pokud se vám to, co vidíte na obrazovce, neshoduje s tím, co je zahrnuté v tomto článku, znamená to, že správce zapnul možnosti zabezpečení (Preview) nebo že má vaše organizace vlastní portál. Další informace o možnosti informace o zabezpečení najdete v tématu [Přehled informací o zabezpečení (Preview)](user-help-security-info-overview.md). Pokud potřebujete další informace o vlastním portálu vaší organizace, obraťte se na oddělení technické podpory.

### <a name="to-get-to-the-additional-security-verification-page"></a>Získání na stránku dodatečného ověření zabezpečení

- Přejít na stránku [Další ověření zabezpečení](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) .

    ![Stránka Další ověření zabezpečení s podrobnostmi o dostupné metodě ověření zabezpečení](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

    Pokud po kliknutí na tento odkaz nefunguje, můžete se také dostat na stránku **Další ověření zabezpečení** pomocí následujících kroků:

    1. Přihlaste se k webu [https://myapps.microsoft.com](https://myapps.microsoft.com).

    2. V pravém horním rohu vyberte název účtu a pak vyberte **profil**.

    3. Vyberte **Další ověření zabezpečení**.  

        ![Odkaz Moje aplikace na stránku dodatečného ověření zabezpečení](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>Informace o použití oddílu **hesla aplikace** na stránce **Další ověření zabezpečení** najdete v tématu [Správa hesel aplikací pro dvoustupňové ověřování](multi-factor-authentication-end-user-app-passwords.md). Hesla aplikací by se měla používat jenom pro aplikace, které ještě nepodporují dvojúrovňové ověřování.

## <a name="change-your-default-security-verification-method"></a>Změna výchozí metody ověření zabezpečení

Po přihlášení ke svému pracovnímu nebo školnímu účtu pomocí uživatelského jména a hesla se automaticky zobrazí vaše zvolená metoda ověřování zabezpečení. V závislosti na požadavcích vaší organizace může to být oznámení nebo ověřovací kód prostřednictvím ověřovací aplikace, textové zprávy nebo telefonního hovoru.

Pokud se rozhodnete, že chcete změnit výchozí metodu ověření zabezpečení, kterou používáte, můžete to udělat tady.

### <a name="to-change-your-default-security-verification-method"></a>Změna výchozí metody ověřování zabezpečení

1. Na stránce **Další ověření zabezpečení** vyberte metodu, která se má použít v rozevíracím seznamu **co je vaše upřednostňovaná možnost** . Zobrazí se všechny možnosti, ale budete moct vybrat jenom ty, které máte k dispozici ve vaší organizaci.

    - **Informujte mě prostřednictvím aplikace.** Budete upozorněni na vaši ověřovací aplikaci, že máte výzvu k ověření.

    - **Zavolejte mi na telefon ověřování.** Na mobilním zařízení se zobrazí telefonní hovor, který vás vyzve k ověření vašich informací.

    - **Textový kód na můj telefon pro ověření** Ověřovací kód získáte jako součást textové zprávy na vašem mobilním zařízení. Tento kód musíte zadat do výzvy k ověření pro svůj pracovní nebo školní účet.

    - **Zavolejte mi na telefon do kanceláře.** Získáte telefonní hovor na telefonu do kanceláře a budete vyzváni k ověření vašich informací.

    - **Použijte ověřovací kód z aplikace.** Pomocí aplikace ověřovatele získáte ověřovací kód, který zadáte do výzvy ze svého pracovního nebo školního účtu.

2. Vyberte **Uložit**.

## <a name="add-or-change-your-phone-number"></a>Přidání nebo změna telefonního čísla

Na stránce **Další ověření zabezpečení** můžete přidat nová telefonní čísla nebo aktualizovat stávající čísla.

>[!Important]
>Důrazně doporučujeme, abyste přidali sekundární telefonní číslo, které vám umožní zabránit tomu, aby se zabránilo uzamknutí vašeho účtu, pokud dojde ke ztrátě nebo odcizení vašeho primárního telefonu nebo když obdržíte nový telefon a už nemáte původní primární telefonní číslo.

### <a name="to-change-your-phone-numbers"></a>Změna telefonních čísel

1. V části **jak chcete reagovat?** na stránce **Další ověření zabezpečení** aktualizujte informace o telefonním číslu pro váš **telefon pro ověření** (vaše primární mobilní zařízení) a **telefon do kanceláře**.

2. Zaškrtněte políčko vedle možnosti **telefon alternativního ověřování** a potom zadejte sekundární telefonní číslo, kde můžete přijímat textové zprávy nebo telefonní hovory, pokud nemůžete získat přístup k primárnímu zařízení.

3. Vyberte **Uložit**.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Přidání nového účtu do aplikace Microsoft Authenticator

Svůj pracovní nebo školní účet můžete nastavit v aplikaci Microsoft Authenticator pro [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) nebo [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).

Pokud jste v aplikaci Microsoft Authenticator vytvořili svůj pracovní nebo školní účet, nemusíte to dělat znovu.

1. V části **jak chcete reagovat?** na stránce **Další ověření zabezpečení** vyberte tlačítko **nastavit aplikaci ověřovatele** .

    ![Nastavení pracovního nebo školního účtu v aplikaci Microsoft Authenticator](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

2. Postupujte podle pokynů na obrazovce, včetně použití vašeho mobilního zařízení k naskenování kódu QR, a pak vyberte **Další**.

    Budete požádáni, abyste schválili oznámení prostřednictvím aplikace Microsoft Authenticator, abyste ověřili vaše informace.

3. Vyberte **Uložit**.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Odstranění účtu nebo zařízení z aplikace Microsoft Authenticator

Účet můžete odstranit z aplikace Microsoft Authenticator a zařízení můžete odstranit ze svého pracovního nebo školního účtu. Obvykle odstraníte zařízení, aby se trvale odebralo ztracené, odcizené nebo staré zařízení z vašeho účtu, a odstraníte svůj účet, abyste se pokusili opravit některé problémy s připojením nebo vyřešit změnu účtu, jako je třeba nové uživatelské jméno.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>Odstranění zařízení ze svého pracovního nebo školního účtu

1. V části **jak chcete reagovat?** na stránce **Další ověření zabezpečení** vyberte tlačítko **nastavit aplikaci ověřovatele** .

2. Vyberte **Uložit**.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Odstranění účtu z aplikace Microsoft Authenticator

- Z aplikace Microsoft Authenticator vyberte tlačítko **Odstranit** vedle zařízení, které chcete odstranit.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>Zapnutí dvojúrovňovéch výzev ověřování na důvěryhodném zařízení

V závislosti na nastavení organizace se může zobrazit zaškrtávací políčko, které po X dnech po provedení dvojúrovňové ověřování v prohlížeči se **znovu nedotazuje** . Pokud jste toto políčko zaškrtli, aby se vám pozastavily dvoustupňové ověřování, a potom ztratíte vaše zařízení, nebo je možné, že vaše zařízení může ohrozit zabezpečení, měli byste pro ochranu svého účtu zapnout znovu výzvy pro ověření dvou faktorů. Bohužel nemůžete znovu zapnout výzvy pro jedno zařízení. Výzvy pro všechna vaše zařízení je třeba zapnout současně.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>Postup zapnutí služby dvojúrovňového ověřování na zařízeních znovu

- Na stránce **Další ověření zabezpečení** vyberte možnost **obnovit službu Multi-Factor Authentication u dříve důvěryhodných zařízení**.

    Až se příště přihlásíte k jakémukoli zařízení, zobrazí se výzva k provedení dvojúrovňového ověřování.

## <a name="next-steps"></a>Další kroky

Po přidání nebo aktualizaci nastavení dvojúrovňového ověřování můžete spravovat hesla aplikací, přihlašovat se nebo získat pomoc s některými běžnými problémy souvisejícími s ověřováním dvou faktorů.

- [Spravujte hesla aplikací pro dvoustupňové ověřování](multi-factor-authentication-end-user-app-passwords.md) pro všechny aplikace, které nepodporují dvojúrovňové ověřování.

- [Přihlášení Pomocí dvojúrovňového ověřování](multi-factor-authentication-end-user-signin.md)

- [Získat pomoc se dvojúrovňovém ověřováním](multi-factor-authentication-end-user-troubleshoot.md)
