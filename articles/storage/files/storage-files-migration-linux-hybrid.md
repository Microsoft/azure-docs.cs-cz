---
title: Migrace pro Linux na Synchronizace souborů Azure
description: Naučte se migrovat soubory z umístění serveru Linux do hybridního cloudu s Synchronizace souborů Azure a sdílenými složkami Azure.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: ff26318cafdf493579961fc718643f831ae9efeb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564250"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migrace ze systému Linux na nasazení do hybridního cloudu pomocí Synchronizace souborů Azure

Tento článek migrace je jedním z několika, které zahrnují klíčová slova NFS a Synchronizace souborů Azure. Podívejte se, jestli tento článek platí pro váš scénář:

> [!div class="checklist"]
> * Zdroj dat: úložiště připojené k síti (NAS)
> * Trasa migrace: Linux Server se &rArr; systémem Samba Windows server 2012R2 nebo novější &rArr; synchronizace se sdílenými složkami Azure
> * Místní ukládání souborů do mezipaměti: Ano, konečným cílem je nasazení Synchronizace souborů Azure.

Pokud je váš scénář jiný, podívejte se do [tabulky Průvodce migrací](storage-files-migration-overview.md#migration-guides).

Synchronizace souborů Azure funguje na instancích Windows serveru s přímým připojeným úložištěm (DAS). Nepodporuje synchronizaci do a z klientů se systémem Linux nebo sdílené složky protokolu SMB (Server Message Block) nebo sdílené složky systému souborů NFS (Network File System).

Výsledkem je, že transformace souborové služby do hybridního nasazení vede k tomu, že je potřeba migrovat na Windows Server. Tento článek vás provede plánováním a prováděním této migrace.

## <a name="migration-goals"></a>Cíle migrace

Cílem je přesunout sdílené složky, které máte na serveru Linux Samba, do instance Windows serveru. Pak použijte Synchronizace souborů Azure pro hybridní nasazení v cloudu. Tato migrace se musí udělat způsobem, který zaručuje integritu produkčních dat a dostupnosti během migrace. Ta ta vyžaduje udržení minimálního výpadku, aby bylo možné se přizpůsobit nebo jen mírně překročit pravidelná časová období údržby.

## <a name="migration-overview"></a>Přehled migrace

Jak je uvedeno v [článku Přehled migrace](storage-files-migration-overview.md)souborů Azure, je důležité použít správný nástroj pro kopírování a přístup. Server Linux Samba zveřejňuje sdílené složky SMB přímo v místní síti. V tomto scénáři migrace jsou soubory Robocopy integrované v systému Windows Server nejvhodnější k přesunutí souborů.

Pokud na serveru Linux nepoužíváte službu Samba a místo toho chcete migrovat složky do hybridního nasazení na Windows serveru, můžete místo nástroje Robocopy použít nástroje pro kopírování pro Linux. Seznamte se s možnostmi věrnosti vašeho nástroje pro kopírování. V [části Základy migrace](storage-files-migration-overview.md#migration-basics) v článku Přehled migrace najdete informace o tom, co je potřeba najít v nástroji pro kopírování.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fáze 1: určení, kolik sdílených složek Azure potřebujete

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-instance-on-premises"></a>Fáze 2: zřízení vhodné instance Windows serveru v místním prostředí

* Vytvořte instanci Windows serveru 2019 jako virtuální počítač nebo fyzický server. Minimálním požadavkem je Windows Server 2012 R2. Podporuje se i cluster s podporou převzetí služeb při selhání se systémem Windows Server.
* Zřídit nebo přidat přímo připojené úložiště (DAS). Úložiště připojené k síti (NAS) není podporováno.

  Velikost úložiště, kterou zřídíte, může být menší než v současnosti na serveru Linux Samba, pokud používáte funkci Synchronizace souborů Azureho [cloudového vrstvení](storage-sync-cloud-tiering-overview.md) . 

Velikost úložiště, kterou zřizujete, může být menší než ta, kterou aktuálně používáte na serveru Linux Samba. Tato volba konfigurace vyžaduje, abyste také využívali funkci [cloudových vrstev](storage-sync-cloud-tiering-overview.md) Azure File Sync. Když ale kopírujete soubory z většího prostoru serveru Linux Samba na menší svazek Windows serveru v pozdější fázi, budete muset pracovat v dávkách:

  1. Přesuňte sadu souborů, které se vejdou na disk.
  2. Umožněte synchronizaci souborů a vrstvení cloudu.
  3. Pokud je na svazku vytvořeno více volného místa, pokračujte dalším dávkou souborů. Další možností je zkontrolovat pomocí příkazu Robocopy v [oddílu nadcházející Robocopy](#phase-7-robocopy) použití nového `/LFSM` přepínače. Použití nástroje `/LFSM` může výrazně zjednodušit úlohy Robocopy, ale není kompatibilní s některými jinými přepínači Robocopy, na kterých byste měli.
    
  Tomuto přístupu k dávkám se můžete vyhnout tím, že zřizujete stejné místo na instanci Windows serveru, kterou soubory zabírají na serveru Linux Samba. Zvažte možnost povolit odstraňování duplicitních dat ve Windows. Pokud nechcete trvale zasílat této velikosti úložiště do vaší instance Windows serveru, můžete zmenšit velikost svazku po migraci a před úpravou zásad cloudové vrstvy. Tím se vytvoří menší místní mezipaměť sdílených složek Azure.

Konfigurace prostředků (COMPUTE a RAM) instance Windows serveru, kterou nasazujete, závisí hlavně na počtu položek (soubory a složky), které budete synchronizovat. Pokud máte nějaké obavy, doporučujeme, abyste se s vyšším výkonem přečetli.

[Naučte se, jak nastavit velikost instance Windows serveru na základě počtu položek (souborů a složek), které potřebujete synchronizovat.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> Dříve propojený článek prezentuje tabulku s rozsahem pro paměť serveru (RAM). Můžete se orientovat směrem k menšímu počtu serverů, ale očekávat, že počáteční synchronizace může trvat delší dobu.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fáze 3: nasazení cloudového prostředku Synchronizace souborů Azure

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fáze 4: nasazení prostředků služby Azure Storage

V této fázi si Projděte tabulku mapování z fáze 1 a použijte ji ke zřízení správného počtu účtů úložiště Azure a sdílených složek v nich.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fáze 5: nasazení agenta Synchronizace souborů Azure

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server-deployment"></a>Fáze 6: Konfigurace Synchronizace souborů Azure na nasazení Windows serveru

Zaregistrovaná místní instance Windows serveru musí být pro tento proces připravená a připojená k Internetu.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Vrstvení cloudu je funkce Synchronizace souborů Azure, která umožňuje místnímu serveru mít méně úložnou kapacitu, než je uložený v cloudu, ale má úplný obor názvů k dispozici. Místně zajímavá data se ukládají také místně do mezipaměti pro výkon s rychlým přístupem. Vrstva cloudu je volitelná funkce pro každý koncový bod serveru Synchronizace souborů Azure.

> [!WARNING]
> Pokud jste na svazcích Windows serveru zřídili méně úložiště než vaše data, která se používají na serveru Linux Samba, je vrstva cloudu povinná. Pokud nepovolíte vrstvení cloudu, váš server nebude mít volné místo pro uložení všech souborů. Nastavte zásady vrstvení dočasně pro migraci na 99% volného místa pro svazek. Po dokončení migrace se ujistěte, že se vrátíte k nastavení vrstvení cloudu a v dlouhodobém období nastavte zásady na užitečnější úroveň.

Opakujte kroky při vytváření skupiny synchronizace a přidání odpovídající složky serveru jako koncového bodu serveru pro všechny sdílené složky Azure a umístění serverů, která je potřeba nakonfigurovat pro synchronizaci.

Po vytvoření všech koncových bodů serveru synchronizace funguje. Můžete vytvořit testovací soubor a vidět jeho synchronizaci z umístění na serveru s připojenou sdílenou složkou Azure (jak popisuje koncový bod cloudu ve skupině synchronizace).

Obě umístění, složky serveru a sdílené složky Azure jsou jinak prázdná a čekají na data. V dalším kroku začnete kopírovat soubory do instance Windows serveru, aby se Synchronizace souborů Azure přesunuly do cloudu. Pokud jste povolili vrstvení cloudu, server pak začne na úrovni souborů, pokud na místních svazcích dojde k nedostatku kapacity.

## <a name="phase-7-robocopy"></a>Fáze 7: Robocopy

Základní postup migrace je použití nástroje Robocopy ke kopírování souborů a použití Synchronizace souborů Azure k synchronizaci.

Spusťte první místní kopii do cílové složky Windows serveru:

1. Identifikujte první umístění na serveru Linux Samba.
1. Identifikujte odpovídající složku na instanci Windows serveru, která už je Synchronizace souborů Azure nakonfigurovaná.
1. Spusťte kopii pomocí nástroje Robocopy.

Následující příkaz Robocopy zkopíruje soubory ze svého úložiště serveru Linux Samba do cílové složky Windows serveru. Windows Server ho synchronizuje se sdílenými složkami Azure. 

Pokud jste v instanci Windows serveru zřídili méně úložiště, než vaše soubory zabírají na serveru Linux Samba, nakonfigurujete tak cloudovou vrstvu. Vzhledem k to, že místní svazek Windows serveru se stane plným, budou se [vrstvy cloudu](storage-sync-cloud-tiering-overview.md) spouštět a soubory vrstev, které se úspěšně synchronizovaly, už. Vrstvení cloudu vytvoří dostatek místa pro pokračování kopie ze serveru Linux Samba. Vrstvení cloudu kontroluje jednu hodinu, která se synchronizuje, a uvolní místo na disku, abyste dosáhli zásad 99 procent volného místa pro svazek.

Je možné, že Robocopy přesouvá soubory rychleji, než je můžete synchronizovat s cloudem a vrstvou místně, což způsobí, že dojde místo na místním disku. Příkaz Robocopy se pak nezdaří. Doporučujeme, abyste ve svých sdílených složkách pracovali v sekvenci, která brání problému. Zvažte například, že nebudete spouštět úlohy nástroje Robocopy pro všechny sdílené složky ve stejnou dobu. Nebo zvažte přesunutí sdílených složek, které odpovídají aktuálnímu množství volného místa v instanci systému Windows Server. Pokud vaše úloha Robocopy selže, můžete vždy znovu spustit příkaz, pokud použijete následující možnost zrcadlení/vyprázdnění:

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-8-user-cut-over"></a>Fáze 8: vyjmutí uživatele z převzetí

Při prvním spuštění příkazu Robocopy budou uživatelé a aplikace pořád používat soubory na serveru Linux Samba a můžou je měnit. Je možné, že Robocopy zpracovaly adresář a přesune se k dalšímu a pak uživatel ve zdrojovém umístění (Linux) přidá, změní nebo odstraní soubor, který se teď v aktuálním běhu nástroje Robocopy nezpracovává. Jde o očekávané chování.

Prvním spuštěním je o přesunu velkých objemů dat do instance Windows serveru a do cloudu prostřednictvím Synchronizace souborů Azure. Tato první kopie může trvat dlouhou dobu, v závislosti na:

* Šířku pásma pro stažení.
* Šířka pásma nahrávání.
* Rychlost místní sítě a počet, jak optimálně se počet vláken v prostředí Robocopy shoduje.
* Počet položek (soubory a složky), které nástroje Robocopy a Synchronizace souborů Azure potřebují zpracovat.

Po dokončení počátečního běhu znovu spusťte příkaz.

Tím se čas dokončí rychleji, protože potřebuje přenést pouze změny, ke kterým došlo od posledního spuštění. Během této sekundy se stále můžou shromažďovat nové změny.

Tento postup opakujte, dokud nebudete přesvědčeni, že doba potřebná k dokončení operace Robocopy pro konkrétní umístění spadá do přijatelného okna pro výpadky.

Když zvažujete nedodržení a jste připravení na umístění systému Linux v režimu offline, můžete změnit seznamy ACL v kořenovém adresáři sdílené složky tak, že uživatelé nebudou mít přístup k umístění. Případně můžete provést libovolný jiný vhodný krok, který zabrání změně obsahu v této složce na serveru Linux.

Spusťte jeden poslední příkaz Robocopy. Vybírá všechny změny, které se mohly vyzradit. Doba, po kterou tento poslední krok trvá, závisí na rychlosti kontroly nástroje Robocopy. Můžete odhadnout čas (který se rovná vašemu výpadku), a to měřením doby trvání předchozího běhu.

Vytvořte sdílenou složku ve složce Windows serveru a případně upravte nasazení DFS-N tak, aby odkazovalo na něj. Nezapomeňte nastavit stejné oprávnění na úrovni sdílení jako u sdílených složek SMB serveru Linux Samba. Pokud jste na serveru Linux Samba používali místní uživatele, musíte tyto uživatele znovu vytvořit jako místní uživatele Windows serveru. Také je nutné namapovat existující identifikátory SID, které Robocopy přesunuly do vaší instance Windows serveru, na identifikátory SID nových místních uživatelů Windows serveru. Pokud jste použili účty služby Active Directory a seznamy řízení přístupu (ACL), program Robocopy je přesune tak, jak jsou, a není nutné provádět žádné další akce.

Dokončili jste migraci sdílené složky nebo skupiny sdílených složek do společného kořenového adresáře nebo svazku (v závislosti na mapování ze fáze 1).

Můžete zkusit spustit několik z těchto kopií paralelně. Doporučujeme, abyste v jednom okamžiku zpracovali obor jedné sdílené složky Azure.

> [!WARNING]
> Po přesunutí všech dat ze serveru Linux Samba do instance Windows serveru a dokončení migrace se vraťte do *všech*  skupin synchronizace v Azure Portal. Upravte procentuální hodnotu volného místa pro svazek vrstev cloudu na něco lépe vhodné pro využití mezipaměti, jako je například 20 procent. 

Zásada pro volné místo ve svazku na úrovni cloudu funguje na úrovni svazku s potenciálně synchronizovanými koncovými body serveru. Pokud zapomenete upravit volné místo na jednom koncovém bodu serveru, bude synchronizace dál používat nejvíce omezující pravidlo a pokusí se udržet volné místo na disku 99 procent. Místní mezipaměť pak nemusí fungovat podle očekávání. Výkon může být přijatelný, pokud vaším cílem je mít obor názvů pro svazek, který obsahuje jenom zřídka používaná archivní data, a Vy zachováte zbývající část prostoru úložiště pro jiný scénář.

## <a name="troubleshoot"></a>Řešení potíží

Nejběžnějším problémem je to, že příkaz Robocopy se na straně serveru Windows nezdařil s **plným svazkem** . Vrstvení cloudu slouží jednou za hodinu k vyvádění obsahu z místního disku s Windows serverem, který se synchronizuje. Jeho cílem je dosáhnout volného místa na svazku o 99 procent.

Umožněte synchronizaci v průběhu a vrstvení cloudu uvolněte místo na disku. To můžete sledovat v Průzkumníkovi souborů v systému Windows Server.

Pokud má vaše instance Windows serveru dostatek dostupné kapacity, problém vyřešíte tak, že znovu spustíte příkaz. Po obdržení této situace se nic neukončí a můžete se s jistotou přesunout vpřed. Komplikace při opakovaném spuštění příkazu jsou pouze v důsledku.

Pokud chcete řešit potíže s Synchronizace souborů Azure problémy, podívejte se na odkaz v následující části.

## <a name="next-steps"></a>Další kroky

K dispozici je více informací o sdílených složkách a Synchronizace souborů Azure Azure. Následující články obsahují pokročilé možnosti, osvědčené postupy a pomoc při odstraňování potíží. Tyto články v případě potřeby odkazují na [dokumentaci ke sdílení souborů Azure](storage-files-introduction.md) .

* [Přehled Synchronizace souborů Azure](./storage-sync-files-planning.md)
* [Průvodce nasazením Synchronizace souborů Azure](./storage-how-to-create-file-share.md)
* [Řešení potíží se službou Synchronizace souborů Azure](storage-sync-files-troubleshoot.md)