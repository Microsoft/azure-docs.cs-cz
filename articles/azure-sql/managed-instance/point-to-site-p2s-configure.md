---
title: Konfigurace připojení Point-to-site pomocí SSMS
titleSuffix: Azure SQL Managed Instance
description: Připojte se ke spravované instanci Azure SQL pomocí SQL Server Management Studio (SSMS) pomocí připojení typu Point-to-Site z místního klientského počítače.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 3baf2d7ed7c326895ae40948fc2d0a4cc03021f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92788362"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-azure-sql-managed-instance-from-on-premises"></a>Rychlý Start: Konfigurace připojení typu Point-to-site k spravované instanci Azure SQL z místního prostředí
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Tento rychlý Start ukazuje, jak se připojit ke spravované instanci Azure SQL pomocí [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS) z místního klientského počítače přes připojení typu Point-to-site. Informace o připojení typu Point-to-site najdete v tématu věnovaném [síti VPN typu Point-to-site](../../vpn-gateway/point-to-site-about.md).

## <a name="prerequisites"></a>Předpoklady

Tento rychlý start:

- Používá prostředky vytvořené v části  [Vytvoření spravované instance](instance-create-quickstart.md) jako počáteční bod.
- Vyžaduje PowerShell 5,1 a Azure PowerShell 1.4.0 nebo novější na místním klientském počítači. V případě potřeby si přečtěte pokyny k [instalaci modulu Azure PowerShell](/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Vyžaduje nejnovější verzi [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) na místním klientském počítači.

## <a name="attach-a-vpn-gateway-to-a-virtual-network"></a>Připojení brány VPN k virtuální síti

1. Otevřete PowerShell na místním klientském počítači.

2. Zkopírujte tento skript PowerShellu. Tento skript připojí bránu VPN k virtuální síti spravované instance SQL, kterou jste vytvořili v rychlém startu [Vytvoření spravované instance](instance-create-quickstart.md) . Tento skript používá Azure PowerShell AZ Module a pro hostitele se systémem Windows nebo Linux provádí následující akce:

   - Vytvoří a nainstaluje certifikáty na klientském počítači.
   - Vypočítá rozsah IP adres podsítě budoucí brány VPN.
   - Vytvoří podsíť brány.
   - Nasadí šablonu Azure Resource Manager, která připojí bránu VPN k podsíti VPN.

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

3. Vložte skript do okna PowerShellu a zadejte požadované parametry. Hodnoty pro `<subscriptionId>` , `<resourceGroup>` a `<virtualNetworkName>` by měly odpovídat těm, které jste použili pro rychlý Start pro [Vytvoření spravované instance](instance-create-quickstart.md) . Hodnota pro `<certificateNamePrefix>` může být zvoleným řetězcem.

4. Spusťte skript prostředí PowerShell.

> [!IMPORTANT]
> Nepokračujte, dokud se PowerShellový skript nedokončí.

## <a name="create-a-vpn-connection"></a>Vytvoření připojení VPN

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Otevřete skupinu prostředků, ve které jste vytvořili bránu virtuální sítě, a pak otevřete prostředek brány virtuální sítě.
3. Vyberte **Konfigurace Point-to-site** a pak vyberte **stáhnout klienta VPN**.

    ![Stáhnout klienta VPN](./media/point-to-site-p2s-configure/download-vpn-client.png)  
4. V místním klientském počítači rozbalte soubory ze souboru zip a pak otevřete složku s extrahované soubory.
5. Otevřete složku **WindowsAmd64** a otevřete soubor **VpnClientSetupAmd64.exe** .
6. Pokud se zobrazí zpráva o **počítači chráněná systémem Windows** , klikněte na tlačítko **Další informace** a pak klikněte na tlačítko **Spustit**.

    ![Nainstalovat klienta VPN](./media/point-to-site-p2s-configure/vpn-client-defender.png)
7. V dialogovém okně Ovládání uživatelského účtu pokračujte kliknutím na **Ano**.
8. V dialogovém okně odkazujícím na virtuální síť vyberte **Ano** pro instalaci klienta VPN pro virtuální síť.

## <a name="connect-to-the-vpn-connection"></a>Připojení k síti VPN

1. Pokud chcete navázat připojení k této virtuální síti, klikněte na síť **VPN** v **síti & Internetu** na místním klientském počítači a vyberte svou virtuální síť spravované instance SQL. Na následujícím obrázku je virtuální síť s názvem **MyNewVNet**.

    ![Připojení VPN](./media/point-to-site-p2s-configure/vpn-connection.png)  
2. Vyberte **Connect** (Připojit).
3. V dialogovém okně vyberte **připojit**.

    ![Snímek obrazovky, který zvýrazní tlačítko připojit](./media/point-to-site-p2s-configure/vpn-connection2.png)  
4. Když se zobrazí výzva, že správce připojení potřebuje zvýšená oprávnění pro aktualizaci směrovací tabulky, vyberte **pokračovat**.
5. Chcete-li pokračovat, vyberte v dialogovém okně Řízení uživatelských účtů možnost **Ano** .

   Navázali jste připojení VPN k virtuální síti spravované instance SQL.

    ![Snímek obrazovky, který zvýrazní připojenou zprávu po navázání připojení.](./media/point-to-site-p2s-configure/vpn-connection-succeeded.png)  

## <a name="connect-with-ssms"></a>Připojení přes SSMS

1. V místním klientském počítači otevřete SQL Server Management Studio.
2. V dialogovém okně **připojit k serveru** zadejte do pole **název serveru** plně kvalifikovaný **název hostitele** vaší spravované instance.
3. Vyberte **SQL Server ověřování**, zadejte svoje uživatelské jméno a heslo a pak vyberte **připojit**.

    ![SSMS připojit](./media/point-to-site-p2s-configure/ssms-connect.png)  

Po připojení můžete zobrazit systémové a uživatelské databáze v uzlu databáze. Můžete také zobrazit různé objekty v uzlech zabezpečení, objekty serveru, replikace, Správa, Agent SQL Server a XEvent Profiler.

## <a name="next-steps"></a>Další kroky

- Rychlý Start ukazující, jak se připojit z virtuálního počítače Azure, najdete v tématu [Konfigurace připojení typu Point-to-site](point-to-site-p2s-configure.md).
- Přehled možností připojení pro aplikace najdete v tématu [připojení aplikací k spravované instanci SQL](connect-application-instance.md).
- Chcete-li obnovit stávající databázi SQL Server z místního prostředí do spravované instance, můžete použít [Azure Database Migration Service pro migraci](../../dms/tutorial-sql-server-to-managed-instance.md) nebo [příkaz T-SQL Restore](restore-sample-database-quickstart.md) pro obnovení ze záložního souboru databáze.