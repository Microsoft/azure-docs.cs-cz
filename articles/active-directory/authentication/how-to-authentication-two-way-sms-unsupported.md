---
title: Obousměrný server SMS už není podporovaný – Azure Active Directory
description: Vysvětluje, jak povolit další metodu pro uživatele, kteří stále používají dvoucestné SMS.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/02/2021
ms.author: justinha
author: rhicock
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d25ed1e46823ec6d820addf3944c96c97fcabcb8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101689024"
---
# <a name="two-way-sms-unsupported"></a>Dvoucestné nepodporované SMS

Oboustranný server SMS pro Azure AD Multi-Factor Authentication (MFA) byl původně zastaralý v 2018 a už není podporovaný po 24. únoru 2021. Správci by měli povolit jinou metodu pro uživatele, kteří stále používají oboustranný SMS.

E-mailová oznámení a oznámení o Azure Portal Service Health (informační zprávy na portálu) se poslala postiženým správcům od 8. prosince 2020 a 28. ledna 2021. Výstrahy se přizpůsobily rolím pro vlastníka, spoluvlastníkům, správcům a správcům služby, které jsou svázané s předplatnými. Pokud jste již dokončili následující kroky, není nutná žádná akce.

## <a name="required-actions"></a>Požadované akce

1. Povolte mobilní aplikaci pro uživatele, pokud jste to ještě neudělali. Další informace najdete v tématu [Povolení ověřování mobilních aplikací pomocí MFA serveru](howto-mfaserver-deploy-mobileapp.md).
1. Informování koncových uživatelů o aktivaci mobilní aplikace na [portálu User Portal](howto-mfaserver-deploy-userportal.md) pro MFA Server [Microsoft Authenticator aplikace](https://www.microsoft.com/en-us/account/authenticator) je doporučenou možností ověření, protože je bezpečnější než dvoucestné SMS. Další informace najdete v tématu čas, [jak zavěsit na přenosech telefonů pro ověřování](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752).
1. Změňte nastavení uživatele z obousměrné textové zprávy na mobilní aplikace jako výchozí metodu.

## <a name="faq"></a>Časté otázky

### <a name="what-if-i-dont-change-the-default-method-from-two-way-sms-to-the-mobile-app"></a>Co když neměním výchozí metodu z obousměrné služby SMS na mobilní aplikaci?
Dvoucestné služby SMS selžou po 24. únoru 2021. Uživatelům se při pokusu o přihlášení a předání MFA zobrazí chyba.

### <a name="how-do-i-change-the-user-settings-from-two-way-text-message-to-mobile-app"></a>Návody změnit nastavení uživatele z obousměrné textové zprávy do mobilní aplikace?

Uživatelská nastavení byste měli změnit pomocí následujících kroků:

1. V části MFA Server vyfiltrujte seznam uživatelů pro obousměrnou textovou zprávu.
1. Vyberte všichni uživatelé.
1. Otevřete dialog Upravit uživatele.
1. Změní uživatele z textové zprávy do mobilní aplikace.

   ![Snímek obrazovky koncových uživatelů](media/how-to-authentication-two-way-sms-unsupported/end-users.png)

### <a name="do-my-users-need-to-take-any-action-if-yes-how"></a>Musí si uživatelé nějakou akci udělat? Pokud ano, jak?
Ano. Koncoví uživatelé si musí navštívit konkrétního uživatelského portálu MFA serveru a aktivovat tak mobilní aplikaci, pokud jste tak ještě neučinili. Až dokončíte krok 3, všichni uživatelé, kteří si na portálu User Portal nenavštívili nastavení mobilní aplikace, začnou neúspěšné přihlášení, dokud nenavštíví uživatelský portál, aby se znovu zaregistrovali.

### <a name="what-if-my-users-cant-install-the-mobile-app-what-other-options-do-they-have"></a>Co když moji uživatelé nemůžou nainstalovat mobilní aplikaci? Jaké další možnosti jsou k dispozici?
Alternativou k obousměrnému SMS nebo mobilní aplikaci je telefonní hovor. Microsoft Authenticator aplikace je však doporučovanou metodou ověřování.

### <a name="will-one-way-sms-be-deprecated-as-well"></a>Bude jednosměrná zpráva SMS zastaralá?
Ne, jenom obousměrný server SMS se už nepoužívá. V případě MFA serveru funguje jednosměrný server SMS pro podmnožinu scénářů:

- Adaptér AD FS
- Ověřování IIS (vyžaduje uživatelský portál a konfiguraci)
- RADIUS (vyžaduje, aby klienti RADIUS podporovali přístup k výzvě a byl použit protokol PAP)

Existují určitá omezení, pokud je možné použít jednosměrný server SMS, který mobilní aplikaci nabízí lepší alternativu, protože nevyžaduje výzvu k zadání ověřovacího kódu.
Pokud v některých scénářích chcete dál používat jednosměrný server SMS, můžete je nechat zaškrtnuté, ale změnit oddíl **nastavení společnosti** **Obecné** **textové zprávy s výchozími hodnotami uživatele** na **jednosměrné** místo **obousměrné**. Pokud použijete synchronizaci adresářů, která je výchozím nastavením serveru SMS, je nutné ji změnit na One-Way namísto obousměrného postupu.

### <a name="how-can-i-check-which-users-are-still-using-two-way-sms"></a>Jak můžu ověřit, kteří uživatelé pořád používají dvoucestné SMS?
Chcete-li zobrazit seznam těchto uživatelů, spusťte **MFA Server**, vyberte část **Uživatelé** , klikněte na možnost **filtrovat seznam uživatelů** a filtrujte **textovou zprávu dvěma způsoby**.

### <a name="how-do-we-hide-two-way-sms-as-an-option-in-the-mfa-portal-to-prevent-users-from-selecting-it-in-the-future"></a>Jak se dá na portálu MFA skrýt obousměrný server SMS, abyste uživatelům zabránili v jeho výběru v budoucnu?
Na portálu User Portal pro MFA Server klikněte na **Nastavení**, můžete vymazat **textovou zprávu** , aby nebyla k dispozici. Totéž platí v části **AD FS** , pokud používáte AD FS pro zápis uživatele.

