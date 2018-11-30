---
title: 'Azure Portal: Vytvoření spravované instance SQL | Microsoft Docs'
description: Vytvoříte spravovanou instanci SQL, síťové prostředí a klientský virtuální počítač pro přístup.
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
ms.date: 11/28/2018
ms.openlocfilehash: b7a3fc6e5dafb59d6981ff4302d4b060b0c73d6b
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52499241"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Rychlý start: Vytvoření spravované instance Azure SQL Database

V tomto rychlém startu se dozvíte, jak na webu Azure Portal vytvořit [spravovanou instanci](sql-database-managed-instance.md) Azure SQL Database. 

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Vytvoření Managed Instance

Následujícím postupem vytvoříte spravovanou instanci.

1. V levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.
2. Najděte **spravovanou instanci** a vyberte **Spravovaná instance Azure SQL**.
3. Vyberte **Vytvořit**.

   ![Vytvoření spravované instance](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Vyplňte **Managed Instance** ve formuláři požadované informace s použitím informací v následující tabulce:

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   | **Předplatné** | Vaše předplatné | Předplatné, ve kterém máte oprávnění vytvářet nové prostředky |
   |**Název spravované instance**|Libovolné platné jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Přihlašovací jméno správce spravované instance**|Jakékoli platné uživatelské jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Nepoužívejte "serveradmin", protože to je vyhrazené serverové role.| 
   |**Heslo**|Libovolné platné heslo|Heslo musí obsahovat nejméně 16 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Umístění**|Umístění, ve kterém chcete vytvořit spravovanou instanci|Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/).|
   |**Virtuální síť**|Vyberte buď **vytvořit novou virtuální síť** nebo virtuální sítě, kterou jste vytvořili ve skupině prostředků, které jste zadali dříve v tomto formuláři.| Pokud chcete při konfiguraci virtuální sítě pro spravovanou instanci použít vlastní nastavení, přečtěte si článek o [konfiguraci šablony prostředí virtuální sítě pro spravovanou instanci SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) v Githubu. Informace ohledně požadavků na konfiguraci síťového prostředí pro Managed Instance najdete v tématu [konfigurace virtuální sítě pro Azure SQL Database Managed Instance](sql-database-managed-instance-vnet-configuration.md). |
   |**Skupina prostředků**|Nová nebo stávající skupina prostředků|Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|

   ![formulář spravované instance](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Vyberte **cenová úroveň** velikost výpočetních a úložných kapacit také projděte si možnosti cenové úrovně. Výchozí hodnotou je cenová úroveň s 32 GB paměti a 16 virtuálními jádry pro obecné účely.
6. Pomocí posuvník nebo textových polí zadejte velikost úložiště a počet virtuálních jader. 
7. Jakmile budete hotovi, zvolte **použít** uložte svůj výběr.  
8. Vyberte **vytvořit** k nasazení spravované Instance.
9. Vyberte **oznámení** ikonu k zobrazení stavu nasazení.

    ![průběh nasazení spravované instance](./media/sql-database-managed-instance-get-started/deployment-progress.png)

10. Vyberte **probíhá nasazení** otevřete okno Managed Instance můžete dále monitorovat průběh nasazení. 

> [!IMPORTANT]
> U první instance v podsíti trvá nasazení obvykle mnohem delší než v dalších instancí. Nepřerušujte operace nasazení, protože trvá déle, než jste očekávali. Vytvoření druhé spravované instance v podsíti zabere jenom pár minut.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Kontrola prostředků a načíst vaše plně kvalifikovaný název serveru

Jakmile úspěšně dokončíte nasazení, zkontrolujte vytvořené prostředky a získejte plně kvalifikovaný název serveru, abyste ho mohli použít v dalších rychlých startech.

1. Otevřete skupinu prostředků spravované instance a prohlédněte si prostředky, které pro vás byly vytvořeny v rychlém startu o [vytvoření spravované instance](#create-a-managed-instance).

2. Vyberte spravované Instance.

   ![Prostředky spravované instance](./media/sql-database-managed-instance-get-started/resources.png)

3. Na **přehled** kartu, vyhledejte **hostitele** vlastnosti a zkopírujte plně kvalifikovaný hostitel adres pro Managed Instance.


   ![Prostředky spravované instance](./media/sql-database-managed-instance-get-started/host-name.png)

   Název se podobá **your_machine_name.a1b2c3d4e5f6.database.windows.net**.

## <a name="next-steps"></a>Další postup

- Další informace o připojení ke spravované instanci najdete zde:
  - Přehled možností připojení aplikací najdete v článku o [připojení aplikací ke spravované instanci](sql-database-managed-instance-connect-app.md).
  - Rychlý start o připojení ke spravované instanci z virtuálního počítače Azure najdete v článku o [konfiguraci připojení virtuálního počítače Azure](sql-database-managed-instance-configure-vm.md).
  - Rychlý start s návodem, jak se z místního klientského počítače připojit ke spravované instanci pomocí připojení typu point-to-site, najdete v článku o [konfiguraci připojení typu point-to-site](sql-database-managed-instance-configure-p2s.md).
- Pokud chcete obnovit stávající databázi SQL Serveru z místní do spravované instance, můžete k migraci použít [službu Azure Database Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) a obnovit zálohu databáze nebo můžete k obnovení zálohy databáze použít [příkaz T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md).
- Informace o pokročilém monitorování výkonu databáze spravované instance s využitím integrovaných inteligentních funkcí pro řešení potíží najdete v tématu [Monitorování služby Azure SQL Database pomocí Azure SQL Analytics](../log-analytics/log-analytics-azure-sql.md).
