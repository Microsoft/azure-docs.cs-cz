---
title: Migrace do sdílených složek Azure
description: Přečtěte si o migraci do sdílených složek Azure a vyhledejte Průvodce migrací.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 27056f39885949d52c9fcc0d1472033cfc8f9aa0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554866"
---
# <a name="migrate-to-azure-file-shares"></a>Migrace do sdílených složek Azure

Tento článek se zabývá základními aspekty migrace do sdílených složek Azure.

Tento článek obsahuje základní informace o migraci a tabulku průvodců migrací. Tyto příručky vám pomůžou přesunout soubory do sdílených složek Azure. Příručky jsou uspořádány podle toho, kde jsou vaše data a jaký model nasazení (pouze cloudová nebo hybridní) přesouváte do.

## <a name="migration-basics"></a>Základy migrace

Azure má několik dostupných typů cloudového úložiště. Základní aspekt migrace souborů do Azure je určení možnosti úložiště Azure, která je pro vaše data nejvhodnější.

[Sdílené složky Azure](storage-files-introduction.md) jsou vhodné pro data souborů pro obecné účely. Mezi tato data patří cokoli, co používáte pro aplikaci místní sdílenou složku SMB nebo NFS. Pomocí [synchronizace souborů Azure](storage-sync-files-planning.md)můžete ukládat obsah několika sdílených složek Azure na serverech, na kterých běží Windows Server místně.

Pro aplikaci, která je aktuálně spuštěná na místním serveru, může být vhodné zvolit ukládání souborů ve sdílené složce Azure. Aplikaci můžete přesunout do Azure a používat sdílené složky Azure jako sdílené úložiště. V tomto scénáři můžete zvážit také [disky Azure](../../virtual-machines/managed-disks-overview.md) .

Některé cloudové aplikace nezávisí na SMB nebo na místním přístupovém přístupu k datům nebo na sdíleném přístupu v počítači. Pro tyto aplikace je často nejlepší volbou úložiště objektů, jako jsou [objekty blob Azure](../blobs/storage-blobs-overview.md) .

Při přesunu souborů z aktuálního umístění úložiště do Azure je klíč v jakékoli migraci zachytávání všech relevantních kvalit souborů. Kolik věrných možností Azure Storage podporuje a kolik vašich scénářů vyžaduje také vám pomůže vybrat správné úložiště Azure. Data souborů pro obecné účely jsou tradičně závislá na metadatech souborů. Data aplikace nemusí být.

Tady jsou dvě základní komponenty souboru:

- **Datový proud**: datový proud souboru uchovává obsah souboru.
- **Metadata souboru**: metadata souboru mají tyto dílčí součásti:
   * Atributy souboru jako jen pro čtení
   * Oprávnění k souborům, která se můžou označovat jako *oprávnění systému souborů NTFS* nebo *seznamy ACL souborů a složek*
   * Časová razítka, hlavně vytváření a časová razítka naposledy upravených
   * Alternativní datový proud, což je prostor pro ukládání většího množství nestandardních vlastností

Přesnost souborů v migraci je možné definovat jako schopnost:

- Uloží všechny použitelné informace o souboru na zdroj.
- Přeneste soubory pomocí nástroje pro migraci.
- Ukládá soubory do cílového úložiště migrace.

Abyste zajistili plynulé zacílení migrace, identifikujte si [nejvhodnější nástroj pro kopírování podle svých potřeb](#migration-toolbox) a porovnejte cíl úložiště se zdrojem.

Při přijetí předchozích informací do účtu vidíte, že cílové úložiště pro soubory pro obecné účely v Azure je [sdílené složky Azure](storage-files-introduction.md).

Na rozdíl od úložiště objektů v objektech blob Azure může sdílená složka Azure nativně ukládat metadata souborů. Sdílené složky Azure také zachovávají hierarchii souborů a složek, atributy a oprávnění. Oprávnění NTFS se můžou ukládat do souborů a složek, protože jsou místní.

Uživatel služby Active Directory, který je místním řadičem domény, může nativně přistupovat ke sdílené složce Azure. Takže můžete uživatele Azure Active Directory Domain Services (Azure služba AD DS). Každá z nich používá jejich aktuální identitu k získání přístupu na základě oprávnění ke sdílení a seznamů ACL pro soubory a složky. Toto chování je podobné jako uživatel, který se připojuje k místní sdílené složce souborů.

Alternativním datovým proudem je primární aspekt věrnosti souborů, který se v tuto chvíli nedá Uložit do souboru ve sdílené složce Azure. Při použití Synchronizace souborů Azure se zachová místně.

Přečtěte si další informace o [ověřování Azure AD](storage-files-identity-auth-active-directory-enable.md) a [ověřování Azure služba AD DS](storage-files-identity-auth-active-directory-domain-service-enable.md) pro sdílené složky Azure.

## <a name="migration-guides"></a>Průvodci migrací

V následující tabulce jsou uvedeny podrobné Příručky k migraci.

Jak používat tabulku:

1. Vyhledejte řádek zdrojového systému, ve kterém jsou soubory aktuálně uloženy.

1. Vyberte jeden z následujících cílů:

   - Hybridní nasazení, které používá Synchronizace souborů Azure k ukládání obsahu sdílených složek Azure do mezipaměti v místním prostředí
   - Sdílené složky Azure v cloudu

   Vyberte cílový sloupec, který odpovídá vašemu výběru.

1. V průsečíku zdroje a cíle obsahuje buňka tabulky dostupné scénáře migrace. Vyberte jednu pro přímý odkaz na podrobný průvodce migrací.

Scénář bez propojení zatím nemá publikovaný Průvodce migrací. V této tabulce jsou občas aktualizace zaškrtnuté. Nové příručky budou publikovány, jakmile budou k dispozici.

| Zdroj | Cíl: </br>Hybridní nasazení | Cíl: </br>Pouze cloudové nasazení |
|:---|:--|:--|
| | Kombinace nástrojů:| Kombinace nástrojů: |
| Windows Server 2012 R2 a novější | <ul><li>[Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)</li><li>[Synchronizace souborů Azure a Azure DataBox](storage-sync-offline-data-transfer.md)</li></ul> | <ul><li>Přes Robocopy do připojené sdílené složky Azure</li><li>Prostřednictvím Synchronizace souborů Azure</li></ul> |
| Windows Server 2012 a starší | <ul><li>Přes DataBox a Synchronizace souborů Azure na nejnovější serverový operační systém</li><li>Přes službu migrace úložiště na nejnovější Server s Synchronizace souborů Azure a pak nahrajte</li></ul> | <ul><li>Přes službu migrace úložiště na nejnovější Server s Synchronizace souborů Azure</li><li>Přes Robocopy do připojené sdílené složky Azure</li></ul> |
| Úložiště připojené k síti (NAS) | <ul><li>[Přes Synchronizace souborů Azure nahrát](storage-files-migration-nas-hybrid.md)</li><li>[Přes DataBox + Synchronizace souborů Azure](storage-files-migration-nas-hybrid-databox.md)</li></ul> | <ul><li>Přes Robocopy do připojené sdílené složky Azure</li></ul> |
| Linux/Samba | <ul><li>[Synchronizace souborů Azure a Robocopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Přes Robocopy do připojené sdílené složky Azure</li></ul> |
| Microsoft Azure StorSimple cloudové zařízení 8100 nebo StorSimple Cloud Appliance 8600 | <ul><li>[Přes vyhrazenou cloudovou službu pro migraci dat](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple Cloud Appliance 1200 | <ul><li>[Prostřednictvím Synchronizace souborů Azure](storage-files-migration-storsimple-1200.md)</li></ul> | |

## <a name="migration-toolbox"></a>Sada nástrojů pro migraci

### <a name="file-copy-tools"></a>Nástroje pro kopírování souborů

Je k dispozici několik nástrojů pro kopírování souborů od Microsoftu a dalších. Pokud chcete vybrat správný nástroj pro váš scénář migrace, musíte vzít v úvahu tyto základní otázky:

* Podporuje nástroj zdrojovou a cílovou lokalitu pro kopírování souborů?

* Podporuje nástroj pro cestu k síti nebo dostupné protokoly (například REST, SMB nebo NFS) mezi zdrojovým a cílovým úložištěm?

* Zachovává nástroj potřebnou kvalitu souborů podporovanou zdrojovým a cílovým umístěním?

    V některých případech vaše cílové úložiště nepodporuje stejnou věrnost jako váš zdroj. Pokud je cílové úložiště dostačující pro vaše potřeby, nástroj musí odpovídat jenom funkcím v cílové přesnosti souborů.

* Má tento nástroj funkce, které umožňují přizpůsobit strategii migrace?

    Můžete například zvážit, zda vám nástroj umožní minimalizovat prostoje.
    
    Když nástroj podporuje možnost zrcadlit zdroj k cíli, můžete ho často spustit několikrát na stejném zdroji a cíli, zatímco zdroj zůstane přístupný.

    Při prvním spuštění nástroje kopíruje Hromadná data. Tento počáteční běh může poslední chvíli trvat. Často trvá déle, než budete chtít převést zdroj dat do režimu offline pro vaše obchodní procesy.

    Zrcadlením zdroje na cíl (stejně jako u nástroje **Robocopy/Mir**) můžete znovu spustit nástroj na stejném zdroji a cíli. Běh je mnohem rychlejší, protože potřebuje přenést pouze změny zdrojového kódu, ke kterým dojde po předchozím spuštění. Tento způsob, jakým se nástroj pro kopírování znovu spustí, může výrazně snížit prostoje.

Následující tabulka klasifikuje nástroje Microsoftu a jejich aktuální vhodnost pro sdílené složky Azure:

| Doporučeno | Nástroj | Podpora sdílených složek Azure | Zachování věrnosti souborů |
| :-: | :-- | :---- | :---- |
|![Ano, doporučeno](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | Podporuje se. Sdílené složky Azure je možné připojit jako síťové jednotky. | Plná přesnost. * |
|![Ano, doporučeno](media/storage-files-migration-overview/circle-green-checkmark.png)| Synchronizace souborů Azure | Nativně integrovaná do sdílených složek Azure. | Plná přesnost. * |
|![Ano, doporučeno](media/storage-files-migration-overview/circle-green-checkmark.png)| Služba migrace úložiště | Nepřímo podporováno. Sdílené složky Azure je možné na cílových serverech SMS připojit jako síťové jednotky. | Plná přesnost. * |
|![Ano, doporučeno](media/storage-files-migration-overview/circle-green-checkmark.png)| AzCopy </br>verze 10,6 | Podporuje se. | Nepodporuje kopírování seznamu ACL zdrojového kořenu, jinak plná přesnost. * </br>[Naučte se používat AzCopy s Azure File shares](../common/storage-use-azcopy-files.md) |
|![Ano, doporučeno](media/storage-files-migration-overview/circle-green-checkmark.png)| Data Box | Podporuje se. | DataBox plně podporuje metadata. |
|![Nedoporučuje se úplně](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Průzkumník služby Azure Storage </br>verze 1,14 | Podporuje se. | Nekopíruje seznamy ACL. Podporuje časová razítka.  |
|![Nedoporučuje se](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Podporuje se. | Nekopíruje metadata. |
|||||

*\* Úplná věrnost: splňuje nebo překračuje možnosti sdílení souborů Azure.*

### <a name="migration-helper-tools"></a>Pomocné nástroje pro migraci

Tato část popisuje nástroje, které vám pomůžou naplánovat a spustit migrace.

#### <a name="robocopy-from-microsoft-corporation"></a>Robocopy od společnosti Microsoft Corporation

Robocopy je jedním z nástrojů, které jsou nejvíce použitelné pro migrace souborů. Je součástí systému Windows. Hlavní [dokumentace nástroje Robocopy](/windows-server/administration/windows-commands/robocopy) je užitečným prostředkem pro mnoho možností tohoto nástroje.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize od zaseknutí softwaru GmbH

Synchronizace souborů Azure škáluje primárně s počtem položek (soubory a složkami) a ne s celkovou velikostí úložiště. Nástroj TreeSize umožňuje určit počet položek na svazcích Windows serveru.

Pomocí tohoto nástroje můžete vytvořit perspektivu před [nasazením synchronizace souborů Azure](storage-sync-files-deployment-guide.md). Můžete ji také použít, když se po nasazení zapojí vrstva cloudu. V takovém scénáři vidíte počet položek a adresáře, které v mezipaměti serveru používají.

Testovaná verze nástroje je verze 4.4.1. Je kompatibilní se soubory na úrovni cloudu. Nástroj během normálního fungování nezpůsobí odvolání vrstvených souborů.

## <a name="next-steps"></a>Další kroky

1. Vytvořte plán, pro který chcete nasadit sdílené složky Azure (jenom pro Cloud nebo hybridní).
1. Projděte si seznam dostupných průvodců migrací, kde najdete podrobný průvodce, který odpovídá vašemu zdroji a nasazení sdílených složek Azure.

Další informace o technologiích souborů Azure, které jsou uvedené v tomto článku:

* [Přehled služby Azure File Share](storage-files-introduction.md)
* [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
* [Synchronizace souborů Azure: vrstvení cloudu](storage-sync-cloud-tiering-overview.md)