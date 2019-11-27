---
title: Připojení soukromě k účtu úložiště pomocí privátního koncového bodu Azure
description: Naučte se připojit soukromě k účtu úložiště v Azure pomocí privátního koncového bodu.
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
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Připojení soukromě k účtu úložiště pomocí privátního koncového bodu Azure
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

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte *MyVirtualNetwork*. |
    | Adresní prostor | Zadejte *10.1.0.0/16*. |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **vytvořit nový**, zadejte *myResourceGroup*a pak vyberte **OK**. |
    | Umístění | Vyberte **WestCentralUS**.|
    | Název podsítě | Zadejte *mySubnet*. |
    | Podsíť – Rozsah adres | Zadejte *10.1.0.0/24*. |
    |||
1. Ponechte REST jako výchozí a vyberte **vytvořit**.


### <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levé horní části obrazovky Azure Portal vyberte **vytvořit prostředek** > **výpočetní** > **virtuální počítač**.

1. V nástroji **vytvořit virtuální počítač základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.  |
    | **PODROBNOSTI INSTANCE** |  |
    | Název virtuálního počítače | Zadejte *myVm*. |
    | Region (Oblast) | Vyberte **WestCentralUS**. |
    | Možnosti dostupnosti | Nechte výchozí nastavení **bez nutnosti redundance infrastruktury**. |
    | Image | Vyberte **Windows Server 2019 Datacenter**. |
    | Velikost | Ponechte výchozí hodnotu **Standard DS1 v2**. |
    | **ÚČET SPRÁVCE** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno, které si zvolíte. |
    | Heslo | Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrzení hesla | Zadejte znovu heslo. |
    | **PRAVIDLA PORTŮ PRO PŘÍCHOZÍ SPOJENÍ** |  |
    | Veřejné příchozí porty | Nechejte výchozí nastavení **žádné**. |
    | **ÚSPORA PENĚZ** |  |
    | Máte už licenci na Windows? | Ponechte výchozí hodnotu **ne**. |
    |||

1. Vyberte **Další: disky**.

1. V části **vytvořit virtuální počítač – disky**ponechte výchozí hodnoty a vyberte **Další: sítě**.

1. V nástroji **vytvořit virtuální počítač – síť**vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Virtuální síť | Ponechte výchozí **MyVirtualNetwork**.  |
    | Adresní prostor | Ponechte výchozí **10.1.0.0/24**.|
    | Podsíť | Ponechte výchozí **mySubnet (10.1.0.0/24)** .|
    | Veřejná IP adresa | Ponechte výchozí **(New) myVm-IP**. |
    | Veřejné příchozí porty | Vyberte možnost **Povolení vybraných portů**. |
    | Vybrat příchozí porty | Vyberte **http** a **RDP**.|
    ||

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvořit** , kde Azure ověřuje vaši konfiguraci.

1. Když se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit**.

## <a name="create-your-private-endpoint"></a>Vytvoření privátního koncového bodu
V této části vytvoříte privátním koncovým bodem privátního účtu úložiště. 

1. V levé horní části obrazovky Azure Portal vyberte **vytvořit prostředek** > **úložiště** > **účet úložiště**.

1. V nástroji **vytvořit účet úložiště – základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.|
    | **PODROBNOSTI INSTANCE** |  |
    | Název účtu úložiště  | Zadejte *mystorageaccount*. Pokud se tento název povede, vytvořte jedinečný název. |
    | Region (Oblast) | Vyberte **WestCentralUS**. |
    | Výkon| Ponechte výchozí **Standard**. |
    | Account kind (Druh účtu) | Ponechte výchozí **úložiště (pro obecné účely v2)** . |
    | Replikace | Vyberte **geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)** . |
    |||
  
3. Vyberte **Další: sítě**.
4. V možnosti **vytvořit účet úložiště – síť**, způsob připojení vyberte **privátní koncový bod**.
5. V nástroji **vytvořit účet úložiště – síť**vyberte **Přidat privátní koncový bod**. 
6. V **Vytvoření privátního koncového bodu**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.|
    |Umístění|Vyberte **WestCentralUS**.|
    |Název|Zadejte *myPrivateEndpoint*.  |
    |Dílčí prostředek úložiště|Ponechte výchozí **objekt BLOB**. |
    | **SÍTĚ** |  |
    | Virtuální síť  | Vyberte *MyVirtualNetwork* ze skupiny prostředků *myResourceGroup*. |
    | Podsíť | Vyberte *mySubnet*. |
    | **INTEGRACE PRIVÁTNÍ DNS**|  |
    | Integrace s privátní zónou DNS  | Ponechte výchozí **hodnotu Ano**. |
    | Zóna privátního DNS  | Ponechte výchozí * * (New) privatelink.blob.core.windows.net * *. |
    |||
7. Vyberte **OK**. 
8. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvořit** , kde Azure ověřuje vaši konfiguraci. 
9. Když se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit**. 
10. Přejděte k prostředku účtu úložiště, který jste právě vytvořili.
11. V nabídce vlevo obsah vyberte **přístupové klíče** .
12. Vyberte možnost **Kopírovat** na připojovací řetězec pro klíč1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Připojte se k virtuálnímu počítači *myVm* z Internetu následujícím způsobem:

1. Na panelu hledání na portálu zadejte *myVm*.

1. Klikněte na tlačítko **Připojit**. Po výběru tlačítka **připojit** se **připojte k virtuálnímu počítači** .

1. Vyberte **Stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) ( *. RDP*) a stáhne ho do vašeho počítače.

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
> Konfigurace DNS pro úložiště vyžaduje ruční úpravu souboru hostitelů, aby zahrnovala plně kvalifikovaný název domény konkrétního účtu. Upravte následující soubor pomocí oprávnění správce ve Windows: c:\Windows\System32\Drivers\etc\hosts nebo Linux/etc/hosts zahrnuje informace DNS pro účet z předchozího kroku v následujícím formátu [privátní IP adresa] myaccount.blob.core.windows.net

1. Ve vzdálené ploše *myVM*otevřete PowerShell.
2. Zadejte `nslookup mystorageaccount.blob.core.windows.net` obdržíte zprávu podobnou této:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Nainstalujte [Průzkumníka služby Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Vyberte **účty úložiště** kliknutím pravým tlačítkem myši.
5. Vyberte **připojit k úložišti Azure**.
6. Vyberte **Použít připojovací řetězec**.
7. Vyberte **Next** (Další).
8. Vložte připojovací řetězec vložením dříve zkopírovaných informací.
9. Vyberte **Next** (Další).
10. Vyberte **Connect** (Připojit).
11. Procházení kontejnerů objektů BLOB z mystorageaccount 
12. Volitelně Vytvořte složky nebo nahrajte soubory do *mystorageaccount*. 
13. Zavřete připojení ke vzdálené ploše pro *myVM*. 

Další možnosti pro přístup k účtu úložiště:
- Průzkumník služby Microsoft Azure Storage je samostatná bezplatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linux. Můžete nainstalovat aplikaci, která bude procházet soukromý obsah účtu úložiště. 
 
- Nástroj AzCopy je další možností pro vysoce výkonný přenos dat pro Azure Storage. Pomocí AzCopy můžete přenášet data do a ze služeb Blob, File a Table Storage. 


## <a name="clean-up-resources"></a>Vyčištění prostředků 
Až budete hotovi s použitím privátního koncového bodu, účtu úložiště a virtuálního počítače, odstraňte skupinu prostředků a všechny prostředky, které obsahuje: 
1. Do **vyhledávacího** pole v horní části portálu zadejte *myResourceGroup* a ve výsledcích hledání vyberte *myResourceGroup* . 
2. Vyberte **Odstranit skupinu prostředků**. 
3. Zadejte *myResourceGroup* pro **typ název skupiny prostředků** a vyberte **Odstranit**. 

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste vytvořili virtuální počítač ve virtuální síti a účtu úložiště a privátním koncovém bodu. Připojili jste se k jednomu virtuálnímu počítači z Internetu a zabezpečeně komunikovali s účtem úložiště pomocí privátního odkazu. Další informace o privátním koncovém bodu najdete v tématu [co je privátní koncový bod Azure](private-endpoint-overview.md).
