---
title: 'Kurz: Rozšíření souborových serverů Windows s využitím Synchronizace souborů Azure | Microsoft Docs'
description: Seznamte se s kompletním postupem rozšíření souborových serverů Windows s využitím Synchronizace souborů Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: cc34411cc27870dbd9c707a34ebf34b96c7253dc
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986113"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Kurz: Rozšíření souborových serverů Windows s využitím Synchronizace souborů Azure
V tomto kurzu si ukážeme základní postup rozšíření kapacity úložiště Windows Serveru s využitím Synchronizace souborů Azure. Přestože pro účely tohoto kurzu používáme virtuální počítač Azure s Windows Serverem, obvykle tento postup použijete u svých místních serverů. Pokud jste připraveni nasadit Synchronizaci souborů Azure ve vlastním prostředí, místo tohoto kurzu postupujte podle článku [Nasazení Synchronizace souborů Azure](storage-sync-files-deployment-guide.md).

> [!div class="checklist"]
> * Nasazení služby synchronizace úložiště
> * Příprava Windows Serveru na použití se Synchronizací souborů Azure
> * Instalace agenta Synchronizace souborů Azure
> * Registrace Windows Serveru ve službě synchronizace úložiště
> * Vytvoření skupiny synchronizace a koncového bodu cloudu
> * Vytvoření koncového bodu serveru

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="prepare-your-environment"></a>Příprava prostředí
Před nasazením Synchronizace souborů Azure je pro účely tohoto kurzu potřeba nastavit několik věcí. Společně s vytvořením účtu služby Azure Storage a sdílené složky vytvoříte virtuální počítač s Windows Serverem 2016 Datacenter a připravíte tento server pro Synchronizaci souborů Azure.

### <a name="create-a-folder-and-txt-file"></a>Vytvoření složky a souboru .txt

Na svém místním počítači vytvořte novou složku *FilesToSync* a přidejte do ní textový soubor *mytestdoc.txt*. V pozdější části tohoto kurzu tento soubor nahrajete do sdílené složky.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Vytvoření sdílené složky
Dále vytvoříte sdílenou složku.

1. Po dokončení nasazení účtu úložiště Azure klikněte na **Přejít k prostředku**.
1. V podokně účtu úložiště klikněte na **Soubory**.

    ![Kliknutí na Soubory](./media/storage-sync-files-extend-servers/click-files.png)

1. Klikněte na **+ Sdílená složka**.

    ![Kliknutí na tlačítko Přidat sdílenou složku](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Pojmenujte novou sdílenou složku *afsfileshare*, jako **Kvóta** zadejte 1 a pak klikněte na **Vytvořit**. Kvóta může být maximálně 5 TiB, ale pro účely tohoto kurzu vám stačí 1 GB.

    ![Zadání názvu a kvóty pro novou sdílenou složku](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Vyberte novou sdílenou složku a pak v umístění sdílené složky klikněte na **Nahrát**.

    ![Nahrání souboru](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Přejděte do složky *FilesToSync*, kde jste vytvořili soubor .txt, vyberte soubor *mytestdoc.txt* a klikněte na **Nahrát**.

    ![Procházení sdílené složky](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Právě jste v Azure vytvořili účet služby Azure Storage a sdílenou složku obsahující jeden soubor. Teď vytvoříte virtuální počítač Azure s Windows Serverem 2016 Datacenter, který bude v tomto kurzu představovat místní server.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Nasazení virtuálního počítače a připojení datového disku

1. Teď rozbalte nabídku na levé straně portálu a v levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.
1. Ve vyhledávacím poli nad seznamem prostředků **Azure Marketplace** vyhledejte a vyberte **Windows Server 2016 Datacenter** a pak zvolte **Vytvořit**.
1. Na kartě **Základy** v části **Podrobnosti o projektu** vyberte skupinu prostředků, kterou jste pro tento kurz vytvořili.

   ![Zadání základních informací o virtuálním počítači v okně portálu](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. V části **Podrobnosti o instanci** zadejte název virtuálního počítače, například *myVM*.
1. U položek **Oblast**, **Možnosti dostupnosti**, **Image** a **Velikost** ponechte výchozí nastavení.
1. V části **Účet správce** zadejte **Uživatelské jméno** a **Heslo** pro virtuální počítač.
1. V části **Pravidla portů pro příchozí spojení** vyberte **Povolit vybrané porty** a pak z rozevíracího seznamu vyberte **RDP (3389)** a **HTTP**.

   Před vytvořením virtuálního počítače je potřeba vytvořit datový disk.

1. Klikněte na **Další: Disky**.

   ![Použití datových disků](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

1. Na kartě **Disky** v části **Možnosti disku** ponechte výchozí nastavení.
1. V části **DATOVÉ DISKY** klikněte na **Vytvořit a připojit nový disk**.

1. Pro účely tohoto kurzu ponechte výchozí hodnoty, ale změňte **Velikost (GiB)** na **1 GB**.

   ![Podrobnosti o datovém disku](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

1. Klikněte na **OK**.
1. Klikněte na **Zkontrolovat a vytvořit**.
1. Klikněte na možnost **Vytvořit**.

   Můžete kliknout na ikonu **Oznámení** a sledovat **Průběh nasazení**. Vytvoření nového virtuálního počítače bude trvat několik minut.

1. Po dokončení nasazení virtuálního počítače klikněte na **Přejít k prostředku**.

   ![Přejít k prostředku](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

   Právě jste vytvořili nový virtuální počítač a připojili jste datový disk. Teď je potřeba se k tomuto virtuálnímu počítači připojit.

### <a name="connect-to-your-vm"></a>Připojení k virtuálnímu počítači

1. Na stránce vlastností virtuálního počítače na webu Azure Portal klikněte na **Připojit**.

   ![Připojení k virtuálnímu počítači Azure z portálu](./media/storage-sync-files-extend-servers/connect-vm.png)

1. Na stránce **Připojení k virtuálnímu počítači** ponechte výchozí výběr možností pro připojení podle **IP adresy** přes port 3389 a klikněte na **Stáhnout soubor RDP**.

   ![Stažení souboru RDP](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Otevřete stažený soubor RDP a po zobrazení výzvy klikněte na **Připojit**.
1. V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte uživatelské jméno ve formátu *místní_hostitel\uživatelské_jméno* a heslo, které jste pro virtuální počítač vytvořili, a pak klikněte na **OK**.

   ![Další možnosti](./media/storage-sync-files-extend-servers/local-host2.png)

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na **Ano** nebo **Pokračovat** a vytvořte připojení.

### <a name="prepare-the-windows-server"></a>Příprava Windows Serveru
Pro server **Windows Server 2016 Datacenter** zakažte možnost **Konfigurace rozšířeného zabezpečení aplikace Internet Explorer**. Tento krok se vyžaduje pouze při počáteční registraci serveru. Po zaregistrování serveru můžete tuto možnost znovu povolit.

Na virtuálním počítači s **Windows Serverem 2016 Datacenter** se automaticky otevře **Správce serveru**.  Pokud se **Správce serveru** neotevře, vyhledejte ho v Průzkumníku.

1. Ve **Správci serveru** klikněte na **Místní server**.

   ![Místní server na levé straně uživatelského rozhraní Správce serveru](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. V podokně **Vlastnosti** vyberte odkaz **Konfigurace rozšířeného zabezpečení aplikace Internet Explorer**.  

    ![Podokno Konfigurace rozšířeného zabezpečení aplikace Internet Explorer v uživatelském rozhraní Správce serveru](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. V dialogovém okně **Konfigurace rozšířeného zabezpečení aplikace Internet Explorer** vyberte u položek **Správci** a **Uživatelé** možnost **Vypnuto**.

    ![Automaticky otevírané okno Konfigurace rozšířeného zabezpečení aplikace Internet Explorer s vybranou možností Vypnuto](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

   Teď můžete k virtuálnímu počítači přidat datový disk.

### <a name="add-the-data-disk"></a>Přidání datového disku

1. Na virtuálním počítači s **Windows Serverem 2016 Datacenter** klikněte na **Soubory a služby úložiště** > **Svazky** > **Disky**.

    ![Datový disk](media/storage-sync-files-extend-servers/your-disk.png)

1. Klikněte pravým tlačítkem na 1GB disk s názvem **Msft Virtual Disk** a klikněte na **Nový svazek**.
1. Dokončete průvodce s použitím výchozích hodnot, poznamenejte si písmeno jednotky a klikněte na **Vytvořit**.
1. Klikněte na **Zavřít**.

   Právě jste disk převedli do stavu online a vytvořili jste svazek. Úspěšné přidání datového disku můžete potvrdit tak, že na virtuálním počítači otevřete Průzkumníka a zkontrolujete, že je k dispozici nová jednotka.

1. V Průzkumníku na virtuálním počítači rozbalte **Tento počítač** a dvakrát klikněte na novou jednotku. V tomto příkladu je to jednotka F:.
1. Klikněte pravým tlačítkem a vyberte **Nový** > **Složka**. Pojmenujte složku *FilesToSync*.
1. Dvakrát klikněte na složku **FilesToSync**.
1. Klikněte pravým tlačítkem a vyberte **Nový** > **Textový dokument**. Pojmenujte textový soubor *MyTestFile*.

    ![Přidání nového textového souboru](media/storage-sync-files-extend-servers/new-file.png)

1. Zavřete **Průzkumníka** a **Správce serveru**.

### <a name="download-the-azurerm-powershell-module"></a>Stažení modulu AzureRM PowerShell
Dále na virtuálním počítači s **Windows Serverem 2016 Datacenter** nainstalujte na server **modul AzureRM PowerShell**.

1. Na virtuálním počítači otevřete okno PowerShellu se zvýšenými oprávněními.
1. Spusťte následující příkaz:

   ```powershell
   Install-Module -Name AzureRM -AllowClobber
   ```

   > [!NOTE]
   > Pokud máte verzi NuGetu, která je starší než 2.8.5.201, zobrazí se výzva ke stažení a instalaci nejnovější verze NuGetu.

   Ve výchozím nastavení není Galerie prostředí PowerShell pro PowerShellGet nakonfigurovaná jako důvěryhodné úložiště. Při prvním použití PSGallery se zobrazí tato výzva:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. Pokračujte v instalaci výběrem `Yes` nebo `Yes to All`.

Modul `AzureRM` je kumulativní modul pro rutiny Azure PowerShellu. Jeho instalace stáhne všechny dostupné moduly Azure Resource Manageru a zpřístupní jejich rutiny k použití.

Právě jste dokončili nastavování svého prostředí pro tento kurz a jste připraveni začít nasazovat **službu synchronizace úložiště**.

## <a name="deploy-the-service"></a>Nasazení služby 
Nasazení Synchronizace souborů Azure začíná umístěním prostředku **služby synchronizace úložiště** do skupiny prostředků pro vaše vybrané předplatné. Služba synchronizace úložiště zdědí přístupová oprávnění z předplatného a skupiny prostředků, do kterých ji nasadíte.

1. Na webu Azure Portal klikněte na **Vytvořit prostředek** a pak vyhledejte **Synchronizace souborů Azure**.
1. Ve výsledcích hledání vyberte **Synchronizace souborů Azure**.
1. Výběrem možnosti **Vytvořit** otevřete kartu **Nasadit synchronizaci úložiště**.

   ![Nasazení synchronizace úložiště](media/storage-sync-files-extend-servers/afs-info.png)

   V podokně, které se otevře, zadejte následující informace:

   | Hodnota | Popis |
   | ----- | ----- |
   | **Název** | Jedinečný název (pro každé předplatné) služby synchronizace úložiště.<br><br>V tomto kurzu používáme *afssyncservice02*. |
   | **Předplatné** | Předplatné, které v tomto kurzu používáte. |
   | **Skupina prostředků** | Skupina prostředků, kterou v tomto kurzu používáte.<br><br>V tomto kurzu používáme *afsresgroup101918*. |
   | **Umístění** | USA – východ |

1. Jakmile budete hotovi, vyberte **Vytvořit** a nasaďte **službu synchronizace úložiště**.
1. Klikněte na kartu **Oznámení** a pak na **Přejít k prostředku**.

## <a name="install-the-agent"></a>Instalace agenta
Agent Synchronizace souborů Azure je balíček ke stažení, který umožňuje synchronizaci Windows Serveru se sdílenou složkou Azure.

1. Přepněte zpět na virtuální počítač s **Windows Serverem 2016 Datacenter** a otevřete aplikaci **Internet Explorer**.
1. Přejděte na web [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Posuňte se dolů k části **Agent Synchronizace souborů Azure** a klikněte na **Stáhnout**.

   ![Stažení agenta synchronizace](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Zaškrtněte políčko u položky **StorageSyncAgent_V3_WS2016.EXE** a klikněte na **Další**.

   ![Výběr agenta](media/storage-sync-files-extend-servers/select-agent.png)

1. Vyberte **Povolit jednou** > **Spustit** > **Otevřít** a otevřete soubor.
1. Pokud jste to ještě neudělali, zavřete okno PowerShellu.
1. V **Průvodci instalací agenta synchronizace úložiště** přijměte výchozí hodnoty.
1. Klikněte na **Nainstalovat**.
1. Klikněte na **Dokončit**.

Na virtuální počítač s **Windows Serverem 2016 Datacenter** jste nasadili službu synchronizace Azure a nainstalovali agenta. Teď je potřeba virtuální počítač zaregistrovat ve **službě synchronizace úložiště**.

## <a name="register-windows-server"></a>Registrace Windows Serveru
Registrací vašeho Windows Serveru ve službě synchronizace úložiště se vytvoří vztah důvěryhodnosti mezi vaším serverem (nebo clusterem) a službou synchronizace úložiště. Každý server je možné zaregistrovat pouze k jedné službě synchronizace úložiště a může se synchronizovat s ostatními servery a sdílenými složkami Azure přidruženými ke stejné službě synchronizace úložiště.

Po dokončení instalace **agenta Synchronizace souborů Azure** by se mělo automaticky otevřít uživatelské rozhraní pro registraci serveru. Pokud ne, můžete ho otevřít ručně z umístění jeho souboru: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.

1. Jakmile se na virtuálním počítači otevře uživatelské prostředí pro registraci serveru, klikněte na **OK**.
1. Začněte kliknutím na **Přihlásit se**.
1. Přihlaste se pomocí svých přihlašovacích údajů Azure a klikněte na **Přihlásit se**.
1. Zadejte tyto informace:

   ![Snímek obrazovky uživatelského rozhraní pro registraci serveru](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Hodnota | Popis |
   | **Předplatné Azure** | Předplatné, které obsahuje službu synchronizace úložiště pro účely tohoto kurzu. |
   | **Skupina prostředků** | Skupina prostředků, která obsahuje službu synchronizace úložiště pro účely tohoto kurzu. V celém tomto kurzu používáme *afsresgroup101918*. |
   | **Služba synchronizace úložiště** | Název služby synchronizace úložiště, který jste pro tento kurz použili. V celém tomto kurzu používáme *afssyncservice02*. |

1. Kliknutím na **Zaregistrovat** dokončete registraci serveru.
1. V rámci procesu registrace se zobrazí výzva k dalšímu přihlášení. Přihlaste se a klikněte na **Další**.
1. Klikněte na **OK**.

## <a name="create-a-sync-group"></a>Vytvoření skupiny synchronizace
Skupina synchronizace definuje topologii synchronizace sady souborů. Skupina synchronizace musí obsahovat jeden koncový bod cloudu, který představuje sdílenou složku Azure, a jeden nebo několik koncových bodů serveru. Koncový bod serveru představuje cestu na zaregistrovaném serveru.

1. Pokud chcete vytvořit skupinu synchronizace, na webu [Azure Portal](https://portal.azure.com/) ve službě synchronizace úložiště, kterou jste pro tento kurz vytvořili, vyberte **+ Skupina synchronizace**. Jako příklad v tomto kurzu jsme použili *afssyncservice02*.

   ![Vytvoření nové skupiny synchronizace na webu Azure Portal](media/storage-sync-files-extend-servers/add-sync-group.png)

1. V podokně, které se otevře, zadejte následující informace pro vytvoření skupiny synchronizace s koncovým bodem cloudu:

   | Hodnota | Popis |
   | ----- | ----- |
   | **Název skupiny synchronizace** | Tento název musí být jedinečný v rámci služby synchronizace úložiště, ale může to být libovolný název, který vám dává smysl. V tomto kurzu používáme *afssyncgroup*.|
   | **Předplatné** | Předplatné, do kterého jste nasadili službu synchronizace úložiště pro účely tohoto kurzu. |
   | **Účet úložiště** |Klikněte na **Vybrat účet úložiště**. V podokně, které se zobrazí, vyberte účet úložiště obsahující sdílenou složku Azure, kterou jste vytvořili pro účely tohoto kurzu. My jsme použili *afsstoracct101918*. |
   | **Sdílená složka Azure** | Název sdílené složky Azure, kterou jste vytvořili pro účely tohoto kurzu. My jsme použili *afsfileshare*. |

1. Klikněte na možnost **Vytvořit**.

Pokud vyberete svou skupinu synchronizace, uvidíte, že teď máte jeden **koncový bod cloudu**.

## <a name="add-a-server-endpoint"></a>Přidání koncového bodu serveru
Koncový bod serveru představuje konkrétní umístění na zaregistrovaném serveru, například složku na svazku serveru.

1. Pokud chcete přidat koncový bod serveru, vyberte nově vytvořenou skupinu synchronizace a pak vyberte **Přidat koncový bod serveru**.

   ![Přidání nového koncového bodu serveru v podokně skupiny synchronizace](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. V podokně **Přidat koncový bod serveru** zadejte následující informace pro vytvoření koncového bodu serveru:

   | | |
   | ----- | ----- |
   | Hodnota | Popis |
   | **Zaregistrovaný server** | Název serveru, který jste vytvořili pro účely tohoto kurzu. V tomto kurzu jsme použili *afsvm101918*. |
   | **Cesta** | Cesta Windows Serveru k jednotce, kterou jste vytvořili pro účely tohoto kurzu. V našem příkladu to je *f:\filestosync*. |
   | **Vrstvení cloudu** | Pro účely tohoto kurzu nechte tuto možnost zakázanou. |
   | **Volné místo svazku** | Pro účely tohoto kurzu ponechte tuto hodnotu prázdnou. |

1. Klikněte na možnost **Vytvořit**.

Vaše soubory se teď synchronizují mezi sdílenou složkou Azure a Windows Serverem.

![Úspěšně synchronizovaná služba Azure Storage](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili základní postup rozšíření kapacity úložiště Windows Serveru s využitím Synchronizace souborů Azure. Na následujícím odkazu najdete podrobnější přehled plánování nasazení Synchronizace souborů Azure.

> [!div class="nextstepaction"]
> [Plánování nasazení Synchronizace souborů Azure](./storage-sync-files-planning.md)