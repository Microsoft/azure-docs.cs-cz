---
title: 'Rychlý Start: vytvoření serveru-Azure Portal-Azure Database for MySQL'
description: Tento článek vás provede rychlým vytvořením ukázkového serveru Azure Database for MySQL v pěti minutách pomocí portálu Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: eabc077080e16578857f9ba06e6874441dad07ee
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425834"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-in-the-azure-portal"></a>Rychlý Start: vytvoření serveru Azure Database for MySQL v Azure Portal

Azure Database for MySQL je spravovaná služba, která slouží k provozu, správě a škálování vysoce dostupných databází MySQL v cloudu. V tomto rychlém startu se dozvíte, jak pomocí Azure Portal vytvořit Azure Database for MySQL jeden server a připojit se k serveru.

## <a name="prerequisites"></a>Předpoklady
Vyžaduje se předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-azure-database-for-mysql-single-server"></a>Vytvoření jednoho serveru Azure Database for MySQL
Chcete-li vytvořit databázi MySQL s jedním serverem, otevřete [Azure Portal](https://portal.azure.com/) . Vyhledejte a vyberte *Azure Database for MySQL*.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Najít Azure Database for MySQL":::

1. Vyberte **Přidat**.

2. Na stránce vytvořit Azure Database for MySQL vyberte  **jeden server**.
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="Zvolit jeden server":::

3. Nyní zadejte základní nastavení nového jednoho serveru.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Formulář vytvoření serveru":::

   **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
   ---|---|---
   Předplatné | Vaše předplatné | Vyberte požadované předplatné Azure.
   Skupina prostředků | *myresourcegroup* | Nová nebo existující skupina prostředků z vašeho předplatného.
   Název serveru | *mydemoserver* | Zadejte jedinečný název. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí se skládat ze 3 až 63 znaků.
   Zdroj dat |*Žádný* | Pokud chcete vytvořit nový server úplně od začátku, vyberte Žádný. Zálohování vyberte jenom v případě, že obnovujete z geografické zálohy existujícího serveru.
   Uživatelské jméno správce | *mydemoadmin* | Zadejte uživatelské jméno správce serveru. Jako uživatelské jméno správce nemůžete použít **azure_superuser** , **admin** , **Administrator** , **root** , **Guest** ani **Public** .
   Heslo | vaše heslo | Nové heslo pro uživatele správce serveru. Heslo musí mít délku 8 až 128 znaků a kombinaci velkých a malých písmen, číslic a jiných než alfanumerických znaků (!, $, #,% a tak dále).
   Umístění |Vaše požadované umístění | V rozevíracím seznamu vyberte umístění.
   Verze | nejnovější hlavní verze| Použijte nejnovější hlavní verzi. Zobrazit [všechny podporované verze](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)
   Výpočty a úložiště | použít výchozí| Výchozí cenová úroveň je Pro obecné účely se **4 virtuální jádra** a **100 GB** úložiště. Uchovávání záloh je nastaveno na **7 dní** s možností geograficky redundantního zálohování.<br/>Seznamte se s [cenami](https://azure.microsoft.com/pricing/details/mysql/) a v případě potřeby aktualizujte výchozí hodnoty.

   > [!NOTE]
   > Zvažte použití cenové úrovně Basic, pokud je pro vaše zatížení vhodné světlé výpočetní prostředky a vstupně-výstupní operace. Upozorňujeme, že servery vytvořené v cenové úrovni Basic se nedají později škálovat na Pro obecné účely nebo paměťově optimalizované.

4. Vyberte **zkontrolovat + vytvořit** a zřiďte Server.

5. Počkejte, než se na stránce portálu zobrazí **dokončené vaše nasazení**. Vyberte **Přejít k prostředku** a přejdete na stránku nově vytvořeného serveru.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="úspěšné nasazení":::

[Máte problémy? Dejte nám prosím jistotu](https://aka.ms/mysql-doc-feedback)

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurace pravidla brány firewall na úrovni serveru

Ve výchozím nastavení je server, který jste vytvořili, chráněný pomocí brány firewall. Abyste se mohli připojit, musíte přístup k vaší IP adrese udělit pomocí následujících kroků:

1. V nabídce na levé straně pro prostředek serveru přejděte na **zabezpečení připojení** . Nevíte, jak najít prostředek, najdete v tématu [Postup otevření prostředku](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources).

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Zabezpečení připojení – Pravidla brány firewall":::

2. Vyberte **Přidat aktuální IP adresu klienta** a pak vyberte **Uložit**.

   > [!NOTE]
   > Ověřte, jestli vaše síť umožňuje odchozí provoz přes port 3306, který používá Azure Database for MySQL, aby se předešlo problémům s připojením.

Můžete přidat další IP adresy nebo zadat rozsah IP adres pro připojení k serveru z těchto IP adres. Další informace najdete v tématu [Správa pravidel brány firewall na serveru Azure Database for MySQL](./concepts-firewall-rules.md) .


[Máte problémy? Dejte nám prosím jistotu](https://aka.ms/mysql-doc-feedback)

## <a name="connect-to-the-server-with-mysql-command-line-client"></a>Připojení k serveru pomocí klienta příkazového řádku MySQL
Můžete zvolit [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) nebo [MySQL Workbench](./connect-workbench.md) pro připojení k serveru z místního prostředí. V tomto rychlém startu spustíme **mysql.exe** v [Azure Cloud Shell](../cloud-shell/overview.md) , abyste se mohli připojit k serveru.


1. Na portálu spusťte Azure Cloud Shell kliknutím na zvýrazněnou ikonu v levé horní části. Poznamenejte si název serveru, přihlašovací jméno správce serveru, heslo a předplatné nově vytvořeného serveru z **přehledu** , jak je znázorněno na následujícím obrázku.

    > [!NOTE]
    > Pokud spouštíte Cloud Shell poprvé, zobrazí se výzva k vytvoření skupiny prostředků a účtu úložiště. Jedná se o jednorázový krok, který se automaticky připojí pro všechny relace.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Zobrazení úplného zobrazení portálu Cloud Shell":::
2. Spusťte tento příkaz na Azure Cloud Shell terminálu. Nahraďte hodnoty skutečným názvem serveru a přihlašovacím jménem uživatele správce. Uživatelské jméno správce vyžaduje ' @ \<servername> ', jak je znázorněno níže pro Azure Database for MySQL

      ```azurecli-interactive
      mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p
      ```

      Tady je postup, jak vypadat v Cloud Shell terminálu.

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
3. Ve stejném Azure Cloud Shell terminálu vytvořte **hosta** databáze.
      ```
      mysql> CREATE DATABASE guest;
      Query OK, 1 row affected (0.27 sec)
      ```
4. Změnit na **hosta** databáze
      ```
      mysql> USE guest;
      Database changed
      ```
5. Zadejte ```quit``` a potom vyberte klávesu ENTER pro ukončení MySQL.

[Máte problémy? Dejte nám prosím jistotu](https://aka.ms/mysql-doc-feedback)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Úspěšně jste vytvořili Azure Database for MySQL server ve skupině prostředků.  Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, můžete je odstranit odstraněním skupiny prostředků nebo pouhým odstraněním serveru MySQL. Pokud chcete odstranit skupinu prostředků, postupujte podle těchto kroků:
1. V Azure Portal vyhledejte a vyberte **skupiny prostředků**.
2. V seznamu Skupina prostředků vyberte název vaší skupiny prostředků.
3. Na stránce Přehled vaší skupiny prostředků vyberte **Odstranit skupinu prostředků**.
4. V potvrzovacím dialogovém okně zadejte název vaší skupiny prostředků a pak vyberte **Odstranit**.

Pokud chcete odstranit server, můžete kliknout na tlačítko **Odstranit** na stránce **Přehled** vašeho serveru, jak je znázorněno níže:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Odstranění prostředků":::

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
>[Vytvoření aplikace v PHP ve Windows pomocí MySQL](../app-service/app-service-web-tutorial-php-mysql.md) <br/>

> [!div class="nextstepaction"]
>[Sestavení aplikace PHP v systému Linux pomocí MySQL](../app-service/containers/tutorial-php-mysql-app.md)<br/><br/>

[Nemůžete najít, co hledáte? Dejte nám prosím jistotu.](https://aka.ms/mysql-doc-feedback)
