---
title: Vytvoření SQL Server na virtuálním počítači s Windows v Azure Portal | Microsoft Docs
description: V tomto kurzu se dozvíte, jak vytvořit virtuální počítač s Windows s SQL Server 2017 v Azure Portal.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 07/11/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 335a63faa440e057c282f992b67b301289a7a4bb
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356952"
---
# <a name="quickstart-create-sql-server-2017-on-a-windows-virtual-machine-in-the-azure-portal"></a>Rychlý Start: vytvoření SQL Server 2017 na virtuálním počítači s Windows v Azure Portal

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


> [!div class="op_single_selector"]
> * [Windows](sql-vm-create-portal-quickstart.md)
> * [Linux](../linux/sql-vm-create-portal-quickstart.md)

Tento rychlý Start prochází kroky vytvoření virtuálního počítače s SQL Server v Azure Portal.


  > [!TIP]
  > - Tento rychlý start obsahuje postup pro rychlé zřízení virtuálního počítače SQL a připojení k němu. Další informace o dalších možnostech zřizování virtuálních počítačů s SQL najdete v tématu [průvodce zřizováním SQL Server na virtuálním počítači s Windows v Azure Portal](create-sql-vm-portal.md).
  > - Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](frequently-asked-questions-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a>Získání předplatného Azure

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="select-a-sql-server-vm-image"></a><a id="select"></a> Výběr image virtuálního počítače s SQL Serverem

1. Přihlaste se pomocí svého účtu k webu [Azure Portal](https://portal.azure.com).

1. V nabídce na levé straně Azure Portal vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby** a do vyhledávacího pole zadejte *Azure SQL* .
1. Výběrem **+ Přidat** otevřete stránku **vybrat možnost nasazení SQL** . Další informace si můžete zobrazit tak, že na dlaždici **virtuální počítače SQL** vyberete **Zobrazit podrobnosti** .
1. Vyberte **bezplatnou licenci SQL Server: SQL Server 2017 vývojář na obrázku Windows serveru 2016** z rozevíracího seznamu.

   ![Snímek obrazovky, který ukazuje, kde si vyberete bezplatnou licenci SQL Server: SQL Server 2017 vývojář na obrázku Windows serveru 2016.](./media/sql-vm-create-portal-quickstart/select-sql-2017-vm-image.png)

1. Vyberte **Vytvořit**.

   ![Nové okno hledání](./media/sql-vm-create-portal-quickstart/create-sql-2017-vm-image.png)

## <a name="provide-basic-details"></a><a id="configure"></a> Poskytnutí základních informací

Na kartě **základy** zadejte následující informace:

1. V části **Project Details (podrobnosti projektu** ) vyberte předplatné Azure a pak vyberte **vytvořit novou** a vytvořte novou skupinu prostředků. Jako název zadejte _SQLVM-RG_ .

   ![Předplatné](./media/sql-vm-create-portal-quickstart/basics-project-details.png)

1. V části **Podrobnosti instance**:
    1. Jako **název virtuálního počítače** zadejte _SQLVM_ . 
    1. Vyberte umístění pro vaši **oblast**. 
    1. Pro účely tohoto rychlého startu nechte **Možnosti dostupnosti** nastavené na _nepotřebnou redundanci infrastruktury_. Další informace o možnostech dostupnosti najdete v tématu [dostupnost](../../../virtual-machines/availability.md). 
    1. V seznamu **Obrázek** vyberte _bezplatný SQL Server licence: SQL Server 2017 Developer v systému Windows Server 2016_. 
    1. Zvolte, chcete-li **změnit velikost** pro **Velikost** virtuálního počítače, a vyberte položku **základní nabídka a2** . Abyste zabránili neočekávaným poplatkům, nezapomeňte prostředky vyčistit. 

   ![Podrobnosti o instancích](./media/sql-vm-create-portal-quickstart/basics-instance-details.png)

1. V části **účet správce** zadejte uživatelské jméno, například _azureuser_ a heslo. Heslo musí mít délku aspoň 12 znaků a musí splňovat [definované požadavky na složitost](../../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Účet správce](./media/sql-vm-create-portal-quickstart/basics-administrator-account.png)

1. V části **pravidla portů pro příchozí spojení** zvolte **Povolit vybrané porty** a v rozevíracím seznamu vyberte **RDP (3389)** . 

   ![Pravidla portů pro příchozí provoz](./media/sql-vm-create-portal-quickstart/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>Nastavení SQL Serveru

Na kartě **nastavení SQL Server** nakonfigurujte následující možnosti:

1. V části **zabezpečení & sítě** vyberte možnost _veřejné (Internet_) pro **připojení SQL** a změňte port tak, abyste `1401` se vyhnuli použití dobře známého čísla portu ve veřejném scénáři. 
1. V části **ověřování SQL** vyberte **Povolit**. Přihlašovací údaje SQL se nastaví na stejné uživatelské jméno a heslo, které jste nakonfigurovali pro virtuální počítač. Pro [**integraci Azure Key Vault**](azure-key-vault-integration-configure.md)použijte výchozí nastavení. **Konfigurace úložiště** není pro základní SQL Server image virtuálního počítače dostupná, ale další informace o dostupných možnostech pro další image najdete v části [Konfigurace úložiště](storage-configuration.md#new-vms).  

   ![Nastavení zabezpečení systému SQL Server](./media/sql-vm-create-portal-quickstart/sql-server-settings.png)


1. V případě potřeby změňte všechna další nastavení a potom vyberte **zkontrolovat + vytvořit**. 

   ![Kontrola a vytvoření](./media/sql-vm-create-portal-quickstart/review-create.png)


## <a name="create-the-sql-server-vm"></a>Vytvoření virtuálního počítače s SQL Serverem

Na kartě **Revize + vytvořit** zkontrolujte souhrn a vyberte  **vytvořit** a vytvořte SQL Server, skupinu prostředků a prostředky zadané pro tento virtuální počítač.

Nasazení můžete monitorovat z webu Azure Portal. Tlačítko **Oznámení** v horní části obrazovky zobrazuje základní stav nasazení. Nasazení může trvat několik minut. 

## <a name="connect-to-sql-server"></a>Připojení k SQL Serveru

1. Na portálu vyhledejte **veřejnou IP adresu** vašeho virtuálního počítače SQL Server v části **Přehled** vlastností virtuálního počítače.

1. Na jiném počítači připojeném k Internetu otevřete [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).


1. V dialogovém okně **Připojit k serveru** nebo **Connect to Database Engine** (Připojit k databázovému stroji) upravte hodnotu **Název serveru**. Zadejte veřejnou IP adresu vašeho virtuálního počítače. Pak přidejte čárku a přidejte vlastní port (**1401**), který jste zadali při konfiguraci nového virtuálního počítače. Například `11.22.33.444,1401`.

1. V poli **Ověřování** vyberte **Ověřování serveru SQL Server**.

1. Do pole **Přihlášení** zadejte název platného přihlášení SQL.

1. Do pole **Heslo** zadejte heslo pro přihlášení.

1. Vyberte **Connect** (Připojit).

    ![Připojení přes SSMS](./media/sql-vm-create-portal-quickstart/ssms-connect.png)

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
> [Migrace databáze na virtuální počítač s SQL](migrate-to-vm-from-sql-server.md)