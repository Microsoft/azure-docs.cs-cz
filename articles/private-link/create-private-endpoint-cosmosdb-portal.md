---
title: Připojení k účtu Azure Cosmos pomocí Privátního propojení Azure
description: Zjistěte, jak bezpečně přistupovat k účtu Azure Cosmos z virtuálního počítače vytvořením privátního koncového bodu.
author: malopMSFT
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: b7a50a2dabc9503ca5dbdd3388e29cfc69963885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252606"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Připojení privátně k účtu Azure Cosmos pomocí Privátního propojení Azure

Azure Private Endpoint je základní stavební blok pro privátní spojení v Azure. Umožňuje prostředky Azure, jako jsou virtuální počítače (VM), soukromě komunikovat s prostředky Private Link.

V tomto článku se dozvíte, jak vytvořit virtuální počítač ve virtuální síti Azure a účet Azure Cosmos s privátním koncovým bodem pomocí portálu Azure. Potom můžete bezpečně přistupovat k účtu Azure Cosmos z virtuálního počítače.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [portálu Azure.](https://portal.azure.com)

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se používá pro přístup k prostředku privátního propojení (účet Azure Cosmos v tomto příkladu).

V této části budete muset nahradit následující parametry v krocích s níže uvedenými informacemi:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<>název skupiny prostředků**  | myResourceGroup|
| **\<>názvů virtuálních sítí** | myVirtualNetwork         |
| **\<>názvu oblasti**          | USA – středozápad     |
| **\<>adresního prostoru IPv4**   | 10.1.0.0\16          |
| **\<>názvu podsítě**          | mySubnet        |
| **\<>rozsah emitované sítě** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-the-virtual-machine"></a>Vytvoření virtuálního počítače

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
    | Uživatelské jméno | Zadejte uživatelské jméno podle vašeho výběru. |
    | Heslo | Zadejte heslo podle vašeho výběru. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrdit heslo | Zadejte heslo znovu. |
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

## <a name="create-an-azure-cosmos-account"></a>Vytvoření účtu Azure Cosmos

Vytvořte [účet rozhraní SQL API Azure Cosmos](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). Pro jednoduchost můžete vytvořit účet Azure Cosmos ve stejné oblasti jako ostatní prostředky (to je "WestCentralUS").

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Vytvoření privátního koncového bodu pro váš účet Azure Cosmos

Vytvořte privátní odkaz pro váš účet Azure Cosmos, jak je popsáno v [části Vytvořit privátní odkaz pomocí](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) části portálu Azure v propojeném článku.

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Připojte se k *myvm* virtuálního zařízení z internetu takto:

1. Na vyhledávacím panelu portálu zadejte *myVm*.

1. Klikněte na tlačítko **Připojit**. Po výběru tlačítka **Připojit** se otevře **možnost Připojit k virtuálnímu počítači.**

1. Vyberte **stáhnout soubor RDP**. Azure vytvoří soubor rdp (Remote Desktop Protocol *)* a stáhne ho do počítače.

1. Otevřete stažený soubor *RDP.*

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

        > [!NOTE]
        > Možná budete muset vybrat **Další volby:** > K zadání pověření, která jste zadali při vytváření virtuálního vztahu,**můžete zadat jiný účet**.

1. Vyberte **OK**.

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění na certifikát, vyberte **ano** nebo **pokračovat**.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji a vraťte se na místní plochu.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Přístup k účtu Azure Cosmos soukromě z virtuálního počítače

V této části se připojíte soukromě k účtu Azure Cosmos pomocí privátní koncový bod. 

1. Chcete-li zahrnout IP adresu a mapování DNS, přihlaste `c:\Windows\System32\Drivers\etc\hosts` se do virtuálního počítače *myVM*, otevřete soubor a zahrňte informace DNS z předchozího kroku v následujícím formátu:

   [Soukromá IP adresa] [Koncový bod účtu].documents.azure.com

   **Příklad:**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. Na vzdálenou plochu *myVM*nainstalujte [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).

1. Vyberte **účty Cosmos DB (Preview)** kliknutím pravým tlačítkem myši.

1. Vyberte **Připojit k Cosmos DB**.

1. Vyberte **Rozhraní API**.

1. Zadejte připojovací řetězec vložením dříve zkopírovaných informací.

1. Vyberte **další**.

1. Vyberte **Connect** (Připojit).

1. Prohlédněte si databáze a kontejnery Azure Cosmos z *mycosmosaccount*.

1. (Volitelně) přidat nové položky *mycosmosaccount*.

1. Zavřete připojení ke vzdálené ploše *myVM*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až skončíte s privátním koncovým bodem, účtem Azure Cosmos a virtuálním počítačem, odstraňte skupinu prostředků a všechny prostředky, které obsahuje: 

1. Do pole **Hledat** v horní části portálu zadejte *myResourceGroup* a z výsledků hledání vyberte *myResourceGroup.*

1. Vyberte **Odstranit skupinu prostředků**.

1. Zadejte *myResourceGroup* pro **typ názvu skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili virtuální počítač ve virtuální síti, účet Azure Cosmos a privátní koncový bod. Připojili jste se k virtuálnímu počítači z internetu a bezpečně komunikovali s účtem Azure Cosmos pomocí privátního spojení.

* Další informace o privátním koncovém bodu najdete v [tématu Co je Azure Private Endpoint?](private-endpoint-overview.md).

* Další informace o omezení privátního koncového bodu při použití s Azure Cosmos DB najdete v článku [Azure Private Link s Azure Cosmos DB.](../cosmos-db/how-to-configure-private-endpoints.md)
