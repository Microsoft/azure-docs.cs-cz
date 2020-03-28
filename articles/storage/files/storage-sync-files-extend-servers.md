---
title: Kurz – Rozšíření souborových serverů Windows pomocí Azure File Sync | Dokumenty společnosti Microsoft
description: Zjistěte, jak rozšířit souborové servery Windows pomocí Azure File Sync od začátku do konce.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: eb00234fb7522c763dbaa910bee99cf327bebaf1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77597894"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Kurz: Rozšíření souborových serverů Windows s využitím Synchronizace souborů Azure

Tento článek ukazuje základní kroky pro rozšíření kapacity úložiště serveru Windows pomocí Azure File Sync. Přestože kurz obsahuje Windows Server jako virtuální počítač Azure (VM), obvykle byste tento proces provést pro místní servery. Pokyny k nasazení Azure File Sync ve vlastním prostředí najdete v článku [Nasazení synchronizace souborů Azure.](storage-sync-files-deployment-guide.md)

> [!div class="checklist"]
> * Nasazení služby synchronizace úložiště
> * Příprava Windows Serveru na použití se Synchronizací souborů Azure
> * Instalace agenta Synchronizace souborů Azure
> * Registrace systému Windows Server pomocí služby Synchronizace úložiště
> * Vytvoření skupiny synchronizace a koncového bodu cloudu
> * Vytvoření koncového bodu serveru

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [portálu Azure](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Příprava prostředí

V tomto kurzu je třeba provést následující kroky před nasazením Azure File Sync:

- Vytvoření účtu úložiště Azure a sdílené složky
- Nastavení virtuálního aplikace datacentra Windows Server 2016
- Příprava virtuálního počítače se systémem Windows Server na synchronizaci souborů Azure

### <a name="create-a-folder-and-txt-file"></a>Vytvoření složky a souboru .txt

Na svém místním počítači vytvořte novou složku _FilesToSync_ a přidejte do ní textový soubor _mytestdoc.txt_. Tento soubor nahrajete do sdílené složky později v tomto kurzu.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Vytvoření sdílené složky

Po nasazení účtu úložiště Azure vytvoříte sdílenou složku.

1. Na webu Azure Portal vyberte **Přejít na prostředek**.
1. V podokně účtu úložiště vyberte **Soubory.**

    ![Vybrat soubory](./media/storage-sync-files-extend-servers/click-files.png)

1. Vyberte **+ Sdílení souborů**.

    ![Výběr tlačítka Přidat sdílení souborů](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Pojmenujte novou sdílenou složku _afsfileshare_. Zadejte "1" pro **kvótu**a pak vyberte **Vytvořit**. Kvóta může být maximálně 5 TiB, ale pro účely tohoto kurzu vám stačí 1 GB.

    ![Zadání názvu a kvóty pro novou sdílenou složku](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Vyberte novou sdílenou složku. V umístění sdílené složky vyberte **Nahrát**.

    ![Nahrání souboru](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Přejděte do složky _FilesToSync,_ do které jste vytvořili soubor TXT, vyberte _soubor mytestdoc.txt_ a vyberte **Nahrát**.

    ![Procházení sdílené složky](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

V tomto okamžiku jste vytvořili účet úložiště a sdílenou složku s jedním souborem v něm. Dále nasadíte virtuální počítač Azure s Windows Server 2016 Datacenter reprezentovat místní server v tomto kurzu.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Nasazení virtuálního počítače a připojení datového disku

1. Přejděte na portál Azure a rozbalte nabídku vlevo. V levém horním rohu **zvolte Vytvořit zdroj.**
1. Ve vyhledávacím poli nad seznamem prostředků **Azure Marketplace** vyhledejte Datové centrum Windows **Serveru 2016** a vyberte ho ve výsledcích. Zvolte **Vytvořit**.
1. Přejděte na kartu **Základy.** V části **Podrobnosti projektu**vyberte skupinu zdrojů, kterou jste pro tento kurz vytvořili.

   ![Zadání základních informací o virtuálním počítači na okně portálu](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. V **části Podrobnosti instance**zadejte název virtuálního_kva. Použijte například _myVM_.
1. Neměňte výchozí nastavení pro **oblast**, **možnosti dostupnosti**, **Obrázek**a **Velikost**.
1. V části **Účet správce** zadejte **Uživatelské jméno** a **Heslo** pro virtuální počítač.
1. V části **Pravidla příchozího portu**zvolte **Povolit vybrané porty** a v rozevírací nabídce vyberte **protokolrdt (3389)** a **HTTP.**

1. Před vytvořením virtuálního počítače je potřeba vytvořit datový disk.

   1. Vyberte **Další:Disky**.

      ![Použití datových disků](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. Na kartě **Disky** ponechejte v části **Možnosti disku**výchozí hodnoty.
   1. V části **DATA DISKS**vyberte **Vytvořit a připojte nový disk**.

   1. Použijte výchozí nastavení s výjimkou **Velikost (GiB)**, které můžete změnit na **1 GB** pro tento kurz.

      ![Podrobnosti o datovém disku](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. Vyberte **OK**.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.

   Můžete vybrat ikonu **Oznámení** a sledovat **průběh nasazení**. Vytvoření nového virtuálního virtuálního movitého virtuálního<eseje může trvat několik minut.

1. Po dokončení nasazení virtuálního počítače vyberte **Přejít na prostředek**.

   ![Přejít k prostředku](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

Právě jste vytvořili nový virtuální počítač a připojili jste datový disk. Dále se připojíte k virtuálnímu virtuálnímu virtuálnímu zařízení.

### <a name="connect-to-your-vm"></a>Připojení k virtuálnímu počítači

1. Na webu Azure Portal vyberte **Připojit** na stránce vlastností virtuálního počítače.

   ![Připojení k virtuálnímu počítači Azure z portálu](./media/storage-sync-files-extend-servers/connect-vm.png)

1. Na stránce **Připojit k virtuálnímu počítači** zachovat výchozí možnosti pro připojení pomocí **IP adresy** přes port 3389. Vyberte **Stáhnout soubor RDP**.

   ![Stažení souboru RDP](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Otevřete stažený soubor RDP a po zobrazení výzvy vyberte **Připojit.**
1. V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte uživatelské jméno jako *localhost\username*, zadejte heslo, které jste pro virtuální počítač vytvořili, a vyberte **OK**.

   ![Další možnosti](./media/storage-sync-files-extend-servers/local-host2.png)

1. Při přihlášení se může zobrazit upozornění na certifikát. Chcete-li vytvořit připojení, vyberte **ano** nebo **Pokračovat.**

### <a name="prepare-the-windows-server"></a>Příprava serveru Windows

Pro server Windows Server 2016 Datacenter zakažte možnost Konfigurace rozšířeného zabezpečení aplikace Internet Explorer. Tento krok se vyžaduje pouze při počáteční registraci serveru. Po zaregistrování serveru můžete tuto možnost znovu povolit.

V virtuálním zařízení datacentra Windows Server 2016 se Správce serveru otevře automaticky.  Pokud se Správce serveru ve výchozím nastavení neotevře, vyhledejte ho v nabídce Start.

1. Ve **Správci serveru**vyberte **možnost Místní server**.

   ![Místní server na levé straně uživatelského rozhraní Správce serveru](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. V podokně **Vlastnosti** vyberte odkaz pro **konfiguraci rozšířeného zabezpečení aplikace IE**.  

    ![Podokno Konfigurace rozšířeného zabezpečení aplikace Internet Explorer v uživatelském rozhraní Správce serveru](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. V dialogovém okně **Konfigurace rozšířeného zabezpečení aplikace Internet Explorer** vyberte u položek **Správci** a **Uživatelé** možnost **Vypnuto**.

    ![Automaticky otevírané okno Konfigurace rozšířeného zabezpečení aplikace Internet Explorer s vybranou možností Vypnuto](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

Teď můžete k virtuálnímu počítači přidat datový disk.

### <a name="add-the-data-disk"></a>Přidání datového disku

1. Zatímco jste ještě ve virtuálním počítače **datového centra Windows Server 2016,** vyberte **Soubory a disky svazků** > **Volumes** > úložiště **.**

    ![Datový disk](media/storage-sync-files-extend-servers/your-disk.png)

1. Klepněte pravým tlačítkem myši na disk o velikosti 1 GB s názvem **Virtuální disk Msft** a vyberte příkaz **Nový svazek**.
1. Dokončete průvodce. Použijte výchozí nastavení a poznamenejte si přiřazené písmeno jednotky.
1. Vyberte **Vytvořit**.
1. Vyberte **Zavřít**.

   Právě jste disk převedli do stavu online a vytvořili jste svazek. Chcete-li potvrdit přítomnost naposledy přidaného datového disku, otevřete v modulu Windows Server VM Průzkumníksouborů.

1. V Průzkumníkovi souborů ve virtuálním počítači rozbalte **tento počítač** a otevřete novou jednotku. V tomto příkladu je to jednotka F:.
1. Klepněte pravým tlačítkem myši a vyberte **položku Nová** > **složka**. Pojmenujte složku _FilesToSync_.
1. Otevřete složku **FilesToSync.**
1. Klepněte pravým tlačítkem myši a vyberte **nový** > **textový dokument**. Pojmenujte textový soubor _MyTestFile_.

    ![Přidání nového textového souboru](media/storage-sync-files-extend-servers/new-file.png)

1. Zavřete **Průzkumníka souborů** a **Správce serveru**.

### <a name="download-the-azure-powershell-module"></a>Stažení modulu Azure PowerShell

Dále ve virtuálním počítači datacentra Windows Server 2016 nainstalujte modul Azure PowerShell na server.

1. Ve virtuálním provozu otevřete okno powershellu se zvýšenými oprávněními.
1. Spusťte následující příkaz:

   ```powershell
   Install-Module -Name Az
   ```

   > [!NOTE]
   > Pokud máte verzi NuGet, která je starší než 2.8.5.201, budete vyzváni ke stažení a instalaci nejnovější verze NuGet.

   Ve výchozím nastavení není Galerie prostředí PowerShell pro PowerShellGet nakonfigurovaná jako důvěryhodné úložiště. Při prvním použití psgallery, zobrazí se následující výzva:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. Chcete-li pokračovat v instalaci, odpovězte **ano** nebo **ano všem.**

Modul `Az` je kumulativní modul pro rutiny Azure PowerShellu. Jeho instalace stáhne všechny dostupné moduly Azure Resource Manageru a zpřístupní jejich rutiny k použití.

V tomto okamžiku jste nastavili prostředí pro kurz. Jste připraveni nasadit službu synchronizace úložiště.

## <a name="deploy-the-service"></a>Nasazení služby

Chcete-li nasadit Azure File Sync, nejprve umístěte prostředek **služby synchronizace úložiště** do skupiny prostředků pro vybrané předplatné. Služba synchronizace úložiště dědí přístupová oprávnění ze své skupiny předplatného a prostředků.

1. Na webu Azure Portal vyberte **Vytvořit prostředek** a pak vyhledejte Azure **File Sync**.
1. Ve výsledcích hledání vyberte **Synchronizace souborů Azure**.
1. Výběrem možnosti **Vytvořit** otevřete kartu **Nasadit synchronizaci úložiště**.

   ![Nasazení synchronizace úložiště](media/storage-sync-files-extend-servers/afs-info.png)

   V podokně, které se otevře, zadejte následující informace:

   | Hodnota | Popis |
   | ----- | ----- |
   | **Název** | Jedinečný název (pro každé předplatné) služby synchronizace úložiště.<br><br>Pro účely tohoto kurzu použijte _afssyncservice02._ |
   | **Předplatné** | Předplatné Azure, které používáte pro tento kurz. |
   | **Skupina prostředků** | Skupina prostředků, která obsahuje službu synchronizace úložiště.<br><br>Pro účely tohoto kurzu použijte _afsresgroup101918._ |
   | **Umístění** | USA – východ |

1. Jakmile budete hotovi, vyberte **Vytvořit** a nasaďte **službu synchronizace úložiště**.
1. Vyberte kartu **Oznámení** > **Přejít na zdroj**.

## <a name="install-the-agent"></a>Instalace agenta

Agent Synchronizace souborů Azure je balíček ke stažení, který umožňuje synchronizaci Windows Serveru se sdílenou složkou Azure.

1. Ve virtuálním provozu **datového centra Windows Server 2016** sem otevřete **aplikaci Internet Explorer**.
1. Přejděte na web [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Přejděte dolů do části **Azure File Sync Agent** a vyberte **Stáhnout**.

   ![Stažení agenta synchronizace](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Zaškrtněte políčko pro **StorageSyncAgent_V3_WS2016. EXE** a vyberte **Další**.

   ![Výběr agenta](media/storage-sync-files-extend-servers/select-agent.png)

1. Vyberte **Povolit jednou** > **spustit** > **otevřít**.
1. Pokud jste to ještě neudělali, zavřete okno PowerShellu.
1. V **Průvodci instalací agenta synchronizace úložiště** přijměte výchozí hodnoty.
1. Vyberte **Install** (Nainstalovat).
1. Vyberte **Finish** (Dokončit).

Na virtuální počítač s Windows Serverem 2016 Datacenter jste nasadili službu synchronizace Azure a nainstalovali agenta. Teď je potřeba virtuální počítač zaregistrovat ve službě synchronizace úložiště.

## <a name="register-windows-server"></a>Registrace Windows Serveru

Registrace serveru Windows pomocí služby Synchronizace úložiště vytvoří vztah důvěryhodnosti mezi serverem (nebo clusterem) a službou Synchronizace úložiště. Server lze zaregistrovat pouze na jednu službu synchronizace úložiště. Může se synchronizovat s jinými servery a sdílenými složkami Azure, které jsou přidruženy k této službě synchronizace úložiště.

UI registrace serveru by se měl otevřít automaticky po instalaci agenta Azure File Sync. Pokud tomu tak není, můžete jej otevřít ručně z umístění souboru:`C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`

1. Když se ve virtuálním virtuálním ms otevře nové tlačítko Registrace serveru, vyberte **OK**.
1. **Chcete-li začít, vyberte Přihlásit** se.
1. Přihlaste se pomocí přihlašovacích údajů k účtu Azure a vyberte **Přihlásit se**.
1. Zadejte tyto informace:

   ![Snímek obrazovky uživatelského rozhraní pro registraci serveru](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Hodnota | Popis |
   | **Předplatné Azure** | Předplatné, které obsahuje službu synchronizace úložiště pro účely tohoto kurzu. |
   | **Skupina prostředků** | Skupina prostředků, která obsahuje službu synchronizace úložiště. Pro účely tohoto kurzu použijte _afsresgroup101918._ |
   | **Služba synchronizace úložiště** | Název služby synchronizace úložiště. Pro účely tohoto kurzu použijte _afssyncservice02._ |

1. Chcete-li dokončit registraci serveru, vyberte **možnost Registrovat.**
1. V rámci procesu registrace budete vyzváni k zadání dalšího přihlášení. Přihlaste se a vyberte **Další**.
1. Vyberte **OK**.

## <a name="create-a-sync-group"></a>Vytvoření skupiny synchronizace

Skupina synchronizace definuje topologii synchronizace sady souborů. Skupina synchronizace musí obsahovat jeden koncový bod cloudu, který představuje sdílenou složku Azure. Skupina synchronizace musí také obsahovat jeden nebo více koncových bodů serveru. Koncový bod serveru představuje cestu na zaregistrovaném serveru. Vytvoření skupiny synchronizace:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte **skupinu + Sync** ze služby Synchronizace úložiště. Pro účely tohoto kurzu použijte *afssyncservice02.*

   ![Vytvoření nové skupiny synchronizace na webu Azure Portal](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Chcete-li vytvořit skupinu synchronizace s cloudovým koncovým bodem, zadejte následující informace:

   | Hodnota | Popis |
   | ----- | ----- |
   | **Název skupiny synchronizace** | Tento název musí být jedinečný v rámci služby synchronizace úložiště, ale může to být libovolný název, který vám dává smysl. Pro účely tohoto kurzu použijte *afssyncgroup.*|
   | **Předplatné** | Předplatné, do kterého jste nasadili službu synchronizace úložiště pro účely tohoto kurzu. |
   | **Účet úložiště** | Zvolte **Vybrat účet úložiště**. V podokně, které se zobrazí, vyberte účet úložiště, který má sdílenou složku Azure, kterou jste vytvořili. Použijte *afsstoracct101918* pro tento kurz. |
   | **Sdílená složka Azure** | Název sdílené složky Azure, kterou jste vytvořili. Použijte *afsfileshare* pro tento kurz. |

1. Vyberte **Vytvořit**.

Pokud vyberete svou skupinu synchronizace, uvidíte, že teď máte jeden **koncový bod cloudu**.

## <a name="add-a-server-endpoint"></a>Přidání koncového bodu serveru

Koncový bod serveru představuje konkrétní umístění na registrovaném serveru. Například složka na svazku serveru. Přidání koncového bodu serveru:

1. Vyberte nově vytvořenou skupinu synchronizace a pak vyberte **Přidat koncový bod serveru**.

   ![Přidání nového koncového bodu serveru v podokně skupiny synchronizace](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. V podokně **Přidat koncový bod serveru** zadejte následující informace pro vytvoření koncového bodu serveru:

   | | |
   | ----- | ----- |
   | Hodnota | Popis |
   | **Zaregistrovaný server** | Název serveru, který jste vytvořili. Pro účely tohoto kurzu použijte *afsvm101918.* |
   | **Cesta** | Cesta systému Windows Server k jednotce, kterou jste vytvořili. V tomto kurzu použijte *f:\filestosync.* |
   | **Vrstvení cloudu** | Pro účely tohoto kurzu nechte tuto možnost zakázanou. |
   | **Volné místo svazku** | Pro účely tohoto kurzu ponechte tuto hodnotu prázdnou. |

1. Vyberte **Vytvořit**.

Vaše soubory se teď synchronizují mezi sdílenou složkou Azure a Windows Serverem.

![Úspěšně synchronizovaná služba Azure Storage](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili základní kroky k rozšíření kapacity úložiště serveru Windows pomocí Azure File Sync. Podrobnější informace o plánování nasazení azure file sync najdete v tématu:

> [!div class="nextstepaction"]
> [Plánování nasazení Synchronizace souborů Azure](./storage-sync-files-planning.md)
