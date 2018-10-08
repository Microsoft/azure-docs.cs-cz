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
ms.date: 09/23/2018
ms.openlocfilehash: c0c249ffe426e86049024122d9cbf786bb677220
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47160634"
---
# <a name="create-an-azure-sql-database-managed-instance"></a>Vytvoření spravované instance Azure SQL Database

V tomto rychlém startu se dozvíte, jak na webu Azure Portal vytvořit [spravovanou instanci](sql-database-managed-instance.md) Azure SQL Database. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Vytvoření Managed Instance

Následujícím postupem vytvoříte spravovanou instanci.

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Najděte **spravovanou instanci** a vyberte **Spravovaná instance Azure SQL**.
3. Klikněte na možnost **Vytvořit**.

   ![Vytvoření spravované instance](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Ve formuláři Managed Instance vyplňte požadované informace s použitím informací v následující tabulce:

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   | **Předplatné** | Vaše předplatné | Předplatné, ve kterém máte oprávnění vytvářet nové prostředky |
   |**Název spravované instance**|Libovolné platné jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Přihlašovací jméno správce spravované instance**|Jakékoli platné uživatelské jméno|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Nepoužívejte „serveradmin“, protože tento název je vyhrazená role na úrovni serveru.| 
   |**Heslo**|Libovolné platné heslo|Heslo musí obsahovat nejméně 16 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Skupina prostředků**|Nová nebo stávající skupina prostředků|Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Umístění**|Umístění, ve kterém chcete vytvořit spravovanou instanci|Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/).|
   |**Virtuální síť**|Vyberte **Vytvořit novou virtuální síť** nebo vyberte virtuální síť, kterou jste vytvořili dříve ve skupině prostředků zadaných v tomto formuláři.| Pokud chcete při konfiguraci virtuální sítě pro spravovanou instanci použít vlastní nastavení, přečtěte si článek o [konfiguraci šablony prostředí virtuální sítě pro spravovanou instanci SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) v Githubu. Informace o požadavcích na konfiguraci síťového prostředí pro spravovanou instanci najdete v článku o [konfiguraci virtuální sítě pro spravovanou instanci Azure SQL Database](sql-database-managed-instance-vnet-configuration.md). |

   ![formulář spravované instance](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Klikněte na **Cenová úroveň** a určete velikost výpočetních prostředků a prostředků úložiště a prohlédněte si možnosti cenové úrovně. Výchozí hodnotou je cenová úroveň s 32 GB paměti a 16 virtuálními jádry pro obecné účely.
6. Pomocí posuvník nebo textových polí zadejte velikost úložiště a počet virtuálních jader. 
7. Jakmile budete hotovi, kliknutím na **Použít** uložte provedený výběr.  
8. Kliknutím na **Vytvořit** nasaďte Managed Instance.
9. Kliknutím na ikonu **Oznámení** zobrazte stav nasazení.

    ![průběh nasazení spravované instance](./media/sql-database-managed-instance-get-started/deployment-progress.png)

10. Kliknutím na **Probíhá nasazení** otevřete okno Managed Instance, kde můžete dále monitorovat průběh nasazení. 

> [!IMPORTANT]
> Čas nasazení první instance v podsíti je obvykle mnohem delší než v případě dalších instancí. Nerušte operaci nasazování jenom proto, že trvá déle, než jste očekávali. Vytvoření druhé spravované instance v podsíti zabere jenom pár minut.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Kontrola prostředků a načtení plně kvalifikovaného názvu serveru

Jakmile úspěšně dokončíte nasazení, zkontrolujte vytvořené prostředky a získejte plně kvalifikovaný název serveru, abyste ho mohli použít v dalších rychlých startech.

1. Otevřete skupinu prostředků spravované instance a prohlédněte si prostředky, které pro vás byly vytvořeny v rychlém startu o [vytvoření spravované instance](sql-database-managed-instance-get-started.md).

   ![Prostředky spravované instance](./media/sql-database-managed-instance-get-started/resources.png)Na webu Azure Portal otevřete prostředek své spravované instance.

2. Klikněte na spravovanou instanci.
3. Na kartě **Přehled** přejděte k vlastnosti **Hostitel** a zkopírujte si plně kvalifikovanou adresu hostitele spravované instance.


   ![Prostředky spravované instance](./media/sql-database-managed-instance-get-started/host-name.png)

   Jde o podobný název, jako je tento: **nazev_vaseho_pocitace.neu15011648751ff.database.windows.net**.

## <a name="next-steps"></a>Další kroky

- Další informace o připojení ke spravované instanci najdete zde:
  - Přehled možností připojení aplikací najdete v článku o [připojení aplikací ke spravované instanci](sql-database-managed-instance-connect-app.md).
  - Rychlý start o připojení ke spravované instanci z virtuálního počítače Azure najdete v článku o [konfiguraci připojení virtuálního počítače Azure](sql-database-managed-instance-configure-vm.md).
  - Rychlý start s návodem, jak se z místního klientského počítače připojit ke spravované instanci pomocí připojení typu point-to-site, najdete v článku o [konfiguraci připojení typu point-to-site](sql-database-managed-instance-configure-p2s.md).
- Pokud chcete obnovit stávající databázi SQL Serveru z místní do spravované instance, můžete k migraci použít [službu Azure Database Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) a obnovit zálohu databáze nebo můžete k obnovení zálohy databáze použít [příkaz T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md).
