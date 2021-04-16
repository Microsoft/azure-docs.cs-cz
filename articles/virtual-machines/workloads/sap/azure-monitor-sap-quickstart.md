---
title: Nasazení Azure Monitor pro řešení SAP pomocí Azure Portal
description: Nasazení Azure Monitor pro řešení SAP pomocí Azure Portal
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.date: 08/17/2020
ms.openlocfilehash: 908de54ee66772f3eb648895529c843675c3bf15
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538647"
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

### <a name="sap-netweaver-provider"></a>Poskytovatel SAP NetWeaver

#### <a name="prerequisites-for-adding-netweaver-provider"></a>Předpoklady pro přidání poskytovatele NetWeaver

Služba SAP Start Service poskytuje hostitele služeb, včetně monitorování systému SAP. Využíváme "SAPControl", což je rozhraní webové služby SOAP, které zpřístupňuje tyto možnosti. Toto rozhraní SAPControl WebService rozlišuje mezi [chráněnými a nechráněnými](https://wiki.scn.sap.com/wiki/display/SI/Protected+web+methods+of+sapstartsrv) metodami WebService. Aby bylo možné načíst konkrétní metriky, budete muset zrušit ochranu některých metod. Pokud chcete zrušit ochranu požadovaných metod pro aktuální vydání, postupujte prosím podle následujících kroků pro každý systém SAP:

1. Otevření připojení k serveru SAP v grafickém uživatelském rozhraní SAP
2. Přihlášení pomocí účtu správce
3. Spustit transakční RZ10
4. Vyberte vhodný profil (výchozí. PFL)
5. Vyberte rozšířená údržba a klikněte na změnit. 
6. Upravte hodnotu ovlivněného parametru "Service/protectedwebmethods" na "SDEFAULT-GetQueueStatistic-ABAPGetWPTable-EnqGetStatistic-GetProcessList" na Doporučené nastavení a klikněte na tlačítko Kopírovat.
7. Vraťte se zpátky a vyberte profil->Uložit.
8. Restartujte systém, aby se projevil platný parametr.

>[!Tip]
> K filtrování přístupu k portu serveru použijte seznam Access Control (ACL). Podívejte se na svou [poznámku ke SAP](https://launchpad.support.sap.com/#/notes/1495075) .

#### <a name="installing-netweaver-provider-on-the-azure-portal"></a>Instalace poskytovatele NetWeaver na Azure Portal
1.  Ujistěte se, že byly dokončeny požadované kroky a server byl restartován.
2.  V Azure Portal v části AMS vyberte Přidat poskytovatele a z rozevírací nabídky zvolte SAP NetWeaver.
3.  Zadejte název hostitele systému SAP a subdomény (Pokud je k dispozici).
4.  Zadejte číslo instance odpovídající zadanému názvu hostitele. 
5.  Zadejte ID systému (SID).
6.  Po dokončení vyberte Přidat poskytovatele.
7.  V případě potřeby dál přidejte další poskytovatele nebo vyberte zkontrolovat + vytvořit a dokončete nasazení.

![image](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

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

   >[!IMPORTANT]
   > Pokud chcete nakonfigurovat poskytovatele operačního systému (Linux), ujistěte se, že je na každém hostiteli (BareMetal nebo VM), který chcete monitorovat, nainstalovaná nejnovější verze Node_Exporter. Pomocí tohoto [odkazu](https://prometheus.io/download/#node_exporter) můžete najít nejnovější verzi. Další informace najdete v tématu [Node_Exporter](https://github.com/prometheus/node_exporter)

2. Zadejte název, který bude identifikátorem instance BareMetal.
3. Zadejte koncový bod exportéra uzlu ve formátu http://IP:9100/metrics .

   >[!IMPORTANT]
   >Použijte prosím privátní IP adresu hostitele se systémem Linux. Ujistěte se prosím, že hostitel a prostředek AMS jsou ve stejné virtuální síti. 

   >[!Note]
   > Na hostiteli se systémem Linux by měl být otevřen port brány firewall "9100".
   >Pokud používáte bránu firewall-cmd: firewall-cmd--trvalá--Add-port = 9100/TCP firewall-cmd--reload, pokud používáte UFW: UFW Allow 9100/TCP UFW reloading

    >[!Tip]
    > Pokud je hostitel systému Linux virtuálním počítačem Azure, zajistěte, aby všechny platné skupin zabezpečení sítě povolovaly příchozí provoz na portu 9100 od "VirtualNetwork" jako zdroj.
 
5. Po dokončení vyberte **Přidat poskytovatele**. V případě potřeby pokračujte v přidávání dalších zprostředkovatelů nebo kliknutím na tlačítko **zkontrolovat + vytvořit**   dokončete nasazení. 


## <a name="next-steps"></a>Další kroky

Další informace o [Azure monitor pro řešení SAP](azure-monitor-overview.md)
