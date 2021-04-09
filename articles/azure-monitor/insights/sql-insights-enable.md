---
title: Povolit SQL Insights
description: Povolit SQL Insights v Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: e8dd887d151eb553131048f232940555dbef324b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025029"
---
# <a name="enable-sql-insights-preview"></a>Povolit SQL Insights (Náhled)
Tento článek popisuje, jak povolit [SQL Insights](sql-insights-overview.md) pro monitorování vašich nasazení SQL. Monitorování se provádí z virtuálního počítače Azure, který vytváří připojení k vašim nasazením SQL a pomocí zobrazení dynamické správy (zobrazení dynamické správy) shromažďuje data monitorování. Můžete řídit, které datové sady jsou shromažďovány a četnost shromažďování pomocí profilu monitorování.

## <a name="create-log-analytics-workspace"></a>Vytvoření pracovního prostoru služby Log Analytics
SQL Insights ukládá data do jednoho nebo více [Log Analytics pracovních prostorů](../logs/data-platform-logs.md#log-analytics-workspaces).  Než budete moct povolit SQL Insights, musíte buď [vytvořit pracovní prostor](../logs/quick-create-workspace.md) , nebo vybrat existující. Jeden pracovní prostor se dá použít s několika profily monitorování, ale pracovní prostor a profily se musí nacházet ve stejné oblasti Azure. K povolení a přístupu k funkcím v SQL Insights potřebujete [roli přispěvatel Log Analytics](../logs/manage-access.md) v pracovním prostoru. 

## <a name="create-monitoring-user"></a>Vytvořit monitorovacího uživatele 
Potřebujete uživatele na nasazeních SQL, která chcete monitorovat. Použijte následující postupy pro různé typy nasazení SQL.

### <a name="azure-sql-database"></a>Databáze Azure SQL
V Azure Portal otevřete Azure SQL Database pomocí [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) nebo [Editoru dotazů (Preview)](../../azure-sql/database/connect-query-portal.md) .

Spusťte následující skript, který vytvoří uživatele s požadovanými oprávněními. Nahraďte *uživatele uživatelským* jménem a *mystrongpassword* heslem.

```
CREATE USER [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW DATABASE STATE TO [user]; 
GO 
```

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-script.png" alt-text="Vytvořte uživatelský skript telegraf." lightbox="media/sql-insights-enable/telegraf-user-database-script.png":::

Ověřte, zda byl uživatel vytvořen.

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-verify.png" alt-text="Ověřte uživatelský skript telegraf." lightbox="media/sql-insights-enable/telegraf-user-database-verify.png":::

### <a name="azure-sql-managed-instance"></a>Spravovaná instance Azure SQL
Přihlaste se ke spravované instanci SQL Azure a pomocí [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) nebo podobného nástroje spusťte následující skript, který vytvoří uživatele monitorování s potřebnými oprávněními. Nahraďte *uživatele uživatelským* jménem a *mystrongpassword* heslem.

 
```
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO 
```

### <a name="sql-server"></a>SQL Server
Přihlaste se k virtuálnímu počítači Azure se systémem SQL Server a pomocí [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) nebo podobného nástroje spusťte následující skript, který vytvoří uživatele monitorování s požadovanými oprávněními. Nahraďte *uživatele uživatelským* jménem a *mystrongpassword* heslem.

 
```
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO
```

## <a name="create-azure-virtual-machine"></a>Vytvořit virtuální počítač Azure 
Budete muset vytvořit jeden nebo víc virtuálních počítačů Azure, které se použijí ke shromažďování dat pro monitorování SQL.  

> [!NOTE]
>  [Profily monitorování](#create-sql-monitoring-profile) určují, jaká data budou shromažďována z různých typů SQL, které chcete monitorovat. Ke každému virtuálnímu počítači monitorování může být přidružen pouze jeden profil monitorování. Pokud potřebujete více profilů monitorování, musíte pro každý z nich vytvořit virtuální počítač.

### <a name="azure-virtual-machine-requirements"></a>Požadavky na virtuální počítače Azure
Virtuální počítače Azure mají následující požadavky.

- Operační systém: Ubuntu 18,04 
- Doporučené velikosti virtuálních počítačů Azure: Standard_B2s (2 procesory, 4 GiB paměť) 
- Podporované oblasti: jakákoli [oblast podporovaná agentem Azure monitor](../agents/azure-monitor-agent-overview.md#supported-regions)

> [!NOTE]
> Velikost virtuálního počítače Standard_B2s (2 CPU, 4 GiB paměť) bude podporovat až 100 připojovacích řetězců. Neměli byste přidělit více než 100 připojení k jednomu virtuálnímu počítači.

V závislosti na nastavení sítě vašich prostředků SQL může být nutné umístit virtuální počítače do stejné virtuální sítě jako prostředky SQL, aby mohli síťová připojení shromažďovat data monitorování.  

## <a name="configure-network-settings"></a>Konfigurace nastavení sítě
Každý typ SQL nabízí metody pro váš virtuální počítač pro monitorování, aby mohl bezpečně přistupovat k SQL.  Níže uvedené části obsahují možnosti založené na typu SQL.

### <a name="azure-sql-databases"></a>Azure SQL Databases  

SQL Insights podporuje přístup k vašemu Azure SQL Database prostřednictvím veřejného koncového bodu a také z jeho virtuální sítě.

Pro přístup prostřednictvím veřejného koncového bodu byste přidali pravidlo na stránce **nastavení brány firewall** a v části [nastavení brány firewall protokolu IP](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-firewall-rules) .  Pro zadání přístupu z virtuální sítě můžete nastavit [pravidla brány firewall virtuální sítě](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#virtual-network-firewall-rules) a nastavit [značky služby vyžadované agentem Azure monitor](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview#networking).  [Tento článek](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-vs-virtual-network-firewall-rules) popisuje rozdíly mezi těmito dvěma typy pravidel brány firewall.

:::image type="content" source="media/sql-insights-enable/set-server-firewall.png" alt-text="Nastavení brány firewall serveru" lightbox="media/sql-insights-enable/set-server-firewall.png":::

:::image type="content" source="media/sql-insights-enable/firewall-settings.png" alt-text="Nastavení brány firewall." lightbox="media/sql-insights-enable/firewall-settings.png":::


### <a name="azure-sql-managed-instances"></a>Azure SQL Managed Instances 

Pokud bude váš virtuální počítač pro monitorování ve stejné virtuální síti jako prostředky SQL moje, najdete informace v tématu [připojení ve stejné virtuální](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-the-same-vnet)síti. Pokud se váš virtuální počítač pro monitorování nachází v jiné virtuální síti než vaše prostředky SQL moje, podívejte se do tématu [připojení v jiné virtuální](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-a-different-vnet)síti.


### <a name="azure-virtual-machine-and-azure-sql-virtual-machine"></a>Virtuální počítač Azure a virtuální počítač Azure SQL  
Pokud je váš virtuální počítač pro monitorování ve stejné virtuální síti jako prostředky virtuálních počítačů SQL, najdete informace v tématu [připojení k SQL Server v rámci virtuální sítě](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-within-a-virtual-network). Pokud se váš virtuální počítač pro monitorování nachází v jiné virtuální síti než vaše prostředky virtuálních počítačů SQL, najdete informace v tématu  [připojení k SQL Server přes Internet](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-over-the-internet).

## <a name="store-monitoring-password-in-key-vault"></a>Ukládat heslo monitorování v Key Vault
Heslo pro připojení uživatele SQL byste měli ukládat do Key Vault místo toho, aby je bylo možné zadávat přímo do připojovacích řetězců k profilům monitorování.

Když nastavíte profil pro monitorování SQL, budete potřebovat jedno z následujících oprávnění pro Key Vault prostředek, který chcete použít:

- Microsoft.Authorization/roleAssignments/write 
- Oprávnění Microsoft. Authorization/roleAssignments/DELETE, jako je například správce nebo vlastník přístupu uživatele 

Nové zásady přístupu se automaticky vytvoří v rámci vytváření profilu SQL Monitoring, který používá Key Vault, které jste zadali. Pro Key Vault nastavení sítě použijte možnost *Povolení přístupu ze všech sítí* .


## <a name="create-sql-monitoring-profile"></a>Vytvořit profil monitorování SQL
Otevřete SQL Insights výběrem **SQL (Preview)** v části **přehled** v nabídce **Azure monitor** v Azure Portal. Klikněte na **vytvořit nový profil**. 

:::image type="content" source="media/sql-insights-enable/create-new-profile.png" alt-text="Vytvořte nový profil." lightbox="media/sql-insights-enable/create-new-profile.png":::

Profil uloží informace, které chcete shromažďovat ze systémů SQL.  Má konkrétní nastavení pro: 

- Azure SQL Database 
- Azure SQL Managed Instances 
- SQL Server spuštěných na virtuálních počítačích  

Můžete například vytvořit jeden profil s názvem *produkce SQL* a jiný s názvem *Příprava SQL* s různými nastaveními pro frekvenci shromažďování dat, jaká data se mají shromáždit a který pracovní prostor pro odeslání dat. 

Profil je uložený jako prostředek [pravidla shromažďování dat](../agents/data-collection-rule-overview.md) v předplatném a skupině prostředků, kterou vyberete. Každý profil potřebuje následující:

- Název. Po vytvoření nelze upravovat.
- Umístění. Toto je oblast Azure.
- Log Analytics pracovní prostor pro uložení dat monitorování.
- Nastavení kolekce pro četnost a typ dat monitorování SQL ke shromáždění.

> [!NOTE]
> Umístění profilu by mělo být ve stejném umístění jako Log Analytics pracovní prostor, do kterého plánujete odeslat data monitorování.


:::image type="content" source="media/sql-insights-enable/profile-details.png" alt-text="Podrobnosti profilu." lightbox="media/sql-insights-enable/profile-details.png":::

Po zadání podrobností pro váš profil monitorování klikněte na **vytvořit profil monitorování** . Nasazení profilu může trvat až minutu.  Pokud nevidíte nový profil uvedený v poli se seznamem **profil monitorování** , klikněte na tlačítko Aktualizovat a měl by se zobrazit po dokončení nasazení.  Po výběru nového profilu vyberte kartu **Spravovat profil** a přidejte monitorovací počítač, který bude přidružen k profilu.

### <a name="add-monitoring-machine"></a>Přidat monitorovací počítač
Vyberte **Přidat monitorovací počítač** a otevřete tak kontextový panel a vyberte virtuální počítač, který chcete nastavit pro monitorování instancí SQL a zadání připojovacích řetězců.

Vyberte předplatné a název vašeho virtuálního počítače pro monitorování. Pokud používáte Key Vault k ukládání hesla pro uživatele monitorování, vyberte prostředky Key Vault s těmito tajnými kódy a zadejte adresu URL a tajný kód, který se má použít v připojovacích řetězcích. Podrobnosti o identifikaci připojovacího řetězce pro různá nasazení SQL najdete v další části.


:::image type="content" source="media/sql-insights-enable/add-monitoring-machine.png" alt-text="Přidejte monitorovací počítač." lightbox="media/sql-insights-enable/add-monitoring-machine.png":::


### <a name="add-connection-strings"></a>Přidat připojovací řetězce 
Připojovací řetězec Určuje uživatelské jméno, které má SQL Insights použít při přihlašování do SQL pro spouštění zobrazení dynamické správy. Pokud používáte Key Vault k ukládání hesla pro uživatele monitorování, zadejte adresu URL a název tajného klíče, který chcete použít. 

Řetězec připojení se bude lišit pro každý typ prostředku SQL:

#### <a name="azure-sql-databases"></a>Azure SQL Databases 
Zadejte připojovací řetězec ve tvaru:

```
sqlAzureConnections": [ 
   "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=$username;Password=$password;" 
}
```

Získejte podrobnosti z položky nabídky **připojovací řetězce** pro databázi.

:::image type="content" source="media/sql-insights-enable/connection-string-sql-database.png" alt-text="Připojovací řetězec databáze SQL" lightbox="media/sql-insights-enable/connection-string-sql-database.png":::

Chcete-li monitorovat čitelnou sekundární, zahrňte do `ApplicationIntent=ReadOnly` připojovacího řetězce klíčovou hodnotu.


#### <a name="azure-virtual-machines-running-sql-server"></a>Virtuální počítače Azure se systémem SQL Server 
Zadejte připojovací řetězec ve tvaru:

```
"sqlVmConnections": [ 
   "Server=MyServerIPAddress;Port=1433;User Id=$username;Password=$password;" 
] 
```

Pokud je váš virtuální počítač pro monitorování ve stejné virtuální síti, použijte privátní IP adresu serveru.  V opačném případě použijte veřejnou IP adresu. Pokud používáte virtuální počítač Azure SQL, můžete na stránce **zabezpečení** pro daný prostředek zjistit, který port chcete použít.

:::image type="content" source="media/sql-insights-enable/sql-vm-security.png" alt-text="Zabezpečení virtuálního počítače SQL" lightbox="media/sql-insights-enable/sql-vm-security.png":::

Chcete-li monitorovat čitelnou sekundární, zahrňte do `ApplicationIntent=ReadOnly` připojovacího řetězce klíčovou hodnotu.


### <a name="azure-sql-managed-instances"></a>Azure SQL Managed Instances 
Zadejte připojovací řetězec ve tvaru:

```
"sqlManagedInstanceConnections": [ 
      "Server= mysqlserver.database.windows.net;Port=1433;User Id=$username;Password=$password;", 
    ] 
```
Získá podrobnosti z položky nabídky **připojovací řetězce** pro spravovanou instanci.


:::image type="content" source="media/sql-insights-enable/connection-string-sql-managed-instance.png" alt-text="Připojovací řetězec spravované instance SQL" lightbox="media/sql-insights-enable/connection-string-sql-managed-instance.png":::

Chcete-li monitorovat čitelnou sekundární, zahrňte do `ApplicationIntent=ReadOnly` připojovacího řetězce klíčovou hodnotu.



## <a name="monitoring-profile-created"></a>Profil monitorování se vytvořil. 

Vyberte **Přidat virtuální počítač monitorování** a nakonfigurujte virtuální počítač tak, aby shromáždil data z vašich prostředků SQL. Nevratte se zpět na kartu **Přehled** .  Během několika minut by se měl sloupec stav změnit, aby se načetlo "shromažďování", měli byste vidět data pro prostředky SQL, které jste se rozhodli monitorovat.

Pokud nevidíte data, přečtěte si téma [řešení potíží s SQL Insights](sql-insights-troubleshoot.md) k identifikaci problému. 

:::image type="content" source="media/sql-insights-enable/profile-created.png" alt-text="Profil vytvořen" lightbox="media/sql-insights-enable/profile-created.png":::

> [!NOTE]
> Pokud potřebujete aktualizovat svůj profil monitorování nebo připojovací řetězce na vašich virtuálních počítačích monitorování, můžete to udělat přes kartu SQL Insights **Spravovat profil** .  Po uložení aktualizací se změny použijí přibližně po dobu 5 minut.

## <a name="next-steps"></a>Další kroky

- Přečtěte si téma [řešení potíží s SQL Insights](sql-insights-troubleshoot.md) , pokud SQL Insights po povolení nefunguje správně.
