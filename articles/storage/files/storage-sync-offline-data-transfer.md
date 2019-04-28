---
title: Migrace dat do Azure File Sync s použitím Azure Data Box a další metody
description: Migrace hromadných dat způsobem, který je kompatibilní s Azure File Sync.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d1ec5168b898d0aa75c12e6eb435e20c09de1929
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759364"
---
# <a name="migrate-bulk-data-to-azure-file-sync"></a>Migrace hromadných dat do Azure File Sync
Hromadné dat můžete migrovat do služby Azure File Sync dvěma způsoby:

* **Nahrajte soubory pomocí Azure File Sync.** Toto je nejjednodušší způsob. Přesuňte soubory místně na Windows Server 2012 R2 nebo novější a nainstalujte agenta Azure File Sync. Po nastavení synchronizace, nahraje soubory ze serveru. (Naši zákazníci aktuálně prostředí z nahrávání průměrné rychlosti 1 TiB o každé dva dny.) Aby bylo zajištěno, že váš server nepoužívá příliš velkou šířku pásma pro vaše datové centrum, můžete chtít nastavit [plán omezení šířky pásma](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).
* **Přenos souborů do režimu offline.** Pokud nemáte dostatečnou šířku pásma, nebudete moci nahrávat soubory do Azure v rozumném čase. Výzvu se počáteční synchronizace celou sadu souborů. Chcete-li tento problém vyřešili, použijte nástroje pro migraci offline hromadné [rodina zařízení Azure Data Box](https://azure.microsoft.com/services/storage/databox). 

Tento článek vysvětluje, jak migrovat do režimu offline soubory tak, aby je kompatibilní s Azure File Sync. Postupujte podle těchto pokynů, aby nedocházelo ke konfliktům souborů a zachování souborů a složek seznamy řízení přístupu (ACL) a časová razítka po povolení synchronizace.

## <a name="online-migration-tools"></a>Online migrace nástroje
Proces popisujeme v tomto článku funguje pouze pro zařízení Data Box, ale také pro jiné nástroje pro migraci v režimu offline. Funguje i pro online nástrojů, jako je AzCopy, Robocopy, nebo partnerské nástroje a služby. Ale překonat počáteční odeslat výzvu, postupujte podle kroků v tomto článku tyto nástroje používat způsobem, který je kompatibilní s Azure File Sync.


## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Výhody použití nástroje pro přenos dat do offline režimu
Tady jsou hlavní výhody použití nástroj pro přenos jako zařízení Data Box pro offline migrace:

- Není nutné nahrát všechny soubory v síti. Pro velké obory názvů může tento nástroj uložit významné šířky pásma a čas.
- Při použití Azure File Sync, bez ohledu na to, které nástroj pro přenos použijete (zařízení Data Box, služba Azure Import/Export a tak dále), za provozu server odešle pouze soubory, které změna po přesunu dat do Azure.
- Azure File Sync synchronizuje vaše soubory a složkami seznamy ACL i v případě, že nástroj pro migraci offline hromadné nebude přenosu seznamy ACL.
- Data Box a Azure File Sync vyžadují nedojde k žádnému výpadku. Použijete-li k přenosu dat do Azure Data Box, efektivní použití šířky pásma sítě a věrnost souboru. Můžete také udržovat váš obor názvů aktuální odesláním pouze soubory, které změna po přesunu dat do Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Předpoklady pro přenos dat offline
Neměli byste povolit synchronizaci na serveru, který provádíte migraci dokončit přenos dat offline. Je dobré vzít v úvahu před zahájením jsou následující:

- Pokud plánujete použít zařízení Data Box pro hromadné migraci: Zkontrolujte [požadavcích pro nasazení pro zařízení Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Plánování topologie konečné Azure File Sync: [Plánování nasazení služby Azure File Sync](storage-sync-files-planning.md)
- Vyberte účty úložiště Azure, která bude obsahovat sdílené složky, které chcete synchronizovat s. Ujistěte se, že se migrace hromadných stane dočasné pracovní složky ve stejném účtech úložiště. Migrace hromadných jde Povolit jenom využitím konečné a pracovní-sdílenou složku, která se nacházejí ve stejném účtu úložiště.
- Migrace hromadných je možné využít pouze při vytváření nové relace synchronizace s umístěním serveru. Nelze povolit hromadné migrace s existující relace synchronizace.


## <a name="process-for-offline-data-transfer"></a>Proces pro přenos dat offline
Tady je postup pro nastavení Azure File Sync způsobem, který je kompatibilní s nástroji pro migraci hromadné jako je například Azure Data Box:

![Diagram znázorňující, jak nastavit službu Azure File Sync](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Krok | Detail |
|---|---------------------------------------------------------------------------------------|
| ![Krok 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Pořadí Data Box](../../databox/data-box-deploy-ordered.md). Nabídky rodiny zařízení Data Box [několik produktů](https://azure.microsoft.com/services/storage/databox/data) podle svých potřeb. Až se zobrazí Data Box, postupujte podle jeho [dokumentaci ke kopírování dat](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) do této cesty UNC na zařízení Data Box:  *\\< DeviceIPAddres\>\<StorageAccountName_AzFile\> \<ShareName\>*. Tady *ShareName* je název pracovní sdílené složky. Odeslat zpět do Azure Data Box. |
| ![Krok 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Počkejte, dokud vaše soubory zobrazí sdílené složky Azure, které jste zvolili jako dočasné pracovní složky. *Než povolíte synchronizaci do těchto sdílených složek.* |
| ![Krok 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Vytvořte novou prázdnou sdílenou složku pro každou sdílenou složku, která pro vás vytvořili zařízení Data Box. Této nové sdílené složky musí být ve stejném účtu úložiště jako sdílenou složku zařízení Data Box. [Jak vytvořit novou sdílenou složku Azure file](storage-how-to-create-file-share.md). |
| ![Krok 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Vytvořit skupinu synchronizace](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) ve službě synchronizace úložiště. Odkaz na prázdnou sdílenou složku jako koncový bod cloudu. Tento krok opakujte pro každou sdílenou složku zařízení Data Box. [Nastavení Azure File Sync](storage-sync-files-deployment-guide.md). |
| ![Krok 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Přidání adresáře serveru za provozu jako koncový bod serveru](storage-sync-files-deployment-guide.md#create-a-server-endpoint). V procesu zadejte přesunout soubory do Azure a odkazovat na pracovní složky. Můžete povolit nebo zakázat vrstvení podle potřeby cloudu. Při vytváření odkazovat na koncový bod serveru na serveru za pracovní sdílené složky. Na **přidat koncový bod serveru** okně v části **do offline režimu přenosu dat**vyberte **povoleno**a pak vyberte pracovní sdílené složky, které musí být ve stejném účtu úložiště jako cloud koncový bod. Seznam dostupných sdílených složek se tady, filtruje tak, že účet úložiště a sdílené složky, které nejsou již probíhá synchronizace. |

![Snímek obrazovky portálu Azure uživatelského rozhraní, jak povolit přenos offline dat při vytváření nového koncového bodu serveru](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Sdílené složky synchronizace
Po vytvoření koncový bod serveru se spustí synchronizaci. Procesu synchronizace určuje, zda každý soubor na serveru existuje taky v pracovní sdílenou složku, ve kterém zařízení Data Box uloženy soubory. Pokud soubor existuje, zkopíruje procesu synchronizace souboru ze sdílené složce pracovní místo odesílání ze serveru. Pokud soubor neexistuje ve sdílené složce pracovní nebo novější verze je k dispozici na místním serveru, proces synchronizace nahraje soubor z místního serveru.

> [!IMPORTANT]
> Můžete povolit režim migrace hromadných pouze tehdy, když vytváříte koncový bod serveru. Po vytvoření koncový bod serveru nejde migrovat hromadných dat z již probíhá synchronizace serveru integrovat do oboru názvů.

## <a name="acls-and-timestamps-on-files-and-folders"></a>Seznamy řízení přístupu a časová razítka na soubory a složky
Azure File Sync zajistí, že souborům a složkám seznamy ACL jsou synchronizované z serveru za provozu i v případě, že nástroj pro migraci hromadné, který jste použili nebyl původně přenosu seznamy ACL. Z tohoto důvodu pracovní sdílená složka nemusí obsahovat všechny seznamy ACL pro soubory a složky. Když povolíte funkci migrace offline dat při vytváření nového koncového bodu serveru, se synchronizují všechny seznamy ACL v souborech na serveru. Také se synchronizují nově vytvořené a upravené časové razítko.

## <a name="shape-of-the-namespace"></a>Tvar oboru názvů
Když povolíte synchronizaci, je obsah serveru určuje tvar oboru názvů. Pokud se soubory jsou odstraněny z místního serveru po zařízení Data Box snímků a migraci dokončit, tyto soubory nelze přesunout do oboru názvů za provozu, synchronizaci. Zůstanou ve sdílené složce pracovní, ale nejsou zkopírovány. To je nezbytné, protože synchronizace udržuje oboru názvů podle serveru za provozu. Data Box *snímku* je právě pracovní začátku pro kopírování souborů efektivní. Není autority pro obrazec za obor názvů.

## <a name="cleaning-up-after-bulk-migration"></a>Čištění po hromadné migrace 
Server dokončení počáteční synchronizace oboru názvů, používejte soubory migrovat hromadné zařízení Data Box pracovní sdílenou složku. Na **vlastnosti koncového bodu serveru** okna portálu Azure portal, v **do offline režimu přenosu dat** části stav se změní z **probíhá** k **dokončeno** . 

![Snímek obrazovky okna Vlastnosti koncového bodu serveru, kde se nachází stavu a zakázat ovládací prvky pro offline datové přenosy](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Nyní můžete vyčistit testovací sdílené složky pro úsporu nákladů:

1. Na **vlastnosti koncového bodu serveru** okno, pokud je stav **dokončeno**vyberte **zakázat přenos dat offline**.
2. Zvažte odstranění pracovní sdílené složky, abyste ušetřili náklady. Pracovní sdílené složky pravděpodobně neobsahuje souborům a složkám seznamy ACL, takže není velmi užitečné. Pro účely zálohování bodu v čase, vytvořte reálné [snímek synchronizaci sdílené složky Azure](storage-snapshots-files.md). Je možné [nastavení zálohování Azure pro pořizování snímků]( ../../backup/backup-azure-files.md) podle plánu.

Zakázat režim přenosu dat offline pouze v případě, že je stav **dokončeno** nebo pokud chcete zrušit kvůli chybě konfigurace. Je-li zakázat režim během nasazení, soubory se začnou odesílat ze serveru i v případě, že váš pracovní sdílené složky je stále k dispozici.

> [!IMPORTANT]
> Když zakážete režim přenosu dat offline, nelze ho znovu nepovolíte, i v případě, že pracovní sdílené složky z migrace hromadných je stále k dispozici.

## <a name="next-steps"></a>Další postup
- [Plánování nasazení služby Azure File Sync](storage-sync-files-planning.md)
- [Nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md)
