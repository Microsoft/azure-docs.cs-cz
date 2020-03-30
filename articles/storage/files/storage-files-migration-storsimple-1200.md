---
title: Migrace StorSimple 1200 do synchronizace souborů Azure
description: Zjistěte, jak migrovat virtuální zařízení řady StorSimple 1200 do Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 69225da1506ced879363b10b098d939df93cbfba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502374"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>Migrace StorSimple 1200 do synchronizace souborů Azure

Řada StorSimple 1200 je virtuální zařízení, které se spouštějí v místním datovém centru. Je možné migrovat data z tohoto zařízení do prostředí Azure File Sync. Azure File Sync je výchozí a strategická dlouhodobá služba Azure, do které lze migrovat zařízení StorSimple.

Série StorSimple 1200 dosáhne [konce životnosti](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series) v prosinci 2022.  Je důležité začít plánovat migraci co nejdříve. Tento článek obsahuje potřebné znalosti na pozadí a kroky migrace pro úspěšnou migraci do Azure File Sync. 

## <a name="azure-file-sync"></a>Synchronizace souborů Azure

> [!IMPORTANT]
> Společnost Microsoft se zavazuje pomáhat zákazníkům při jejich migraci. E-mail AzureFilesMigration@microsoft .com pro vlastní plán migrace, stejně jako pomoc během migrace.

Azure File Sync je cloudová služba Microsoftu založená na dvou hlavních součástech:

* Synchronizace souborů a vrstvení cloudu.
* Sdílené složky jako nativní úložiště v Azure, které lze přistupovat přes více protokolů, jako je SMB a soubor REST. Sdílená složka Azure je srovnatelná se sdílenou složkou na Windows Serveru, kterou můžete nativně připojit jako síťovou jednotku. Podporuje důležité aspekty věrnosti souborů, jako jsou atributy, oprávnění a časová razítka. Na rozdíl od StorSimple, žádná aplikace /služba je nutné interpretovat soubory a složky uložené v cloudu. Ideální a nejflexibilnější přístup k ukládání dat souborového serveru pro obecné účely, stejně jako některých dat aplikací, v cloudu.

Tento článek se zaměřuje na kroky migrace. Pokud se před migrací chcete dozvědět více o Azure File Sync, doporučujeme následující články:

* [Azure File Sync – přehled](https://aka.ms/AFS "Přehled")
* [Průvodce synchronizací souborů Azure – nasazením](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Cíle migrace

Cílem je zaručit integritu výrobních dat a také zajistit dostupnost. Ten vyžaduje udržení prostojů na minimu, aby se veešel do pravidelná okna údržby nebo je jen mírně překračoval.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>Trasa migrace StorSimple 1200 do synchronizace souborů Azure

Ke spuštění agenta Azure File Sync je potřeba místní Windows Server. Windows Server může být minimálně server 2012R2, ale v ideálním případě je Windows Server 2019.

Existuje mnoho alternativních cest migrace a vytvořit příliš dlouhý článek dokumentovat všechny z nich a ilustrovat, proč nesou riziko nebo nevýhody přes trasu doporučujeme jako osvědčený postup v tomto článku.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="Přehled trasy migrace kroků níže v tomto článku.":::

Předchozí obrázek znázorňuje kroky, které odpovídají oddíly v tomto článku.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>Krok 1: Zřízení místního Windows Serveru a úložiště

1. Vytvořte Windows Server 2019 – minimálně 2012R2 – jako virtuální počítač nebo fyzický server. Je také podporován cluster s podporou převzetí služeb při selhání systému Windows Server.
2. Zřízení nebo přidání přímo připojeného úložiště (DAS ve srovnání s NAS, který není podporován). Velikost úložiště systému Windows Server musí být stejná nebo větší než velikost dostupné kapacity virtuálního zařízení StorSimple 1200.

### <a name="step-2-configure-your-windows-server-storage"></a>Krok 2: Konfigurace úložiště systému Windows Server

V tomto kroku namapujete strukturu úložiště StorSimple (svazky a sdílené složky) na strukturu úložiště systému Windows Server.
Pokud plánujete provést změny ve struktuře úložiště, což znamená počet svazků, přidružení datových složek k svazkům nebo strukturu podsložky nad nebo pod aktuální sdílené složky SMB/NFS, pak je nyní čas vzít tyto změny v úvahu.
Změna struktury souborů a složek po nakonfigurované azure file sync je těžkopádné a je třeba se vyhnout.
Tento článek předpokládá, že mapujete 1:1, takže je nutné vzít v úvahu změny mapování při postupujte podle kroků v tomto článku.

* Žádná z produkčních dat by neměla skončit na systémovém svazku systému Windows Server. Vrstvení cloudu není podporováno na svazcích systému. Tato funkce je však vyžadována pro migraci i nepřetržité operace jako náhrada StorSimple.
* Zřiďte na vašem windows serveru stejný počet svazků, jako u virtuálního zařízení StorSimple 1200.
* Nakonfigurujte všechny role, funkce a nastavení systému Windows Server, které potřebujete. Doporučujeme, abyste se přihlásili k aktualizacím systému Windows Server, abyste měli operační systém v bezpečí a aktuální. Podobně doporučujeme nastavit službu Microsoft Update, aby aplikace Microsoft byly aktuální, včetně agenta Azure File Sync.
* Před přečtením následujících kroků nekonfigurujte žádné složky ani sdílené složky.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>Krok 3: Nasazení prvního cloudového prostředku Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>Krok 4: Přizpůsobení místní struktury svazku a složek prostředkům Azure File Sync a Azure file share

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>Krok 5: Zřízení sdílených složek Azure

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>Krok 6: Konfigurace cílových složek systému Windows Server

V předchozích krocích jste zvážili všechny aspekty, které určí součásti topologie synchronizace. Nyní je čas, připravit server pro příjem souborů pro upload.

Vytvořte **všechny** složky, které budou synchronizovat každý s vlastní sdílenou složku Azure.
Je důležité, abyste postupovali podle struktury složek, kterou jste zdokumentovali dříve. Pokud jste se například rozhodli synchronizovat více místních sdílených složek SMB do jedné sdílené složky Azure, musíte je umístit pod společnou kořenovou složku na svazku. Vytvořte tuto cílovou kořenovou složku na svazku.

Počet sdílených složek Azure, které jste zřídit by měl odpovídat počtu složek, které jste vytvořili v tomto kroku + počet svazků, které budete synchronizovat na kořenové úrovni.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>Krok 7: Nasazení agenta synchronizace souborů Azure

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>Krok 8: Konfigurace synchronizace

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Nezapomeňte zapnout vrstvení cloudu!** To je nutné, pokud místní server nemá dostatek místa pro uložení celkové velikosti dat v cloudovém úložišti StorSimple. Nastavte zásady vrstvení, dočasně pro migraci, na 99 % volného místa na svazku.

Opakujte kroky vytvoření skupiny synchronizace a přidání odpovídající serverové složky jako koncového bodu serveru pro všechny sdílené složky Azure / umístění serveru, které je třeba nakonfigurovat pro synchronizaci.

### <a name="step-9-copy-your-files"></a>Krok 9: Kopírování souborů

Základní přístup migrace je RoboCopy z vašeho virtuálního zařízení StorSimple do vašeho Windows Serveru a Azure File Sync do sdílených složek Azure.

Spusťte první místní kopii do cílové složky systému Windows Server:

* Identifikujte první umístění ve virtuálním zařízení StorSimple.
* Identifikujte odpovídající složku na Windows Serveru, která už má na sobě nakonfigurovanou Azure File Sync.
* Spuštění kopie pomocí RoboCopy

Následující příkaz RoboCopy vyvolá soubory z úložiště StorSimple Azure do místního úložiště StorSimple a pak je přesune do cílové složky Windows Serveru. Windows Server ho synchronizuje se sdílenou složkou Azure. Jak se místní svazek Windows Serveru zaplní, spustí se vrstvení cloudu a soubory úrovní, které se již úspěšně synchronizovaly. Vrstvení cloudu vytvoří dostatek místa pro pokračování v kopii z virtuálního zařízení StorSimple. Vrstvení cloudu kontroluje jednou za hodinu, abyste zjistili, co se synchronizovalo, a uvolnili místo na disku, abyste dosáhli volného místa na 99 % svazku.

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Pozadí:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Umožňuje RoboCopy spustit vícevláknové. Výchozí hodnota je 8, max je 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Výstupy stav log souboru jako UNICODE (přepíše existující protokol).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Výstupy do okna konzoly. Používá se ve spojení s výstupem do souboru protokolu.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Spustí RoboCopy ve stejném režimu, který by použila záložní aplikace. Umožňuje RoboCopy přesunout soubory, ke kterým současný uživatel nemá oprávnění.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Umožňuje spustit tento příkaz RoboCopy několikrát, postupně na stejném cíli / cíli. Identifikuje, co bylo zkopírováno dříve a vyneche ji. Budou zpracovány pouze změny, dodatky a "*odstranění*", ke kterým došlo od posledního spuštění. Pokud příkaz nebyl spuštěn dříve, nic není vynecháno. To je vynikající volbou pro zdrojové lokality, které jsou stále aktivně používány a mění.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:příznak copy[s]
   :::column-end:::
   :::column span="1":::
      věrnost kopie souboru (výchozí hodnota je /COPY:DAT), příznaky kopírování: D=Data, A=Atributy, T=Timestamps, S=Security=NTFS ACL, O=Owner info, U=aUditing info
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      KOPÍROVAT všechny informace o souboru (ekvivalentní /COPY:DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:příznak copy[s]
   :::column-end:::
   :::column span="1":::
      věrnost pro kopii adresářů (výchozí hodnota je /DCOPY:DA), příznaky kopírování: D=Data, A=Atributy, T=Časová razítka
   :::column-end:::
:::row-end:::

Při prvním spuštění příkazu RoboCopy uživatelé a aplikace stále přistupují k souborům a složkám StorSimple a potenciálně je mění. Je možné, že RoboCopy zpracoval adresář, přesune se na další a pak uživatel na zdrojovém umístění (StorSimple) přidá, změní nebo odstraní soubor, který nyní nebude zpracován v tomto aktuálním běhu RoboCopy. To je v pořádku.

První spuštění je o přesunutí převážná část dat zpět do místního prostředí, přes windows server a zálohování do cloudu přes Azure File Sync. To může trvat dlouhou dobu, v závislosti na:

* šířku pásma stahování
* rychlost svolávací rychlost cloudové služby StorSimple
* šířka pásma pro nahrávání
* počet položek (souborů a složek), které musí být zpracovány buď

Po dokončení počátečního spuštění spusťte příkaz znovu.

Podruhé bude dokončena rychleji, protože je třeba pouze k přenosu změn, ke kterým došlo od posledního spuštění. Tyto změny jsou pravděpodobně místní StorSimple již, protože jsou nedávné. To dále zkracuje čas, protože se snižuje potřeba odvolání z cloudu. Během tohoto druhého spuštění se stále mohou hromadit nové změny.

Tento postup opakujte, dokud se nepřesvědčíte, že doba, kterou je k dokončení zapotřebí, je přijatelná doba.

Pokud vezmete v úvahu přijatelné prostoje a jste připraveni převést umístění StorSimple do provozu, učiňte tak nyní: Například odeberte sdílenou složku SMB, aby žádný uživatel neměl přístup ke složce nebo neučinil jiný vhodný krok, který zabrání změně obsahu v tomto složky na StorSimple.

Projed poslední robocopy kolo. Tím se zachytí všechny změny, které mohly být vynechány.
Jak dlouho trvá tento poslední krok, závisí na rychlosti skenování RoboCopy. Čas (který se rovná prostojům) můžete odhadnout měřením doby trvalou předchozí spuštění.

Vytvořte sdílenou složku ve složce Windows Server a případně upravte nasazení dfs-n tak, aby na něj ukazovalo. Nezapomeňte nastavit stejná oprávnění na úrovni sdílené složky jako ve sdílené složce StorSimple SMB.

Dokončili jste migraci sdílené složky nebo skupiny sdílených složek do společného kořenového adresáře nebo svazku. (V závislosti na tom, co jste namapovali a rozhodli, že je potřeba přejít do stejné sdílené složky Azure.)

Můžete zkusit spustit několik z těchto kopií paralelně. Doporučujeme zpracovat obor jedné sdílené složky Azure najednou.

> [!WARNING]
> Po přesunutí všech dat z vás StorSimple na Windows Server a vaše migrace je dokončena: Vraťte se do ***všech*** skupin synchronizace na webu Azure portal a upravte objem vrstvení cloudu procenthodnotu volného místa na něco vhodnějšího pro využití mezipaměti, řekněme 20 %. 

Zásady volného místa na svazku vrstvení v cloudu působí na úrovni svazku s potenciálně více koncovými body serveru, které se z něj synchronizují. Pokud zapomenete upravit volné místo i na jednom koncovém bodu serveru, synchronizace bude nadále používat nejvíce omezující pravidlo a pokusí se zachovat 99 % volného místa na disku, takže místní mezipaměť nebude fungovat tak, jak byste očekávali. Pokud není vaším cílem mít pouze obor názvů pro svazek, který obsahuje pouze zřídka přístupná archivní data.

## <a name="troubleshoot"></a>Řešení potíží

Nejpravděpodobnějšíproblém, na který můžete narazit, je, že příkaz RoboCopy selže s *"Plný svazek"* na straně Windows Server. Pokud tomu tak je, pak je rychlost stahování pravděpodobně lepší než rychlost nahrávání. Vrstvení cloudu provádí jednou za hodinu k evakuaci obsahu z místního disku Windows Server, který byl synchronizován.

Uvolněte průběh synchronizace a vrstvení cloudu. Můžete pozorovat, že v Průzkumníkovi souborů na vašem Windows Server.

Pokud má systém Windows Server dostatečnou dostupnou kapacitu, problém vyřešíte opětovnou spuštěním příkazu. Nic se nerozbije, když se dostanete do této situace a můžete se pohybovat vpřed s důvěrou. Nepříjemnosti spuštění příkazu znovu je jediným důsledkem.

Můžete také spustit do jiných problémů Azure File Sync.
Jak nepravděpodobné, jak mohou být, pokud k tomu dojde, podívejte se na **link Azure file sync poradce při potížích průvodce**.

## <a name="relevant-links"></a>Relevantní odkazy

Obsah migrace:

* [Průvodce migrací řady StorSimple 8000](storage-files-migration-storsimple-8000.md)

Obsah synchronizace souborů Azure:

* [Přehled AFS](https://aka.ms/AFS)
* [Průvodce nasazením AFS](storage-files-deployment-guide.md)
* [Řešení potíží s AFS](storage-sync-files-troubleshoot.md)
