---
title: Použijte Data Box a další metody pro offline ingestování do Azure File Sync.
description: Podpora procesu a osvědčené postupy pro povolení migrace kompatibilní hromadné synchronizace.
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: fauhse
ms.component: files
ms.openlocfilehash: a184e0563d1ad26671c38cabe07f42d97cbe2885
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213291"
---
# <a name="migrate-to-azure-file-sync"></a>Migrace do Azure File Sync
Existuje několik možností pro přesun do Azure File Sync:

### <a name="uploading-files-via-azure-file-sync"></a>Nahrávání souborů pomocí Azure File Sync
Přesuňte soubory místně na Windows Server 2012 R2 nebo novější, a nainstalujte agenta Azure File Sync je nejjednodušší možnost. Jakmile sync je nakonfigurovaná, odešlete soubory ze serveru. Právě jsme vyčistily nahrávání průměrná rychlost přes všechny naše zákazníky o velikosti 1 TB o každé dva dny.
Vezměte v úvahu [plán omezení šířky pásma](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter) k zajištění serveru je dobré občanem ve vašem datovém centru.

### <a name="offline-bulk-transfer"></a>Offline hromadného přenosu
Při nahrávání je určitě nejjednodušší možnost, nemusí fungovat pro vás Pokud vaši dostupnou šířku pásma neumožní kvůli synchronizaci souborů do Azure v rozumném čase. Na výzvu k překonání tady je počáteční synchronizace celou sadu souborů. Následně Azure File Sync se přesune pouze změny při jejich výskytu v oboru názvů, který používá obvykle mnohem menší šířku pásma.
Abyste vyřešili toto počáteční nahrávání výzvu, nástroje pro migraci offline hromadné jako je Azure [rodina zařízení Data Box](https://azure.microsoft.com/services/storage/databox) lze použít. V následujícím článku se zaměřuje na procesu, který musíte dodržet těžit z offline migrace způsobem kompatibilní Azure File Sync. Popisuje osvědčené postupy, které vám pomůžou nedošlo ke konfliktu souborů a zachovat souborům a složkám seznamy řízení přístupu a časová razítka, jakmile povolíte synchronizaci.

### <a name="online-migration-tools"></a>Online migrace nástroje
Pokynů pouze nefunguje pro zařízení Data Box. Funguje pro libovolný nástroj pro migraci v režimu offline (například zařízení Data Box) nebo online nástrojů, jako jsou nástroje AzCopy, Robocopy nebo třetích stran a služby. Bez ohledu na to metoda překonat před obrovskou výzvou – počáteční nahrávání, je stále potřeba postupujte podle kroků uvedených níže, aby se začala používat tyto nástroje v synchronizaci kompatibilní způsobem.


## <a name="offline-data-transfer-benefits"></a>Výhody pro přenos dat offline
Hlavní výhody offline migrace, při použití zařízení Data Box jsou následující:

- Při migraci souborů do Azure prostřednictvím proces přenosu offline hromadné, jako je Data Box, není nutné nahrát všechny soubory ze serveru v síti. Pro velké obory názvů to může znamenat výrazné úspory šířky pásma sítě a čas.
- Při použití Azure File Sync, pak bez ohledu na režim dopravy použita (zařízení Data Box, Import úložiště Azure, atd.), nahraje živé serveru pouze soubory, které se změnily od odeslaná data do Azure.
- Azure File Sync zajistí, že vaše soubory a složkami seznamy ACL se synchronizují také – i v případě, že produkt migrace offline hromadného přenosu není seznamy ACL.
- Při použití Azure Data Box a Azure File Sync, není bez výpadků. Používání zařízení Data Box pro přenos dat v Azure umožňuje efektivnější využití šířky pásma sítě při zachování věrnost souboru. Díky tomu navíc budou váš obor názvů aktuální odesláním pouze soubory, které se změnily od odeslání zařízení Data Box.

## <a name="plan-your-offline-data-transfer"></a>Plánování přenos offline dat.
Než začnete, projděte si následující informace:

- Dokončete proces migrace hromadných na jeden nebo více sdílených složek Azure před povolením synchronizovat s Azure File Sync.
- Pokud plánujete použít zařízení Data Box pro hromadné migraci: Zkontrolujte [požadavcích pro nasazení pro zařízení Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Plánování topologie konečné Azure File Sync: [Plánování nasazení služby Azure File Sync](storage-sync-files-planning.md)
- Vyberte účty úložiště Azure, která bude obsahovat sdílené složky, které chcete synchronizovat s. Ujistěte se, že se migrace hromadných stane dočasné pracovní složky ve stejném účtech úložiště. Migrace hromadných jde Povolit jenom využitím konečné a pracovní-sdílenou složku, která se nacházejí ve stejném účtu úložiště.
- Migrace hromadných je možné využít pouze při vytváření nové relace synchronizace s umístěním serveru. Nelze povolit hromadné migrace s existující relace synchronizace.

## <a name="offline-data-transfer-process"></a>Proces přenosu dat offline
Tato část popisuje postup nastavení Azure File Sync způsobem kompatibilní s nástroji pro migraci hromadné jako je například Azure Data Box.

![Vizualizace kroky procesu, které jsou také podrobně popsány v následujícím odstavci](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Krok | Detail |
|---|---------------------------------------------------------------------------------------|
| ![Krok 1 procesu](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Pořadí Data Box](../../databox/data-box-deploy-ordered.md). Existují [několik nabídek v rámci rodiny zařízení Data Box](https://azure.microsoft.com/services/storage/databox/data) tak, aby odpovídala vašim potřebám. Zobrazí Data Box a postupujte podle zařízení Data Box [dokumentaci ke kopírování dat](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box). Ujistěte se, že jsou data zkopírována do této cesty UNC na zařízení Data Box: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>` kde `ShareName` je název pracovní sdílené složky. Odeslat zpět do Azure Data Box. |
| ![Proces krok 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Počkejte, až se zobrazí vaše soubory do sdílené složky Azure, kterou jste určili jako dočasné pracovní složky. **Než povolíte synchronizaci pro tyto sdílené složky!** |
| ![Proces krok 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Vytvořte novou sdílenou složku, která je prázdná pro každou sdílenou složku, která pro vás vytvořili zařízení Data Box. Zajistěte, aby této nové sdílené složky v rámci stejného účtu úložiště jako sdílenou složku zařízení Data Box. [Jak vytvořit novou sdílenou složku Azure file](storage-how-to-create-file-share.md). |
| ![Proces kroku 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Vytvořit skupinu synchronizace](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) služba synchronizace úložiště a odkaz na prázdnou sdílenou složku jako koncový bod cloudu. Tento krok opakujte pro každou sdílenou složku zařízení Data Box. Zkontrolujte [nasazení Azure File Sync](storage-sync-files-deployment-guide.md) průvodce a postupujte podle pokynů k nastavení Azure File Sync. |
| ![Proces krok 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Přidání adresáře serveru za provozu jako koncový bod serveru](storage-sync-files-deployment-guide.md#create-a-server-endpoint). Zadejte v procesu již přesunout soubory do Azure a odkazovat na pracovní složky. Je podle vašeho výběru k povolení nebo zakázání podle potřeby vrstvení cloudu. Při vytváření koncového bodu serveru na aktivním serveru, budete muset odkaz pracovní sdílené složky. V okně Nový koncový bod serveru povolit "Do offline režimu přenosu dat" (viz obrázek níže) a odkazovat na pracovní sdílenou složku, která se musí nacházet ve stejném účtu úložiště jako koncový bod cloudu. Seznam dostupných sdílených složek se filtruje tak, že účet úložiště a sdílené složky, které nejsou již probíhá synchronizace. |

![Vizualizace Azure portal uživatelského rozhraní pro povolení Offline přenos dat při vytváření nového koncového bodu serveru.](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Sdílené složky synchronizace
Po vytvoření koncový bod serveru se zahájení synchronizace. Pro každý soubor, který existuje v serveru pro synchronizace určí, jestliže tento soubor existuje taky v pracovní sdílenou složku, ve kterém zařízení Data Box uloží soubory, a pokud ano, synchronizace zkopíruje soubor ze sdílené složce pracovní místo odesílání ze serveru. Pokud soubor neexistuje ve sdílené složce pracovní nebo novější verze je k dispozici na místním serveru, pak synchronizace nahrajte soubor z místního serveru.

> [!IMPORTANT]
> Režim migrace hromadných lze povolit pouze při vytváření koncového bodu serveru. Po vytvoření koncový bod serveru neexistuje momentálně žádný způsob, jak integrovat hromadné migraci dat ze serveru již synchronizaci do oboru názvů.

## <a name="acls-and-timestamps-on-files-and-folders"></a>Seznamy řízení přístupu a časová razítka na soubory a složky
Azure File Sync zajistí, že souborům a složkám seznamy ACL jsou synchronizované z serveru za provozu i v případě, že nástroj pro migraci hromadné, který byl použit není přenosu seznamy ACL původně. To znamená, že je OK pracovní sdílená složka neobsahuje žádné seznamy řízení přístupu k souborům a složkám. Když povolíte funkci migrace offline dat při vytváření nového koncového bodu serveru, seznamy ACL se budou synchronizovat v tuto chvíli pro všechny soubory na serveru. Totéž platí pro vytvoření – a změnil-časová razítka.

## <a name="shape-of-the-namespace"></a>Tvar oboru názvů
Co je na serveru, když je povolená synchronizace určuje tvar obor názvů. Pokud odstranění souborů na místním serveru po zařízení Data Box "-snímek" a - migrace a potom tyto soubory nebudou zařazení do systému za provozu, synchronizaci oboru názvů. Bude i nadále v pracovní položce, ale nikdy zkopírovány. To je požadované chování jako synchronizace udržuje oboru názvů podle serveru za provozu. Data Box "snímku" je právě pracovní začátku pro kopírování souborů efektivní a ne autority pro obrazec za obor názvů.

## <a name="finishing-bulk-migration-and-clean-up"></a>Dokončení migrace hromadných a čištění
Následující snímek obrazovky ilustruje okna Vlastnosti koncového bodu serveru na webu Azure Portal. V části offline přenosu dat můžete sledovat stav procesu. Buď se zobrazí "Probíhá" nebo "Dokončeno".

Po dokončení počáteční synchronizace. celý oboru názvů na serveru se bude dokončení využití souboru pracovní soubory migrovat sdílenou složku pomocí hromadné zařízení Data Box. Sledujte ve vlastnostech serveru koncového bodu pro přenos offline dat, který se mění stav na "Dokončeno". V tuto chvíli můžete vyčistit pracovní sdílenou složku snížení nákladů:

1. Spuštění "Zakázat přenos dat v režimu offline" ve vlastnosti koncového bodu serveru dokončení stav"".
2. Zvažte odstranění pracovní sdílenou složku snížení nákladů. Pracovní sdílených složek je nepravděpodobné, že obsahují soubory a složkami seznamy řízení přístupu a jako takový je omezené použití. Pro účely zálohování "bodu v čase", spíše vytvořit reálné [snímek synchronizaci sdílené složky Azure](storage-snapshots-files.md). Je možné [povolit Azure Backup pro pořizování snímků]( ../../backup/backup-azure-files.md) podle plánu.

![Azure portal uživatelského rozhraní pro vlastnosti koncového bodu serveru kde se nachází stavu a zakázat ovládací prvky do offline režimu přenosu dat vizualizace.](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Tento režim zakažte pouze v případě, že stav je "Completed (dokončeno) nebo chcete skutečně zrušit z důvodu chybné konfigurace. Chcete-li zakázat režim uprostřed způsob, jakým legitimní nasazení, soubory se začnou odeslat ze serveru, i v případě, že váš pracovní sdílené složky je stále k dispozici.

> [!IMPORTANT]
> Po zakázání přenosu dat offline neexistuje žádný způsob, jak ji znovu povolit, i v případě, že pracovní sdílené složky z migrace hromadných je stále k dispozici.

## <a name="next-steps"></a>Další postup
- [Plánování nasazení služby Azure File Sync](storage-sync-files-planning.md)
- [Nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md)
