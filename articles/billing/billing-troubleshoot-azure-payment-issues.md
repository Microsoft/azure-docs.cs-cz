---
title: Řešení potíží s platbami za Azure
description: Řešení problémů při aktualizaci platebních informací v profilu účtu na portálu Microsoft Azure nebo v centru účtů
services: azure
author: v-miegge
manager: dcscontentpm
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: jaserano
ms.openlocfilehash: 8fb1af7041b028560a218ba19e10c0df2e921317
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795861"
---
# <a name="troubleshoot-azure-payment-issues"></a>Řešení potíží s platbami za Azure

Může dojít k problému nebo chybě při pokusu o aktualizaci platebních informací v profilu účtu na portálu Microsoft Azure nebo v Centru účtů Azure.

Pokud chcete tyto potíže vyřešit, zvolte si z následujících témat to, které nejvíce připomíná vaši situaci.

## <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>Z uloženého způsobu platby faktur nejde odebrat platební kartu

Záměrně se nedá odebrat platební karta z aktivního předplatného.

Pokud potřebujete stávající kartu odstranit, buď musíte do předplatného přidat novou kartu, aby se dal starý platební nástroj úspěšně odstranit, nebo musíte zrušit předplatné. Tím se předplatné trvale odstraní a karta se odebere.

## <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>Po přidání nového způsobu platby nejde odstranit starý způsob platby

Nový platební nástroj nemusí být přidružený k předplatnému. Pokud chcete přidružit platební nástroj k předplatnému, přečtěte si téma [Přidání, aktualizace nebo odebrání kreditní nebo debetní karty v Azure](billing-how-to-change-credit-card.md).

Pokud chcete řešit problémy s odmítnutou kartou, přečtěte si téma [Řešení potíží s odmítnutou kartou při registraci do Azure](billing-troubleshoot-declined-card.md).

## <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>Nedá se odstranit způsob platby kvůli chybové zprávě *Nejde odstranit způsob platby*

K tomu dochází kvůli nevyrovnanému zůstatku. Dorovnejte nevyrovnaný zůstatek a pak odstraňte způsob platby.

## <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>Nejde aktualizovat způsob platby, protože se nezobrazují předplatná v rámci účtu

Možná používáte ID e-mailu, které se liší od ID používaného pro předplatná.

Pokud chcete tento problém vyřešit, přečtěte si téma [Chyba přihlášení Nenašla se žádná předplatná pro Azure Portal nebo Centrum účtů Azure](billing-no-subscriptions-found.md).

## <a name="unable-to-make-payment-for-a-subscription"></a>Nejde provést platbu za předplatné

Pokud se zobrazí chybová zpráva *Platba je po splatnosti. Došlo k problému s vaším způsobem platby* nebo *Je nám líto, ale údaje se nepodařilo uložit. Zavřete prohlížeč a zkuste to znovu.* , je na kartě platba čekající na zpracování, protože karta byla zamítnuta vaší finanční institucí.

Ověřte, že na platební kartě je dostatečný zůstatek k provedení platby. Pokud není, použijte k provedení platby jinou kartu nebo se obraťte na svou finanční instituci a požádejte o pomoc při řešení problému.

Na svou banku se prosím obraťte při těchto problémech:

- Nejsou povoleny mezinárodní transakce.
- Karta má úvěrový limit a zůstatek musí být vyrovnán.
- Na kartě jsou povoleny opakované platby.

## <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>Nejde změnit způsob platby kvůli problémům s prohlížečem (prohlížeč nereaguje, nenačte se a tak dál)

Odhlaste se ze všech aktivních relací Azure a pak podle pokynů v článku [Procházení InPrivate v Microsoft Edgi](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate) spusťte relaci InPrivate v Microsoft Edgi nebo Internet Exploreru.

V privátní relaci postupujte podle pokynů v tématu [Změna platební karty](billing-how-to-change-credit-card.md) a aktualizujte nebo změňte informace o platební kartě.

Můžete také zkusit udělat následující:

- Aktualizujte prohlížeč.
- Použijte jiný prohlížeč.
- Odstraňte soubory cookie uložené v mezipaměti.

## <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>Předplatné je po aktualizaci způsobu platby stále zakázané

K tomuto problému dochází kvůli nevyrovnanému zůstatku. Dorovnejte nevyrovnaný zůstatek a pak odstraňte způsob platby.

## <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>Nejde změnit způsob platby kvůli stránce chybové odpovědi XML

Tato zpráva se zobrazí, pokud pomocí webu [Azure Portal](https://portal.azure.com/) přidáte novou platební kartu.

Pokud chcete přidat podrobnosti o kartě, přihlaste se k portálu účtů Azure pomocí e-mailové adresy správce účtu.

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
