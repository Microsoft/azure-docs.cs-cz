---
title: 'Kurz: Vytvoření nástroje pro vyrovnávání zatížení mezi oblastmi pomocí Azure Portal'
titleSuffix: Azure Load Balancer
description: Začněte s tímto kurzem nasazení Azure Load Balancer mezi oblastmi s Azure Portal.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 11/24/2020
ms.openlocfilehash: fe335b00df23ff132ad61b27efd733d195ee3bfb
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96031466"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-the-azure-portal"></a>Kurz: vytvoření Azure Load Balancer mezi oblastmi pomocí Azure Portal

Nástroj pro vyrovnávání zatížení mezi oblastmi zajišťuje globálně dostupnou službu napříč několika oblastmi Azure. Pokud dojde k výpadku jedné oblasti, provoz se směruje na další nejbližší zdravý Nástroj pro vyrovnávání zatížení.  

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte Nástroj pro vyrovnávání zatížení mezi oblastmi.
> * Vytvořte back-end fond, který obsahuje dvě místní nástroje pro vyrovnávání zatížení.
> * Vytvořte pravidlo nástroje pro vyrovnávání zatížení.
> * Otestujte Nástroj pro vyrovnávání zatížení.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure.
- Dvě **standardní** SKU služby Vyrovnávání zatížení Azure s back-end fondy nasazenými ve dvou různých oblastech Azure.
    - Informace o tom, jak vytvořit místní standardní nástroj pro vyrovnávání zatížení a virtuální počítače pro back-endové fondy, najdete v tématu [rychlý Start: vytvoření veřejného nástroje pro vyrovnávání zatížení virtuálních počítačů pomocí Azure Portal](quickstart-load-balancer-standard-public-portal.md).
        - Přidejte do každé oblasti název služby Vyrovnávání zatížení, virtuálních počítačů a dalších prostředků v každé oblasti s přepínači **-R1** a **-R2**. 

## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal

> [!IMPORTANT]
> Nástroj pro vyrovnávání zatížení mezi oblastmi je momentálně ve verzi Preview a skrytý na portálu Preview.  Přihlaste se, abyste **https://preview.portal.azure.com/?feature.globallb=true** mohli tuto funkci Zobrazit a nasadit.

## <a name="create-cross-region-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení mezi oblastmi

V této části vytvoříte Nástroj pro vyrovnávání zatížení mezi oblastmi a veřejnou IP adresu.

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek > síťové > Load Balancer** nebo vyhledejte **Load Balancer** do vyhledávacího pole.

2. Na kartě **základy** na stránce **vytvořit nástroj pro vyrovnávání zatížení** zadejte nebo vyberte následující informace: 

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **vytvořit novou** a do textového pole zadejte **CreateCRLBTutorial-RG** .|
    | Name                   | Zadejte **myLoadBalancer-CR** .                                   |
    | Oblast         | Vyberte **západní USA**.                                        |
    | Typ          | Vyberte **Veřejný**.                                        |
    | SKU           | Vybrat **Standard** |
    | Úroveň           | Vybrat **globální** |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu.|
    | Název veřejné IP adresy | Do textového pole zadejte **myPublicIP-CR** .|
    | Předvolba směrování| Vybrat **síť Microsoft** |

    > [!NOTE]
    > Nástroj pro vyrovnávání zatížení mezi oblastmi se dá nasadit jenom v těchto domácích oblastech: **východní USA 2, západní USA, západní Evropa, jihovýchodní Asie, střed USA, Severní Evropa, východní Asie**. Další informace najdete na webu **https://aka.ms/homeregionforglb**.


3. U zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.

4. Na kartě **Revize + vytvořit** vyberte **vytvořit**.   

    :::image type="content" source="./media/tutorial-cross-region-portal/create-cross-region.png" alt-text="Vytvoření nástroje pro vyrovnávání zatížení mezi oblastmi" border="true":::

## <a name="create-backend-pool"></a>Vytvoření back-endového fondu

V této části přidáte do back-endu nástroje pro vyrovnávání zatížení mezi oblastmi dva místní standardní nástroje pro vyrovnávání zatížení.

> [!IMPORTANT]
> Pokud chcete tento postup provést, ujistěte se, že ve vašem předplatném je nasazené dvě místní nástroje pro vyrovnávání zatížení s back-end fondy.  Další informace najdete v tématu **[rychlý Start: vytvoření veřejného nástroje pro vyrovnávání zatížení virtuálních počítačů pomocí Azure Portal](quickstart-load-balancer-standard-public-portal.md)**.

Vytvořte back-end fond adres **myBackendPool-CR** , který bude zahrnovat místní standardní nástroje pro vyrovnávání zatížení.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a v seznamu prostředků vyberte **myLoadBalancer-CR** .

2. V části **Nastavení** vyberte **back-end fondy** a pak vyberte **Přidat**.

3. Na stránce **Přidat fond back-end** serveru zadejte do pole název **myBackendPool-CR**.

4. Vyberte **Přidat**.

4. Vyberte **myBackendPool-CR**.

5. V části nástroje pro vyrovnávání **zatížení** vyberte v části **Nástroj pro vyrovnávání zatížení** políčko pro stažení.

5. Vyberte **myLoadBalancer-R1** nebo název svého nástroje pro vyrovnávání zatížení v oblasti 1.

6. Zaškrtněte políčko pro stažení v části **Konfigurace protokolu IP front-endu**. Vyberte **LoadBalancerFrontEnd**.

7. Opakováním kroků 4-6 přidejte **myLoadBalancer-R2**.

8. Vyberte **Přidat**.

    :::image type="content" source="./media/tutorial-cross-region-portal/add-to-backendpool.png" alt-text="Přidání regionálních nástrojů pro vyrovnávání zatížení do problémových" border="true":::

## <a name="create-a-health-probe"></a>Vytvoření sondy stavu

V této části vytvoříte sondu stavu pro vytvoření pravidla vyrovnávání zatížení:

* S názvem **myHealthProbe**.
* Protokol **TCP**.
* Interval po dobu **5** sekund.
* Prahová hodnota špatného stavu **dvou** selhání.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a v seznamu prostředků vyberte **myLoadBalancer-CR** .

2. V oblasti **Nastavení** vyberte **Sondy stavu**.

3. Pomocí těchto hodnot můžete nakonfigurovat sondu stavu:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **myHealthProbe**. |
    | Protokol | Vyberte **TCP**. |
    | Port | Zadejte **80**. |
    | Interval | Zadejte **5**. |
    | Prahová hodnota pro poškozený stav | Zadejte **2**. |

4. Vyberte **OK**.

    > [!NOTE]
    > Nástroj pro vyrovnávání zatížení pro různé oblasti má vestavěnou sondu stavu. Tento test je zástupný symbol pro fungování vytvoření pravidla vyrovnávání zatížení.  Další informace najdete v tématu **[omezení nástroje pro vyrovnávání zatížení mezi oblastmi](cross-region-overview.md#limitations)**.

## <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

V této části vytvoříte pravidlo nástroje pro vyrovnávání zatížení:

* S názvem **myHTTPRule**.
* Ve front-endu s názvem **LoadBalancerFrontEnd**.
* Naslouchá na **portu 80**.
* Směruje provoz s vyrovnáváním zatížení do back-endu s názvem **myBackendPool-CR** na **portu 80**.

    > [!NOTE]
    > Port front-endu musí odpovídat back-endovému portu a portu front-endu regionálních nástrojů pro vyrovnávání zatížení ve fondu back-endu.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a v seznamu prostředků vyberte **myLoadBalancer-CR** .

2. V části **Nastavení** vyberte **pravidla vyrovnávání zatížení** a pak vyberte **Přidat**.

3. Pomocí těchto hodnot můžete nakonfigurovat pravidlo vyrovnávání zatížení:
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **myHTTPRule**. |
    | Verze protokolu IP | Vybrat **IPv4** |
    | IP adresa front-endu | Vybrat **LoadBalancerFrontEnd** |
    | Protokol | Vyberte **TCP**. |
    | Port | Zadejte **80**.|
    | Back-endový port | Zadejte **80**. |
    | Back-endový fond | Vyberte **myBackendPool**.|
    | Sonda stavu | Vyberte **myHealthProbe**. |
    | Časový limit nečinnosti (minuty) | Přesuňte posuvník na **15**. |
    | Resetování protokolu TCP | Vyberte **Povoleno**. |

4. Ponechte zbytek výchozích hodnot a pak vyberte **OK**.

    :::image type="content" source="./media/tutorial-cross-region-portal/create-lb-rule.png" alt-text="Vytvořit pravidlo nástroje pro vyrovnávání zatížení" border="true":::

## <a name="test-the-load-balancer"></a>Testování Load Balanceru

V této části otestujete Nástroj pro vyrovnávání zatížení mezi oblastmi. Připojíte se k veřejné IP adrese ve webovém prohlížeči.  Virtuální počítače zabráníte v jednom z back-endového fondu služby pro vyrovnávání zatížení a budete sledovat převzetí služeb při selhání.

1. Vyhledejte veřejnou IP adresu nástroje pro vyrovnávání zatížení na obrazovce **Přehled** . V nabídce na levé straně vyberte **všechny služby** a vyberte **všechny prostředky** a pak vyberte **myPublicIP-CR**.

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-1.png" alt-text="Test nástroje pro vyrovnávání zatížení" border="true":::

3. Zastavte virtuální počítače v back-end fondu jednoho z regionálních nástrojů pro vyrovnávání zatížení.

4. Aktualizujte webový prohlížeč a sledujte převzetí služeb při selhání připojení k druhému místnímu nástroji pro vyrovnávání zatížení.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-2.png" alt-text="Test vyrovnávání zátěže po převzetí služeb při selhání" border="true":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, nástroj pro vyrovnávání zatížení a všechny související prostředky. 

Provedete to tak, že vyberete skupinu prostředků **CreateCRLBTutorial-RG** , která obsahuje prostředky, a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

* Vytvořil se nástroj pro vyrovnávání zatížení mezi oblastmi.
* Přidání místních nástrojů pro vyrovnávání zatížení do back-endu pro nástroj pro vyrovnávání zatížení mezi oblastmi.
* Bylo vytvořeno pravidlo vyrovnávání zatížení.
* Otestování nástroje pro vyrovnávání zatížení.

Další informace o nástroji pro vyrovnávání zatížení mezi oblastmi najdete v tématu [Nástroj pro vyrovnávání zatížení mezi oblastmi (Preview)](cross-region-overview.md).


Přejděte k dalšímu článku, kde se dozvíte, jak:
> [!div class="nextstepaction"]
> [Vyrovnávání zatížení virtuálních počítačů napříč zónami dostupnosti](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
