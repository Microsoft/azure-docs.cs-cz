---
title: Quickstart - Manage Private Endpoints in Azure
description: Learn how to create a Private Endpoint using the Azure portal in this Quickstart
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 2cb3c1d798e48017604d72d8a438d6a1071199ea
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74419764"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>Quickstart: Create a Private Endpoint using Azure portal

A Private Endpoint is the fundamental building block for private link in Azure. It enables Azure resources, like Virtual Machines (VMs), to communicate privately with private link resources. In this Quickstart, you will learn how to create a VM on an Azure Virtual Network, a  SQL Database Server with an Azure private endpoint using the Azure Portal. Then, you can securely access the SQL Database Server from the VM.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


> [!NOTE]
> Private endpoint(s) are not permitted in conjunction with service endpoints in the same subnet!

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače
In this section, you will create virtual network and the subnet to host the VM that is used to access your Private Link resource (a SQL server in Azure in this example).

### <a name="create-the-virtual-network"></a>Create the virtual network


In this section, you will create a Virtual Network and the subnet to host the VM that is used to access your Private Link resource.

1. On the upper-left side of the screen, select **Create a resource** > **Networking** > **Virtual network**.
1. In **Create virtual network**, enter or select this information:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Enter *MyVirtualNetwork*. |
    | Adresní prostor | Enter *10.1.0.0/16*. |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Select **Create new**, enter *myResourceGroup*, then select **OK**. |
    | Umístění | Select **WestCentralUS**.|
    | Subnet - Name | Enter *mySubnet*. |
    | Podsíť – Rozsah adres | Enter *10.1.0.0/24*. |
    |||
1. Leave the rest as default and select **Create**.


### <a name="create-virtual-machine"></a>Vytvořit virtuální počítač

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Compute** > **Virtual Machine**.

1. In **Create a virtual machine - Basics**, enter or select this information:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PROJECT DETAILS** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Select **myResourceGroup**. You created this in the previous section.  |
    | **INSTANCE DETAILS** |  |
    | Název virtuálního počítače | Enter *myVm*. |
    | Oblast | Select **WestCentralUS**. |
    | Availability options | Leave the default **No infrastructure redundancy required**. |
    | Obrázek | Select **Windows Server 2019 Datacenter**. |
    | Velikost | Leave the default **Standard DS1 v2**. |
    | **ADMINISTRATOR ACCOUNT** |  |
    | Uživatelské jméno | Enter a username of your choosing. |
    | Heslo | Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirm Password | Reenter password. |
    | **INBOUND PORT RULES** |  |
    | Public inbound ports | Leave the default **None**. |
    | **SAVE MONEY** |  |
    | Already have a Windows license? | Leave the default **No**. |
    |||

1. Select **Next: Disks**.

1. In **Create a virtual machine - Disks**, leave the defaults and select **Next: Networking**.

1. In **Create a virtual machine - Networking**, select this information:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Virtuální síť | Leave the default **MyVirtualNetwork**.  |
    | Adresní prostor | Leave the default **10.1.0.0/24**.|
    | Podsíť | Leave the default **mySubnet (10.1.0.0/24)** .|
    | Veřejná IP adresa | Leave the default **(new) myVm-ip**. |
    | Public inbound ports | Select **Allow selected ports**. |
    | Select inbound ports | Select **HTTP** and **RDP**.|
    |||


1. Vyberte **Zkontrolovat a vytvořit**. You're taken to the **Review + create** page where Azure validates your configuration.

1. When you see the **Validation passed** message, select **Create**.

## <a name="create-a-sql-database-server"></a>Create a SQL database server
In this section, you will create a SQL database server in Azure. 

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Databases** > **SQL database**.

1. In **Create SQL database - Basics**, enter or select this information:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Database details** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Select **myResourceGroup**. You created this in the previous section.|
    | **INSTANCE DETAILS** |  |
    | Název databáze  | Enter *mydatabase*. If this name is taken, create a unique name. |
    |||
5. In **Server**, select **Create new**. 
6. In **New server**, enter or select this information:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Název serveru  | Enter *myserver*. If this name is taken, create a unique name.|
    | Přihlašovací jméno správce serveru| Enter an administrator name of your choosing. |
    | Heslo | Zadejte libovolné heslo. The password must be at least 8 characters long and meet the defined requirements. |
    | Umístění | Select an Azure region where you want to want your SQL Server to reside. |
    
7. Vyberte **OK**. 
8. Vyberte **Zkontrolovat a vytvořit**. You're taken to the **Review + create** page where Azure validates your configuration. 
9. When you see the Validation passed message, select **Create**. 
10. When you see the Validation passed message, select Create. 

## <a name="create-a-private-endpoint"></a>Create a private endpoint

In this section, you will create a SQL server and add a private endpoint to it. 

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Networking** > **Private Link Center (Preview)** .
2. In **Private Link Center - Overview**, on the option to **Build a private connection to a service**, select **Start**.
1. In **Create a private endpoint (Preview) - Basics**, enter or select this information:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Project details** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Select **myResourceGroup**. You created this in the previous section.|
    | **INSTANCE DETAILS** |  |
    | Name (Název) | Enter * myPrivateEndpoint*. If this name is taken, create a unique name. |
    |Oblast|Select **WestCentralUS**.|
    |||
5. Select **Next: Resource**.
6. In **Create a private endpoint - Resource**, enter or select this information:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Způsob připojení  | Select connect to an Azure resource in my directory.|
    | Předplatné| Vyberte své předplatné. |
    | Typ prostředku | Select **Microsoft.Sql/servers**. |
    | Prostředek |Select *myServer*|
    |Target sub-resource |Select *sqlServer*|
    |||
7. Select **Next: Configuration**.
8. In **Create a private endpoint (Preview) - Configuration**, enter or select this information:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |**NETWORKING**| |
    | Virtuální síť| Select *MyVirtualNetwork*. |
    | Podsíť | Select *mySubnet*. |
    |**PRIVATE DNS INTEGRATION**||
    |Integrate with private DNS zone |Vyberte **Ano**. |
    |Private DNS Zone |Select *(New)privatelink.database.windows.net* |
    |||

1. Vyberte **Zkontrolovat a vytvořit**. You're taken to the **Review + create** page where Azure validates your configuration. 
2. When you see the **Validation passed** message, select **Create**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Connect to a VM using Remote Desktop (RDP)


After you've created **myVm*, connect to it from the internet as follows: 

1. In the portal's search bar, enter *myVm*.

1. Klikněte na tlačítko **Připojit**. After selecting the **Connect** button, **Connect to virtual machine** opens.

1. Select **Download RDP File**. Azure creates a Remote Desktop Protocol ( *.rdp*) file and downloads it to your computer.

1. Open the downloaded.rdp* file.

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Enter the username and password you specified when creating the VM.

        > [!NOTE]
        > You may need to select **More choices** > **Use a different account**, to specify the credentials you entered when you created the VM.

1. Vyberte **OK**.

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. If you receive a certificate warning, select **Yes** or **Continue**.

1. Once the VM desktop appears, minimize it to go back to your local desktop.  

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Access the SQL database server privately from the VM

1. In the Remote Desktop of *myVM*, open PowerShell.

2. Enter `nslookup myserver.database.windows.net`. 

    You'll receive a message similar to this:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Install [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. In **Connect to server**, enter or select this information:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Typ serveru| Vyberte **Databázový stroj**.|
    | Název serveru| Select *myserver.database.windows.net* |
    | Uživatelské jméno | Enter username as username@servername which is provided during the SQL server creation. |
    |Heslo |Enter a password provided during the SQL server creation. |
    |Remember password|Vyberte **Ano**.|
    |||
1. Vyberte **Connect** (Připojit).
2. Browse databases from left menu.
3. (Optionally) Create or query information from mydatabase.
4. Close the remote desktop connection to *myVm*. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 
When you're done using the private endpoint, SQL server, and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Vyberte **Odstranit skupinu prostředků**. 
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## <a name="next-steps"></a>Další kroky

In this quickstart, you created a VM on a virtual network, a SQL database server, and a private endpoint for private access. You connected to one VM from the internet and securely communicated to the SQL database server using Private Link. To learn more about private endpoints, see [What is Azure private endpoint?](private-endpoint-overview.md).
