---
title: Nasadit soubor synchronizaci Azure (preview) | Microsoft Docs
description: Zjistěte, jak nasadit Azure souboru Sync, od začátku do konce.
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: a450d3c00627a9b20ff2fe31c4dba49b33352ec1
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738377"
---
# <a name="deploy-azure-file-sync-preview"></a>Nasadit soubor synchronizaci Azure (preview)
Pomocí synchronizace souboru Azure (preview) můžete centralizovat vaší organizace sdílené složky v souborech Azure, zatímco flexibilitu, výkonu a kompatibility pro místní souborový server. Synchronizace služby Azure souboru transformuje na rychlé mezipaměti Azure sdílené složky systému Windows Server. Můžete použít libovolný protokol, který je k dispozici v systému Windows Server pro přístup k datům místně, včetně protokolu SMB, systém souborů NFS a FTPS. Může mít libovolný počet mezipamětí, jako je třeba po celém světě.

Důrazně doporučujeme, abyste si přečetli [plánování nasazení Azure Files](storage-files-planning.md) a [plánování nasazení Azure souboru Sync](storage-sync-files-planning.md) ještě před dokončením kroků popsaných v tomto článku.

## <a name="prerequisites"></a>Požadavky
* Účet úložiště Azure a Azure soubor sdílet ve stejné oblasti, kterou chcete nasadit Azure synchronizace souboru. Další informace naleznete v tématu:
    - [Dostupnost v oblastech](storage-sync-files-planning.md#region-availability) pro synchronizaci souborů Azure.
    - [Vytvoření účtu úložiště](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) podrobný popis toho, jak vytvořit účet úložiště.
    - [Vytvoření sdílené složky](storage-how-to-create-file-share.md) podrobný popis toho, jak vytvořit sdílenou složku.
* Alespoň jeden podporované instance systému Windows Server nebo cluster Windows serveru pro synchronizaci se službou Azure synchronizace souboru. Další informace o podporovaných verzích systému Windows Server najdete v tématu [interoperabilita se systémem Windows Server](storage-sync-files-planning.md#azure-file-sync-interoperability).

## <a name="deploy-the-storage-sync-service"></a>Nasazení služby Sync úložiště 
Synchronizační služba úložiště je prostředek Azure nejvyšší úrovně pro synchronizaci souborů Azure. Chcete-li nasadit službu úložiště synchronizace, přejděte na [portál Azure](https://portal.azure.com/), klikněte na tlačítko *nový* a vyhledejte soubor synchronizaci Azure. Ve výsledcích hledání vyberte **synchronizace souboru Azure (preview)** a potom vyberte **vytvořit** otevřete **nasazení synchronizace úložiště** kartě.

V podokně, které se otevře zadejte následující informace:

- **Název**: jedinečný název (na jedno předplatné) pro službu synchronizace úložiště.
- **Předplatné**: předplatné, ve kterém chcete vytvořit synchronizační služby úložiště. V závislosti na konfiguraci strategie vaší organizace můžete mít přístup k jedné nebo více odběrů. Předplatné Azure je nejzákladnější kontejner pro fakturaci pro jednotlivých cloudových služeb (například soubory Azure).
- **Skupina prostředků**: Skupina prostředků je logické skupiny prostředků Azure, jako je například účet úložiště nebo synchronizační služby úložiště. Můžete vytvořit novou skupinu prostředků nebo použít existující skupinu prostředků pro synchronizaci souborů Azure. (Doporučujeme používat skupiny prostředků jako kontejnery izolovat prostředky logicky pro vaši organizaci, jako je například seskupení HR prostředky nebo prostředky pro konkrétní projekt.)
- **Umístění**: oblast, ve které chcete nasadit Azure synchronizace souboru. Pouze podporované oblasti jsou k dispozici v tomto seznamu.

Po dokončení vyberte **vytvořit** k nasazení služby Sync úložiště.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Příprava systému Windows Server pro použití s Azure souboru Sync
Pro každý server, který chcete používat se synchronizací souboru Azure, včetně uzlů serveru v clusteru s podporou převzetí služeb při selhání proveďte následující kroky:

1. Zakázat **Rozšířená konfigurace zabezpečení aplikace Internet Explorer**. To je potřeba jenom pro registraci počáteční server. Můžete ji můžete znovu povolit po registraci serveru.
    1. Otevřete správce serveru.
    2. Klikněte na tlačítko **místní Server**:  
        !["Místní Server" na levé straně uživatelského rozhraní správce serveru](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. Na **vlastnosti** subpane, vyberte odkaz **konfigurace rozšířeného zabezpečení aplikace Internet Explorer**.  
        ![V podokně "Konfigurace rozšířeného zabezpečení" v uživatelském rozhraní správce serveru](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. V **konfigurace rozšířeného zabezpečení aplikace Internet Explorer** dialogové okno, vyberte **vypnout** pro **správci** a **uživatelé**:  
        ![Vybrané pop okno Konfigurace rozšířeného zabezpečení aplikace Internet Explorer s "Vypnuto"](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. Pokud používáte Windows Server 2012 R2, ujistěte se, že používáte alespoň 5.1 prostředí PowerShell. \*. Tato kontrola na Windows Server 2016, můžete přeskočit bezpečně jako 5.1 prostředí PowerShell je výchozí verze out-of-box. V systému Windows Server 2012 R2 můžete ověřit spuštěný 5.1 prostředí PowerShell. \* pohledem na hodnotu **PSVersion** vlastnost **$PSVersionTable** objektu:

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    Pokud vaše PSVersion hodnota je menší než 5.1. \*jako bude v případě u většiny instalací systému Windows Server 2012 R2, budete moci snadno upgradovat stahuje a instaluje [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Příslušný balíček ke stažení a instalaci pro Windows Server 2012 R2 je **Win8.1AndW2K12R2 KB\*\*\*\*\*\*\*-x64.msu**.

    > [!Note]  
    > Synchronizace služby Azure souboru zatím nepodporuje 6 prostředí PowerShell na Windows Server 2012 R2 nebo Windows Server 2016.

3. [Instalace a konfigurace prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Doporučujeme používat nejnovější verzi modulů prostředí Azure PowerShell.

## <a name="install-the-azure-file-sync-agent"></a>Nainstalujte agenta synchronizace souboru Azure
Agent Azure souboru Sync je ke stažení balíčku, který umožňuje systému Windows Server bude synchronizován se sdílenou složku Azure. Si můžete stáhnout agenta z [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Po dokončení stahování dvakrát klikněte na balíček Instalační služby MSI zahájíte instalaci agenta synchronizace souboru Azure.

> [!Important]  
> Pokud máte v úmyslu používat synchronizaci souboru Azure s clusterem s podporou převzetí služeb při selhání, musí být na všech uzlech v clusteru nainstalován agent synchronizace souboru Azure.


Instalační balíček agenta synchronizace souboru Azure nainstalovat poměrně rychle a bez příliš mnoho dalších výzev. Doporučujeme, abyste provedli následující:
- Ponechte výchozí instalační cesta (C:\Program Files\Azure\StorageSyncAgent), můžete zjednodušit správu řešení potíží a serveru.
- Povolte Microsoft Update a průběžně aktualizovat synchronizace souboru Azure. Všechny aktualizace, agent synchronizace souboru Azure, včetně funkce aktualizace a opravy hotfix, dojít z webu Microsoft Update. Doporučujeme nainstalovat nejnovější aktualizace pro synchronizaci souborů Azure. Další informace najdete v tématu [zásady synchronizace souboru Azure aktualizace](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Po dokončení instalace agenta Azure souboru Sync rozhraní registrace serveru se automaticky otevře. Informace o tom a zaregistrujte tento server se synchronizací souboru Azure, najdete v další části.

## <a name="register-windows-server-with-storage-sync-service"></a>Windows Server zaregistrovat úložiště synchronizační služby
Registrace Windows serveru se službou Sync úložiště vytváří vztah důvěryhodnosti mezi serverem (nebo clusteru) a služby úložiště Sync. Uživatelské rozhraní serveru registrace by měla otevřít automaticky po instalaci agenta synchronizace souboru Azure. Pokud tomu tak není, můžete otevřít jej ručně z jeho umístění souboru: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Když se otevře rozhraní registraci serveru, vyberte **přihlášení** zahájíte.

Po přihlášení, budete vyzváni k zadání následujících informací:

![Snímek obrazovky uživatelského rozhraní registrace serveru](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Předplatné Azure**: odběr, který obsahuje službu synchronizace úložiště (v tématu [nasazení služby Sync úložiště](#deploy-the-storage-sync-service)). 
- **Skupina prostředků**: skupinu prostředků, která obsahuje synchronizační služby úložiště.
- **Služba synchronizace úložiště**: název služby Sync úložiště, pro který chcete zaregistrovat.

Jakmile vyberete příslušné informace, vyberte **zaregistrovat** dokončit registraci serveru. Jako součást procesu registrace budete vyzváni k další sign-in.

> [!Note]  
> Server se dají registrovat jenom s jeden synchronizační služby úložiště v čase.

## <a name="create-a-sync-group"></a>Vytvořte skupinu synchronizace
Synchronizace skupiny definuje topologie synchronizace pro určitou sadu souborů. Koncové body v rámci synchronizace skupiny jsou synchronizovány mezi sebou. Synchronizace skupiny musí obsahovat alespoň jeden cloud koncový bod, který představuje sdílenou složku Azure, a koncový bod, jeden server, který představuje cestu v systému Windows Server. Chcete-li vytvořit skupinu synchronizace v [portál Azure](https://portal.azure.com/), přejděte do vaší služby synchronizace úložiště a potom vyberte **+ skupiny synchronizace**:

![Vytvořit novou skupinu synchronizace na portálu Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

V podokně, které se otevře zadejte následující informace a vytvořte skupinu synchronizace s koncového bodu cloudu:

- **Název skupiny synchronizace**: název skupiny synchronizace, který se má vytvořit. Tento název musí být jedinečný v rámci služby synchronizace úložiště, ale může být jakýkoli název, který je logické za vás.
- **Předplatné**: předplatné, které jste nasadili synchronizační služby úložiště v [nasazení služby Sync úložiště](#deploy-the-storage-sync-service).
- **Účet úložiště**: Pokud jste vybrali **vyberte účet úložiště**, zobrazí se další podokno, ve kterém můžete vybrat účet úložiště, který má sdílenou složku Azure, které chcete synchronizovat s.
- **Sdílenou složku Azure**: název sdílené složky Azure file, pro který chcete synchronizovat.

Chcete-li přidat koncový bod serveru, přejděte do skupiny nově vytvořený synchronizace a potom vyberte **přidat koncový bod serveru**.

![Přidat nový koncový bod serveru v podokně skupiny synchronizace](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

V **přidat koncový bod serveru** podokně zadejte následující informace pro vytvoření koncového bodu serveru:

- **Zaregistrovaný server**: název serveru nebo clusteru, kde chcete vytvořit koncový bod serveru.
- **Cesta**: cestu v serveru Windows možné synchronizovat jako součást skupiny synchronizace.
- **Cloud Tiering**: přepínač k povolení nebo zakázání cloudu vrstev. S cloudem vrstvení, zřídka používají nebo získat přístup k souborů může být rozvrstvena k Azure Files.
- **Volné místo na svazku**: množství volného místa vyhradit na svazku, na kterém je umístěn serveru koncového bodu. Například pokud volné místo na svazku nastavená na 50 % ve svazku, který má koncový bod jeden server, zhruba poloviční množství dat, je vrstvené k Azure Files. Bez ohledu na tom, jestli cloud vrstvení je povoleno, Azure sdílené složky má vždy úplnou kopii dat ve skupině synchronizace.

Chcete-li přidat koncový bod serveru, vyberte **vytvořit**. Soubory jsou teď byly synchronizovány ve sdílenou složku Azure a Windows Server. 

> [!Important]  
> Můžete provádět změny žádný koncový bod cloudu nebo koncový bod serveru ve skupině synchronizace a na dalších koncových bodů ve skupině synchronizace proběhla vaše soubory. Pokud provedete změnu koncového bodu cloudu (Azure sdílené složky) přímo, nutné změny nejprve mají být zjišťované úlohou detekce změn v souboru synchronizace Azure. Úloha zjištění změn je zahájena pro koncový bod cloudu pouze jednou za 24 hodin. Další informace najdete v tématu [nejčastější dotazy k Azure Files](storage-files-faq.md#afs-change-detection).

## <a name="onboarding-with-azure-file-sync"></a>Registrace se synchronizací Azure File
Doporučené kroky zařadit do provozu v Azure souboru Sync pro první s nula výpadek při zachování celého souboru věrnosti a seznam řízení přístupu (ACL) jsou následující:
 
1.  Nasazení služby synchronizace úložiště.
2.  Vytvořte skupinu synchronizace.
3.  Nainstalujte agenta synchronizace souboru Azure na serveru s úplnou datovou sadu.
4.  Zaregistrujte tento server a vytvoření koncového bodu serveru ve sdílené složce. 
5.  Umožní synchronizaci provést úplné nahrávání do sdílené složky Azure file (koncový bod cloudu).  
6.  Po dokončení počáteční nahrávání nainstalujte agenta Azure soubor synchronizaci na všechny zbývající servery.
7.  Vytvoření nové sdílené složky na všechny ostatní servery.
8.  Vytvořte server koncové body pro nové sdílené složky s vrstvení zásady cloudu, v případě potřeby. (Tento krok vyžaduje další úložiště k dispozici pro počáteční instalaci.)
9.  Umožní agenta Azure soubor synchronizaci provést rychlé obnovení úplné oboru názvů bez přenos skutečná data. Po synchronizaci úplné obor názvů naplní synchronizační modul místo na disku na základě zásad vrstvení cloudu pro koncový bod serveru. 
10. Ujistěte se synchronizace dokončí a testování vaší topologie podle potřeby. 
11. Přesměrování uživatelů a aplikací do této nové sdílené složky.
12. Volitelně můžete odstranit duplicitní sdílených složkách na serveru.
 
Pokud nemáte dodatečné úložiště pro počáteční registraci a chcete pro připojení k existující sdílené složky, můžete předvyplnění data v Azure sdílených složek. Tento přístup je navržen jenom v případě můžete přijmout výpadky a absolutně zaručit žádné změny dat ve sdílených složkách serveru během procesu počáteční registrace. 
 
1.  Ujistěte se, že data na žádném serveru se nemůže změnit během procesu registrace.
2.  Předem osivo Azure sdílené složky s daty na serveru, například pomocí libovolného nástroje pro přenos dat přes SMB Robocopy, přímé kopie SMB. Vzhledem k tomu, že AzCopy není načtení dat prostřednictvím SMB proto jej nelze použít pro předem synchronizace replik indexů.
3.  Vytvoření topologie synchronizace souboru Azure s koncovými body požadovaný server, odkazující na existující sdílené složky.
4.  Umožní synchronizaci dokončit odsouhlasení procesu na všechny koncové body. 
5.  Po dokončení odsouhlasení můžete otevřít sdílených složek pro změny.
 
Upozorňujeme, že v současné době předem synchronizace replik indexů přístup má několik omezení- 
- Úplné věrnosti u souborů není zachován. Například soubory ztratit seznamy řízení přístupu a časová razítka.
- Změny dat na serveru před topologie synchronizace je plně nahoru a spuštění může způsobit konflikty na koncové body serveru.  
- Po vytvoření koncového bodu cloudu Azure souboru Sync spustí proces pro zjištění soubory v cloudu před zahájením počáteční synchronizace. Čas potřebný k dokončení tohoto procesu se liší v závislosti na různých faktorech, jako je rychlost sítě, dostupnou šířku pásma a počet souborů a složek. Pro hrubý odhad ve verzi preview spustí proces zjišťování přibližně na 10 soubory za sekundu.  Proto i v případě, že předem rychlé synchronizace replik indexů spustí, celkovou dobu získat plně spuštěný systém může být výrazně déle, pokud je v cloudu předem dosazená data.


## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrace nasazení replikace distribuovaného systému souborů (DFS-R) do Azure souboru Sync
Migrace systému souborů DFS-R nasazení do Azure souboru Sync:

1. Vytvořte skupinu synchronizace představují topologii DFS-R, který chcete nahradit.
2. Spusťte na serveru, který má v úplné sadě dat v topologii DFS-R na migraci. Instalaci synchronizace Azure soubor na tomto serveru.
3. Zaregistrujte tento server a vytvořit koncový bod serveru pro první server chcete migrovat. Nepovolujte cloudu vrstev.
4. Umožní všechny synchronizaci dat do sdílené složky Azure file (koncový bod cloudu).
5. Instalace a registrace agenta Azure soubor synchronizaci na všechny zbývající servery systému souborů DFS-R.
6. Zakázat DFS R. 
7. Vytvoření koncového bodu serveru na všech serverech systému souborů DFS-R. Nepovolujte cloudu vrstev.
8. Ujistěte se synchronizace dokončí a testování vaší topologie podle potřeby.
9. Vyřadit z provozu systému souborů DFS R.
10. Cloud vrstvení může nyní povolit pro libovolný koncový bod serveru podle potřeby.

Další informace najdete v tématu [synchronizace souboru Azure zprostředkovatel komunikace s objekty s distribuovaného systému souborů (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Další postup
- [Přidat nebo odebrat koncový bod serveru synchronizace souboru Azure](storage-sync-files-server-endpoint.md)
- [Zaregistrovat nebo zrušit registraci serveru s Azure souboru Sync](storage-sync-files-server-registration.md)
