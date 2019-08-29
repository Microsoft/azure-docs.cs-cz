---
title: 'Kurz: Přidání elastického fondu Azure SQL Database do skupiny převzetí služeb při selhání | Microsoft Docs'
description: Přidejte Azure SQL Database elastický fond do skupiny převzetí služeb při selhání pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 2d46e6f1d5c7079ab5bbfea39a85ea0a7592afc8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099345"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Kurz: Přidání elastického fondu Azure SQL Database do skupiny převzetí služeb při selhání

Nakonfiguruje skupinu převzetí služeb při selhání pro elastický fond Azure SQL Database a testovací převzetí služeb při selhání pomocí Azure Portal.  V tomto kurzu se naučíte, jak:

> [!div class="checklist"]
> - Vytvořte Azure SQL Database izolovanou databázi.
> - Přidejte do elastického fondu jednu databázi. 
> - Vytvořte [skupinu převzetí služeb při selhání](sql-database-auto-failover-group.md) pro elastický fond mezi dvěma logickými servery SQL.
> - Testovací převzetí služeb při selhání.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující: 

- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) .


## <a name="1---create-a-single-database"></a>1\. vytvoření samostatné databáze 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2 – přidat izolovanou databázi do elastického fondu

1. V nabídce na levé straně Azure Portal vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a do vyhledávacího pole zadejte Azure SQL. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu. 
1. Výběrem **+ Přidat** otevřete stránku **vybrat možnost nasazení SQL** . Další informace o různých databázích můžete zobrazit tak, že na dlaždici databáze vyberete Zobrazit podrobnosti.
1. V rozevíracím seznamu **typ prostředku** na dlaždici **databáze SQL** vyberte **elastický fond** . Vyberte **vytvořit** a vytvořte tak elastický fond. 

    ![Vybrat elastický fond](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. Nastavte elastický fond s následujícími hodnotami:
   - **Název**: Zadejte jedinečný název elastického fondu, například `myElasticPool`. 
   - **Předplatné:** Vyberte vaše předplatné z rozevíracího seznamu.
   - **ResourceGroup**: V `myResourceGroup` rozevíracím seznamu vyberte skupinu prostředků, kterou jste vytvořili v části 1. 
   - **Server**: V rozevíracím seznamu vyberte server, který jste vytvořili v části 1.  

       ![Vytvořit nový server pro elastický fond](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Výpočty a úložiště**: Vyberte **Konfigurovat elastický fond** pro konfiguraci výpočetních prostředků, úložiště a přidejte svoji databázi do elastického fondu. Na kartě **nastavení fondu** ponechte výchozí hodnotu Gen5 se 2 virtuální jádra a 32 GB. 

1. Na stránce **Konfigurace** vyberte kartu **databáze** a pak zvolte možnost **Přidat databázi**. Zvolte databázi, kterou jste vytvořili v části 1, a pak vyberte **použít** pro přidání do elastického fondu. Pokud chcete použít nastavení elastického fondu a zavřít stránku **Konfigurace** , vyberte **použít** znovu. 

    ![Přidat databázi SQL do elastického fondu](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Výběrem možnosti **zkontrolovat + vytvořit** zkontrolujte nastavení elastického fondu a potom vyberte **vytvořit** a vytvořte tak elastický fond. 


## <a name="3---create-the-failover-group"></a>3\. Vytvoření skupiny převzetí služeb při selhání 
V tomto kroku vytvoříte [skupinu převzetí služeb při selhání](sql-database-auto-failover-group.md) mezi existujícím serverem SQL Azure a novým serverem SQL Azure v jiné oblasti. Pak přidejte elastický fond do skupiny převzetí služeb při selhání. 

1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a do vyhledávacího pole zadejte Azure SQL. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu. 
1. Vyberte elastický fond vytvořený v předchozí části, například `myElasticPool`. 
1. V podokně **Přehled** vyberte název serveru v části **název serveru** a otevřete tak nastavení serveru.
  
    ![Otevřít server pro elastický fond](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. V podokně **Nastavení** vyberte **skupiny převzetí služeb při selhání** a pak vyberte **Přidat skupinu** a vytvořte novou skupinu převzetí služeb při selhání. 

    ![Přidat novou skupinu převzetí služeb při selhání](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. Na stránce **Skupina převzetí služeb při selhání** zadejte nebo vyberte následující hodnoty a pak vyberte **vytvořit**:
    - **Název skupiny převzetí služeb při selhání**: Zadejte jedinečný název skupiny převzetí služeb při selhání, `failovergrouptutorial`například. 
    - **Sekundární server**: Vyberte možnost *Konfigurace požadovaných nastavení* a pak zvolte **Vytvoření nového serveru**. Alternativně můžete zvolit již existující server jako sekundární server. Po zadání následujících hodnot pro nový sekundární server vyberte **Vybrat**. 
        - **Název serveru**: Zadejte jedinečný název sekundárního serveru, například `mysqlsecondary`. 
        - **Přihlašovací jméno správce serveru**: Textový`azureuser`
        - **Heslo**: Zadejte komplexní heslo, které splňuje požadavky na heslo.
        - **Umístění**: V rozevíracím seznamu vyberte umístění, například `East US`. Toto umístění nemůže být stejné jako primární server.

       > [!NOTE]
       > Přihlašovací údaje serveru a firewall se musí shodovat s nastavením vašeho primárního serveru. 
    
       ![Vytvoření sekundárního serveru pro skupinu převzetí služeb při selhání](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. Vyberte **databáze ve skupině** a potom vyberte elastický fond, který jste vytvořili v části 2. Mělo by se zobrazit upozornění, které vás vyzve k vytvoření elastického fondu na sekundárním serveru. Vyberte upozornění a pak vyberte **OK** a vytvořte elastický fond na sekundárním serveru. 
        
    ![Přidat elastický fond do skupiny převzetí služeb při selhání](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Vyberte **možnost vyberte** , pokud chcete nastavení elastického fondu použít pro skupinu převzetí služeb při selhání, a pak vyberte **vytvořit** a vytvořte skupinu převzetí služeb při selhání. Přidáním elastického fondu do skupiny převzetí služeb při selhání se automaticky spustí proces geografické replikace. 


## <a name="4---test-failover"></a>4\. testovací převzetí služeb při selhání 
V tomto kroku dojde k selhání skupiny převzetí služeb při selhání pro sekundární server a následnému navrácení služeb po obnovení pomocí Azure Portal. 

1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a do vyhledávacího pole zadejte Azure SQL. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu. 
1. Vyberte elastický fond vytvořený v předchozí části, například `myElasticPool`. 
1. Kliknutím na název serveru v části **název serveru** otevřete nastavení serveru.

    ![Otevřít server pro elastický fond](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. V podokně **Nastavení** vyberte **skupiny převzetí služeb při selhání** a pak vyberte skupinu převzetí služeb při selhání, kterou jste vytvořili v části 2. 
  
   ![Výběr skupiny převzetí služeb při selhání z portálu](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. Zkontrolujte, který server je primární a který server je sekundární. 
1. V podokně úloh vyberte **převzetí služeb při** selhání pro skupinu převzetí služeb při selhání, která obsahuje váš elastický fond. 
1. U upozornění, které vás upozorní na to, že relace TDS budou odpojeny, vyberte **Ano** . 

   ![Převzetí služeb při selhání ve skupině, která obsahuje vaše databáze SQL](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. Zkontrolujte, který server je primární a který server je sekundární. Pokud se převzetí služeb při selhání úspěšně převedlo, musí mít dva servery zaměnitelné role. 
1. Znovu vyberte **převzetí služeb při** selhání, abyste skupinu převzetí služeb při selhání mohli obnovit původní nastavení. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 
Vyčistěte prostředky odstraněním skupiny prostředků. 

1. Přejděte do skupiny prostředků v [Azure Portal](https://portal.azure.com).
1. Vyberte **Odstranit skupinu prostředků** a odstraňte všechny prostředky ve skupině a také samotnou skupinu prostředků. 
1. Zadejte název skupiny `myResourceGroup`prostředků, v textovém poli a pak vyberte **Odstranit** a odstraňte skupinu prostředků.  


## <a name="next-steps"></a>Další postup

V tomto kurzu jste do skupiny převzetí služeb při selhání přidali izolovanou databázi Azure SQL Database a otestujete převzetí služeb při selhání. Naučili jste se tyto postupy:

> [!div class="checklist"]
> - Vytvořte Azure SQL Database izolovanou databázi.
> - Přidejte do elastického fondu jednu databázi. 
> - Vytvořte [skupinu převzetí služeb při selhání](sql-database-auto-failover-group.md) pro elastický fond mezi dvěma logickými servery SQL.
> - Testovací převzetí služeb při selhání.

Přejděte k dalšímu kurzu migrace pomocí DMS.

> [!div class="nextstepaction"]
> [Kurz: Migrace SQL Server do fondu databáze pomocí DMS](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
