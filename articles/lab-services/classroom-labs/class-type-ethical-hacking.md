---
title: Nastavení testovacího prostředí etického hackingu se službami Azure Lab Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak nastavit testovací prostředí pomocí Služby Azure Lab services k výuce etického hackingu.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 2b600edc4c360a2b2990be34e44bb8fbd1c8f721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133180"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Zřídit laboratoř pro výuku etické hacking třídy 
Tento článek vám ukáže, jak nastavit třídu, která se zaměřuje na forenzní stránku etického hackingu. Penetrační testování, což je praxe používaná komunitou etických hackerů, nastává, když se někdo pokusí získat přístup k systému nebo síti, aby prokázal chyby zabezpečení, které může útočník se zlými úmysly zneužít. 

V etické hacking třídy, studenti se mohou naučit moderní techniky pro obranu proti zranitelnosti. Každý student dostane virtuální počítač hostitele Windows Serveru, který má dva vnořené virtuální počítače – jeden virtuální stroj s obrazem [Metasploitable3](https://github.com/rapid7/metasploitable3) a další počítač s obrazem [Kali Linux.](https://www.kali.org/) Metasploitable virtuální stroj se používá pro využití účelů a Kali virtuální stroj poskytuje přístup k nástrojům potřebným k provádění forenzních úkolů.

Tento článek má dvě hlavní části. První část popisuje, jak vytvořit učebnu laboratoře. Druhá část popisuje, jak vytvořit počítač šablony s povolenou vnořenou virtualizací a potřebnými nástroji a obrázky. V tomto případě obraz Metasploitable a kali Linux obraz na počítači, který má Hyper-V povoleno hostit obrázky.

## <a name="lab-configuration"></a>Konfigurace laboratoře
Chcete-li nastavit toto testovací prostředí, budete potřebovat předplatné Azure, abyste mohli začít. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete. Jakmile získáte předplatné Azure, můžete buď vytvořit nový účet testovacího prostředí ve službě Azure Lab Services, nebo použít existující účet. Podívejte se na následující kurz pro vytvoření nového účtu testovacího prostředí: [Kurz pro nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md).

Podle [tohoto kurzu](tutorial-setup-classroom-lab.md) vytvořte nové testovací prostředí a pak použijte následující nastavení:

| Velikost virtuálního počítače | Image |
| -------------------- | ----- | 
| Střední (vnořená virtualizace) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Šablona stroj 

Po vytvoření šablony počítače spusťte počítač a připojte se k němu, abyste dokončili následující tři hlavní úkoly. 
 
1. Nastavte počítač pro vnořenou virtualizaci. Umožňuje všechny příslušné funkce systému Windows, jako je Technologie Hyper-V, a nastavuje síť pro obrázky Hyper-V, aby mohly komunikovat mezi sebou navzájem a s internetem.
2. Nastavte obrázek [Kali](https://www.kali.org/) Linux. Kali je linuxová distribuce, která obsahuje nástroje pro penetrační testování a bezpečnostní audit.
3. Nastavte metasploitelný obraz. V tomto příkladu [metasploitable3](https://github.com/rapid7/metasploitable3) obraz bude použit. Tato bitová kopie je vytvořena tak, aby záměrně měla slabá místa zabezpečení.

Skript, který automatizuje výše uvedené úkoly, je k dispozici na [lab services etické hacking skripty](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking).

### <a name="prepare-template-machine-for-nested-virtualization"></a>Připravit počítač šablony pro vnořenou virtualizaci
Postupujte podle pokynů v [tomto článku](how-to-enable-nested-virtualization-template-vm.md) připravit virtuální počítač šablony pro vnořené virtualizace. 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Nastavení vnořeného virtuálního počítače s Kali Linux Image
Kali je linuxová distribuce, která obsahuje nástroje pro penetrační testování a bezpečnostní audit.

1. Stáhnout obrázek z . [https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)  
    1. Stáhněte si **Kali Linux Hyper-V 64 Bit** pro Hyper-V.
    1. Extrahujte soubor .7z.  Pokud ještě nemáte 7 zip, stáhněte [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html)si jej z . Zapamatujte si umístění extrahované složky, protože ji budete později potřebovat.
2. Otevřete **Správce technologie Hyper-V** z nástroje pro správu.
1. Vyberte **Akce**a pak **vyberte Importovat virtuální počítač**. 
1. Na stránce **Vyhledat složku** průvodce **importem virtuálního počítače** zvolte umístění extrahované složky, která obsahuje bitovou kopii Kali Linux.

    ![Dialogové okno Najít složku](../media/class-type-ethical-hacking/locate-folder.png)
1. Na stránce **Vybrat virtuální stroj** vyberte obrázek Kali Linux.  V tomto případě je obraz **kali-linux-2019.3-hyperv**.

    ![Vyberte obrázek Kali](../media/class-type-ethical-hacking/select-kali-image.png)
1.  Na stránce **Zvolit typ importu** vyberte **Kopírovat virtuální počítač (vytvořit nové jedinečné ID).**

    ![Zvolte typ importu](../media/class-type-ethical-hacking/choose-import-type.png)
1. Přijměte výchozí nastavení v části **Zvolit složky pro soubory virtuálního počítače** a Zvolit složky k uložení stránek **virtuálních pevných disků** a pak vyberte **Další**.
1. Na stránce **Připojit síť** zvolte **LabServicesSwitch** vytvořený dříve v části **Připravit šablonu pro vnořenou virtualizaci** v tomto článku a pak vyberte **Další**.

    ![Připojit síťovou stránku](../media/class-type-ethical-hacking/connect-network.png)
1. Na stránce **Souhrn** vyberte **Dokončit.** Počkejte, dokud nebudou dokončeny operace kopírování a importu. Virtuální stroj Kali Linux bude nyní k dispozici v Hyper-V.
1. Ve **Správci technologie Hyper-V**zvolte**Zahájení** **akce** -> a pak zvolte **Action** -> **Connect** pro připojení k virtuálnímu počítači.  
12. Výchozí uživatelské jméno `root` je a `toor`heslo je . 

    > [!NOTE]
    > Pokud potřebujete obrázek odemknout, stiskněte klávesu CTRL a přetáhněte myš nahoru.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Nastavení vnořeného virtuálního virtuálního movitého virtuálního movitého virtuálního min s metasploitelným obrazem  
Rapid7 Metasploitable image je obraz záměrně nakonfigurován s bezpečnostními chybami. Tento obrázek použijete k testování a hledání problémů. Následující pokyny ukazují, jak používat předem vytvořený obraz Metasploitable. Pokud je však zapotřebí novější verze metasploitelného obrazu, viz [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3).

1. Přejděte [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)na . Vyplňte formulář pro stažení obrázku a vyberte tlačítko **Odeslat.**
1. Vyberte tlačítko **Stáhnout metasploitable nyní.**
1. Po stažení souboru zip extrahujte soubor zip a zapamatujte si umístění.
1. Převeďte extrahovaný soubor vmdk na soubor vhdx, abyste jej mohli použít s technologieí Hyper-V. Chcete-li tak učinit, otevřete prostředí PowerShell s oprávněními správce a přejděte do složky, ve které se soubor vmdk nachází, a postupujte podle následujících pokynů:
    1. Stáhněte si [microsoft virtual machine converter](https://www.microsoft.com/download/details.aspx?id=42497)a po zobrazení výzvy spusťte soubor mvmc_setup.msi.
    1. Naimportujte modul PowerShellu.  Výchozí umístění, ve kterém je modul nainstalován, je C:\Program Files\Microsoft Virtual Machine Converter\

        ```powershell
        Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
        ```
    1. Převeďte vmdk na soubor vhd, který může být použit Hyper-V. Tato operace může trvat několik minut.
    
        ```powershell
        ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath .\Metasploitable.vmdk -DestinationLiteralPath .\Metasploitable.vhdx -VhdType DynamicHardDisk -VhdFormat vhdx
        ```
    1. Zkopírujte nově vytvořené metasploitable.vhdx do c:\Users\Public\Documents\Hyper-V\Virtual Hard Disks\. 
1. Vytvořte nový virtuální počítač Hyper-V.
    1. Otevřete **Správce technologie Hyper-V**.
    1. Zvolte **Akce** -> **Nový** -> **virtuální počítač**.
    1. Na stránce **Před zahájením** **Průvodce novým virtuálním počítačem**klepněte na tlačítko **Další**.
    1. Na stránce **Zadat název a umístění** zadejte **metasploitable** pro **název**a vyberte **Další**.

        ![Průvodce novou image virtuálního virtuálního rozhraní](../media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. Na stránce **Zadat generování** přijměte výchozí hodnoty a vyberte **Další**.
    1. Na stránce **Přiřadit paměť** zadejte **512 MB** pro spouštěcí **paměť**a vyberte **další**. 

        ![Přiřadit stránku paměti](../media/class-type-ethical-hacking/assign-memory-page.png)
    1. Na stránce **Konfigurovat síť** ponechejte připojení jako **Nepřipojené**. Síťový adaptér nastavíte později.
    1. Na stránce **Připojit virtuální pevný disk** vyberte Použít existující virtuální pevný **disk**. Přejděte do umístění souboru **metasploitable.vhdx** vytvořeného v předchozím kroku a vyberte **další**. 

        ![Připojení stránky virtuálního síťového disku](../media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. Na stránce **Průvodce dokončením nového virtuálního počítače** vyberte **Dokončit**.
    1. Po vytvoření virtuálního počítače ho vyberte ve Správci technologie Hyper-V. Ještě nezapínejte stroj.  
    1. Zvolte**Nastavení** **akce** -> .
    1. V dialogovém **okně Nastavení pro metasploitelné** pro vyberte **přidat hardware**. 
    1. Vyberte **starší síťový adaptér**a vyberte **přidat**.

        ![Stránka síťového adaptéru](../media/class-type-ethical-hacking/network-adapter-page.png)
    1. Na stránce **Starší síťový adaptér** vyberte **labservicesswitch** pro nastavení **virtuální přepínač** a vyberte **OK**. LabServicesSwitch byl vytvořen při přípravě počítače šablony pro Hyper-V v **části Připravit šablonu pro vnořenou virtualizaci.**

        ![Stránka staršího síťového adaptéru](../media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. Metasploitable obraz je nyní připraven k použití. Ve **Správci technologie Hyper-V**zvolte**Zahájení** **akce** -> a pak zvolte **Action** -> **Connect** pro připojení k virtuálnímu počítači.  Výchozí uživatelské jméno je **msfadmin** a heslo je **msfadmin**. 


Šablona je nyní aktualizována a má obrázky potřebné pro etické hacking penetrace testování třídy, obraz s nástroji pro testování penetrování a další obrázek s bezpečnostními zranitelnostmi objevit. Obrázek šablony lze nyní publikovat do třídy. Chcete-li šablonu publikovat v testovacím prostředí, vyberte tlačítko **Publikovat** na stránce šablony.
  

## <a name="cost"></a>Náklady  
Pokud chcete odhadnout náklady na tuto testovací prostředí, můžete použít následující příklad: 
 
Pro třídu 25 studentů s 20 hodinami plánovaného času ve třídě a 10 hodinami kvóty pro domácí úkoly nebo úkoly by cena za laboratoř byla: 

25 studentů * (20 + 10) hodin * 55 laboratorních jednotek * 0.01 USD za hodinu = 412.50 USD. 

Další informace o cenách najdete v tématu [Ceny služeb Azure Lab](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Závěr
Tento článek vás provede kroky k vytvoření laboratoře pro etické hacking třídy. Obsahuje kroky k nastavení vnořené virtualizace pro vytvoření dvou virtuálních počítačů uvnitř hostitelského virtuálního počítače pro pronikání testování.

## <a name="next-steps"></a>Další kroky
Další kroky jsou společné pro nastavení libovolného testovacího prostředí:

- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavení plánu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mailové registrační odkazy na studenty](how-to-configure-student-usage.md#send-invitations-to-users). 

