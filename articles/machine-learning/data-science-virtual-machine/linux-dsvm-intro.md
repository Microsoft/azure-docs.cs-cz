---
title: 'Rychlý start: Vytvoření virtuálního počítače centos datové vědy'
titleSuffix: Azure Data Science Virtual Machine
description: Nakonfigurujte a vytvořte virtuální počítač pro datové vědy pro Linux (CentOS) pro analýzu a strojové učení.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: 73541b31125ee6e99dc2351e26f6a564a1603487
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77526021"
---
# <a name="quickstart-set-up-a-centos-linux-data-science-virtual-machine-in-azure"></a>Úvodní příručka: Nastavení virtuálního počítače s datovou vědou CentOS (Linux) v Azure

Zprovoznění s virtuálním počítačem pro datové vědy založeném na CentOS.

## <a name="prerequisites"></a>Požadavky

Chcete-li vytvořit virtuální počítač CentOS Data Science, musíte mít **předplatné Azure**. [Vytvořte bezplatné předplatné](https://azure.com/free).

## <a name="create-your-centos-data-science-virtual-machine"></a>Vytvořte si virtuální počítač CentOS Data Science

Tady jsou kroky k vytvoření instance virtuálního počítače CentOS Data Science:

1. Přejděte na [portál Azure](https://portal.azure.com). Pokud ještě nejste přihlášeni, můžete se k přihlášenému účtu Azure. 
1. Do vyhledávacího panelu zadejte "virtuální stroj pro datové vědy" a vyberte CentOS DSVM.

    ![Výsledek vyhledávání CentOS](./media/linux-dsvm-intro/search-centos.png)

1. V následujícím okně vyberte **Vytvořit**.

    [![](media/linux-dsvm-intro/create-centos.png "Button to create a CentOS machine")](media/linux-dsvm-intro/create-centos-expanded.png#lightbox)

1. Měli byste být přesměrováni na okno "Vytvořit virtuální počítač".
   
   ![Karta Základy odpovídající virtuálnímu počítači CentOS](./media/linux-dsvm-intro/review-create-centos.png)

1. Chcete-li nakonfigurovat každý krok průvodce, zadejte následující informace:

    1. **Základy**:
    
       * **Předplatné**: Pokud máte více než jedno předplatné, vyberte předplatné, ve kterém bude počítač vytvořen a fakturován. Toto předplatné musí mít oprávnění vytvářet prostředky.
       * **Skupina prostředků**: Vytvořte novou skupinu nebo použijte existující skupinu.
       * **Název virtuálního počítače**: Zadejte název virtuálního počítače. Takhle se zobrazí na vašem webu Azure Portal.
       * **Oblast**: Vyberte nejvhodnější datové centrum. Pro nejrychlejší přístup k síti je to datové centrum, které má většinu vašich dat nebo je nejblíže k vaší fyzické poloze. Další informace o [oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Obrázek**: Ponechte výchozí hodnotu.
       * **Velikost**: To by mělo automaticky naplnit s velikostí, která je vhodná pro obecné úlohy. Přečtěte si další informace o [velikostech virtuálních počítačích s Linuxem v Azure](../../virtual-machines/linux/sizes.md).
       * **Typ ověřování**: Pro rychlejší nastavení vyberte možnost Heslo. 
         
         > [!NOTE]
         > Pokud máte v úmyslu používat JupyterHub, ujistěte se, že vybrat "Heslo", jako JupyterHub *není* nakonfigurován pro použití SSH veřejné klíče.

       * **Uživatelské jméno**: Zadejte uživatelské jméno správce. Toto je uživatelské jméno, které budete používat k přihlášení k virtuálnímu počítači a nemusí být stejné jako vaše uživatelské jméno Azure. *Nepoužívejte* velká písmena.
         
         > [!NOTE]
         > Pokud používáte velká písmena v uživatelském jménu, JupyterHub nebude fungovat a narazíte na chybu interního serveru 500.

       * **Heslo**: Zadejte heslo, které použijete k přihlášení do virtuálního počítače.    
    
   1. Vyberte **Zkontrolovat a vytvořit**.
   1. **Zkontrolovat+vytvořit**
      * Ověřte správnost všech zadaných informací. 
      * Vyberte **Vytvořit**.
    
    Zřizování by mělo trvat asi 5 minut. Stav se zobrazí na webu Azure Portal.

## <a name="how-to-access-the-centos-data-science-virtual-machine"></a>Jak získat přístup k virtuálnímu počítači CentOS Data Science

K CentOS DSVM můžete přistupovat jedním ze tří způsobů:

  * SSH pro terminálové relace
  * X2Go pro grafické relace
  * JupyterHub a JupyterLab pro poznámkové bloky Jupyter

Virtuální počítač pro datové vědy můžete také připojit k poznámkovým blokům Azure, abyste mohli spouštět poznámkové bloky Jupyter na virtuálním počítači a obejít omezení úrovně bezplatné služby. Další informace najdete v [tématu Správa a konfigurace projektů poznámkových bloků Azure](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="ssh"></a>SSH

Po vytvoření virtuálního počítače, pokud byl nakonfigurován s přístupem SSH, můžete se k němu přihlásit pomocí SSH. Pro rozhraní textového prostředí použijte přihlašovací údaje účtu, které jste vytvořili v části **Základy** kroku 3. V systému Windows si můžete stáhnout klientský nástroj SSH, jako je [PuTTY](https://www.putty.org). Pokud dáváte přednost grafické ploše (X Window System), můžete použít X11 forwarding na PuTTY.

> [!NOTE]
> Klient X2Go si při testování vedl lépe než x11 forwarding. Pro grafické desktopové rozhraní doporučujeme použít klienta X2Go.

### <a name="x2go"></a>X2Go

Virtuální počítač s Linuxem je již zřízena s X2Go Server a je připraven přijímat připojení klientů. Chcete-li se připojit ke grafické ploše virtuálního počítače s Linuxem, proveďte v klientovi následující postup:

1. Stáhněte a nainstalujte klienta X2Go pro vaši klientskou platformu z [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Poznamenejte si veřejnou IP adresu virtuálního počítače, kterou najdete na webu Azure Portal otevřením virtuálního počítače, který jste právě vytvořili.

   ![IP adresa počítače CentOS](./media/linux-dsvm-intro/centos-ip-address.png)

1. Spusťte klienta X2Go. Pokud se okno Nová relace automaticky nezobrazí, přejděte na session -> nová relace.

1. Ve výsledném konfiguračním okně zadejte následující parametry konfigurace:
   * **Karta Relace**:
     * **Hostitel:** Zadejte IP adresu virtuálního počítače, kterou jste si poznamenali dříve.
     * **Přihlášení**: Zadejte uživatelské jméno na virtuálním počítači s Linuxem.
     * **SSH Port**: Ponechte ji na 22, výchozí hodnota.
     * **Typ relace**: Změňte hodnotu na **XFCE**. V současné době virtuální počítač s Linuxem podporuje pouze plochu XFCE.
   * **Karta Média**: Pokud je nepotřebujete používat, můžete vypnout podporu zvuku a klientský tisk.
   * **Sdílené složky**: Pokud chcete adresáře z klientských počítačů připojených na virtuálním počítači s Linuxem, přidejte adresáře klientských počítačů, které chcete sdílet s virtuálním počítačem na této kartě.

   ![Konfigurace X2go](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Vyberte **OK**.
1. Klikněte na pole v pravém podokně okna X2Go, abyste se dostali na přihlašovací obrazovku pro váš virtuální počítač.
1. Zadejte heslo pro virtuální počítač.
1. Vyberte **OK**.
1. Možná budete muset dát X2Go oprávnění obejít firewall, aby bylo možné dokončit připojení.
1. Nyní byste měli vidět grafické rozhraní pro váš CentOS DSVM. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub a JupyterLab

CentOS DSVM provozuje [JupyterHub](https://github.com/jupyterhub/jupyterhub), víceuživatelský server Jupyter. Chcete-li se připojit, postupujte takto:

   1. Poznamenejte si veřejnou IP adresu pro váš virtuální počítač vyhledáním a výběrem virtuálního počítače na webu Azure Portal.

       ![IP adresa počítače CentOS](./media/linux-dsvm-intro/centos-ip-address.png)

   1. Z místního počítače otevřete webový prohlížeč\/a přejděte na https: /your-vm-ip:8000 a nahraďte "your-vm-ip" IP adresou, kterou jste si dříve všimli.
   1. Zadejte uživatelské jméno a heslo, které jste použili k vytvoření virtuálního počítače a přihlaste se. 

      ![Zadejte přihlášení Jupyteru](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Prohlédněte si mnoho ukázkových poznámkových bloků, které jsou k dispozici.

JupyterLab, nová generace notebooků Jupyter a JupyterHub, je také k dispozici. Chcete-li k němu získat přístup, přihlaste se k\/JupyterHubu a přejděte na adresu URL https: /your-vm-ip:8000/user/your-username/lab a nahraďte "vaše uživatelské jméno" uživatelským jménem, které jste zvolili při konfiguraci virtuálního počítače.

JupyterLab můžete nastavit jako výchozí notebookový server `/etc/jupyterhub/jupyterhub_config.py`přidáním tohoto řádku do :

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Další kroky

Zde je návod, jak můžete pokračovat ve svém učení a průzkumu:

* Návod [datové vědy na virtuálním počítači pro datové vědy pro Linux](linux-dsvm-walkthrough.md) ukazuje, jak provést několik běžných úloh datové vědy s Linuxem DSVM zřízeným zde. 
* Prozkoumejte různé nástroje pro datové vědy v nástroji DSVM vyzkoušením nástrojů popsaných v tomto článku. Můžete také `dsvm-more-info` spustit v prostředí ve virtuálním počítači pro základní úvod a pro odkazy na další informace o nástrojích nainstalovaných na DSVM.  
* Naučte se systematicky vytvářet komplexní analytická řešení pomocí [procesu vědecké analýzy týmových dat](https://aka.ms/tdsp).
* Navštivte [Galerii AI Azure](https://gallery.azure.ai/) pro strojové učení a analýzu dat ukázky, které používají služby Azure AI.
* Prožite příslušnou [referenční dokumentaci](./reference-centos-vm.md) pro tento virtuální počítač.