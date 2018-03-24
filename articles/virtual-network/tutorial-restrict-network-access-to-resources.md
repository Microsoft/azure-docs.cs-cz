---
title: Omezit síťový přístup k prostředkům PaaS - portálu Azure | Microsoft Docs
description: Zjistěte, jak chcete omezit a omezit přístup k síti na prostředky Azure, například Azure Storage a Azure SQL Database, s koncovými body služby virtuální sítě pomocí portálu Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 9a64a5c1f63dc05cba6fdfa310b694e34bdba7d1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Omezit přístup k síti na PaaS prostředky s koncovými body služby virtuální sítě pomocí portálu Azure

Koncové body služby virtuální sítě umožňují omezit přístup k síti k některým prostředkům služby Azure do podsítě virtuální sítě. Můžete také odebrat přístup k Internetu k prostředkům. Koncové body služby poskytují přímé připojení z virtuální sítě k podporované služby Azure, budete moci použít privátní adresní prostor vaší virtuální sítě pro přístup ke službám Azure. Přenosy určené prostředky Azure prostřednictvím koncových bodů služby vždy zůstává na páteřní síti Microsoft Azure. V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Vytvořit virtuální síť s jednou podsítí
> * Přidat podsíť a povolení koncového bodu služby
> * Vytvořit prostředek služby Azure a povolit přístup k síti z jenom podsítě
> * Nasazení virtuálního počítače (VM) pro každou podsíť
> * Potvrďte přístup k prostředku z podsítě
> * Potvrďte, že byl odepřen přístup k prostředku z podsítě a z Internetu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k portálu Azure v http://portal.azure.com.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Vyberte **+ vytvořit prostředek** na horní, levého horního rohu portálu Azure.
2. Vyberte **sítě**a potom vyberte **virtuální síť**.
3. Zadejte, nebo vyberte následující informace a potom vyberte **vytvořit**:

    |Nastavení|Hodnota|
    |----|----|
    |Název| myVirtualNetwork |
    |Adresní prostor| 10.0.0.0/16|
    |Předplatné| Vyberte předplatné|
    |Skupina prostředků | Vyberte **vytvořit nový** a zadejte *myResourceGroup*.|
    |Umístění| Vyberte **východní USA** |
    |Název podsítě| Veřejné|
    |Rozsah adres podsítě| 10.0.0.0/24|
    |Koncové body služby| Zakázáno|

    ![Zadejte základní informace o vaší virtuální sítě](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)


## <a name="enable-a-service-endpoint"></a>Povolení koncového bodu služby

1. V **hledání prostředků, služeb a dokumentace** pole v horní části portálu, zadejte *myVirtualNetwork.* Když **myVirtualNetwork** se zobrazí ve výsledcích hledání, vyberte ho.
2. Přidáte podsíť virtuální sítě. V části **nastavení**, vyberte **podsítě**a potom vyberte **+ podsítě**, jak je znázorněno na následujícím obrázku:

    ![Přidání podsítě](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. V části **přidat podsíť**, vyberte nebo zadejte následující informace a potom vyberte **OK**:

    |Nastavení|Hodnota|
    |----|----|
    |Název| Privátní |
    |Rozsah adres| 10.0.1.0/24|
    |Koncové body služby| Vyberte **Microsoft.Storage** pod **služby**|

## <a name="restrict-network-access-to-and-from-a-subnet"></a>Omezit přístup k síti do a z podsítě

1. Vyberte **+ vytvořit prostředek** na horní, levého horního rohu portálu Azure.
2. Vyberte **sítě**a potom vyberte **skupinu zabezpečení sítě**.
V části **vytvořit skupinu zabezpečení sítě**, zadejte, nebo vyberte následující informace a potom vyberte **vytvořit**:

    |Nastavení|Hodnota|
    |----|----|
    |Název| myNsgPrivate |
    |Předplatné| Vyberte předplatné|
    |Skupina prostředků | Vyberte **použít existující** a vyberte *myResourceGroup*.|
    |Umístění| Vyberte **východní USA** |

4. Po vytvoření skupiny zabezpečení sítě zadejte *myNsgPrivate*v **hledání prostředků, služeb a dokumentace** pole v horní části portálu. Když **myNsgPrivate** se zobrazí ve výsledcích hledání, vyberte ho.
5. V části **nastavení**, vyberte **odchozí pravidla zabezpečení**.
6. Vyberte **+ přidat**.
7. Vytvoření pravidla, které umožňuje odchozí přístup na veřejné IP adresy přiřazené ke službě Azure Storage. Zadejte, nebo vyberte následující informace a potom vyberte **OK**:

    |Nastavení|Hodnota|
    |----|----|
    |Zdroj| Select **VirtualNetwork** |
    |Rozsahy portů zdroje| * |
    |Cíl | Vyberte **služby značky**|
    |Cílové služby značky | Vyberte **úložiště**|
    |Cílový port rozsahů| * |
    |Protocol (Protokol)|Všechny|
    |Akce|Povolit|
    |Priorita|100|
    |Název|Allow-Storage-All|
8. Vytvoření pravidla, která přepisuje výchozí pravidlo zabezpečení, které umožňuje odchozí přístup na všechny veřejné IP adresy. Proveďte kroky 6 a 7 znovu, pomocí následujících hodnot:

    |Nastavení|Hodnota|
    |----|----|
    |Zdroj| Select **VirtualNetwork** |
    |Rozsahy portů zdroje| * |
    |Cíl | Vyberte **služby značky**|
    |Cílové služby značky| Vyberte **Internetu**|
    |Cílový port rozsahů| * |
    |Protocol (Protokol)|Všechny|
    |Akce|Odepřít|
    |Priorita|110|
    |Název|Deny-Internet-All|

9. V části **nastavení**, vyberte **příchozí pravidla zabezpečení**.
10. Vyberte **+ přidat**.
11. Vytvořte pravidlo, které umožňuje přenos protokolu RDP (Remote Desktop) k podsíti příchozí odkudkoli. Toto pravidlo přepisuje výchozí pravidlo zabezpečení, které odepře veškerý příchozí provoz z Internetu. Podsíť je povoleno připojení ke vzdálené ploše, tak, aby připojení může být testována v pozdější fázi. Proveďte kroky 6 a 7 znovu, pomocí následujících hodnot:

    |Nastavení|Hodnota|
    |----|----|
    |Zdroj| Všechny |
    |Rozsahy portů zdroje| * |
    |Cíl | Vyberte **služby značky**|
    |Cílové služby značky| Select **VirtualNetwork**|
    |Cílový port rozsahů| 3389 |
    |Protocol (Protokol)|Všechny|
    |Akce|Povolit|
    |Priorita|120|
    |Název|Povolit všechny protokolu RDP|

12. V části **nastavení**, vyberte **podsítě**.
13. Vyberte **+ přidružení**
14. V části **přidružení podsítě**, vyberte **virtuální síť** a pak vyberte **myVirtualNetwork** pod **vyberte virtuální síť**.
15. V části **zvolte podsíť**, vyberte **privátní**a potom vyberte **OK**.

## <a name="restrict-network-access-to-a-resource"></a>Omezit přístup k síti na prostředek

Kroky potřebné k omezit přístup k síti na prostředky, které jsou vytvořené pomocí služby Azure, které jsou povolené pro koncové body služby se liší v rámci služby. Najdete v dokumentaci pro jednotlivé služby pro konkrétní kroky pro každou službu. Zbývající část tohoto článku obsahuje kroky k omezit přístup k síti pro účet úložiště Azure jako příklad.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

1. Vyberte **+ vytvořit prostředek** na horní, levého horního rohu portálu Azure.
2. Vyberte **úložiště**a potom vyberte **účet úložiště – objekt blob, soubor, tabulka, fronta**.
3. Zadejte, nebo vyberte následující informace, přijměte zbývající výchozí hodnoty a potom vyberte **vytvořit**:

    |Nastavení|Hodnota|
    |----|----|
    |Název| Zadejte název, který je jedinečné ve všech umístěních Azure, 3 až 24 znaků, pomocí jenom čísla a malá písmena.|
    |Account kind (Druh účtu)|StorageV2 (obecné účely v2)|
    |Replikace| Místně redundantní úložiště (LRS)|
    |Předplatné| Vyberte předplatné|
    |Skupina prostředků | Vyberte **použít existující** a vyberte *myResourceGroup*.|
    |Umístění| Vyberte **východní USA** |

### <a name="create-a-file-share-in-the-storage-account"></a>Vytvoření sdílené složky v účtu úložiště

1. Po vytvoření účtu úložiště, zadejte název účtu úložiště v **hledání prostředků, služeb a dokumentace** pole v horní části portálu. Pokud název účtu úložiště se zobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte **soubory**, jak je znázorněno na následujícím obrázku:

    ![Účet úložiště](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 
3. Vyberte **+ sdílená**v části **služba File**.
4. Zadejte *Moje sdílené složky* pod **název**a potom vyberte **OK**.
5. Zavřít **služba File** pole.

### <a name="enable-network-access-from-a-subnet"></a>Povolit přístup k síti z podsítě

Ve výchozím nastavení účty úložiště přijmout síťová připojení z klientů ve žádné síti. Povolit přístup z konkrétní podsítě a odepřít přístup k síti ze všech ostatních sítí, proveďte následující kroky:

1. V části **nastavení** pro účet úložiště vyberte **brány firewall a virtuální sítě**.
2. V části **virtuální sítě**, vyberte **vybrané sítě**.
3. Vyberte **přidat existující virtuální síť**.
4. V části **přidat sítě**, vyberte tyto hodnoty a potom vyberte **přidat**:

    |Nastavení|Hodnota|
    |----|----|
    |Předplatné| Vyberte své předplatné.|
    |Virtuální sítě|Vyberte **myVirtualNetwork**v části **virtuální sítě**|
    |Podsítě| Vyberte **privátní**v části **podsítě**|

    ![Brány firewall a virtuální sítě](./media/tutorial-restrict-network-access-to-resources/storage-firewalls-and-virtual-networks.png) 

5. Vyberte **Uložit**.
6. Zavřít **brány firewall a virtuální sítě** pole.
7. V části **nastavení** pro účet úložiště vyberte **přístupové klíče**, jak je znázorněno na následujícím obrázku:

      ![Brány firewall a virtuální sítě](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

8. Poznámka: **klíč** hodnoty, jak je budete muset ručně zadejte později při mapování sdílené složky na písmeno jednotky ve virtuálním počítači.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Otestovat přístup k síti na účet úložiště, nasaďte virtuální počítač do každé podsítě.

### <a name="create-the-first-virtual-machine"></a>Vytvořte první virtuální počítač

1. Vyberte **+ vytvořit prostředek** najít v levém dolním rohu portálu Azure.
2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**.
3. Zadejte, nebo vyberte následující informace a potom vyberte **OK**:

    |Nastavení|Hodnota|
    |----|----|
    |Název| myVmPublic|
    |Uživatelské jméno|Zadejte uživatelské jméno dle vlastního výběru.|
    |Heslo| Zadejte heslo dle vlastního výběru. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků| Vyberte **použít existující** a vyberte **myResourceGroup**.|
    |Umístění| Vyberte **východní USA**.|

    ![Zadejte základní informace o virtuálním počítači](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)
4. Vyberte velikost virtuálního počítače a pak vyberte **vyberte**.
5. V části **nastavení**, vyberte **sítě** a pak vyberte **myVirtualNetwork**. Potom vyberte **podsíť**a vyberte **veřejné**, jak je znázorněno na následujícím obrázku:

    ![Vyberte virtuální síť.](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)
6. Na **Souhrn** vyberte **vytvořit** ke spuštění nasazení virtuálního počítače. Virtuální počítač trvá několik minut pro nasazení, ale můžete pokračovat dalším krokem při je vytvoření virtuálního počítače.

### <a name="create-the-second-virtual-machine"></a>Vytvořit druhý virtuální počítač

Úplné kroky 1 – 6 znovu, ale v kroku 3, název virtuálního počítače *myVmPrivate* a v kroku 5, vyberte **privátní** podsítě.

Virtuální počítač trvá několik minut pro nasazení. Nebudete pokračovat k dalšímu kroku až se dokončí vytváření a otevřete jeho nastavení na portálu.

## <a name="confirm-access-to-storage-account"></a>Potvrďte volbu přístup k účtu úložiště

1. Jednou *myVmPrivate* virtuálních počítačů dokončí vytváření, nastavení se otevře Azure. Připojit k virtuálnímu počítači tak, že vyberete **Connect** tlačítko, jak je znázorněno na následujícím obrázku:

    ![Připojit k virtuálnímu počítači](./media/tutorial-restrict-network-access-to-resources/connect-to-virtual-machine.png)

2. Po výběru **Connect** tlačítko soubor Remote Desktop Protocol (.rdp) je vytvořen a stažena do počítače.  
3. Otevřete soubor stažený rdp. Po zobrazení výzvy vyberte **Connect**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Je nutné vybrat **další možnosti**, pak **použít jiný účet**, zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače. 
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění, vyberte **Ano** nebo **pokračovat**, chcete-li pokračovat s připojením.
6. Na *myVmPrivate* virtuálních počítačů, mapování jednotky pomocí prostředí PowerShell Z sdílenou složku Azure file. Před spuštěním příkazů, které následují, nahraďte `<storage-account-key>` a `<storage-account-name>` s hodnotami zadaná a načíst v [vytvořit účet úložiště](#create-a-storage-account).

    ```powershell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
    ```
    
    Prostředí PowerShell vrátí výstup podobné výstupu v následujícím příkladu:

    ```powershell
    Name           Used (GB)     Free (GB) Provider      Root
    ----           ---------     --------- --------      ----
    Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
    ```

    Úspěšně namapován na jednotku Z Azure sdílené složky.

7. Potvrďte, že virtuální počítač nemá odchozí připojení k jiné veřejné adresy IP z příkazového řádku:

    ```
    ping bing.com
    ```
    
    Obdržíte bez odpovědi, protože skupina zabezpečení sítě přidružené k *privátní* podsíť neumožňuje odchozí přístup k veřejné IP adresy než adresy přiřazené ke službě Azure Storage.

8. Zavřete relaci vzdálené plochy k *myVmPrivate* virtuálních počítačů.

## <a name="confirm-access-is-denied-to-storage-account"></a>Potvrďte, že byl odepřen přístup k účtu úložiště

1. Zadejte *myVmPublic* v **hledání prostředků, služeb a dokumentace** pole v horní části portálu.
2. Když **myVmPublic** se zobrazí ve výsledcích hledání, vyberte ho.
3. Dokončete kroky 1 – 6 v [potvrzení přístupu k účtu úložiště](#confirm-access-to-storage-account) pro *myVmPublic* virtuálních počítačů.

    Přístup byl odepřen a zobrazí se `New-PSDrive : Access is denied` chyby. Přístup je odepřen, protože *myVmPublic* virtuální počítač nasazen v *veřejné* podsítě. *Veřejné* podsíť nemá povoleno pro Azure Storage koncového bodu služby a účet úložiště pouze umožňuje přístup k síti z *privátní* podsíť, není *veřejné*podsítě.

4. Zavřete relaci vzdálené plochy k *myVmPublic* virtuálních počítačů.

5. Z počítače, přejděte do Azure [portál](https://portal.azure.com).
6. Zadejte název účtu úložiště, kterou jste vytvořili v **hledání prostředků, služeb a dokumentace** pole. Pokud název účtu úložiště se zobrazí ve výsledcích hledání, vyberte ho.
7. Vyberte **Soubory**.
8. Zobrazí se chyba znázorněno na následujícím obrázku:

    ![Chyba odepření přístupu](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)

    Přístup byl odepřen, protože není v počítači *privátní* podsíť *MyVirtualNetwork* virtuální sítě.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Zadejte *myResourceGroup* v **vyhledávání** pole v horní části portálu. Až se zobrazí **myResourceGroup** ve výsledcích hledání vyberte ho.
2. Vyberte **Odstranit skupinu prostředků**.
3. Zadejte *myResourceGroup* pro **název skupiny prostředků typu:** a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste povolili koncového bodu služby pro podsíť virtuální sítě. Jste zjistili, že koncových bodů služby se dá nastavit pro prostředky nasazené s více službami Azure. Vytvořili jste účet úložiště Azure a omezený přístup k síti k účtu úložiště na pouze prostředky v podsíti virtuální sítě. Před vytvořením koncové body služby v produkčním prostředí virtuální sítě, se doporučuje, aby důkladně Seznamte se s [koncové body služby](virtual-network-service-endpoints-overview.md).

Pokud máte více virtuálních sítí ve vašem účtu, můžete k propojení dvou virtuálních sítí tak prostředky v rámci jedné virtuální sítě může komunikovat navzájem. Přechodu na v dalším kurzu se dozvíte, jak připojit virtuální sítě.

> [!div class="nextstepaction"]
> [Připojit virtuální sítě](./tutorial-connect-virtual-networks-portal.md)
