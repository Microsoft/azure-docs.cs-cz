---
title: Nastavení etických laboratoří pro hackery s Azure Lab Services | Microsoft Docs
description: Naučte se, jak nastavit testovací prostředí pomocí Azure Lab Services pro učení etických útoků.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 97cdf13f39cc73ee7f35fb402229469195f1456c
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616418"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Nastavení testovacího prostředí pro výuku etických tříd hackerů

V tomto článku se dozvíte, jak nastavit třídu, která se zaměřuje na forenzníou stranu etických útoků. Testování průniku, praxe používané etickým komunitou pro hackery, nastává, když se někdo pokusí získat přístup k systému nebo síti a Ukázat tak ohrožení zabezpečení, která by škodlivý útočník mohl zneužít.

V etických třídách útoku Students se můžou seznámit s moderními technikami, které chrání před ohrožením zabezpečení. Každý Student získá virtuální počítač hostitele Windows serveru, který má dva vnořené virtuální počítače – jeden virtuální počítač s imagí [Metasploitable3](https://github.com/rapid7/metasploitable3) a další počítač s imagí [Kali Linux](https://www.kali.org/) . Virtuální počítač Metasploitable se používá pro zneužití účely a virtuální počítač Kali poskytuje přístup k nástrojům potřebným ke spouštění úloh forenzní.

Tento článek obsahuje dvě hlavní části. První část obsahuje informace o tom, jak vytvořit prostředí učebny. Druhá část obsahuje informace o tom, jak vytvořit počítač šablony s povolenou vnořenou virtualizací a s potřebnými nástroji a bitovými kopiemi. V tomto případě bitová kopie Metasploitable a image Kali Linux na počítači, který má povolenou technologii Hyper-V pro hostování imagí.

## <a name="lab-configuration"></a>Konfigurace testovacího prostředí

K nastavení tohoto testovacího prostředí potřebujete předplatné Azure, abyste mohli začít. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/). Jakmile získáte předplatné Azure, můžete buď vytvořit nový účet testovacího prostředí v Azure Lab Services nebo použít existující účet. V následujícím kurzu najdete postup vytvoření nového účtu testovacího prostředí: [kurz nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md).

Podle [tohoto kurzu](tutorial-setup-classroom-lab.md) vytvořte nové testovací prostředí a pak použijte následující nastavení:

| Velikost virtuálního počítače | Obrázek |
| -------------------- | ----- |
| Střední (vnořená virtualizace) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Počítač šablony

Po vytvoření počítače s šablonou spusťte počítač a připojte se k němu, abyste mohli dokončit následující tři hlavní úlohy.

1. Nastavte počítač pro vnořenou virtualizaci. Povoluje všechny příslušné funkce systému Windows, jako je například technologie Hyper-V, a nastavuje síť pro Image Hyper-V, aby bylo možné komunikovat mezi sebou a internetem.
2. Nastavte Image [Kali](https://www.kali.org/) Linux. Kali je distribuce systému Linux, která obsahuje nástroje pro testování průniku a audit zabezpečení.
3. Nastavte bitovou kopii Metasploitable. V tomto příkladu se použije obrázek [Metasploitable3](https://github.com/rapid7/metasploitable3) . Tato bitová kopie se vytvoří pro účely ohrožení zabezpečení.

Zbývající část tohoto článku se věnuje ručním krokům k dokončení úkolů uvedených výše.  Alternativně můžete spustit [skripty služby Lab Services technologie Hyper-V](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) a [etickými skripty pro hackery služby Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking).

### <a name="prepare-template-machine-for-nested-virtualization"></a>Připravit počítač šablony pro vnořenou virtualizaci

Postupujte podle pokynů a [Povolte vnořenou virtualizaci](how-to-enable-nested-virtualization-template-vm.md) pro přípravu virtuálního počítače šablony pro vnořenou virtualizaci.

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Nastavení vnořeného virtuálního počítače s imagí Kali Linux

Kali je distribuce systému Linux, která obsahuje nástroje pro testování průniku a audit zabezpečení.

1. Stáhněte si image z [urážlivých imagí virtuálních počítačů s Kali Linuxem zabezpečení](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/).  Mějte na paměti, že na stránce pro stažení se zaznamenalo výchozí uživatelské jméno a heslo.
    1. Stáhněte si image **Kali Linux VMware 64-bit (7z)** pro VMware.
    1. Extrahujte soubor. 7z.  Pokud ještě nemáte 7 zip, Stáhněte si ho z [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html) . Zapamatujte si umístění extrahované složky, protože ji budete potřebovat později.
1. Převeďte extrahovaný soubor VMDK na soubor VHDX, abyste mohli použít soubor VHDX s technologií Hyper-V. K dispozici je několik nástrojů pro převod imagí VMware do imagí technologie Hyper-V.  Budeme používat [převaděč STARWIND V2V](https://www.starwindsoftware.com/starwind-v2v-converter).  Pokud ho chcete stáhnout, přejděte na [stránku pro stažení převaděče V2V StarWind](https://www.starwindsoftware.com/starwind-v2v-converter#download).
    1. Spusťte **převaděč STARWIND V2V**.
    1. Na stránce **Výběr umístění obrázku, který se má převést** zvolte **místní soubor**.  Vyberte **Další**.
    1. Na stránce **zdrojový obrázek** přejděte na a vyberte soubor VMDK Kali Linux extrahovaný v předchozím kroku pro nastavení **názvu souboru** .  Soubor bude ve formátu Kali-Linux-{Version}-VMware-amd64. vmdk.  Vyberte **Další**.
    1. V části **Vybrat umístění cílové image** zvolte **místní soubor**.  Vyberte **Další**.
    1. Na stránce **Vybrat formát cílového obrázku** zvolte **VHD/VHDX**.  Vyberte **Další**.
    1. Na stránce **Výběr možnosti pro formát obrázku VHD/VHDX** zvolte **bitovou kopii VHDX**, kterou lze zvětšit.  Vyberte **Další**.
    1. Na stránce **Vybrat název cílového souboru** přijměte výchozí název souboru.  Vyberte **převést**.
    1. Na stránce **převádění** počkejte na převedení obrázku.  To může trvat několik minut.  Po dokončení převodu vyberte **Dokončit** .
1. Vytvořte nový virtuální počítač s technologií Hyper-V.
    1. Otevřete **Správce technologie Hyper-V**.
    1. Vyberte **Akce**  ->  **Nový**  ->  **virtuální počítač**.
    1. Na stránce **než začnete** v **Průvodci novým virtuálním počítačem** vyberte **Další**.
    1. Na stránce **zadat název a umístění** zadejte **Kali-Linux** pro **název** a vyberte **Další**.
    1. Na stránce **zadat generaci** přijměte výchozí hodnoty a vyberte **Další**.
    1. Na stránce **přiřadit paměť** zadejte **2048 MB** pro **Spouštěcí paměť** a vyberte **Další**.
    1. Na stránce **Konfigurovat síť** nechejte připojení **nepřipojené**. Síťový adaptér budete muset nastavit později.
    1. Na stránce **připojit virtuální pevný disk** vyberte **použít existující virtuální pevný disk**. Přejděte do umístění souboru **Kali-Linux-{Version} – VMware-amd64. vmdk** , který jste vytvořili v předchozím kroku, a vyberte **Další**.
    1. Na stránce **dokončení Průvodce novým virtuálním počítačem** vyberte **Dokončit**.
    1. Po vytvoření virtuálního počítače ho vyberte ve Správci technologie Hyper-V. Tento počítač ještě nezapínejte.  
    1. Vyberte   ->  **Nastavení** akce.
    1. V dialogovém okně **nastavení pro Kali-Linux** pro vyberte **Přidat hardware**.
    1. Vyberte možnost **starší síťový adaptér** a vyberte **Přidat**.
    1. Na stránce **starší síťový adaptér** vyberte pro nastavení **virtuálního přepínače** **LabServicesSwitch** a vyberte **OK**. LabServicesSwitch byl vytvořen při přípravě počítače šablony pro technologii Hyper-V v části **připravit šablonu pro vnořenou virtualizaci** .
    1. Bitová kopie Kali-Linux je teď připravená k použití. Ve **Správci technologie Hyper-V** zvolte **Akce**  ->  **Spustit** a pak   ->  pro připojení k virtuálnímu počítači zvolte akce **připojit** .  Výchozí uživatelské jméno je **Kali** a heslo je **Kali**.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Nastavení vnořeného virtuálního počítače s imagí Metasploitable  

Obrázek Metasploitable Rapid7 je záměrně nakonfigurovaný jako obrázek s chybami zabezpečení. Tento obrázek použijete k otestování a hledání problémů. Následující pokyny ukazují, jak používat předem vytvořenou image Metasploitable. Pokud je však potřeba novější verze image Metasploitable, přečtěte si téma [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3) .

1. Stáhněte si bitovou kopii Metasploitable.
    1. Přejděte na [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html) . Vyplňte formulář pro stažení obrázku a vyberte tlačítko **Odeslat** .
    2. Klikněte na tlačítko **Stáhnout Metasploitable nyní** .
    3. Po stažení souboru ZIP rozbalte soubor zip a zapamatujte si umístění souboru Metasploitable. vmdk.
1. Převeďte extrahovaný soubor VMDK na soubor VHDX, abyste mohli použít soubor VHDX s technologií Hyper-V. K dispozici je několik nástrojů pro převod imagí VMware do imagí technologie Hyper-V.  Znovu použijeme [převaděč STARWIND V2V](https://www.starwindsoftware.com/starwind-v2v-converter) .  Pokud ho chcete stáhnout, přejděte na [stránku pro stažení převaděče V2V StarWind](https://www.starwindsoftware.com/starwind-v2v-converter#download).
    1. Spusťte **převaděč STARWIND V2V**.
    1. Na stránce **Výběr umístění obrázku, který se má převést** zvolte **místní soubor**.  Vyberte **Další**.
    1. Na stránce **zdrojový obrázek** přejděte na a vyberte Metasploitable. vmdk extrahovaný v předchozím kroku pro nastavení **název souboru** .  Vyberte **Další**.
    1. V části **Vybrat umístění cílové image** zvolte **místní soubor**.  Vyberte **Další**.
    1. Na stránce **Vybrat formát cílového obrázku** zvolte **VHD/VHDX**.  Vyberte **Další**.
    1. Na stránce **Výběr možnosti pro formát obrázku VHD/VHDX** zvolte **bitovou kopii VHDX**, kterou lze zvětšit.  Vyberte **Další**.
    1. Na stránce **Vybrat název cílového souboru** přijměte výchozí název souboru.  Vyberte **převést**.
    1. Na stránce **převádění** počkejte na převedení obrázku.  To může trvat několik minut.  Po dokončení převodu vyberte **Dokončit** .
1. Vytvořte nový virtuální počítač s technologií Hyper-V.
    1. Otevřete **Správce technologie Hyper-V**.
    1. Vyberte **Akce**  ->  **Nový**  ->  **virtuální počítač**.
    1. Na stránce **než začnete** v **Průvodci novým virtuálním počítačem** vyberte **Další**.
    1. Na stránce **zadat název a umístění** zadejte **Metasploitable** pro **název** a vyberte **Další**.

        ![Průvodce vytvořením bitové kopie virtuálního počítače](./media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. Na stránce **zadat generaci** přijměte výchozí hodnoty a vyberte **Další**.
    1. Na stránce **přiřadit paměť** zadejte **512 MB** pro **Spouštěcí paměť** a vyberte **Další**.

        ![Stránka přiřazení paměti](./media/class-type-ethical-hacking/assign-memory-page.png)
    1. Na stránce **Konfigurovat síť** nechejte připojení **nepřipojené**. Síťový adaptér budete muset nastavit později.
    1. Na stránce **připojit virtuální pevný disk** vyberte **použít existující virtuální pevný disk**. Přejděte do umístění souboru **metasploitable. vhdx** , který jste vytvořili v předchozím kroku, a vyberte **Další**.

        ![Stránka připojit virtuální síťový disk](./media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. Na stránce **dokončení Průvodce novým virtuálním počítačem** vyberte **Dokončit**.
    1. Po vytvoření virtuálního počítače ho vyberte ve Správci technologie Hyper-V. Tento počítač ještě nezapínejte.  
    1. Vyberte   ->  **Nastavení** akce.
    1. V dialogovém okně **nastavení pro Metasploitable** pro vyberte možnost **Přidat hardware**.
    1. Vyberte možnost **starší síťový adaptér** a vyberte **Přidat**.

        ![Stránka síťového adaptéru](./media/class-type-ethical-hacking/network-adapter-page.png)
    1. Na stránce **starší síťový adaptér** vyberte pro nastavení **virtuálního přepínače** **LabServicesSwitch** a vyberte **OK**. LabServicesSwitch byl vytvořen při přípravě počítače šablony pro technologii Hyper-V v části **připravit šablonu pro vnořenou virtualizaci** .

        ![Stránka staršího síťového adaptéru](./media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. Image Metasploitable je teď připravená k použití. Ve **Správci technologie Hyper-V** zvolte **Akce**  ->  **Spustit** a pak   ->  pro připojení k virtuálnímu počítači zvolte akce **připojit** .  Výchozí uživatelské jméno je **msfadmin** a heslo je **msfadmin**.

Šablona se teď aktualizovala a obsahuje image pro etický testovací třídu průniku hackerů, image s nástroji pro testování průniku a další image s chybami zabezpečení ke zjišťování. Image šablony se teď dají publikovat do třídy. Vyberte tlačítko **publikovat** na stránce šablony a publikujte šablonu do testovacího prostředí.

## <a name="cost"></a>Cost  

Pokud byste chtěli odhadnout náklady na toto testovací prostředí, můžete použít následující příklad:

Pro třídu 25 studentů s 20 hodinami plánovaného času třídy a 10 hodin pro domácí úlohy nebo přiřazení by cena za testovací prostředí byla:

25 studentů \* (20 + 10) hodin \* 55 testovacích jednotek \* 0,01 USD za hodinu = 412,50 USD

>[!IMPORTANT]
>Odhad nákladů slouží pouze jako příklad pro účely. Aktuální podrobnosti o cenách najdete v tématu [Azure Lab Services ceny](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Závěr

Tento článek vás vás provedl postupem, jak vytvořit testovací prostředí pro etický třídu hackerů. Obsahuje postup pro nastavení vnořené virtualizace pro vytváření dvou virtuálních počítačů uvnitř hostitelského virtuálního počítače pro pronikání testování.

## <a name="next-steps"></a>Další kroky

Další kroky jsou běžné pro nastavení testovacího prostředí:

- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavit plán](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Odkazy na registraci e-mailu studentům](how-to-configure-student-usage.md#send-invitations-to-users).
