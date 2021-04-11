---
title: Průvodce migrací Azure Storage
description: Průvodce přehledem migrace úložiště popisuje základní pokyny k migraci úložiště.
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: common
ms.openlocfilehash: f6f00075c7c66679281d776f9472ec4a1a590d76
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231472"
---
# <a name="azure-storage-migration-overview"></a>Přehled migrace Azure Storage

Tento článek se zaměřuje na migrace úložiště do Azure a poskytuje pokyny k následujícím scénářům migrace úložiště:

- Migrace nestrukturovaných dat, jako jsou soubory a objekty
- Migrace zařízení založených na blokování, jako jsou disky a sítě SAN (Storage Area Network)

## <a name="migration-of-unstructured-data"></a>Migrace nestrukturovaných dat

Migrace nestrukturovaných dat zahrnuje následující scénáře:

- Migrace souborů z připojeného úložiště k síti (NAS) do jedné z nabídek Azure File:
  - [Azure Files](https://azure.microsoft.com/services/storage/files/)
  - [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)
  - [řešení nezávislého výrobce softwaru (ISV)](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview).
- Migrace objektů z řešení úložiště objektů na platformu úložiště objektů Azure:
  - [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)
  - [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

### <a name="migration-phases"></a>Fáze migrace

Úplná migrace se skládá z několika různých fází: zjišťování, posouzení a migrace.

![Diagram znázorňující fáze zjišťování, posouzení a migrace migrace](./media/storage-migration-overview/migration-phases.png)

#### <a name="discovery-phase"></a>Fáze zjišťování

Ve fázi zjišťování určíte všechny zdroje, které je třeba migrovat jako sdílené složky SMB, soubory NFS exporty nebo obory názvů objektů. Tuto fázi můžete provést ručně nebo pomocí automatizovaných nástrojů.

#### <a name="assessment-phase"></a>Fáze posouzení

Fáze posouzení je pro porozumění dostupným možnostem migrace kritická. Chcete-li snížit riziko při migraci a vyhnout se běžným nástrah, postupujte podle těchto tří kroků:

| Kroky fáze posouzení                     | Možnosti                                                                          |
|--------------------------------------------|----------------------------------------------------------------------------------|
| **Zvolit cílovou službu úložiště**            | – Azure Blob Storage a Data Lake Storage<br>– Soubory Azure<br>-Azure NetApp Files<br>– Řešení ISV |
| **Vyberte metodu migrace**                  | – Online<br>– Offline<br> – Kombinace obou                                  |
| **Výběr nejlepšího nástroje pro migraci pro úlohu** | – Komerční nástroje (Azure a ISV)<br> -Open Source                             


Ke fázi posouzení může pomáhat několik komerčních nástrojů (ISV). Viz [srovnávací tabulka](../solution-integration/validated-partners/data-management/migration-tools-comparison.md).

##### <a name="choose-a-target-storage-service"></a>Zvolit cílovou službu úložiště

Výběr cílové služby úložiště závisí na aplikaci nebo uživatelích, kteří přistupují k datům. Správná volba závisí na technických i finančních aspektech. Nejprve proveďte technické posouzení, které vyhodnotí možné cíle a určí, které služby splňují požadavky. Dále proveďte finanční vyhodnocení a určete nejlepší volbu.

Aby bylo možné vybrat cílovou službu úložiště pro migraci, vyhodnoťte následující aspekty každé služby:

- Podpora protokolu
- Charakteristiky výkonu
- Omezení cílové služby úložiště

Následující diagram je zjednodušený rozhodovací strom, který vám pomůže s doporučovanou souborovou službou Azure. Pokud nativní služby Azure nesplňují požadavky, celá řada [řešení nezávislých výrobců softwaru (ISV)](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview) bude.

Po dokončení technického posouzení a výběru správného cíle proveďte vyhodnocení nákladů a určete nejefektivnější možnost.

![Základní rozhodovací strom pro výběr správné souborové služby](./media/storage-migration-overview/files-decision-tree.png)

Pro zajištění jednoduchého rozhodovacího stromu není v diagramu zabudovaná omezení cílové služby úložiště. Chcete-li zjistit více o aktuálních omezeních a určit, zda je třeba změnit možnosti na základě nich, přečtěte si téma:

- [Omezení účtu úložiště](/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)
- [Omezení Blob Storage](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-blob-storage-limits)
- [Škálovatelnost a cíle výkonnosti služby Azure Files](/azure/storage/files/storage-files-scale-targets)
- [Azure NetApp Files omezení prostředků](/azure/azure-netapp-files/azure-netapp-files-resource-limits)

Pokud některý z omezení představuje blokování pro používání služby, Azure podporuje několik dodavatelů úložiště, kteří nabízejí řešení na Azure Marketplace. Informace o ověřovaných partnerech ISV, kteří poskytují souborové služby, najdete v tématu [Azure Storage partneři pro primární a sekundární úložiště](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview).

##### <a name="select-the-migration-method"></a>Vyberte metodu migrace

Migrace úložiště mají dvě základní metody migrace.

- **Online**. Online metoda používá síť k migraci dat. Můžete použít veřejný Internet nebo [Azure ExpressRoute](/azure/expressroute/expressroute-introduction) . Pokud služba nemá veřejný koncový bod, musíte použít síť VPN s veřejným internetem.
- **Stav.** Metoda offline používá jedno z [Azure Data box](https://azure.microsoft.com/services/databox/) zařízení.

Rozhodnutí o použití online metody oproti metodě offline závisí na dostupné šířce pásma sítě. Online metoda je upřednostňována v případech, kdy je dostatečná šířka pásma sítě k provedení migrace v rámci potřebné časové osy.

Pro přírůstkovou migraci změn je možné použít kombinaci obou metod, offline metody pro prvotní hromadnou migraci a online metodu. Použití obou metod současně vyžaduje vysokou úroveň koordinace a z tohoto důvodu se nedoporučuje. Pokud se rozhodnete použít obě metody, izolujte datové sady, které jsou migrovány online, ze datových sad, které jsou migrovány offline.

Další informace o různých metodách migrace a pokyny najdete v tématu [Volba řešení Azure pro přenos dat](/azure/storage/common/storage-choose-data-transfer-solution) a [migrace do sdílených složek Azure](/azure/storage/files/storage-files-migration-overview).

##### <a name="choose-the-best-migration-tool-for-the-job"></a>Výběr nejlepšího nástroje pro migraci pro úlohu

Existují různé nástroje pro migraci, které můžete použít k provedení migrace. Některé jsou otevřené zdroje, například AzCopy, Robocopy, xcopy a rsync, zatímco ostatní jsou komerční. Seznam dostupných komerčních nástrojů a jejich porovnání je k dispozici v naší [srovnávací](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)tabulce.

Open Source nástroje jsou vhodné pro krátké migrace. Pro migraci ze souborových serverů Windows do souborů Azure doporučuje Microsoft začít používat nativní funkce Azure Files a využívat [synchronizace souborů Azure](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-file-sync). Pro komplexnější migrace skládající se z různých zdrojů, velké kapacity nebo zvláštních požadavků, jako je omezování nebo podrobnější generování sestav s možnostmi auditu, se jedná o nejlepší volbu komerčních nástrojů. Tyto nástroje usnadňují migraci a výrazně snižují riziko. Většina komerčních nástrojů může také provádět zjišťování, které poskytuje cenné vstupy pro posouzení.

#### <a name="migration-phase"></a>Fáze migrace

Fáze migrace je finální krok migrace, který provádí přesun a migraci dat. Obvykle provedete několikrát prostřednictvím fáze migrace, abyste mohli provést snazší přepínání. Fáze migrace se skládá z následujících kroků:

1. **Počáteční migrace.** Krok počáteční migrace migruje všechna data ze zdroje do cíle. Tento krok migruje Hromadná data, která je třeba migrovat.
2. **Resynchronizace.** Operace opětovné synchronizace migruje všechna data, která byla změněna po kroku prvotní migrace. Tento krok můžete opakovat několikrát, pokud dojde k mnoha změnám. Cílem spuštění více operací opětovné synchronizace je zkrátit dobu potřebnou pro poslední krok. V případě neaktivních dat a dat, která neobsahují žádné změny (třeba data zálohování nebo archivu), můžete tento krok přeskočit.
3. **Poslední přepnutí** Poslední krok přepnutí přepíná aktivní použití dat ze zdroje k cíli a vymění zdroj.

Doba trvání migrace nestrukturovaných dat závisí na několika aspektech. Mimo zvolenou metodu jsou nejdůležitější faktory celková velikost dat a distribuce velikosti souborů. Čím větší je celková datová sada, tím déle bude doba migrace. Čím menší je průměrná velikost souboru, tím déle bude doba migrace. Pokud máte velký počet malých souborů, zvažte jejich archivaci do větších souborů (například souboru. tar nebo. zip), aby se snížila celková doba migrace.

## <a name="migration-of-block-based-devices"></a>Migrace zařízení založených na blokování

Migrace zařízení založených na blocích se obvykle provádí jako součást migrace virtuálního počítače nebo fyzického hostitele. Je to běžný nepojmový koncept, který umožňuje zpozdit rozhodování o úložištích až po migraci. Díky vhodným faktorům pro splnění požadavků na úlohy vede k plynulé migraci do cloudu, aby tato rozhodnutí byla předem v průběhu času.

Pokud chcete prozkoumat úlohy pro migraci a přístup k nim, přečtěte si [dokumentaci Azure Disk Storage](/azure/virtual-machines/disks-types)a materiály na [stránce Disk Storage produktu](https://azure.microsoft.com/services/storage/disks/#resources). Můžete získat informace o tom, které disky vyhovují vašim požadavkům, a nejnovější možnosti, jako je například [diskové shlukování](/azure/virtual-machines/disk-bursting). Informace o tom, jak migrovat virtuální počítače spolu se základními zařízeními založenými na blocích, najdete v dokumentaci k [Azure Migrate](/azure/migrate/) .

## <a name="see-also"></a>Viz také

- [Výběr řešení Azure pro přenos dat](/azure/storage/common/storage-choose-data-transfer-solution)
- [Srovnání komerčních nástrojů pro migraci](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)
- [Migrace do sdílených složek Azure](/azure/storage/files/storage-files-migration-overview)
- [Migrace na Data Lake Storage s platformou WANdisco LiveData pro Azure](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Kopírování nebo přesun dat Azure Storage pomocí AzCopy](https://aka.ms/azcopy)
- [Migrace velkých datových sad do Azure Blob Storage pomocí AzReplicate](https://github.com/Azure/AzReplicate/tree/master/)