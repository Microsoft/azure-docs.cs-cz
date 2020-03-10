---
title: Konfigurace spravované instance P2S
description: Připojte se k Azure SQL Database spravované instanci pomocí SQL Server Management Studio pomocí připojení typu Point-to-Site z místního klientského počítače.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 30b2ba92174996ea2bae34e7553a3258d8ebee27
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388187"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Rychlý Start: Konfigurace připojení typu Point-to-site k Azure SQL Database spravované instanci z místního prostředí

Tento rychlý Start ukazuje, jak se připojit k Azure SQL Database spravované instanci pomocí [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) z místního klientského počítače přes připojení typu Point-to-site. Informace o připojení typu Point-to-site najdete v tématu [informace o síti VPN typu Point-to-](../vpn-gateway/point-to-site-about.md) site.

## <a name="prerequisites"></a>Požadavky

Tento rychlý start:

- Používá vytvořené prostředky [Vytvoření spravované instance](sql-database-managed-instance-get-started.md) jako počátečního bodu.
- Vyžaduje PowerShell 5,1 a AZ PowerShell 1.4.0 nebo novější na místním klientském počítači. V případě potřeby si přečtěte pokyny k [instalaci modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Vyžaduje nejnovější verzi [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) na místním klientském počítači.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Připojení brány VPN k virtuální síti spravované instance

1. Otevřete PowerShell na místním klientském počítači.

2. Zkopírujte tento skript PowerShellu. Tento skript připojí VPN Gateway k virtuální síti spravované instance, kterou jste vytvořili v rychlém startu [Vytvoření spravované instance](sql-database-managed-instance-get-started.md) . Tento skript používá Azure PowerShell AZ Module a provede následující pro hostitele založené na systému Windows nebo Linux:

   - Vytvoří a nainstaluje certifikáty na klientském počítači.
   - Vypočítá budoucí VPN Gateway rozsah IP adres podsítě.
   - Vytvoří GatewaySubnet.
   - Nasadí šablonu Azure Resource Manager, která připojuje VPN Gateway k podsíti VPN.

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGateway.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

3. Vložte skript do okna PowerShellu a zadejte požadované parametry. Hodnoty pro `<subscriptionId>`, `<resourceGroup>`a `<virtualNetworkName>` by měly odpovídat těm, které jste použili v rychlém startu [Vytvoření spravované instance](sql-database-managed-instance-get-started.md) . Hodnota pro `<certificateNamePrefix>` může být zvoleným řetězcem.

4. Spusťte skript prostředí PowerShell.

> [!IMPORTANT]
> Nepokračujte, dokud se PowerShellový skript nedokončí.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Vytvoření připojení VPN ke spravované instanci

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Otevřete skupinu prostředků, ve které jste vytvořili bránu virtuální sítě, a pak otevřete prostředek brány virtuální sítě.
3. Vyberte **Konfigurace Point-to-site** a pak vyberte **stáhnout klienta VPN**.

    ![Stáhnout klienta VPN](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. V místním klientském počítači rozbalte soubory ze souboru zip a pak otevřete složku s extrahované soubory.
5. Otevřete složku**WindowsAmd64** a otevřete soubor **VpnClientSetupAmd64. exe** .
6. Pokud se zobrazí zpráva o **počítači chráněná systémem Windows** , klikněte na tlačítko **Další informace** a pak klikněte na tlačítko **Spustit**.

    ![Nainstalovat klienta VPN](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. V dialogovém okně Řízení uživatelských účtů pokračujte kliknutím na tlačítko **Ano** .
8. V dialogovém okně odkazujícím na virtuální síť vyberte **Ano** pro instalaci klienta VPN pro virtuální síť.

## <a name="connect-to-the-vpn-connection"></a>Připojení k síti VPN

1. Pokud chcete navázat připojení k této virtuální síti, klikněte na síť **VPN** v **síti & Internetu** na místním klientském počítači a vyberte svou virtuální síť spravované instance. Na následujícím obrázku je virtuální síť s názvem **MyNewVNet**.

    ![Připojení VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Vyberte **Connect** (Připojit).
3. V dialogovém okně vyberte **připojit**.

    ![Připojení VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. Po zobrazení výzvy, že správce připojení potřebuje zvýšené oprávnění pro aktualizaci směrovací tabulky, vyberte **pokračovat**.
5. Chcete-li pokračovat, vyberte v dialogovém okně Řízení uživatelských účtů možnost **Ano** .

   Navázali jste připojení VPN k virtuální síti spravované instance.

    ![Připojení VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Použití SSMS k připojení k Managed Instance

1. Na místním klientském počítači otevřete SQL Server Management Studio (SSMS).
2. V dialogovém okně **připojit k serveru** zadejte do pole **název serveru** plně kvalifikovaný **název hostitele** vaší spravované instance.
3. Vyberte **SQL Server ověřování**, zadejte svoje uživatelské jméno a heslo a pak vyberte **připojit**.

    ![Připojení přes SSMS](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Po připojení můžete zobrazit systémové a uživatelské databáze v uzlu databáze. Můžete také zobrazit různé objekty v uzlech zabezpečení, objekty serveru, replikace, Správa, Agent SQL Server a XEvent Profiler.

## <a name="next-steps"></a>Další kroky

- Rychlý Start ukazující, jak se připojit z virtuálního počítače Azure, najdete v tématu [Konfigurace připojení typu Point-to-site](sql-database-managed-instance-configure-p2s.md).
- Přehled možností připojení aplikací najdete v článku o [připojení aplikací ke spravované instanci](sql-database-managed-instance-connect-app.md).
- Pokud chcete obnovit stávající databázi SQL Server z místního prostředí do spravované instance, můžete k obnovení ze záložního souboru databáze použít [Azure Database Migration Service (DMS) pro migraci](../dms/tutorial-sql-server-to-managed-instance.md) nebo [příkaz T-SQL Restore](sql-database-managed-instance-get-started-restore.md) .
