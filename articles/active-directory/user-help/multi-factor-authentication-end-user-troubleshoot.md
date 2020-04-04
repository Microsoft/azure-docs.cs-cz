---
title: Běžné problémy s dvoufaktorovým ověřováním účtu – Azure AD
description: Řešení některých běžných dvoufaktorových ověřovacích problémů a vašeho pracovního nebo školního účtu.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: 1703853f42b52dccc80fdfadaa09b67e18a19db8
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632898"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Běžné problémy s dvoufaktorovým ověřením a pracovním nebo školním účtem

Když vaše organizace Azure Active Directory (Azure AD) zapne dvoufaktorové ověření, vaše pracovní nebo školní účet přihlášení vyžaduje kombinaci uživatelského jména, hesla a mobilního zařízení nebo telefonu. Je bezpečnější než jen heslo, spoléhá se na dvě formy ověřování: něco, co víte, a něco, co máte s sebou. Dvoufaktorové ověření může pomoci zabránit škodlivým hackerům, aby předstírali, že jste vy, protože i když mají vaše heslo, je pravděpodobné, že nemají také vaše zařízení.

<center>

![Obrázek metod konceptuálního ověřování](../authentication/media/concept-mfa-howitworks/methods.png)</center>

Tam jsou některé společné dvoufaktorové ověřování problémy, které se zdají stát častěji, než kterýkoli z nás by chtěl. Dali jsme dohromady tento článek, abychom vyřešili nejčastější problémy a některé možné opravy.

>[!Important]
>Pokud jste správce, najdete další informace o tom, jak nastavit a spravovat prostředí Azure AD v [dokumentaci k Azure AD](https://docs.microsoft.com/azure/active-directory).
>
>Tento obsah je také určen pouze pro použití s vaším pracovním nebo školním účtem, alain@contoso.comcož je účet, který vám poskytla vaše organizace (například ). Pokud máte problémy s dvoufaktorovým ověřením a osobním účtem Microsoft, což je účet, který si sami nastavíte (například danielle@outlook.com), přečtěte si část Zapnutí nebo vypnutí [dvoufaktorového ověření pro svůj účet Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-dont-have-my-mobile-device-with-me"></a>Nemám u sebe mobilní zařízení

To se stává. Mobilní zařízení jste nechali doma a nyní nemůžete telefon používat k ověření, kdo jste. Pokud jste dříve přidali jinou metodu pro přihlášení k účtu, například telefon v kanceláři, měli byste být schopni tuto metodu použít nyní. Pokud jste nikdy nepřidali další metodu ověření, budete muset kontaktovat oddělení technické podpory vaší organizace a nechat ji, aby vám pomohla se vrátit ke svému účtu.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Přihlášení k pracovnímu nebo školnímu účtu pomocí jiné metody ověření

1. Přihlaste se ke svému účtu, ale na stránce **Dvoufaktorové ověření** vyberte odkaz Přihlásit se **jiným způsobem.**

    ![Změnit způsob ověření přihlášení](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Pokud odkaz Podepsat jiným **způsobem** nevidíte, znamená to, že jste nenastavili žádné jiné metody ověřování. Budete muset kontaktovat správce o pomoc s přihlášením k účtu.

2. Vyberte si alternativní metodu ověření a pokračujte v procesu dvoufaktorového ověření.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Ztratil(a) jsem mobilní zařízení nebo bylo ukradeno

Pokud jste mobilní zařízení ztratili nebo vám bylo odcizeno, můžete se přihlásit jinou metodou nebo požádat oddělení nápovědy vaší organizace o vymazání nastavení. Důrazně doporučujeme, aby helpdesk vaší organizace věděl, zda došlo ke ztrátě nebo odcizení telefonu, aby bylo možné provést příslušné aktualizace vašeho účtu. Po vymazání nastavení budete při příštím přihlášení vyzváni k [registraci k ověření dvou faktorů.](multi-factor-authentication-end-user-first-time.md)

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Ověřovací kód se mi neodesílá do mobilního zařízení

Nezískání ověřovacího kódu je běžný problém a obvykle souvisí s mobilním zařízením a jeho nastavením. Některé možné věci vyzkoušet:

Zkuste toto | Pokyny
--------- | ------------
Restartování mobilního zařízení | Někdy vaše zařízení potřebuje jen obnovit. Restartováním zařízení ukončíte všechny procesy nebo služby na pozadí, které jsou aktuálně spuštěné, a mohou způsobit problémy spolu s obnovením základních součástí zařízení a restartováním v případě, že se v určitém okamžiku zhroutí.
Ověření správnost bezpečnostních údajů | Zkontrolujte, zda jsou informace o metodě ověření zabezpečení přesné, zejména telefonní čísla. Pokud založíte nesprávné telefonní číslo, všechna upozornění přejdou na toto nesprávné číslo. Naštěstí tento uživatel nebude moci s výstrahami nic dělat, ale také vám nepomůže přihlásit se k vašemu účtu. Chcete-li se ujistit, že jsou vaše informace správné, přečtěte si pokyny v článku [Správa dvoufaktorových metod ověření.](multi-factor-authentication-end-user-manage-settings.md)
Ověření zapnutých oznámení | Ujistěte se, že je v mobilním zařízení zapnutá oznámení a že jste vybrali metodu oznámení, která umožňuje telefonníhovory, ověřovací aplikaci a aplikaci pro zasílání zpráv (pro textové zprávy) odesílat viditelná upozornění do mobilního zařízení.
Zkontrolujte, zda máte signál zařízení a připojení k internetu. | Zkontrolujte, zda se telefonní hovory a textové zprávy dostávají do mobilního zařízení. Ať vám zavolá přítel a pošle vám textovou zprávu, abyste se ujistili, že obdržíte obojí. Pokud tak neučiníte, nejprve zkontrolujte, zda je vaše mobilní zařízení zapnuté. Pokud je vaše zařízení zapnuté, ale stále se vám hovor nebo textová zpráva nedostává, je to s největší pravděpodobností problém s vaší sítí a budete si muset promluvit se svým poskytovatelem. Pokud máte často problémy související se signálem, doporučujeme nainstalovat a používat [aplikaci Microsoft Authenticator](user-help-auth-app-download-install.md) na mobilním zařízení. Ověřovací aplikace může generovat náhodné bezpečnostní kódy pro přihlášení, aniž by bylo nutné jakýkoli mobilní signál nebo připojení k internetu.
Vypnout funkce Nerušit | Ujistěte se, že jste pro své mobilní zařízení nezapnuli funkci **Nerušit.** Když je tato funkce zapnutá, oznámení vás na mobilním zařízení nebudou moci upozorňovat. Pokyny k vypnutí této funkce naleznete v příručce k mobilnímu zařízení.
Odblokování telefonních čísel | Ve Spojených státech hlasové hovory od microsoftu pocházejí z následujících čísel: +1 (866) 539 4191, +1 (855) 330 8653 a +1 (877) 668 6536.
Kontrola nastavení souvisejících s baterií | Ten se zdá být na povrchu trochu zvláštní, ale pokud jste nastavili optimalizaci baterie, abyste zabránili méně používaným aplikacím, aby zůstaly aktivní na pozadí, váš oznamovací systém byl s největší pravděpodobností ovlivněn. Chcete-li se pokusit tento problém vyřešit, vypněte optimalizaci baterie pro ověřovací aplikaci a aplikaci pro zasílání zpráv a zkuste se k účtu přihlásit znovu.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Nedostává se na mě výzva k zadání druhých ověřovacích informací

Pokud jste se k pracovnímu nebo školnímu účtu přihlásili pomocí uživatelského jména a hesla, ale nezobrazí se vám výzva k zadání dalších informací o ověření zabezpečení, možná jste zařízení ještě nenastavili. Vaše mobilní zařízení musí být nastaveno tak, aby fungovalo s konkrétní doplňkovou metodou ověření zabezpečení. Pokud se chcete ujistit, že jste mobilní zařízení zapnuli a že je dostupné pro použití s metodou ověření, přečtěte si článek [Správa nastavení dvoufaktorového ověření.](multi-factor-authentication-end-user-manage-settings.md) Pokud víte, že jste zařízení ani svůj účet nenastavili, můžete to udělat nyní podle pokynů v článku [Nastavení účtu pro dvoustupňové ověření.](multi-factor-authentication-end-user-first-time.md)

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>Mám nové telefonní číslo a chci ho přidat

Pokud jste získali nové telefonní číslo, budete muset aktualizovat podrobnosti o metodě ověření zabezpečení, aby se výzvy k ověření dostaly na správné místo. Pokud chcete aktualizovat metodu ověření, postupujte podle pokynů v části **Přidání nebo změna telefonního čísla** v článku Správa [dvoufaktorového nastavení metody ověření.](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number)

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>Mám nové mobilní zařízení a chci ho přidat

Pokud jste získali nové mobilní zařízení, budete ho muset nastavit tak, aby fungovalo s dvoufaktorovým ověřením. Toto je vícestupňové řešení:

1. Nastavte si zařízení tak, aby fungovalo s pracovním nebo školním účtem, podle pokynů v článku [Nastavení účtu pro dvoustupňové ověření.](multi-factor-authentication-end-user-first-time.md)

1. Aktualizujte informace o účtu a zařízení na stránce **Další ověření zabezpečení,** smažete staré zařízení a přidejte nové zařízení. Další informace najdete v článku [Správa nastavení metody dvoufaktorového ověření.](multi-factor-authentication-end-user-manage-settings.md)

Volitelné kroky:

- Stáhněte, nainstalujte a nastavte aplikaci Microsoft Authenticator na svém mobilním zařízení podle pokynů v článku [Stáhnout a nainstalovat aplikaci Microsoft Authenticator.](user-help-auth-app-download-install.md)

- Dvoufaktorové ověření pro důvěryhodná zařízení zapněte podle pokynů v části **Zapnout dvoufaktorové ověření na důvěryhodném zařízení** v článku [Správa nastavení dvoufaktorového ověření.](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Mám problémy s přihlášením na mobilním zařízení na cestách

Může být obtížnější používat metodu ověřování související s mobilním zařízením, například textovou zprávu, když jste v mezinárodním umístění. Je také možné, že vám mobilní zařízení může způsobit poplatky za roaming. V takovém případě doporučujeme použít aplikaci Microsoft Authenticator s možností připojení k hotspotu Wi-Fi. Další informace o tom, jak stáhnout, nainstalovat a nastavit aplikaci Microsoft Authenticator na mobilním zařízení, najdete v článku [Stažení a instalace aplikace Microsoft Authenticator.](user-help-auth-app-download-install.md)

## <a name="i-cant-get-my-app-passwords-to-work"></a>Neznemožňuji zpracovat hesla aplikací

Hesla aplikací nahrazují normální heslo pro starší desktopové aplikace, které nepodporují dvoufaktorové ověření. Nejprve se ujistěte, že jste heslo zadali správně. Pokud to neopraví, zkuste pro aplikaci vytvořit nové heslo aplikace podle pokynů v článku **Vytvořit a odstranit hesla aplikací pomocí** části Portál mých aplikací v článku Správa [hesel aplikací pro dvoustupňové ověření.](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)

## <a name="i-cant-turn-two-factor-verification-off"></a>Nelze vypnout dvoufaktorové ověření

Pokud používáte dvoufaktorové ověření s pracovním nebo školním alain@contoso.comúčtem (například ), s největší pravděpodobností to znamená, že se vaše organizace rozhodla, že musíte použít tuto přidanou funkci zabezpečení. Vzhledem k tomu, že se vaše organizace rozhodla, že musíte tuto funkci používat, neexistuje žádný způsob, jak ji jednotlivě vypnout. Pokud však používáte dvoufaktorové ověření s osobním alain@outlook.comúčtem, například , můžete funkci zapnout a vypnout. Pokyny, jak řídit dvoufaktorové ověřování osobních účtů, najdete v [tématu Zapnutí nebo vypnutí dvoufaktorového ověření pro váš účet Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

Pokud nemůžete vypnout dvoufaktorové ověřování, může to být také z důvodu výchozího zabezpečení, které byly použity na úrovni organizace. Další informace o výchozích hodnotách zabezpečení naleznete v tématu [Co jsou defulta zabezpečení?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nenašel jsem odpověď na můj problém

Pokud jste vyzkoušeli tyto kroky, ale stále dochází k problémům, obraťte se na oddělení technické podpory vaší organizace.

## <a name="related-articles"></a>Související články

- [Správa nastavení dvoufaktorových metod ověření](multi-factor-authentication-end-user-manage-settings.md)

- [Nastavení účtu pro dvoustupňové ověření](multi-factor-authentication-end-user-first-time.md)

- [Nejčastější dotazy k aplikaci Microsoft Authenticator](user-help-auth-app-faq.md)
