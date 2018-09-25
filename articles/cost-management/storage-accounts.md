---
title: Konfigurace účtů úložiště pro Cloudyn v Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak nakonfigurovat účty úložiště Azure a kontejnery úložiště AWS pro Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: 829995d40a73dc181a28a467e4d16fef0bdeb454
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990650"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Konfigurace účtů úložiště pro Cloudyn

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

Cloudyn sestavy můžete uložit na portálu Cloudyn, Azure storage nebo kontejnery úložiště AWS. Ukládání sestav na portálu Cloudyn je zdarma. Ale ukládání sestav do poskytovatele cloudových služeb úložiště je volitelná a má za následek další poplatky. Tento článek vám pomůže nakonfigurovat účty úložiště Azure a kontejnery úložiště Amazon Web Services (AWS) pro uložení sestavy.

## <a name="prerequisites"></a>Požadavky

Musíte mít účet úložiště Azure nebo kontejneru úložiště Amazon.

Pokud nemáte účet úložiště Azure, musíte ho vytvořit. Další informace o vytváření účtu služby Azure storage najdete v tématu [vytvořit účet úložiště](../storage/common/storage-quickstart-create-account.md).

Pokud nemáte k dispozici AWS kbelíku simple storage service (S3), musíte ji vytvořit. Další informace o vytvoření kontejneru S3, naleznete v tématu [vytvořit Kbelíky](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Konfigurace účtu služby Azure storage

Konfigurace je úložiště Azure pro použití u Cloudyn je jednoduché. Shromážděte informace o účtu úložiště a zkopírujte je na portálu Cloudyn.

1. Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.
2. Klikněte na tlačítko **všechny služby**vyberte **účty úložiště**, přejděte do účtu úložiště, který chcete použít a potom vyberte účet.
3. Na stránce svého účtu úložiště v rámci **nastavení**, klikněte na tlačítko **přístupové klíče**.
4. Kopii vaší **název účtu úložiště** a **připojovací řetězec** pod klíč1.  
![Přístupové klíče k úložišti Azure](./media/storage-accounts/azure-storage-access-keys.png)  
5. Portál Cloudyn můžete otevřít z webu Azure Portal nebo můžete přejít na adresu https://azure.cloudyn.com a přihlásit se.
6. Kliknutím na symbol ozubeného kola a pak vyberte **Správa sestav úložišť**.
7. Klikněte na tlačítko **přidat nový +** a zkontrolujte, že je vybraná možnost Microsoft Azure. Vložte název účtu úložiště Azure v **název** oblasti. Vložit vaše **připojovací řetězec** v příslušné oblasti. Zadejte název kontejneru a pak klikněte na tlačítko **Uložit**.  
![Cloudyn úložiště nakonfigurovaný pro Azure](./media/storage-accounts/azure-cloudyn-storage.png)

  Vaše nové úložiště položka Azure sestavy se zobrazí v seznamu účtů úložiště.  
    ![Nové úložiště Azure sestavy v seznamu](./media/storage-accounts/azure-storage-entry.png)


Sestavy můžete uložit do služby Azure storage. V sestavách, klikněte na tlačítko **akce** a pak vyberte **Naplánování sestavy**. Název sestavy a pak přidejte vlastní adresu URL nebo použít automaticky vytvořený adresu URL. Vyberte **uložit do úložiště** a pak vyberte účet úložiště. Zadejte předponu, která získá připojeným k názvu souboru sestavy. Vyberte formát souboru CSV nebo JSON a uložte sestavu.

## <a name="configure-an-aws-storage-bucket"></a>Konfigurace kontejneru úložiště AWS

Cloudyn používá existující přihlašovací údaje AWS: uživatel nebo Role, ukládání sestav do vašeho kontejneru. Pokud chcete otestovat přístup, Cloudyn pokusí uložit malý textový soubor do kontejneru s názvem souboru _kontrola sady permission.txt_.

Poskytnete Cloudyn role nebo uživatele s oprávněním umístit objekt do vašeho kontejneru. Potom použijte existující sady nebo vytvořit nový pro uložení sestavy. Nakonec se rozhodněte, jak spravovat třídy úložiště, nastavení životního cyklu pravidla nebo odeberte všechny nepotřebné soubory.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Přiřadit oprávnění uživatele AWS nebo role

Když vytvoříte novou zásadu, je třeba zadat přesné oprávnění potřebná k uložení sestavy do sady S3.

1. Přihlaste se ke konzole AWS a vyberte **služby**.
2. Vyberte **IAM** ze seznamu služeb.
3. Vyberte **zásady** na levé straně konzoly a pak klikněte na **vytvořit zásadu**.
4. Klikněte na tlačítko **JSON** kartu.
5. Tyto zásady můžete uložit sestavu do sady S3. Zkopírujte a vložte následující příklad zásady pro **JSON** kartu. Nahraďte &lt;bucketname&gt; názvem vašeho kontejneru.

  ```
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

6. Klikněte na tlačítko **zkontrolujte zásady**.  
    ![Zkontrolujte zásady](./media/storage-accounts/aws-policy.png)  
7. Na stránce zkontrolovat zásady zadejte název zásady. Například _CloudynSaveReport2S3_.
8. Klikněte na tlačítko **vytvořit zásadu**.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>Připojit zásady Cloudyn role nebo uživatele ve vašem účtu

Pro připojení nové zásady, otevřete konzoly AWS a upravte Cloudyn role nebo uživatele.

1. Přihlaste se ke konzole AWS a vyberte **služby**a pak vyberte **IAM** ze seznamu služeb.
2. Vyberte buď **role** nebo **uživatelé** na levé straně konzoly.

**Pro role:**

  1. Klikněte na název role Cloudyn.
  2. Na **oprávnění** klikněte na tlačítko **připojit zásady**.
  3. Vyhledejte zásadu, kterou jste vytvořili a vyberte ji a pak klikněte na **připojit zásady**.
    ![AWS – připojit zásady pro roli](./media/storage-accounts/aws-attach-policy-role.png)

**Pro uživatele:**

1. Vyberte uživatele, Cloudyn.
2. Na **oprávnění** klikněte na tlačítko **přidat oprávnění**.
3. V **udělit oprávnění** vyberte **připojit existující zásady přímo**.
4. Vyhledejte zásadu, kterou jste vytvořili a vyberte ji a pak klikněte na **Další: Revize**.
5. Na oprávnění přidat na stránku název role, klikněte na tlačítko **přidat oprávnění**.  
    ![AWS – připojit zásady uživatele](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Volitelné: Nastavte oprávnění pomocí sady zásad

Můžete také nastavit oprávnění k vytváření sestav s vaší sady S3 pomocí sady zásad. V klasickém zobrazení S3:

1. Vytvořte nebo vyberte existující sady.
2. Vyberte **oprávnění** kartu a potom klikněte na tlačítko **kontejneru zásad**.
3. Zkopírujte a vložte následující ukázkové zásady. Nahraďte &lt;kbelíku\_název&gt; a &lt;Cloudyn\_Princip&gt; s ARN vašeho kontejneru. Nahraďte ARN role nebo uživatele používají Cloudyn.

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

4. V editoru zásad kontejneru, klikněte na tlačítko **Uložit**.

### <a name="add-aws-report-storage-to-cloudyn"></a>Přidání úložiště AWS sestavy do Cloudyn

1. Portál Cloudyn můžete otevřít z webu Azure Portal nebo můžete přejít na adresu https://azure.cloudyn.com a přihlásit se.
2. Kliknutím na symbol ozubeného kola a pak vyberte **Správa sestav úložišť**.
3. Klikněte na tlačítko **přidat nový +** a zkontrolujte, že je vybraná AWS.
4. Vyberte účet a úložiště kontejneru. Název kontejneru úložiště AWS je automaticky vyplněné.  
    ![Přidat sestavu úložiště AWS sady](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Klikněte na tlačítko **Uložit** a potom klikněte na tlačítko **Ok**.

    Vaše nová položka sestavy úložiště AWS se zobrazí v seznamu účtů úložiště.  
    ![Nové úložiště AWS sestavy v seznamu](./media/storage-accounts/aws-storage-entry.png)


Sestavy můžete uložit do služby Azure storage. V sestavách, klikněte na tlačítko **akce** a pak vyberte **Naplánování sestavy**. Název sestavy a pak přidejte vlastní adresu URL nebo použít automaticky vytvořený adresu URL. Vyberte **uložit do úložiště** a pak vyberte účet úložiště. Zadejte předponu, která získá připojeným k názvu souboru sestavy. Vyberte formát souboru CSV nebo JSON a uložte sestavu.

## <a name="next-steps"></a>Další postup

- Kontrola [Principy Cloudyn sestavy](understanding-cost-reports.md) Další informace o základní strukturu a funkce sestavách Cloudyn.
