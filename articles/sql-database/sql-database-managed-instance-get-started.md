---
title: 'Portál Azure: Vytvoření spravované instance'
description: Vytvořte spravovanou instanci sql database, síťové prostředí a virtuální virtuální modul klienta pro přístup.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80257610"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Úvodní příručka: Vytvoření spravované instance Azure SQL Database

Tento rychlý start vás provede vytvořením [spravované instance](sql-database-managed-instance.md) Azure SQL Database na webu Azure Portal.

> [!IMPORTANT]
> Omezení naleznete v tématu [Podporované oblasti](sql-database-managed-instance-resource-limits.md#supported-regions) a Podporované [typy předplatného](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Vytvoření spravované instance

Následující kroky ukazují, jak vytvořit spravovanou instanci:

1. V levé nabídce portálu Azure portal vyberte **Azure SQL.** Pokud **Azure SQL** není v seznamu, vyberte Všechny **služby**a zadejte **Azure SQL** do vyhledávacího pole.
2. Výběrem **možnosti +Přidat** otevřete stránku **možnosti Vybrat nasazení SQL.** Další informace o spravované instanci Azure SQL Database můžete zobrazit tak, že na dlaždici **Spravované instance** vyberete **Zobrazit podrobnosti.**
3. Vyberte **Vytvořit**.

   ![Vytvoření spravované instance](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. Pomocí karet ve formuláři **Vytvoření azure databáze spravované instance** přidejte požadované a volitelné informace. Následující části popisují tyto karty.

### <a name="basics"></a>Základy

- Vyplňte povinné informace požadované na kartě **Základy.** Toto je minimální sada informací potřebných k zřízení spravované instance.

   ![Karta Základy pro vytvoření spravované instance](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

   Použijte níže uvedenou tabulku jako referenci pro informace požadované na této kartě.

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   | **Předplatné** | Vaše předplatné. | Předplatné, které vám dává oprávnění k vytváření nových prostředků. |
   | **Skupina prostředků** | Nová nebo existující skupina prostředků.|Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming).|
   | **Název spravované instance** | Libovolný platný název.|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming).|
   | **Oblasti** |Oblast, ve které chcete vytvořit spravovanou instanci.|Informace o oblastech najdete v tématu [Oblasti Azure](https://azure.microsoft.com/regions/).|
   | **Přihlašovací jméno správce spravované instance** | Jakékoli platné uživatelské jméno. | Platné názvy najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). Nepoužívejte "serveradmin", protože se to rovná rezervované roli na úrovni serveru.|
   | **Heslo** | Jakékoli platné heslo.| Heslo musí obsahovat nejméně 16 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Vyberte **Konfigurovat spravovanou instanci** pro velikost výpočetních prostředků a prostředků úložiště a zkontrolujte cenové úrovně. Pomocí posuvník nebo textových polí zadejte velikost úložiště a počet virtuálních jader. Po dokončení výběr uložte vyberte **Použít.** 

   ![Formulář spravované instance](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

- Chcete-li zkontrolovat volby před vytvořením spravované instance, můžete vybrat **možnost Revize + vytvořit**. Nebo nakonfigurujte možnosti sítě výběrem možnosti **Další: Síť**.

### <a name="networking"></a>Síťové služby

- Na kartě **Síť** vyplňte volitelné informace. Pokud tyto informace vynecháte, portál použije výchozí nastavení.

   ![Karta Síťpro vytvoření spravované instance](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

   Použijte níže uvedenou tabulku jako referenci pro informace požadované na této kartě.

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   | **Virtuální síť** | Vyberte Vytvořit **novou virtuální síť** nebo platnou virtuální síť a podsíť.| Pokud síť nebo podsíť není k dispozici, musí být [upravena tak, aby splňovala požadavky sítě,](sql-database-managed-instance-configure-vnet-subnet.md) než ji vyberete jako cíl pro novou spravovanou instanci. Informace o požadavcích na konfiguraci síťového prostředí pro spravovanou instanci naleznete v [tématu Konfigurace virtuální sítě pro spravovanou instanci](sql-database-managed-instance-connectivity-architecture.md). |
   | **Typ připojení** | Vyberte si mezi proxy serverem a typem připojení přesměrování.|Další informace o typech připojení naleznete v tématu [Zásady připojení Azure SQL Database](sql-database-connectivity-architecture.md#connection-policy).|
   | **Veřejný koncový bod**  | Vyberte **Povolit**. | Aby byla spravovaná instance přístupná prostřednictvím koncového bodu veřejných dat, musíte tuto možnost povolit. | 
   | **Povolit přístup z** (pokud je povolen **veřejný koncový bod)** | Vyberte jednu z možností.   |Prostředí portálu umožňuje konfiguraci skupiny zabezpečení s veřejným koncovým bodem. </br> </br> Na základě scénáře vyberte jednu z následujících možností: </br> <ul> <li>**Služby Azure**: Tuto možnost doporučujeme, když se připojujete z Power BI nebo jiné víceklientské služby. </li> <li> **Internet**: Použijte pro testovací účely, pokud chcete rychle střídat spravovanou instanci. Nedoporučujeme ji pro produkční prostředí. </li> <li> **Žádný přístup**: Tato možnost vytvoří pravidlo zabezpečení **Odepřít.** Upravte toto pravidlo tak, aby spravovaná instance byla přístupná prostřednictvím veřejného koncového bodu. </li> </ul> </br> Další informace o zabezpečení veřejného koncového bodu najdete [v tématu bezpečné použití instance spravované službou Azure SQL Database s veřejným koncovým bodem](sql-database-managed-instance-public-endpoint-securely.md).|

- Vyberte **Zkontrolovat + vytvořit** a zkontrolujte své volby před vytvořením spravované instance. Nebo nakonfigurujte další vlastní nastavení výběrem možnosti **Další: Další nastavení**.

### <a name="additional-settings"></a>Další nastavení

- Na kartě **Další nastavení** vyplňte volitelné informace. Pokud tyto informace vynecháte, portál použije výchozí nastavení.

   ![Karta Další nastavení pro vytvoření spravované instance](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

   Použijte níže uvedenou tabulku jako referenci pro informace požadované na této kartě.

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   | **Kolace** | Zvolte řazení, které chcete použít pro spravovanou instanci. Pokud migrujete databáze ze serveru SQL Server, zkontrolujte zdrojové řazení pomocí `SELECT SERVERPROPERTY(N'Collation')` a použijte tuto hodnotu.| Informace o řazení naleznete v [tématu Nastavení nebo změna řazení serveru](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Časové pásmo** | Vyberte časové pásmo, které bude vaše spravovaná instance sledovat.|Další informace naleznete v [tématu Časová pásma](sql-database-managed-instance-timezone.md).|
   | **Použít jako sekundární převzetí služeb při selhání** | Vyberte **ano**. | Tuto možnost povolte, chcete-li použít spravovanou instanci jako sekundární skupinu s podporou převzetí služeb při selhání.|
   | **Primární spravovaná instance** (pokud **je použití jako sekundární převzetí služeb při selhání** nastaveno na **ano**) | Zvolte existující primární spravovanou instanci, která bude spojena ve stejné zóně DNS se spravovanou instancí, kterou vytváříte. | Tento krok umožní konfiguraci skupiny převzetí služeb při selhání po vytvoření. Další informace naleznete v [tématu Výuka: Přidání instance spravované databází SQL do skupiny s podporou převzetí služeb při selhání](sql-database-managed-instance-failover-group-tutorial.md).|

### <a name="review--create"></a>Recenze + vytvořit

5. Vyberte **Karta Revize + vytvoření** a zkontrolujte své volby před vytvořením spravované instance.

   ![Karta pro revizi a vytvoření spravované instance](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

6. Výběrem **možnosti Vytvořit** zahájíte zřizování spravované instance.

> [!IMPORTANT]
> Nasazení spravované instance je dlouhotrvající operace. Nasazení první instance v podsíti obvykle trvá mnohem déle než nasazení do podsítě s existujícíspravované instance. Průměrná doba zřizování najdete v tématu [Operace správy spravovaných instancí](sql-database-managed-instance.md#managed-instance-management-operations).

### <a name="monitor-deployment-progress"></a>Sledování průběhu nasazení

7. Chcete-li zobrazit stav nasazení, vyberte ikonu **Oznámení.**

   ![Průběh nasazení nasazení spravované instance](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

8. Vyberte **Nasazení probíhá** v oznámení otevřít okno spravované instance a dále sledovat průběh nasazení. 

> [!TIP]
> Pokud jste zavřeli webový prohlížeč nebo jste se odklonili od obrazovky průběhu nasazení, vyhledejte zpět obrazovku průběhu nasazení následujícím postupem:
> 1. Na webu Azure Portal otevřete skupinu prostředků (na kartě **Základy),** do které nasazujete spravovanou instanci.
> 2. Vyberte **možnost Nasazení**.
> 3. Vyberte probíhající operaci nasazení spravované instance.

> [!IMPORTANT]
> Aby bylo možné získat stav vytváření spravovaných instancí, musíte mít **oprávnění ke čtení** přes skupinu prostředků. Pokud nemáte toto oprávnění nebo odvolat, zatímco spravovaná instance je v procesu vytváření, to může způsobit spravované instance není viditelné v seznamu nasazení skupiny prostředků.
>

## <a name="post-deployment-operations"></a>Operace po nasazení

Chcete-li zkontrolovat vytvořené prostředky, doladit nastavení sítě a načíst podrobnosti o připojení hostitele (FQDN) postupujte podle kroků popsaných v této části.

### <a name="view-resources-created"></a>Zobrazit vytvořené zdroje

Po úspěšném nasazení spravované instance chcete zobrazit vytvořené prostředky:

1. Otevřete skupinu prostředků pro spravovanou instanci. Zobrazit jeho prostředky, které byly vytvořeny pro vás v [Vytvořit spravované instance](#create-a-managed-instance) rychlý start.

   ![Prostředky spravované instance](./media/sql-database-managed-instance-get-started/resources.png)

### <a name="view-and-fine-tune-network-settings"></a>Zobrazení a jemné doladění nastavení sítě

Chcete-li volitelně doladit nastavení sítě, zkontrolujte následující:

1. Vyberte směrovací tabulku a zkontrolujte uživatelem definovanou trasu (UDR), která byla vytvořena za vás.

   ![Tabulka směrování](./media/sql-database-managed-instance-get-started/route-table.png)

2. V tabulce tras zkontrolujte položky směrovat provoz z a v rámci virtuální sítě spravované instance. Pokud vytvoříte nebo nakonfigurujete směrovací tabulku ručně, ujistěte se, že tyto položky vytvoříte ve spravovací tabulce tras instance.

   ![Položka podsítě spravované instance do místní](./media/sql-database-managed-instance-get-started/udr.png)

3. Vraťte se do skupiny prostředků a vyberte skupinu zabezpečení sítě.

   ![Skupina zabezpečení sítě](./media/sql-database-managed-instance-get-started/network-security-group.png)

4. Zkontrolujte příchozí a odchozí pravidla zabezpečení. 

   ![Pravidla zabezpečení](./media/sql-database-managed-instance-get-started/security-rules.png)

> [!IMPORTANT]
> Pokud jste nakonfigurovali veřejný koncový bod pro spravovanou instanci, musíte otevřít porty, abyste povolili síťový provoz umožňující připojení ke spravované instanci z veřejného Internetu, přečtěte si další informace v tématu [Konfigurace veřejného koncového bodu pro spravovanou instanci.](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)
>

### <a name="retrieve-connection-details-to-managed-instance"></a>Načtení podrobností o připojení do spravované instance

Chcete-li se připojit ke spravované instanci, načtěte název hostitele a plně kvalifikovaný název domény (Plně kvalifikovaný název domény):

1. Vraťte se do skupiny prostředků a vyberte spravovanou instanci.

   ![Spravovaná instance ve skupině prostředků](./media/sql-database-managed-instance-get-started/managed-instance.png)

2. Na kartě **Přehled** vyhledejte vlastnost **Host.** Zkopírujte název hostitele spravované instance pro použití v dalším rychlém startu.

   ![Název hostitele](./media/sql-database-managed-instance-get-started/host-name.png)

   Zkopírovaná hodnota představuje plně kvalifikovaný název domény (FQDN), který lze použít k připojení ke spravované instanci. Je podobný následujícímu příkladu adresy: *your_host_name.a1b2c3d4e5f6.database.windows.net*.

## <a name="next-steps"></a>Další kroky

Jak se připojit ke spravované instanci, jak se připojit ke spravované instanci:
- Přehled možností připojení pro aplikace najdete v tématu [Připojení aplikací ke spravované instanci](sql-database-managed-instance-connect-app.md).
- Úvodní příručka, která ukazuje, jak se připojit ke spravované instanci z virtuálního počítače Azure, najdete v článku [Konfigurace připojení virtuálního počítače Azure](sql-database-managed-instance-configure-vm.md).
- Rychlý start, který ukazuje, jak se připojit ke spravované instanci z místního klientského počítače pomocí připojení bodu k síti, naleznete v [tématu Konfigurace připojení bodu k síti](sql-database-managed-instance-configure-p2s.md).

Obnovení existující databáze serveru SQL Server z místní do spravované instance: 
- K obnovení ze záložního souboru databáze použijte [službu Migrace databáze Azure.](../dms/tutorial-sql-server-to-managed-instance.md) 
- Pomocí [příkazu T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) obnovte ze záložního souboru databáze.

Pokročilé monitorování výkonu databáze spravovaných instancí s integrovanou inteligencí řešení potíží najdete v tématu [Monitorování Azure SQL Database pomocí Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
