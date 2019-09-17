---
title: Vytvoření privátního koncového bodu Azure pomocí Azure Portal | Microsoft Docs
description: Seznamte se s privátním koncovým bodem Azure
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 8a569f91a27884c320f4af5b1282bfe4dd9a5a99
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018942"
---
# <a name="create-a-private-endpoint-using-the-azure-portal"></a>Vytvoření privátního koncového bodu pomocí Azure Portal
Privátní koncový bod Azure je základním stavebním blokem privátního propojení v Azure. Umožňuje prostředkům Azure, jako jsou virtuální počítače (VM), komunikovat soukromě s prostředky privátního propojení.

V tomto rychlém startu se dozvíte, jak vytvořit virtuální počítač ve službě Azure Virtual Network, účet úložiště s privátním koncovým bodem pomocí Azure Portal. Pak můžete z virtuálního počítače bezpečně přistupovat k účtu úložiště.


## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače
V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se používá pro přístup k prostředku privátního propojení (účet úložiště v tomto příkladu).

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se používá pro přístup k prostředku privátního propojení.

1. V levé horní části obrazovky vyberte **vytvořit prostředek** > **síť** > **virtuální síť**.
1. V nástroji **vytvořit virtuální síť**zadejte nebo vyberte tyto informace:

    | Nastavení | Value |
    | ------- | ----- |
    | Name | Zadejte *MyVirtualNetwork*. |
    | Adresní prostor | Zadejte *10.1.0.0/16*. |
    | Subscription | Vyberte své předplatné.|
    | Resource group | Vyberte **vytvořit nový**, zadejte *myResourceGroup*a pak vyberte **OK**. |
    | Location | Vyberte **WestCentralUS**.|
    | Název podsítě | Zadejte *mySubnet*. |
    | Podsíť – Rozsah adres | Zadejte *10.1.0.0/24*. |
    |||
1. Ponechte REST jako výchozí a vyberte **vytvořit**.


### <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levé horní části obrazovky Azure Portal vyberte **vytvořit** > **virtuální počítač** **COMPUTE** > .

1. V nástroji **vytvořit virtuální počítač základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Value |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Subscription | Vyberte své předplatné. |
    | Resource group | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.  |
    | **PODROBNOSTI INSTANCE** |  |
    | Název virtuálního počítače | Zadejte *myVm*. |
    | Oblast | Vyberte **WestCentralUS**. |
    | Možnosti dostupnosti | Nechte výchozí nastavení **bez nutnosti redundance infrastruktury**. |
    | Image | Vyberte **Windows Server 2019 Datacenter**. |
    | Size | Ponechte výchozí hodnotu **Standard DS1 v2**. |
    | **ÚČET SPRÁVCE** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno, které si zvolíte. |
    | Heslo | Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrzení hesla | Zadejte znovu heslo. |
    | **PRAVIDLA PORTŮ PRO PŘÍCHOZÍ SPOJENÍ** |  |
    | Veřejné příchozí porty | Nechejte výchozí nastavení **žádné**. |
    | **ÚSPORA PENĚZ** |  |
    | Máte už licenci na Windows? | Ponechte výchozí hodnotu **ne**. |
    |||

1. Vyberte **další: Disky**.

1. V části **vytvořit virtuální počítač – disky**ponechte výchozí hodnoty a vyberte **další: Sítě**.

1. V nástroji **vytvořit virtuální počítač – síť**vyberte tyto informace:

    | Nastavení | Value |
    | ------- | ----- |
    | Virtuální síť | Ponechte výchozí **MyVirtualNetwork**.  |
    | Adresní prostor | Ponechte výchozí **10.1.0.0/24**.|
    | Subnet | Ponechte výchozí **mySubnet (10.1.0.0/24)** .|
    | Veřejná IP adresa | Ponechte výchozí **(New) myVm-IP**. |
    | Veřejné příchozí porty | Vyberte možnost **Povolení vybraných portů**. |
    | Vybrat příchozí porty | Vyberte **http** a **RDP**.|
    ||

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvořit** , kde Azure ověřuje vaši konfiguraci.

1. Když se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit**.

## <a name="create-your-private-endpoint"></a>Vytvoření privátního koncového bodu
V této části vytvoříte privátním koncovým bodem privátního účtu úložiště. 

1. V levé horní části obrazovky Azure Portal vyberte **vytvořit prostředek** >  > úložiště**účet**úložiště.

1. V nástroji **vytvořit účet úložiště – základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Value |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Subscription | Vyberte své předplatné. |
    | Resource group | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.|
    | **PODROBNOSTI INSTANCE** |  |
    | Název účtu úložiště  | Zadejte *mystorageaccount*. Pokud se tento název povede, vytvořte jedinečný název. |
    | Oblast | Vyberte **WestCentralUS**. |
    | Výkon| Ponechte výchozí **Standard**. |
    | Druh účtu | Ponechte výchozí **úložiště (pro obecné účely v2)** . |
    | Replikace | Vyberte **geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)** . |
    |||
  
3. Vyberte **další: Sítě**.
4. V možnosti **vytvořit účet úložiště – síť**, způsob připojení vyberte **privátní koncový bod**.
5. V nástroji **vytvořit účet úložiště – síť**vyberte **Přidat privátní koncový bod**. 
6. V **Vytvoření privátního koncového bodu**zadejte nebo vyberte tyto informace:

    | Nastavení | Value |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Subscription | Vyberte své předplatné. |
    | Resource group | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.|
    |Location|Vyberte **WestCentralUS**.|
    |Name|Zadejte *myPrivateEndpoint*.  |
    |Dílčí prostředek úložiště|Ponechte výchozí **objekt BLOB**. |
    | **SÍTĚ** |  |
    | Virtuální síť  | Vyberte *MyVirtualNetwork* ze skupiny prostředků *myResourceGroup*. |
    | Subnet | Vyberte *mySubnet*. |
    | **INTEGRACE PRIVÁTNÍ DNS**|  |
    | Integrace s privátní zónou DNS  | Ponechte výchozí **hodnotu Ano**. |
    | Zóna Privátní DNS  | Ponechte výchozí  ** (New) privatelink.blob.Core.Windows.NET**. |
    |||
7. Vyberte **OK**. 
8. Vyberte **zkontrolovat + vytvořit**. Přejdete na stránku **Revize + vytvořit** , kde Azure ověřuje vaši konfiguraci. 
9. Když se zobrazí zpráva s **potvrzením ověření**, vyberte **vytvořit**. 
10. Přejděte k prostředku účtu úložiště, který jste právě vytvořili.
11. V nabídce vlevo obsah vyberte **přístupové klíče** .
12. Vyberte možnost **Kopírovat** na připojovací řetězec pro klíč1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Připojte se k virtuálnímu počítači *myVm* z Internetu následujícím způsobem:

1. Na panelu hledání na portálu zadejte *myVm*.

1. Klikněte na tlačítko **Připojit**. Po výběru tlačítka **připojit** se **připojte k virtuálnímu počítači** .

1. Vyberte **stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) ( *. RDP*) a stáhne ho do vašeho počítače.

1. Otevřete stažený soubor. RDP *.

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

        > [!NOTE]
        > Možná budete muset vybrat **Další volby** > **použít jiný účet**a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

1. Vyberte **OK**.

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění certifikátu, vyberte **Ano** nebo **pokračovat**.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji tak, aby se vrátila k místnímu počítači.  

## <a name="access-storage-account-privately-from-the-vm"></a>Přístup k účtu úložiště soukromě z virtuálního počítače

V této části se soukromě připojíte k účtu úložiště pomocí privátního koncového bodu.

> [!IMPORTANT]
> Konfigurace DNS pro úložiště vyžaduje ruční úpravu souboru hostitelů, aby zahrnovala plně kvalifikovaný název domény konkrétního účtu. Upravte prosím následující soubor pomocí oprávnění správce ve Windows: c:\Windows\System32\Drivers\etc\hosts nebo Linux/etc/hosts. Zahrňte informace DNS pro účet z předchozího kroku v následujícím formátu [privátní IP adresa] myaccount.blob.core.windows.net

1. Ve vzdálené ploše *myVM*otevřete PowerShell.
2. Po `nslookup mystorageaccount.blob.core.windows.net` zadání této zprávy se zobrazí zpráva podobná této:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
3. Install [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. Select **Next**.
8. Enter the connection string by pasting the information previously copied.
9. Select **Next**.
10. Select **Connect**.
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 

Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. Use AzCopy to transfer data to and from Blob, File, and Table storage. 


## Clean up resources 
When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## Next steps
In this Quickstart, you created a VM on a virtual network and storage account and a Private Endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
