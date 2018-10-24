---
title: Kurz rozšíření Windows souborové servery pomocí služby Azure File Sync | Dokumentace Microsoftu
description: Zjistěte, jak rozšířit Windows souborové servery pomocí služby Azure File Sync, od začátku do konce.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 10/23/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 4c3ceead792f60ceac7c5eddb64dc4644d662f83
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49960376"
---
<!---Customer intent: As a IT Administrator, I want see how to extend Windows file servers with Azure File Sync, so I can evaluate the process for extending storage capacity of my Windows Servers.
--->

# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Kurz: Rozšíření souborové servery Windows pomocí služby Azure File Sync
Používáte Azure File Sync centralizovat sdílené složky vaší organizaci a rozšířit kapacitu úložiště s místními souborového serveru. Azure File Sync transformuje serveru systému Windows na rychlou mezipaměť sdílené složky Azure.

V tomto kurzu vám ukážeme základní kroky pro rozšíření kapacity úložiště Windows serveru pomocí Azure File Sync. Pro účely tohoto kurzu používáme virtuální počítač Azure s Windows serverem, ale proces by obvykle děláte u místních serverů. Pokud jste připraveni k nasazení služby Azure File Sync ve vašem prostředí, použijte [nasazení Azure File Sync](storage-sync-files-deployment-guide.md) článek místo.

> [!div class="checklist"]
> * Nasazení služby synchronizace úložiště
> * Připravit systém Windows Server pro použití s Azure File Sync
> * Instalace agenta Azure File Sync
> * Registrace serveru systému Windows pomocí služby synchronizace úložiště
> * Vytvořit skupinu synchronizace a koncový bod cloudu.
> * Vytvořit koncový bod serveru

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="prepare-your-environment-for-the-tutorial"></a>Příprava prostředí pro tento kurz
Existuje několik věcí, které je třeba umístit na místě pro účely tohoto kurzu, před nasazením Azure File Sync. Vytvářet účet úložiště Azure a soubor sdílet, vytvoříte virtuální počítač s Windows serverem 2016 Datacenter a Příprava serveru pro Azure File Sync.

### <a name="create-a-folder-and-txt-file"></a>Vytvořte soubor složky a txt

V místním počítači, vytvořte novou složku s názvem *FilesToSync* a přidání textového souboru s názvem *mytestdoc.txt*. Tento soubor nahrajete do sdílené složky souboru v tomto kurzu.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Vytvoření sdílené složky
V dalším kroku vytvoříte sdílenou složku.

1. Po dokončení nasazení účtu úložiště Azure, klikněte na tlačítko **přejít k prostředku**.
1. Klikněte na tlačítko **soubory** z podokna účtu úložiště.

    ![Klikněte na soubory](./media/storage-sync-files-extend-servers/click-files.png)

1. Klikněte na tlačítko **+ sdílená složka,**.

    ![Klikněte na tlačítko Přidat sdílenou složku souboru](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Název nové sdílené složky *afsfileshare* a zadejte "1" **kvóty**, pak klikněte na tlačítko **vytvořit**. Kvóta může být maximálně 5 TiB, ale potřebujete jenom 1 GB pro účely tohoto kurzu.

    ![Zadejte název a kvótu pro novou sdílenou složku](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Vyberte nové sdílené složky, klikněte na umístění sdílené složky souboru **nahrát**.

    ![Nahrání souboru](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Přejděte *FilesToSync* složku, ve které jste vytvořili svůj soubor .txt, vyberte *mytestdoc.txt* a klikněte na tlačítko **nahrát**.

    ![Procházet sdílené složky](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

V tomto okamžiku jste vytvořili účet služby Azure Storage a sdílené složky s jedním souborem v něm v Azure. Teď vytvoříte virtuální počítač Azure s Windows Server 2016 Datacenter znázornit na místním serveru v tomto kurzu.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Nasazení virtuálního počítače a připojení datového disku

1. V dalším kroku v nabídce na levé straně portálu rozbalte a vyberte možnost **vytvořit prostředek** v levém horním rohu webu Azure portal.
1. Na panelu hledání nad seznamem **Azure Marketplace** prostředky, vyhledejte a vyberte **systému Windows Server 2016 Datacenter**, klikněte na tlačítko **vytvořit**.
1. V **Základy** ve skupině **podrobnosti o projektu**, vyberte skupinu prostředků, kterou jste vytvořili pro účely tohoto kurzu.

   ![Zadání základních informací o virtuálním počítači v okně portálu](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. V části **Instance podrobnosti**, zadejte název virtuálního počítače, jako třeba *myVM*.
1. Ponechejte výchozí nastavení pro **oblasti**, **možností dostupnosti**, **Image**, a **velikost**.
1. V části **účet správce**, zadejte **uživatelské jméno** a **heslo** pro virtuální počítač.
1. V části **Pravidla portů pro příchozí spojení** vyberte **Povolit vybrané porty** a pak z rozevíracího seznamu vyberte **RDP (3389)** a **HTTP**.

   Než vytvoříte virtuální počítač, je potřeba vytvořit datový disk.

1. Klikněte na tlačítko **Další: disků**

   ![Použití datových disků](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

1. V **disky** ve skupině **disku možnosti**, ponechte výchozí nastavení. 
1. V části **datové disky**, klikněte na tlačítko **vytvořit a připojit nový disk**.

1. Ponechte výchozí hodnoty, s výjimkou změn **velikost (GiB)** k **1 GB** pro účely tohoto kurzu.

   ![Podrobnosti datového disku](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

1. Klikněte na **OK**.
1. Klikněte na **Zkontrolovat a vytvořit**.
1. Klikněte na možnost **Vytvořit**.

   Můžete kliknout **oznámení** ikonu a podívejte se **průběh nasazení**. Vytvoření nového virtuálního počítače bude trvat několik minut.

1. Po dokončení nasazení virtuálního počítače klikněte na tlačítko **přejít k prostředku**.

   ![Přejít k prostředku](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

   V tomto okamžiku jste vytvořili nový virtuální počítač a připojit datový disk. Teď je potřeba připojit k virtuálnímu počítači.

### <a name="connect-to-your-vm"></a>Připojte se ke svému virtuálnímu počítači

1. Na webu Azure Portal, klikněte na tlačítko **připojit** na stránce vlastností virtuálního počítače.

   ![Připojení k virtuálnímu počítači Azure z portálu](./media/storage-sync-files-extend-servers/connect-vm.png)

1. V **připojit k virtuálnímu počítači** stránce, ponechte výchozí nastavení pro připojení s **IP adresu** více než port 3389 a klikněte na tlačítko **soubor stáhnout RDP**.

   ![Stáhnout soubor RDP](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Otevřete stažený soubor RDP a po zobrazení výzvy klikněte na **Připojit**.
1. V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte uživatelské jméno jako *localhost\username*, zadejte heslo, které jste vytvořili pro virtuální počítač a potom klikněte na tlačítko **OK**.

   ![Další možnosti](./media/storage-sync-files-extend-servers/local-host2.png)

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na **Ano** nebo **Pokračovat** a vytvořte připojení.

### <a name="prepare-the-windows-server"></a>Příprava serveru systému Windows
Pro **systému Windows Server 2016 Datacenter** serveru, zakažte **konfigurace rozšířeného zabezpečení aplikace Internet Explorer**. Tento krok se vyžaduje jenom pro počáteční server registrace. Můžete znovu povolit ho po registraci serveru.

V **systému Windows Server 2016 Datacenter** virtuálního počítače, **správce serveru** se automaticky otevře.  Pokud **správce serveru** neotevře ve výchozím nastavení, vyhledejte si ho v Průzkumníku.

1. V **správce serveru** klikněte na tlačítko **místní Server**.

   !["Místního serveru" v levé části uživatelského rozhraní správce serveru](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. Na **vlastnosti** podokno, vyberte odkaz pro **konfigurace rozšířeného zabezpečení Internet Exploreru**.  

    ![V podokně "Konfigurace rozšířeného zabezpečení" v Uživatelském rozhraní správce serveru](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. V **konfigurace rozšířeného zabezpečení aplikace Internet Explorer** dialogu **vypnout** pro **správci** a **uživatelé**:  

    ![Pop – okno Konfigurace rozšířeného zabezpečení aplikace Internet Explorer s "Off" vybraná](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

   Nyní můžete přidat datový disk k virtuálnímu počítači.

### <a name="add-the-data-disk"></a>Přidání datového disku

1. Pokud máte **systému Windows Server 2016 Datacenter** virtuálního počítače, klikněte na tlačítko **soubory a služby úložiště** > **svazky** > **disky** .

    ![Datový disk](media/storage-sync-files-extend-servers/your-disk.png)

1. Klikněte pravým tlačítkem na 1 GB disk s názvem **MSFT na úrovni virtuálního disku** a klikněte na tlačítko **nový svazek**.
1. Dokončete Průvodce nechat výchozí hodnoty na místě, zmínku písmeno jednotky přiřazené a klikněte na tlačítko **vytvořit**.
1. Klikněte na **Zavřít**.

   V tomto okamžiku jste převést do režimu online na disk a vytvořili svazek. Můžete potvrdit, že přidáte datový disk proběhla úspěšně: otevření Průzkumníka na virtuálním počítači a potvrdí, že nové jednotky k dispozici.

1. V Průzkumníku na virtuálním počítači, rozbalte **tento počítač** a dvakrát klikněte na novou jednotku. To je jednotka F: v tomto příkladu.
1. Klikněte pravým tlačítkem a vyberte **nový** > **složky**. Název složky *FilesToSync*.
1. Dvakrát klikněte **FilesToSync** složky.
1. Klikněte pravým tlačítkem a vyberte **nový** > **textový dokument**. Název textového souboru *MyTestFile*.

    ![Přidat nový textový soubor](media/storage-sync-files-extend-servers/new-file.png)

1. Zavřít **Explorer** a **správce serveru**.

### <a name="download-the-azurerm-powershell-module"></a>Stáhnout modul AzureRM Powershellu
Dále v **systému Windows Server 2016 Datacenter** virtuálního počítače, nainstalujte **modul AzureRM Powershellu** na serveru.

1. Ve virtuálním počítači otevřete okno Powershellu se zvýšenými oprávněními
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

Modul `AzureRM` je kumulativní modul pro rutiny Azure PowerShellu. Nainstalujete ho stáhne všechny dostupné moduly Azure Resource Manageru a zpřístupní jejich rutiny pro použití.

V tomto okamžiku jste dokončili nastavení prostředí pro tento kurz a budete připraveni začít s nasazováním **služba synchronizace úložiště**.

## <a name="deploy-the-storage-sync-service"></a>Nasazení služby synchronizace úložiště 
Nasazení Azure File Sync začíná umístěním **služba synchronizace úložiště** prostředků do skupiny prostředků pro vybrané předplatné. Služba synchronizace úložiště zdědí oprávnění k přístupu z dané předplatné a skupinu prostředků, který nasadíte do.

1. Na webu Azure Portal, klikněte na tlačítko **vytvořit prostředek** a vyhledejte **Azure File Sync**.
1. Ve výsledcích hledání vyberte **Azure File Sync**.
1. Vyberte **vytvořit** otevřít **nasadit synchronizaci úložiště** kartu.

   ![Nasadit synchronizaci úložiště](media/storage-sync-files-extend-servers/afs-info.png)

   V podokně, které se otevře zadejte následující informace:

   | Hodnota | Popis |
   | ----- | ----- |
   | **Název** | Jedinečný název (předplatné) pro službu synchronizace úložiště.<br><br>V tomto kurzu používáme *afssyncservice02*. |
   | **Předplatné** | Předplatné, které používáte pro účely tohoto kurzu. |
   | **Skupina prostředků** | Skupina prostředků, které používáte pro účely tohoto kurzu.<br><br>V tomto kurzu používáme *afsresgroup101918*. |
   | **Umístění** | USA – východ |

1. Jakmile budete hotovi, vyberte **vytvořit** k nasazení **služba synchronizace úložiště**.
1. Klikněte na tlačítko **oznámení** kartu > **přejít k prostředku**.

## <a name="install-the-azure-file-sync-agent"></a>Instalace agenta Azure File Sync
Agenta Azure File Sync je ke stažení balíčku, který umožňuje synchronizovat se sdílenými složkami Azure Windows serveru.

1. Přepněte zpět **systému Windows Server 2016 Datacenter** virtuálního počítače a otevřete **aplikace Internet Explorer**
1. Přejděte [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Přejděte dolů k položce **Azure File Sync Agent** části a klikněte na tlačítko **Stáhnout**.

   ![Stáhnout agenta synchronizace](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Zaškrtněte políčko u **StorageSyncAgent_V3_WS2016. Soubor EXE** a klikněte na tlačítko **Další**.

   ![Výběr agenta](media/storage-sync-files-extend-servers/select-agent.png)

1. **Povolit jednou** > **spustit** > **otevřít** souboru.
1. Pokud jste tak již neučinili, zavřete okno prostředí PowerShell.
1. Přijměte výchozí hodnoty v **Průvodce instalací agenta synchronizace úložiště**.
1. Klikněte na **Nainstalovat**.
1. Klikněte na **Dokončit**.

Jste nasadili službu synchronizace Azure a instalace agenta v **systému Windows Server 2016 Datacenter** virtuálního počítače. Teď budete muset zaregistrovat virtuální počítač pomocí **služba synchronizace úložiště**.

## <a name="register-windows-server-with-storage-sync-service"></a>Registrace serveru systému Windows pomocí služby synchronizace úložiště
Registrace služby synchronizace úložiště systému Windows Server vytvoří vztah důvěryhodnosti mezi serverem (nebo clusteru) a služba synchronizace úložiště. Server lze registrovat pouze do jedné služby synchronizace úložiště a můžete synchronizovat s jinými servery a Azure sdílené složky přidružené stejné služby synchronizace úložiště.

Uživatelské rozhraní serveru registrace by měla otevřít automaticky po instalaci **agenta Azure File Sync**. Pokud tomu tak není, lze jej otevřít ručně z umístění souboru: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.

1. Po registraci uživatelského rozhraní serveru otevře ve virtuálním počítači, klikněte na tlačítko **OK**.
1. Klikněte na tlačítko **přihlášení** začít.
1. Přihlaste se pomocí přihlašovacích údajů k účtu Azure a klikněte na tlačítko **přihlášení**.
1. Zadejte tyto informace:

   ![Snímek obrazovky registrace uživatelského rozhraní serveru](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Hodnota | Popis |
   | **Předplatné Azure** | Předplatné, které obsahuje službu synchronizace úložiště pro účely tohoto kurzu. |
   | **Skupina prostředků** | Skupina prostředků, která obsahuje službu synchronizace úložiště pro účely tohoto kurzu. Jsme použili *afsresgroup101918* v celém tomto kurzu. |
   | **Služba synchronizace úložiště** | Název služby synchronizace úložiště, který jste použili pro účely tohoto kurzu. Jsme použili *afssyncservice02* v celém tomto kurzu. |

1. Klikněte na tlačítko **zaregistrovat** k dokončení registrace serveru.
1. Jako součást procesu registrace zobrazí se výzva další přihlásit. Přihlaste se a klikněte na tlačítko **Další**.
1. Klikněte na tlačítko **OK**.

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Vytvořit skupinu synchronizace a koncový bod cloudu.
Skupina synchronizace definuje topologie synchronizace pro sadu souborů. Skupina synchronizace musí obsahovat jeden koncový bod cloudu, který představuje jednu nebo více koncových bodů serveru a sdílené složky Azure. Koncový bod serveru představuje cestu na registrovaném serveru.

1. Chcete-li vytvořit skupinu synchronizace v [webu Azure portal](https://portal.azure.com/)vyberte **+ skupina synchronizace** od služby synchronizace úložiště, který jste vytvořili pro účely tohoto kurzu. Použili jsme *afssyncservice02* stejně jako v příkladu v tomto kurzu.

   ![Na webu Azure Portal vytvořte novou skupinu synchronizace](media/storage-sync-files-extend-servers/add-sync-group.png)

1. V podokně, které se otevře zadejte následující informace, chcete-li vytvořit skupinu synchronizace s koncovým bodem cloudové:

   | Hodnota | Popis |
   | ----- | ----- |
   | **Název skupiny synchronizace** | Tento název musí být jedinečný v rámci služby synchronizace úložiště, ale může být jakýkoli název, který dává smysl pro vás. V tomto kurzu používáme *afssyncgroup*.|
   | **Předplatné** | Předplatné, kam jste nasadili službu synchronizace úložiště pro účely tohoto kurzu. |
   | **Účet úložiště** |Klikněte na tlačítko **vyberte účet úložiště**. V podokně se zobrazí vyberte účet úložiště obsahující sdílenou složku Azure, kterou jste vytvořili pro účely tohoto kurzu. Použili jsme *afsstoracct101918*. |
   | **Sdílené složky Azure** | Název sdílené složky Azure, kterou jste vytvořili pro účely tohoto kurzu. Použili jsme *afsfileshare*. |

1. Klikněte na možnost **Vytvořit**.

Když vyberete vaši skupinu synchronizace, uvidíte, že teď máte jeden **koncový bod v cloudu**.

## <a name="add-a-server-endpoint"></a>Přidat koncový bod serveru
Koncový bod serveru představuje určitého umístění na registrovaném serveru, jako je například složka svazku serveru.

1. Chcete-li přidat koncový bod serveru, vyberte nově vytvořený synchronizace skupiny a pak vyberte **přidat koncový bod serveru**.

   ![Přidat nový koncový bod serveru v okně skupiny synchronizace](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. V **přidat koncový bod serveru** podokně zadejte následující informace a vytvořte koncový bod serveru:

   | | |
   | ----- | ----- |
   | Hodnota | Popis |
   | **Registrovaný server** | Název serveru, který jste vytvořili pro účely tohoto kurzu. Použili jsme *afsvm101918* v tomto kurzu ||
   | **Cesta** | Cesta Windows serveru na jednotku, kterou jste vytvořili pro účely tohoto kurzu. V našem příkladu je *f:\filestosync*. |
   | **Vrstvení cloudu** | Nechte zakázané pro účely tohoto kurzu. |
   | **Volné místo na svazku** | Pro účely tohoto kurzu ponechte prázdné. |

1. Klikněte na možnost **Vytvořit**.

Soubory jsou teď udržovat synchronizované sdílené složky Azure a Windows Server.

![Úložiště Azure, které úspěšně synchronizovaly](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, základní kroky pro rozšíření kapacity úložiště Windows serveru s využitím Azure File Sync. Přejděte na tento odkaz pro podrobnější pohled na plánování nasazení služby Azure File Sync.

> [!div class="nextstepaction"]
> [Plán nasazení Azure File Sync](./storage-sync-files-planning.md)