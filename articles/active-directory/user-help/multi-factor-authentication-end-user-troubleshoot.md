---
title: Běžné problémy s účtem dvojúrovňové ověřování – Azure AD
description: Řešení pro některé z častých nejčastějších potíží s ověřováním dvou faktorů a svého pracovního nebo školního účtu.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 09/01/2020
ms.author: curtand
ms.reviewer: kexia
metadata ms.custom: contperfq1
ms.openlocfilehash: 3d95ad4aa100b0a185bde015dfe34d747fc77ec9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89322598"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Běžné problémy s dvojúrovňovým ověřováním a pracovním nebo školním účtem

Existují některé běžné problémy se dvěma faktory ověřování, které se zdají nastat častěji než u nás. V tomto článku jsme popsali opravy pro nejběžnější problémy.

Vaše organizace Azure Active Directory (Azure AD) může u svého účtu zapnout dvojúrovňové ověřování. Pokud je zapnuté dvojúrovňové ověřování, vyžaduje přihlášení k účtu kombinaci těchto dat:

- Vaše uživatelské jméno
- Vaše heslo
- Mobilní zařízení nebo telefon

Dvojúrovňové ověřování je bezpečnější než heslo, protože dvojúrovňové ověřování vyžaduje něco, co _víte_ a co _máte_. Žádný hacker nemá váš fyzický telefon.

>[!Important]
>Pokud jste správce, můžete najít další informace o tom, jak nastavit a spravovat prostředí služby Azure AD v [dokumentaci k Azure AD](../index.yml).

Tento obsah vám může přispět k pracovnímu nebo školnímu účtu, což je účet, který vám poskytla vaše organizace (například dritan@contoso.com ). Pokud máte problémy se dvojúrovňovém ověřováním na osobním účet Microsoft, což je účet, který jste si nastavili pro sebe (například danielle@outlook.com ), přečtěte si téma [Zapnutí nebo vypnutí dvojúrovňové ověřování pro vaši účet Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-dont-have-my-mobile-device-with-me"></a>Nemám mi své mobilní zařízení

Dojde k tomu. Mobilní zařízení jste opustili doma a teď nemůžete použít svůj telefon, abyste ověřili, kdo jste vy. Možná jste předtím přidali alternativní metodu pro přihlášení ke svému účtu, například přes telefon do kanceláře. Pokud ano, můžete použít tuto alternativní metodu hned teď. Pokud jste nikdy nepřidali alternativní metodu ověřování, můžete požádat o pomoc oddělení technické podpory vaší organizace.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Přihlášení k pracovnímu nebo školnímu účtu pomocí jiné metody ověřování

1. Přihlaste se ke svému účtu, ale na stránce **dvojúrovňové ověřování** vyberte odkaz **způsob podpisu** .

    ![Změnit metodu ověření přihlášení](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Pokud nevidíte odkaz **jiný způsob** , znamená to, že jste nevytvořili žádné další metody ověřování. Budete muset požádat správce, aby vám pomohly přihlašovat se k vašemu účtu.

2. Vyberte metodu alternativního ověřování a pokračujte v procesu dvojúrovňového ověřování.

## <a name="i-cant-turn-two-factor-verification-off"></a>Nejde mi zapnout dvojúrovňové ověřování po dvou faktorech

- Pokud používáte dvojúrovňové ověřování s osobním účtem služby společnosti Microsoft, například alain@outlook.com , můžete [funkci zapnout nebo vypnout](https://account.live.com/proofs/Manage).

- Pokud používáte dvojúrovňové ověřování u svého pracovního nebo školního účtu, pravděpodobně to znamená, že vaše organizace se rozhodla použít tuto přidanou funkci zabezpečení. Neexistuje žádný způsob, jak ho individuálně vypnout.

Pokud nemůžete zapnout dvojúrovňové ověřování, může to být také kvůli výchozím hodnotám zabezpečení, které byly použity na úrovni organizace. Další informace o výchozím nastavení zabezpečení najdete v tématu [co jsou výchozí nastavení zabezpečení?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="my-device-was-lost-or-stolen"></a>Moje zařízení bylo ztraceno nebo odcizeno.

Pokud jste ztratili nebo máte odcizené mobilní zařízení, můžete provést jednu z následujících akcí:

- Přihlaste se pomocí jiné metody.
- Požádejte technickou podporu vaší organizace, aby vymazala vaše nastavení.

Důrazně doporučujeme poznat oddělení technické podpory vaší organizace, pokud došlo ke ztrátě nebo odcizení vašeho telefonu. Oddělení technické podpory může provést příslušné aktualizace vašeho účtu. Po vymazání nastavení budete při příštím přihlášení vyzváni k [registraci ke dvojúrovňovému ověření](multi-factor-authentication-end-user-first-time.md) .

## <a name="im-not-receiving-the-verification-code-sent-to-my-mobile-device"></a>Nepřijímám ověřovací kód odeslaný do mého mobilního zařízení

Nepříjem ověřovacího kódu je běžný problém. K tomuto problému obvykle dochází v souvislosti s vaším mobilním zařízením a jeho nastavením. Tady jsou některé akce, které můžete vyzkoušet.

Vyzkoušejte toto | Informace o doprovodnéch materiálech
--------- | ------------
Restartujte mobilní zařízení. | Někdy vaše zařízení potřebuje jenom aktualizaci. Po restartování zařízení skončí všechny procesy na pozadí a služby. Restartování také vypne základní součásti vašeho zařízení. Po restartování zařízení se aktualizují všechny služby nebo komponenty.
Ověřte správnost bezpečnostních údajů | Ujistěte se, že informace o metodě ověření zabezpečení jsou přesné, zejména vaše telefonní čísla. Pokud vložíte špatné telefonní číslo, všechny výstrahy budou přecházet na toto nesprávné číslo. Naštěstí nebude moct tento uživatel dělat cokoli s výstrahami, ale neumožňuje vám taky přihlásit se ke svému účtu. Chcete-li se ujistit, že jsou vaše informace správné, přečtěte si pokyny v článku věnovaném [Nastavení metod pro správu dvou faktorů ověřování](multi-factor-authentication-end-user-manage-settings.md) .
Ověřte, že jsou vaše oznámení zapnutá. | Ujistěte se, že vaše mobilní zařízení má zapnutá oznámení. Ujistěte se, že jsou povolené následující režimy oznámení: <br/><br/> &bull; Telefonní hovory <br/> &bull; Vaše aplikace pro ověřování <br/> &bull; Vaše aplikace pro zasílání zpráv SMS <br/><br/> Zajistěte, aby tyto režimy vytvořily výstrahu, která se _zobrazí_ na vašem zařízení.
Ujistěte se, že máte signál zařízení a připojení k Internetu | Ujistěte se, že vaše telefonní hovory a textové zprávy se připravují do mobilního zařízení. Požádejte přítele a pošle vám textovou zprávu, abyste se ujistili, že dostanete. Pokud neobdržíte hovor nebo text, nejdřív zkontrolujte, že je vaše mobilní zařízení zapnuté. Pokud je zařízení zapnuté, ale stále nepřijímáte volání nebo text, pravděpodobně došlo k potížím se sítí. Budete se muset spojit s poskytovatelem. Pokud často máte problémy související s signály, doporučujeme nainstalovat a používat [aplikaci Microsoft Authenticator](user-help-auth-app-download-install.md) na svém mobilním zařízení. Aplikace ověřovatele může vygenerovat náhodné kódy zabezpečení pro přihlášení, aniž by museli mít žádný signál v buňce nebo připojení k Internetu.
Vypnout Nerušit | Ujistěte se, že jste neaktivovali funkci **Nerušit** pro vaše mobilní zařízení. Když je tato funkce zapnutá, oznámení na vašem mobilním zařízení nemůžou upozorňovat. Pokyny, jak tuto funkci vypnout, najdete v příručce k vašemu mobilnímu zařízení.
Odblokovat telefonní čísla | V USA hlasové hovory od Microsoftu pocházely z následujících čísel: + 1 (866) 539 4191, + 1 (855) 330 8653 a + 1 (877) 668 6536.
Ověřit nastavení týkající se baterie | Pokud jste nastavili optimalizaci baterie, aby nedošlo ke zbývajícímu aktivnímu používání aplikací na pozadí, je pravděpodobné, že váš systém oznámení byl ovlivněn. Zkuste vypnout optimalizaci baterie pro vaši aplikaci pro ověřování a aplikaci pro zasílání zpráv. Pak se znovu pokuste přihlásit ke svému účtu.
Zakázat aplikace zabezpečení třetích stran | Některé aplikace pro zabezpečení telefonů blokují textové zprávy a telefonní hovory od neznámých neznámých volajících. Aplikace zabezpečení může vašemu telefonu zabránit v příjmu ověřovacího kódu. Zkuste zakázat jakékoli aplikace zabezpečení třetích stran na telefonu a potom požádat o odeslání dalšího ověřovacího kódu.

## <a name="im-not-being-prompted-for-my-second-verification-information"></a>Nezobrazuje se mi výzva k zadání informací o druhé kontrole

Přihlašujete se ke svému pracovnímu nebo školnímu účtu pomocí svého uživatelského jména a hesla. Dál byste měli být vyzváni k zadání dalších informací o ověření zabezpečení. Pokud se vám nezobrazí výzva, možná jste ještě nevytvořili své zařízení. Mobilní zařízení musí být nastavené tak, aby fungovalo s konkrétní další metodou ověřování zabezpečení.

Možná jste ještě nevytvořili vaše zařízení. Mobilní zařízení musí být nastavené tak, aby fungovalo s konkrétní další metodou ověřování zabezpečení. Postup, jak nastavit, aby vaše mobilní zařízení bylo k dispozici pro vaši metodu ověřování, najdete v tématu [Správa nastavení dvou metod ověřování](multi-factor-authentication-end-user-manage-settings.md). Pokud víte, že jste ještě nenastavili vaše zařízení nebo účet, můžete postupovat podle kroků v článku [Nastavení účtu pro dvoustupňové ověřování](multi-factor-authentication-end-user-first-time.md) .

## <a name="i-have-a-new-phone-number-and-i-want-to-add-it"></a>Mám nové telefonní číslo a chci ho přidat

Pokud máte nové telefonní číslo, budete muset aktualizovat podrobnosti metody ověření zabezpečení. To umožňuje vašim ověřovacím dotazům přejít do správného umístění. Chcete-li aktualizovat metodu ověřování, postupujte podle kroků v části **Přidání nebo změna telefonního čísla** v článku [Správa nastavení dvou metod ověřování](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) .

## <a name="i-have-a-new-mobile-device-and-i-want-to-add-it"></a>Mám nové mobilní zařízení a chci ho přidat

Pokud máte nové mobilní zařízení, budete ho muset nastavit na práci se dvojúrovňovém ověřováním. Toto je řešení s více kroky:

1. Podle postupu v článku [Nastavení účtu pro dvoustupňové ověření](multi-factor-authentication-end-user-first-time.md) nastavte zařízení pro práci s vaším účtem.

1. Aktualizujte informace o účtu a zařízení na stránce **Další ověření zabezpečení** . Proveďte aktualizaci tak, že odstraníte původní zařízení a přidáte ho do nového. Další informace najdete v článku [Správa nastavení dvou metod ověřování](multi-factor-authentication-end-user-manage-settings.md) .

Volitelné kroky:

- Nainstalujte aplikaci Microsoft Authenticator na mobilní zařízení podle kroků v článku [Stažení a instalace Microsoft Authenticator aplikace](user-help-auth-app-download-install.md) .

- Pro důvěryhodná zařízení zapněte dvojúrovňové ověřování pomocí postupu v části **Zapnutí dvojúrovňového ověřování v části důvěryhodné zařízení v** článku [Správa nastavení dvou metod ověřování](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) .

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Mám problémy s přihlášením na mobilním zařízení během cestování

V rámci mezinárodního umístění může být obtížnější použít metodu ověřování související s mobilním zařízením, jako je textové zasílání zpráv. Je také možné, že vaše mobilní zařízení vám může způsobit poplatky za roaming. Pro tuto situaci doporučujeme, abyste používali aplikaci Microsoft Authenticator s možností připojení k Wi-Fi aktivnímu bodu. Další informace o tom, jak nastavit Microsoft Authenticator aplikaci na mobilním zařízení, najdete v článku o [Stažení a instalaci Microsoft Authenticator aplikace](user-help-auth-app-download-install.md) .

## <a name="i-cant-get-my-app-passwords-to-work"></a>Nejde mi získat hesla k aplikacím

Hesla aplikací nahrazují normální heslo pro starší aplikace klasické pracovní plochy, které nepodporují dvojúrovňové ověřování. Nejdřív se ujistěte, že jste zadali heslo správně. Pokud to neopraví, zkuste pro aplikaci vytvořit nové heslo aplikace. Provedete to podle kroků v části **hesla pro vytvoření a odstranění aplikace pomocí portálu moje aplikace** v tématu [Správa hesel aplikací v heslech pro dva kroky ověřování](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) .

## <a name="i-cant-turn-off-two-factor-verification"></a>Nejde vypnout dvojúrovňové ověřování

Pokud používáte dvojúrovňové ověřování se svým pracovním nebo školním účtem (například alain@contoso.com ), pravděpodobně to znamená, že vaše organizace se rozhodla použít tuto přidanou funkci zabezpečení. Vzhledem k tomu, že vaše organizace rozhodla použít tuto funkci, neexistuje způsob, jak ji individuálně vypnout. Pokud ale používáte dvojúrovňové ověřování pomocí osobního účtu, například alain@outlook.com , máte možnost zapnout nebo vypnout funkci. Pokyny k řízení dvojúrovňové ověřování pro osobní účty najdete v tématu [Zapnutí nebo vypnutí dvojúrovňového ověřování pro vaše účet Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

Pokud nemůžete zapnout dvojúrovňové ověřování, může to být také kvůli výchozím hodnotám zabezpečení, které byly použity na úrovni organizace. Další informace o výchozím nastavení zabezpečení najdete v tématu [co jsou výchozí nastavení zabezpečení?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nenašli jsme odpověď na můj problém

Pokud jste provedli tyto kroky, ale stále dochází k problémům, požádejte o pomoc oddělení technické podpory vaší organizace.

## <a name="related-articles"></a>Související články

- [Správa nastavení dvojúrovňové metody ověřování](multi-factor-authentication-end-user-manage-settings.md)

- [Nastavení účtu pro dvoustupňové ověřování](multi-factor-authentication-end-user-first-time.md)

- [Nejčastější dotazy k aplikaci Microsoft Authenticator](user-help-auth-app-faq.md)