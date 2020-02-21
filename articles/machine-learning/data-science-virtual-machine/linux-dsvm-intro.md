---
title: 'Rychlý Start: vytvoření Data Science Virtual Machine CentOS'
titleSuffix: Azure Data Science Virtual Machine
description: Nakonfigurujte a vytvořte Data Science Virtual Machine pro Linux (CentOS), abyste mohli provádět analýzy a strojové učení.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: 73541b31125ee6e99dc2351e26f6a564a1603487
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526021"
---
# <a name="quickstart-set-up-a-centos-linux-data-science-virtual-machine-in-azure"></a>Rychlý Start: nastavení CentOS (Linux) Data Science Virtual Machine v Azure

Začněte pracovat s CentOSem na základě Data Science Virtual Machine.

## <a name="prerequisites"></a>Požadavky

Pokud chcete vytvořit Data Science Virtual Machine CentOS, musíte mít **předplatné Azure**. [Vytvořte bezplatné předplatné](https://azure.com/free).

## <a name="create-your-centos-data-science-virtual-machine"></a>Vytvoření Data Science Virtual Machine CentOS

Tady je postup vytvoření instance Data Science Virtual Machine CentOS:

1. Přejděte na [Azure Portal](https://portal.azure.com). Může se zobrazit výzva, abyste se přihlásili ke svému účtu Azure, pokud ještě nejste přihlášení. 
1. Do vyhledávacího panelu zadejte "virtuální počítač pro datové vědy" a vyberte CentOS DSVM.

    ![Výsledek hledání CentOS](./media/linux-dsvm-intro/search-centos.png)

1. V dalším okně vyberte **vytvořit**.

    [![](media/linux-dsvm-intro/create-centos.png "Button to create a CentOS machine")](media/linux-dsvm-intro/create-centos-expanded.png#lightbox)

1. Měli byste se přesměrovat na okno vytvořit virtuální počítač.
   
   ![Karta základy odpovídající virtuálnímu počítači s CentOS](./media/linux-dsvm-intro/review-create-centos.png)

1. Zadáním následujících informací nakonfigurujte jednotlivé kroky průvodce:

    1. **Základy**:
    
       * **Předplatné**: Pokud máte více než jedno předplatné, vyberte ten, na kterém se bude počítač vytvářet a účtují. Toto předplatné musí mít oprávnění vytvářet prostředky.
       * **Skupina prostředků**: Vytvořte novou skupinu nebo použijte existující.
       * **Název virtuálního počítače**: zadejte název virtuálního počítače. Tímto způsobem se zobrazí ve vašem Azure Portal.
       * **Oblast**: vyberte příslušné datové centrum. Pro nejrychlejší přístup k síti je to datové centrum, které má většinu vašich dat nebo je nejblíže vašemu fyzickému umístění. Přečtěte si další informace o [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Obrázek**: ponechte výchozí hodnotu.
       * **Velikost**: Tato hodnota by měla automaticky naplnit velikost, která je vhodná pro obecné úlohy. Přečtěte si další informace o [velikostech virtuálních počítačů s Linux v Azure](../../virtual-machines/linux/sizes.md).
       * **Typ ověřování**: pro rychlejší nastavení vyberte možnost heslo. 
         
         > [!NOTE]
         > Pokud máte v úmyslu používat JupyterHub, ujistěte se, že jste vybrali možnost "heslo", protože JupyterHub *není nakonfigurován k* používání veřejných klíčů ssh.

       * **Uživatelské jméno**: zadejte uživatelské jméno správce. Toto je uživatelské jméno, které použijete k přihlášení k virtuálnímu počítači, a nemusí být stejné jako uživatelské jméno Azure. Nepoužívejte *Velká* písmena.
         
         > [!NOTE]
         > Pokud v uživatelském jméně použijete velká písmena, JupyterHub nebude fungovat a dojde k chybě 500 interního serveru.

       * **Heslo**: zadejte heslo, které budete používat pro přihlášení k virtuálnímu počítači.    
    
   1. Vyberte **Zkontrolovat a vytvořit**.
   1. **Zkontrolovat a vytvořit**
      * Ověřte, zda všechny informace, které jste zadali správný. 
      * Vyberte **Create** (Vytvořit).
    
    Zřizování by měla trvat asi 5 minut. Stav se zobrazí v Azure Portal.

## <a name="how-to-access-the-centos-data-science-virtual-machine"></a>Jak přistupovat k CentOS Data Science Virtual Machine

K CentOS DSVM máte přístup jedním ze tří způsobů:

  * Na terminálu relace SSH
  * X2Go grafické relací
  * JupyterHub a JupyterLab pro aplikace Jupyter notebook

K Azure Notebooks můžete také připojit Data Science Virtual Machine ke spuštění poznámkových bloků Jupyter na virtuálním počítači a obejít omezení úrovně bezplatné služby. Další informace najdete v tématu [Správa a konfigurace Azure Notebooksch projektů](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="ssh"></a>SSH

Pokud byl virtuální počítač vytvořen, pokud byl nakonfigurován s přístupem SSH, můžete se k němu přihlásit pomocí SSH. Pro rozhraní textového prostředí použijte přihlašovací údaje účtu, které jste vytvořili v části **základy** v kroku 3. Ve Windows si můžete stáhnout klientský nástroj SSH [, jako je](https://www.putty.org)například výstup. Pokud dáváte přednost grafické ploše (systém Windows X), můžete použít předávání X11 na výstupu.

> [!NOTE]
> Klient X2Go lepších výsledků než X11 předávání v testování. Doporučujeme používat X2Go klienta klasické pracovní plochy grafické rozhraní.

### <a name="x2go"></a>X2Go

Virtuální počítač se systémem Linux je již zřízený serverem X2Go a připraven k přijetí připojení klienta. Pro připojení k desktopu grafické virtuálního počítače s Linuxem, proveďte následující postup u svého klienta:

1. Stáhněte a nainstalujte klienta X2Go pro vaši klientskou platformu z [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Poznamenejte si veřejnou IP adresu virtuálního počítače, kterou můžete najít v Azure Portal otevřením virtuálního počítače, který jste právě vytvořili.

   ![IP adresa počítače CentOS](./media/linux-dsvm-intro/centos-ip-address.png)

1. Spusťte klienta X2Go. Pokud se okno Nová relace automaticky neotevře, přečtěte si relaci-> novou relaci.

1. V okně výsledná konfigurace zadejte následující parametry konfigurace:
   * **Karta relace**:
     * **Hostitel**: zadejte IP adresu vašeho virtuálního počítače, který jste si poznamenali dříve.
     * **Přihlášení**: zadejte uživatelské jméno na virtuálním počítači se systémem Linux.
     * **Port SSH**: ponechte ho v 22, výchozí hodnota.
     * **Typ relace**: Změňte hodnotu na **desktop Xfce**. Virtuální počítač se systémem Linux v současné době podporuje pouze desktop Xfce plochu.
   * **Karta média**: Pokud je nepotřebujete používat, můžete vypnout zvukovou podporu a tisk klienta.
   * **Sdílené složky**: Pokud chcete adresáře z klientských počítačů připojených k virtuálnímu počítači se systémem Linux, přidejte do této karty adresáře klientských počítačů, které chcete sdílet s virtuálním počítačem.

   ![Konfigurace X2go](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Vyberte **OK**.
1. Kliknutím na pole v pravém podokně okna X2Go otevřete obrazovku pro přihlášení k vašemu VIRTUÁLNÍmu počítači.
1. Zadejte heslo pro svůj virtuální počítač.
1. Vyberte **OK**.
1. Aby bylo možné dokončit připojení, může být nutné udělit oprávnění X2Go k objednání brány firewall.
1. Nyní byste měli vidět grafické rozhraní pro CentOS DSVM. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub a JupyterLab

CentOS DSVM spouští [JupyterHub](https://github.com/jupyterhub/jupyterhub), víceuživatelského Jupyter serveru. Chcete-li se připojit, proveďte následující kroky:

   1. Poznamenejte si veřejnou IP adresu svého virtuálního počítače tak, že na Azure Portal vyhledáte a vyberete svůj virtuální počítač.

       ![IP adresa počítače CentOS](./media/linux-dsvm-intro/centos-ip-address.png)

   1. Z místního počítače otevřete webový prohlížeč a přejděte do https:\//Your-VM-IP: 8000 a nahraďte "Your-VM-IP" IP adresou, kterou jste si poznamenali dříve.
   1. Zadejte uživatelské jméno a heslo, které jste použili k vytvoření virtuálního počítače, a přihlaste se. 

      ![Zadejte Jupyter přihlášení.](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Projděte si mnoho dostupných ukázkových poznámkových bloků.

JupyterLab, generace poznámkové bloky Jupyter a JupyterHub, je také k dispozici. Pokud k němu chcete získat přístup, přihlaste se k JupyterHub a potom přejděte na adresu URL https:\//Your-VM-IP: 8000/User/Your-username/Lab a nahraďte "Your-username" uživatelským jménem, které jste si zvolili při konfiguraci virtuálního počítače.

JupyterLab můžete nastavit jako výchozí server Poznámkový blok přidáním tohoto řádku do `/etc/jupyterhub/jupyterhub_config.py`:

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Další kroky

Zde je, jak můžete pokračovat v učení a zkoumání:

* Návod pro [datovou vědu na data Science Virtual Machine pro Linux](linux-dsvm-walkthrough.md) vám ukáže, jak provést několik běžných úloh pro datové vědy se systémem Linux DSVM zřízeným zde. 
* Vyzkoušením nástrojů popsaných v tomto článku prozkoumejte různé nástroje pro datové vědy na DSVM. Můžete také spustit `dsvm-more-info` v prostředí ve virtuálním počítači pro základní Úvod a pro ukazatele na Další informace o nástrojích nainstalovaných v DSVM.  
* Naučte se systematicky sestavovat komplexní Analytická řešení pomocí [procesu vědeckého zpracování týmových dat](https://aka.ms/tdsp).
* Podívejte se na [Azure AI Gallery](https://gallery.azure.ai/) pro strojové učení a ukázky analýzy dat, které používají služby Azure AI.
* Projděte si příslušnou [referenční dokumentaci](./reference-centos-vm.md) pro tento virtuální počítač.