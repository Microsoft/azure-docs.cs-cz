---
title: Rychlý Start – vytvoření virtuálního počítače s důvěrnými informacemi z Azure pomocí Marketplace
description: Začněte s nasazeními a Naučte se, jak rychle vytvořit důvěrný výpočetní virtuální počítač s Marketplace.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: aba23b67574fb74b7cd571dc5d4642bb8b991b93
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102566630"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-marketplace"></a>Rychlý Start: nasazení virtuálního počítače s důvěrnými výpočetními prostředími Azure na webu Marketplace

Seznamte se s důvěrnými výpočetními prostředími Azure pomocí Azure Marketplace k vytvoření virtuálního počítače, který je podporovaný procesorem Intel SGX. Potom nainstalujete Open enklávy Software Development Kit (SDK) a nastavte své vývojové prostředí. 

Tento kurz se doporučuje, pokud chcete rychle začít nasazovat důvěrný výpočetní virtuální počítač. Virtuální počítače jsou spuštěné na základě specializovaného hardwaru a vyžadují, aby konkrétní vstupy konfigurace běžely tak, jak mají. Nabídka Marketplace popsaná v tomto rychlém startu usnadňuje nasazení tím, že omezuje vstup uživatele.

Pokud vás zajímá nasazení důvěrného výpočetního virtuálního počítače s více vlastními konfiguracemi, postupujte podle [kroků pro nasazení virtuálního počítače Azure Portal důvěrného COMPUTE](quick-create-portal.md).

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) před tím, než začnete.

> [!NOTE]
> Bezplatné zkušební účty nemají přístup k virtuálním počítačům použitým v tomto kurzu. Upgradujte prosím na předplatné s průběžnými platbami.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. V horní části zadejte do panelu hledání **důvěrné výpočetní prostředí Azure** .

1. V části **Marketplace** vyberte **Azure důvěrné výpočetní prostředí (virtuální počítač)** . 

    ![Výběr Marketplace](media/quick-create-marketplace/portal-search-marketplace.png)    

1. Na cílové stránce Azure důvěrné výpočetní nasazení vyberte **vytvořit**.
 

## <a name="configure-your-virtual-machine"></a>Konfigurace virtuálního počítače

1. Na kartě **základy** vyberte své **předplatné** a **skupinu prostředků**. Skupina prostředků musí být prázdná, aby do ní bylo možné nasadit virtuální počítač z této šablony.

1. Zadejte nebo vyberte tyto hodnoty:

   * **Oblast**: Vyberte oblast Azure, která je pro vás nejvhodnější.

        > [!NOTE]
        > Důvěrné výpočetní virtuální počítače se spouští jenom na specializovaném hardwaru dostupném v konkrétních oblastech. Nejnovější dostupné oblasti pro DCsv2-Series virtuálních počítačů najdete v části [dostupné oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).
    
    * **Zvolte obrázek**: Vyberte libovolný obrázek. Pokud byste chtěli tento konkrétní kurz dokončit, vyberte Ubuntu 18,04 (Gen 2). Jinak budete přesměrováni podle příslušných kroků níže. 

    * **Název virtuálního počítače**, zadejte název nového virtuálního počítače.

    * **Typ ověřování**: Pokud VYTVÁŘÍTE virtuální počítač se systémem Linux, vyberte **veřejný klíč SSH** . 

         > [!NOTE]
         > U ověřování máte na výběr mezi používáním veřejného klíče SSH nebo hesla. SSH je bezpečnější. Pokyny k vygenerování klíče SSH najdete v tématu [Vytvoření klíčů SSH v Linuxu a na Macu pro virtuální počítače s Linuxem v Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Uživatelské jméno**: Zadejte jméno správce pro virtuální počítač.

    * **Veřejný klíč SSH**: Pokud je k dispozici, zadejte svůj veřejný klíč RSA.
    
    * **Heslo**: Pokud je to možné, zadejte heslo pro ověřování.
 
1. V dolní části obrazovky vyberte tlačítko **Další: nastavení virtuálního počítače** .

    > [!IMPORTANT]
    > Počkejte, až se stránka aktualizuje. *Neměla by* se zobrazit zpráva s oznámením, že virtuální počítače s důvěrnými výpočetními systémy DCsv2-Series jsou dostupné v omezeném počtu oblastí. Pokud tato zpráva přetrvává, vraťte se na předchozí stránku a vyberte dostupnou DCsv2-Series oblast.

1. V části **změnit velikost** vyberte virtuální počítač s důvěrnými výpočetními funkcemi v selektoru velikosti. 

    > [!TIP]
    > Měli byste vidět velikosti **DC1s_v2**, **DC2s_v2**, **DC4s_V2** a **DC8_v2**. Toto jsou jediné velikosti virtuálních počítačů, které aktuálně podporují důvěrný výpočetní výkon. [Další informace](virtual-machine-solutions.md).

1. Jako **typ disku s operačním systémem** vyberte typ disku.

1. Pro **Virtual Network** vytvořte novou nebo vyberte z existujícího prostředku.

1. V případě **podsítě** vytvořte novou položku nebo vyberte některou z existujících prostředků.

1. V **možnosti vybrat veřejné příchozí porty** zvolte **SSH (Linux)/RDP (Windows)**. V tomto rychlém startu je tento krok nezbytný pro připojení k virtuálnímu počítači a dokončení konfigurace Open enklávy SDK. 

1. V případě **diagnostiky spouštění** nechte tuto položku zakázanou pro tento rychlý Start. 

1. Vyberte **Zkontrolovat a vytvořit**.

1. V podokně **Revize + vytvořit** vyberte **vytvořit**.

> [!NOTE]
> Přejděte k další části a pokračujte v tomto kurzu, pokud jste nasadili virtuální počítač se systémem Linux. Pokud jste nasadili virtuální počítač s Windows, [Připojte se k virtuálnímu počítači s Windows pomocí následujícího postupu](../virtual-machines/windows/connect-logon.md) a pak [nainstalujte sadu OE SDK ve Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md).


## <a name="connect-to-the-linux-vm"></a>Připojení k virtuálnímu počítači s Linuxem

Pokud už používáte prostředí BASH, připojte se k virtuálnímu počítači Azure pomocí příkazu **ssh**. V následujícím příkazu nahraďte uživatelské jméno a IP adresu virtuálního počítače pro připojení k vašemu virtuálnímu počítači s Linuxem.

```bash
ssh azureadmin@40.55.55.555
```

Veřejnou IP adresu svého virtuálního počítače najdete v Azure Portal v části Přehled svého virtuálního počítače.

![IP adresa na webu Azure Portal](media/quick-create-portal/public-ip-virtual-machine.png)

Pokud používáte Windows a nemáte prostředí BASH, nainstalujte klienta SSH, jako je například.

1. [Stáhněte a nainstalujte PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Spusťte PuTTY.

1. Na obrazovce konfigurace PuTTY zadejte veřejnou IP adresu vašeho virtuálního počítače.

1. Vyberte **otevřít** a na výzvy zadejte své uživatelské jméno a heslo.

Další informace o připojení k virtuálním počítačům s Linuxem najdete v tématu [Vytvoření virtuálního počítače s Linuxem v Azure pomocí portálu](../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Pokud se zobrazí výstraha zabezpečení výstupů na klíč hostitele serveru, který není uložen do mezipaměti v registru, vyberte z následujících možností. Pokud tomuto hostiteli důvěřujete, vyberte **Ano** a přidejte klíč do mezipaměti pro výstup a pokračujte v připojování. Chcete-li provést připojení pouze jednou, aniž byste museli přidat klíč do mezipaměti, vyberte možnost **ne**. Pokud tomuto hostiteli nedůvěřujete, vyberte **Zrušit** , aby se připojení zrušilo.

## <a name="install-the-open-enclave-sdk-oe-sdk"></a>Instalace Open enklávy SDK (OE SDK) <a id="Install"></a>

Postupujte podle podrobných pokynů k instalaci sady [OE SDK](https://github.com/openenclave/openenclave) na DCsv2-Series virtuální počítač s bitovou kopií 18,04 LTS 2. generace. 

Pokud je váš virtuální počítač spuštěný na Ubuntu 16,04 LTS Gen 2, budete muset postupovat podle [pokynů k instalaci pro Ubuntu 16,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md). 

#### <a name="1-configure-the-intel-and-microsoft-apt-repositories"></a>1. Konfigurace úložišť Intel a Microsoft APT

```bash
echo 'deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main' | sudo tee /etc/apt/sources.list.d/intel-sgx.list
wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | sudo apt-key add -

echo "deb http://apt.llvm.org/bionic/ llvm-toolchain-bionic-7 main" | sudo tee /etc/apt/sources.list.d/llvm-toolchain-bionic-7.list
wget -qO - https://apt.llvm.org/llvm-snapshot.gpg.key | sudo apt-key add -

echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | sudo tee /etc/apt/sources.list.d/msprod.list
wget -qO - https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
```

#### <a name="2-install-the-intel-sgx-dcap-driver"></a>2. Nainstalujte ovladač Intel SGX DCAP.

```bash
sudo apt update
sudo apt -y install dkms
wget https://download.01.org/intel-sgx/sgx-dcap/1.4/linux/distro/ubuntuServer18.04/sgx_linux_x64_driver_1.21.bin -O sgx_linux_x64_driver.bin
chmod +x sgx_linux_x64_driver.bin
sudo ./sgx_linux_x64_driver.bin
```

> [!WARNING]
> Použijte prosím nejnovější ovladač Intel SGX DCAP z [webu SGX od společnosti Intel](https://01.org/intel-software-guard-extensions/downloads).

#### <a name="3-install-the-intel-and-open-enclave-packages-and-dependencies"></a>3. nainstalujte balíčky a enklávy balíčky Intel a otevřete tyto součásti:

```bash
sudo apt -y install clang-7 libssl-dev gdb libsgx-enclave-common libsgx-enclave-common-dev libprotobuf10 libsgx-dcap-ql libsgx-dcap-ql-dev az-dcap-client open-enclave
```

> [!NOTE] 
> Tento krok nainstaluje taky balíček [AZ-Dcap-Client](https://github.com/microsoft/azure-dcap-client) , který je nezbytný k provádění vzdáleného ověřování v Azure.

#### <a name="4-verify-the-open-enclave-sdk-install"></a>4. **Ověřte, že je otevřená instalace sady enklávy SDK** .

Projděte si téma [použití sady Open enklávy SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/Linux_using_oe_sdk.md) na GitHubu pro ověření a používání nainstalované sady SDK.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, virtuální počítač a všechny související prostředky. 

Vyberte skupinu prostředků pro virtuální počítač a pak vyberte **Odstranit**. Potvrďte název skupiny prostředků, abyste dokončili odstraňování prostředků.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili důvěrný výpočetní virtuální počítač a nainstalovali jste Open enklávy SDK. Další informace o důvěrných výpočetních virtuálních počítačích v Azure najdete v tématu [řešení na Virtual Machines](virtual-machine-solutions.md). 

Zjistěte, jak můžete vytvářet důvěrné výpočetní aplikace, a to tak, že budete pokračovat na otevřené ukázky sady enklávy SDK na GitHubu. 

> [!div class="nextstepaction"]
> [Sestavování otevřených ukázek sady enklávy SDK](https://github.com/openenclave/openenclave/blob/master/samples/README.md)
