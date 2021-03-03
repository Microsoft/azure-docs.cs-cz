---
title: 'Kurz: Vytvoření brány NAT – Azure Portal'
titlesuffix: Azure Virtual Network NAT
description: V tomto rychlém startu se dozvíte, jak vytvořit bránu NAT pomocí Azure Portal
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 98989a38a0c1a7f57fc8eba70e064e5d6f2cce8f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671528"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>Kurz: Vytvoření brány NAT pomocí Azure Portal

V tomto kurzu se dozvíte, jak používat službu Azure Virtual Network NAT. Vytvoříte bránu NAT pro poskytování odchozího připojení pro virtuální počítač v Azure. 

Pokud chcete, můžete tento postup provést pomocí [Azure CLI](quickstart-create-nat-gateway-cli.md), [Azure PowerShell](quickstart-create-nat-gateway-powershell.md)nebo nasadit [šablonu ARM](quickstart-create-nat-gateway-powershell.md) namísto portálu.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

Než nasadíte virtuální počítač a můžete použít bránu NAT, musíme vytvořit skupinu prostředků a virtuální síť.

V této části budete muset v krocích níže nahradit následující parametry:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | USA – východ 2      |
| **\<IPv4-address-space>**   | 192.168.0.0/16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-vm-to-use-the-nat-gateway"></a>Vytvoření virtuálního počítače pro použití brány NAT

Nyní vytvoříme virtuální počítač pro použití služby NAT. Tento virtuální počítač má veřejnou IP adresu, která se používá jako veřejná IP adresa na úrovni instance, která umožňuje přístup k virtuálnímu počítači. Služba NAT má na vědomí směr toku a nahradí výchozí internetový cíl ve vaší podsíti. Veřejná IP adresa virtuálního počítače se nebude používat pro odchozí připojení.

1. V levém horním rohu portálu vyberte **vytvořit Resource**  >  **COMPUTE**  >  **Ubuntu Server 18,04 LTS** nebo vyhledejte **Ubuntu Server 18,04 LTS** v hledání na webu Marketplace.

2. V části **vytvořit virtuální počítač** zadejte nebo vyberte následující hodnoty na kartě **základy** :
   - **Předplatné**  >  **Skupina prostředků**: vyberte **myResourceGroupNAT**.
   - **Podrobnosti instance**  >  **Název virtuálního počítače**: zadejte **myVM**.
   - **Podrobnosti instance**  >  **Oblast** > vyberte **východní USA 2**.
   - **Účet správce**  >  **Typ ověřování**: vyberte **heslo**.
   - **Účet správce** > zadejte **uživatelské jméno**, **heslo** a potvrzení informací o **hesle** .
   - Pravidla portů pro **příchozí spojení**  >  **Veřejné příchozí porty**: vyberte **Povolit vybrané porty**.
   - Pravidla portů pro **příchozí spojení**  >  **Vyberte příchozí porty**: vybrat **SSH (22)**
   - Vyberte kartu **síť** nebo vyberte **Další: disky** a **Další: síť**.

3. Na kartě **sítě** zkontrolujte, že jsou vybrané následující:
   - **Virtuální síť**: **myVnet**
   - **Podsíť**: **mySubnet**
   - **Veřejná IP adresa** > vyberte **vytvořit novou**.  V okně **vytvořit veřejnou IP adresu** do pole **název** zadejte **MyPublicIPVM** a jako **SKU** vyberte **Standard** .  Klikněte na **OK**.
   - **Skupina zabezpečení sítě síťové karty**: vyberte **základní**.
   - **Veřejné příchozí porty**: vyberte **Povolit vybrané porty**.
   - **Vyberte příchozí porty**: potvrďte, že je zaškrtnuté políčko Potvrdit **SSH** .

4. Na kartě **Správa** v části **monitorování** nastavte **diagnostiku spouštění** na **vypnuto**.

5. Vyberte **Zkontrolovat a vytvořit**. 

6. Zkontrolujte nastavení a klikněte na **vytvořit**.

## <a name="create-the-nat-gateway"></a>Vytvoření brány NAT

Můžete použít jeden nebo více prostředků veřejné IP adresy, předpony veřejných IP adres nebo obojí. Přidáme prostředek veřejné IP adresy, předponu veřejné IP adresy a prostředek brány NAT.

Tato část podrobně popisuje, jak můžete vytvořit a nakonfigurovat následující součásti služby NAT pomocí prostředku brány NAT:
  - Veřejný fond IP adres a předponu veřejných IP adres, které se mají použít pro odchozí toky přeložené prostředkem brány NAT.
  - Změňte časový limit nečinnosti z výchozí hodnoty 4 minuty na 10 minut.

### <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

1. V levém horním rohu portálu vyberte **vytvořit**  >    >  **veřejnou IP adresu** sítě prostředku nebo v hledání na webu Marketplace vyhledejte **veřejnou IP adresu** .

2. V nástroji **vytvořit veřejnou IP adresu** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Verze protokolu IP | Vyberte **IPv4**.
    | SKU | Vyberte **Standard**.
    | Název | Zadejte **myPublicIP**. |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **myResourceGroupNAT**. |
    | Umístění | Vyberte **USA – východ 2**.|

3. Ponechte zbytek výchozích hodnot a vyberte **vytvořit**.

### <a name="create-a-public-ip-prefix"></a>Vytvoření předpony veřejné IP adresy

1. V levém horním rohu portálu vyberte **vytvořit**  >    >  **předponu veřejné IP adresy** sítě prostředku nebo v hledání na webu Marketplace vyhledejte **předponu veřejné IP adresy** . 

2. V části **vytvořit předponu veřejné IP adresy** zadejte nebo vyberte následující hodnoty na kartě **základy** :
   - **Předplatné**  >  **Skupina prostředků**: vyberte **myResourceGroupNAT**>
   - **Podrobnosti instance**  >  **Název**: zadejte **myPublicIPprefix**.
   - **Podrobnosti instance**  >  **Oblast**: vyberte **východní USA 2**.
   - **Podrobnosti instance**  >  **Velikost předpony**: vyberte **/31 (2 adresy)**

3. Ponechte zbytek výchozí hodnoty a vyberte **zkontrolovat + vytvořit**.

4. Zkontrolujte nastavení a pak vyberte **vytvořit**.
   

### <a name="create-a-nat-gateway-resource"></a>Vytvoření prostředku brány NAT

1. V levém horním rohu portálu vyberte **vytvořit prostředek**  >  **síťové** služby  >  **NAT Gateway** nebo vyhledejte **bránu NAT** v hledání na webu Marketplace.

2. V části **vytvořit bránu pro překlad síťových adres (NAT)** zadejte nebo vyberte na kartě **základy** následující hodnoty:
   - **Předplatné**  >  **Skupina prostředků**: vyberte **myResourceGroupNAT**.
   - **Podrobnosti instance**  >  **Název brány NAT**: zadejte **myNATgateway**.
   - **Podrobnosti instance**  >  **Oblast**: vyberte **východní USA 2**.
   - **Podrobnosti instance**  >  **Časový limit nečinnosti (minuty)**: typ **10**.
   - Vyberte kartu **Veřejná IP adresa** nebo vyberte **Další: veřejná IP adresa**.

3. Na kartě **Veřejná IP adresa** zadejte nebo vyberte následující hodnoty:
   - **Veřejné IP adresy**: vyberte **myPublicIP**.
   - **Předpony veřejných IP adres**: vyberte **myPublicIPprefix**.
   - Vyberte kartu **podsíť** nebo vyberte **Další: podsíť**.

4. Na kartě **podsíť** zadejte nebo vyberte následující hodnoty:
   - **Virtual Network**: vyberte **myResourceGroupNAT**  >  **myVnet**.
   - **Název podsítě**: zaškrtněte políčko vedle **mySubnet**.

5. Vyberte **Zkontrolovat a vytvořit**.

6. Zkontrolujte nastavení a pak vyberte **vytvořit**.

## <a name="discover-the-ip-address-of-the-vm"></a>Zjištění IP adresy virtuálního počítače

1. Na levé straně portálu vyberte **skupiny prostředků**.
2. Vyberte **myResourceGroupNAT**.
3. Vyberte **myVM**.
4. V části **Přehled** Zkopírujte hodnotu **Veřejná IP adresa** a vložte ji do poznámkového bloku, abyste ji mohli použít pro přístup k virtuálnímu počítači.

>[!IMPORTANT]
>Zkopírujte veřejnou IP adresu a vložte ji do poznámkového bloku, abyste ji mohli použít pro přístup k virtuálnímu počítači.

## <a name="sign-in-to-vm"></a>Přihlášení k virtuálnímu počítači

Otevřete [Azure Cloud Shell](https://shell.azure.com) v prohlížeči. Pro SSH k virtuálnímu počítači použijte IP adresu získanou v předchozím kroku.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Nyní jste připraveni použít službu překladu adres (NAT).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, bránu NAT a všechny související prostředky. Vyberte skupinu prostředků **myResourceGroupNAT** , která obsahuje bránu NAT, a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili bránu NAT a virtuální počítač pro jeho použití. 

Zkontrolujte metriky v Azure Monitor a podívejte se, jak vaše služba NAT funguje. Diagnostikujte problémy, jako je například vyčerpání prostředků dostupných portů SNAT.  Vyčerpání prostředků SNAT se řeší přidáním dalších prostředků veřejné IP adresy nebo prostředků předpony veřejných IP adres nebo obou.


- Přečtěte si o [službě Azure Virtual Network NAT](./nat-overview.md)
- Přečtěte si o [prostředku brány NAT](./nat-gateway-resource.md).
- Rychlý Start pro nasazení [prostředku brány NAT pomocí Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Rychlý Start pro nasazení [prostředku brány NAT pomocí Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Rychlý Start pro nasazení [prostředku brány NAT pomocí Azure Portal](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]

