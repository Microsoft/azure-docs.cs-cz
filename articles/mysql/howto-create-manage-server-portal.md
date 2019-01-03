---
title: Vytvoření a správa Azure Database for MySQL pomocí webu Azure portal
description: Tento článek popisuje, jak můžete rychle vytvořit nový server Azure Database for MySQL a spravovat server pomocí webu Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 39ffe1b71fb002658110c63c825e0515f38e2b9e
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53537320"
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Vytvoření a správa Azure Database for MySQL pomocí webu Azure portal
Toto téma popisuje, jak můžete rychle vytvořit nový server Azure Database for MySQL. Obsahuje také informace o tom, jak spravovat server s využitím webu Azure portal. Správa serveru obsahuje zobrazení Podrobnosti o serveru a databáze, resetuje se heslo, škálování prostředků a odstraněním serveru.

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal
Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Vytvoření serveru Azure Database for MySQL
Následující postup použijte k vytvoření serveru Azure Database for MySQL s názvem "mydemoserver."

1. Klikněte na tlačítko **vytvořit prostředek** tlačítko umístěné v levém horním rohu webu Azure portal.

2. Na nové stránce vyberte **databází**a pak na stránce databáze, vyberte **– Azure Database for MySQL**.

    > Azure Database for MySQL serveru se vytvoří s definovanou sadou [výpočetní výkon a úložiště](./concepts-pricing-tiers.md) prostředky. Vytvoření databáze v rámci skupiny prostředků Azure a ve službě Azure Database for MySQL server.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Azure Database for MySQL – formulář vyplňte pomocí následujících informací:

    | **Pole formuláře** | **Popis pole** |
    |----------------|-----------------------|
    | *Název serveru* | mydemoserver (název serveru je globálně jedinečný) |
    | *Předplatné* | mysubscription (vyberte z rozevírací nabídky) |
    | *Skupina prostředků* | myresourcegroup (vytvořit novou skupinu prostředků nebo použijte již existující) |
    | *Výběr zdroje* | Prázdné (Vytvoření prázdné serveru MySQL) |
    | *Přihlašovací jméno správce serveru* | myadmin (název účtu správce instalace) |
    | *Heslo* | Nastavit heslo účtu správce |
    | *Potvrdit heslo* | potvrďte heslo účtu správce |
    | *Umístění* | Jihovýchodní Asie (vyberte mezi Severní Evropa a západní USA) |
    | *Verze* | 5.7 (zvolte databázi Azure pro verzi serveru MySQL) |

4. Klikněte na tlačítko **cenová úroveň** k určení úrovně vrstvy a výkon služby pro nový server. Vyberte **Obecné** kartu. *Gen 4*, *2 virtuální jádra*, *5 GB* a *7 dní* jsou výchozí hodnoty pro **Výpočetní generaci**, **Virtuální jádra**, **Úložiště** a **Období uchování zálohy**. Můžete ponechat tyto posuvníky tak, jak jsou. Pokud chcete povolit zálohování serveru v geograficky redundantním úložišti, vyberte v **Možnosti redundance zálohy** možnost **Geograficky redundantní**.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Klikněte na **Vytvořit**, aby se server zřídil. Zřizování trvá několik minut.

    > Vyberte **připnout na řídicí panel** možnost, povolíte snadné sledování vašich nasazení.

## <a name="update-an-azure-database-for-mysql-server"></a>Aktualizace serveru Azure Database for MySQL
Po zřízení nového serveru se uživatel má několik možností, jak ke konfiguraci existující server, včetně resetování hesla správce a škálování směrem nahoru nebo dolů na serveru tak, že změníte vCore a úložiště.

### <a name="change-the-administrator-user-password"></a>Změna hesla správce uživatele
1. Ze serveru **přehled**, klikněte na tlačítko **resetovat heslo** zobrazíte okno pro resetování hesla.

   ![overview](./media/howto-create-manage-server-portal/overview.png)

2. Zadejte nové heslo a potvrzení hesla v okně, jak je znázorněno:

   ![resetování hesla](./media/howto-create-manage-server-portal/reset-password.png)

3. Klikněte na tlačítko **OK** nové heslo uložte.

### <a name="scale-vcores-updown"></a>Virtuální jádra pro škálování směrem nahoru nebo dolů

1. Klikněte na **cenová úroveň**, který je umístěn v rámci **nastavení**.

2. Změnit **vCore** nastavení přesunete posuvník na požadovanou hodnotu.

    ![škálujte kapacitu výpočetních prostředků](./media/howto-create-manage-server-portal/scale-compute.png)

3. Kliknutím na **OK** uložte změny.

### <a name="scale-storage-up"></a>Úložiště o velikosti až

1. Klikněte na **cenová úroveň**, který je umístěn v rámci **nastavení**.

2. Změnit **úložiště** nastavení přesunete posuvník na požadovanou hodnotu.

    ![škálování úložiště](./media/howto-create-manage-server-portal/scale-storage.png)

3. Kliknutím na **OK** uložte změny.

## <a name="delete-an-azure-database-for-mysql-server"></a>Odstranění serveru Azure Database for MySQL

1. Ze serveru **přehled**, klikněte na tlačítko **odstranit** tlačítko Otevřít výzvu k potvrzení odstranění.

    ![delete](./media/howto-create-manage-server-portal/delete.png)

2. Zadejte název serveru, do vstupního pole pro potvrzení double.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm.png)

3. Klikněte na tlačítko **odstranit** potvrďte odstranění serveru. "Byl úspěšně odstraněn server MySQL" pop čekat až se zobrazí v oznamovacím pruhu.

## <a name="list-the-azure-database-for-mysql-databases"></a>Seznam Azure Database for MySQL – databáze
Ze serveru **přehled**, posuňte se dolů, dokud se nezobrazí databáze v dolní části dlaždice. Všechny databáze na serveru jsou uvedené v tabulce.

   ![zobrazení databáze](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Zobrazit podrobnosti o serveru Azure Database for MySQL
Klikněte na **vlastnosti**, který je umístěn v rámci **nastavení** Chcete-li zobrazit podrobné informace o serveru.

![properties](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>Další postup

[Rychlý start: Vytvoření Azure Database for MySQL pomocí webu Azure portal](./quickstart-create-mysql-server-database-using-azure-portal.md)