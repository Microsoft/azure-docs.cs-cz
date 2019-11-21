---
title: Connect privately to a storage account using Azure Private Endpoint
description: Learn how to connect privately to a storage account in Azure using a Private Endpoint.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: cfe0caaf199821358f8a66ac65ae75c38336c725
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228096"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Connect privately to a storage account using Azure Private Endpoint
Azure Private Endpoint is the fundamental building block for Private Link in Azure. It enables Azure resources, like virtual machines (VMs), to communicate privately with Private Link resources.

In this Quickstart, you will learn how to create a VM on an Azure virtual network, a storage account with a Private Endpoint using the Azure portal. Then, you can securely access the storage account from the VM.


## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače
In this section, you will create virtual network and the subnet to host the VM that is used to access your Private Link Resource (a storage account in this example).

### <a name="create-the-virtual-network"></a>Create the virtual network

In this section, you will create virtual network and the subnet to host the VM that is used to access your Private Link resource.

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


### <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Compute** > **Virtual machine**.

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
    ||

1. Vyberte **Zkontrolovat a vytvořit**. You're taken to the **Review + create** page where Azure validates your configuration.

1. When you see the **Validation passed** message, select **Create**.

## <a name="create-your-private-endpoint"></a>Create your Private Endpoint
In this section, you will create a private storage account using a Private Endpoint to it. 

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Storage** > **Storage account**.

1. In **Create storage account - Basics**, enter or select this information:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PROJECT DETAILS** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Select **myResourceGroup**. You created this in the previous section.|
    | **INSTANCE DETAILS** |  |
    | Název účtu úložiště  | Enter *mystorageaccount*. If this name is taken, create a unique name. |
    | Oblast | Select **WestCentralUS**. |
    | Výkon| Leave the default **Standard**. |
    | Account kind (Druh účtu) | Leave the default **Storage (general purpose v2)** . |
    | Replikace | Select **Read-access geo-redundant storage (RA-GRS)** . |
    |||
  
3. Select **Next: Networking**.
4. In **Create a storage account - Networking**, connectivity method, select **Private Endpoint**.
5. In **Create a storage account - Networking**, select **Add Private Endpoint**. 
6. In **Create Private Endpoint**, enter or select this information:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PROJECT DETAILS** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Select **myResourceGroup**. You created this in the previous section.|
    |Umístění|Select **WestCentralUS**.|
    |Name (Název)|Enter *myPrivateEndpoint*.  |
    |Storage sub-resource|Leave the default **Blob**. |
    | **NETWORKING** |  |
    | Virtuální síť  | Select *MyVirtualNetwork* from resource group *myResourceGroup*. |
    | Podsíť | Select *mySubnet*. |
    | **PRIVATE DNS INTEGRATION**|  |
    | Integrate with private DNS zone  | Leave the default **Yes**. |
    | Zóna privátního DNS  | Leave the default ** (New) privatelink.blob.core.windows.net**. |
    |||
7. Vyberte **OK**. 
8. Vyberte **Zkontrolovat a vytvořit**. You're taken to the **Review + create** page where Azure validates your configuration. 
9. When you see the **Validation passed** message, select **Create**. 
10. Browse to the storage account resource that you just created.
11. Select **Access Keys** from the left content menu.
12. Select **Copy** on the connection string for key1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Connect to the VM *myVm* from the internet as follows:

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

## <a name="access-storage-account-privately-from-the-vm"></a>Access storage account privately from the VM

In this section, you will connect privately to the storage account using the Private Endpoint.

> [!IMPORTANT]
> DNS configuration for storage needs a manual modification on the hosts file to include the FQDN of the specific account Please modify the following file using administrator permissions on Windows: c:\Windows\System32\Drivers\etc\hosts or Linux /etc/hosts Include the DNS information for the account from previous step in the following format [Private IP Address] myaccount.blob.core.windows.net

1. In the Remote Desktop of *myVM*, open PowerShell.
2. Enter `nslookup mystorageaccount.blob.core.windows.net` You'll receive a message similar to this:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Nainstalujte [Průzkumníka služby Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. Vyberte **Další**.
8. Enter the connection string by pasting the information previously copied.
9. Vyberte **Další**.
10. Vyberte **Connect** (Připojit).
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 

Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. Pomocí AzCopy můžete přenášet data do a ze služeb Blob, File a Table Storage. 


## <a name="clean-up-resources"></a>Vyčištění prostředků 
When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Vyberte **Odstranit skupinu prostředků**. 
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## <a name="next-steps"></a>Další kroky
In this Quickstart, you created a VM on a virtual network and storage account and a Private Endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
