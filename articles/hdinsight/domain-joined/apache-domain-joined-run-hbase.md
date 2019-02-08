---
title: Nakonfigurovat zásady Apache HBase v HDInsight s balíčkem Enterprise Security Package - Azure
description: Zjistěte, jak nakonfigurovat zásady Apache Rangeru pro HBase v Azure HDInsight s balíčkem Enterprise Security Package.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: tutorial
ms.date: 02/01/2019
ms.openlocfilehash: e1a0dda4c13baf7fc2e5ba65d599db8c74591adb
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55893234"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Kurz: Nakonfigurovat zásady Apache HBase v HDInsight s balíčkem Enterprise Security Package (Preview)

Zjistěte, jak nakonfigurovat zásady Apache Rangeru pro clustery Apache HBase Enterprise Security Package (ESP). ESP clustery jsou připojené k doméně, což uživatelům umožňuje ověření pomocí přihlašovacích údajů do domény. V tomto kurzu vytvoříte dvě zásady Ranger pro omezení přístupu k jiné rodin sloupců v tabulce HBase.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření uživatelů domén
> * Vytvoření zásad Ranger
> * Vytvoření tabulek v clusteru služby HBase
> * Testování zásad Ranger

## <a name="before-you-begin"></a>Před zahájením

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

* Vytvoření [cluster HDInsight HBase s balíčkem Enterprise Security Package](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Připojení k uživatelskému rozhraní správce Apache Ranger

1. Z prohlížeče se připojte k uživatelskému rozhraní Ranger Admin pomocí adresy URL `https://<ClusterName>.azurehdinsight.net/Ranger/`. Nezapomeňte změnit `<ClusterName>` na název vašeho clusteru HBase.

    > [!NOTE]  
    > Přihlašovací údaje k Rangeru nejsou stejné jako údaje ke clusteru Hadoop. Abyste zabránili prohlížeči v použití přihlašovacích údajů clusteru Hadoop uložených v mezipaměti, použijte pro připojení k uživatelskému rozhraní správce Ranger nové okno prohlížeče v režimu InPrivate.

2. Přihlaste se pomocí svých přihlašovacích údajů správce k Azure Active Directory (AD). Přihlašovací údaje správce k Azure AD nejsou stejné jako přihlašovací údaje ke clusteru HDInsight nebo přihlašovací údaje SSH k uzlu HDInsight Linux.

## <a name="create-domain-users"></a>Vytvoření uživatelů domén

Navštivte [vytvoření clusteru HDInsight s balíčkem Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds), se naučíte vytvořit **sales_user1** a **marketing_user1** uživatele domény. V produkční scénáři uživatelé domény pocházejí z vašeho klienta služby Active Directory.

## <a name="create-hbase-tables-and-import-sample-data"></a>Vytváření tabulek HBase a import ukázkových dat

SSH slouží k připojení ke clusterům HBase a používání [Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) k vytváření tabulek HBase, vkládání dat a dotazování na data. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="to-use-the-hbase-shell"></a>Postup používání prostředí HBase

1. Ze SSH spusťte následující příkaz HBase:
   
    ```bash
    hbase shell
    ```

2. Vytvoření tabulky HBase `Customers` s dvěma skupinami sloupců: `Name` a `Contact`.

    ```hbaseshell   
    create 'Customers', 'Name', 'Contact'
    list
    ```
3. Vložte některá data:
    
    ```hbaseshell   
    put 'Customers','1001','Name:First','Alice'
    put 'Customers','1001','Name:Last','Johnson'
    put 'Customers','1001','Contact:Phone','333-333-3333'
    put 'Customers','1001','Contact:Address','313 133rd Place'
    put 'Customers','1001','Contact:City','Redmond'
    put 'Customers','1001','Contact:State','WA'
    put 'Customers','1001','Contact:ZipCode','98052'
    put 'Customers','1002','Name:First','Robert'
    put 'Customers','1002','Name:Last','Stevens'
    put 'Customers','1002','Contact:Phone','777-777-7777'
    put 'Customers','1002','Contact:Address','717 177th Ave'
    put 'Customers','1002','Contact:City','Bellevue'
    put 'Customers','1002','Contact:State','WA'
    put 'Customers','1002','Contact:ZipCode','98008'
    ```
4. Zobrazte obsah v tabulce:
    
    ```hbaseshell
    scan 'Contacts'
    ```
    ![Prostředí HDInsight Hadoop HBase](./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png)

## <a name="create-ranger-policies"></a>Vytvoření zásad Ranger

Vytvoření zásad Ranger pro **sales_user1** a **marketing_user1**.

1. Otevřete **Uživatelské rozhraní správce Ranger**. Klikněte na tlačítko  **\<Název_clusteru > _hbase** pod **HBase**.

   ![Uživatelské rozhraní správce Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png)

2. **Seznam zásad** obrazovce se zobrazí všechny zásady Ranger pro tento cluster vytvořit. Může být uvedena jedna předem nakonfigurovaná zásada. Klikněte na tlačítko **přidat nové zásady**.

    ![Zásada vytvoření uživatelského rozhraní správce Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png)

3.  Na **vytvořit zásadu** obrazovky, zadejte následující hodnoty:

   |**Nastavení**  |**Navrhovaná hodnota**  |
   |---------|---------|
   |Název zásady  |  sales_customers_name_contact   |
   |Tabulky HBase   |  Zákazníci |
   |HBase rodin sloupců   |  Jméno kontaktu |
   |Sloupec HBase   |  * |
   |Vybrat skupinu  | |
   |Vybrat uživatele  | sales_user1 |
   |Oprávnění  | Čtení |

   Následující zástupné znaky mohou být součástí názvu tématu:

   * `*` Určuje žádnou nebo několik výskytů znaků.
   * `?` označuje jeden znak.

   ![Zásada vytvoření uživatelského rozhraní správce Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png)

   >[!NOTE]
   >Pokud uživatel domény v části **Select User** (Vybrat uživatele) není k dispozici, chvíli počkejte, než se Ranger synchronizuje s AAD.

4. Kliknutím na **Přidat** uložte zásadu.

5. Klikněte na **Add New Policy** (Přidat novou zásadu) a pak zadejte následující hodnoty:

   |**Nastavení**  |**Navrhovaná hodnota**  |
   |---------|---------|
   |Název zásady  |  marketing_customers_contact   |
   |Tabulky HBase   |  Zákazníci |
   |HBase rodin sloupců   |  Kontakt |
   |Sloupec HBase   |  * |
   |Vybrat skupinu  | |
   |Vybrat uživatele  | marketing_user1 |
   |Oprávnění  | Čtení |

   ![Zásada vytvoření uživatelského rozhraní správce Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png)  

6. Kliknutím na **Přidat** uložte zásadu.

## <a name="test-the-ranger-policies"></a>Testování zásad Ranger

Na základě zásad Ranger nakonfigurovaná, **sales_user1** můžete zobrazit všechna data pro sloupce v obou `Name` a `Contact` rodin sloupců. **Marketing_user1** může zobrazit jenom data v `Contact` rodin sloupců.

### <a name="access-data-as-salesuser1"></a>Přístup k datům jako sales_user1

1. Otevřete nové připojení SSH ke clusteru. Použijte následující příkaz pro přihlášení ke clusteru:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Chcete-li změnit kontextu naše požadovaného uživatele pomocí příkazu kinit.

   ```bash
   kinit sales_user1
   ```

2. Otevřete prostředí hbase a prohledávání tabulky `Customers`.

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. Všimněte si, že prodejní uživatel si může zobrazit všechny sloupce `Customers` včetně dvou sloupců v tabulce `Name` rodin sloupců, jakož i pět sloupců v `Contact` rodin sloupců.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1001                              column=Name:First, timestamp=1548894871561, value=Alice
     1001                              column=Name:Last, timestamp=1548894871707, value=Johnson
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
     1002                              column=Name:First, timestamp=1548894897419, value=Robert
     1002                              column=Name:Last, timestamp=1548894897487, value=Stevens
    2 row(s) in 0.1000 seconds
    ```

### <a name="access-data-as-marketinguser1"></a>Přístup k datům jako marketing_user1

1. Otevřete nové připojení SSH ke clusteru. Použijte následující příkaz pro přihlášení jako **marketing_user1**:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Pomocí příkazu kinit můžete změnit na kontext naše určeným uživatelem.

   ```bash
   kinit marketing_user1
   ```

2. Otevřete prostředí hbase a prohledávání tabulky `Customers`:

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

3. Všimněte si, že marketingové uživatel může zobrazit jenom pět sloupců `Contact` rodin sloupců.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
    2 row(s) in 0.0730 seconds
    ```

9. Zobrazte události přístupu k auditu v uživatelském rozhraní Ranger.

   ![Audit zásad uživatelského rozhraní Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci používat, odstraňte cluster HBase, který jste vytvořili pomocí následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V **hledání** pole v horní části, typ **HDInsight**. 
1. Vyberte **clustery HDInsight** pod **služby**.
1. V seznamu clusterů HDInsight, který se zobrazí, klikněte **...**  u clusteru, který jste vytvořili pro účely tohoto kurzu. 
1. Klikněte na **Odstranit**. Klikněte na **Ano**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Začínáme s Apache HBase](../hbase/apache-hbase-tutorial-get-started-linux.md)
