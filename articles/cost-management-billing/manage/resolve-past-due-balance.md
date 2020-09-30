---
title: E-mail o zůstatku po splatnosti z Azure
description: Popisuje, jak provést platbu v případě, že je u předplatného Azure zůstatek po splatnosti.
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 3c42db58017dc3a692c227f6eb454cf7b597e77b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91297580"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Řešení zůstatku po splatnosti u předplatného Azure

Tento článek se týká zákazníků, kteří si zaregistrovali do Azure online s použitím platební karty a mají fakturační účet programu Microsoft Online Services. Zjistěte, jak [zkontrolovat typ vašeho fakturačního účtu](#check-the-type-of-your-account). Pokud máte fakturační účet pro Smlouvu se zákazníkem Microsoftu, přečtěte si místo toho článek [Placení vyúčtování služeb Microsoft Azure](../understand/pay-bill.md).

Pokud vaše platba nedorazí nebo ji nemůžeme zpracovat, přijde vám e-mail a na webu Azure Portal se zobrazí upozornění, že je vaše předplatné po splatnosti. Pokud je váš výchozí způsob platby platební karta, [správce účtu](../understand/subscription-transfer.md#whoisaa), může nezaplacené poplatky uhradit na webu Azure Portal. Pokud platíte pomocí faktury (šekem nebo převodem), zašlete svou platbu na adresu uvedenou ve spodní části faktury.

> [!IMPORTANT]
> * Pokud používáte stejnou platební kartu u více předplatných a všechna jsou po splatnosti, musíte celý nevyrovnaný zůstatek uhradit najednou.
> * Platební karta, kterou použijete k uhrazení nezaplacených poplatků, se nastaví jako nový výchozí způsob platby pro všechna předplatná, u kterých se používal způsob platby, který selhal.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Řešení zůstatku po splatnosti na webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako správce účtu.
1. Vyhledejte položku **Správa nákladů a fakturace**.
1. Na stránce **Přehled** vyberte předplatné po splatnosti.
1. Na stránce **Přehled předplatného** klikněte na červený banner s informací o zůstatku po splatnosti a vyrovnejte zůstatek.
    > [!NOTE]
    > Pokud nejste správce účtu, nebudete moct zůstatek vyrovnat.
1. Na nové stránce **Vyrovnat zůstatek** klikněte na **Vybrat způsob platby**.
    ![Snímek obrazovky s odkazem Vybrat způsob platby](./media/resolve-past-due-balance/settle-balance-screen.png)

1. V novém okně na pravé straně vyberte platební kartu z rozevíracího seznamu nebo přidejte novou kliknutím na modrý odkaz **Přidat nový způsob platby**. Tato platební karta se nastaví jako aktivní způsob platby pro všechna předplatná, u kterých se aktuálně používá způsob platby, který selhal.
     > [!NOTE]
     > * Celkový nevyrovnaný zůstatek odráží nezaplacené poplatky napříč všemi službami Microsoftu, u kterých se používá způsob platby, který selhal.
     > * Pokud jsou i u vybraného způsobu platby nezaplacené poplatky za služby Microsoftu, promítnou se do celkového nevyrovnaného zůstatku. Musíte uhradit i tyto nezaplacené poplatky.
1. Klikněte na **Zaplatit**.

## <a name="troubleshoot-declined-credit-card"></a>Řešení potíží se zamítnutou platební kartou

Pokud je platba platební kartou zamítnutá vaší finanční institucí, spojte se se svou finanční institucí a problém vyřešte. Kontaktujte banku a ujistěte se, že:
- Na kartě jsou povolené mezinárodní transakce.
- Karta má dostatečný limit kreditu nebo finančních prostředky pro vyrovnání zůstatku.
- Na kartě jsou povolené opakované platby.

## <a name="not-getting-billing-email-notifications"></a>Nepřicházejí vám e-mailová oznámení o fakturaci?

Pokud jste správce účtu, [zkontrolujte, která e-mailová adresa je nastavená jako příjemce oznámení](change-azure-account-profile.md). Doporučujeme použít e-mailovou adresu, kterou pravidelně kontrolujete. Pokud je e-mail správný, zkontrolujte složku s nevyžádanou poštou.

## <a name="if-i-forget-to-pay-what-happens"></a>Co se stane, když zapomenu zaplatit?

Služba se zruší a vaše prostředky už nebudou k dispozici. Vaše data v Azure se odstraní 90 dnů po ukončení služby. Další informace najdete v článku [Centrum zabezpečení Microsoftu – jak spravujeme vaše data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

Pokud jste si jisti, že se vaše platba zpracovala, ale vaše předplatné je stále deaktivované, kontaktujte [podporu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="check-the-type-of-your-account"></a>Ověření typu účtu
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
