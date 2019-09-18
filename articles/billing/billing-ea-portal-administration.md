---
title: Správa portálu Azure EA
description: V tomto článku jsou vysvětlené nejčastější úkoly, které provádí správce portálu Azure EA.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 7b594cef2fefa164ef900cbfd65fcf95d5d47b94
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901130"
---
# <a name="azure-ea-portal-administration"></a>Správa portálu Azure EA

V tomto článku jsou vysvětlené nejčastější úkoly, které provádí správce portálu Azure EA Portal (https://ea.azure.com). Portál Azure EA je online portál pro správu, kde můžou zákazníci spravovat náklady na služby Azure EA. Úvodní informace o portálu Azure EA najdete v článku [Začínáme s portálem Azure EA](billing-ea-portal-get-started.md).

## <a name="add-a-new-enterprise-administrator"></a>Přidání nového podnikového správce

Podnikoví správci mají při správě registrace do Azure EA největší oprávnění. Úvodní správce Azure EA byl vytvořen při nastavení smlouvy EA. Nové správce můžete kdykoliv přidat nebo odebrat. Nové správce přidávají jenom stávající správci. Další informace o přidání dalších podnikových správců najdete v tématu [Vytvoření dalšího podnikového správce](billing-ea-portal-get-started.md#create-another-enterprise-admin). Další informace o rolích a úlohách fakturačního profilu najdete v tématu [Role a úlohy fakturačního profilu](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-user-state-from-pending-to-active"></a>Aktualizace stavu uživatele z Čeká na vyřízení na Aktivní

Při prvním přidání nových vlastníků účtů do registrace Azure EA se u nových vlastníků zobrazí stav _Čeká na vyřízení_. Jakmile noví vlastníci účtu dostanou úvodní aktivační e-mail, můžou se přihlásit a aktivovat si účet. Když si účet aktivují, aktualizuje se stav účtu z _Čeká na vyřízení_ na _Aktivní_. Při vytvoření komerčního účtu se může novým uživatelům zobrazit výzva k zadání jména a příjmení. Pokud se zobrazí, musí zadat požadované informace, aby mohli pokračovat, a pak se účet aktivuje.

## <a name="add-a-department-admin"></a>Přidání správce oddělení

Jakmile správce Azure EA vytvoří oddělení, může přidat správce oddělení a přidružit je k oddělením. Správce oddělení může vytvářet nové účty. Nové účty jsou potřeba pro vytvářená předplatná Azure EA.

Další informace o přidání oddělení najdete v tématu Vytvoření oddělení Azure EA.

## <a name="enterprise-user-roles"></a>Role podnikových uživatelů

Na portálu Azure EA můžete spravovat náklady a použití Azure EA. Na portálu Azure EA jsou tři hlavní role:

- Správce smlouvy Enterprise
- Správce oddělení
- Vlastník účtu

Každá role má jinou úroveň přístupu a jiná oprávnění.

Další informace o rolích uživatelů najdete v tématu Role podnikových uživatelů.

## <a name="add-an-azure-ea-account"></a>Přidání účtu Azure EA

Účet Azure EA je organizační jednotka portálu Azure EA, která se používá ke správě předplatných a při vytváření sestav. Pokud chcete získat přístup ke službám Azure, potřebujete vytvořit účet nebo vám ho někdo musí vytvořit.

Další informace o účtech Azure najdete v tématu Přidání účtu.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Převod podnikového účtu do nové registrace

Při převodu podnikového účtu do nové registrace mějte na paměti následující body:

- Převádějí se jenom účty, které jsou zadané v požadavku. Pokud zvolíte všechny účty, přenesou se všechny.
- Zdrojová registrace zůstane v aktivním nebo prodlouženém stavu. Registraci můžete používat dál, dokud jí nevyprší platnost.

### <a name="effective-transfer-date"></a>Platné datum převodu

Platným datem převodu může být datum, které je stejné nebo následuje po počátečním datu registrace, do které chcete provést převod. Registrace, do které účet převádíte, se nazývá _cílová registrace_. Po převodu účtu zůstanou všechny informace o použití, které byly v účtu před platným datem převodu, v registraci, ze které provádíte převod. Registrace, ze které provádíte převod, je _zdrojová registrace_.  Využití zdrojové registrace se odečte z peněžního závazku nebo se účtuje jako nadlimitní využití. Využití, ke kterému dojde po platném datu převodu, se přenese do nové registrace, kde se odpovídajícím způsobem odúčtuje.

Převod účtu je možné provést k dřívějšímu datu, ale nejpozději k počátečnímu datu cílové registrace. Nebo nejpozději k platnému počátečnímu datu zdrojové registrace.

### <a name="monetary-commitment"></a>Peněžní závazek

Peněžní závazek nejde mezi registracemi převést. Zůstatky peněžních závazků jsou smluvně vázány na registraci, kde byly objednány. V rámci převodu účtu nebo registrace se peněžní závazek nepřevádí.

### <a name="services-affected"></a>Dotčené služby

Při převodu účtu nedochází k výpadku. Pokud jsou poskytnuté všechny potřebné informace, může převod proběhnout tentýž den jako požadavek.

### <a name="prerequisites"></a>Požadavky

K žádosti o převod účtu je potřeba sdělit následující informace:


- Název převáděného účtu a ID jeho vlastníka.
- U zdrojové registrace uveďte její číslo a převáděný účet.
- U cílové registrace uveďte číslo registrace, do které probíhá převod.
- Platné datum převodu účtu může být stejné datum, jako je počáteční datum cílové registrace, nebo datum pozdější.

Další body, na které je potřeba myslet před převodem účtu:

- Cílovou a zdrojovou registraci musí schválit správce EA.
  - V některých případech může Microsoft požádat správce EA, který spravuje zdrojovou registraci, o další schválení.
- Pokud převod účtu vašim požadavkům nevyhovuje, můžete zvážit převod registrace.
- Při převodu účtu se převádějí všechny služby, předplatná, účty, oddělení a celá struktura registrace, včetně všech správců oddělení EA.
- Převodem účtu se u zdrojové registrace nastaví stav _Přenesené_. Převedený účet bude k dispozici jenom pro sestavy, které se týkají historie použití.
- Do registrace, která je v přeneseném stavu, nemůžete přidávat role ani předplatná. Stav brání dalšímu použití registrace.
- Zbývající zůstatek peněžního závazku ve zdrojové smlouvě se ruší, včetně budoucích podmínek.


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Převod podnikové registrace do nové registrace

Pokud požádáte o převod celé podnikové registrace do jiné registrace, provedou se následující akce:

- Převedou se všechny služby Azure, všechna předplatná, účty, oddělení a celá struktura registrace, včetně všech správců oddělení EA.
- U registrace se nastaví stav _Přenesené_. Přenesená registrace bude k dispozici jenom pro sestavy, které se týkají historie použití.
- Do přenesené registrace už nemůžete přidávat role ani předplatná. Přenesený stav brání dalšímu použití této registrace.
- Zbývající zůstatek peněžního závazku ve smlouvě se ruší, včetně budoucích podmínek.

### <a name="effective-transfer-date"></a>Platné datum převodu

Platným datem převodu může být datum, které je stejné nebo následuje po počátečním datu registrace, kterou chcete převést do cílové registrace.

Využití zdrojové registrace se odečte z peněžního závazku nebo se účtuje jako nadlimitní využití. Využití, ke kterému dojde po platném datu převodu, se přenese do nové registrace, kde se odpovídajícím způsobem odúčtuje.

### <a name="effective-transfer-date-in-the-past"></a>Platné datum převodu v minulosti

Převod účtu je možné provést k dřívějšímu datu, ale nejpozději k počátečnímu datu cílové registrace. Nebo nejpozději k platnému počátečnímu datu zdrojové registrace.

### <a name="monetary-commitment"></a>Peněžní závazek

Peněžní závazek nejde mezi registracemi převést. Zůstatky peněžních závazků jsou smluvně vázány na registraci, kde byly objednány. V rámci převodu účtu nebo registrace se peněžní závazek nepřevádí.

### <a name="services-affected"></a>Dotčené služby

Při převodu účtu nedochází k výpadku. Pokud jsou poskytnuté všechny požadované informace, může převod proběhnout tentýž den jako požadavek.

### <a name="prerequisites"></a>Požadavky

K žádosti o převod registrace je potřeba sdělit následující informace:

- U zdrojové registrace uveďte její číslo a převáděný účet.
- U cílové registrace uveďte číslo registrace, do které probíhá převod.
- Platné datum převodu registrace může být stejné datum, jako je počáteční datum cílové registrace, nebo datum pozdější. Vybrané datum neovlivňuje použití u již vydaných faktur za nadlimitní využití.

Další body, na které je potřeba myslet před převodem registrace:

- Cílovou a zdrojovou registraci musí schválit správce EA.
  - V některých případech může Microsoft požádat správce EA, který spravuje zdrojovou registraci, o další schválení.
- Pokud převod registrace vašim požadavkům nevyhovuje, zvažte možnost převodu účtu.
- Převádějí se jenom účty, které zadáte. Můžete požádat o převod všech svých účtů.
- Zdrojová registrace zůstane v aktivním nebo prodlouženém stavu. Registraci můžete používat dál, dokud jí nevyprší platnost.

## <a name="change-account-owner"></a>Změna vlastníka účtu

Na portálu Azure EA můžete převádět předplatná od jednoho vlastníka účtu k jinému. Další informace najdete v tématu o [změně vlastníka účtu](billing-ea-portal-get-started.md#change-account-owner).

## <a name="subscription-transfer-effects"></a>Účinek převodu předplatného

Pokud se předplatné Azure převádí do účtu ve stejném tenantovi Azure Active Directory, zachovají si všichni uživatelé, skupiny a instanční objekty, které ke správě prostředků používaly [přístup na základě role (RBAC)](../role-based-access-control/overview.md), stále svůj přístup.

Zobrazení uživatelů s přístupem RBAC k předplatnému:

1. Na webu Azure Portal otevřete **Předplatná**.
2. Vyberte předplatné, které chcete zobrazit, a pak vyberte **Řízení přístupu (IAM)** .
3. Vyberte **Přiřazení rolí**. Na stránce Přiřazení rolí se zobrazí seznam všech uživatelů, kteří mají k předplatnému přístup RBAC.

Pokud se předplatné převede do účtu jiného tenanta Azure AD, tak všichni uživatelé, skupiny a instanční objekty, které měly ke správě prostředků přístup [RBAC](../role-based-access-control/overview.md), tento přístup _ztratí_. I když neexistuje přístup RBAC, může být přístup k předplatnému dostupný prostřednictvím bezpečnostních mechanismů, jako je například:

- Správa certifikátů, které udělují uživateli oprávnění správce prostředků předplatného. Další informace najdete v tématu o [vytvoření a nahrání certifikátu pro správu Azure](../cloud-services/cloud-services-certs-create.md).
- Přístupové klíče pro služby, jako je Storage. Další informace najdete v tématu [Přehled účtu Azure Storage](../storage/common/storage-account-overview.md).
- Oprávnění pro vzdálený přístup ke službám, jako je Azure Virtual Machines.

Pokud příjemce potřebuje omezit přístup k prostředkům Azure, měl by zvážit aktualizaci tajných kódů přidružených ke službě. Většinu prostředků můžete aktualizovat takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V nabídce centra vyberte **Všechny prostředky**.
3. Vyberte prostředek.
4. Na stránce prostředku klikněte na **Nastavení**. Tady se můžete podívat na stávající tajné kódy nebo je můžete aktualizovat.



## <a name="close-an-azure-enterprise-enrollment"></a>Zavření registrace Azure Enterprise

Pokud chcete zavřít registraci Azure EA, máte na výběr tyto možnosti:

- Zrušte na webu Azure Portal všechna předplatná přidružená k vaší smlouvě Azure EA.
- Obraťte se na softwarového poradce zákazníka nebo na partnera a požádejte ho o ukončení smlouvy Azure Enterprise Agreement.

## <a name="update-notification-settings"></a>Aktualizace nastavení oznámení

Registrace podnikových správců probíhá automaticky, aby mohli dostávat oznámení o použití, která se týkají jejich registrace. Každý podnikový správce může změnit interval jednotlivých oznámení nebo může tato oznámení úplně vypnout.

Kontakty pro oznámení se na portálu Azure EA zobrazují v oddílu **Kontakt pro oznámení**. Správou kontaktů pro oznámení zajistíte doručování oznámení Azure EA v organizaci těm správným lidem.

Zobrazení aktuálního nastavení oznámení:

1. Na portálu Azure EA přejděte na **Správa** > **Kontakt pro oznámení**.
2. E-mailová adresa – e-mailová adresa přidružená k účtu Microsoft podnikového správce nebo k pracovnímu či školnímu účtu, na který chodí oznámení.
3. Frekvence oznamování nefakturovaného zůstatku – nastavená frekvence posílání oznámení každému podnikovému správci.

Přidání kontaktu:

1. Klikněte na **+Přidat kontakt**.
2. Zadejte e-mailovou adresu a potvrďte ji.
3. Klikněte na **Uložit**.

Nový kontakt pro oznámení se zobrazí v oddílu **Kontakt pro oznámení**. Pokud chcete změnit frekvenci oznamování, vyberte kontakt pro oznámení a klikněte na symbol tužky, který je napravo od vybraného řádku. Nastavte **denní**, **týdenní**, **měsíční** nebo **žádnou** frekvenci.

Můžete také potlačit _blížící se koncové datum pokrytého období_ a _vypnout a zrušit zřízené blížící se datum_ u oznámení o životním cyklu. Pokud vypnete oznámení o životním cyklu, potlačíte tím oznámení o pokrytém období a o datu ukončení smlouvy.

## <a name="manage-partner-notifications"></a>Správa oznámení pro partnery

Správci partnerů můžou spravovat frekvenci, s jakou budou u svých registrací dostávat oznámení o jejich využití. Oznámení o nefakturovaném zůstatku dostávají automaticky jednou týdně. Frekvenci jednotlivých oznámení můžou změnit na měsíční, týdenní, denní nebo můžou oznámení úplně vypnout.

Pokud uživatel nedostává oznámení, zkontrolujte, jestli jsou správně nastavená. Použijte k tomu následující postup.

1. Přihlaste se na portál Azure EA jako správce partnera.
2. Klikněte na **Spravovat** a pak klikněte na kartu **Partner**.
3. Prohlédněte si seznam správců v oddílu **Správce**.
4. Pokud chcete upravit předvolby oznámení, najeďte myší na příslušného správce a klikněte na symbol tužky.
5. Podle potřeby aktualizujte frekvenci a životní cyklus oznámení.
6. Pokud potřebujete přidat kontakt, klikněte na **Přidat**.
7. Klikněte na **Uložit**.

![Příklad přidání kontaktu ](./media/billing-ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="azure-sponsorship-offer"></a>Nabídka Azure Sponsorship
Nabídka Azure Sponsorship se týká omezeného sponzorovaného účtu Microsoft Azure. Nabídka je k dispozici jenom na základě e-mailové pozvánky určené omezenému počtu zákazníků, které vybral Microsoft. Pokud máte na nabídku Microsoft Azure Sponsorship nárok, přijde vám e-mailem pozvánka určená pro vaše ID účtu.
Další informace naleznete v tématu:

- Přehled nabídky sponzoringu – https://azure.microsoft.com/en-us/offers/ms-azr-0143p/
- Portál se zůstatkem sponzoringu – https://www.microsoftazuresponsorships.com/balance  
- Nejčastější externí dotazy ke sponzoringu – https://azuresponsorships-staging.azurewebsites.net/faq
- Žádost o podporu při aktivaci sponzoringu – http://aka.ms/azrsponsorship

## <a name="next-steps"></a>Další kroky
- Přečtěte si, jak vám můžou [rezervace virtuálních počítačů](billing-ea-portal-vm-reservations.md) pomoci ušetřit peníze.
- Pokud potřebujete pomoc při řešení potíží s portálem Azure EA, přečtěte si téma [Řešení potíží s přístupem k portálu Azure EA](billing-ea-portal-troubleshoot.md).
