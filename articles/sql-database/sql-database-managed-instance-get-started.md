---
title: 'Azure portal: Vytvoření spravované instance SQL | Dokumentace Microsoftu'
description: Vytvoření SQL managed instance, síťového prostředí a klientského virtuálního počítače pro přístup.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: Carlrab
manager: craigg
ms.date: 02/18/2019
ms.openlocfilehash: 481c82eb74bcf80c3d0546324009ec0bf6495cfb
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587051"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Rychlý start: Vytvoření spravované instance Azure SQL Database

Tento rychlý start vás provede postupem vytvoření Azure SQL Database [spravovanou instanci](sql-database-managed-instance.md) na webu Azure Portal.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Vytvoření spravované instance

Následující kroky ukazují, jak vytvořit spravovanou instanci.

1. V levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.
2. Vyhledejte **spravovanou instanci** a pak vyberte **spravované Instance Azure SQL**.
3. Vyberte **Vytvořit**.

   ![Vytvoření spravované instance](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Vyplňte **spravovaná instance SQL** ve formuláři požadované informace s použitím informací v následující tabulce:

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   | **Předplatné** | Vaše předplatné | Předplatné, ve kterém máte oprávnění vytvářet nové prostředky |
   |**Název spravované instance**|Libovolné platné jméno|Platné názvy najdete v tématu [pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Přihlašovací jméno správce spravované instance**|Jakékoli platné uživatelské jméno|Platné názvy najdete v tématu [pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Nepoužívejte "serveradmin", protože to je vyhrazené serverové role.|
   |**Heslo**|Libovolné platné heslo|Heslo musí obsahovat nejméně 16 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Kolace**|Kolace, kterou chcete použít pro spravovanou instanci|Pokud se migrace databáze z SQL serveru, zkontrolujte řazení zdroje s použitím `SELECT SERVERPROPERTY(N'Collation')` a tuto hodnotu použít. Informace o kolacích najdete v tématu [serverové kolace](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|
   |**Umístění**|Umístění, ve kterém chcete vytvořit spravovanou instanci|Informace o oblastech najdete v tématu [oblastí Azure](https://azure.microsoft.com/regions/).|
   |**Virtuální síť**|Vyberte buď **vytvořit novou virtuální síť** nebo platný virtuální sítě a podsítě.| Pokud síť/podsíť je zobrazené šedě je musí být [upravit tak, aby byly splněny požadavky na síť](sql-database-managed-instance-configure-vnet-subnet.md) vybrat jako cíl pro nové spravované instance. Informace ohledně požadavků na konfiguraci síťového prostředí pro spravovanou instanci najdete v tématu [konfigurace virtuální sítě pro spravovanou instanci](sql-database-managed-instance-connectivity-architecture.md). |
   |**Skupina prostředků**|Nová nebo stávající skupina prostředků|Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|

   ![formulář spravované instance](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Použití spravované instance jako sekundární skupina převzetí služeb při selhání instance, vyberte rezervace a zadejte spravovanou instanci DnsAzurePartner. Tato funkce je ve verzi preview a není vidět doprovodné snímku obrazovky.
6. Vyberte **cenová úroveň** velikost výpočetních a úložných kapacit také projděte si možnosti cenové úrovně. Výchozí hodnotou je cenová úroveň s 32 GB paměti a 16 virtuálními jádry pro obecné účely.
7. Pomocí posuvník nebo textových polí zadejte velikost úložiště a počet virtuálních jader.
8. Jakmile budete hotovi, zvolte **použít** uložte svůj výběr.  
9. Vyberte **vytvořit** k nasazení spravované instance.
10. Vyberte **oznámení** ikonu k zobrazení stavu nasazení.

    ![průběh nasazení spravované instance](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. Vyberte **probíhá nasazení** otevřete okno spravované instance můžete dále monitorovat průběh nasazení.

> [!IMPORTANT]
> U první instance v podsíti trvá nasazení obvykle mnohem delší než v dalších instancí. Nepřerušujte operace nasazení, protože trvá déle, než jste očekávali. Vytvoření druhého spravované instance v podsíti trvá jenom pár minut.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Kontrola prostředků a načíst vaše plně kvalifikovaný název serveru

Jakmile úspěšně dokončíte nasazení, zkontrolujte vytvořené prostředky a získejte plně kvalifikovaný název serveru, abyste ho mohli použít v dalších rychlých startech.

1. Otevřete skupinu prostředků pro spravovanou instanci a zobrazte její prostředky, které byly vytvořeny pro vás [vytvoření managed instance](#create-a-managed-instance) rychlý start.

   ![Prostředky spravované instance](./media/sql-database-managed-instance-get-started/resources.png)

2. Vybrat tabulku směrování ke kontrole uživatelsky definované trase UDR) tabulku, která se pro vás vytvořil.

   ![Tabulka směrování](./media/sql-database-managed-instance-get-started/route-table.png)

3. Ve směrovací tabulce zkontrolujte položky pro směrování provozu z a v rámci virtuální sítě spravované instance. Pokud provádíte tvorbu nebo ruční konfiguraci směrovací tabulky, musí být potřeba vytvořit tyto položky ve směrovací tabulce.

   ![Položka pro MI do místní podsítě](./media/sql-database-managed-instance-get-started/udr.png)

4. Vraťte se do skupiny prostředků a vyberte skupinu zabezpečení sítě k prověřte pravidla zabezpečení.

   ![Network-security-group](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Prověřte pravidla zabezpečení příchozích a odchozích.

   ![Pravidla zabezpečení](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Vraťte se do skupiny prostředků a vyberte spravovanou instanci.

   ![Spravovaná instance](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. Na **přehled** kartu, vyhledejte **hostitele** vlastnosti a zkopírujte adresu plně kvalifikovaný hostitel pro spravovanou instanci pro použití v dalším rychlém startu.

   ![Název hostitele](./media/sql-database-managed-instance-get-started/host-name.png)

   Název se podobá **your_machine_name.a1b2c3d4e5f6.database.windows.net**.

## <a name="next-steps"></a>Další postup

- Další informace o připojení k managed instance najdete v tématu:
  - Přehled možností připojení pro aplikace, najdete v části [připojení aplikací k managed instance](sql-database-managed-instance-connect-app.md).
  - Rychlý start ukazuje, jak připojit k managed instance z virtuálního počítače Azure, najdete v části [konfigurace připojení virtuálního počítače Azure](sql-database-managed-instance-configure-vm.md).
  - Rychlý start ukazuje, jak připojit k managed instance z místní klientských počítačů pomocí připojení point-to-site, najdete v části [konfigurace připojení typu point-to-site](sql-database-managed-instance-configure-p2s.md).
- Pokud chcete obnovit stávající databázi SQL Serveru z místní do spravované instance, můžete k migraci použít [službu Azure Database Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) a obnovit zálohu databáze nebo můžete k obnovení zálohy databáze použít [příkaz T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md).
- Rozšířené monitorování výkonu databáze spravované instance s integrovanými inteligentními funkcemi pro řešení potíží, najdete v části [monitorování Azure SQL Database pomocí Azure SQL Analytics](../azure-monitor/insights/azure-sql.md)
