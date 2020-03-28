---
title: Balíček Apache HBase & podnikové zabezpečení – Azure HDInsight
description: Kurz – Naučte se nakonfigurovat zásady Apache Ranger pro HBase v Azure HDInsight s balíčkem podnikového zabezpečení.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: 89e9faeb3c67d0fd0c57adea3a3f69ec5438e3a0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73044646"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package"></a>Kurz: Konfigurace zásad Apache HBase v HDInsightu pomocí balíčku podnikového zabezpečení

Přečtěte si, jak nakonfigurovat zásady Apache Ranger pro clustery Apache HBase (Enterprise Security Package). ESP clustery jsou připojené k doméně, což uživatelům umožňuje ověření pomocí přihlašovacích údajů do domény. V tomto kurzu vytvoříte dvě zásady Ranger omezit přístup k různým rodinám sloupců v tabulce HBase.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření uživatelů domén
> * Vytvoření zásad Ranger
> * Vytvoření tabulek v clusteru HBase
> * Testování zásad Ranger

## <a name="before-you-begin"></a>Než začnete

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* Přihlaste se k [portálu Azure](https://portal.azure.com/).

* Vytvořte [cluster HDInsight HBase s balíčkem enterprise security package](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Připojení k uživatelskému rozhraní správce Apache Ranger

1. Z prohlížeče se připojte k uživatelskému rozhraní Ranger Admin pomocí adresy URL `https://<ClusterName>.azurehdinsight.net/Ranger/`. Nezapomeňte změnit `<ClusterName>` název clusteru HBase.

    > [!NOTE]  
    > Přihlašovací údaje k Rangeru nejsou stejné jako údaje ke clusteru Hadoop. Abyste zabránili prohlížeči v použití přihlašovacích údajů clusteru Hadoop uložených v mezipaměti, použijte pro připojení k uživatelskému rozhraní správce Ranger nové okno prohlížeče v režimu InPrivate.

2. Přihlaste se pomocí svých přihlašovacích údajů správce k Azure Active Directory (AD). Přihlašovací údaje správce k Azure AD nejsou stejné jako přihlašovací údaje ke clusteru HDInsight nebo přihlašovací údaje SSH k uzlu HDInsight Linux.

## <a name="create-domain-users"></a>Vytvoření uživatelů domén

Navštivte [stránku Vytvoření clusteru HDInsight pomocí balíčku enterprise security package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds), kde se dozvíte, jak vytvořit **sales_user1** a **marketing_user1** uživatele domény. V produkční scénáři uživatelé domény pocházejí z vašeho klienta služby Active Directory.

## <a name="create-hbase-tables-and-import-sample-data"></a>Vytvoření tabulek HBase a import ukázkových dat

Pomocí SSH se můžete připojit ke clusterům HBase a potom pomocí [prostředí Apache HBase](https://hbase.apache.org/0.94/book/shell.html) vytvořit tabulky HBase, vložit data a data dotazů. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="to-use-the-hbase-shell"></a>Postup používání prostředí HBase

1. Ze SSH spusťte následující příkaz HBase:
   
    ```bash
    hbase shell
    ```

2. Vytvořte tabulku `Customers` HBase se dvěma rodinami sloupců: `Name` a `Contact`.

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
4. Zobrazení obsahu tabulky:
    
    ```hbaseshell
    scan 'Customers'
    ```

    ![Výstup prostředí HDInsight Hadoop HBase](./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png)

## <a name="create-ranger-policies"></a>Vytvoření zásad Ranger

Vytvořte zásadu rangerpro **sales_user1** a **marketing_user1**.

1. Otevřete **Uživatelské rozhraní správce Ranger**. Klepněte na ** \<položku Název_clusteru>_hbase** v části **HBase**.

   ![HDInsight Apache Ranger admin uživatelské rozhraní](./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png)

2. Na obrazovce **Seznam zásad** se zobrazí všechny zásady rangerů vytvořené pro tento cluster. Může být uvedena jedna předem nakonfigurovaná zásada. Klepněte na tlačítko **Přidat nové zásady**.

    ![Seznam zásad Apache Ranger HBase](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png)

3. Na obrazovce **Vytvořit zásady** zadejte následující hodnoty:

   |**Nastavení**  |**Navrhovaná hodnota**  |
   |---------|---------|
   |Název zásady  |  sales_customers_name_contact   |
   |HBTabulka   |  Zákazníci |
   |HBase Column-family   |  Jméno, Kontakt |
   |Sloupec HBase   |  * |
   |Vybrat skupinu  | |
   |Vybrat uživatele  | sales_user1 |
   |Oprávnění  | Čtení |

   Následující zástupné znaky mohou být součástí názvu tématu:

   * `*`označuje nula nebo více výskytů znaků.
   * `?`označuje jeden znak.

   ![Zásady Apache Ranger vytvářejí prodej](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png)

   >[!NOTE]
   >Pokud uživatel domény v části **Select User** (Vybrat uživatele) není k dispozici, chvíli počkejte, než se Ranger synchronizuje s AAD.

4. Kliknutím na **Přidat** uložte zásadu.

5. Klikněte na **Přidat nové zásady** a zadejte následující hodnoty:

   |**Nastavení**  |**Navrhovaná hodnota**  |
   |---------|---------|
   |Název zásady  |  marketing_customers_contact   |
   |HBTabulka   |  Zákazníci |
   |HBase Column-family   |  Kontakt |
   |Sloupec HBase   |  * |
   |Vybrat skupinu  | |
   |Vybrat uživatele  | marketing_user1 |
   |Oprávnění  | Čtení |

   ![Zásady Apache Ranger vytvářejí marketing](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png)  

6. Kliknutím na **Přidat** uložte zásadu.

## <a name="test-the-ranger-policies"></a>Testování zásad Ranger

Na základě nakonfigurovaných zásad Ranger **sales_user1** zobrazit všechna `Name` data `Contact` pro sloupce v rodinách sloupců i sloupců. Marketing_user1 **marketing_user1** lze zobrazit pouze `Contact` data v rodině sloupců.

### <a name="access-data-as-sales_user1"></a>Přístup k datům jako sales_user1

1. Otevřete nové připojení SSH ke clusteru. Pro přihlášení ke clusteru použijte následující příkaz:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Pomocí příkazu kinit změňte kontext našeho požadovaného uživatele.

   ```bash
   kinit sales_user1
   ```

2. Otevřete prostředí HBase a `Customers`naskenuj tabulku .

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. Všimněte si, že uživatel prodeje `Customers` může zobrazit všechny `Name` sloupce tabulky včetně dvou sloupců v `Contact` rodině sloupců a také pět sloupců v rodině sloupců.

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

### <a name="access-data-as-marketing_user1"></a>Přístup k datům jako marketing_user1

1. Otevřete nové připojení SSH ke clusteru. Pomocí následujícího příkazu se přihlaste jako **marketing_user1**:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Pomocí příkazu kinit změňte kontext našeho požadovaného uživatele

   ```bash
   kinit marketing_user1
   ```

1. Otevřete prostředí HBase a `Customers`naskenujete tabulku :

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

1. Všimněte si, že marketingový uživatel může `Contact` zobrazit pouze pět sloupců rodiny sloupců.

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

1. Zobrazte události přístupu k auditu v uživatelském rozhraní Ranger.

   ![Audit zásad ui rozhraní HDInsight Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete nadále používat tuto aplikaci, odstraňte cluster HBase, který jste vytvořili pomocí následujících kroků:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Do pole **Hledat** v horní části zadejte **HDInsight**. 
1. V části **Služby** **vyberte clustery HDInsight** .
1. V seznamu clusterů HDInsight, který se zobrazí, klikněte na **...** vedle clusteru, který jste vytvořili pro tento kurz. 
1. Klepněte na **tlačítko Odstranit**. Klepněte na tlačítko **Ano**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s Apache HBase](../hbase/apache-hbase-tutorial-get-started-linux.md)
