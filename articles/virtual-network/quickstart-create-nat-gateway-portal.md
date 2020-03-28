---
title: 'Úvodní příručka: Vytvoření brány NAT – portál Azure'
titlesuffix: Azure Virtual Network NAT
description: Tento rychlý start ukazuje, jak vytvořit bránu NAT pomocí portálu Azure
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 1ff13d8ef0ca4c6cf499c3245d3ef14370283075
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80066392"
---
# <a name="quickstart-create-a-nat-gateway-using-the-azure-portal"></a>Úvodní příručka: Vytvoření brány NAT pomocí portálu Azure

Tento rychlý start ukazuje, jak používat službu Azure Virtual Network NAT. Vytvoříte bránu NAT, která zajistí odchozí připojení pro virtuální počítač v Azure. 

Pokud dáváte přednost, můžete provést tyto kroky pomocí [Azure CLI](quickstart-create-nat-gateway-cli.md), [Azure PowerShell](quickstart-create-nat-gateway-powershell.md), nebo nasadit [ŠABLONU ARM](quickstart-create-nat-gateway-powershell.md) místo portálu.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [portálu Azure](https://portal.azure.com).

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

Než nasadíte virtuální počítač a můžete použít bránu NAT, musíme vytvořit skupinu prostředků a virtuální síť.

V této části budete muset nahradit následující parametry v krocích s níže uvedenými informacemi:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<>název skupiny prostředků**  | myResourceGroupNAT |
| **\<>názvů virtuálních sítí** | myVNet          |
| **\<>názvu oblasti**          | USA – východ 2      |
| **\<>adresního prostoru IPv4**   | 192.168.0.0\16          |
| **\<>názvu podsítě**          | mySubnet        |
| **\<>rozsah emitované sítě** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-vm-to-use-the-nat-gateway"></a>Vytvoření virtuálního počítače pro použití brány NAT

Teď vytvoříme virtuální hod pro použití služby NAT. Tento virtuální virtuální soud má veřejnou IP adresu, kterou se dá použít jako veřejná IP adresa na úrovni instance, která vám umožní přístup k virtuálnímu virtuálnímu jevu. Služba NAT je upozorněna na směr toku a nahradí výchozí cíl Internetu v podsíti. Veřejná IP adresa virtuálního počítačů se nepoužije pro odchozí připojení.

1. Na levé horní straně portálu vyberte **Vytvořit zdroj** > **Compute** > **Ubuntu Server 18.04 LTS**nebo vyhledejte **Ubuntu Server 18.04 LTS** ve vyhledávání Marketplace.

2. Do **pole Vytvořit virtuální počítač**zadejte nebo vyberte na kartě **Základy** následující hodnoty:
   - **Subscription** > **Skupina prostředků předplatného**: Vyberte **položku myResourceGroupNAT**.
   - **Podrobnosti instance** > **Název virtuálního počítače**: Zadejte **myVM**.
   - **Oblast podrobností** > **Region** instance > vyberte **možnost Východní USA 2**.
   - **Typ** > **ověření**účtu správce : Vyberte **heslo**.
   - **Účet správce** > Zadejte **uživatelské jméno**, **heslo**a potvrďte informace o **hesle.**
   - **Pravidla příchozího portu** > **Veřejná příchozí porty**: Vyberte **Povolit vybrané porty**.
   - **Pravidla příchozího portu** > **Vyberte příchozí porty:** Vyberte **SSH (22)**
   - Vyberte kartu **Síť** nebo **další: Disky**, pak **Další: Síť**.

3. Na kartě **Síť** zkontrolujte, zda jsou vybrány následující položky:
   - **Virtuální síť**: **myVnet**
   - **Podsíť**: **myPodnet**
   - **Veřejná IP** > Vyberte **Vytvořit nový**.  V okně **Vytvořit veřejnou IP adresu** zadejte do pole **Název** **myPublicIPVM** a zvolte **Standardní** pro **skladovou položku**.  Klikněte na tlačítko **OK**.
   - **Skupina zabezpečení sítě NIC**: Vyberte **základní**.
   - **Veřejné příchozí porty**: Vyberte **Povolit vybrané porty**.
   - **Vybrat příchozí porty**: Potvrzení, že je **vybránssh.**

4. Na kartě **Správa** nastavte v části **Sledování** **diagnostiku spouštění** na **Vypnuto**.

5. Vyberte **Zkontrolovat a vytvořit**. 

6. Zkontrolujte nastavení a klepněte na tlačítko **Vytvořit**.

## <a name="create-the-nat-gateway"></a>Vytvoření brány NAT

Můžete použít jeden nebo více veřejných prostředků IP adres, veřejné předpony IP nebo obojí. Přidáme veřejný ip prostředek, veřejnou předponu IP a prostředek brány NAT.

V této části je podrobně uvedeno, jak můžete vytvořit a nakonfigurovat následující součásti služby NAT pomocí prostředku brány NAT:
  - Veřejný fond IP adres a veřejná předpona IP pro odchozí toky přeložené prostředkem brány NAT.
  - Změňte časový limit nečinnosti z výchozí hodnoty 4 minut na 10 minut.

### <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

1. Na levé horní straně portálu vyberte Vytvořit**Networking** > **veřejnou IP adresu** **sítě prostředků** > nebo vyhledejte **veřejnou IP adresu** ve vyhledávání marketplace.

2. Do **položky Vytvořit veřejnou IP adresu**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Verze IP | Vyberte **IPv4**.
    | Skladová jednotka (SKU) | Vyberte **standardní**.
    | Name (Název) | Zadejte **myPublicIP**. |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **položku myResourceGroupNAT**. |
    | Umístění | Vyberte **USA – východ 2**.|

3. Ponechejte zbývající výchozí hodnoty a vyberte **Vytvořit**.

### <a name="create-a-public-ip-prefix"></a>Vytvoření veřejné předpony IP

1. Na levé horní straně portálu vyberte Vytvořit**předponu veřejné IP adresy**pro síť **nebo** > **Networking** > vyhledejte **předponu Veřejné IP adresy** ve vyhledávání marketplace. 

2. Do **pole Vytvořit veřejnou předponu IP**zadejte nebo vyberte na kartě **Základy** následující hodnoty:
   - **Skupina** > **prostředků předplatného:** Vyberte **myResourceGroupNAT**>
   - **Název podrobností instance** > **Name**: Zadejte **myPublicIPprefix**.
   - **Podrobnosti instance** > **Oblast**: Vyberte východní **USA 2**.
   - **Podrobnosti instance** > **Velikost předpony**: Vyberte **/31 (2 adresy)**

3. Zbytek ponechejte jako výchozí a vyberte **Zkontrolovat + vytvořit**.

4. Zkontrolujte nastavení a pak vyberte **Vytvořit**.
   

### <a name="create-a-nat-gateway-resource"></a>Vytvoření prostředku brány NAT

1. Na levé horní straně portálu vyberte Vytvořit**Networking** > **bránu NAT** **sítě prostředků** > nebo vyhledejte **bránu NAT** ve vyhledávání na Marketplace.

2. Do **pole Vytvořit bránu překladu síťových adres (NAT)** zadejte nebo vyberte na kartě **Základy** následující hodnoty:
   - **Subscription** > **Skupina prostředků předplatného**: Vyberte **položku myResourceGroupNAT**.
   - **Podrobnosti instance** > **NÁZEV brány NAT**: Zadejte **myNATgateway**.
   - **Podrobnosti instance** > **Oblast**: Vyberte východní **USA 2**.
   - **Podrobnosti o instanci** > **Časový limit nečinnosti (minuty):** Typ **10**.
   - Vyberte kartu **Veřejná ADRESA IP** nebo **možnost Další: Veřejná ADRESA IP**.

3. Na kartě **Veřejná ip** adresa zadejte nebo vyberte následující hodnoty:
   - **Veřejné IP adresy**: Vyberte **myPublicIP**.
   - **Veřejné IP předpony**: Vyberte **myPublicIPprefix**.
   - Vyberte kartu **Podsíť** nebo vyberte **Další: Podsíť**.

4. Na kartě **Podsíť** zadejte nebo vyberte následující hodnoty:
   - **Virtuální síť**: Vyberte **myResourceGroupNAT** > **myVnet**.
   - **Název podsítě**: Zaškrtněte políčko vedle **mySubnet**.

5. Vyberte **Zkontrolovat a vytvořit**.

6. Zkontrolujte nastavení a pak vyberte **Vytvořit**.

## <a name="discover-the-ip-address-of-the-vm"></a>Zjištění IP adresy virtuálního soudu

1. Na levé straně portálu vyberte **skupiny prostředků**.
2. Vyberte **položku myResourceGroupNAT**.
3. Vyberte **myVM**.
4. V **přehledu**zkopírujte hodnotu **Veřejné IP adresy** a vložte do poznámkového bloku, abyste ji mohli použít pro přístup k virtuálnímu virtuálnímu provozu.

>[!IMPORTANT]
>Zkopírujte veřejnou IP adresu a vložte ji do poznámkového bloku, abyste ji mohli použít pro přístup k virtuálnímu virtuálnímu okraji.

## <a name="sign-in-to-vm"></a>Přihlášení k virtuálnímu virtuálnímu virtuálnímu montovně

Otevřete [Azure Cloud Shell](https://shell.azure.com) ve svém prohlížeči. Použijte IP adresu načtenou v předchozím kroku na SSH do virtuálního počítače.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Nyní jste připraveni používat službu NAT.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, odstraňte skupinu prostředků, bránu NAT a všechny související prostředky. Vyberte skupinu prostředků **myResourceGroupNAT,** která obsahuje bránu NAT, a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili bránu NAT a virtuální počítač, abyste ji používali. 

Zkontrolujte metriky v Azure Monitoru a podívejte se, jak vaše služba NAT funguje. Diagnostikujte problémy, jako je vyčerpání prostředků z dostupných portů SNAT.  Vyčerpání prostředků portů SNAT je řešeno přidáním dalších prostředků veřejné IP adresy nebo veřejných prostředků předpony IP nebo obojího.


- Další informace o [virtuální síti Azure NAT](./nat-overview.md)
- Informace o [prostředku brány NAT](./nat-gateway-resource.md).
- Úvodní příručka pro nasazení [prostředků brány NAT pomocí Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Úvodní příručka pro nasazení [prostředků brány NAT pomocí Azure PowerShellu](./quickstart-create-nat-gateway-powershell.md).
- Úvodní příručka pro nasazení [prostředků brány NAT pomocí portálu Azure](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]

