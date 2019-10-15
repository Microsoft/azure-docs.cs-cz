---
title: Přiřazení přístupu k datům Azure Cost Management
description: Tento článek vás provede přiřazením oprávnění k Azure Cost Management dat pro různé obory přístupu.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: ebc56d27b7adc8f1fea9eafabe1b211f3f0ad560
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375134"
---
# <a name="assign-access-to-cost-management-data"></a>Přiřazení přístupu k datům Cost Management

Pro uživatele se smlouvou Azure Enterprise je kombinací oprávnění udělených v Azure Portal a portálu Enterprise (EA) definována úroveň přístupu uživatelů k datům Azure Cost Management. Pro uživatele s jinými typy účtů Azure je snazší definovat úroveň přístupu uživatelů k Cost Management dat pomocí řízení přístupu na základě role Azure. Tento článek vás provede procesem přiřazování přístupu k Cost Management datům. Po přiřazení kombinace oprávnění uživatel zobrazí data v Cost Management na základě oboru, ke kterému mají přístup, a v oboru, který vybírá v Azure Portal.

Rozsah, který uživatel vybere, se používá v celém Cost Management k zajištění konsolidace dat a řízení přístupu k informacím o nákladech. Když uživatelé používají obory, nevybírejte je vícenásobně. Místo toho si vyberou větší rozsah, ve kterém jsou podřízené obory zahrnuté, a pak se vyfiltrují podle toho, co chtějí zobrazit. Pro pochopení dat je důležité pochopit, že někteří lidé by neměli mít přístup k nadřazenému oboru, na který se nacházejí podřízené obory.

Podívejte se na [Postup přiřazení přístupu k Azure cost management](https://www.youtube.com/watch?v=J997ckmwTa8) videu, kde se dozvíte, jak přiřazovat přístup k nákladům na zobrazení a poplatkům pomocí řízení přístupu na základě role Azure.

>[!VIDEO https://www.youtube.com/embed/J997ckmwTa8]

## <a name="cost-management-scopes"></a>Obory Cost Management

Správa nákladů podporuje nejrůznější typy účtů Azure. Úplný seznam podporovaných typů účtů najdete v tématu [pochopení cost management dat](understand-cost-mgt-data.md). Typ účtu určuje dostupné obory.

### <a name="azure-ea-subscription-scopes"></a>Obory předplatného Azure EA

Chcete-li zobrazit nákladová data pro předplatná Azure EA, musí mít uživatel alespoň přístup pro čtení k jednomu nebo více následujícím rozsahům.

| **Rozsah** | **Definovaný na** | **Požadovaný přístup k zobrazení dat** | **Požadované nastavení EA** | **Konsoliduje data do** |
| --- | --- | --- | --- | --- |
| Fakturační účet<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Podnikový správce | Žádné | Všechna předplatná ze smlouvy Enterprise |
| Oddělení | [https://ea.azure.com](https://ea.azure.com/) | Správce oddělení | Povolené **poplatky za zobrazení da** | Všechna předplatná patřící do registračního účtu propojeného s oddělením |
| Registrační účet<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Vlastník účtu | Povolené **poplatky za zobrazení Ao** | Všechna předplatná z registračního účtu |
| Skupina pro správu | [https://portal.azure.com](https://portal.azure.com/) | Čtenář Cost Management (nebo Čtenář) | Povolené **poplatky za zobrazení Ao** | Všechna předplatná spadající do skupiny pro správu |
| Předplatné | [https://portal.azure.com](https://portal.azure.com/) | Čtenář Cost Management (nebo Čtenář) | Povolené **poplatky za zobrazení Ao** | Všechny prostředky nebo skupiny prostředků v předplatném |
| Skupina prostředků | [https://portal.azure.com](https://portal.azure.com/) | Čtenář Cost Management (nebo Čtenář) | Povolené **poplatky za zobrazení Ao** | Všechny prostředky ve skupině prostředků |

<sup>1</sup> fakturační účet se také označuje jako smlouva Enterprise nebo registrace.

<sup>2</sup> účet pro zápis se označuje také jako vlastník účtu.

Následující diagram znázorňuje vztah mezi Cost Management obory s rolemi a nastavením portálu EA.

![Diagram znázorňující vztah mezi Cost Management obory s rolemi a nastavením portálu EA](./media/assign-access-acm-data/scope-access-relationship-diagram.png)

Pokud jsou **poplatky za zobrazení da** na portálu EA zakázané, zobrazí se při pokusu o zobrazení nákladů na oddělení a účty zpráva informující *o nákladech, které jsou pro vaši organizaci zakázané* .

Podobně platí, že když se na portálu EA zakáže **poplatky za zobrazení Ao** , zobrazí se vám při pokusu o zobrazení nákladů na účty pro zápis, skupiny pro správu, předplatná a skupiny prostředků zpráva s informacemi o tom, že jsou *pro vaši organizaci zakázané náklady* .

## <a name="other-azure-account-scopes"></a>Další obory účtů Azure

Chcete-li zobrazit nákladová data pro ostatní předplatná Azure, musí mít uživatel alespoň přístup pro čtení k jednomu nebo více následujícím rozsahům:

- Účet Azure
- Skupina pro správu
- Skupina prostředků

Po připojení zákazníků k zákaznickým smlouvám Microsoftu jsou k dispozici různé obory. Zákazníci CSP potom můžou používat funkce Cost Management, když je povolí jejich partner CSP. Další informace najdete v tématu [Začínáme s Azure cost management pro partnery](get-started-partners.md).

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Povolení přístupu k nákladům na portálu EA

Obor oddělení vyžaduje, aby byla na portálu EA **povolená** možnost **Zobrazit poplatky za da** . U všech ostatních oborů je na portálu EA **povolená** možnost **poplatky za zobrazení Ao** .

Povolení možnosti:

1. Přihlaste se k portálu EA na adrese [https://ea.azure.com](https://ea.azure.com) s účtem správce rozlehlé sítě.
2. V levém podokně vyberte **Spravovat** .
3. U oborů služby cost management, ke kterým chcete poskytnout přístup, povolte možnost poplatky za položku **náklady na zobrazení da** a **poplatky za zobrazení Ao**.  
    karta ![Enrollment ukazující možnosti nákladů na zobrazení DA a AO @ no__t-1

Po povolení možností zobrazení poplatků bude většina oborů vyžadovat konfiguraci oprávnění řízení přístupu na základě role (RBAC) v Azure Portal.

## <a name="enterprise-administrator-role"></a>Role podnikového správce

Ve výchozím nastavení má podnikový správce přístup k fakturačnímu účtu (smlouva Enterprise/registraci) a ke všem ostatním oborům, které jsou podřízenými obory. Podnikový správce přiřadí přístup k oborům pro ostatní uživatele. Osvědčeným postupem pro provozní kontinuitu je, že byste měli mít vždycky dva uživatele s přístupem Enterprise Administrators. V následujících částech najdete příklady ukázkových podnikových správců, kteří přiřazují přístup k oborům pro jiné uživatele.

## <a name="assign-billing-account-scope-access"></a>Přiřazení přístupu k oboru fakturačního účtu

Přístup k rozsahu fakturačního účtu vyžaduje oprávnění Enterprise Administrators na portálu EA. Podnikový správce má přístup k zobrazení nákladů napříč celým zápisem EA nebo několika zápisy. V Azure Portal pro obor fakturačního účtu není nutná žádná akce.

1. Přihlaste se k portálu EA na adrese [https://ea.azure.com](https://ea.azure.com) s účtem správce rozlehlé sítě.
2. V levém podokně vyberte **Spravovat** .
3. Na kartě **zápis** vyberte registraci, kterou chcete spravovat.  
    @no__t 0select registraci na portálu EA @ no__t-1
4. Klikněte na **+ přidat správce**.
5. V poli přidat správce vyberte typ ověřování a zadejte e-mailovou adresu uživatele.
6. Pokud má uživatel přístup jen pro čtení k datům o nákladech a využití, vyberte v části **jen pro čtení**možnost **Ano**.  V opačném případě vyberte možnost **ne**.
7. Kliknutím na **Přidat** vytvořte účet.  
    informace ![example, které jsou uvedeny v poli přidat správce @ no__t-1

Může trvat až 30 minut, než nový uživatel bude moci získat přístup k datům v Cost Management.

### <a name="assign-department-scope-access"></a>Přiřazení přístupu k oboru oddělení

Přístup k oboru oddělení vyžaduje přístup správce oddělení (poplatky za zobrazení DA) na portálu EA. Správce oddělení má přístup k zobrazení nákladů a dat o využití spojených s oddělením nebo s více odděleními. Data pro oddělení zahrnují všechna předplatná patřící k účtu registrace, který je spojený s oddělením. V Azure Portal není nutná žádná akce.

1. Přihlaste se k portálu EA na adrese [https://ea.azure.com](https://ea.azure.com) s účtem správce rozlehlé sítě.
2. V levém podokně vyberte **Spravovat** .
3. Na kartě **zápis** vyberte registraci, kterou chcete spravovat.
4. Klikněte na kartu **oddělení** a pak klikněte na **přidat správce**.
5. V poli přidat správce oddělení vyberte typ ověřování a zadejte e-mailovou adresu uživatele.
6. Pokud má uživatel přístup jen pro čtení k datům o nákladech a využití, vyberte v části **jen pro čtení**možnost **Ano**.  V opačném případě vyberte možnost **ne**.
7. Vyberte oddělení, kterým chcete udělit oprávnění správce oddělení.
8. Kliknutím na **Přidat** vytvořte účet.  
    @no__t – 0enter požadované informace v poli přidat správce oddělení @ no__t-1

## <a name="assign-enrollment-account-scope-access"></a>Přiřazení přístupu k oboru účtu registrace

Přístup k oboru účtu registrace vyžaduje přístup vlastníka účtu (poplatky za AO) na portálu EA. Vlastník účtu může zobrazit náklady a data o využití související s předplatnými vytvořenými z daného účtu pro zápis. V Azure Portal není nutná žádná akce.

1. Přihlaste se k portálu EA na adrese [https://ea.azure.com](https://ea.azure.com) s účtem správce rozlehlé sítě.
2. V levém podokně vyberte **Spravovat** .
3. Na kartě **zápis** vyberte registraci, kterou chcete spravovat.
4. Klikněte na kartu **účet** a pak klikněte na **Přidat účet**.
5. V poli Přidat účet vyberte **oddělení** , ke kterému chcete účet přidružit, nebo ho nechte nepřiřazený.
6. Vyberte typ ověřování a zadejte název účtu.
7. Zadejte e-mailovou adresu uživatele a volitelně zadejte nákladové středisko.
8. Kliknutím na **Přidat** vytvořte účet.  
    @no__t – 0enter požadované informace v poli Přidat účet pro účet pro zápis @ no__t-1

Po dokončení výše uvedeného postupu se uživatelský účet bude účtem pro zápis na portálu Enterprise a může vytvářet předplatná. Uživatel má přístup k datům o nákladech a využití pro předplatná, která vytvoří.

## <a name="assign-management-group-scope-access"></a>Přiřazení přístupu k oboru skupiny pro správu

Přístup k zobrazení rozsahu skupiny pro správu vyžaduje alespoň oprávnění čtenář (nebo čtenář) Cost Management. Můžete nakonfigurovat oprávnění pro skupinu pro správu v Azure Portal. Aby bylo možné povolit přístup ostatním uživatelům, musíte mít alespoň oprávnění správce přístupu (nebo vlastník) pro skupinu pro správu. A v případě účtů EA Azure je také nutné povolit nastavení **nákladů na zobrazení Ao** na portálu EA.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na bočním panelu vyberte **všechny služby** , vyhledejte _skupiny pro správu_a pak vyberte **skupiny pro správu**.
3. Vyberte skupinu pro správu v hierarchii.
4. Vedle názvu skupiny pro správu klikněte na tlačítko **Podrobnosti**.
5. V levém podokně vyberte **Access Control (IAM)** .
6. Klikněte na tlačítko **Přidat**.
7. V části **role**vyberte **cost management Reader**.
8. V části **přiřadit přístup k**vyberte možnost **uživatel, skupina nebo aplikace služby Azure AD**.
9. Chcete-li přiřadit přístup, vyhledejte a vyberte uživatele.
10. Klikněte na **Uložit**.  
    @no__t 0example informace v poli Přidat oprávnění pro skupinu pro správu @ no__t-1

## <a name="assign-subscription-scope-access"></a>Přiřazení přístupu k oboru předplatného

Přístup k předplatnému vyžaduje alespoň oprávnění čtenář (nebo čtenář) Cost Management. Můžete nakonfigurovat oprávnění k předplatnému v Azure Portal. Abyste mohli povolit přístup ostatním uživatelům, musíte mít aspoň oprávnění správce přístupu (nebo vlastník) pro toto předplatné. A v případě účtů EA Azure je také nutné povolit nastavení **nákladů na zobrazení Ao** na portálu EA.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na bočním panelu vyberte **všechny služby** , vyhledejte _předplatná_a pak vyberte **předplatná**.
3. Vyberte své předplatné.
4. V levém podokně vyberte **Access Control (IAM)** .
5. Klikněte na tlačítko **Přidat**.
6. V části **role**vyberte **cost management Reader**.
7. V části **přiřadit přístup k**vyberte možnost **uživatel, skupina nebo aplikace služby Azure AD**.
8. Chcete-li přiřadit přístup, vyhledejte a vyberte uživatele.
9. Klikněte na **Uložit**.

## <a name="assign-resource-group-scope-access"></a>Přiřazení přístupu k oboru skupiny prostředků

Přístup ke skupině prostředků vyžaduje aspoň oprávnění čtenář Cost Management (nebo čtenář). Oprávnění můžete nakonfigurovat pro skupinu prostředků v Azure Portal. Aby bylo možné povolit přístup pro ostatní uživatele, musíte mít alespoň oprávnění správce přístupu (nebo vlastník) pro skupinu prostředků. A v případě účtů EA Azure je také nutné povolit nastavení **nákladů na zobrazení Ao** na portálu EA.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na bočním panelu vyberte **všechny služby** , vyhledejte _skupiny prostředků_a pak vyberte **skupiny prostředků**.
3. Vyberte skupinu prostředků.
4. V levém podokně vyberte **Access Control (IAM)** .
5. Klikněte na tlačítko **Přidat**.
6. V části **role**vyberte **cost management Reader**.
7. V části **přiřadit přístup k**vyberte možnost **uživatel, skupina nebo aplikace služby Azure AD**.
8. Chcete-li přiřadit přístup, vyhledejte a vyberte uživatele.
9. Klikněte na **Uložit**.

## <a name="cross-tenant-authentication-issues"></a>Problémy s ověřováním mezi klienty

V současné době má Azure Cost Management omezená podpora pro ověřování mezi klienty. V některých případech se při pokusu o ověření napříč klienty může při analýze nákladů zobrazit chyba **odepření přístupu** . K tomuto problému může dojít, pokud nakonfigurujete řízení přístupu na základě role (RBAC) na předplatné jiného tenanta a pak se pokusíte zobrazit data o nákladech.

Řešení *problému*: po nakonfigurování RBAC mezi klienty můžete počkat hodinu. Pak se pokuste zobrazit náklady v analýze nákladů nebo udělit uživatelům přístup k Cost Managementům v obou klientech.  


## <a name="next-steps"></a>Další kroky

- Pokud jste ještě nedokončili první rychlý Start pro Cost Management, přečtěte si ho v části [zahájení analýzy nákladů](quick-acm-cost-analysis.md).
