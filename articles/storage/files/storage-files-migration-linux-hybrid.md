---
title: Migrace Linuxu do Azure File Sync
description: Zjistěte, jak migrovat soubory z umístění na serveru Linux do hybridního cloudového nasazení pomocí Azure File Sync a sdílených složek Azure.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: c80f43d61aeea8aba803267b7908831209812d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247714"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migrace z Linuxu do hybridního cloudového nasazení pomocí Azure File Sync

Azure File Sync funguje na serverech Windows s přímo připojeným úložištěm (DAS). Nepodporuje synchronizaci s Linuxem a z Linuxu nebo vzdálenou sdílenou složku SMB.
V důsledku toho transformace souborových služeb na hybridní nasazení způsobí, že migrace na systém Windows Server bude nezbytná. Tento článek vás provede plánováním a prováděním takové migrace.

## <a name="migration-goals"></a>Cíle migrace

Cílem je přesunout sdílené složky, které máte na serveru Linux Samba, na systém Windows Server. Pak využijte Azure File Sync pro nasazení hybridního cloudu. Tuto migraci je třeba provést způsobem, který zaručí integritu výrobních dat a dostupnost během migrace. Ten vyžaduje udržení prostojů na minimu, aby se veešel do pravidelná okna údržby nebo je jen mírně překračoval.

## <a name="migration-overview"></a>Přehled migrace

Jak je uvedeno v [článku přehled migrace](storage-files-migration-overview.md)souborů Azure , použití nástroje pro správnou kopírování a přístup u je důležité. Váš server Linux Samba vystavuje sdílené složky SMB přímo v místní síti. RoboCopy, vestavěný do Windows Serveru, je nejlepší způsob, jak přesunout soubory v tomto scénáři migrace.

Pokud nepoužíváte Sambu na serveru Linux a chcete migrovat složky do hybridního nasazení na Windows Serveru, můžete místo RoboCopy použít nástroje pro kopírování Linuxu. Pokud tak učiníte, mějte na paměti možnosti věrnosti v nástroji pro kopírování souborů. Přečtěte si [část Základy migrace](storage-files-migration-overview.md#migration-basics) v článku přehled migrace, kde se dozvíte, co hledat v nástroji pro kopírování.

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

    Velikost úložiště, které zřídíte, může být menší než to, co aktuálně používáte na serveru Linux Samba, pokud používáte funkci azure file syncs [cloud tiering.](storage-sync-cloud-tiering.md)
    Pokud však zkopírujete soubory z většího místa na serveru Linux Samba na menší svazek Windows Serveru v pozdější fázi, budete muset pracovat v dávkách:

    1. Přesuňte sadu souborů, která se vejde na disk.
    2. Nechte synchronizaci souborů a vrstvení cloudu zapojit.
    3. Při vytváření více volného místa na svazku pokračujte další dávkou souborů. 
    
    Tomuto dávkovému přístupu se můžete vyhnout zřízením ekvivalentního místa na systému Windows Server, které vaše soubory zabírají na serveru Linux Samba. Zvažte povolení odstranění duplicit v systému Windows. Pokud nechcete trvale potvrdit toto vysoké množství úložiště do windows serveru, můžete snížit velikost svazku po migraci a před úpravou zásad vrstvení cloudu. To vytvoří menší místní mezipaměť sdílených složek Azure.

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
> Pokud jste zřídit méně úložiště na svazcích serveru Windows, než vaše data použitá na serveru Linux Samba, pak cloud vrstvení je povinné. Pokud nezapnete vrstvení cloudu, server neuvolní místo pro uložení všech souborů. Nastavte zásady vrstvení, dočasně pro migraci, na 99 % volného místa na svazku. Nezapomeňte se po dokončení migrace vrátit k nastavení vrstvení cloudu a nastavte ho na dlouhodobější užitečnou úroveň.

Opakujte kroky vytvoření skupiny synchronizace a přidání odpovídající serverové složky jako koncového bodu serveru pro všechny sdílené složky Azure / umístění serveru, které je třeba nakonfigurovat pro synchronizaci.

Po vytvoření všech koncových bodů serveru synchronizace funguje. Můžete vytvořit testovací soubor a zobrazit jeho synchronizaci z umístění serveru do připojené sdílené složky Azure (jak je popsáno koncovým bodem cloudu ve skupině synchronizace).

Obě umístění, složky serveru a sdílené složky Azure jsou jinak prázdné a čekají na data v obou umístěních. V dalším kroku začnete kopírovat soubory do Windows Server pro Azure File Sync přesunout do cloudu. V případě, že jste povolili vrstvení cloudu, server pak začne vrstvit soubory, pokud vám dojde kapacita na místním svazku (svazcích).

## <a name="phase-7-robocopy"></a>Fáze 7: RoboCopy

Základní přístup k migraci je RoboCopy z vašeho serveru Linux Samba do windows serveru a Azure File Sync do sdílených složek Azure.

Spusťte první místní kopii do cílové složky systému Windows Server:

1. Identifikujte první umístění na serveru Linux Samba.
1. Identifikujte odpovídající složku na Windows Serveru, která už má na sobě nakonfigurovanou Azure File Sync.
1. Spusťte kopii pomocí RoboCopy.

Následující příkaz RoboCopy zkopíruje soubory z úložiště serverů Linux Samba do cílové složky systému Windows Server. Windows Server ho synchronizuje se sdílenou složkou Azure. 

Pokud jste zřídili méně úložiště na vašem Windows Serveru, než vaše soubory zabírají na serveru Linux Samba, pak jste nakonfigurovali vrstvení cloudu. Jak se místní svazek Windows Serveru zaplní, spustí [se vrstvení cloudu](storage-sync-cloud-tiering.md) a soubory úrovní, které se již úspěšně synchronizovaly. Vrstvení cloudu vygeneruje dostatek místa pro pokračování v kopírování ze serveru Linux Samba. Vrstvení cloudu kontroluje jednou za hodinu, abyste zjistili, co se synchronizovalo, a uvolnili místo na disku, abyste dosáhli volného místa na 99 % svazku.
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

Když poprvé spustíte příkaz RoboCopy, vaši uživatelé a aplikace stále přistupují k souborům na serveru Linux Samba a potenciálně je mění. Je možné, že RoboCopy zpracoval adresář, přesune se na další a pak uživatel na zdrojovém umístění (Linux) přidá, změní nebo odstraní soubor, který nyní nebude zpracován v tomto aktuálním běhu RoboCopy. Jde o očekávané chování.

První spuštění je o přesunutí převážné části dat do windows serveru a do cloudu prostřednictvím Azure File Sync. Tato první kopie může trvat dlouhou dobu, v závislosti na:

* Vaše stahování šířky pásma.
* Šířka pásma nahrávání.
* Rychlost místní sítě a počet optimálního počtu vláken RoboCopy odpovídá.
* Počet položek (souborů a složek), které je potřeba zpracovat RoboCopy a Azure File Sync.

Po dokončení počátečního spuštění spusťte příkaz znovu.

Podruhé bude dokončena rychleji, protože je třeba pouze k přenosu změn, ke kterým došlo od posledního spuštění. Během tohoto druhého spuštění se stále mohou hromadit nové změny.

Tento postup opakujte, dokud se nepřesvědčíte, že doba, kterou trvá dokončení RoboCopy pro určité místo, je v přijatelném okně pro prostoje.

Když vezmete v úvahu přijatelné prostoje a jste připraveni převést umístění Linuxu do provozu: Chcete-li převést přístup uživatelů do offline, máte možnost změnit počet alokací ACL v kořenovém adresáři sdílené složky tak, aby uživatelé již nemohli přistupovat k umístění nebo přijímat jiné vhodné krok, který zabrání změně obsahu v této složce na serveru Linux.

Projed poslední robocopy kolo. Zachytí všechny změny, které mohly být vynechány.
Jak dlouho trvá tento poslední krok, závisí na rychlosti skenování RoboCopy. Čas (který se rovná prostojům) můžete odhadnout měřením doby trvalou předchozí spuštění.

Vytvořte sdílenou složku ve složce Windows Server a případně upravte nasazení dfs-n tak, aby na něj ukazovalo. Nezapomeňte nastavit stejná oprávnění na úrovni sdílení jako u sdílených složek smb serveru Linux Samba. Pokud jste na serveru Linux Samba použili místní uživatele, je třeba tyto uživatele znovu vytvořit jako místní uživatele systému Windows Server a namapovat stávající sidy, které robokopie přesunula na váš systém Windows Server na SID nových místních uživatelů systému Windows Server. Pokud jste použili účty AD a ACL, RoboCopy je přesune tak, jak jsou, a není nutná žádná další akce.

Dokončili jste migraci sdílené složky nebo skupiny sdílených složek do společného kořenového adresáře nebo svazku. (V závislosti na mapování z fáze 1)

Můžete zkusit spustit několik z těchto kopií paralelně. Doporučujeme zpracovat obor jedné sdílené složky Azure najednou.

> [!WARNING]
> Po přesunutí všech dat ze serveru Linux Samba na Windows Server a migrace je dokončena: Vraťte se do ***všech*** synchronizačních skupin na webu Azure Portal a upravte hodnotu volného místa na svazku cloudového vrstvení na něco vhodnějšího pro využití mezipaměti, řekněme 20 %. 

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
