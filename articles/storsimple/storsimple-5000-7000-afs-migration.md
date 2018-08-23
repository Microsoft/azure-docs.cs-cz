---
title: Migrovat data ze StorSimple řad 5000 – 7000 do Azure File Sync | Dokumentace Microsoftu
description: Popisuje, jak migrovat data z StorSimple 5000/7000 series do Azure File Sync (AFS).
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/15/2018
ms.author: alkohli
ms.openlocfilehash: 6a52db27491ef707b813a7645d275b371b11368c
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054426"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Migrovat data z StorSimple 5000 – 7000 series do Azure File Sync

Migrace dat je proces přesunu dat z jednoho umístění úložiště. To znamená, že přesnou kopii aktuální data vaší organizace z jednoho zařízení na jiné zařízení – pokud možno bez přerušení nebo zakázání aktivní aplikace – a pak všechny vstupní a výstupní (I/O) aktivity přesměrování na nová zařízení. 

StorSimple 5000 a 7000 series úložná zařízení bude dosáhnout konce služby v červenci 2019. Z toho vyplývá, že Microsoft už by se na podporu hardwaru a softwaru pro řady StorSimple 5000/7000 za červenec 2019. Zákazníci, kteří využívají tato zařízení byste migrovat StorSimple data na jiná hybridní řešení úložišť v Azure. Tento článek popisuje migraci dat ze zařízení řady StorSimple 5000/7000 k Azure File Sync (AFS).

## <a name="intended-audience"></a>Plánovaná cílová skupina

Tento článek je určený pro odborníky v oblasti technologií (IT) informace a kvalifikovaní pracovníci zodpovědná za nasazení a správa zařízení řady StorSimple 5000/7000 v datové centrum. Zákazníci, kteří používají zařízení StorSimple pro úlohy serveru souboru (v systému Windows Server) může najít tento způsob migrace zejména atraktivní. Pokud se domníváte, že funkce služby Azure File Sync fungovat dobře ve vaší organizaci, a potom tento článek vám pomůže pochopit, jak přejít k těmto řešením od StorSimple.

## <a name="migration-considerations"></a>Aspekty migrace

Tento proces funguje pro zákazníky, kteří nakonfigurovali sdílenou složku Windows pomocí svazek StorSimple pro úložiště. Migrace dat ze StorSimple 5000/7000 do Azure File Sync zahrnuje převod tohoto umístění sdílené složky souboru [koncový bod serveru](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) a následným použitím místně připojené disky jako jiný koncový bod, který se pak do nového umístění. 

Při přechodu na AFS, musí vzít v úvahu následující body:

1. Služba soubory Azure aktuálně má omezení 5 TB/sdílené složky. Toto omezení je možné překonat s použitím Azure File Sync se data z více sdílených složek Azure. Další informace najdete v článku [vzorek růst dat pro nasazení služby soubory Azure](https://docs.microsoft.com/azure/storage/files/storage-files-planning).
2. Tato migrace stáhne primární celou datovou sadu do místního zařízení, jako je kopírování dat probíhá prostřednictvím místního zařízení. Ujistěte se, že máte dostatečnou šířku pásma tak, aby vyhovovaly tento přenos.
3. Tento proces nezachová snímky, které již byly vytvořeny. Migruje pouze primární data. Proces také nezachová šablon přidružené šířky pásma nebo zásady zálohování. [Použití Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files) nastavení zásady zálohování po migraci oznámení o data v Azure File sdílet.
4. StorSimple nabízí první strany hardwaru. Nicméně s Azure soubory nebo Azure File Sync, používáte místním hardwaru systému Windows Server jako místní mezipaměti. Ujistěte se, že máte dostatečnou kapacitu úložiště udržovat místní sady dat podle vašeho výběru. Další informace o ovládání datových vrstev a požadované volné místo cílové nastavení, najdete v tématu Jak [vytvořit koncový bod serveru při nasazení Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal). 
5. Zkontrolujte [ceny za Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/) jak se liší od StorSimple. AFS nemá deduplikace a komprese jako StorSimple.

## <a name="migration-prerequisites"></a>Požadavky na migraci

Tady najdete požadavky migrace pro starší verze zařízení řady 5000 a 7000 na Azure File Sync. Než začnete, ujistěte se, že máte:

- Přístup StorSimple 5000/7000 series zařízení spuštěný software verze v2.1.1.518 nebo novější. Starší verze se nepodporují. Pravém horním rohu webového uživatelského rozhraní vašeho zařízení StorSimple by se zobrazit, na kterém běží verze softwaru.  
    Pokud zařízení neběží v2.1.1.518, upgradujte systém na požadovanou minimální verzi. Podrobné pokyny najdete v tématu [upgradovat systém v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
- Zkontrolujte všechny aktivní úlohy zálohování, které běží na zdrojové zařízení. To zahrnuje úlohy na hostiteli konzole ochrany dat StorSimple. Počkejte na dokončení aktuální úlohy. 
- Přístup k hostiteli systému Windows Server připojený k StorSimple zařízení řady 5000-7000. Hostitel musí běžet podporovaná verze systému Windows Server podle popisu v [vzájemná funkční spolupráce Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning).
- Svazky zařízení StorSimple jsou připojené na hostiteli a obsahují sdílené složky.
- Hostitel má dostatečnou místní úložiště pro uložení vašich dat místně uložených v mezipaměti.
- Vlastník úroveň přístupu k předplatnému Azure, který použijete k nasazení služby Azure File Sync. Při vytváření koncového bodu cloudu pro vaši skupinu synchronizace, pokud máte vlastníka nebo oprávnění na úrovni správce, může docházet k problémům.
- Přístup k [účet úložiště pro obecné účely v2](https://docs.microsoft.com/azure/storage/common/storage-account-options) s sdílené složky Azure, který chcete synchronizovat. Další informace najdete v části 
 - Jak [vytvořit účet úložiště General Purpose v2](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal#create-a-general-purpose-storage-account).
 - Jak [vytvořit sdílenou složku Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share#create-file-share-through-the-azure-portal).

## <a name="migration-process"></a>Proces migrace

Migrace dat ze StorSimple 5000 – 7000 k AFS je dvoustupňový proces:
1.  Replikaci dat ze souborového serveru v místním, kde jsou svazky zařízení StorSimple připojené k Azure Files sdílet.  Replikace se provádí prostřednictvím AFS agenta, kterou instalujete.
2.  Odpojte zařízení StorSimple. Místní disky pak fungují jako místní mezipaměti.

### <a name="migration-steps"></a>Kroky migrace

Proveďte následující kroky pro migraci Windows sdílené nakonfigurované na svazky zařízení StorSimple ke sdílené složce Azure File Sync. 
1.  Tyto kroky proveďte v rámci stejného hostitele Windows serveru, kde jsou připojené svazky zařízení StorSimple, nebo použijte jiný systém. 
    - [Připravit systém Windows Server pro použití s Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Instalace agenta Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [Nasazení služby synchronizace úložiště](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Registrace serveru systému Windows pomocí služby synchronizace úložiště](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Vytvořit skupinu synchronizace a koncového bodu cloudu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint). Skupiny synchronizace. musí být provedeny pro každé sdílení souboru Windows, kterou je potřeba migrovat z hostitele.
    - [Vytvoření koncového bodu serveru](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). Zadejte cestu jako cestu svazek StorSimple, která obsahuje data sdílené složky souborů. Například, pokud svazek StorSimple využívá jednotky `J`, a máte data uložená v `J:/<myafsshare>`, pak přidat tuto cestu jako koncový bod serveru. Nechte **ovládání datových vrstev na** jako **zakázané**.
2.  Počkejte, dokud neskončí synchronizace souboru serveru. Pro každý server ve skupině dané synchronizace Ujistěte se, že:
    - Jsou poslední časové razítko pro poslední pokus o synchronizaci pro nahrávání a stahování.
    - Stav je zelená pro nahrávání a stahování.
    - **Aktivitu synchronizace** zobrazuje velmi málo nebo žádná zbývajících pro synchronizaci souborů.
    - **Soubory nesynchronizuje** je 0 pro nahrávání a stahování.
    Další informace o po dokončení synchronizace serveru, přejděte na [Poradce při potížích s Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other). Synchronizace může trvat několik hodin, dní v závislosti na velikosti dat a šířky pásma. Po dokončení synchronizace všechna vaše data jsou bezpečně ve sdílené složce Azure. 
3.  Přejděte do sdílené složky na svazky zařízení StorSimple. Vyberte sdílenou složku, klikněte pravým tlačítkem a vyberte **vlastnosti**. Všimněte si oprávnění ke sdíleným složkám pod **zabezpečení**. Tato oprávnění bude nutné ručně použít pro novou sdílenou složku v pozdějším kroku.
4.  Další kroky se liší v závislosti na tom, jestli používáte stejného hostitele Windows serveru nebo na jiném.

    Tento krok přeskočit a přejít k dalšímu kroku, pokud používáte jiného hostitele Windows serveru. Pokud používáte stejný souborový Server Windows pro AFS, nastane nyní pár minut prostojů. 
    - **Výpadek nastane** – odstranit koncový bod serveru, který jste vytvořili v *krok 1F*. 
    - Vytvořte nový koncový bod serveru se cesta kde chcete data uložená směrem vpřed.
    - Jakmile se koncový bod serveru se zobrazí jako v pořádku (to může trvat několik minut), zobrazí se data v tomto novém umístění. Teď můžete nakonfigurovat hostitele Windows serveru k poskytování souborů z tohoto nového umístění. - **Výpadek skončí**.
5.  Pokud používáte jiný souborový Server Windows pro Azure File Sync, nebude docházet žádné výpadky. 
    - Přidáte jiný koncový bod serveru se cesta k místní úložiště, které jsou připravené k použití jako mezipaměť namísto zařízení StorSimple. 
    - Budete moci zobrazit soubory do nového serveru během několika minut. Můžete libovolně udělovat ulehčili přechod z vašeho zařízení StorSimple do tohoto nového umístění v hostiteli v každém okamžiku.

    > [!TIP] 
    > Vezměte v úvahu konfiguraci této nové sdílené složky se stejným názvem a stejnou cestu jako jeden it mění, aby se minimalizovalo přerušení. Pokud pomocí systému souborů DFS-N, to může vyžadovat, abyste provedli změny v konfiguraci.
6.  Změna konfigurace oprávnění ke sdílení, jak je uvedeno v *kroku 3*.

Pokud zaznamenáte jakékoli problémy během migrace dat, [kontaktujte podporu Microsoftu](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>Další postup

Pokud AFS není má to pravé řešení pro vás, zjistěte, jak [migrovat data z StorSimple řad 5000 – 7000 zařízení řady 8000](storsimple-8000-migrate-from-5000-7000.md).

