---
title: Řešení potíží při registraci nového účtu na webu Azure Portal nebo Centru účtů Azure
description: Řešení problému při pokusu o registraci nového účtu na portálu Microsoft Azure nebo v centru účtů
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: v-miegge
ms.openlocfilehash: 91b3b7b71524c76006ad4d431774e05e47fd0266
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131067"
---
# <a name="troubleshoot-issues-when-you-sign-up-for-a-new-account-in-azure-portal-or-azure-account-center"></a>Řešení potíží při registraci nového účtu na webu Azure Portal nebo Centru účtů Azure

Při pokusu o registraci nového účtu na portálu Microsoft Azure nebo v Centru účtů Azure může dojít k problému. Tato stručná příručka vás provede procesem registrace a probere běžné potíže pro jednotlivé kroky.

> [!NOTE]
> Pokud už máte účet a hledáte pokyny pro řešení potíží s přihlašováním, přečtěte si téma [Řešení potíží s přihlášením k předplatnému Azure](./troubleshoot-sign-in-issue.md).

## <a name="before-you-begin"></a>Než začnete

Než začnete s registrací, ověřte následující:

- Informace, které jste zadali pro profil účtu Azure (včetně kontaktní e-mailové adresy, adresy a telefonního čísla), jsou správné.
- Údaje o platební kartě jsou správné.​
- Ještě nemáte účet Microsoft se stejnými údaji.

## <a name="guided-walkthrough-of-azure-sign-up"></a>Podrobný názorný postup pro registraci do Azure

Prostředí pro registraci do Azure se skládá ze čtyř částí:

- Vaše údaje
- Telefonické ověření identity
- Ověření identity kartou
- Smlouva

Tento názorný postup ukazuje příklady správných informací pro registraci účtu Azure. Každá část obsahuje taky některé běžné problémy a jejich řešení.

## <a name="about-you"></a>Vaše údaje

![Vaše údaje](./media/troubleshoot-azure-sign-up/1.png)
 
### <a name="common-issues-and-solutions"></a>Běžné problémy a jejich řešení

#### <a name="you-see-the-message-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Zobrazí se zpráva typu Nemůžeme pokračovat v registraci kvůli problému s vaším účtem. Kontaktujte prosím podporu fakturace.

Pokud chcete tuto chybu vyřešit, postupujte podle těchto kroků:

1.  Přihlaste se k [Centru účtů Azure](https://account.azure.com/Profile) pomocí přihlašovacích údajů správce účtu.
1.  Vyberte **Upravit podrobnosti**.
1.  Ověřte, že všechna pole adresy jsou vyplněná a platná.
1.  Když si registrujete předplatné Azure, ověřte, že fakturační adresa pro registraci platební karty odpovídá vašim bankovním záznamům.

Pokud se vám tato zpráva bude zobrazovat dál, zkuste se zaregistrovat pomocí jiného prohlížeče.

A co procházení InPrivate?

#### <a name="free-trial-is-not-available"></a>Bezplatná zkušební verze není k dispozici

Použili jste v minulosti předplatné Azure? Smlouva o podmínkách použití Azure omezuje aktivaci bezplatné zkušební verze pouze na nové uživatele Azure. Pokud jste už měli nějaký jiný typ předplatného Azure, nemůžete si aktivovat bezplatnou zkušební verzi. Zvažte registraci [předplatného s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/).

#### <a name="you-see-the-message-you-are-not-eligible-for-an-azure-subscription"></a>Zobrazí se zpráva typu Nemáte oprávnění získat předplatné Azure.

Pokud chcete tento problém vyřešit, překontrolujte, jestli jsou pravdivá následující tvrzení:

- Informace, které jste zadali pro profil účtu Azure (včetně kontaktní e-mailové adresy, adresy a telefonního čísla), jsou správné.
- Údaje o platební kartě jsou správné.​
- Ještě nemáte účet Microsoft se stejnými údaji.

#### <a name="you-see-the-message-your-current-account-type-is-not-supported"></a>Zobrazí se zpráva typu Váš aktuální typ účtu se nepodporuje.

K tomuto problému může dojít, pokud je účet registrován v [nespravovaném adresáři Azure AD](../../active-directory/users-groups-roles/directory-self-service-signup.md) a není v adresáři Azure AD vaší organizace.
Pokud chcete tento problém vyřešit, zaregistrujte si účet Azure pomocí jiného účtu nebo převezměte nespravovaný adresář služby AD. Další informace najdete v tématu o [převzetí nespravovaného adresáře jako správce v Azure Active Directory](../../active-directory/users-groups-roles/domains-admin-takeover.md).

## <a name="identity-verification-by-phone"></a>Telefonické ověření identity

![Telefonické ověření identity](./media/troubleshoot-azure-sign-up/2.png)
 
Když prostřednictvím textové zprávy nebo telefonního hovoru přijmete kód, zadejte ho do textového pole.

### <a name="common-issues-and-solutions"></a>Běžné problémy a jejich řešení

#### <a name="no-verification-text-message-or-phone-call"></a>Žádná ověřovací textová zpráva ani telefonní hovor

I když je proces ověření registrace obvykle rychlý, může doručení ověřovacího kódu trvat až 4 minuty.

Tady jsou některé další tipy:

- Pro ověření můžete použít libovolné telefonní číslo, pokud splňuje příslušné požadavky. Telefonní číslo, které zadáte pro ověření, není uloženo jako kontaktní číslo pro účet.
  - Pro proces ověřování telefonem nejde použít telefonní číslo VoiP (Voice-over-IP).
  - Ujistěte se, že váš telefon může přijímat hovory nebo zprávy SMS z telefonních čísel v USA.
- Překontrolujte telefonní číslo, které zadáte, včetně kódu země, který vyberete v rozevírací nabídce.
- Pokud váš telefon nepřijímá textové zprávy (SMS), vyzkoušejte možnost **Zavolejte mi**.

## <a name="identity-verification-by-card"></a>Ověření identity kartou

![Ověření identity kartou](./media/troubleshoot-azure-sign-up/3.png)
 
### <a name="common-issues-and-solutions"></a>Běžné problémy a jejich řešení

#### <a name="credit-card-declined-or-not-accepted"></a>Platební karta byla odmítnuta nebo nebyla přijata

Při platbě za předplatná Azure se nepřijímají virtuální nebo předplacené kreditní nebo debetní karty. Další informace o tom, co dalšího může být příčinou odmítnutí vaší karty, najdete v tématu [Řešení potíží s odmítnutou kartou při registraci do Azure](./troubleshoot-declined-card.md).

#### <a name="credit-card-form-doesnt-support-my-billing-address"></a>Formulář platební karty nepodporuje vaši fakturační adresu

Vaše fakturační adresa musí být v zemi, kterou vyberete v části **Vaše údaje**. Ověřte, že jste vybrali správnou zemi.

#### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>Indikátor průběhu zamrzne v části Ověření identity kartou

Aby bylo možné ověření identity kartou dokončit, musí být v prohlížeči povoleny soubory cookie třetích stran.

Aktualizujte nastavení souborů cookie v prohlížeči následujícím postupem.

1. Aktualizujte nastavení souborů cookie.
   - Pokud používáte **Chrome**:
     - Vyberte **Nastavení** > **Zobrazit rozšířená nastavení** > **Ochrana soukromí** > **Nastavení obsahu**. Zrušte zaškrtnutí políčka **Blokovat soubory cookie třetích stran a data webů**.

   - Pokud používáte **Microsoft Edge**:
     - Vyberte **Nastavení** > **Zobrazit upřesňující nastavení** > **Soubory cookie** > **Neblokovat soubory cookie**.

1. Aktualizujte registrační stránku Azure a zkontrolujte, jestli je problém vyřešený.
1. Pokud aktualizace problém nevyřešila, ukončete a restartujte prohlížeč a potom to zkuste znovu.

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Na účtu bezplatné zkušební verze vidím naúčtovaný poplatek

Po registraci se na účtu platební karty může objevit dočasné ověřovací zadržení malé částky. Bude odebráno do tří až pěti dnů. Pokud máte obavy o správu nákladů, přečtěte si další informace o [analýze neočekávaných poplatků](../understand/analyze-unexpected-charges.md).

## <a name="agreement"></a>Smlouva

Dokončete smlouvu.

## <a name="other-issues"></a>Další problémy

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Nejde aktivovat plán výhod Azure, jako je MSDN, BizSpark, BizSparkPlus nebo MPN

Zkontrolujte, že k přihlášení používáte správné přihlašovací údaje. Potom zkontrolujte program výhod a ujistěte se, že jste pro něj způsobilí.
- MSDN
  - Stav způsobilosti si můžete ověřit na [stránce účtu MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  - Pokud si stav nemůžete ověřit, kontaktujte [centra služeb zákazníkům pro předplatná MSDN](/previous-versions/mappoint/aa493452(v=msdn.10)).
- Microsoft for Startups
  - Přihlaste se na [portál Microsoft for Startups](https://startups.microsoft.com/#start-two) a ověřte si stav způsobilosti pro Microsoft for Startups.
  - Pokud si stav nemůžete ověřit, můžete získat pomoc ve [fórech programu Microsoft for Startups](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN
  - Přihlaste se na [portál MPN](https://mspartner.microsoft.com/Pages/Locale.aspx) a ověřte si stav způsobilosti. Pokud máte příslušné [kompetence pro cloudovou platformu](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx), můžete být způsobilí pro další výhody.
  - Pokud si stav nemůžete ověřit, kontaktujte [podporu MPN](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).


### <a name="cant-activate-new-azure-in-open-subscription"></a>Nejde aktivovat předplatné Azure v rámci licenčního programu Open

Pokud chcete vytvořit předplatné Azure v rámci licenčního programu Open, musíte mít platný klíč OSA (Online Service Activation), ke kterému je přidružený alespoň jeden token Azure v rámci licenčního programu Open. Pokud klíč OSA nemáte, kontaktujte některého z partnerů Microsoftu, kteří jsou uvedení na webu [Microsoft Pinpoint.](https://pinpoint.microsoft.com/)

## <a name="additional-help-resources"></a>Další pomocné materiály

Další články týkající se řešení potíží s fakturací a předplatnými Azure

- [Odmítnutá karta](./troubleshoot-declined-card.md)
- [Problémy s přihlašováním k předplatnému](./troubleshoot-sign-in-issue.md)
- [Nenalezena žádná předplatná](./no-subscriptions-found.md)
- [Zákaz zobrazení firemních nákladů](./enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Kontaktujte nás, abychom vám pomohli

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="find-out-more-about-azure-cost-management"></a>Další informace o službě Azure Cost Management

- [Dokumentace ke správě nákladů a fakturaci Azure](../index.yml)