---
title: 'Kurz: připojení k účtu Azure Cosmos pomocí privátního koncového bodu Azure'
titleSuffix: Azure Private Link
description: Začněte s tímto kurzem pomocí privátního koncového bodu Azure a připojovat se k účtu Azure Cosmos soukromě.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: 3a7e75641f6bb84b490231fcd06e04c3cbad06d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99063463"
---
# <a name="tutorial-connect-to-an-azure-cosmos-account-using-an-azure-private-endpoint"></a>Kurz: připojení k účtu Azure Cosmos pomocí privátního koncového bodu Azure

Privátní koncový bod Azure je základním stavebním blokem privátního propojení v Azure. Umožňuje prostředkům Azure, jako jsou virtuální počítače, komunikovat soukromě s prostředky privátního propojení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte virtuální síť a hostitele bastionu.
> * Vytvoří virtuální počítač.
> * Vytvořte účet Cosmos DB s privátním koncovým bodem.
> * Otestujte připojení k Cosmos DB privátní koncový bod účtu.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network-and-bastion-host"></a>Vytvoření virtuální sítě a hostitele bastionu

V této části vytvoříte virtuální síť, podsíť a hostitele bastionu. 

Hostitel bastionu se bude používat k zabezpečenému připojení k virtuálnímu počítači za účelem testování privátního koncového bodu.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek > Sítě > Virtuální síť** nebo do vyhledávacího pole zadejte **Virtuální síť**.

2. V části **vytvořit virtuální síť** zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Vyberte své předplatné Azure.                                  |
    | Skupina prostředků   | Vybrat **myResourceGroup** |
    | **Podrobnosti o instancích** |                                                                 |
    | Name             | Zadejte **myVNet**                                    |
    | Oblast           | Vyberte **východní USA** |

3. Vyberte kartu **IP adresy** nebo v dolní části stránky vyberte tlačítko **Další: IP adresy** .

4. Na kartě **IP adresy** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Adresní prostor protokolu IPv4 | Zadejte **10.1.0.0/16** |

5. V části **název podsítě** vyberte slovo **výchozí**.

6. V **Upravit podsíť** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název podsítě | Zadejte **mySubnet** |
    | Rozsah adres podsítě | Zadejte **10.1.0.0/24** |

7. Vyberte **Uložit**.

8. Vyberte kartu **zabezpečení** .

9. V části **BastionHost** vyberte **Povolit**. Zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název bastionu | Zadejte **myBastionHost** |
    | Adresní prostor AzureBastionSubnet | Zadejte **10.1.1.0/24** |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Jako **název** zadejte **myBastionIP**. </br> Vyberte **OK**. |


8. Vyberte kartu **Revize + vytvořit** nebo vyberte tlačítko **Revize + vytvořit** .

9. Vyberte **Vytvořit**.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

V této části vytvoříte virtuální počítač, který se použije k otestování privátního koncového bodu.

1. V levé horní části portálu vyberte **vytvořit prostředek**  >    >  **virtuální počítač** COMPUTE nebo ve vyhledávacím poli vyhledejte **virtuální počítač** .
   
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **myResourceGroup** |
    | **Podrobnosti o instancích** |  |
    | Název virtuálního počítače | Zadejte **myVM** |
    | Oblast | Vyberte **východní USA** |
    | Možnosti dostupnosti | Vyberte možnost **nepožaduje se žádná redundance infrastruktury** . |
    | Image | Vyberte **Windows Server 2019 Datacenter – Gen1** |
    | Instance Azure Spot | Vybrat **ne** |
    | Velikost | Vyberte velikost virtuálního počítače nebo použijte výchozí nastavení. |
    | **Účet správce** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno. |
    | Heslo | Zadat heslo |
    | Potvrzení hesla | Znovu zadejte heslo. |

3. Vyberte kartu **síť** nebo vyberte **Další: disky** a **Další: síť**.
  
4. Na kartě sítě vyberte nebo zadejte:

    | Nastavení | Hodnota |
    |-|-|
    | **Síťové rozhraní** |  |
    | Virtuální síť | **myVNet** |
    | Podsíť | **mySubnet** |
    | Veřejná IP adresa | Vyberte **Žádná**. |
    | Skupina zabezpečení sítě NIC | **Basic**|
    | Veřejné příchozí porty | Vyberte **Žádná**. |
   
5. Vyberte **Zkontrolovat a vytvořit**. 
  
6. Zkontrolujte nastavení a pak vyberte **vytvořit**.

## <a name="create-a-cosmos-db-account-with-a-private-endpoint"></a>Vytvoření účtu Cosmos DB s privátním koncovým bodem

V této části vytvoříte účet Cosmos DB a nakonfigurujete privátní koncový bod.

1. V nabídce na levé straně vyberte vytvořit databáze **prostředků**  >    >  **Cosmos DB účet** nebo ve vyhledávacím poli vyhledejte **účet Cosmos DB** .

2. Na kartě **základy** v části **vytvořit Cosmos DB účet** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vyberte **myResourceGroup**. |
    | **Podrobnosti o instancích** |  |
    | Název účtu | Zadejte **mycosmosdb**. Pokud není název k dispozici, zadejte jedinečný název. |
    | Rozhraní API | Vyberte **Core (SQL)**. |
    | Umístění | Vyberte **USA – východ**. |
    | Režim kapacity | Ponechte výchozí **zřízenou propustnost**. |
    | Použít slevu založenou na bezplatné úrovni | Nechejte výchozí nastavení **Nepoužito**. |
    | Geografická redundance | Nechte výchozí nastavení **zakázáno**. |
    | Zápisy do více oblastí | Nechte výchozí nastavení **zakázáno**. |
   
3. Vyberte kartu **síť** nebo vyberte tlačítko **Další: síťové** .

4. Na kartě **sítě** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Připojení k síti** | |
    | Metoda připojení | Vyberte **privátní koncový bod**. |
    | **Konfigurace brány firewall** | |
    | Povolení přístupu z Azure Portal | Nechejte výchozí **Povolení**. |
    | Povolení přístupu z IP adresy | Ponechte výchozí nastavení **Odepřít**. |

5. V **privátním koncovém bodu** vyberte **+ Přidat**.

6. V části **vytvořit privátní koncový bod** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **myResourceGroup** |
    | Umístění | Vyberte **východní USA** |
    | Name | Zadejte **myPrivateEndpoint** |
    | Cílový podprostředek | Ponechat výchozí **jádro (SQL)** |
    | **Sítě** |  |
    | Virtuální síť | Vybrat **myVNet** |
    | Podsíť | Vyberte **mySubnet**. |
    | **Integrace Privátní DNS** |
    | Integrovat s privátní zónou DNS | Ponechat výchozí nastavení **Ano** |
    | Zóna Privátního DNS | Ponechte výchozí (New) privatelink.documents.azure.com |

7. Vyberte **OK**.

8. Vyberte **Zkontrolovat a vytvořit**.

9. Vyberte **Vytvořit**.

### <a name="add-a-database-and-a-container"></a>Přidání databáze a kontejneru

1. Vyberte možnost **získáno do prostředku** nebo v nabídce na levé straně Azure Portal vyberte **všechny prostředky**  >  **mycosmosdb**.

2. V nabídce na levé straně vyberte **Průzkumník dat**.

3. V okně **Průzkumník dat** vyberte **Nový kontejner**.

4. V části **Přidat kontejner** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | ID databáze | Ponechte výchozí hodnotu **vytvořit novou**. </br> Do textového pole zadejte **mydatabaseid** . |
    | Propustnost (400 – 100 000 RU/s) | Ponechte výchozí nastavení **Ruční**. </br> Do textového pole zadejte **400** . |
    | ID kontejneru | Zadejte **mycontainerid** |
    | Klíč oddílu | Zadejte **/myKey** |

5. Vyberte **OK**.

6. V části **Nastavení** účtu CosmosDB vyberte **klíče**.

7. Vyberte Kopírovat na **primárním připojovacím řetězci**.

## <a name="test-connectivity-to-private-endpoint"></a>Test připojení k privátnímu koncovému bodu

V této části použijete virtuální počítač, který jste vytvořili v předchozím kroku, abyste se připojili k účtu Cosmos DB v rámci privátního koncového bodu.

1. V levém navigačním podokně vyberte **skupiny prostředků** .

1. Vyberte **myResourceGroup**.

1. Vyberte **myVM**.

1. Na stránce Přehled pro **myVM** vyberte **připojit** a pak **bastionu**.

1. Vyberte tlačítko modrého **použití bastionu** .

1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

1. Po připojení otevřete Windows PowerShell na serveru.

1. Zadejte `nslookup <cosmosdb-account-name>.documents.azure.com` a ověřte překlad názvů. Nahraďte **\<cosmosdb-account-name>** názvem Cosmos DB účtu, který jste vytvořili v předchozích krocích. 

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mycosmosdb8675.privatelink.documents.azure.com
    Address:  10.1.0.5
    Aliases:  mycosmosdb8675.documents.azure.com
    ```
    Pro název Cosmos DB účtu se vrátí privátní IP adresa **10.1.0.5** .  Tato adresa je v podsíti virtuální sítě, kterou jste vytvořili dříve.
    
1. Získejte Azure Cosmos DB primární připojovací řetězec z portálu. Platný připojovací řetězec je ve formátu:
   
   Pro účty rozhraní SQL API: `https://<accountName>.documents.azure.com:443/;AccountKey=<accountKey>;` pro Azure Cosmos DB API pro MongoDB: `mongodb://<accountName>:<accountKey>@cdbmongo36.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false`

1. Nainstalujte na virtuální počítač [Průzkumník služby Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

1. Po instalaci **Průzkumník služby Microsoft Azure Storage** vyberte **Dokončit** .  Ponechte zaškrtnuté políčko pro otevření aplikace.

1. Na obrazovce **připojit k Azure Storage** vyberte **Zrušit**.

1. V Průzkumník služby Storage vyberte v **Cosmos DB účtech** pravé tlačítko myši a vyberte **připojit k Cosmos DB**.

1. V části **Vybrat rozhraní API** ponechte výchozí hodnotu **SQL** .

1. V poli **připojovací řetězec** vložte připojovací řetězec z účtu Cosmos DB, který jste zkopírovali v předchozích krocích.

1. Vyberte **Další**.

1. Ověřte správnost nastavení v **souhrnu připojení**.  

1. Vyberte **Connect** (Připojit).

1. Ukončete připojení k **myVM**.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte virtuální síť, virtuální počítač a Cosmos DB účet pomocí následujících kroků:

1. V nabídce na levé straně vyberte **skupiny prostředků**.

2. Vyberte **myResourceGroup**.

3. Vyberte **Odstranit skupinu prostředků**.

4. Do **pole zadejte název skupiny prostředků** zadejte **myResourceGroup** .

5. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili:

* Virtuální síť a hostitel bastionu
* Virtuální počítač.
* Účet Cosmos DB.

Naučte se vytvořit službu privátního propojení:
> [!div class="nextstepaction"]
> [Vytvoření služby privátního propojení](create-private-link-service-portal.md)
