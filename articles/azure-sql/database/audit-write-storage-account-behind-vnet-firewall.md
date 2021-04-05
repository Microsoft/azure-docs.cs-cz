---
title: Audit na účet úložiště za virtuální sítí a branou firewall
description: Konfigurace auditování pro zápis událostí databáze na účtu úložiště za virtuální sítí a bránou firewall
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 06/17/2020
ms.custom: azure-synapse
ms.openlocfilehash: 908c9f1d05c83eaa58f77b79a32d956898c35076
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93348249"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Zápis auditu do účtu úložiště za virtuální sítí a branou firewall
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Auditování pro [Azure SQL Database](sql-database-paas-overview.md) a [Azure synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) podporuje zápis událostí databáze na [účet Azure Storage](../../storage/common/storage-account-overview.md) za virtuální sítí a bránou firewall.

Tento článek vysvětluje dva způsoby, jak nakonfigurovat Azure SQL Database a účet úložiště Azure pro tuto možnost. První používá Azure Portal, druhá používá REST.

## <a name="background"></a>Pozadí

[Azure Virtual Network (VNET)](../../virtual-network/virtual-networks-overview.md) je základní stavební blok vaší privátní sítě v Azure. Virtuální síť umožňuje mnoha typům prostředků Azure, jako je Azure Virtual Machines (VM), bezpečně komunikovat mezi sebou, internetem a místními sítěmi. Virtuální síť je podobná tradiční síti ve vašem vlastním datovém centru, ale přináší další výhody infrastruktury Azure, jako je například škálování, dostupnost a izolace.

Další informace o konceptech virtuální sítě, osvědčených postupech a mnoha dalších najdete v tématu [co je Azure Virtual Network](../../virtual-network/virtual-networks-overview.md).

Další informace o tom, jak vytvořit virtuální síť, najdete v tématu [rychlý Start: vytvoření virtuální sítě pomocí Azure Portal](../../virtual-network/quick-create-portal.md).

## <a name="prerequisites"></a>Požadavky

Aby mohl audit zapisovat do účtu úložiště za virtuální sítí nebo bránou firewall, vyžadují se tyto požadavky:

> [!div class="checklist"]
>
> * Účet úložiště pro obecné účely v2. Pokud máte účet úložiště pro obecné účely v1 nebo blob, [upgradujte na účet úložiště pro obecné účely v2](../../storage/common/storage-account-upgrade.md). Další informace najdete v tématu [typy účtů úložiště](../../storage/common/storage-account-overview.md#types-of-storage-accounts).
> * Účet úložiště musí být ve stejném předplatném a ve stejném umístění jako [logický SQL Server](logical-servers.md).
> * Účet Azure Storage vyžaduje `Allow trusted Microsoft services to access this storage account` . Nastavte tuto hodnotu na **brány firewall a virtuální sítě** v účtu úložiště.
> * Musíte mít `Microsoft.Authorization/roleAssignments/write` oprávnění pro vybraný účet úložiště. Další informace najdete v tématu [Předdefinované role v Azure](../../role-based-access-control/built-in-roles.md).

## <a name="configure-in-azure-portal"></a>Konfigurace na webu Azure Portal

Připojte se k [Azure Portal](https://portal.azure.com) k vašemu předplatnému. Přejděte do skupiny prostředků a na server.

1. V záhlaví zabezpečení klikněte na **auditování** . Vyberte **zapnuto**.

2. Vyberte **úložiště**. Vyberte účet úložiště, do kterého se budou ukládat protokoly. Účet úložiště musí splňovat požadavky uvedené v části [požadavky](#prerequisites).

3. Otevřít **Podrobnosti o úložišti**

  > [!NOTE]
  > Pokud je vybraný účet úložiště za virtuální sítí, zobrazí se tato zpráva:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage requires to enable 'Allow trusted Microsoft services to access this storage account' on the storage account and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Pokud se tato zpráva nezobrazuje, účet úložiště není za virtuální sítí.

4. Vyberte počet dní pro dobu uchování. Pak klikněte na **OK**. Protokoly starší než doba uchování se odstraní.

5. V nastavení auditování vyberte **Uložit** .

Úspěšně jste nakonfigurovali audit pro zápis do účtu úložiště za virtuální sítí nebo bránou firewall.

## <a name="configure-with-rest-commands"></a>Konfigurace pomocí příkazů REST

Jako alternativu k používání Azure Portal můžete pomocí příkazů REST nakonfigurovat audit na zápis událostí databáze na účet úložiště za virtuální sítí a branou firewall.

Ukázkové skripty v této části vyžadují, abyste skript aktualizovali předtím, než je spustíte. Ve skriptech nahraďte následující hodnoty:

|Ukázková hodnota|Popis ukázky|
|:-----|:-----|
|`<subscriptionId>`| ID předplatného Azure|
|`<resource group>`| Skupina prostředků|
|`<logical SQL Server>`| Název serveru|
|`<administrator login>`| Účet správce |
|`<complex password>`| Složitá hesla pro účet správce|

Konfigurace auditu SQL pro zápis událostí do účtu úložiště za virtuální sítí nebo bránou firewall:

1. Zaregistrujte server pomocí Azure Active Directory (Azure AD). Použijte buď PowerShell, nebo REST API.

   **PowerShell**

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```

   [**REST API**](/rest/api/sql/servers/createorupdate):

   Ukázková žádost

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
   }
   ```

2. Otevřete [Azure Portal](https://portal.azure.com). Přejděte na svůj účet úložiště. Vyhledejte **Access Control (IAM)** a klikněte na **Přidat přiřazení role**. Přiřaďte roli Azure **Přispěvatel dat objektů BLOB úložiště** k serveru, který je hostitelem databáze, kterou jste zaregistrovali ve službě Azure Active Directory (Azure AD) jako v předchozím kroku.

   > [!NOTE]
   > Tento krok mohou provádět pouze členové s oprávněním vlastníka. Informace o různých předdefinovaných rolích Azure najdete [v tématu předdefinované role Azure](../../role-based-access-control/built-in-roles.md).

3. Nakonfigurujte [zásady auditování objektů BLOB serveru](/rest/api/sql/server%20auditing%20settings/createorupdate)bez zadání *storageAccountAccessKey*:

   Ukázková žádost

   ```html
     PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>/auditingSettings/default?api-version=2017-03-01-preview
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

## <a name="using-azure-powershell"></a>Použití Azure Powershell

- [Vytvořit nebo aktualizovat zásady auditování databáze (set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Vytvořit nebo aktualizovat zásady auditování serveru (set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)

## <a name="using-azure-resource-manager-template"></a>Pomocí šablony Azure Resource Manageru

Můžete nakonfigurovat auditování pro zápis událostí databáze na účet úložiště za virtuální sítí a bránou firewall pomocí [Azure Resource Manager](../../azure-resource-manager/management/overview.md) šablony, jak je znázorněno v následujícím příkladu:

> [!IMPORTANT]
> Aby bylo možné používat účet úložiště za virtuální sítí a bránou firewall, je třeba nastavit parametr **isStorageBehindVnet** na hodnotu true.

- [Nasazení Azure SQL Server s povoleným auditováním pro zápis protokolů auditu do úložiště objektů BLOB](https://azure.microsoft.com/resources/templates/201-sql-auditing-server-policy-to-blob-storage)

> [!NOTE]
> Propojená ukázka je na externím veřejném úložišti a je poskytována "tak, jak je", bez záruky a není podporována v rámci žádného programu nebo služby podpory společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

* [Pomocí PowerShellu vytvořte koncový bod služby virtuální sítě a potom pravidlo virtuální sítě pro Azure SQL Database.](scripts/vnet-service-endpoint-rule-powershell-create.md)
* [Pravidla Virtual Network: operace s rozhraními REST API](/rest/api/sql/virtualnetworkrules)
* [Použití koncových bodů a pravidel služby virtuální sítě pro servery](vnet-service-endpoint-rule-overview.md)
