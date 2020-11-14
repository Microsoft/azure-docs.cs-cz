---
title: Migrace místních serverů NAS na Azure File Sync
description: Naučte se migrovat soubory z místního umístění úložiště (NAS) do hybridního cloudu pomocí Azure File Sync a sdílených složek Azure.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 0d8d19256dfca21cc805c2689557099a6785f76b
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629202"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migrace z úložiště připojení k síti (NAS) do hybridního nasazení v cloudu pomocí Azure File Sync

Azure File Sync funguje v umístěních přímo připojeného úložiště (DAS) a nepodporuje synchronizaci s umístěními síťového připojeného úložiště (NAS).
Tato skutečnost provede migraci vašich souborů a tento článek vás provede plánováním a prováděním této migrace.

## <a name="migration-goals"></a>Cíle migrace

Cílem je přesunout sdílené složky, které máte na zařízení NAS, na Windows Server. Pak využijte Azure File Sync pro nasazení hybridního cloudu. Tato migrace se musí udělat způsobem, který zaručuje integritu produkčních dat i dostupnost během migrace. Ta ta vyžaduje udržení minimálního výpadku, aby bylo možné se přizpůsobit nebo jen mírně překročit pravidelná časová období údržby.

## <a name="migration-overview"></a>Přehled migrace

Jak je uvedeno v [článku Přehled migrace](storage-files-migration-overview.md)souborů Azure, je důležité použít správný nástroj pro kopírování a přístup. Vaše zařízení NAS zveřejňuje sdílené složky SMB přímo v místní síti. V rámci tohoto scénáře migrace je to nejlepší způsob, jak přesunout soubory z nástroje Robocopy, integrované do Windows serveru.

- Fáze 1: [určení, kolik sdílených složek Azure potřebujete](#phase-1-identify-how-many-azure-file-shares-you-need)
- Fáze 2: [zřízení vhodného Windows serveru v místním prostředí](#phase-2-provision-a-suitable-windows-server-on-premises)
- Fáze 3: [nasazení cloudového prostředku Azure File Sync](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- Fáze 4: [nasazení prostředků služby Azure Storage](#phase-4-deploy-azure-storage-resources)
- Fáze 5: [nasazení agenta Azure File Sync](#phase-5-deploy-the-azure-file-sync-agent)
- Fáze 6: [konfigurace Azure File Sync na Windows serveru](#phase-6-configure-azure-file-sync-on-the-windows-server)
- Fáze 7: [Robocopy](#phase-7-robocopy)
- Fáze 8: [vyjmutí uživatele z převzetí](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fáze 1: určení, kolik sdílených složek Azure potřebujete

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>Fáze 2: zřízení vhodného Windows serveru v místním prostředí

* Vytvořte Windows Server 2019 – minimálně 2012R2 jako virtuální počítač nebo fyzický server. Podporuje se taky cluster Windows serveru s podporou převzetí služeb při selhání.
* Zřídit nebo přidat přímo připojené úložiště (DAS ve srovnání se serverem NAS, který není podporován).

    Velikost úložiště, kterou zřídíte, může být menší než vaše zařízení, které aktuálně používáte, v případě, že použijete funkci [cloudové vrstvy](storage-sync-cloud-tiering.md) Azure File Sync.
    Když ale kopírujete soubory z většího prostoru NAS do menšího svazku Windows serveru v pozdější fázi, budete muset pracovat v dávkách:

    1. Přesunutí sady souborů, které se vejdou na disk
    2. Povolit synchronizaci souborů a vrstvení cloudu
    3. Pokud je na svazku vytvořeno více volného místa, pokračujte dalším dávkou souborů. 
    
    Tomuto přístupu k dávkám se můžete vyhnout tím, že zřizujete stejné místo na Windows serveru, které vaše soubory zaujímají na zařízení NAS. Zvažte odstranění duplicitních dat na serveru NAS nebo v systému Windows. Pokud nechcete, aby se na Windows Server trvale potvrdilo toto vysoké množství úložiště, můžete zmenšit velikost svazku po migraci a před úpravou zásad cloudové vrstvy. Tím se vytvoří menší místní mezipaměť sdílených složek Azure.

Konfigurace prostředků (COMPUTE a RAM) systému Windows Server, který nasazujete, závisí hlavně na počtu položek (soubory a složky), které budete synchronizovat. Pokud máte nějaké obavy, doporučujeme, abyste se s vyšší konfigurací výkonu přečetli.

[Naučte se, jak nastavit systém Windows Server podle počtu položek (soubory a složky), které je třeba synchronizovat.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> Dříve propojený článek prezentuje tabulku s rozsahem pro paměť serveru (RAM). Můžete orientovat směrem k menšímu počtu serverů, ale očekávat, že počáteční synchronizace může trvat delší dobu.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fáze 3: nasazení cloudového prostředku Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fáze 4: nasazení prostředků služby Azure Storage

V této fázi si Projděte tabulku mapování z fáze 1 a použijte ji ke zřízení správného počtu účtů úložiště Azure a sdílených složek v nich.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fáze 5: nasazení agenta Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>Fáze 6: konfigurace Azure File Sync na Windows serveru

Váš registrovaný místní Windows Server musí být pro tento proces připravený a připojený k Internetu.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Vrstvení cloudu je funkce nástroje AFS, která umožňuje místnímu serveru mít menší kapacitu úložiště než je uložená v cloudu, ale má úplný obor názvů k dispozici. Místně zajímavá data se ukládají také místně do mezipaměti pro výkon s rychlým přístupem. Vrstvení cloudu je volitelná funkce pro každý Azure File Sync koncový bod serveru.

> [!WARNING]
> Pokud jste na svazcích Windows serveru zřídili méně úložiště než vaše data používaná na zařízení NAS, je vrstva cloudu povinná. Pokud nepovolíte vrstvení cloudu, server nebude mít volné místo pro uložení všech souborů. Nastavte zásady vrstvení dočasně pro migraci na 99% volného místa na svazku. Až se migrace dokončí, nezapomeňte se vrátit k nastavení vrstvení cloudu a nastavit ji na obecnější užitečnou úroveň.

Opakujte kroky při vytváření skupiny synchronizace a přidání odpovídající složky serveru jako koncového bodu serveru pro všechna sdílená složka Azure nebo umístění na serveru, která je potřeba nakonfigurovat pro synchronizaci.

Po vytvoření všech koncových bodů serveru synchronizace funguje. Můžete vytvořit testovací soubor a vidět jeho synchronizaci z umístění na serveru s připojenou sdílenou složkou Azure (jak popisuje koncový bod cloudu ve skupině synchronizace).

Obě umístění, serverové složky a sdílené složky Azure jsou v opačném případě prázdná a čekají na data v libovolném umístění. V dalším kroku začnete kopírovat soubory do Windows serveru pro Azure File Sync, abyste je přesunuli do cloudu. V případě, že jste povolili vrstvení cloudu, bude server začínat soubory vrstev, takže by se měla vykonat kapacita na místních svazcích.

## <a name="phase-7-robocopy"></a>Fáze 7: Robocopy

Základní způsob migrace je prostředí Robocopy ze zařízení NAS na Windows Server a Azure File Sync sdílené složky Azure.

Spusťte první místní kopii do cílové složky Windows serveru:

* Identifikujte první umístění na vašem zařízení NAS.
* Identifikujte odpovídající složku na Windows serveru, která už má Azure File Sync nakonfigurovanou.
* Spuštění kopírování pomocí nástroje Robocopy

Následující příkaz Robocopy zkopíruje soubory z úložiště NAS do cílové složky Windows serveru. Systém Windows Server provede synchronizaci se sdílenými složkami Azure. 

Pokud jste na Windows serveru zřídili méně úložiště, než vaše soubory zabírají na zařízení NAS, pak jste nakonfigurovali vrstvu cloudu. Vzhledem k plnémumu místnímu svazku Windows serveru se [vrstvení cloudu](storage-sync-cloud-tiering.md) zahájí v souborech a vrstvách, které se už úspěšně synchronizovaly. Vrstvení cloudu vytvoří dostatek místa pro pokračování kopie ze zařízení NAS. Vrstvení cloudu kontroluje jednu hodinu, která se synchronizuje, a uvolní místo na disku, abyste dosáhli volného místa na 99% svazku.
Je možné, že Robocopy přesouvá soubory rychleji, než je můžete synchronizovat s cloudem a vrstvou místně, takže na místním disku dochází místo. Příkaz Robocopy se nezdaří. Doporučujeme, abyste procházeli prostřednictvím sdílených složek v sekvenci, která to brání. Například nespouštíte úlohy Robocopy pro všechny sdílené složky ve stejnou dobu nebo pouze přesunutím sdílených složek, které odpovídají aktuálnímu volnému místu na Windows serveru, abyste si vyuváděli pár.

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
      /UNILOG:\<file name\>
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
      Umožňuje několikrát spustit tento příkaz Robocopy na stejném cíli nebo cíli. Určuje, co se zkopírovalo dřív, a vynechá ho. Budou zpracovány pouze změny, přidání a " *odstranění* ", k nimž došlo od posledního spuštění. Pokud příkaz neběžel dřív, nevynechá se nic. Příznak */Mir* je vynikající možností pro umístění zdrojů, která se pořád aktivně používají a mění.
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

## <a name="phase-8-user-cut-over"></a>Fáze 8: vyjmutí uživatele z převzetí

Při prvním spuštění příkazu Robocopy budou uživatelé a aplikace pořád přistupovat k souborům na serveru NAS a můžou je měnit. Je možné, že Robocopy zpracovaly adresář, přesune se k dalšímu a pak uživatel na zdrojovém umístění přidá, změní nebo odstraní soubor, který se teď v tomto aktuálním běhu Robocopy nezpracovává. Jde o očekávané chování.

Prvním spuštěním je o přesunu velkých objemů dat na Windows Server a do cloudu prostřednictvím Azure File Sync. Tato první kopie může trvat dlouhou dobu, v závislosti na:

* Šířka pásma ke stažení
* Šířka pásma nahrávání
* rychlost místní sítě a počet, jak optimálně je počet vláken, která se v nástroji Robocopy shoduje
* počet položek (soubory a složky), které je potřeba zpracovat pomocí nástroje Robocopy a Azure File Sync

Po dokončení počátečního spuštění znovu spusťte příkaz.

Druhý čas se dokončí rychleji, protože potřebuje pouze přenést změny, ke kterým došlo od posledního spuštění. V průběhu tohoto druhého běhu se stále mohou nashromáždit nové změny.

Tento postup opakujte, dokud nebudete přesvědčeni, že doba potřebná k dokončení nástroje Robocopy pro konkrétní umístění spadá do přijatelného okna pro výpadky.

Když posuzujete nepotřebné výpadky a jste připraveni přejít na umístění serveru NAS do režimu offline: aby uživatel mohl pracovat offline, máte možnost změnit seznamy řízení přístupu v kořenovém adresáři sdílené složky tak, aby uživatelé nemuseli mít přístup k umístění nebo provedli jiný vhodný krok, který brání změně obsahu v této složce na serveru NAS.

Spusťte jeden poslední příkaz Robocopy. V takovém případě se zobrazí všechny změny, které mohly být vynechány.
Doba, po kterou tento poslední krok trvá, závisí na rychlosti kontroly nástroje Robocopy. Můžete odhadnout čas (který se rovná vašemu výpadku), a to měřením doby trvání předchozího běhu.

Vytvořte sdílenou složku ve složce Windows serveru a případně upravte nasazení DFS-N tak, aby odkazovalo na něj. Nezapomeňte nastavit stejné oprávnění na úrovni sdílení jako u sdílené složky SMB serveru NAS. Pokud máte server NAS připojený k doméně na podnikové úrovni, pak se identifikátory SID uživatelů automaticky shodují s tím, jak uživatelé existují ve službě Active Directory, a Robocopy kopíruje soubory a metadata s plnou věrností. Pokud jste na svém serveru NAS používali místní uživatele, musíte tyto uživatele znovu vytvořit jako místní uživatele Windows serveru a namapovat stávající nástroje Robocopy, které jste přesunuli na Windows Server, na identifikátory SID nových místních uživatelů Windows serveru.

Dokončili jste migraci sdílené složky nebo skupiny sdílených složek do společného kořenového adresáře nebo svazku. (V závislosti na mapování ze fáze 1)

Můžete zkusit spustit několik z těchto kopií paralelně. Doporučujeme, abyste v jednom okamžiku zpracovali obor jedné sdílené složky Azure.

> [!WARNING]
> Po přesunutí všech dat z vašeho serveru NAS do systému Windows Server a dokončení migrace se vraťte do složky * **všechny** skupiny synchronizace v Azure Portal a nastavte procentuální hodnotu volného místa na úrovni cloudu na něco lépe využívaného pro využití mezipaměti, řekněme, že 20%. 

Zásada pro volné místo svazku ve vrstvách cloudu funguje na úrovni svazku s potenciálně synchronizovanými koncovými body serveru. Pokud zapomenete upravit volné místo na jednom koncovém bodu serveru, bude synchronizace dál používat nejvíce omezující pravidlo a pokusí se zachovávat 99% volného místa na disku, takže místní mezipaměť nefunguje, protože byste mohli očekávat. Pokud se nejedná o váš cíl jenom pro svazek, který obsahuje jenom zřídka využívaný, archivní data a vy znovu zachováte zbývající část prostoru úložiště pro jiný scénář.

## <a name="troubleshoot"></a>Řešení potíží

Nejpravděpodobnějším problémem, ke kterému můžete spustit, je, že příkaz Robocopy se na straně serveru Windows nezdařil s názvem "svazek je plný" *. Vrstvení cloudu slouží jednou za hodinu k vyvádění obsahu z místního disku Windows serveru, který se synchronizuje. Jeho cílem je dosáhnout 99% volného místa na svazku.

Umožněte synchronizaci v průběhu a vrstvení cloudu uvolněte místo na disku. Můžete si všimnout, že v Průzkumníkovi souborů na vašem Windows serveru.

Pokud má Windows Server dostatek dostupné kapacity, problém vyřešíte tak, že znovu spustíte příkaz. Po dosažení této situace se nic neukončí a můžete se přesunout vpřed s jistotou. Nepohodlí spuštění příkazu je jediné, co je to v tomto důsledku.

Pokud chcete řešit potíže s Azure File Sync problémy, podívejte se na odkaz v následující části.

## <a name="next-steps"></a>Další kroky

K dispozici je více informací o sdílených složkách a Azure File Sync Azure. Následující články vám pomůžou pochopit pokročilé možnosti, osvědčené postupy a také pomoc při odstraňování potíží. Tyto články v případě potřeby odkazují na [dokumentaci ke sdílení souborů Azure](storage-files-introduction.md) .

* [Přehled AFS](./storage-sync-files-planning.md)
* [Průvodce nasazením pro AFS](storage-files-deployment-guide.md)
* [Řešení potíží AFS](storage-sync-files-troubleshoot.md)