---
title: Změna způsobu replikace účtu úložiště
titleSuffix: Azure Storage
description: Přečtěte si, jak změnit způsob replikace dat v existujícím účtu úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 5c37dbdc34138faab8adae6ad18252c18a75cad4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337078"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Změna způsobu replikace účtu úložiště

Azure Storage vždy ukládá více kopií vašich dat tak, aby byla chráněna před plánovanými a neplánovanými událostmi, včetně přechodných selhání hardwaru, výpadků sítě nebo napájení a rozsáhlých přírodních katastrof. Redundance zajišťuje, že váš účet úložiště splňuje [smlouvu o úrovni služeb (SLA) pro Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/) i tváří v tvář selhání.

Azure Storage nabízí následující typy replikace:

- Místně redundantní úložiště (LRS)
- Zónově redundantní úložiště (ZRS)
- Geograficky redundantní úložiště (GRS) nebo geograficky redundantní úložiště pro čtení (RA-GRS)
- Geograficky redundantní úložiště (GZRS) nebo geograficky redundantní úložiště pro čtení (RA-GZRS) (preview)

Přehled jednotlivých možností najdete v článku [redundance úložiště Azure](storage-redundancy.md).

## <a name="switch-between-types-of-replication"></a>Přepínání mezi typy replikace

Účet úložiště můžete přepnout z jednoho typu replikace na jiný typ, ale některé scénáře jsou jednodušší než jiné. Pokud chcete přidat nebo odebrat geografickou replikaci nebo přístup pro čtení do sekundární oblasti, můžete k aktualizaci nastavení replikace použít portál Azure, PowerShell nebo Azure CLI. Pokud však chcete změnit způsob replikace dat v primární oblasti přesunutím z LRS na ZRS nebo naopak, je nutné provést ruční migraci.

Následující tabulka obsahuje přehled, jak přepnout z každého typu replikace na jiný:

| Přepínání | ... do LRS | ... do GRS/RA-GRS | ... do ZRS | ... do GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... od LRS</b> | Není dostupné. | Změna nastavení replikace<sup>1</sup> pomocí portálu Azure, PowerShellu nebo cli | Ruční migrace <br /><br />Žádost o migraci za provozu | Ruční migrace <br /><br /> NEBO <br /><br /> Nejprve přepněte na GRS/RA-GRS a poté požádejte o migraci za provozu<sup>1</sup> |
| <b>... od GRS/RA-GRS</b> | Změna nastavení replikace pomocí portálu Azure, PowerShellu nebo cli | Není dostupné. | Ruční migrace <br /><br /> NEBO <br /><br /> Nejprve přepněte na LRS a potom požádejte o migraci za provozu | Ruční migrace <br /><br /> Žádost o migraci za provozu |
| <b>... od ZRS</b> | Ruční migrace | Ruční migrace | Není dostupné. | Změna nastavení replikace<sup>1</sup> pomocí portálu Azure, PowerShellu nebo cli |
| <b>... od GZRS/RA-GZRS</b> | Ruční migrace | Ruční migrace | Změna nastavení replikace pomocí portálu Azure, PowerShellu nebo cli | Není dostupné. |

<sup>1</sup> Účtuje jednorázový poplatek za odchozí přenos.

> [!CAUTION]
> Pokud jste provedli [převzetí služeb při selhání účtu](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance) pro váš účet (RA-)GRS nebo (RA-)GZRS, je nakonfigurován tak, aby byl místně redundantní v nové primární oblasti. Migrace za provozu na ZRS nebo GZRS pro tyto účty LRS není podporována. Budete muset provést [ruční migraci](https://docs.microsoft.com/azure/storage/common/redundancy-migration#perform-a-manual-migration-to-zrs).

## <a name="change-the-replication-setting"></a>Změna nastavení replikace

Pomocí portálu Azure, PowerShellu nebo Azure CLI můžete změnit nastavení replikace pro účet úložiště, pokud nezměníte způsob replikace dat v primární oblasti. Pokud migrujete z LRS v primární oblasti do ZRS v primární oblasti nebo naopak, musíte provést [ruční migraci](#perform-a-manual-migration-to-zrs) nebo [migraci za provozu](#request-a-live-migration-to-zrs).

Změna způsobu replikace účtu úložiště nevede k prostojů aplikací.

# <a name="portal"></a>[Portál](#tab/portal)

Pokud chcete změnit možnost redundance pro váš účet úložiště na webu Azure Portal, postupujte takto:

1. Na webu Azure Portal přejděte na svůj účet úložiště.
1. Vyberte nastavení **Konfigurace.**
1. Aktualizujte nastavení **Replikace.**

![Snímek obrazovky znázorňující, jak změnit možnost replikace na portálu](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Chcete-li změnit možnost redundance pro váš účet úložiště pomocí prostředí PowerShell, `-SkuName` zavolejte příkaz [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) a zadejte parametr:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete změnit možnost redundance pro váš účet úložiště pomocí rozhraní příkazového `--sku` řádku Azure, zavolejte příkaz aktualizace účtu úložiště [a](/cli/azure/storage/account#az-storage-account-update) zadejte parametr:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs"></a>Ruční migrace do ZRS

Pokud chcete změnit způsob replikace dat v účtu úložiště v primární oblasti přesunutím z LRS na ZRS nebo naopak, můžete se rozhodnout provést ruční migraci. Ruční migrace poskytuje větší flexibilitu než migrace za chodu. Můžete řídit časování ruční migrace, takže tuto možnost použijte, pokud potřebujete migraci dokončit do určitého data.

Při provádění ruční migrace z LRS do ZRS v primární oblasti nebo naopak, cílový účet úložiště může být geograficky redundantní a může být také nakonfigurován pro přístup pro čtení do sekundární oblasti. Můžete například migrovat účet LRS na účet GZRS nebo RA-GZRS v jednom kroku.

Ruční migrace může způsobit prostoje aplikace. Pokud vaše aplikace vyžaduje vysokou dostupnost, Microsoft nabízí také možnost migrace za chodu. Migrace za chodu je místní migrace bez výpadku.

Při ruční migraci zkopírujete data z existujícího účtu úložiště do nového účtu úložiště, který používá ZRS v primární oblasti. Ruční migraci můžete provést pomocí jedné z následujících možností:

- Zkopírujte data pomocí existujícího nástroje, jako je AzCopy, jedna z klientských knihoven Azure Storage nebo spolehlivý nástroj třetí strany.
- Pokud jste obeznámeni s Hadoop nebo HDInsight, můžete připojit účet zdrojového úložiště a účet cílového úložiště ke svému clusteru. Potom paralelizovat proces kopírování dat s nástrojem, jako je DistCp.

## <a name="request-a-live-migration-to-zrs"></a>Žádost o migraci za provozu do ZRS

Pokud potřebujete migrovat účet úložiště z LRS nebo GRS do ZRS v primární oblasti bez prostojů aplikací, můžete požádat o migraci za provozu od společnosti Microsoft. Během migrace za provozu můžete přistupovat k datům ve svém účtu úložiště a bez ztráty odolnosti nebo dostupnosti. SLA úložiště Azure se udržuje během procesu migrace. Neexistuje žádná ztráta dat spojená s migrací za provozu. Koncové body služby, přístupové klíče, podpisy sdíleného přístupu a další možnosti účtu zůstanou po migraci nezměněny.

ZRS podporuje pouze účty pro obecné účely v2, takže nezapomeňte upgradovat účet úložiště před odesláním žádosti o migraci za provozu do ZRS. Další informace naleznete v [tématu Upgrade na účet úložiště pro obecné účely v2](storage-account-upgrade.md). Účet úložiště musí obsahovat data, která mají být migrována prostřednictvím migrace za provozu.

Migrace za provozu je podporována pouze pro účty úložiště, které používají replikaci LRS nebo GRS. Pokud váš účet používá RA-GRS, musíte nejprve změnit typ replikace účtu na LRS nebo GRS, než budete pokračovat. Tento zprostředkující krok odebere sekundární koncový bod jen pro čtení, který poskytuje RA-GRS před migrací.

Přestože Microsoft zpracuje vaši žádost o migraci za chodu téměř okamžitě, neposkytuje žádné záruky ohledně toho, kdy se migrace za chodu dokončí. Pokud potřebujete data migrovat do zónově redundantního úložiště do určitého data, Microsoft místo toho doporučuje provést ruční migraci. Obecně platí, že čím více dat v účtu máte, dím déle trvá jejich migrace.

Ruční migraci je nutné provést, pokud:

- Chcete migrovat data do účtu úložiště ZRS, který se nachází v jiné oblasti než zdrojový účet.
- Váš účet úložiště je účet objektů blob stránky premium nebo blok blob.
- Chcete migrovat data z ZRS do LRS, GRS nebo RA-GRS.
- Váš účet úložiště obsahuje data v archivní vrstvě.

Můžete požádat o migraci za provozu prostřednictvím [portálu podpory Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Na portálu vyberte účet úložiště, který chcete převést na ZRS.

1. Vybrat **novou žádost o podporu**
2. Vyplňte **základy** na základě informací o vašem účtu. V části **Služba** vyberte **Správa účtu úložiště** a prostředek, který chcete převést na ZRS.
3. Vyberte **další**.
4. Zadejte následující hodnoty v části **Problém:**
    - **Závažnost**: Výchozí hodnotu ponechejte tak, jak je.
    - **Typ problému**: Vyberte **migraci dat**.
    - **Kategorie**: Vyberte **migrovat na ZRS**.
    - **Název**: Zadejte popisný název, například **migrace účtu ZRS**.
    - **Podrobnosti**: Do pole **Podrobnosti** zadejte další podrobnosti, například bych chtěl migrovat \_ \_ na ZRS z [LRS, GRS] v regionu.
5. Vyberte **další**.
6. Ověřte, zda jsou kontaktní informace v okně **Kontaktní informace** správné.
7. Vyberte **Vytvořit**.

Osoba podpory vás bude kontaktovat a poskytne vám jakoukoli pomoc, kterou potřebujete.

> [!NOTE]
> Migrace za provozu není aktuálně podporována pro sdílené složky premium. V současné době je podporováno pouze ruční kopírování nebo přesouvání dat.
>
> Účty úložiště GZRS aktuálně nepodporují úroveň archivu. Další informace najdete v [tématu Azure Blob storage: hot, cool a archive access tiers](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) for more details.
>
> Spravované disky jsou k dispozici pouze pro LRS a nelze je migrovat do zrs. Snímky a obrázky pro standardní disky spravované s ssd syd můžete ukládat do standardního úložiště HDD a [vybírat mezi možnostmi LRS a ZRS](https://azure.microsoft.com/pricing/details/managed-disks/). Informace o integraci se sadami dostupnosti najdete [v tématu Úvod do spravovaných disků Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).

## <a name="switch-from-zrs-classic"></a>Přepnutí z ZRS Classic

> [!IMPORTANT]
> Microsoft bude odsuzovat a migrovat ZRS Classic účty března 31, 2021. Další podrobnosti budou poskytnuty zákazníkům ZRS Classic před vyřazením.
>
> Po ZRS bude obecně k dispozici v dané oblasti, zákazníci již nebudou moci vytvářet účty ZRS Classic z portálu Azure v této oblasti. Použití Microsoft PowerShell a Azure CLI k vytvoření účtů ZRS Classic je možnost, dokud ZRS Classic je zastaralé. Informace o tom, kde je k dispozici ZRS, najdete [v tématu redundance úložiště Azure](storage-redundancy.md).

ZRS Classic asynchronně replikuje data napříč datovými centry v rámci jedné až dvou oblastí. Replikovaná data nemusí být k dispozici, pokud společnost Microsoft nezahájí převzetí služeb při selhání sekundární. Účet ZRS Classic nelze převést na nebo z LRS, GRS nebo RA-GRS. ZRS Classic účty také nepodporují metriky nebo protokolování.

ZRS Classic je k dispozici pouze pro **objekty BLOB bloku** v účtech úložiště pro obecné účely V1 (GPv1). Další informace o účtech úložiště najdete v tématu [Přehled účtu úložiště Azure](storage-account-overview.md).

Chcete-li ručně migrovat data účtu ZRS do nebo z účtu LRS, GRS, RA-GRS nebo ZRS Classic, použijte jeden z následujících nástrojů: AzCopy, Azure Storage Explorer, PowerShell nebo Azure CLI. Můžete také vytvořit vlastní řešení migrace s jedním z klientských knihoven Azure Storage.

Můžete také upgradovat svůj účet úložiště ZRS Classic na ZRS pomocí portálu Azure, PowerShellu nebo Azure CLI v oblastech, kde je k dispozici ZRS.

# <a name="portal"></a>[Portál](#tab/portal)

Pokud chcete upgradovat na ZRS na webu Azure Portal, přejděte do nastavení **konfigurace** účtu a zvolte **Upgrade**:

![Upgrade ZRS Classic na ZRS na portálu](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Chcete-li upgradovat na ZRS pomocí prostředí PowerShell, zavolejte následující příkaz:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li upgradovat na ZRS pomocí příkazového příkazu Azure, zavolejte následující příkaz:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Náklady spojené se změnou způsobu replikace dat

Náklady spojené se změnou způsobu replikace dat závisí na konverzní cestě. Řazení od nejméně nejdražší, Azure Storage redundance nabídky patří LRS, ZRS, GRS, RA-GRS, GZRS a RA-GZRS.

Například přechod *z* LRS na jakýkoli jiný typ replikace bude mít za následek další poplatky, protože se přesouváte na sofistikovanější úroveň redundance. Migrace *na* GRS nebo RA-GRS bude účtovat poplatek za odchozí šířku pásma, protože vaše data (v primární oblasti) jsou replikována do vzdálené sekundární oblasti. Tento poplatek je jednorázový poplatek při počátečním nastavení. Po zkopírování dat nejsou žádné další poplatky za migraci. Podrobnosti o poplatcích za šířku pásma najdete na [stránce Ceny úložiště Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Pokud migrujete účet úložiště z GRS do LRS, nepředstavují žádné další náklady, ale replikovaná data se odstraní ze sekundárního umístění.

> [!IMPORTANT]
> Pokud migrujete účet úložiště z RA-GRS na GRS nebo LRS, bude se tento účet účtovat jako RA-GRS dalších 30 dní po datu, kdy byl převeden.

## <a name="see-also"></a>Viz také

- [Redundance azure úložiště](storage-redundancy.md)
- [Kontrola vlastnosti Poslední čas synchronizace pro účet úložiště](last-sync-time-get.md)
- [Navrhování vysoce dostupných aplikací pomocí geograficky redundantního úložiště pro přístup ke čtení](storage-designing-ha-apps-with-ragrs.md)
