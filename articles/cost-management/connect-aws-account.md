---
title: Připojení účtu Amazon Web Services do Cloudyn v Azure | Dokumentace Microsoftu
description: Připojení účtu Amazon Web Services k zobrazení nákladů a využití dat v sestavách Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 676c01a26d67b395340e5b1ed2dacc6b3b824742
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219744"
---
# <a name="connect-an-amazon-web-services-account"></a>Připojení účtu Amazon Web Services

Máte dvě možnosti pro propojení účtu Amazon Web Services (AWS) do Cloudyn. Můžete se připojit s rolí IAM nebo pomocí uživatelského účtu IAM jen pro čtení. IAM role je doporučeno, protože umožňuje delegovat přístup s oprávněními definované pro důvěryhodné entity. IAM role nevyžaduje můžete sdílet dlouhodobé přístupové klíče. Po připojení účtu AWS do Cloudyn, nákladů a využití dat je k dispozici v sestavách Cloudyn. Tento dokument vás provede obě možnosti.

Další informace o identitách IAM v AWS najdete v tématu [identity (uživatelé, skupiny a role)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Také povolit podrobné AWS fakturace sestav a tyto informace uložit do sady AWS simple storage service (S3). Podrobné sestavy fakturační zahrnují účtování poplatků informacemi značku a prostředek po hodinách. Ukládání sestav umožňuje načíst z vašeho kontejneru a zobrazení informací ve svých sestavách Cloudyn.


## <a name="aws-role-based-access"></a>Přístup na základě rolí AWS

Následující části vás provede vytvořením IAM role jen pro čtení k poskytnutí přístupu do Cloudyn.

### <a name="get-your-cloudyn-account-external-id"></a>Získání externí ID účtu Cloudyn

Prvním krokem je získání připojení jedinečné heslo z portálu Cloudyn. Používá se v AWS jako **externí ID**.

1. Otevřete portál Cloudyn z Azure Portal nebo přejděte na [https://azure.cloudyn.com](https://azure.cloudyn.com) a přihlaste se.
2. Klikněte na symbol ozubeného kola a pak vyberte **cloudové účty**.
3. V části Správa účtů vyberte kartu **účty AWS** a pak klikněte na **Přidat nové +** .
4. V dialogovém okně **Přidat účet AWS** ZKOPÍRUJTE **externí ID** a v další části uložte hodnotu pro kroky vytvoření role AWS. Externí ID je jedinečné pro váš účet. Na následujícím obrázku je příkladem externího ID hodnota _Contoso_ následovaný číslem. Vaše ID se liší.  
    v poli Přidat účet AWS se zobrazí externí ID ![](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Přidat AWS přístup jen pro čtení na základě rolí

1. Přihlaste se ke konzole AWS na https://console.aws.amazon.com/iam/home a vyberte **role**.
2. Klikněte na **vytvořit roli** a pak vyberte **jiný účet AWS**.
3. Do pole **ID účtu** vložte `432263259397`. Toto ID účtu se účet kolekce dat Cloudyn přiřadil AWS ke službě Cloudyn. Použijte přesnou zobrazené ID účtu.
4. V poli **Možnosti**vyberte **vyžadovat externí ID**. Vložte jedinečnou hodnotu, která byla dříve zkopírována z pole **externího ID** v Cloudyn. Pak klikněte na **Další: oprávnění**.  
    na stránce vytvořit roli ![vložit externí ID z Cloudyn](./media/connect-aws-account/create-role01.png)
5. V části **připojit zásady oprávnění**do pole Filtr **typu zásad** zadejte `ReadOnlyAccess`, vyberte **ReadOnlyAccess**a potom klikněte na **Další: Kontrola**.  
    v seznamu názvů zásad ![vybrat přístup jen pro čtení.](./media/connect-aws-account/readonlyaccess.png)
6. Na stránce kontrola ověřte správnost vašich výběrů a zadejte **název role**. Například *Azure-cost-správce*. Zadejte **Popis role**. Například _přiřazení role pro Cloudyn_a pak klikněte na **vytvořit roli**.
7. V seznamu **role** klikněte na roli, kterou jste vytvořili, a zkopírujte hodnotu **role ARN** ze stránky shrnutí. Používejte roli ARN (název zdroje Amazon) později při registraci vaší konfigurace v Cloudyn.  
    ![ARN role na stránce Souhrn](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>Nakonfigurovat přístup role AWS IAM ve službě Cloudyn

1. Otevřete portál Cloudyn z Azure Portal nebo přejděte na https://azure.cloudyn.com/ a přihlaste se.
2. Klikněte na symbol ozubeného kola a pak vyberte **cloudové účty**.
3. V části Správa účtů vyberte kartu **účty AWS** a pak klikněte na **Přidat nové +** .
4. Do pole **název účtu**zadejte název účtu.
5. Vedle **pole Typ přístupu**vyberte **role IAM**.
6. Do pole **role ARN** vložte hodnotu, kterou jste dříve zkopírovali, a potom klikněte na **Uložit**.  
    do pole přidat účet AWS ![vložte ARN role](./media/connect-aws-account/add-aws-account-box.png)


Vašemu účtu AWS se zobrazí v seznamu účtů. Uvedené **ID vlastníka** odpovídá hodnotě vaší role ARN. Váš **stav účtu** by měl mít symbol zelené značky zaškrtnutí, což znamená, že Cloudyn může získat přístup k vašemu účtu AWS. Dokud nepovolíte podrobné AWS fakturace, stav konsolidace se zobrazí jako **samostatný**.

![Stav účtu AWS zobrazený na stránce Správa účtů](./media/connect-aws-account/aws-account-status01.png)

Cloudyn spustí shromažďování dat a naplnění sestavy. Potom [Povolte podrobnou fakturaci AWS](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>Přístup na základě uživatele AWS

Následující části vás provede vytvořením uživatele jen pro čtení k poskytnutí přístupu do Cloudyn.

### <a name="add-aws-read-only-user-based-access"></a>Přidat AWS přístup jen pro čtení založené na uživatelích

1. Přihlaste se ke konzole AWS na https://console.aws.amazon.com/iam/home a vyberte **Uživatelé**.
2. Klikněte na **Přidat uživatele**.
3. Do pole **uživatelské jméno** zadejte uživatelské jméno.
4. V poli **typ přístupu**vyberte **programový přístup** a klikněte na **Další: oprávnění**.  
    ![zadejte uživatelské jméno na stránce Přidat uživatele](./media/connect-aws-account/add-user01.png)
5. V případě oprávnění vyberte možnost **připojit existující zásady přímo**.
6. V části **připojit zásady oprávnění**do pole Filtr **typu zásad** zadejte `ReadOnlyAccess`, vyberte **ReadOnlyAccess**a potom klikněte na **Další: zkontrolovat**.  
    ![vyberte ReadOnlyAccess pro nastavení oprávnění pro uživatele](./media/connect-aws-account/set-permission-for-user.png)
7. Na stránce Kontrola zkontrolujte, že jsou vaše volby správné, a klikněte na **vytvořit uživatele**.
8. Na stránce dokončení se zobrazí Access key ID a tajný kód přístupový klíč. Tyto informace můžete použít ke konfiguraci registrace ve službě Cloudyn.
9. Klikněte na **Stáhnout. csv** a uložte soubor. csv s přihlašovacími údaji do zabezpečeného umístění.  
    ![klikněte na stáhnout. csv a přihlašovací údaje uložte](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>Konfigurace přístupu na základě uživatele AWS IAM ve službě Cloudyn

1. Portál Cloudyn můžete otevřít z webu Azure Portal nebo můžete přejít na adresu https://azure.cloudyn.com/ a přihlásit se.
2. Klikněte na symbol ozubeného kola a pak vyberte **cloudové účty**.
3. V části Správa účtů vyberte kartu **účty AWS** a pak klikněte na **Přidat nové +** .
4. Jako **název účtu**zadejte název účtu.
5. Vedle **pole Typ přístupu**vyberte **uživatel IAM**.
6. Do pole **přístupový klíč**vložte hodnotu **ID přístupového klíče** ze souboru přihlašovacích údajů. csv.
7. V části **tajný klíč**vložte hodnotu **tajného přístupového klíče** ze souboru přihlašovacích údajů. csv a pak klikněte na **Uložit**.  

Vašemu účtu AWS se zobrazí v seznamu účtů. Váš **stav účtu** by měl mít symbol zelené značky zaškrtnutí.

Cloudyn spustí shromažďování dat a naplnění sestavy. Potom [Povolte podrobnou fakturaci AWS](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Povolit podrobnou účtování AWS

Chcete-li získat vaše Role ARN AWS, postupujte následovně. Role ARN můžete udělit oprávnění ke čtení do fakturačního kontejneru.

1. Přihlaste se ke konzole AWS na https://console.aws.amazon.com a vyberte **služby**.
2. Do vyhledávacího pole služby zadejte *IAM*a vyberte tuto možnost.
3. V nabídce na levé straně vyberte **role** .
4. V seznamu rolí vyberte roli, kterou jste vytvořili pro Cloudyn přístup.
5. Na stránce Souhrn rolí kliknutím zkopírujte **ARN role**. Zachovejte Role ARN po ruce pro pozdější kroky.

### <a name="create-an-s3-bucket"></a>Vytvoření kontejneru S3

Vytvoření kontejneru S3 uložit podrobné informace o fakturaci.

1. Přihlaste se ke konzole AWS na https://console.aws.amazon.com a vyberte **služby**.
2. Do vyhledávacího pole služby zadejte *S3*a vyberte **S3**.
3. Na stránce Amazon S3 klikněte na **vytvořit kontejner**.
4. V Průvodci vytvořením sady zvolte název a oblast intervalu a pak klikněte na **Další**.  
    ![ukázkové informace jedna stránka vytvořit kontejner](./media/connect-aws-account/create-bucket.png)
5. Na stránce **nastavit vlastnosti** ponechte výchozí hodnoty a potom klikněte na tlačítko **Další**.
6. Na stránce Kontrola klikněte na možnost **vytvořit kontejner**. Zobrazí se seznam vašich kontejneru.
7. Klikněte na kontejner, který jste vytvořili, vyberte kartu **oprávnění** a pak vyberte možnost **zásady kontejneru**. Otevře se editor zásad kontejneru.
8. V následujícím příkladu JSON zkopírujte a vložte ho v editoru zásad kontejneru.
   - Nahraďte `<BillingBucketName>` názvem svého intervalu S3.
   - Nahraďte `<ReadOnlyUserOrRole>` rolí nebo uživatelem ARN, kterou jste dříve zkopírovali.

   ```json
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

9. Klikněte na možnost **Uložit**.  
    ![klikněte na Uložit v editoru zásad sad](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Povolení fakturace sestavy AWS

Po vytvoření a konfiguraci intervalu S3 přejděte na [Předvolby fakturace](https://console.aws.amazon.com/billing/home?#/preference) v konzole AWS.

1. Na stránce Předvolby vyberte **příjem fakturačních sestav**.
2. V části **příjem fakturačních sestav**zadejte název vytvořeného intervalu a potom klikněte na tlačítko **ověřit**.  
3. Vyberte všechny čtyři možnosti členitosti sestavy a pak klikněte na **uložit předvolby**.  
    ![vybrat členitost a povolit sestavy](./media/connect-aws-account/enable-reports.png)

Cloudyn načte podrobné informace o fakturaci z vaší sady S3 a naplní sestavy po podrobné fakturace se aktivuje. Může trvat až 24 hodin, než podrobné fakturační data se zobrazí v konzole pro Cloudyn. Když jsou k dispozici podrobná data fakturace, stav konsolidace účtu se zobrazí jako **konsolidovaná**. Stav účtu se zobrazuje jako **dokončený**.

![Stav konsolidace zobrazený na kartě účtů AWS](./media/connect-aws-account/consolidated-status.png)

Některé sestavy optimalizace může vyžadovat několik dnů od data, abyste získali přesná doporučení velikost vzorku odpovídající data.

## <a name="next-steps"></a>Další kroky

- Další informace o Cloudyn najdete v kurzu [Kontrola využití a nákladů](tutorial-review-usage.md) pro Cloudyn.
