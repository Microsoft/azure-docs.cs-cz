---
title: Přiřazení přístupu k datům služby Azure Cost Management
description: Tento článek vás provede přiřazením oprávnění k datům služby Azure Cost Management pro různé rozsahy přístupu.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: secdec18
ms.openlocfilehash: cb54c5f7334120f6cd01ed1704939c5c1a55e7c6
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645291"
---
# <a name="assign-access-to-cost-management-data"></a>Přiřazení přístupu k datům služby Cost Management

Pro uživatele se smlouvou Azure Enterprise je úroveň jejich přístupu k datům Azure Cost Management definována kombinací oprávnění udělených na portálu Azure Portal a na portálu Enterprise (EA). Pro uživatele s jinými typy účtů Azure je snazší definovat úroveň přístupu k datům služby Cost Management pomocí řízení přístupu na základě role v Azure (Azure RBAC). Tento článek vás provede postupem přiřazení přístupu k datům služby Cost Management. Po přiřazení kombinace oprávnění se uživateli zobrazí data ve službě Cost Management na základě rozsahu jeho přístupu a v rozsahu, který si vybere na webu Azure Portal.

Rozsah, který uživatel vybere, se používá v celé službě Cost Management k poskytování konsolidace dat a k řízení přístupu k informacím o nákladech. Uživatelé při použití nevybírají rozsahy vícenásobně. Místo toho si vyberou větší rozsah, který již zahrnuje podřízené rozsahy, a potom vyfiltrují, co chtějí zobrazit. Konsolidace dat je důležitá, protože někteří uživatelé by neměli mít přístup k nadřazenému rozsahu, který zahrnuje data z podřízených rozsahů.

Informace o přiřazení přístupu k zobrazení nákladů a poplatků pomocí řízení přístupu na základě role v Azure (Azure RBAC) najdete ve videu věnovaném [řízení přístupu ve službě Cost Management](https://www.youtube.com/watch?v=_uQzQ9puPyM). Další videa najdete v [kanálu služby Cost Management na YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/_uQzQ9puPyM]

## <a name="cost-management-scopes"></a>Rozsahy služby Cost Management

Služba Cost Management podporuje nejrůznější typy účtů Azure. Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku [Vysvětlení dat služby Cost Management](understand-cost-mgt-data.md). Typ účtu určuje dostupné rozsahy.

### <a name="azure-ea-subscription-scopes"></a>Rozsahy předplatného Azure EA

Aby mohl uživatel zobrazit data nákladů pro předplatná Azure EA, musí mít alespoň přístup pro čtení k nejméně jednomu z následujících rozsahů.

| **Rozsah** | **Definovaný na** | **Požadovaný přístup k zobrazení dat** | **Požadované nastavení EA** | **Úroveň konsolidace dat** |
| --- | --- | --- | --- | --- |
| Fakturační účet<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Podnikový správce | Žádná | Všechna předplatná ze smlouvy Enterprise |
| Oddělení | [https://ea.azure.com](https://ea.azure.com/) | Správce oddělení | Povolené nastavení **DA view charges** (Správce oddělení může zobrazit náklady) | Všechna předplatná patřící do registračního účtu propojeného s oddělením |
| Registrační účet<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Vlastník účtu | Povolené nastavení **AO view charges** (Vlastník účtu může zobrazit náklady) | Všechna předplatná z registračního účtu |
| Skupina pro správu | [https://portal.azure.com](https://portal.azure.com/) | Čtenář Cost Management (nebo přispěvatel) | Povolené nastavení **AO view charges** (Vlastník účtu může zobrazit náklady) | Všechna předplatná spadající do skupiny pro správu |
| Předplatné | [https://portal.azure.com](https://portal.azure.com/) | Čtenář Cost Management (nebo přispěvatel) | Povolené nastavení **AO view charges** (Vlastník účtu může zobrazit náklady) | Všechny prostředky nebo skupiny prostředků v předplatném |
| Skupina prostředků | [https://portal.azure.com](https://portal.azure.com/) | Čtenář Cost Management (nebo přispěvatel) | Povolené nastavení **AO view charges** (Vlastník účtu může zobrazit náklady) | Všechny prostředky ve skupině prostředků |

<sup>1</sup>Fakturační účet se také označuje jako smlouva Enterprise nebo registrace.

<sup>2</sup> Registrační účet s se často označuje jako vlastník účtu.


## <a name="other-azure-account-scopes"></a>Další rozsahy účtů Azure

Aby mohl uživatel zobrazit data nákladů pro další předplatná Azure, musí mít alespoň přístup pro čtení k nejméně jednomu z následujících rozsahů:

- Skupina pro správu
- Předplatné
- Skupina prostředků

Když partneři zařadí zákazníky do Smlouvy se zákazníkem Microsoftu, jsou k dispozici různé rozsahy. Zákazníci CSP pak můžou používat funkce služby Cost Management, pokud jim ji zpřístupní jejich partner CSP. Další informace najdete v článku [Začínáme se službou Azure Cost Management pro partnery](get-started-partners.md).

## <a name="enable-access-to-costs-in-the-azure-portal"></a>Povolení přístupu k nákladům na webu Azure Portal

Rozsah oddělení vyžaduje, aby byla **aktivovaná** možnost **Správci oddělení si můžou zobrazovat poplatky** (DA view charges). Tuto možnost můžete nakonfigurovat na webu Azure Portal nebo EA Portal. Všechny ostatní obory vyžadují, aby byla **aktivovaná** možnost **Vlastníci účtu si můžou zobrazovat poplatky** (AO view charges).

Pokud chcete aktivovat možnost na webu Azure Portal, postupujte takto:

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com pod účtem podnikového správce.
1. Vyberte položku nabídky **Správa nákladů a fakturace**.
1. Vyberte **Rozsahy fakturace**. Zobrazí se seznam dostupných rozsahů fakturace a fakturačních účtů.
1. Ze seznamu dostupných fakturačních účtů vyberte váš **fakturační účet**.
1. V části **Nastavení** vyberte položku nabídky **Zásady** a potom nakonfigurujte požadované nastavení.  
    ![Zásady rozsahu fakturace s možnostmi pro zobrazení poplatků](./media/assign-access-acm-data/azure-portal-policies-view-charges.png)

Po povolení možností zobrazení nákladů bude většina rozsahů na portálu Azure Portal vyžadovat konfiguraci oprávnění řízení přístupu na základě role v Azure (Azure RBAC).

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Povolení přístupu k nákladům na portálu EA Portal

Rozsah oddělení vyžaduje, aby bylo nastavení **DA view charges** (Správce oddělení může zobrazit náklady) na portálu EA Portal **Enabled** (Povoleno). Tuto možnost můžete nakonfigurovat na webu Azure Portal nebo EA Portal. Všechny ostatní rozsahy vyžadují, aby bylo nastavení **AO view charges** (Vlastník účtu může zobrazit náklady) na portálu EA Portal **Enabled** (Povoleno).

Pokud chcete aktivovat možnost na webu EA Portal, postupujte takto:

1. Přihlaste se k portálu EA Portal na [https://ea.azure.com](https://ea.azure.com) pod účtem podnikového správce.
2. V levém podokně vyberte **Manage** (Spravovat).
3. Pro rozsahy řízení nákladů, ke kterým chcete poskytnout přístup, povolte možnost **DA view charges** (Správce oddělení může zobrazit náklady) nebo **AO view charges** (Vlastník účtu může zobrazit náklady).  
    ![Karta registrace s možnostmi zobrazení nákladů správce oddělení a vlastníka účtu](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

Po povolení možností zobrazení nákladů bude většina rozsahů na portálu Azure Portal vyžadovat konfiguraci oprávnění řízení přístupu na základě role v Azure (Azure RBAC).

## <a name="enterprise-administrator-role"></a>Role podnikového správce

Ve výchozím nastavení má podnikový správce přístup k fakturačnímu účtu (smlouva Enterprise / registrace) a ke všem ostatním podřízeným rozsahům. Přístup k rozsahům pro ostatní uživatele přiřadí podnikový správce. Osvědčeným postupem k zajištění provozní kontinuity je, že byste měli mít vždycky dva uživatele s přístupem podnikového správce. V následujících částech najdete názorné příklady podnikových správců, kteří přiřazují přístup k rozsahům pro jiné uživatele.

## <a name="assign-billing-account-scope-access"></a>Přiřazení přístupu k rozsahu fakturačního účtu

Přístup k rozsahu fakturačního účtu vyžaduje oprávnění podnikového správce na portálu EA Portal. Podnikový správce může zobrazovat náklady napříč celou registrací EA nebo několika registracemi. Na portálu Azure Portal není pro rozsah fakturačního účtu nutná žádná akce.

1. Přihlaste se k portálu EA Portal na [https://ea.azure.com](https://ea.azure.com) pod účtem podnikového správce.
2. V levém podokně vyberte **Manage** (Spravovat).
3. Na kartě **Enrollment** (Registrace) vyberte registraci, kterou chcete spravovat.  
    ![Výběr registrace na portálu EA Portal](./media/assign-access-acm-data/ea-portal.png)
4. Vyberte **+ Add Administrator** (Přidat správce).
5. V okně pro přidání správce vyberte typ ověřování a zadejte e-mailovou adresu uživatele.
6. Pokud má uživatel přístup jen pro čtení k datům o nákladech a využití, vyberte v části **Read-only** (Jen pro čtení) možnost **Yes** (Ano).  Jinak vyberte **No** (Ne).
7. Výběrem **Add** (Přidat) vytvořte účet.  
    ![Příklad informací zobrazených v okně pro přidání správce](./media/assign-access-acm-data/add-admin.png)

Může trvat až 30 minut, než bude nový uživatel moct získat přístup k datům ve službě Cost Management.

### <a name="assign-department-scope-access"></a>Přiřazení přístupu k rozsahu oddělení

Přístup k rozsahu oddělení vyžaduje přístup správce oddělení (DA view charges (Správce oddělení může zobrazit náklady)) na portálu EA Portal. Správce oddělení může zobrazovat údaje o nákladech a využití spojené s oddělením nebo s více odděleními. K datům oddělení patří všechna předplatná patřící do registračního účtu, která jsou propojená s oddělením. Na portálu Azure Portal není nutná žádná akce.

1. Přihlaste se k portálu EA Portal na [https://ea.azure.com](https://ea.azure.com) pod účtem podnikového správce.
2. V levém podokně vyberte **Manage** (Spravovat).
3. Na kartě **Enrollment** (Registrace) vyberte registraci, kterou chcete spravovat.
4. Vyberte kartu **Department** (Oddělení) a potom vyberte **Add Administrator** (Přidat správce).
5. V okně pro přidání správce oddělení vyberte typ ověřování a potom zadejte e-mailovou adresu uživatele.
6. Pokud má uživatel přístup jen pro čtení k datům o nákladech a využití, vyberte v části **Read-only** (Jen pro čtení) možnost **Yes** (Ano).  Jinak vyberte **No** (Ne).
7. Vyberte oddělení, kterým chcete udělit oprávnění správce oddělení.
8. Výběrem **Add** (Přidat) vytvořte účet.  
    ![Zadání požadovaných informací do okna pro přidání správce oddělení](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Přiřazení přístupu k rozsahu registračního účtu

Přístup k rozsahu registračního účtu vyžaduje přístup vlastníka účtu (AO view charges (Vlastník účtu může zobrazit náklady)) na portálu EA Portal. Vlastník účtu může zobrazit data o nákladech využití související s předplatnými vytvořenými v rámci daného registračního účtu. Na portálu Azure Portal není nutná žádná akce.

1. Přihlaste se k portálu EA Portal na [https://ea.azure.com](https://ea.azure.com) pod účtem podnikového správce.
2. V levém podokně vyberte **Manage** (Spravovat).
3. Na kartě **Enrollment** (Registrace) vyberte registraci, kterou chcete spravovat.
4. Vyberte kartu **Account** (Účet) a pak vyberte **Add Account** (Přidat účet).
5. V okně přidání účtu vyberte **Department** (Oddělení), ke kterému chcete účet přidružit, nebo ho nechte nepřiřazený.
6. Vyberte typ ověřování a zadejte název účtu.
7. Zadejte e-mailovou adresu uživatele a volitelně zadejte nákladové středisko.
8. Výběrem **Add** (Přidat) vytvořte účet.  
    ![Zadání požadovaných informací do okna pro přidání registračního účtu](./media/assign-access-acm-data/add-account.png)

Po dokončení výše uvedeného postupu se uživatelský účet stane registračním účtem na portálu Enterprise Portal a může vytvářet předplatná. Uživatel má přístup k datům o nákladech a využití pro předplatná, která vytvoří.

## <a name="assign-management-group-scope-access"></a>Přiřazení přístupu k rozsahu skupiny pro správu

Přístup k zobrazení rozsahu skupiny pro správu vyžaduje alespoň oprávnění Čtenář Cost Management (nebo Čtenář). Oprávnění pro skupinu pro správu můžete nakonfigurovat na portálu Azure Portal. Abyste mohli povolit přístup ostatním uživatelům, musíte mít pro skupinu pro správu alespoň oprávnění Správce uživatelských přístupů (nebo Vlastník). Pro účty EA Azure je také nutné povolit na portálu EA Portal nastavení **AO view charges** (Vlastník účtu může zobrazit náklady).

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na bočním panelu vyberte **Všechny služby**, vyhledejte _skupiny pro správu_ a pak vyberte **skupiny pro správu**.
3. Vyberte skupinu pro správu v hierarchii.
4. Vedle názvu skupiny pro správu vyberte **Podrobnosti**.
5. V levém podokně vyberte **Access Control (IAM)** .
6. Vyberte **Přidat**.
7. V části **Role** vyberte **Čtenář Cost Management**.
8. V rozbalovací nabídce **Přiřadit přístup k** vyberte **Uživatel, skupina nebo aplikace Azure AD**.
9. Chcete-li přiřadit přístup, vyhledejte a vyberte uživatele.
10. Vyberte **Uložit**.  
    ![Příklady informací v okně Přidat oprávnění pro skupinu pro správu](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Přiřazení přístupu k rozsahu předplatného

Přístup k předplatnému vyžaduje alespoň oprávnění Čtenář Cost Management (nebo Čtenář). Oprávnění pro předplatné můžete nakonfigurovat na portálu Azure Portal. Abyste mohli povolit přístup ostatním uživatelům, musíte mít pro předplatné alespoň oprávnění Správce uživatelských přístupů (nebo Vlastník). Pro účty EA Azure je také nutné povolit na portálu EA Portal nastavení **AO view charges** (Vlastník účtu může zobrazit náklady).

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na bočním panelu vyberte **Všechny služby**, vyhledejte _předplatná_ a pak vyberte **Předplatná**.
3. Vyberte své předplatné.
4. V levém podokně vyberte **Access Control (IAM)** .
5. Vyberte **Přidat**.
6. V části **Role** vyberte **Čtenář Cost Management**.
7. V rozbalovací nabídce **Přiřadit přístup k** vyberte **Uživatel, skupina nebo aplikace Azure AD**.
8. Chcete-li přiřadit přístup, vyhledejte a vyberte uživatele.
9. Vyberte **Uložit**.

## <a name="assign-resource-group-scope-access"></a>Přiřazení přístupu k rozsahu skupiny prostředků

Přístup ke skupině prostředků vyžaduje alespoň oprávnění Čtenář Cost Management (nebo Čtenář). Oprávnění pro skupinu prostředků můžete nakonfigurovat na portálu Azure Portal. Abyste mohli povolit přístup ostatním uživatelům, musíte mít pro skupinu prostředků alespoň oprávnění Správce uživatelských přístupů (nebo Vlastník). Pro účty EA Azure je také nutné povolit na portálu EA Portal nastavení **AO view charges** (Vlastník účtu může zobrazit náklady).

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na bočním panelu vyberte **Všechny služby**, vyhledejte _skupiny prostředků_ a pak vyberte **skupiny prostředků**.
3. Vyberte skupinu prostředků.
4. V levém podokně vyberte **Access Control (IAM)** .
5. Vyberte **Přidat**.
6. V části **Role** vyberte **Čtenář Cost Management**.
7. V rozbalovací nabídce **Přiřadit přístup k** vyberte **Uživatel, skupina nebo aplikace Azure AD**.
8. Chcete-li přiřadit přístup, vyhledejte a vyberte uživatele.
9. Vyberte **Uložit**.

## <a name="cross-tenant-authentication-issues"></a>Problémy s ověřováním napříč tenanty

V současné době má Azure Cost Management omezenou podporu pro ověřování napříč tenanty. V některých případech se může stát, že při pokusu o ověření napříč tenanty dojde v analýze nákladů k chybě **odepření přístupu**. K tomuto problému může dojít, pokud nakonfigurujete řízení přístupu na základě role v Azure (Azure RBAC) u předplatného jiného tenanta a pak se pokusíte zobrazit data nákladů.

*Řešení problému*: Až nakonfigurujete Azure RBAC napříč tenanty, počkejte hodinu. Pak se pokuste zobrazit náklady v analýze nákladů nebo udělte uživatelům přístup ke službě Cost Management v obou tenantech.  


## <a name="next-steps"></a>Další kroky

- Pokud jste si ještě neprošli úvodní příručku pro Cost Management, najdete ji v tématu [Začínáme s analýzou nákladů](quick-acm-cost-analysis.md).
