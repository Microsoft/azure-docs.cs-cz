---
title: Řešení potíží se zobrazením fakturačního účtu na webu Azure Portal
description: Tento článek vám pomůže vyřešit případné potíže při pokusu o zobrazení fakturačního účtu na webu Azure Portal.
author: amberbhargava
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 71561f6ecdde09f259e54260e7bab4dcc0e85c98
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132631"
---
# <a name="troubleshoot-viewing-your-billing-account-in-the-azure-portal"></a>Řešení potíží se zobrazením fakturačního účtu na webu Azure Portal

Když se zaregistrujete do Azure, vytvoří se vám fakturační účet. Ten slouží ke správě faktur a plateb a sledování nákladů. Je možné, že máte přístup k několika fakturačním účtům. Může to být třeba v situaci, kdy se zaregistrujete do Azure, abyste mohli pracovat na svých osobních projektech. ale současně máte k Azure přístup i prostřednictvím smlouvy Enterprise vaší organizace nebo smlouvy se zákazníkem Microsoftu. Pro každý z těchto scénářů tak máte samostatný fakturační účet. Tento článek vám pomůže vyřešit případné potíže při pokusu o zobrazení fakturačního účtu na webu Azure Portal.

Fakturační účty můžete zobrazit a analyzovat na stránce [Správa nákladů a fakturace](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) v Azure.

Další informace o fakturačních účtech a určení typu fakturačního účtu najdete v tématu [Zobrazení fakturačních účtů na webu Azure Portal](view-all-accounts.md).

Pokud se vám fakturační účet na webu Azure Portal nezobrazuje, vyzkoušejte následující možnosti:

## <a name="sign-in-to-a-different-tenant"></a>Přihlášení k jinému tenantovi

Fakturační účet je přidružený k jednomu konkrétnímu tenantovi Azure Active Directory. Pokud jste přihlášení k nesprávnému tenantovi, váš fakturační účet na stránce Správa nákladů a fakturace nezobrazí. Pomocí následujících kroků můžete na webu Azure Portal přepnout na jiného tenanta a zobrazit fakturační účty v tomto tenantovi.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V horní části stránky vyberte váš profil (e-mailovou adresu).
1. Vyberte **Přepnout adresář**.  
    ![Screenshot zachycující výběr položky pro přepnutí adresáře na portálu](./media/troubleshoot-account-not-found/select-switch-directory.png)
1. V části **Všechny adresáře** vyberte požadovaný adresář.  
    ![Screenshot zachycující výběr adresáře na portálu](./media/troubleshoot-account-not-found/select-directory.png)

## <a name="sign-in-with-a-different-email-address"></a>Přihlášení s jinou e-mailovou adresou

Někteří uživatelé mají několik e-mailových adres, pomocí kterých se mohou přihlásit na [Azure Portal](https://portal.azure.com). Ne všechny e-mailové adresy mají přístup k fakturačnímu účtu. Pokud se přihlásíte pomocí e-mailové adresy, která má oprávnění ke správě prostředků, ale nemá oprávnění k zobrazení fakturačního účtu, fakturační účet se vám na stránce [Správa nákladů a fakturace](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) na webu Azure Portal nezobrazí.

Pokud si chcete zajistit přístup k fakturačnímu účtu, přihlaste se k webu Azure Portal pomocí e-mailové adresy, která má pro tento fakturační účet oprávnění.

## <a name="sign-in-with-a-different-identity"></a>Přihlášení s jinou identitou

Někteří uživatelé mají dvě identity se stejnou e-mailovou adresou – pracovní nebo školní účet a osobní účet. K zobrazení fakturačního účtu ale obvykle má oprávnění jenom jedna z těchto identit. Možná máte dvě identity se stejnou e-mailovou adresou. Když se přihlásíte pomocí identity, která nemá oprávnění k zobrazení fakturačního účtu, fakturační účet se vám na stránce [Správa nákladů a fakturace](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) nezobrazí. K přepnutí identity použijte následující postup:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) v anonymním okně nebo okně InPrivate.
1. Pokud vaše e-mailová adresa obsahuje dvě identity, zobrazí se možnost výběru osobního účtu nebo pracovního/školního účtu. Vyberte jeden z těchto účtů.
1. Pokud se fakturační účet na stránce Správa nákladů a fakturace na webu Azure Portal nezobrazí, zopakujte kroky 1 a 2 a vyberte druhou identitu.

## <a name="contact-us-for-help"></a>Kontaktujte nás, abychom vám pomohli

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

Přečtěte si následující články věnované předplatným a fakturaci, které vám pomohou s řešením vašich potíží.

- [Odmítnutá karta](./troubleshoot-declined-card.md)
- [Problémy s přihlášením k předplatnému](./troubleshoot-sign-in-issue.md)
- [Nenalezena žádná předplatná](./no-subscriptions-found.md)
- [Zákaz zobrazení firemních nákladů](./enterprise-mgmt-grp-troubleshoot-cost-view.md)