---
title: Připojení k Azure náklady na správu účtu Amazon Web Services | Microsoft Docs
description: Připojení Amazon Web Services účet, který chcete zobrazit data o využití a náklady v sestavách náklady na správu.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/06/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: 109235718f085ea2912f601f0657e08230e3e91d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="connect-an-amazon-web-services-account"></a>Připojit účtu Amazon Web Services

Máte dvě možnosti pro připojení k Azure náklady na správu účtu Amazon Web Services (AWS). Můžete připojit IAM role nebo s jen pro čtení IAM uživatelský účet. Roli IAM je doporučená, protože umožňuje delegovat přístup s definovaných oprávnění k důvěryhodné entity. Roli IAM nevyžaduje sdílet dlouhodobé přístupové klíče. Po připojení účtu AWS k náklady na správu, data o využití a náklady je k dispozici v sestavách náklady na správu. Tento dokument vás provede obě možnosti.

Další informace o identitách AWS IAM najdete v tématu [identity (uživatelů, skupin a rolí)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Také povolit AWS podrobné sestavy Fakturace a uložte informace AWS jednoduché úložiště služby (S3) sady. Podrobné sestavy a fakturace zahrnují fakturace poplatků s značky a prostředků informacemi o hodinu. Ukládání sestav umožňuje náklady na správu je načíst z vaší sady a zobrazení informací v její sestavy.


## <a name="aws-role-based-access"></a>AWS přístupu podle rolí

Následující části vás provede procesem vytvoření role IAM jen pro čtení pro poskytnutí přístupu k náklady na správu.

### <a name="get-your-cost-management-account-external-id"></a>Získání ID externí účet náklady na správu

Prvním krokem je získat přístupové heslo jedinečný připojení z portálu Azure náklady na správu. Používá se v AWS, jako **externí ID**.

1. Otevřete portál Cloudyn z portálu Azure nebo přejděte na [ https://azure.cloudyn.com ](https://azure.cloudyn.com) a přihlaste se.
2. Kliknutím na ikonu symbol a pak vyberte **cloudové účty**.
3. Ve správě účtů, vyberte **AWS účty** a pak klikněte **přidat nové +**.
4. V **přidat účet AWS** dialogové okno, kopie **externí ID** a uložte hodnota pro roli AWS vytvoření kroků v další části. Externí ID je jedinečný pro váš účet. Na následujícím obrázku příklad externí ID je _Contoso_ a číslo. Vaše ID se liší.  
    ![Externí ID](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Přidat AWS jen pro čtení přístupu podle rolí

1. Přihlaste se ke konzole AWS v https://console.aws.amazon.com/iam/home a vyberte **role**.
2. Klikněte na tlačítko **vytvořit roli** a pak vyberte **účtu jiné AWS**.
3. V **ID účtu** pole, vložte `432263259397`. Toto ID účtu je účet kolekce dat náklady na správu přiřadila AWS ke službě Cloudyn. Pomocí zobrazené přesný ID účtu.
4. Vedle **možnosti**, vyberte **vyžadují externí ID**. Vložit vaše jedinečnou hodnotu, který dříve zkopírovali ze **externí ID** pole náklady na správu. Pak klikněte na tlačítko **Další: oprávnění**.  
    ![Vytvoření role](./media/connect-aws-account/create-role01.png)
5. V části **připojit zásady oprávnění**v **typ zásad** vyhledávání pole filtru, typ `ReadOnlyAccess`, vyberte **ReadOnlyAccess**, pak klikněte na tlačítko **Další: Zkontrolujte**.  
    ![Jen pro čtení.](./media/connect-aws-account/readonlyaccess.png)
6. Na kontrolní stránce zkontrolujte vybrané položky jsou správné a zadejte **název Role**. Například *Azure. náklady Mgt*. Zadejte **popis Role**. Například _přiřazení Role pro správu Azure náklady_, pak klikněte na tlačítko **vytvořit role**.
7. V **role** seznamu, klikněte na roli, které jste vytvořili a zkopírujte **Role informace** hodnota na stránce Souhrn. Používejte Role informace (název prostředku Amazon) později při registraci vaší konfigurace v Azure náklady na správu.  
    ![Role informace](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cost-management"></a>Nakonfigurovat přístup role AWS IAM v náklady na správu

1. Otevřete portál Cloudyn z portálu Azure nebo přejděte na https://azure.cloudyn.com/ a přihlaste se.
2. Kliknutím na ikonu symbol a pak vyberte **cloudové účty**.
3. Ve správě účtů, vyberte **AWS účty** a pak klikněte **přidat nové +**.
4. V **název účtu**, zadejte název pro účet.
5. Vedle **typ přístupu**, vyberte **IAM Role**.
6. V **Role informace** pole, vložte hodnotu, kterou jste dříve zkopírovali a pak klikněte na tlačítko **Uložit**.  
    ![Přidání účtu AWS pole](./media/connect-aws-account/add-aws-account-box.png)


Vašemu účtu AWS se zobrazí v seznamu účtů. **ID vlastníka** uvedené odpovídá hodnotě vaše informace Role. Vaše **stav účtu** by měl mít symbol zelená značka zaškrtnutí, která určuje, že náklady na správu můžete přístup k vašemu účtu AWS. Dokud nepovolíte podrobné AWS fakturace vaší konsolidace stav se zobrazí jako **samostatné**.

![Stav účtu AWS](./media/connect-aws-account/aws-account-status01.png)

Náklady na správu spustí shromažďování dat a naplnění sestavy. Dále [povolit podrobné AWS fakturace](#enable-detailed-aws-billing).


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
2. Kliknutím na ikonu symbol a pak vyberte **cloudové účty**.
3. Ve správě účtů, vyberte **AWS účty** a pak klikněte **přidat nové +**.
4. Pro **název účtu**, zadejte název účtu.
5. Vedle **typ přístupu**, vyberte **IAM uživatele**.
6. V **přístupový klíč**, vložte **přístup klíče ID** hodnotu ze souboru credentials.csv.
7. V **tajný klíč**, vložte **tajný přístupový klíč** hodnotu ze souboru credentials.csv a pak klikněte na **Uložit**.  

Vašemu účtu AWS se zobrazí v seznamu účtů. Vaše **stav účtu** by měl mít symbol zelená značka zaškrtnutí.

Náklady na správu spustí shromažďování dat a naplnění sestavy. Dále [povolit podrobné AWS fakturace](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Povolit podrobné AWS fakturace

Pomocí následujících kroků získat vaše informace Role AWS. Role informace slouží k udělení oprávnění ke čtení pro fakturaci sady.

1. Přihlaste se ke konzole AWS v https://console.aws.amazon.com a vyberte **služby**.
2. Do služby vyhledávání zadejte *IAM*a vyberte tuto možnost.
3. Vyberte **role** z nabídky na levé straně.
4. V seznamu rolí vyberte roli, kterou jste vytvořili pro Cloudyn přístup.
5. Na stránce Souhrn rolí klikněte na zkopírovat **Role informace**. Zachovat informace Role užitečný v pozdějších krocích.

### <a name="create-an-s3-bucket"></a>Vytvoření sady S3

Můžete vytvořit sady S3 uložit podrobné informace o fakturaci.

1. Přihlaste se ke konzole AWS v https://console.aws.amazon.com a vyberte **služby**.
2. Do služby vyhledávání zadejte *S3*a vyberte **S3**.
3. Na stránce Amazon S3, klikněte na **vytvořit sady**.
4. V Průvodci vytvořením sady a sady název a potom na tlačítko **Další**.  
    ![Vytvoření sady](./media/connect-aws-account/create-bucket.png)
5. Na **nastavit vlastnosti** stránka, ponechte výchozí hodnoty a pak klikněte na tlačítko **Další**.
6. Na kontrolní stránce klikněte na tlačítko **vytvořit sady**. Zobrazí se seznam vaší sady.
7. Klikněte na tlačítko bloku, který jste vytvořili a vyberte **oprávnění** a pak vyberte **sady zásad**. Otevře se editor sady zásad.
8. Následující příklad JSON zkopírujte a vložte ji v editoru zásad sady.
  - Nahraďte `<BillingBucketName>` s názvem vaší sady S3.
  - Nahraďte `<ReadOnlyUserOrRole>` s rolí nebo informace uživatele, který jste dříve zkopírovali.

  ```
  {
    "Version": "2012-10-17",
    "Id": "Policy1426774604000",
    "Statement": [
        {
            "Sid": "Stmt1426774604000",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": [
                "s3:GetBucketAcl",
                "s3:GetBucketPolicy"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>"
        },
        {
            "Sid": "Stmt1426774604001",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        },
        {
            "Sid": "Stmt1426774604002",
            "Effect": "Allow",
            "Principal": {
                "AWS": "<ReadOnlyUserOrRole>"
            },
            "Action": [
                "s3:List*",
                "s3:Get*"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        }
    ]
  }
  ```

9. Klikněte na **Uložit**.  
    ![Editor zásad sady](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Povolit AWS fakturace sestavy

Po vytvoření a konfigurace sady S3, přejděte na [fakturace Předvolby](https://console.aws.amazon.com/billing/home?#/preference) v konzole AWS.

1. Na stránce předvoleb vyberte **přijímat fakturace sestavy**.
2. V části **přijímat fakturace sestavy**, zadejte název bloku, který jste vytvořili a pak klikněte na tlačítko **ověřte**.  
3. Vyberte všechny čtyři sestavy členitosti možnosti a pak klikněte na tlačítko **uložit předvolby**.  
    ![Povolit sestavy](./media/connect-aws-account/enable-reports.png)

Náklady správy načte podrobné fakturační informace z vaší sady S3 a naplní sestavy po povolení podrobné fakturace. Může trvat až 24 hodin, dokud se zobrazí v konzole Cloudyn podrobné fakturační údaje. Pokud je k dispozici podrobné fakturační údaje, stav konsolidace účtu se zobrazí jako **konsolidované**. Stav účtu se zobrazí jako **dokončeno**.

![Účet konsolidovat stav](./media/connect-aws-account/consolidated-status.png)

Některé sestavy optimalizace může vyžadovat několik dnů dat se získat velikost vzorku dostatek dat pro přesné doporučení.

## <a name="next-steps"></a>Další postup

- Další informace o Azure náklady na správu, pokračujte [zkontrolujte využití a náklady](tutorial-review-usage.md) kurzu náklady na správu.
