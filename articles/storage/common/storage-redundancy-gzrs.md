---
title: Vytváření vysoce dostupných Azure Storage aplikací s geograficky redundantním úložištěm (GZRS) (Preview) | Microsoft Docs
description: Geograficky redundantní úložiště (GZRS) manželství vysoké dostupnosti zóny redundantního úložiště (ZRS) s ochranou z oblasti výpadků poskytované geograficky redundantním úložištěm (GRS). Data v účtu úložiště GZRS se replikují v rámci zón dostupnosti Azure v primární oblasti a také se replikují do sekundární geografické oblasti pro ochranu z regionálních katastrof.
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 99def93a20a365dd0ff5fc27e9c52909ee30bd83
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028130"
---
# <a name="build-highly-available-azure-storage-applications-with-geo-zone-redundant-storage-gzrs-preview"></a>Vytváření vysoce dostupných Azure Storage aplikací s geograficky redundantním úložištěm (GZRS) (Preview)

Geograficky redundantní úložiště (GZRS) (Preview) manželství vysoké dostupnosti [zóny redundantního úložiště (ZRS)](storage-redundancy-zrs.md) s ochranou z oblasti výpadků poskytované [geograficky redundantním úložištěm (GRS)](storage-redundancy-grs.md). Data v účtu úložiště GZRS se replikují mezi tři [zóny dostupnosti Azure](../../availability-zones/az-overview.md) v primární oblasti a také se replikují do sekundární geografické oblasti pro ochranu z regionálních katastrof. Každá oblast Azure je spárovaná s jinou oblastí v rámci stejné geografické oblasti a tvoří tak místní dvojici. Další podrobnosti a výjimky najdete v [dokumentaci](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

S účtem úložiště GZRS můžete dál číst a zapisovat data, pokud se zóna dostupnosti stane nedostupnou nebo nejde obnovit. Kromě toho jsou vaše data také odolná v případě kompletního oblasti výpadku nebo havárie, ve které není primární oblast obnovitelné. GZRS je navržený tak, aby poskytoval alespoň 99.99999999999999% (16 9) odolnosti objektů v průběhu daného roku. GZRS také nabízí stejné [cíle škálovatelnosti](storage-scalability-targets.md) jako LRS, ZRS, GRS nebo RA-GRS. Volitelně můžete povolit přístup pro čtení k datům v sekundární oblasti pomocí geograficky redundantního úložiště s přístupem pro čtení (RA-GZRS), pokud vaše aplikace musí být schopné číst data v případě havárie v primární oblasti.

Microsoft doporučuje používat GZRS pro aplikace, které vyžadují konzistenci, odolnost, vysokou dostupnost, vynikající výkon a odolnost proti zotavení po havárii. Pro zvýšení zabezpečení přístupu pro čtení do sekundární oblasti v případě regionální havárie povolte RA-GZRS pro váš účet úložiště.

## <a name="about-the-preview"></a>O verzi Preview

GZRS a RA-GZRS podporují jenom účty úložiště pro obecné účely verze 2. Další informace o typech účtů úložiště najdete v tématu [Přehled účtu Azure Storage](storage-account-overview.md). GZRS a RA-GZRS podporují objekty blob bloku, objekty blob stránky, které nejsou disky VHD, soubory, tabulky a fronty.

GZRS a RA-GZRS jsou aktuálně k dispozici pro verzi Preview v následujících oblastech:

- Evropa – sever
- Evropa – západ
- USA – východ
- USA – východ 2
- USA – střed

Microsoft nadále povoluje GZRS a RA-GZRS v dalších oblastech Azure. Další informace o podporovaných oblastech najdete v části [aktualizace služby Azure](https://azure.microsoft.com/updates/)@no__t – 1page.

Informace o cenách verze Preview najdete v tématu ceny GZRS ve verzi Preview pro [objekty blob](https://azure.microsoft.com/pricing/details/storage/blobs), [soubory](https://azure.microsoft.com/pricing/details/storage/files/), [fronty](https://azure.microsoft.com/pricing/details/storage/queues/)a [tabulky](https://azure.microsoft.com/pricing/details/storage/tables/).

> [!IMPORTANT]
> Microsoft doporučuje použití funkcí verze Preview pro produkční úlohy.

## <a name="how-gzrs-and-ra-gzrs-work"></a>Jak fungují GZRS a RA-GZRS

Když se data zapisují do účtu úložiště s povoleným GZRS nebo RA-GZRS, budou se tato data nejdřív replikovat synchronně v primární oblasti ve třech zónách dostupnosti. Data se pak asynchronně replikují do druhé oblasti, která je od sebe stovky kilometrů. Když jsou data zapsána do sekundární oblasti, je v rámci této oblasti dále replikována s použitím [místně redundantního úložiště (LRS)](storage-redundancy-lrs.md).

> [!IMPORTANT]
> Asynchronní replikace zahrnuje zpoždění mezi časem zápisu dat do primární oblasti a při replikaci do sekundární oblasti. V případě regionálních havárií může dojít ke ztrátě změn, které ještě nebyly replikovány do sekundární oblasti, pokud tato data nebude možné obnovit z primární oblasti.

Při vytváření účtu úložiště určíte, jak budou data v tomto účtu replikována, a také určíte primární oblast pro tento účet. Spárovaná sekundární oblast pro geograficky replikovaný účet je určena v závislosti na primární oblasti a nelze ji změnit. Aktuální informace o oblastech podporovaných v Azure najdete v tématu [provozní kontinuita a zotavení po havárii (BCDR): spárované oblasti Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Informace o vytvoření účtu úložiště pomocí GZRS nebo RA-GZRS najdete v tématu [Vytvoření účtu úložiště](storage-quickstart-create-account.md).

### <a name="use-ra-gzrs-for-high-availability"></a>Použití RA-GZRS pro vysokou dostupnost

Když pro svůj účet úložiště povolíte RA-GZRS, můžete data číst ze sekundárního koncového bodu i z primárního koncového bodu pro váš účet úložiště. Sekundární koncový bod připojí příponu *– sekundární* k názvu účtu. Pokud je například primárním koncovým bodem pro Blob service hodnota @ no__t-0, pak je sekundární koncový bod @ no__t-1. Přístupové klíče pro váš účet úložiště jsou u primárních i sekundárních koncových bodů stejné.

Pokud chcete využít výhod RA-GZRS v případě regionálního výpadku, musíte aplikaci navrhnout předem, abyste mohli tento scénář zpracovat. Vaše aplikace by měla číst a zapisovat do primárního koncového bodu, ale v případě, že primární oblast nebude k dispozici, přepnout na použití sekundárního koncového bodu. Pokyny k navrhování vysoké dostupnosti pomocí RA-GZRS najdete v tématu [navrhování vysoce dostupných aplikací pomocí RA-GZRS nebo RA-GRS](https://docs.microsoft.com/azure/storage/common/storage-designing-ha-apps-with-ragrs).

Vzhledem k tomu, že data jsou replikována do sekundární oblasti asynchronně, Sekundární oblast je často za primární oblastí. Chcete-li zjistit, které operace zápisu byly replikovány do sekundární oblasti, aplikace kontroluje čas poslední synchronizace účtu úložiště. Všechny operace zápisu zapsané do primární oblasti před poslední dobou synchronizace byly úspěšně replikovány do sekundární oblasti, což znamená, že je možné je načíst ze sekundárního umístění. Všechny operace zápisu zapsané do primární oblasti po poslední době synchronizace mohou nebo nemusí být replikovány do sekundární oblasti, což znamená, že nemusí být k dispozici pro operace čtení.

Můžete zadat dotaz na hodnotu vlastnosti **čas poslední synchronizace** pomocí Azure PowerShell, Azure CLI nebo některé z klientských knihoven Azure Storage. Vlastnost **čas poslední synchronizace** je hodnota data a času GMT.

Další pokyny týkající se výkonu a škálovatelnosti pomocí RA-GZRS najdete v [Microsoft Azure Storage kontrolní seznam pro výkon a škálovatelnost](storage-performance-checklist.md).

### <a name="availability-zone-outages"></a>Výpadky zóny dostupnosti

V případě selhání ovlivňujícího zónu dostupnosti v primární oblasti můžou vaše aplikace bez problémů číst a zapisovat do svého účtu úložiště pomocí dalších zón dostupnosti pro tuto oblast. Společnost Microsoft doporučuje, abyste při použití GZRS nebo ZRS dál dodržovali postupy pro zpracování přechodných chyb. Tyto postupy zahrnují implementaci zásad opakování pomocí exponenciálního zálohování.

Když je zóna dostupnosti nedostupná, Azure si neuvolní síťové aktualizace, jako je třeba přesměrování DNS. Tyto aktualizace můžou ovlivnit vaši aplikaci, pokud k datům přistupujete ještě před dokončením aktualizací.

### <a name="regional-outages"></a>Regionální výpadky

Pokud při selhání dojde k ovlivnění celé primární oblasti, Microsoft se nejdřív pokusí obnovit primární oblast. Pokud není možné obnovení, pak Microsoft převezme služby při selhání sekundární oblastí, aby se sekundární oblast stala novou primární oblastí. Pokud má účet úložiště povolenou možnost RA-GZRS, aplikace navržené pro tento scénář můžou číst ze sekundární oblasti při čekání na převzetí služeb při selhání. Pokud účet úložiště nemá povolené RA-GZRS, aplikace se nebudou moct číst ze sekundárního nástroje, dokud se převzetí služeb při selhání nedokončí.

> [!NOTE]
> GZRS a RA-GZRS jsou momentálně ve verzi Preview jenom v USA – východ oblasti. Převzetí služeb při selhání účtu spravovaného zákazníkem (Preview) ještě není v USA – východ 2 dostupné, takže zákazníci teď nemůžou spravovat události převzetí služeb při selhání účtu s GZRS a RA-GZRS účty. V průběhu verze Preview bude společnost Microsoft spravovat všechny události převzetí služeb při selhání, které mají vliv na účty GZRS a RA-GZRS.

Vzhledem k tomu, že data jsou replikována do sekundární oblasti asynchronně, selhání ovlivňující primární oblast může způsobit ztrátu dat, pokud nelze obnovit primární oblast. Interval mezi nejnovějšími zápisy do primární oblasti a posledním zápisem do sekundární oblasti se označuje jako cíl bodu obnovení (RPO). RPO označuje bod v čase, do kterého lze obnovit data. Azure Storage obvykle má RPO méně než 15 minut, ale v současné době není k dispozici žádná smlouva SLA, jak dlouho trvá replikace dat do sekundární oblasti.

Doba obnovení (RTO) je mírou, jak dlouho trvá provedení převzetí služeb při selhání a získání účtu úložiště zpět do režimu online. Tato míra indikuje dobu potřebnou pro Azure k provedení převzetí služeb při selhání změnou primárních záznamů DNS tak, aby odkazovaly na sekundární umístění.

## <a name="migrate-a-storage-account-to-gzrs-or-ra-gzrs"></a>Migrace účtu úložiště do GZRS nebo RA-GZRS

Existující účet úložiště můžete migrovat na GZRS nebo RA-GZRS. Migrace z existujícího účtu ZRS na GZRS nebo RA-GZRS je jednoduchá a při migraci z účtu LRS, GRS nebo RA-GRS se zapojí. Následující části popisují, jak provést migraci v obou případech.

### <a name="migrating-from-a-zrs-account"></a>Migrace z účtu ZRS

Pokud chcete převést existující účet ZRS na RA-GZRS, změňte SKU účtu pomocí rutiny [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) . Nezapomeňte nahradit hodnoty zástupných symbolů vlastními hodnotami:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -SkuName "Standard_RAGZRS"
```

### <a name="migrating-from-an-lrs-grs-or-ra-grs-account"></a>Migrace z účtu LRS, GRS nebo RA-GRS

Existují dvě možnosti migrace na GZRS nebo RA-GZRS z účtu LRS, GRS nebo RA-GRS:

- Můžete ručně zkopírovat nebo přesunout data do nového účtu GZRS nebo RA-GZRS z existujícího účtu.
- Můžete si vyžádat migraci za provozu.

#### <a name="perform-a-manual-migration"></a>Provedení ruční migrace

Pokud potřebujete migraci dokončit do určitého data, zvažte provedení ruční migrace. Ruční migrace poskytuje větší flexibilitu než migrace za chodu. Při ruční migraci máte kontrolu nad načasováním.

Pokud chcete ručně migrovat data z existujícího účtu na účet GZRS nebo RA-GZRS, použijte nástroj, který umožňuje efektivně kopírovat data. Možné příklady:

- Použijte nástroj, jako je AzCopy nebo spolehlivý nástroj třetí strany. Informace o AzCopy najdete v tématu Začínáme [s AzCopy](storage-use-azcopy-v10.md).
- Pokud jste obeznámeni se systémem Hadoop nebo HDInsight, připojte zdrojové i cílové účty úložiště k vašemu clusteru. Potom paralelizovat proces kopírování dat pomocí nástroje, jako je DistCp.
- Vytvářejte vlastní nástroje pomocí jedné z Azure Storage klientských knihoven.

#### <a name="perform-a-live-migration"></a>Provedení migrace za provozu

Ruční migrace může vést k výpadkům aplikací. Pokud vaše aplikace vyžaduje vysokou dostupnost, Microsoft nabízí také možnost migrace za chodu. Migrace za chodu je místní migrace bez výpadku.

Během migrace za provozu můžete použít svůj účet úložiště, zatímco vaše data jsou migrována mezi zdrojovým a cílovým účtem úložiště. Během procesu migrace za provozu váš účet stále splňuje podmínky smlouvy SLA pro zajištění odolnosti a dostupnosti. Nedochází k výpadkům nebo ztrátě dat způsobené migrací za provozu.

Jenom účty pro obecné účely v2 podporují GZRS/RA-GZRS, takže před odesláním žádosti o migraci za provozu do GZRS/RA-GZRS je potřeba upgradovat svůj účet na obecné účely v2. Další informace najdete v tématu [Přehled účtu Azure storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview)@no__t 1and [upgrade na účet úložiště pro obecné účely v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).

Po dokončení migrace se nastavení replikace účtu úložiště aktualizuje na **geograficky redundantní úložiště (GZRS)** nebo **geograficky redundantní úložiště s přístupem pro čtení (RA-GZRS)** . Koncové body služby, přístupové klíče, sdílené přístupové podpisy (SAS) a jakékoli další možnosti konfigurace účtu zůstávají beze změny a nedotčeny.

Při migraci za provozu Pamatujte na následující omezení:

- Přestože Microsoft zpracuje vaši žádost o migraci za chodu téměř okamžitě, neposkytuje žádné záruky ohledně toho, kdy se migrace za chodu dokončí. Pokud potřebujete svá data migrovat do GZRS nebo RA-GZRS do určitého data, Microsoft doporučuje, abyste místo toho prováděli ruční migraci. Obecně platí, že čím více dat v účtu máte, dím déle trvá jejich migrace.
- Váš účet musí obsahovat data.
- Data můžete migrovat jenom v rámci stejné oblasti.
- Migrace za provozu podporují jenom standardní typy účtů úložiště. Účty úložiště úrovně Premium se musí migrovat ručně.
- Migrace za provozu z účtu GZRS nebo RA-GZRS na účet LRS, GRS nebo RA-GRS se nepodporuje. Data bude nutné ručně přesunout do nového nebo existujícího účtu úložiště.
- Můžete si vyžádat migraci za provozu z RA-GRS do RA-GZRS. Migrace z RA-GRS na GZRS se ale nepodporuje. V takovém případě musíte požádat o migraci za provozu do RA-GZRS a pak ručně převést účet úložiště tak, aby používal GZRS.
- Spravované disky podporují jenom LRS a nedají se migrovat do GZRS nebo RA-GZRS. Informace o integraci se skupinami dostupnosti najdete v tématu [Úvod do služby Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).
- Můžete ukládat snímky a obrázky pro SSD úrovně Standard Managed Disks na HDD úrovně Standard úložiště a [volit mezi možnostmi LRS, ZRS, GZRS a RA-GZRS](https://azure.microsoft.com/pricing/details/managed-disks/).
- Účty obsahující velké sdílené složky se pro GZRS nepodporují.

K vyžádání migrace za provozu použijte [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Na portálu vyberte účet úložiště, který se má migrovat na GZRS nebo RA-GZRS, a postupujte podle těchto pokynů:

1. Vyberte **novou žádost o podporu**.
2. Dokončete **základy** na základě informací o účtu. V části **Služba** vyberte **Správa účtu úložiště** a zadejte účet, který se má migrovat.
3. Vyberte **Další**.
4. V části **problém** zadejte následující hodnoty:
    - **Závažnost**: ponechte výchozí hodnotu tak, jak je.
    - **Typ problému**: vyberte **migrace dat**.
    - **Kategorie**: vyberte **MIGROVAT do (RA-) GZRS v rámci oblasti**.
    - **Title**: zadejte popisný název, například **(RA-) GZRS Account Migration**.
    - **Podrobnosti**: v poli **podrobností** zadejte další podrobnosti, například "Chci migrovat na GZRS z [LRS, GRS] v oblasti \_ @ no__t-3." nebo "Chci migrovat na RA-GZRS z [LRS, RA-GRS] v oblasti \_ @ no__t-1."
5. Vyberte **Další**.
6. Ověřte, že kontaktní informace jsou správné v okně **kontaktní údaje** .
7. Vyberte **Create** (Vytvořit).

Zástupce podpory vás bude kontaktovat, abyste mohli poskytnout pomoc.

## <a name="see-also"></a>Další informace najdete v tématech

- [Účet replikace Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
- [Místně redundantní úložiště (LRS): redundance dat s nízkými náklady pro Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)
- [Zóna – redundantní úložiště (ZRS): vysoce dostupné aplikace Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) 
