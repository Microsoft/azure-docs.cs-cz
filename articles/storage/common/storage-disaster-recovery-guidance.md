---
title: Po havárii pro obnovení a úložiště účtu převzetí služeb při selhání (preview) – služby Azure Storage
description: Azure Storage podporuje účet převzetí služeb při selhání (preview) pro účty geograficky redundantní úložiště. Pomocí účtu převzetí služeb při selhání můžete zahájit procesu převzetí služeb při selhání pro váš účet úložiště, pokud primární koncový bod nebude dostupný.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/30/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: b203acefb962d5b3a782ba0ce1e667b6f18b7951
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508925"
---
# <a name="disaster-recovery-and-storage-account-failover-preview-in-azure-storage"></a>Po havárii pro obnovení a úložiště účtu převzetí služeb při selhání (preview) ve službě Azure Storage

Cílem Microsoftu je zasloužit ujistit, že služby Azure jsou vždy k dispozici. Však může dojít, neplánované výpadky. Pokud vaše aplikace vyžaduje odolnost proti chybám, Microsoft doporučuje používat geograficky redundantní úložiště, aby vaše data se replikují do druhé oblasti. Kromě toho Zákazníci by měli připravený pro zpracování výpadku oblasti služeb plán zotavení po havárii. Důležitou součástí plánu zotavení po havárii se připravuje na převzetí služeb při selhání do sekundárního koncového bodu v případě, že primární koncový bod nebude dostupný. 

Azure Storage podporuje účet převzetí služeb při selhání (preview) pro účty geograficky redundantní úložiště. Pomocí účtu převzetí služeb při selhání můžete zahájit procesu převzetí služeb při selhání pro váš účet úložiště, pokud primární koncový bod nebude dostupný. Aktualizuje převzetí služeb při selhání sekundární koncový bod pro stát primární koncový bod vašeho účtu úložiště. Po dokončení převzetí služeb klientům můžete začít psát na novou primární koncový bod.

Tento článek popisuje koncepty a proces se účtu převzetí služeb při selhání a popisuje, jak můžete připravit obnovení s minimální množství dopad pro zákazníka v účtu úložiště. Další spuštění účet převzetí služeb při selhání webu Azure portal nebo Powershellu najdete v tématu [zahájit účtu převzetí služeb při selhání (preview)](storage-initiate-account-failover.md).

## <a name="choose-the-right-redundancy-option"></a>Zvolte možnost správné redundance

Všechny účty úložiště se replikují pro zajištění redundance. Zvolené možnosti redundance, zvolte pro svůj účet, závisí na úroveň odolnosti, které potřebujete. Pro ochranu před regionální výpadky zvolte geograficky redundantní úložiště s nebo bez možnosti čtení ze sekundární oblasti:  

**Geograficky redundantní úložiště (GRS)** replikuje data asynchronně ve dvou geografických oblastí, které jsou alespoň stovky mil. Pokud primární oblast odkážete výpadku, sekundární oblasti slouží jako záložní zdroj pro vaše data. Můžete spustit převzetí služeb při selhání do sekundárního koncového bodu transformovat na primární koncový bod.

**Geograficky redundantní úložiště jen pro čtení (RA-GRS)** poskytuje další výhody přístup pro čtení na sekundární koncový bod geograficky redundantní úložiště. V případě výpadku primární koncový bod aplikace nakonfigurována pro RA-GRS a navržené pro zajištění vysoké dostupnosti můžete nadále číst ze sekundárního koncového bodu. Společnost Microsoft doporučuje pro maximální odolnost proti chybám pro vaše aplikace RA-GRS.

Další možnosti redundance Azure Storage zahrnují zónově redundantní úložiště (ZRS), která replikuje vaše data napříč zónami dostupnosti v jedné oblasti a místně redundantní úložiště (LRS), která replikuje vaše data do jednoho datového centra v jedné oblasti. Pokud váš účet úložiště je nakonfigurovaný pro ZRS nebo LRS, můžete převést tento účet se má použít GRS nebo RA-GRS. Konfigurace účtu pro geograficky redundantní úložiště s sebou nese náklady spojené další náklady. Další informace najdete v tématu [replikace Azure Storage](storage-redundancy.md).

> [!WARNING]
> Geograficky redundantní úložiště s sebou nese riziko ztráty. Data se replikují do sekundární oblasti asynchronně, což znamená, že dochází ke zpoždění mezi při zápisu dat zapsaných do primární oblasti do sekundární oblasti. V případě výpadku zápisu na primární koncový bod operací, které nebyly dosud replikovány na sekundární koncový bod se ztratí. 

## <a name="design-for-high-availability"></a>Návrh pro zajištění vysoké dostupnosti

Je důležité při návrhu vaší aplikace pro zajištění vysoké dostupnosti od začátku. Přečtěte si na tyto prostředky Azure, kde najdete pokyny k návrhu vaší aplikace a plánování zotavení po havárii:

* [Návrh odolných aplikací pro Azure](https://docs.microsoft.com/azure/architecture/resiliency/): Přehled klíčových konceptů pro navrhování aplikací s vysokou dostupností v Azure.
* [Kontrolní seznam k dostupnosti](https://docs.microsoft.com/azure/architecture/checklist/availability): Kontrolní seznam pro ověření, že vaše aplikace implementuje osvědčené postupy pro navrhování pro vysokou dostupnost.
* [Navrhování aplikací s vysokou dostupností pomocí RA-GRS](storage-designing-ha-apps-with-ragrs.md): Návrh pokyny pro vytváření aplikací, abyste mohli využívat RA-GRS.
* [Kurz: Sestavení aplikace s vysokou dostupností s úložištěm objektů Blob](../blobs/storage-create-geo-redundant-storage.md): Kurz, který ukazuje, jak vytvářet aplikace s vysokou dostupností, která automaticky přepne mezi koncovými body jako selhání a obnovení jsou simulované. 

Kromě toho mějte na paměti tyto osvědčené postupy pro zachování vysoké dostupnosti pro vaše data služby Azure Storage:

* **Disky:** Použití [Azure Backup](https://azure.microsoft.com/services/backup/) zálohování disků virtuálních počítačů používají virtuální počítače Azure. Také zvážit použití [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) k ochraně vašich virtuálních počítačů v případě regionálního.
* **Objekty BLOB bloku:** Zapnout [obnovitelné odstranění](../blobs/storage-blob-soft-delete.md) k ochraně proti odstranění na úrovni objektů a přepíše nebo zkopírovat objekty BLOB bloku do jiného účtu úložiště v jiné oblasti pomocí [AzCopy](storage-use-azcopy.md), [prostředí Azure PowerShell ](storage-powershell-guide-full.md), nebo [knihovna pro přesun dat Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* **Soubory:** Použití [AzCopy](storage-use-azcopy.md) nebo [prostředí Azure PowerShell](storage-powershell-guide-full.md) kopírování souborů do jiného účtu úložiště v jiné oblasti.
* **Tabulky:** použít [AzCopy](storage-use-azcopy.md) exportovat data tabulky do jiného účtu úložiště v jiné oblasti.

## <a name="track-outages"></a>Sledovat výpadků

Zákazníci mohou přihlásit k odběru [řídicí panel stavu služby Azure](https://azure.microsoft.com/status/) ke sledování stavu a stavu služby Azure Storage a dalšími službami Azure.

Microsoft také doporučuje navrhnout aplikaci Příprava možnost chyby zápisu. Vaše aplikace by měly vystavit chyby zápisu tak, aby vás upozorní na možnost výpadku v primární oblasti.

## <a name="understand-the-account-failover-process"></a>Účet procesu převzetí služeb při selhání

Převzetí služeb při selhání účtu spravovaného zákazníkem (preview) umožňuje převzít vaše celý účet úložiště do sekundární oblasti, pokud z nějakého důvodu nedostupný primární. Při vynucení převzetí služeb při selhání do sekundární oblasti, můžete začít klienti zápis dat do sekundárního koncového bodu po dokončení převzetí služeb. Převzetí služeb při selhání trvá obvykle zhruba do hodiny.

### <a name="how-an-account-failover-works"></a>Jak funguje účtem převzetí služeb při selhání

Za normálních okolností klient zapisuje data do účtu služby Azure Storage v primární oblasti a tato data se asynchronně replikuje do sekundární oblasti. Následující obrázek ukazuje scénář, když primární oblast je k dispozici:

![Klienti zapisovat data do účtu úložiště v primární oblasti](media/storage-disaster-recovery-guidance/primary-available.png)

Pokud z nějakého důvodu nedostupný primární koncový bod, klient je již nemůže zapisovat do účtu úložiště. Následující obrázek ukazuje scénář, kdy primární má k dispozici, ale bez obnovení ještě nedošlo k:

![Primární není k dispozici, aby klienti nelze zapisovat data](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

Zákazník zahájí účet převzetí služeb při selhání do sekundárního koncového bodu. Proces převzetí služeb při selhání aktualizuje položku DNS poskytuje Azure Storage tak, aby sekundární koncový bod se stane nová primární koncový bod vašeho účtu úložiště, jak je znázorněno na následujícím obrázku:

![Zákazník iniciuje účet převzetí služeb při selhání do sekundárního koncového bodu](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

Přístup pro zápis se obnoví pro účty GRS a RA-GRS, jakmile byla aktualizována položka DNS a požadavky jsou nasměrování na nový primární koncový bod. Koncovými body existující úložiště pro objekty BLOB, tabulky, fronty a soubory nebudou měnit převzetí služeb při selhání.

> [!IMPORTANT]
> Po dokončení převzetí služeb při selhání je místně redundantní v nové primární koncový bod nakonfigurovaný účet úložiště. Obnovit na nový sekundární replikace, nakonfigurujte účet, který chcete použít geograficky redundantní úložiště znovu (RA-GRS nebo GRS).
>
> Mějte na paměti, že převod účet LRS a GRS RA-GRS, má náklady. Tyto náklady se vztahuje na aktualizace účtu úložiště v nové primární oblasti po převzetí služeb při použití RA-GRS nebo GRS.  

### <a name="anticipate-data-loss"></a>Příprava na vyčerpání ztráty dat

> [!CAUTION]
> Převzetí služeb při selhání účet obvykle zahrnuje ztrátu některých dat. Je důležité si uvědomit důsledky inicializaci účtu převzetí služeb při selhání.  

Protože data se zapisují asynchronně z primární oblasti do sekundární oblasti, je vždy zpoždění před zápis do primární oblasti se replikují do sekundární oblasti. Pokud primární oblast stane nedostupnou, nejnovější zápisy nemusí ještě replikovaly do sekundární oblasti.

Při vynucení převzetí služeb při selhání dojde ke ztrátě všech dat v primární oblasti, sekundární oblast stane nová primární oblast a účet úložiště je nakonfigurovaný jako místně redundantní. Všechna data již replikovány na sekundární se nepoužije, když se stane, převzetí služeb při selhání. Však žádná data zapsána do primární, která nebyla replikována také do sekundární dojde ke ztrátě trvale. 

**Čas poslední synchronizace** vlastnost označuje nejvíce času poslední, že data z primární oblasti je zaručeno, že byl zapsán do sekundární oblasti. Všechna data zapsaná před čas poslední synchronizace je k dispozici na sekundárním při data zapsaná po čas poslední synchronizace pravděpodobně byl zapsán do sekundární a mohou být ztraceny. Odhad množství ztráty dat, kterou případně utrpíte pomocí inicializace účtu převzetí služeb při selhání pomocí této vlastnosti v případě výpadku. 

Jako osvědčený postup Navrhněte svou aplikaci tak, aby čas poslední synchronizace můžete použít k vyhodnocení ztráty očekávaná data. Například pokud všechny operace zápisu se přihlašujete, pak ho můžete porovnat čas poslední operací zápisu na poslední synchronizaci času k určení, která se zapisuje nebyly synchronizovány do sekundární.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Buďte opatrní při selhání zpátky do původní databáze

Poté, co jste převzetí služeb při selhání z primární do sekundární oblasti, je na místně redundantní v nové primární oblasti nakonfigurovaný účet úložiště. Můžete nakonfigurovat účet pro geografickou redundancí znovu aktualizací používal GRS nebo RA-GRS. Pokud účet nakonfigurován pro geografickou redundancí znovu po převzetí služeb, začne nové primární oblasti okamžitě replikuje data do nové sekundární oblasti, která byla před původní převzetí služeb při selhání primární. Však může trvat určitou dobu před existujících dat v primárním je plně replikovat do nové sekundární lokality.

Po účet úložiště se zálohou pro geografickou redundanci, je možné zahájit jiného převzetí služeb při selhání nový primární zpět do nové sekundární lokality. V tomto případě původní primární oblast před převzetí služeb při selhání primární oblast opět a byl nakonfigurován jako místně redundantní. Všechna data v primární oblasti (původní sekundární) – příspěvek převzetí služeb při selhání pak ztratí. Pokud se většina dat v účtu úložiště nebyla replikována do nové sekundární lokality před navrácení služeb po obnovení, může být negativně ztrátu hlavní data. 

Aby nedošlo ke ztrátě hlavní data, zkontrolujte hodnotu **čas poslední synchronizace** vlastnost před navrácením služby po obnovení. Porovnat čas poslední synchronizace s posledním časy tato data byla zapsána do nové primární vyhodnotit ztráty očekávaná data. 

## <a name="initiate-an-account-failover"></a>Zahájit účtu převzetí služeb při selhání

Můžete spustit účtu převzetí služeb při selhání z webu Azure portal, Powershellu, rozhraní příkazového řádku Azure nebo rozhraní API poskytovatele prostředků služby Azure Storage. Další informace o tom, jak spustit převzetí služeb při selhání najdete v části [zahájit účtu převzetí služeb při selhání (preview)](storage-initiate-account-failover.md).

## <a name="about-the-preview"></a>Informace o verzi preview

účet převzetí služeb při selhání je dostupná ve verzi preview pro všechny zákazníky využívající GRS nebo RA-GRS se nasazení Azure Resource Manageru. Jsou podporovány pro obecné účely v1, v2 pro obecné účely a typech účtů úložiště Blob. účet převzetí služeb při selhání je teď dostupná v těchto oblastech:

- USA – západ 2
- USA – středozápad

Verzi preview je určeno pouze pro nevýrobní prostředí. Produkční smlouvy o úrovni služeb (SLA) nejsou aktuálně k dispozici.

### <a name="register-for-the-preview"></a>Registr ve verzi Preview

Chcete-li zaregistrovat verzi preview, spusťte následující příkazy v prostředí PowerShell. Ujistěte se, zda jste zástupný symbol v závorkách nahraďte vlastním ID předplatného:

```PowerShell
Connect-AzureRmAccount -SubscriptionId <subscription-id>
Register-AzureRmProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

To může trvat 1 – 2 dnů, která získala schválení pro verzi preview. K ověření, že vaše registrace byla schválena, spusťte následující příkaz:

```PowerShell
Get-AzureRmProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

### <a name="additional-considerations"></a>Další aspekty 

Přečtěte si další důležité informace, které jsou popsané v této části, abyste pochopili, jak vaše aplikace a služby mohou být ovlivněny vynutit převzetí služeb při selhání během období preview.

#### <a name="azure-virtual-machines"></a>Virtuální počítače Azure

Virtuální počítače Azure (VM) není převzetí služeb při selhání jako součást účet převzetí služeb při selhání. Pokud primární oblast stane nedostupnou a převzetí služeb při selhání do sekundární oblasti, budete muset znovu vytvořit všechny virtuální počítače po převzetí služeb. 

#### <a name="azure-unmanaged-disks"></a>Nespravované disky Azure

Jako osvědčený postup společnost Microsoft doporučuje převod nespravovaných disků na managed disks. Ale pokud je potřeba převzetí služeb při selhání účtu, který obsahuje nespravované disky připojené k virtuálním počítačům Azure, musíte vypnout virtuální počítač před inicializací převzetí služeb při selhání.

Nespravované disky jsou uložené jako objekty BLOB stránky ve službě Azure Storage. Když na virtuálním počítači běží v Azure, jsou zapůjčeny jakékoli nespravované disky připojené k virtuálnímu počítači. Účtu převzetí služeb při selhání nemůže pokračovat, když existuje zapůjčení objektu BLOB. Pokud chcete provést převzetí služeb při selhání, postupujte takto:

1. Než začnete, poznamenejte si názvy jakékoli nespravované disky, jejich logickým jednotkám (LUN) a virtuálních počítačů, ke kterému jsou připojené. To vám usnadní opětovné připojení disků po převzetí služeb. 
2. Vypněte virtuální počítač.
3. Odstranění virtuálního počítače, ale zachovat soubory virtuálního pevného disku pro nespravované disky. Poznámka: čas, kdy odstraníte virtuální počítač.
4. Počkejte, dokud **čas poslední synchronizace** byl aktualizován a je pozdější než čas, kdy odstraníte virtuální počítač. Tento krok je důležitý, protože pokud sekundární koncový bod nebyl aktualizován plně se soubory virtuálního pevného disku dojde převzetí služeb při selhání, pak virtuální počítač možná nebude fungovat správně v nové primární oblasti.
5. Zahájení převzetí služeb při selhání účtu.
6. Počkejte na dokončení převzetí služeb při selhání účtu a sekundární oblasti jsou nové primární oblasti.
7. Vytvoření virtuálního počítače v nové primární oblasti a znovu ho připojte virtuální pevné disky.
8. Spuštění nového virtuálního počítače.

Uvědomte si, že všechna data uložená v dočasný disk je ztraceny, pokud je virtuální počítač vypnutý.

#### <a name="azure-file-sync"></a>Synchronizace souborů Azure

Azure File Sync podporuje účet převzetí služeb při selhání. Je však potřeba znovu nakonfigurovat všechna nastavení Azure File Sync po dokončení převzetí služeb.

### <a name="unsupported-features-or-services"></a>Nepodporované funkce nebo služby

Následující funkce nebo služby se nepodporují u účtu převzetí služeb při selhání pro verzi preview:

- Azure Data Lake Storage Gen2 hierarchické sdílené složky nemůže být převzetí služeb při selhání.
- Účet úložiště obsahující archivované objekty BLOB nemůže být převzetí služeb při selhání. Udržujte archivované objektů BLOB v účtu samostatného úložiště, který nemáte v plánu převzít služby při selhání.
- Účet úložiště obsahující objekty BLOB bloku premium nejde převzít. Účty úložiště, které podporují objekty BLOB bloku premium aktuálně nepodporují geografickou redundancí.

## <a name="copying-data-as-an-alternative-to-failover"></a>Kopírování dat jako alternativu k převzetí služeb při selhání

Pokud je nakonfigurovaný účet úložiště za RA-GRS, pak máte přístup pro čtení k datům pomocí sekundárního koncového bodu. Pokud nechcete převzetí služeb při selhání v případě výpadku v primární oblasti, můžete pomocí nástrojů, jako [AzCopy](storage-use-azcopy.md), [prostředí Azure PowerShell](storage-powershell-guide-full.md), nebo [knihovna pro přesun dat Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) do kopírování dat z vašeho účtu úložiště v sekundární oblasti do jiného účtu úložiště v oblasti to neovlivní. Potom můžete odkazovat vaše aplikace na tento účet úložiště pro čtení a zápis dostupnosti.

## <a name="microsoft-managed-failover"></a>Spravovaná Microsoftem převzetí služeb při selhání

V extrémních případech kde oblast je ztraceno v důsledku významné po havárii může Microsoft zahájit regionální převzetí služeb při selhání. V takovém případě nemusíte vaší nic dělat. Až do dokončení převzetí služeb spravovaných microsoftem, nebudete mít přístup pro zápis do účtu úložiště. Vaše aplikace může číst ze sekundární oblasti, pokud je nakonfigurovaný účet úložiště za RA-GRS. 

## <a name="see-also"></a>Další informace najdete v tématech

* [Zahájit účtu převzetí služeb při selhání (preview)](storage-initiate-account-failover.md)
* [Návrh aplikací s vysokou dostupností pomocí RA-GRS](storage-designing-ha-apps-with-ragrs.md)
* [Kurz: Sestavení aplikace s vysokou dostupností s úložištěm objektů Blob](../blobs/storage-create-geo-redundant-storage.md) 
