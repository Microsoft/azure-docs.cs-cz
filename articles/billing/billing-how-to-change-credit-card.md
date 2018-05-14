---
title: Změnit platební karty pro Azure. | Microsoft Docs
description: Popisuje, jak na tom, jak změnit z platební karty použít k platbě za předplatné Azure
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 6b0db12da7c187ce80f1d55ce8b9900601033068
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Přidání, aktualizace nebo odeberte kreditní nebo debetní kartu pro Azure

V Centru pro účet můžete přidat novou platební kartu, aktualizovat existující platební karty nebo odstranit platební karty, které nepoužíváte. Musí být [správce účtu](billing-subscription-transfer.md#whoisaa) k provedení těchto změn.

**Chcete přepnout věnovat podle faktury?** V tématu [platit pro předplatná Azure podle faktury](billing-how-to-pay-by-invoice.md).
 
<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card"></a>Přidat novou kartu kreditní nebo debetní

1. Přihlaste se k [centra účtů](https://account.windowsazure.com/Subscriptions) jako správce účtu.
1. Vyberte předplatné.
1. Na pravé straně stránky vyberte **Spravovat způsoby platby**.

    ![Snímek obrazovky zobrazující spravovat platební metody možnost.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Vyberte "+" přidat na kartě.

    ![Snímek obrazovky, který ukazuje možnost Upravit vedle platby.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Zadejte kreditní nebo debetní karty podrobnosti.
1. Vyberte **Uložit**. 

Pokud dojde k chybě po přidání platební karty, přečtěte si téma [platební karty odmítl v Azure registrace](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-or-debit-card"></a>Aktualizovat stávající kreditní nebo debetní karty

Pokud získá obnovit platební karty a číslo zůstane stejný, aktualizujte existující platební karty podrobnosti, třeba datum vypršení platnosti. Pokud změny číslo platební karty protože karta dojde ke ztrátě nebo odcizení nebo jejichž platnost vypršela, postupujte podle kroků v [přidat platební kartu jako způsob platby](#addcard) části. Nemusíte aktualizovat CVV.

1. Přihlaste se k [centra účtů Azure](https://account.windowsazure.com/Subscriptions) jako správce účtu.
1. Vyberte odběr, který je spojen s kartou.
1. Vyberte **spravovat způsoby platby**.
1. Vyberte **upravit** vedle karty, které chcete aktualizovat.
1. Karta kreditní nebo debetní podrobné informace o aktualizaci.
1. Vyberte **Uložit**.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Použijte jinou platební kartu pro předplatné Azure

1. Přihlaste se k [centra účtů Azure](https://account.windowsazure.com/Subscriptions) jako správce účtu.
1. Vyberte odběr, který je spojen s kartou.
1. Na pravé straně stránky vyberte **Spravovat způsoby platby**.
1. Klikněte na tlačítko **místo toho použijte** vedle kartu, která chcete použít. Tím se aktualizuje také žádné předplatné, které aktuálně přidružen Tato karta. 

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>Odeberte kreditní nebo debetní kartu z účtu

1. Přihlaste se k [centra účtů Azure](https://account.windowsazure.com/Subscriptions) jako správce účtu.
1. Vyberte odběr, který je spojen s kartou.
3. Na pravé straně stránky vyberte **Spravovat způsoby platby**.
4. Klikněte na tlačítko **odstranit** pro platební karty, které chcete odstranit.

Pokud platební karty je spojen s další aktivní odběry Microsoft, nelze odebrat z účtu Azure. Odebrání z platební karty všechny aktivní odběry, které jste se společností Microsoft a zkuste to znovu.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Moje předplatné je zakázané. Proč nelze odebrat platební kartou nyní?

Po vaše předplatné je zakázané nebo zrušit, čekáme 90 dní před trvalému odstranění předplatného. V případě, že chcete znovu aktivovat předplatné jsme váš způsob platby u souboru zachovat během doby uchování. Potom odběr úplně odstranit.

Pokud je třeba odebrat kreditní nebo debetní karty před končí-90denní období, [znova si předplatné aktivovat](billing-subscription-become-disable.md). Pokud nelze znovu aktivovat, [požádejte podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Proč zobrazuje "vypršela platnost vaší relace přihlášení. Prosím kliknutím sem se přihlaste"?

Pokud stále se zobrazuje tato chybová zpráva, i když už jste se odhlásili a zpět v, zkuste to znovu s privátní procházení relací.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Jak použít jinou kartu pro každé předplatné, k dispozici?

Bohužel Pokud vaše předplatná se už používá stejnou kartu, není možné oddělit, aby uživatelé používali různé karty. Ale při registraci nového předplatného, můžete použít nový způsob platby pro toto předplatné.

### <a name="how-do-i-make-payments"></a>Jak lze vytvořit platby?

Pokud jste nastavili kreditní nebo debetní karty jako váš způsob platby, můžeme automaticky účtují karty po každé fakturačního období. Nemusíte provádět žádné kroky.

Pokud jste [platícího podle faktury](billing-how-to-pay-by-invoice.md), odeslání vaší platební do umístění uvedených v dolní části faktury.

### <a name="how-do-i-make-a-one-time-payment"></a>Jak lze vytvořit jednorázové platba?

Bohužel Azure aktuálně nepodporuje jednorázové platby kreditní nebo debetní karet. 

### <a name="how-do-i-change-the-tax-id"></a>Jak změním daňové číslo?

Chcete-li přidat nebo aktualizovat daňové číslo, navštivte [ **profil** v centru účtů Azure](https://account.azure.com/Profile), pak vyberte **daně záznam**. Toto ID daň se používá pro výpočty výjimky daně a zobrazí se na vaší faktuře.

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.
