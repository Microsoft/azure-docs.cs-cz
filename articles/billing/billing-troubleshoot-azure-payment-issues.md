---
title: Řešení problémů s platbami Azure
description: Řešení problému při aktualizaci účtu platební informace v portál Microsoft Azure nebo centru účtů
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
ms.author: jaserano
ms.openlocfilehash: 45f6f181ddbf7e76662f76c433d8ff68c18f0de1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657083"
---
# <a name="troubleshoot-azure-payment-issues"></a>Řešení problémů s platbami Azure

Když se pokusíte aktualizovat účet platební informace v portál Microsoft Azure nebo centru účtů Azure, může dojít k potížím. Před řešením problému Vezměte v úvahu následující pokyny:

- Ujistěte se, že informace, které jste zadali pro váš profil účtu Azure (včetně kontaktní e-mailové adresy, ulice a telefonního čísla), jsou správné.
- Ujistěte se, že údaje o platební kartě jsou správné.​
- Ujistěte se, že ještě nemáte účet Microsoft se stejnými údaji.

## <a name="issues"></a>Problémy

Pokud chcete vyřešit všechny chyby, vyberte problém, ke kterému dochází při pokusu o registraci do Azure.

### <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>Nepovedlo se odebrat platební kartu z uloženého způsobu platby za fakturaci.

Podle návrhu nemůžete odebrat platební kartu z aktivního předplatného.

Pokud se má odstranit existující karta, musí se do předplatného přidat buď nová karta, aby se starý platební nástroj mohl úspěšně odstranit, nebo musíte předplatné zrušit. Tím se předplatné trvale odstraní a karta se odstraní.

### <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>Po přidání nového způsobu platby nejde odstranit starý způsob platby.

Nový platební nástroj nemusí být přidružený k předplatnému. Pokud chcete přidružit platební nástroj k předplatnému, přečtěte si téma [Přidání, aktualizace nebo odebrání kreditní nebo debetní karty pro Azure](billing-how-to-change-credit-card.md).

Informace o řešení potíží týkajících se odmítnutých karet najdete v tématu [jak řešit potíže s odmítnutou kartou při registraci v Azure](billing-troubleshoot-declined-card.md).

### <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>Nejde odstranit způsob platby, protože *nejde odstranit způsob platby* – chyba.

K tomu dochází z důvodu nevyrovnaného zůstatku. Před odstraněním způsobu platby vymažte všechny nedokončené zůstatky.

### <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>V části můj účet se nepovedlo zobrazit předplatná, abyste mohli aktualizovat způsob platby.

Můžete používat ID e-mailu, které se liší od verze, která se používá pro odběry.

Pokud chcete tento problém vyřešit, přečtěte si téma [žádná předplatná se nenašla Chyba při přihlašování pro Azure Portal nebo centrum účtů Azure](billing-no-subscriptions-found.md).

### <a name="unable-to-make-payment-for-a-subscription"></a>Nepovedlo se provést platbu za předplatné.

Pokud se zobrazí chybová zpráva: *Platba je po splatnosti. Došlo k potížím s vaším způsobem* platby nebo *je nám líto, ale informace nejde Uložit. Zavřete prohlížeč a zkuste to znovu.* a na kartě se pak čeká na platbu, protože karta byla vaší finanční institucí zamítnutá.

Ověřte, že platební karta má dostatečný zůstatek k provedení platby. Pokud tomu tak není, použijte k provedení platby jinou kartu nebo se obraťte na svou finanční instituci a problém vyřešte.

Následující problémy vám poskytne vaše banka:

- Mezinárodní transakce nejsou povolené.
- Karta má úvěrový limit a zůstatek musí být vyrovnán.
- Na kartě je povolena opakovaná platba.

### <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>Nejde změnit způsob platby kvůli problémům s prohlížečem (prohlížeč nereaguje, nenačte se atd.).

Odhlaste se ze všech aktivních relací Azure a pak postupujte podle kroků v [článku procházení InPrivate v Microsoft Edge](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate) a spusťte relaci InPrivate v Microsoft Edge nebo Internet Exploreru.

V privátní relaci postupujte podle kroků uvedených v části [Změna platební karty](billing-how-to-change-credit-card.md) pro aktualizaci nebo změnu informací o platební kartě.

Můžete se také pokusit provést následující akce:

- Aktualizace prohlížeče
- Použít jiný prohlížeč
- Odstranění souborů cookie uložených v mezipaměti

### <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>Moje předplatné je po aktualizaci způsobu platby stále zakázané.

K tomuto problému dochází z důvodu nevyrovnaného zůstatku. Před odstraněním způsobu platby vymažte všechny nedokončené zůstatky.

### <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>Nejde změnit způsob platby kvůli stránce s odpovědí na chybu XML.

Tato zpráva se zobrazí, pokud pomocí [Azure Portal](https://portal.azure.com/) přidáte novou platební kartu.

Pokud chcete přidat podrobnosti karty, přihlaste se k portálu účtů Azure pomocí e-mailové adresy správce účtu.

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
