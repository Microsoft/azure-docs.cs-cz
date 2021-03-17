---
title: Apache HBA & Balíček zabezpečení podniku – Azure HDInsight
description: Kurz – Přečtěte si, jak nakonfigurovat zásady Apache Ranger pro adaptéry HBA v Azure HDInsight pomocí Balíček zabezpečení podniku.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: deefda126602e9b276b74752330a14cc3eb2aa95
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933719"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package"></a>Kurz: Konfigurace zásad Apache HBA v HDInsight pomocí Balíček zabezpečení podniku

Přečtěte si, jak nakonfigurovat zásady Apache Ranger pro clustery Apache HBA pro Balíček zabezpečení podniku (ESP). ESP clustery jsou připojené k doméně, což uživatelům umožňuje ověření pomocí přihlašovacích údajů do domény. V tomto kurzu vytvoříte dvě zásady Ranger, abyste omezili přístup k různým rodinám sloupců v tabulce HBA.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření uživatelů domén
> * Vytvoření zásad Ranger
> * Vytváření tabulek v clusteru HBA
> * Testování zásad Ranger

## <a name="before-you-begin"></a>Než začnete

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* Přihlaste se na [Azure Portal](https://portal.azure.com/).

* Vytvořte [cluster HDInsight HBA pomocí balíček zabezpečení podniku](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Připojení k uživatelskému rozhraní správce Apache Ranger

1. Z prohlížeče se připojte k uživatelskému rozhraní Ranger Admin pomocí adresy URL `https://<ClusterName>.azurehdinsight.net/Ranger/`. Nezapomeňte změnit `<ClusterName>` název clusteru HBA.

    > [!NOTE]  
    > Přihlašovací údaje k Rangeru nejsou stejné jako údaje ke clusteru Hadoop. Abyste zabránili prohlížeči v použití přihlašovacích údajů clusteru Hadoop uložených v mezipaměti, použijte pro připojení k uživatelskému rozhraní správce Ranger nové okno prohlížeče v režimu InPrivate.

2. Přihlaste se pomocí svých přihlašovacích údajů správce k Azure Active Directory (AD). Přihlašovací údaje správce k Azure AD nejsou stejné jako přihlašovací údaje ke clusteru HDInsight nebo přihlašovací údaje SSH k uzlu HDInsight Linux.

## <a name="create-domain-users"></a>Vytvoření uživatelů domén

Informace o tom, jak vytvořit **sales_user1** a **marketing_user1** uživatelů domény, najdete v tématu [Vytvoření clusteru HDInsight s balíček zabezpečení podniku](./apache-domain-joined-configure-using-azure-adds.md). V produkční scénáři uživatelé domény pocházejí z vašeho klienta služby Active Directory.

## <a name="create-hbase-tables-and-import-sample-data"></a>Vytváření tabulek HBA a Import ukázkových dat

Pomocí SSH se můžete připojit k clusterům HBA a pak pomocí [prostředí Apache HBA](https://hbase.apache.org/0.94/book/shell.html) vytvořit tabulky HBA, vkládat data a dotazovat data. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="to-use-the-hbase-shell"></a>Postup používání prostředí HBase

1. Ze SSH spusťte následující příkaz HBase:
   
    ```bash
    hbase shell
    ```

2. Vytvořte tabulku HBA `Customers` se dvěma skupinami sloupců: `Name` a `Contact` .

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
4. Zobrazit obsah tabulky:
    
    ```hbaseshell
    scan 'Customers'
    ```

    ![Výstup prostředí HDInsight Hadoop HBA](./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png)

## <a name="create-ranger-policies"></a>Vytvoření zásad Ranger

Vytvořte zásady Ranger pro **sales_user1** a **marketing_user1**.

1. Otevřete **Uživatelské rozhraní správce Ranger**. Klikněte na **\<ClusterName> _hbase** pod položkou **HBA**.

   ![Uživatelské rozhraní správce HDInsight Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png)

2. Na obrazovce **seznam zásad** se zobrazí všechny zásady Ranger vytvořené pro tento cluster. Může být uvedena jedna předem nakonfigurovaná zásada. Klikněte na **Přidat novou zásadu**.

    ![Seznam zásad HBA pro Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png)

3. Na obrazovce **vytvořit zásadu** zadejte následující hodnoty:

   |**Nastavení**  |**Navrhovaná hodnota**  |
   |---------|---------|
   |Název zásady  |  sales_customers_name_contact   |
   |Tabulka HBA   |  Zákazníci |
   |Řady sloupců HBA   |  Jméno, kontakt |
   |Sloupce HBA   |  * |
   |Vybrat skupinu  | |
   |Vybrat uživatele  | sales_user1 |
   |Oprávnění  | Číst |

   Následující zástupné znaky mohou být součástí názvu tématu:

   * `*` označuje nula nebo více výskytů znaků.
   * `?` označuje jeden znak.

   ![Zásady Apache Ranger vytvořit prodej](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png)

   >[!NOTE]
   >Pokud uživatel domény v části **Select User** (Vybrat uživatele) není k dispozici, chvíli počkejte, než se Ranger synchronizuje s AAD.

4. Kliknutím na **Přidat** uložte zásadu.

5. Klikněte na **Přidat novou zásadu** a zadejte následující hodnoty:

   |**Nastavení**  |**Navrhovaná hodnota**  |
   |---------|---------|
   |Název zásady  |  marketing_customers_contact   |
   |Tabulka HBA   |  Zákazníci |
   |Řady sloupců HBA   |  Kontakt |
   |Sloupce HBA   |  * |
   |Vybrat skupinu  | |
   |Vybrat uživatele  | marketing_user1 |
   |Oprávnění  | Číst |

   ![Zásady Apache Ranger vytvořit marketing](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png)  

6. Kliknutím na **Přidat** uložte zásadu.

## <a name="test-the-ranger-policies"></a>Testování zásad Ranger

V závislosti na nakonfigurovaných zásadách Ranger může **sales_user1** Zobrazit všechna data pro sloupce ve `Name` `Contact` skupinách a. **Marketing_user1** může zobrazit pouze data ve `Contact` skupině sloupců.

### <a name="access-data-as-sales_user1"></a>Přístup k datům jako sales_user1

1. Otevřete nové připojení SSH ke clusteru. Ke clusteru se přihlaste pomocí následujícího příkazu:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Pomocí příkazu kinit se změňte na kontext našeho požadovaného uživatele.

   ```bash
   kinit sales_user1
   ```

2. Otevřete prostředí HBA a naskenujte tabulku `Customers` .

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. Všimněte si, že uživatel Sales může zobrazit všechny sloupce `Customers` tabulky včetně dvou sloupců v `Name` řadě sloupců a také pět sloupců v `Contact` řadě sloupců.

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

1. Pomocí příkazu kinit se změňte kontext našeho požadovaného uživatele.

   ```bash
   kinit marketing_user1
   ```

1. Otevřete prostředí HBA a naskenujte tabulku `Customers` :

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

1. Všimněte si, že marketingový uživatel může zobrazit jenom pět sloupců `Contact` řady sloupců.

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

   ![Audit zásad uživatelského rozhraní HDInsight Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte cluster HBA, který jste vytvořili, pomocí následujícího postupu:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Do **vyhledávacího** pole v horní části zadejte **HDInsight**. 
1. V části **služby** vyberte **clustery HDInsight** .
1. V seznamu clusterů HDInsight, které se zobrazí, klikněte na **...** vedle clusteru, který jste vytvořili pro účely tohoto kurzu. 
1. Klikněte na **Odstranit**. Klikněte na **Ano**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s adaptéry Apache HBA](../hbase/apache-hbase-tutorial-get-started-linux.md)