---
title: Připojení účtu Amazon Web Services do Cloudyn v Azure | Dokumentace Microsoftu
description: Připojení účtu Amazon Web Services k zobrazení nákladů a využití dat v sestavách Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 44bf1d9cd270394720aee71862c1e65118084259
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978216"
---
# <a name="connect-an-amazon-web-services-account"></a>Připojení účtu Amazon Web Services

Máte dvě možnosti pro propojení účtu Amazon Web Services (AWS) do Cloudyn. Můžete se připojit s rolí IAM nebo pomocí uživatelského účtu IAM jen pro čtení. IAM role je doporučeno, protože umožňuje delegovat přístup s oprávněními definované pro důvěryhodné entity. IAM role nevyžaduje můžete sdílet dlouhodobé přístupové klíče. Po připojení účtu AWS do Cloudyn, nákladů a využití dat je k dispozici v sestavách Cloudyn. Tento dokument vás provede obě možnosti.

Další informace o identitách AWS IAM najdete v tématu [identit (uživatelů, skupin a rolí)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Také povolit podrobné AWS fakturace sestav a tyto informace uložit do sady AWS simple storage service (S3). Podrobné sestavy fakturační zahrnují účtování poplatků informacemi značku a prostředek po hodinách. Ukládání sestav umožňuje načíst z vašeho kontejneru a zobrazení informací ve svých sestavách Cloudyn.


## <a name="aws-role-based-access"></a>Přístup na základě rolí AWS

Následující části vás provede vytvořením IAM role jen pro čtení k poskytnutí přístupu do Cloudyn.

### <a name="get-your-cloudyn-account-external-id"></a>Získání externí ID účtu Cloudyn

Prvním krokem je získání připojení jedinečné heslo z portálu Cloudyn. Používá se v AWS, jako **externí ID**.

1. Otevřete portál Cloudyn z portálu Azure portal nebo přejděte na [ https://azure.cloudyn.com ](https://azure.cloudyn.com) a přihlaste se.
2. Kliknutím na symbol ozubeného kola a pak vyberte **cloudové účty**.
3. Správa účtů, vyberte **účtů AWS** kartu a potom klikněte na tlačítko **přidat nový +**.
4. V **přidání účtu AWS** dialogové okno, kopie **externí ID** a uložte hodnotu pro roli AWS vytváření kroky v další části. Externí ID je jedinečné pro váš účet. Na následujícím obrázku příklad externí ID se _Contoso_ následuje ji číslo. Vaše ID se liší.  
    ![Externí ID](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Přidat AWS přístup jen pro čtení na základě rolí

1. Přihlaste se ke konzole AWS na https://console.aws.amazon.com/iam/home a vyberte **role**.
2. Klikněte na tlačítko **vytvořit roli** a pak vyberte **účtu AWS jiného**.
3. V **ID účtu** vložte `432263259397`. Toto ID účtu se účet kolekce dat Cloudyn přiřadil AWS ke službě Cloudyn. Použijte přesnou zobrazené ID účtu.
4. Vedle položky **možnosti**vyberte **vyžadují externí ID**. Vložit vaše jedinečná hodnota, která dříve zkopírovali z **externí ID** pole ve službě Cloudyn. Pak klikněte na tlačítko **Další: oprávnění**.  
    ![Vytvoření role](./media/connect-aws-account/create-role01.png)
5. V části **připojit zásady oprávnění**v **typ zásad** vyhledávací pole filtru, typu `ReadOnlyAccess`vyberte **ReadOnlyAccess**, pak klikněte na tlačítko **Další: Kontrola**.  
    ![Přístup jen pro čtení](./media/connect-aws-account/readonlyaccess.png)
6. Na stránce Kontrola zkontrolujte zvolené položky jsou správné a zadejte **název Role**. Například *Azure-Cost-Správa*. Zadejte **popis Role**. Například _přiřazení Role pro Cloudyn_, pak klikněte na tlačítko **vytvořit roli**.
7. V **role** seznamu, klikněte na roli, které jste vytvořili a zkopírujte **Role ARN** hodnotu na stránce souhrnu. Používejte roli ARN (název zdroje Amazon) později při registraci vaší konfigurace v Cloudyn.  
    ![Role ARN](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>Nakonfigurovat přístup role AWS IAM ve službě Cloudyn

1. Otevřete portál Cloudyn z portálu Azure portal nebo přejděte na https://azure.cloudyn.com/ a přihlaste se.
2. Kliknutím na symbol ozubeného kola a pak vyberte **cloudové účty**.
3. Správa účtů, vyberte **účtů AWS** kartu a potom klikněte na tlačítko **přidat nový +**.
4. V **název účtu**, zadejte název pro účet.
5. Vedle položky **typ přístupu**vyberte **IAM Role**.
6. V **Role ARN** pole, vložte tuto hodnotu jste dříve zkopírovali a potom klikněte na **Uložit**.  
    ![Přidání pole účtu AWS](./media/connect-aws-account/add-aws-account-box.png)


Vašemu účtu AWS se zobrazí v seznamu účtů. **ID vlastníka** uvedená odpovídá vaší roli ARN hodnotě. Vaše **stav účtu** by měl mít symbol zelená značka zaškrtnutí označující, že Cloudyn můžete přístup k vašemu účtu AWS. Dokud nepovolíte fakturaci podrobné AWS, konsolidace stav se zobrazí jako **samostatné**.

![Stav účtu AWS](./media/connect-aws-account/aws-account-status01.png)

Cloudyn spustí shromažďování dat a naplnění sestavy. Dále [povolit podrobné účtování AWS](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>Přístup na základě uživatele AWS

Následující části vás provede vytvořením uživatele jen pro čtení k poskytnutí přístupu do Cloudyn.

### <a name="add-aws-read-only-user-based-access"></a>Přidat AWS přístup jen pro čtení založené na uživatelích

1. Přihlaste se ke konzole AWS na https://console.aws.amazon.com/iam/home a vyberte **uživatelé**.
2. Klikněte na tlačítko **přidat uživatele**.
3. V **uživatelské jméno** pole, zadejte uživatelské jméno.
4. Pro **získat přístup k typu**vyberte **programový přístup** a klikněte na tlačítko **Další: oprávnění**.  
    ![Přidat uživatele](./media/connect-aws-account/add-user01.png)
5. Vyberte oprávnění **připojit existující zásady přímo**.
6. V části **připojit zásady oprávnění**v **typ zásad** vyhledávací pole filtru, typu `ReadOnlyAccess`vyberte **ReadOnlyAccess**a potom klikněte na tlačítko **další : Zkontrolujte**.  
    ![Nastavit oprávnění pro uživatele](./media/connect-aws-account/set-permission-for-user.png)
7. Na stránce Kontrola zkontrolujte zvolené položky jsou správné, a klikněte na tlačítko **vytvořit uživatele**.
8. Na stránce dokončení se zobrazí Access key ID a tajný kód přístupový klíč. Tyto informace můžete použít ke konfiguraci registrace ve službě Cloudyn.
9. Klikněte na tlačítko **stáhnout CSV** a credentials.csv soubor uložte do zabezpečeného umístění.  
    ![Stáhněte si přihlašovací údaje](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>Konfigurace přístupu na základě uživatele AWS IAM ve službě Cloudyn

1. Portál Cloudyn můžete otevřít z webu Azure Portal nebo můžete přejít na adresu https://azure.cloudyn.com/ a přihlásit se.
2. Kliknutím na symbol ozubeného kola a pak vyberte **cloudové účty**.
3. Správa účtů, vyberte **účtů AWS** kartu a potom klikněte na tlačítko **přidat nový +**.
4. Pro **název účtu**, zadejte název účtu.
5. Vedle položky **typ přístupu**vyberte **IAM uživatele**.
6. V **přístupový klíč**, vložte **přístup klíče ID** hodnotu ze souboru credentials.csv.
7. V **tajný klíč**, vložte **tajný přístupový klíč** hodnotu ze souboru credentials.csv a potom klikněte na tlačítko **Uložit**.  

Vašemu účtu AWS se zobrazí v seznamu účtů. Vaše **stav účtu** by měl mít symbol zelená značka zaškrtnutí.

Cloudyn spustí shromažďování dat a naplnění sestavy. Dále [povolit podrobné účtování AWS](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Povolit podrobnou účtování AWS

Chcete-li získat vaše Role ARN AWS, postupujte následovně. Role ARN můžete udělit oprávnění ke čtení do fakturačního kontejneru.

1. Přihlaste se ke konzole AWS na https://console.aws.amazon.com a vyberte **služby**.
2. Do služby vyhledávání zadejte *IAM*a vyberte příslušnou možnost.
3. Vyberte **role** z nabídky na levé straně.
4. V seznamu rolí vyberte roli, kterou jste vytvořili pro Cloudyn přístup.
5. Na stránce Souhrn rolí klikněte na tlačítko pro kopírování **Role ARN**. Zachovejte Role ARN po ruce pro pozdější kroky.

### <a name="create-an-s3-bucket"></a>Vytvoření kontejneru S3

Vytvoření kontejneru S3 uložit podrobné informace o fakturaci.

1. Přihlaste se ke konzole AWS na https://console.aws.amazon.com a vyberte **služby**.
2. Do služby vyhledávání zadejte *S3*a vyberte **S3**.
3. Klikněte na stránce Amazon S3 **vytvoření kontejneru**.
4. V Průvodci sady vytvořit zvolte název kontejneru a oblast a klikněte na **Další**.  
    ![Vytvoření kontejneru](./media/connect-aws-account/create-bucket.png)
5. Na **nastavit vlastnosti** stránce, ponechte výchozí hodnoty a pak klikněte na tlačítko **Další**.
6. Na stránce zkontrolovat, klikněte na tlačítko **vytvoření kontejneru**. Zobrazí se seznam vašich kontejneru.
7. Klikněte na tlačítko bloku, který jste vytvořili a vyberte **oprávnění** kartu a potom vyberte **sady zásad**. Otevře se editor zásad kontejneru.
8. V následujícím příkladu JSON zkopírujte a vložte ho v editoru zásad kontejneru.
  - Nahraďte `<BillingBucketName>` s názvem vaší sady S3.
  - Nahraďte `<ReadOnlyUserOrRole>` s rolí nebo uživatelské ARN, který jste dříve zkopírovali.

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
    ![Editor sady zásad](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Povolení fakturace sestavy AWS

Po vytvoření a konfigurace sady S3, přejděte do [fakturace Předvolby](https://console.aws.amazon.com/billing/home?#/preference) v konzole AWS.

1. Na stránce předvoleb vyberte **přijímat zprávy fakturace**.
2. V části **přijímat zprávy fakturace**, zadejte název kontejneru, který jste vytvořili a potom klikněte na tlačítko **ověřte**.  
3. Vyberte všechny čtyři členitosti možnosti sestav a klikněte na **uložit předvolby**.  
    ![Povolit sestavy](./media/connect-aws-account/enable-reports.png)

Cloudyn načte podrobné informace o fakturaci z vaší sady S3 a naplní sestavy po podrobné fakturace se aktivuje. Může trvat až 24 hodin, než podrobné fakturační data se zobrazí v konzole pro Cloudyn. Pokud je k dispozici podrobné fakturačních dat, konsolidace stavu vašeho účtu se zobrazí jako **konsolidované**. Stav účtu se zobrazí jako **dokončeno**.

![Konsolidované stavu účtu](./media/connect-aws-account/consolidated-status.png)

Některé sestavy optimalizace může vyžadovat několik dnů od data, abyste získali přesná doporučení velikost vzorku odpovídající data.

## <a name="next-steps"></a>Další postup

- Chcete-li další informace o Cloudyn, pokračujte [kontrola využití a nákladů](tutorial-review-usage.md) kurz pro Cloudyn.
