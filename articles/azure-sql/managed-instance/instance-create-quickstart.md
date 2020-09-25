---
title: 'Rychlý Start: vytvoření spravované instance spravované instance SQL (portál)'
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
ms.date: 09/26/2019
ms.openlocfilehash: 2f2e8c6be128e5004769dca2f42a6c8013510cde
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325228"
---
# <a name="quickstart-create-a-managed-instance-of-sql-managed-instance"></a>Rychlý Start: vytvoření spravované instance spravované instance SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

V tomto rychlém startu se naučíte vytvořit spravovanou instanci [spravované instance Azure SQL](sql-managed-instance-paas-overview.md) ve Azure Portal.

> [!IMPORTANT]
> Omezení najdete v tématu [podporované oblasti](resource-limits.md#supported-regions) a [podporované typy předplatného](resource-limits.md#supported-subscription-types).

## <a name="create-a-managed-instance"></a>Vytvoření spravované instance

Chcete-li vytvořit spravovanou instanci, postupujte podle těchto kroků: 

### <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Pokud nemáte předplatné Azure, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. V levé nabídce Azure Portal vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a pak do vyhledávacího pole zadejte **Azure SQL** .
1. Výběrem **+ Přidat** otevřete stránku **vybrat možnost nasazení SQL** . Další informace o spravované instanci Azure SQL získáte tak, že na dlaždici **spravované instance SQL** vyberete **Zobrazit podrobnosti** .
1. Vyberte **Vytvořit**.

   ![Vytvoření spravované instance](./media/instance-create-quickstart/create-managed-instance.png)

4. K přidání požadovaných a volitelných informací použijte karty ve formuláři **Vytvoření spravované instance Azure SQL** . Tyto karty jsou popsány v následujících částech.

### <a name="basics-tab"></a>Karta základy

- Vyplňte povinné informace požadované na kartě **základy** . Jedná se o minimální sadu informací potřebných ke zřízení spravované instance.

   ![Karta základy pro vytvoření spravované instance](./media/instance-create-quickstart/mi-create-tab-basics.png)

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

   ![Formulář spravované instance](./media/instance-create-quickstart/mi-create-tab-configure-performance.png)

- Chcete-li zkontrolovat volby před vytvořením spravované instance SQL, můžete vybrat možnost **zkontrolovat + vytvořit**. Případně nakonfigurujte možnosti sítě výběrem možnosti **Další: sítě**.

### <a name="networking-tab"></a>Karta sítě

- Vyplňte volitelné informace na kartě **síť** . Pokud tyto informace vynecháte, bude portál používat výchozí nastavení.

   ![Karta sítě pro vytvoření spravované instance](./media/instance-create-quickstart/mi-create-tab-networking.png)

   Následující tabulku použijte jako referenci pro informace požadované na této kartě.

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   | **Virtuální síť** | Vyberte buď možnost **vytvořit novou virtuální síť** , nebo platnou virtuální síť a podsíť.| Pokud síť nebo podsíť není k dispozici, je nutné ji [upravit tak, aby splňovala požadavky na síť](vnet-existing-add-subnet.md) předtím, než ji vyberete jako cíl pro novou spravovanou instanci. Informace o požadavcích na konfiguraci prostředí sítě pro spravovanou instanci SQL najdete v tématu [Konfigurace virtuální sítě pro spravovanou instanci SQL](connectivity-architecture-overview.md). |
   | **Typ připojení**: | Vyberte mezi proxy serverem a typem připojení přesměrování.|Další informace o typech připojení najdete v tématu [Typ připojení spravované instance SQL Azure](../database/connectivity-architecture.md#connection-policy).|
   | **Veřejný koncový bod**  | Vyberte **Povolit**. | Aby bylo možné získat přístup ke spravované instanci prostřednictvím koncového bodu veřejné dat, je nutné povolit tuto možnost. | 
   | **Povolit přístup z** (Pokud je povolený **veřejný koncový bod** ) | Vyberte jednu z možností.   |Prostředí portálu umožňuje konfigurovat skupinu zabezpečení s veřejným koncovým bodem. </br> </br> V závislosti na vašem scénáři vyberte jednu z následujících možností: </br> <ul> <li>**Služby Azure**: tuto možnost doporučujeme, když se připojujete z Power BI nebo jiné víceklientské služby. </li> <li> **Internet**: použijte pro účely testování, pokud chcete rychle aktivovat spravovanou instanci. Nedoporučujeme ho pro produkční prostředí. </li> <li> **Bez přístupu**: Tato možnost vytvoří pravidlo zabezpečení **Odepřít** . Změnou tohoto pravidla zpřístupníte spravovanou instanci prostřednictvím veřejného koncového bodu. </li> </ul> </br> Další informace o veřejném koncovém bodu zabezpečení najdete v tématu [bezpečné použití spravované instance Azure SQL s veřejným koncovým bodem](public-endpoint-overview.md).|

- Vyberte **zkontrolovat + vytvořit** a před vytvořením spravované instance si prohlédněte své volby. Případně nakonfigurujte více vlastních nastavení výběrem možnosti **Další: Další nastavení**.

### <a name="additional-settings"></a>Další nastavení

- Vyplňte volitelné informace na kartě **Další nastavení** . Pokud tyto informace vynecháte, bude portál používat výchozí nastavení.

   ![Karta Další nastavení pro vytvoření spravované instance](./media/instance-create-quickstart/mi-create-tab-additional-settings.png)

   Následující tabulku použijte jako referenci pro informace požadované na této kartě.

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   | **Kolace** | Vyberte kolaci, kterou chcete použít pro spravovanou instanci. Pokud migrujete databáze z SQL Server, Projděte si zdrojovou kolaci pomocí `SELECT SERVERPROPERTY(N'Collation')` a použijte tuto hodnotu.| Informace o kolacích najdete v tématu [nastavení nebo změna kolace serveru](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Časové pásmo** | Vyberte časové pásmo, které bude spravovaná instance sledovat.|Další informace najdete v tématu [Časová pásma](timezones-overview.md).|
   | **Použít jako sekundární převzetí služeb při selhání** | Vyberte **Ano**. | Tuto možnost povolte, pokud chcete použít spravovanou instanci jako sekundární skupinu převzetí služeb při selhání.|
   | **Primární spravovaná instance SQL** (Pokud se **používá jako sekundární převzetí služeb při selhání,** je nastavená na **Ano**). | Vyberte existující primární spravovanou instanci, která bude připojena ke stejné zóně DNS se spravovanou instancí, kterou vytváříte. | Tento krok umožní konfiguraci po vytvoření skupiny převzetí služeb při selhání. Další informace najdete v tématu [kurz: Přidání spravované instance do skupiny převzetí služeb při selhání](failover-group-add-instance-tutorial.md).|

## <a name="review--create"></a>Kontrola a vytvoření

1. Vyberte možnost **zkontrolovat + vytvořit** kartu a zkontrolujte své volby ještě před vytvořením spravované instance.

   ![Karta pro kontrolu a vytvoření spravované instance](./media/instance-create-quickstart/mi-create-tab-review-create.png)

1. Výběrem **vytvořit** spusťte zřizování spravované instance.

> [!IMPORTANT]
> Nasazení spravované instance je dlouhodobě běžící operace. Nasazení první instance v podsíti obvykle trvá mnohem déle než nasazení do podsítě se stávajícími spravovanými instancemi. Průměrné doby zřizování najdete v tématu [operace správy spravované instance SQL](sql-managed-instance-paas-overview.md#management-operations).

## <a name="monitor-deployment-progress"></a>Průběh nasazení monitorování

1. Výběrem ikony **oznámení** zobrazíte stav nasazení.

   ![Průběh nasazení spravované instance SQL](./media/instance-create-quickstart/mi-create-deployment-in-progress.png)

1. V oznámení vyberte **nasazení** probíhá a otevřete okno spravovaná instance SQL a dále Sledujte průběh nasazení. 

> [!TIP]
> Pokud jste webový prohlížeč zavřeli nebo jste ho přesunuli z obrazovky průběh nasazení, postupujte podle těchto kroků a najděte obrazovku průběh nasazení:
> 1. V Azure Portal otevřete skupinu prostředků (na kartě **základy** ), na kterou nasazujete SPRAVOVANOU instanci SQL.
> 2. Vyberte **nasazení**.
> 3. Vyberte probíhající operaci nasazení spravované instance SQL.

> [!IMPORTANT]
> - Vytváření spravované instance SQL je dlouhodobá operace, kterou může trvat několik hodin v závislosti na konkrétních okolnostech. Běžné časy vytváření najdete v tématu [Doba trvání operací správy](management-operations-overview.md#duration) .
> - Spuštění vytváření spravované instance SQL může být zpožděno v případech, kdy existují jiné operace s vlivem na změny, například dlouhotrvající operace obnovení nebo škálování na jiné spravované instance ve stejné podsíti. Další informace najdete v tématu [různé operace správy](management-operations-overview.md#management-operations-cross-impact).
> - Aby bylo možné získat stav vytváření spravované instance, je nutné mít **oprávnění ke čtení pro** skupinu prostředků. Pokud toto oprávnění nemáte nebo ho odvoláte, zatímco je spravovaná instance v procesu vytváření, může to způsobit, že se spravovaná instance SQL nebude zobrazovat v seznamu nasazení skupiny prostředků.
>

## <a name="view-resources-created"></a>Zobrazení vytvořených prostředků

Po úspěšném nasazení spravované instance pro zobrazení vytvořených prostředků:

1. Otevřete skupinu prostředků pro spravovanou instanci. 

   ![Prostředky spravované instance SQL](./media/instance-create-quickstart/resources.png)

## <a name="view-and-fine-tune-network-settings"></a>Zobrazit a vyladit nastavení sítě

Pokud chcete volitelně doladit nastavení sítě, zkontrolujte následující:

1. Vyberte směrovací tabulku pro kontrolu uživatelsky definované trasy (UDR), která byla vytvořena za vás.

   ![Tabulka směrování](./media/instance-create-quickstart/route-table.png)

2. V tabulce směrování si prohlédněte položky, které směrují provoz z a do virtuální sítě spravované instance SQL. Pokud tabulku směrování vytváříte nebo konfigurujete ručně, ujistěte se, že jste tyto položky vytvořili v tabulce směrování spravované instance SQL.

   ![Záznam pro podsíť spravované instance SQL do místní sítě](./media/instance-create-quickstart/udr.png)

3. Vraťte se do skupiny prostředků a vyberte skupinu zabezpečení sítě.

   ![Skupina zabezpečení sítě](./media/instance-create-quickstart/network-security-group.png)

4. Zkontrolujte pravidla zabezpečení příchozích a odchozích připojení. 

   ![Pravidla zabezpečení](./media/instance-create-quickstart/security-rules.png)

> [!IMPORTANT]
> Pokud jste nakonfigurovali veřejný koncový bod pro spravovanou instanci SQL, musíte otevřít porty a povolit tak síťové přenosy umožňující připojení ke spravované instanci SQL z veřejného Internetu. Další informace najdete v tématu [Konfigurace veřejného koncového bodu pro spravovanou instanci SQL](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) .
>

## <a name="retrieve-connection-details-to-sql-managed-instance"></a>Načtení podrobností připojení do služby SQL Managed Instance

Pokud se chcete připojit ke spravované instanci SQL, postupujte podle těchto kroků a načtěte název hostitele a plně kvalifikovaný název domény (FQDN):

1. Vraťte se do skupiny prostředků a vyberte spravovanou instanci.

   ![Spravovaná instance ve skupině prostředků](./media/instance-create-quickstart/managed-instance.png)

2. Na kartě **Přehled** vyhledejte vlastnost **hostitel** . Zkopírujte název hostitele spravované instance pro použití v dalším rychlém startu.

   ![Název hostitele](./media/instance-create-quickstart/host-name.png)

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
