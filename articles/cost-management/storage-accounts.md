---
title: Konfigurace účtů úložiště pro Azure náklady na správu | Microsoft Docs
description: Tento článek popisuje, jak nakonfigurovat úložiště kbelíků AWS a účet úložiště Azure pro Azure náklady na správu.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: e37604e5cd36cfed016ef596060459011ec32d35
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297831"
---
# <a name="configure-storage-accounts-for-cost-management"></a>Konfigurace účtů úložiště pro náklady na správu

<!--- intent: As a Cost Management user, I want to configure Cost Management to use my cloud service provider storage account to store my reports. -->

Náklady na správu sestavy můžete uložit v Cloudyn portál, úložiště Azure nebo kbelíků úložiště AWS. Uložení sestavy na portál Cloudyn je zdarma. Ale ukládání sestavy do poskytovatele cloudových služeb úložiště je volitelný a způsobuje náklady na další. Tento článek vám pomůže nakonfigurovat účty úložiště Azure a kbelíků úložiště Amazon Web Services (AWS) pro uložení sestavy.

## <a name="prerequisites"></a>Požadavky

Musíte mít účet úložiště Azure nebo bucket úložiště Amazon.

Pokud nemáte účet úložiště Azure, budete muset vytvořit. Další informace o vytvoření účtu úložiště Azure najdete v tématu [vytvořit účet úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account).

Pokud nemáte AWS sady jednoduché úložiště služby (S3), musíte vytvořit jednu. Další informace o vytváření sady S3 najdete v tématu [vytvořit blok](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Konfigurace účtu úložiště Azure

Konfigurace, můžete je jednoduchá úložiště Azure za účelem použití náklady na správu. Shromážděte informace o účtu úložiště a zkopírujte je na portálu Cloudyn.

1. Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.
2. Klikněte na tlačítko **všechny služby**, vyberte **účty úložiště**, přejděte k účtu úložiště, který chcete použít a potom vyberte účet.
3. Na stránce účet úložiště v rámci **nastavení**, klikněte na tlačítko **přístupové klíče**.
4. Kopie vašeho **název účtu úložiště** a **připojovací řetězec** pod key1.  
![Přístupové klíče k úložišti Azure](./media/storage-accounts/azure-storage-access-keys.png)  
5. Otevřete portál Cloudyn z portálu Azure nebo přejděte na https://azure.cloudyn.com a přihlaste se.
6. Kliknutím na ikonu symbol a pak vyberte **Správa sestav úložišť**.
7. Klikněte na tlačítko **přidat nové +** a ujistěte se, že je vybraný Microsoft Azure. Vložte název účtu úložiště Azure v **název** oblasti. Vložení vaše **připojovací řetězec** v odpovídající oblasti. Zadejte název kontejneru a pak klikněte na tlačítko **Uložit**.  
![Úložiště Cloudyn nakonfigurován pro Azure.](./media/storage-accounts/azure-cloudyn-storage.png)

  Vaše nové položky úložiště Azure sestavy se zobrazí v seznamu účtů úložiště.  
    ![Nové úložiště Azure sestavy v seznamu](./media/storage-accounts/azure-storage-entry.png)


Sestavy můžete nyní uložit do úložiště Azure. V sestavách, klikněte na tlačítko **akce** a pak vyberte **Naplánování sestavy**. Název sestavy a potom přidat vlastní adresou URL nebo použít automaticky vytvořený adresy URL. Vyberte **uložit do úložiště** a potom vyberte účet úložiště. Zadejte předponu, která získá připojeným k názvu souboru sestavy. Vyberte formát souboru CSV nebo formátu JSON a potom uložte sestavu.

## <a name="configure-an-aws-storage-bucket"></a>Konfigurace sady AWS úložiště

Cloudyn používá existující přihlašovacích údajů AWS: uživatel nebo Role, uložení sestavy do vaší sady. K otestování přístupu, pokusí uložit malý textový soubor do sady s názvem souboru Cloudyn _kontrola bucket-permission.txt_.

Poskytnete Cloudyn role nebo uživatele s oprávněním umístit objekt na vaší sady. Pak použijte existující sady nebo vytvořte novou pro uložení sestavy. Nakonec rozhodnete, jak spravovat třídy úložiště, nastavte pravidla životního cyklu nebo odeberte všechny nepotřebné soubory.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Přiřadit oprávnění k AWS uživatel nebo role

Když vytvoříte novou zásadu, je třeba zadat přesný oprávnění potřebná ke uložit sestavu do sady S3.

1. Přihlaste se ke konzole AWS a vyberte **služby**.
2. Vyberte **IAM** ze seznamu služeb.
3. Vyberte **zásady** na levé straně konzoly a pak klikněte na **vytvořit zásadu**.
4. Klikněte **JSON** kartě.
5. Tyto zásady můžete uložit sestavu do sady S3. Zkopírujte a vložte následující příklad zásad na **JSON** kartě. Nahraďte &lt;bucketname&gt; s název vaší sady.

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
7. Na kontrolní stránce zásady zadejte název zásady. Například _CloudynSaveReport2S3_.
8. Klikněte na tlačítko **vytvořit zásadu**.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>Připojit zásady Cloudyn roli nebo uživatele ve vašem účtu

Připojit nové zásady, otevřete konzolu AWS a upravit roli Cloudyn nebo uživatele.

1. Přihlaste se ke konzole AWS a vyberte **služby**, pak vyberte **IAM** ze seznamu služeb.
2. Vyberte buď **role** nebo **uživatelé** z levé straně konzoly.

**Pro role:**

  1. Klikněte na název role Cloudyn.
  2. Na **oprávnění** , klikněte na **připojit zásady**.
  3. Vyhledejte zásadu, kterou jste vytvořili a vyberte ho a pak klikněte na tlačítko **připojit zásady**.
    ![AWS - připojte zásad pro roli](./media/storage-accounts/aws-attach-policy-role.png)

**Pro uživatele:**

1. Vyberte uživatele, Cloudyn.
2. Na **oprávnění** , klikněte na **přidat oprávnění**.
3. V **udělit oprávnění** vyberte **přímo připojit existující zásady**.
4. Vyhledejte zásadu, kterou jste vytvořili a vyberte ho a pak klikněte na tlačítko **Další: Zkontrolujte**.
5. Na oprávnění přidat na stránku název role, klikněte na tlačítko **přidat oprávnění**.  
    ![AWS - připojit zásady pro uživatele](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Volitelné: Nastavte oprávnění pomocí sady zásad

Můžete také nastavit oprávnění k vytvoření sestavy v vaší sady S3 pomocí sady zásad. V klasickém zobrazení S3:

1. Vytvořte nebo vyberte existující sady.
2. Vyberte **oprávnění** a pak klikněte **sady zásad**.
3. Zkopírujte a vložte následující ukázka zásad. Nahraďte &lt;sady\_název&gt; a &lt;Cloudyn\_Princip&gt; s informace o vaší sady. Nahradí informace role nebo uživatel používá Cloudyn.

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

4. V editoru zásad sady, klikněte na tlačítko **Uložit**.

### <a name="add-aws-report-storage-to-cloudyn"></a>Přidání úložiště AWS sestavy do Cloudyn

1. Otevřete portál Cloudyn z portálu Azure nebo přejděte na https://azure.cloudyn.com a přihlaste se.
2. Kliknutím na ikonu symbol a pak vyberte **Správa sestav úložišť**.
3. Klikněte na tlačítko **přidat nové +** a ujistěte se, že je vybraný AWS.
4. Vyberte sady účtu a úložiště. Název sady úložiště AWS je automaticky vyplněné.  
    ![Přidání sestavy úložiště AWS sady](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Klikněte na tlačítko **Uložit** a pak klikněte na **Ok**.

    Nové položky úložiště sestavy AWS se zobrazí v seznamu účtů úložiště.  
    ![Nové úložiště AWS sestavy v seznamu](./media/storage-accounts/aws-storage-entry.png)


Sestavy můžete nyní uložit do úložiště Azure. V sestavách, klikněte na tlačítko **akce** a pak vyberte **Naplánování sestavy**. Název sestavy a potom přidat vlastní adresou URL nebo použít automaticky vytvořený adresy URL. Vyberte **uložit do úložiště** a potom vyberte účet úložiště. Zadejte předponu, která získá připojeným k názvu souboru sestavy. Vyberte formát souboru CSV nebo formátu JSON a potom uložte sestavu.

## <a name="next-steps"></a>Další postup

- Zkontrolujte [Principy náklady sestavy správy](understanding-cost-reports.md) Další informace o základní struktura a funkce, náklady na správu sestav.
