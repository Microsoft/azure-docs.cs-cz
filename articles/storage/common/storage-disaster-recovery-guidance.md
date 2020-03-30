---
title: Převzetí služeb při selhání účtu zotavení po havárii a úložiště (preview)
titleSuffix: Azure Storage
description: Azure Storage podporuje převzetí služeb při selhání (náhled) účtu pro geograficky redundantní účty úložiště. S převzetíslužeb při selhání účtu, můžete zahájit proces převzetí služeb při selhání pro váš účet úložiště, pokud primární koncový bod přestane být k dispozici.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: f7a8f6d0d3ab3b456c41128da9b689f6b7eda0f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365355"
---
# <a name="disaster-recovery-and-account-failover-preview"></a>Zotavení po havárii a převzetí služeb při selhání účtu (náhled)

Microsoft se snaží zajistit, aby služby Azure byly vždy dostupné. Může však dojít k neplánovaným výpadkům služby. Pokud vaše aplikace vyžaduje odolnost proti chybám, společnost Microsoft doporučuje používat geograficky redundantní úložiště, aby byla data zkopírována do druhé oblasti. Kromě toho by zákazníci měli mít plán zotavení po havárii na místě pro zpracování výpadku regionální služby. Důležitou součástí plánu zotavení po havárii se připravuje na převzetí služeb při selhání do sekundárního koncového bodu v případě, že primární koncový bod přestane být k dispozici.

Azure Storage podporuje převzetí služeb při selhání (náhled) účtu pro geograficky redundantní účty úložiště. S převzetíslužeb při selhání účtu, můžete zahájit proces převzetí služeb při selhání pro váš účet úložiště, pokud primární koncový bod přestane být k dispozici. Převzetí služeb při selhání aktualizuje sekundární koncový bod, aby se stal primárním koncovým bodem pro váš účet úložiště. Po dokončení převzetí služeb při selhání mohou klienti začít zapisovat do nového primárního koncového bodu.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Tento článek popisuje koncepty a procesy spojené s převzetí služeb při selhání účtu a popisuje, jak připravit účet úložiště pro obnovení s nejmenším dopadem na zákazníka. Informace o tom, jak zahájit převzetí služeb při selhání účtu na webu Azure Portal nebo PowerShellu, najdete v [tématu Zahájení převzetí služeb při selhání účtu (preview).](storage-initiate-account-failover.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Výběr správné možnosti redundance

Azure Storage udržuje několik kopií vašeho účtu úložiště, aby byla zajištěna odolnost a vysoká dostupnost. Možnost redundance, kterou pro svůj účet zvolíte, závisí na stupni odolnosti, kterou potřebujete. Pro ochranu před regionálními výpadky zvolte geograficky redundantní úložiště s možností přístupu pro čtení ze sekundární oblasti nebo bez něj:  

**Geograficky redundantní úložiště (GRS) nebo geograficky zónově redundantní úložiště (GZRS) (preview)** kopíruje data asynchronně ve dvou geografických oblastech, které jsou od sebe vzdáleny alespoň stovky kilometrů. Pokud primární oblast trpí výpadku, sekundární oblast slouží jako redundantní zdroj pro vaše data. Můžete zahájit převzetí služeb při selhání transformovat sekundární koncový bod do primárního koncového bodu.

**Geograficky redundantní úložiště pro čtení (RA-GRS) nebo geograficky redundantní úložiště pro čtení (RA-GZRS) (preview)** poskytuje geograficky redundantní úložiště s další výhodou přístupu pro čtení ke sekundárnímu koncovému bodu. Pokud dojde k výpadku v primárním koncovém bodu, aplikace nakonfigurované pro RA-GRS a určené pro vysokou dostupnost mohou pokračovat ve čtení ze sekundárního koncového bodu. Společnost Microsoft doporučuje RA-GRS pro maximální odolnost pro vaše aplikace.

Další informace o redundanci ve službě Azure Storage najdete v [tématu Redundance Azure Storage](storage-redundancy.md).

> [!WARNING]
> Geograficky redundantní úložiště s sebou nese riziko ztráty dat. Data jsou zkopírována do sekundární oblasti asynchronně, což znamená, že existuje zpoždění mezi při zápisu dat do primární oblasti do sekundární oblasti. V případě výpadku budou ztraceny operace zápisu do primárního koncového bodu, které ještě nebyly zkopírovány do sekundárního koncového bodu.

## <a name="design-for-high-availability"></a>Návrh pro zajištění vysoké dostupnosti

Je důležité navrhnout aplikaci pro vysokou dostupnost od začátku. Pokyny při navrhování aplikace a plánování zotavení po havárii najdete v těchto prostředcích Azure:

- [Navrhování odolných aplikací pro Azure:](/azure/architecture/checklist/resiliency-per-service)Přehled klíčových konceptů pro navrhování vysoce dostupných aplikací v Azure.
- [Kontrolní seznam dostupnosti](/azure/architecture/checklist/resiliency-per-service): Kontrolní seznam pro ověření, že vaše aplikace implementuje osvědčené postupy návrhu pro vysokou dostupnost.
- [Navrhování vysoce dostupných aplikací pomocí RA-GRS:](storage-designing-ha-apps-with-ragrs.md)Pokyny k návrhu pro vytváření aplikací s využitím využití RA-GRS.
- [Kurz: Vytvoření vysoce dostupné aplikace s úložištěm objektů Blob](../blobs/storage-create-geo-redundant-storage.md): Kurz, který ukazuje, jak vytvořit vysoce dostupnou aplikaci, která automaticky přepíná mezi koncovými body, protože jsou simulovány chyby a obnovení. 

Kromě toho mějte na paměti tyto doporučené postupy pro udržování vysoké dostupnosti pro vaše data azure storage:

- **Disky:** Azure [Backup](https://azure.microsoft.com/services/backup/) slouží k zálohování disků virtuálních počítačů používaných vašimi virtuálními počítači Azure. Zvažte také použití [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) k ochraně virtuálních počítačů v případě místní havárie.
- **Objekty BLOB bloku:** Zapněte [obnovitelné odstranění,](../blobs/storage-blob-soft-delete.md) abyste ochránili odstranění a přepsání na úrovni objektů, nebo zkopírujte objekty BLOB bloku do jiného účtu úložiště v jiné oblasti pomocí [AzCopy](storage-use-azcopy.md), [Azure PowerShellnebo](storage-powershell-guide-full.md) [knihovny Azure Data Movement](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
- **Soubory:** Ke kopírování souborů do jiného účtu úložiště v jiné oblasti použijte [AzCopy](storage-use-azcopy.md) nebo [Azure PowerShell.](storage-powershell-guide-full.md)
- **Tabulky:** Pomocí [AzCopy](storage-use-azcopy.md) exportujte data tabulky do jiného účtu úložiště v jiné oblasti.

## <a name="track-outages"></a>Sledování výpadků

Zákazníci se mohou přihlásit k odběru [řídicího panelu stavu služby Azure](https://azure.microsoft.com/status/) a sledovat stav a stav Azure Storage a dalších služeb Azure.

Společnost Microsoft také doporučuje navrhnout aplikaci připravit se na možnost selhání zápisu. Vaše aplikace by měla vystavit selhání zápisu způsobem, který vás upozorní na možnost výpadku v primární oblasti.

## <a name="understand-the-account-failover-process"></a>Principy procesu převzetí služeb při selhání účtu

Převzetí služeb při selhání účtu spravovaného zákazníkem (preview) umožňuje selhat celý účet úložiště do sekundární oblasti, pokud primární přestane být z nějakého důvodu k dispozici. Když vynutíte převzetí služeb při selhání do sekundární oblasti, klienti mohou začít zapisovat data do sekundárního koncového bodu po dokončení převzetí služeb při selhání. Převzetí služeb při selhání obvykle trvá přibližně hodinu.

### <a name="how-an-account-failover-works"></a>Jak funguje převzetí služeb při selhání

Za normálních okolností klient zapíše data do účtu Azure Storage v primární oblasti a tato data se zkopírují asynchronně do sekundární oblasti. Následující obrázek znázorňuje scénář, kdy je k dispozici primární oblast:

![Klienti zapisují data do účtu úložiště v primární oblasti](media/storage-disaster-recovery-guidance/primary-available.png)

Pokud primární koncový bod přestane být z nějakého důvodu k dispozici, klient již není schopen zapisovat do účtu úložiště. Následující obrázek znázorňuje scénář, kdy primární stal se k dispozici, ale žádné obnovení se dosud nestalo:

![Primární není k dispozici, takže klienti nemohou zapisovat data](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

Zákazník iniciuje převzetí služeb při selhání účtu do sekundárního koncového bodu. Proces převzetí služeb při selhání aktualizuje položku DNS poskytovanou službou Azure Storage tak, aby se sekundární koncový bod stal novým primárním koncovým bodem pro váš účet úložiště, jak je znázorněno na následujícím obrázku:

![Zákazník iniciuje převzetí služeb při selhání účtu do sekundárního koncového bodu](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

Přístup pro zápis je obnoven pro účty GRS a RA-GRS po aktualizaci položky DNS a směrování požadavků na nový primární koncový bod. Existující koncové body služby úložiště pro objekty BLOB, tabulky, fronty a soubory zůstanou stejné po převzetí služeb při selhání.

> [!IMPORTANT]
> Po dokončení převzetí služeb při selhání je účet úložiště nakonfigurován tak, aby byl místně redundantní v novém primárním koncovém bodu. Chcete-li pokračovat v replikaci na novou sekundární, nakonfigurujte účet tak, aby znovu používal geograficky redundantní úložiště (ra-grs nebo GRS).
>
> Mějte na paměti, že převod účtu LRS na RA-GRS nebo GRS za následek náklady. Tyto náklady se vztahují na aktualizaci účtu úložiště v nové primární oblasti pro použití RA-GRS nebo GRS po převzetí služeb při selhání.  

### <a name="anticipate-data-loss"></a>Předvídání ztráty dat

> [!CAUTION]
> Převzetí služeb při selhání účtu obvykle zahrnuje ztrátu některých dat. Je důležité pochopit důsledky zahájení převzetí služeb při selhání účtu.  

Vzhledem k tomu, že data jsou zapsána asynchronně z primární oblasti do sekundární oblasti, je vždy zpoždění před zápisem do primární oblasti je zkopírován do sekundární oblasti. Pokud primární oblast přestane být k dispozici, nejnovější zápisy pravděpodobně ještě nebyly zkopírovány do sekundární oblasti.

Když vynutíte převzetí služeb při selhání, dojde ke ztrátě všech dat v primární oblasti, protože sekundární oblast se stane novou primární oblastí a účet úložiště je nakonfigurován tak, aby byl místně redundantní. Všechna data již zkopírovaná do sekundárního se uchovávají, když dojde k převzetí služeb při selhání. Všechna data zapsaná do primární, která nebyla také zkopírována do sekundární, jsou však trvale ztracena.

Vlastnost **Doba poslední synchronizace** označuje poslední čas, kdy je zaručeno, že data z primární oblasti byla zapsána do sekundární oblasti. Všechna data zapsaná před časem poslední synchronizace jsou k dispozici na sekundární, zatímco data zapsaná po poslední době synchronizace nemusí být zapsána do sekundárnía a může dojít ke ztrátě. Tuto vlastnost použijte v případě výpadku k odhadu množství ztráty dat, která může vzniknout spuštěním převzetí služeb při selhání účtu.

Jako osvědčený postup navrhněte aplikaci tak, aby bylo možné použít čas poslední synchronizace k vyhodnocení očekávané ztráty dat. Například pokud zaznamenáváte všechny operace zápisu, můžete porovnat čas posledních operací zápisu s časem poslední synchronizace a určit, které zápisy nebyly synchronizovány se sekundárním.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Buďte opatrní při selhání zpět na původní primární

Po převzetí služeb při selhání z primární do sekundární oblasti je váš účet úložiště nakonfigurován tak, aby byl místně redundantní v nové primární oblasti. Účet pro geografickou redundanci můžete znovu nakonfigurovat tak, že jej aktualizujete tak, aby používal grs nebo RA-GRS. Když je účet znovu nakonfigurován pro geografickou redundanci po převzetí služeb při selhání, nová primární oblast okamžitě začne kopírovat data do nové sekundární oblasti, která byla primární před původním převzetím služeb při selhání. Však může trvat nějakou dobu, než existující data v primární je plně zkopírována do nové sekundární.

Po překonfigurování účtu úložiště pro geografickou redundanci je možné zahájit další převzetí služeb při selhání z nové primární zpět na nové sekundární. V tomto případě původní primární oblast před převzetím služeb při selhání se stane primární oblasti znovu a je nakonfigurován jako místně redundantní. Všechna data v primární oblasti po převzetí služeb při selhání (původní sekundární) se pak ztratí. Pokud většina dat v účtu úložiště nebyla zkopírována do novésekundární před vrácením po obnovení, může dojít ke ztrátě velkých dat.

Chcete-li se vyhnout velké ztrátě dat, zkontrolujte hodnotu vlastnosti **Poslední doba synchronizace** před selháním. Porovnejte čas poslední synchronizace s poslední časy, kdy byla data zapsána do nové primární vyhodnotit očekávanou ztrátu dat. 

## <a name="initiate-an-account-failover"></a>Zahájení převzetí služeb při selhání účtu

Převzetí služeb při selhání účtu můžete zahájit z webu Azure Portal, PowerShellu, rozhraní API Azure nebo rozhraní API zprostředkovatele prostředků Azure Storage. Další informace o zahájení převzetí služeb při selhání naleznete v [tématu Zahájení převzetí služeb při selhání účtu (náhled).](storage-initiate-account-failover.md)

## <a name="about-the-preview"></a>O náhledu

Převzetí služeb při selhání účtu je k dispozici ve verzi preview pro všechny zákazníky, kteří používají GRS nebo RA-GRS s nasazením Azure Resource Manager. Obecné účely v1, Obecné účely v2 a typy účtů úložiště objektů blob jsou podporovány. Převzetí služeb při selhání účtu je aktuálně k dispozici ve všech veřejných oblastech. Převzetí služeb při selhání účtu není v tuto chvíli k dispozici v suverénních/národních cloudech.

Náhled je určen pouze pro neprodukční použití. Smlouvy o úrovni výrobních služeb (SLA) nejsou v současné době k dispozici.

### <a name="additional-considerations"></a>Další aspekty

Další důležité informace popsané v této části naleznete v následujících aspektech, abyste pochopili, jak mohou být vaše aplikace a služby ovlivněny, když během období náhledu vynutíte převzetí služeb při selhání.

#### <a name="storage-account-containing-archived-blobs"></a>Účet úložiště obsahující archivované objekty BLOB

Účty úložiště obsahující převzetí služeb při selhání archivovaných objektů BLOB podporují. Po dokončení převzetí služeb při selhání, chcete-li převést účet zpět na GRS nebo RA-GRS všechny archivované objekty BLOB je třeba rehydratovat na online úroveň jako první.

#### <a name="storage-resource-provider"></a>Poskytovatel prostředků úložiště

Po dokončení převzetí služeb při selhání mohou klienti znovu číst a zapisovat data azure storage v nové primární oblasti. Zprostředkovatel prostředků úložiště Azure však není převzetí služeb při selhání, takže operace správy prostředků musí stále probíhat v primární oblasti. Pokud primární oblast není k dispozici, nebudete moci provádět operace správy v účtu úložiště.

Vzhledem k tomu, že poskytovatel prostředků úložiště Azure nepřevezme služby při selhání, vlastnost [Location](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location) vrátí původní primární umístění po dokončení převzetí služeb při selhání.

#### <a name="azure-virtual-machines"></a>Virtuální počítače Azure

Virtuální počítače Azure (VM) nejsou převzetí služeb při selhání jako součást převzetí služeb při selhání účtu. Pokud primární oblast přestane být k dispozici a převzetí služeb při selhání do sekundární oblasti, budete muset znovu vytvořit všechny virtuální počítače po převzetí služeb při selhání. Také je potenciální ztráta dat přidružené převzetí služeb při selhání účtu. Společnost Microsoft doporučuje následující pokyny [pro vysokou dostupnost](../../virtual-machines/windows/manage-availability.md) a zotavení po [havárii](../../virtual-machines/virtual-machines-disaster-recovery-guidance.md) specifické pro virtuální počítače v Azure.

#### <a name="azure-unmanaged-disks"></a>Nespravované disky Azure

Jako osvědčený postup společnost Microsoft doporučuje převést nespravované disky na spravované disky. Pokud však potřebujete převzetí služeb při selhání účet, který obsahuje nespravované disky připojené k virtuálním počítačům Azure, budete muset vypnout virtuální počítač před zahájením převzetí služeb při selhání.

Nespravované disky se ve službě Azure Storage ukládají jako objekty BLOB stránky. Když virtuální počítač běží v Azure, všechny nespravované disky připojené k virtuálnímu počítači jsou pronajaté. Převzetí služeb při selhání účtu nemůže pokračovat, pokud je zapůjčení na objekt blob. Chcete-li provést převzetí služeb při selhání, postupujte takto:

1. Než začnete, poznamenejte si názvy všech nespravovaných disků, jejich logická čísla jednotek (LUN) a virtuální ho virtuálního počítače, ke kterému jsou připojeny. To usnadní opětovné připojení disků po převzetí služeb při selhání.
2. Vypněte virtuální hod.
3. Odstraňte virtuální ho, ale zachovat soubory VHD pro nespravované disky. Všimněte si, kdy jste odstranili virtuální ho.
4. Počkejte, dokud se aktualizuje **poslední čas synchronizace** a je pozdější než čas, kdy jste odstranili virtuální počítač. Tento krok je důležitý, protože pokud sekundární koncový bod nebyl plně aktualizován soubory Virtuálního pevného disku, když dojde k převzetí služeb při selhání, pak virtuální počítač nemusí fungovat správně v nové primární oblasti.
5. Iniciujte převzetí služeb při selhání účtu.
6. Počkejte, dokud nebude převzetí služeb při selhání účtu dokončeno a sekundární oblast se stala novou primární oblastí.
7. Vytvořte virtuální virtuální město v nové primární oblasti a znovu připojte virtuální diody.
8. Spusťte nový virtuální ms.

Mějte na paměti, že všechna data uložená na dočasném disku dojde ke ztrátě při vypnutí virtuálního počítače.

### <a name="unsupported-features-and-services"></a>Nepodporované funkce a služby

Následující funkce a služby nejsou podporovány pro převzetí služeb při selhání účtu pro předběžnou verzi:

- Azure File Sync nepodporuje převzetí služeb při selhání účtu úložiště. U účtů úložiště obsahujících sdílené složky Azure, které se v Synchronizaci souborů Azure používají jako koncové body cloudu, by se nemělo provádět převzetí služeb při selhání. Pokud to uděláte, synchronizace přestane fungovat a v případě nově vrstvených souborů může dojít i k neočekávané ztrátě dat.
- Účty úložiště ADLS Gen2 (účty, které mají povolen hierarchický obor názvů) nejsou v současné době podporovány.
- Účet úložiště obsahující objekty BLOB bloku premium nelze přepojit. Účty úložiště, které podporují objekty BLOB premium bloku aktuálně nepodporují geografickou redundanci.
- Účet úložiště obsahující všechny kontejnery s [povolenou zásadou neměnnosti červa](../blobs/storage-blob-immutable-storage.md) nelze přepojit. Odemčené/uzamčené zásady uchovávání informací nebo blokování z právních předpisů založené na čase zabraňují převzetí služeb při selhání, aby bylo zachováno dodržování předpisů.
- Po dokončení převzetí služeb při selhání mohou následující funkce přestat fungovat, pokud je původně povoleno: [odběry událostí](../blobs/storage-blob-event-overview.md), [kanál změn](../blobs/storage-blob-change-feed.md), [zásady životního cyklu](../blobs/storage-lifecycle-management-concepts.md)a [protokolování analýzy úložiště](storage-analytics-logging.md).

## <a name="copying-data-as-an-alternative-to-failover"></a>Kopírování dat jako alternativa k převzetí služeb při selhání

Pokud je váš účet úložiště nakonfigurován pro RA-GRS, máte přístup pro čtení k datům pomocí sekundárního koncového bodu. Pokud nechcete převzetí služeb při selhání v případě výpadku v primární oblasti, můžete použít nástroje, jako je [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)nebo [knihovna Azure Data Movement](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) ke kopírování dat z vašeho účtu úložiště v sekundární oblasti do jiného účtu úložiště v neovlivněné oblasti. Potom můžete nasměrovat aplikace na tento účet úložiště pro dostupnost čtení i zápisu.

> [!CAUTION]
> Převzetí služeb při selhání účtu by nemělo být používáno jako součást strategie migrace dat.


## <a name="microsoft-managed-failover"></a>Převzetí služeb při selhání spravované společností Microsoft

V extrémních případech, kdy dojde ke ztrátě oblasti v důsledku významné havárie, může společnost Microsoft zahájit místní převzetí služeb při selhání. V takovém případě není nutná žádná akce z vaší strany. Dokud nebude dokončeno převzetí služeb při selhání spravované společností Microsoft, nebudete mít přístup pro zápis do vašeho účtu úložiště. Vaše aplikace mohou číst ze sekundární oblasti, pokud je váš účet úložiště nakonfigurován pro RA-GRS. 

## <a name="see-also"></a>Viz také

- [Zahájení převzetí služeb při selhání účtu (náhled)](storage-initiate-account-failover.md)
- [Návrh aplikací s vysokou dostupností pomocí RA-GRS](storage-designing-ha-apps-with-ragrs.md)
- [Kurz: Vytvoření vysoce dostupné aplikace s úložištěm objektů blob](../blobs/storage-create-geo-redundant-storage.md) 
