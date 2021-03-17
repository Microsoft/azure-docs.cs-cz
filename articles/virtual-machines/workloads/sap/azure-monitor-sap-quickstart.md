---
title: Nasazení Azure Monitor pro řešení SAP pomocí Azure Portal
description: Nasazení Azure Monitor pro řešení SAP pomocí Azure Portal
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.date: 08/17/2020
ms.openlocfilehash: d9febb4efba85d47abe1cc11a3cb52dc0393c036
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672008"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>Nasazení Azure Monitor pro řešení SAP pomocí Azure Portal

Azure Monitor pro prostředky řešení SAP lze vytvořit prostřednictvím [Azure Portal](https://azure.microsoft.com/features/azure-portal). Tato metoda poskytuje uživatelské rozhraní založené na prohlížeči pro nasazení Azure Monitor pro řešení SAP a konfiguraci zprostředkovatelů.

## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-monitoring-resource"></a>Vytvořit prostředek monitorování

1. Vyberte **Azure monitor pro řešení SAP** z **Azure Marketplace**.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="Image zobrazuje výběr Azure Monitor pro řešení SAP, které nabízí Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. Na kartě **základy** zadejte požadované hodnoty. V případě potřeby můžete použít existující pracovní prostor Log Analytics.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Zobrazení možností konfigurace Azure Portal." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. Při výběru virtuální sítě se ujistěte, že jsou systémy, které chcete monitorovat, dosažitelné v rámci této virtuální sítě. 

   > [!IMPORTANT]
   > Výběr **sdílené složky** pro sdílení dat s Microsoftem umožňuje našim týmům podpory poskytovat další podporu.

## <a name="configure-providers"></a>Konfigurovat zprostředkovatele

### <a name="sap-hana-provider"></a>Poskytovatel SAP HANA 

1. Vyberte kartu **zprostředkovatel** a přidejte zprostředkovatele, které chcete konfigurovat. Po nasazení prostředku monitorování můžete přidat více poskytovatelů nebo je přidat. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Zobrazí kartu poskytovatel, kde můžete přidat další poskytovatele Azure Monitor pro řešení SAP." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. Vyberte **Přidat poskytovatele** a v rozevíracím seznamu zvolte **SAP HANA** . 

   > [!IMPORTANT]
   > Zajistěte, aby byl zprostředkovatel SAP HANA nakonfigurovaný pro SAP HANA "hlavní" uzel.

3. Zadejte privátní IP adresu pro server HANA.

4. Zadejte název tenanta databáze, který chcete použít. Můžete si ale vybrat libovolného klienta. doporučujeme používat **SYSTEMDB** , protože umožňuje širší spektrum oblastí monitorování. 

5. Zadejte číslo portu SQL přidružené k vaší databázi HANA. Číslo portu by mělo být ve formátu **[3]**  +  **[instance #]**  +  **[13]**. Například 30013. 

6. Zadejte uživatelské jméno databáze, které chcete použít. Zajistěte, aby měl uživatel databáze přiřazené role **monitorování** a **katalog** . 

7. Po dokončení vyberte **Přidat poskytovatele**. V případě potřeby pokračujte v přidávání dalších zprostředkovatelů nebo kliknutím na tlačítko **zkontrolovat + vytvořit** dokončete nasazení.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Obrázek možností konfigurace při přidávání informací o poskytovateli." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>Poskytovatel clusteru s vysokou dostupností (Pacemaker)

1. Z rozevíracího seznamu vyberte **cluster s vysokou dostupností (Pacemaker)** . 

   > [!IMPORTANT]
   > Pokud chcete nakonfigurovat poskytovatele clusteru s vysokou dostupností (Pacemaker), ujistěte se, že je v každém uzlu nainstalovaný ha_cluster_provider. Další informace najdete v tématu o [exportéru clusteru ha](https://github.com/ClusterLabs/ha_cluster_exporter#installation) .

2. Zadejte koncový bod Prometheus ve formátu http://IP:9664/metrics . 
 
3. Zadejte ID systému (SID), název hostitele a název clusteru.

4. Po dokončení vyberte **Přidat poskytovatele**. V případě potřeby pokračujte v přidávání dalších zprostředkovatelů nebo kliknutím na tlačítko **zkontrolovat + vytvořit** dokončete nasazení.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="Image zobrazuje možnosti související s poskytovatelem Pacemaker clusteru HA." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::


### <a name="os-linux-provider"></a>Poskytovatel operačního systému (Linux) 

1. V rozevíracím seznamu vyberte operační systém (Linux). 

> [!IMPORTANT]
> Pokud chcete nakonfigurovat poskytovatele operačního systému (Linux), ujistěte se, že je v každé instanci BareMetal nainstalovaná Node_Exporter. Další informace najdete v tématu [Node_Exporter](https://github.com/prometheus/node_exporter)

2. Zadejte název, který bude identifikátorem instance BareMetal.
3. Zadejte koncový bod exportéra uzlu ve formátu http://IP:9100/metrics .
4. Po dokončení vyberte **Přidat poskytovatele**. V případě potřeby pokračujte v přidávání dalších zprostředkovatelů nebo kliknutím na tlačítko **zkontrolovat + vytvořit**   dokončete nasazení. 


### <a name="microsoft-sql-server-provider"></a>Poskytovatel Microsoft SQL Server

1. Před přidáním poskytovatele Microsoft SQL Server byste měli spustit následující skript v SQL Server Management Studio a vytvořit uživatele s příslušnými oprávněními potřebnými ke konfiguraci poskytovatele.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. Vyberte **Přidat poskytovatele** a v rozevíracím seznamu zvolte **Microsoft SQL Server** . 

3. Vyplňte pole pomocí informací přidružených k vašemu Microsoft SQL Server. 

4. Po dokončení vyberte **Přidat poskytovatele**. V případě potřeby pokračujte v přidávání dalších zprostředkovatelů nebo kliknutím na tlačítko **zkontrolovat + vytvořit** dokončete nasazení.

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="Image zobrazuje informace související s přidáním poskytovatele Microsoft SQL Server." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

## <a name="next-steps"></a>Další kroky

Další informace o [Azure monitor pro řešení SAP](azure-monitor-overview.md)
