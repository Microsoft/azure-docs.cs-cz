---
title: "Připojení k Azure náklady na správu účtu Amazon Web Services | Microsoft Docs"
description: "Připojení Amazon Web Services účet, který chcete zobrazit data o využití a náklady v sestavách náklady na správu."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/08/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 4a0280420132aad9f1e0b17d5998ec225bb0eaa1
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="connect-an-amazon-web-services-account"></a>Připojit účtu Amazon Web Services

Máte dvě možnosti pro připojení k Azure náklady na správu účtu Amazon Web Services (AWS). Můžete připojit IAM role nebo s jen pro čtení IAM uživatelský účet. Roli IAM je doporučená, protože umožňuje delegovat přístup s definovaných oprávnění k důvěryhodné entity. Roli IAM nevyžaduje sdílet dlouhodobé přístupové klíče. Po připojení účtu AWS k náklady na správu, data o využití a náklady je k dispozici v sestavách náklady na správu. Tento dokument vás provede obě možnosti.

Další informace o identitách AWS IAM najdete v tématu [identity (uživatelů, skupin a rolí)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

## <a name="aws-role-based-access"></a>AWS přístupu podle rolí

Následující části vás provede procesem vytvoření role IAM jen pro čtení pro poskytnutí přístupu k náklady na správu.

### <a name="get-your-cost-management-account-external-id"></a>Získání ID externí účet náklady na správu

Prvním krokem je získat přístupové heslo jedinečný připojení z portálu Azure náklady na správu. Používá se v AWS, jako **externí ID**.

1. Otevřete portál Cloudyn z portálu Azure nebo přejděte na [https://azure.cloudyn.com](https://azure.cloudyn.com) a přihlaste se.
2. Klikněte na tlačítko **nastavení** (ikona ozubené kolo) a potom vyberte **cloudové účty**.
3. Ve správě účtů, vyberte **AWS účty** a pak klikněte **přidat nové +**.
4. V **přidat účet AWS** dialogové okno, kopie **externí ID** a uložte tuto hodnotu pro roli AWS vytvoření kroků v další části. Na následujícím obrázku je příklad ID _Cloudyn_. Vaše ID se liší.  
    ![Externí ID](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Přidat AWS jen pro čtení přístupu podle rolí

1. Přihlaste se ke konzole AWS v https://console.aws.amazon.com/iam/home a vyberte **role**.
2. Klikněte na tlačítko **vytvořit roli** a pak vyberte **účtu jiné AWS**.
3. Vložte identitu `432263259397` v **ID účtu** pole. Toto ID účtu je účet kolekce dat náklady na správu, který je nutné použít.
4. Vedle **možnosti**, vyberte **vyžadují externí ID** vložte hodnotu, kterou zkopírovali dříve v **externí ID** pole a pak klikněte na tlačítko **Další: Oprávnění**.  
    ![Vytvoření role](./media/connect-aws-account/create-role01.png)
5. V části **připojit zásady oprávnění**v **typ zásad** vyhledávání pole filtru, typ `ReadOnlyAccess`, vyberte **ReadOnlyAccess**, pak klikněte na tlačítko **Další: Zkontrolujte**.  
    ![Jen pro čtení.](./media/connect-aws-account/readonlyaccess.png)
6. Na kontrolní stránce zkontrolujte vybrané položky jsou správné a zadejte **název Role**. Například *Azure. náklady Mgt*. Zadejte **popis Role**. Například _přiřazení Role pro správu Azure náklady_, pak klikněte na tlačítko **vytvořit role**.
7. V **role** seznamu, klikněte na roli, které jste vytvořili a zkopírujte **Role informace** hodnota na stránce Souhrn. Používejte informace Role později při registraci vaší konfigurace v Azure náklady na správu.  
    ![Role informace](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cost-management"></a>Nakonfigurovat přístup role AWS IAM v náklady na správu

1. Otevřete portál Cloudyn z portálu Azure nebo přejděte na https://azure.cloudyn.com/ a přihlaste se.
2. Klikněte na tlačítko **nastavení** (ikona ozubené kolo) a potom vyberte **cloudové účty**.
3. Ve správě účtů, vyberte **AWS účty** a pak klikněte **přidat nové +**.
4. V **název účtu**, zadejte název pro účet.
5. Vedle **typ přístupu**, vyberte **IAM Role**.
6. V **Role informace** pole, vložte hodnotu, kterou jste dříve zkopírovali a pak klikněte na tlačítko **Uložit**.  
    ![Stav účtu AWS](./media/connect-aws-account/aws-account-status01.png)

Vašemu účtu AWS se zobrazí v seznamu účtů. **ID vlastníka** uvedené odpovídá hodnotě vaše informace Role. Vaše **stav účtu** by měl mít symbol zelená značka zaškrtnutí.

Náklady na správu spustí shromažďování dat a naplnění sestavy. Některé sestavy optimalizace však může vyžadovat data z několik dní, než jsou generovány přesné doporučení.

## <a name="aws-user-based-access"></a>AWS přístupu na základě uživatele

Následující části vás provede procesem vytvoření jen pro čtení uživatelům poskytnout přístup k náklady na správu.

### <a name="add-aws-read-only-user-based-access"></a>Přidat AWS jen pro čtení na základě uživatele přístup

1. Přihlaste se ke konzole AWS v https://console.aws.amazon.com/iam/home a vyberte **uživatelé**.
2. Klikněte na tlačítko **přidat uživatele**.
3. V **uživatelské jméno** pole, zadejte uživatelské jméno.
4. Pro **přistupovat typu**, vyberte **programový přístup** a klikněte na tlačítko **Další: oprávnění**.  
    ![Přidat uživatele](./media/connect-aws-account/add-user01.png)
5. Vyberte oprávnění **přímo připojit existující zásady**.
6. V části **připojit zásady oprávnění**v **typ zásad** vyhledávání pole filtru, typ `ReadOnlyAccess`, vyberte **ReadOnlyAccess**a pak klikněte na tlačítko **další : Zkontrolujte**.  
    ![Nastavte oprávnění pro uživatele](./media/connect-aws-account/set-permission-for-user.png)
7. Na kontrolní stránce zkontrolujte vybrané položky jsou správné, a klikněte na tlačítko **vytvořit uživateli**.
8. Na stránce dokončení se zobrazí váš přístup klíče ID a tajný klíč přístupový klíč. Tyto informace použít ke konfiguraci registrace v náklady na správu.
9. Klikněte na tlačítko **stáhnout .csv** a credentials.csv soubor uložte do bezpečného umístění.  
    ![Stáhněte si přihlašovací údaje](./media/connect-aws-account/download-csv.png)


### <a name="configure-aws-iam-user-based-access-in-cost-management"></a>Konfigurace přístupu na základě uživatele AWS IAM v náklady na správu

1. Otevřete portál Cloudyn z portálu Azure nebo přejděte na https://azure.cloudyn.com/ a přihlaste se.
2. Klikněte na tlačítko **nastavení** (ikona ozubené kolo) a potom vyberte **cloudové účty**.
3. Ve správě účtů, vyberte **AWS účty** a pak klikněte **přidat nové +**.
4. Pro **název účtu**, zadejte název účtu.
5. Vedle **typ přístupu**, vyberte **IAM uživatele**.
6. V **přístupový klíč**, vložte **přístup klíče ID** hodnotu ze souboru credentials.csv.
7. V **tajný klíč**, vložte **tajný přístupový klíč** hodnotu ze souboru credentials.csv a pak klikněte na **Uložit**.  
    ![Stav účtu AWS uživatele](./media/connect-aws-account/aws-user-account-status.png)

Vašemu účtu AWS se zobrazí v seznamu účtů. Vaše **stav účtu** by měl mít symbol zelená značka zaškrtnutí.

Náklady na správu spustí shromažďování dat a naplnění sestavy. Některé sestavy optimalizace však může vyžadovat data z několik dní, než jsou generovány přesné doporučení.

## <a name="next-steps"></a>Další postup

- Další informace o Azure náklady na správu, pokračujte [zkontrolujte využití a náklady](tutorial-review-usage.md) kurzu náklady na správu.
