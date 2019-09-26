---
title: E-mail o zůstatku po splatnosti z Azure | Microsoft Docs
description: Popisuje, jak provést platbu v případě, že je u předplatného Azure zůstatek po splatnosti.
services: ''
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: billing
ms.assetid: d0b88c92-fb9d-4d12-931b-c26104ad63e9
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: banders
ms.openlocfilehash: 8f2dbc84fe09c825d8ef25aabafba7a672220192
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121712"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Řešení zůstatku po splatnosti u předplatného Azure

Pokud vaše platba nedorazí nebo ji nemůžeme zpracovat, může vám přijít e-mail nebo se může v Centru účtů nebo na webu Azure Portal zobrazit upozornění.
Pokud jste [správce účtu](billing-subscription-transfer.md#whoisaa), můžete nezaplacené poplatky uhradit na webu [Azure Portal](https://portal.azure.com). Pokud platíte pomocí faktury, zašlete svoji platbu na adresu uvedenou v dolní části faktury.

> [!IMPORTANT]
> * Pokud používáte stejnou platební kartu u více předplatných a všechna jsou po splatnosti, musíte celý nevyrovnaný zůstatek uhradit najednou.
> * Platební nástroj, který použijete k uhrazení nezaplacených poplatků, se nastaví jako nový aktivní způsob platby pro všechna předplatná, u kterých jste používali způsob platby, který selhal.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Řešení zůstatku po splatnosti na webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako správce účtu.
1. Vyhledejte **Cost Management a fakturace**.
1. Na stránce Přehled se zobrazí seznam vašich předplatných. Pokud je předplatné ve stavu po splatnosti, klikněte na odkaz **Vyrovnat zůstatek**.
    ![Snímek obrazovky s odkazem Vyrovnat zůstatek](./media/billing-azure-subscription-past-due-balance/settle-balance-entry-point.png)
1. Celkový nevyrovnaný zůstatek odráží nezaplacené poplatky napříč všemi službami Microsoftu, u kterých se používá způsob platby, který selhal.
1. Vyberte způsob platby, který chcete použít k uhrazení zůstatku. Tento způsob platby se nastaví jako aktivní způsob platby pro všechna předplatná, u kterých se aktuálně používá způsob platby, který selhal.
    ![Snímek obrazovky s odkazem Vybrat způsob platby](./media/billing-azure-subscription-past-due-balance/settle-balance-screen.png)
1. Pokud jsou i u vybraného způsobu platby nezaplacené poplatky za služby Microsoftu, promítnou se do celkového nevyrovnaného zůstatku. Musíte uhradit i tyto nezaplacené poplatky.
1. Klikněte na **Zaplatit**.

## <a name="not-getting-billing-email-notifications"></a>Nepřicházejí vám e-mailová oznámení o fakturaci?

Pokud jste správce účtu, [zkontrolujte, která e-mailová adresa je nastavená jako příjemce oznámení](billing-how-to-change-azure-account-profile.md). Doporučujeme použít e-mailovou adresu, kterou pravidelně kontrolujete. Pokud je e-mail správný, zkontrolujte složku s nevyžádanou poštou.

## <a name="if-i-forget-to-pay-what-happens"></a>Co se stane, když zapomenu zaplatit?

Služba se zruší a vaše prostředky už nebudou k dispozici. Vaše data v Azure se odstraní 90 dnů po ukončení služby. Další informace najdete v tématu [Centrum zabezpečení Microsoftu – Jak spravujeme vaše data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

Pokud jste si jisti, že se vaše platba zpracovala, ale vaše předplatné je stále deaktivované, kontaktujte [podporu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
