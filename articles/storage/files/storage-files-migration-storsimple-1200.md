---
title: Migrace StorSimple 1200 na Azure File Sync
description: Přečtěte si, jak migrovat virtuální zařízení řady StorSimple 1200 na Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d9cf7b3cf996e41f90e3a40a6ee08d0fd51c8457
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85510332"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>Migrace StorSimple 1200 na Azure File Sync

Série StorSimple 1200 je virtuální zařízení, které běží v místním datovém centru. Je možné migrovat data z tohoto zařízení do Azure File Syncho prostředí. Azure File Sync je výchozí a strategická dlouhodobá služba Azure, na kterou je možné StorSimple zařízení migrovat.

Série StorSimple 1200 se [dokončí do konce životnosti](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series) v prosinci 2022.  Je důležité zahájit plánování migrace co nejrychleji. Tento článek popisuje nezbytné kroky pro znalosti a migrace na pozadí pro úspěšnou migraci na Azure File Sync. 

## <a name="azure-file-sync"></a>Synchronizace souborů Azure

> [!IMPORTANT]
> Společnost Microsoft se zavazuje pomáhat zákazníkům při jejich migraci. E-mail AzureFilesMigration@microsoft . com pro vlastní plán migrace a pomoc při migraci.

Azure File Sync je cloudová služba Microsoftu založená na dvou hlavních součástech:

* Synchronizace souborů a vrstvení cloudu.
* Sdílené složky jako nativní úložiště v Azure, které je možné používat v různých protokolech jako SMB a REST souborů. Sdílená složka Azure je srovnatelná se sdílenou složkou na Windows serveru, kterou můžete nativně připojit jako síťovou jednotku. Podporuje důležité aspekty souborů, jako jsou atributy, oprávnění a časová razítka. Na rozdíl od StorSimple není potřeba k interpretaci souborů a složek uložených v cloudu žádná aplikace ani služba. Ideální a nejpružnější přístup k ukládání dat souborového serveru pro obecné účely i k určitým datům aplikace v cloudu.

Tento článek se zaměřuje na kroky migrace. Pokud před migrací chcete získat další informace o Azure File Sync, doporučujeme následující články:

* [Azure File Sync – přehled](https://aka.ms/AFS "Přehled")
* [Průvodce nasazením Azure File Sync](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Cíle migrace

Cílem je zaručit integritu produkčních dat a zaručit dostupnost. Ta ta vyžaduje udržení minimálního výpadku, aby bylo možné se přizpůsobit nebo jen mírně překročit pravidelná časová období údržby.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>Cesta migrace StorSimple 1200 na Azure File Sync

Pro spuštění agenta Azure File Sync je vyžadován místní Windows Server. Windows Server může mít minimálně server 2012R2, ale v ideálním případě je to Windows Server 2019.

Existuje spousta alternativních cest migrace a vytvoří se příliš dlouhý článek, který by měl dokument zdokumentovat a ilustruje, proč představují riziko nebo nevýhody v trase, které doporučujeme jako osvědčený postup v tomto článku.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="Přehled postupu migrace dalších kroků v tomto článku.":::

Předchozí obrázek znázorňuje kroky, které odpovídají oddílům v tomto článku.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>Krok 1: zřízení místního Windows serveru a úložiště

1. Vytvořte Windows Server 2019 – minimálně 2012R2 jako virtuální počítač nebo fyzický server. Podporuje se taky cluster Windows serveru s podporou převzetí služeb při selhání.
2. Zřídit nebo přidat přímo připojené úložiště (DAS ve srovnání se serverem NAS, který není podporován). Velikost úložiště Windows serveru musí být stejná nebo větší než velikost dostupné kapacity vašeho zařízení Virtual StorSimple 1200.

### <a name="step-2-configure-your-windows-server-storage"></a>Krok 2: konfigurace úložiště Windows serveru

V tomto kroku namapujete strukturu úložiště StorSimple (svazky a sdílené složky) do struktury úložiště Windows serveru.
Pokud máte v plánu provádět změny ve své struktuře úložiště, což znamená počet svazků, přidružení složek dat ke svazkům nebo strukturu podsložek nad rámec aktuálních sdílených složek SMB/NFS, a teď je čas provést tyto změny v potaz.
Změna struktury souborů a složek po nakonfigurování Azure File Sync je nenáročná a měla by se vám vyhnout.
V tomto článku se předpokládá, že jste namapováni 1:1, takže pokud budete postupovat podle kroků v tomto článku, musíte vzít v úvahu změny v mapování.

* Žádná z vašich produkčních dat by neměla skončit na svazku systému Windows Server. V systémových svazcích není podporováno vrstvení cloudu. Tato funkce se ale vyžaduje pro migraci i pro průběžné operace jako StorSimple náhrada.
* Na Windows serveru zajistěte stejný počet svazků, jako máte na virtuálním zařízení s StorSimple 1200.
* Nakonfigurujte jakékoli role, funkce a nastavení systému Windows Server, které potřebujete. Doporučujeme, abyste se přihlásili k aktualizacím Windows serveru, aby byl váš operační systém v bezpečí a byl v aktuálním stavu. Podobně doporučujeme, abyste se přihlásili Microsoft Update, aby se aplikace Microsoftu udržovaly v aktuálním stavu, včetně agenta Azure File Sync.
* Před čtením následujících kroků nekonfigurujte žádné složky ani sdílené složky.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>Krok 3: nasazení prvního Azure File Syncho cloudového prostředku

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>Krok 4: Vyhledání místní struktury svazků a složek pro Azure File Sync a prostředků sdílené složky Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>Krok 5: zřízení sdílených složek Azure

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>Krok 6: Konfigurace cílových složek Windows serveru

V předchozích krocích jste se domnívali všemi aspekty, které určují komponenty topologií synchronizace. Nyní je čas připravit server pro příjem souborů k nahrání.

Vytvořte **všechny** složky, které se synchronizují s vlastní sdílenou složkou Azure.
Je důležité postupovat podle struktury složek, kterou jste si popsali dříve. Pokud jste se rozhodli například synchronizovat více místních sdílených složek SMB do jedné sdílené složky Azure, musíte je umístit do společné kořenové složky na tomto svazku. Vytvořte tuto cílovou kořenovou složku na svazku nyní.

Počet sdílených sdílených složek Azure, které jste zřídili, by měl odpovídat počtu složek, které jste vytvořili v tomto kroku, a počtu svazků, které budete synchronizovat na kořenové úrovni.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>Krok 7: nasazení agenta Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>Krok 8: Konfigurace synchronizace

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Nezapomeňte zapnout vrstvení cloudu!** To se vyžaduje v případě, že váš místní server nemá dost místa pro uložení celkové velikosti vašich dat v cloudovém úložišti StorSimple. Nastavte zásady vrstvení dočasně pro migraci na 99% volného místa na svazku.

Opakujte kroky při vytváření skupiny synchronizace a přidání odpovídající složky serveru jako koncového bodu serveru pro všechna sdílená složka Azure nebo umístění na serveru, která je potřeba nakonfigurovat pro synchronizaci.

### <a name="step-9-copy-your-files"></a>Krok 9: zkopírování souborů

Základní přístup k migraci je příkaz Robocopy z virtuálního zařízení StorSimple k vašemu Windows serveru a Azure File Sync ke sdíleným složkám Azure.

Spusťte první místní kopii do cílové složky Windows serveru:

* Identifikujte první umístění na virtuálním zařízení StorSimple.
* Identifikujte odpovídající složku na Windows serveru, která už má Azure File Sync nakonfigurovanou.
* Spuštění kopírování pomocí nástroje Robocopy

Následující příkaz Robocopy stáhne soubory z StorSimple Azure Storage do místního StorSimple a pak je přesune do cílové složky Windows serveru. Systém Windows Server provede synchronizaci se sdílenými složkami Azure. Vzhledem k plnémumu místnímu svazku Windows serveru se vrstvení cloudu zahájí v souborech a vrstvách, které se už úspěšně synchronizovaly. Vrstvení cloudu vytvoří dostatek místa pro pokračování kopie z virtuálního zařízení StorSimple. Vrstvení cloudu kontroluje jednu hodinu, která se synchronizuje, a uvolní místo na disku, abyste dosáhli volného místa na 99% svazku.

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Pozadí

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Umožňuje nástroji Robocopy spustit vícevláknové procesy. Výchozí hodnota je 8, maximální hodnota je 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Výstupuje stav do souboru protokolu jako UNICODE (přepíše existující protokol).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Provede výstup do okna konzoly. Používá se ve spojení s výstupem do souboru protokolu.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Spustí příkaz Robocopy ve stejném režimu, který používá zálohovací aplikace. Umožňuje nástroji Robocopy přesunout soubory, ke kterým aktuální uživatel nemá oprávnění.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Umožňuje několikrát spustit tento příkaz Robocopy na stejném cíli nebo cíli. Určuje, co se zkopírovalo dřív, a vynechá ho. Budou zpracovány pouze změny, přidání a "*odstranění*", k nimž došlo od posledního spuštění. Pokud příkaz neběžel dřív, nevynechá se nic. Toto je vynikající možnost pro umístění zdrojů, která se pořád aktivně používají a mění.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      věrnost kopírování souborů (výchozí je/COPY: DAT), příznaky kopírování: D = data, A = atributy, T = časová razítka, S = zabezpečení – seznamy řízení přístupu NTFS, O = informace o vlastníkovi, U = informace o auditování
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      Kopírovat všechny informace o souboru (ekvivalentní k/COPY: DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      věrnost pro kopii adresářů (výchozí hodnota je/DCOPY: DA), příznaky kopírování: D = data, A = atributy, T = časová razítka
   :::column-end:::
:::row-end:::

Při prvním spuštění příkazu Robocopy budou uživatelé a aplikace nadále přistupovat k souborům a složkám StorSimple a potenciálně je mohou změnit. Je možné, že Robocopy zpracovaly adresář, přesune se k dalšímu a pak uživatel na zdrojovém umístění (StorSimple) přidá, změní nebo odstraní soubor, který se teď v tomto aktuálním běhu Robocopy nezpracovává. To je dobré.

Prvním spuštěním je o přesunu velkých objemů dat zpátky do místního prostředí a k zálohování do cloudu přes Azure File Sync. Tato akce může trvat dlouhou dobu, v závislosti na:

* Šířka pásma ke stažení
* rychlost odvolání cloudové služby StorSimple
* Šířka pásma nahrávání
* počet položek (soubory a složky), které je potřeba zpracovat buď pomocí služby

Po dokončení počátečního spuštění znovu spusťte příkaz.

Druhý čas se dokončí rychleji, protože potřebuje pouze přenést změny, ke kterým došlo od posledního spuštění. Tyto změny jsou pravděpodobně místní pro StorSimple, protože jsou již poslední. Tím se zkrátí čas, protože nutnost odvolání z cloudu se zmenší. V průběhu tohoto druhého běhu se stále mohou nashromáždit nové změny.

Tento postup opakujte, dokud nebudete přesvědčeni, že doba potřebná k dokončení je přijatelné výpadky.

Když posuzujete nepotřebné výpadky a jste připraveni převést umístění StorSimple do režimu offline, postupujte takto: například odeberte sdílenou složku SMB, aby k této složce nikdo uživatel nemohly získat přístup, nebo provést jiný vhodný krok, který zabrání změně obsahu v této složce na StorSimple.

Spusťte jeden poslední příkaz Robocopy. Tato akce vybírá všechny změny, které mohly být vynechány.
Doba, po kterou tento poslední krok trvá, závisí na rychlosti kontroly nástroje Robocopy. Můžete odhadnout čas (který se rovná vašemu výpadku), a to měřením doby trvání předchozího běhu.

Vytvořte sdílenou složku ve složce Windows serveru a případně upravte nasazení DFS-N tak, aby odkazovalo na něj. Nezapomeňte nastavit stejné oprávnění na úrovni sdílení jako u sdílené složky SMB StorSimple.

Dokončili jste migraci sdílené složky nebo skupiny sdílených složek do společného kořenového adresáře nebo svazku. (V závislosti na tom, co jste namapovali a rozhodli jste se, že je potřeba přejít do stejné sdílené složky Azure.)

Můžete zkusit spustit několik z těchto kopií paralelně. Doporučujeme, abyste v jednom okamžiku zpracovali obor jedné sdílené složky Azure.

> [!WARNING]
> Po přesunutí všech dat StorSimple do Windows serveru a dokončení migrace se vraťte do ***všech***  skupin synchronizace v Azure Portal a nastavte hodnotu hlasitosti volného místa na úrovni cloudu na něco většího, než je procento využití mezipaměti, řekněme 20%. 

Zásada pro volné místo svazku ve vrstvách cloudu funguje na úrovni svazku s potenciálně synchronizovanými koncovými body serveru. Pokud zapomenete upravit volné místo na jednom koncovém bodu serveru, bude synchronizace dál používat nejvíce omezující pravidlo a pokusí se zachovávat 99% volného místa na disku, takže místní mezipaměť nefunguje, protože byste mohli očekávat. Pokud se nejedná o váš cíl jenom pro svazek, který obsahuje jenom zřídka používané, archivní data.

## <a name="troubleshoot"></a>Řešení potíží

Nejpravděpodobnějším problémem, ke kterému můžete spustit, je, že příkaz Robocopy se na straně serveru Windows nezdařil s *názvem "svazek je plný"* . Pokud se jedná o tento případ, bude vaše rychlost stahování nejspíš lepší než rychlost nahrávání. Vrstvení cloudu slouží jednou za hodinu k vyvádění obsahu z místního disku Windows serveru, který se synchronizuje.

Umožněte synchronizaci v průběhu a vrstvení cloudu uvolněte místo na disku. Můžete si všimnout, že v Průzkumníkovi souborů na vašem Windows serveru.

Pokud má Windows Server dostatek dostupné kapacity, problém vyřešíte tak, že znovu spustíte příkaz. Po dosažení této situace se nic neukončí a můžete se přesunout vpřed s jistotou. Nepohodlí spuštění příkazu je jediné, co je to v tomto důsledku.

Můžete také spustit jiné problémy Azure File Sync.
V případě nepravděpodobného chování, pokud k tomu dojde, se podívejte na **Průvodce odstraňováním potíží s odkazem Azure File Sync**.

## <a name="relevant-links"></a>Relevantní odkazy

Obsah migrace:

* [Průvodce migrací řady StorSimple 8000](storage-files-migration-storsimple-8000.md)

Azure File Sync obsah:

* [Přehled AFS](https://aka.ms/AFS)
* [Průvodce nasazením pro AFS](storage-files-deployment-guide.md)
* [Řešení potíží AFS](storage-sync-files-troubleshoot.md)
