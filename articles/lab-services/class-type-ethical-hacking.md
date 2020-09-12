---
title: Nastavení etických laboratoří pro hackery s Azure Lab Services | Microsoft Docs
description: Naučte se, jak nastavit testovací prostředí pomocí Azure Lab Services pro učení etických útoků.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5134a7db824bad69f42a4051319479f712051446
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297582"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Nastavení testovacího prostředí pro výuku etických tříd hackerů 
V tomto článku se dozvíte, jak nastavit třídu, která se zaměřuje na forenzníou stranu etických útoků. Testování průniku, praxe používané etickým komunitou pro hackery, nastává, když se někdo pokusí získat přístup k systému nebo síti a Ukázat tak ohrožení zabezpečení, která by škodlivý útočník mohl zneužít. 

V etických třídách útoku Students se můžou seznámit s moderními technikami, které chrání před ohrožením zabezpečení. Každý Student získá virtuální počítač hostitele Windows serveru, který má dva vnořené virtuální počítače – jeden virtuální počítač s imagí [Metasploitable3](https://github.com/rapid7/metasploitable3) a další počítač s imagí [Kali Linux](https://www.kali.org/) . Virtuální počítač Metasploitable se používá pro zneužití účely a virtuální počítač Kali poskytuje přístup k nástrojům potřebným ke spouštění úloh forenzní.

Tento článek obsahuje dvě hlavní části. První část obsahuje informace o tom, jak vytvořit prostředí učebny. Druhá část obsahuje informace o tom, jak vytvořit počítač šablony s povolenou vnořenou virtualizací a s potřebnými nástroji a bitovými kopiemi. V tomto případě bitová kopie Metasploitable a image Kali Linux na počítači, který má povolenou technologii Hyper-V pro hostování imagí.

## <a name="lab-configuration"></a>Konfigurace testovacího prostředí
K nastavení tohoto testovacího prostředí potřebujete předplatné Azure, abyste mohli začít. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/). Jakmile získáte předplatné Azure, můžete buď vytvořit nový účet testovacího prostředí v Azure Lab Services nebo použít existující účet. V následujícím kurzu najdete postup vytvoření nového účtu testovacího prostředí: [kurz nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md).

Podle [tohoto kurzu](tutorial-setup-classroom-lab.md) vytvořte nové testovací prostředí a pak použijte následující nastavení:

| Velikost virtuálního počítače | Image |
| -------------------- | ----- | 
| Střední (vnořená virtualizace) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Počítač šablony 

Po vytvoření počítače s šablonou spusťte počítač a připojte se k němu, abyste mohli dokončit následující tři hlavní úlohy. 
 
1. Nastavte počítač pro vnořenou virtualizaci. Povoluje všechny příslušné funkce systému Windows, jako je například technologie Hyper-V, a nastavuje síť pro Image Hyper-V, aby bylo možné komunikovat mezi sebou a internetem.
2. Nastavte Image [Kali](https://www.kali.org/) Linux. Kali je distribuce systému Linux, která obsahuje nástroje pro testování průniku a audit zabezpečení.
3. Nastavte bitovou kopii Metasploitable. V tomto příkladu se použije obrázek [Metasploitable3](https://github.com/rapid7/metasploitable3) . Tato bitová kopie se vytvoří pro účely ohrožení zabezpečení.

Skript, který automatizuje úkoly popsané výše, je k dispozici v části [laboratorní služby – skripty pro hackery](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking).

### <a name="prepare-template-machine-for-nested-virtualization"></a>Připravit počítač šablony pro vnořenou virtualizaci
Postupujte podle pokynů v [tomto článku](how-to-enable-nested-virtualization-template-vm.md) a připravte svůj virtuální počítač šablony pro vnořenou virtualizaci. 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Nastavení vnořeného virtuálního počítače s imagí Kali Linux
Kali je distribuce systému Linux, která obsahuje nástroje pro testování průniku a audit zabezpečení.

1. Stáhnout obrázek z [https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/) .  
    1. Stáhněte si **bit Kali Linux Hyper-v 64** pro Hyper-v.
    1. Extrahujte soubor. 7z.  Pokud ještě nemáte 7 zip, Stáhněte si ho z [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html) . Zapamatujte si umístění extrahované složky, protože ji budete potřebovat později.
2. Otevřete **Správce technologie Hyper-V** z nástrojů pro správu.
1. Vyberte **akci**a pak vyberte **importovat virtuální počítač**. 
1. Na stránce **najít složku** v průvodci **importem virtuálního počítače** vyberte umístění extrahované složky, která obsahuje image Kali Linux.

    ![Dialogové okno najít složku](./media/class-type-ethical-hacking/locate-folder.png)
1. Na stránce **Vybrat virtuální počítač** vyberte Image Kali Linux.  V takovém případě je bitová kopie **Kali-Linux-2019,3-HyperV**.

    ![Vybrat Kali obrázek](./media/class-type-ethical-hacking/select-kali-image.png)
1.  Na stránce **Vybrat typ importu** vyberte možnost **zkopírovat virtuální počítač (vytvořit nové jedinečné ID)**.

    ![Zvolit typ importu](./media/class-type-ethical-hacking/choose-import-type.png)
1. Přijměte výchozí nastavení pro **možnost vybrat složky pro soubory virtuálního počítače** a **Zvolte složky pro uložení stránek virtuálních pevných disků** a pak vyberte **Další**.
1. Na stránce **připojit síť** zvolte **LabServicesSwitch** vytvořené dříve v části **připravit šablonu pro vnořenou virtualizaci** tohoto článku a pak vyberte **Další**.

    ![Stránka připojit síť](./media/class-type-ethical-hacking/connect-network.png)
1. Na stránce **Souhrn** vyberte **Dokončit** . Počkejte, dokud nebudou dokončeny operace kopírování a importu. Virtuální počítač se systémem Kali Linux bude nyní k dispozici v Hyper-V.
1. Ve **Správci technologie Hyper-V**zvolte **Akce**  ->  **Spustit**a pak **Action**  ->  pro připojení k virtuálnímu počítači zvolte akce**připojit** .  
12. Výchozí uživatelské jméno je `root` a heslo `toor` . 

    > [!NOTE]
    > Pokud potřebujete odemknout obrázek, stiskněte klávesu CTRL a přetáhněte ukazatel myši nahoru.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Nastavení vnořeného virtuálního počítače s imagí Metasploitable  
Obrázek Metasploitable Rapid7 je záměrně nakonfigurovaný jako obrázek s chybami zabezpečení. Tento obrázek použijete k otestování a hledání problémů. Následující pokyny ukazují, jak používat předem vytvořenou image Metasploitable. Pokud je však potřeba novější verze image Metasploitable, přečtěte si téma [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3) .

1. Přejděte na [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html) . Vyplňte formulář pro stažení obrázku a vyberte tlačítko **Odeslat** .
1. Klikněte na tlačítko **Stáhnout Metasploitable nyní** .
1. Po stažení souboru ZIP rozbalte soubor zip a zapamatujte si umístění.
1. Převeďte extrahovaný soubor VMDK na soubor VHDX, abyste mohli používat technologii Hyper-V. Provedete to tak, že otevřete PowerShell s oprávněními správce a přejdete do složky, kde se nachází soubor VMDK, a pak postupujte podle těchto pokynů:
    1. Stáhněte si [Microsoft VM Converter](https://download.microsoft.com/download/9/1/E/91E9F42C-3F1F-4AD9-92B7-8DD65DA3B0C2/mvmc_setup.msi)a po zobrazení výzvy spusťte mvmc_setup.msi soubor.
    1. Naimportujte modul PowerShellu.  Výchozí umístění, ve kterém je modul nainstalovaný, je C:\Program Files\Microsoft Virtual Machine Converter.

        ```powershell
        Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
        ```
    1. Převeďte VMDK na soubor VHD, který může používat technologie Hyper-V. Tato operace může trvat několik minut.
    
        ```powershell
        ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath .\Metasploitable.vmdk -DestinationLiteralPath .\Metasploitable.vhdx -VhdType DynamicHardDisk -VhdFormat vhdx
        ```
    1. Zkopírujte nově vytvořený soubor metasploitable. vhdx do C:\Users\Public\Documents\Hyper-V\Virtual pevného Disks\. 
1. Vytvořte nový virtuální počítač s technologií Hyper-V.
    1. Otevřete **Správce technologie Hyper-V**.
    1. Vyberte **Akce**  ->  **Nový**  ->  **virtuální počítač**.
    1. Na stránce **než začnete** v **Průvodci novým virtuálním počítačem**klikněte na **Další**.
    1. Na stránce **zadat název a umístění** zadejte **Metasploitable** pro **název**a vyberte **Další**.

        ![Průvodce vytvořením bitové kopie virtuálního počítače](./media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. Na stránce **zadat generaci** přijměte výchozí hodnoty a vyberte **Další**.
    1. Na stránce **přiřadit paměť** zadejte **512 MB** pro **Spouštěcí paměť**a vyberte **Další**. 

        ![Stránka přiřazení paměti](./media/class-type-ethical-hacking/assign-memory-page.png)
    1. Na stránce **Konfigurovat síť** nechejte připojení **nepřipojené**. Síťový adaptér budete muset nastavit později.
    1. Na stránce **připojit virtuální pevný disk** vyberte **použít existující virtuální pevný disk**. Přejděte do umístění souboru **metasploitable. vhdx** , který jste vytvořili v předchozím kroku, a vyberte **Další**. 

        ![Stránka připojit virtuální síťový disk](./media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. Na stránce **dokončení Průvodce novým virtuálním počítačem** vyberte **Dokončit**.
    1. Po vytvoření virtuálního počítače ho vyberte ve Správci technologie Hyper-V. Tento počítač ještě nezapínejte.  
    1. Vyberte **Action**  ->  **Nastavení**akce.
    1. V dialogovém okně **nastavení pro Metasploitable** pro vyberte možnost **Přidat hardware**. 
    1. Vyberte možnost **starší síťový adaptér**a vyberte **Přidat**.

        ![Stránka síťového adaptéru](./media/class-type-ethical-hacking/network-adapter-page.png)
    1. Na stránce **starší síťový adaptér** vyberte pro nastavení **virtuálního přepínače** **LabServicesSwitch** a vyberte **OK**. LabServicesSwitch byl vytvořen při přípravě počítače šablony pro technologii Hyper-V v části **připravit šablonu pro vnořenou virtualizaci** .

        ![Stránka staršího síťového adaptéru](./media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. Image Metasploitable je teď připravená k použití. Ve **Správci technologie Hyper-V**zvolte **Akce**  ->  **Spustit**a pak **Action**  ->  pro připojení k virtuálnímu počítači zvolte akce**připojit** .  Výchozí uživatelské jméno je **msfadmin** a heslo je **msfadmin**. 


Šablona se teď aktualizovala a obsahuje image pro etický testovací třídu průniku hackerů, image s nástroji pro testování průniku a další image s chybami zabezpečení ke zjišťování. Image šablony se teď dají publikovat do třídy. Vyberte tlačítko **publikovat** na stránce šablony a publikujte šablonu do testovacího prostředí.
  

## <a name="cost"></a>Cost  
Pokud byste chtěli odhadnout náklady na toto testovací prostředí, můžete použít následující příklad: 
 
Pro třídu 25 studentů s 20 hodinami plánovaného času třídy a 10 hodin pro domácí úlohy nebo přiřazení by cena za testovací prostředí byla: 

25 Students * (20 + 10) hod. × 55 jednotek testovacího prostředí × 0,01 USD za hodinu = 412,50 USD. 

Další informace o cenách najdete v tématu [Azure Lab Services ceny](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Závěr
Tento článek vás vás provedl postupem, jak vytvořit testovací prostředí pro etický třídu hackerů. Obsahuje postup pro nastavení vnořené virtualizace pro vytváření dvou virtuálních počítačů uvnitř hostitelského virtuálního počítače pro pronikání testování.

## <a name="next-steps"></a>Další kroky
Další kroky jsou běžné pro nastavení testovacího prostředí:

- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavit plán](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Odkazy na registraci e-mailu studentům](how-to-configure-student-usage.md#send-invitations-to-users). 

