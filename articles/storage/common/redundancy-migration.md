---
title: Změna způsobu replikace účtu úložiště
titleSuffix: Azure Storage
description: Naučte se, jak změnit způsob replikace dat v existujícím účtu úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/24/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 5f570f13fd39bd25b37c35a2c823e64eaa02fef5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91295392"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Změna způsobu replikace účtu úložiště

Azure Storage vždycky ukládá více kopií vašich dat, takže je chráněná před plánovanými a neplánovanými událostmi, včetně přechodných selhání hardwaru, sítě nebo výpadků napájení a obrovských přirozených havárií. Redundance zajišťuje, že váš účet úložiště splňuje [smlouvu o úrovni služeb (SLA) pro Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/) i v případě selhání.

Azure Storage nabízí následující typy replikace:

- Místně redundantní úložiště (LRS)
- Zónově redundantní úložiště (ZRS)
- Geograficky redundantní úložiště (GRS) nebo geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)
- Geograficky redundantní úložiště (GZRS) nebo geograficky redundantní úložiště s přístupem pro čtení (RA-GZRS)

Přehled každé z těchto možností najdete v tématu [Azure Storage redundance](storage-redundancy.md).

## <a name="switch-between-types-of-replication"></a>Přepínání mezi typy replikace

Můžete přepnout účet úložiště z jednoho typu replikace na jiný typ, ale některé scénáře jsou jednodušší než jiné. Pokud chcete přidat nebo odebrat geografickou replikaci nebo přístup pro čtení do sekundární oblasti, můžete nastavení replikace aktualizovat pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure. Pokud ale chcete změnit způsob replikace dat v primární oblasti, přesunutím z LRS na ZRS nebo naopak, musíte provést ruční migraci.

Následující tabulka poskytuje přehled toho, jak přepínat mezi jednotlivými typy replikace na jiný:

| Přepínání | ... do LRS | ... na GRS/RA – GRS | ... do ZRS | ... na GZRS/RA – GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... z LRS</b> | Není k dispozici | Pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku můžete změnit nastavení replikace<sup>1</sup> . | Provedení ruční migrace <br /><br /> NEBO <br /><br /> Vyžádání migrace za provozu | Provedení ruční migrace <br /><br /> NEBO <br /><br /> Nejprve přepněte na GRS/RA-GRS a pak požádejte o migraci za provozu<sup>1</sup> . |
| <b>... z GRS/RA – GRS</b> | Nastavení replikace změníte pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku. | Není k dispozici | Provedení ruční migrace <br /><br /> NEBO <br /><br /> Nejprve přepněte na LRS a pak požádejte o migraci za provozu. | Provedení ruční migrace <br /><br /> NEBO <br /><br /> Vyžádání migrace za provozu |
| <b>... z ZRS</b> | Provedení ruční migrace | Provedení ruční migrace | Není k dispozici | Pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku můžete změnit nastavení replikace<sup>1, 2</sup> . |
| <b>... z GZRS/RA – GZRS</b> | Provedení ruční migrace | Provedení ruční migrace | Nastavení replikace změníte pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku. | Není k dispozici |

<sup>1</sup> dojde k jednorázovému výstupnímu poplatku.<br />
<sup>2</sup> převod z ZRS na GZRS/RA-GZRS nebo naopak není podporován v následujících oblastech: USA – východ 2, USA – východ, Evropa – západ.

> [!CAUTION]
> Pokud jste provedli [převzetí služeb při selhání u účtu](storage-disaster-recovery-guidance.md) (RA-) GRS nebo (RA-) GZRS, účet se po převzetí služeb při selhání místně redundantní v nové primární oblasti. Migrace za provozu na ZRS nebo GZRS pro účet LRS, který vyplývají z převzetí služeb při selhání, se nepodporuje. To platí i v případě, že se říká operace navrácení služeb po obnovení. Pokud třeba provedete převzetí služeb při selhání z RA-GZRS do LRS v sekundární oblasti a pak ji znovu nakonfigurujete na RA-GRS a provedete jiné převzetí služeb při selhání do původní primární oblasti, nebudete moct kontaktovat podporu pro původní migraci za provozu do RA-GZRS v primární oblasti. Místo toho budete muset provést ruční migraci na ZRS nebo GZRS.

## <a name="change-the-replication-setting"></a>Změna nastavení replikace

Pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure můžete změnit nastavení replikace pro účet úložiště, pokud neměníte způsob, jakým se data replikují v primární oblasti. Pokud migrujete z LRS v primární oblasti na ZRS v primární oblasti nebo naopak, musíte provést ruční migraci nebo migraci za provozu.

Změna způsobu replikace účtu úložiště nevede k nezměnění času pro vaše aplikace.

# <a name="portal"></a>[Azure Portal](#tab/portal)

Pokud chcete pro svůj účet úložiště v Azure Portal změnit možnost redundance, postupujte takto:

1. Na webu Azure Portal přejděte na svůj účet úložiště.
1. Vyberte nastavení **Konfigurace** .
1. Aktualizujte nastavení **replikace** .

![Snímek obrazovky, který ukazuje, jak změnit možnost replikace na portálu](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete změnit možnost redundance pro svůj účet úložiště pomocí PowerShellu, zavolejte příkaz [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) a zadejte `-SkuName` parametr:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete pro svůj účet úložiště v Azure CLI změnit možnost redundance, zavolejte příkaz [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) a zadejte `--sku` parametr:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs-gzrs-or-ra-gzrs"></a>Provedení ruční migrace do ZRS, GZRS nebo RA-GZRS

Pokud chcete změnit způsob, jakým se data v účtu úložiště replikují v primární oblasti, přemístěte z LRS na ZRS nebo naopak a pak se můžete rozhodnout provést ruční migraci. Ruční migrace poskytuje větší flexibilitu než migrace za chodu. Můžete řídit časování ruční migrace, takže tuto možnost použijte, pokud potřebujete migraci dokončit do určitého data.

Když provedete ruční migraci z LRS do ZRS v primární oblasti nebo naopak, cílový účet úložiště může být geograficky redundantní a dá se taky nakonfigurovat pro přístup pro čtení do sekundární oblasti. Například můžete migrovat účet LRS do účtu GZRS nebo RA-GZRS v jednom kroku.

Ruční migrace může vést k výpadkům aplikací. Pokud vaše aplikace vyžaduje vysokou dostupnost, Microsoft nabízí také možnost migrace za chodu. Migrace za chodu je místní migrace bez výpadku.

Při ruční migraci kopírujete data z existujícího účtu úložiště do nového účtu úložiště, který v primární oblasti používá ZRS. K provedení ruční migrace můžete použít jednu z následujících možností:

- Kopírování dat pomocí existujícího nástroje, jako je AzCopy, jedna z klientských knihoven Azure Storage, nebo spolehlivého nástroje třetí strany.
- Pokud jste obeznámeni se systémem Hadoop nebo HDInsight, můžete ke svému clusteru připojit účet zdrojového úložiště i cílový účet úložiště. Pak paralelizovat proces kopírování dat pomocí nástroje, jako je DistCp.

## <a name="request-a-live-migration-to-zrs-gzrs-or-ra-gzrs"></a>Vyžádání migrace za provozu na ZRS, GZRS nebo RA-GZRS

Pokud potřebujete migrovat účet úložiště z LRS na ZRS v primární oblasti bez výpadků aplikací, můžete požádat o migraci za provozu od Microsoftu. Pokud chcete migrovat z LRS na GZRS nebo RA-GZRS, nejdřív přepněte na GRS nebo RA-GRS a pak požádejte o migraci za provozu. Podobně můžete požádat o migraci za provozu z GRS nebo RA-GRS na GZRS nebo RA-GZRS. Pokud chcete migrovat z GRS nebo RA-GRS na ZRS, nejdřív přepněte na LRS a pak požádejte o migraci za provozu.

Během migrace za provozu máte přístup k datům ve vašem účtu úložiště bez ztráty odolnosti nebo dostupnosti. Azure Storage smlouva SLA se udržuje během procesu migrace. Při migraci za provozu nedochází k žádné ztrátě dat. Koncové body služby, přístupové klíče, signatury sdíleného přístupu a další možnosti účtu zůstávají po migraci beze změny.

ZRS podporuje jenom účty pro obecné účely v2, takže před odesláním žádosti o migraci za provozu do ZRS nezapomeňte upgradovat svůj účet úložiště. Další informace najdete v tématu [upgrade na účet úložiště pro obecné účely v2](storage-account-upgrade.md). Účet úložiště musí obsahovat data, která se mají migrovat prostřednictvím migrace za provozu.

Migrace za provozu je podporovaná jenom pro účty úložiště, které používají replikaci LRS nebo GRS. Pokud váš účet používá RA-GRS, musíte nejdřív před pokračováním změnit typ replikace svého účtu na LRS nebo GRS. Tento zprostředkující krok odstraní sekundární koncový bod jen pro čtení, který poskytuje RA-GRS před migrací.

Přestože Microsoft zpracuje vaši žádost o migraci za chodu téměř okamžitě, neposkytuje žádné záruky ohledně toho, kdy se migrace za chodu dokončí. Pokud potřebujete data migrovat do zónově redundantního úložiště do určitého data, Microsoft místo toho doporučuje provést ruční migraci. Obecně platí, že čím více dat v účtu máte, dím déle trvá jejich migrace.

Ruční migraci je nutné provést v těchto případech:

- Chcete migrovat data do účtu úložiště ZRS, který se nachází v jiné oblasti než zdrojový účet.
- Váš účet úložiště je objekt blob stránky úrovně Premium nebo účet objektu blob bloku.
- Chcete migrovat data z ZRS na LRS, GRS nebo RA-GRS.
- Váš účet úložiště zahrnuje data z archivní úrovně.

Migraci za provozu si můžete vyžádat prostřednictvím [portálu podpory Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Na portálu vyberte účet úložiště, který chcete převést na ZRS.

1. Vybrat **novou žádost o podporu**
2. Dokončete **základy** na základě informací o účtu. V části **Služba** vyberte **Správa účtu úložiště** a prostředek, který chcete převést na ZRS.
3. Vyberte **Další**.
4. V části **problém** zadejte následující hodnoty:
    - **Závažnost**: ponechte výchozí hodnotu tak, jak je.
    - **Typ problému**: vyberte **migrace dat**.
    - **Kategorie**: vyberte **migrovat do ZRS**.
    - **Title**: zadejte popisný název, například **migrace účtu ZRS**.
    - **Podrobnosti**: v poli **podrobností** zadejte další podrobnosti, například přejete si migrovat na ZRS z [LRS, GRS] v \_ \_ oblasti.
5. Vyberte **Další**.
6. Ověřte, že kontaktní informace jsou správné v okně **kontaktní údaje** .
7. Vyberte **Vytvořit**.

Pracovník podpory vás bude kontaktovat a poskytne vám pomoc, kterou potřebujete.

> [!NOTE]
> Migrace za provozu není v současné době pro sdílené složky Premium podporována. V tuto chvíli se podporuje jenom ruční kopírování a přesouvání dat.
>
> Účty úložiště GZRS momentálně nepodporují archivní vrstvu. Další podrobnosti najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň přístupu](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) .
>
> Spravované disky jsou dostupné jenom pro LRS a nedají se migrovat na ZRS. Můžete ukládat snímky a image pro standardní spravované disky SSD na úrovni Standard HDD a [volit mezi LRS a ZRS možností](https://azure.microsoft.com/pricing/details/managed-disks/). Informace o integraci se skupinami dostupnosti najdete v tématu [Seznámení se službou Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).

## <a name="switch-from-zrs-classic"></a>Přepnutí z ZRS Classic

> [!IMPORTANT]
> Společnost Microsoft bude zastaralá a migrovat ZRS Classic na 31. března 2021. Další podrobnosti budou k dispozici pro zákazníky ZRS Classic před vyřazením.
>
> Jakmile se ZRS stane všeobecně dostupným v dané oblasti, zákazníci už nebudou moct vytvářet účty ZRS Classic z Azure Portal v této oblasti. Použití prostředí Microsoft PowerShell a rozhraní příkazového řádku Azure k vytvoření klasických účtů ZRS je možnost, dokud se ZRS Classic nepoužívá. Informace o tom, kde je ZRS k dispozici, najdete v tématu [Azure Storage redundance](storage-redundancy.md).

ZRS Classic asynchronně replikuje data napříč datovými centry v jedné nebo dvou oblastech. Replikovaná data nemusí být k dispozici, pokud společnost Microsoft nespustí převzetí služeb při selhání sekundárním systémem. Účet ZRS Classic se nedá převést na LRS, GRS nebo RA-GRS. Účty ZRS Classic také nepodporují metriky ani protokolování.

ZRS Classic je k dispozici pouze pro **objekty blob bloku** v účtech úložiště pro obecné účely V1 (GPv1). Další informace o účtech úložiště najdete v tématu [Přehled účtu Azure Storage](storage-account-overview.md).

Pokud chcete data účtu ZRS ručně migrovat do nebo z klasického účtu LRS, GRS, RA-GRS nebo ZRS, použijte jeden z následujících nástrojů: AzCopy, Průzkumník služby Azure Storage, PowerShell nebo Azure CLI. Můžete také vytvořit vlastní řešení migrace pomocí jedné z Azure Storage klientských knihoven.

Účet úložiště ZRS Classic můžete také upgradovat na ZRS pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure CLI v oblastech, kde je k dispozici ZRS.

# <a name="portal"></a>[Azure Portal](#tab/portal)

Pokud chcete upgradovat na ZRS v Azure Portal, přejděte do nastavení **Konfigurace** účtu a vyberte **upgradovat**:

![Upgrade ZRS Classic na ZRS na portálu](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete upgradovat na ZRS pomocí PowerShellu, zavolejte tento příkaz:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete upgradovat na ZRS pomocí rozhraní příkazového řádku Azure, zavolejte následující příkaz:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Náklady spojené se změnou způsobu replikace dat

Náklady spojené se změnou způsobu replikace dat závisí na vaší cestě převodu. Řazení z nejméně na nejlevnější, Azure Storage nabídky redundance, zahrnuje LRS, ZRS, GRS, RA-GRS, GZRS a RA-GZRS.

Například *z* LRS na jakýkoli jiný typ replikace budou účtovány další poplatky, protože přecházíte na propracovanější úroveň redundance. Migrace *na* GRS nebo RA-GRS se bude účtovat jako výstupní šířka pásma, protože vaše data (v primární oblasti) se replikují do vzdálené sekundární oblasti. Tento poplatek je jednorázová cena při počátečním nastavení. Po zkopírování dat se neúčtují žádné další poplatky za migraci. Podrobnosti o cenách za šířku pásma najdete na [stránce s cenami Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Při migraci účtu úložiště z GRS do LRS se neúčtují žádné další náklady, ale replikovaná data se odstraní ze sekundárního umístění.

> [!IMPORTANT]
> Pokud migrujete účet úložiště z RA-GRS na GRS nebo LRS, tento účet se fakturuje jako RA-GRS po dobu dalších 30 dnů od data, kdy se převedlo.

## <a name="see-also"></a>Viz také

- [Azure Storage redundance](storage-redundancy.md)
- [Podívejte se na vlastnost čas poslední synchronizace pro účet úložiště.](last-sync-time-get.md)
- [Použití geografické redundance k návrhu vysoce dostupných aplikací](geo-redundant-design.md)
