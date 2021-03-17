---
title: Připojení účtu Amazon Web Services ke službě Cloudyn v Azure
description: Připojte účet Amazon Web Services, abyste mohli v sestavách Cloudyn prohlížet data o nákladech a využití.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: de0fd1a27ca97545ccdd534908eb802993514017
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690166"
---
# <a name="connect-an-amazon-web-services-account"></a>Připojení účtu Amazon Web Services

Účet Amazon Web Services (AWS) můžete připojit ke Cloudynu dvěma způsoby. K připojení můžete použít roli IAM nebo účet uživatele IAM jen pro čtení. Doporučuje se použít roli IAM, která umožňuje delegovat přístup s definovaným oprávněním na důvěryhodné entity. Role IAM nevyžaduje dlouhodobé sdílení přístupových klíčů. Po připojení účtu AWS ke Cloudynu budou data o nákladech a využití k dispozici v sestavách Cloudyn. Tento dokument vás provede oběma možnostmi.

Další informace o identitách IAM v AWS najdete v článku o [identitách (uživatelích, skupinách a rolích)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Povolíte také podrobné fakturační sestavy AWS a uložíte tyto informace v kbelíku Simple Storage Service (S3) služeb AWS. Podrobné fakturační sestavy obsahují fakturované poplatky se značkami a informacemi o prostředcích po hodinách. Ukládání sestav umožňuje Cloudynu načítat sestavy z kbelíku a zobrazovat tyto informace v sestavách.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]
## <a name="aws-role-based-access"></a>Přístup založený na rolích AWS

Následující oddíly vás povedou při vytvoření role IAM jen pro čtení, která poskytuje přístup ke Cloudynu.

### <a name="get-your-cloudyn-account-external-id"></a>Získání externího ID účtu Cloudyn

V prvním kroku získáte z portálu Cloudyn jedinečné připojovací heslo. V AWS se používá jako **externí ID**.

1. Portál Cloudyn můžete otevřít z webu Azure Portal nebo můžete přejít na adresu [https://azure.cloudyn.com](https://azure.cloudyn.com) a přihlásit se.
2. Klikněte na symbol ozubeného kola a vyberte **Cloudové účty**.
3. Ve Správě účtů vyberte **Účty AWS** a klikněte na **Přidat nový +** .
4. V dialogovém okně **přidání účtu AWS** zkopírujte **externí ID** a uložte si tuto hodnotu pro postup vytvoření role AWS, který je v další části. Externí ID je pro váš účet jedinečné. Na následujícím obrázku je příklad externího ID _Contoso_, za kterým následuje číslo. Vaše ID je jiné.  
    ![Externí ID zobrazené v poli pro přidání účtu AWS](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Přidání přístupu AWS pro čtení na základě rolí

1. Přihlaste se ke konzole AWS na adrese [https://console.aws.amazon.com/iam/home](https://console.aws.amazon.com/iam/home) a vyberte **Roles** (Role).
2. Klikněte na **Create Role** (Vytvořit roli) a vyberte **Another AWS account** (Jiný účet AWS).
3. Do pole **Account ID** (ID účtu) vložte `432263259397`. Toto ID účtu představuje účet kolektoru dat Cloudyn, který AWS přiřadí službě Cloudyn. Použijte přesně stejné ID účtu, jako je zde.
4. Vedle **Options** (Možnosti) vyberte **Require external ID** (Vyžadovat externí ID). Vložte do pole jedinečnou hodnotu, kterou jste zkopírovali z pole **externího ID** v Cloudynu. Pak klikněte na **Další: Permissions** (Další: Oprávnění).  
    ![Vložení externího ID z Cloudynu na stránce pro vytvoření role](./media/connect-aws-account/create-role01.png)
5. V části **Attach permissions policies** (Připojit zásady oprávnění) ve vyhledávacím poli filtru **Policy type** (Typ zásady) zadejte `ReadOnlyAccess`, vyberte **ReadOnlyAccess** (přístup jen pro čtení) a klikněte na **Next: Review** (Další: Kontrola).  
    ![Výběr přístupu jen pro čtení v seznamu názvů zásad](./media/connect-aws-account/readonlyaccess.png)
6. Na stránce Review (Kontrola) zkontrolujte správnost vybraných údajů a zadejte **název role**. Příklad: *Azure-Cost-Mgt*. Zadejte **popis role**. Zadejte například _Role assignment for Cloudyn_ (Přiřazení role pro Cloudyn) a klikněte na **Create role** (Vytvořit roli).
7. V seznamu **rolí** klikněte na vytvořenou roli a zkopírujte hodnotu **ARN role** ze souhrnné stránky. Hodnotu ARN (Amazon Resource Name) role použijete později, až budete konfiguraci registrovat v Cloudynu.  
    ![Kopírování ARN role ze souhrnné stránky](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>Konfigurace přístupu role IAM pro AWS v Cloudynu

1. Z webu Azure Portal otevřete portál Cloudyn nebo přejděte na https://azure.cloudyn.com/ a přihlaste se.
2. Klikněte na symbol ozubeného kola a vyberte **Cloudové účty**.
3. Ve Správě účtů vyberte **Účty AWS** a klikněte na **Přidat nový +** .
4. Do pole s **názvem účtu** zadejte název účtu.
5. Vedle pole **Access Type** (Typ přístupu) vyberte **IAM Role** (Role IAM).
6. Do pole **Role ARN** (ARN role) vložte zkopírovanou hodnotu a klikněte na **Save** (Uložit).  
    ![Vložení ARN role do pole pro přidání účtu AWS](./media/connect-aws-account/add-aws-account-box.png)


Váš účet AWS se zobrazí v seznamu účtů. Uvedené **ID vlastníka** odpovídá hodnotě ARN role. U **stavu účtu** by mělo být zelené zaškrtnutí, které označuje, že Cloudyn má přístup k vašemu účtu AWS. Dokud nepovolíte podrobnou fakturaci AWS, stav konsolidace bude **samostatná**.

![Stav účtu AWS zobrazený na stránce pro správu účtů](./media/connect-aws-account/aws-account-status01.png)

Služba Cloudyn začne shromažďovat data a plnit sestavy. V dalším kroku [povolíte podrobnou fakturaci AWS](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>Přístup založený na uživateli AWS

Následující oddíly vás povedou při vytvoření uživatele jen pro čtení, který umožňuje přístup ke Cloudynu.

### <a name="add-aws-read-only-user-based-access"></a>Přidání přístupu AWS jen pro čtení na základě uživatele

1. Přihlaste se ke konzole AWS na adrese [https://console.aws.amazon.com/iam/home](https://console.aws.amazon.com/iam/home) a vyberte **Users** (Uživatelé).
2. Klikněte na **Add User** (Přidat uživatele).
3. Do pole **User name** (Uživatelské jméno) zadejte jméno uživatele.
4. V poli **Access type** (Typ přístupu) vyberte **Programmatic access** (Programový přístup) a klikněte na **Next: Permissions** (Další: Oprávnění).  
    ![Zadání uživatelského jména na stránce pro přidání uživatele](./media/connect-aws-account/add-user01.png)
5. V oprávněních vyberte **Attach existing policies directly** (Připojit existující zásady přímo).
6. V části **Attach permissions policies** (Připojit zásady oprávnění) ve vyhledávacím poli filtru **Policy type** (Typ zásady) zadejte `ReadOnlyAccess`, vyberte **ReadOnlyAccess** (přístup jen pro čtení) a klikněte na **Next: Review** (Další: Kontrola).  
    ![Výběr přístupu jen pro čtení při nastavení oprávnění uživatele](./media/connect-aws-account/set-permission-for-user.png)
7. Na stránce Review (Kontrola) zkontrolujte správnost vybraných údajů a klikněte na **Create user** (Vytvořit uživatele).
8. Na poslední stránce se zobrazí vaše ID přístupového klíče a tajný přístupový klíč. Tyto údaje můžete použít ke konfiguraci registrace v Cloudynu.
9. Klikněte na **Download .csv** (Stáhnout .csv) a uložte si soubor credentials.csv na bezpečné místo.  
    ![Kliknutí na tlačítko pro stažení souboru .csv a uložení přihlašovacích údajů](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>Konfigurace uživatelského přístupu IAM pro AWS v Cloudynu

1. Portál Cloudyn můžete otevřít z webu Azure Portal nebo můžete přejít na adresu https://azure.cloudyn.com/ a přihlásit se.
2. Klikněte na symbol ozubeného kola a vyberte **Cloudové účty**.
3. Ve Správě účtů vyberte **Účty AWS** a klikněte na **Přidat nový +** .
4. Do pole **Account Name** (Název účtu) zadejte název účtu.
5. Vedle pole **Access Type** (Typ přístupu) vyberte **IAM User** (Uživatel IAM).
6. Do pole **Access Key** (Přístupový klíč) vložte hodnotu **Access key ID** (ID přístupového klíče) ze souboru credentials.csv.
7. Do pole **Secret Key** (Tajný klíč) vložte hodnotu **tajného přístupového klíče** ze souboru credentials.csv a klikněte na **Save** (Uložit).  

Váš účet AWS se zobrazí v seznamu účtů. Ve **stavu účtu** by se měl zobrazit symbol zeleného zaškrtnutí.

Služba Cloudyn začne shromažďovat data a plnit sestavy. V dalším kroku [povolíte podrobnou fakturaci AWS](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Povolení podrobné fakturace AWS

Následující postup použijte k získání ARN role pro AWS. ARN role umožňuje udělit oprávnění ke čtení fakturačního kbelíku.

1. Přihlaste se ke konzole AWS na adrese [https://console.aws.amazon.com](https://console.aws.amazon.com) a vyberte **Services** (Služby).
2. Do pole pro vyhledávání služby zadejte *IAM* a vyberte tuto možnost.
3. V nabídce vlevo vyberte **Roles** (Role).
4. Ze seznamu rolí vyberte roli, kterou jste vytvořili pro přístup ke Cloudynu.
5. Na stránce se souhrnem rolí kliknutím zkopírujte hodnotu **Role ARN** (ARN role). Hodnotu ARN role si nechce po ruce pro další kroky.

### <a name="create-an-s3-bucket"></a>Vytvoření kbelíku S3

Vytvořte kbelík S3, do kterého se budou ukládat podrobné informace o fakturaci.

1. Přihlaste se ke konzole AWS na adrese [https://console.aws.amazon.com](https://console.aws.amazon.com) a vyberte **Services** (Služby).
2. Do pole pro vyhledávání služby zadejte *S3* a vyberte **S3**.
3. Na stránce Amazon S3 klikněte na **Create bucket** (Vytvořit kbelík).
4. V průvodci vytvořením kbelíku zvolte název kbelíku a oblast a klikněte na **Next** (Další).  
    ![Příklad informací na stránce pro vytvoření kbelíku](./media/connect-aws-account/create-bucket.png)
5. Na stránce **Set properties** (Nastavení vlastností) nechte výchozí hodnoty a klikněte na **Next** (Další).
6. Na stránce Review (Kontrola) klikněte na **Create bucket** (Vytvořit kbelík). Zobrazí se seznam kbelíků.
7. Klikněte na vytvořený kbelík, vyberte kartu **Permissions** (Oprávnění) a vyberte **Bucket Policy** (Zásada kbelíku). Otevře se editor zásady kbelíku.
8. Zkopírujte následující ukázkový kód JSON a vložte ho do editoru zásady kbelíku.
   - Hodnotu `<BillingBucketName>` nahraďte názvem kbelíku S3.
   - Hodnotu `<ReadOnlyUserOrRole>` nahraďte hodnotou ARN role nebo uživatele, kterou jste si předtím zkopírovali.

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

9. Klikněte na **Uložit**.  
    ![Kliknutí na Uložit v editoru zásady kbelíku](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Povolení fakturačních sestav AWS

Jakmile vytvoříte a nakonfigurujete kbelík S3, přejděte na konzole AWS na [Billing Preferences](https://console.aws.amazon.com/billing/home?#/preference) (Předvolby fakturace).

1. Na stránce předvoleb vyberte **Receive Billing Reports** (Přijímat sestavy fakturace).
2. V části **Receive Billing Reports** (Přijímat sestavy fakturace) zadejte název vytvořeného kbelíku a klikněte na **Verify** (Ověřit).  
3. Vyberte všechny čtyři možné podrobnosti sestav a klikněte na **Save preferences** (Uložit předvolby).  
    ![Výběr podrobnosti při povolení sestav](./media/connect-aws-account/enable-reports.png)

Cloudyn načte z kbelíku S3 podrobné fakturační údaje a po povolení podrobné fakturace naplní sestavy. Zobrazení podrobných fakturačních dat na konzole Cloudyn může trvat až 24 hodin. Jakmile jsou k dispozici podrobná fakturační data, zobrazí se stav konsolidace účtu **Consolidated** (Konsolidovaný). Stav účtu bude **Completed** (Dokončený).

![Stav konsolidace zobrazený na kartě účtů AWS](./media/connect-aws-account/consolidated-status.png)

U některých optimalizačních sestav může získání odpovídajícího vzorku dat trvat několik dnů, aby byla doporučení přesná.

## <a name="next-steps"></a>Další kroky

- Pokud se chcete dozvědět více o Cloudynu, pokračujte kurzem o [kontrole využití a nákladů](tutorial-review-usage.md) v Cloudynu.
