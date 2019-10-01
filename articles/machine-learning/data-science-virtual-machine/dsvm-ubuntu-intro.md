---
title: 'Rychlý Start: vytvoření DSVMu Ubuntu'
description: Nakonfigurujte a vytvořte Data Science Virtual Machine pro Linux (Ubuntu), abyste mohli provádět analýzy a strojové učení.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/10/2019
ms.openlocfilehash: 951191422e80ef6224cd140beed782de31cb3822
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677847"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-linux-ubuntu"></a>Rychlý Start: nastavení Data Science Virtual Machine pro Linux (Ubuntu)

Zprovoznění Ubuntu Data Science Virtual Machine.

## <a name="prerequisites"></a>Požadavky

Pokud chcete vytvořit Data Science Virtual Machine Windows, musíte mít předplatné Azure. [Vyzkoušejte si Azure zdarma](https://azure.com/free).
Upozorňujeme prosím, že bezplatné účty Azure nepodporují SKU virtuálních počítačů s povoleným GPU.

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Vytvoření Data Science Virtual Machine pro Linux

Tady je postup vytvoření instance Data Science Virtual Machine pro Linux:

1. Pokud ještě nejste přihlášení, můžete přejít na [Azure Portal](https://portal.azure.com) zobrazí se výzva k přihlášení k účtu Azure.
1. Vyhledejte výpis virtuálního počítače zadáním příkazu "virtuální počítač pro datové vědy" a výběrem Data Science Virtual Machine pro Linux (Ubuntu).
    
    ![Seznam virtuálních počítačů Ubuntu](./media/dsvm-ubuntu-intro/search-ubuntu.png)

1. V dalším okně vyberte **vytvořit**.

   [![](media/dsvm-ubuntu-intro/create-linux-expanded.png#lightbox)(media/dsvm-ubuntu-intro/create-linux.png "Tlačítko pro vytvoření počítače s Ubuntu")](media/dsvm-ubuntu-intro/create-linux-expanded.png#lightbox)

1. Měli byste se přesměrovat na okno vytvořit virtuální počítač.
   
   ![Karta základy odpovídající virtuálnímu počítači s Ubuntu](./media/dsvm-ubuntu-intro/review-create-ubuntu.png)

1. Zadáním následujících informací nakonfigurujte jednotlivé kroky průvodce:

    1. **Základy**:
    
       * **Předplatné**: Pokud máte více než jedno předplatné, vyberte ten, na kterém se bude počítač vytvářet a účtují. Pro toto předplatné musíte mít oprávnění pro vytváření prostředků.
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
    
   1. Vyberte **zkontrolovat + vytvořit**.
   1. **Zkontrolovat a vytvořit**
      * Ověřte, zda jsou všechny informace, které jste zadali, správné. 
      * Vyberte **vytvořit**.
    
    Zřizování by mělo trvat přibližně 5 minut. Stav se zobrazí v Azure Portal.

## <a name="how-to-access-the-ubuntu-data-science-virtual-machine"></a>Jak přistupovat k Ubuntu Data Science Virtual Machine

K Ubuntu DSVM máte přístup jedním ze tří způsobů:

  * SSH pro relace Terminálové služby
  * X2Go pro grafické relace
  * JupyterHub a JupyterLab pro notebooky Jupyter

K Azure Notebooks můžete také připojit Data Science Virtual Machine ke spuštění poznámkových bloků Jupyter na virtuálním počítači a obejít omezení úrovně bezplatné služby. Další informace najdete v tématu [Správa a konfigurace Azure Notebooksch projektů](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="ssh"></a>PROTOKOLY

Pokud byl virtuální počítač vytvořen, pokud byl nakonfigurován s přístupem SSH, můžete se k němu přihlásit pomocí SSH. Pro rozhraní textového prostředí použijte přihlašovací údaje účtu, které jste vytvořili v části **základy** v kroku 3. Ve Windows si můžete stáhnout klientský nástroj SSH [, jako je](https://www.putty.org)například výstup. Pokud dáváte přednost grafické ploše (systém Windows X), můžete použít předávání X11 na výstupu.

> [!NOTE]
> Klient X2Go v testování provedl lepší předávání dat než X11. Pro grafické rozhraní plochy doporučujeme použít klienta X2Go.

### <a name="x2go"></a>X2Go

Virtuální počítač se systémem Linux je již zřízený serverem X2Go a připraven k přijetí připojení klienta. Pokud se chcete připojit k grafickému počítači se systémem Linux, proveďte na svém klientovi následující postup:

1. Stáhněte a nainstalujte klienta X2Go pro vaši klientskou platformu z [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Poznamenejte si veřejnou IP adresu virtuálního počítače, kterou můžete najít v Azure Portal otevřením virtuálního počítače, který jste právě vytvořili.

   ![IP adresa počítače Ubuntu](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

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
1. Nyní byste měli vidět grafické rozhraní pro Ubuntu DSVM. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub a JupyterLab

Ubuntu DSVM spouští [JupyterHub](https://github.com/jupyterhub/jupyterhub), víceuživatelského Jupyter serveru. Chcete-li se připojit, proveďte následující kroky:

   1. Poznamenejte si veřejnou IP adresu svého virtuálního počítače tak, že na Azure Portal vyhledáte a vyberete svůj virtuální počítač.
      ![Ubuntu IP adresa počítače @ no__t-1

   1. Z místního počítače otevřete webový prohlížeč a přejděte do https: \//VM-IP-IP: 8000 a nahraďte "Your-VM-IP" adresou, kterou jste si poznamenali dříve.
   1. Zadejte uživatelské jméno a heslo, které jste použili k vytvoření virtuálního počítače, a přihlaste se. 

      ![Zadejte Jupyter přihlášení.](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Projděte si mnoho dostupných ukázkových poznámkových bloků.

JupyterLab, je k dispozici také další generace poznámkových bloků Jupyter a JupyterHub. Pokud k němu chcete získat přístup, přihlaste se k JupyterHub a potom přejděte na adresu URL https: \//VM-IP-IP: 8000/User/Your-username/Lab, nahraďte "Your-username" uživatelským jménem, které jste si zvolili při konfiguraci virtuálního počítače.

JupyterLab můžete nastavit jako výchozí server Poznámkový blok přidáním tohoto řádku do `/etc/jupyterhub/jupyterhub_config.py`:

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Další kroky

Tady je postup, jak můžete pokračovat ve studiu a průzkumu:

* V názorných kurzech [Data Science Virtual Machine pro Linux](linux-dsvm-walkthrough.md) se dozvíte, jak provést několik běžných úloh vědeckého zpracování dat se systémem Linux DSVM zřízeným zde. 
* Vyzkoušením nástrojů popsaných v tomto článku prozkoumejte různé nástroje pro datové vědy na DSVM. V prostředí virtuálního počítače můžete také spustit `dsvm-more-info` a získat tak základní Úvod a odkazy na Další informace o nástrojích nainstalovaných na VIRTUÁLNÍm počítači.  
* Naučte se systematicky sestavovat komplexní Analytická řešení pomocí [procesu vědeckého zpracování týmových dat](https://aka.ms/tdsp).
* Podívejte se na [Azure AI Gallery](https://gallery.azure.ai/) pro strojové učení a ukázky analýzy dat, které používají služby Azure AI.
* Projděte si příslušnou [referenční dokumentaci](./reference-ubuntu-vm.md) pro tento virtuální počítač.
