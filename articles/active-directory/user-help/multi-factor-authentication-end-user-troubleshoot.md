---
title: Řešení potíží s dvoustupňovým ověřováním – Azure Active Directory | Dokumentace Microsoftu
description: Poskytuje pokyny pro uživatele o tom, co dělat v případě, že narazíte na problém s Azure Multi-Factor Authentication a dvoustupňové ověřování.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: lizross
ms.reviewer: kexia
ms.collection: M365-identity-device-management
ms.openlocfilehash: b74049833b055caa112c346b74798893f2c0febf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56181828"
---
# <a name="get-help-with-two-step-verification"></a>Získat pomoc s dvoustupňovým ověřováním

Dvoustupňové ověření je funkce zabezpečení, které vaše organizace používá k ochraně vašich účtů. Dvoustupňové ověřování je bezpečnější než pouhé heslo, protože se spoléhá na dva způsoby ověření: něco, co znáte, a něco, co máte u sebe. Něco, které už znáte je vaše heslo, když něco, co máte s vámi telefonu nebo zařízení. Pomocí dvoustupňového ověření může pomoct hackerům škodlivý přihlašování jako vy, i když získávají své heslo.

Společnost Microsoft nabízí dvoustupňové ověření, je vaší organizaci, který určuje, zda používáte funkci. Nelze odhlásit Pokud vaše organizace vyžaduje, stejně jako jste nemohou výslovně nesouhlasit pomocí hesla, abyste ochránili svůj účet.

>[!Note]
>Pokud hledáte další informace o dvoustupňové ověření pomocí osobního účtu Microsoft, přečtěte si článek [o dvoustupňovém ověřování](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) článku.

## <a name="why-do-i-need-to-use-another-verification-method"></a>Proč je nutné použít jinou metodu ověřování?

Tady je několik důvodů, proč budete muset použít metodu alternativní ověření pomocí svého účtu. Příklad:

- **Můžete si vzpomenout na telefonu nebo zařízení.** Několik dní, po které opustit váš telefon v domácnostech, ale stále je potřeba se přihlásit v práci. Nejprve pokusíte přihlásit pomocí jiné metody, která nepotřebuje telefonu.

- **Ztratí telefon nebo máte nové telefonní číslo.** Pokud jste ztratíte telefon nebo získali nové číslo, můžete přihlásit pomocí jiné metody nebo požádejte správce, aby zrušte nastavení. Důrazně doporučujeme umožňuje vašemu správci vědět, pokud byl váš telefon ztráty nebo odcizení, proto příslušné aktualizace můžete nastavit ke svému účtu. Po nastavení jsou vymazány, budete vyzváni k [zaregistrovat k dvoustupňovému ověřování](multi-factor-authentication-end-user-first-time.md) při příštím přihlášení.

- **Nedokáže získat text ověřování nebo telefonní hovor.** Tady je několik důvodů, proč nemusí získat text nebo telefonní hovor. Pokud úspěšně jste začali textů nebo telefonních hovorů v minulosti, je to pravděpodobně problém s poskytovateli telefonní není váš účet. Pokud máte zpoždění z důvodu chybné signál, doporučujeme použít [aplikaci Microsoft Authenticator](user-help-auth-app-download-install.md) na svém mobilním zařízení. Tuto aplikaci můžete vygenerovat náhodné zabezpečovací kódy pro přihlášení, bez vyžadování jakéhokoli buňky signál nebo připojení k Internetu.<br><br>Pokud se snažíte bude doručena zpráva SMS, požádejte přítelem text jako test, abyste měli jistotu, můžete získat a pokud jsme získali několik zpráv, ujistěte se, že jste používejte kód, než je aktuální.

- **Hesla aplikací nefungují.** Hesla aplikací nahradit normální heslo pro starší desktopové aplikace, ve kterých se dvoustupňové ověřování. Nejprve zkontrolujte, zda že jste správně zadali heslo. Pokud, který se neodstraní, zkuste se přihlásit k [vytvořit nové heslo aplikace](multi-factor-authentication-end-user-app-passwords.md) nebo můžete požádat správce, aby [odstranit stávající hesla aplikací](../authentication/howto-mfa-userdevicesettings.md) tak můžete vytvořit nový.

## <a name="sign-in-using-another-verification-method"></a>Přihlaste se pomocí jinou metodu ověřování

1. normálně přihlásit ke svému účtu a zvolte **podepsat jiným způsobem** odkaz na **dvoustupňové ověřování** stránky.

    ![Změnit přihlašovací metoda ověření](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Pokud se nezobrazí **podepsat jiným způsobem** propojit, znamená to, že jste nenastavili žádné jiné metody ověřování. Budete mít požádat správce o pomoc přihlášení k účtu. Po přihlášení, ujistěte se, že přidáte další ověřovací metody. Další informace o přidání metod ověřování, najdete v článku [spravovat nastavení pro dvoustupňové ověřování](multi-factor-authentication-end-user-manage-settings.md) článku.<br><br>Pokud jste na odkaz, ale stále nevidíte žádné jiné metody ověřování, budete muset požádejte o pomoc s přihlášením k účtu správce.

2. Zvolte metodu alternativní ověření a pokračujte v procesu dvoustupňové ověření.

3. Jakmile budete zpět ve vašem účtu, můžete aktualizovat své metody ověřování (v případě potřeby). Další informace o přidání nebo změna vašich metodách, naleznete v tématu [spravovat nastavení pro dvoustupňové ověřování](multi-factor-authentication-end-user-manage-settings.md) článku.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nepovedlo se najít odpověď na mé problému

Pokud jste se pokusili tyto kroky, ale jsou stále narazíte na problémy, požádejte o pomoc svého správce.

## <a name="related-topics"></a>Související témata

* [Správa nastavení pro dvoustupňové ověřování.](multi-factor-authentication-end-user-manage-settings.md)

* [Nejčastější dotazy k aplikaci Microsoft Authenticator](user-help-auth-app-faq.md)
