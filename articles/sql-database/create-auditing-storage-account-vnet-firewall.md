---
title: Auditování účtu úložiště za virtuální sítí a bránou firewall
description: Konfigurace auditování pro zápis událostí databáze na účet úložiště za virtuální sítí a bránou firewall
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/19/2020
ms.custom: azure-synapse
ms.openlocfilehash: 6345d210e26747f921595039a2a3c8e11be11fda
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387625"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Zápis auditu do účtu úložiště za virtuální sítí a bránou firewall

Auditování pro [Azure SQL Database](sql-database-technical-overview.md) a Azure [Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) podporuje zápis databázových událostí do [účtu Azure Storage](../storage/common/storage-account-overview.md) za virtuální sítí a bránou firewall. 

Tento článek vysvětluje dva způsoby konfigurace Azure SQL Server a účet úložiště Azure pro tuto možnost. První používá portál Azure, druhý používá REST.

### <a name="background"></a>Pozadí

[Virtuální síť Azure (VNet)](../virtual-network/virtual-networks-overview.md) je základní stavební blok pro vaši privátní síť v Azure. Virtuální síť umožňuje mnoho typů prostředků Azure, jako jsou virtuální počítače Azure (VM), bezpečně komunikovat mezi sebou, internet a místní sítě. Virtuální síť se podobá tradiční síti ve vašem vlastním datovém centru, ale přináší s sebou další výhody infrastruktury Azure, jako je škálování, dostupnost a izolace.

Další informace o konceptech virtuální sítě, doporučených postupech a mnoha dalších tématech najdete [v tématu Co je virtuální síť Azure](../virtual-network/virtual-networks-overview.md).

Další informace o tom, jak vytvořit virtuální síť, najdete v [tématu Úvodní příručka: Vytvoření virtuální sítě pomocí portálu Azure](../virtual-network/quick-create-portal.md).

## <a name="prerequisites"></a>Požadavky

Pro audit zápis u účtu úložiště za virtuální sítí nebo bránou firewall jsou vyžadovány následující požadavky:

> [!div class="checklist"]
> * Účet úložiště pro obecné účely v2. Pokud máte účet úložiště pro obecné účely v1 nebo objektblo, [upgradujte na účet úložiště pro obecné účely v2](../storage/common/storage-account-upgrade.md). Další informace naleznete [v tématu Typy účtů úložiště](../storage/common/storage-account-overview.md#types-of-storage-accounts).
> * Účet úložiště musí být ve stejném předplatném a ve stejném umístění jako server Azure SQL Database. 
> * Účet Azure Storage `Allow trusted Microsoft services to access this storage account`vyžaduje . Tuto možnost nastavte v bráně firewall účtu úložiště **a ve virtuálních sítích**.
> * Musíte mít `Microsoft.Authorization/roleAssignments/write` oprávnění k vybranému účtu úložiště. Další informace najdete [v tématu Předdefinované role Azure](../role-based-access-control/built-in-roles.md).

## <a name="configure-in-azure-portal"></a>Konfigurace na webu Azure Portal

Připojte se k [portálu Azure](https://portal.azure.com) s předplatným. Přejděte na skupinu prostředků a databázový server Azure SQL.

1. Klikněte na **Auditování** v záhlaví Zabezpečení. Vyberte **Možnost Zapnuto**.

2. Vyberte **možnost Úložiště**. Vyberte účet úložiště, do kterého budou protokoly uloženy. Účet úložiště musí splňovat požadavky uvedené v [části Požadavky](#prerequisites).

3. Otevřít **podrobnosti úložiště** 

  > [!NOTE]
  > Pokud je vybraný účet úložiště za virtuální sítí, zobrazí se následující zpráva:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage: requires an Active Directory admin on the server; enables 'Allow trusted Microsoft services to access this storage account' on the storage account; and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Pokud tuto zprávu nevidíte, účet úložiště není za virtuální sítí.

4. Vyberte počet dní pro retenční období. Pak klikněte na **OK**. Protokoly starší než doba uchování jsou odstraněny.

5. Vyberte **Uložit** nastavení auditování.

Audit jste úspěšně nakonfigurovali tak, aby zapisoval do účtu úložiště za virtuální sítí nebo bránou firewall. 

## <a name="configure-with-rest-commands"></a>Konfigurace pomocí příkazů REST

Jako alternativu k používání portálu Azure můžete pomocí příkazů REST nakonfigurovat audit k zápisu událostí databáze na účet úložiště za virtuální sítí a bránou firewall. 

Ukázkové skripty v této části vyžadují aktualizaci skriptu před jejich spuštěním. Ve skriptech nahraďte následující hodnoty:

|Ukázková hodnota|Popis vzorku|
|:-----|:-----|
|`<subscriptionId>`| ID předplatného Azure|
|`<resource group>`| Skupina prostředků|
|`<sql database server>`| Název databázového serveru Azure SQL|
|`<administrator login>`| Účet správce databáze SQL |
|`<complex password>`| Komplexní heslo pro účet správce|

Konfigurace nástroje SQL Audit pro zápis událostí do účtu úložiště za virtuální sítí nebo bránou firewall:

1. Zaregistrujte svůj server Azure SQL Database pomocí Azure Active Directory (Azure AD). Použijte rozhraní API prostředí PowerShell nebo REST.

   **PowerShell**
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```
   
   [**REST API**](https://docs.microsoft.com/rest/api/sql/servers/createorupdate):

   Požadavek na ukázku

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   Text požadavku

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
   }
   ```

2. Otevřete [Azure Portal](https://portal.azure.com). Přejděte na svůj účet úložiště. Vyhledejte **řízení přístupu (IAM)** a klepněte na tlačítko **Přidat přiřazení role**. Přiřaďte roli RBAC **přispěvatele objektů blob úložiště** k vaší Azure SQL Serveru, která hostuje vaši databázi Azure SQL, kterou jste zaregistrovali ve službě Azure Active Directory (Azure AD) jako v předchozím kroku.

   > [!NOTE]
   > Tento krok mohou provést pouze členové s oprávněním vlastníka. Pro různé předdefinované role pro prostředky Azure, najdete v části [Azure integrované role](../role-based-access-control/built-in-roles.md).

3. Nakonfigurujte [zásady auditování objektů blob serveru Azure SQL](/rest/api/sql/server%20auditing%20settings/createorupdate)bez zadání klíče *storageAccountAccessKey*:

   Požadavek na ukázku

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2017-03-01-preview
   ```

   Text požadavku

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="next-steps"></a>Další kroky

- [Pomocí PowerShellu vytvořte koncový bod virtuální síťové služby a pak pravidlo virtuální sítě pro Azure SQL Database.](sql-database-vnet-service-endpoint-rule-powershell.md)
- [Pravidla virtuální sítě: Operace s rest API](/rest/api/sql/virtualnetworkrules)
- [Použití koncových bodů a pravidel služby virtuální sítě pro databázové servery](sql-database-vnet-service-endpoint-rule-overview.md)
