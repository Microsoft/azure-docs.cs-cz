---
title: 'Rychlý Start: vytvoření spravované instance Azure SQL (portál)'
description: Vytvořte spravovanou instanci, síťové prostředí a klientský virtuální počítač pro přístup pomocí Azure Portal v tomto rychlém startu.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 1/29/2021
ms.openlocfilehash: d356cad1b4754875574e19be732fdf6481c61e22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101691208"
---
# <a name="quickstart-create-an-azure-sql-managed-instance"></a>Rychlý Start: vytvoření spravované instance Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

V tomto rychlém startu se naučíte vytvořit [spravovanou instanci Azure SQL](sql-managed-instance-paas-overview.md) v Azure Portal.

> [!IMPORTANT]
> Omezení najdete v tématu [podporované oblasti](resource-limits.md#supported-regions) a [podporované typy předplatného](resource-limits.md#supported-subscription-types).

## <a name="create-an-azure-sql-managed-instance"></a>Vytvoření spravované instance Azure SQL

K vytvoření spravované instance SQL použijte následující postup: 

### <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Pokud nemáte předplatné Azure, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. V levé nabídce Azure Portal vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby** a pak do vyhledávacího pole zadejte **Azure SQL** .
1. Výběrem **+ Přidat** otevřete stránku **vybrat možnost nasazení SQL** . Další informace o spravované instanci Azure SQL získáte tak, že na dlaždici **spravované instance SQL** vyberete **Zobrazit podrobnosti** .
1. Vyberte **Vytvořit**.

   ![Vytvoření spravované instance](./media/instance-create-quickstart/create-azure-sql-managed-instance.png)

4. K přidání požadovaných a volitelných informací použijte karty ve formuláři **Vytvoření spravované instance Azure SQL** . Tyto karty jsou popsány v následujících částech.

### <a name="basics-tab"></a>Karta základy

- Vyplňte povinné informace požadované na kartě **základy** . Jedná se o minimální sadu informací potřebných ke zřízení spravované instance SQL.

   ![Karta základy pro vytvoření spravované instance SQL](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-basics.png)

   Následující tabulku použijte jako referenci pro informace požadované na této kartě.

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   | **Předplatné** | Vaše předplatné. | Předplatné, které vám poskytne oprávnění k vytváření nových prostředků. |
   | **Skupina prostředků** | Nová nebo existující skupina prostředků.|Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming).|
   | **Název spravované instance** | Libovolný platný název.|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming).|
   | **Oblast** |Oblast, ve které chcete vytvořit spravovanou instanci.|Informace o oblastech najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/).|
   | **Přihlašovací jméno správce spravované instance** | Jakékoli platné uživatelské jméno. | Platné názvy najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). Nepoužívejte "serveradmin", protože to je vyhrazená role na úrovni serveru.|
   | **Heslo** | Jakékoli platné heslo.| Heslo musí obsahovat nejméně 16 znaků a musí splňovat [zadané požadavky na složitost](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Vyberte **Konfigurovat spravovanou instanci** pro velikost výpočetních prostředků a prostředků úložiště a zkontrolujte cenové úrovně. Pomocí posuvník nebo textových polí zadejte velikost úložiště a počet virtuálních jader. Až budete hotovi, vyberte **použít** a uložte svůj výběr. 

   ![Formulář spravované instance](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-configure-performance.png)

| Nastavení| Navrhovaná hodnota | Popis |
| ------ | --------------- | ----------- |
| **Úroveň služby** | Vyberte jednu z možností. | V závislosti na vašem scénáři vyberte jednu z následujících možností: </br> <ul><li>**Pro obecné účely**: pro většinu produkčních úloh a výchozí možnost.</li><li>**Pro důležité obchodní informace**: navržené pro úlohy s nízkou latencí s vysokou odolností vůči chybám a rychlým převzetí služeb při selhání.</li></ul><BR>Další informace najdete v tématech [služby Azure SQL Database a Azure SQL Managed instance úrovně](../../azure-sql/database/service-tiers-general-purpose-business-critical.md) a [Přehled o omezeních prostředků spravované instance Azure SQL](../../azure-sql/managed-instance/resource-limits.md).|
| **Generování hardwaru** | Vyberte jednu z možností. | Generování hardwaru obecně definuje omezení výpočetních hodnot a paměti a další vlastnosti, které mají vliv na výkon úlohy. Výchozí hodnota je **Gen5** .|
| **výpočetní model vCore** | Vyberte možnost. | Virtuální jádra reprezentuje přesné množství výpočetních prostředků, které jsou vždy zřízené pro vaše zatížení. Výchozí hodnota je **osm virtuální jádra** .|
| **Úložiště v GB** | Vyberte možnost. | Velikost úložiště v GB vyberte v závislosti na očekávané velikosti dat. Pokud migrujete stávající data z místního prostředí nebo na různé cloudové platformy, přečtěte si téma [Přehled migrace: SQL Server do spravované instance SQL](../../azure-sql/migration-guides/managed-instance/sql-server-to-managed-instance-overview.md).|
| **Zvýhodněné hybridní využití Azure** | Možnost check, pokud je k dispozici. | Pro využití existující licence pro Azure. Další informace najdete v tématu [zvýhodněné hybridní využití Azure-Azure SQL Database & spravované instance SQL](../../azure-sql/azure-hybrid-benefit.md). |
| **Redundance úložiště zálohování** | Vyberte **geograficky redundantní úložiště zálohování**. | Redundance úložiště v Azure pro úložiště zálohování. Všimněte si, že tuto hodnotu nelze později změnit. Geograficky redundantní úložiště zálohování je výchozí a doporučuje se, i když zóna a místní redundance umožňují větší flexibilitu nákladů a zaregistrování dat v jedné oblasti. Další informace najdete v tématu [redundance úložiště zálohování](../database/automated-backups-overview.md?tabs=managed-instance#backup-storage-redundancy).|


- Chcete-li zkontrolovat volby před vytvořením spravované instance SQL, můžete vybrat možnost **zkontrolovat + vytvořit**. Případně nakonfigurujte možnosti sítě výběrem možnosti **Další: sítě**.

### <a name="networking-tab"></a>Karta sítě

- Vyplňte volitelné informace na kartě **síť** . Pokud tyto informace vynecháte, bude portál používat výchozí nastavení.

   ![Karta sítě pro vytvoření spravované instance](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-networking.png)

   Následující tabulku použijte jako referenci pro informace požadované na této kartě.

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   | **Virtuální síť** | Vyberte buď možnost **vytvořit novou virtuální síť** , nebo platnou virtuální síť a podsíť.| Pokud síť nebo podsíť není k dispozici, je nutné ji [upravit tak, aby splňovala požadavky na síť](vnet-existing-add-subnet.md) předtím, než ji vyberete jako cíl pro novou spravovanou instanci. Informace o požadavcích na konfiguraci prostředí sítě pro spravovanou instanci SQL najdete v tématu [Konfigurace virtuální sítě pro spravovanou instanci SQL](connectivity-architecture-overview.md). |
   | **Typ připojení**: | Vyberte mezi proxy serverem a typem připojení přesměrování.|Další informace o typech připojení najdete v tématu [Typ připojení spravované instance SQL Azure](../database/connectivity-architecture.md#connection-policy).|
   | **Veřejný koncový bod**  | Vyberte **Zakázat**. | Aby bylo možné získat přístup ke spravované instanci prostřednictvím koncového bodu veřejné dat, je nutné povolit tuto možnost. | 
   | **Povolit přístup z** (Pokud je povolený **veřejný koncový bod** ) | Vybrat **bez přístupu**  |Prostředí portálu umožňuje konfigurovat skupinu zabezpečení s veřejným koncovým bodem. </br> </br> V závislosti na vašem scénáři vyberte jednu z následujících možností: </br> <ul> <li>**Služby Azure**: tuto možnost doporučujeme, když se připojujete z Power BI nebo jiné víceklientské služby. </li> <li> **Internet**: použijte pro účely testování, pokud chcete rychle aktivovat spravovanou instanci. Nedoporučujeme ho pro produkční prostředí. </li> <li> **Bez přístupu**: Tato možnost vytvoří pravidlo zabezpečení **Odepřít** . Změnou tohoto pravidla zpřístupníte spravovanou instanci prostřednictvím veřejného koncového bodu. </li> </ul> </br> Další informace o veřejném koncovém bodu zabezpečení najdete v tématu [bezpečné použití spravované instance Azure SQL s veřejným koncovým bodem](public-endpoint-overview.md).|

- Vyberte **zkontrolovat + vytvořit** a před vytvořením spravované instance si prohlédněte své volby. Případně nakonfigurujte více vlastních nastavení výběrem možnosti **Další: Další nastavení**.


### <a name="additional-settings"></a>Další nastavení

- Vyplňte volitelné informace na kartě **Další nastavení** . Pokud tyto informace vynecháte, bude portál používat výchozí nastavení.

   ![Karta Další nastavení pro vytvoření spravované instance](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-additional-settings.png)

   Následující tabulku použijte jako referenci pro informace požadované na této kartě.

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   | **Kolace** | Vyberte kolaci, kterou chcete použít pro spravovanou instanci. Pokud migrujete databáze z SQL Server, Projděte si zdrojovou kolaci pomocí `SELECT SERVERPROPERTY(N'Collation')` a použijte tuto hodnotu.| Informace o kolacích najdete v tématu [nastavení nebo změna kolace serveru](/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Časové pásmo** | Vyberte časové pásmo, které bude spravovaná instance sledovat.|Další informace najdete v tématu [Časová pásma](timezones-overview.md).|
   | **Použít jako sekundární převzetí služeb při selhání** | Vyberte **Ano**. | Tuto možnost povolte, pokud chcete použít spravovanou instanci jako sekundární skupinu převzetí služeb při selhání.|
   | **Primární spravovaná instance SQL** (Pokud se **používá jako sekundární převzetí služeb při selhání,** je nastavená na **Ano**). | Vyberte existující primární spravovanou instanci, která bude připojena ke stejné zóně DNS se spravovanou instancí, kterou vytváříte. | Tento krok umožní konfiguraci po vytvoření skupiny převzetí služeb při selhání. Další informace najdete v tématu [kurz: Přidání spravované instance do skupiny převzetí služeb při selhání](failover-group-add-instance-tutorial.md).|

- Vyberte **zkontrolovat + vytvořit** a před vytvořením spravované instance si prohlédněte své volby. Nebo nakonfigurujte značky Azure výběrem možnosti **Další: značky** (doporučeno).

### <a name="tags"></a>Značky

- Přidání značek k prostředkům v šabloně Azure Resource Manager (šablona ARM). [Značky](../../azure-resource-manager/management/tag-resources.md) vám pomůžou logicky organizovat vaše prostředky. Hodnoty značek se zobrazují v sestavách nákladů a umožňují ostatním aktivitám správy podle značek. 

- Vezměte v úvahu aspoň označení vaší nové spravované instance SQL pomocí značky Owner, abyste identifikovali, kdo vytvořil, a značku prostředí, abyste identifikovali, jestli je tento systém produkční, vývoj atd. Další informace najdete v tématu [vývoj strategie vytváření názvů a označování pro prostředky Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
 
- Pokračujte výběrem **Zobrazit + vytvořit** .

## <a name="review--create"></a>Kontrola a vytvoření

1. Vyberte možnost **zkontrolovat + vytvořit** kartu a zkontrolujte své volby ještě před vytvořením spravované instance.

   ![Karta pro kontrolu a vytvoření spravované instance](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-review-create.png)

1. Výběrem **vytvořit** spusťte zřizování spravované instance.

> [!IMPORTANT]
> Nasazení spravované instance je dlouhodobě běžící operace. Nasazení první instance v podsíti obvykle trvá mnohem déle než nasazení do podsítě se stávajícími spravovanými instancemi. Průměrné doby zřizování najdete v tématu [Přehled operací správy spravované instance Azure SQL](management-operations-overview.md#duration).

## <a name="monitor-deployment-progress"></a>Průběh nasazení monitorování

1. Výběrem ikony **oznámení** zobrazíte stav nasazení.

   ![Průběh nasazení spravované instance SQL](./media/instance-create-quickstart/azure-sql-managed-instance-create-deployment-in-progress.png)

1. V oznámení vyberte **nasazení** probíhá a otevřete okno spravovaná instance SQL a dále Sledujte průběh nasazení. 

> [!TIP]
> - Pokud jste zavřeli webový prohlížeč nebo jste ho přesunuli z obrazovky průběh nasazení, můžete operaci zřizování sledovat prostřednictvím stránky **Přehled** spravované instance nebo pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI. Další informace najdete v tématu [monitorování operací](management-operations-monitor.md#monitor-operations). 
> - Proces zřizování můžete zrušit prostřednictvím Azure Portal, nebo pomocí PowerShellu nebo rozhraní příkazového řádku Azure nebo jiného nástroje pomocí REST API. Viz [zrušení operací správy spravované instance Azure SQL](management-operations-cancel.md).

> [!IMPORTANT]
> - Spuštění vytváření spravované instance SQL může být zpožděno v případech, kdy existují jiné operace s vlivem na změny, například dlouhotrvající operace obnovení nebo škálování na jiné spravované instance ve stejné podsíti. Další informace najdete v tématu [různé operace správy](management-operations-overview.md#management-operations-cross-impact).
> - Aby bylo možné získat stav vytváření spravované instance, je nutné mít **oprávnění ke čtení pro** skupinu prostředků. Pokud toto oprávnění nemáte nebo ho odvoláte, zatímco je spravovaná instance v procesu vytváření, může to způsobit, že se spravovaná instance SQL nebude zobrazovat v seznamu nasazení skupiny prostředků.
>

## <a name="view-resources-created"></a>Zobrazení vytvořených prostředků

Po úspěšném nasazení spravované instance pro zobrazení vytvořených prostředků:

1. Otevřete skupinu prostředků pro spravovanou instanci. 

   ![Prostředky spravované instance SQL](./media/instance-create-quickstart/azure-sql-managed-instance-resources.png)

## <a name="view-and-fine-tune-network-settings"></a>Zobrazit a vyladit nastavení sítě

Pokud chcete volitelně doladit nastavení sítě, zkontrolujte následující:

1. V seznamu prostředků vyberte tabulku směrování pro kontrolu uživatelsky definovaného objektu směrovací tabulky (UDR), který byl vytvořen.

2. V tabulce směrování si prohlédněte položky, které směrují provoz z a do virtuální sítě spravované instance SQL. Pokud vytváříte nebo konfigurujete směrovací tabulku ručně, vytvořte tyto položky v tabulce směrování spravované instance SQL.

   ![Záznam pro podsíť spravované instance SQL do místní sítě](./media/instance-create-quickstart/azure-sql-managed-instance-route-table-user-defined-route.png)

    Chcete-li změnit nebo přidat trasy, otevřete **trasy** v nastavení směrovací tabulky.

3. Vraťte se do skupiny prostředků a vyberte objekt skupiny zabezpečení sítě (NSG), který byl vytvořen.

4. Zkontrolujte pravidla zabezpečení příchozích a odchozích připojení. 

   ![Pravidla zabezpečení](./media/instance-create-quickstart/azure-sql-managed-instance-security-rules.png)

    Chcete-li změnit nebo přidat pravidla, otevřete pravidla **příchozího zabezpečení** a **odchozí pravidla zabezpečení** v nastavení skupiny zabezpečení sítě.

> [!IMPORTANT]
> Pokud jste nakonfigurovali veřejný koncový bod pro spravovanou instanci SQL, musíte otevřít porty a povolit tak síťové přenosy umožňující připojení ke spravované instanci SQL z veřejného Internetu. Další informace najdete v tématu [Konfigurace veřejného koncového bodu pro spravovanou instanci SQL](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).
>

## <a name="retrieve-connection-details-to-sql-managed-instance"></a>Načtení podrobností připojení do služby SQL Managed Instance

Pokud se chcete připojit ke spravované instanci SQL, postupujte podle těchto kroků a načtěte název hostitele a plně kvalifikovaný název domény (FQDN):

1. Vraťte se do skupiny prostředků a vyberte objekt spravované instance SQL, který byl vytvořen.

2. Na kartě **Přehled** vyhledejte vlastnost **hostitel** . Zkopírujte název hostitele do schránky pro spravovanou instanci pro použití v dalším rychlém startu kliknutím na tlačítko **Kopírovat do schránky** .

   ![Název hostitele](./media/instance-create-quickstart/azure-sql-managed-instance-host-name.png)

   Zkopírovaná hodnota představuje plně kvalifikovaný název domény (FQDN), který se dá použít pro připojení k spravované instanci SQL. Je podobný následujícímu příkladu adresy: *your_host_name. a1b2c3d4e5f6. Database. Windows. NET*.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak se připojit ke spravované instanci SQL:
- Přehled možností připojení pro aplikace najdete v tématu [připojení aplikací k spravované instanci SQL](connect-application-instance.md).
- Rychlý Start, který ukazuje, jak se připojit ke spravované instanci SQL z virtuálního počítače Azure, najdete v tématu [Konfigurace připojení k virtuálnímu počítači Azure](connect-vm-instance-configure.md).
- Rychlý Start, který ukazuje, jak se připojit ke spravované instanci SQL z místního klientského počítače pomocí připojení typu Point-to-site, najdete v tématu [Konfigurace připojení typu Point-to-site](point-to-site-p2s-configure.md).

Obnovení stávající databáze SQL Server z místního prostředí do spravované instance SQL: 
- Použijte [Azure Database Migration Service pro migraci](../../dms/tutorial-sql-server-to-managed-instance.md) k obnovení ze záložního souboru databáze. 
- Použijte [příkaz T-SQL Restore](restore-sample-database-quickstart.md) k obnovení ze záložního souboru databáze.

Informace o pokročilém monitorování výkonu databáze spravované instance SQL s integrovanými funkcemi pro řešení potíží najdete v tématu [monitorování spravované instance Azure SQL pomocí Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).