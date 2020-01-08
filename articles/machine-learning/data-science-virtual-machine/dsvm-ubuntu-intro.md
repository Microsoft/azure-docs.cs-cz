---
title: 'Rychlý Start: vytvoření DSVMu Ubuntu'
description: Nakonfigurujte a vytvořte Data Science Virtual Machine pro Linux (Ubuntu), abyste mohli provádět analýzy a strojové učení.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: 263c12b344e8634c639167aa3e455032f0817e2f
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2020
ms.locfileid: "75612083"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-linux-ubuntu"></a>Rychlý Start: nastavení Data Science Virtual Machine pro Linux (Ubuntu)

Načtěte si Ubuntu 18,04 Data Science Virtual Machine a spusťte ho.

## <a name="prerequisites"></a>Požadavky

Pokud chcete vytvořit Data Science Virtual Machine 18,04 Ubuntu, musíte mít předplatné Azure. [Vyzkoušejte si Azure zdarma](https://azure.com/free).
Upozorňujeme prosím, že bezplatné účty Azure nepodporují SKU virtuálních počítačů s povoleným GPU.

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Vytvořit váš virtuální počítač pro datové vědy pro Linux

Tady je postup vytvoření instance Data Science Virtual Machine Ubuntu 18,04:

1. Pokud ještě nejste přihlášení, můžete přejít na [Azure Portal](https://portal.azure.com) zobrazí se výzva k přihlášení k účtu Azure.
1. Vyhledejte výpis virtuálního počítače zadáním příkazu "virtuální počítač pro datové vědy" a výběrem Data Science Virtual Machine-Ubuntu 18,04 Preview.

1. V dalším okně vyberte **vytvořit**.

1. Měli byste se přesměrovat na okno vytvořit virtuální počítač.
   
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
      * Vyberte **Vytvořit**.
    
    Zřizování by měla trvat asi 5 minut. Stav se zobrazí v Azure Portal.

## <a name="how-to-access-the-ubuntu-data-science-virtual-machine"></a>Jak přistupovat k Ubuntu Data Science Virtual Machine

K Ubuntu DSVM máte přístup jedním ze tří způsobů:

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

   ![IP adresa počítače Ubuntu](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

1. Spusťte klienta X2Go. Pokud se okno Nová relace automaticky neotevře, přečtěte si relaci-> novou relaci.

1. V okně výsledná konfigurace zadejte následující parametry konfigurace:
   * **Karta relace**:
     * **Hostitel**: zadejte IP adresu vašeho virtuálního počítače, který jste si poznamenali dříve.
     * **Přihlášení**: zadejte uživatelské jméno na virtuálním počítači se systémem Linux.
     * **SSH Port**: ponechte výchozí hodnota 22.
     * **Typ relace**: Změňte hodnotu na **XFCE**. Virtuální počítač se systémem Linux v současné době podporuje pouze desktop Xfce plochu.
   * **Karta média**: můžete vypnout podpory zvuku a klient tisku, pokud není nutné k jejich použití.
   * **Sdílené složky**: Pokud chcete adresářů z klientských počítačů připojené na Linuxovém virtuálním počítači, přidejte adresáře klientské počítače, které chcete sdílet s virtuálním Počítačem na této kartě.

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
      ![IP adresa počítače Ubuntu](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

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

* V názorných kurzech [Data Science Virtual Machine pro Linux](linux-dsvm-walkthrough.md) se dozvíte, jak provést několik běžných úloh vědeckého zpracování dat se systémem Linux DSVM zřízeným zde. 
* Vyzkoušením nástrojů popsaných v tomto článku prozkoumejte různé nástroje pro datové vědy na DSVM. V prostředí virtuálního počítače můžete také spustit `dsvm-more-info`, kde najdete základní Úvod a odkazy na Další informace o nástrojích nainstalovaných na VIRTUÁLNÍm počítači.  
* Další informace o vytváření analytická řešení začátku do konce systematicky pomocí [vědecké zpracování týmových dat](https://aka.ms/tdsp).
* Přejděte [galerii Azure AI](https://gallery.azure.ai/) pro machine learning a datové analýzy ukázek, které využívají služby Azure AI.
* Projděte si příslušnou [referenční dokumentaci](./reference-ubuntu-vm.md) pro tento virtuální počítač.
