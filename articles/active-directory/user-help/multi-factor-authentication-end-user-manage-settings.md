---
title: Změna dvoufaktorové metody a nastavení ověření – Azure Active Directory
description: Přečtěte si, jak změnit metodu ověření zabezpečení a nastavení pracovního nebo školního účtu, a to na stránce Další ověření zabezpečení.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: curtand
ms.openlocfilehash: 5949f04ecc28a88e340a9c2de530031793f193a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253244"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>Změna dvoufaktorové metody a nastavení ověření

Po nastavení metod ověření zabezpečení pro pracovní nebo školní účet můžete aktualizovat všechny související podrobnosti, včetně:

- Výchozí metoda ověření zabezpečení

- Podrobnosti o metodě ověření zabezpečení, jako je vaše telefonní číslo

- Nastavení ověřovací aplikace nebo odstranění zařízení z ověřovací aplikace

## <a name="using-the-additional-security-verification-page"></a>Použití stránky Další ověření zabezpečení

Pokud vám vaše organizace poskytla konkrétní kroky týkající se zapnutí a správy dvoufaktorového ověřování, měli byste se nejprve řídit těmito pokyny. V opačném případě se k nastavení metody ověření zabezpečení dostanete na stránce [Další ověření zabezpečení.](https://docs.microsoft.com/azure/active-directory/user-help/multi-factor-authentication-end-user-first-time)

>[!Note]
>Pokud to, co vidíte na obrazovce, neodpovídá tomu, co je uvedeno v tomto článku, znamená to, že buď správce zapnul prostředí **bezpečnostních údajů (náhled),** nebo že vaše organizace poskytla vlastní portál. Další informace o novém prostředí s bezpečnostními údaji naleznete v [tématu Přehled bezpečnostních údajů (preview).](user-help-security-info-overview.md) Další informace o vlastním portálu vaší organizace získáte od oddělení technické podpory vaší organizace.

### <a name="to-get-to-the-additional-security-verification-page"></a>Postup na stránku Dodatečné ověření zabezpečení

Tento odkaz můžete sledovat na [stránce Další ověření zabezpečení](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1).

![Další stránka pro ověření zabezpečení s podrobnostmi o dostupné metodě ověření zabezpečení](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

Na stránku **Další ověření zabezpečení** se můžete dostat také takto:

1. Přihlaste [https://myapps.microsoft.com](https://myapps.microsoft.com)se do .

1. Vpravém horním rohu vyberte název účtu a pak vyberte **profil**.

1. Vyberte **další ověření zabezpečení**.  

    ![Odkaz Moje aplikace na stránku Další ověření zabezpečení](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>Informace o používání části **Hesla aplikací** na stránce Další **ověření zabezpečení** najdete v [tématu Správa hesel aplikací pro dvoufaktorové ověření](multi-factor-authentication-end-user-app-passwords.md). Hesla aplikací se mají používat jenom pro aplikace, které nepodporují dvoufaktorové ověření.

## <a name="change-your-default-security-verification-method"></a>Změna výchozí metody ověření zabezpečení

Po přihlášení k pracovnímu nebo školnímu účtu pomocí uživatelského jména a hesla se automaticky zobrazí zvolená metoda ověření zabezpečení. V závislosti na požadavcích vaší organizace se může jedná o onotací nebo ověřovací kód prostřednictvím ověřovací aplikace, textové zprávy nebo telefonního hovoru.

Pokud se rozhodnete, že chcete změnit výchozí metodu ověření zabezpečení, kterou používáte, můžete to udělat odtud.

### <a name="to-change-your-default-security-verification-method"></a>Změna výchozí metody ověření zabezpečení

1. Na stránce **Další ověření zabezpečení** vyberte metodu, kterou chcete použít, ze seznamu **upřednostňovaných možností What's preferované možnosti.** Zobrazí se všechny možnosti, ale můžete vybrat pouze ty, které vám organizace dá k dispozici.

    - **Upozornit prostřednictvím aplikace**: Prostřednictvím ověřovací aplikace budete upozorněni, že máte výzvu k ověření čekání.

    - **Zavolejte**na můj ověřovací telefon : Na mobilním zařízení vám někdo zavolá a požádá vás o ověření vašich údajů.

    - **Textový kód na můj ověřovací telefon**: Ověřovací kód dostanete jako součást textové zprávy na mobilním zařízení. Tento kód musíte zadat do výzvy k ověření pracovního nebo školního účtu.

    - **Zavolejte mi do kanceláře:** Na telefonu v kanceláři vám někdo zavolá a požádá vás o ověření vašich informací.

    - **Použijte ověřovací kód z aplikace**: K získání ověřovacího kódu, který zadáte do výzvy z pracovního nebo školního účtu, použijete ověřovací aplikaci.

2. Vyberte **Uložit**.

## <a name="add-or-change-your-phone-number"></a>Přidání nebo změna telefonního čísla

Na stránce **Další ověření zabezpečení** můžete přidat nová telefonní čísla nebo aktualizovat existující čísla.

>[!Important]
>Důrazně doporučujeme přidat sekundární telefonní číslo, které zabrání uzamčení účtu v případě ztráty nebo odcizení primárního telefonu nebo pokud získáte nový telefon a již nemáte původní primární telefonní číslo.

### <a name="to-change-your-phone-numbers"></a>Změna telefonních čísel

1. V části **Jak chcete reagovat?** na stránce **Další ověření zabezpečení** aktualizujte informace o telefonním čísle **ověřovacího telefonu** (primárního mobilního zařízení) a telefonu **Office**.

1. Zaškrtněte políčko vedle **možnosti Alternativní ověřovací telefon** a zadejte sekundární telefonní číslo, na kterém můžete přijímat textové zprávy nebo telefonní hovory, pokud nemáte přístup k primárnímu zařízení.

1. Vyberte **Uložit**.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Přidání nového účtu do aplikace Microsoft authenticator

Pracovní nebo školní účet můžete nastavit v aplikaci Microsoft Authenticator pro [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) nebo [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).

Pokud jste si už v aplikaci Microsoft Authenticator nastavili pracovní nebo školní účet, nemusíte to dělat znovu.

1. V části **Jak chcete reagovat?** na stránce **Další ověření zabezpečení** vyberte Nastavit **aplikaci Authenticator**.

    ![Nastavení pracovního nebo školního účtu v aplikaci Microsoft Authenticator](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. Postupujte podle pokynů na obrazovce, včetně naskenování QR kódu pomocí mobilního zařízení, a pak vyberte **Další**.

    Budete vyzváni ke schválení oznámení prostřednictvím aplikace Microsoft Authenticator, abyste ověřili své informace.

1. Vyberte **Uložit**.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Odstranění účtu nebo zařízení z aplikace Microsoft Authenticator

Svůj účet můžete odstranit z aplikace Microsoft Authenticator a zařízení můžete odstranit z pracovního nebo školního účtu. Zařízení obvykle odstraníte, abyste ze svého účtu trvale odstranili ztracené, odcizené nebo staré zařízení, a smažete svůj účet, abyste se pokusili vyřešit některé problémy s připojením nebo vyřešit změnu účtu, například nové uživatelské jméno.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>Odstranění zařízení z pracovního nebo školního účtu

1. V části **Jak chcete reagovat?** na stránce **Další ověření zabezpečení** vyberte tlačítko Nastavit **aplikaci Authenticator.**

1. Vyberte **Uložit**.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Odstranění účtu z aplikace Microsoft Authenticator

V aplikaci Microsoft Authenticator vyberte tlačítko **Odstranit** vedle zařízení, které chcete odstranit.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>Zapnutí dvoufaktorových ověřovacích výzev na důvěryhodném zařízení

V závislosti na nastavení organizace se může zobrazit zaškrtávací políčko **Neptat se znovu na X dní,** když v prohlížeči provedete dvoufaktorové ověření. Pokud jste vybrali tuto možnost, jak zastavit výzvy k dvoufaktorovému ověření, a pak ztratíte zařízení nebo je vaše zařízení potenciálně ohroženo, měli byste zapnout výzvy k dvoufaktorovým ověřením, abyste pomohli chránit svůj účet. Výzvy je nutné zapnout pro všechna zařízení současně. Výzvy bohužel nelze zapnout pouze pro určité zařízení.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>Zapnutí dvoufaktorových ověřovacích výzev pro vaše zařízení

Na [stránce **Další ověření zabezpečení** ](#to-get-to-the-additional-security-verification-page)vyberte Obnovit **vícefaktorové ověřování na dříve důvěryhodných zařízeních**. Při příštím přihlášení na jakémkoli zařízení budete vyzváni k provedení dvoufaktorového ověření.

## <a name="next-steps"></a>Další kroky

Po přidání nebo aktualizaci dvoufaktorového nastavení ověření můžete spravovat hesla aplikací, přihlásit se nebo získat pomoc s některými běžnými problémy souvisejícími s dvoufaktorovým ověřováním.

- [Spravujte hesla aplikací pro dvoufaktorové ověřování](multi-factor-authentication-end-user-app-passwords.md) pro všechny aplikace, které nepodporují dvoufaktorové ověření.

- [Jak se přihlásit pomocí dvoufaktorového ověření](multi-factor-authentication-end-user-signin.md)

- [Řešení běžných problémů s dvoufaktorovým ověřením](multi-factor-authentication-end-user-troubleshoot.md)
