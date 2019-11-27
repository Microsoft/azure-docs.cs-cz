---
title: Přiřazení přístupu k datům Azure Cost Management
description: Tento článek vás provede, když přiřadíte oprávnění k datům Azure Cost Management pro různé obory přístupu.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: e3140ee990127db6815828314103a09dff7cf26e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219849"
---
# <a name="assign-access-to-cost-management-data"></a>Přiřazení přístupu k datům služby Cost Management

Pro uživatele se smlouvou Azure Enterprise je kombinací oprávnění udělených v Azure Portal a portálu Enterprise (EA) definována úroveň přístupu uživatelů k datům Azure Cost Management. Pro uživatele s jinými typy účtů Azure je snazší definovat úroveň přístupu uživatelů k Cost Management dat pomocí řízení přístupu na základě role Azure. Tento článek vás provede přiřazuje se přístup k datům služby Cost Management. Po přiřazení kombinace oprávnění na základě zobrazení dat uživatele ve službě Cost Management oboru, aby měli přístup k a v oboru, vyberte na portálu Azure portal.

Obor, který uživatel vybere se používá v celém Cost Management k poskytování konsolidace dat a k řízení přístupu na informace o nákladech. Při použití rozsahů, uživatelé není vícenásobný výběr je. Místo toho vyberte větší rozsah, který až vrátit podřízených oborech a pak se filtr seznamu na to, co chtějí zobrazit. Konsolidace dat je důležité pochopit, protože někteří uživatelé neměli mít přístup, který podřízené obory vrátit až nadřazený obor.

Podívejte se na [Postup přiřazení přístupu k Azure cost management](https://www.youtube.com/watch?v=J997ckmwTa8) videu, kde se dozvíte, jak přiřazovat přístup k nákladům na zobrazení a poplatkům pomocí řízení přístupu na základě role Azure.

>[!VIDEO https://www.youtube.com/embed/J997ckmwTa8]

## <a name="cost-management-scopes"></a>Cost Management obory

Správa nákladů podporuje nejrůznější typy účtů Azure. Úplný seznam podporovaných typů účtů najdete v tématu [pochopení cost management dat](understand-cost-mgt-data.md). Typ účtu určuje dostupné obory.

### <a name="azure-ea-subscription-scopes"></a>Obory předplatného Azure EA

Chcete-li zobrazit nákladová data pro předplatná Azure EA, musí mít uživatel alespoň přístup pro čtení k jednomu nebo více následujícím rozsahům.

| **Rozsah** | **Definovaný na** | **Požadovaný přístup k zobrazení dat** | **Požadované nastavení EA** | **Konsoliduje data do** |
| --- | --- | --- | --- | --- |
| Fakturační účet<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Podnikový správce | Žádný | Všechna předplatná ze smlouvy Enterprise |
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

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Povolení přístupu k náklady na portálu EA

Obor oddělení vyžaduje, aby byla na portálu EA **povolená** možnost **Zobrazit poplatky za da** . U všech ostatních oborů je na portálu EA **povolená** možnost **poplatky za zobrazení Ao** .

Pokud chcete povolit možnost:

1. Přihlaste se k portálu EA na [https://ea.azure.com](https://ea.azure.com) s účtem správce rozlehlé sítě.
2. V levém podokně vyberte **Spravovat** .
3. U oborů služby cost management, ke kterým chcete poskytnout přístup, povolte možnost poplatky za položku **náklady na zobrazení da** a **poplatky za zobrazení Ao**.  
    karta registrace ![zobrazuje možnosti nákladů na zobrazení DA a AO](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

Po povolení možnosti zobrazení poplatek se většina oborů také vyžadovat konfigurace oprávnění řízení přístupu na základě role na webu Azure Portal.

## <a name="enterprise-administrator-role"></a>Role správce rozlehlé sítě

Ve výchozím nastavení správce podnikové sítě má přístup k fakturačnímu účtu (smlouva Enterprise pro zápis) a všechny ostatní obory, které jsou podřízené obory. Podnikový správce přiřadí přístup obory pro ostatní uživatele. Jako osvědčený postup zajišťuje nepřetržitý chod podniků byste měli mít vždy dva uživatele s přístupem správce organizace. Následující části jsou příklady názorném postupu přiřazení přístupu správce organizace do oborů pro ostatní uživatele.

## <a name="assign-billing-account-scope-access"></a>Přiřazení přístupu oboru fakturační účet

Přístup k oboru fakturační účet vyžaduje oprávnění správce podniku na portálu EA. Správce rozlehlé sítě má přístup k zobrazení náklady napříč celou registraci smlouvy Enterprise nebo více registrací. Na webu Azure Portal pro obor fakturační účet nevyžaduje žádná akce.

1. Přihlaste se k portálu EA na [https://ea.azure.com](https://ea.azure.com) s účtem správce rozlehlé sítě.
2. V levém podokně vyberte **Spravovat** .
3. Na kartě **zápis** vyberte registraci, kterou chcete spravovat.  
    ![vyberte registraci na portálu EA](./media/assign-access-acm-data/ea-portal.png)
4. Klikněte na **+ přidat správce**.
5. V dialogovém okně Přidat správce vyberte typ ověřování a zadejte e-mailovou adresu uživatele.
6. Pokud má uživatel přístup jen pro čtení k datům o nákladech a využití, vyberte v části **jen pro čtení**možnost **Ano**.  V opačném případě vyberte možnost **ne**.
7. Kliknutím na **Přidat** vytvořte účet.  
    ![příklady informací zobrazených v poli přidat správce](./media/assign-access-acm-data/add-admin.png)

Může trvat až 30 minut, než se nový uživatel může přístup k datům ve službě Cost Management.

### <a name="assign-department-scope-access"></a>Přiřazení oddělení obor přístupu

Přístup k oboru oddělení vyžaduje oddělení správce (DA zobrazit náklady) přístupu přes portál EA. Správce oddělení má přístup k zobrazení související s oddělení nebo několika oddělení data o využití a nákladů. Data pro oddělení zahrnují všechna předplatná, které patří k účtu pro zápis, které jsou spojeny s oddělení. Na webu Azure Portal nevyžaduje žádná akce.

1. Přihlaste se k portálu EA na [https://ea.azure.com](https://ea.azure.com) s účtem správce rozlehlé sítě.
2. V levém podokně vyberte **Spravovat** .
3. Na kartě **zápis** vyberte registraci, kterou chcete spravovat.
4. Klikněte na kartu **oddělení** a pak klikněte na **přidat správce**.
5. V dialogovém okně Přidat Správce oddělení vyberte typ ověřování a pak zadejte e-mailovou adresu uživatele.
6. Pokud má uživatel přístup jen pro čtení k datům o nákladech a využití, vyberte v části **jen pro čtení**možnost **Ano**.  V opačném případě vyberte možnost **ne**.
7. Vyberte jako vodítko použijte oddělení, které chcete udělit oprávnění pro správu oddělení.
8. Kliknutím na **Přidat** vytvořte účet.  
    ![zadejte požadované informace v poli přidat správce oddělení](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Přiřadit přístup k oboru registraci účtu

Přístup k rozsahu registrace účtu vyžaduje (AO zobrazit náklady) přístup vlastníka účtu na portálu EA. Vlastník účtu můžete zobrazit přidružené k předplatným vytvořené z daného účtu registrace data o využití a nákladů. Na webu Azure Portal nevyžaduje žádná akce.

1. Přihlaste se k portálu EA na [https://ea.azure.com](https://ea.azure.com) s účtem správce rozlehlé sítě.
2. V levém podokně vyberte **Spravovat** .
3. Na kartě **zápis** vyberte registraci, kterou chcete spravovat.
4. Klikněte na kartu **účet** a pak klikněte na **Přidat účet**.
5. V poli Přidat účet vyberte **oddělení** , ke kterému chcete účet přidružit, nebo ho nechte nepřiřazený.
6. Vyberte typ ověřování a zadejte název účtu.
7. Zadejte e-mailovou adresu uživatele a volitelně zadejte nákladové středisko.
8. Kliknutím na **Přidat** vytvořte účet.  
    ![do pole přidat účet pro účet pro zápis zadejte požadované informace](./media/assign-access-acm-data/add-account.png)

Po dokončení výše uvedených kroků, uživatelský účet stane registraci účtu na webu Enterprise Portal a můžete vytvářet odběry. Má uživatel přístup data o využití a nákladů pro předplatné, které vytvářejí.

## <a name="assign-management-group-scope-access"></a>Přiřazení přístupu obor skupiny správy

Přístup k zobrazení rozsahu skupiny pro správu vyžaduje alespoň oprávnění čtenář (nebo čtenář) Cost Management. Na webu Azure Portal můžete nakonfigurovat oprávnění pro skupinu pro správu. Musíte mít alespoň oprávnění správce přístupu uživatelů (nebo vlastník) pro skupinu pro správu umožňující přístup pro ostatní uživatele. A v případě účtů EA Azure je také nutné povolit nastavení **nákladů na zobrazení Ao** na portálu EA.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na bočním panelu vyberte **všechny služby** , vyhledejte _skupiny pro správu_a pak vyberte **skupiny pro správu**.
3. Vyberte skupiny pro správu v hierarchii.
4. Vedle názvu skupiny pro správu klikněte na tlačítko **Podrobnosti**.
5. V levém podokně vyberte **Access Control (IAM)** .
6. Klikněte na **Přidat**.
7. V části **role**vyberte **cost management Reader**.
8. V části **přiřadit přístup k**vyberte možnost **uživatel, skupina nebo aplikace služby Azure AD**.
9. K přiřazení přístupu, vyhledejte a vyberte uživatele.
10. Klikněte na možnost **Uložit**.  
    ![ukázkové informace v poli Přidat oprávnění pro skupinu pro správu](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Přiřazení předplatného obor přístupu

Přístup k předplatnému vyžaduje alespoň oprávnění Čtenář náklady na správu (nebo Čtenář). Na webu Azure Portal můžete nakonfigurovat oprávnění k předplatnému. Musíte mít alespoň oprávnění správce přístupu uživatelů (nebo vlastník) pro předplatné, které chcete povolit přístup pro ostatní uživatele. A v případě účtů EA Azure je také nutné povolit nastavení **nákladů na zobrazení Ao** na portálu EA.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na bočním panelu vyberte **všechny služby** , vyhledejte _předplatná_a pak vyberte **předplatná**.
3. Vyberte své předplatné.
4. V levém podokně vyberte **Access Control (IAM)** .
5. Klikněte na **Přidat**.
6. V části **role**vyberte **cost management Reader**.
7. V části **přiřadit přístup k**vyberte možnost **uživatel, skupina nebo aplikace služby Azure AD**.
8. K přiřazení přístupu, vyhledejte a vyberte uživatele.
9. Klikněte na možnost **Uložit**.

## <a name="assign-resource-group-scope-access"></a>Přiřazení přístupu oboru skupiny prostředků

Přístup do skupiny prostředků vyžaduje alespoň oprávnění Čtenář náklady na správu (nebo Čtenář). Na webu Azure Portal můžete nakonfigurovat oprávnění pro skupinu prostředků. Musíte mít alespoň oprávnění správce přístupu uživatelů (nebo vlastník) pro skupinu prostředků pro povolení přístupu pro ostatní uživatele. A v případě účtů EA Azure je také nutné povolit nastavení **nákladů na zobrazení Ao** na portálu EA.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na bočním panelu vyberte **všechny služby** , vyhledejte _skupiny prostředků_a pak vyberte **skupiny prostředků**.
3. Vyberte skupinu prostředků.
4. V levém podokně vyberte **Access Control (IAM)** .
5. Klikněte na **Přidat**.
6. V části **role**vyberte **cost management Reader**.
7. V části **přiřadit přístup k**vyberte možnost **uživatel, skupina nebo aplikace služby Azure AD**.
8. K přiřazení přístupu, vyhledejte a vyberte uživatele.
9. Klikněte na možnost **Uložit**.

## <a name="cross-tenant-authentication-issues"></a>Problémy s ověřováním mezi klienty

V současné době má Azure Cost Management omezená podpora pro ověřování mezi klienty. V některých případech se při pokusu o ověření napříč klienty může při analýze nákladů zobrazit chyba **odepření přístupu** . K tomuto problému může dojít, pokud nakonfigurujete řízení přístupu na základě role (RBAC) na předplatné jiného tenanta a pak se pokusíte zobrazit data o nákladech.

Řešení *problému*: po nakonfigurování RBAC mezi klienty můžete počkat hodinu. Pak se pokuste zobrazit náklady v analýze nákladů nebo udělit uživatelům přístup k Cost Managementům v obou klientech.  


## <a name="next-steps"></a>Další kroky

- Pokud jste ještě nedokončili první rychlý Start pro Cost Management, přečtěte si ho v části [zahájení analýzy nákladů](quick-acm-cost-analysis.md).
