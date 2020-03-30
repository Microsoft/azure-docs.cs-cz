---
title: Místní migrace NAS do Azure File Sync
description: Zjistěte, jak migrovat soubory z místního umístění network attached storage (NAS) do hybridního cloudového nasazení pomocí Azure File Sync a sdílených složek Azure.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7b0c7a30580d3863a78e85b8b45287a598bbf394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247346"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migrace ze síťového úložiště (NAS) do hybridního cloudového nasazení s Azure File Sync

Azure File Sync funguje v umístěních úložiště s přímým připojením (DAS) a nepodporuje synchronizaci s umístěními úložiště připojeného k síti (NAS).
Tato skutečnost je migrace souborů nezbytné a tento článek vás provede plánování a provádění takové migrace.

## <a name="migration-goals"></a>Cíle migrace

Cílem je přesunout sdílené složky, které máte na zařízení NAS, na systém Windows Server. Pak využijte Azure File Sync pro nasazení hybridního cloudu. Tuto migraci je třeba provést způsobem, který zaručí integritu výrobních dat a dostupnost během migrace. Ten vyžaduje udržení prostojů na minimu, aby se veešel do pravidelná okna údržby nebo je jen mírně překračoval.

## <a name="migration-overview"></a>Přehled migrace

Jak je uvedeno v [článku přehled migrace](storage-files-migration-overview.md)souborů Azure , použití nástroje pro správnou kopírování a přístup u je důležité. Zařízení NAS vystavuje sdílené složky SMB přímo v místní síti. RoboCopy, vestavěný do Windows Serveru, je nejlepší způsob, jak přesunout soubory v tomto scénáři migrace.

- Fáze 1: [Zjistěte, kolik sdílených složek Azure potřebujete](#phase-1-identify-how-many-azure-file-shares-you-need)
- Fáze 2: [Zřízení vhodného místního systému Windows Server](#phase-2-provision-a-suitable-windows-server-on-premises)
- Fáze 3: [Nasazení cloudového prostředku Azure File Sync](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- Fáze 4: [Nasazení prostředků úložiště Azure](#phase-4-deploy-azure-storage-resources)
- Fáze 5: [Nasazení agenta synchronizace souborů Azure](#phase-5-deploy-the-azure-file-sync-agent)
- Fáze 6: [Konfigurace synchronizace souborů Azure na Windows Serveru](#phase-6-configure-azure-file-sync-on-the-windows-server)
- Fáze 7: [RoboCopy](#phase-7-robocopy)
- Fáze 8: [Přestřiháno uživatele](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fáze 1: Zjistěte, kolik sdílených složek Azure potřebujete

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>Fáze 2: Zřízení vhodného místního systému Windows Server

* Vytvořte Windows Server 2019 – minimálně 2012R2 – jako virtuální počítač nebo fyzický server. Je také podporován cluster s podporou převzetí služeb při selhání systému Windows Server.
* Zřízení nebo přidání přímo připojeného úložiště (DAS ve srovnání s NAS, který není podporován).

    Pokud používáte funkci azure file syncs [cloud tiering,](storage-sync-cloud-tiering.md) může být velikost úložiště, které zřídíte, menší než aktuálně používáte na zařízení NAS.
    Pokud však zkopírujete soubory z většího prostoru NAS na menší svazek Windows Serveru v pozdější fázi, budete muset pracovat v dávkách:

    1. Přesunutí sady souborů, které se vejdou na disk
    2. nechat synchronizaci souborů a vrstvení cloudu zapojit
    3. Při vytváření většího volného místa na svazku pokračujte další dávkou souborů. 
    
    Tomuto dávkovému přístupu se můžete vyhnout zřízením ekvivalentního místa na systému Windows Server, které vaše soubory zabírají na zařízení NAS. Zvažte odstranění duplicit na NAS / Windows. Pokud nechcete trvale potvrdit toto vysoké množství úložiště do windows serveru, můžete snížit velikost svazku po migraci a před úpravou zásad vrstvení cloudu. To vytvoří menší místní mezipaměť sdílených složek Azure.

Konfigurace prostředků (výpočetní prostředky a paměť RAM) systému Windows Server, který nasazujete, závisí především na počtu položek (souborů a složek), které budete synchronizovat. Pokud máte nějaké obavy, doporučujeme použít konfiguraci s vyšším výkonem.

[Přečtěte si, jak velikost systému Windows Server na základě počtu položek (souborů a složek), které potřebujete synchronizovat.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> Dříve propojený článek představuje tabulku s rozsahem pro paměť serveru (RAM). Můžete se orientovat na menší číslo pro váš server, ale očekávat, že počáteční synchronizace může trvat podstatně déle.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fáze 3: Nasazení cloudového prostředku Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fáze 4: Nasazení prostředků úložiště Azure

V této fázi se podívejte do tabulky mapování z fáze 1 a použijte ji k zajištění správného počtu účtů úložiště Azure a sdílených složek v nich.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fáze 5: Nasazení agenta synchronizace souborů Azure

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>Fáze 6: Konfigurace synchronizace souborů Azure na Windows Serveru

Pro tento proces musí být registrovaný místní systém Windows Server připraven a připojen k Internetu.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Vrstvení cloudu je funkce AFS, která umožňuje místnímu serveru mít menší kapacitu úložiště, než je uloženo v cloudu, ale mají k dispozici celý obor názvů. Místně zajímavá data jsou také uložena do mezipaměti místně pro rychlý přístup k výkonu. Vrstvení cloudu je volitelná funkce pro synchronizaci souborů Azure "koncový bod serveru".

> [!WARNING]
> Pokud jste na svazcích serveru Windows zřídíte méně úložného prostoru než data používaná na zařízení NAS, je vrstvení v cloudu povinné. Pokud nezapnete vrstvení cloudu, server neuvolní místo pro uložení všech souborů. Nastavte zásady vrstvení, dočasně pro migraci, na 99 % volného místa na svazku. Nezapomeňte se po dokončení migrace vrátit k nastavení vrstvení cloudu a nastavte ho na dlouhodobější užitečnou úroveň.

Opakujte kroky vytvoření skupiny synchronizace a přidání odpovídající serverové složky jako koncového bodu serveru pro všechny sdílené složky Azure / umístění serveru, které je třeba nakonfigurovat pro synchronizaci.

Po vytvoření všech koncových bodů serveru synchronizace funguje. Můžete vytvořit testovací soubor a zobrazit jeho synchronizaci z umístění serveru do připojené sdílené složky Azure (jak je popsáno koncovým bodem cloudu ve skupině synchronizace).

Obě umístění, složky serveru a sdílené složky Azure jsou jinak prázdné a čekají na data v obou umístěních. V dalším kroku začnete kopírovat soubory do Windows Server pro Azure File Sync přesunout do cloudu. V případě, že jste povolili vrstvení cloudu, server pak začne vrstvit soubory, pokud vám dojde kapacita na místním svazku (svazcích).

## <a name="phase-7-robocopy"></a>Fáze 7: RoboCopy

Základním přístupem migrace je RoboCopy z vašeho zařízení NAS do windows serveru a Azure File Sync do sdílených složek Azure.

Spusťte první místní kopii do cílové složky systému Windows Server:

* Identifikujte první umístění na zařízení NAS.
* Identifikujte odpovídající složku na Windows Serveru, která už má na sobě nakonfigurovanou Azure File Sync.
* Spuštění kopie pomocí RoboCopy

Následující příkaz RoboCopy zkopíruje soubory z úložiště NAS do cílové složky systému Windows Server. Windows Server ho synchronizuje se sdílenou složkou Azure. 

Pokud jste na Windows Serveru zřídí méně místa, než se soubory zabírají na zařízení NAS, nakonfigurovali jste vrstvení cloudu. Jak se místní svazek Windows Serveru zaplní, spustí [se vrstvení cloudu](storage-sync-cloud-tiering.md) a soubory úrovní, které se již úspěšně synchronizovaly. Vrstvení cloudu vytvoří dostatek místa pro pokračování v kopírování ze zařízení NAS. Vrstvení cloudu kontroluje jednou za hodinu, abyste zjistili, co se synchronizovalo, a uvolnili místo na disku, abyste dosáhli volného místa na 99 % svazku.
Je možné, že RoboCopy přesouvá soubory rychleji, než můžete synchronizovat s cloudem a vrstvou místně, čímž dochází místní místo na disku. RoboCopy selže. Doporučujese pracovat prostřednictvím sdílených složek v pořadí, které tomu brání. Například nespouštět úlohy RoboCopy pro všechny sdílené složky současně nebo pouze přesunout sdílené složky, které se vejdou na aktuální množství volného místa na Windows Server, abych zmínil několik.

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
      /UNILOG:\<název souboru\>
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
      Umožňuje spustit tento příkaz RoboCopy několikrát, postupně na stejném cíli / cíli. Identifikuje, co bylo zkopírováno dříve a vyneche ji. Budou zpracovány pouze změny, dodatky a "*odstranění*", ke kterým došlo od posledního spuštění. Pokud příkaz nebyl spuštěn dříve, nic není vynecháno. Příznak */MIR* je vynikající volbou pro zdrojová umístění, která jsou stále aktivně používána a mění.
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

## <a name="phase-8-user-cut-over"></a>Fáze 8: Přestřiháno uživatele

Při prvním spuštění příkazu RoboCopy uživatelé a aplikace stále přistupují k souborům na NAS a potenciálně je mění. Je možné, že RoboCopy zpracoval adresář, přesune se na další a pak uživatel na zdrojovém umístění (NAS) přidá, změní nebo odstraní soubor, který nyní nebude zpracován v tomto aktuálním běhu RoboCopy. Jde o očekávané chování.

První spuštění je o přesunutí převážné části dat do windows serveru a do cloudu prostřednictvím Azure File Sync. Tato první kopie může trvat dlouhou dobu, v závislosti na:

* šířku pásma stahování
* šířka pásma pro nahrávání
* rychlost místní sítě a počet, jak optimálně se počet vláken RoboCopy shoduje
* počet položek (souborů a složek), které je třeba zpracovat robocopy a Azure File Sync

Po dokončení počátečního spuštění spusťte příkaz znovu.

Podruhé bude dokončena rychleji, protože je třeba pouze k přenosu změn, ke kterým došlo od posledního spuštění. Během tohoto druhého spuštění se stále mohou hromadit nové změny.

Tento postup opakujte, dokud se nepřesvědčíte, že doba, kterou trvá dokončení RoboCopy pro určité místo, je v přijatelném okně pro prostoje.

Když považujete prostoje za přijatelné a jste připraveni převést umístění NAS do provozu: Chcete-li přístup uživatelů offline, máte možnost změnit počet alokací ACv v kořenovém adresáři sdílené složky tak, aby uživatelé již nemohli přistupovat k umístění nebo provést jiný vhodný krok , který zabraňuje změně obsahu v této složce na nas.

Projed poslední robocopy kolo. Zachytí všechny změny, které mohly být vynechány.
Jak dlouho trvá tento poslední krok, závisí na rychlosti skenování RoboCopy. Čas (který se rovná prostojům) můžete odhadnout měřením doby trvalou předchozí spuštění.

Vytvořte sdílenou složku ve složce Windows Server a případně upravte nasazení dfs-n tak, aby na něj ukazovalo. Nezapomeňte nastavit stejná oprávnění na úrovni sdílené složky jako ve sdílené složce SMB na NAS. Pokud jste měli na sys spojený s doménou podnikové třídy, budou se uživatelská rozhraní SID automaticky shodovat s tím, jak uživatelé existují ve službě Active Directory a RoboCopy kopíruje soubory a metadata s plnou věrností. Pokud jste na serveru NAS použili místní uživatele, je třeba tyto uživatele znovu vytvořit jako místní uživatele systému Windows Server a namapovat stávající zařízení SID, které roboCopy přesunula na systém Windows Server, na sidy nových místních uživatelů systému Windows Server.

Dokončili jste migraci sdílené složky nebo skupiny sdílených složek do společného kořenového adresáře nebo svazku. (V závislosti na mapování z fáze 1)

Můžete zkusit spustit několik z těchto kopií paralelně. Doporučujeme zpracovat obor jedné sdílené složky Azure najednou.

> [!WARNING]
> Po přesunutí všech dat z NAS do Windows Serveru a dokončení migrace: Vraťte se do ***všech*** synchronizačních skupin na webu Azure Portal a upravte hodnotu volného místa na svazku cloudového vrstvení na hodnotu vhodnější pro využití mezipaměti, řekněme 20 %. 

Zásady volného místa na svazku vrstvení v cloudu působí na úrovni svazku s potenciálně více koncovými body serveru, které se z něj synchronizují. Pokud zapomenete upravit volné místo i na jednom koncovém bodu serveru, synchronizace bude nadále používat nejvíce omezující pravidlo a pokusí se zachovat 99 % volného místa na disku, takže místní mezipaměť nebude fungovat tak, jak byste očekávali. Pokud není vaším cílem mít pouze obor názvů pro svazek, který obsahuje pouze zřídka přístupná archivní data a vyhrazujete si zbývající úložný prostor pro jiný scénář.

## <a name="troubleshoot"></a>Řešení potíží

Nejpravděpodobnějšíproblém, na který můžete narazit, je, že příkaz RoboCopy selže s *"Plný svazek"* na straně Windows Server. Vrstvení cloudu provádí jednou za hodinu k evakuaci obsahu z místního disku Windows Server, který byl synchronizován. Jeho cílem je dosáhnout 99% volného místa na svazku.

Uvolněte průběh synchronizace a vrstvení cloudu. Můžete pozorovat, že v Průzkumníkovi souborů na vašem Windows Server.

Pokud má systém Windows Server dostatečnou dostupnou kapacitu, problém vyřešíte opětovnou spuštěním příkazu. Nic se nerozbije, když se dostanete do této situace a můžete se pohybovat vpřed s důvěrou. Nepříjemnosti spuštění příkazu znovu je jediným důsledkem.

V následující části najdete odkaz na řešení potíží se synchronizací souborů Azure.

## <a name="next-steps"></a>Další kroky

O sdílených sporejích souborů Azure a Azure File Sync se dozvíte víc. Následující články pomáhají porozumět pokročilým možnostem, doporučeným postupům a obsahují také nápovědu k řešení potíží. Tyto články odkazují na [dokumentaci ke sdílení souborů Azure](storage-files-introduction.md) podle potřeby.

* [Přehled AFS](https://aka.ms/AFS)
* [Průvodce nasazením AFS](storage-files-deployment-guide.md)
* [Řešení potíží s AFS](storage-sync-files-troubleshoot.md)
