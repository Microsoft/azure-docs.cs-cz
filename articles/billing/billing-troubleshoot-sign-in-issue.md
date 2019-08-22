---
title: Řešení potíží s přihlašováním k předplatným Azure
description: Pomáhá vyřešit problémy, u kterých se nemůžete přihlásit Azure Portal nebo centra účtů Azure.
author: v-miegge
manager: na
editor: na
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: ca641813e8b01a39d31a56e3730424b0fa1d6436
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657044"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Řešení potíží s přihlašováním k předplatným Azure

Tato příručka vám pomůže vyřešit problémy, při kterých se nemůžete přihlásit Azure Portal nebo centra účtů Azure. 

## <a name="issues"></a>Problémy

### <a name="page-hangs-in-the-loading-status"></a>Stránka se zablokuje ve stavu načítání.

Pokud vaše stránka internetového prohlížeče přestane reagovat, vyzkoušejte každý z následujících kroků, dokud nebudete moci získat přístup k Azure Portal.

- Aktualizujte stránku.
- Použijte jiný internetový prohlížeč.
- Pro váš prohlížeč použijte privátní režim procházení. Pro Internet Explorer: Klikněte na **nástroje** > **zabezpečení** > **InPrivate procházení**a potom přejděte a přihlaste se do [Azure Portal](https://portal.azure.com/) nebo [centra účtů Azure](https://account.azure.com/Subscriptions).

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>Automaticky jste přihlášeni jako jiný uživatel.

K tomuto problému může dojít, pokud použijete více než jeden uživatelský účet v internetovém prohlížeči.

Chcete-li tento problém vyřešit, zkuste jednu z následujících metod:

- Vymažte mezipaměť a odstraňte soubory cookie v Internetu. V Internet Exploreru klikněte na **nástroje** > **Možnosti** > Internetu**Odstranit**. Ujistěte se, že jsou zaškrtnutá políčka pro dočasné soubory, soubory cookie, heslo a historii procházení, a pak klikněte na Odstranit.
- Resetujte nastavení aplikace Internet Explorer tak, aby vrátilo všechna osobní nastavení, která jste udělali. Klikněte na **nástroje** > **Možnosti** > Internetu**Upřesnit** > zaškrtněte políčko **Odstranit osobní nastavení** > **resetovat**.
- Pro váš prohlížeč použijte privátní režim procházení. Pro Internet Explorer:  Klikněte na **nástroje** > **zabezpečení** > **InPrivate procházení**a potom přejděte a přihlaste se do [Azure Portal](https://portal.azure.com/) nebo [centra účtů Azure](https://account.azure.com/Subscriptions).

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Můžu se přihlásit, ale nenašly se *žádné odběry*

K tomuto problému dochází, pokud jste vybrali na špatný adresář, nebo pokud váš účet nemá dostatečná oprávnění.

**Scénář 1:** V [Azure Portal](https://portal.azure.com/) byla přijata chybová zpráva.

Chcete-li tento problém vyřešit:

- Ujistěte se, že je vybraná na správný adresář Azure kliknutím na váš účet v pravém horním rohu.
- Pokud je vybraný správný adresář Azure, ale pořád se vám zobrazí chybová zpráva, přidaný účet [přidaný jako vlastník](billing-add-change-azure-subscription-administrator.md).

**Scénář 2:** V [centrum účtů Azure](https://account.windowsazure.com/Subscriptions) byla přijata chybová zpráva.

Zkontrolujte, zda je účet, který jste použili účet správce. Pokud chcete ověřit, kdo je správcem účtu, postupujte podle těchto kroků:

1. Přihlaste se k [zobrazit předplatná na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Vyberte předplatné, které chcete zkontrolovat a pak hledejte v části **nastavení**.

3. Vyberte **vlastnosti**. Správce účtu předplatného se zobrazí v **správce účtu** pole.

## <a name="additional-help-resources"></a>Další materiály pro usnadnění

Další články týkající se řešení potíží s fakturací a předplatnými Azure

- [Odmítnutá karta](billing-troubleshoot-declined-card.md)
- [Problémy s registrací předplatného](billing-troubleshoot-azure-sign-up.md)
- [Nenalezena žádná předplatná](billing-no-subscriptions-found.md)
- [Zákaz zobrazení firemních nákladů](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Kontaktujte nás, abychom vám pomohli.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další postup

- [Dokumentace k fakturaci Azure](index.md)
