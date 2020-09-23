---
title: 'Rychlý Start: vytvoření serveru-Azure Portal-Azure Database for MySQL'
description: Tento článek vás provede rychlým vytvořením ukázkového serveru Azure Database for MySQL v pěti minutách pomocí portálu Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 7/15/2020
ms.openlocfilehash: cdddd9a90911499421351adf0f41ef90f0e2f9a5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906564"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-in-the-azure-portal"></a>Rychlý Start: vytvoření serveru Azure Database for MySQL v Azure Portal

Azure Database for MySQL je spravovaná služba, která slouží k provozu, správě a škálování vysoce dostupných databází MySQL v cloudu. V tomto rychlém startu se dozvíte, jak přibližně během pěti minut vytvořit server Azure Database for MySQL pomocí webu Azure Portal.  

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
Otevřete webový prohlížeč a přejděte na web [Azure Portal](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.

## <a name="create-an-azure-database-for-mysql-server"></a>Vytvoření serveru Azure Database for MySQL
Server Azure Database for MySQL vytvoříte s definovanou sadou [výpočetních prostředků a prostředků úložiště](./concepts-compute-unit-and-storage.md). Server vytvoříte v rámci [skupiny prostředků Azure](../azure-resource-manager/management/overview.md).

Server Azure Database for MySQL vytvoříte pomocí tohoto postupu:

1. V levém horním rohu portálu vyberte **vytvořit prostředek** (+).

2. Vyberte **databáze**  >  **Azure Database for MySQL**. Službu můžete vyhledat také zadáním **MySQL** do vyhledávacího pole.

  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="Možnost Azure Database for MySQL":::

3. Do formuláře podrobností o novém serveru zadejte následující informace:
    
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Formulář vytvoření serveru":::

**Nastavení** | **Navrhovaná hodnota** | **Popis pole** 
---|---|---
Předplatné | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro váš server. Pokud máte více předplatných, zvolte předplatné, ve kterém se vám prostředek účtuje.
Skupina prostředků | *myresourcegroup* | Zadejte název nové nebo existující skupiny prostředků. Skupinu prostředků lze použít k uspořádání závislostí, které patří do jednoho projektu.
Název serveru | Jedinečný název serveru | Zadejte jedinečný název, který identifikuje server Azure Database for MySQL. Například ' mysqldbserver '. Název serveru může obsahovat jenom malá písmena, číslice a znak spojovníku (-). Musí se skládat ze 3 až 63 znaků.
Zdroj dat |*Žádný* | Pokud chcete vytvořit nový server úplně od začátku, vyberte *Žádný*. ( *Zálohování* vyberete, pokud jste vytvořili server z geografické zálohy existujícího Azure Database for MySQLho serveru).
Přihlášení správce serveru | myadmin | Zadejte uživatelské jméno pro správce serveru. Jako uživatelské jméno správce nemůžete použít **azure_superuser**, **admin**, **Administrator**, **root**, **Guest**ani **Public** .
Heslo | *Podle vašeho výběru* | Zadejte nové heslo pro účet správce serveru. Heslo musí mít délku 8 až 128 znaků a kombinaci velkých a malých písmen, číslic a jiných než alfanumerických znaků (!, $, #,% a tak dále).
Potvrzení hesla | *Podle vašeho výběru*| Potvrďte heslo účtu správce.
Umístění | *Oblast nejblíže vašim uživatelům*| Vyberte umístění co nejblíže vašim uživatelům nebo vašim dalším aplikacím Azure.
Verze | *Nejnovější hlavní verze*| Nejnovější hlavní verze (pokud nemáte specifické požadavky vyžadující jinou verzi).
Výpočty a úložiště | **Obecné účely**, **Gen 5**, **2 virtuální jádra**, **5 GB**, **7 dní**, **Geograficky redundantní** |Konfigurace výpočtů, úložiště a zálohování pro nový server. Vyberte **Konfigurovat Server**. Pak vyberte příslušnou cenovou úroveň. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/mysql/). Pokud chcete povolit zálohování serveru v geograficky redundantním úložišti, vyberte z **možností redundance zálohy**možnost **geograficky redundantní** . Vyberte **OK**.

   > [!NOTE]
   > Zvažte použití cenové úrovně Basic, pokud je pro vaše zatížení vhodné světlé výpočetní prostředky a vstupně-výstupní operace. Upozorňujeme, že servery vytvořené v cenové úrovni Basic se nedají později škálovat na Pro obecné účely nebo paměťově optimalizované. 

4. Vyberte **zkontrolovat + vytvořit** a zřiďte Server. Zřizování může trvat až 20 minut.
   
5. Pokud chcete monitorovat proces nasazení, vyberte **Oznámení** (ikona zvonku) na panelu nástrojů.
   
Ve výchozím nastavení se v rámci vašeho serveru vytvoří následující databáze: **information_schema**, **mysql**, **performance_schema** a **sys**.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurace pravidla brány firewall na úrovni serveru
Ve výchozím nastavení je server, který jste vytvořili, chráněný pomocí brány firewall a není veřejně přístupný. Pokud chcete umožnit přístup k vaší IP adrese, přejděte k prostředku serveru v Azure Portal a vyberte **zabezpečení připojení** z nabídky na levé straně pro prostředek serveru. Nevíte, jak najít prostředek, najdete v tématu [Postup otevření prostředku](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources).

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Zabezpečení připojení – Pravidla brány firewall":::
   
Teď vyberte **Přidat aktuální IP adresu klienta** a pak vyberte **Uložit**. Můžete přidat další IP adresy nebo zadat rozsah IP adres pro připojení k serveru z těchto IP adres. Další informace najdete v tématu [Správa pravidel brány firewall na serveru Azure Database for MySQL](./concepts-firewall-rules.md) .

> [!NOTE]
> Ověřte, jestli vaše síť umožňuje odchozí provoz přes port 3306, který používá Azure Database for MySQL, aby se předešlo problémům s připojením.  

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Připojení k Azure Database for MySQL serveru pomocí klienta příkazového řádku MySQL
Můžete zvolit [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) nebo [MySQL Workbench](./connect-workbench.md) pro připojení k serveru z místního prostředí. V tomto rychlém startu spustíme **mysql.exe** v [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) , abyste se mohli připojit k serveru.

1. Na portálu spusťte Azure Cloud Shell kliknutím na zvýrazněnou ikonu v levé horní části. Poznamenejte si název serveru, přihlašovací jméno správce serveru, heslo a předplatné nově vytvořeného serveru z **přehledu** , jak je znázorněno na následujícím obrázku.

    >[!NOTE]
    >Pokud spouštíte Cloud Shell poprvé, zobrazí se výzva k vytvoření skupiny prostředků a účtu úložiště. Jedná se o jednorázový krok, který se automaticky připojí pro všechny relace. 

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
>[Vytvoření aplikace v PHP ve Windows pomocí MySQL](../app-service/app-service-web-tutorial-php-mysql.md) 
> [Sestavení aplikace PHP v systému Linux pomocí MySQL](../app-service/containers/tutorial-php-mysql-app.md) 
> [Sestavení jarní aplikace založené na jazyce Java pomocí MySQL](https://docs.microsoft.com/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)
