---
title: Konfigurace účtů úložiště pro Cloudyn v Azure
description: Tento článek popisuje, jak nakonfigurovat účty úložiště Azure a kontejnery úložiště AWS pro Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: secdec18
ROBOTS: NOINDEX
ms.openlocfilehash: 130542833f2f5e0d4ae63cfa7f681b390e46fa1e
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689656"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Konfigurace účtů úložiště pro Cloudyn

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

Sestavy Cloudynu můžete ukládat na portálu Cloudyn, v úložišti Azure nebo v kontejnerech úložiště AWS. Ukládání sestav na portál Cloudyn je bezplatné. Ukládání sestav do úložiště vašeho poskytovatele cloudových služeb je ale volitelné a může vést k dodatečným nákladům. Tento článek vám pomůže nakonfigurovat účty úložiště Azure a kontejnery úložiště Amazon Web Services (AWS) pro ukládání sestav.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="prerequisites"></a>Požadavky

Musíte mít buď účet úložiště Azure, nebo kontejner úložiště Amazon.

Pokud nemáte účet úložiště Azure, musíte si ho vytvořit. Další informace o vytvoření účtu úložiště Azure najdete v článku [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md).

Pokud nemáte kontejner AWS Simple Storage Service (S3), musíte si ho vytvořit. Další informace o vytvoření kontejneru S3 najdete v článku [Vytvoření kontejneru](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Konfigurace účtu úložiště Azure

Konfigurace úložiště Azure pro použití s Cloudynem je jednoduchá. Shromážděte podrobnosti o účtu úložiště a zkopírujte je na portál Cloudyn.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Klikněte na **Všechny služby**, vyberte **Účty úložiště**, vyhledejte účet úložiště, který chcete použít, a pak tento účet vyberte.
3. V oblasti **Nastavení** stránky s účtem úložiště klikněte na **Přístupové klíče**.
4. Zkopírujte **Název účtu úložiště** a **Připojovací řetězec** pod klíčem 1.  
   ![Zkopírování názvu účtu a připojovacího řetězce úložiště](./media/storage-accounts/azure-storage-access-keys.png)  
5. Portál Cloudyn můžete otevřít z webu Azure Portal nebo můžete přejít na adresu [https://azure.cloudyn.com](https://azure.cloudyn.com) a přihlásit se.
6. Klikněte na symbol ozubeného kola a vyberte **Reports Storage Management** (Správa úložiště sestav).
7. Klikněte na **Add new +** (Přidat nový) a ověřte, že je vybraná možnost Microsoft Azure. Do oblasti **Name** (Název) vložte název svého účtu úložiště Azure. Vložte **připojovací řetězec** do příslušné oblasti. Zadejte název kontejneru a klikněte na **Save** (Uložit).  
   ![Vložení názvu účtu a připojovacího řetězce úložiště Azure v okně pro přidání nového úložiště sestav](./media/storage-accounts/azure-cloudyn-storage.png)

   V seznamu účtů úložišť se objeví nová položka úložiště sestav Azure.  
    ![Nová položka úložiště sestav Azure v seznamu](./media/storage-accounts/azure-storage-entry.png)


Teď můžete sestavy ukládat do úložiště Azure. V libovolné sestavě klikněte na **Actions** (Akce) a potom vyberte **Schedule report** (Naplánovat sestavu). Pojmenujte sestavu a pak buď přidejte svoji vlastní adresu URL, nebo použijte automaticky vytvořenou adresu URL. Vyberte **Save to storage** (Uložit do úložiště) a vyberte účet úložiště. Zadejte předponu, která se připojí k názvu souboru sestavy. Vyberte formát souboru CSV nebo JSON a pak tuto sestavu uložte.

## <a name="configure-an-aws-storage-bucket"></a>Konfigurace kontejneru úložiště AWS

Cloudyn používá k ukládání sestav do kontejneru existující přihlašovací údaje AWS: Uživatel nebo Role. Kvůli otestování přístupu se Cloudyn snaží do kontejneru uložit malý textový soubor s názvem _check-bucket-permission.txt_.

Roli nebo uživatele Cloudynu zadáte pomocí oprávnění PutObject ke svému kontejneru. K ukládání sestav pak použijete existující kontejner nebo vytvoříte nový. Nakonec rozhodnete, jak spravovat třídu úložiště, nastavit pravidla životního cyklu nebo odebírat nepotřebné soubory.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Přiřazení oprávnění k uživateli nebo roli AWS

Při vytváření nové zásady zadáte přesná oprávnění potřebná k uložení sestavy do kontejneru S3.

1. Přihlaste se ke konzole AWS a vyberte **Services** (Služby).
2. V seznamu služeb vyberte **IAM**.
3. Na levé straně konzoly vyberte **Policies** (Zásady) a pak klikněte na **Create Policy** (Vytvořit zásadu).
4. Klikněte na kartu **JSON**.
5. Následující zásada vám umožní uložit sestavu do kontejneru S3. Zkopírujte a vložte následující příklad zásady na kartu **JSON**. Parametr &lt;bucketname&gt; nahraďte názvem svého kontejneru.

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

6. Klikněte na **Review policy** (Zkontrolovat zásadu).  
    ![Zásada AWS JSON zobrazující ukázkové informace](./media/storage-accounts/aws-policy.png)  
7. Na stránce pro kontrolu zásady zadejte název zásady. Použijte například _CloudynSaveReport2S3_.
8. Klikněte na **Create policy** (Vytvořit zásadu).

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>Připojení zásady k roli nebo uživateli Cloudynu ve vašem účtu

Novou zásadu připojíte tak, že otevřete konzolu AWS a upravíte roli nebo uživatele Cloudynu.

1. Přihlaste se ke konzole AWS, vyberte **Services** (Služby) a v seznamu služeb vyberte **IAM**.
2. Na levé straně konzoly vyberte **Roles** (Role) nebo **Users** (Uživatelé).

**Pro role:**

  1. Klikněte na název role Cloudynu.
  2. Na kartě **Persmissions** (Oprávnění) klikněte na **Attach Policy** (Připojit zásadu).
  3. Vyhledejte vytvořenou zásadu, vyberte ji a pak klikněte na **Attach Policy** (Připojit zásadu).
    ![Příklad zásady připojené k roli Cloudynu](./media/storage-accounts/aws-attach-policy-role.png)

**Pro uživatele:**

1. Vyberte uživatele Cloudynu.
2. Na kartě **Permissions** (Oprávnění) klikněte na **Add permissions** (Přidat oprávnění).
3. V části **Grant Permissions** (Udělit oprávnění) vyberte **Attach existing policies directly** (Připojit existující zásady přímo).
4. Vyhledejte vytvořenou zásadu, vyberte ji a pak klikněte na **Next: Review** (Další: Zkontrolovat).
5. Na stránce Add permissions to role name (Přidat oprávnění k názvu role) klikněte na **Add permissions** (Přidat oprávnění).  
    ![Příklad zásady připojené k uživateli Cloudynu](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Volitelné: Nastavení oprávnění pomocí zásady kontejneru

Oprávnění k vytváření sestav v kontejneru S3 můžete vytvořit také pomocí zásady kontejneru. V klasickém zobrazení S3 postupujte takto:

1. Vytvořte nebo vyberte existující kontejner.
2. Vyberte kartu **Permissions** (Oprávnění) a pak klikněte na **Bucket policy** (Zásada kontejneru).
3. Zkopírujte a vložte následující ukázku zásady. Parametry &lt;bucket\_name&gt; a &lt;Cloudyn\_principle&gt; nahraďte ARN kontejneru. ARN nahraďte rolí nebo uživatelem, které používá Cloudyn.

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

4. V editoru zásad kontejneru klikněte na **Save** (Uložit).

### <a name="add-aws-report-storage-to-cloudyn"></a>Přidání úložiště sestav AWS do Cloudynu

1. Portál Cloudyn můžete otevřít z webu Azure Portal nebo můžete přejít na adresu [https://azure.cloudyn.com](https://azure.cloudyn.com) a přihlásit se.
2. Klikněte na symbol ozubeného kola a vyberte **Reports Storage Management** (Správa úložiště sestav).
3. Klikněte na **Add new +** (Přidat nový) a ověřte, že je vybraná možnost AWS.
4. Vyberte účet a kontejner úložiště. Název kontejneru úložiště AWS se vyplní automaticky.  
    ![Příklad informací v okně Add a new report storage (Přidat nové úložiště sestav)](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Klikněte na **Save** (Uložit) a potom na **OK**.

    V seznamu účtů úložišť se objeví nová položka úložiště sestav AWS.  
    ![Nová položka úložiště sestav AWS zobrazená v seznamu účtů úložiště](./media/storage-accounts/aws-storage-entry.png)


Teď můžete sestavy ukládat do úložiště Azure. V libovolné sestavě klikněte na **Actions** (Akce) a potom vyberte **Schedule report** (Naplánovat sestavu). Pojmenujte sestavu a pak buď přidejte svoji vlastní adresu URL, nebo použijte automaticky vytvořenou adresu URL. Vyberte **Save to storage** (Uložit do úložiště) a vyberte účet úložiště. Zadejte předponu, která se připojí k názvu souboru sestavy. Vyberte formát souboru CSV nebo JSON a pak tuto sestavu uložte.

## <a name="next-steps"></a>Další kroky

- Informace o základní struktuře a funkcích sestav Cloudynu najdete v článku [Vysvětlení sestav Cloudynu](understanding-cost-reports.md).
