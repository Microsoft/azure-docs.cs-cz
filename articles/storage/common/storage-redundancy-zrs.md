---
title: Vytváření aplikací s vysokou dostupností s využitím redundantního úložiště v zóně (ZRS)
titleSuffix: Azure Storage
description: Zóna – redundantní úložiště (ZRS) nabízí jednoduchý způsob, jak vytvářet vysoce dostupné aplikace. ZRS chrání před selháním hardwaru v datacentru a před některými oblastními katastrofami.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 7d341c7081fef7aee2c33b9a7080d60417ce410d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895181"
---
# <a name="build-highly-available-applications-with-zone-redundant-storage-zrs"></a>Vytváření aplikací s vysokou dostupností s využitím redundantního úložiště v zóně (ZRS)

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Pokrytí podpory a regionální dostupnost

ZRS aktuálně podporuje typy účtů úložiště standard pro obecné účely v2, úložiště úložiště a BlockBlobStorage. Další informace o typech účtů úložiště najdete v tématu [Přehled účtu Azure Storage](storage-account-overview.md).

Účty pro obecné účely v2 ZRS podporují objekty blob bloku, objekty blob stránky mimo disk, standardní sdílené složky, tabulky a fronty.

Pro účty pro obecné účely v2 je ZRS všeobecně dostupná v následujících oblastech:

- Jihovýchodní Asie
- Evropa – sever
- Evropa – západ
- Francie – střed
- Japonsko – východ
- Velká Británie – jih
- USA – střed
- USA – východ
- USA – východ 2
- USA – západ 2

Pro účty úložiště souborů (prémiové sdílené složky) a účty BlockBlobStorage (rozhraní blob bloku úrovně Premium) je ZRS všeobecně k dispozici v těchto oblastech:

- Evropa – západ
- USA – východ

Microsoft nadále povoluje ZRS v dalších oblastech Azure. Další informace o nových oblastech najdete na stránce [aktualizace služby Azure](https://azure.microsoft.com/updates/) .

**Známá omezení**

- Úroveň archivu se v účtech ZRS aktuálně nepodporuje. Další podrobnosti najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň přístupu](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) .
- Spravované disky nepodporují ZRS. Můžete ukládat snímky a obrázky pro SSD úrovně Standard Managed Disks na HDD úrovně Standard úložiště a [volit mezi možnostmi LRS a ZRS](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Co se stane, když se zóna stane nedostupnou?

Vaše data jsou stále přístupná pro operace čtení i zápisu i v případě, že zóna nebude k dispozici. Společnost Microsoft doporučuje, abyste dál dodržovali postupy pro zpracování přechodných chyb. Tyto postupy zahrnují implementaci zásad opakování pomocí exponenciálního zálohování.

Když je zóna nedostupná, Azure si převezme síťové aktualizace, jako je třeba nasměrování DNS. Tyto aktualizace můžou ovlivnit vaši aplikaci, pokud k datům přistupujete ještě před dokončením aktualizací.

ZRS nemusí chránit vaše data před regionální havárií, při které je trvale ovlivněno více zón. Místo toho ZRS nabízí odolnost za vaše data, pokud bude dočasně nedostupná. Microsoft doporučuje používat geograficky redundantní úložiště (GRS), aby se mohla chránit před místními katastrofami. Další informace o GRS najdete v tématu [geograficky redundantní úložiště (GRS): replikace mezi různými oblastmi pro Azure Storage](storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Převádění na replikaci ZRS

Migrace na LRS, GRS a RA-GRS je jednoduchá. Pro změnu typu redundance účtu použijte rozhraní API pro Azure Portal nebo poskytovatele prostředků úložiště. Azure následně replikuje vaše data. 

Migrace dat do ZRS vyžaduje jinou strategii. Migrace ZRS zahrnuje fyzické přesuny dat z jediného úložného razítka do několika razítek v rámci oblasti.

Existují dvě primární možnosti migrace na ZRS: 

- Můžete ručně zkopírovat nebo přesunout data ze stávajícího účtu do nového účtu zónově redundantního úložiště.
- Můžete požádat o migraci za chodu.

> [!IMPORTANT]
> Migrace za provozu není v současné době pro sdílené složky Premium podporována. V tuto chvíli se podporuje jenom ruční kopírování a přesouvání dat.

Pokud potřebujete, aby se migrace dokončila do určitého data, zvažte provedení ruční migrace. Ruční migrace poskytuje větší flexibilitu než migrace za chodu. Při ruční migraci máte kontrolu nad načasováním.

K provedení ruční migrace máte tyto možnosti:
- Používejte existující nástroje, jako je AzCopy, jednu z Azure Storage klientských knihoven nebo spolehlivé nástroje třetích stran.
- Pokud jste obeznámeni se systémem Hadoop nebo HDInsight, připojte zdrojový a cílový účet (ZRS) k vašemu clusteru. Pak paralelizovat proces kopírování dat pomocí nástroje, jako je DistCp.
- Vytvářejte vlastní nástroje pomocí jedné z Azure Storage klientských knihoven.

Ruční migrace může vést k výpadkům aplikací. Pokud vaše aplikace vyžaduje vysokou dostupnost, Microsoft nabízí také možnost migrace za chodu. Migrace za chodu je místní migrace bez výpadku. 

Během migrace za chodu můžete používat svůj účet úložiště, zatímco se vaše data migrují mezi razítky zdrojového a cílového úložiště. Během procesu migrace máte k dispozici stejnou úroveň odolnosti a smlouvu SLA o dostupnosti jako obvykle.

Při migraci za provozu Pamatujte na následující omezení:

- Přestože Microsoft zpracuje vaši žádost o migraci za chodu téměř okamžitě, neposkytuje žádné záruky ohledně toho, kdy se migrace za chodu dokončí. Pokud potřebujete data migrovat do zónově redundantního úložiště do určitého data, Microsoft místo toho doporučuje provést ruční migraci. Obecně platí, že čím více dat v účtu máte, dím déle trvá jejich migrace. 
- Migrace za provozu je podporovaná jenom pro účty úložiště, které používají replikaci LRS nebo GRS. Pokud váš účet používá RA-GRS, musíte nejdřív před pokračováním změnit typ replikace svého účtu na LRS nebo GRS. Tento zprostředkující krok odstraní sekundární koncový bod jen pro čtení, který poskytuje RA-GRS před migrací.
- Váš účet musí obsahovat data.
- Data můžete migrovat jenom v rámci stejné oblasti. Pokud chcete migrovat data do účtu ZRS umístěného v jiné oblasti, než je zdrojový účet, musíte provést ruční migraci.
- Migrace za provozu podporují jenom standardní typy účtů úložiště. Účty úložiště úrovně Premium se musí migrovat ručně.
- Migrace za provozu z ZRS na LRS, GRS nebo RA-GRS se nepodporuje. Data budete muset ručně přesunout do nového nebo stávajícího účtu úložiště.
- Spravované disky jsou dostupné jenom pro LRS a nedají se migrovat na ZRS. Můžete ukládat snímky a obrázky pro SSD úrovně Standard Managed Disks na HDD úrovně Standard úložiště a [volit mezi možnostmi LRS a ZRS](https://azure.microsoft.com/pricing/details/managed-disks/). Integrace se skupinami dostupnosti najdete v tématu [Úvod do služby Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).
- Účty LRS nebo GRS s archivní daty nelze migrovat na ZRS.

Migraci za provozu si můžete vyžádat prostřednictvím [portálu podpory Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Na portálu vyberte účet úložiště, který chcete převést na ZRS.
1. Vybrat **novou žádost o podporu**
2. Dokončete **základy** na základě informací o účtu. V části **Služba** vyberte **Správa účtu úložiště** a prostředek, který chcete převést na ZRS. 
3. Vyberte **Další**. 
4. V části **problém** zadejte následující hodnoty: 
    - **Závažnost**: ponechte výchozí hodnotu tak, jak je.
    - **Typ problému**: vyberte **migrace dat**.
    - **Kategorie**: vyberte **migrovat do ZRS**.
    - **Title**: zadejte popisný název, například **migrace účtu ZRS**.
    - **Podrobnosti**: v poli **podrobností** zadejte další podrobnosti, například chci migrovat na ZRS z [LRS, GRS] v oblasti \_\_. 
5. Vyberte **Další**.
6. Ověřte, že kontaktní informace jsou správné v okně **kontaktní údaje** .
7. Vyberte **Create** (Vytvořit).

Pracovník podpory vás bude kontaktovat a poskytne vám pomoc, kterou potřebujete.

## <a name="live-migration-to-zrs-faq"></a>Nejčastější dotazy k migraci za provozu na ZRS

**Mám během migrace naplánovat nějaké výpadky?**

Migrace nepředstavuje žádný výpadek. Během migrace za provozu můžete pokračovat v používání účtu úložiště během migrace dat mezi zdrojovým a cílovým úložným razítkem. Během procesu migrace máte k dispozici stejnou úroveň odolnosti a smlouvu SLA o dostupnosti jako obvykle.

**Je k migraci k dispozici nějaká ztráta dat?**

K migraci není přidružena žádná ztráta dat. Během procesu migrace máte k dispozici stejnou úroveň odolnosti a smlouvu SLA o dostupnosti jako obvykle.

**Vyžadují aplikace po dokončení migrace nějaké aktualizace?**

Po dokončení migrace se typ replikace účtů změní na úložiště "zóna – redundantní úložiště (ZRS)". Koncové body služby, přístupové klíče, SAS a jakékoli další možnosti konfigurace účtu zůstávají beze změny a nedotčeny.

**Můžu si vyžádat migraci za provozu mých účtů pro obecné účely V1 na ZRS?**

ZRS podporuje jenom účty pro obecné účely v2, takže před odesláním žádosti o migraci za provozu do ZRS se ujistěte, že upgradujete vaše účty na obecné účely v2. Další podrobnosti najdete v tématu [Přehled účtu Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview) a [upgrade na účet úložiště pro obecné účely v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) .

**Můžu si vyžádat migraci z účtu geograficky redundantního úložiště s přístupem pro čtení (RA-GRS) z provozu do ZRS?**

Před odesláním žádosti o migraci za provozu do ZRS ověřte, že vaše aplikace nebo úlohy už nevyžadují přístup k sekundárnímu koncovému bodu jen pro čtení, a změňte typ replikace vašich účtů úložiště na geograficky redundantní úložiště (GRS). Další podrobnosti najdete v tématu [Změna strategie replikace](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy) .

**Můžu pro ZRS do jiné oblasti požádat o migraci účtů úložiště za provozu?**

Pokud chcete migrovat data do účtu ZRS, který se nachází v jiné oblasti než v oblasti zdrojového účtu, musíte provést ruční migraci.

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS Classic: možnost starší verze pro redundanci objektů blob bloku
> [!NOTE]
> Společnost Microsoft bude zastaralá a migrovat ZRS Classic na 31. března 2021. Další podrobnosti budou k dispozici pro zákazníky ZRS Classic před vyřazením. 
>
> Jakmile se ZRS bude [všeobecně dostupné](#support-coverage-and-regional-availability) v oblasti, zákazníci nebudou moct vytvářet účty ZRS Classic z portálu v této oblasti. Použití prostředí Microsoft PowerShell a rozhraní příkazového řádku Azure k vytvoření klasických účtů ZRS je možnost, dokud se ZRS Classic nepoužívá.

ZRS Classic asynchronně replikuje data napříč datovými centry v jedné nebo dvou oblastech. Replikovaná data nemusí být k dispozici, pokud společnost Microsoft nespustí převzetí služeb při selhání sekundárním systémem. Účet ZRS Classic se nedá převést na LRS, GRS nebo RA-GRS. Účty ZRS Classic také nepodporují metriky ani protokolování.

ZRS Classic je k dispozici pouze pro **objekty blob bloku** v účtech úložiště pro obecné účely V1 (GPv1). Další informace o účtech úložiště najdete v [přehledu účtu úložiště Azure](storage-account-overview.md).

Pokud chcete data účtu ZRS ručně migrovat do nebo z účtu LRS, ZRS Classic, GRS nebo RA-GRS, použijte jeden z následujících nástrojů: AzCopy, Průzkumník služby Azure Storage, Azure PowerShell nebo Azure CLI. Můžete také vytvořit vlastní řešení migrace pomocí jedné z Azure Storage klientských knihoven.

Můžete také upgradovat účty ZRS Classic na ZRS na portálu nebo pomocí Azure PowerShell nebo rozhraní příkazového řádku Azure v oblastech, kde je ZRS k dispozici. Pokud chcete upgradovat na ZRS v Azure Portal, přejděte do části **Konfigurace** účtu a vyberte **upgradovat**:

![Upgrade ZRS Classic na ZRS na portálu](media/storage-redundancy-zrs/portal-zrs-classic-upgrade.png)

Pokud chcete upgradovat na ZRS pomocí prostředí PowerShell, použijte následující příkaz:
```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

Pokud chcete upgradovat na ZRS pomocí rozhraní příkazového řádku, použijte následující příkaz:
```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

## <a name="see-also"></a>Další informace najdete v tématech
- [Účet replikace Azure Storage](storage-redundancy.md)
- [Místně redundantní úložiště (LRS): redundance dat s nízkými náklady pro Azure Storage](storage-redundancy-lrs.md)
- [Geograficky redundantní úložiště (GRS): replikace mezi různými oblastmi pro Azure Storage](storage-redundancy-grs.md)
