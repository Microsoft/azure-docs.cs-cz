---
title: Konfigurace nástroje pro vyrovnávání zatížení – pouze odchozí
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte, jak vytvořit interní nástroj pro vyrovnávání zatížení pomocí odchozího překladu adres (NAT).
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2020
ms.author: allensu
ms.openlocfilehash: ee264a22de5ce094e8a4c1335ace77cbbba49270
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94694989"
---
# <a name="outbound-only-load-balancer-configuration"></a>Konfigurace nástroje pro vyrovnávání zatížení – pouze odchozí

Použijte kombinaci interních a externích nástrojů pro vyrovnávání zatížení k vytvoření odchozího připojení pro virtuální počítače za interním nástrojem pro vyrovnávání zatížení. 

Tato konfigurace poskytuje odchozí překlad adres (NAT) pro interní scénář nástroje pro vyrovnávání zatížení, který pro váš back-end fond vytvoří nastavení pouze odchozí.


<p align="center">
  <img src="./media/egress-only/load-balancer-egress-only.png" alt="Figure depicts a egress only load balancer configuration." width="500" title="Virtual Network NAT">
</p>


<!-- 
:::image type="content" source="./media/egress-only/load-balancer-egress-only.png" alt-text="Figure depicts a egress only load balancer configuration" border="true":::
-->


*Obrázek: Konfigurace nástroje pro vyrovnávání zatížení jen pro výstup*

Požadované kroky:

1. Vytvořte virtuální síť s hostitelem bastionu.
2. Vytvořte virtuální počítač s pouze soukromou IP adresou.
3. Vytvořte interní i veřejné nástroje pro vyrovnávání zatížení.
4. Přidejte back-end fondy do obou nástrojů pro vyrovnávání zatížení a umístěte virtuální počítač do každého fondu.
5. Připojte se k VIRTUÁLNÍmu počítači přes hostitele bastionu a:
    1. Otestování odchozího připojení, 
    2. Nakonfigurujte odchozí pravidlo pro veřejný Nástroj pro vyrovnávání zatížení.
    3. Proveďte znovu test odchozího připojení.

## <a name="create-virtual-network-and-virtual-machine"></a>Vytvořit virtuální síť a virtuální počítač

Vytvořte virtuální síť se dvěma podsítěmi:

* Primární podsíť
* Bastionu podsíť

Vytvořte virtuální počítač v nové virtuální síti.

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

1. [Přihlaste se](https://portal.azure.com) na web Azure Portal.

2. V levém horním rohu obrazovky vyberte **Vytvořit prostředek > Sítě > Virtuální síť** nebo do vyhledávacího pole zadejte **Virtuální síť**.

2. V části **vytvořit virtuální síť** zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Vyberte své předplatné Azure.                                  |
    | Skupina prostředků   | Vyberte, že chcete **vytvořit novou** IP adresu. </br> Zadejte **myResourceGroupLB**. </br> Vyberte **OK**. |
    | **Podrobnosti o instancích** |                                                                 |
    | Name             | Zadejte **myVNet**                                    |
    | Oblast           | Vyberte **východní USA 2** |

3. Vyberte kartu **IP adresy** nebo v dolní části stránky vyberte tlačítko **Další: IP adresy** .

4. Na kartě **IP adresy** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Adresní prostor protokolu IPv4 | Zadejte **10.1.0.0/16** |

5. V části **název podsítě** vyberte slovo **výchozí**.

6. V **Upravit podsíť** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název podsítě | Zadejte **myBackendSubnet** |
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

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levé horní části portálu vyberte **vytvořit prostředek**  >    >  **virtuální počítač** Compute. 
   
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte hodnoty na kartě **základy** :

    | Nastavení | Hodnota                                          |
    |-----------------------|----------------------------------|
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné Azure. |
    | Skupina prostředků | Vybrat **myResourceGroupLB** |
    | **Podrobnosti o instancích** |  |
    | Název virtuálního počítače | Zadejte **myVM** |
    | Oblast | Vyberte **východní USA 2** |
    | Možnosti dostupnosti | Vyberte možnost **nepožaduje se žádná redundance infrastruktury** . |
    | Image | Vyberte **Windows Server 2019 Datacenter** |
    | Instance Azure Spot | Vybrat **ne** |
    | Velikost | Vyberte velikost virtuálního počítače nebo použijte výchozí nastavení. |
    | **Účet správce** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno. |
    | Heslo | Zadat heslo |
    | Potvrzení hesla | Znovu zadejte heslo. |
    | **Pravidla portů pro příchozí spojení** |  |
    | Veřejné příchozí porty | Vyberte možnost **Povolení vybraných portů** . |
    | Vyberte příchozí porty | Vybrat **RDP (3389)** |

3. Vyberte kartu **síť** nebo vyberte **Další: disky** a **Další: síť**.
  
4. Na kartě sítě vyberte nebo zadejte:

    | Nastavení | Hodnota |
    |-----|------------|
    | **Síťové rozhraní** |  |
    | Virtuální síť | **myVNet** |
    | Podsíť | **myBackendSubnet** |
    | Veřejná IP adresa | Vyberte **Žádná**. |
    | Skupina zabezpečení sítě NIC | Vybrat **žádné**|
    | Umístit tento virtuální počítač za stávající řešení vyrovnávání zatížení? | Vybrat **ne** |
   
5. Vyberte kartu **Správa** nebo vyberte možnost **Další**  >  **Správa**.

6. Na kartě **Správa** vyberte nebo zadejte:
    
    | Nastavení | Hodnota |
    |-|-|
    | **Monitorování** |  |
    | Diagnostika spouštění | Vybrat **vypnuto** |
   
7. Vyberte **Zkontrolovat a vytvořit**. 
  
8. Zkontrolujte nastavení a pak vyberte **vytvořit**.

## <a name="create-load-balancers-and-test-connectivity"></a>Vytvoření nástrojů pro vyrovnávání zatížení a testování připojení

K vytvoření použijte Azure Portal:

* Interní nástroj pro vyrovnávání zatížení
* Veřejný nástroj pro vyrovnávání zatížení
 
Přidejte vytvořený virtuální počítač do fondu back-end každého.  Pak nastavíte konfiguraci tak, aby povolovala pouze odchozí připojení z virtuálního počítače a testování před a po.

### <a name="create-internal-load-balancer"></a>Vytvořit interní nástroj pro vyrovnávání zatížení

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek**  >  **síť**  >  **Load Balancer**.

2. Na kartě **základy** na stránce **vytvořit nástroj pro vyrovnávání zatížení** zadejte nebo vyberte následující informace: 

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **myResourceGroupLB** vytvořené v předchozím kroku.|
    | Name                   | Zadejte **myInternalLoadBalancer**                                   |
    | Oblast         | Vyberte **USA – východ 2**.                                        |
    | Typ          | Vyberte **interní**.                                        |
    | SKU           | Vybrat **Standard** |
    | Virtuální síť | Vyberte **myVNet** vytvořené v předchozím kroku. |
    | Podsíť  | Vyberte **myBackendSubnet** vytvořené v předchozím kroku. |
    | Přiřazení IP adresy | Vyberte **Dynamická**. |

3. U zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.

4. Na kartě **Revize + vytvořit** vyberte **vytvořit**.   

### <a name="create-public-load-balancer"></a>Vytvořit veřejný Nástroj pro vyrovnávání zatížení

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek**  >  **síť**  >  **Load Balancer**.

2. Na kartě **základy** na stránce **vytvořit nástroj pro vyrovnávání zatížení** zadejte nebo vyberte následující informace: 

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **vytvořit nový** a do textového pole zadejte **myResourceGroupLB** .|
    | Name                   | Zadejte **myPublicLoadBalancer**                                   |
    | Oblast         | Vyberte **USA – východ 2**.                                        |
    | Typ          | Vyberte **Veřejný**.                                        |
    | SKU           | Vybrat **Standard** |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. |
    | Název veřejné IP adresy | Do textového pole zadejte **myFrontendIP** .|
    | Zóna dostupnosti | Vybrat **zónu – redundantní** |
    | Přidat veřejnou IPv6 adresu | Vyberte **Ne**. |

3. U zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.

4. Na kartě **Revize + vytvořit** vyberte **vytvořit**.   
   
### <a name="create-internal-backend-address-pool"></a>Vytvořit interní fond adres back-endu

Vytvořte **myInternalBackendPool** fond adres back-end:

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom v seznamu prostředků vyberte **myInternalLoadBalancer** .

2. V části **Nastavení** vyberte **back-end fondy** a pak vyberte **Přidat**.

3. Do pole název na stránce **Přidat fond back-end** serveru zadejte **myInternalBackendPool** jako název vašeho back-end fondu.
 
4. Ve **virtuální síti** vyberte **myVNet**.

5. V části **virtuální počítače** vyberte **Přidat** a zvolte **myVM**.
 
6. Vyberte **Přidat**.

### <a name="create-public-backend-address-pool"></a>Vytvořit veřejný fond adres back-endu

Vytvořte **myPublicBackendPool** fond adres back-end:

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom v seznamu prostředků vyberte **myPublicLoadBalancer** .

2. V části **Nastavení** vyberte **back-end fondy** a pak vyberte **Přidat**.

3. Do pole název na stránce **Přidat fond back-end** serveru zadejte **myPublicBackendPool** jako název vašeho back-end fondu.

4. Ve **virtuální síti** vyberte **myVNet**.
 
5. V části **virtuální počítače** vyberte **Přidat** a zvolte **myVM**.
 
6. Vyberte **Přidat**.

### <a name="test-connectivity-before-outbound-rule"></a>Před odchozím pravidlem testovat připojení

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom v seznamu prostředky vyberte **myVM** , která je umístěná ve skupině prostředků **myResourceGroupLB** .

2. Na stránce **Přehled** vyberte **připojit** a pak **bastionu**.

4. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

5. Vyberte **Connect** (Připojit).

6. Otevřete Internet Explorer.

7. **https://whatsmyip.org** Do adresního řádku zadejte.

8. Připojení by nemělo selhat. Standardní veřejný Nástroj pro vyrovnávání zatížení ve výchozím nastavení [neumožňuje odchozí přenosy bez definovaného odchozího pravidla](load-balancer-overview.md#securebydefault).
 
### <a name="create-a-public-load-balancer-outbound-rule"></a>Vytvoření odchozího pravidla veřejného nástroje pro vyrovnávání zatížení

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom v seznamu prostředků vyberte **myPublicLoadBalancer** .

2. V části **Nastavení** vyberte **odchozí pravidla** a pak vyberte **Přidat**.

3. Pomocí těchto hodnot nakonfigurujte odchozí pravidla:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **myOutboundRule**. |
    | IP adresa front-endu | Vyberte **LoadBalancerFrontEnd**.|
    | Časový limit nečinnosti (minuty) | Přesuňte posuvník na **15 minut**.|
    | Resetování protokolu TCP | Vyberte **Povoleno**.|
    | Back-endový fond | Vyberte **myPublicBackendPool**.| |
    | Přidělování portů-> přidělování portů | Vyberte **použít výchozí počet odchozích portů** . |

4. Vyberte **Přidat**.

### <a name="test-connectivity-after-outbound-rule"></a>Testovat připojení po odchozím pravidle

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom v seznamu prostředky vyberte **myVM** , která je umístěná ve skupině prostředků **myResourceGroupLB** .

2. Na stránce **Přehled** vyberte **připojit** a pak **bastionu**.

4. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

5. Vyberte **Connect** (Připojit).

6. Otevřete Internet Explorer.

7. **https://whatsmyip.org** Do adresního řádku zadejte.

8. Připojení by mělo být úspěšné.

9. Zobrazená IP adresa by měla být front-end IP adresa **myPublicLoadBalancer**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, nástroje pro vyrovnávání zatížení, virtuální počítač a všechny související prostředky. 

Provedete to tak, že vyberete skupinu prostředků **myResourceGroupLB** a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili konfiguraci pouze odchozích dat s kombinací veřejných a interních nástrojů pro vyrovnávání zatížení.  

Tato konfigurace umožňuje vyrovnávat zatížení příchozích interních přenosů do back-endu fondu a zároveň přitom bránit jakýmkoli veřejným příchozím připojením.

- Přečtěte si o [Azure Load Balancer](load-balancer-overview.md).
- Přečtěte si o [odchozích připojeních v Azure](load-balancer-outbound-connections.md).
- [Nejčastější dotazy](load-balancer-faqs.md)k nástroji pro vyrovnávání zatížení.
- Další informace o [Azure bastionu](../bastion/bastion-overview.md)