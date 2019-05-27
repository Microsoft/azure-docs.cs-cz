---
title: 'Azure portal: Vytvoření SQL Database managed instance | Dokumentace Microsoftu'
description: Vytvoření SQL Database managed instance, síťového prostředí a klientského virtuálního počítače pro přístup.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: f54cea75e6f7866f1be41d3b0f82393af2235a0a
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65949872"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Rychlý start: Vytvoření spravované instance Azure SQL Database

Tento rychlý start vás provede postupy vytvoření Azure SQL Database [spravovanou instanci](sql-database-managed-instance.md) na webu Azure Portal.

> [!IMPORTANT]
> Omezení, najdete v části [podporované oblasti](sql-database-managed-instance-resource-limits.md#supported-regions) a [podporované typy předplatného](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Vytvoření spravované instance

Následující kroky ukazují, jak vytvořit spravovanou instanci.

1. Vyberte **vytvořit prostředek** v levém horním rohu webu Azure portal.
2. Vyhledejte **spravovanou instanci**a pak vyberte **spravované Instance Azure SQL**.
3. Vyberte **Vytvořit**.

   ![Vytvoření spravované instance](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Vyplňte **spravovaná instance SQL** formuláře požadované informace s použitím informací v následující tabulce.

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   | **Předplatné** | Vaše předplatné. | Předplatné, které poskytuje oprávnění vytvářet nové prostředky. |
   |**Název spravované instance**|Libovolný platný název.|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Přihlašovací jméno správce spravované instance**|Libovolné platné uživatelské jméno.|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Nepoužívejte "serveradmin", protože se jedná o vyhrazené role serveru.|
   |**Heslo**|Libovolné platné heslo.|Heslo musí obsahovat nejméně 16 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Časové pásmo**|Časové pásmo má být dodržen spravované instance.|Další informace najdete v tématu [časových pásem](sql-database-managed-instance-timezone.md).|
   |**Kolace**|Kolace, kterou chcete použít pro spravovanou instanci.|Pokud provádíte migraci databáze z SQL serveru, zkontrolujte zdroje řazení pomocí `SELECT SERVERPROPERTY(N'Collation')` a tuto hodnotu použít. Informace o kolacích najdete v tématu [nastavení nebo změna kolace serveru](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|
   |**Umístění**|Umístění, ve kterém chcete vytvořit spravovanou instanci.|Informace o oblastech najdete v tématu [oblastí Azure](https://azure.microsoft.com/regions/).|
   |**Virtuální síť**|Vyberte buď **vytvořit novou virtuální síť** nebo platný virtuální sítě a podsítě.| Pokud není k dispozici síť nebo podsíť, musí být [upravit tak, aby byly splněny požadavky na síť](sql-database-managed-instance-configure-vnet-subnet.md) vybrat jako cíl pro nové spravované instance. Informace o požadavcích na konfiguraci síťového prostředí pro spravovanou instanci najdete v tématu [konfigurace virtuální sítě pro spravovanou instanci](sql-database-managed-instance-connectivity-architecture.md). |
   |**Povolení veřejného koncového bodu**   |Zaškrtněte tuto možnost k povolení veřejného koncového bodu   |Pro spravovanou instanci byla přístupná přes koncový bod veřejné datové **povolit veřejný koncový bod** musí být vráceny.| 
   |**Povolit přístup z**   |Vyberte jednu z možností: <ul> <li>**Služby Azure**</li> <li>**Internet**</li> <li>**Žádný přístup**</li></ul>   |Prostředí portálu umožňuje konfiguraci skupiny zabezpečení s veřejným koncovým bodem. </br> </br> Na základě vašeho scénáře, vyberte jednu z následujících možností: </br> <ul> <li>Služby Azure – doporučuje se, když připojení z Power BI nebo jinou víceklientskou službu. </li> <li> Internet – použijte pro účely testování, pokud chcete rychle zprovozněte managed instance. To se nedoporučuje používat v produkčním prostředí. </li> <li> Bez přístupu – tuto možnost vytvoří pravidlo odepřít zabezpečení. Bude potřeba upravit toto pravidlo, aby mohl uskutečnit spravované instance přístupné prostřednictvím veřejného koncového bodu. </li> </ul> </br> Další informace o zabezpečení veřejný koncový bod, najdete v části [využívající Azure SQL Database managed instance bezpečně s veřejným koncovým bodem](sql-database-managed-instance-public-endpoint-securely.md).|
   |**Typ připojení**|Výběr mezi proxy server a typ připojení pro přesměrování.|Další informace o typech připojení najdete v tématu [zásady připojení Azure SQL Database](sql-database-connectivity-architecture.md#connection-policy).|
   |**Skupina prostředků**|Nové nebo existující skupinu prostředků.|Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|

   ![spravovaná instance formuláře](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Použití spravované instance jako sekundární skupina převzetí služeb při selhání instance, vyberte rezervace a zadejte spravovanou instanci DnsAzurePartner. Tato funkce je ve verzi preview a není zobrazena na následujícím snímku obrazovky.
6. Vyberte **cenová úroveň** velikost výpočetní a úložnou kapacitu a projděte si možnosti cenové úrovně. Výchozí hodnotou je cenová úroveň s 32 GB paměti a 16 virtuálními jádry pro obecné účely.
7. Pomocí posuvník nebo textových polí zadejte velikost úložiště a počet virtuálních jader.
8. Jakmile budete hotovi, vyberte **použít** uložte svůj výběr. 
9. Vyberte **vytvořit** k nasazení spravované instance.
10. Vyberte **oznámení** ikonu k zobrazení stavu nasazení.

    ![průběh nasazení spravované instance](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. Vyberte **probíhá nasazení** otevřete okno spravované instance můžete dále monitorovat průběh nasazení. 

> [!IMPORTANT]
> U první instance v podsíti trvá nasazení obvykle mnohem delší než v dalších instancí. Nepřerušujte operace nasazení, protože trvá déle, než jste očekávali. Vytvoření druhého spravované instance v podsíti trvá jenom pár minut.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Kontrola prostředků a načíst vaše plně kvalifikovaný název serveru

Po úspěšném nasazení zkontrolujte prostředky, které byly vytvořeny a načíst plně kvalifikovaný název serveru pro použití v pozdější šablon rychlý start.

1. Otevřete skupinu prostředků pro spravovanou instanci. Zobrazit její prostředky, které byly vytvořeny pro vás [vytvoření managed instance](#create-a-managed-instance) rychlý start.

   ![Prostředky spravované instance](./media/sql-database-managed-instance-get-started/resources.png)

2. Vyberte tabulku směrovací tabulku trasy definované uživatelem (UDR), který byl vytvořen za vás.

   ![Tabulka směrování](./media/sql-database-managed-instance-get-started/route-table.png)

3. Ve směrovací tabulce zkontrolujte položky pro směrování provozu z a v rámci virtuální sítě spravované instance. Pokud vytváříte nebo konfigurujete směrovací tabulka ručně, musí být potřeba vytvořit tyto položky ve směrovací tabulce.

   ![Položka pro spravovanou instanci podsítě na místní](./media/sql-database-managed-instance-get-started/udr.png)

4. Vraťte se do skupiny prostředků a vyberte skupinu zabezpečení sítě k prověřte pravidla zabezpečení.

   ![Skupina zabezpečení sítě](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Prověřte pravidla zabezpečení příchozích a odchozích. Pokud jste nakonfigurovali veřejné koncové body pro spravovanou instanci, najdete v článku [nakonfigurovat veřejný koncový bod](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) Další informace.

   ![Pravidla zabezpečení](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Vraťte se do skupiny prostředků a vyberte spravovanou instanci.

   ![Spravovaná instance](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. Na **přehled** kartu, vyhledejte **hostitele** vlastnost. Zkopírujte adresu plně kvalifikovaný hostitel pro spravovanou instanci pro použití v dalším rychlém startu.

   ![Název hostitele](./media/sql-database-managed-instance-get-started/host-name.png)

   Název se podobá **your_machine_name.a1b2c3d4e5f6.database.windows.net**.

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak se připojit k managed instance:
  - Přehled možností připojení pro aplikace, najdete v části [připojení aplikací k managed instance](sql-database-managed-instance-connect-app.md).
  - Rychlý start ukazuje, jak se připojit k managed instance z virtuálního počítače Azure, najdete v části [konfigurace připojení virtuálního počítače Azure](sql-database-managed-instance-configure-vm.md).
  - Rychlý start ukazuje, jak se připojit k managed instance z místní klientských počítačů pomocí připojení point-to-site, najdete v části [konfigurace připojení typu point-to-site](sql-database-managed-instance-configure-p2s.md).
- Obnovit existující databáze SQL serveru z místního pro spravovanou instanci: 
    - Použití [Azure Database Migration Service (DMS) pro migraci](../dms/tutorial-sql-server-to-managed-instance.md) obnovit ze záložní soubor databáze. 
    - Použití [příkaz T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) obnovit ze záložní soubor databáze.
- Rozšířené monitorování výkonu databáze spravované instance s integrovanými inteligentními funkcemi pro řešení potíží, najdete v části [monitorování Azure SQL Database s využitím Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
