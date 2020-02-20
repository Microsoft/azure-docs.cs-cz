---
title: Řešení potíží s přihlášením k předplatnému Azure
description: Tato příručka vám pomůže vyřešit problémy s přihlášením na Azure Portal nebo Centrum účtů Azure.
author: v-miegge
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: v-miegge
ms.openlocfilehash: 40a645fe4b74043d84dc770f15d5e3c3ec907f02
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199479"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Řešení potíží s přihlášením k předplatnému Azure

Tato příručka vám pomůže vyřešit problémy s přihlášením na Azure Portal nebo Centrum účtů Azure.

## <a name="issues"></a>Problémy

### <a name="page-hangs-in-the-loading-status"></a>Stránka zamrzne ve stavu načítání

Pokud stránka v internetovém prohlížeči zamrzne, vyzkoušejte postupně všechny následující kroky, dokud se nedostanete na Azure Portal.

- Aktualizujte stránku.
- Použijte jiný internetový prohlížeč.
- Použijte privátní režim prohlížeče. Internet Explorer: Klikněte na **Nástroje** > **Zabezpečení** > **Procházení InPrivate**, a pak přejděte na [Azure Portal](https://portal.azure.com/) nebo [Centrum účtů Azure](https://account.azure.com/Subscriptions).

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>Automaticky se přihlásíte jako jiný uživatel

K tomuto problému může dojít v případě, že v internetovém prohlížeči používáte více než jeden účet.

Při řešení tohoto problému zkuste použít některou z následujících metod:

- Vymažte mezipaměť a odstraňte internetové soubory cookie. V Internet Exploreru klikněte na **Nástroje** > **Možnosti internetu** > **Odstranit**. Ujistěte se, že jsou zaškrtnutá políčka pro dočasné soubory, soubory cookie, hesla a historie procházení, a pak klikněte na Odstranit.
- Resetováním nastavení aplikace Internet Explorer vraťte všechna osobní nastavení, která jste nakonfigurovali. Klikněte na **Nástroje** > **Možnosti internetu** > **Upřesnit** > vyberte políčko **Odstranit osobní nastavení** > **Resetovat**.
- Použijte privátní režim prohlížeče. Internet Explorer:  Klikněte na **Nástroje** > **Zabezpečení** > **Procházení InPrivate**, a pak přejděte na [Azure Portal](https://portal.azure.com/) nebo [Centrum účtů Azure](https://account.azure.com/Subscriptions).

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Můžu se přihlásit, ale zobrazí se zpráva *Nenašla se žádná předplatná*

K tomuto problému dochází, pokud jste vybrali nesprávný adresář nebo pokud váš účet nemá dostatečná oprávnění.

**Scénář 1:** Chybová zpráva se objeví na webu [Azure Portal](https://portal.azure.com/).

Pokud chcete tento problém vyřešit:

- Klikněte vpravo nahoře na svůj účet a ujistěte se, že je vybrán správný adresář Azure.
- Pokud je vybrán správný adresář Azure, ale chybová zpráva se přesto zobrazuje, zajistěte, aby váš účet byl [přidán jako Vlastník](add-change-subscription-administrator.md).

**Scénář 2:** Chybová zpráva se objeví v [Centru účtů Azure](https://account.windowsazure.com/Subscriptions).

Zkontrolujte, jestli použitý účet je Správce účtu. Pokud chcete ověřit, kdo je správce účtu, postupujte takto:

1. Přihlaste se k [zobrazení předplatných na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Vyberte předplatné, které chcete zkontrolovat, a pak se podívejte pod **Nastavení**.

3. Vyberte **Vlastnosti**. Správce účtu předplatného se zobrazí v poli **Správce účtu**.

## <a name="additional-help-resources"></a>Další pomocné materiály

Další články týkající se řešení potíží s fakturací a předplatnými Azure

- [Odmítnutá karta](troubleshoot-declined-card.md)
- [Problémy s registrací předplatného](troubleshoot-azure-sign-up.md)
- [Nenalezena žádná předplatná](no-subscriptions-found.md)
- [Zákaz zobrazení firemních nákladů](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Kontaktujte nás, abychom vám pomohli

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

- [Dokumentace k fakturaci Azure](../../billing/index.md)
