---
title: Vytvoření a správě Azure databáze pro server databáze MySQL pomocí portálu Azure
description: Tento článek popisuje, jak můžete rychle vytvořit novou databázi MySQL server Azure a spravovat server pomocí portálu Azure.
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 4b52cb9e42e582d42424c2814e2e30f764a8679b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Vytvoření a správě Azure databáze pro server databáze MySQL pomocí portálu Azure
Toto téma popisuje, jak můžete rychle vytvořit novou databázi MySQL serveru Azure. Zahrnuje také informace o tom, jak spravovat server pomocí portálu Azure. Správa serveru zahrnuje zobrazení Podrobnosti o serveru a databází, resetuje se heslo, škálování prostředky a odstranění serveru.

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal
Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Vytvoření serveru Azure Database for MySQL
Postupujte podle těchto kroků k vytvoření Azure databáze MySQL serveru s názvem "mydemoserver."

1. Klikněte **vytvořit prostředek** tlačítko umístěné v levém horním rohu portálu Azure.

2. Na nové stránce vyberte **databáze**a pak na stránce databáze, vyberte **Azure Database pro databázi MySQL**.

    > Azure databáze pro server databáze MySQL byla vytvořena s definovanou sadu [výpočetního prostředí a úložiště](./concepts-pricing-tiers.md) prostředky. Vytvoření databáze v rámci skupiny prostředků Azure a v databázi aplikace Azure pro server databáze MySQL.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Vyplňte Azure databáze MySQL formuláře pomocí následující informace:

    | **Pole formuláře** | **Popis pole** |
    |----------------|-----------------------|
    | *Název serveru* | mydemoserver (název serveru je globálně jedinečný) |
    | *Předplatné* | mysubscription (vyberte z rozevírací nabídky) |
    | *Skupina prostředků* | myresourcegroup (vytvořit novou skupinu prostředků nebo použijte existující) |
    | *Výběr zdroje* | Prázdné (vytvořit prázdný MySQL serveru) |
    | *Přihlašovací jméno správce serveru* | myadmin (název účtu správce instalace) |
    | *Heslo* | Nastavit heslo účtu správce |
    | *Potvrdit heslo* | potvrďte heslo účtu správce |
    | *Umístění* | Asie a Tichomoří – jihovýchod (vyberte mezi Severní Evropa a západní USA) |
    | *Verze* | 5.7 (zvolte Azure databáze MySQL server verze) |

4. Klikněte na tlačítko **cenová úroveň** k určení úrovně služby a výkonu pro nový server. Vyberte **obecné účely** kartě. *Gen 4*, *2 virtuální jádra*, *5 GB* a *7 dní* jsou výchozí hodnoty pro **Výpočetní generaci**, **Virtuální jádra**, **Úložiště** a **Období uchování zálohy**. Můžete ponechat tyto posuvníky tak, jak jsou. Pokud chcete povolit zálohování serveru v geograficky redundantním úložišti, vyberte v **Možnosti redundance zálohy** možnost **Geograficky redundantní**.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Klikněte na **Vytvořit**, aby se server zřídil. Zřizování trvá několik minut.

    > Vyberte **připnout na řídicí panel** možnost, povolíte snadné sledování vašich nasazení.

## <a name="update-an-azure-database-for-mysql-server"></a>Aktualizovat databázi služby Azure pro server databáze MySQL
Poté, co byla vytvořena na nový server, uživatel má několik možností pro konfiguraci stávajícího serveru, včetně resetování hesla správce a škálování nahoru nebo dolů serveru změnou vCore nebo úložiště.

### <a name="change-the-administrator-user-password"></a>Změna hesla správce uživatele
1. Ze serveru **přehled**, klikněte na tlačítko **resetovat heslo** zobrazíte heslo resetovat okno.

   ![overview](./media/howto-create-manage-server-portal/overview.png)

2. Zadejte nové heslo a potvrzení hesla do okna, jak je znázorněno:

   ![Resetování hesla](./media/howto-create-manage-server-portal/reset-password.png)

3. Klikněte na tlačítko **OK** uložit nové heslo.

### <a name="scale-vcores-updown"></a>Škálování vCores číselník

1. Klikněte na **cenová úroveň**, který je umístěn v části **nastavení**.

2. Změna **vCore** nastavení přesunutím jezdec na požadovanou hodnotu.

    ![scale-compute](./media/howto-create-manage-server-portal/scale-compute.png)

3. Kliknutím na **OK** uložte změny.

### <a name="scale-storage-up"></a>Úložiště škálování nahoru

1. Klikněte na **cenová úroveň**, který je umístěn v části **nastavení**.

2. Změna **úložiště** nastavení přesunutím jezdec na požadovanou hodnotu.

    ![škálování úložiště](./media/howto-create-manage-server-portal/scale-storage.png)

3. Kliknutím na **OK** uložte změny.

## <a name="delete-an-azure-database-for-mysql-server"></a>Odstraňte databázi Azure pro server databáze MySQL

1. Ze serveru **přehled**, klikněte **odstranit** tlačítko otevřete výzvu k potvrzení odstranění.

    ![odstraňovat](./media/howto-create-manage-server-portal/delete.png)

2. Zadejte název serveru do vstupního pole pro potvrzení double.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm.png)

3. Klikněte **odstranit** tlačítko potvrďte odstranění serveru. Počkejte "úspěšně odstranil MySQL server" pop, až se zobrazí na panelu oznámení.

## <a name="list-the-azure-database-for-mysql-databases"></a>Seznam databáze Azure pro databází MySQL
Ze serveru **přehled**, posuňte se dolů, dokud neuvidíte databázi dlaždici v dolní části. V tabulce jsou uvedeny všechny databáze na serveru.

   ![Zobrazit databáze](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Zobrazit podrobnosti databáze Azure pro server databáze MySQL
Klikněte na **vlastnosti**, který je umístěn v části **nastavení** k zobrazení podrobných informací o serveru.

![properties](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>Další postup

[Rychlý úvod: Vytvoření databáze Azure pro server databáze MySQL pomocí portálu Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)