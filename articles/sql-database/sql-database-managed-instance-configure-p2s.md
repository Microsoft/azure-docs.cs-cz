---
title: Konfigurace instance P2S – spravovaná
description: Připojte se k instanci spravované databáze Azure SQL pomocí sql serveru Management Studio pomocí připojení point-to-site z místního klientského počítače.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268883"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Úvodní příručka: Konfigurace připojení point-to-site ke spravované instanci Azure SQL Database ze místní hospo-

Tento rychlý start ukazuje, jak se připojit k instanci spravované databáze Azure SQL database pomocí [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) z místního klientského počítače přes připojení point-to-site. Informace o připojeních point-to-site naleznete v tématu [O síti VPN z bodu na web.](../vpn-gateway/point-to-site-about.md)

## <a name="prerequisites"></a>Požadavky

Tento rychlý start:

- Jako výchozí bod použije vytvořené prostředky [Vytvořit spravovanou instanci.](sql-database-managed-instance-get-started.md)
- Vyžaduje prostředí PowerShell 5.1 a AZ PowerShell 1.4.0 nebo novější v místním klientském počítači. V případě potřeby najdete v pokynech k [instalaci modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Vyžaduje nejnovější verzi [sql server management studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) v místním klientském počítači.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Připojení brány VPN k virtuální síti spravované instance

1. Otevřete PowerShell v místním klientském počítači.

2. Zkopírujte tento skript prostředí PowerShell. Tento skript připojí bránu VPN k virtuální síti spravované instance, kterou jste vytvořili v rychlém startu [Vytvořit spravovanou instanci.](sql-database-managed-instance-get-started.md) Tento skript používá modul Azure PowerShell Az a provede následující akce pro hostitele založené na Windows nebo Linuxu:

   - Vytváří a instaluje certifikáty v klientském počítači.
   - Vypočítá budoucí rozsah IP adres brány VPN.
   - Vytvoří gatewaySubnet
   - Nasazuje šablonu Azure Resource Manager, která připojuje bránu VPN k podsíti VPN.

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

3. Vložte skript do okna PowerShellu a zadejte požadované parametry. Hodnoty pro `<subscriptionId>` `<resourceGroup>`, `<virtualNetworkName>` a by měly odpovídat hodnotám, které jste použili pro rychlý start [Vytvořit spravovanou instanci.](sql-database-managed-instance-get-started.md) Hodnota pro `<certificateNamePrefix>` může být řetězec dle vašeho výběru.

4. Spusťte skript prostředí PowerShell.

> [!IMPORTANT]
> Nepokračujte, dokud se skript prostředí PowerShell nedokončí.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Vytvoření připojení VPN ke spravované instanci

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Otevřete skupinu prostředků, ve které jste vytvořili bránu virtuální sítě, a pak otevřete prostředek brány virtuální sítě.
3. Vyberte **konfiguraci bodu k webu** a pak vyberte **Stáhnout klienta VPN**.

    ![Stáhnout klienta VPN](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. V místním klientském počítači extrahujte soubory ze souboru ZIP a otevřete složku s extrahovanými soubory.
5. Otevřete složku '**WindowsAmd64** a otevřete soubor **VpnClientSetupAmd64.exe.**
6. Pokud se vám zobrazí zpráva **o ochraně počítače se systémem Windows,** klepněte na tlačítko **Další informace** a přesto klepněte na tlačítko **Spustit**.

    ![Instalace klienta VPN](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. V dialogovém okně Ovládání uživatelského účtu pokračujte kliknutím na **Ano**.
8. V dialogovém okně odkazujícím na vaši virtuální síť vyberte **Ano,** chcete-li nainstalovat klienta VPN pro vaši virtuální síť.

## <a name="connect-to-the-vpn-connection"></a>Připojení k připojení VPN

1. Přejděte na **VPN** v **síti & Internetu** v místním klientském počítači a vyberte virtuální síť spravované instance, chcete-li navázat připojení k této virtuální síti. V následujícím obrázku virtuální síť s názvem **MyNewVNet**.

    ![Připojení VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Vyberte **Connect** (Připojit).
3. V dialogovém okně vyberte **Připojit**.

    ![Připojení VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. Po zobrazení výzvy, že program Connection Manager potřebuje ke zvýšení oprávnění k aktualizaci směrovací tabulky zvýšené oprávnění, zvolte **Pokračovat**.
5. Chcete-li pokračovat, pokračujte v možnosti **Ano** v dialogovém okně Řízení uživatelských účtů.

   Navázali jste připojení VPN k virtuální síti spravované instance.

    ![Připojení VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Připojení ke spravované instanci pomocí SSMS

1. V místním klientském počítači otevřete SQL Server Management Studio (SSMS).
2. V dialogovém okně **Připojit k serveru** zadejte do pole **Název serveru** úplný název **hostitele** spravované instance.
3. Vyberte **sql server authentication**, zadejte své uživatelské jméno a heslo a pak vyberte **Připojit**.

    ![Připojení přes SSMS](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Po připojení můžete zobrazit systémové a uživatelské databáze v uzlu Databáze. Můžete také zobrazit různé objekty v uzlech Zabezpečení, Serverové objekty, Replikace, Správa, Agent serveru SQL a XEvent Profiler.

## <a name="next-steps"></a>Další kroky

- Úvodní příručka o tom, jak se připojit z virtuálního počítače Azure, najdete v [tématu Konfigurace připojení bodu k webu](sql-database-managed-instance-configure-p2s.md).
- Přehled možností připojení pro aplikace najdete v článku o [připojení aplikací ke spravované instanci](sql-database-managed-instance-connect-app.md).
- Chcete-li obnovit existující databázi SERVERU SQL Server z místní na spravovanou instanci, můžete použít [službu Migrace databáze Azure (DMS) pro migraci](../dms/tutorial-sql-server-to-managed-instance.md) nebo [příkaz T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) k obnovení ze záložního souboru databáze.
