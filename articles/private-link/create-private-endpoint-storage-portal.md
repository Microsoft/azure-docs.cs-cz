---
title: Privátní připojení k účtu úložiště s využitím privátního koncového bodu Azure
description: Zjistěte, jak se soukromě připojit k účtu úložiště v Azure pomocí privátního koncového bodu.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 111e6e2f80c3460f363c496b7b32befdca16250d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115114"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Privátní připojení k účtu úložiště s využitím privátního koncového bodu Azure
Azure Private Endpoint je základní stavební blok pro privátní spojení v Azure. Umožňuje prostředky Azure, jako jsou virtuální počítače (VM), soukromě komunikovat s prostředky Private Link.

V tomto rychlém startu se dozvíte, jak vytvořit virtuální počítač ve virtuální síti Azure, účet úložiště s privátním koncovým bodem pomocí portálu Azure. Potom můžete bezpečně přistupovat k účtu úložiště z virtuálního účtu.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače
V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se používá pro přístup k prostředku privátního propojení (účet úložiště v tomto příkladu).

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se používá pro přístup k prostředku private link.

V této části budete muset nahradit následující parametry v krocích s níže uvedenými informacemi:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<>název skupiny prostředků**  | myResourceGroup |
| **\<>názvů virtuálních sítí** | myVirtualNetwork          |
| **\<>názvu oblasti**          | USA – středozápad      |
| **\<>adresního prostoru IPv4**   | 10.1.0.0\16          |
| **\<>názvu podsítě**          | mySubnet        |
| **\<>rozsah emitované sítě** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]


### <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. Na levé horní straně obrazovky na webu Azure Portal vyberte **Vytvořit prostředek** > **Výpočetní** > virtuální**počítač**.

1. V **části Vytvořit virtuální počítač – základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **položku myResourceGroup**. Vytvořili jste to v předchozí části.  |
    | **PODROBNOSTI INSTANCE** |  |
    | Název virtuálního počítače | Zadejte *myVm*. |
    | Region (Oblast) | Vyberte **možnost WestCentralUS**. |
    | Možnosti dostupnosti | Ponechte výchozí **Není vyžadována redundance žádné infrastruktury**. |
    | Image | Vyberte **Datové centrum Windows Server 2019**. |
    | Velikost | Ponechte výchozí **standardní DS1 v2**. |
    | **ÚČET SPRÁVCE** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno podle svého výběru. |
    | Heslo | Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrdit heslo | Znovu zadejte heslo. |
    | **PRAVIDLA PŘÍCHOZÍHO PORTU** |  |
    | Veřejné příchozí porty | Ponechte výchozí **žádný**. |
    | **UŠETŘETE PENÍZE** |  |
    | Máte už licenci na Windows? | Ponechte výchozí **ne**. |
    |||

1. Vyberte **další: Disky**.

1. V **části Vytvoření virtuálního počítače – disky**ponechte výchozí hodnoty a vyberte **Další: Síť**.

1. V **panelu Vytvořit virtuální počítač – Vytváření sítí**vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Virtuální síť | Ponechte výchozí **program MyVirtualNetwork**.  |
    | Adresní prostor | Ponechte výchozí **hodnotu 10.1.0.0/24**.|
    | Podsíť | Ponechte výchozí **mySubnet (10.1.0.0/24)**.|
    | Veřejná IP adresa | Ponechte výchozí **(nový) myVm-ip**. |
    | Veřejné příchozí porty | Vyberte **Povolit vybrané porty**. |
    | Výběr příchozích portů | Vyberte **možnosti HTTP** a **RDP**.|
    ||

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvoření,** kde Azure ověřuje vaši konfiguraci.

1. Když se zobrazí zpráva **Ověření předáno,** vyberte **vytvořit**.

## <a name="create-your-private-endpoint"></a>Vytvoření privátního koncového bodu
V této části vytvoříte účet soukromého úložiště pomocí soukromého koncového bodu. 

1. Na levé horní straně obrazovky na webu Azure Portal vyberte Vytvořit**účet úložiště****úložiště** >  **prostředků** > .

1. V **části Vytvořit účet úložiště – základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **položku myResourceGroup**. Vytvořili jste to v předchozí části.|
    | **PODROBNOSTI INSTANCE** |  |
    | Název účtu úložiště  | Zadejte *mystorageaccount*. Pokud je tento název přijat, vytvořte jedinečný název. |
    | Region (Oblast) | Vyberte **možnost WestCentralUS**. |
    | Výkon| Ponechte výchozí **standard**. |
    | Account kind (Druh účtu) | Ponechte výchozí **úložiště (pro obecné účely v2)**. |
    | Replikace | Vyberte **geograficky redundantní úložiště pro čtení (RA-GRS).** |
    |||
  
3. Vyberte **další: Síť .**
4. V **části Vytvořit účet úložiště – Síť**, metoda připojení vyberte **Položku Soukromý koncový bod**.
5. V **části Vytvořit účet úložiště – Síť**vyberte Přidat soukromý koncový **bod**. 
6. V **části Vytvořit soukromý koncový bod**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **položku myResourceGroup**. Vytvořili jste to v předchozí části.|
    |Umístění|Vyberte **možnost WestCentralUS**.|
    |Name (Název)|Zadejte *myPrivateEndpoint*.  |
    |Dílčí prostředek úložiště|Ponechte výchozí **objekt blob**. |
    | **Sítí** |  |
    | Virtuální síť  | Vyberte *možnost MyVirtualNetwork* ze skupiny prostředků *myResourceGroup*. |
    | Podsíť | Vyberte *možnost mySubnet*. |
    | **PRIVÁTNÍ INTEGRACE DNS**|  |
    | Integrace se soukromou zónou DNS  | Ponechte výchozí **hodnotu Ano**. |
    | Zóna privátního DNS  | Ponechte výchozí **(Nový) privatelink.blob.core.windows.net**. |
    |||
7. Vyberte **OK**. 
8. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvoření,** kde Azure ověřuje vaši konfiguraci. 
9. Když se zobrazí zpráva **Ověření předáno,** vyberte **vytvořit**. 
10. Přejděte na prostředek účtu úložiště, který jste právě vytvořili.
11. V levé nabídce obsahu vyberte **Přístupové klávesy.**
12. V připojovacím řetězci pro key1 vyberte **Kopírovat.**
 
## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Připojte se k *myvm* virtuálního zařízení z internetu takto:

1. Na vyhledávacím panelu portálu zadejte *myVm*.

1. Klikněte na tlačítko **Připojit**. Po výběru tlačítka **Připojit** se otevře **možnost Připojit k virtuálnímu počítači.**

1. Vyberte **stáhnout soubor RDP**. Azure vytvoří soubor rdp (Remote Desktop Protocol *)* a stáhne ho do počítače.

1. Otevřete *soubor Downloaded.rdp.*

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

        > [!NOTE]
        > Možná budete muset vybrat **Další volby:** > K zadání pověření, která jste zadali při vytváření virtuálního vztahu,**můžete zadat jiný účet**.

1. Vyberte **OK**.

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění na certifikát, vyberte **ano** nebo **pokračovat**.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji a vraťte se na místní plochu.  

## <a name="access-storage-account-privately-from-the-vm"></a>Přístup k účtu úložiště soukromě z virtuálního účtu

V této části se připojíte soukromě k účtu úložiště pomocí privátní koncový bod.

1. Na vzdálené ploše *myVM*otevřete PowerShell.
2. Zadejte `nslookup mystorageaccount.blob.core.windows.net` Obdržíte zprávu podobnou této:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Nainstalujte [Průzkumníka služby Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Vyberte **Účty úložiště** kliknutím pravým tlačítkem myši.
5. Vyberte **Připojit k úložišti azure**.
6. Vyberte **Použít připojovací řetězec**.
7. Vyberte **další**.
8. Zadejte připojovací řetězec vložením dříve zkopírovaných informací.
9. Vyberte **další**.
10. Vyberte **Connect** (Připojit).
11. Procházení kontejnerů objektů Blob z účtu mystorageaccount 
12. (Volitelně) Vytvořte složky nebo nahrajte soubory do *účtu mystorageaccount*. 
13. Zavřete připojení ke vzdálené ploše *myVM*. 

Další možnosti přístupu k účtu úložiště:
- Microsoft Azure Storage Explorer je samostatná bezplatná aplikace od Microsoftu, která vám umožní vizuálně pracovat s daty úložiště Azure ve Windows, macOS a Linuxu. Aplikaci můžete nainstalovat a procházet obsah účtu úložiště soukromě. 
 
- Nástroj AzCopy je další možností pro vysoce výkonný skriptovatelný přenos dat pro úložiště Azure. Pomocí AzCopy můžete přenášet data do a ze služeb Blob, File a Table Storage. 


## <a name="clean-up-resources"></a>Vyčištění prostředků 
Až budete hotovi s privátním koncovým bodem, účtem úložiště a virtuálním virtuálním serverem, odstraňte skupinu prostředků a všechny prostředky, které obsahuje: 
1. Do pole **Hledat** v horní části portálu zadejte *myResourceGroup* a z výsledků hledání vyberte *myResourceGroup.* 
2. Vyberte **Odstranit skupinu prostředků**. 
3. Zadejte *myResourceGroup* pro **typ názvu skupiny prostředků** a vyberte **Odstranit**. 

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste vytvořili virtuální počítač na účet virtuální sítě a úložiště a soukromý koncový bod. Připojili jste se k jednomu virtuálnímu virtuálnímu síti z internetu a bezpečně jste komunikovali s účtem úložiště pomocí private link. Další informace o privátním koncovém bodu najdete v [tématu Co je Azure Private Endpoint?](private-endpoint-overview.md).
