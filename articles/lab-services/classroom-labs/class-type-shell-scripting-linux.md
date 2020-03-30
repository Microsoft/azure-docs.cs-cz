---
title: Nastavení testovacího prostředí prostředí Linux u služby Azure Lab Services | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nastavit testovací prostředí pro výuku skriptování prostředí na Linuxu.
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
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 100a485588c77f6977001dae984b30ebcb1de557
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443546"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Nastavení laboratoře pro výuku skriptování prostředí na Linuxu
Tento článek ukazuje, jak nastavit testovací prostředí pro výuku skriptování prostředí na Linuxu. Skriptování je užitečnou součástí správy systému, která správcům umožňuje vyhnout se opakovaným úlohám. V tomto ukázkovém scénáři třída zahrnuje tradiční bash skripty a rozšířené skripty. Rozšířené skripty jsou skripty, které kombinují bash příkazy a Ruby. Tento přístup umožňuje Ruby předávat data a bash příkazy pro interakci s shell. 

Studenti, kteří tyto skriptovací třídy, získají virtuální stroj Linux, aby se naučili základy Linuxu, a také se seznámili s skriptováním bash shellu. Virtuální počítač S Linuxem je dodáván s povoleným přístupem ke vzdálené ploše a s nainstalovanými textovými editory [gedit](https://help.gnome.org/users/gedit/stable/) a [Visual Studio Code.](https://code.visualstudio.com/)

## <a name="lab-configuration"></a>Konfigurace laboratoře
Chcete-li nastavit toto testovací prostředí, budete potřebovat předplatné Azure, abyste mohli začít. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete. Jakmile budete mít předplatné Azure, můžete buď vytvořit nový účet testovacího prostředí ve službě Azure Lab Services nebo použít existující účet testovacího prostředí. Podívejte se na následující kurz pro vytvoření nového účtu testovacího prostředí: [Kurz na nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md).

Po vytvoření účtu testovacího prostředí povolte v účtu testovacího prostředí následující nastavení: 

| Nastavení účtu laboratoře | Pokyny |
| ----------- | ------------ |  
| Obrázky na marketplace | Povolte bitovou kopii [Ubuntu Server 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) pro použití v rámci vašeho laboratorního účtu. Další informace naleznete [v tématu Specify Marketplace images available to lab creators](specify-marketplace-images.md). | 

Podle [tohoto kurzu](tutorial-setup-classroom-lab.md) vytvořte nové testovací prostředí a použijte následující nastavení:

| Nastavení laboratoře | Hodnota/instrukce | 
| ------------ | ------------------ |
| Velikost virtuálního počítače (VM) | Krátkodobé používání  |
| Obrázek virtuálního virtuálního montova | [Ubuntu Server 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) |
| Povolení připojení ke vzdálené ploše | Povolit. <p>Povolení tohoto nastavení umožní učitelům a studentům připojit se ke svým virtuálním počítačům pomocí vzdálené plochy (RDP). Další informace najdete [v tématu Povolení vzdálené plochy pro virtuální počítače s Linuxem v testovacím prostředí ve službě Azure Lab Services](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm). </p>|


## <a name="install-desktop-and-xrdp"></a>Instalace stolního počítače a xrdpu
Obrázek [Ubuntu Server 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) nemá ve výchozím nastavení nainstalovaný server vzdálené plochy. Postupujte podle pokynů v [článku Instalace a konfigurace vzdálené plochy pro připojení k virtuálnímu počítači SVS v Azure](../../virtual-machines/linux/use-remote-desktop.md) a nainstalujte balíčky, které jsou potřeba v počítači šablony pro připojení pomocí protokolu vzdálené plochy.

## <a name="install-ruby"></a>Instalace Ruby
Ruby je open-source dynamický jazyk, který lze kombinovat s bash skripty. Tato část ukazuje, `apt-get` jak nainstalovat nejnovější verzi [aplikace Ruby](https://www.ruby-lang.org/).

1. Nainstalujte aktualizace spuštěním následujících příkazů:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  Nainstalujte [Ruby](https://www.ruby-lang.org/).  Ruby je open-source dynamický jazyk, který lze kombinovat s bash skripty. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Instalace vývojových nástrojů
V této části se zobrazí postup instalace několika textových editorů. Gedit je výchozí textový editor pro desktopové prostředí gnome. Je navržen jako univerzální textový editor. Visual Studio Code je textový editor, který obsahuje podporu pro ladění a integraci správy zdrojového kódu.

> [!NOTE]
> K dispozici je několik různých textových editorů. Visual Studio Code a gedit jsou pouze dva příklady.

1. Nainstalujte [gedit](https://help.gnome.org/users/gedit/stable/).

    ```bash
    sudo apt-get install gedit
    ```
1. Nainstalujte [kód sady Visual Studio](https://code.visualstudio.com/).  Kód sady Visual Studio lze nainstalovat pomocí úložiště Snap Store.  Alternativní možnosti instalace naleznete v tématu [alternativní soubory ke stažení kódu sady Visual Studio](https://code.visualstudio.com/#alt-downloads).

    ```bash
    sudo snap install vscode --classic 
    ```

    Šablona je nyní aktualizována a obsahuje programovací jazyk a vývojové nástroje potřebné k dokončení testovacího prostředí. Obrázek šablony lze nyní publikovat do testovacího prostředí. Chcete-li šablonu publikovat v testovacím prostředí, vyberte tlačítko **Publikovat** na stránce šablony.  

## <a name="cost"></a>Náklady 
Pokud chcete odhadnout náklady na tuto testovací prostředí, můžete použít následující příklad:
 
Pro třídu 25 studentů s 20 hodinami plánovaného času ve třídě a 10 hodinami kvóty pro domácí úkoly nebo úkoly by cena za laboratoř byla: 

25 studentů * (20 + 10) hodin * 20 laboratorních jednotek * 0.01 USD za hodinu = 150 USD

Další informace o cenách najdete v následujícím dokumentu: [Ceny služeb Azure Lab](https://azure.microsoft.com/pricing/details/lab-services/)Services .

## <a name="conclusion"></a>Závěr
Tento článek vás provede kroky k vytvoření testovacího prostředí pro skriptování tříd. Zatímco tento článek se zaměřil na nastavení skriptovacích nástrojů Ruby na počítači s Linuxem, stejné nastavení lze použít pro jiné skriptovací třídy, jako je Python na Linuxu.

## <a name="next-steps"></a>Další kroky
Další kroky jsou společné pro nastavení libovolného testovacího prostředí:

- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavení plánu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mailové registrační odkazy na studenty](how-to-configure-student-usage.md#send-invitations-to-users). 





