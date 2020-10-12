---
title: Migrace dat z řady StorSimple 5000-7000 na Azure File Sync | Microsoft Docs
description: Popisuje, jak migrovat data z řady StorSimple 5000/7000 na Azure File Sync (AFS).
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 5d656fd8757580b8ce96acf168e92fc847d400ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514064"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Migrace dat z řady StorSimple 5000-7000 na Azure File Sync

> [!IMPORTANT]
> 9. července 2019 bude StorSimple série 5000/7000 získat stav EOS (konec podpory). Zákazníkům řady StorSimple 5000/7000 doporučujeme migrovat na jednu z alternativ popsaných v dokumentu.

Migrace dat je proces přesunu dat z jednoho umístění úložiště do druhého. To předpokládá, že bude mít přesnou kopii aktuálních dat organizace z jednoho zařízení do jiného zařízení – nejlépe bez přerušení nebo zakázání aktivních aplikací – a pak přesměrovat všechny aktivity vstupu a výstupu (v/v) na nové zařízení. 

Zařízení StorSimple 5000 a 7000 Series Storage budou v červenci 2019 na konci služby. To znamená, že společnost Microsoft již nebude moci podporovat hardware a software pro StorSimple 5000/7000 Series po 2019. července. Zákazníci, kteří používají tato zařízení, by měli migrovat svá StorSimple data do jiných řešení hybridních úložišť v Azure. Tento článek popisuje migraci dat ze zařízení řady StorSimple 5000/7000 na Azure File Sync (AFS).

## <a name="intended-audience"></a>Zamýšlená cílová skupina

Tento článek je určený pro odborníky v oblasti informačních technologií (IT) a pracovníky znalostní báze, kteří zodpovídají za nasazení a správu zařízení řady StorSimple 5000/7000 Series v datacentrech. Zákazníci, kteří používají svá zařízení StorSimple pro úlohy souborového serveru (se systémem Windows Server), můžou tuto cestu migrace obzvlášť odvolat. Pokud se domníváte, že funkce Azure File Sync dobře fungovat pro vaši organizaci, pak tento článek vám pomůže pochopit, jak na tato řešení přejít z StorSimple.

## <a name="migration-considerations"></a>Posouzení migrace

Tento proces funguje pro zákazníky, kteří nakonfigurovali sdílenou složku systému Windows pomocí svazku StorSimple pro úložiště. Migrace dat z StorSimple 5000/7000 na Azure File Sync zahrnuje převod tohoto umístění sdílené složky na [koncový bod serveru](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) a následné použití místně připojených jednotek jako jiného koncového bodu, který se pak stane vaším novým umístěním. 

Při přechodu na AFS by se měly vzít v úvahu tyto body:

1. Soubory Azure mají v současné době omezení 5 TB/sdílení. Toto omezení se dá překonat pomocí Azure File Sync s daty rozloženými mezi více sdílených složek Azure. Další informace najdete v části [model růstu dat pro nasazení souborů Azure](https://docs.microsoft.com/azure/storage/files/storage-files-planning).
2. Tato migrace stáhne celou primární datovou sadu na místní zařízení, protože se tato kopie dat provádí z místního zařízení. Ujistěte se, že máte dostatečnou šířku pásma pro přizpůsobení tohoto přenosu.
3. Tento proces nezachovává snímky, které již byly vytvořeny. Migruje pouze primární data. Proces také nezachovává přidružené šablony šířky pásma ani zásady zálohování. Po migraci dat do sdílené složky Azure [použijte Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files) k nastavení zásad zálohování.
4. StorSimple poskytuje hardware první strany. V případě služby Azure Files/Azure File Sync ale používáte jako místní mezipaměť vlastní místní hardware Windows serveru. Je nutné zajistit, abyste měli dostatečnou úložnou kapacitu, abyste zachovali datovou sadu svého výběru v místním prostředí. Další informace o vrstvení a nastavení cíle volného místa najdete v tématu Postup [Vytvoření koncového bodu serveru při nasazení Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal). 
5. Projděte si [ceny za Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/) , jak se liší od StorSimple. AFS nemá odstranění duplicit a kompresi jako StorSimple.

## <a name="migration-prerequisites"></a>Předpoklady migrace

Tady najdete potřebné předpoklady migrace pro zařízení řady starší 5000 nebo 7000, aby bylo možné Azure File Sync. Než začnete, ujistěte se, že máte:

- Přístup k zařízení řady StorSimple 5000/7000 s verzí software verze v 2.1.1.518 nebo novějším. Starší verze se nepodporují. V pravém horním rohu webového uživatelského rozhraní zařízení StorSimple by se měla zobrazit verze softwaru, která je spuštěná.  
    Pokud vaše zařízení neběží v 2.1.1.518, upgradujte prosím systém na požadovanou minimální verzi. Podrobné pokyny najdete v tématu [upgrade systému na verzi v 2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
- Vyhledejte všechny aktivní úlohy zálohování, které běží na zdrojovém zařízení. To zahrnuje úlohy na hostiteli konzoly StorSimple data Protection. Počkejte, než se aktuální úlohy dokončí. 
- Přístup k hostiteli Windows serveru připojenému k zařízení řady StorSimple 5000-7000 V hostiteli musí být spuštěná podporovaná verze Windows serveru, jak je popsáno v tématu [interoperabilita Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning).
- Svazky StorSimple jsou připojené k hostiteli a obsahují sdílené složky.
- Hostitel má dostatečné místní úložiště pro ukládání dat uložených v místní mezipaměti.
- Přístup na úrovni vlastníka k předplatnému Azure, které použijete k nasazení Azure File Sync. Pokud nemáte oprávnění na úrovni vlastníka nebo správce, může docházet k problémům při vytváření koncového bodu cloudu pro skupinu synchronizace.
- Přístup k [účtu úložiště pro obecné účely v2](https://docs.microsoft.com/azure/storage/common/storage-account-overview) se sdílenou složkou Azure, ke které se chcete synchronizovat. Další informace najdete v článku o [vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
  - Jak [vytvořit sdílenou složku Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share)

## <a name="migration-process"></a>Proces migrace

Migrace dat z StorSimple 5000-7000 na AFS je proces se dvěma kroky:
1.  Replikujte data z místního souborového serveru, kde jsou svazky StorSimple připojené ke sdílené složce služby soubory Azure.  Replikaci provádíte prostřednictvím agenta AFS, který nainstalujete.
2.  Odpojte zařízení StorSimple. Místní disky pak fungují jako místní mezipaměť.

### <a name="migration-steps"></a>Kroky migrace

Provedením následujících kroků migrujete sdílenou složku systému Windows nakonfigurovanou na svazcích StorSimple do sdílené složky Azure File Sync. 
1.  Proveďte tyto kroky na stejném hostiteli s Windows serverem, kde jsou připojené svazky StorSimple nebo použijte jiný systém. 
    - [Připravte Windows Server pro použití s Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Nainstalujte agenta Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [Nasaďte službu synchronizace úložiště](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Zaregistrujte Windows Server se službou synchronizace úložiště](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Vytvořte skupinu synchronizace a koncový bod cloudu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint). Skupiny synchronizace je třeba provést pro každou sdílenou složku systému Windows, kterou je třeba migrovat z hostitele.
    - [Vytvořte koncový bod serveru](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). Zadejte cestu jako cestu ke svazku StorSimple, který obsahuje data sdílené složky souborů. Pokud je například svazek StorSimple jednotka `J` a vaše data se nachází v `J:/<myafsshare>` , přidejte tuto cestu jako koncový bod serveru. Ponechte **vrstvu** **zakázanou**.
2.  Počkejte, než se dokončí synchronizace souborového serveru. Pro každý server v dané skupině synchronizace se ujistěte, že:
    - Časová razítka posledního pokusu o synchronizaci pro nahrávání a stahování jsou poslední.
    - Stav je zelený pro nahrávání i stahování.
    - **Aktivita synchronizace** zobrazuje hodně nebo žádné soubory, které by se nezbyly synchronizovat.
    - Soubory, které **nejsou synchronizované** , jsou 0 pro nahrávání i stahování.
    Další informace o tom, kdy je synchronizace serveru dokončena, najdete v tématu [řešení potíží s Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other). Synchronizace může trvat několik hodin, a to v závislosti na velikosti dat a šířce pásma. Po dokončení synchronizace všechna vaše data budou bezpečně ve sdílené složce Azure. 
3.  Přejít ke sdíleným složkám na StorSimple svazcích. Vyberte sdílenou složku, klikněte pravým tlačítkem a vyberte **vlastnosti**. Poznamenejte si oprávnění ke sdílení v části **zabezpečení**. Tato oprávnění bude nutné v pozdějším kroku použít pro novou sdílenou složku ručně.
4.  V závislosti na tom, jestli používáte stejný hostitel Windows serveru nebo jiný, se další kroky liší.

    Pokud používáte jiného hostitele se systémem Windows Server, přeskočte tento krok a přejděte k dalšímu kroku. Pokud používáte stejný souborový server Windows pro AFS, budete teď mít několik minut prostojů. 
    - **Spuštění výpadku** – odstraní koncový bod serveru, který jste vytvořili v *kroku 1f*. 
    - Vytvořte nový koncový bod serveru s cestou, kde chcete, aby se data nacházela dál.
    - Jakmile se koncový bod serveru ukáže jako v pořádku (může to trvat několik minut), zobrazí se tato data v tomto novém umístění. Teď můžete nakonfigurovat hostitele Windows serveru tak, aby sloužil souborům z tohoto nového umístění. - **Výpadky skončí**.
5.  Pokud pro Azure File Sync používáte jiný souborový server Windows, nebudete mít žádný výpadek. 
    - Přidejte další koncový bod serveru s cestou k místnímu úložišti, které jste připravili k použití jako mezipaměť místo zařízení StorSimple. 
    - Soubory na novém serveru budete moct zobrazit během několika minut. V rámci bezplatného přepínání ze zařízení StorSimple na toto nové umístění v hostiteli.

    > [!TIP] 
    > Zvažte konfiguraci této nové sdílené složky se stejným názvem a stejnou cestou, jako je ta, kterou nahrazujete, aby se minimalizovalo přerušení. Pokud používáte DFS-N, může to vyžadovat provedení změn v konfiguraci.
6.  Překonfigurujte oprávnění ke sdílení, jak je uvedeno v *kroku 3*.

Pokud při migraci dat dojde k problémům, [kontaktujte prosím podpora Microsoftu](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>Další kroky

Pokud se pro vás nejedná o správné řešení AFS, Naučte se [migrovat data z řady StorSimple 5000-7000 na zařízení 8000 series](storsimple-8000-migrate-from-5000-7000.md).

