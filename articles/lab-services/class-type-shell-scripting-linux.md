---
title: Nastavení skriptovacího prostředí pro prostředí Linux pomocí Azure Lab Services | Microsoft Docs
description: Naučte se, jak nastavit testovací prostředí pro učení skriptování prostředí v systému Linux.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ba14f2fb5263367014b57741c78d6e509df044b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85444960"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Nastavení testovacího prostředí pro učení skriptování prostředí v systému Linux
V tomto článku se dozvíte, jak nastavit testovací prostředí pro učení skriptování prostředí v systému Linux. Skriptování je užitečnou součástí správy systému, která správcům umožňuje vyhnout se opakovaným úlohám. V tomto ukázkovém scénáři třída pokrývá tradiční skripty bash a rozšířené skripty. Rozšířené skripty jsou skripty, které kombinují příkazy bash a Ruby. Tento přístup umožňuje Ruby předávat data kolem a bash příkazů pro interakci s prostředím. 

Studenti, kteří přebírají tyto třídy skriptování, získají virtuální počítač se systémem Linux, aby se seznámili se základy systému Linux, a také se seznámili s skriptováním prostředí bash. Virtuální počítač se systémem Linux přichází s povoleným přístupem ke vzdálené ploše a s nainstalovanými [gedit](https://help.gnome.org/users/gedit/stable/) a [Visual Studio Codemi](https://code.visualstudio.com/) textovými editory.

## <a name="lab-configuration"></a>Konfigurace testovacího prostředí
K nastavení tohoto testovacího prostředí potřebujete předplatné Azure, abyste mohli začít. Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/). Jakmile budete mít předplatné Azure, můžete buď vytvořit nový účet testovacího prostředí v Azure Lab Services, nebo použít existující účet testovacího prostředí. V následujícím kurzu najdete postup vytvoření nového účtu testovacího prostředí: [kurz nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md).

Po vytvoření účtu testovacího prostředí povolte v účtu testovacího prostředí následující nastavení: 

| Nastavení účtu testovacího prostředí | Pokyny |
| ----------- | ------------ |  
| Image Marketplace | Povolte image Ubuntu serveru 18,04 LTS pro použití v rámci vašeho účtu testovacího prostředí. Další informace najdete v tématu [určení imagí z Marketplace dostupných pro tvůrce testovacích prostředí](specify-marketplace-images.md). | 

Podle [tohoto kurzu](tutorial-setup-classroom-lab.md) vytvořte nové testovací prostředí a použijte následující nastavení:

| Nastavení testovacího prostředí | Hodnota/pokyny | 
| ------------ | ------------------ |
| Velikost virtuálního počítače (VM) | Malý  |
| Image virtuálního počítače | Ubuntu Server 18.04 LTS|
| Povolit připojení ke vzdálené ploše | Aby. <p>Povolením tohoto nastavení umožníte učitelům a studentům připojit se ke svým virtuálním počítačům pomocí vzdálené plochy (RDP). Další informace najdete v tématu [Povolení služby Vzdálená plocha pro virtuální počítače se systémem Linux v testovacím prostředí v Azure Lab Services](how-to-enable-remote-desktop-linux.md). </p>|

## <a name="install-desktop-and-rdp"></a>Instalace desktopu a protokolu RDP
Image Ubuntu serveru 18,04 LTS nemá ve výchozím nastavení nainstalovaný server vzdálené plochy RDP. Postupujte podle pokynů v tématu [instalace a konfigurace vzdálené plochy pro připojení k virtuálnímu počítači se systémem Linux v Azure](../virtual-machines/linux/use-remote-desktop.md) a nainstalujte balíčky, které jsou potřeba na počítači šablony pro připojení přes protokol RDP (Remote Desktop Protocol).

## <a name="install-ruby"></a>Instalace Ruby
Ruby je open source dynamický jazyk, který se dá kombinovat s bash skripty. V této části se dozvíte, jak použít `apt-get` k instalaci nejnovější verze [Ruby](https://www.ruby-lang.org/).

1. Nainstalujte aktualizace spuštěním následujících příkazů:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  Nainstalujte [Ruby](https://www.ruby-lang.org/).  Ruby je open source dynamický jazyk, který se dá kombinovat s bash skripty. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Instalace vývojářských nástrojů
V této části se dozvíte, jak nainstalovat několik textových editorů. Gedit je výchozí textový editor pro desktopové prostředí GNOME. Je navržený jako textový editor pro obecné účely. Visual Studio Code je textový editor, který obsahuje podporu pro ladění a integraci správy zdrojového kódu.

> [!NOTE]
> K dispozici je několik různých textových editorů. Visual Studio Code a gedit jsou pouze dva příklady.

1. Nainstalujte [gedit](https://help.gnome.org/users/gedit/stable/).

    ```bash
    sudo apt-get install gedit
    ```
1. Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).  Visual Studio Code se dá nainstalovat pomocí obchodu s modulem snap-in.  Alternativní možnosti instalace najdete v tématu [Visual Studio Code alternativní soubory ke stažení](https://code.visualstudio.com/#alt-downloads).

    ```bash
    sudo snap install vscode --classic 
    ```

    Šablona je nyní aktualizována a má programovací jazyk i vývojové nástroje potřebné k dokončení testovacího prostředí. Image šablony se teď dají publikovat do testovacího prostředí. Vyberte tlačítko **publikovat** na stránce šablony a publikujte šablonu do testovacího prostředí.  

## <a name="cost"></a>Náklady 
Pokud byste chtěli odhadnout náklady na toto testovací prostředí, můžete použít následující příklad:
 
Pro třídu 25 studentů s 20 hodinami plánovaného času třídy a 10 hodin pro domácí úlohy nebo přiřazení by cena za testovací prostředí byla: 

25 studentů * (20 + 10) hodin × 20 jednotek testovacího prostředí × 0,01 USD za hodinu = 150 USD

Další informace o cenách najdete v následujícím dokumentu: [Azure Lab Services ceny](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Závěr
Tento článek vás vás provedl postupem, jak vytvořit testovací prostředí pro třídy skriptování. I když se tento článek zaměřuje na nastavení nástrojů pro skriptování Ruby na počítači se systémem Linux, lze použít stejné nastavení jako pro jiné třídy skriptování jako Python v systému Linux.

## <a name="next-steps"></a>Další kroky
Další kroky jsou běžné pro nastavení testovacího prostředí:

- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavit plán](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Odkazy na registraci e-mailu studentům](how-to-configure-student-usage.md#send-invitations-to-users). 





