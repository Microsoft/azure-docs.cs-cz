---
title: 'Azure Portal: vytvoření spravované instance'
description: Vytvořte SQL Database spravovanou instanci, síťové prostředí a klientský virtuální počítač pro přístup.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein, carlrab
ms.date: 09/26/2019
ms.openlocfilehash: 25128442cd922f6b9130586e245695b6880f661c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80257610"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Rychlý Start: vytvoření spravované instance Azure SQL Database

Tento rychlý Start vás provede vytvořením Azure SQL Database [spravované instance](sql-database-managed-instance.md) v Azure Portal.

> [!IMPORTANT]
> Omezení najdete v tématu [podporované oblasti](sql-database-managed-instance-resource-limits.md#supported-regions) a [podporované typy předplatného](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Vytvoření spravované instance

Následující kroky ukazují, jak vytvořit spravovanou instanci:

1. V levé nabídce Azure Portal vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a pak do vyhledávacího pole zadejte **Azure SQL** .
2. Výběrem **+ Přidat** otevřete stránku **vybrat možnost nasazení SQL** . Další informace o spravované instanci Azure SQL Database můžete zobrazit tak, že na dlaždici **spravované instance** vyberete **Zobrazit podrobnosti** .
3. Vyberte **Vytvořit**.

   ![Vytvoření spravované instance](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. Pomocí karet na formuláři pro **Vytvoření spravované Instance Azure SQL Database** můžete přidat požadované a volitelné informace. Tyto karty jsou popsány v následujících částech.

### <a name="basics"></a>Základy

- Vyplňte povinné informace požadované na kartě **základy** . Jedná se o minimální sadu informací potřebných ke zřízení spravované instance.

   ![Karta základy pro vytvoření spravované instance](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

   Následující tabulku použijte jako referenci pro informace požadované na této kartě.

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   | **Předplatné** | Vaše předplatné. | Předplatné, které vám poskytne oprávnění k vytváření nových prostředků. |
   | **Skupina prostředků** | Nová nebo existující skupina prostředků.|Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming).|
   | **Název spravované instance** | Libovolný platný název.|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming).|
   | **Oblast** |Oblast, ve které chcete vytvořit spravovanou instanci.|Informace o oblastech najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/).|
   | **Přihlašovací jméno správce spravované instance** | Jakékoli platné uživatelské jméno. | Platné názvy najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). Nepoužívejte "serveradmin", protože to je vyhrazená role na úrovni serveru.|
   | **Heslo** | Jakékoli platné heslo.| Heslo musí obsahovat nejméně 16 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Vyberte **Konfigurovat spravovanou instanci** pro velikost výpočetních prostředků a prostředků úložiště a zkontrolujte cenové úrovně. Pomocí posuvník nebo textových polí zadejte velikost úložiště a počet virtuálních jader. Až budete hotovi, vyberte **použít** a uložte svůj výběr. 

   ![Formulář spravované instance](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

- Chcete-li zkontrolovat volby před vytvořením spravované instance, můžete vybrat možnost **zkontrolovat + vytvořit**. Případně nakonfigurujte možnosti sítě výběrem možnosti **Další: sítě**.

### <a name="networking"></a>Sítě

- Vyplňte volitelné informace na kartě **síť** . Pokud tyto informace vynecháte, bude portál používat výchozí nastavení.

   ![Karta sítě pro vytvoření spravované instance](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

   Následující tabulku použijte jako referenci pro informace požadované na této kartě.

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   | **Virtuální síť** | Vyberte buď možnost **vytvořit novou virtuální síť** , nebo platnou virtuální síť a podsíť.| Pokud síť nebo podsíť není k dispozici, je nutné ji [upravit tak, aby splňovala požadavky na síť](sql-database-managed-instance-configure-vnet-subnet.md) předtím, než ji vyberete jako cíl pro novou spravovanou instanci. Informace o požadavcích na konfiguraci síťového prostředí pro spravovanou instanci najdete v tématu [Konfigurace virtuální sítě pro spravovanou instanci](sql-database-managed-instance-connectivity-architecture.md). |
   | **Typ připojení** | Vyberte mezi proxy serverem a typem připojení přesměrování.|Další informace o typech připojení najdete v tématu [Azure SQL Database zásady připojení](sql-database-connectivity-architecture.md#connection-policy).|
   | **Veřejný koncový bod**  | Vyberte **Povolit**. | Aby bylo možné získat přístup ke spravované instanci prostřednictvím koncového bodu veřejné dat, je nutné povolit tuto možnost. | 
   | **Povolit přístup z** (Pokud je povolený **veřejný koncový bod** ) | Vyberte jednu z možností.   |Prostředí portálu umožňuje konfigurovat skupinu zabezpečení s veřejným koncovým bodem. </br> </br> V závislosti na vašem scénáři vyberte jednu z následujících možností: </br> <ul> <li>**Služby Azure**: tuto možnost doporučujeme, když se připojujete z Power BI nebo jiné víceklientské služby. </li> <li> **Internet**: použijte pro účely testování, pokud chcete rychle aktivovat spravovanou instanci. Nedoporučujeme ho pro produkční prostředí. </li> <li> **Bez přístupu**: Tato možnost vytvoří pravidlo zabezpečení **Odepřít** . Změnou tohoto pravidla zpřístupníte spravovanou instanci prostřednictvím veřejného koncového bodu. </li> </ul> </br> Další informace o zabezpečení veřejného koncového bodu najdete v tématu [použití spravované instance Azure SQL Database bezpečně s veřejným koncovým bodem](sql-database-managed-instance-public-endpoint-securely.md).|

- Vyberte **zkontrolovat + vytvořit** a před vytvořením spravované instance si prohlédněte své volby. Případně nakonfigurujte více vlastních nastavení výběrem možnosti **Další: Další nastavení**.

### <a name="additional-settings"></a>Další nastavení

- Vyplňte volitelné informace na kartě **Další nastavení** . Pokud tyto informace vynecháte, bude portál používat výchozí nastavení.

   ![Karta Další nastavení pro vytvoření spravované instance](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

   Následující tabulku použijte jako referenci pro informace požadované na této kartě.

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   | **Kolace** | Vyberte kolaci, kterou chcete použít pro spravovanou instanci. Pokud migrujete databáze z SQL Server, Projděte si zdrojovou kolaci pomocí `SELECT SERVERPROPERTY(N'Collation')` a použijte tuto hodnotu.| Informace o kolacích najdete v tématu [nastavení nebo změna kolace serveru](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Časové pásmo** | Vyberte časové pásmo, které bude vaše spravovaná instance sledovat.|Další informace najdete v tématu [Časová pásma](sql-database-managed-instance-timezone.md).|
   | **Použít jako sekundární převzetí služeb při selhání** | Vyberte **Ano**. | Tuto možnost povolte, pokud chcete použít spravovanou instanci jako sekundární skupinu převzetí služeb při selhání.|
   | **Primární spravovaná instance** (Pokud se **používá jako sekundární převzetí služeb při selhání,** je nastavená na **Ano**). | Vyberte existující primární spravovanou instanci, která bude připojena ke stejné zóně DNS se spravovanou instancí, kterou vytváříte. | Tento krok umožní konfiguraci po vytvoření skupiny převzetí služeb při selhání. Další informace najdete v tématu [kurz: Přidání spravované instance SQL Database do skupiny převzetí služeb při selhání](sql-database-managed-instance-failover-group-tutorial.md).|

### <a name="review--create"></a>Zkontrolovat a vytvořit

5. Vyberte možnost **zkontrolovat + vytvořit** kartu a zkontrolujte své volby ještě před vytvořením spravované instance.

   ![Karta pro kontrolu a vytvoření spravované instance](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

6. Výběrem **vytvořit** spusťte zřizování spravované instance.

> [!IMPORTANT]
> Nasazení spravované instance je dlouhodobě běžící operace. Nasazení první instance v podsíti obvykle trvá mnohem déle než nasazení do podsítě se stávajícími spravovanými instancemi. Průměrné doby zřizování najdete v tématu [operace správy spravované instance](sql-database-managed-instance.md#managed-instance-management-operations).

### <a name="monitor-deployment-progress"></a>Průběh nasazení monitorování

7. Výběrem ikony **oznámení** zobrazíte stav nasazení.

   ![Průběh nasazení spravované instance](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

8. V oznámení vyberte **nasazení** probíhá a otevřete okno spravovaná instance a dále Sledujte průběh nasazení. 

> [!TIP]
> Pokud jste webový prohlížeč zavřeli nebo jste ho přesunuli z obrazovky průběh nasazení, pomocí následujícího postupu Najděte obrazovku průběh nasazení:
> 1. V Azure Portal otevřete skupinu prostředků (na kartě **základy** ), na kterou nasazujete spravovanou instanci.
> 2. Vyberte **nasazení**.
> 3. Vyberte probíhající operaci nasazení spravované instance.

> [!IMPORTANT]
> Aby bylo možné získat stav vytváření spravované instance, je nutné mít **oprávnění ke čtení pro** skupinu prostředků. Pokud toto oprávnění nemáte nebo ho odvoláte, zatímco je spravovaná instance v procesu vytváření, může to způsobit, že se spravovaná instance nebude zobrazovat v seznamu nasazení skupiny prostředků.
>

## <a name="post-deployment-operations"></a>Operace po nasazení

Chcete-li zkontrolovat vytvořené prostředky, doladit nastavení sítě a načíst podrobnosti o připojení hostitele (FQDN), postupujte podle kroků popsaných v této části.

### <a name="view-resources-created"></a>Zobrazení vytvořených prostředků

Po úspěšném nasazení spravované instance pro zobrazení vytvořených prostředků:

1. Otevřete skupinu prostředků pro spravovanou instanci. Prohlédněte si své prostředky, které jste vytvořili v rychlém startu [Vytvoření spravované instance](#create-a-managed-instance) .

   ![Prostředky spravované instance](./media/sql-database-managed-instance-get-started/resources.png)

### <a name="view-and-fine-tune-network-settings"></a>Zobrazit a vyladit nastavení sítě

Pokud chcete volitelně doladit nastavení sítě, zkontrolujte následující:

1. Vyberte směrovací tabulku pro kontrolu uživatelsky definované trasy (UDR), která byla vytvořena za vás.

   ![Tabulka směrování](./media/sql-database-managed-instance-get-started/route-table.png)

2. V tabulce směrování si prohlédněte položky, které směrují provoz z a do virtuální sítě spravované instance. Pokud tabulku směrování vytváříte nebo konfigurujete ručně, ujistěte se, že jste tyto položky vytvořili v tabulce směrování spravované instance.

   ![Položka pro podsíť spravované instance do místní sítě](./media/sql-database-managed-instance-get-started/udr.png)

3. Vraťte se do skupiny prostředků a vyberte skupinu zabezpečení sítě.

   ![Skupina zabezpečení sítě](./media/sql-database-managed-instance-get-started/network-security-group.png)

4. Zkontrolujte pravidla zabezpečení příchozích a odchozích připojení. 

   ![Pravidla zabezpečení](./media/sql-database-managed-instance-get-started/security-rules.png)

> [!IMPORTANT]
> Pokud jste pro spravovanou instanci nakonfigurovali veřejný koncový bod, musíte otevřít porty, aby síťový provoz povoloval připojení ke spravované instanci z veřejného Internetu. Další informace najdete v tématu [Konfigurace veřejného koncového bodu pro spravovanou instanci](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) .
>

### <a name="retrieve-connection-details-to-managed-instance"></a>Načíst podrobnosti o připojení ke spravované instanci

Pokud se chcete připojit ke spravované instanci, postupujte podle těchto kroků a načtěte název hostitele a plně kvalifikovaný název domény (FQDN):

1. Vraťte se do skupiny prostředků a vyberte spravovanou instanci.

   ![Spravovaná instance ve skupině prostředků](./media/sql-database-managed-instance-get-started/managed-instance.png)

2. Na kartě **Přehled** vyhledejte vlastnost **hostitel** . Zkopírujte název hostitele spravované instance pro použití v dalším rychlém startu.

   ![Název hostitele](./media/sql-database-managed-instance-get-started/host-name.png)

   Zkopírovaná hodnota představuje plně kvalifikovaný název domény (FQDN), který se dá použít pro připojení ke spravované instanci. Je podobný následujícímu příkladu adresy: *your_host_name. a1b2c3d4e5f6. Database. Windows. NET*.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak se připojit ke spravované instanci:
- Přehled možností připojení pro aplikace najdete v tématu [připojení aplikací ke spravované instanci](sql-database-managed-instance-connect-app.md).
- Rychlý Start, který ukazuje, jak se připojit ke spravované instanci z virtuálního počítače Azure, najdete v tématu [Konfigurace připojení k virtuálnímu počítači Azure](sql-database-managed-instance-configure-vm.md).
- Pro rychlý Start, který ukazuje, jak se připojit ke spravované instanci z místního klientského počítače pomocí připojení typu Point-to-site, najdete informace v tématu [Konfigurace připojení typu Point-to-site](sql-database-managed-instance-configure-p2s.md).

Obnovení stávající databáze SQL Server z místního prostředí do spravované instance: 
- Použijte [Azure Database Migration Service pro migraci](../dms/tutorial-sql-server-to-managed-instance.md) k obnovení ze záložního souboru databáze. 
- Použijte [příkaz T-SQL Restore](sql-database-managed-instance-get-started-restore.md) k obnovení ze záložního souboru databáze.

Pro pokročilé monitorování výkonu databáze spravované instance s integrovanými funkcemi řešení potíží najdete informace v tématu [monitorování Azure SQL Database pomocí Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
