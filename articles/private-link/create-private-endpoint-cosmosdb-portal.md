---
title: Připojení k účtu Azure Cosmos pomocí privátního odkazu Azure
description: Přečtěte si, jak bezpečně přistupovat k účtu Azure Cosmos z virtuálního počítače vytvořením privátního koncového bodu.
author: asudbring
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: 72dbddd449afb262941de93fd812428554496339
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91849006"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Privátní připojení k účtu služby Azure Cosmos DB s využitím služby Azure Private Link

Privátní koncový bod Azure je základním stavebním blokem privátního propojení v Azure. Umožňuje prostředkům Azure, jako jsou virtuální počítače (VM), komunikovat soukromě s prostředky privátního propojení.

V tomto článku se dozvíte, jak pomocí webu Azure Portal vytvořit virtuální počítač ve virtuální síti Azure a účet služby Azure Cosmos DB s privátním koncovým bodem. Pak budete moct bezpečně přistupovat z virtuálního počítače k účtu služby Azure Cosmos DB.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [Azure Portal.](https://portal.azure.com)

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se používá pro přístup k prostředku privátního propojení (v tomto příkladu účet Azure Cosmos).

V této části budete muset v krocích níže nahradit následující parametry:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup|
| **\<virtual-network-name>** | myVirtualNetwork         |
| **\<region-name>**          | USA – středozápad     |
| **\<IPv4-address-space>**   | 10.1.0.0 \ 16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-the-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levé horní části obrazovky Azure Portal vyberte **vytvořit**  >  **Compute**  >  **virtuální počítač**Compute.

1. V nástroji **vytvořit virtuální počítač základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.  |
    | **PODROBNOSTI INSTANCE** |  |
    | Název virtuálního počítače | Zadejte *myVm*. |
    | Oblast | Vyberte **WestCentralUS**. |
    | Možnosti dostupnosti | Nechte výchozí nastavení **bez nutnosti redundance infrastruktury**. |
    | Image | Vyberte **Windows Server 2019 Datacenter**. |
    | Velikost | Ponechte výchozí hodnotu **Standard DS1 v2**. |
    | **ÚČET SPRÁVCE** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno podle vašeho výběru. |
    | Heslo | Zadejte heslo podle svého výběru. Heslo musí mít délku aspoň 12 znaků a musí splňovat [definované požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrdit heslo | Znovu zadejte heslo. |
    | **PRAVIDLA PORTŮ PRO PŘÍCHOZÍ SPOJENÍ** |  |
    | Veřejné příchozí porty | Nechejte výchozí nastavení **žádné**. |
    | **UŠETŘETE PENÍZE** |  |
    | Máte už licenci na Windows? | Ponechte výchozí hodnotu **ne**. |
    |||

1. Vyberte **Další: disky**.

1. V okně **Vytvořit virtuální počítač – Disky** nechte vybrané výchozí hodnoty a vyberte **Další: Sítě**.

1. V nástroji **vytvořit virtuální počítač – síť**vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Virtuální síť | Ponechte výchozí **MyVirtualNetwork**.  |
    | Adresní prostor | Ponechte výchozí **10.1.0.0/24**.|
    | Podsíť | Ponechte výchozí **mySubnet (10.1.0.0/24)**.|
    | Veřejná IP adresa | Ponechte výchozí **(New) myVm-IP**. |
    | Veřejné příchozí porty | Vyberte možnost **Povolení vybraných portů**. |
    | Vyberte příchozí porty | Vyberte **http** a **RDP**.|
    ||

1. Vyberte **Zkontrolovat a vytvořit**. Budete přesměrováni na stránku **Zkontrolovat a vytvořit**, kde Azure ověří konfiguraci.

1. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně**, vyberte **Vytvořit**.

## <a name="create-an-azure-cosmos-account"></a>Vytvoření účtu Azure Cosmos

Vytvořte [účet rozhraní SQL API služby Azure Cosmos](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). Pro zjednodušení můžete vytvořit účet Azure Cosmos ve stejné oblasti jako ostatní prostředky (tj. "WestCentralUS").

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Vytvoření privátního koncového bodu pro účet Azure Cosmos

Vytvořte privátní odkaz na účet Azure Cosmos, jak je popsáno v části [Vytvoření privátního odkazu](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) v článku věnovaném použití Azure Portal v odkazovaném článku.

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Připojte se k virtuálnímu počítači *myVm* z Internetu následujícím způsobem:

1. Na portálu zadejte na panelu hledání *myVm*.

1. Klikněte na tlačítko **Připojit**. Po výběru tlačítka **připojit** se **připojte k virtuálnímu počítači** .

1. Vyberte **Stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) (*. RDP*) a stáhne ho do vašeho počítače.

1. Otevřete stažený soubor *. RDP* .

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

        > [!NOTE]
        > Možná budete muset vybrat **Další volby**  >  **použít jiný účet**a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

1. Vyberte **OK**.

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění na certifikát, vyberte **Ano** nebo **Pokračovat**.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji tak, aby se vrátila k místnímu počítači.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Privátní přístup k účtu Azure Cosmos z virtuálního počítače

V této části se připojíte soukromě k účtu Azure Cosmos pomocí privátního koncového bodu. 

1. Pokud chcete zahrnout IP adresu a mapování DNS, přihlaste se k virtuálnímu počítači *myVM*, otevřete `c:\Windows\System32\Drivers\etc\hosts` soubor a zahrňte informace DNS z předchozího kroku v následujícím formátu:

   [Privátní IP adresa] [Koncový bod účtu]. Documents. Azure. com

   **Příklad:**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. Ve vzdálené ploše *myVM*nainstalujte [Průzkumník služby Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).

1. Kliknutím pravým tlačítkem myši vyberte **Cosmos DB účty (Preview)** .

1. Vyberte **připojit k Cosmos DB**.

1. Vyberte **rozhraní API**.

1. Vložte připojovací řetězec vložením dříve zkopírovaných informací.

1. Vyberte **Další**.

1. Vyberte **Připojit**.

1. Procházejte databáze a kontejnery Azure Cosmos z *mycosmosaccount*.

1. (Volitelně) přidejte nové položky do *mycosmosaccount*.

1. Zavřete připojení ke vzdálené ploše pro *myVM*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete hotovi s použitím privátního koncového bodu, účtu Azure Cosmos a virtuálního počítače, odstraňte skupinu prostředků a všechny prostředky, které obsahuje: 

1. Do **vyhledávacího** pole v horní části portálu zadejte *myResourceGroup* a ve výsledcích hledání vyberte *myResourceGroup* .

1. Vyberte **Odstranit skupinu prostředků**.

1. Zadejte *myResourceGroup* pro **typ název skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili virtuální počítač ve virtuální síti, účtu Azure Cosmos a privátním koncovém bodu. Připojili jste se k virtuálnímu počítači z Internetu a bezpečně komunikovali s účtem Azure Cosmos pomocí privátního odkazu.

* Další informace o privátním koncovém bodu najdete v tématu [co je privátní koncový bod Azure](private-endpoint-overview.md).

* Pokud chcete získat další informace o omezení privátního koncového bodu při použití s Azure Cosmos DB, přečtěte si článek [privátní odkaz na Azure s článkem Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md) .
