---
title: 'Kurz: vytvoření a otestování brány NAT – Azure Portal'
titlesuffix: Azure Virtual Network NAT
description: V tomto kurzu se dozvíte, jak vytvořit bránu NAT pomocí Azure Portal a testovat službu NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: d798725892a9586c17cd7023863fe5cf7df05cb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84417833"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal-and-test-the-nat-service"></a>Kurz: Vytvoření brány NAT pomocí Azure Portal a testování služby NAT

V tomto kurzu vytvoříte bránu NAT, která poskytuje odchozí připojení k virtuálním počítačům v Azure. Pokud chcete otestovat bránu NAT, nasadíte zdrojový a cílový virtuální počítač. Bránu NAT otestujete tak, že odchozí připojení k veřejné IP adrese ze zdroje do cílového virtuálního počítače.  Tento kurz nasadí zdroj a cíl ve dvou různých virtuálních sítích ve stejné skupině prostředků jenom pro zjednodušení.

Pokud chcete, můžete tyto kroky provést pomocí [Azure CLI](tutorial-create-validate-nat-gateway-cli.md) nebo [Azure PowerShell](tutorial-create-validate-nat-gateway-powershell.md) místo na portálu.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="prepare-the-source-for-outbound-traffic"></a>Příprava zdroje na odchozí provoz

Provedeme vás konfigurací úplného testovacího prostředí a provedením testů v dalších krocích. Začneme se zdrojem, který bude používat prostředek brány NAT, který vytvoříme v pozdějších krocích.

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

Než nasadíte virtuální počítač a můžete použít bránu NAT, musíme vytvořit skupinu prostředků a virtuální síť.

V této části budete muset v krocích níže nahradit následující parametry:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetsource          |
| **\<region-name>**          | USA – východ 2      |
| **\<IPv4-address-space>**   | 192.168.0.0/16          |
| **\<subnet-name>**          | mySubnetsource        |
| **\<subnet-address-range>** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-source-virtual-machine"></a>Vytvořit zdrojový virtuální počítač

Nyní vytvoříme virtuální počítač pro použití služby NAT. Tento virtuální počítač má veřejnou IP adresu, která se používá jako veřejná IP adresa na úrovni instance, která umožňuje přístup k virtuálnímu počítači. Služba NAT má na vědomí směr toku a nahradí výchozí internetový cíl ve vaší podsíti. Veřejná IP adresa virtuálního počítače se nebude používat pro odchozí připojení.

K otestování brány NAT přiřadíme prostředek veřejné IP adresy jako veřejnou IP adresu na úrovni instance pro přístup k tomuto virtuálnímu počítači z vnějšku. Tato adresa se používá jenom pro přístup k tomuto testu.  Ukážeme, jak má služba NAT přednost před jinými odchozími možnostmi.

Tento virtuální počítač můžete také vytvořit bez veřejné IP adresy a vytvořit jiný virtuální počítač pro použití jako JumpBox bez veřejné IP adresy jako cvičení.

1. V levém horním rohu portálu vyberte **vytvořit Resource**  >  **COMPUTE**  >  **Ubuntu Server 18,04 LTS**nebo vyhledejte **Ubuntu Server 18,04 LTS** v hledání na webu Marketplace.

2. V části **vytvořit virtuální počítač**zadejte nebo vyberte následující hodnoty na kartě **základy** :
   - **Předplatné**  >  **Skupina prostředků**: vyberte **myResourceGroupNAT**.
   - **Podrobnosti instance**  >  **Název virtuálního počítače**: zadejte **myVMsource**.
   - **Podrobnosti instance**  >  **Oblast** > vyberte **východní USA 2**.
   - **Účet správce**  >  **Ověřování zadejte**: vyberte **heslo**.
   - **Účet správce** > zadejte **uživatelské jméno**, **heslo**a potvrzení informací o **hesle** .
   - Pravidla portů pro **příchozí spojení**  >  **Veřejné příchozí porty**: vyberte **Povolit vybrané porty**.
   - Pravidla portů pro **příchozí spojení**  >  **Vyberte příchozí porty**: vybrat **SSH (22)**
   - Vyberte kartu **síť** nebo vyberte **Další: disky**a **Další: síť**.

3. Na kartě **sítě** zkontrolujte, že jsou vybrané následující:
   - **Virtuální síť**: **myVnetsource**
   - **Podsíť**: **mySubnetsource**
   - **Veřejná IP adresa** > vyberte **vytvořit novou**.  V okně **vytvořit veřejnou IP adresu** do pole **název** zadejte **myPublicIPsourceVM** . Jako **SKU**vyberte **Standard** . Ve výchozím nastavení ponechte zbytek a klikněte na **OK**.
   - **Skupina zabezpečení sítě síťové karty**: vyberte **základní**.
   - **Veřejné příchozí porty**: vyberte **Povolit vybrané porty**.
   - **Vyberte příchozí porty**: potvrďte, že je zaškrtnuté políčko Potvrdit **SSH** .

4. Na kartě **Správa** v části **monitorování**nastavte **diagnostiku spouštění** na **vypnuto**.

5. Vyberte **Zkontrolovat a vytvořit**.

6. Zkontrolujte nastavení a klikněte na **vytvořit**.

## <a name="create-the-nat-gateway"></a>Vytvoření brány NAT

Pomocí brány NAT můžete použít jeden nebo více prostředků veřejné IP adresy, předpony veřejných IP adres nebo obojí. Přidáme prostředek veřejné IP adresy, předponu veřejné IP adresy a prostředek brány NAT.

Tato část podrobně popisuje, jak můžete vytvořit a nakonfigurovat následující součásti služby NAT pomocí prostředku brány NAT:
  - Veřejný fond IP adres a předponu veřejných IP adres, které se mají použít pro odchozí toky přeložené prostředkem brány NAT.
  - Změňte časový limit nečinnosti z výchozí hodnoty 4 minuty na 10 minut.

### <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

1. V levém horním rohu portálu vyberte **vytvořit**  >  **Networking**  >  **veřejnou IP adresu**sítě prostředku nebo v hledání na webu Marketplace vyhledejte **veřejnou IP adresu** . 

2. V nástroji **vytvořit veřejnou IP adresu**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Verze protokolu IP | Vyberte **IPv4**.
    | Skladová jednotka (SKU) | Vyberte **Standard**.
    | Název | Zadejte **myPublicIPsource**. |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **myResourceGroupNAT**. |
    | Umístění | Vyberte **USA – východ 2**.|

3. Ponechte zbytek výchozích hodnot a vyberte **vytvořit**.

### <a name="create-a-public-ip-prefix"></a>Vytvoření předpony veřejné IP adresy

1. V levém horním rohu portálu vyberte **vytvořit**  >  **Networking**  >  **předponu veřejné IP adresy**sítě prostředku nebo v hledání na webu Marketplace vyhledejte **předponu veřejné IP adresy** .

2. V části **vytvořit předponu veřejné IP adresy**zadejte nebo vyberte následující hodnoty na kartě **základy** :
   - **Předplatné**  >  **Skupina prostředků**: vyberte **myResourceGroupNAT**>
   - **Podrobnosti instance**  >  **Název**: zadejte **myPublicIPprefixsource**.
   - **Podrobnosti instance**  >  **Oblast**: vyberte **východní USA 2**.
   - **Podrobnosti instance**  >  **Velikost předpony**: vyberte **/31 (2 adresy)**

3. Ponechte zbytek výchozí hodnoty a vyberte **zkontrolovat + vytvořit**.

4. Zkontrolujte nastavení a pak vyberte **vytvořit**.


### <a name="create-a-nat-gateway-resource"></a>Vytvoření prostředku brány NAT

1. V levém horním rohu portálu vyberte **vytvořit prostředek**  >  **síťové**služby  >  **NAT Gateway**nebo vyhledejte **bránu NAT** v hledání na webu Marketplace.

2. V části **vytvořit bránu pro překlad síťových adres (NAT)** zadejte nebo vyberte následující hodnoty na kartě **základy** :
   - **Předplatné**  >  **Skupina prostředků**: vyberte **myResourceGroupNAT**.
   - **Podrobnosti instance**  >  **Název brány NAT**: zadejte **myNATgateway**.
   - **Podrobnosti instance**  >  **Oblast**: vyberte **východní USA 2**.
   - **Podrobnosti instance**  >  **Časový limit nečinnosti (minuty)**: zadejte **10**.
   - Vyberte kartu **Veřejná IP adresa** nebo vyberte **Další: veřejná IP adresa**.

3. Na kartě **Veřejná IP adresa** zadejte nebo vyberte následující hodnoty:
   - **Veřejné IP adresy**: vyberte **myPublicIPsource**.
   - **Předpony veřejných IP adres**: vyberte **myPublicIPprefixsource**.
   - Vyberte kartu **podsíť** nebo vyberte **Další: podsíť**.

4. Na kartě **podsíť** zadejte nebo vyberte následující hodnoty:
   - **Virtual Network**: vyberte **myResourceGroupNAT**  >  **myVnetsource**.
   - **Název podsítě**: zaškrtněte políčko vedle **mySubnetsource**.

5. Vyberte **Zkontrolovat a vytvořit**.

6. Zkontrolujte nastavení a pak vyberte **vytvořit**.

Veškerý odchozí provoz do internetových cílů teď používá službu překladu adres (NAT).  Není nutné konfigurovat UDR.


## <a name="prepare-destination-for-outbound-traffic"></a>Příprava cíle pro odchozí provoz

Nyní vytvoříme cíl pro odchozí přenosy přeložené službou překladu adres (NAT), abyste je mohli otestovat.


## <a name="virtual-network-and-parameters-for-destination"></a>Virtuální síť a parametry pro cíl

Před nasazením virtuálního počítače pro cíl musíme vytvořit virtuální síť, ve které se může umístit cílový virtuální počítač. Níže jsou uvedené stejné kroky jako u zdrojového virtuálního počítače s některými malými změnami k vystavení cílového koncového bodu.

V této části budete muset v krocích níže nahradit následující parametry:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetdestination          |
| **\<region-name>**          | USA – východ 2      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnetdestination        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-destination-virtual-machine"></a>Vytvořit cílový virtuální počítač

1. V levém horním rohu portálu vyberte **vytvořit Resource**  >  **COMPUTE**  >  **Ubuntu Server 18,04 LTS**nebo vyhledejte **Ubuntu Server 18,04 LTS** v hledání na webu Marketplace.

2. V části **vytvořit virtuální počítač**zadejte nebo vyberte následující hodnoty na kartě **základy** :
   - **Předplatné**  >  **Skupina prostředků**: vyberte **myResourceGroupNAT**.
   - **Podrobnosti instance**  >  **Název virtuálního počítače**: zadejte **myVMdestination**.
   - **Podrobnosti instance**  >  **Oblast** > vyberte **východní USA 2**.
   - **Účet správce**  >  **Ověřování zadejte**: vyberte **heslo**.
   - **Účet správce** > zadejte **uživatelské jméno**, **heslo**a potvrzení informací o **hesle** .
   - Pravidla portů pro **příchozí spojení**  >  **Veřejné příchozí porty**: vyberte **Povolit vybrané porty**.
   - Pravidla portů pro **příchozí spojení**  >  **Vyberte příchozí porty**: vyberte **SSH (22)** a **http (80)**.
   - Vyberte kartu **síť** nebo vyberte **Další: disky**a **Další: síť**.

3. Na kartě **sítě** zkontrolujte, že jsou vybrané následující:
   - **Virtuální síť**: **myVnetdestination**
   - **Podsíť**: **mySubnetdestination**
   - **Veřejná IP adresa** > vyberte **vytvořit novou**.  V okně **vytvořit veřejnou IP adresu** do pole **název** zadejte **myPublicIPdestinationVM** . Jako **SKU**vyberte **Standard** . Ve výchozím nastavení ponechte zbytek a klikněte na **OK**.
   - **Skupina zabezpečení sítě síťové karty**: vyberte **základní**.
   - **Veřejné příchozí porty**: vyberte **Povolit vybrané porty**.
   - **Vyberte příchozí porty**: potvrďte, že je zaškrtnuté políčko **SSH** a **http** .

4. Na kartě **Správa** v části **monitorování**nastavte **diagnostiku spouštění** na **vypnuto**.

5. Vyberte **Zkontrolovat a vytvořit**.

6. Zkontrolujte nastavení a pak vyberte **vytvořit**.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Příprava webového serveru a datové části testu na cílovém virtuálním počítači

Nejdřív musíme zjistit IP adresu cílového virtuálního počítače. 

1. Na levé straně portálu vyberte **skupiny prostředků**.
2. Vyberte **myResourceGroupNAT**.
3. Vyberte **myVMdestination**.
4. V části **Přehled**Zkopírujte hodnotu **Veřejná IP adresa** a vložte ji do poznámkového bloku, abyste ji mohli použít pro přístup k virtuálnímu počítači.

>[!IMPORTANT]
>Zkopírujte veřejnou IP adresu a vložte ji do poznámkového bloku, abyste ji mohli použít v následujících krocích. Označuje, že se jedná o cílový virtuální počítač.

### <a name="sign-in-to-destination-vm"></a>Přihlášení k cílovému virtuálnímu počítači

Otevřete [Azure Cloud Shell](https://shell.azure.com) v prohlížeči. Pro SSH k virtuálnímu počítači použijte IP adresu získanou v předchozím kroku.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Po přihlášení zkopírujte a vložte následující příkazy.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Tyto příkazy aktualizují váš virtuální počítač, nainstaluje Nginx a vytvoří soubor 100-kilobajtů. Tento soubor se načte ze zdrojového virtuálního počítače pomocí služby NAT.

Zavřete relaci SSH s cílovým virtuálním počítačem.

## <a name="prepare-test-on-source-vm"></a>Příprava testu na zdrojovém virtuálním počítači

Nejdřív musíme zjistit IP adresu zdrojového virtuálního počítače.

1. Na levé straně portálu vyberte **skupiny prostředků**.
2. Vyberte **myResourceGroupNAT**.
3. Vyberte **myVMsource**.
4. V části **Přehled**Zkopírujte hodnotu **Veřejná IP adresa** a vložte ji do poznámkového bloku, abyste ji mohli použít pro přístup k virtuálnímu počítači.

>[!IMPORTANT]
>Zkopírujte veřejnou IP adresu a vložte ji do poznámkového bloku, abyste ji mohli použít v následujících krocích. Označuje, že se jedná o zdrojový virtuální počítač.

### <a name="log-into-source-vm"></a>Přihlášení ke zdrojovému virtuálnímu počítači

Otevře novou kartu pro [Azure Cloud Shell](https://shell.azure.com) v prohlížeči.  Pro SSH k virtuálnímu počítači použijte IP adresu získanou v předchozím kroku. 

```azurecli-interactive
ssh <username>@<ip-address-source>
```

Zkopírujte a vložte následující příkazy, které se připraví na testování služby překladu adres (NAT).

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Tento příkaz aktualizuje váš virtuální počítač, nainstaluje se, nainstaluje [Hey](https://github.com/rakyll/hey) z GitHubu a aktualizuje prostředí prostředí.

Nyní jste připraveni otestovat službu NAT.

## <a name="validate-nat-service"></a>Ověřit službu NAT

Během přihlášení ke zdrojovému virtuálnímu počítači můžete k vygenerování požadavků na cílovou IP adresu použít **oblé** a **Hey** .

Pomocí oblé načtěte soubor 100-kilobajtů.  **\<ip-address-destination>** V následujícím příkladu nahraďte cílovou IP adresou, kterou jste zkopírovali dříve.  Parametr **--Output** označuje, že načtený soubor bude zahozen.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Můžete také vygenerovat sérii požadavků pomocí **Hey**. Znovu nahraďte **\<ip-address-destination>** cílovou IP adresou, kterou jste zkopírovali dříve.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Tento příkaz vygeneruje 100 požadavků, 10 současně s časovým limitem 30 sekund a bez opakovaného použití připojení TCP.  Každý požadavek získá 100 kB.  Na konci běhu ohlásí funkce **Hey** informace o tom, jak dobře služba NAT provedla.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, bránu NAT a všechny související prostředky. Vyberte skupinu prostředků **myResourceGroupNAT** , která obsahuje bránu NAT, a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili bránu NAT, vytvořili zdrojový a cílový virtuální počítač a potom jste otestovali bránu NAT.

Zkontrolujte metriky v Azure Monitor a podívejte se, jak vaše služba NAT funguje. Diagnostikujte problémy, jako je například vyčerpání prostředků dostupných portů SNAT.  Vyčerpání prostředků na portech SNAT se dá snadno adresovat přidáním dalších prostředků veřejné IP adresy nebo prostředků předpony veřejných IP adres nebo obou.

- Informace o [Virtual Network NAT](./nat-overview.md)
- Přečtěte si o [prostředku brány NAT](./nat-gateway-resource.md).
- Rychlý Start pro nasazení [prostředku brány NAT pomocí Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Rychlý Start pro nasazení [prostředku brány NAT pomocí Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Rychlý Start pro nasazení [prostředku brány NAT pomocí Azure Portal](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

