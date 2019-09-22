---
title: 'Azure Portal: Vytvoření spravované instance SQL Database | Microsoft Docs'
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
ms.date: 05/07/2019
ms.openlocfilehash: 5f37ca8e22b07e39eda87e11f52358e1d1497c60
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178498"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Rychlý start: Vytvoření spravované instance Azure SQL Database

Tento rychlý Start vás provede vytvořením Azure SQL Database [spravované instance](sql-database-managed-instance.md) v Azure Portal.

> [!IMPORTANT]
> Omezení najdete v tématu [podporované oblasti](sql-database-managed-instance-resource-limits.md#supported-regions) a [podporované typy předplatného](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Vytvoření spravované instance

Následující kroky ukazují, jak vytvořit spravovanou instanci.

1. V nabídce na levé straně Azure Portal vyberte **Azure SQL** . Pokud Azure SQL není v seznamu, vyberte **všechny služby**a do vyhledávacího pole zadejte *Azure SQL* .
2. Výběrem **+ Přidat** otevřete stránku **vybrat možnost nasazení SQL** . Další informace o Azure SQL Database Managed instance si můžete zobrazit tak, že na dlaždici **spravované instance** vyberete **Zobrazit podrobnosti** .
3. Vyberte **vytvořit**:

   ![Vytvoření spravované instance](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. Vyplňte formulář pro zřizování **spravované instance vytvoření SQL Database** vyplněním informací vyžadovaných na kartě Basic. K nakonfigurování dalších nastavení použijte možnosti sítě a rozšířená nastavení.

### <a name="basic-tab"></a>Karta Basic

Vyplňte povinné informace na kartě **Basic** pomocí následující tabulky. Toto je minimální sada informací pro zřízení spravované instance.

   ![Karta základ pro vytvoření spravované instance](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   | **Předplatné** | Vaše předplatné. | Předplatné, které vám poskytne oprávnění k vytváření nových prostředků. |
   | **Skupina prostředků** | Nová nebo existující skupina prostředků.|Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   | **Název spravované instance** | Libovolný platný název.|Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   | **Oblast** |Oblast, ve které chcete vytvořit spravovanou instanci.|Informace o oblastech najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/).|
| **Přihlašovací jméno správce spravované instance** | Jakékoli platné uživatelské jméno. | Platné názvy najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Nepoužívejte "serveradmin", protože to je vyhrazená role na úrovni serveru.|
   | **Heslo** | Jakékoli platné heslo.| Heslo musí obsahovat nejméně 16 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Vyberte možnost **Konfigurace spravované instance** pro velikost výpočetních prostředků a prostředků úložiště a zkontrolujte možnosti cenové úrovně.
- Pomocí posuvník nebo textových polí zadejte velikost úložiště a počet virtuálních jader.
- Až budete hotovi, vyberte **použít** a uložte svůj výběr. 

  ![formulář spravované instance](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

Kliknutím na **zkontrolovat + vytvořit** můžete před vytvořením spravované instance zkontrolovat vybrané možnosti, nebo můžete vlastní konfiguraci sítě kliknutím na **další: Sítě**.

### <a name="networking-tab"></a>Karta sítě

Vyplníte volitelné informace na kartě sítě pomocí následující tabulky. V případě, že jsou tyto informace vynechány, použije se výchozí nastavení.

   ![Karta pro vytvoření sítě spravované instance](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   | **Virtuální síť** | Vyberte buď možnost **vytvořit novou virtuální síť** , nebo platnou virtuální síť a podsíť.| Pokud síť nebo podsíť není k dispozici, je nutné ji [upravit tak, aby splňovala požadavky na síť](sql-database-managed-instance-configure-vnet-subnet.md) předtím, než ji vyberete jako cíl pro novou spravovanou instanci. Informace o požadavcích na konfiguraci síťového prostředí pro spravovanou instanci najdete v tématu [Konfigurace virtuální sítě pro spravovanou instanci](sql-database-managed-instance-connectivity-architecture.md). |
   | **Typ připojení** | Vyberte mezi proxy serverem a typem připojení přesměrování.|Další informace o typech připojení najdete v tématu [Azure SQL Database zásady připojení](sql-database-connectivity-architecture.md#connection-policy).|
   | **Veřejný koncový bod**  | Tuto možnost vyberte, pokud chcete povolit veřejný koncový bod. | Aby bylo možné získat přístup ke spravované instanci prostřednictvím koncového bodu veřejné dat, je nutné vybrat možnost **Povolit** veřejný koncový bod. | 
   | **Povolení přístupu z** (v případě, že je veřejný koncový bod nastaven na povoleno) | Vyberte jednu z možností: <ul> <li>**Služby Azure**</li> <li>**Internet**</li> <li>**Bez přístupu**</li></ul>   |Prostředí portálu umožňuje konfigurovat skupinu zabezpečení s veřejným koncovým bodem. </br> </br> V závislosti na vašem scénáři vyberte jednu z následujících možností: </br> <ul> <li>Služby Azure – doporučuje se při připojování z Power BI nebo jiné služby pro více tenantů. </li> <li> Internet – použijte pro účely testování, pokud chcete rychle aktivovat spravovanou instanci. Nedoporučuje se pro použití v produkčních prostředích. </li> <li> Bez přístupu – Tato možnost vytvoří pravidlo zabezpečení Odepřít. Abyste mohli spravovat spravovanou instanci prostřednictvím veřejného koncového bodu, budete muset toto pravidlo upravit. </li> </ul> </br> Další informace o zabezpečení veřejného koncového bodu najdete v tématu [použití spravované instance Azure SQL Database bezpečně s veřejným koncovým bodem](sql-database-managed-instance-public-endpoint-securely.md).|

Můžete vybrat možnost **zkontrolovat + vytvořit** a před vytvořením spravované instance zkontrolovat vybrané možnosti, nebo můžete nastavit vlastní konfiguraci dalších nastavení kliknutím na **další: Další nastavení**

### <a name="additional-settings-tab"></a>Karta Další nastavení

Vyplňte volitelné informace na kartě **Další nastavení** pomocí následující tabulky. V případě, že jsou tyto informace vynechány, použije se výchozí nastavení.

   ![Spravovaná instance – vytvořit další kartu nastavení](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

   | Nastavení| Navrhovaná hodnota | Popis |
   | ------ | --------------- | ----------- |
   | **Velké** | Kolace, kterou chcete použít pro spravovanou instanci.|Pokud migrujete databáze z SQL Server, Projděte si zdrojovou kolaci `SELECT SERVERPROPERTY(N'Collation')` pomocí a použijte tuto hodnotu. Informace o kolacích najdete v tématu [nastavení nebo změna kolace serveru](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Časové pásmo** | Časové pásmo, které má vaše spravovaná instance dodržovat.|Další informace najdete v tématu [Časová pásma](sql-database-managed-instance-timezone.md).|
   | **Použít jako sekundární převzetí služeb při selhání** | Pokud chcete použít spravovanou instanci jako sekundární skupinu převzetí služeb při selhání, vyberte Ano. |
   | **Primární spravovaná instance** (v případě, že se použije jako sekundární převzetí služeb při selhání, se nastaví na Ano | Volba primární spravované instance (musí již existovat), se kterou spravovaná instance, kterou vytváříte, bude připojena ve stejné zóně DNS. Toto je krok předběžné podmínky, který umožňuje po vytvoření nakonfigurovat skupinu převzetí služeb při selhání. Podrobnosti najdete v [kurzu: Přidejte SQL Database spravovanou instanci do skupiny](sql-database-managed-instance-failover-group-tutorial.md)převzetí služeb při selhání. |

### <a name="review--create-tab"></a>Revize + vytvořit kartu

1. Vyberte kartu **Revize + vytvořit** a před vytvořením spravované instance si Projděte vybrané možnosti.

   ![Kontrola spravované instance a karta vytvořit](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

1. Výběrem **vytvořit** spusťte zřizování spravované instance.

> [!IMPORTANT]
> Nasazení spravované instance je dlouhodobě běžící operace. Nasazení první instance v podsíti obvykle trvá mnohem déle než nasazení do podsítě se stávajícími spravovanými instancemi. Průměrné doby zřizování najdete v tématu [operace správy spravované instance](sql-database-managed-instance.md#managed-instance-management-operations).

### <a name="monitor-deployment-progress"></a>Průběh nasazení monitorování

1. Výběrem ikony **oznámení** zobrazíte stav nasazení.

    ![průběh nasazení spravované instance](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

1. V oznámení vyberte **nasazení** . tím otevřete okno spravovaná instance, abyste mohli dále monitorovat průběh nasazení. 

> [!TIP]
> Pokud jste zavřeli webový prohlížeč nebo jste přešli z obrazovky průběh nasazení, postupujte podle těchto kroků a najděte obrazovku průběh nasazení:
> 1. V Azure Portal otevřete skupinu prostředků (zadanou na kartě **základní** ), do které nasazujete spravovanou instanci.
> 2. Vyberte **nasazení**.
> 3. Vyberte probíhající operaci nasazení spravované instance.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Kontrola prostředků a načtení plně kvalifikovaného názvu serveru

Po úspěšném nasazení zkontrolujte prostředky, které byly vytvořeny, a načtěte plně kvalifikovaný název serveru pro použití v pozdějších rychlých startech.

1. Otevřete skupinu prostředků pro spravovanou instanci. Prohlédněte si své prostředky, které jste vytvořili v rychlém startu [Vytvoření spravované instance](#create-a-managed-instance) .

   ![Prostředky spravované instance](./media/sql-database-managed-instance-get-started/resources.png)

2. Vyberte směrovací tabulku pro kontrolu tabulky trasy definované uživatelem (UDR), která byla vytvořena za vás.

   ![Tabulka směrování](./media/sql-database-managed-instance-get-started/route-table.png)

3. V tabulce směrování si prohlédněte položky, které směrují provoz z a do virtuální sítě spravované instance. Pokud tabulku směrování vytváříte nebo konfigurujete ručně, musíte tyto záznamy vytvořit v tabulce směrování.

   ![Položka pro podsíť spravované instance do místní sítě](./media/sql-database-managed-instance-get-started/udr.png)

4. Vraťte se do skupiny prostředků a vyberte skupinu zabezpečení sítě a zkontrolujte pravidla zabezpečení.

   ![Skupina zabezpečení sítě](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Zkontrolujte pravidla zabezpečení příchozích a odchozích připojení. Pokud jste pro spravovanou instanci nakonfigurovali veřejné koncové body, přečtěte si článek [Konfigurace veřejného koncového bodu](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) , kde najdete další informace.

   ![Pravidla zabezpečení](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Vraťte se do skupiny prostředků a vyberte spravovanou instanci.

   ![Spravovaná instance](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. Na kartě **Přehled** vyhledejte vlastnost **hostitel** . Zkopírujte plně kvalifikovanou adresu hostitele spravované instance pro použití v dalším rychlém startu.

   ![Název hostitele](./media/sql-database-managed-instance-get-started/host-name.png)

   Název se podobá **your_machine_name. a1b2c3d4e5f6. Database. Windows. NET**.

## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak se připojit ke spravované instanci:
  - Přehled možností připojení pro aplikace najdete v tématu [připojení aplikací ke spravované instanci](sql-database-managed-instance-connect-app.md).
  - Rychlý Start, který ukazuje, jak se připojit ke spravované instanci z virtuálního počítače Azure, najdete v tématu [Konfigurace připojení k virtuálnímu počítači Azure](sql-database-managed-instance-configure-vm.md).
  - Pro rychlý Start, který ukazuje, jak se připojit ke spravované instanci z místního klientského počítače pomocí připojení typu Point-to-site, najdete informace v tématu [Konfigurace připojení typu Point-to-site](sql-database-managed-instance-configure-p2s.md).
- Obnovení stávající databáze SQL Server z místního prostředí do spravované instance: 
    - Použijte [Azure Database Migration Service (DMS) pro migraci](../dms/tutorial-sql-server-to-managed-instance.md) k obnovení ze záložního souboru databáze. 
    - Použijte [příkaz T-SQL Restore](sql-database-managed-instance-get-started-restore.md) k obnovení ze záložního souboru databáze.
- Pro pokročilé monitorování výkonu databáze spravované instance s integrovanými funkcemi řešení potíží najdete informace v tématu [monitorování Azure SQL Database pomocí Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
