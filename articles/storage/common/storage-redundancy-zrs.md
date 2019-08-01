---
title: Vytváření aplikací s vysokou dostupností Azure Storage v zóně – redundantní úložiště (ZRS) | Microsoft Docs
description: Zóna – redundantní úložiště (ZRS) nabízí jednoduchý způsob, jak vytvářet vysoce dostupné aplikace. ZRS chrání před selháním hardwaru v datacentru a před některými oblastními katastrofami.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/24/2018
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 5fefe469bfac4816a67c6ceb344f12c1e52de60c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68550454"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>Redundantní úložiště zóny (ZRS): Vysoce dostupné Azure Storage aplikace
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Pokrytí podpory a regionální dostupnost
ZRS aktuálně podporuje standardní typy účtů pro obecné účely verze 2. Další informace o typech účtů úložiště najdete v tématu [Přehled účtu Azure Storage](storage-account-overview.md).

ZRS je k dispozici pro objekty blob bloku, objekty blob stránky mimo disk, soubory, tabulky a fronty.

ZRS je všeobecně dostupná v následujících oblastech:

- Jihovýchodní Asie
- Evropa – západ
- Evropa – sever
- Francie – střed
- Japonsko – východ
- Velká Británie – jih
- USA – střed
- USA – východ
- USA – východ 2
- USA – západ 2

Microsoft nadále povoluje ZRS v dalších oblastech Azure. Další informace o nových oblastech najdete na stránce [aktualizace služby Azure](https://azure.microsoft.com/updates/) .

**Známá omezení**

- Úroveň archivu se v účtech ZRS aktuálně nepodporuje. Další podrobnosti najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň přístupu](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) .
- Spravované disky nepodporují ZRS. Můžete ukládat snímky a obrázky pro SSD úrovně Standard Managed Disks na HDD úrovně Standard úložiště a [volit mezi možnostmi LRS a ZRS](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Co se stane, když se zóna stane nedostupnou?
Vaše data jsou stále přístupná pro operace čtení i zápisu i v případě, že zóna nebude k dispozici. Společnost Microsoft doporučuje, abyste dál dodržovali postupy pro zpracování přechodných chyb. Tyto postupy zahrnují implementaci zásad opakování pomocí exponenciálního zálohování.

Když je zóna nedostupná, Azure si převezme síťové aktualizace, jako je třeba nasměrování DNS. Tyto aktualizace můžou ovlivnit vaši aplikaci, pokud k datům přistupujete ještě před dokončením aktualizací.

ZRS nemusí chránit vaše data před regionální havárií, při které je trvale ovlivněno více zón. Místo toho ZRS nabízí odolnost za vaše data, pokud bude dočasně nedostupná. Microsoft doporučuje používat geograficky redundantní úložiště (GRS), aby se mohla chránit před místními katastrofami. Další informace o GRS najdete v tématu [geograficky redundantní úložiště (GRS): Replikace mezi různými oblastmi pro](storage-redundancy-grs.md)Azure Storage.

## <a name="converting-to-zrs-replication"></a>Převádění na replikaci ZRS
Migrace na LRS, GRS a RA-GRS je jednoduchá. Pro změnu typu redundance účtu použijte rozhraní API pro Azure Portal nebo poskytovatele prostředků úložiště. Azure následně replikuje vaše data. 

Migrace dat do ZRS vyžaduje jinou strategii. Migrace ZRS zahrnuje fyzické přesuny dat z jediného úložného razítka do několika razítek v rámci oblasti.

Existují dvě primární možnosti migrace na ZRS: 

- Ručně zkopírujte nebo přesuňte data do nového účtu ZRS z existujícího účtu.
- Požádejte o migraci za provozu.

Microsoft důrazně doporučuje provést ruční migraci. Ruční migrace poskytuje větší flexibilitu než migrace za provozu. Při ruční migraci budete řídit časování.

K provedení ruční migrace máte tyto možnosti:
- Používejte existující nástroje, jako je AzCopy, jednu z Azure Storage klientských knihoven nebo spolehlivé nástroje třetích stran.
- Pokud jste obeznámeni se systémem Hadoop nebo HDInsight, připojte zdrojový a cílový účet (ZRS) k vašemu clusteru. Pak paralelizovat proces kopírování dat pomocí nástroje, jako je DistCp.
- Vytvářejte vlastní nástroje pomocí jedné z Azure Storage klientských knihoven.

Ruční migrace může vést k výpadkům aplikací. Pokud vaše aplikace vyžaduje vysokou dostupnost, nabízí Microsoft taky možnost migrace za provozu. Migrace za provozu je místní migrace. 

Během migrace za provozu můžete použít svůj účet úložiště, zatímco vaše data jsou migrována mezi zdrojovým a cílovým úložným razítkem. Během procesu migrace máte k dispozici stejnou úroveň platnosti smlouvy SLA a dostupnost.

Při migraci za provozu Pamatujte na následující omezení:

- Přestože Microsoft zpracuje vaši žádost o migraci za chodu téměř okamžitě, neposkytuje žádné záruky ohledně toho, kdy se migrace za chodu dokončí. Pokud budete potřebovat data migrovat do ZRS do určitého data, společnost Microsoft doporučuje místo toho provést ruční migraci. Obecně platí, že čím více dat v účtu máte, dím déle trvá jejich migrace. 
- Migrace za provozu je podporovaná jenom pro účty úložiště, které používají replikaci LRS nebo GRS. Pokud váš účet používá RA-GRS, musíte nejdřív před pokračováním změnit typ replikace svého účtu na LRS nebo GRS. Tento zprostředkující krok odstraní sekundární koncový bod jen pro čtení, který poskytuje RA-GRS před migrací.
- Váš účet musí obsahovat data.
- Data můžete migrovat jenom v rámci stejné oblasti. Pokud chcete migrovat data do účtu ZRS umístěného v jiné oblasti, než je zdrojový účet, musíte provést ruční migraci.
- Migrace za provozu podporují jenom standardní typy účtů úložiště. Účty úložiště úrovně Premium se musí migrovat ručně.
- Migrace za provozu z ZRS na LRS, GRS nebo RA-GRS se nepodporuje. Data bude nutné ručně přesunout do nového nebo existujícího účtu úložiště.
- Spravované disky jsou dostupné jenom pro LRS a nedají se migrovat na ZRS. Můžete ukládat snímky a obrázky pro SSD úrovně Standard Managed Disks na HDD úrovně Standard úložiště a [volit mezi možnostmi LRS a ZRS](https://azure.microsoft.com/pricing/details/managed-disks/). Integrace se skupinami dostupnosti najdete v tématu [Úvod do služby Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).
- Účty LRS nebo GRS s archivní daty nelze migrovat na ZRS.

Migraci za provozu si můžete vyžádat prostřednictvím [portálu podpory Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Na portálu vyberte účet úložiště, který chcete převést na ZRS.
1. Vybrat **novou žádost o podporu**
2. Dokončete **základy** na základě informací o účtu. V části **Služba** vyberte **Správa účtu úložiště** a prostředek, který chcete převést na ZRS. 
3. Vyberte **Další**. 
4. V části **problém** zadejte následující hodnoty: 
    - **Závažnost**: Ponechte výchozí hodnotu tak, jak je.
    - **Typ problému**: Vyberte **migrace dat**.
    - **Kategorie**: Vyberte možnost **migrovat do ZRS v rámci oblasti**.
    - **Název**: Zadejte popisný název, například **migrace účtu ZRS**.
    - **Podrobnosti**: V poli **podrobností** zadejte další podrobnosti, například přejete si migrovat na ZRS z [LRS, GRS] v \_ \_ oblasti. 
5. Vyberte **Další**.
6. Ověřte, že kontaktní informace jsou správné v okně **kontaktní údaje** .
7. Vyberte **Vytvořit**.

Pracovník podpory vás bude kontaktovat a poskytne vám pomoc, kterou potřebujete.

## <a name="live-migration-to-zrs-faq"></a>Nejčastější dotazy k migraci za provozu na ZRS

**Mám během migrace naplánovat nějaké výpadky?**

Migrace nepředstavuje žádný výpadek. Během migrace za provozu můžete pokračovat v používání účtu úložiště během migrace dat mezi zdrojovým a cílovým úložným razítkem. Během procesu migrace máte k dispozici stejnou úroveň platnosti smlouvy SLA a dostupnost.

**Je k migraci k dispozici nějaká ztráta dat?**

K migraci není přidružena žádná ztráta dat. Během procesu migrace máte k dispozici stejnou úroveň platnosti smlouvy SLA a dostupnost.

**Vyžadují aplikace po dokončení migrace nějaké aktualizace?**

Po dokončení migrace se typ replikace účtů změní na úložiště "zóna – redundantní úložiště (ZRS)". Koncové body služby, přístupové klíče, SAS a jakékoli další možnosti konfigurace účtu zůstávají beze změny a nedotčeny.

**Můžu si vyžádat migraci za provozu mých účtů pro obecné účely V1 na ZRS?**

ZRS podporuje jenom účty pro obecné účely v2, takže před odesláním žádosti o migraci za provozu do ZRS se ujistěte, že upgradujete vaše účty na obecné účely v2. Další podrobnosti najdete v tématu [Přehled účtu Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview) a [upgrade na účet úložiště pro obecné účely v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) .

**Můžu si vyžádat migraci z účtu geograficky redundantního úložiště s přístupem pro čtení (RA-GRS) z provozu do ZRS?**

Před odesláním žádosti o migraci za provozu do ZRS ověřte, že vaše aplikace nebo úlohy už nevyžadují přístup k sekundárnímu koncovému bodu jen pro čtení, a změňte typ replikace vašich účtů úložiště na geograficky redundantní úložiště (GRS). Další podrobnosti najdete v tématu [Změna strategie replikace](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy) .

**Můžu pro ZRS do jiné oblasti požádat o migraci účtů úložiště za provozu?**

Pokud chcete migrovat data do účtu ZRS, který se nachází v jiné oblasti než v oblasti zdrojového účtu, musíte provést ruční migraci.

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS Classic: Možnost starší verze pro redundanci objektů blob bloku
> [!NOTE]
> Společnost Microsoft bude zastaralá a migrovat ZRS Classic na 31. března 2021. Další podrobnosti budou k dispozici pro zákazníky ZRS Classic před vyřazením. 
>
> Jakmile se ZRS bude [všeobecně dostupné](#support-coverage-and-regional-availability) v oblasti, zákazníci nebudou moct vytvářet účty ZRS Classic z portálu v této oblasti. Použití prostředí Microsoft PowerShell a rozhraní příkazového řádku Azure k vytvoření klasických účtů ZRS je možnost, dokud se ZRS Classic nepoužívá.

ZRS Classic asynchronně replikuje data napříč datovými centry v jedné nebo dvou oblastech. Replikovaná data nemusí být k dispozici, pokud společnost Microsoft nespustí převzetí služeb při selhání sekundárním systémem. Účet ZRS Classic se nedá převést na LRS, GRS nebo RA-GRS. Účty ZRS Classic také nepodporují metriky ani protokolování.

ZRS Classic je k dispozici pouze pro **objekty blob bloku** v účtech úložiště pro obecné účely V1 (GPv1). Další informace o účtech úložiště najdete v [přehledu účtu Azure Storage](storage-account-overview.md).

Pokud chcete data účtu ZRS ručně migrovat do nebo z účtu LRS, ZRS Classic, GRS nebo RA-GRS, použijte jeden z následujících nástrojů: AzCopy, Průzkumník služby Azure Storage, Azure PowerShell nebo Azure CLI. Můžete také vytvořit vlastní řešení migrace pomocí jedné z Azure Storage klientských knihoven.

Můžete také upgradovat účty ZRS Classic na ZRS na portálu nebo pomocí Azure PowerShell nebo rozhraní příkazového řádku Azure v oblastech, kde je ZRS k dispozici.

Pokud chcete upgradovat na ZRS na portálu, přejít do části Konfigurace účtu a vyberte upgradovat:![Upgrade ZRS Classic na ZRS na portálu](media/storage-redundancy-zrs/portal-zrs-classic-upgrade.jpg)

Pokud chcete upgradovat na ZRS pomocí prostředí PowerShell, použijte následující příkaz:
```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

Pokud chcete upgradovat na ZRS pomocí rozhraní příkazového řádku, použijte následující příkaz:
```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

## <a name="see-also"></a>Viz také:
- [Účet replikace Azure Storage](storage-redundancy.md)
- [Místně redundantní úložiště (LRS): Redundance dat pro Azure Storage s nízkou cenou](storage-redundancy-lrs.md)
- [Geograficky redundantní úložiště (GRS): Replikace mezi různými oblastmi pro Azure Storage](storage-redundancy-grs.md)
