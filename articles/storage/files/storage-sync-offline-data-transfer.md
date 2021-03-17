---
title: Migrace dat do Azure File Sync s využitím Azure Data Box
description: Migrujte Hromadná data offline, která jsou kompatibilní s Azure File Sync. Vyhněte se konfliktům souborů a zachovejte seznamy ACL souborů a složek a časová razítka poté, co povolíte synchronizaci.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 94abb33d39765a19306a013576d43fb2602d1c37
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017623"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Migrace hromadných dat do Azure File Sync s využitím Azure DataBoxu
Hromadná data můžete migrovat do Azure File Sync dvěma způsoby:

* **Nahrajte soubory pomocí Azure File Sync.** Toto je nejjednodušší způsob. Přesuňte soubory místně do systému Windows Server 2012 R2 nebo novějšího a nainstalujte agenta Azure File Sync. Po nastavení synchronizace se budou soubory nahrávat ze serveru. (Naši zákazníci aktuálně dostanou průměrnou rychlost nahrávání 1 TiB o všech dvou dnech.) Pokud chcete zajistit, aby váš server nepoužíval příliš velkou šířku pásma pro vaše datové centrum, možná budete chtít nastavit [plán omezení šířky pásma](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).
* **Přeneste soubory do offline režimu.** Pokud nemáte dostatečnou šířku pásma, možná nebudete moci v rozumné době nahrávat soubory do Azure. Výzvou je počáteční synchronizace celé sady souborů. K překonání této výzvy použijte offline nástroje pro hromadnou migraci, jako je například [rodina Azure Data box](https://azure.microsoft.com/services/storage/databox). 

Tento článek vysvětluje, jak migrovat soubory offline způsobem, který je kompatibilní s Azure File Sync. Postupujte podle těchto pokynů, abyste se vyhnuli konfliktům souborů a zachovali seznamy řízení přístupu (ACL) k souborům a složkám a časová razítka po povolení synchronizace.

## <a name="migration-tools"></a>Nástroje pro migraci
Proces, který popisujeme v tomto článku, funguje nejen pro Data Box, ale také pro jiné nástroje offline migrace. Funguje taky pro nástroje, jako jsou AzCopy, Robocopy nebo partnerské nástroje a služby, které fungují přímo přes Internet. Chcete-li však přecházet na úvodní výzvu k odeslání, postupujte podle kroků v tomto článku, abyste tyto nástroje používali způsobem, který je kompatibilní s Azure File Sync.

V některých případech je před přijetím Azure File Sync nutné přesunout z jednoho serveru Windows do jiného systému Windows Server. [Služba migrace úložiště](/windows-server/storage/storage-migration-service/overview) (SMS) vám může s tím pomáhat. Bez ohledu na to, jestli potřebujete migrovat na verzi operačního systému serveru, která je podporovaná Azure File Sync (Windows server 2012R2 a novější), nebo stačí provést migraci, protože si koupíte nový systém pro Azure File Sync, má SMS spoustu funkcí a výhod, které vám pomůžou zajistit plynulé dokončení migrace.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Výhody použití nástroje k přenosu dat offline
Tady jsou hlavní výhody použití nástroje pro přenos, jako je Data Box pro offline migraci:

- Nemusíte nahrávat všechny soubory přes síť. Pro velké obory názvů může tento nástroj ušetřit značnou šířku pásma sítě a čas.
- Pokud používáte Azure File Sync bez ohledu na to, který nástroj pro přenos používáte (Data Box, službu Azure import/export a tak dále), Live Server nahraje pouze soubory, které se po přesunu dat do Azure mění.
- Azure File Sync synchronizuje seznamy řízení přístupu k souborům a složkám i v případě, že offline nástroj pro hromadné migraci nepřenosuje seznamy ACL.
- Data Box a Azure File Sync nevyžadují žádné výpadky. Když použijete Data Box k přenosu dat do Azure, použijete efektivně šířku pásma sítě a zachováte věrnost souborů. Svůj obor názvů můžete také udržovat v aktuálním stavu tím, že nahrajete pouze soubory, které se po přesunu dat do Azure změní.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Předpoklady pro přenos offline dat
Před dokončením offline přenosu dat byste neměli na serveru, který migrujete, povolit synchronizaci. Další věci, které je potřeba vzít v úvahu před zahájením, jsou následující:

- Pokud plánujete použít Data Box pro hromadnou migraci: Přečtěte si [požadavky nasazení pro data box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Plánování finální topologie Azure File Sync: [Plánování nasazení Azure File Sync](storage-sync-files-planning.md)
- Vyberte účty úložiště Azure, ve kterých budou uloženy sdílené složky, se kterými chcete synchronizovat. Ujistěte se, že se Hromadná migrace stane dočasnými pracovními podíly ve stejných účtech úložiště. Hromadná migrace se dá povolit jenom s využitím finální a pracovní sdílené složky, která se nachází ve stejném účtu úložiště.
- Hromadná migrace se dá využít jenom při vytváření nové relace synchronizace s umístěním na serveru. Nemůžete povolit hromadnou migraci s existující relací synchronizace.


## <a name="process-for-offline-data-transfer"></a>Postup pro přenos offline dat
Tady je postup nastavení Azure File Sync tak, aby byl kompatibilní s nástroji pro hromadnou migraci, jako je například Azure Data Box:

![Diagram znázorňující, jak nastavit Azure File Sync](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Krok | Podrobnosti |
|---|---------------------------------------------------------------------------------------|
| ![Krok 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Seřazení data box](../../databox/data-box-deploy-ordered.md). Řada Data Box nabízí [několik produktů](https://azure.microsoft.com/services/storage/databox/data) , které vyhovují vašim potřebám. Po přijetí Data Box postupujte podle příslušné [dokumentace a zkopírujte data](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) do této cesty UNC v Data Box: *\\<DeviceIPAddres \> \<StorageAccountName_AzFile\> \<ShareName\>*. Tady je *název sdílené pracovní* složky. Odešlete Data Box zpět do Azure. |
| ![Krok 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Počkejte, než se soubory zobrazí ve sdílených složkách Azure, které jste zvolili jako dočasné pracovní sdílené složky. *Nepovolujte synchronizaci s těmito sdílenými složkami.* |
| ![Krok 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | <ul><li>Pro každou sdílenou složku, kterou Data Box vytvořili, vytvořte novou prázdnou sdílenou složku. Tato nová sdílená složka by měla být ve stejném účtu úložiště jako sdílená složka Data Box. [Jak vytvořit novou sdílenou složku Azure](storage-how-to-create-file-share.md)</li><li>[Vytvořte skupinu synchronizace](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) ve službě synchronizace úložiště. Odkázat na prázdnou sdílenou složku jako koncový bod cloudu. Tento krok opakujte pro každou Data Box sdílenou složku. [Nastavte Azure File Sync](storage-sync-files-deployment-guide.md).</li></ul> |
| ![Krok 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Přidejte adresář živého serveru jako koncový bod serveru](storage-sync-files-deployment-guide.md#create-a-server-endpoint). V tomto procesu Určete, že jste přesunuli soubory do Azure a odkázat na pracovní sdílené složky. Můžete povolit nebo zakázat vrstvení cloudu podle potřeby. Při vytváření koncového bodu serveru na svém aktivním serveru se odkázat na pracovní sdílenou složku. V okně **přidat koncový bod serveru** v části **offline přenos dat** vyberte **povoleno** a potom vyberte pracovní sdílenou složku, která musí být ve stejném účtu úložiště jako koncový bod cloudu. Tady je seznam dostupných sdílených složek filtrovaný účtem úložiště a sdílenými složkami, které ještě nejsou synchronizované. Snímek obrazovky následující v této tabulce ukazuje, jak odkazovat na sdílenou složku DataBox během vytváření koncového bodu serveru v Azure Portal. |
| ![Krok 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | Po přidání koncového bodu serveru v předchozím kroku se data spustí automaticky z správného zdroje. Část [synchronizace sdílené složky](#syncing-the-share) vysvětluje, kdy toky dat buď ze sdílené složky Databox, nebo z Windows serveru. |
| |

![Snímek obrazovky Azure Portal uživatelského rozhraní, který ukazuje, jak povolit offline přenos dat při vytváření nového koncového bodu serveru](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Synchronizuje se sdílená složka
Po vytvoření koncového bodu serveru se synchronizace spustí. Proces synchronizace Určuje, jestli každý soubor na serveru existuje i v pracovní sdílené složce, kde Data Box soubory uložily. Pokud soubor existuje, proces synchronizace zkopíruje soubor z pracovní sdílené složky a nebude ho nahrávat ze serveru. Pokud soubor v pracovní sdílené složce neexistuje nebo pokud je v místním serveru k dispozici novější verze, proces synchronizace soubor nahraje z místního serveru.

Při synchronizaci sdílené složky synchronizace sloučí všechny chybějící atributy souborů, oprávnění nebo časová razítka z variant souborů na místním serveru a zkombinuje je se svými protějšky souborů ze sdílené složky DataBox. Tím se zajistí, že každý soubor a složka dorazí ve sdílené složce Azure na všechny možné věrnosti souborů.

> [!IMPORTANT]
> Režim hromadné migrace můžete povolit jenom při vytváření koncového bodu serveru. Po zřízení koncového bodu serveru nemůžete integrovat hromadně migrovaná data z již synchronizovaného serveru do oboru názvů.

## <a name="acls-and-timestamps-on-files-and-folders"></a>Seznamy řízení přístupu a časová razítka pro soubory a složky
Azure File Sync zajistí, aby byly seznamy řízení přístupu k souborům a složkám synchronizované z živého serveru i v případě, že jste použili nástroj pro hromadné migraci, který jste použili při počátečním přenosu ACL Proto pracovní sdílená složka nemusí obsahovat žádné seznamy ACL pro soubory a složky. Když při vytváření nového koncového bodu serveru povolíte funkci offline migrace dat, všechny seznamy ACL souborů se synchronizují na serveru. Synchronizována jsou také nově vytvořená a upravená časová razítka.

## <a name="shape-of-the-namespace"></a>Tvar oboru názvů
Pokud povolíte synchronizaci, obsah serveru určí tvar oboru názvů. Pokud se soubory po dokončení Data Box snímku a migrace odstraní z místního serveru, nepřesunou se tyto soubory do aktivního a synchronizovaného oboru názvů. Zůstanou v pracovní sdílené složce, ale nekopírují se. To je nezbytné, protože synchronizace udržuje obor názvů podle živého serveru. *Snímek* data box je pouze pracovní uzemnění pro efektivní kopírování souborů. Není to autorita pro tvar živého oboru názvů.

## <a name="cleaning-up-after-bulk-migration"></a>Vyčištění po hromadné migraci 
Protože server dokončí počáteční synchronizaci oboru názvů, Data Box hromadně migrované soubory používají pracovní sdílenou složku. V okně **Vlastnosti koncového bodu serveru** v Azure Portal v části **offline přenos dat** se stav změní z **probíhá** na **dokončeno**. 

![Snímek obrazovky okna vlastností koncového bodu serveru, kde se nachází stav a zakázat ovládací prvky pro přenos offline dat](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Nyní můžete vyčistit pracovní sdílenou složku a ušetřit tak náklady:

1. V okně **Vlastnosti koncového bodu serveru** , když je stav **dokončený**, vyberte **Zakázat offline přenos dat**.
2. Zvažte odstranění pracovní sdílené složky za účelem úspory nákladů. Pracovní sdílená složka pravděpodobně neobsahuje seznamy řízení přístupu k souborům a složkám, takže není pravděpodobně užitečná. Pro účely zálohování v čase vytvořte skutečný [snímek synchronizace sdílené složky Azure](storage-snapshots-files.md). Můžete [nastavit Azure Backup pro pořizování snímků]( ../../backup/backup-afs.md) podle plánu.

Režim offline přenosu dat zakažte pouze v případě, že je stav **dokončen** nebo pokud chcete operaci zrušit z důvodu neplatného nastavení. Pokud režim během nasazování zakážete, začnou se soubory nahrávat ze serveru i v případě, že je vaše pracovní sdílená složka stále k dispozici.

> [!IMPORTANT]
> Když režim offline přenosu dat zakážete, nebudete ho moct znovu povolit, i když je stále dostupná pracovní sdílená složka z hromadné migrace.

## <a name="azure-file-sync-and-pre-seeded-files-in-the-cloud"></a>Azure File Sync a předem vysazených souborů v cloudu

Pokud ve sdílené složce Azure máte osazené soubory jiným způsobem než DataBox – například prostřednictvím AzCopy, Robocopy ze zálohy v cloudu nebo jakékoli jiné metody – pak byste měli postupovat i [Offline přenos dat proces](#process-for-offline-data-transfer) popsaný v tomto článku. DataBox budete muset jenom odvažovat za metodu, kterou soubory přesunete do cloudu. Je ale nejdůležitější, abyste se ujistili, že nadále probíhá proces osazení souborů do *pracovní sdílené složky* a nikoli do konečné, Azure File Sync připojené sdílené složky.

> [!WARNING]
> **Postupujte podle postupu osazení souborů do pracovní sdílené složky, nikoli z konečné**, Azure File Sync připojené sdílené složky. Pokud to neuděláte, může dojít ke konfliktům souborů (obě verze souborů budou uložené) i soubory odstraněné na živém serveru se můžou vrátit, pokud pořád existují ve starším, naočkované sadě souborů. Kromě toho se změny složek sloučí mezi sebou, takže je velmi obtížné oddělit obor názvů po takovém případě omylem.

## <a name="next-steps"></a>Další kroky
- [Plánování nasazení Azure File Sync](storage-sync-files-planning.md)
- [Nasazení Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)