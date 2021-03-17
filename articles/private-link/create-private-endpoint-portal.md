---
title: Rychlý Start – vytvoření privátního koncového bodu pomocí Azure Portal
description: V tomto rychlém startu se dozvíte, jak vytvořit privátní koncový bod pomocí Azure Portal.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: allensu
ms.openlocfilehash: 395912ae70c5a01bd7de9a80cf8a507dd516028e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018065"
---
# <a name="quickstart-create-a-private-endpoint-using-the-azure-portal"></a>Rychlý Start: Vytvoření privátního koncového bodu pomocí Azure Portal

Začínáme s privátním koncovým bodem Azure pomocí privátního koncového bodu pro zabezpečené připojení k webové aplikaci Azure.

V tomto rychlém startu vytvoříte privátní koncový bod pro webovou aplikaci Azure a nasadíte virtuální počítač pro otestování privátního připojení.  

Pro různé druhy služeb Azure, jako je Azure SQL a Azure Storage, je možné vytvořit privátní koncové body.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Webovou aplikaci Azure s **PremiumV2** nebo vyšším plánem služby App Service nasazeným ve vašem předplatném Azure.  
    * Další informace a příklad najdete v tématu [rychlý Start: Vytvoření webové aplikace v ASP.NET Core v Azure](../app-service/quickstart-dotnetcore.md). 
    * Podrobný kurz týkající se vytvoření webové aplikace a koncového bodu najdete v tématu [kurz: připojení k webové aplikaci pomocí privátního koncového bodu Azure](tutorial-private-endpoint-webapp-portal.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-virtual-network-and-bastion-host"></a>Vytvoření virtuální sítě a hostitele bastionu

V této části vytvoříte virtuální síť, podsíť a hostitele bastionu. 

Hostitel bastionu se bude používat k zabezpečenému připojení k virtuálnímu počítači za účelem testování privátního koncového bodu.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek > Sítě > Virtuální síť** nebo do vyhledávacího pole zadejte **Virtuální síť**.

2. V části **vytvořit virtuální síť** zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Vyberte své předplatné Azure.                                  |
    | Skupina prostředků   | Vybrat **CreatePrivateEndpointQS-RG** |
    | **Podrobnosti o instancích** |                                                                 |
    | Name             | Zadejte **myVNet**                                    |
    | Oblast           | Vyberte **Západní Evropa**.|

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

1. V levé horní části portálu vyberte **vytvořit prostředek**  >  **Compute**  >  **virtuální počítač** COMPUTE nebo ve vyhledávacím poli vyhledejte **virtuální počítač** .
   
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **CreatePrivateEndpointQS-RG** |
    | **Podrobnosti o instancích** |  |
    | Název virtuálního počítače | Zadejte **myVM** |
    | Oblast | Vyberte **Západní Evropa**. |
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

## <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu

V této části vytvoříte privátní koncový bod pro webovou aplikaci, kterou jste vytvořili v části požadavky.

1. V levé horní části obrazovky na portálu vyberte vytvořit privátní síťové připojení **k prostředkům**  >  **Networking**  >  **Private Link** nebo zadejte do vyhledávacího pole **privátní odkaz**.

2. Vyberte **Vytvořit**.

3. V **centru privátních odkazů** vyberte v nabídce vlevo možnost **privátní koncové body** .

4. V **privátních koncových bodech** vyberte **+ Přidat**.

5. Na kartě **základy** pro **Vytvoření privátního koncového bodu** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **CreatePrivateEndpointQS-RG**. Tuto skupinu prostředků jste vytvořili v předchozí části.|
    | **Podrobnosti o instancích** |  |
    | Name  | Zadejte **myPrivateEndpoint**. |
    | Oblast | Vyberte **Západní Evropa**. |

6. Vyberte kartu **prostředek** nebo tlačítko **Další: prostředek** ve spodní části stránky.
    
7. V **prostředku** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Způsob připojení | **V adresáři vyberte připojit k prostředku Azure**. |
    | Předplatné | Vyberte své předplatné. |
    | Typ prostředku | Vyberte **Microsoft. Web/weby**. |
    | Prostředek | Vyberte **\<your-web-app-name>**. </br> Vyberte název webové aplikace, kterou jste vytvořili v části požadavky. |
    | Cílový dílčí prostředek | Vyberte **weby**. |

8. V dolní části obrazovky vyberte kartu **Konfigurace** nebo tlačítko **Další: Konfigurace** .

9. V **konfiguraci** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Sítě** |  |
    | Virtuální síť | Vyberte **myVNet**. |
    | Podsíť | Vyberte **mySubnet**. |
    | **Integrace Privátní DNS** |  |
    | Integrovat s privátní zónou DNS | Ponechte výchozí **hodnotu Ano**. |
    | Předplatné | Vyberte své předplatné. |
    | Privátní zóny DNS | Ponechte výchozí hodnotu **(New) privatelink.azurewebsites.NET**.
    

13. Vyberte **Zkontrolovat a vytvořit**.

14. Vyberte **Vytvořit**.

## <a name="test-connectivity-to-private-endpoint"></a>Test připojení k privátnímu koncovému bodu

V této části použijete virtuální počítač, který jste vytvořili v předchozím kroku, abyste se připojili k webové aplikaci v rámci privátního koncového bodu.

1. V levém navigačním podokně vyberte **skupiny prostředků** .

2. Vyberte **CreatePrivateEndpointQS-RG**.

3. Vyberte **myVM**.

4. Na stránce Přehled pro **myVM** vyberte **připojit** a pak **bastionu**.

5. Vyberte tlačítko modrého **použití bastionu** .

6. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

7. Po připojení otevřete Windows PowerShell na serveru.

8. Zadejte `nslookup <your-webapp-name>.azurewebsites.net`. Nahraďte **\<your-webapp-name>** názvem webové aplikace, kterou jste vytvořili v předchozích krocích.  Zobrazí se zpráva podobná tomu, co se zobrazuje níže:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Pro název webové aplikace se vrátí privátní IP adresa **10.1.0.5** .  Tato adresa je v podsíti virtuální sítě, kterou jste vytvořili dříve.

11. V připojení bastionu k **myVM** otevřete Internet Explorer.

12. Zadejte adresu URL vaší webové aplikace, **https:// \<your-webapp-name> . azurewebsites.NET**.

13. Pokud vaše aplikace nebyla nasazena, obdržíte výchozí stránku webové aplikace:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Výchozí stránka webové aplikace" border="true":::

18. Ukončete připojení k **myVM**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte virtuální síť, virtuální počítač a webovou aplikaci pomocí následujících kroků:

1. V nabídce na levé straně vyberte **skupiny prostředků**.

2. Vyberte **CreatePrivateEndpointQS-RG**.

3. Vyberte **Odstranit skupinu prostředků**.

4. Do **pole zadejte název skupiny prostředků** zadejte **CreatePrivateEndpointQS-RG** .

5. Vyberte **Odstranit**.


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili:

* Virtuální síť a hostitel bastionu
* Virtuální počítač.
* Soukromý koncový bod pro webovou aplikaci Azure.

Virtuální počítač jste použili k zabezpečenému otestování připojení k webové aplikaci v rámci privátního koncového bodu.



Další informace o službách, které podporují soukromý koncový bod, najdete v těchto tématech:
> [!div class="nextstepaction"]
> [Dostupnost privátního propojení](private-link-overview.md#availability)
