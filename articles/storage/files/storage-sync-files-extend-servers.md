---
title: Kurz – rozšíření souborové servery Windows pomocí služby Azure File Sync | Dokumentace Microsoftu
description: Zjistěte, jak rozšířit souborové servery Windows pomocí služby Azure File Sync, od začátku do konce.
services: storage
author: wmgries
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 661df6039948539d6b50b4c8caf8ca7bd9b58730
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321483"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Kurz: Rozšíření souborových serverů Windows s využitím Synchronizace souborů Azure

Tento článek ukazuje základní kroky pro rozšíření kapacity úložiště Windows serveru pomocí Azure File Sync. I když se v kurzu funkce systému Windows Server jako virtuální počítač Azure (VM), bude tento proces obvykle děláte u místních serverů. Pokyny pro nasazení ve vašem prostředí v Azure File Sync najdete [nasazení Azure File Sync](storage-sync-files-deployment-guide.md) článku.

> [!div class="checklist"]
> * Nasazení služby synchronizace úložiště
> * Příprava Windows Serveru na použití se Synchronizací souborů Azure
> * Instalace agenta Synchronizace souborů Azure
> * Zaregistrovat do služby synchronizace úložiště Windows serveru
> * Vytvoření skupiny synchronizace a koncového bodu cloudu
> * Vytvoření koncového bodu serveru

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Příprava prostředí

Pro účely tohoto kurzu budete muset před nasazením Azure File Sync, postupujte takto:

- Vytvoření služby Azure storage souborů a sdílené složky
- Nastavení virtuálního počítače s Windows serverem 2016 Datacenter
- Připravit systém Windows Server virtuálních počítačů pro Azure File Sync

### <a name="create-a-folder-and-txt-file"></a>Vytvoření složky a souboru .txt

Na svém místním počítači vytvořte novou složku _FilesToSync_ a přidejte do ní textový soubor _mytestdoc.txt_. Tento soubor nahrajete do sdílené složky souboru v tomto kurzu.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Vytvoření sdílené složky

Poté, co nasadíte účet úložiště Azure, vytvořte sdílenou složku.

1. Na webu Azure Portal, vyberte **přejít k prostředku**.
1. Vyberte **soubory** z podokna účtu úložiště.

    ![Vyberte soubory](./media/storage-sync-files-extend-servers/click-files.png)

1. Vyberte **+ sdílená složka,**.

    ![Vyberte tlačítko Přidat sdílenou složku souboru](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Název nové sdílené složky _afsfileshare_. Zadejte "1" **kvóty**a pak vyberte **vytvořit**. Kvóta může být maximálně 5 TiB, ale pro účely tohoto kurzu vám stačí 1 GB.

    ![Zadání názvu a kvóty pro novou sdílenou složku](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Vyberte nové sdílené složky. Na umístění sdílené složky souboru, vyberte **nahrát**.

    ![Nahrání souboru](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Přejděte _FilesToSync_ složku, ve které jste vytvořili svůj soubor .txt, vyberte _mytestdoc.txt_ a vyberte **nahrát**.

    ![Procházení sdílené složky](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

V tomto okamžiku jste vytvořili účet úložiště a sdílené složky s jedním souborem v ní. V dalším kroku nasaďte virtuální počítač Azure s Windows Server 2016 Datacenter znázornit na místním serveru v tomto kurzu.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Nasazení virtuálního počítače a připojení datového disku

1. Přejděte na web Azure Portal a rozbalením nabídky na levé straně. Zvolte **vytvořit prostředek** v levém horním rohu.
1. Na panelu hledání nad seznamem **Azure Marketplace** hledat prostředky, **systému Windows Server 2016 Datacenter** a vyberte ve výsledcích. Zvolte **Vytvořit**.
1. Přejděte **Základy** kartu. V části **podrobnosti o projektu**, vyberte skupinu prostředků, kterou jste vytvořili pro účely tohoto kurzu.

   ![Zadání základních informací o virtuálním počítači v okně portálu](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. V části **Instance podrobnosti**, zadejte název virtuálního počítače. Například použít _myVM_.
1. Neměnit výchozí nastavení pro **oblasti**, **možností dostupnosti**, **Image**, a **velikost**.
1. V části **Účet správce** zadejte **Uživatelské jméno** a **Heslo** pro virtuální počítač.
1. V části **příchozí pravidla portů**, zvolte **povolit vybrané porty** a pak vyberte **protokolu RDP (3389)** a **HTTP** z rozevírací nabídky.

1. Před vytvořením virtuálního počítače je potřeba vytvořit datový disk.

   1. Vyberte **Další: disky**.

      ![Použití datových disků](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. Na **disky** ve skupině **disku možnosti**, ponechte výchozí nastavení.
   1. V části **datové disky**vyberte **vytvořit a připojit nový disk**.

   1. Použít výchozí nastavení s výjimkou **velikost (GiB)**, které můžete změnit na **1 GB** pro účely tohoto kurzu.

      ![Podrobnosti o datovém disku](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. Vyberte **OK**.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.

   Můžete vybrat **oznámení** ikonu a podívejte se **průběh nasazení**. Vytváření nového virtuálního počítače může trvat několik minut.

1. Po dokončení nasazení virtuálního počítače vyberte **přejít k prostředku**.

   ![Přejít k prostředku](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

Právě jste vytvořili nový virtuální počítač a připojili jste datový disk. Další připojení k virtuálnímu počítači.

### <a name="connect-to-your-vm"></a>Připojení k virtuálnímu počítači

1. Na webu Azure Portal, vyberte **připojit** na stránce vlastností virtuálního počítače.

   ![Připojení k virtuálnímu počítači Azure z portálu](./media/storage-sync-files-extend-servers/connect-vm.png)

1. Na **připojit k virtuálnímu počítači** stránce, ponechte výchozí nastavení pro připojení s **IP adresu** přes port 3389. Vyberte **Stáhnout soubor RDP**.

   ![Stažení souboru RDP](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Otevřete stažený soubor RDP a vyberte **připojit** po zobrazení výzvy.
1. V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte uživatelské jméno jako *localhost\username*, zadejte heslo, které jste vytvořili pro virtuální počítač a potom vyberte **OK**.

   ![Další možnosti](./media/storage-sync-files-extend-servers/local-host2.png)

1. Při přihlášení se může zobrazit upozornění na certifikát. Vyberte **Ano** nebo **pokračovat** k vytvoření připojení.

### <a name="prepare-the-windows-server"></a>Příprava serveru Windows

Server Windows Server 2016 Datacenter zakažte konfiguraci rozšířeného zabezpečení aplikace Internet Explorer. Tento krok se vyžaduje pouze při počáteční registraci serveru. Po zaregistrování serveru můžete tuto možnost znovu povolit.

V systému Windows Server 2016 Datacenter virtuální počítač se automaticky otevře správce serveru.  Pokud se ve výchozím nastavení se neotevře správce serveru, vyhledejte ho v Průzkumníku souborů.

1. V **správce serveru**vyberte **místní Server**.

   ![Místní server na levé straně uživatelského rozhraní Správce serveru](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. Na **vlastnosti** podokně, vyberte odkaz pro **konfigurace rozšířeného zabezpečení Internet Exploreru**.  

    ![Podokno Konfigurace rozšířeného zabezpečení aplikace Internet Explorer v uživatelském rozhraní Správce serveru](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. V dialogovém okně **Konfigurace rozšířeného zabezpečení aplikace Internet Explorer** vyberte u položek **Správci** a **Uživatelé** možnost **Vypnuto**.

    ![Automaticky otevírané okno Konfigurace rozšířeného zabezpečení aplikace Internet Explorer s vybranou možností Vypnuto](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

Teď můžete k virtuálnímu počítači přidat datový disk.

### <a name="add-the-data-disk"></a>Přidání datového disku

1. Pokud máte **systému Windows Server 2016 Datacenter** virtuálního počítače, vyberte **soubory a služby úložiště** > **svazky** > **disky** .

    ![Datový disk](media/storage-sync-files-extend-servers/your-disk.png)

1. Klikněte pravým tlačítkem na 1 GB disk s názvem **MSFT na úrovni virtuálního disku** a vyberte **nový svazek**.
1. Dokončete průvodce. Použít výchozí nastavení a poznamenejte si písmeno jednotky přiřazené.
1. Vyberte **Vytvořit**.
1. Vyberte **Zavřít**.

   Právě jste disk převedli do stavu online a vytvořili jste svazek. Otevřete Průzkumníka souborů do virtuálního počítače s Windows serverem potvrďte přítomnost nedávno přidal datový disk.

1. V Průzkumníku souborů ve virtuálním počítači, rozbalte **tento počítač** a otevřete novou jednotku. V tomto příkladu je to jednotka F:.
1. Klikněte pravým tlačítkem a vyberte **Nový** > **Složka**. Pojmenujte složku _FilesToSync_.
1. Otevřít **FilesToSync** složky.
1. Klikněte pravým tlačítkem a vyberte **Nový** > **Textový dokument**. Pojmenujte textový soubor _MyTestFile_.

    ![Přidání nového textového souboru](media/storage-sync-files-extend-servers/new-file.png)

1. Zavřít **Průzkumník souborů** a **správce serveru**.

### <a name="download-the-azure-powershell-module"></a>Stáhnout modul Azure PowerShell

V dalším kroku v systému Windows Server 2016 Datacenter virtuální počítač, nainstalujte modul Azure PowerShell na serveru.

1. Ve virtuálním počítači otevřete okno Powershellu se zvýšenými oprávněními.
1. Spusťte následující příkaz:

   ```powershell
   Install-Module -Name AzureRm
   ```

   > [!NOTE]
   > Pokud máte verzi NuGet, který je starší než 2.8.5.201, budete vyzváni ke stažení a instalaci nejnovější verze Nugetu.

   Ve výchozím nastavení není Galerie prostředí PowerShell pro PowerShellGet nakonfigurovaná jako důvěryhodné úložiště. Při prvním použití PSGallery, se zobrazí následující výzva:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. Odpověď **Ano** nebo **Ano všem** pokračujte v instalaci.

Modul `AzureRM` je kumulativní modul pro rutiny Azure PowerShellu. Jeho instalace stáhne všechny dostupné moduly Azure Resource Manageru a zpřístupní jejich rutiny k použití.

V tomto okamžiku jste nastavili prostředí pro tento kurz. Jste připraveni nasadit službu synchronizace úložiště.

## <a name="deploy-the-service"></a>Nasazení služby

K nasazení služby Azure File Sync, je třeba nejprve umístit **služba synchronizace úložiště** prostředků do skupiny prostředků pro vybrané předplatné. Služba synchronizace úložiště zdědí oprávnění k přístupu z jeho předplatné a skupinu prostředků.

1. Na webu Azure Portal, vyberte **vytvořit prostředek** a vyhledejte **Azure File Sync**.
1. Ve výsledcích hledání vyberte **Synchronizace souborů Azure**.
1. Výběrem možnosti **Vytvořit** otevřete kartu **Nasadit synchronizaci úložiště**.

   ![Nasazení synchronizace úložiště](media/storage-sync-files-extend-servers/afs-info.png)

   V podokně, které se otevře, zadejte následující informace:

   | Hodnota | Popis |
   | ----- | ----- |
   | **Název** | Jedinečný název (pro každé předplatné) služby synchronizace úložiště.<br><br>Použití _afssyncservice02_ pro účely tohoto kurzu. |
   | **Předplatné** | Předplatné Azure, které používáte pro účely tohoto kurzu. |
   | **Skupina prostředků** | Skupina prostředků, která obsahuje službu synchronizace úložiště.<br><br>Použití _afsresgroup101918_ pro účely tohoto kurzu. |
   | **Umístění** | USA – východ |

1. Jakmile budete hotovi, vyberte **Vytvořit** a nasaďte **službu synchronizace úložiště**.
1. Vyberte **oznámení** kartu > **přejít k prostředku**.

## <a name="install-the-agent"></a>Instalace agenta

Agent Synchronizace souborů Azure je balíček ke stažení, který umožňuje synchronizaci Windows Serveru se sdílenou složkou Azure.

1. V **systému Windows Server 2016 Datacenter** virtuální počítač, Otevřít **aplikace Internet Explorer**.
1. Přejděte na web [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Přejděte dolů k položce **Azure File Sync Agent** a vyberte **Stáhnout**.

   ![Stažení agenta synchronizace](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Zaškrtněte políčko pro **StorageSyncAgent_V3_WS2016. Soubor EXE** a vyberte **Další**.

   ![Výběr agenta](media/storage-sync-files-extend-servers/select-agent.png)

1. Vyberte **povolit jednou** > **spustit** > **otevřít**.
1. Pokud jste to ještě neudělali, zavřete okno PowerShellu.
1. V **Průvodci instalací agenta synchronizace úložiště** přijměte výchozí hodnoty.
1. Vyberte **Install** (Nainstalovat).
1. Vyberte **Finish** (Dokončit).

Jste nasadili službu synchronizace Azure a instalace agenta na virtuálním počítači se systémem Windows Server 2016 Datacenter. Teď budete muset virtuální počítač zaregistrovat do služby synchronizace úložiště.

## <a name="register-windows-server"></a>Registrace Windows Serveru

Registrace serveru Windows pomocí služby synchronizace úložiště vytváří vztah důvěryhodnosti mezi serverem (nebo clusteru) a služba synchronizace úložiště. Server lze zaregistrovat pouze do jedné služby synchronizace úložiště. Můžete synchronizovat s jinými servery a sdílené složky Azure, které jsou spojené s touto službou synchronizace úložiště.

Uživatelské rozhraní serveru registrace otvírat automaticky po instalaci agenta Azure File Sync. Pokud tomu tak není, lze jej otevřít ručně z umístění souboru: `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`

1. Při registraci uživatelského rozhraní serveru otevře ve virtuálním počítači, vybrat **OK**.
1. Vyberte **přihlášení** začít.
1. Přihlaste se pomocí přihlašovacích údajů k účtu Azure a vyberte **přihlášení**.
1. Zadejte tyto informace:

   ![Snímek obrazovky uživatelského rozhraní pro registraci serveru](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Hodnota | Popis |
   | **Předplatné Azure** | Předplatné, které obsahuje službu synchronizace úložiště pro účely tohoto kurzu. |
   | **Skupina prostředků** | Skupina prostředků, která obsahuje službu synchronizace úložiště. Použití _afsresgroup101918_ pro účely tohoto kurzu. |
   | **Služba synchronizace úložiště** | Název služby synchronizace úložiště. Použití _afssyncservice02_ pro účely tohoto kurzu. |

1. Vyberte **zaregistrovat** k dokončení registrace serveru.
1. Jako součást procesu registrace budete vyzváni další přihlásit. Přihlaste se a vyberte **Další**.
1. Vyberte **OK**.

## <a name="create-a-sync-group"></a>Vytvoření skupiny synchronizace

Skupina synchronizace definuje topologii synchronizace sady souborů. Skupina synchronizace musí obsahovat jeden koncový bod cloudu, který představuje sdílenou složku Azure. Skupina synchronizace musí obsahovat také jeden nebo více koncových bodů serveru. Koncový bod serveru představuje cestu na zaregistrovaném serveru. Chcete-li vytvořit skupinu synchronizace:

1. V [webu Azure portal](https://portal.azure.com/)vyberte **+ skupina synchronizace** od služby synchronizace úložiště. Použití *afssyncservice02* pro účely tohoto kurzu.

   ![Vytvoření nové skupiny synchronizace na webu Azure Portal](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Zadejte následující informace, chcete-li vytvořit skupinu synchronizace s koncovým bodem cloudové:

   | Hodnota | Popis |
   | ----- | ----- |
   | **Název skupiny synchronizace** | Tento název musí být jedinečný v rámci služby synchronizace úložiště, ale může to být libovolný název, který vám dává smysl. Použití *afssyncgroup* pro účely tohoto kurzu.|
   | **Předplatné** | Předplatné, do kterého jste nasadili službu synchronizace úložiště pro účely tohoto kurzu. |
   | **Účet úložiště** | Zvolte **vyberte účet úložiště**. V podokně se zobrazí vyberte účet úložiště obsahující sdílenou složku Azure, kterou jste vytvořili. Použití *afsstoracct101918* pro účely tohoto kurzu. |
   | **Sdílená složka Azure** | Název sdílené složky Azure, kterou jste vytvořili. Použití *afsfileshare* pro účely tohoto kurzu. |

1. Vyberte **Vytvořit**.

Pokud vyberete svou skupinu synchronizace, uvidíte, že teď máte jeden **koncový bod cloudu**.

## <a name="add-a-server-endpoint"></a>Přidání koncového bodu serveru

Koncový bod serveru představuje určitého umístění na registrovaném serveru. Například složkou ve svazku serveru. Chcete-li přidat koncový bod serveru:

1. Vyberte nově vytvořený synchronizace skupiny a pak vyberte **přidat koncový bod serveru**.

   ![Přidání nového koncového bodu serveru v podokně skupiny synchronizace](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. Na **přidat koncový bod serveru** podokně zadejte následující informace a vytvořte koncový bod serveru:

   | | |
   | ----- | ----- |
   | Hodnota | Popis |
   | **Zaregistrovaný server** | Název serveru, který jste vytvořili. Použití *afsvm101918* pro účely tohoto kurzu. |
   | **Cesta** | Cesta Windows serveru na jednotku, kterou jste vytvořili. Použití *f:\filestosync* v tomto kurzu. |
   | **Vrstvení cloudu** | Pro účely tohoto kurzu nechte tuto možnost zakázanou. |
   | **Volné místo svazku** | Pro účely tohoto kurzu ponechte tuto hodnotu prázdnou. |

1. Vyberte **Vytvořit**.

Vaše soubory se teď synchronizují mezi sdílenou složkou Azure a Windows Serverem.

![Úspěšně synchronizovaná služba Azure Storage](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, základní kroky pro rozšíření kapacity úložiště Windows serveru pomocí Azure File Sync. Podrobnější pohled na plánování nasazení služby Azure File Sync naleznete v tématu:

> [!div class="nextstepaction"]
> [Plánování nasazení Synchronizace souborů Azure](./storage-sync-files-planning.md)
