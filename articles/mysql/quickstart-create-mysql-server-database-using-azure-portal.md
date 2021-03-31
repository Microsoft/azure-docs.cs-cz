---
title: 'Rychlý Start: vytvoření serveru-Azure Portal-Azure Database for MySQL'
description: Tento článek vás provede vytvořením ukázkového serveru Azure Database for MySQL v pěti minutách pomocí portálu Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: b8f3a8e06bb27051417205c4dc3141948960bb00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98567805"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>Rychlý Start: vytvoření serveru Azure Database for MySQL pomocí Azure Portal

Azure Database for MySQL je spravovaná služba, pomocí které můžete spouštět, spravovat a škálovat vysoce dostupné databáze MySQL v cloudu. V tomto rychlém startu se dozvíte, jak pomocí Azure Portal vytvořit Azure Database for MySQL jeden server. Také se dozvíte, jak se připojit k serveru.

## <a name="prerequisites"></a>Požadavky
Vyžaduje se předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-azure-database-for-mysql-single-server"></a>Vytvoření jednoho serveru Azure Database for MySQL
1. Chcete-li vytvořit databázi MySQL s jedním serverem, otevřete [Azure Portal](https://portal.azure.com/) . Vyhledejte a vyberte **Azure Database for MySQL**:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Najít Azure Database for MySQL":::

1. Vyberte **Přidat**.

2. Na stránce **vybrat možnost nasazení Azure Database for MySQL** vyberte  **jeden server**:
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="Snímek obrazovky, který zobrazuje jednu možnost serveru.":::

3. Zadejte základní nastavení nového jednoho serveru:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Snímek obrazovky, který ukazuje stránku vytvořit server MySQL.":::

   **Nastavení** | **Navrhovaná hodnota** | **Popis**
   ---|---|---
   Předplatné | Vaše předplatné | Vyberte požadované předplatné Azure.
   Skupina prostředků | **myresourcegroup** | Zadejte novou skupinu prostředků nebo existující v rámci svého předplatného.
   Název serveru | **mydemoserver** | Zadejte jedinečný název. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí obsahovat 3 až 63 znaků.
   Zdroj dat |**Žádný** | Pokud chcete vytvořit nový server úplně od začátku, vyberte **Žádný**. **Zálohování** vyberte jenom v případě, že obnovujete z geografické zálohy existujícího serveru.
   Umístění |Vaše požadované umístění | Vyberte umístění ze seznamu.
   Verze | Nejnovější hlavní verze| Použijte nejnovější hlavní verzi. Zobrazit [všechny podporované verze](concepts-supported-versions.md).
   Výpočty + úložiště | Použít výchozí| Výchozí cenová úroveň je **pro obecné účely** se **4 virtuální jádra** a **100 GB** úložiště. Uchovávání záloh je nastaveno na **7 dní** s možností **geograficky redundantního** zálohování.<br/>Projděte si stránku s [cenami](https://azure.microsoft.com/pricing/details/mysql/) a aktualizujte výchozí nastavení, pokud potřebujete.
   Uživatelské jméno správce | **mydemoadmin** | Zadejte uživatelské jméno správce serveru. Pro uživatelské jméno správce nemůžete použít **azure_superuser**, **admin**, **Administrator**, **root**, **Guest** ani **Public** .
   Heslo | Heslo | Nové heslo pro uživatele správce serveru. Heslo musí mít délku 8 až 128 znaků a obsahovat kombinaci velkých a malých písmen, číslic a jiných než alfanumerických znaků (!, $, #,% a tak dále).
  

   > [!NOTE]
   > Zvažte použití cenové úrovně Basic, pokud je pro vaše zatížení vhodné světlé výpočetní prostředky a vstupně-výstupní operace. Upozorňujeme, že servery vytvořené v cenové úrovni Basic se nemůžou později škálovat na Pro obecné účely nebo paměť optimalizované pro paměť.

4. Vyberte **zkontrolovat + vytvořit** a zřiďte Server.

5. Počkejte, než se na stránce portálu zobrazí **dokončené vaše nasazení**. Vyberte **Přejít k prostředku** a přejít na stránku nově vytvořeného serveru:

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="Snímek obrazovky zobrazující zprávu o dokončení vašeho nasazení":::

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/mysql-doc-feedback)

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurace pravidla brány firewall na úrovni serveru

Ve výchozím nastavení je nový server chráněný bránou firewall. Chcete-li se připojit, musíte poskytnout přístup k vaší IP adrese provedením těchto kroků:

1. V levém podokně svého prostředku serveru použijte **zabezpečení připojení** . Pokud si nejste jisti, jak prostředek najít, přečtěte si téma [Postup otevření prostředku](../azure-resource-manager/management/manage-resources-portal.md#open-resources).

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Snímek obrazovky zobrazující stránku pravidla brány firewall > zabezpečení připojení":::

2. Vyberte **Přidat aktuální IP adresu klienta** a pak vyberte **Uložit**.

   > [!NOTE]
   > Pokud se chcete vyhnout problémům s připojením, ověřte, jestli vaše síť umožňuje odchozí přenosy přes port 3306, který používá Azure Database for MySQL. 

Můžete přidat další IP adresy nebo zadat rozsah IP adres pro připojení k serveru z těchto IP adres. Další informace najdete v tématu [Správa pravidel brány firewall na serveru Azure Database for MySQL](./concepts-firewall-rules.md).


[Máte problémy? Dejte nám prosím jistotu](https://aka.ms/mysql-doc-feedback)

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Připojení k serveru pomocí mysql.exe
K připojení serveru z místního prostředí můžete použít buď [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) , nebo [MySQL Workbench](./connect-workbench.md) . V tomto rychlém startu použijeme mysql.exe v [Azure Cloud Shell](../cloud-shell/overview.md) k připojení k serveru.


1. Na portálu otevřete Azure Cloud Shell, a to tak, že na panelu nástrojů vyberete první tlačítko, jak je znázorněno na následujícím snímku obrazovky. Poznamenejte si název serveru, název správce serveru a předplatné nového serveru v části **Přehled** , jak je znázorněno na snímku obrazovky.

    > [!NOTE]
    > Pokud Cloud Shell poprvé otevíráte, zobrazí se výzva k vytvoření skupiny prostředků a účtu úložiště. Jedná se o jednorázový krok. Automaticky se připojí pro všechny relace.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Snímek obrazovky zobrazující Cloud Shell v Azure Portal":::
2. Spusťte následující příkaz v terminálu Azure Cloud Shell. Nahraďte zobrazené hodnoty skutečným názvem serveru a uživatelským jménem správce. Pro Azure Database for MySQL je třeba přidat `@\<servername>` do uživatelského jména správce, jak je znázorněno zde: 

      ```azurecli-interactive
      mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p
      ```

      V Cloud Shell terminálu to vypadá nějak takto:

      ```
      Requesting a Cloud Shell.Succeeded.
      Connecting terminal...

      Welcome to Azure Cloud Shell

      Type "az" to use Azure CLI
      Type "help" to learn about Cloud Shell

      user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
      Enter password:
      Welcome to the MySQL monitor.  Commands end with ; or \g.
      Your MySQL connection id is 64796
      Server version: 5.6.42.0 Source distribution

      Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.
      Oracle is a registered trademark of Oracle Corporation and/or its
      affiliates. Other names may be trademarks of their respective
      owners.

      Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
      mysql>
      ```
3. Ve stejném Azure Cloud Shell terminálu vytvořte databázi s názvem `guest` :
      ```
      mysql> CREATE DATABASE guest;
      Query OK, 1 row affected (0.27 sec)
      ```
4. Přepněte do `guest` databáze:
      ```
      mysql> USE guest;
      Database changed
      ```
5. Zadejte `quit` a potom vyberte **ENTER** pro ukončení MySQL.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/mysql-doc-feedback)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Nyní jste vytvořili Azure Database for MySQL server ve skupině prostředků.  Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, můžete je odstranit odstraněním skupiny prostředků, nebo můžete jenom odstranit server MySQL. Pokud chcete odstranit skupinu prostředků, proveďte tyto kroky:
1. V Azure Portal vyhledejte a vyberte **skupiny prostředků**.
2. V seznamu skupin prostředků vyberte název vaší skupiny prostředků.
3. Na stránce **Přehled** pro skupinu prostředků vyberte **Odstranit skupinu prostředků**.
4. V potvrzovacím dialogovém okně zadejte název vaší skupiny prostředků a pak vyberte **Odstranit**.

Chcete-li odstranit server, můžete vybrat položku **Odstranit** na stránce **Přehled** pro váš server, jak je znázorněno zde:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Snímek obrazovky, který zobrazuje tlačítko Odstranit na stránce Přehled serveru.":::

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
>[Vytvoření aplikace v PHP ve Windows pomocí MySQL](../app-service/tutorial-php-mysql-app.md) <br/>

> [!div class="nextstepaction"]
>[Sestavení aplikace PHP v systému Linux pomocí MySQL](../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%3fpivots%3dplatform-linux)<br/><br/>

[Nemůžete najít, co hledáte? Dejte nám prosím jistotu.](https://aka.ms/mysql-doc-feedback)
