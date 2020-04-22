---
title: Migrace do sdílených složek Azure
description: Přečtěte si o migraci do sdílených složek Azure a najděte průvodce migrací.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d6141d48d67dd44c348961c6e09acf4e2531a61e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685993"
---
# <a name="migrate-to-azure-file-shares"></a>Migrace do sdílených složek Azure

Tento článek popisuje základní aspekty migrace do sdílených složek Azure.

Tento článek obsahuje základy migrace a tabulku průvodců migrací. Tyto příručky vám pomůžou přesunout soubory do sdílených složek Azure. Průvodci jsou uspořádány podle toho, kde jsou vaše data a jaký model nasazení (pouze cloud nebo hybrid) přejdete.

## <a name="migration-basics"></a>Základy migrace

Azure má několik dostupných typů cloudového úložiště. Základním aspektem migrace souborů do Azure je určení, která možnost úložiště Azure je vhodná pro vaše data.

[Sdílené složky Azure](storage-files-introduction.md) jsou vhodné pro data souborů pro obecné účely. Tato data zahrnují vše, pro co používáte místní sdílenou složku SMB nebo NFS. Pomocí [Azure File Sync](storage-sync-files-planning.md)můžete ukládat obsah několika sdílených složek Azure do mezipaměti na serverech se systémem Windows Server v místním prostředí.

Pro aplikaci, která aktuálně běží na místním serveru, může být ukládání souborů ve sdílené složce Azure dobrou volbou. Aplikaci můžete přesunout do Azure a používat sdílené složky Azure jako sdílené úložiště. Můžete také zvážit [Disky Azure](../../virtual-machines/windows/managed-disks-overview.md) pro tento scénář.

Některé cloudové aplikace nezávisí na SMB nebo na přístupu k místním upočítače nebo sdíleném přístupu. Pro tyto aplikace je úložiště objektů, jako jsou [objekty BLOB Azure,](../blobs/storage-blobs-overview.md) často nejlepší volbou.

Klíčem v jakékoli migraci je zachytit všechny příslušné věrnost souborů při přesunu souborů z jejich aktuální umístění úložiště do Azure. Jak moc věrnost možnost úložiště Azure podporuje a kolik váš scénář vyžaduje také vám pomůže vybrat správné úložiště Azure. Data souborů pro obecné účely tradičně závisí na metadatech souboru. Data aplikace nemusí.

Zde jsou dvě základní součásti souboru:

- **Datový proud**: Datový proud souboru ukládá obsah souboru.
- **Metadata souboru**: Metadata souboru mají tyto dílčí součásti:
   * Atributy souborů, jako je jen pro čtení
   * Oprávnění k souborům, která lze označovat jako *oprávnění ntfs* nebo *soubory ACL souborů a složek*
   * Časová razítka, zejména vytvoření a poslední změněná časová razítka
   * Alternativní datový tok, což je prostor pro uložení většího množství nestandardních vlastností

Věrnost souborů při migraci lze definovat jako schopnost:

- Uložte všechny příslušné informace o souboru do zdroje.
- Přenos souborů pomocí nástroje pro migraci.
- Uložte soubory do cílového úložiště migrace.

Chcete-li zajistit hladký průběh migrace, [určete nejlepší nástroj pro kopírování podle svých potřeb](#migration-toolbox) a přizpůsobte cíl úložiště zdroji.

Vezmeme-li v úvahu předchozí informace, uvidíte, že cílové úložiště pro soubory pro obecné účely v Azure jsou [sdílené složky Azure](storage-files-introduction.md).

Na rozdíl od úložiště objektů v objektech BLOB Azure může sdílená složka Azure nativně ukládat metadata souborů. Sdílené složky Azure také zachovat hierarchii souborů a složek, atributy a oprávnění. Oprávnění ntfs lze uložit do souborů a složek, protože jsou místní.

Uživatel služby Active Directory, což je jejich místní řadič domény, může nativně přistupovat ke sdílené složce Azure. Stejně tak může uživatel služby Azure Active Directory Domain Services (Azure AD DS). Každý z nich používá svou aktuální identitu k získání přístupu na základě oprávnění ke sdílení a na seznamu ACL souborů a složek. Toto chování je podobné uživateli, který se připojuje k místní sdílené složce.

Alternativní datový proud je primární aspekt věrnosti souborů, který momentálně nejde uložit do souboru ve sdílené složce Azure. Je zachována místně při použití Azure File Sync.

Další informace o [ověřování Azure AD](storage-files-identity-auth-active-directory-enable.md) a [ověřování Azure AD DS](storage-files-identity-auth-active-directory-domain-service-enable.md) pro sdílené složky Azure.

## <a name="migration-guides"></a>Průvodci migrací

V následující tabulce jsou uvedeny podrobné příručky pro migraci.

Jak používat tabulku:

1. Vyhledejte řádek zdrojového systému, ve které jsou soubory aktuálně uloženy.

1. Vyberte si jeden z těchto cílů:

   - Hybridní nasazení využívající Azure File Sync k ukládání obsahu sdílených složek Azure místně
   - Sdílené složky Azure v cloudu

   Vyberte cílový sloupec, který odpovídá vaší volbě.

1. V rámci průniku zdroje a cíle jsou v buňce tabulky uvedeny dostupné scénáře migrace. Vyberte jednu, která chcete přímo propojit s podrobným průvodcem migrací.

Scénář bez odkazu ještě nemá publikovaného průvodce migrací. V této tabulce občas zkontrolujte aktualizace. Jakmile budou k dispozici, budou publikováni noví průvodci.

| Zdroj | Cíl: </br>Hybridní nasazení | Cíl: </br>Nasazení pouze v cloudu |
|:---|:--|:--|
| | Kombinace nástrojů:| Kombinace nástrojů: |
| Windows Server 2012 R2 a novější | <ul><li>[Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)</li><li>[Synchronizace souborů Azure a datová skříňka Azure](storage-sync-offline-data-transfer.md)</li><li>Služba synchronizace souborů azure a migrace úložišť</li></ul> | <ul><li>Synchronizace souborů Azure</li><li>Synchronizace souborů Azure a datová skříňka</li><li>Služba synchronizace souborů azure a migrace úložišť</li><li>Robocopy</li></ul> |
| Windows Server 2012 a starší | <ul><li>Synchronizace souborů Azure a datová skříňka</li><li>Služba synchronizace souborů azure a migrace úložišť</li></ul> | <ul><li>Služba synchronizace souborů azure a migrace úložišť</li><li>Robocopy</li></ul> |
| Úložiště připojené k síti (NAS) | <ul><li>[Synchronizace souborů Azure a robokopie](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Linux nebo Samba | <ul><li>[Synchronizace souborů Azure a robokopie](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Cloudové zařízení Microsoft Azure StorSimple 8100 nebo StorSimple Cloud Appliance 8600 | <ul><li>[Azure synchronizace souborů a StorSimple Cloud Appliance 8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple Cloud Appliance 1200 | <ul><li>[Synchronizace souborů Azure](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Panel nástrojů migrace

### <a name="file-copy-tools"></a>Nástroje pro kopírování souborů

Existuje několik nástrojů pro kopírování souborů, které jsou k dispozici od společnosti Microsoft a dalších. Chcete-li vybrat správný nástroj pro váš scénář migrace, je třeba zvážit tyto základní otázky:

* Podporuje nástroj zdrojová a cílová umístění pro kopii souboru?

* Podporuje nástroj vaši síťovou cestu nebo dostupné protokoly (například REST, SMB nebo NFS) mezi zdrojovým a cílovým umístěním úložiště?

* Zachová nástroj potřebnou věrnost souborů podporovanou zdrojovými a cílovými umístěními?

    V některých případech vaše cílové úložiště nepodporuje stejnou věrnost jako váš zdroj. Pokud cílové úložiště je dostatečné pro vaše potřeby, nástroj musí odpovídat pouze možnosti věrnosti souborů cíle.

* Má nástroj funkce, které umožňují, aby se vešly do strategie migrace?

    Zvažte například, zda nástroj umožňuje minimalizovat prostoje.
    
    Pokud nástroj podporuje možnost zrcadlit zdroj do cíle, můžete jej často spustit vícekrát na stejném zdroji a cíli, zatímco zdroj zůstane přístupný.

    Při prvním spuštění nástroje zkopíruje většinu dat. Tento počáteční běh může chvíli trvat. Často trvá déle, než chcete pro přepnutí zdroje dat do offline pro vaše obchodní procesy.

    Zrcadlením zdroje na cíl (stejně jako **u robocopy /MIR**) můžete nástroj spustit znovu na stejném zdroji a cíli. Spuštění je mnohem rychlejší, protože je třeba přenášet pouze zdrojové změny, ke kterým dochází po předchozím spuštění. Opětovným spuštěním nástroje pro kopírování tímto způsobem může výrazně snížit prostoje.

Následující tabulka klasifikuje nástroje Microsoftu a jejich aktuální vhodnost pro sdílené složky Azure:

| Doporučené | Nástroj | Podpora sdílených složek Azure | Zachování věrnosti souborů |
| :-: | :-- | :---- | :---- |
|![Ano, doporučeno](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy | Podporuje se. Sdílené složky Azure lze připojit jako síťové jednotky. | Plná věrnost.* |
|![Ano, doporučeno](media/storage-files-migration-overview/circle-green-checkmark.png)| Synchronizace souborů Azure | Nativně integrované do sdílených složek Azure. | Plná věrnost.* |
|![Ano, doporučeno](media/storage-files-migration-overview/circle-green-checkmark.png)| Služba migrace úložiště | Nepřímo podporováno. Sdílené složky Azure lze připojit jako síťové jednotky na serverech SMS target. | Plná věrnost.* |
|![Není plně doporučeno](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Data Box | Podporuje se. | Nekopíruje metadata. [Datovou schránku lze použít se synchronizací souborů Azure](storage-sync-offline-data-transfer.md). |
|![Nedoporučuje se](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | Podporuje se. | Nekopíruje metadata. |
|![Nedoporučuje se](media/storage-files-migration-overview/circle-red-x.png)| Azure Storage Explorer | Podporuje se. | Nekopíruje metadata. |
|![Nedoporučuje se](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Podporuje se. | Nekopíruje metadata. |
|||||

*\*Plná věrnost: splňuje nebo překračuje možnosti sdílení souborů Azure.*

### <a name="migration-helper-tools"></a>Pomocné nástroje pro migraci

Tato část popisuje nástroje, které vám pomohou plánovat a spouštět migrace.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy od společnosti Microsoft Corporation

RoboCopy je jedním z nástrojů, které jsou nejvíce použitelné pro migraci souborů. Dodává se jako součást systému Windows. Hlavní [dokumentace RoboCopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) je užitečným zdrojem pro mnoho možností tohoto nástroje.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize od společnosti JAM Software GmbH

Azure File Sync se škáluje především podle počtu položek (souborů a složek) a ne podle celkové velikosti úložiště. Nástroj TreeSize umožňuje určit počet položek na svazcích systému Windows Server.

Pomocí tohoto nástroje můžete vytvořit perspektivu před [nasazením Synchronizace souborů Azure](storage-sync-files-deployment-guide.md). Můžete ji také použít, když je po nasazení zapojeno vrstvení cloudu. V tomto scénáři uvidíte počet položek a adresáře, které používají mezipaměti serveru nejvíce.

Testovaná verze nástroje je verze 4.4.1. Je kompatibilní se soubory s vrstvením v cloudu. Nástroj nezpůsobí odvolání vrstvených souborů během jeho normálního provozu.

## <a name="next-steps"></a>Další kroky

1. Vytvořte plán, pro který chcete nasazení sdílených složek Azure (pouze cloud nebo hybridní).
1. Projděte si seznam dostupných průvodců migrací a najděte podrobný průvodce, který odpovídá vašemu zdroji a nasazení sdílených složek Azure.

Tady jsou další informace o technologiích Azure Files uvedených v tomto článku:

* [Přehled sdílení souborů Azure](storage-files-introduction.md)
* [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
* [Synchronizace souborů Azure: Vrstvení cloudu](storage-sync-cloud-tiering.md)
