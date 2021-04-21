---
title: Kurz – rozšiřování souborových serverů s Windows pomocí Synchronizace souborů Azure | Microsoft Docs
description: Naučte se, jak pomocí Synchronizace souborů Azure roztáhnout souborové servery Windows, od začátku do konce.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c71a0ddece95eb7d6a651da9c307912a1a24c800
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797456"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Kurz: Rozšíření souborových serverů Windows s využitím Synchronizace souborů Azure

Tento článek popisuje základní kroky pro rozšíření kapacity úložiště Windows serveru pomocí Synchronizace souborů Azure. I když kurz nabízí Windows Server jako virtuální počítač Azure (VM), obvykle tento proces provedete pro vaše místní servery. Pokyny pro nasazení Synchronizace souborů Azure ve vlastním prostředí najdete v článku o [nasazení synchronizace souborů Azure](file-sync-deployment-guide.md) .

> [!div class="checklist"]
> * Nasazení služby synchronizace úložiště
> * Příprava Windows Serveru na použití se Synchronizací souborů Azure
> * Instalace agenta Synchronizace souborů Azure
> * Registrace Windows serveru pomocí služby synchronizace úložiště
> * Vytvoření skupiny synchronizace a koncového bodu cloudu
> * Vytvoření koncového bodu serveru

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Příprava prostředí

Pro tento kurz je potřeba provést následující kroky, abyste mohli nasadit Synchronizace souborů Azure:

- Vytvoření účtu úložiště Azure a sdílení souborů
- Nastavení virtuálního počítače s Windows serverem 2016 Datacenter
- Příprava virtuálního počítače s Windows serverem pro Synchronizace souborů Azure

### <a name="create-a-folder-and-txt-file"></a>Vytvoření složky a souboru .txt

Na svém místním počítači vytvořte novou složku _FilesToSync_ a přidejte do ní textový soubor _mytestdoc.txt_. Tento soubor nahrajete do sdílené složky později v tomto kurzu.

### <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Vytvoření sdílené složky

Po nasazení účtu úložiště Azure vytvoříte sdílenou složku.

1. V Azure Portal vyberte **Přejít k prostředku**.
1. V podokně účet úložiště vyberte **soubory** .

    ![Vybrat soubory](./media/storage-sync-files-extend-servers/click-files.png)

1. Vyberte **+ sdílení souborů**.

    ![Vyberte tlačítko Přidat sdílení souborů.](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Pojmenujte novou sdílenou složku _afsfileshare_. Pro **kvótu** zadejte "1" a pak vyberte **vytvořit**. Kvóta může být maximálně 5 TiB, ale pro účely tohoto kurzu vám stačí 1 GB.

    ![Zadání názvu a kvóty pro novou sdílenou složku](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Vyberte novou sdílenou složku. V umístění sdílení souborů vyberte **Odeslat**.

    ![Nahrání souboru](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Přejděte do složky _FilesToSync_ , ve které jste vytvořili soubor. txt, vyberte _mytestdoc.txt_ a vyberte **nahrát**.

    ![Procházení sdílené složky](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

V tomto okamžiku jste vytvořili účet úložiště a sdílenou složku s jedním souborem. Dále nasadíte virtuální počítač Azure s Windows serverem 2016 Datacenter, který bude představovat místní server v tomto kurzu.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Nasazení virtuálního počítače a připojení datového disku

1. Přejděte na Azure Portal a rozbalte nabídku vlevo. V levém horním rohu vyberte **vytvořit prostředek** .
1. Ve vyhledávacím poli nad seznamem prostředků **Azure Marketplace** vyhledejte **Windows Server 2016 Datacenter** a vyberte ho ve výsledcích. Vyberte **vytvořit**.
1. Přejít na kartu **základy** . V části **Project Details (podrobnosti projektu**) vyberte skupinu prostředků, kterou jste pro tento kurz vytvořili.

   ![Zadání základních informací o VIRTUÁLNÍm počítači v okně portálu](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. V části **Podrobnosti instance** zadejte název virtuálního počítače. Použijte například _myVM_.
1. Neměňte výchozí nastavení pro **oblast**, **Možnosti dostupnosti**, **Obrázek** a **Velikost**.
1. V části **Účet správce** zadejte **Uživatelské jméno** a **Heslo** pro virtuální počítač.
1. V části **pravidla portů pro příchozí spojení** zvolte **Povolit vybrané porty** a v rozevírací nabídce vyberte **RDP (3389)** a **http** .

1. Před vytvořením virtuálního počítače je potřeba vytvořit datový disk.

   1. Vyberte **Další: disky**.

      ![Použití datových disků](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. Na kartě **disky** v části **Možnosti disku** ponechte výchozí nastavení.
   1. V části **datové disky** vyberte **vytvořit a připojit nový disk**.

   1. Použijte výchozí nastavení s výjimkou **velikosti (GIB)**, kterou můžete pro tento kurz změnit na **1 GB** .

      ![Podrobnosti o datovém disku](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. Vyberte **OK**.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.

   Můžete vybrat ikonu **oznámení** a sledovat **Průběh nasazení**. Dokončení vytvoření nového virtuálního počítače může trvat několik minut.

1. Po dokončení nasazení virtuálního počítače vyberte **Přejít k prostředku**.

   ![Přejít k prostředku](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

Právě jste vytvořili nový virtuální počítač a připojili jste datový disk. Pak se připojíte k virtuálnímu počítači.

### <a name="connect-to-your-vm"></a>Připojení k virtuálnímu počítači

1. V Azure Portal na stránce vlastností virtuálního počítače vyberte **připojit** .

   ![Připojení k virtuálnímu počítači Azure z portálu](./media/storage-sync-files-extend-servers/connect-vm.png)

1. Na stránce **připojit k virtuálnímu počítači** ponechte výchozí možnosti připojit pomocí **IP adresy** přes port 3389. Vyberte **Stáhnout soubor RDP**.

   ![Stažení souboru RDP](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Otevřete stažený soubor RDP a po zobrazení výzvy vyberte **připojit** .
1. V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte uživatelské jméno jako *localhost\username*, zadejte heslo, které jste vytvořili pro virtuální počítač, a pak vyberte **OK**.

   ![Další možnosti](./media/storage-sync-files-extend-servers/local-host2.png)

1. Při přihlášení se může zobrazit upozornění na certifikát. Vyberte **Ano** nebo **pokračovat** a vytvořte připojení.

### <a name="prepare-the-windows-server"></a>Příprava Windows serveru

Pro server Windows Server 2016 Datacenter zakažte možnost Konfigurace rozšířeného zabezpečení aplikace Internet Explorer. Tento krok se vyžaduje pouze při počáteční registraci serveru. Po zaregistrování serveru můžete tuto možnost znovu povolit.

Na virtuálním počítači Windows Server 2016 Datacenter se Správce serveru automaticky otevře.  Pokud se Správce serveru ve výchozím nastavení neotevře, vyhledejte ji v nabídce Start.

1. V **Správce serveru** vyberte **místní server**.

   ![Místní server na levé straně uživatelského rozhraní Správce serveru](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. V podokně **vlastnosti** vyberte odkaz pro **konfiguraci rozšířeného zabezpečení aplikace Internet Explorer**.  

    ![Podokno Konfigurace rozšířeného zabezpečení aplikace Internet Explorer v uživatelském rozhraní Správce serveru](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. V dialogovém okně **Konfigurace rozšířeného zabezpečení aplikace Internet Explorer** vyberte u položek **Správci** a **Uživatelé** možnost **Vypnuto**.

    ![Automaticky otevírané okno Konfigurace rozšířeného zabezpečení aplikace Internet Explorer s vybranou možností Vypnuto](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

Teď můžete k virtuálnímu počítači přidat datový disk.

### <a name="add-the-data-disk"></a>Přidání datového disku

1. Pořád na virtuálním počítači **Windows Server 2016 Datacenter** vyberte **soubory a**  >    >  **disky** svazky služby úložiště.

    ![Datový disk](media/storage-sync-files-extend-servers/your-disk.png)

1. Pravým tlačítkem myši klikněte na 1 GB disk s názvem **MSFT Virtual disk** a vyberte **nový svazek**.
1. Dokončete průvodce. Použijte výchozí nastavení a poznamenejte si přiřazené písmeno jednotky.
1. Vyberte **Vytvořit**.
1. Vyberte **Zavřít**.

   Právě jste disk převedli do stavu online a vytvořili jste svazek. Otevřete Průzkumníka souborů na virtuálním počítači s Windows serverem a potvrďte přítomnost nedávno přidaného datového disku.

1. V Průzkumníku souborů na VIRTUÁLNÍm počítači rozbalte **Tento počítač** a otevřete novou jednotku. V tomto příkladu je to jednotka F:.
1. Klikněte pravým tlačítkem a vyberte **Nová**  >  **Složka**. Pojmenujte složku _FilesToSync_.
1. Otevřete složku **FilesToSync** .
1. Klikněte pravým tlačítkem a vyberte **Nový**  >  **textový dokument**. Pojmenujte textový soubor _MyTestFile_.

    ![Přidání nového textového souboru](media/storage-sync-files-extend-servers/new-file.png)

1. Ukončete **Průzkumníka souborů** a **Správce serveru**.

### <a name="download-the-azure-powershell-module&quot;></a>Stáhnout modul Azure PowerShell

Pak na virtuálním počítači s Windows serverem 2016 Datacenter nainstalujte modul Azure PowerShell na server.

1. Na virtuálním počítači otevřete okno PowerShellu se zvýšenými oprávněními.
1. Spusťte následující příkaz:

   ```powershell
   Install-Module -Name Az
   ```

   > [!NOTE]
   > Pokud máte verzi NuGet, která je starší než 2.8.5.201, budete vyzváni ke stažení a instalaci nejnovější verze NuGetu.

   Ve výchozím nastavení není Galerie prostředí PowerShell pro PowerShellGet nakonfigurovaná jako důvěryhodné úložiště. Při prvním použití PSGallery se zobrazí následující výzva:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is &quot;N"):
   ```

1. Pokračujte v instalaci tak, že odpovíte **Ano** nebo **Ano všem** .

Modul `Az` je kumulativní modul pro rutiny Azure PowerShellu. Jeho instalace stáhne všechny dostupné moduly Azure Resource Manageru a zpřístupní jejich rutiny k použití.

V tuto chvíli jste si pro tento kurz nastavili prostředí. Jste připraveni nasadit službu synchronizace úložiště.

## <a name="deploy-the-service"></a>Nasazení služby

Pokud chcete nasadit Synchronizace souborů Azure, nejdřív umístěte prostředek **služby synchronizace úložiště** do skupiny prostředků pro vybrané předplatné. Služba synchronizace úložiště dědí přístupová oprávnění z předplatného a skupiny prostředků.

1. V Azure Portal vyberte **vytvořit prostředek** a pak vyhledejte **synchronizace souborů Azure**.
1. Ve výsledcích hledání vyberte **Synchronizace souborů Azure**.
1. Výběrem možnosti **Vytvořit** otevřete kartu **Nasadit synchronizaci úložiště**.

   ![Nasazení synchronizace úložiště](media/storage-sync-files-extend-servers/afs-info.png)

   V podokně, které se otevře, zadejte následující informace:

   | Hodnota | Popis |
   | ----- | ----- |
   | **Název** | Jedinečný název (pro každé předplatné) služby synchronizace úložiště.<br><br>Pro tento kurz použijte _afssyncservice02_ . |
   | **Předplatné** | Předplatné Azure, které používáte pro tento kurz. |
   | **Skupina prostředků** | Skupina prostředků, která obsahuje službu synchronizace úložiště.<br><br>Pro tento kurz použijte _afsresgroup101918_ . |
   | **Umístění** | East US |

1. Jakmile budete hotovi, vyberte **Vytvořit** a nasaďte **službu synchronizace úložiště**.
1. Vyberte kartu **oznámení** > **Přejít na prostředek**.

## <a name="install-the-agent"></a>Instalace agenta

Agent Synchronizace souborů Azure je balíček ke stažení, který umožňuje synchronizaci Windows Serveru se sdílenou složkou Azure.

1. Na virtuálním počítači s **Windows serverem 2016 Datacenter** otevřete **Internet Explorer**.
1. Přejděte na web [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Přejděte dolů do části **Agent synchronizace souborů Azure** a vyberte **Stáhnout**.

   ![Stažení agenta synchronizace](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Zaškrtněte políčko pro **StorageSyncAgent_V3_WS2016.EXE** a vyberte možnost **Další**.

   ![Výběr agenta](media/storage-sync-files-extend-servers/select-agent.png)

1. Vyberte možnost **po**  >  **spuštění**  >  **otevřít**.
1. Pokud jste to ještě neudělali, zavřete okno PowerShellu.
1. V **Průvodci instalací agenta synchronizace úložiště** přijměte výchozí hodnoty.
1. Vyberte **Nainstalovat**.
1. Vyberte **Dokončit**.

Na virtuální počítač s Windows Serverem 2016 Datacenter jste nasadili službu synchronizace Azure a nainstalovali agenta. Teď je potřeba virtuální počítač zaregistrovat ve službě synchronizace úložiště.

## <a name="register-windows-server"></a>Registrace Windows Serveru

Registrace Windows serveru pomocí služby synchronizace úložiště vytvoří vztah důvěryhodnosti mezi serverem (nebo clusterem) a službou synchronizace úložiště. Server lze registrovat pouze do jedné služby synchronizace úložiště. Může se synchronizovat s ostatními servery a sdílenými složkami Azure, které jsou přidružené k této službě synchronizace úložiště.

Uživatelské rozhraní pro registraci serveru by se mělo po instalaci agenta Synchronizace souborů Azure otevřít automaticky. Pokud ne, můžete ho otevřít ručně z umístění souboru: `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`

1. Když se na virtuálním počítači otevře uživatelské rozhraní pro registraci serveru, vyberte **OK**.
1. Začněte tím, že vyberete **přihlášení** .
1. Přihlaste se pomocí svých přihlašovacích údajů k účtu Azure a vyberte **Přihlásit** se.
1. Zadejte tyto informace:

   ![Snímek obrazovky uživatelského rozhraní pro registraci serveru](media/storage-sync-files-extend-servers/signin.png)

   | Hodnota | Popis |
   | ----- | ----- |
   | **Předplatné Azure** | Předplatné, které obsahuje službu synchronizace úložiště pro účely tohoto kurzu. |
   | **Skupina prostředků** | Skupina prostředků, která obsahuje službu synchronizace úložiště. Pro tento kurz použijte _afsresgroup101918_ . |
   | **Služba synchronizace úložiště** | Název služby synchronizace úložiště. Pro tento kurz použijte _afssyncservice02_ . |

1. Kliknutím na **Registrovat** dokončete registraci serveru.
1. V rámci procesu registrace budete vyzváni k zadání dalšího přihlášení. Přihlaste se a vyberte **Další**.
1. Vyberte **OK**.

## <a name="create-a-sync-group"></a>Vytvoření skupiny synchronizace

Skupina synchronizace definuje topologii synchronizace sady souborů. Skupina synchronizace musí obsahovat jeden koncový bod cloudu, který představuje sdílenou složku Azure. Skupina synchronizace také musí obsahovat jeden nebo více koncových bodů serveru. Koncový bod serveru představuje cestu na zaregistrovaném serveru. Vytvoření skupiny synchronizace:

1. V [Azure Portal](https://portal.azure.com/)v části Služba synchronizace úložiště vyberte **+ Skupina synchronizace** . Pro tento kurz použijte *afssyncservice02* .

   ![Vytvoření nové skupiny synchronizace na webu Azure Portal](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Zadejte následující informace, chcete-li vytvořit skupinu synchronizace s koncovým bodem cloudu:

   | Hodnota | Popis |
   | ----- | ----- |
   | **Název skupiny synchronizace** | Tento název musí být jedinečný v rámci služby synchronizace úložiště, ale může to být libovolný název, který vám dává smysl. Pro tento kurz použijte *afssyncgroup* .|
   | **Předplatné** | Předplatné, do kterého jste nasadili službu synchronizace úložiště pro účely tohoto kurzu. |
   | **Účet úložiště** | Zvolte **Vybrat účet úložiště**. V podokně, které se zobrazí, vyberte účet úložiště, který obsahuje sdílenou složku Azure, kterou jste vytvořili. Pro tento kurz použijte *afsstoracct101918* . |
   | **Sdílená složka Azure** | Název sdílené složky Azure, kterou jste vytvořili. Pro tento kurz použijte *afsfileshare* . |

1. Vyberte **Vytvořit**.

Pokud vyberete svou skupinu synchronizace, uvidíte, že teď máte jeden **koncový bod cloudu**.

## <a name="add-a-server-endpoint"></a>Přidání koncového bodu serveru

Koncový bod serveru představuje konkrétní umístění na zaregistrovaném serveru. Například složka na svazku serveru. Postup přidání koncového bodu serveru:

1. Vyberte nově vytvořenou skupinu synchronizace a pak vyberte **přidat koncový bod serveru**.

   ![Přidání nového koncového bodu serveru v podokně skupiny synchronizace](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. V podokně **přidat koncový bod serveru** zadejte následující informace pro vytvoření koncového bodu serveru:

   | Hodnota | Popis |
   | ----- | ----- |
   | **Registrovaný Server** | Název serveru, který jste vytvořili. Pro tento kurz použijte *afsvm101918* . |
   | **Cesta** | Cesta k Windows serveru na jednotku, kterou jste vytvořili. V tomto kurzu použijte *f:\filestosync* . |
   | **Vrstvení cloudu** | Pro účely tohoto kurzu nechte tuto možnost zakázanou. |
   | **Volné místo svazku** | Pro účely tohoto kurzu ponechte tuto hodnotu prázdnou. |

1. Vyberte **Vytvořit**.

Vaše soubory se teď synchronizují mezi sdílenou složkou Azure a Windows Serverem.

![Úspěšně synchronizovaná služba Azure Storage](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky, které jste vytvořili v tomto kurzu, odeberte nejprve koncové body ze služby synchronizace úložiště. Pak zrušte registraci serveru u služby synchronizace úložiště, odeberte skupiny synchronizace a odstraňte synchronizační službu.

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se seznámili se základními kroky pro rozšiřování kapacity úložiště Windows serveru pomocí Synchronizace souborů Azure. Důkladnější pohled na plánování nasazení Synchronizace souborů Azure najdete v tématu:

> [!div class="nextstepaction"]
> [Plánování nasazení Synchronizace souborů Azure](file-sync-planning.md)
