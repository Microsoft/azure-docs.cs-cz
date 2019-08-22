---
title: Řešení potíží s registrací Azure
description: Řešení problému při pokusu o registraci nového účtu v centru účtů portál Microsoft Azure.
author: v-miegge
manager: na
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: 57dc9c05778563bc0c5df1a8ac8895b51008b01f
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657070"
---
# <a name="troubleshoot-azure-sign-up"></a>Řešení potíží s registrací Azure

Při pokusu o registraci nového účtu v portál Microsoft Azure nebo centru účtů Azure se může vyskytnout problém. Před řešením tohoto problému nejdřív ověřte následující:

- Informace, které jste zadali pro váš profil účtu Azure (včetně kontaktní e-mailové adresy, ulice a telefonního čísla), jsou správné.
- Informace o kreditní kartě jsou správné.
- Ještě nemáte účet Microsoft, která má stejné informace.

## <a name="resolutions"></a>Řešení

Pokud chcete vyřešit všechny chyby, vyberte problém, ke kterému dochází při pokusu o registraci do Azure.

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Chyba: *V důsledku potíží s vaším účtem nemůžeme pokračovat v registraci. Kontaktujte prosím podporu fakturace.*

Chcete-li tento problém vyřešit, postupujte podle následujících kroků:

1. Přihlaste se k [centru účtů Azure](https://account.azure.com/Profile) pomocí pověření správce účtu.

2. Vyberte **Upravit podrobnosti**.

3. Ujistěte se, že jsou všechna pole adresy dokončená a platná.

4. Když se přihlásíte k předplatnému Azure, ujistěte se, že fakturační adresa pro registraci platební karty odpovídá vašim bankovním záznamům.

Pokud se vám tato chybová zpráva bude zobrazovat dál, zkuste se zaregistrovat pomocí jiného prohlížeče.

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>Indikátor průběhu se zablokuje v části *ověření identity podle karty* .

Aby bylo možné dokončit ověření identity na kartě, musí být v prohlížeči povoleny soubory cookie třetích stran.

![Ověření identity kartou](./media/billing-troubleshoot-azure-sign-up/identify-verification-by-card.png)
 
Pomocí následujících kroků aktualizujte nastavení souborů cookie v prohlížeči.

1. Pokud používáte Chrome, vyberte **Nastavení** > **Zobrazit upřesňující nastavení** > **Nastavení obsahu** **ochrany osobních údajů** > . Vymazat **blokování souborů cookie třetích stran a dat lokality**.

2. Pokud používáte Microsoft Edge, vyberte **Nastavení** > **Zobrazit Upřesnit nastavení** > **soubory cookie** > neblokovat**soubory cookie**.

3. Aktualizujte přihlašovací stránku Azure a potom zkontrolujte, jestli je problém vyřešený.

4. Pokud aktualizace problém nevyřešila, ukončete a restartujte prohlížeč a pak to zkuste znovu.

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>Formulář platební karty nepodporuje fakturační adresu.

Fakturační adresa musí být umístěná v zemi, kterou jste vybrali v části **o produktu** . Ujistěte se, že jste vybrali správnou zemi.

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Žádné textové zprávy ani volání během ověřování účtu registrace

I když je proces obvykle rychlý, může trvat až 4 minuty, než se ověřovací kód doručí. Telefonní číslo, které zadáte pro ověření, není uloženo jako kontaktní číslo pro účet.

Tady je několik dalších tipů:

- Telefonní číslo služby Voice-over-IP (VoiP) nelze použít pro proces ověřování telefonem.
- Překontrolujte telefonní číslo, které jste zadali, včetně kódu země, který jste vybrali v rozevírací nabídce.
- Pokud váš telefon nepřijímá textové zprávy (SMS), zkuste **zavolat možnost zavolat mi** .
- Ujistěte se, že váš telefon může přijímat volání nebo zprávy SMS od telefonního čísla založeného na USA.

Když dostanete textovou zprávu nebo telefonní hovor, zadejte kód, který se zobrazí v textovém poli.

### <a name="credit-card-declined-or-not-accepted"></a>Platební karta byla zamítnuta nebo přijata.

Virtuální nebo předem placené kreditní a debetní karty se jako platba za předplatná Azure neakceptují. Další informace o tom, co další může způsobit odmítnutí vaší karty, najdete v článku o [registraci v Azure](https://support.microsoft.com/help/4042960), pokud se vaše debetní karta nebo platební karta neodmítly.

### <a name="free-trial-is-not-available"></a>Bezplatná zkušební verze není k dispozici.

Použili jste v minulosti předplatné Azure? Smlouva o podmínkách použití Azure omezuje aktivaci bezplatné zkušební verze pouze na nové uživatele Azure. Pokud jste už měli nějaký jiný typ předplatného Azure, nemůžete si aktivovat bezplatnou zkušební verzi. Zvažte registraci k předplatnému s průběžnými [platbami](https://azure.microsoft.com/offers/ms-azr-0003p/).

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Na účtu bezplatné zkušební verze se mi účtuje poplatek

Po registraci se na účtu platební karty může zobrazit malý ověřovací symbol. Tento postup je odebraný do tří až pěti dnů. Pokud máte obavy o správu nákladů, přečtěte si další informace o tom, jak [zabránit](billing-getting-started.md)neočekávaným nákladům.

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Nejde aktivovat plán výhod Azure, jako je MSDN, BizSpark, BizSparkPlus nebo MPN.

Ujistěte se, že používáte správné přihlašovací údaje pro přihlášení. Pak zkontrolujte program zvýhodnění a ujistěte se, že máte nárok.

- MSDN 
  - Ověřte svůj stav způsobilosti na [stránce účtu MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  - Pokud nemůžete ověřit svůj stav, obraťte se na [centra služeb zákazníkům MSDN](https://msdn.microsoft.com/library/aa493452.aspx)pro předplatná.
- Microsoft for Startups
  - Přihlaste se k [portálu Microsoftu pro spouštění](https://startups.microsoft.com/#start-two) a ověřte si stav nároku pro Microsoft pro spouštění.
  - Pokud nemůžete ověřit svůj stav, můžete získat pomoc ve [fórech Microsoftu pro spouštění](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN 
  - Přihlaste se k [portálu MPN](https://mspartner.microsoft.com/Pages/Locale.aspx) a ověřte svůj stav nároku. Pokud máte příslušné kompetence pro [cloudovou platformu](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx), můžete mít nárok na další výhody.
  - Pokud nemůžete ověřit svůj stav, obraťte se na [podporu programu MPN](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Nejde aktivovat nové předplatné Azure v otevřeném předplatném.

Pokud chcete vytvořit předplatné Azure v programu Open, musíte mít platný klíč OSA (Online Service Activation), ke kterému je přidružený aspoň jeden Azure v otevřeném tokenu. Pokud nemáte klíč OSA, kontaktujte některého z partnerů Microsoftu, kteří jsou uvedení v [Microsoft Pinpoint](http://pinpoint.microsoft.com/).

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>Chyba: *Nemáte nárok na předplatné Azure.*

Chcete-li vyřešit tento problém, dvakrát ověřte, zda jsou splněny následující položky:

- Informace, které jste zadali pro váš profil účtu Azure (včetně kontaktní e-mailové adresy, ulice a telefonního čísla), jsou správné.
- Informace o kreditní kartě jsou správné.
- Ještě nemáte účet Microsoft, která používá stejné informace.

### <a name="error-your-current-account-type-is-not-supported"></a>Chyba: *Váš aktuální typ účtu není podporovaný.*

K tomuto problému může dojít, pokud je účet zaregistrován v [nespravovaném adresáři služby Azure AD](../active-directory/users-groups-roles/directory-self-service-signup.md)a není v adresáři služby Azure AD vaší organizace. 

Pokud chcete tento problém vyřešit, zaregistrujte si účet Azure pomocí jiného účtu nebo převezměte nespravovaný adresář služby AD. Další informace najdete v tématu [převzetí nespravovaného adresáře jako správce v Azure Active Directory](../active-directory/users-groups-roles/domains-admin-takeover.md).
 
## <a name="additional-help-resources"></a>Další materiály pro usnadnění

Další články týkající se řešení potíží s fakturací a předplatnými Azure

- [Odmítnutá karta](billing-troubleshoot-declined-card.md)
- [Problémy s přihlašováním předplatného](billing-troubleshoot-sign-in-issue.md)
- [Nenalezena žádná předplatná](billing-no-subscriptions-found.md)
- [Zákaz zobrazení firemních nákladů](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Kontaktujte nás, abychom vám pomohli.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další postup

- [Dokumentace k fakturaci Azure](index.md)
