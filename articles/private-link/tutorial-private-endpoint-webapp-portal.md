---
title: 'Kurz: připojení k webové aplikaci pomocí privátního koncového bodu Azure'
titleSuffix: Azure Private Link
description: Začněte s tímto kurzem pomocí privátního koncového bodu Azure a připojte se k WebApp soukromě.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 10/19/2020
ms.openlocfilehash: 6d4d9fd901337b9c05c7d7d7f271974273e9fe37
ms.sourcegitcommit: 94ca9e89501e65f4dcccc3789249357c7d5e27e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92170061"
---
# <a name="tutorial-connect-to-a-web-app-using-an-azure-private-endpoint"></a>Kurz: připojení k webové aplikaci pomocí privátního koncového bodu Azure

Privátní koncový bod Azure je základním stavebním blokem privátního propojení v Azure. Umožňuje prostředkům Azure, jako jsou virtuální počítače, komunikovat soukromě s prostředky privátního propojení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte virtuální síť a hostitele bastionu.
> * Vytvoří virtuální počítač.
> * Vytvořte WebApp.
> * Vytvořte privátní koncový bod.
> * Otestujte připojení k privátnímu koncovému bodu webové aplikace.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!Note]
> Privátní koncový bod je k dispozici ve veřejných oblastech pro PremiumV2 webové aplikace PremiumV3 Windows, Linux Web Apps a plán služby Azure Functions Premium (někdy se mu říká plán elastické Premium). 

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network-and-bastion-host"></a>Vytvoření virtuální sítě a hostitele bastionu

V této části vytvoříte virtuální síť, podsíť a hostitele bastionu. 

Hostitel bastionu se bude používat k zabezpečenému připojení k virtuálnímu počítači za účelem testování privátního koncového bodu.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek > Sítě > Virtuální síť** nebo do vyhledávacího pole zadejte **Virtuální síť**.

2. V části **vytvořit virtuální síť**zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Vyberte své předplatné Azure.                                  |
    | Resource Group   | Vybrat **myResourceGroup** |
    | **Podrobnosti o instancích** |                                                                 |
    | Name             | Zadejte **myVNet**                                    |
    | Oblast           | Vyberte **východní USA** |

3. Vyberte kartu **IP adresy** nebo v dolní části stránky vyberte tlačítko **Další: IP adresy** .

4. Na kartě **IP adresy** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Adresní prostor protokolu IPv4 | Zadejte **10.1.0.0/16** |

5. V části **název podsítě**vyberte slovo **výchozí**.

6. V **Upravit podsíť**zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název podsítě | Zadejte **mySubnet** |
    | Rozsah adres podsítě | Zadejte **10.1.0.0/24** |

7. Vyberte **Uložit**.

8. Vyberte kartu **zabezpečení** .

9. V části **BastionHost**vyberte **Povolit**. Zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název bastionu | Zadejte **myBastionHost** |
    | Adresní prostor AzureBastionSubnet | Zadejte **10.1.1.0/24** |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Jako **název**zadejte **myBastionIP**. </br> Vyberte **OK**. |


8. Vyberte kartu **Revize + vytvořit** nebo vyberte tlačítko **Revize + vytvořit** .

9. Vyberte **Vytvořit**.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

V této části vytvoříte virtuální počítač, který se použije k otestování privátního koncového bodu.


1. V levé horní části portálu vyberte **vytvořit prostředek**  >  **Compute**  >  **virtuální počítač** COMPUTE nebo ve vyhledávacím poli vyhledejte **virtuální počítač** .
   
2. V části **vytvořit virtuální počítač**zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Resource Group | Vybrat **myResourceGroup** |
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

3. Vyberte kartu **síť** nebo vyberte **Další: disky**a **Další: síť**.
  
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

## <a name="create-web-app"></a>Vytvoření webové aplikace

V této části vytvoříte webovou aplikaci.

1. V nabídce na levé straně vyberte **vytvořit**  >  **Storage**  >  **webovou aplikaci**úložiště prostředků nebo ve vyhledávacím poli vyhledejte **webovou aplikaci** .

2. Na kartě **základy** v části **vytvořit webovou aplikaci** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Resource Group | Vybrat **myResourceGroup** |
    | **Podrobnosti o instancích** |  |
    | Name | Zadejte **MyWebApp**. Pokud není název k dispozici, zadejte jedinečný název. |
    | Publikovat | Vyberte **Kód**. |
    | Zásobník modulu runtime | Vyberte **.NET Core 3,1 (LTS)**. |
    | Operační systém | Vyberte možnost **Windows**. |
    | Oblast | Vyberte **východní USA** |
    | **Plán služby App Service** |  |
    | Plán Windows (Východní USA) | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Jako **název**zadejte **myServicePlan** . |
    | SKU a velikost | Vyberte **Změnit velikost**. </br> Na obrazovce **pro výběr specifikace** vyberte **P2V2** . </br> Vyberte **Použít**. |
   
3. Vyberte **Zkontrolovat a vytvořit**.

4. Vyberte **Vytvořit**.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/create-web-app.png" alt-text="Karta základy pro vytvoření webové aplikace v Azure Portal" border="true":::

## <a name="create-private-endpoint"></a>Vytvořit privátní koncový bod

1. V nabídce na levé straně vyberte **všechny prostředky**  >  **MyWebApp** nebo název, který jste zvolili při vytváření.

2. V přehledu webové aplikace vyberte **Nastavení**  >  **sítě**.

3. V **síťové síti**vyberte **Konfigurovat připojení privátních koncových bodů**.

4. Na obrazovce **připojení privátního koncového bodu** vyberte **+ Přidat** .

5. Na obrazovce **Přidat privátní koncový bod** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **mywebappendpoint**. |
    | Předplatné | Vyberte své předplatné. |
    | Virtuální síť | Vyberte **myVNet**. |
    | Podsíť | Vyberte **mySubnet**. |
    | Integrovat s privátní zónou DNS | Vyberte **Ano**. |

6. Vyberte **OK**.
    

## <a name="test-connectivity-to-private-endpoint"></a>Test připojení k privátnímu koncovému bodu

V této části použijete virtuální počítač, který jste vytvořili v předchozím kroku, abyste se připojili k webové aplikaci v rámci privátního koncového bodu.

1. V levém navigačním podokně vyberte **skupiny prostředků** .

2. Vyberte **myResourceGroup**.

3. Vyberte **myVM**.

4. Na stránce Přehled pro **myVM**vyberte **připojit** a pak **bastionu**.

5. Vyberte tlačítko modrého **použití bastionu** .

6. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

7. Po připojení otevřete Windows PowerShell na serveru.

8. Zadejte `nslookup <webapp-name>.azurewebsites.net`. Nahraďte **\<webapp-name>** názvem webové aplikace, kterou jste vytvořili v předchozích krocích.  Zobrazí se zpráva podobná tomu, co se zobrazuje níže:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Pro název webové aplikace se vrátí privátní IP adresa **10.1.0.5** .  Tato adresa je v podsíti virtuální sítě, kterou jste vytvořili dříve.

9. Otevřete webový prohlížeč na místním počítači a zadejte externí adresu URL vaší webové aplikace, **https:// \<webapp-name> . azurewebsites.NET**.

10. Ověřte, že se zobrazí stránka **403** . Tato stránka indikuje, že webová aplikace není dostupná externě.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-ext-403.png" alt-text="Karta základy pro vytvoření webové aplikace v Azure Portal" border="true":::

11. V připojení bastionu k **myVM**otevřete Internet Explorer.

12. Zadejte adresu URL vaší webové aplikace, **https:// \<webapp-name> . azurewebsites.NET**.

13. Ověřte, že obdržíte výchozí stránku webové aplikace.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-default-page.png" alt-text="Karta základy pro vytvoření webové aplikace v Azure Portal" border="true":::

18. Ukončete připojení k **myVM**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte virtuální síť, virtuální počítač a webovou aplikaci pomocí následujících kroků:

1. V nabídce na levé straně vyberte **skupiny prostředků**.

2. Vyberte **myResourceGroup**.

3. Vyberte **Odstranit skupinu prostředků**.

4. Do **pole zadejte název skupiny prostředků**zadejte **myResourceGroup** .

5. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Naučte se vytvořit službu privátního propojení:
> [!div class="nextstepaction"]
> [Vytvoření služby privátního propojení](create-private-link-service-portal.md)
