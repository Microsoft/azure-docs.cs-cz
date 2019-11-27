---
title: Konfigurace účtů úložiště pro Cloudyn v Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak nakonfigurovat účty úložiště Azure a kontejnery úložiště AWS pro Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: secdec18
ms.openlocfilehash: 1ac4442aa5a7e5e4367a03d33169412d37b3f1ea
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229930"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Konfigurace účtů úložiště pro Cloudyn

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

Cloudyn sestavy můžete uložit na portálu Cloudyn, Azure storage nebo kontejnery úložiště AWS. Ukládání sestav na portálu Cloudyn je zdarma. Ale ukládání sestav do poskytovatele cloudových služeb úložiště je volitelná a má za následek další poplatky. Tento článek vám pomůže nakonfigurovat účty úložiště Azure a kontejnery úložiště Amazon Web Services (AWS) pro uložení sestavy.

## <a name="prerequisites"></a>Požadavky

Musíte mít účet úložiště Azure nebo kontejneru úložiště Amazon.

Pokud nemáte účet úložiště Azure, musíte ho vytvořit. Další informace o vytvoření účtu úložiště Azure najdete v tématu [Vytvoření účtu úložiště](../storage/common/storage-quickstart-create-account.md).

Pokud nemáte k dispozici AWS kbelíku simple storage service (S3), musíte ji vytvořit. Další informace o vytváření intervalu S3 najdete v tématu [Vytvoření sady](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Konfigurace účtu služby Azure storage

Konfigurace je úložiště Azure pro použití u Cloudyn je jednoduché. Shromážděte informace o účtu úložiště a zkopírujte je na portálu Cloudyn.

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
2. Klikněte na **všechny služby**, vyberte **účty úložiště**, přejděte k účtu úložiště, který chcete použít, a pak vyberte účet.
3. Na stránce účtu úložiště v části **Nastavení**klikněte na **přístupové klíče**.
4. Do klíč1 zkopírujte **název svého účtu úložiště** a **připojovací řetězec** .  
   ![kopírovat název účtu úložiště a připojovací řetězec](./media/storage-accounts/azure-storage-access-keys.png)  
5. Portál Cloudyn můžete otevřít z webu Azure Portal nebo můžete přejít na adresu https://azure.cloudyn.com a přihlásit se.
6. Klikněte na symbol ozubeného kola a pak vyberte **Správa úložiště sestav**.
7. Klikněte na **Přidat nové +** a ujistěte se, že je vybraná možnost Microsoft Azure. Do oblasti **název** vložte název svého účtu služby Azure Storage. Vložte **připojovací řetězec** do odpovídající oblasti. Zadejte název kontejneru a pak klikněte na **Uložit**.  
   ![do pole přidat nové úložiště sestavy vložte název účtu úložiště Azure a připojovací řetězec](./media/storage-accounts/azure-cloudyn-storage.png)

   Vaše nové úložiště položka Azure sestavy se zobrazí v seznamu účtů úložiště.  
    ![Nová položka úložiště Azure sestavy v seznamu](./media/storage-accounts/azure-storage-entry.png)


Sestavy můžete uložit do služby Azure storage. V libovolné sestavě klikněte na **Akce** a pak vyberte **naplánovat sestavu**. Název sestavy a pak přidejte vlastní adresu URL nebo použít automaticky vytvořený adresu URL. Vyberte **Uložit do úložiště** a pak vyberte účet úložiště. Zadejte předponu, která získá připojeným k názvu souboru sestavy. Vyberte formát souboru CSV nebo JSON a uložte sestavu.

## <a name="configure-an-aws-storage-bucket"></a>Konfigurace kontejneru úložiště AWS

Cloudyn používá existující přihlašovací údaje AWS: uživatel nebo Role, ukládání sestav do vašeho kontejneru. Chcete-li otestovat přístup, Cloudyn se pokusí uložit malý textový soubor do kontejneru s názvem souboru _check-Bucket-Permission. txt_.

Poskytnete Cloudyn role nebo uživatele s oprávněním umístit objekt do vašeho kontejneru. Potom použijte existující sady nebo vytvořit nový pro uložení sestavy. Nakonec se rozhodněte, jak spravovat třídy úložiště, nastavení životního cyklu pravidla nebo odeberte všechny nepotřebné soubory.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Přiřadit oprávnění uživatele AWS nebo role

Když vytvoříte novou zásadu, je třeba zadat přesné oprávnění potřebná k uložení sestavy do sady S3.

1. Přihlaste se ke konzole AWS a vyberte **služby**.
2. V seznamu služeb vyberte **IAM** .
3. Na levé straně konzoly vyberte **zásady** a pak klikněte na **vytvořit zásadu**.
4. Klikněte na kartu **JSON** .
5. Tyto zásady můžete uložit sestavu do sady S3. Zkopírujte a vložte následující příklad zásad na kartu **JSON** . nahraďte &lt;název sady&gt; názvem svého intervalu.

   ```json
   {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid":  "CloudynSaveReport2S3",
        "Effect":      "Allow",
        "Action": [
          "s3:PutObject"
        ],
        "Resource": [
          "arn:aws:s3:::<bucketname>/*"
        ]
      }
    ]
   }
   ```

6. Klikněte na **zkontrolovat zásadu**.  
    ![zásady JSON AWS zobrazující informace o příkladech](./media/storage-accounts/aws-policy.png)  
7. Na stránce zkontrolovat zásady zadejte název zásady. Například _CloudynSaveReport2S3_.
8. Klikněte na **vytvořit zásadu**.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>Připojit zásady Cloudyn role nebo uživatele ve vašem účtu

Pro připojení nové zásady, otevřete konzoly AWS a upravte Cloudyn role nebo uživatele.

1. Přihlaste se ke konzole AWS a vyberte **služby**a v seznamu služeb vyberte **IAM** .
2. Na levé straně konzoly vyberte **role** nebo **uživatele** .

**Pro role:**

  1. Klikněte na název role Cloudyn.
  2. Na kartě **oprávnění** klikněte na **připojit zásadu**.
  3. Vyhledejte zásadu, kterou jste vytvořili, vyberte ji a pak klikněte na **připojit zásadu**.
    ![ukázkové zásady připojené k vaší roli Cloudyn](./media/storage-accounts/aws-attach-policy-role.png)

**Pro uživatele:**

1. Vyberte uživatele, Cloudyn.
2. Na kartě **oprávnění** klikněte na **Přidat oprávnění**.
3. V části **udělení oprávnění** vyberte **připojit existující zásady přímo**.
4. Vyhledejte zásadu, kterou jste vytvořili, vyberte ji a pak klikněte na **Další: zkontrolovat**.
5. Na stránce Přidat oprávnění k názvu role klikněte na **Přidat oprávnění**.  
    ![ukázkové zásady připojené k vašemu uživateli Cloudyn](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Volitelné: Nastavte oprávnění pomocí sady zásad

Můžete také nastavit oprávnění k vytváření sestav s vaší sady S3 pomocí sady zásad. V klasickém zobrazení S3:

1. Vytvořte nebo vyberte existující sady.
2. Vyberte kartu **oprávnění** a pak klikněte na **zásady sad**.
3. Zkopírujte a vložte následující ukázkové zásady. Nahraďte &lt;,\_název&gt; a &lt;Cloudyn\_hlavní&gt; s ARN vašeho intervalu. Nahraďte ARN role nebo uživatele používají Cloudyn.

   ```
   {
   "Id": "Policy1485775646248",
   "Version": "2012-10-17",
   "Statement": [
    {
      "Sid": "SaveReport2S3",
      "Action": [
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": "<bucket_name>/*",
      "Principal": {
        "AWS": [
          "<Cloudyn_principle>"
        ]
      }
    }
   ]
   }
   ```

4. V editoru zásad sad klikněte na **Uložit**.

### <a name="add-aws-report-storage-to-cloudyn"></a>Přidání úložiště AWS sestavy do Cloudyn

1. Portál Cloudyn můžete otevřít z webu Azure Portal nebo můžete přejít na adresu https://azure.cloudyn.com a přihlásit se.
2. Klikněte na symbol ozubeného kola a pak vyberte **Správa úložiště sestav**.
3. Klikněte na **Přidat nové +** a ujistěte se, že je vybraná možnost AWS.
4. Vyberte účet a úložiště kontejneru. Název kontejneru úložiště AWS je automaticky vyplněné.  
    ![Informace z příkladu v přidat nová pole úložiště sestavy](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Klikněte na **Uložit** a pak na **OK**.

    Vaše nová položka sestavy úložiště AWS se zobrazí v seznamu účtů úložiště.  
    ![Nové AWS sestavě úložiště položky zobrazit v seznamu účtů úložiště](./media/storage-accounts/aws-storage-entry.png)


Sestavy můžete uložit do služby Azure storage. V libovolné sestavě klikněte na **Akce** a pak vyberte **naplánovat sestavu**. Název sestavy a pak přidejte vlastní adresu URL nebo použít automaticky vytvořený adresu URL. Vyberte **Uložit do úložiště** a pak vyberte účet úložiště. Zadejte předponu, která získá připojeným k názvu souboru sestavy. Vyberte formát souboru CSV nebo JSON a uložte sestavu.

## <a name="next-steps"></a>Další kroky

- Informace o základní struktuře a funkcích sestav Cloudyn najdete v části [Principy sestav Cloudyn](understanding-cost-reports.md) .
