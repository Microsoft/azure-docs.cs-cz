---
title: 'Kurz: Vytvoření a testování brány NAT – portál Azure'
titlesuffix: Azure Virtual Network NAT
description: Tento kurz ukazuje, jak vytvořit bránu NAT pomocí portálu Azure a otestovat službu NAT
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: ceadbb4297ad0c5ce28470dd75b3f3496c9c5152
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084739"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal-and-test-the-nat-service"></a>Kurz: Vytvoření brány NAT pomocí portálu Azure a testování služby NAT

V tomto kurzu vytvoříte bránu NAT, která zajistí odchozí připojení pro virtuální počítače v Azure. Chcete-li otestovat bránu NAT, nasadíte zdrojový a cílový virtuální počítač. Bránu NAT otestujete vytvořením odchozích připojení k veřejné IP adrese ze zdroje do cílového virtuálního počítače.  Tento kurz nasazuje zdroj a cíl ve dvou různých virtuálních sítích ve stejné skupině prostředků pouze pro jednoduchost.

Pokud dáváte přednost, můžete provést tyto kroky pomocí [Azure CLI](tutorial-create-validate-nat-gateway-cli.md) nebo [Azure PowerShell](tutorial-create-validate-nat-gateway-powershell.md) místo portálu.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="prepare-the-source-for-outbound-traffic"></a>Příprava zdroje pro odchozí provoz

Provedeme vás konfigurací úplného testovacího prostředí a provedením samotných testů v dalších krocích. Začneme se zdrojem, který bude používat prostředek brány NAT, který vytvoříme v pozdějších krocích.

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

Než nasadíte virtuální počítač a můžete použít bránu NAT, musíme vytvořit skupinu prostředků a virtuální síť.

V této části budete muset nahradit následující parametry v krocích s níže uvedenými informacemi:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<>název skupiny prostředků**  | myResourceGroupNAT |
| **\<>názvů virtuálních sítí** | myVNetsource          |
| **\<>názvu oblasti**          | USA – východ 2      |
| **\<>adresního prostoru IPv4**   | 192.168.0.0/16          |
| **\<>názvu podsítě**          | mySubnetsource        |
| **\<>rozsah emitované sítě** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-source-virtual-machine"></a>Vytvoření zdrojového virtuálního počítače

Teď vytvoříme virtuální hod pro použití služby NAT. Tento virtuální virtuální soud má veřejnou IP adresu, kterou se dá použít jako veřejná IP adresa na úrovni instance, která vám umožní přístup k virtuálnímu virtuálnímu jevu. Služba NAT je upozorněna na směr toku a nahradí výchozí cíl Internetu v podsíti. Veřejná IP adresa virtuálního počítačů se nepoužije pro odchozí připojení.

Chcete-li otestovat bránu NAT, přiřadíme prostředek veřejné IP adresy jako veřejnou IP adresu na úrovni instance pro přístup k tomuto virtuálnímu počítači zvenčí. Tato adresa se používá pouze pro přístup k ní pro test.  Ukážeme, jak má služba NAT přednost před ostatními odchozími možnostmi.

Tento virtuální virtuální ms můžete také vytvořit bez veřejné IP adresy a vytvořit jiný virtuální virtuální virtuální< systém, který se použije jako jumpbox bez veřejné IP adresy jako cvičení.

1. Na levé horní straně portálu vyberte **Vytvořit zdroj** > **Compute** > **Ubuntu Server 18.04 LTS**nebo vyhledejte **Ubuntu Server 18.04 LTS** ve vyhledávání Marketplace.

2. V **části Vytvoření virtuálního počítače**zadejte nebo vyberte na kartě **Základy** následující hodnoty:
   - **Subscription** > **Skupina prostředků předplatného**: Vyberte **položku myResourceGroupNAT**.
   - **Podrobnosti instance** > **Název virtuálního počítače:** zadejte **myVMsource**.
   - **Oblast podrobností** > **Region** instance > vyberte **možnost Východní USA 2**.
   - **Zadání ověřování účtu** > **Authentication enter**správce : Vyberte **heslo**.
   - **Účet správce** > Zadejte **uživatelské jméno**, **heslo**a potvrďte informace o **hesle.**
   - **Pravidla příchozího portu** > **Veřejná příchozí porty**: Vyberte **Povolit vybrané porty**.
   - **Pravidla příchozího portu** > **Vyberte příchozí porty:** Vyberte **SSH (22)**
   - Vyberte kartu **Síť** nebo **další: Disky**, pak **Další: Síť**.

3. Na kartě **Síť** zkontrolujte, zda jsou vybrány následující položky:
   - **Virtuální síť**: **myVnetsource**
   - **Podsíť**: **mySubnetsource**
   - **Veřejná IP** > Vyberte **Vytvořit nový**.  V okně **Vytvořit veřejnou IP adresu** zadejte do pole **Název** **myPublicIPsourceVM.** Vyberte **standardní** pro **skladovou položku**. Zbytek ponechejte ve výchozím nastavení a klepněte na **tlačítko OK**.
   - **Skupina zabezpečení sítě NIC**: Vyberte **základní**.
   - **Veřejné příchozí porty**: Vyberte **Povolit vybrané porty**.
   - **Vybrat příchozí porty**: Potvrzení, že je **vybránssh.**

4. Na kartě **Správa** nastavte v části **Sledování** **diagnostiku spouštění** na **Vypnuto**.

5. Vyberte **Zkontrolovat a vytvořit**.

6. Zkontrolujte nastavení a klepněte na tlačítko **Vytvořit**.

## <a name="create-the-nat-gateway"></a>Vytvoření brány NAT

Můžete použít jeden nebo více veřejných prostředků IP adres, veřejné IP předpony nebo obojí s bránou NAT. Přidáme veřejný ip prostředek, veřejnou předponu IP a prostředek brány NAT.

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
    | Název | Zadejte **myPublicIPsource**. |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **položku myResourceGroupNAT**. |
    | Umístění | Vyberte **USA – východ 2**.|

3. Ponechejte zbývající výchozí hodnoty a vyberte **Vytvořit**.

### <a name="create-a-public-ip-prefix"></a>Vytvoření veřejné předpony IP

1. Na levé horní straně portálu vyberte Vytvořit**předponu veřejné IP adresy**pro síť **nebo** > **Networking** > vyhledejte **předponu Veřejné IP adresy** ve vyhledávání marketplace.

2. V **části Vytvořit veřejnou předponu IP**zadejte nebo vyberte na kartě **Základy** následující hodnoty:
   - **Skupina** > **prostředků předplatného:** Vyberte **myResourceGroupNAT**>
   - **Název podrobností instance** > **:** zadejte **myPublicIPprefixsource**.
   - **Podrobnosti instance** > **Oblast**: Vyberte východní **USA 2**.
   - **Podrobnosti instance** > **Velikost předpony**: Vyberte **/31 (2 adresy)**

3. Zbytek ponechejte jako výchozí a vyberte **Zkontrolovat + vytvořit**.

4. Zkontrolujte nastavení a pak vyberte **Vytvořit**.


### <a name="create-a-nat-gateway-resource"></a>Vytvoření prostředku brány NAT

1. Na levé horní straně portálu vyberte Vytvořit**Networking** > **bránu NAT** **sítě prostředků** > nebo vyhledejte **bránu NAT** ve vyhledávání na Marketplace.

2. V **části Vytvořit bránu překladu síťových adres (NAT)** zadejte nebo vyberte na kartě **Základy** následující hodnoty:
   - **Subscription** > **Skupina prostředků předplatného**: Vyberte **položku myResourceGroupNAT**.
   - **Podrobnosti instance** > **NÁZEV brány NAT**: zadejte **myNATgateway**.
   - **Podrobnosti instance** > **Oblast**: Vyberte východní **USA 2**.
   - **Podrobnosti instance** > **Časový limit nečinnosti (minuty):** zadejte **hodnotu 10**.
   - Vyberte kartu **Veřejná ADRESA IP** nebo **možnost Další: Veřejná ADRESA IP**.

3. Na kartě **Veřejná ip** adresa zadejte nebo vyberte následující hodnoty:
   - **Veřejné IP adresy**: Vyberte **myPublicIPsource**.
   - **Veřejné IP předpony**: Vyberte **myPublicIPprefixsource**.
   - Vyberte kartu **Podsíť** nebo vyberte **Další: Podsíť**.

4. Na kartě **Podsíť** zadejte nebo vyberte následující hodnoty:
   - **Virtuální síť**: Vyberte **myResourceGroupNAT** > **myVnetsource**.
   - **Název podsítě**: Zaškrtněte políčko vedle **položky mySubnetsource**.

5. Vyberte **Zkontrolovat a vytvořit**.

6. Zkontrolujte nastavení a pak vyberte **Vytvořit**.

Veškerý odchozí provoz do internetových cílů nyní používá službu NAT.  Není nutné konfigurovat UDR.


## <a name="prepare-destination-for-outbound-traffic"></a>Příprava cíle pro odchozí provoz

Nyní vytvoříme cíl pro odchozí provoz přeložený službou NAT, abyste jej mohli otestovat.


## <a name="virtual-network-and-parameters-for-destination"></a>Virtuální síť a parametry pro cíl

Před nasazením virtuálního počítače pro cíl, musíme vytvořit virtuální síť, kde se může nasazovat cílový virtuální počítač. Následují stejné kroky jako u zdrojového virtuálního virtuálního aplikace s některými malými změnami, které zveřejňují cílový koncový bod.

V této části budete muset nahradit následující parametry v krocích s níže uvedenými informacemi:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<>název skupiny prostředků**  | myResourceGroupNAT |
| **\<>názvů virtuálních sítí** | myVNetdestination          |
| **\<>názvu oblasti**          | USA – východ 2      |
| **\<>adresního prostoru IPv4**   | 192.168.0.0/16          |
| **\<>názvu podsítě**          | mySubnetdestination        |
| **\<>rozsah emitované sítě** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-destination-virtual-machine"></a>Vytvoření cílového virtuálního počítače

1. Na levé horní straně portálu vyberte **Vytvořit zdroj** > **Compute** > **Ubuntu Server 18.04 LTS**nebo vyhledejte **Ubuntu Server 18.04 LTS** ve vyhledávání Marketplace.

2. V **části Vytvoření virtuálního počítače**zadejte nebo vyberte na kartě **Základy** následující hodnoty:
   - **Subscription** > **Skupina prostředků předplatného**: Vyberte **položku myResourceGroupNAT**.
   - **Podrobnosti instance** > **Název virtuálního počítače:** zadejte **myVMdestination**.
   - **Oblast podrobností** > **Region** instance > vyberte **možnost Východní USA 2**.
   - **Zadání ověřování účtu** > **Authentication enter**správce : Vyberte **heslo**.
   - **Účet správce** > Zadejte **uživatelské jméno**, **heslo**a potvrďte informace o **hesle.**
   - **Pravidla příchozího portu** > **Veřejná příchozí porty**: Vyberte **Povolit vybrané porty**.
   - **Pravidla příchozího portu:** > **Select inbound ports**Vyberte **SSH (22)** a **HTTP (80).**
   - Vyberte kartu **Síť** nebo **další: Disky**, pak **Další: Síť**.

3. Na kartě **Síť** zkontrolujte, zda jsou vybrány následující položky:
   - **Virtuální síť**: **myVnetdestination**
   - **Podsíť**: **mySubnetdestination**
   - **Veřejná IP** > Vyberte **Vytvořit nový**.  V okně **Vytvořit veřejnou IP adresu** zadejte do pole **Název** **myPublicIPdestinationVM.** Vyberte **standardní** pro **skladovou položku**. Zbytek ponechejte ve výchozím nastavení a klepněte na **tlačítko OK**.
   - **Skupina zabezpečení sítě NIC**: Vyberte **základní**.
   - **Veřejné příchozí porty**: Vyberte **Povolit vybrané porty**.
   - **Vybrat příchozí porty**: Potvrďte, že je **vybrána možnost SSH** a **HTTP.**

4. Na kartě **Správa** nastavte v části **Sledování** **diagnostiku spouštění** na **Vypnuto**.

5. Vyberte **Zkontrolovat a vytvořit**.

6. Zkontrolujte nastavení a pak vyberte **Vytvořit**.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Příprava webového serveru a testování datové části na cílovém virtuálním počítači

Nejprve musíme zjistit IP adresu cílového virtuálního počítačů. 

1. Na levé straně portálu vyberte **skupiny prostředků**.
2. Vyberte **položku myResourceGroupNAT**.
3. Vyberte **myVMdestination**.
4. V **přehledu**zkopírujte hodnotu **Veřejné IP adresy** a vložte do poznámkového bloku, abyste ji mohli použít pro přístup k virtuálnímu virtuálnímu provozu.

>[!IMPORTANT]
>Zkopírujte veřejnou IP adresu a vložte ji do poznámkového bloku, abyste ji mohli použít v následujících krocích. Označte, že se jedná o cílový virtuální počítač.

### <a name="sign-in-to-destination-vm"></a>Přihlášení k cílovému virtuálnímu virtuálnímu mísu

Otevřete [Azure Cloud Shell](https://shell.azure.com) ve svém prohlížeči. Použijte IP adresu načtenou v předchozím kroku na SSH do virtuálního počítače.

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

Tyto příkazy aktualizují váš virtuální počítač, nainstalují nginx a vytvoří soubor 100 KBytes. Tento soubor bude načten ze zdrojového virtuálního virtuálního aplikace pomocí služby NAT.

Zavřete relaci SSH s cílovým virtuálním virtuálním mem.

## <a name="prepare-test-on-source-vm"></a>Příprava testu na zdrojovém virtuálním počítači

Nejprve musíme zjistit IP adresu zdrojového virtuálního počítačů.

1. Na levé straně portálu vyberte **skupiny prostředků**.
2. Vyberte **položku myResourceGroupNAT**.
3. Vyberte **myVMsource**.
4. V **přehledu**zkopírujte hodnotu **Veřejné IP adresy** a vložte do poznámkového bloku, abyste ji mohli použít pro přístup k virtuálnímu virtuálnímu provozu.

>[!IMPORTANT]
>Zkopírujte veřejnou IP adresu a vložte ji do poznámkového bloku, abyste ji mohli použít v následujících krocích. Označte, že se jedná o zdrojový virtuální počítač.

### <a name="log-into-source-vm"></a>Přihlášení ke zdrojovému virtuálnímu virtuálnímu montovně

Otevřete novou kartu pro [Azure Cloud Shell](https://shell.azure.com) ve vašem prohlížeči.  Použijte IP adresu načtenou v předchozím kroku na SSH do virtuálního počítače. 

```azurecli-interactive
ssh <username>@<ip-address-source>
```

Zkopírujte a vložte následující příkazy a připravte se na testování služby NAT.

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

Tento příkaz aktualizuje váš virtuální počítač, nainstaluje go, nainstaluje hej z [GitHubu](https://github.com/rakyll/hey) a aktualizuje prostředí prostředí prostředí.

Nyní jste připraveni otestovat službu NAT.

## <a name="validate-nat-service"></a>Ověřit službu NAT

Při přihlášení ke zdrojovému virtuálnímu virtuálnímu počítačůmu můžete ke generování požadavků na cílovou IP adresu použít **curl** a **hey.**

Pomocí zvlnění načtěte soubor 100 KBytes.  Nahraďte ** \<cílovou>ip-adres** v níže uvedeném příkladu cílovou adresou IP, kterou jste dříve zkopírovali.  Parametr **--output** označuje, že načtený soubor bude zahozen.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Můžete také generovat řadu požadavků pomocí **hey**. Opět nahraďte ** \<cílovou>ip-address>** cílovou ADRESOu IP, kterou jste dříve zkopírovali.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Tento příkaz vygeneruje 100 požadavků, 10 současně, s časovým limitem 30 sekund a bez opětovného použití připojení TCP.  Každý požadavek načte 100 kbajtů.  Na konci běhu, **bude hej** zprávu některé statistiky o tom, jak dobře na vychýlivá služba NAT.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, odstraňte skupinu prostředků, bránu NAT a všechny související prostředky. Vyberte skupinu prostředků **myResourceGroupNAT,** která obsahuje bránu NAT, a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili bránu NAT, vytvořili zdrojový a cílový virtuální počítač a pak otestovali bránu NAT.

Zkontrolujte metriky v Azure Monitoru a podívejte se, jak vaše služba NAT funguje. Diagnostikujte problémy, jako je vyčerpání prostředků z dostupných portů SNAT.  Vyčerpání prostředků portů SNAT lze snadno vyřešit přidáním dalších prostředků veřejné IP adresy nebo veřejných prostředků předpony IP nebo obojího.

- Další informace o [překladu virtuálních sítí](./nat-overview.md)
- Informace o [prostředku brány NAT](./nat-gateway-resource.md).
- Úvodní příručka pro nasazení [prostředků brány NAT pomocí Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Úvodní příručka pro nasazení [prostředků brány NAT pomocí Azure PowerShellu](./quickstart-create-nat-gateway-powershell.md).
- Úvodní příručka pro nasazení [prostředků brány NAT pomocí portálu Azure](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

