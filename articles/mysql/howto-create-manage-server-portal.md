---
title: Vytvoření a Správa Azure Database for MySQL serveru pomocí Azure Portal
description: Tento článek popisuje, jak můžete rychle vytvořit nový Azure Database for MySQL server a spravovat server pomocí Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: fdcb302d3a14b02ea86fb92c8dbf822ef3f42177
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142138"
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Vytvoření a Správa Azure Database for MySQL serveru pomocí Azure Portal
Toto téma popisuje, jak můžete rychle vytvořit nový Azure Database for MySQL server. Obsahuje také informace o tom, jak spravovat server pomocí Azure Portal. Správa serveru zahrnuje zobrazení podrobností serveru a databází, resetování hesla, škálování prostředků a odstranění serveru.

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal
Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Vytvoření serveru Azure Database for MySQL
Pomocí těchto kroků vytvořte Azure Database for MySQL server s názvem "mydemoserver".

1. Klikněte na tlačítko **vytvořit prostředek** umístěný v levém horním rohu Azure Portal.

2. Na stránce Nový vyberte **databáze**a potom na stránce databáze vyberte možnost **Azure Database for MySQL**.

    > Server Azure Database for MySQL se vytvoří s definovanou sadou výpočetních prostředků [a prostředků úložiště](./concepts-pricing-tiers.md) . Databáze se vytvoří v rámci skupiny prostředků Azure a na serveru Azure Database for MySQL.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Vyplňte Azure Database for MySQL formuláře pomocí následujících informací:

    | **Pole formuláře** | **Popis pole** |
    |----------------|-----------------------|
    | *Název serveru* | mydemoserver (název serveru je globálně jedinečný) |
    | *Předplatné* | mysubscription (výběr z rozevírací nabídky) |
    | *Skupina prostředků* | myresourcegroup (vytvoření nové skupiny prostředků nebo použití existujícího) |
    | *Výběr zdroje* | Prázdné (Vytvoření prázdného serveru MySQL) |
    | *Přihlašovací jméno správce serveru* | myadmin (název účtu správce instalace) |
    | *Heslo* | nastavit heslo účtu správce |
    | *Potvrdit heslo* | potvrďte heslo účtu správce |
    | *Location* | Jihovýchodní Asie (výběr mezi Severní Evropa a Západní USA) |
    | *Verze* | 5,7 (vyberte verzi serveru Azure Database for MySQL) |

   ![create-new-server](./media/howto-create-manage-server-portal/form-field.png)

4. Klikněte na **cenová úroveň** a zadejte úroveň služby a úroveň výkonu pro nový server. Vyberte kartu **pro obecné účely** . *Gen 5*, *2 virtuální jádra*, *5 GB* a *7 dní* jsou výchozí hodnoty pro **Výpočetní generaci**, **Virtuální jádra**, **Úložiště** a **Období uchování zálohy**. Můžete ponechat tyto posuvníky tak, jak jsou. Pokud chcete povolit zálohování serveru v geograficky redundantním úložišti, vyberte v **Možnosti redundance zálohy** možnost **Geograficky redundantní**.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Klikněte na **Vytvořit**, aby se server zřídil. Zřizování trvá několik minut.

    > Vyberte možnost **Připnout na řídicí panel** , abyste povolili snadné sledování vašich nasazení.

## <a name="update-an-azure-database-for-mysql-server"></a>Aktualizace serveru Azure Database for MySQL
Po zřízení nového serveru má uživatel několik možností konfigurace existujícího serveru, včetně resetování hesla správce, změny cenové úrovně a škálování serveru nahoru nebo dolů změnou vCore nebo úložiště.

### <a name="change-the-administrator-user-password"></a>Změna hesla uživatele správce
1. V **přehledu**serveru klikněte na **resetovat heslo** , aby se zobrazilo okno pro resetování hesla.

   ![přehled](./media/howto-create-manage-server-portal/overview.png)

2. Zadejte nové heslo a potvrďte heslo v okně, jak je znázorněno níže:

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Kliknutím na tlačítko **OK** uložte nové heslo.

### <a name="change-the-pricing-tier"></a>Změna cenové úrovně
> [!NOTE]
> Škálování se podporuje jenom z Pro obecné účely na paměťově optimalizované úrovně služeb a naopak. Po vytvoření serveru v Azure Database for MySQL nezapomeňte změnit na cenovou úroveň Basic a na ni.
> 
1. Klikněte na **cenová úroveň**, která je umístěná v části **Nastavení**.
2. Vyberte **cenovou úroveň** , na kterou chcete přejít.

    ![Změna – cenová úroveň](./media/howto-create-manage-server-portal/change-pricing-tier.png)

4. Kliknutím na **OK** uložte změny. 

### <a name="scale-vcores-updown"></a>Škálovat virtuální jádra nahoru/dolů

1. Klikněte na **cenová úroveň**, která je umístěná v části **Nastavení**.

2. Změňte nastavení **Vcore** přesunutím posuvníku na požadovanou hodnotu.

    ![škálování – COMPUTE](./media/howto-create-manage-server-portal/scale-compute.png)

3. Kliknutím na **OK** uložte změny.

### <a name="scale-storage-up"></a>Škálování úložiště nahoru

1. Klikněte na **cenová úroveň**, která je umístěná v části **Nastavení**.

2. Změňte nastavení **úložiště** přesunutím posuvníku na požadovanou hodnotu.

    ![škálování – úložiště](./media/howto-create-manage-server-portal/scale-storage.png)

3. Kliknutím na **OK** uložte změny.

## <a name="delete-an-azure-database-for-mysql-server"></a>Odstranění serveru Azure Database for MySQL

1. V **přehledu**serveru klikněte na tlačítko **Odstranit** a otevřete tak výzvu k potvrzení odstranění.

    ![odstraňovat](./media/howto-create-manage-server-portal/delete.png)

2. Do vstupního pole zadejte název serveru pro dvojité potvrzení.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm.png)

3. Kliknutím na tlačítko **Odstranit** potvrďte odstranění serveru. Počkejte, než se na oznamovacím panelu zobrazí překryvný Server serveru MySQL.

## <a name="list-the-azure-database-for-mysql-databases"></a>Výpis databází Azure Database for MySQL
V **přehledu**serveru se posuňte dolů, dokud se v dolní části nezobrazí dlaždice databáze. Všechny databáze na serveru jsou uvedené v tabulce.

   ![Zobrazit databáze](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Zobrazit podrobnosti Azure Database for MySQLho serveru
Kliknutím na **vlastnosti**v části **Nastavení** zobrazíte podrobné informace o serveru.

![properties](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>Další postup

[Rychlé zprovoznění: Vytvoření serveru Azure Database for MySQL pomocí Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)