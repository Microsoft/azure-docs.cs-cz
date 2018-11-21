---
title: Změna platební karty pro Azure | Dokumentace Microsoftu
description: Popisuje postup při změně platební karty použité k úhradě předplatného Azure
services: ''
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: cwatson
ms.openlocfilehash: 0db76d7133e28a0826d9ff07cefe02134cd95a47
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52274468"
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Přidání, aktualizace nebo odebrání kreditní nebo debetní kartou pro Azure

V centru účtů můžete přidat novou platební kartu, aktualizovat stávající platební kartu nebo odstranit platební karty, které nepoužíváte. Musí být [správce účtu](billing-subscription-transfer.md#whoisaa) k provedení těchto změn.

**Chcete přepnout na platbu na fakturu?** Zobrazit [platbu na fakturu za odběr služeb Azure](billing-how-to-pay-by-invoice.md).
 
<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card"></a>Přidat novou kreditní nebo debetní kartu

1. Přihlaste se k [centra pro účty](https://account.windowsazure.com/Subscriptions) jako správce účtu.
1. Vyberte předplatné.
1. Na pravé straně stránky vyberte **Spravovat způsoby platby**.

    ![Snímek obrazovky s možností metody spravovat vybrali.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Vyberte "+" a přidejte kartu.

    ![Snímek obrazovky zobrazující možnost Upravit vedle způsob platby.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Zadejte podrobnosti o kreditní nebo debetní kartě.
1. Vyberte **Uložit**. 

Pokud dojde k chybě po přidat platební kartu, najdete v článku [odmítl platební karty v Azure, zaregistrujte](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-or-debit-card"></a>Aktualizovat existující kreditní nebo debetní kartu

Pokud vaší platební karty získá obnoveno a počet zůstala stejná, aktualizujte existující platební karty podrobnosti jako datum vypršení platnosti. Pokud změny číslo platební karty protože karty dojde ke ztrátě nebo vám ho někdo ukradne, nebo vypršela jeho platnost, postupujte podle pokynů [přidat platební kartu jako způsob platby](#addcard) oddílu. Není nutné aktualizovat CVV.

1. Přihlaste se k [centra účtů Azure](https://account.windowsazure.com/Subscriptions) jako správce účtu.
1. Vyberte předplatné, který je spojen s kartou.
1. Vyberte **spravovat způsoby platby**.
1. Vyberte **upravit** vedle karty, které chcete aktualizovat.
1. Aktualizujte podrobnosti kreditní nebo debetní kartu.
1. Vyberte **Uložit**.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Použití jiné platební karty pro předplatné Azure

1. Přihlaste se k [centra účtů Azure](https://account.windowsazure.com/Subscriptions) jako správce účtu.
1. Vyberte předplatné, který je spojen s kartou.
1. Na pravé straně stránky vyberte **Spravovat způsoby platby**.
1. Klikněte na tlačítko **místo toho použijte** vedle karty, kterou chcete použít. Tím se aktualizuje také další předplatná, aktuálně přiřazen k této karty. 

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>Odebrat kreditní nebo debetní kartu z účtu

1. Přihlaste se k [centra účtů Azure](https://account.windowsazure.com/Subscriptions) jako správce účtu.
1. Vyberte předplatné, který je spojen s kartou.
3. Na pravé straně stránky vyberte **Spravovat způsoby platby**.
4. Klikněte na tlačítko **odstranit** pro platební karty, kterou chcete odstranit.

Pokud je spojen s jiné aktivní předplatné Microsoft vaší platební karty, nelze odebrat z účtu Azure. Odeberte platební kartu z všechny aktivní odběry, které máte s Microsoftem a zkuste to znovu.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Moje předplatné je zakázané. Proč nelze odebrat platební karty nyní?

Poté, co vaše předplatné je zakázané nebo bylo zrušeno, čekáme 90 dní, než vaše předplatné se odstraňuje natrvalo. V případě, že chcete předplatné znovu aktivovat uchováváme během doby uchování vaším způsobem platby na soubor. Poté je zcela odstranit předplatné.

Pokud je potřeba odebrat kreditní nebo debetní karta předtím, než skončí se uchovávají 90 dnů, [znovu aktivovat vaše předplatné](billing-subscription-become-disable.md). Pokud nelze aktivovat, [požádejte podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Proč zobrazuje "vypršela platnost vaší relace přihlášení. Klikněte prosím sem a znovu přihlásit,"?

Pokud i v případě, že jste už přihlášení bude dále zobrazovat tato chybová zpráva a zpět v, zkuste to znovu s Chcete relaci privátního procházení.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Jak použít jinou kartu pro každé předplatné, které budu mít?

Bohužel Pokud předplatné už používají stejné karty, není možné oddělit je, aby používaly různé karty. Ale když se zaregistrujete k novému předplatnému, můžete použít nový způsob platby pro dané předplatné.

### <a name="how-do-i-make-payments"></a>Jak mohu učinit platby?

Pokud jste nastavili kreditní nebo debetní kartu jako způsob platby, účtujeme automaticky vaší karty po každého fakturačního období. Není nutné nic dělat.

Pokud jste [platíte fakturami](billing-how-to-pay-by-invoice.md), odeslat vaše platba do umístění uvedených v dolní části faktury.

### <a name="how-do-i-make-a-one-time-payment"></a>Jak mohu učinit jednorázovou platbu?

Bohužel Azure v současné době nepodporuje platby jednorázové pro kreditní nebo debetní karty. 

### <a name="how-do-i-change-the-tax-id"></a>Jak změním ID daň?

Chcete-li přidat nebo aktualizovat daňové identifikační číslo, navštivte [ **profilu** v centru účtů Azure](https://account.azure.com/Profile)a pak vyberte **daňové záznam**. Toto DIČ slouží k výpočtu slevy na dani a zobrazí se na vaší faktuře.

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
