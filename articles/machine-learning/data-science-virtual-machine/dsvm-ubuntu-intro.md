---
title: 'Rychlý Start: vytvoření Data Science Virtual Machine Ubuntu'
titleSuffix: Azure Data Science Virtual Machine
description: Nakonfigurujte a vytvořte Data Science Virtual Machine pro Linux (Ubuntu), abyste mohli provádět analýzy a strojové učení.
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: quickstart
ms.date: 03/10/2020
ms.openlocfilehash: b25d9846f3bb678c182fa22d824f7b4171d2d803
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515627"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-linux-ubuntu"></a>Rychlý Start: nastavení Data Science Virtual Machine pro Linux (Ubuntu)

Načtěte si Ubuntu 18,04 Data Science Virtual Machine a spusťte ho.

## <a name="prerequisites"></a>Požadavky

Pokud chcete vytvořit Data Science Virtual Machine 18,04 Ubuntu, musíte mít předplatné Azure. [Vyzkoušejte si Azure zdarma](https://azure.com/free).

>[!NOTE]
>Bezplatné účty Azure nepodporují skladové položky virtuálních počítačů s podporou GPU.

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Vytvoření Data Science Virtual Machine pro Linux

Tady je postup vytvoření instance Data Science Virtual Machine Ubuntu 18,04:

1. Přejděte na [Azure Portal](https://portal.azure.com). Může se zobrazit výzva, abyste se přihlásili ke svému účtu Azure, pokud ještě nejste přihlášení.
1. Vyhledejte výpis virtuálního počítače zadáním příkazu "virtuální počítač pro datové vědy" a výběrem Data Science Virtual Machine-Ubuntu 18,04.

1. V dalším okně vyberte **vytvořit**.

1. Měli byste se přesměrovat na okno vytvořit virtuální počítač.
   
1. Zadáním následujících informací nakonfigurujte jednotlivé kroky průvodce:

    1. **Základy**:
    
       * **Předplatné**: Pokud máte více než jedno předplatné, vyberte ten, na kterém se bude počítač vytvářet a účtují. Toto předplatné musí mít oprávnění vytvářet prostředky.
       * **Skupina prostředků**: Vytvořte novou skupinu nebo použijte existující.
       * **Název virtuálního počítače**: zadejte název virtuálního počítače. Tento název se použije ve vašem Azure Portal.
       * **Oblast**: vyberte příslušné datové centrum. Pro nejrychlejší přístup k síti je to datové centrum, které má většinu vašich dat nebo je nejblíže vašemu fyzickému umístění. Přečtěte si další informace o [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Obrázek**: ponechte výchozí hodnotu.
       * **Velikost**: Tato možnost by měla automaticky naplnit velikost, která je vhodná pro obecné úlohy. Přečtěte si další informace o [velikostech virtuálních počítačů s Linux v Azure](../../virtual-machines/sizes.md).
       * **Typ ověřování**: pro rychlejší nastavení vyberte možnost heslo. 
         
         > [!NOTE]
         > Pokud máte v úmyslu používat JupyterHub, ujistěte se, že jste vybrali možnost "heslo", protože JupyterHub *není nakonfigurován k* používání veřejných klíčů ssh.

       * **Uživatelské jméno**: zadejte uživatelské jméno správce. Toto uživatelské jméno použijete k přihlášení k virtuálnímu počítači. Toto uživatelské jméno se nemusí shodovat s vaším uživatelským jménem Azure. Nepoužívejte *Velká* písmena.
         
         > [!IMPORTANT]
         > Pokud v uživatelském jméně použijete velká písmena, JupyterHub nebude fungovat a dojde k chybě 500 interního serveru.

       * **Heslo**: zadejte heslo, které budete používat pro přihlášení k virtuálnímu počítači.    
    
   1. Vyberte **Zkontrolovat a vytvořit**.
   1. **Zkontrolovat a vytvořit**
      * Ověřte správnost všech zadaných informací. 
      * Vyberte **Vytvořit**.
    
    Zřizování by mělo trvat přibližně 5 minut. Stav se zobrazí v Azure Portal.

## <a name="how-to-access-the-ubuntu-data-science-virtual-machine"></a>Jak přistupovat k Ubuntu Data Science Virtual Machine

K Ubuntu DSVM máte přístup jedním ze tří způsobů:

  * SSH pro terminálové relace
  * X2Go pro grafické relace
  * JupyterHub a JupyterLab pro poznámkové bloky Jupyter

### <a name="ssh"></a>SSH

Pokud jste virtuální počítač nakonfigurovali pomocí ověřování SSH, můžete se přihlásit pomocí přihlašovacích údajů k účtu, které jste vytvořili v části **základy** kroku 3 pro rozhraní textového prostředí. Ve Windows si můžete stáhnout klientský nástroj SSH [, jako je](https://www.putty.org)například výstup. Pokud dáváte přednost grafické ploše (systém Windows X), můžete použít předávání X11 na výstupu.

> [!NOTE]
> Klient X2Go v testování provedl lepší předávání dat než X11. Pro grafické rozhraní plochy doporučujeme použít klienta X2Go.

### <a name="x2go"></a>X2Go

Virtuální počítač se systémem Linux je již zřízený serverem X2Go a připraven k přijetí připojení klienta. Pokud se chcete připojit k virtuální grafické ploše se systémem Linux, proveďte na svém klientovi následující postup:

1. Stáhněte a nainstalujte klienta X2Go pro klientskou platformu z [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Poznamenejte si veřejnou IP adresu virtuálního počítače, kterou můžete najít v Azure Portal otevřením virtuálního počítače, který jste vytvořili.

   ![IP adresa počítače Ubuntu](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

1. Spusťte klienta X2Go. Pokud se okno Nová relace automaticky neotevře, přečtěte si relaci-> novou relaci.

1. Ve výsledném okně konfigurace zadejte následující konfigurační parametry:
   * **Karta Relace**:
     * **Hostitel**: Zadejte IP adresu vašeho virtuálního počítače, kterou jste si poznamenali dříve.
     * **Přihlášení**: Zadejte uživatelské jméno na virtuálním počítači se systémem Linux.
     * **Port SSH**: Ponechte ho na výchozí hodnotě 22.
     * **Typ relace**: Změňte hodnotu na **XFCE**. Virtuální počítač se systémem Linux v současné době podporuje pouze desktop XFCE.
   * **Karta Media (Média)**: Pokud nepotřebujete používat zvukovou podporu a tisk klienta, můžete je vypnout.
   * **Sdílené složky**: pomocí této karty Přidejte adresář klientských počítačů, který chcete připojit k virtuálnímu počítači. 

   ![Konfigurace X2go](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Vyberte **OK**.
1. Kliknutím na pole v pravém podokně okna X2Go otevřete obrazovku pro přihlášení k vašemu VIRTUÁLNÍmu počítači.
1. Zadejte heslo k virtuálnímu počítači.
1. Vyberte **OK**.
1. Je možné, že budete muset udělit oprávnění nástroji X2Go, aby se vynechala brána firewall a bylo možné dokončit připojení.
1. Nyní byste měli vidět grafické rozhraní pro Ubuntu DSVM. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub a JupyterLab

Ubuntu DSVM spouští [JupyterHub](https://github.com/jupyterhub/jupyterhub), víceuživatelského Jupyter serveru. Chcete-li se připojit, proveďte následující kroky:

   1. Poznamenejte si veřejnou IP adresu svého virtuálního počítače tak, že na Azure Portal vyhledáte a vyberete svůj virtuální počítač.
      ![IP adresa počítače Ubuntu](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

   1. Z místního počítače otevřete webový prohlížeč a přejděte na https: \/ /Your-VM-IP: 8000 a nahraďte "Your-VM-IP" IP adresou, kterou jste si poznamenali dříve.
   1. Váš prohlížeč vám pravděpodobně znemožní otevřít stránku přímo a oznamuje vám, že došlo k chybě certifikátu. DSVM zajišťuje zabezpečení prostřednictvím certifikátu podepsaného svým držitelem. Většina prohlížečů vám po tomto upozornění umožní kliknout na. Mnoho prohlížečů bude nadále poskytovat určitý druh vizuálního upozornění na certifikát v rámci vaší webové relace.

      >[!NOTE]
      > Pokud se `ERR_EMPTY_RESPONSE` v prohlížeči zobrazí chybová zpráva, ujistěte se, že k počítači přistupujete explicitně pomocí protokolu *https* , a ne pomocí *protokolu HTTP* nebo pouze webové adresy. Pokud zadáte webovou adresu bez na `https://` adresní lince, bude se ve výchozím nastavení většina prohlížečů `http` zobrazovat a zobrazí se tato chyba.

   1. Zadejte uživatelské jméno a heslo, které jste použili k vytvoření virtuálního počítače, a přihlaste se. 

      ![Zadejte Jupyter přihlášení.](./media/dsvm-ubuntu-intro/jupyter-login.png)

      >[!NOTE]
      > Pokud v této fázi obdržíte chybu 500, je pravděpodobně v uživatelském jménu použita velká písmena. Jedná se o známou interakci mezi Jupyter centrem a PAMAuthenticator, kterou používá. Pokud se zobrazí chyba "nelze se spojit s touto stránkou", je pravděpodobně nutné upravit oprávnění skupiny zabezpečení sítě. V Azure Portal vyhledejte prostředek skupiny zabezpečení sítě v rámci vaší skupiny prostředků. Pokud chcete získat přístup k JupyterHub z veřejného Internetu, musíte mít otevřený port 8000. (Obrázek ukazuje, že tento virtuální počítač je nakonfigurovaný pro přístup za běhu, což se důrazně doporučuje. Přečtěte si téma [zabezpečení portů pro správu pomocí přístupu za běhu](../../security-center/security-center-just-in-time.md).) ![Konfigurace skupiny zabezpečení sítě](./media/dsvm-ubuntu-intro/nsg-permissions.png)

   1. Projděte si mnoho dostupných ukázkových poznámkových bloků.

JupyterLab, je k dispozici také další generace poznámkových bloků Jupyter a JupyterHub. Abyste k němu měli přístup, přihlaste se k JupyterHub a pak přejděte na adresu URL https: \/ /Your-VM-IP: 8000/User/Your-username/Lab a nahraďte "Your-username" uživatelským jménem, které jste si zvolili při konfiguraci virtuálního počítače. Je možné, že se zpočátku zablokuje přístup k webu z důvodu chyby certifikátu.

Můžete nastavit JupyterLab jako výchozí Poznámkový Server přidáním tohoto řádku do `/etc/jupyterhub/jupyterhub_config.py` :

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Další kroky

Tady je postup, jak můžete pokračovat ve studiu a průzkumu:

* V názorných kurzech [Data Science Virtual Machine pro Linux](linux-dsvm-walkthrough.md) se dozvíte, jak provést několik běžných úloh vědeckého zpracování dat se systémem Linux DSVM zřízeným zde. 
* Vyzkoušením nástrojů popsaných v tomto článku prozkoumejte různé nástroje pro datové vědy na DSVM. V `dsvm-more-info` prostředí virtuálního počítače můžete také spustit základní Úvod a odkazy na Další informace o nástrojích nainstalovaných na virtuálním počítači.  
* Naučte se systematicky sestavovat Analytická řešení pomocí [vědeckého zpracování týmových dat](../team-data-science-process/index.yml).
* Podívejte se na [Azure AI Gallery](https://gallery.azure.ai/) pro strojové učení a ukázky analýzy dat, které používají služby Azure AI.
* Projděte si příslušnou [referenční dokumentaci](./reference-ubuntu-vm.md) pro tento virtuální počítač.