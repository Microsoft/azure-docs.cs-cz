---
title: Konfigurace P2S – Azure SQL Database Managed Instance | Dokumentace Microsoftu
description: Připojte se k Azure SQL Database Managed Instance pomocí SQL Server Management Studio pomocí připojení point-to-site z místní klientských počítačů.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova, jovanpop
manager: craigg
ms.date: 03/13/2019
ms.openlocfilehash: 9ca9506ad9ca826349b2815e504e9ba8bf976faa
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59359672"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Rychlý start: Konfigurace připojení typu point-to-site k Azure SQL Database Managed Instance z místní

Tento rychlý start ukazuje, jak se připojit k Azure SQL Database Managed Instance pomocí [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) z místních klientských počítačů prostřednictvím připojení point-to-site. Informace o připojení point-to-site najdete v tématu [o Point-to-Site VPN](../vpn-gateway/point-to-site-about.md)

## <a name="prerequisites"></a>Požadavky

Tento rychlý start:

- Využívá prostředky vytvořené [vytvoříte Managed Instance](sql-database-managed-instance-get-started.md) jako výchozí bod.
- Vyžaduje prostředí PowerShell 5.1 a AZ Powershellu 1.4.0 nebo později v místním klientském počítači. V případě potřeby, přečtěte si pokyny pro [instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Vyžaduje nejnovější verzi [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) na místním klientském počítači.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Připojit bránu VPN k virtuální síti spravované Instance

1. Otevřete prostředí PowerShell v klientském počítači místní.

2. Zkopírujte tento skript Powershellu. Tento skript připojí bránu sítě VPN do virtuální sítě Managed Instance, které jste vytvořili [vytvoříte Managed Instance](sql-database-managed-instance-get-started.md) rychlý start. Skript provede následující akce:

   - Vytvoří a instalace certifikátů v klientském počítači
   - Vypočítá budoucí rozsah IP podsítě brány sítě VPN
   - Vytvoří podsítě GatewaySubnet
   - Nasazení šablony Azure Resource Manageru, který se připojí k podsíti VPN brány sítě VPN

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGatewayAz.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

     > [!IMPORTANT]
     > Pokud chcete použít modul Azure PowerShell – Resource Manager místo modulu Az, použijte následující rutinu: `attachVPNGateway.ps1` místo `attachVPNGatewayAz.ps1` rutiny.

3. Vložte skript do okna prostředí PowerShell a zadejte požadované parametry. Hodnoty pro `<subscriptionId>`, `<resourceGroup>`, a `<virtualNetworkName>` by měl odpovídat těch, které jste použili pro [vytvoření Managed Instance](sql-database-managed-instance-get-started.md) rychlý start. Hodnota pro `<certificateNamePrefix>` může jednat o řetězec podle vašeho výběru.

4. Spusťte skript prostředí PowerShell.

> [!IMPORTANT]
> Nepokračujte, dokud se nedokončí skript prostředí PowerShell.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Vytvoření připojení VPN k Managed Instance

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Otevřete skupinu prostředků, ve které jste vytvořili bránu virtuální sítě a pak otevřete prostředku brány virtuální sítě.
3. Vyberte **KonfiguracePoint-to-site** a pak vyberte **stáhnout klienta VPN**.

    ![Stáhnout klienta VPN](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. Na místním klientském počítači extrahujte soubory ze souboru zip a potom tuto složku otevřete extrahované soubory.
5. Otevřít "**WindowsAmd64** složky a otevřete **VpnClientSetupAmd64.exe** souboru.
6. Pokud se zobrazí **Windows chráněný počítač** zprávy, klikněte na tlačítko **informace** a potom klikněte na **přesto spustit**.

    ![Instalace klienta VPN](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. V dialogovém okně Řízení uživatelských účtů klikněte na tlačítko **Ano** pokračujte.
8. V dialogovém okně odkazující na virtuální síť, vyberte **Ano** k instalaci klienta VPN pro vaši virtuální síť.

## <a name="connect-to-the-vpn-connection"></a>Připojte se k připojení k síti VPN

1. Přejděte na **VPN** v **síť a Internet** na místním klientském počítači a vyberte virtuální síť Managed Instance pro navázání připojení k této virtuální síti. Na následujícím obrázku je síť VNet s názvem **MyNewVNet**.

    ![Připojení VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Vyberte **Connect** (Připojit).
3. V dialogovém okně vyberte **připojit**.

    ![Připojení VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. Po zobrazení výzvy, že Správce připojení vyžaduje zvýšená oprávnění k aktualizaci směrovací tabulky, zvolte **pokračovat**.
5. Vyberte **Ano** v dialogovém okně Řízení uživatelských účtů, abyste mohli pokračovat.

   Když jste vytvořili připojení k síti VPN k virtuální síti spravované Instance.

    ![Připojení VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Použití SSMS k připojení k Managed Instance

1. V místním klientském počítači otevřete SQL Server Management Studio (SSMS).
2. V **připojit k serveru** dialogového okna zadejte plně kvalifikovaný **název hostitele** pro Managed Instance v **název serveru** pole.
3. Vyberte **ověřování systému SQL Server**, zadejte své uživatelské jméno a heslo a pak vyberte **připojit**.

    ![Připojení přes SSMS](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Po připojení, můžete zobrazit systémové a uživatelské databáze v uzlu databáze. Můžete také zobrazit různé objekty v uzlech zabezpečení, objekty serveru, replikace, správa, Agent systému SQL Server a Profiler XEvent.

## <a name="next-steps"></a>Další postup

- Rychlý start ukazuje, jak připojit z virtuálního počítače Azure, najdete v části [konfigurace připojení typu point-to-site](sql-database-managed-instance-configure-p2s.md).
- Přehled možností připojení aplikací najdete v článku o [připojení aplikací ke spravované instanci](sql-database-managed-instance-connect-app.md).
- Obnovit existující databáze SQL serveru v místním do Managed Instance, můžete použít [Azure Database Migration Service (DMS) pro migraci](../dms/tutorial-sql-server-to-managed-instance.md) nebo [příkaz T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) obnovit z záložní soubor databáze.
