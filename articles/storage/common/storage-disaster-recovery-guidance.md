---
title: Zotavení po havárii a převzetí služeb při selhání účtu úložiště
titleSuffix: Azure Storage
description: Azure Storage podporuje převzetí služeb při selhání účtu v geograficky redundantních účtech úložiště. S převzetím služeb při selhání můžete zahájit proces převzetí služeb při selhání pro váš účet úložiště, pokud primární koncový bod nebude k dispozici.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: f556c7acd903c108193f9c12a2849500645b119b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506697"
---
# <a name="disaster-recovery-and-storage-account-failover"></a>Zotavení po havárii a převzetí služeb při selhání účtu úložiště

Microsoft usiluje o to, aby byly služby Azure vždycky dostupné. Může ale dojít k neplánovaným výpadkům služby. Pokud vaše aplikace vyžaduje odolnost, společnost Microsoft doporučuje používat geograficky redundantní úložiště, aby se vaše data zkopírovala do druhé oblasti. Kromě toho by zákazníci měli mít k dispozici plán zotavení po havárii pro zpracování oblasti výpadku regionální služby. Důležitou součástí plánu zotavení po havárii je příprava na převzetí služeb při selhání sekundárním koncovým bodem v případě, že primární koncový bod nebude k dispozici.

Azure Storage podporuje převzetí služeb při selhání účtu v geograficky redundantních účtech úložiště. S převzetím služeb při selhání můžete zahájit proces převzetí služeb při selhání pro váš účet úložiště, pokud primární koncový bod nebude k dispozici. Převzetí služeb při selhání aktualizuje sekundární koncový bod tak, aby se stal primárním koncovým bodem pro váš účet úložiště. Až se převzetí služeb při selhání dokončí, můžou klienti začít zapisovat do nového primárního koncového bodu.

Převzetí služeb při selhání účtu je k dispozici pro účty úložiště pro obecné účely verze 1, účty úložiště pro obecné účely verze 2 a účty úložiště objektů blob v nasazeních Azure Resource Manageru. Převzetí služeb při selhání účtu je podporované pro všechny veřejné oblasti, ale v současnosti není dostupné v svrchovaných nebo národních cloudech.

Tento článek popisuje koncepty a procesy spojené s převzetím služeb při selhání a popisuje, jak připravit účet úložiště k obnovení s minimálním dopadem na zákazníky. Informace o tom, jak iniciovat převzetí služeb při selhání účtu v Azure Portal nebo PowerShellu, najdete v tématu [spuštění převzetí služeb při selhání](storage-initiate-account-failover.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Zvolit správnou možnost redundance

Azure Storage udržuje víc kopií vašeho účtu úložiště, aby se zajistila odolnost a vysoká dostupnost. Jakou možnost redundance zvolíte pro svůj účet závisí na stupni odolnosti, kterou potřebujete. Z důvodu ochrany před místními výpadky nakonfigurujte účet pro geograficky redundantní úložiště s možností přístupu pro čtení ze sekundární oblasti nebo bez něj:  

**Geograficky redundantní úložiště (GRS) nebo geograficky redundantní úložiště (GZRS)** kopírují data asynchronně ve dvou geografických oblastech, které mají od sebe aspoň stovky kilometrů. Pokud primární oblast zpomaluje výpadek, pak sekundární oblast slouží jako redundantní zdroj dat. Můžete iniciovat převzetí služeb při selhání a transformovat sekundární koncový bod do primárního koncového bodu.

Geograficky redundantní úložiště s **přístupem pro čtení (RA-GRS) nebo geograficky redundantní úložiště s přístupem pro čtení (RA-GZRS)** nabízí geograficky redundantní úložiště s dodatečnou výhodou přístupu pro čtení sekundárního koncového bodu. Pokud dojde v primárním koncovém bodě k výpadku, můžou aplikace nakonfigurované pro přístup pro čtení sekundárního a navrženého pro vysokou dostupnost dál číst ze sekundárního koncového bodu. Microsoft doporučuje RA-GZRS pro zajištění maximální dostupnosti a odolnosti pro vaše aplikace.

Další informace o redundanci v Azure Storage najdete v tématu [Azure Storage redundance](storage-redundancy.md).

> [!WARNING]
> Geograficky redundantní úložiště přináší riziko ztráty dat. Data se zkopírují do sekundární oblasti asynchronně, což znamená, že mezi daty zapsanými do primární oblasti dojde k zápisu do sekundární oblasti. V případě výpadku dojde ke ztrátě operací zápisu do primárního koncového bodu, které ještě nebyly zkopírovány do sekundárního koncového bodu.

## <a name="design-for-high-availability"></a>Návrh pro zajištění vysoké dostupnosti

Je důležité navrhnout aplikaci pro zajištění vysoké dostupnosti od začátku. Pokyny pro návrh aplikace a plánování zotavení po havárii najdete v těchto prostředcích Azure:

- [Návrh odolných aplikací pro Azure](/azure/architecture/framework/resiliency/app-design): Přehled klíčových konceptů pro navrhování vysoce dostupných aplikací v Azure.
- [Kontrolní seznam odolnosti](/azure/architecture/checklist/resiliency-per-service): kontrolní seznam pro ověření, že vaše aplikace implementuje osvědčené postupy návrhu pro vysokou dostupnost.
- [Návrh aplikací s vysokou dostupností pomocí geografické redundance](geo-redundant-design.md): pokyny k návrhu pro vytváření aplikací pro využití geograficky redundantního úložiště.
- [Kurz: vytvoření vysoce dostupné aplikace s využitím úložiště objektů BLOB](../blobs/storage-create-geo-redundant-storage.md): kurz, který ukazuje, jak vytvořit vysoce dostupnou aplikaci, která automaticky přepíná mezi koncovými body jako se selháním a obnovením se simulují. 

Kromě toho mějte na paměti tyto osvědčené postupy pro udržení vysoké dostupnosti dat Azure Storage:

- **Disky:** Použijte [Azure Backup](https://azure.microsoft.com/services/backup/) k zálohování disků virtuálních počítačů využívaných virtuálními počítači Azure. Zvažte také použití [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) k ochraně vašich virtuálních počítačů v případě regionálních havárií.
- **Objekty blob bloku:** Zapněte [obnovitelné odstranění](../blobs/soft-delete-blob-overview.md) pro ochranu proti odstranění na úrovni objektu a přepsání nebo zkopírujte objekty blob bloku do jiného účtu úložiště v jiné oblasti pomocí [AzCopy](./storage-use-azcopy-v10.md), [Azure PowerShell](/powershell/module/az.storage/)nebo [knihovny pro přesun dat Azure](storage-use-data-movement-library.md).
- **Soubory:** Použijte [Azure Backup](../../backup/azure-file-share-backup-overview.md) k zálohování sdílených složek souborů. Povolit i [obnovitelné odstranění](../files/storage-files-prevent-file-share-deletion.md) , aby se chránily proti náhodným odstranění sdílení souborů. Pro geografickou redundanci, pokud není k dispozici GRS, zkopírujte soubory do jiného účtu úložiště v jiné oblasti pomocí [AzCopy](./storage-use-azcopy-v10.md) nebo [Azure PowerShell](/powershell/module/az.storage/) .
- **Tabulky:** pomocí [AzCopy](./storage-use-azcopy-v10.md) můžete exportovat data tabulky do jiného účtu úložiště v jiné oblasti.

## <a name="track-outages"></a>Sledovat výpadky

Zákazníci se můžou přihlásit k odběru [řídicího panelu Azure Service Health](https://azure.microsoft.com/status/) , aby mohli sledovat stav a stav Azure Storage a dalších služeb Azure.

Microsoft také doporučuje navrhovat aplikace, aby se připravila možnost selhání zápisu. Vaše aplikace by měla vystavovat chyby při psaní způsobem, který vás upozorní na možnost výpadku v primární oblasti.

## <a name="understand-the-account-failover-process"></a>Pochopení procesu převzetí služeb při selhání účtu

Převzetí služeb při selhání účtu spravovaného zákazníkem vám umožní převzít služby při selhání celého účtu úložiště do sekundární oblasti, pokud primární z nějakého důvodu nebude k dispozici. Když vynutíte převzetí služeb při selhání sekundární oblastí, můžou klienti po dokončení převzetí služeb při selhání začít zapisovat data do sekundárního koncového bodu. Převzetí služeb při selhání obvykle trvá přibližně hodinu.

### <a name="how-an-account-failover-works"></a>Jak funguje převzetí služeb při selhání

Za normálních okolností klient zapisuje data do účtu Azure Storage v primární oblasti a tato data se zkopírují asynchronně do sekundární oblasti. Následující obrázek znázorňuje situaci, kdy je primární oblast k dispozici:

![Klienti zapisují data do účtu úložiště v primární oblasti.](media/storage-disaster-recovery-guidance/primary-available.png)

Pokud z nějakého důvodu nebude primární koncový bod k dispozici, klient už nebude moct zapisovat do účtu úložiště. Na následujícím obrázku je znázorněn scénář, ve kterém primární událost není k dispozici, ale ještě neproběhlo žádné obnovení:

![Primární není k dispozici, takže klienti nemůžou zapisovat data.](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

Zákazník zahájí převzetí služeb při selhání účtu sekundárním koncovým bodem. Proces převzetí služeb při selhání aktualizuje položku DNS, kterou poskytuje Azure Storage, aby se sekundární koncový bod stal novým primárním koncovým bodem pro váš účet úložiště, jak je znázorněno na následujícím obrázku:

![Zákazník iniciuje převzetí služeb při selhání účtem sekundárního koncového bodu.](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

Jakmile se položka DNS aktualizuje a požadavky se přesměrují do nového primárního koncového bodu, pro geografické redundantní účty se obnoví přístup pro zápis. Stávající koncové body služby úložiště pro objekty blob, tabulky, fronty a soubory zůstávají po převzetí služeb při selhání stejné.

> [!IMPORTANT]
> Po dokončení převzetí služeb při selhání se účet úložiště nakonfiguruje na místně redundantní v novém primárním koncovém bodu. Pokud chcete obnovit replikaci do nového sekundárního počítače, nakonfigurujte znovu účet pro geografickou redundanci.
>
> Mějte na paměti, že převod účtu LRS na použití geografické redundance se účtuje za cenu. Tyto náklady se vztahují na aktualizaci účtu úložiště v nové primární oblasti po převzetí služeb při selhání.  

### <a name="anticipate-data-loss"></a>Předvídání ztráty dat

> [!CAUTION]
> Převzetí služeb při selhání účtu obvykle zahrnuje určitou ztrátu dat. Je důležité pochopit důsledky zahájení převzetí služeb při selhání účtu.  

Vzhledem k tomu, že data jsou zapsána asynchronně z primární oblasti do sekundární oblasti, je vždy zpoždění před zápisem do primární oblasti zkopírována do sekundární oblasti. Pokud primární oblast nebude k dispozici, nejaktuálnější zápisy se ještě nezkopírovaly do sekundární oblasti.

Když vynutíte převzetí služeb při selhání, všechna data v primární oblasti se ztratí, protože se sekundární oblast stala novou primární oblastí a účet úložiště se nakonfiguruje jako místně redundantní. Všechna data, která jsou už zkopírovaná do sekundárního, se při převzetí služeb při selhání uchovávají. Veškerá data zapsaná na primární, která se také zkopírovala do sekundárního, se ale ztratí trvale.

Vlastnost **čas poslední synchronizace** označuje poslední čas, po který je zaručeno, že data z primární oblasti budou zapsána do sekundární oblasti. Všechna data zapsaná před časem poslední synchronizace jsou k dispozici na sekundárním počítači, zatímco data zapsaná po poslední synchronizaci nemusí být zapsána do sekundárního a mohou být ztracena. Tuto vlastnost použijte v případě výpadku k odhadu množství ztráty dat, která se může vyvolávat při převzetí služeb při selhání účtu.

Osvědčeným postupem je navrhnout aplikaci tak, aby bylo možné použít čas poslední synchronizace k vyhodnocení očekávané ztráty dat. Pokud například protokoluje všechny operace zápisu, můžete porovnat čas poslední operace zápisu s časem poslední synchronizace a určit, které zápisy nebyly synchronizovány do sekundárního.

Další informace o kontrole vlastnosti **Poslední čas synchronizace** najdete v tématu [Kontrola vlastnosti času poslední synchronizace pro účet úložiště](last-sync-time-get.md).

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Při navrácení služeb po obnovení původní primární služby postupujte opatrně.

Po převzetí služeb při selhání z primárního umístění do sekundární oblasti je váš účet úložiště nakonfigurovaný místně redundantní v nové primární oblasti. Pak můžete znovu nakonfigurovat účet pro geografickou redundanci. Když je účet po převzetí služeb při selhání nakonfigurovaný na geografickou redundanci, nová primární oblast začne okamžitě kopírovat data do nové sekundární oblasti, která byla primární před původní převzetí služeb při selhání. Může ale nějakou dobu trvat, než se stávající data v primárním čase zkopírují do nového sekundárního.

Po překonfigurování účtu úložiště pro geografickou redundanci je možné zahájit jiné převzetí služeb při selhání z nové primární služby zpátky do nového sekundárního objektu. V takovém případě se původní primární oblast před převzetím služeb při selhání znovu vytvoří v primární oblasti a nakonfiguruje se tak, aby byla místně redundantní. Všechna data v primární oblasti po převzetí služeb při selhání (původní sekundární) se pak ztratí. Pokud se většina dat v účtu úložiště před navrácením služeb po obnovení nezkopírovala do nového sekundárního, může dojít k výrazné ztrátě dat.

Chcete-li se vyhnout zásadní ztrátě dat, před navrácením služeb po obnovení ověřte hodnotu vlastnosti **čas poslední synchronizace** . Porovnejte čas poslední synchronizace s časem, kdy byla data zapsána do nové primární pro vyhodnocení očekávané ztráty dat. 

## <a name="initiate-an-account-failover"></a>Zahájení převzetí služeb při selhání účtu

Převzetí služeb při selhání účtu můžete iniciovat z rozhraní API Azure Portal, PowerShellu, Azure CLI nebo poskytovatele prostředků Azure Storage. Další informace o tom, jak iniciovat převzetí služeb při selhání, najdete v tématu [spuštění převzetí služeb při selhání](storage-initiate-account-failover.md).

## <a name="additional-considerations"></a>Další aspekty

Další informace popsané v této části vám pomohou pochopit, jak můžou být vaše aplikace a služby ovlivněné při vynucení převzetí služeb při selhání.

### <a name="storage-account-containing-archived-blobs"></a>Účet úložiště obsahující archivované objekty blob

Účty úložiště obsahující archivované objekty blob podporují převzetí služeb při selhání účtu. Po dokončení převzetí služeb při selhání musí být všechny archivní objekty blob znovu vyčištěné do online úrovně, než bude možné účet nakonfigurovat pro geografickou redundanci.

### <a name="storage-resource-provider"></a>Poskytovatel prostředků úložiště

Po dokončení převzetí služeb při selhání mohou klienti znovu číst a zapisovat Azure Storage data v nové primární oblasti. Poskytovatel prostředků Azure Storage ale nepřevezme služby při selhání, takže operace správy prostředků musí stále probíhat v primární oblasti. Pokud není primární oblast k dispozici, nebudete moci provádět operace správy v účtu úložiště.

Vzhledem k tomu, že poskytovatel prostředků Azure Storage převezme služby při selhání, vlastnost [Location](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location) po dokončení převzetí služeb při selhání vrátí původní primární umístění.

### <a name="azure-virtual-machines"></a>Virtuální počítače Azure

Virtuální počítače Azure při převzetí služeb při selhání v rámci účtu převezmou služby při selhání. Pokud primární region přestane být k dispozici a převezmete služby při selhání do sekundární oblasti, budete muset po převzetí služeb při selhání znovu vytvořit všechny virtuální počítače. K převzetí služeb při selhání účtu taky může dojít ke ztrátě dat. Microsoft doporučuje následující pokyny pro [vysokou dostupnost](../../virtual-machines/availability.md) a [zotavení po havárii](../../virtual-machines/backup-recovery.md) , které jsou specifické pro virtuální počítače v Azure.

### <a name="azure-unmanaged-disks"></a>Nespravované disky Azure

Osvědčeným postupem je, že společnost Microsoft doporučuje převést nespravované disky na spravované disky. Pokud ale potřebujete převzít služby při selhání účtu, který obsahuje nespravované disky připojené k virtuálním počítačům Azure, budete muset před zahájením převzetí služeb při selhání vypnout virtuální počítač.

Nespravované disky se ukládají jako objekty blob stránky v Azure Storage. Když je virtuální počítač spuštěný v Azure, všechny nespravované disky připojené k virtuálnímu počítači se zapůjčují. Převzetí služeb při selhání účtu nemůže pokračovat, pokud je u objektu BLOB zapůjčení. K provedení převzetí služeb při selhání použijte následující postup:

1. Než začnete, poznamenejte si názvy všech nespravovaných disků, jejich logické jednotky (LUN) a virtuální počítač, ke kterému jsou připojené. Díky tomu bude snazší znovu připojit disky po převzetí služeb při selhání.
2. Vypněte virtuální počítač.
3. Odstraňte virtuální počítač, ale zachovejte soubory VHD pro nespravované disky. Poznamenejte si čas, kdy jste virtuální počítač odstranili.
4. Počkejte, až se aktualizuje **čas poslední synchronizace** , a pozdější dobu než čas, kdy jste virtuální počítač odstranili. Tento krok je důležitý, protože pokud se sekundární koncový bod v případě převzetí služeb při selhání plně neaktualizoval pomocí souborů VHD, nemusí virtuální počítač fungovat správně v nové primární oblasti.
5. Zahajte převzetí služeb při selhání účtu.
6. Počkejte, než se dokončí převzetí služeb při selhání účtu a sekundární oblast se stane novou primární oblastí.
7. Vytvořte virtuální počítač v nové primární oblasti a znovu připojte virtuální pevné disky.
8. Spusťte nový virtuální počítač.

Mějte na paměti, že při vypnutí virtuálního počítače dojde ke ztrátě všech dat uložených na dočasném disku.

## <a name="unsupported-features-and-services"></a>Nepodporované funkce a služby

Pro převzetí služeb při selhání účtu se nepodporují následující funkce a služby:

- Synchronizace souborů Azure nepodporuje převzetí služeb při selhání účtu úložiště. U účtů úložiště obsahujících sdílené složky Azure, které se v Synchronizaci souborů Azure používají jako koncové body cloudu, by se nemělo provádět převzetí služeb při selhání. Pokud to uděláte, synchronizace přestane fungovat a v případě nově vrstvených souborů může dojít i k neočekávané ztrátě dat.
- Účty úložiště ADLS Gen2 (účty s povoleným hierarchickým oborem názvů) se v tuto chvíli nepodporují.
- Nepovedlo se převzít služby účtů úložiště obsahující objekty blob bloku Premium. Účty úložiště, které podporují objekty blob bloku Premium, v současné době nepodporují geografickou redundanci.
- Nepovedlo se převzít služby účtů úložiště obsahující jakékoli povolené kontejnery [zásad neměnnosti worm](../blobs/storage-blob-immutable-storage.md) . Odemčené nebo uzamčené časové uchovávání na základě času nebo zásady právního blokování brání převzetí služeb při selhání, aby se zachovalo dodržování předpisů

## <a name="copying-data-as-an-alternative-to-failover"></a>Kopírování dat jako alternativa k převzetí služeb při selhání

Pokud je váš účet úložiště nakonfigurovaný pro přístup pro čtení sekundárního, můžete aplikaci navrhnout tak, aby se načetla ze sekundárního koncového bodu. Pokud v případě výpadku v primární oblasti nechcete převzít služby při selhání, můžete pomocí nástrojů, jako jsou [AzCopy](./storage-use-azcopy-v10.md), [Azure PowerShell](/powershell/module/az.storage/)nebo [knihovny pro přesun dat Azure](../common/storage-use-data-movement-library.md) , kopírovat data z účtu úložiště v sekundární oblasti do jiného účtu úložiště v neovlivněné oblasti. Pak můžete své aplikace nasměrovat na tento účet úložiště pro čtení i zápis.

> [!CAUTION]
> Převzetí služeb při selhání účtu by se nemělo používat jako součást vaší strategie migrace dat.

## <a name="microsoft-managed-failover"></a>Převzetí služeb při selhání spravované Microsoftem

V extrémních situacích, kdy dojde ke ztrátě oblasti z důvodu významné havárie, může společnost Microsoft zahájit místní převzetí služeb při selhání. V takovém případě není nutná žádná akce s vaší částí. Dokud neproběhne převzetí služeb při selhání spravované Microsoftem, nebudete mít k účtu úložiště přístup pro zápis. Vaše aplikace se můžou číst ze sekundární oblasti, pokud je váš účet úložiště nakonfigurovaný pro RA-GRS nebo RA-GZRS.

## <a name="see-also"></a>Viz také

- [Použití geografické redundance k návrhu vysoce dostupných aplikací](geo-redundant-design.md)
- [Zahájení převzetí služeb při selhání účtu](storage-initiate-account-failover.md)
- [Podívejte se na vlastnost čas poslední synchronizace pro účet úložiště.](last-sync-time-get.md)
- [Kurz: vytvoření vysoce dostupné aplikace s úložištěm BLOB](../blobs/storage-create-geo-redundant-storage.md)