---
title: Rychlý Start – vytvoření virtuálního počítače s důvěrnými informacemi z Azure v Azure Portal
description: Začněte s nasazeními a Naučte se, jak rychle vytvořit virtuální počítač s důvěrným výpočetním prostředím v Azure Portal.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 04/23/2020
ms.author: JenCook
ms.openlocfilehash: 3f0984acd66bd5d6c148be8451938d3152fb9ca7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566664"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-azure-portal"></a>Rychlý Start: nasazení virtuálního počítače s důvěrnými výpočetními prostředími Azure v Azure Portal

Seznamte se s důvěrnými výpočetními prostředími Azure pomocí Azure Portal k vytvoření virtuálního počítače, který je podporovaný procesorem Intel SGX. Potom nainstalujete Open enklávy Software Development Kit (SDK) a nastavte své vývojové prostředí. 

Tento kurz se doporučuje, pokud vás zajímá nasazení důvěrného výpočetního virtuálního počítače s vlastní konfigurací. V opačném případě doporučujeme, abyste provedli [kroky nasazení důvěrného výpočetního virtuálního počítače pro komerční tržiště Microsoftu](quick-create-marketplace.md).


## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) před tím, než začnete.

> [!NOTE]
> Bezplatné zkušební účty nemají přístup k virtuálním počítačům použitým v tomto kurzu. Upgradujte prosím na předplatné s průběžnými platbami.


## <a name="sign-in-to-azure"></a>Přihlášení k Azure

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. V horní části vyberte **vytvořit prostředek**.

1. V podokně **Marketplace** vyberte na levé straně **výpočty** .

1. Vyhledejte a vyberte **virtuální počítač**.

    ![Nasazení virtuálního počítače](media/quick-create-portal/compute-virtual-machine.png)

1. Na cílové stránce virtuálního počítače vyberte **vytvořit**.


## <a name="configure-a-confidential-computing-virtual-machine"></a>Konfigurace pro důvěrný výpočetní virtuální počítač

1. Na kartě **základy** vyberte své **předplatné** a **skupinu prostředků**.

1. Jako **název virtuálního počítače** zadejte název nového virtuálního počítače.

1. Zadejte nebo vyberte tyto hodnoty:

   * **Oblast**: Vyberte oblast Azure, která je pro vás nejvhodnější.

        > [!NOTE]
        > Důvěrné výpočetní virtuální počítače se spouští jenom na specializovaném hardwaru dostupném v konkrétních oblastech. Nejnovější dostupné oblasti pro DCsv2-Series virtuálních počítačů najdete v části [dostupné oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

1. Nakonfigurujte bitovou kopii operačního systému, kterou chcete použít pro virtuální počítač.

    * **Zvolte obrázek**: pro tento kurz vyberte Ubuntu 18,04 LTS. Můžete také vybrat možnost Windows Server 2019, Windows Server 2016 nebo Ubuntu 16,04 LTS. Pokud se rozhodnete tak učinit, budete v tomto kurzu přesměrováni podle potřeby.
    
    * **Přepněte obrázek pro Gen 2**: důvěrné výpočetní virtuální počítače běží jenom na imagí [generace 2](../virtual-machines/generation-2.md) . Ujistěte se, že vybraný obrázek je obrázek 2. generace. Klikněte na kartu **Upřesnit** nad místem, kde konfigurujete virtuální počítač. Posuňte se dolů, dokud nenajdete část s označením "generování virtuálního počítače". Vyberte Obecné 2 a potom se vraťte na kartu **základy** .
    

        ![Karta Upřesnit](media/quick-create-portal/advanced-tab-virtual-machine.png)


        ![Generace virtuálního počítače](media/quick-create-portal/gen2-virtual-machine.png)

    * **Návrat zpět na základní konfiguraci**: Vraťte se na kartu **základy** pomocí navigace v horní části.

1. Zvolte virtuální počítač s důvěrnými výpočetními funkcemi v selektoru velikosti výběrem možnosti **změnit velikost**. V nástroji pro výběr velikosti virtuálního počítače klikněte na **Vymazat všechny filtry**. Zvolte **Přidat filtr**, pro typ filtru vyberte **rodina** a pak vyberte jenom **důvěrné výpočetní** prostředky.

    ![DCsv2-Series virtuálních počítačů](media/quick-create-portal/dcsv2-virtual-machines.png)

    > [!TIP]
    > Měli byste vidět velikosti **DC1s_v2**, **DC2s_v2**, **DC4s_V2** a **DC8_v2**. Toto jsou jediné velikosti virtuálních počítačů, které aktuálně podporují důvěrný výpočetní výkon. [Další informace](virtual-machine-solutions.md).

1. Zadejte následující informace:

   * **Typ ověřování**: Pokud VYTVÁŘÍTE virtuální počítač se systémem Linux, vyberte **veřejný klíč SSH** . 

        > [!NOTE]
        > U ověřování máte na výběr mezi používáním veřejného klíče SSH nebo hesla. SSH je bezpečnější. Pokyny k vygenerování klíče SSH najdete v tématu [Vytvoření klíčů SSH v Linuxu a na Macu pro virtuální počítače s Linuxem v Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Uživatelské jméno**: Zadejte jméno správce pro virtuální počítač.

    * **Veřejný klíč SSH**: Pokud je k dispozici, zadejte svůj veřejný klíč RSA.
    
    * **Heslo**: Pokud je to možné, zadejte heslo pro ověřování.

    * **Veřejné příchozí porty**: zvolte **Povolit vybrané porty** a v seznamu **Vyberte veřejné příchozí porty** vyberte **SSH (22)** a **http (80)** . Pokud nasazujete virtuální počítač s Windows, vyberte **http (80)** a **RDP (3389)**. V tomto rychlém startu je tento krok nezbytný pro připojení k virtuálnímu počítači a dokončení konfigurace Open enklávy SDK. 

     ![Příchozí porty](media/quick-create-portal/inbound-port-virtual-machine.png)


1. Proveďte změny na kartě **disky** .

   * Pokud jste zvolili **DC1s_v2**, **DC2s_v2** **DC4s_V2** virtuální počítač, vyberte typ disku, který je buď **SSD úrovně Standard** nebo **SSD úrovně Premium**. 
   * Pokud jste zvolili **DC8_v2** virtuální počítač, zvolte **SSD úrovně Standard** jako typ disku.

1. Proveďte všechny požadované změny nastavení na následujících kartách nebo ponechte výchozí nastavení.

    * **Sítě**
    * **správy**
    * **Konfigurace hosta**
    * **Značky**

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

:::image type="content" source="media/quick-create-portal/public-ip-virtual-machine.png" alt-text="IP adresa na webu Azure Portal":::

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
wget https://download.01.org/intel-sgx/sgx-dcap/1.9/linux/distro/ubuntu18.04-server/sgx_linux_x64_driver_1.36.2.bin -O sgx_linux_x64_driver.bin
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