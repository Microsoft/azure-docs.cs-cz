---
title: Migrace dat z řady StorSimple 5000-7000 do synchronizace souborů Azure| Dokumenty společnosti Microsoft
description: Popisuje, jak migrovat data z řady StorSimple 5000/7000 do Azure File Sync (AFS).
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
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: b46e9ee8fc3e14981a01cc2425a8ce55d06c5a9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65150743"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Migrace dat z řady StorSimple 5000-7000 do Azure File Sync

> [!IMPORTANT]
> Července 9, 2019 Série StorSimple 5000/7000 dosáhne stavu ukončení podpory (EOS). Doporučujeme, aby zákazníci řady StorSimple 5000/7000 migrovali na jednu z alternativ popsaných v dokumentu.

Migrace dat je proces přesouvání dat z jednoho umístění úložiště do jiného. To znamená vytvořit přesnou kopii aktuálních dat organizace z jednoho zařízení do jiného zařízení – nejlépe bez narušení nebo zakázání aktivních aplikací – a poté přesměrovat veškerou aktivitu vstupu/výstupu (I/O) na nové zařízení. 

Úložná zařízení řady StorSimple 5000 a 7000 dosáhnou konce služby v červenci 2019. To znamená, že společnost Microsoft již nebude moci podporovat hardware a software pro řadu StorSimple 5000/7000 po červenci 2019. Zákazníci, kteří používají tato zařízení, by měli svá data StorSimple migrovat do jiných hybridních úložných řešení v Azure. Tento článek popisuje migraci dat ze zařízení řady StorSimple 5000/7000 do Azure File Sync (AFS).

## <a name="intended-audience"></a>Zamýšlená cílová skupina

Tento článek je určen pro odborníky v oblasti informačních technologií (IT) a znalostní pracovníky odpovědné za nasazení a správu zařízení řady StorSimple 5000/7000 v datovém centru. Zákazníkům, kteří používají svá zařízení StorSimple pro úlohy souborového serveru (se systémem Windows Server), může být tato cesta migrace obzvláště lákavá. Pokud se domníváte, že funkce Azure File Sync fungují dobře pro vaši organizaci, pak tento článek vám pomůže pochopit, jak přejít na tato řešení ze StorSimple.

## <a name="migration-considerations"></a>Požadavky na migraci

Tento proces funguje pro zákazníky, kteří nakonfigurovali sdílenou složku systému Windows pomocí svazku StorSimple pro úložiště. Migrace dat ze StorSimple 5000/7000 do Azure File Sync zahrnuje převod tohoto umístění sdílené složky na [koncový bod serveru](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) a pak pomocí místně připojených jednotek jako jiného koncového bodu, který se pak stane vaším novým umístěním. 

Při přechodu na AFS je třeba vzít v úvahu následující body:

1. Soubory Azure má aktuálně omezení 5 TB/sdílené složky. Toto omezení lze překonat pomocí Azure File Sync s daty rozloženými napříč několika sdílenými složkami Azure File. Další informace najděte [vzor růstu dat pro nasazení souborů Azure](https://docs.microsoft.com/azure/storage/files/storage-files-planning).
2. Tato migrace stáhne celou primární sadu dat do místního zařízení, protože se kopírování dat provádí z místního zařízení. Ujistěte se, že máte dostatečnou šířku pásma pro tento přenos.
3. Tento proces nezachová snímky, které již byly vytvořeny. Migruje pouze primární data. Proces také nezachovává přidružené šablony šířky pásma nebo zásady zálohování. [Azure Backup slouží](https://docs.microsoft.com/azure/backup/backup-azure-files) k nastavení zásad zálohování po migraci dat ve sdílené složce Azure.
4. StorSimple poskytuje hardware první strany. Však s Azure Files/Azure File Sync, používáte vlastní místní windows server hardware jako místní mezipaměti. Musíte zajistit, že máte dostatečnou úložnou kapacitu, abyste udrželi místní datovou sadu podle vašeho výběru. Další informace o vrstvení a nastavení cíle požadovaného volného místa najděte v článku [Vytvoření koncového bodu serveru při nasazování Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal). 
5. Zkontrolujte [ceny pro Azure File Sync,](https://azure.microsoft.com/pricing/details/storage/files/) protože se liší od StorSimple. AFS nemá deduplikaci a kompresi jako StorSimple.

## <a name="migration-prerequisites"></a>Předpoklady migrace

Tady najdete předpoklady migrace pro vaše starší zařízení řady 5000 nebo 7000 do Azure File Sync. Než začnete, ujistěte se, že máte:

- Přístup k zařízení řady StorSimple 5000/7000 se softwarem verze v2.1.1.518 nebo novější. Dřívější verze nejsou podporovány. V pravém horním rohu webového uživatelského rozhraní zařízení StorSimple by měla být zobrazena verze softwaru, která je spuštěna.  
    Pokud vaše zařízení není spuštěno v2.1.1.518, upgradujte systém na požadovanou minimální verzi. Podrobné pokyny naleznete v [podrobnostech o upgradu systému na v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
- Zkontrolujte všechny aktivní úlohy zálohování, které jsou spuštěny na zdrojovém zařízení. To zahrnuje úlohy na hostiteli StorSimple Data Protection Console. Počkejte na dokončení aktuálních úloh. 
- Přístup k hostiteli systému Windows Server připojenému k zařízení řady StorSimple 5000-7000. Hostitel musí mít podporovanou verzi Windows Serveru, jak je popsáno v [interoperabilitě synchronizace souborů Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning).
- Svazky StorSimple jsou připojeny na hostiteli a obsahují sdílené složky.
- Hostitel má dostatečné místní úložiště pro uložení dat uložených v místní mezipaměti.
- Přístup na úrovni vlastníka k předplatnému Azure, které použijete k nasazení Azure File Sync. Pokud nemáte oprávnění na úrovni vlastníka nebo správce, může se při vytváření cloudového koncového bodu pro vaši skupinu synchronizace vyskytnat problémy.
- Přístup k [účtu úložiště pro obecné účely v2](https://docs.microsoft.com/azure/storage/common/storage-account-overview) se sdílenou složkou Azure, se kterou chcete synchronizovat. Další informace najdete v článku o [vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
  - Jak [vytvořit sdílenou složku Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

## <a name="migration-process"></a>Proces migrace

Migrace dat ze StorSimple 5000-7000 do AFS je dvoustupňový proces:
1.  Replikujte data z místního souborového serveru, kde se svazky StorSimple připojují ke sdílené složce Azure Files.  Replikace se provádí prostřednictvím agenta AFS, který nainstalujete.
2.  Odpojte zařízení StorSimple. Místní disky pak fungují jako místní mezipaměti.

### <a name="migration-steps"></a>Kroky migrace

Proveďte následující kroky k migraci sdílené složky systému Windows nakonfigurované na svazcích StorSimple do sdílené složky Azure File Sync. 
1.  Tyto kroky proveďte na stejném hostiteli systému Windows Server, kde jsou připojeny svazky StorSimple, nebo použijte jiný systém. 
    - [Připravte Windows Server na použití s Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Nainstalujte agenta Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [Nasazení služby Synchronizace úložiště](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Zaregistrujte službu Windows Server with Storage Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Vytvořte skupinu synchronizace a koncový bod cloudu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint). Pro každou sdílenou složku systému Windows, kterou je třeba migrovat z hostitele, je třeba vytvořit skupiny synchronizace.
    - [Vytvořte koncový bod serveru](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). Zadejte cestu jako cestu svazku StorSimple, který obsahuje data sdílení souborů. Například pokud je svazek StorSimple jednotky `J`a `J:/<myafsshare>`data jsou umístěna v aplikaci , přidejte tuto cestu jako koncový bod serveru. Ponechte **vrstvení** jako **zakázané**.
2.  Počkejte na dokončení synchronizace souborového serveru. U každého serveru v dané skupině synchronizace se ujistěte, že:
    - Časová razítka pro poslední pokus o synchronizaci pro nahrávání i stahování jsou nedávné.
    - Stav je zelený pro nahrávání i stahování.
    - **Aktivita synchronizace** zobrazuje velmi málo nebo žádné soubory, které by zůstaly synchronizovány.
    - **Soubory, které nejsou synchronizovány,** jsou 0 pro nahrávání i stahování.
    Další informace o dokončení synchronizace serveru najdete [v článku Poradce při potížích se synchronizací souborů Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other). Synchronizace může trvat několik hodin až dní v závislosti na velikosti dat a šířce pásma. Po dokončení synchronizace se všechna vaše data bezpečně napojila ve sdílené složce Azure. 
3.  Přejděte na sdílené složky na svazcích StorSimple. Vyberte sdílenou složku, klepněte pravým tlačítkem myši a vyberte **vlastnosti**. Poznamenejte si oprávnění ke sdílení v části **Zabezpečení**. Tato oprávnění bude nutné ručně použít na novou sdílenou složku v pozdějším kroku.
4.  V závislosti na tom, zda používáte stejného hostitele systému Windows Server nebo jiného hostitele, budou další kroky odlišné.

    Tento krok přeskočte a přejděte k dalšímu kroku, pokud používáte jiného hostitele systému Windows Server. Pokud používáte stejný souborový server systému Windows pro systém AFS, dojde nyní k několika minutám prostojů. 
    - **Spustí se prostoje** – odstraní koncový bod serveru, který jste vytvořili v *kroku 1F*. 
    - Vytvořte nový koncový bod serveru s cestou, na které se mají data do budoucna naskládat.
    - Jakmile koncový bod serveru zobrazí jako v pořádku (to může trvat několik minut), zobrazí se data v tomto novém umístění. Nyní můžete nakonfigurovat hostitele systému Windows Server tak, aby zobrazoval soubory z tohoto nového umístění. - **Prostoje končí**.
5.  Pokud používáte jiný souborový server Windows pro Azure File Sync, pak nebudete mít žádné prostoje. 
    - Přidejte další koncový bod serveru s cestou místního úložiště, které jste připraveni použít jako mezipaměť namísto zařízení StorSimple. 
    - Budete mít možnost vidět soubory na novém serveru během několika minut. Můžete kdykoli provést přechod ze zařízení StorSimple do tohoto nového umístění na hostiteli.

    > [!TIP] 
    > Zvažte konfiguraci této nové sdílené složky se stejným názvem a stejnou cestou, kterou nahrazuje, abyste minimalizovali narušení. Pokud používáte DFS-N, může to vyžadovat změny v konfiguraci.
6.  Překonfigurujte oprávnění ke sdílení, jak je uvedeno v *kroku 3*.

Pokud během migrace dat narazíte na nějaké problémy, [obraťte se na podporu společnosti Microsoft](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>Další kroky

Pokud afs není pro vás to pravé řešení, přečtěte si, jak [migrovat data ze série StorSimple 5000-7000 do zařízení řady 8000](storsimple-8000-migrate-from-5000-7000.md).

