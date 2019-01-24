---
title: Spravovat svoje bezpečnostní údaje – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak spravovat svoje bezpečnostní údaje, včetně postupu při práci s nastavením dvoustupňové ověření.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 34023e74fb30f202760b35120da78a18ca653d69
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815357"
---
# <a name="manage-your-security-info-preview"></a>Spravovat svoje bezpečnostní údaje (preview)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Vaše bezpečnostní údaje můžete použít pro přihlášení ke svému pracovnímu nebo školnímu účtu nebo si chcete resetovat heslo.

Při přihlášení, v závislosti na nastavení vaší organizace, může se zobrazit zaškrtávací políčko s textem, **nezobrazovat dotaz dalších X dní**. Díky tomu zaškrtávací políčko, které můžete zůstat přihlášení k zařízení pro počet dnů, po který váš správce umožní bez nutnosti reverification.

## <a name="change-your-info"></a>Změňte své údaje
Můžete aktualizovat nebo přidejte bezpečnostní údaje nebo změnit výchozí, podle povoluje správce a vaší organizaci.

### <a name="to-change-your-info"></a>Chcete-li změnit vaše informace

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu.

2. Přejděte na myapps.microsoft.com, vyberte své jméno v pravém horním rohu stránky a pak vyberte **profilu**.

3. V **spravovat účet** vyberte **upravit bezpečnostní údaje**.

    ![Profil obrazovky se zvýrazněným odkazem informace o zabezpečení úpravy](media/security-info/security-info-profile.png)

4. Pomocí vaše výchozí metoda schválit přístup a zobrazíte vaše aktuální informace o podrobné informace o zabezpečení, pokud správce nastavil toto prostředí pro vaši organizaci.

5. Na **bezpečnost vašeho účtu** stránky, můžete:

    - Vyberte **přidejte bezpečnostní údaje** přidáte další metody.

    - Vyberte **změnit výchozí nastavení** Chcete-li změnit výchozí metodu.

    - Vyberte **tužky** ikonu vedle existující metodu aktualizovat vaše informace.

    ![Informace o okně zabezpečení s existující, upravovat informace](media/security-info/security-info-edit.png)

6. Po provedení změny stránku lze opustit a změny se uloží.

Pokud se tyto možnosti nezobrazí, nebo jste nejde získat přístup ke stránce myapps.microsoft.com, je možné, že vaše organizace používá vlastní možnosti nebo vlastní stránky. Bude nutné požádat o další pomoc svého správce.

## <a name="manage-your-security-info-for-a-lost-or-potentially-compromised-device"></a>Spravovat vaše bezpečnostní údaje na ztracené nebo potenciálně napadeného zařízení

Pokud dojde ke ztrátě zařízení nebo zařízení ohroženo, budete mít ke svému ověření procesu pro všechny dřív důvěryhodných zařízení.

### <a name="to-manage-your-security-info-for-lost-or-potentially-compromised-devices"></a>Ke správě své bezpečnostní údaje u ztracených nebo ohrožených potenciálně zařízení

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu.

2. Přejděte na myapps.microsoft.com, vyberte své jméno v pravém horním rohu stránky a pak vyberte **profilu**.

3. V **spravovat účet** vyberte **zapomenout MFA na zapamatovaných zařízeních**.
    
    Když zvolíte tuto možnost, budete muset projít procesem ověřování služby Multi-Factor Authentication (MFA) znovu po přihlášení.

    ![Profil obrazovky se zvýrazněným odkazem zapomenout](media/security-info/security-info-forget.png)

## <a name="common-problems-and-solutions-with-your-security-info"></a>Běžné problémy a řešení s využitím bezpečnostních údajů

Tento článek pomůže vyřešit vaše bezpečnostní údaje, včetně dvoustupňové ověření související problémy.

|Problém|Řešení|
|-------|--------|
|Nemám mi na telefon se se mnou|To je to možné, že nemáte telefon u sebe kdykoli, ale, že budete stále chcete Přihlaste se k vaší pracovní nebo školní účet. Chcete-li vyřešit tento problém, můžete přihlásit pomocí různých ověřovacích metody, která nevyžaduje, aby váš telefon, jako je váš telefon do kanceláře. Chcete-li přidat další metody ke svým bezpečnostním údajům, provedením kroků v [změnit své údaje](#change-your-info) oddílu.|
|Můžu dojde ke ztrátě mi na telefon nebo byla ukradena|Bohužel může dojít ztráty telefonu nebo jeho krádeže. V takovém případě důrazně doporučujeme nechat vaše organizace měla vědět tak, aby vaši pracovníci IT můžou resetovat hesla aplikací a zrušte všechny zapamatovaných zařízení ze seznamu důvěryhodných zařízení. Zapomenete důvěryhodných zařízeních můžete také pomocí následujících kroků v [spravovat vaše bezpečnostní údaje na ztracené nebo potenciálně napadeného zařízení](#manage-your-security-info-for-a-lost-or-potentially-compromised-device) oddílu.|
|Zobrazilo se mi nové telefonní číslo|Existují dva způsoby, jak tento problém vyřešit. Můžete se přihlásit pomocí metody alternativní ověření, který nevyžaduje, aby vaše telefonní číslo, například e-mailu, nebo pokud to není možné, můžete kontaktovat vaše organizace IT oddělení společnosti a potom kliknul zrušte nastavení. Chcete-li přidat další metody ke svým bezpečnostním údajům, provedením kroků v [změnit své údaje](#change-your-info) oddílu.|
|Výchozí metodu je nesprávný|V možnosti zabezpečení můžete aktualizovat vaše výchozí metoda. Pro konkrétní podrobnosti, můžete přejít na [změnit své údaje](#change-your-info) oddílu.|
|Můžu doručována text nebo volání na mobilní zařízení.|Pokud v minulosti jste úspěšně přijata textů nebo telefonních hovorů na vaše mobilní zařízení, je tento problém nejpravděpodobnější s poskytovateli telefonní není váš účet. Ujistěte se, že máte dobré buňky signál a budete moct přijímat textové zprávy a telefonních hovorů. Můžete požádat o přítelem hovor nebo zprávu jako test.<br><br>Pokud se může úspěšně přijímat zprávy text a telefon, ale pořád ještě získali oznámení, můžete zkusit použít jinou metodu. Můžete přidat další metody ke svým bezpečnostním údajům podle postupu v [změnit své údaje](#change-your-info) oddílu. Pokud nemáte jinou metodu, chcete-li přidat, můžete obraťte na podporu společnosti a požádejte ho, aby zrušte nastavení, abyste mohli nastavit tak metody při příštím přihlášení.<br><br>Pokud máte problémy se často z důvodu chybné buňky příjem, doporučujeme že použít aplikaci Microsoft Authenticator na vašem mobilním zařízení. Aplikaci můžete vygenerovat náhodné zabezpečovací kódy, které používáte k přihlášení a tyto kódy nevyžadují žádné buňky signál nebo připojení k Internetu. Další informace o aplikaci Microsoft Authenticator, najdete v článku [Začínáme s aplikací Microsoft Authenticator](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to) článku.|
|Žádnou z možností v této tabulce byl vyřešen problém|Pokud jste se pokusili tyto kroky pro řešení potíží, ale jsou stále narazíte na problémy. Obraťte se na svou firemní podporu, by měl mít vám pomůže.|

## <a name="next-steps"></a>Další postup

- Další informace o bezpečnostním údajům v [zabezpečení informací (preview) – přehled](user-help-security-info-overview.md).

- Další informace o dvoustupňovém ověřování v [dvoustupňové ověření přehled](user-help-two-step-verification-overview.md) článku. 

- Proveďte jeden z těchto články s postupy pro další informace o tom, jak nastavit vaše zařízení v oblasti zabezpečení informací:

    - [Nastavení bezpečnostních údajů pro používání ověřovací aplikace](security-info-setup-auth-app.md)

    - [Nastavení bezpečnostních údajů pro používání telefonních hovorů](security-info-setup-phone-number.md)

    - [Upravit informace o zabezpečení tak, aby pomocí textové zprávy](security-info-setup-text-msg.md)

    - [Nastavení bezpečnostních údajů pro používání e-mailu](security-info-setup-email.md)

    - [Nastavte si bezpečnostní údaje použít bezpečnostní otázky](security-info-setup-questions.md)

- Resetování hesla, pokud jste ztratíte nebo zapomenete, z [portál pro resetování hesel](https://passwordreset.microsoftonline.com/) nebo postupujte podle kroků v [obnovit heslo pracovního nebo školního](user-help-reset-password.md) článku.

- Získejte řešení potíží, tipy a nápovědu pro problémy s přihlášením v [nemůžete se přihlásit ke svému účtu Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) článku.