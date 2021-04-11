---
title: Filtrování síťového provozu – kurz – Azure Portal
titlesuffix: Azure Virtual Network
description: V tomto kurzu se naučíte filtrovat síťový provoz do podsítě s použitím skupiny zabezpečení sítě pomocí Azure Portal.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: kumud
ms.openlocfilehash: cfbb499c79761e1f2014c834e65dac35fe09ef90
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057282"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Kurz: filtrování síťového provozu pomocí skupiny zabezpečení sítě pomocí Azure Portal

Skupinu zabezpečení sítě můžete použít k filtrování příchozího a odchozího síťového provozu z podsítě virtuální sítě.

Skupiny zabezpečení sítě obsahují pravidla zabezpečení, která filtrují síťový provoz podle IP adresy, portu a protokolu. Pravidla zabezpečení se vztahují na prostředky nasazené v podsíti. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny zabezpečení sítě a pravidel zabezpečení
> * Vytvoření virtuální sítě a přidružení skupiny zabezpečení sítě k podsíti
> * Nasazení virtuálních počítačů do podsítě
> * Testování filtrů provozu

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. V levém horním rohu portálu vyberte **vytvořit prostředek** .

2. Do vyhledávacího pole zadejte **Virtual Network**. Ve výsledcích hledání vyberte **Virtual Network** .

3. Na stránce **Virtual Network** vyberte **vytvořit**.

4. V části **vytvořit virtuální síť** zadejte nebo vyberte tyto informace na kartě **základy** :

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** |   |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte, že chcete **vytvořit novou** IP adresu.  </br> Zadejte **myResourceGroup**. </br> Vyberte **OK**. |
    | **Podrobnosti o instancích** |   |
    | Name | Zadejte **myVNet**. |
    | Oblast | Vyberte **(US) východní USA**. |

5. Vyberte kartu **Revize + vytvořit** nebo v dolní části stránky vyberte tlačítko modrá **recenze + vytvořit** .

6. Vyberte **Vytvořit**.

## <a name="create-application-security-groups"></a>Vytvoření skupin zabezpečení aplikací

Skupina zabezpečení aplikací umožňuje seskupovat servery s podobnými funkcemi, například webové servery.

1. V levém horním rohu portálu vyberte **vytvořit prostředek** .

2. Do vyhledávacího pole zadejte **skupinu zabezpečení aplikace**. Ve výsledcích hledání vyberte **skupinu zabezpečení aplikace** .

3. Na stránce **Skupina zabezpečení aplikace** vyberte **vytvořit**.

4. V části **vytvořit skupinu zabezpečení aplikace** zadejte nebo vyberte tyto informace na kartě **základy** :

    | Nastavení | Hodnota |
    | ------- | ----- |
    |**Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. |
    | **Podrobnosti o instancích** |  |
    | Name | Zadejte **myAsgWebServers**. |
    | Oblast | Vyberte **(US) východní USA**. | 

5. Vyberte kartu **Revize + vytvořit** nebo v dolní části stránky vyberte tlačítko modrá **recenze + vytvořit** .

6. Vyberte **Vytvořit**.

7. Opakujte krok 4 a určete následující hodnoty:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |**Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. |
    | **Podrobnosti o instancích** |  |
    | Name | Zadejte **myAsgMgmtServers**. |
    | Oblast | Vyberte **(US) východní USA**. |

8. Vyberte kartu **Revize + vytvořit** nebo v dolní části stránky vyberte tlačítko modrá **recenze + vytvořit** .

9. Vyberte **Vytvořit**.

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Skupina zabezpečení sítě zabezpečuje síťový provoz ve vaší virtuální síti.

1. V levém horním rohu portálu vyberte **vytvořit prostředek** .

2. Do vyhledávacího pole zadejte **skupinu zabezpečení sítě**. Ve výsledcích hledání vyberte **skupinu zabezpečení sítě** .

3. Na stránce **Skupina zabezpečení sítě** vyberte **vytvořit**.

4. V části **vytvořit skupinu zabezpečení sítě** zadejte nebo vyberte tyto informace na kartě **základy** :

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** |   |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. |
    | **Podrobnosti o instancích** |   |
    | Name | Zadejte **myNSG**. |
    | Umístění | Vyberte **(US) východní USA**. | 

5. Vyberte kartu **Revize + vytvořit** nebo v dolní části stránky vyberte tlačítko modrá **recenze + vytvořit** .

6. Vyberte **Vytvořit**.

## <a name="associate-network-security-group-to-subnet"></a>Přidružení skupiny zabezpečení sítě k podsíti

V této části přidružíme skupinu zabezpečení sítě k podsíti virtuální sítě, kterou jsme vytvořili dříve.

1. Do pole **Hledat prostředky, služby a dokumenty** v horní části portálu začněte psát **myNsg**. Jakmile se ve výsledcích hledání zobrazí položka **myNsg**, vyberte ji.

2. Na stránce Přehled v **myNSG** vyberte **podsítě** v **Nastavení**.

3. Na stránce **Nastavení** vyberte **přidružit**:

    :::image type="content" source="./media/tutorial-filter-network-traffic/associate-nsg-subnet.png" alt-text="Přidružte NSG k podsíti." border="true":::

3. V části **přidružit podsíť** vyberte **virtuální síť** a pak vyberte **myVNet**. 

4. Vyberte **podsíť**, vyberte **výchozí** a pak vyberte **OK**.

## <a name="create-security-rules"></a>Vytvoření pravidel zabezpečení

1. V **Nastavení** **myNSG** vyberte **příchozí pravidla zabezpečení**.

2. V **pravidla zabezpečení příchozí** vyberte **+ Přidat**:

    :::image type="content" source="./media/tutorial-filter-network-traffic/add-inbound-rule.png" alt-text="Přidat příchozí pravidlo zabezpečení" border="true":::

3. Vytvořte pravidlo zabezpečení, které skupině zabezpečení aplikace **myAsgWebServers** povolí porty 80 a 443. V části **Přidat příchozí pravidlo zabezpečení** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Zdroj | Nechte výchozí hodnotu **Any**. |
    | Rozsahy zdrojových portů | Ponechte výchozí hodnotu **(*)** . |
    | Cíl | Vyberte **Skupina zabezpečení aplikace**. |
    | Cílová skupina zabezpečení aplikace | Vyberte **myAsgWebServers**. |
    | Služba | Ponechte výchozí hodnotu **Custom (vlastní**). |
    | Rozsahy cílových portů | Zadejte **80 443**. |
    | Protokol | Vyberte **TCP**. |
    | Akce | Ponechte výchozí hodnotu **Allow**. |
    | Priorita | Ponechte výchozí hodnotu **100**. |
    | Name | Zadejte možnost **Allow-web-All**. |

    :::image type="content" source="./media/tutorial-filter-network-traffic/inbound-security-rule.png" alt-text="Příchozí pravidlo zabezpečení" border="true":::

3. Zopakujte 2. krok s použitím následujících hodnot:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Zdroj | Nechte výchozí hodnotu **Any**. |
    | Rozsahy zdrojových portů | Ponechte výchozí hodnotu **(*)** . |
    | Cíl | Vyberte **Skupina zabezpečení aplikace**. |
    | Cílová skupina zabezpečení aplikace | Vyberte **myAsgMgmtServers**. |
    | Služba | Ponechte výchozí hodnotu **Custom (vlastní**). |
    | Rozsahy cílových portů | Zadejte **3389**. |
    | Protokol | Vyberte **TCP**. |
    | Akce | Ponechte výchozí hodnotu **Allow**. |
    | Priorita | Ponechte výchozí hodnotu **110**. |
    | Name | Zadejte **Allow-RDP-All**. |

    > [!CAUTION]
    > V tomto článku je protokol RDP (port 3389) zpřístupněný pro Internet pro virtuální počítač, který je přiřazený ke skupině zabezpečení aplikací **myAsgMgmtServers** . 
    >
    > V produkčních prostředích místo vystavení portu 3389 na Internet doporučujeme připojit se k prostředkům Azure, které chcete spravovat, pomocí sítě VPN, připojení k privátní síti nebo Azure bastionu.
    >
    > Další informace o Azure bastionu najdete v tématu [co je Azure bastionu?](../bastion/bastion-overview.md).

Po dokončení kroků 1–3 zkontrolujte vytvořená pravidla. Seznam by měl vypadat jako v následujícím příkladu jako v seznamu:

:::image type="content" source="./media/tutorial-filter-network-traffic/security-rules.png" alt-text="Pravidla zabezpečení." border="true":::

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače.

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

1. V levém horním rohu portálu vyberte **vytvořit prostředek** .

2. Vyberte **COMPUTE** a potom vyberte **virtuální počítač**.

3. V části **vytvořit virtuální počítač** zadejte nebo vyberte tyto informace na kartě **základy** :

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** |  |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. |
    | **Podrobnosti o instancích** |   |
    | Název virtuálního počítače | Zadejte **myVMWeb**. |
    | Oblast | Vyberte **(US) východní USA**. |
    | Možnosti dostupnosti | Nechte výchozí nastavení bez nutnosti redundance. |
    | Image | Vyberte **Windows Server 2019 Datacenter – Gen1**. |
    | Instance Azure Spot | Nechte výchozí nastavení nezaškrtnuto. |
    | Velikost | Vyberte **Standard_D2s_V3**. |
    | **Účet správce** |   |
    | Uživatelské jméno | Zadejte uživatelské jméno. |
    | Heslo | Zadejte heslo. |
    | Potvrzení hesla | Zadejte znovu heslo. |
    | **Pravidla portů pro příchozí spojení** |   |
    | Veřejné příchozí porty | Vyberte **Žádná**. |

4. Vyberte kartu **Sítě**.

5. Na kartě **sítě** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Síťové rozhraní** |   |
    | Virtuální síť | Vyberte **myVNet**. |
    | Podsíť | Vyberte **výchozí (10.0.0.0/24)**. |
    | Veřejná IP adresa | Ponechte výchozí nastavení nové veřejné IP adresy. |
    | Skupina zabezpečení sítě NIC | Vyberte **Žádná**. | 

6. Vyberte kartu **Revize + vytvořit** nebo v dolní části stránky vyberte tlačítko modrá **recenze + vytvořit** .

7. Vyberte **Vytvořit**.

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

Proveďte kroky 1-7 znovu, ale v kroku 3 pojmenujte virtuální počítač **myVMMgmt**. Nasazení virtuálního počítače trvá několik minut. 

Nepokračujte k dalšímu kroku, dokud není nasazený virtuální počítač.

## <a name="associate-network-interfaces-to-an-asg"></a>Přidružení síťových rozhraní ke skupině zabezpečení aplikace

Když portál vytvoří virtuální počítače, vytvoří pro každý z nich síťové rozhraní, které připojí k virtuálnímu počítači. 

Síťové rozhraní každého virtuálního počítače přidejte k jedné ze dříve vytvořených skupin zabezpečení aplikace:

1. V poli **Hledat prostředky, služby a dokumenty** v horní části portálu začněte zadávat **myVMWeb**. Jakmile se ve výsledcích hledání zobrazí virtuální počítač **myVMWeb** , vyberte ho.

2. V **Nastavení** vyberte **sítě**.  

3. Vyberte kartu **skupiny zabezpečení aplikace** a pak vyberte **Konfigurovat skupiny zabezpečení aplikací**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/configure-app-sec-groups.png" alt-text="Nakonfigurujte skupiny zabezpečení aplikací." border="true":::

4. V **části konfigurovat skupiny zabezpečení aplikace** vyberte **myAsgWebServers**. Vyberte **Uložit**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/select-asgs.png" alt-text="Vyberte skupiny zabezpečení aplikace." border="true":::

5. Opakujte kroky 1 a 2 a vyhledejte virtuální počítač **myVMMgmt** a vyberte  **myAsgMgmtServers** ASG.

## <a name="test-traffic-filters"></a>Testování filtrů provozu

1. Připojte se k virtuálnímu počítači **myVMMgmt** . Do vyhledávacího pole v horní části portálu zadejte **myVMMgmt** . Pokud se ve výsledcích hledání zobrazí **myVMMgmt** , vyberte ji. Klikněte na tlačítko **Připojit**.

2. Vyberte **Stáhnout soubor RDP**.

3. Otevřete stažený soubor RDP a vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

4. Vyberte **OK**.

5. Během procesu připojování se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění, vyberte **Ano** nebo **pokračovat** a pokračujte v připojování.

    Připojení je úspěšné, protože port 3389 je povolený pro příchozí připojení z Internetu do skupiny zabezpečení aplikací **myAsgMgmtServers** . 
    
    Síťové rozhraní pro **myVMMgmt** je přidruženo ke skupině zabezpečení aplikace **myAsgMgmtServers** a umožňuje připojení.

6. Otevřete relaci PowerShellu v **myVMMgmt**. Připojte se k **myVMWeb** pomocí následujícího příkladu: 

    ```powershell
    mstsc /v:myVmWeb
    ```

    Připojení RDP z **myVMMgmt** do **myVMWeb** je úspěšné, protože virtuální počítače ve stejné síti můžou ve výchozím nastavení komunikovat s každým z libovolných portů.
    
    Nemůžete vytvořit připojení RDP k virtuálnímu počítači s **myVMWeb** z Internetu. Pravidlo zabezpečení pro **myAsgWebServers** zabraňuje připojením k portu 3389 příchozímu z Internetu. Pro všechny prostředky ve výchozím nastavení je povolený příchozí provoz z Internetu.

7. Pokud chcete nainstalovat Microsoft IIS na virtuálním počítači s **myVMWeb** , zadejte následující příkaz z relace PowerShellu na virtuálním počítači **myVMWeb** :

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. Po dokončení instalace služby IIS se odpojte z virtuálního počítače s **myVMWeb** , který vám ponechá připojení ke vzdálené ploše virtuálního počítače **myVMMgmt** .

9. Odpojte se od virtuálního počítače **myVMMgmt** .

10. V poli **Hledat prostředky, služby a dokumenty** v horní části Azure Portal začněte psát **myVMWeb** z počítače. Pokud se ve výsledcích hledání zobrazí **myVMWeb** , vyberte ji. Poznamenejte si **veřejnou IP adresu** svého virtuálního počítače. Adresa zobrazená v následujícím příkladu je 23.96.39.113, ale vaše adresa je odlišná:

    :::image type="content" source="./media/tutorial-filter-network-traffic/public-ip-address.png" alt-text="Veřejná IP adresa." border="true":::
    
11. Pokud chcete potvrdit, že máte přístup k webovému serveru **myVMWeb** z Internetu, otevřete internetový prohlížeč na počítači a přejděte na adresu `http://<public-ip-address-from-previous-step>` . 

Zobrazí se uvítací obrazovka služby IIS, protože port 80 je povolený pro příchozí připojení z Internetu do skupiny zabezpečení aplikací **myAsgWebServers** . 

Síťové rozhraní připojené pro **myVMWeb** je přidruženo ke skupině zabezpečení aplikace **myAsgWebServers** a umožňuje připojení. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do pole **Hledat** v horní části portálu zadejte **myResourceGroup**. Jakmile se ve výsledcích hledání zobrazí skupina prostředků **myResourceGroup**, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. V části **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** zadejte **myResourceGroup** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

* Byla vytvořena skupina zabezpečení sítě a přidružená k podsíti virtuální sítě. 
* Byly vytvořeny skupiny zabezpečení aplikace pro web a správu.
* Vytvořili jste dva virtuální počítače.
* Byl testován filtr sítě skupiny zabezpečení aplikace.

Další informace o skupinách zabezpečení sítě najdete v tématech [Přehled skupin zabezpečení sítě](./network-security-groups-overview.md) a [Správa skupiny zabezpečení sítě](manage-network-security-group.md).

Provoz mezi podsítěmi směruje ve výchozím nastavení Azure. Místo toho se můžete rozhodnout směrovat provoz mezi podsítěmi například prostřednictvím virtuálního počítače, který slouží jako brána firewall. 

Pokud chcete zjistit, jak vytvořit směrovací tabulku, pokračujte k dalšímu kurzu.
> [!div class="nextstepaction"]
> [Vytvoření směrovací tabulky](./tutorial-create-route-table-portal.md)