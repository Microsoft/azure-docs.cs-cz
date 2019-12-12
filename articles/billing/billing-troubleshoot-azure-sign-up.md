---
title: Řešení potíží s registrací do Azure
description: Řešení problému při pokusu o registraci nového účtu na portálu Microsoft Azure nebo v centru účtů
services: billing
author: v-miegge
manager: dcscontentpm
editor: v-jesits
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: f1414f96a00626b8e47decd22e4560bff1394468
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872719"
---
# <a name="troubleshoot-azure-sign-up"></a>Řešení potíží s registrací do Azure

Při pokusu o registraci nového účtu na portálu Microsoft Azure nebo v Centru účtů Azure může dojít k problému. Než problém začnete řešit, ověřte platnost následujících tvrzení:

- Informace, které jste zadali pro profil účtu Azure (včetně kontaktní e-mailové adresy, adresy a telefonního čísla), jsou správné.
- Údaje o platební kartě jsou správné.​
- Ještě nemáte účet Microsoft se stejnými údaji.

## <a name="resolutions"></a>Řešení

Při řešení chyb vyberte z následujících problémů ten, ke kterému dochází, když se pokusíte zaregistrovat do Azure.

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Chyba: *Nemůžeme pokračovat v registraci kvůli problému s vaším účtem. Kontaktujte prosím podporu fakturace.*

Pokud chcete tento problém vyřešit, postupujte následovně:

1. Přihlaste se k [Centru účtů Azure](https://account.azure.com/Profile) pomocí přihlašovacích údajů správce účtu.

2. Vyberte **Upravit podrobnosti**.

3. Zajistěte, aby byla všechna pole adresy vyplněná a platná.

4. Když se registrujete pro předplatné Azure, zajistěte, aby fakturační adresa pro registraci platební karty odpovídala vašim bankovním záznamům.

Pokud se vám tato chybová zpráva bude zobrazovat dál, zkuste se zaregistrovat pomocí jiného prohlížeče.

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>Indikátor průběhu zamrzne v části *Ověření identity kartou*.

Aby bylo možné ověření identity kartou dokončit, musí být v prohlížeči povoleny soubory cookie třetích stran.

![Ověření identity kartou](./media/billing-troubleshoot-azure-sign-up/identify-verification-by-card.png)
 
Aktualizujte nastavení souborů cookie v prohlížeči následujícím postupem.

1. Pokud používáte Chrome, vyberte **Nastavení** > **Zobrazit rozšířená nastavení** > **Ochrana soukromí** > **Nastavení obsahu**. Zrušte zaškrtnutí políčka **Blokovat soubory cookie třetích stran a data webů**.

2. Pokud používáte Microsoft Edge, vyberte **Nastavení** > **Zobrazit upřesňující nastavení** > **Soubory cookie** > **Neblokovat soubory cookie**.

3. Aktualizujte registrační stránku Azure a potom zkontrolujte, jestli je problém vyřešený.

4. Pokud aktualizace problém nevyřešila, ukončete a restartujte prohlížeč a pak to zkuste znovu.

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>Formulář platební karty nepodporuje vaši fakturační adresu

Vaše fakturační adresa musí být umístěná v zemi, kterou vyberete v části **Vaše údaje**. Zajistěte, abyste vybrali správnou zemi.

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Žádné textové zprávy ani hovory během ověřování účtu registrace

I když je proces obvykle rychlý, může doručení ověřovacího kódu trvat až 4 minuty. Telefonní číslo, které zadáte pro ověření, není uloženo jako kontaktní číslo pro účet.

Tady jsou některé další tipy:

- Pro proces ověřování telefonem nejde použít telefonní číslo VoiP (Voice-over-IP).
- Překontrolujte telefonní číslo, které zadáte, včetně kódu země, který vyberete v rozevírací nabídce.
- Pokud váš telefon nepřijímá textové zprávy (SMS), vyzkoušejte možnost **Zavolejte mi**.
- Ujistěte se, že váš telefon může přijímat hovory nebo zprávy SMS z telefonních čísel v USA.

Když prostřednictvím textové zprávy nebo telefonního hovoru přijmete kód, zadejte ho do textového pole.

### <a name="credit-card-declined-or-not-accepted"></a>Platební karta byla odmítnuta nebo nebyla přijata

Při platbě za předplatná Azure se nepřijímají virtuální nebo předplacené kreditní nebo debetní karty. Další informace o tom, co dalšího může být příčinou odmítnutí vaší karty, najdete v tématu [o řešení potíží s odmítnutou kartou při registraci do Azure](https://support.microsoft.com/help/4042960).

### <a name="free-trial-is-not-available"></a>Bezplatná zkušební verze není k dispozici

Použili jste v minulosti předplatné Azure? Smlouva o podmínkách použití Azure omezuje aktivaci bezplatné zkušební verze pouze na nové uživatele Azure. Pokud jste už měli nějaký jiný typ předplatného Azure, nemůžete si aktivovat bezplatnou zkušební verzi. Zvažte registraci [předplatného s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/).

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Na účtu bezplatné zkušební verze vidím naúčtovaný poplatek

Po registraci se na účtu platební karty může objevit ověřovací zadržení malé částky. To bude odebráno do tří až pěti dnů. Pokud máte obavy o správu nákladů, přečtěte si další informace o [prevenci neočekávaných nákladů](billing-getting-started.md).

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Nejde aktivovat plán výhod Azure, jako je MSDN, BizSpark, BizSparkPlus nebo MPN

Ujistěte se, že k přihlášení používáte správné přihlašovací údaje. Pak zkontrolujte program výhod a ujistěte se, že jste pro něj způsobilí.

- MSDN 
  - Stav způsobilosti si můžete ověřit na [stránce účtu MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  - Pokud si stav nemůžete ověřit, kontaktujte [centra služeb zákazníkům pro předplatná MSDN](https://msdn.microsoft.com/library/aa493452.aspx).
- Microsoft for Startups
  - Přihlaste se na [portál Microsoft for Startups](https://startups.microsoft.com/#start-two) a ověřte si stav způsobilosti pro Microsoft for Startups.
  - Pokud si stav nemůžete ověřit, můžete získat pomoc ve [fórech programu Microsoft for Startups](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN 
  - Přihlaste se na [portál MPN](https://mspartner.microsoft.com/Pages/Locale.aspx) a ověřte si stav způsobilosti. Pokud máte příslušné [kompetence pro cloudovou platformu](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx), můžete být způsobilí pro další výhody.
  - Pokud si stav nemůžete ověřit, kontaktujte [podporu MPN](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Nejde aktivovat předplatné Azure v rámci licenčního programu Open

Pokud chcete vytvořit předplatné Azure v rámci licenčního programu Open, musíte mít platný klíč OSA (Online Service Activation), ke kterému je přidružený alespoň jeden token Azure v rámci licenčního programu Open. Pokud klíč OSA nemáte, kontaktujte některého z partnerů Microsoftu, kteří jsou uvedení na webu [Microsoft Pinpoint.](https://pinpoint.microsoft.com/)

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>Chyba: *Nemáte oprávnění získat předplatné Azure*

Pokud chcete tento problém vyřešit, překontrolujte, jestli jsou pravdivá následující tvrzení:

- Informace, které jste zadali pro profil účtu Azure (včetně kontaktní e-mailové adresy, adresy a telefonního čísla), jsou správné.
- Údaje o platební kartě jsou správné.​
- Ještě nemáte účet Microsoft se stejnými údaji.

### <a name="error-your-current-account-type-is-not-supported"></a>Chyba: *Váš aktuální typ účtu se nepodporuje*

K tomuto problému může dojít, pokud je účet registrován v [nespravovaném adresáři Azure AD](../active-directory/users-groups-roles/directory-self-service-signup.md) a není v adresáři Azure AD vaší organizace. 

Pokud chcete tento problém vyřešit, zaregistrujte si účet Azure pomocí jiného účtu nebo převezměte nespravovaný adresář služby AD. Další informace najdete v tématu o [převzetí nespravovaného adresáře jako správce v Azure Active Directory](../active-directory/users-groups-roles/domains-admin-takeover.md).
 
## <a name="additional-help-resources"></a>Další pomocné materiály

Další články týkající se řešení potíží s fakturací a předplatnými Azure

- [Odmítnutá karta](billing-troubleshoot-declined-card.md)
- [Problémy s přihlašováním k předplatnému](billing-troubleshoot-sign-in-issue.md)
- [Nenalezena žádná předplatná](billing-no-subscriptions-found.md)
- [Zákaz zobrazení firemních nákladů](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Kontaktujte nás, abychom vám pomohli

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

- [Dokumentace k fakturaci Azure](index.md)
