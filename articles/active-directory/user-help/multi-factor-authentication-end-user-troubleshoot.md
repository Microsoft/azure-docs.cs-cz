---
title: Řešení potíží s dvoustupňovým ověřováním - Azure AD | Dokumentace Microsoftu
description: Tento dokument se poskytují uživatelům informace o co dělat v případě, že narazíte na problém s ověřováním Azure Multi-Factor Authentication.
services: multi-factor-authentication
keywords: vícefaktorové ověřování klienta, problém s ověřováním, ID korelace
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/06/2017
ms.author: lizross
ms.reviewer: richagi
ms.custom: end-user
ms.openlocfilehash: deb75c2601fa55f7cdb1681d8f73e94d6b01310a
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060072"
---
# <a name="get-help-with-two-step-verification"></a>Získat pomoc s dvoustupňovým ověřováním
Tento článek obsahuje odpovědi na nejčastější dotazy, které uživatelé požádat o dvoustupňové ověření.

## <a name="why-do-i-have-to-perform-two-step-verification-can-i-turn-it-off"></a>Proč je nutné provést dvoustupňové ověřování? Můžete zapnout ho?

Dvoustupňové ověření je funkce zabezpečení, která vaší organizaci se rozhodli používat k ochraně vašich účtů. Je bezpečnější než jen s heslem, protože závisí na dva typy ověřování: něco víte a něco, co máte s vámi. Je něco, které už znáte heslo. Je něco, co máte s vámi telefonu nebo zařízení, která obvykle mít u sebe. Pokud váš účet je chráněný s dvoustupňovým ověřováním, to znamená, že kyberzločinci nelze se přihlásit jako pokud dostanou heslo nějakým způsobem vzhledem k tomu, že nemají přístup na váš telefon příliš.

Společnost Microsoft nabízí dvoustupňové ověřování, ale vaše organizace rozhodne použít funkci. Nelze odhlásit pokud ho z vás, vaše firemní podpora požaduje, stejně jako jste nemohou výslovně nesouhlasit pomocí hesla, abyste ochránili svůj účet.

Pokud jste dvoustupňové ověřování zapnout na váš osobní účet Microsoft a chcete změnit nastavení, přečtěte si [o dvoustupňovém ověřování](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) místo.

## <a name="i-dont-have-my-phone-with-me-today"></a>Nemám mi na telefon se se mnou ještě dnes

Několik dní, po které opustit váš telefon v domácnostech, ale stále je potřeba se přihlásit v práci. První věc, kterou byste se měli pokusit je přihlašování pomocí jinou metodu ověření. Při registraci k dvoustupňovému ověřování můžete nastavit více než jedno telefonní číslo? Zkuste se přihlásit jinou metodu, postupujte podle těchto kroků:

1. Přihlaste se jako obvykle.
2. Až se otevře stránka dvoustupňové ověření, zvolte **použít jinou možnost ověření**.

   ![Různé ověření](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Vyberte možnost ověření, kterou chcete použít.
4. Pokračujte s dvoustupňovým ověřováním.

Pokud se nezobrazí **použít jinou možnost ověření** propojení, pak to znamená, že jste nenastavili alternativní metody při první registraci k dvoustupňovému ověřování. Obraťte se na podporu společnosti získat pomoc s přihlášením k účtu. Jakmile jste přihlášení, ujistěte se, že [spravovat nastavení](multi-factor-authentication-end-user-manage-settings.md) přidáte další ověřovací metody pro další použití.

Pokud se zobrazí **použít jinou možnost ověření** odkaz, ale nemají přístup k alternativní metody buď, obraťte se na podporu vaší společnosti chcete-li získat pomoc s přihlášením k účtu.

## <a name="i-lost-my-phone-or-got-a-new-number"></a>Můžu ztratí telefon nebo máte nové číslo
Existují dva způsoby, jak se znova dostali do svého účtu. První je k přihlášení pomocí vašich alternativních číslo telefonu pro ověření, pokud nastavíte jednu. Druhým je požádejte svou firemní podporu, vymazat svá nastavení.

Pokud váš telefon byl ztratíte nebo vám ho někdo ukradne, doporučujeme také informace vaší společnosti, podpora resetování vašeho hesla aplikací a vymažte všechny pamatovat zařízení.

### <a name="use-an-alternate-phone-number"></a>Použít alternativní telefonní číslo
Pokud nastavíte více možností ověření, včetně záložní telefonní číslo nebo ověřovací aplikaci na jiném zařízení, můžete použít jeden z nich k přihlášení.

Přihlaste se pomocí alternativní telefonní číslo, postupujte podle těchto kroků:

1. Přihlaste se jako obvykle.
2. Po zobrazení výzvy provést ještě další ověření vašeho účtu, zvolte **použít jinou možnost ověření**.

   ![Různé ověření](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Vyberte telefonní číslo nebo zařízení, které máte přístup.
4. Jakmile budete zpět ve vašem účtu [spravovat nastavení](multi-factor-authentication-end-user-manage-settings.md) Chcete-li změnit číslo telefonu pro ověření.

### <a name="clear-your-settings"></a>Vymazat nastavení
Pokud jste nenakonfigurovali telefonní číslo sekundární ověřování, budete muset požádat o pomoc podporu vaší společnosti. Požádejte vymazat nastavení, aby při příštím přihlášení, zobrazí se výzva k [zaregistrovat k dvoustupňovému ověřování](multi-factor-authentication-end-user-first-time.md) znovu.

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>Můžu doručována text nebo volání na telefon
Tady je několik důvodů, proč se může pokusit o přihlášení, ale nezobrazí text nebo telefonní hovor. Pokud jste úspěšně dostanete textů nebo telefonních hovorů na váš telefon v minulosti, pak jde pravděpodobně problém s poskytovateli telefonní není váš účet. Ujistěte se, že máte dobré buňky signál, a pokud se snažíte zobrazit textovou zprávu Ujistěte se, že budete moct přijímat textové zprávy. Požádejte přítelem, zavoláme vám nebo text jako test.

Pokud jste počkat několik minut, než textovém editoru nebo volání, nejrychlejší způsob, jak dostat do vašeho účtu je zkuste jinou možnost.

1. Vyberte **použít jinou možnost ověření** na stránce, který čeká na ověření.

    ![Různé ověření](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. Vyberte telefonní číslo nebo doručení metodu, kterou chcete použít.

    Pokud jste dostali více ověřovacích kódů, použijte nejnovější.

Pokud nemáte jinou metodu nakonfigurované, obraťte se na podporu společnosti a požádejte ho o zrušte nastavení. Při příštím přihlášení se zobrazí výzva k [nastavení služby Multi-Factor authentication](multi-factor-authentication-end-user-first-time.md) znovu.

Pokud máte zpoždění z důvodu chybné buňky signál, doporučujeme použít [aplikaci Microsoft Authenticator](microsoft-authenticator-app-how-to.md) na vašem smartphonu. Aplikaci můžete vygenerovat náhodné zabezpečovací kódy, které používáte k přihlášení a tyto kódy nevyžadují žádné buňky signál nebo připojení k Internetu.

## <a name="app-passwords-are-not-working"></a>Hesla aplikací nefungují.
Nejprve se ujistěte, zda jste správně zadali heslo aplikace. Heslo generované aplikace nahrazuje normální heslo, ale pouze pro starší desktopové aplikace, ve kterých se dvoustupňové ověřování. Pokud to pořád nefunguje, zkuste přihlášení a [vytvořit nové heslo aplikace](multi-factor-authentication-end-user-app-passwords.md).  Pokud ani to nepomůže, obraťte se na podporu společnosti a potom kliknul [odstranit stávající hesla aplikací](../authentication/howto-mfa-userdevicesettings.md) a vytvořte nový.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Můžu nenašli odpověď na problém.
Pokud jste se pokusili postup řešení, ale jsou stále narazíte na problémy, obraťte se na svou firemní podporu. By měli mít přístup k vám.

## <a name="related-topics"></a>Související témata
* [Správa nastavení pro dvoustupňové ověřování.](multi-factor-authentication-end-user-manage-settings.md)  
* [Nejčastější dotazy k aplikaci Microsoft Authenticator](microsoft-authenticator-app-faq.md)
