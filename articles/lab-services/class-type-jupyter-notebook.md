---
title: Nastavení testovacího prostředí pro učení datových věd pomocí poznámkových bloků Python a Jupyter | Microsoft Docs
description: Naučte se, jak nastavit testovací prostředí pro učení datových věd pomocí poznámkových bloků Python a Jupyter.
author: emaher
ms.topic: article
ms.date: 09/29/2020
ms.author: enewman
ms.openlocfilehash: d4034f889334bcf1e4eaa3710a32db60b6a9936b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94648017"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Nastavení testovacího prostředí pro učení datových věd pomocí poznámkových bloků Python a Jupyter
Tento článek popisuje, jak nastavit virtuální počítač šablony (VM) v testovacích službách pomocí nástrojů, které jsou potřeba pro učení studentů, jak používat [notebooky Jupyter](http://jupyter-notebook.readthedocs.io/)a jak se můžou studenti připojit ke svým notebookům na svých virtuálních počítačích.

Jupyter Poznámkový blok je open source projekt, který umožňuje snadno kombinovat text a spustitelný zdrojový kód Pythonu na jednom plátně s názvem Poznámkový blok. Spuštění poznámkového bloku má za následek lineární záznam vstupů a výstupů. Tyto výstupy můžou zahrnovat text, tabulky informací, bodové a další.

## <a name="set-up-the-lab"></a>Nastavení testovacího prostředí

### <a name="lab-configuration"></a>Konfigurace testovacího prostředí
K nastavení tohoto testovacího prostředí potřebujete přístup k předplatnému Azure a účtu testovacího prostředí. Prodiskutujte se správcem vaší organizace a zjistěte, jestli můžete získat přístup k existujícímu předplatnému Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).

Jakmile budete mít předplatné Azure, vytvořte nový účet testovacího prostředí v Azure Lab Services podle pokynů v tomto kurzu: [Nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md). Můžete použít i existující účet testovacího prostředí.

### <a name="lab-account-settings"></a>Nastavení účtu testovacího prostředí
Povolte nastavení popsaná v následující tabulce pro účet testovacího prostředí. Další informace o povolení imagí na Marketplace najdete v tématu [určení imagí Marketplace dostupných pro tvůrce testovacích prostředí](specify-marketplace-images.md).

| Nastavení účtu testovacího prostředí | Pokyny |
| ------------------- | ------------ |
| Image z Marketplace | V rámci účtu testovacího prostředí povolte jeden z Azure Marketplace imagí na základě potřeb vašeho operačního systému: <br/><ul><li>Data Science Virtual Machine – Windows Server 2019</li><li>Data Science Virtual Machine – Ubuntu 18,04</li></ul> |

> [!NOTE]
> Tento článek používá image virtuálních počítačů pro datové vědy, které jsou dostupné na webu Azure Marketplace, protože jsou předem nakonfigurované s Jupyter Notebook. Tyto obrázky ale také obsahují mnoho dalších nástrojů pro vývoj a modelování pro datové vědy. Pokud nechcete, aby tyto další nástroje a nemusely mít zjednodušenou instalaci se pouze Jupyter poznámkami, vytvořte vlastní image virtuálního počítače. Příklad [instalace JupyperHub v Azure](http://tljh.jupyter.org/en/latest/install/azure.html). Jakmile je vlastní image vytvořená, můžete ji nahrát do galerie sdílených imagí a použít tak image v Azure Lab Services. Přečtěte si další informace o [použití Galerie sdílených imagí v Azure Lab Services](how-to-attach-detach-shared-image-gallery.md). 

### <a name="lab-settings"></a>Nastavení testovacího prostředí
Nakonfigurujte nastavení **velikosti virtuálního počítače** a **bitové kopie virtuálního počítače** , jak je znázorněno v následující tabulce při nastavování testovacího prostředí pro učebnu. Pokyny k vytvoření testovacího prostředí učebny najdete v tématu [Nastavení testovacího prostředí pro učebnu](tutorial-setup-classroom-lab.md).

| Nastavení testovacího prostředí | Hodnota/pokyny |
| ------------ | ------------------ | 
| Velikost virtuálního počítače | <p>Velikost, kterou si vyberete, závisí na zatížení, které chcete spustit:</p><ul><li>Malé nebo střední – dobré pro základní nastavení přístupu k poznámkovým blokům Jupyter</li><li>Malý grafický procesor (COMPUTE) – nejvhodnější pro aplikace náročné na výpočetní výkon a pro náročné síťové služby, jako jsou umělá inteligentní a obsáhlý Learning</li></ul> | 
| Image virtuálního počítače | <p>V závislosti na potřebách operačního systému vyberte jednu z následujících imagí:</p><ul><li>[Data Science Virtual Machine – Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019)</li><li>[Data Science Virtual Machine – Ubuntu 18,04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)</li></ul> |


### <a name="template-virtual-machine"></a>Virtuální počítač šablony
Po vytvoření testovacího prostředí se vytvoří virtuální počítač šablony na základě velikosti virtuálního počítače a image, kterou jste zvolili. Nakonfigurujete virtuální počítač šablony se všemi, které chcete poskytnout studentům pro tuto třídu. Další informace najdete v tématu [Správa virtuálního počítače šablony](how-to-create-manage-template.md). 

Ve výchozím nastavení jsou Data Science VM obrázky dodávány s mnoha rozhraními pro datové vědy a nástroji vyžadovanými pro tento typ třídy. Mezi tyto bitové kopie patří například:

- [Jupyter poznámkové bloky](http://jupyter-notebook.readthedocs.io/): webová aplikace, která umožňuje datovým vědcům přijímat nezpracovaná data, provádět výpočty a zobrazovat výsledky ve stejném prostředí. Spustí se místně na virtuálním počítači šablony.  
- [Visual Studio Code](https://code.visualstudio.com/): integrované vývojové prostředí (IDE), které poskytuje komplexní interaktivní prostředí při psaní a testování poznámkového bloku. Další informace najdete v tématu [práce s Jupyter poznámkovým blokem v Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support).

### <a name="provide-notebooks-for-the-class"></a>Poskytněte poznámkové bloky pro třídu.
Dalším úkolem je poskytnout studentům pomocí poznámkových bloků, které chcete používat. Pokud chcete zadat vlastní poznámkové bloky, můžete poznámkové bloky ukládat lokálně na virtuálním počítači šablony. 

Pokud chcete použít ukázkové poznámkové bloky z Azure Machine Learning, přečtěte si téma [Konfigurace prostředí pomocí poznámkových bloků Jupyter](../machine-learning/how-to-configure-environment.md#jupyter). 

### <a name="optional-enable-graphical-desktop-for-linux"></a>Volitelné: povolení grafické plochy pro Linux 
**Data Science Virtual Machine – image Ubuntu** je už zřízená s X2GO serverem a je připravená přijmout připojení klientů. Při nastavování virtuálního počítače šablony se nevyžadují žádné další kroky. 

### <a name="publish-the-template-machine"></a>Publikování počítače šablony
Když publikujete šablonu, každý student zaregistrovaný do vašeho testovacího prostředí získá kopii šablony virtuálního počítače se všemi místními nástroji a poznámkami, které jste si v něm nastavili.

## <a name="how-students-connect-to-jupyter-notebooks"></a>Jak se studenti připojí k poznámkovým blokům Jupyter?
Po publikování šablony budou mít každý student přístup k virtuálnímu počítači, který je součástí všeho, co jste předtím nakonfigurovali pro třídu, včetně notebooků Jupyter. Následující části ukazují různé způsoby, jak se studenti připojit k poznámkovým blokům Jupyter. 

### <a name="for-windows-vms"></a>Pro virtuální počítače s Windows
Pokud jste poskytli studenty s virtuálními počítači s Windows, musí se připojit ke svým virtuálním počítačům a používat poznámkové bloky Jupyter, které jsou k dispozici místně. 

Pro připojení k virtuálnímu počítači s Windows může student použít připojení ke vzdálené ploše (RDP). Podrobný postup najdete v tématu [Jak získat přístup k prostředí učebny](how-to-use-classroom-lab.md). 

Student používající Mac nebo Chromebook se může podle pokynů z následujících článků připojit k virtuálnímu počítači s Windows pro datové vědy. 

- [Připojení k virtuálnímu počítači pomocí protokolu RDP na Macu](connect-virtual-machine-mac-remote-desktop.md)
- [Připojení k virtuálnímu počítači pomocí protokolu RDP na Chromebook](connect-virtual-machine-chromebook-remote-desktop.md)

### <a name="for-linux-vms"></a>Pro virtuální počítače s Linuxem
Pokud jste poskytli studenty s virtuálními počítači se systémem Linux, můžete k připojení ke svým Jupyter poznámkovým blokům na virtuálních počítačích použít několik možností studentů:

- Přístup k poznámkovým blokům Jupyter místně po připojení k virtuálnímu počítači
    - SSH k virtuálnímu počítači pro relace Terminálové služby
     - X2Go připojení k virtuálnímu počítači pro grafické relace
- Pomocí tunelového propojení SSH se připojte z místního počítače studenta přímo k serveru Jupyter na VIRTUÁLNÍm počítači. 

Následující části obsahují podrobné informace o těchto způsobech, jak se připojit k poznámkovým blokům Jupyter. 

#### <a name="ssh-to-virtual-machine"></a>SSH k virtuálnímu počítači
Studenti se můžou přes relaci Terminálové služby připojit přes SSH k virtuálním počítačům Linux. Podrobný postup najdete v tématu [Jak získat přístup k prostředí učebny](how-to-use-classroom-lab.md). Pokud používají klientský počítač se systémem Windows, bude muset povolit klienta SSH stažením [výstupu](https://www.putty.org/) nebo povolením [OpenSSH ve Windows](/windows-server/administration/openssh/openssh_install_firstuse) do SSH z příkazového řádku. 

1.  Spusťte virtuální počítač.
2.  Jakmile je virtuální počítač spuštěný, klikněte na **připojit**. tím se zobrazí dialogové okno s řetězcem příkazu SSH, který bude vypadat jako v následující ukázce:
    
     ```shell
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3.  Přejděte do příkazového řádku nebo terminálu a vložte tento příkaz a stiskněte klávesu **ENTER**.
4.  Zadejte heslo pro přihlášení k virtuálnímu počítači. 

Po připojení studentů k virtuálním počítačům můžou místně přistupovat k poznámkovým blokům Jupyter a spouštět je.

#### <a name="x2go-to-virtual-machine"></a>X2Go k virtuálnímu počítači
**Data Science Virtual Machine – image Ubuntu** je už zřízená s X2GO serverem a je připravená přijmout připojení klientů. Pokud se chcete připojit k grafické ploše počítače se systémem Linux, musí studenti postupovat podle těchto jednorázových kroků k nastavení X2Go na klientských počítačích:

1.  Stáhněte a nainstalujte [klienta X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) pro vaši klientskou platformu.
2.  Na [portálu Azure Lab Services](https://labs.azure.com)se ujistěte, že je spuštěný virtuální počítač se systémem Linux, ke kterému se chcete připojit.
3.  Jakmile je virtuální počítač spuštěný, klikněte na **připojit**. tím se zobrazí dialogové okno s řetězcem příkazu SSH, který bude vypadat jako v následující ukázce:

    ```
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```    
4. Jakmile budete mít tyto informace, otevřete klientskou aplikaci X2Go a vytvořte novou relaci. 
5.  V podokně **Předvolby relace** zadejte následující hodnoty:
    - **Název relace**: může to být cokoli, co potřebujete, ale doporučujeme použít název testovacího virtuálního počítače.
     - **Hostitel**: `ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`
     - **Přihlášení**: student
     - **Port SSH**: 12345
     - **Typ relace**: desktop Xfce
6. Vyberte **OK**. 

    > [!NOTE]
     > Při vytváření nové relace X2Go se ujistěte, že používáte port SSH, **nikoli** port RDP.

Nyní se můžete připojit k virtuálnímu počítači pomocí následujících kroků:    

1.  V klientovi X2Go dvakrát klikněte na virtuální počítač, ke kterému se chcete připojit. 

    ![Klient X2Go](./media/class-type-jupyter-notebook/x2go-client.png)
2. Zadejte heslo pro připojení k virtuálnímu počítači. (Možná budete muset udělit oprávnění X2Go pro obejít připojení brány firewall, aby bylo možné dokončit připojení.)
3.  Nyní byste měli vidět grafické rozhraní pro Ubuntu Data Science VM.


#### <a name="ssh-tunnel-to-jupyter-server-on-the-vm"></a>Tunel SSH na Jupyter Server na virtuálním počítači
Někteří studenti se můžou chtít přímo z místního počítače připojit přímo k serveru Jupyter ve svých virtuálních počítačích. Protokol SSH umožňuje přesměrování portů mezi místním počítačem a vzdáleným serverem (v našem případě testovacího virtuálního počítače studenta), aby byla aplikace spuštěná na určitém portu na serveru **Tunelově** připojená k portu mapování v místním počítači. Studenti by měli postupovat podle těchto kroků k tunelu SSH na Jupyter serveru na svých testovacích virtuálních počítačích:

1.  Na [portálu Azure Lab Services](https://labs.azure.com)se ujistěte, že je spuštěný virtuální počítač se systémem Linux, který chcete připojit.
2.  Jakmile je virtuální počítač spuštěný, klikněte na **připojit**. tím se zobrazí dialogové okno, které poskytuje řetězec příkazu SSH, který bude vypadat jako následující řetězec:

    ```bash
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3. V místním počítači spusťte terminál nebo příkazový řádek a zkopírujte do něj připojovací řetězec SSH. Pak přidejte `-L 8888:localhost:8888` do řetězce příkazu, který vytvoří **tunel** mezi porty. Konečný řetězec by měl vypadat takto:

    ```bash
     ssh –L 8888:localhost:8888 -p 12345 student@ml-lab-b720853e-570f-49ac-9cb2-bd0bd2aeec35.eastus.cloudapp.azure.com
     ```
4. Stiskněte klávesu **ENTER** a spusťte příkaz. 
5.  Po zobrazení výzvy zadejte heslo pro připojení k testovacímu virtuálnímu počítači. 
6.  Až budete připojeni k virtuálnímu počítači, spusťte server Jupyter pomocí tohoto příkazu: 

    ```bash
     jupyter notebook
     ```
7. Spuštění příkazu vám poskytne adresu URL v terminálu nebo příkazovém řádku. Adresa URL by měla vypadat takto:

    ```bash
     http://localhost:8888/?token=8c09ecfc93e6a8cbedf9c66dffdae19670a64acc1d37
     ```
8. Vložte tuto adresu URL do prohlížeče v místním počítači, abyste se mohli připojit a pracovat na svém Jupyter Notebook. 

    > [!NOTE]
    > Visual Studio Code také umožňuje skvělé [Jupyter notebook editační prostředí](https://code.visualstudio.com/docs/python/jupyter-support). Můžete postupovat podle pokynů v tématu [jak se připojit ke vzdálenému serveru Jupyter](https://code.visualstudio.com/docs/python/jupyter-support#_connect-to-a-remote-jupyter-server) a použít stejnou adresu URL z předchozího kroku pro připojení z vs Code místo z prohlížeče. 


## <a name="cost-estimate"></a>Odhad nákladů
Pojďme pro tuto třídu pokrýt možné náklady. Použijeme třídu 25 studentů. Naplánovaný čas třídy je 20 hodin. Každý student navíc získá kvótu 10 hodin pro domácí nebo přiřazení mimo plánovanou dobu třídy. Velikost virtuálního počítače, kterou jsme zvolili, byla malá GPU (COMPUTE), což je 139 jednotek testovacího prostředí. Pokud chcete použít malé (20 jednotek testovacích prostředí) nebo střední velikost (jednotky testovacího prostředí 42), můžete část jednotky testovacího prostředí v níže uvedené rovnici nahradit správným číslem.  

Tady je příklad možného odhadu nákladů pro tuto třídu: 25 studentů * (20 plánovaných hodin + 10 hodin pracovní doby) * 139 jednotek testovacího prostředí × 0,01 USD za hodinu = 1042,5 USD.

Další podrobnosti o cenách najdete v článku [Azure Lab Services ceny](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Závěr
V tomto článku jsme vás provedl prostřednictvím kroků pro vytvoření testovacího prostředí pro třídu notebooků Jupyter. Podobné nastavení můžete použít pro jiné třídy strojového učení.

## <a name="next-steps"></a>Další kroky

Další kroky jsou běžné pro nastavení testovacího prostředí.

- [Vytvoření a Správa šablony](how-to-create-manage-template.md)
- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavit plán](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Odkazy na registraci e-mailu studentům](how-to-configure-student-usage.md#send-invitations-to-users)