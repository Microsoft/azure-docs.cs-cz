---
title: Správa portálu Azure EA
description: V tomto článku jsou vysvětlené nejčastější úkoly, které provádí správce portálu Azure EA.
author: bandersmsft
ms.author: banders
ms.date: 09/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 33a53fa46d7d07183b77f2608d44f8ea5d0d2804
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132257"
---
# <a name="azure-ea-portal-administration"></a>Správa portálu Azure EA

V tomto článku jsou vysvětlené nejčastější úkoly, které provádí správce portálu Azure EA Portal (https://ea.azure.com). Portál Azure EA je online portál pro správu, kde můžou zákazníci spravovat náklady na služby Azure EA. Úvodní informace o portálu Azure EA najdete v článku [Začínáme s portálem Azure EA](ea-portal-get-started.md).

## <a name="associate-an-account-to-a-department"></a>Přidružit účet k oddělení

Podnikoví správci můžou přidružovat existující účty k oddělením v rámci registrace.

### <a name="to-associate-an-account-to-a-department"></a>Přidružení účtu k oddělení

1. Přihlaste se k portálu Azure EA jako podnikový správce.
1. V levém navigačním panelu vyberte **Manage** (Spravovat).
1. Vyberte **Oddělení**.
1. Najeďte myší na řádek s požadovaným účtem a vyberte ikonu tužky na pravé straně.
1. V rozevírací nabídce vyberte požadované oddělení.
1. Vyberte **Uložit**.

## <a name="department-spending-quotas"></a>Kvóty útraty oddělení

Zákazníci se smlouvou Enterprise můžou nastavit nebo změnit kvóty útraty pro každé oddělení v rámci registrace. Výše kvóty útraty se nastavuje pro aktuální období zálohy. Pokud nedojde k aktualizaci hodnot, na konci aktuálního období zálohy systém prodlouží stávající kvótu útraty na další období zálohy.

Správce oddělení může zobrazit kvótu útraty, ale pouze podnikový správce může aktualizovat její výši. Podnikový správce a správce oddělení obdrží oznámení, když dojde k dosažení 50 %, 75 %, 90 % a 100 % kvóty.

### <a name="enterprise-administrator-to-set-the-quota"></a>Nastavení kvóty podnikovým správcem:

 1. Otevřete portál Azure EA.
 1. V levém navigačním panelu vyberte **Manage** (Spravovat).
 1. Vyberte kartu **Oddělení**.
 1. Vyberte požadované oddělení.
 1. Vyberte symbol tužky v části Podrobnosti o oddělení nebo vyberte symbol **+ Přidat oddělení** a přidejte nové oddělení i s kvótou útraty.
 1. V části Podrobnosti o oddělení zadejte do pole Kvóta útraty (USD) výši kvóty útraty v měně registrace (hodnota musí být vyšší než 0).
    - V tuto chvíli můžete upravit také Název oddělení a Nákladové středisko.
 1. Vyberte **Uložit**.

Kvóta útraty oddělení se teď zobrazí na kartě Oddělení v zobrazení Seznam oddělení. Na konci aktuálního období zálohy se na portálu Azure EA zachovají kvóty útraty pro další období zálohy.

Výše kvóty oddělení je nezávislá na aktuální záloze na Azure a výše kvóty a související upozornění se vztahují pouze na využití první stranou. Kvóta útraty oddělení slouží pouze k informačním účelům a nevynucuje limity útraty.

### <a name="department-administrator-to-view-the-quota"></a>Zobrazení kvóty správcem oddělení:

1. Otevřete portál Azure EA.
1. V levém navigačním panelu vyberte **Manage** (Spravovat).
1. Výběrem karty **Oddělení** zobrazte zobrazení Seznam oddělení s kvótami útraty.

Pokud jste nepřímý zákazník, váš distribuční partner musí povolit funkce nákladů.

## <a name="enterprise-user-roles"></a>Role podnikových uživatelů

Na portálu Azure EA můžete spravovat náklady a použití Azure EA. Na portálu Azure EA jsou tři hlavní role:

- Správce smlouvy Enterprise
- Správce oddělení
- Vlastník účtu

Každá role má jinou úroveň přístupu a jiná oprávnění.

Další informace o rolích uživatelů najdete v tématu [Role podnikových uživatelů](./ea-portal-get-started.md#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Přidání účtu Azure EA

Účet Azure EA je organizační jednotka portálu Azure EA Portal. Slouží ke správě předplatných a také ke generování sestav. Pokud chcete získat přístup ke službám Azure, potřebujete vytvořit účet nebo vám ho někdo musí vytvořit.

Další informace o účtech Azure najdete v tématu [Přidání účtu](./ea-portal-get-started.md#add-an-account).

## <a name="enterprise-devtest-offer"></a>Nabídka Enterprise pro vývoj/testování

Jako podnikový správce Azure můžete vlastníkům účtů ve vaší organizaci umožnit vytvářet předplatná na základě nabídky EA pro vývoj/testování. Můžete to provést zaškrtnutím políčka Vývoj/testování u příslušného vlastníka účtu na portálu Azure EA Portal.

Po zaškrtnutí políčka Vývoj/testování informujte daného vlastníka účtu, aby mohl nastavit potřebná předplatná EA pro vývoj/testování pro týmy předplatitelů nabídky Vývoj/testování.

Tato nabídka umožňuje aktivním předplatitelům sady Visual Studio spouštět v Azure vývojové a testovací úlohy za speciální sazby pro vývoj/testování. Poskytuje přístup k celé galerii imagí pro vývoj/testování, včetně imagí Windows 8.1 a Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Nastavení nabídky Enterprise pro vývoj/testování:

1. Přihlaste se jako podnikový správce.
1. V levém navigačním panelu vyberte **Manage** (Spravovat).
1. Vyberte kartu **Account** (Účet).
1. Vyberte řádek účtu, kterému chcete povolit přístup k nabídce Vývoj/testování.
1. Vyberte symbol tužky napravo od řádku.
1. Zaškrtněte políčko Vývoj/testování.
1. Vyberte **Uložit**.

Když se uživatel přidá jako vlastník účtu přes portál Azure EA, všechna předplatná Azure na základě nabídky Vývoj/testování s průběžnými platbami nebo nabídek měsíčního kreditu pro předplatitele sady Visual Studio přidružená k danému vlastníkovi účtu se převedou na nabídku EA pro vývoj/testování. Předplatná na základě jiných typů nabídek, jako jsou nabídky průběžných plateb, přidružená k danému vlastníkovi účtu se převedou na nabídky Microsoft Azure Enterprise.

Nabídka Vývoj/testování se v současné době nevztahuje na zákazníky Azure Government.

## <a name="delete-subscription"></a>Odstranění předplatného

Odstranění předplatného, ve kterém jste vlastníkem účtu:

1. Přihlaste se k webu Azure Portal pomocí přihlašovacích údajů přidružených k vašemu účtu.
1. V nabídce centra vyberte **Předplatná**.
1. Na kartě Předplatná v levém horním rohu stránky vyberte předplatné, které chcete zrušit, a výběrem možnosti **Zrušit předplatné** otevřete kartu Zrušení.
1. Zadejte název předplatného, zvolte důvod pro zrušení a vyberte tlačítko **Zrušit předplatné**.

Předplatná můžou rušit pouze správci účtů.

Další informace najdete v části [Co se stane po zrušení předplatného?](cancel-azure-subscription.md#what-happens-after-i-cancel-my-subscription).

## <a name="delete-an-account"></a>Odstranění účtu

Odebrat je možné pouze aktivní účty bez aktivních předplatných.

1. V levém navigačním panelu portálu Enterprise Portal vyberte **Spravovat**.
1. Vyberte kartu **Account** (Účet).
1. V tabulce Účty vyberte účet, který chcete odstranit.
1. Vyberte symbol X na pravé straně řádku účtu.
1. Jakmile účet nebude obsahovat žádná aktivní předplatná, výběrem možnosti **Ano** pod řádkem účtu potvrďte odebrání účtu.

## <a name="update-notification-settings"></a>Aktualizace nastavení oznámení

Registrace podnikových správců probíhá automaticky, aby mohli dostávat oznámení o použití, která se týkají jejich registrace. Každý podnikový správce může změnit interval jednotlivých oznámení nebo může tato oznámení úplně vypnout.

Kontakty pro oznámení se na portálu Azure EA zobrazují v oddílu **Kontakt pro oznámení**. Správou kontaktů pro oznámení zajistíte doručování oznámení Azure EA v organizaci těm správným lidem.

Zobrazení aktuálního nastavení oznámení:

1. Na portálu Azure EA přejděte na **Správa** > **Kontakt pro oznámení**.
2. E-mailová adresa – e-mailová adresa přidružená k účtu Microsoft podnikového správce nebo k pracovnímu či školnímu účtu, na který chodí oznámení.
3. Frekvence oznamování nefakturovaného zůstatku – nastavená frekvence posílání oznámení každému podnikovému správci.

Přidání kontaktu:

1. Vyberte **+ Přidat kontakt**.
2. Zadejte e-mailovou adresu a potvrďte ji.
3. Vyberte **Uložit**.

Nový kontakt pro oznámení se zobrazí v oddílu **Kontakt pro oznámení**. Pokud chcete změnit frekvenci oznamování, vyberte kontakt pro oznámení a pak vyberte symbol tužky, který je napravo od vybraného řádku. Nastavte **denní**, **týdenní**, **měsíční** nebo **žádnou** frekvenci.

Můžete také potlačit _blížící se koncové datum pokrytého období_ a _vypnout a zrušit zřízené blížící se datum_ u oznámení o životním cyklu. Pokud vypnete oznámení o životním cyklu, potlačíte tím oznámení o pokrytém období a o datu ukončení smlouvy.

## <a name="azure-sponsorship-offer"></a>Nabídka Azure Sponsorship

Nabídka Azure Sponsorship se týká omezeného sponzorovaného účtu Microsoft Azure. Nabídka je k dispozici jenom na základě e-mailové pozvánky určené omezenému počtu zákazníků, které vybral Microsoft. Pokud máte na nabídku Microsoft Azure Sponsorship nárok, přijde vám e-mailem pozvánka určená pro vaše ID účtu.

Další informace získáte vytvořením [žádosti o podporu při aktivaci nabídky Sponsorship](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>Převod na ověřování pomocí pracovního nebo školního účtu

Podnikoví uživatelé Azure můžou převést typ ověřování z ověřování pomocí účtu Microsoft (MSA nebo Live ID) na ověřování pomocí pracovního nebo školního účtu (s využitím Active Directory v Azure).

Začněte následovně:

1. Na portálu Azure EA přidejte pracovní nebo školní účet do požadovaných rolí.
1. Pokud dojde k chybám, účet možná není v Active Directory platný.  Azure používá hlavní název uživatele (UPN), který není vždy stejný jako e-mailová adresa.
1. Ověřte se na portálu Azure EA pomocí pracovního nebo školního účtu.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Převod předplatných z účtů Microsoft na pracovní nebo školní účty:

1. Přihlaste se k portálu pro správu pomocí účtu Microsoft, který je vlastníkem předplatných.
1. Pomocí převodu vlastnictví účtu proveďte přesun na nový účet.
1. Účet Microsoft by teď neměl obsahovat žádná aktivní předplatná a můžete ho odstranit.
1. Všechny odstraněné účty se kvůli zachování historie fakturace budou na portálu dál zobrazovat v neaktivním stavu.  Zaškrtnutím políčka je můžete vyfiltrovat a zobrazit pouze aktivní účty.

## <a name="account-subscription-ownership-faq"></a>Nejčastější dotazy k vlastnictví předplatného účtu

V tomto dokumentu najdete odpovědi na nejčastější dotazy související s vlastnictvím předplatného účtu.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Kolik vlastníků účtů Azure je možné mít pro jedno předplatné?

V jednom předplatném je povolený pouze jeden vlastník účtu.  Další role je možné přidat pomocí řízení přístupu na základě role v Azure (Azure RBAC) na kartě předplatného v levém horním rohu stránky na webu[Azure Portal](https://portal.azure.com).

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Může být vlastník účtu Azure uvedený u více než jednoho oddělení?

Ne, vlastník účtu může být přidružený pouze k jednomu oddělení. Tato zásada pomáhá zajistit přesné monitorování a rozdělování útraty a nákladů přidružených k odpovídajícímu oddělení v rámci registrace EA na portálu Azure EA Portal.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Může být vlastník účtu Azure uvedený jako skupina zabezpečení?

Ne, vlastníkem předplatného musí být jedinečný účet Microsoft (MSA) nebo ověřování Azure Active Directory (AAD). Pokud chcete v rámci organizace zohlednit nástupnictví, můžete zvážit vytvoření obecných účtů a použití AAD ke správě přístupu k předplatným.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Může jeden uživatel vlastnit více předplatných?

Vlastník účtu Azure může vytvářet a spravovat neomezené množství předplatných.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Jak můžu zobrazit všechna předplatná organizace nebo k nim získat přístup?

V současné době je potřeba k tomu použít zásady. To znamená, že byste museli vyžadovat, aby se pro každé vytvořené předplatné váš účet pomocí řízení přístupu na základě role v Azure (Azure RBAC) přidal do role předplatného.

### <a name="where-do-i-go-to-create-a-subscription"></a>Kde můžu vytvořit předplatné?

Abyste mohli vytvořit předplatné nabídky Enterprise Azure (EA), správce vaší registrace EA musí na portálu Azure EA Portal přidat váš účet do role vlastníka účtu. Pak se musíte přihlásit k portálu Azure EA Portal a získat oprávnění k vytváření předplatných nabídek EA. První předplatné EA doporučujeme vytvořit pomocí odkazu + Přidat předplatné na kartě předplatného na portálu EA Portal.  Jakmile však váš účet získá oprávnění, může být snadnější vytvářet předplatná na webu portal.azure.com na kartě předplatného v levém horním rohu stránky, kde můžete předplatné v jednom kroku vytvořit i přejmenovat.

### <a name="who-can-create-a-subscription"></a>Kdo může vytvořit předplatné?

Abyste mohli vytvořit předplatné nabídky Enterprise Azure, musíte na [portálu EA](https://ea.azure.com) mít oprávnění role vlastníka účtu.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak vám můžou [rezervace virtuálních počítačů](ea-portal-vm-reservations.md) pomoci ušetřit peníze.
- Pokud potřebujete pomoc při řešení potíží s portálem Azure EA, přečtěte si téma [Řešení potíží s přístupem k portálu Azure EA](ea-portal-troubleshoot.md).