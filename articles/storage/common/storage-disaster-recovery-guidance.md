---
title: Zotavení po havárii a převzetí služeb při selhání účtu úložiště (Preview) – Azure Storage
description: Azure Storage podporuje převzetí služeb při selhání účtu (Preview) u geograficky redundantních účtů úložiště. S převzetím služeb při selhání můžete zahájit proces převzetí služeb při selhání pro váš účet úložiště, pokud primární koncový bod nebude k dispozici.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 4a621f8976efe395014c073a6bd7c5d09d19d915
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671083"
---
# <a name="disaster-recovery-and-storage-account-failover-preview-in-azure-storage"></a>Zotavení po havárii a převzetí služeb při selhání účtu úložiště (Preview) v Azure Storage

Microsoft usiluje o to, aby byly služby Azure vždycky dostupné. Může ale dojít k neplánovaným výpadkům služby. Pokud vaše aplikace vyžaduje odolnost, společnost Microsoft doporučuje používat geograficky redundantní úložiště, aby se vaše data mohla replikovat do druhé oblasti. Kromě toho by zákazníci měli mít k dispozici plán zotavení po havárii pro zpracování oblasti výpadku regionální služby. Důležitou součástí plánu zotavení po havárii je příprava na převzetí služeb při selhání sekundárním koncovým bodem v případě, že primární koncový bod nebude k dispozici. 

Azure Storage podporuje převzetí služeb při selhání účtu (Preview) u geograficky redundantních účtů úložiště. S převzetím služeb při selhání můžete zahájit proces převzetí služeb při selhání pro váš účet úložiště, pokud primární koncový bod nebude k dispozici. Převzetí služeb při selhání aktualizuje sekundární koncový bod tak, aby se stal primárním koncovým bodem pro váš účet úložiště. Až se převzetí služeb při selhání dokončí, můžou klienti začít zapisovat do nového primárního koncového bodu.

Tento článek popisuje koncepty a procesy spojené s převzetím služeb při selhání a popisuje, jak připravit účet úložiště k obnovení s minimálním dopadem na zákazníky. Informace o tom, jak iniciovat převzetí služeb při selhání účtu v Azure Portal nebo PowerShellu, najdete v tématu o [inicializaci převzetí služeb při selhání (Preview)](storage-initiate-account-failover.md).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Zvolit správnou možnost redundance

Všechny účty úložiště se replikují pro redundanci. Jakou možnost redundance zvolíte pro svůj účet závisí na stupni odolnosti, kterou potřebujete. Z důvodu ochrany před místními výpadky vyberte geograficky redundantní úložiště s možností přístupu pro čtení ze sekundární oblasti nebo bez něj:  

**Geograficky redundantní úložiště (GRS)** replikuje vaše data asynchronně ve dvou geografických oblastech, které mají od sebe aspoň stovky kilometrů. Pokud primární oblast zpomaluje výpadek, pak sekundární oblast slouží jako redundantní zdroj dat. Můžete iniciovat převzetí služeb při selhání a transformovat sekundární koncový bod do primárního koncového bodu.

**Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)** poskytuje geograficky redundantní úložiště s dodatečnou výhodou přístupu pro čtení sekundárního koncového bodu. Pokud v primárním koncovém bodě dojde k výpadku, můžou aplikace nakonfigurované pro RA-GRS a určené pro vysokou dostupnost pokračovat v čtení ze sekundárního koncového bodu. Microsoft doporučuje RA-GRS pro maximální odolnost vašich aplikací.

Mezi další Azure Storage možnosti redundance patří úložiště redundantní v zóně (ZRS), které replikuje vaše data napříč zónami dostupnosti v jedné oblasti a místně redundantní úložiště (LRS), které replikuje vaše data v jednom datovém centru v jedné oblasti. Pokud je váš účet úložiště nakonfigurovaný pro ZRS nebo LRS, můžete tento účet převést na použití GRS nebo RA-GRS. Konfigurace účtu pro geograficky redundantní úložiště má za následek další náklady. Další informace najdete v tématu [replikace Azure Storage](storage-redundancy.md).

> [!NOTE]
> Geograficky redundantní úložiště (GZRS) a geograficky redundantní úložiště s přístupem pro čtení (RA-GZRS) jsou momentálně ve verzi Preview, ale ještě nejsou dostupné ve stejných oblastech jako převzetí služeb při selhání účtů spravovaných zákazníkem. Z tohoto důvodu zákazníci momentálně nemůžou spravovat události převzetí služeb při selhání účtu s GZRS a RA-GZRS účty. V průběhu verze Preview bude společnost Microsoft spravovat všechny události převzetí služeb při selhání, které mají vliv na účty GZRS/RA-GZRS.

> [!WARNING]
> Geograficky redundantní úložiště přináší riziko ztráty dat. Data se replikují do sekundární oblasti asynchronně, což znamená, že mezi daty zapsanými do primární oblasti dojde k zápisu do sekundární oblasti. V případě výpadku dojde ke ztrátě operací zápisu do primárního koncového bodu, které ještě nebyly replikovány do sekundárního koncového bodu.

## <a name="design-for-high-availability"></a>Návrh pro zajištění vysoké dostupnosti

Je důležité navrhnout aplikaci pro zajištění vysoké dostupnosti od začátku. Pokyny pro návrh aplikace a plánování zotavení po havárii najdete v těchto prostředcích Azure:

* [Navrhování odolných aplikací pro Azure](https://docs.microsoft.com/azure/architecture/resiliency/): Přehled klíčových konceptů pro navrhování vysoce dostupných aplikací v Azure.
* [Kontrolní seznam dostupnosti](https://docs.microsoft.com/azure/architecture/checklist/availability): Kontrolní seznam pro ověření, že vaše aplikace implementuje osvědčené postupy návrhu pro vysokou dostupnost.
* [Návrh aplikací s vysokou dostupností pomocí RA-GRS](storage-designing-ha-apps-with-ragrs.md): Pokyny k návrhu pro vytváření aplikací, které využívají výhod RA-GRS.
* [Kurz: Vytvoření vysoce dostupné aplikace s úložištěm](../blobs/storage-create-geo-redundant-storage.md)objektů BLOB: Kurz, ve kterém se dozvíte, jak vytvořit vysoce dostupnou aplikaci, která automaticky přepíná mezi koncovými body v podobě selhání a jsou simulovaná obnovení. 

Kromě toho mějte na paměti tyto osvědčené postupy pro udržení vysoké dostupnosti dat Azure Storage:

* **Disků** Použijte [Azure Backup](https://azure.microsoft.com/services/backup/) k zálohování disků virtuálních počítačů využívaných virtuálními počítači Azure. Zvažte také použití [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) k ochraně vašich virtuálních počítačů v případě regionálních havárií.
* **Objekty blob bloku:** Zapněte [obnovitelné odstranění](../blobs/storage-blob-soft-delete.md) pro ochranu proti odstranění na úrovni objektu a přepsání nebo zkopírujte objekty blob bloku do jiného účtu úložiště v jiné oblasti pomocí [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)nebo [knihovny pro přesun dat Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* **Spis** Pomocí [AzCopy](storage-use-azcopy.md) nebo [Azure PowerShell](storage-powershell-guide-full.md) zkopírujte soubory do jiného účtu úložiště v jiné oblasti.
* **Tabulky:** pomocí [AzCopy](storage-use-azcopy.md) můžete exportovat data tabulky do jiného účtu úložiště v jiné oblasti.

## <a name="track-outages"></a>Sledovat výpadky

Zákazníci se můžou přihlásit k odběru [řídicího panelu Azure Service Health](https://azure.microsoft.com/status/) , aby mohli sledovat stav a stav Azure Storage a dalších služeb Azure.

Microsoft také doporučuje navrhovat aplikace, aby se připravila možnost selhání zápisu. Vaše aplikace by měla vystavovat chyby při psaní způsobem, který vás upozorní na možnost výpadku v primární oblasti.

## <a name="understand-the-account-failover-process"></a>Pochopení procesu převzetí služeb při selhání účtu

Převzetí služeb při selhání účtu spravovaného zákazníkem (ve verzi Preview) vám umožní převzít služby při selhání celého účtu úložiště do sekundární oblasti, pokud primární z nějakého důvodu nebude k dispozici. Když vynutíte převzetí služeb při selhání sekundární oblastí, můžou klienti po dokončení převzetí služeb při selhání začít zapisovat data do sekundárního koncového bodu. Převzetí služeb při selhání obvykle trvá přibližně hodinu.

### <a name="how-an-account-failover-works"></a>Jak funguje převzetí služeb při selhání účtu

Za normálních okolností klient zapisuje data do účtu Azure Storage v primární oblasti a tato data se replikují asynchronně do sekundární oblasti. Následující obrázek znázorňuje situaci, kdy je primární oblast k dispozici:

![Klienti zapisují data do účtu úložiště v primární oblasti.](media/storage-disaster-recovery-guidance/primary-available.png)

Pokud z nějakého důvodu nebude primární koncový bod k dispozici, klient už nebude moct zapisovat do účtu úložiště. Na následujícím obrázku je znázorněn scénář, ve kterém primární událost není k dispozici, ale ještě neproběhlo žádné obnovení:

![Primární není k dispozici, takže klienti nemůžou zapisovat data.](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

Zákazník zahájí převzetí služeb při selhání účtu sekundárním koncovým bodem. Proces převzetí služeb při selhání aktualizuje položku DNS, kterou poskytuje Azure Storage, aby se sekundární koncový bod stal novým primárním koncovým bodem pro váš účet úložiště, jak je znázorněno na následujícím obrázku:

![Zákazník iniciuje převzetí služeb při selhání účtem sekundárního koncového bodu.](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

Jakmile se položka DNS aktualizuje a požadavky se přesměrují do nového primárního koncového bodu, pro účty GRS a RA-GRS se obnoví přístup pro zápis. Stávající koncové body služby úložiště pro objekty blob, tabulky, fronty a soubory zůstávají po převzetí služeb při selhání stejné.

> [!IMPORTANT]
> Po dokončení převzetí služeb při selhání se účet úložiště nakonfiguruje na místně redundantní v novém primárním koncovém bodu. Pokud chcete obnovit replikaci do nového sekundárního počítače, nakonfigurujte účet tak, aby znovu používal geograficky redundantní úložiště (buď RA-GRS nebo GRS).
>
> Mějte na paměti, že převod účtu LRS na RA-GRS nebo GRS stojí za cenu. Tato cena se vztahuje na aktualizaci účtu úložiště v nové primární oblasti tak, aby po převzetí služeb při selhání používala RA-GRS nebo GRS.  

### <a name="anticipate-data-loss"></a>Předvídání ztráty dat

> [!CAUTION]
> Převzetí služeb při selhání účtu obvykle zahrnuje určitou ztrátu dat. Je důležité pochopit důsledky zahájení převzetí služeb při selhání účtu.  

Vzhledem k tomu, že data jsou zapsána asynchronně z primární oblasti do sekundární oblasti, je vždy zpoždění před tím, než je zápis do primární oblasti replikován do sekundární oblasti. Pokud primární oblast nebude k dispozici, nejaktuálnější zápisy ještě nemusí být replikovány do sekundární oblasti.

Když vynutíte převzetí služeb při selhání, všechna data v primární oblasti se ztratí, protože se sekundární oblast stala novou primární oblastí a účet úložiště se nakonfiguruje jako místně redundantní. Všechna data, která se už replikují do sekundárního, se při převzetí služeb při selhání uchovávají. Veškerá data zapsaná na primární, která nebyla také replikována do sekundárního, se však trvale ztratí. 

Vlastnost **čas poslední synchronizace** označuje poslední čas, po který je zaručeno, že data z primární oblasti budou zapsána do sekundární oblasti. Všechna data zapsaná před časem poslední synchronizace jsou k dispozici na sekundárním počítači, zatímco data zapsaná po poslední synchronizaci nemusí být zapsána do sekundárního a mohou být ztracena. Tuto vlastnost použijte v případě výpadku k odhadu množství ztráty dat, která se může vyvolávat při převzetí služeb při selhání účtu. 

Osvědčeným postupem je navrhnout aplikaci tak, aby bylo možné použít čas poslední synchronizace k vyhodnocení očekávané ztráty dat. Pokud například protokoluje všechny operace zápisu, můžete porovnat čas poslední operace zápisu s časem poslední synchronizace a určit, které zápisy nebyly synchronizovány do sekundárního.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Při navrácení služeb po obnovení původní primární služby postupujte opatrně.

Po převzetí služeb při selhání z primárního umístění do sekundární oblasti je váš účet úložiště nakonfigurovaný místně redundantní v nové primární oblasti. Účet pro geografickou redundanci můžete znovu nakonfigurovat tak, že ho aktualizujete tak, aby používal GRS nebo RA-GRS. Když je účet nakonfigurovaný pro geografickou redundanci znovu po převzetí služeb při selhání, nová primární oblast okamžitě zahájí replikaci dat do nové sekundární oblasti, která byla primární před původní převzetí služeb při selhání. Může ale nějakou dobu trvat, než se stávající data v primárním čase replikují do nového sekundárního.

Po překonfigurování účtu úložiště pro geografickou redundanci je možné zahájit jiné převzetí služeb při selhání z nové primární služby zpátky do nového sekundárního objektu. V takovém případě se původní primární oblast před převzetím služeb při selhání znovu vytvoří v primární oblasti a nakonfiguruje se tak, aby byla místně redundantní. Všechna data v primární oblasti po převzetí služeb při selhání (původní sekundární) se pak ztratí. Pokud před navrácením služeb po obnovení není většina dat v účtu úložiště replikována do nového sekundárního, může dojít k výrazné ztrátě dat. 

Chcete-li se vyhnout zásadní ztrátě dat, před navrácením služeb po obnovení ověřte hodnotu vlastnosti **čas poslední synchronizace** . Porovnejte čas poslední synchronizace s časem, kdy byla data zapsána do nové primární pro vyhodnocení očekávané ztráty dat. 

## <a name="initiate-an-account-failover"></a>Zahájení převzetí služeb při selhání účtu

Převzetí služeb při selhání účtu můžete iniciovat z rozhraní API Azure Portal, PowerShellu, Azure CLI nebo poskytovatele prostředků Azure Storage. Další informace o tom, jak iniciovat převzetí služeb při selhání, najdete v tématu o inicializaci převzetí služeb při [selhání (Preview)](storage-initiate-account-failover.md).

## <a name="about-the-preview"></a>O verzi Preview

převzetí služeb při selhání účtu je dostupné ve verzi Preview pro všechny zákazníky, kteří používají GRS nebo RA-GRS s nasazeními Azure Resource Manager. Podporují se typy účtů pro obecné účely V1, obecné účely v2 a BLOB Storage. převzetí služeb při selhání účtu je aktuálně dostupné v těchto oblastech:

- USA – západ 2
- USA – středozápad

Verze Preview je určena pouze pro neprodukční použití. Smlouvy o úrovni produkčních služeb (SLA) nejsou aktuálně k dispozici.

### <a name="register-for-the-preview"></a>Zaregistrovat se pro verzi Preview

Pokud se chcete zaregistrovat ve verzi Preview, spusťte v PowerShellu následující příkazy. Zástupný symbol v závorkách nahraďte vlastním ID předplatného:

```powershell
Connect-AzAccount -SubscriptionId <subscription-id>
Register-AzProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

Získání schválení pro verzi Preview může trvat 1-2 dní. Chcete-li ověřit, zda byla registrace schválena, spusťte následující příkaz:

```powershell
Get-AzProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

### <a name="additional-considerations"></a>Další aspekty 

Další informace popsané v této části vám pomohou pochopit, jak můžou být vaše aplikace a služby ovlivněné při vynucení převzetí služeb při selhání během období Preview.

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

Virtuální počítače Azure při převzetí služeb při selhání v rámci účtu převezmou služby při selhání. Pokud primární region přestane být k dispozici a převezmete služby při selhání do sekundární oblasti, budete muset po převzetí služeb při selhání znovu vytvořit všechny virtuální počítače. 

#### <a name="azure-unmanaged-disks"></a>Nespravované disky Azure

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

### <a name="unsupported-features-or-services"></a>Nepodporované funkce nebo služby
Pro převzetí služeb při selhání účtu verze Preview nejsou podporované tyto funkce nebo služby:

- Azure File Sync nepodporuje převzetí služeb při selhání účtu úložiště. Účty úložiště obsahující sdílené složky Azure, které se používají jako koncové body cloudu v Azure File Sync by neměly přenášet služby při selhání. Tím dojde k tomu, že synchronizace přestane fungovat a může také způsobit neočekávanou ztrátu dat v případě nově vrstvených souborů.  
- Účet úložiště obsahující archivované objekty blob nejde převzít služby při selhání. Udržujte archivované objekty BLOB v samostatném účtu úložiště, u kterých neplánujete převzít služby při selhání.
- Nepovedlo se převzít služby účtů úložiště obsahující objekty blob bloku Premium. Účty úložiště, které podporují objekty blob bloku Premium, v současné době nepodporují geografickou redundanci.
- Po dokončení převzetí služeb při selhání přestane následující funkce fungovat, pokud jsou původně povolené: [Odběry událostí](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-overview), [Zásady životního cyklu](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts)a [protokolování analýza úložiště](https://docs.microsoft.com/rest/api/storageservices/about-storage-analytics-logging).

## <a name="copying-data-as-an-alternative-to-failover"></a>Kopírování dat jako alternativu k převzetí služeb při selhání

Pokud je váš účet úložiště nakonfigurovaný pro RA-GRS, máte k datům přístup pro čtení pomocí sekundárního koncového bodu. Pokud v případě výpadku v primární oblasti nechcete převzít služby při selhání, můžete pomocí nástrojů, jako jsou [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)nebo [knihovny pro přesun dat Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) , kopírovat data z účtu úložiště v sekundární oblasti do jiné. účet úložiště v neovlivněné oblasti. Pak můžete své aplikace nasměrovat na tento účet úložiště pro čtení i zápis.

## <a name="microsoft-managed-failover"></a>Převzetí služeb při selhání spravované Microsoftem

V extrémních situacích, kdy dojde ke ztrátě oblasti z důvodu významné havárie, může společnost Microsoft zahájit místní převzetí služeb při selhání. V takovém případě není nutná žádná akce s vaší částí. Dokud neproběhne převzetí služeb při selhání spravované Microsoftem, nebudete mít k účtu úložiště přístup pro zápis. Vaše aplikace se můžou číst ze sekundární oblasti, pokud je váš účet úložiště nakonfigurovaný pro RA-GRS. 

## <a name="see-also"></a>Viz také:

* [Iniciovat převzetí služeb při selhání účtu (Preview)](storage-initiate-account-failover.md)
* [Návrh aplikací s vysokou dostupností pomocí RA-GRS](storage-designing-ha-apps-with-ragrs.md)
* [Kurz: Vytvoření vysoce dostupné aplikace s úložištěm objektů BLOB](../blobs/storage-create-geo-redundant-storage.md) 
