---
title: Migrace dat do Azure Synchronizace souborů pomocí Datové schránky Azure
description: Migrujte hromadná data způsobem, který je kompatibilní s Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d0331419de89775062f1309c5d854cd7325c68e4
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656767"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Migrace hromadných dat do Azure File Sync s využitím Azure DataBoxu
Hromadná data do Azure File Sync můžete migrovat dvěma způsoby:

* **Nahrajte soubory pomocí Azure File Sync.** Toto je nejjednodušší metoda. Přesuňte soubory místně na Windows Server 2012 R2 nebo novější a nainstalujte agenta Azure File Sync. Po nastavení synchronizace budou soubory odeslány ze serveru. (Naši zákazníci v současné době zažívají průměrnou rychlost nahrávání 1 TiB přibližně každé dva dny.) Chcete-li zajistit, aby server nepoužíval příliš velkou šířku pásma pro datové centrum, můžete nastavit [plán omezení šířky pásma](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).
* **Přeneste soubory do pouzdřovat.** Pokud nemáte dostatečnou šířku pásma, nemusí být možné nahrát soubory do Azure v přiměřené mnoitu. Výzvou je počáteční synchronizace celé sady souborů. Chcete-li tento problém překonat, použijte offline nástroje hromadné migrace, jako je [rodina Azure Data Box](https://azure.microsoft.com/services/storage/databox). 

Tento článek vysvětluje, jak migrovat soubory offline způsobem, který je kompatibilní s Azure File Sync. Podle těchto pokynů se vyhněte konfliktům souborů a po povolení synchronizace zachovejte seznamy řízení přístupu k souborům a složkám (Seznamy ACL) a časová razítka.

## <a name="migration-tools"></a>Nástroje pro migraci
Proces, který popisujeme v tomto článku, funguje nejen pro Datovou schránku, ale i pro další nástroje pro migraci offline. Funguje také pro nástroje, jako je AzCopy, Robocopy nebo partnerské nástroje a služby, které pracují přímo přes internet. Chcete-li však překonat počáteční problém nahrávání, postupujte podle kroků v tomto článku a použijte tyto nástroje způsobem, který je kompatibilní s Azure File Sync.

V některých případech je třeba přesunout z jednoho Windows Serveru na jiný Windows Server před přijetím Azure File Sync. [S](https://aka.ms/storagemigrationservice) tím vám může pomoci služba migrace úložiště (SMS). Ať už potřebujete migrovat na verzi operačního systému serveru, která je podporovaná Azure File Sync (Windows Server 2012R2 a vyšší), nebo prostě potřebujete migrovat, protože kupujete nový systém pro Azure File Sync, SMS má mnoho funkcí a výhod, které vám pomohou provést migraci hladce.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Výhody použití nástroje pro přenos dat offline
Zde jsou hlavní výhody použití nástroje pro přenos, jako je Data Box pro migraci offline:

- Nemusíte nahrávat všechny soubory v síti. U velkých oborů názvů by tento nástroj mohl ušetřit značnou šířku pásma sítě a čas.
- Když používáte Azure File Sync, bez ohledu na to, který nástroj pro přenos používáte (Data Box, Azure Import/Export service a tak dále), váš živý server nahraje jenom soubory, které se změní po přesunutí dat do Azure.
- Azure File Sync synchronizuje vaše soubory a soubory ACL, i když nástroj pro hromadnou migraci offline nepřenáší alokace.
- Data Box a Azure File Sync nevyžadují žádné prostoje. Při použití data box u přenosu dat do Azure, můžete používat šířku pásma sítě efektivně a zachovat věrnost souboru. Obor názvů také udržujete aktuální tím, že nahrajete jenom soubory, které se po přesunutí dat do Azure změní.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Předpoklady pro přenos dat offline
Před dokončením offline přenosu dat byste neměli povolit synchronizaci na serveru, který migrujete. Další věci, které je třeba zvážit, než začnete, jsou následující:

- Pokud plánujete použít datovou schránku pro hromadnou migraci: Zkontrolujte [požadavky nasazení pro datovou schránku](../../databox/data-box-deploy-ordered.md#prerequisites).
- Plánování konečné topologie synchronizace souborů Azure: [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
- Vyberte účty úložiště Azure, které budou obsahovat sdílené složky, se kterými chcete synchronizovat. Ujistěte se, že vaše hromadná migrace se stane dočasné pracovní sdílené složky ve stejném účtu úložiště. Hromadná migrace může být povolena pouze s využitím konečné a pracovní- sdílené složky, které jsou umístěny ve stejném účtu úložiště.
- Hromadnou migraci lze využít pouze při vytvoření nového synchronizačního vztahu s umístěním serveru. Hromadnou migraci nelze povolit s existujícím synchronizačním vztahem.


## <a name="process-for-offline-data-transfer"></a>Proces pro přenos dat offline
Tady je postup, jak nastavit Azure File Sync způsobem, který je kompatibilní s nástroji hromadné migrace, jako je Azure Data Box:

![Diagram znázorňující, jak nastavit Azure File Sync](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Krok | Podrobnosti |
|---|---------------------------------------------------------------------------------------|
| ![Krok 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Objednejte si datovou schránku](../../databox/data-box-deploy-ordered.md). Rodina Data Box nabízí [několik produktů, které](https://azure.microsoft.com/services/storage/databox/data) vyhovují vašim potřebám. Když obdržíte datovou schránku, postupujte podle jeho [dokumentace a zkopírujte data](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) do této cesty UNC na poli Data Box: * \\<DeviceIPAddres\>\<StorageAccountName_AzFile\>\<ShareName\>*. Zde *ShareName* je název pracovní sdílené složky. Pošlete datovou schránku zpět do Azure. |
| ![Krok 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Počkejte, až se vaše soubory zobrazí ve sdílených složkách Azure, které jste zvolili jako dočasné pracovní sdílené složky. *Nepovolujte synchronizaci s těmito sdílenými složky.* |
| ![Krok 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | <ul><li>Vytvořte novou prázdnou sdílenou složku pro každou sdílenou složku, kterou pro vás Data Box vytvořil. Tato nová sdílená složky by měla být ve stejném účtu úložiště jako sdílená datová schránka. [Jak vytvořit novou sdílenou složku Azure](storage-how-to-create-file-share.md).</li><li>[Vytvořte skupinu synchronizace](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) ve službě Synchronizace úložiště. Odkaz na prázdnou sdílenou složku jako koncový bod cloudu. Tento krok opakujte pro každou sdílenou složku datové schránky. [Nastavte Azure File Sync](storage-sync-files-deployment-guide.md).</li></ul> |
| ![Krok 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Přidejte svůj živý adresář serveru jako koncový bod serveru](storage-sync-files-deployment-guide.md#create-a-server-endpoint). V procesu určete, že jste přesunuli soubory do Azure a odkazovat na pracovní sdílené složky. Podle potřeby můžete povolit nebo zakázat vrstvení cloudu. Při vytváření koncového bodu serveru na živém serveru odkazujte na pracovní sdílenou složku. V okně **Přidat koncový bod serveru** v části **Offline Data Transfer**vyberte **Povoleno**a pak vyberte pracovní sdílenou složku, která musí být ve stejném účtu úložiště jako koncový bod cloudu. Zde je seznam dostupných sdílených složek filtrován podle účtu úložiště a sdílených složek, které se ještě nesynchronizují. Snímek obrazovky následující v této tabulce ukazuje, jak odkazovat na sdílenou složku DataBox při vytváření koncového bodu serveru na webu Azure Portal. |
| ![Krok 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | Po přidání koncového bodu serveru v předchozím kroku se data spustí automaticky z pravého zdroje. Část [Synchronizace sdílené položky](#syncing-the-share) vysvětluje, kdy data proudí ze sdílené složky DataBox nebo ze serveru Windows Server. |
| |

![Snímek obrazovky s uživatelským rozhraním portálu Azure, který ukazuje, jak povolit offline přenos dat při vytváření nového koncového bodu serveru](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Synchronizace sdílené složky
Po vytvoření koncového bodu serveru se spustí synchronizace. Proces synchronizace určuje, zda každý soubor na serveru také existuje v pracovní sdílené složce, kde data box uloženy soubory. Pokud soubor existuje, proces synchronizace zkopíruje soubor z pracovní sdílené složky, nikoli z jeho odeslání ze serveru. Pokud soubor v pracovní sdílené složce neexistuje nebo pokud je na místním serveru k dispozici novější verze, proces synchronizace soubor odešle z místního serveru.

Při synchronizaci sdílené složky bude synchronizace sloučit všechny chybějící atributy souboru, oprávnění nebo časová razítka z variant souborů na místním serveru a zkombinovat je s jejich protějšky ze sdílené složky DataBox. Tím zajistíte, že každý soubor a složka dorazí se všemi možnými věrnostmi souborů ve sdílené složce Azure.

> [!IMPORTANT]
> Režim hromadné migrace můžete povolit pouze při vytváření koncového bodu serveru. Po vytvoření koncového bodu serveru nelze integrovat hromadně migrovaná data z již synchronizovaného serveru do oboru názvů.

## <a name="acls-and-timestamps-on-files-and-folders"></a>Seznamy AC a časová razítka u souborů a složek
Azure File Sync zajišťuje, že soubory ACL souborů a složek jsou synchronizovány ze živého serveru i v případě, že nástroj hromadné migrace, který jste použili, zpočátku nepřenášel aloky ACL. Z tohoto důvodu pracovní sdílená složka nemusí obsahovat žádné seznamy ACL pro soubory a složky. Pokud povolíte funkci migrace dat offline při vytváření nového koncového bodu serveru, budou na serveru synchronizovány všechny soubory ACL souborů. Nově vytvořená a upravená časová razítka jsou také synchronizována.

## <a name="shape-of-the-namespace"></a>Tvar oboru názvů
Pokud povolíte synchronizaci, obsah serveru určí tvar oboru názvů. Pokud jsou soubory odstraněny z místního serveru po dokončení snímku datové schránky a dokončení migrace, tyto soubory se nepřesunou do živého synchronizačního oboru názvů. Zůstávají v inscenačním podílu, ale nejsou zkopírovány. To je nezbytné, protože synchronizace udržuje obor názvů podle živého serveru. Snímek *snapshot* datové schránky je pouze pracovní půdou pro efektivní kopírování souborů. Není to autorita pro tvar živého oboru názvů.

## <a name="cleaning-up-after-bulk-migration"></a>Úklid po hromadné migraci 
Jakmile server dokončí počáteční synchronizaci oboru názvů, soubory přenesené hromadně migrují datové schránky pomocí pracovní sdílené složky. V okně **Vlastnosti koncového bodu serveru** na webu Azure Portal se v části **Přenos dat offline** změní stav z **Probíhá** na **Dokončeno**. 

![Snímek obrazovky s rozhraním Vlastnosti koncového bodu serveru, kde jsou umístěny ovládací prvky stavu a zakázání přenosu dat offline](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Nyní můžete vyčistit pracovní sdílenou složku a ušetřit náklady:

1. V okně **Vlastnosti koncového bodu** **Completed**serveru vyberte po dokončení stavu **Zakázat přenos dat offline**.
2. Zvažte odstranění pracovní sdílené složky, abyste ušetřili náklady. Pracovní sdílená složka pravděpodobně neobsahuje alokace ACL souborů a složek, takže je nepravděpodobné, že by byla užitečná. Pro účely zálohování bodu v čase vytvořte skutečný [snímek synchronizační sdílené složky Azure](storage-snapshots-files.md). Azure Backup můžete [nastavit tak, aby pořizovat snímky]( ../../backup/backup-afs.md) podle plánu.

Režim přenosu dat offline zakažte pouze v **případě,** že je stav dokončen nebo pokud chcete zrušit z důvodu chybné konfigurace. Pokud režim během nasazení zakážete, soubory se začnou nahrávat ze serveru, i když je pracovní sdílená složka stále k dispozici.

> [!IMPORTANT]
> Po zakázání režimu přenosu dat offline jej nelze znovu povolit, a to ani v případě, že pracovní sdílená část z hromadné migrace je stále k dispozici.

## <a name="next-steps"></a>Další kroky
- [Plánování nasazení synchronizace souborů Azure](storage-sync-files-planning.md)
- [Nasazení Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)
