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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
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

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="select-a-sql-server-vm-image"></a><a id="select"></a> Výběr image virtuálního počítače s SQL Serverem

1. Přihlaste se pomocí svého účtu k webu [Azure Portal](https://portal.azure.com).

1. V nabídce na levé straně Azure Portal vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a do vyhledávacího pole zadejte *Azure SQL* .
1. Výběrem **+ Přidat** otevřete stránku **vybrat možnost nasazení SQL** . Další informace si můžete zobrazit tak, že na dlaždici **virtuální počítače SQL** vyberete **Zobrazit podrobnosti** .
1. Vyberte **bezplatnou licenci SQL Server: SQL Server 2017 vývojář na obrázku Windows serveru 2016** z rozevíracího seznamu.

   ![Nové okno hledání](./media/quickstart-sql-vm-create-portal/select-sql-2017-vm-image.png)

1. Vyberte **Vytvořit**.

   ![Nové okno hledání](./media/quickstart-sql-vm-create-portal/create-sql-2017-vm-image.png)

## <a name="provide-basic-details"></a><a id="configure"></a> Poskytnutí základních informací

Na kartě **základy** zadejte následující informace:

1. V části **Project Details (podrobnosti projektu** ) vyberte předplatné Azure a pak vyberte **vytvořit novou** a vytvořte novou skupinu prostředků. Jako název zadejte _SQLVM-RG_ .

   ![Předplatné](media/quickstart-sql-vm-create-portal/basics-project-details.png)

1. V části **Podrobnosti instance**:
    1. Jako **název virtuálního počítače**zadejte _SQLVM_ . 
    1. Vyberte umístění pro vaši **oblast**. 
    1. Pro účely tohoto rychlého startu nechte **Možnosti dostupnosti** nastavené na _nepotřebnou redundanci infrastruktury_. Další informace o možnostech dostupnosti najdete v tématu [dostupnost](../../windows/availability.md). 
    1. V seznamu **Obrázek** vyberte _bezplatný SQL Server licence: SQL Server 2017 Developer v systému Windows Server 2016_. 
    1. Zvolte, chcete-li **změnit velikost** pro **Velikost** virtuálního počítače, a vyberte položku **základní nabídka a2** . Abyste zabránili neočekávaným poplatkům, nezapomeňte prostředky vyčistit. 

   ![Podrobnosti instance](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

1. V části **účet správce**zadejte uživatelské jméno, například _azureuser_ a heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Účet správce](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

1. V části **pravidla portů pro příchozí spojení**zvolte **Povolit vybrané porty** a v rozevíracím seznamu vyberte **RDP (3389)** . 

   ![Pravidla portů pro příchozí provoz](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>Nastavení SQL Serveru

Na kartě **nastavení SQL Server** nakonfigurujte následující možnosti:

1. V části **zabezpečení & sítě**vyberte možnost _veřejné (Internet_) pro **připojení SQL** a změňte port tak `1401` , abyste se vyhnuli použití dobře známého čísla portu ve veřejném scénáři. 
1. V části **ověřování SQL**vyberte **Povolit**. Jako přihlašovací údaje pro SQL je nastavené stejné uživatelské jméno a heslo, které jste nakonfigurovali pro virtuální počítač. Pro [**integraci Azure Key Vault**](virtual-machines-windows-ps-sql-keyvault.md)použijte výchozí nastavení. **Konfigurace úložiště** není k dispozici pro základní SQL Server image virtuálního počítače, ale můžete najít další informace o dostupných možnostech pro další image v [konfiguraci úložiště](virtual-machines-windows-sql-server-storage-configuration.md#new-vms).  

   ![Nastavení zabezpečení systému SQL Server](media/quickstart-sql-vm-create-portal/sql-server-settings.png)


1. V případě potřeby změňte všechna další nastavení a potom vyberte **zkontrolovat + vytvořit**. 

   ![Zkontrolovat a vytvořit](media/quickstart-sql-vm-create-portal/review-create.png)


## <a name="create-the-sql-server-vm"></a>Vytvoření virtuálního počítače s SQL Serverem

Na kartě **Revize + vytvořit** zkontrolujte souhrn a vyberte **vytvořit** a vytvořte SQL Server, skupinu prostředků a prostředky zadané pro tento virtuální počítač.

Nasazení můžete monitorovat z webu Azure Portal. Tlačítko **Oznámení** v horní části obrazovky zobrazuje základní stav nasazení. Nasazení může trvat několik minut. 

## <a name="connect-to-sql-server"></a>Připojení k SQL Serveru

1. Na portálu vyhledejte **veřejnou IP adresu** vašeho virtuálního počítače SQL Server v části **Přehled** vlastností virtuálního počítače.

1. Na jiném počítači připojeném k Internetu otevřete [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).


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

V tomto rychlém startu jste v Azure Portal vytvořili virtuální počítač s SQL Server 2017. Další informace o tom, jak na tento nový SQL Server přenést data, najdete v následujícím článku.

> [!div class="nextstepaction"]
> [Migrace databáze na virtuální počítač s SQL](virtual-machines-windows-migrate-sql.md)
