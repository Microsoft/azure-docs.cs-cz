---
title: Vytvoření virtuálního počítače s Windows a SQL Serverem na portálu | Microsoft Docs
description: Díky tomuto kurzu se dozvíte, jak vytvořit virtuální počítač s Windows a SQL Serverem 2017 na webu Azure Portal.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 07/11/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 801a6fc0602882d1af49c06bafcfd51942e6da2e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75965652"
---
# <a name="quickstart-create-a-sql-server-2017-windows-virtual-machine-in-the-azure-portal"></a>Rychlý start: Vytvoření virtuálního počítače s Windows a SQL Serverem 2017 na webu Azure Portal

> [!div class="op_single_selector"]
> * [Windows](quickstart-sql-vm-create-portal.md)
> * [Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)

Tento rychlý start vás provede vytvořením virtuálního počítače s SQL Serverem na webu Azure Portal.


  > [!TIP]
  > - Tento rychlý start obsahuje postup pro rychlé zřízení virtuálního počítače SQL a připojení k němu. Další informace o jiných možnostech zřizování virtuálních počítačů SQL najdete v tématu [Průvodce zřizováním virtuálních počítačů s Windows a SQL Serverem na webu Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).
  > - Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a>Získání předplatného Azure

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="select-a-sql-server-vm-image"></a><a id="select"></a> Výběr image virtuálního počítače s SQL Serverem

1. Přihlaste se pomocí svého účtu k webu [Azure Portal](https://portal.azure.com).

1. V levé nabídce portálu Azure vyberte **Azure SQL.** Pokud **Azure SQL** není v seznamu, vyberte Všechny **služby**, zadejte *Azure SQL* do vyhledávacího pole.
1. Výběrem **možnosti +Přidat** otevřete stránku **možnosti Vybrat nasazení SQL.** Další informace můžete zobrazit výběrem zobrazit **podrobnosti** na dlaždici **virtuálních počítačů SQL.**
1. V rozevíracím programu vyberte **bezplatnou licenci SQL Serveru: SQL Server 2017 Developer na** bitové kopii Windows Serveru 2016.

   ![Nové okno hledání](./media/quickstart-sql-vm-create-portal/select-sql-2017-vm-image.png)

1. Vyberte **Vytvořit**.

   ![Nové okno hledání](./media/quickstart-sql-vm-create-portal/create-sql-2017-vm-image.png)

## <a name="provide-basic-details"></a><a id="configure"></a> Poskytnutí základních informací

Na kartě **Základy** zadejte následující informace:

1. V části **Podrobnosti projektu** vyberte předplatné Azure a pak vyberte **Vytvořit nový** a vytvořte novou skupinu prostředků. Zadejte _SQLVM-RG_ pro název.

   ![Předplatné](media/quickstart-sql-vm-create-portal/basics-project-details.png)

1. V **části Podrobnosti instance**:
    1. Zadejte _SQLVM_ pro **název virtuálního počítače**. 
    1. Zvolte umístění pro vaši **oblast**. 
    1. Pro účely tohoto rychlého startu ponechejte **možnosti dostupnosti** nastavené na _není nutná redundance infrastruktury_. Další informace o možnostech dostupnosti naleznete v [tématu Dostupnost](../../windows/availability.md). 
    1. V seznamu **Obrázek** vyberte _bezplatnou licenci sql serveru: SQL Server 2017 Developer v systému Windows Server 2016_. 
    1. Zvolte **změnit velikost** pro **velikost** virtuálního počítače a vyberte nabídku **A2 Basic.** Nezapomeňte vyčistit své prostředky, jakmile budete hotovi s nimi, aby se zabránilo neočekávaným poplatkům. 

   ![Podrobnosti instance](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

1. V **části Správce účtu**, zadejte uživatelské jméno, jako je _například azureuser_ a heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Účet správce](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

1. V části **Pravidla příchozího portu**zvolte **Povolit vybrané porty** a v rozevíracím řádu vyberte **rdp (3389).** 

   ![Pravidla portů pro příchozí provoz](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>Nastavení SQL Serveru

Na kartě **Nastavení serveru SQL Server** nakonfigurujte následující možnosti:

1. V části **Zabezpečení & sítě**vyberte možnost Veřejné _(Internet)_ pro **připojení SQL** a změňte port tak, aby `1401` se ve veřejném scénáři nepoužíval známé číslo portu. 
1. V části **Ověřování SQL**vyberte **Povolit**. Jako přihlašovací údaje pro SQL je nastavené stejné uživatelské jméno a heslo, které jste nakonfigurovali pro virtuální počítač. Výchozí nastavení použijte pro [**integraci služby Azure Key Vault**](virtual-machines-windows-ps-sql-keyvault.md). **Konfigurace úložiště** není k dispozici pro základní bitovou kopii virtuálního počítače SQL Server, ale další informace o dostupných možnostech pro jiné bitové kopie najdete v [konfiguraci úložiště](virtual-machines-windows-sql-server-storage-configuration.md#new-vms).  

   ![Nastavení zabezpečení serveru SQL server](media/quickstart-sql-vm-create-portal/sql-server-settings.png)


1. V případě potřeby změňte všechna další nastavení a pak vyberte **Zkontrolovat + vytvořit**. 

   ![Recenze + vytvořit](media/quickstart-sql-vm-create-portal/review-create.png)


## <a name="create-the-sql-server-vm"></a>Vytvoření virtuálního počítače s SQL Serverem

Na kartě **Revize + vytvořit** zkontrolujte souhrn a vyberte **Vytvořit** pro vytvoření SQL Serveru, skupiny prostředků a prostředků určených pro tento virtuální počítač.

Nasazení můžete monitorovat z webu Azure Portal. Tlačítko **Oznámení** v horní části obrazovky zobrazuje základní stav nasazení. Nasazení může trvat několik minut. 

## <a name="connect-to-sql-server"></a>Připojení k SQL Serveru

1. Na portálu najděte **veřejnou IP adresu** virtuálního počítače SQL Server v části **Přehled** vlastností virtuálního počítače.

1. V jiném počítači připojeném k Internetu otevřete [sql server management studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).


1. V dialogovém okně **Připojit k serveru** nebo **Connect to Database Engine** (Připojit k databázovému stroji) upravte hodnotu **Název serveru**. Zadejte veřejnou IP adresu vašeho virtuálního počítače. Potom zadejte čárku a vlastní port, **1401**, který jsme zadali, když jste konfigurovali nový virtuální počítač. Například, `11.22.33.444,1401`.

1. V poli **Ověřování** vyberte **Ověřování serveru SQL Server**.

1. Do pole **Přihlášení** zadejte název platného přihlášení SQL.

1. Do pole **Heslo** zadejte heslo pro přihlášení.

1. Vyberte **Connect** (Připojit).

    ![Připojení přes SSMS](./media/quickstart-sql-vm-create-portal/ssms-connect.png)

## <a name="log-in-to-the-vm-remotely"></a><a id="remotedesktop"></a> Vzdálené přihlášení k virtuálnímu počítači

Podle následujícího postupu se připojte k virtuálnímu počítači s SQL Serverem pomocí Vzdálené plochy:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Po připojení k virtuálnímu počítači s SQL Serverem můžete spustit SQL Server Management Studio a připojit se pomocí ověřování systému Windows se svými přihlašovacími údaji místního správce. Pokud jste povolili ověřování SQL Serveru, můžete se také připojit pomocí ověřování SQL Serveru a použít k tomu přihlašovací jméno a heslo SQL Serveru, které jste nakonfigurovali během zřizování.

Přístup k počítači vám umožňuje podle potřeb přímo měnit nastavení počítače a SQL Serveru. Můžete například nakonfigurovat nastavení brány firewall nebo změnit nastavení konfigurace SQL Serveru.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nepotřebujete, aby virtuální počítač SQL VM běžel nepřetržitě, můžete se vyhnout zbytečným poplatkům: když počítač nepoužíváte, zastavte ho. Můžete také trvale odstranit všechny prostředky přidružené k virtuálnímu počítači odstraněním příslušné skupiny prostředků na portálu. Tím trvale odstraníte i virtuální počítač, proto tento příkaz používejte opatrně. Další informace najdete v tématu [Správa prostředků Azure prostřednictvím portálu](../../../azure-resource-manager/management/manage-resource-groups-portal.md).


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste na webu Azure Portal vytvořili virtuální počítač SQL Serveru 2017. Další informace o tom, jak na tento nový SQL Server přenést data, najdete v následujícím článku.

> [!div class="nextstepaction"]
> [Migrace databáze na virtuální počítač s SQL](virtual-machines-windows-migrate-sql.md)
