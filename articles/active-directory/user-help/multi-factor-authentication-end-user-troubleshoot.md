---
title: Běžné problémy & řešení s ověřováním účtů – Azure AD
description: Seznamte se s možnými problémy a řešeními pro některé z častých nejčastějších potíží s ověřováním ve dvou faktorech a pracovního nebo školního účtu.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: lizross
ms.reviewer: kexia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d998fafbf6b3ba0547991de6c3a8e71b66d91f7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231881"
---
# <a name="common-problems-and-solutions-with-two-factor-verification-and-your-work-or-school-account"></a>Běžné problémy a řešení se dvojúrovňovém ověřováním a vaším pracovním nebo školním účtem

Vaše organizace zapnula dvojúrovňové ověřování, což znamená, že přihlášení k pracovnímu nebo školnímu účtu teď vyžaduje kombinaci uživatelského jména, hesla a mobilního zařízení nebo telefonu. Vaše organizace toto dodatečné ověření zapnula, protože je bezpečnější než heslo, spoléhá se na dvě formy ověřování: něco, co znáte a co s vámi máte. Dvojúrovňové ověřování může přispět k zastavení škodlivého hackera před tím, než budete mít v úmyslu, protože i když mají heslo, lichá, že nemají vaše zařízení.

Existují některé běžné problémy se dvěma faktory ověřování, které se zdají nastat častěji než u nás. Provedli jsme tento článek doufá že, který řeší nejběžnější problémy a některé možné opravy.

>[!Important]
>Tento obsah je určený pro uživatele. Pokud jste správce, najdete další informace o nastavení a správě vašeho prostředí Azure Active Directory (Azure AD) v [dokumentaci k Azure Active Directory](https://docs.microsoft.com/azure/active-directory).
>
>Tento obsah je taky určený jenom pro použití s vaším pracovním nebo školním účtem, který vám poskytla vaše organizace (například alain@contoso.com). Pokud máte problémy se dvojúrovňovém ověřováním a osobním účet Microsoft, účet, který jste si nastavili pro sebe (například danielle@outlook.com), najdete v tématu [Zapnutí nebo vypnutí dvojúrovňového ověřování pro vaše účet Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-forgot-my-mobile-device-at-home"></a>Zapomněl (a) jsem mobilní zařízení na domovské stránce

Dojde k tomu. Mobilní zařízení jste opustili doma a teď nemůžete použít svůj telefon, abyste ověřili, na koho jste řekli. Pokud jste dříve přidali další metodu pro přihlášení k účtu, jako je například váš telefon do kanceláře, měli byste tuto metodu nyní používat. Pokud jste nikdy nepřidali další metodu ověření, budete se muset obrátit na helpdesk a požádejte ho, aby vám pomohli se vrátit k vašemu účtu.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Přihlášení k pracovnímu nebo školnímu účtu pomocí jiné metody ověřování

1. Přihlaste se ke svému účtu normálně a na stránce **dvojúrovňové ověřování** klikněte na odkaz **Přihlásit se jiným způsobem** .

    ![Změnit metodu ověření přihlášení](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Pokud nevidíte odkaz **jiný způsob** , znamená to, že jste nevytvořili žádné další metody ověřování. Budete muset požádat správce, aby vám pomohly přihlašovat se k vašemu účtu.

2. Vyberte metodu alternativního ověřování a pokračujte v procesu dvojúrovňového ověřování.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Ztratil jsem své mobilní zařízení nebo bylo odcizeno.

Pokud jste ztratili nebo máte odcizené mobilní zařízení, můžete se přihlásit pomocí jiné metody, nebo můžete požádat oddělení technické podpory, aby vaše nastavení vymazalo. Důrazně doporučujeme, abyste oddělení technické podpory věděli, jestli došlo ke ztrátě nebo odcizení vašeho telefonu, a můžete tak učinit příslušné aktualizace svého účtu. Po vymazání nastavení budete při příštím přihlášení vyzváni k [registraci ke dvojúrovňovému ověření](multi-factor-authentication-end-user-first-time.md) .

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Nezobrazuje se ověřovací kód odeslaný do mého mobilního zařízení

Nezískávání ověřovacího kódu je běžný problém a obvykle se vztahuje na vaše mobilní zařízení a jeho nastavení. Je možné, že si vyzkoušíte tyto věci:

- **Restartujte své mobilní zařízení.** Někdy vaše zařízení potřebuje jenom aktualizaci. Restartování zařízení ukončí všechny procesy na pozadí nebo služby, které jsou aktuálně spuštěné a můžou způsobit problémy, a to tak, že se aktualizují základní komponenty vašeho zařízení a restartuje se v případě, že v určitém okamžiku došlo k chybě.

- **Ověřte správnost informací o zabezpečení.** Ujistěte se, že informace o metodě ověření zabezpečení jsou přesné, zejména vaše telefonní čísla. Pokud vložíte špatné telefonní číslo, všechny výstrahy budou přecházet na toto nesprávné číslo. Naštěstí nebude moct tento uživatel dělat cokoli s výstrahami, ale neumožňuje vám taky přihlásit se ke svému účtu. Chcete-li se ujistit, že jsou vaše informace správné, přečtěte si pokyny v článku věnovaném [Nastavení metod pro správu dvou faktorů ověřování](multi-factor-authentication-end-user-manage-settings.md) .

- **Ověřte, že jsou vaše oznámení zapnutá.** Ujistěte se, že vaše mobilní zařízení má zapnutá oznámení a že jste vybrali metodu oznámení, která umožňuje telefonní hovory, aplikaci pro ověřování a aplikaci pro zasílání zpráv (pro textové zprávy), aby se na vaše mobilní zařízení odesílala viditelná oznámení výstrah.

- **Ujistěte se, že máte signál zařízení a připojení k Internetu.** Ujistěte se, že vaše telefonní hovory a textové zprávy se připravují do mobilního zařízení. Požádejte přítele a pošle vám textovou zprávu, abyste se ujistili, že dostanete. Pokud to neuděláte, ujistěte se, že je vaše mobilní zařízení zapnuté. Pokud je zařízení zapnuté, ale stále nezískáváte volání nebo text, pravděpodobně došlo k potížím se sítí a vy budete muset kontaktovat svého poskytovatele. Pokud často máte problémy související s signály, doporučujeme nainstalovat a používat [aplikaci Microsoft Authenticator](user-help-auth-app-download-install.md) na svém mobilním zařízení. Aplikace ověřovatele může vygenerovat náhodné kódy zabezpečení pro přihlášení, aniž by museli mít žádný signál v buňce nebo připojení k Internetu.

- **Vypnout Nerušit.** Ujistěte se, že jste neaktivovali funkci **Nerušit** pro vaše mobilní zařízení. Když je tato funkce zapnutá, oznámení na vašem mobilním zařízení nemůžou upozorňovat. Pokyny, jak tuto funkci vypnout, najdete v příručce k vašemu mobilnímu zařízení.

- **Odblokovat telefonní čísla** V USA hlasové hovory od Microsoftu pocházely z následujících čísel: + 1 (866) 539 4191, + 1 (855) 330 8653 a + 1 (877) 668 6536.

- **Ověřte nastavení týkající se baterie.** Zdá se, že na povrchu je trochu liché, ale pokud jste nastavili optimalizaci baterie, aby nedošlo ke zbývajícímu aktivnímu používání aplikací na pozadí, váš systém oznámení má pravděpodobně vliv na největší dopad. Pokud se chcete pokusit tento problém vyřešit, vypněte optimalizaci baterie pro aplikaci pro ověřování a aplikaci pro zasílání zpráv a zkuste se znovu přihlásit ke svému účtu.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Nezobrazuje se mi výzva k zadání informací o druhé kontrole

Pokud jste se přihlásili ke svému pracovnímu nebo školnímu účtu pomocí svého uživatelského jména a hesla, ale neobdrželi jste žádné informace o vašich dalších ověřovacích informacích o zabezpečení, může se stát, že jste zařízení ještě nevytvořili. Vaše mobilní zařízení musí být specificky nastavené tak, aby fungovalo s vaší další metodou ověřování zabezpečení. Pokud chcete mít jistotu, že máte zapnuté mobilní zařízení a že je k dispozici pro použití s vaší metodou ověřování, přečtěte si článek [Správa nastavení dvou metod ověřování](multi-factor-authentication-end-user-manage-settings.md) . Pokud víte, že jste zařízení nebo účet nevytvořili, můžete to udělat hned podle kroků v článku [Nastavení účtu pro dvoustupňové ověřování](multi-factor-authentication-end-user-first-time.md) .

## <a name="i-got-a-new-phone-number-how-do-i-change-it-for-two-factor-verification"></a>Dostali jsme nové telefonní číslo, jak ho změnit pro dvoustupňové ověřování?

Pokud se vám zobrazí nové telefonní číslo, budete muset aktualizovat podrobnosti metody ověření zabezpečení, aby vaše výzvy k ověření přešly do správného umístění. Chcete-li aktualizovat metodu ověřování, postupujte podle kroků v části **Přidání nebo změna telefonního čísla** v článku [Správa nastavení dvou metod ověřování](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) .

## <a name="i-got-a-new-mobile-device-how-do-i-add-it"></a>Mám nové mobilní zařízení, jak ho mohu přidat?

Pokud jste se dopracovali s novým mobilním zařízením, budete ho muset nastavit na práci se dvěma faktory ověřování. Toto je řešení s více kroky:

1. Nastavte zařízení tak, aby fungovalo s pracovním nebo školním účtem, podle kroků v článku [Nastavení účtu pro dvoustupňové ověřování](multi-factor-authentication-end-user-first-time.md) .

2. Aktualizujte informace o účtu a zařízení na stránce **Další ověření zabezpečení** , odstraňte staré zařízení a přidejte nový. Další informace najdete v článku [Správa nastavení dvou metod ověřování](multi-factor-authentication-end-user-manage-settings.md) .

3. Volitelná. Stáhněte, nainstalujte a nastavte Microsoft Authenticator aplikaci na mobilním zařízení podle kroků v článku [Stažení a instalace Microsoft Authenticator aplikace](user-help-auth-app-download-install.md) .

4. Volitelná. Pro důvěryhodná zařízení zapněte dvojúrovňové ověřování pomocí postupu v části **Zapnutí dvojúrovňového ověřování v části důvěryhodné zařízení v** článku [Správa nastavení dvou metod ověřování](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) .

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Mám problémy s přihlášením na mobilním zařízení během cestování

V rámci mezinárodního umístění může být obtížnější použít metodu ověřování související s mobilním zařízením, jako je textové zasílání zpráv. Je také možné, že vaše mobilní zařízení vám může způsobit poplatky za roaming. Pro tuto situaci doporučujeme používat aplikaci Microsoft Authenticator s možností připojení k Wi-Fi hotspotu. Další informace o tom, jak stáhnout, nainstalovat a nastavit Microsoft Authenticator aplikaci na mobilním zařízení, najdete v článku [Stažení a instalace Microsoft Authenticator aplikace](user-help-auth-app-download-install.md) .

## <a name="i-cant-get-my-app-passwords-to-work"></a>Nejde mi získat hesla k aplikacím

Hesla aplikací nahrazují normální heslo pro starší aplikace klasické pracovní plochy, které nepodporují dvojúrovňové ověřování. Nejdřív se ujistěte, že jste zadali heslo správně. Pokud to neopraví, zkuste pro aplikaci vytvořit nové heslo aplikace pomocí postupu v části **hesla pro vytvoření a odstranění aplikace pomocí portálu** [Správa hesel aplikací v heslech pro dvoustupňové ověřování](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) .

## <a name="why-cant-i-turn-two-factor-verification-off"></a>Proč nejde zapnout dvojúrovňové ověřování se dvěma faktory?

Pokud používáte dvojúrovňové ověřování u svého pracovního nebo školního účtu (například alain@contoso.com), pravděpodobně to znamená, že se vaše organizace rozhodla použít tuto přidanou funkci zabezpečení. Vzhledem k tomu, že vaše organizace rozhodla použít tuto funkci, neexistuje způsob, jak ji individuálně vypnout. Pokud ale používáte dvojúrovňové ověřování pomocí osobního účtu, například alain@outlook.com, máte možnost zapnout nebo vypnout funkci. Pokyny, jak řídit dvojúrovňové ověřování pro osobní účty Microsoft, najdete [v tématu Zapnutí nebo vypnutí dvojúrovňového ověřování pro vaše účet Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nenašli jsme odpověď na můj problém

Pokud jste tento postup vyzkoušeli, ale stále dochází k problémům, požádejte o pomoc helpdesk.

## <a name="related-articles"></a>Související články

- [Správa nastavení dvojúrovňové metody ověřování](multi-factor-authentication-end-user-manage-settings.md)

- [Nastavení účtu pro dvoustupňové ověřování](multi-factor-authentication-end-user-first-time.md)

- [Nejčastější dotazy k aplikaci Microsoft Authenticator](user-help-auth-app-faq.md)
