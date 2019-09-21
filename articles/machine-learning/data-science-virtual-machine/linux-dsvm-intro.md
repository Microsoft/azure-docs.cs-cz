---
title: 'Rychlý start: Vytvoření Data Science Virtual Machine CentOS'
description: Nakonfigurujte a vytvořte Data Science Virtual Machine pro Linux (CentOS), abyste mohli provádět analýzy a strojové učení.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: 34aaae23b834ec1fb9e071f31c6d8e94c9c7dd0a
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170813"
---
# <a name="quickstart-set-up-a-centos-linux-data-science-virtual-machine-in-azure"></a>Rychlý start: Nastavení CentOS (Linux) Data Science Virtual Machine v Azure

Začněte pracovat s CentOSem na základě Data Science Virtual Machine.

## <a name="prerequisites"></a>Požadavky

Pokud chcete vytvořit Data Science Virtual Machine CentOS, musíte mít **předplatné Azure**. [Vytvořte bezplatné předplatné](https://azure.com/free).

## <a name="create-your-centos-data-science-virtual-machine"></a>Vytvoření Data Science Virtual Machine CentOS

Tady je postup vytvoření instance Data Science Virtual Machine CentOS:

1. Přejděte na [Azure Portal](https://portal.azure.com). Může se zobrazit výzva, abyste se přihlásili ke svému účtu Azure, pokud ještě nejste přihlášení. 
1. Do vyhledávacího panelu zadejte "virtuální počítač pro datové vědy" a vyberte CentOS DSVM.

    ![Výsledek hledání CentOS](./media/linux-dsvm-intro/search-centos.png)

1. V dalším okně vyberte **vytvořit**.

    ![Průvodce vytvořením virtuálního počítače](./media/linux-dsvm-intro/create-centos.png)

1. Měli byste se přesměrovat na okno vytvořit virtuální počítač.
   
   ![Karta základy odpovídající virtuálnímu počítači s CentOS](./media/linux-dsvm-intro/review-create-centos.png)

1. Zadáním následujících informací nakonfigurujte jednotlivé kroky průvodce:

    1. **Základy**:
    
       * **Předplatné:** Pokud máte více než jedno předplatné, vyberte ten, na kterém se bude počítač vytvářet a účtují. Toto předplatné musí mít oprávnění vytvářet prostředky.
       * **Skupina prostředků**: Vytvořte novou skupinu nebo použijte existující.
       * **Název virtuálního počítače**: Zadejte název virtuálního počítače. Tímto způsobem se zobrazí ve vašem Azure Portal.
       * **Oblast:** Vyberte odpovídající datové centrum. Pro nejrychlejší přístup k síti je to datové centrum, které má většinu vašich dat nebo je nejblíže vašemu fyzickému umístění. Přečtěte si další informace o [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Obrázek**: Nechte zadanou výchozí hodnotu.
       * **Velikost**: To by mělo automaticky naplnit velikost, která je vhodná pro obecné úlohy. Přečtěte si další informace o [velikostech virtuálních počítačů s Linux v Azure](../../virtual-machines/linux/sizes.md).
       * **Typ ověřování**: V případě rychlejšího nastavení vyberte možnost heslo. 
         
         > [!NOTE]
         > Pokud máte v úmyslu používat JupyterHub, ujistěte se, že jste vybrali možnost "heslo", protože JupyterHub *není nakonfigurován k* používání veřejných klíčů ssh.

       * **Uživatelské jméno**: Zadejte uživatelské jméno správce. Toto je uživatelské jméno, které použijete k přihlášení k virtuálnímu počítači, a nemusí být stejné jako uživatelské jméno Azure. Nepoužívejte *Velká* písmena.
         
         > [!NOTE]
         > Pokud v uživatelském jméně použijete velká písmena, JupyterHub nebude fungovat a dojde k chybě 500 interního serveru.

       * **Heslo**: Zadejte heslo, které použijete k přihlášení k virtuálnímu počítači.    
    
   1. Vyberte **Zkontrolovat a vytvořit**.
   1. **Zkontrolovat a vytvořit**
      * Ověřte, zda všechny informace, které jste zadali správný. 
      * Vyberte **Vytvořit**.
    
    Zřizování by měla trvat asi 5 minut. Stav se zobrazí v Azure Portal.

## <a name="how-to-access-the-centos-data-science-virtual-machine"></a>Jak přistupovat k CentOS Data Science Virtual Machine

K CentOS DSVM máte přístup jedním ze tří způsobů:

  * Na terminálu relace SSH
  * X2Go grafické relací
  * JupyterHub a JupyterLab pro aplikace Jupyter notebook

K Azure Notebooks můžete také připojit Data Science Virtual Machine ke spuštění poznámkových bloků Jupyter na virtuálním počítači a obejít omezení úrovně bezplatné služby. Další informace najdete v tématu [Správa a konfigurace Azure Notebooksch projektů](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="ssh"></a>SSH

Pokud byl virtuální počítač vytvořen, pokud byl nakonfigurován s přístupem SSH, můžete se k němu přihlásit pomocí SSH. Pomocí přihlašovacích údajů účtu, které jste vytvořili v **Základy** část krok 3 pro rozhraní text prostředí. Ve Windows si můžete stáhnout klientský nástroj SSH [, jako je](https://www.putty.org)například výstup. Pokud dáváte přednost grafické ploše (systém Windows X), můžete použít předávání X11 na výstupu.

> [!NOTE]
> Klient X2Go lepších výsledků než X11 předávání v testování. Doporučujeme používat X2Go klienta klasické pracovní plochy grafické rozhraní.

### <a name="x2go"></a>X2Go

Virtuální počítač se systémem Linux je již zřízený serverem X2Go a připraven k přijetí připojení klienta. Pro připojení k desktopu grafické virtuálního počítače s Linuxem, proveďte následující postup u svého klienta:

1. Stažení a instalace klienta X2Go pro vaše klientská platforma z [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Poznamenejte si veřejnou IP adresu virtuálního počítače, kterou můžete najít v Azure Portal otevřením virtuálního počítače, který jste právě vytvořili.

   ![IP adresa počítače CentOS](./media/linux-dsvm-intro/centos-ip-address.png)

1. Spusťte klienta X2Go. Pokud se okno Nová relace automaticky neotevře, přečtěte si relaci-> novou relaci.

1. V okně výsledná konfigurace zadejte následující parametry konfigurace:
   * **Karta relace**:
     * **Hostitel**: Zadejte IP adresu vašeho virtuálního počítače, který jste si poznamenali dříve.
     * **Přihlašovací jméno**: Zadejte uživatelské jméno na virtuálním počítači se systémem Linux.
     * **Port SSH**: Ponechte to 22, výchozí hodnota.
     * **Typ relace**: Změňte hodnotu na **desktop Xfce**. Virtuální počítač se systémem Linux v současné době podporuje pouze desktop Xfce plochu.
   * **Karta média**: Pokud je nepotřebujete používat, můžete vypnout zvukovou podporu a tisk klienta.
   * **Sdílené složky**: Pokud chcete adresáře z klientských počítačů připojených k VIRTUÁLNÍmu počítači se systémem Linux, přidejte do této karty adresáře klientských počítačů, které chcete sdílet s VIRTUÁLNÍm počítačem.

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

   1. Z místního počítače otevřete webový prohlížeč a přejděte na https:\//Your-VM-IP: 8000 a nahraďte "Your-VM-IP" IP adresou, kterou jste si poznamenali dříve.
   1. Zadejte uživatelské jméno a heslo, které jste použili k vytvoření virtuálního počítače, a přihlaste se. 

      ![Zadejte Jupyter přihlášení.](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Projděte si mnoho dostupných ukázkových poznámkových bloků.

JupyterLab, generace poznámkové bloky Jupyter a JupyterHub, je také k dispozici. Abyste k němu měli přístup, přihlaste se k JupyterHub a pak přejděte na adresu\/URL https:/your-VM-IP: 8000/User/Your-username/Lab a nahraďte "Your-username" uživatelským jménem, které jste si zvolili při konfiguraci virtuálního počítače.

Můžete nastavit JupyterLab jako výchozí Poznámkový Server přidáním tohoto řádku do `/etc/jupyterhub/jupyterhub_config.py`:

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Další kroky

Zde je, jak můžete pokračovat v učení a zkoumání:

* Návod pro [datovou vědu na data Science Virtual Machine pro Linux](linux-dsvm-walkthrough.md) vám ukáže, jak provést několik běžných úloh pro datové vědy se systémem Linux DSVM zřízeným zde. 
* Vyzkoušením nástrojů popsaných v tomto článku prozkoumejte různé nástroje pro datové vědy na DSVM. Pro základní Úvod a `dsvm-more-info` pro ukazatele na Další informace o nástrojích nainstalovaných v DSVM můžete také spustit v prostředí ve virtuálním počítači.  
* Další informace o vytváření analytická řešení začátku do konce systematicky pomocí [vědecké zpracování týmových dat](https://aka.ms/tdsp).
* Přejděte [galerii Azure AI](https://gallery.azure.ai/) pro machine learning a datové analýzy ukázek, které využívají služby Azure AI.
* Projděte si příslušnou [referenční dokumentaci](./reference-centos-vm.md) pro tento virtuální počítač.