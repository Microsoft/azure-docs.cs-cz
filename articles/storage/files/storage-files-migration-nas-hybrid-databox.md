---
title: Migrace místních serverů NAS do Synchronizace souborů Azure prostřednictvím Azure DataBox
description: Naučte se migrovat soubory z místního umístění úložiště na síti (NAS) do hybridního cloudového nasazení pomocí Synchronizace souborů Azure přes Azure DataBox.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 144b2f23e40f315441c3de2482ae8aeffe77ec75
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583852"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Použití DataBox k migraci z úložiště připojení k síti (NAS) do hybridního cloudového nasazení pomocí Synchronizace souborů Azure

Tento článek migrace je jedním z několika součástí, které zahrnují klíčová slova NAS, Synchronizace souborů Azure a Azure DataBox. Podívejte se, jestli tento článek platí pro váš scénář:

> [!div class="checklist"]
> * Zdroj dat: úložiště připojené k síti (NAS)
> * Trasa migrace: NAS &rArr; Databox &rArr; sdílené složky Azure &rArr; se synchronizací s Windows serverem
> * Místní ukládání souborů do mezipaměti: Ano, konečným cílem je nasazení Synchronizace souborů Azure.

Pokud je váš scénář jiný, podívejte se do [tabulky Průvodce migrací](storage-files-migration-overview.md#migration-guides).

Synchronizace souborů Azure funguje v umístěních přímo připojeného úložiště (DAS) a nepodporuje synchronizaci s umístěními síťového připojeného úložiště (NAS).
Tato skutečnost provede migraci vašich souborů a tento článek vás provede plánováním a prováděním této migrace.

## <a name="migration-goals"></a>Cíle migrace

Cílem je přesunout sdílené složky, které máte na zařízení NAS, na Windows Server. Pak využijte Synchronizace souborů Azure pro nasazení hybridního cloudu. Tato migrace se musí udělat způsobem, který zaručuje integritu produkčních dat a dostupnosti během migrace. Ta ta vyžaduje udržení minimálního výpadku, aby bylo možné se přizpůsobit nebo jen mírně překročit pravidelná časová období údržby.

## <a name="migration-overview"></a>Přehled migrace

Proces migrace se skládá z několika fází. Budete muset nasadit účty úložiště Azure a sdílené složky, nasadit místní Windows Server, nakonfigurovat Synchronizace souborů Azure, migrovat je pomocí nástroje Robocopy a nakonec provést vyjmutí. V následujících oddílech jsou podrobně popsány fáze procesu migrace.

> [!TIP]
> Pokud se vrátíte k tomuto článku, pomocí navigace na pravé straně přejděte do fáze migrace, kde jste skončili.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fáze 1: určení, kolik sdílených složek Azure potřebujete

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>Fáze 2: nasazení prostředků služby Azure Storage

V této fázi si Projděte tabulku mapování z fáze 1 a použijte ji ke zřízení správného počtu účtů úložiště Azure a sdílených složek v nich.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-databox-appliances-you-need"></a>Fáze 3: určení, kolik zařízení Azure DataBox potřebujete

Tento krok spusťte pouze v případě, že jste dokončili předchozí fázi. V tuto chvíli by se měly vytvořit prostředky služby Azure Storage (účty úložiště a sdílené složky). Během DataBox objednávky musíte zadat, do kterých účtů úložiště DataBox přesouvá data.

V této fázi potřebujete namapovat výsledky plánu migrace z předchozí fáze na limity dostupných možností DataBox. Tyto informace vám pomůžou s plánem, pro který byste si měli vybrat DataBox možnosti a kolik z nich budete potřebovat k přesunutí svých sdílených složek NAS do sdílených složek Azure.

Pokud chcete zjistit, kolik zařízení je potřeba, zvažte tyto důležité limity:

* Jakákoli služba Azure DataBox může přesunout data do až 10 účtů úložiště. 
* Každá možnost DataBox se dostane na svou vlastní použitelnou kapacitu. Viz [Možnosti Databox](#databox-options).

Projděte si plán migrace pro počet účtů úložiště, které jste se rozhodli vytvořit, a sdílené položky v každém z nich. Pak se podívejte na velikost jednotlivých sdílených složek na serveru NAS. Kombinování těchto informací vám umožní optimalizovat a rozhodnout, které zařízení má odesílat data, na které se mají účty úložiště posílat. Můžete mít dvě zařízení DataBox přesunout soubory do stejného účtu úložiště, ale nerozdělovat obsah jedné sdílené složky mezi 2 datapoli.

### <a name="databox-options"></a>DataBox možnosti

U standardní migrace by se měla zvolit jedna nebo kombinace těchto tří DataBox možností: 

* Disky DataBox vám Microsoft pošle jeden a až pět disků SSD s kapacitou 8 TiB, a to maximálně na 40 TiB. Použitelná kapacita je přibližně o 20% méně, kvůli šifrování a režii systému souborů. Další informace najdete v [dokumentaci ke Databox diskům](../../databox/data-box-disk-overview.md).
* DataBox Toto je nejběžnější možnost. Dostanou robustní zařízení DataBox, které funguje podobně jako na serveru NAS. Má použitelnou kapacitu 80 TiB. Další informace najdete v [dokumentaci k Databox](../../databox/data-box-overview.md).
* DataBoxá Tato možnost vyfunguje robustní zařízení DataBox na kolech, které funguje podobně jako u serveru NAS s kapacitou 1 PiB. Použitelná kapacita je přibližně o 20% méně, kvůli šifrování a režii systému souborů. Další informace najdete v [Databox těžké dokumentaci](../../databox/data-box-heavy-overview.md).

## <a name="phase-4-provision-a-suitable-windows-server-on-premises"></a>Fáze 4: zřízení vhodného Windows serveru v místním prostředí

Až budete čekat na doručení Azure DataBox, můžete již začít kontrolovat potřeby jednoho nebo více serverů s Windows, které budete používat s Synchronizace souborů Azure.

* Vytvořte Windows Server 2019 – minimálně 2012R2 jako virtuální počítač nebo fyzický server. Podporuje se taky cluster Windows serveru s podporou převzetí služeb při selhání.
* Zřídit nebo přidat přímo připojené úložiště (DAS ve srovnání se serverem NAS, který není podporován).

Konfigurace prostředků (COMPUTE a RAM) systému Windows Server, který nasazujete, závisí hlavně na počtu položek (soubory a složky), které budete synchronizovat. Pokud máte nějaké obavy, doporučuje se vyšší konfigurace výkonu.

[Naučte se, jak nastavit systém Windows Server podle počtu položek (soubory a složky), které je třeba synchronizovat.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> Dříve propojený článek prezentuje tabulku s rozsahem pro paměť serveru (RAM). Můžete orientovat směrem k menšímu počtu serverů, ale očekávat, že počáteční synchronizace může trvat delší dobu.

## <a name="phase-5-copy-files-onto-your-databox"></a>Fáze 5: zkopírování souborů do DataBox

Až DataBox přijde, budete muset nastavit DataBox na zařízení na serveru NAS. Postupujte podle pokynů k instalaci pro typ DataBox, který jste objednali.

* [Nastavení Data Boxu](../../databox/data-box-quickstart-portal.md)
* [Nastavení Data Box Disku](../../databox/data-box-disk-quickstart-portal.md)
* [Nastavení zařízení Data Box Heavy](../../databox/data-box-heavy-quickstart-portal.md)

V závislosti na typu DataBox jsou k dispozici nástroje pro kopírování DataBox. V tuto chvíli se nedoporučují migrace do sdílených složek Azure, protože nekopírují soubory s plnou věrností do DataBox. Místo toho použijte příkaz Robocopy.

Po přijetí služby DataBox budou mít předem zřízené sdílené složky SMB k dispozici pro každý účet úložiště, který jste zadali v době jeho řazení.

* Pokud vaše soubory přecházejí do sdílené složky Azure Premium, bude pro každý účet úložiště File Storage úrovně Premium jedna sdílená složka SMB.
* Pokud vaše soubory přecházejí do účtu úložiště úrovně Standard, budou se pro účet úložiště Standard (GPv1 a GPv2) jednat o tři sdílené složky SMB. Pro vaši migraci jsou relevantní jenom sdílené složky, které končí `_AzFiles` . Ignorujte všechny sdílené složky objektů blob bloku a stránky.

Postupujte podle kroků v dokumentaci ke službě Azure DataBox:

1. [Připojení k Data Boxu](../../databox/data-box-deploy-copy-data.md)
1. Kopírování dat do Data Boxu
1. [Příprava DataBox k odeslání do Azure](../../databox/data-box-deploy-picked-up.md)

Související dokumentace DataBox Určuje příkaz Robocopy. Tento příkaz však není vhodný pro zachování celé přesnosti souborů a složek. Místo toho použijte tento příkaz:

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>Fáze 6: nasazení cloudového prostředku Synchronizace souborů Azure

Než budete pokračovat v této příručce, počkejte, dokud všechny soubory nebudou doručeny do správných sdílených složek Azure. Proces doručení a ingestování DataBox dat bude trvat čas.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>Fáze 7: nasazení agenta Synchronizace souborů Azure

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server"></a>Fáze 8: Konfigurace Synchronizace souborů Azure na Windows serveru

Váš registrovaný místní Windows Server musí být pro tento proces připravený a připojený k Internetu.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

Zapněte funkci vrstvení cloudu a v části prvotní stažení vyberte pouze obor názvů.

> [!IMPORTANT]
> Vrstvení cloudu je funkce nástroje AFS, která umožňuje místnímu serveru mít menší kapacitu úložiště než je uložená v cloudu, ale má úplný obor názvů k dispozici. Místně zajímavá data se ukládají také místně do mezipaměti pro výkon s rychlým přístupem. Vrstvení cloudu je volitelná funkce pro každý Synchronizace souborů Azure koncový bod serveru. Tuto funkci je potřeba použít, pokud nemáte na Windows serveru dostatek úložných kapacit pro ukládání všech cloudových dat a pokud chcete zabránit stahování všech dat z cloudu.

Opakujte kroky při vytváření skupiny synchronizace a přidání odpovídající složky serveru jako koncového bodu serveru pro všechna sdílená složka Azure nebo umístění na serveru, která je potřeba nakonfigurovat pro synchronizaci. Počkejte, než se dokončí synchronizace oboru názvů. V následující části najdete podrobné informace o tom, jak to můžete zajistit.

> [!NOTE]
> Po vytvoření koncového bodu serveru synchronizace funguje. Synchronizace ale potřebuje vypsat (zjišťovat) soubory a složky, které jste přesunuli přes DataBox do sdílené složky Azure. V závislosti na velikosti oboru názvů to může trvat poměrně dlouho, než se obor názvů cloudu začne zobrazovat na serveru.

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>Fáze 9: čekání na úplné zobrazení oboru názvů na serveru

Je nutné, abyste počkali na všechny další kroky migrace, které server plně stáhl z cloudové sdílené složky. Pokud začnete přesouvat soubory na server příliš brzy, můžete riskovat nepotřebná nahrávání a dokonce i konflikty synchronizace souborů.

Pokud chcete zjistit, jestli se na serveru dokončila počáteční synchronizace stahování, otevřete Prohlížeč událostí na vašem serveru Windows a použijte protokol událostí telemetrie Synchronizace souborů Azure.
Protokol událostí telemetrie se nachází v Prohlížeč událostí v části aplikace a Services\Microsoft\FileSync\Agent.

Vyhledejte nejnovější událost 9102. Po dokončení relace synchronizace se protokoluje událost s ID 9102. V textu události je pole pro stažení směru synchronizace. ( `HResult` musí být také nula a stahování souborů).
 
Chcete zobrazit dvě po sobě jdoucí události tohoto typu a obsahu, abyste informovali, že server dokončil stahování oboru názvů. Je to v pořádku, pokud se mezi 2 9102 událostmi vyvolává různé události.

## <a name="phase-10-catch-up-robocopy-from-your-nas"></a>Fáze 10: zachytávání nástroje Robocopy z vašeho serveru NAS

Jakmile server dokončí počáteční synchronizaci celého oboru názvů ze sdílené složky v cloudu, můžete v tomto kroku pokračovat. Před tím, než budete pokračovat v tomto kroku, je nezbytné, aby byl tento krok dokončen. Podrobnosti najdete v předchozí části.

V tomto kroku spustíte úlohy nástroje Robocopy, abyste mohli zachytit sdílené složky cloudu s nejnovějšími změnami na serveru NAS od doby, kdy jste své sdílené složky rozpracovali do DataBox.
V závislosti na množství změn, ke kterým došlo na sdílených složkách na serveru NAS, může tato složka Robocopy rychle skončit nebo nějakou dobu trvat.

> [!WARNING]
> Z důvodu navráceného chování funkce Robocopy ve Windows serveru 2019 není/MIR přepínač Robocopy kompatibilní s vrstveným cílovým adresářem. Pro tuto fázi migrace nesmíte používat klienta Windows Server 2019 nebo Windows 10. Použijte příkaz Robocopy na zprostředkujícím Windows serveru 2016.

Základní způsob migrace je prostředí Robocopy ze zařízení NAS na Windows Server a Synchronizace souborů Azure sdílené složky Azure.

Spusťte první místní kopii do cílové složky Windows serveru:

1. Identifikujte první umístění na vašem zařízení NAS.
1. Identifikujte odpovídající složku na Windows serveru, která už má Synchronizace souborů Azure nakonfigurovanou.
1. Spuštění kopírování pomocí nástroje Robocopy

Následující příkaz Robocopy zkopíruje pouze rozdíly (aktualizované soubory a složky) z úložiště NAS do cílové složky Windows serveru. Systém Windows Server pak provede synchronizaci se sdílenými složkami Azure. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

Pokud jste na Windows serveru zřídili méně úložiště, než vaše soubory zabírají na zařízení NAS, pak jste nakonfigurovali vrstvu cloudu. Vzhledem k plnémumu místnímu svazku Windows serveru se [vrstvení cloudu](storage-sync-cloud-tiering-overview.md) zahájí v souborech a vrstvách, které se už úspěšně synchronizovaly. Vrstvení cloudu vytvoří dostatek místa pro pokračování kopie ze zařízení NAS. Vrstvení cloudu kontroluje jednu hodinu, která se synchronizuje, a uvolní místo na disku, abyste dosáhli volného místa na 99% svazku.
Je možné, že prostředí Robocopy potřebuje přesunout mnoho souborů, více než máte místní úložiště pro systém Windows Server. V průměru můžete očekávat, že program Robocopy přesune větší množství větší než Synchronizace souborů Azure může nahrávat soubory a navrstvit je mimo místní svazek. Příkaz Robocopy se nezdaří. Doporučujeme, abyste procházeli prostřednictvím sdílených složek v sekvenci, která to brání. Například nespouštíte úlohy Robocopy pro všechny sdílené složky ve stejnou dobu nebo pouze přesunutím sdílených složek, které odpovídají aktuálnímu volnému místu na Windows serveru, abyste si vyuváděli pár. Dobrá zpráva je, že přepínač/MIR přesune jenom rozdíly a po přesunu Delta nebude znovu nutné úlohu znovu přesunout.

### <a name="user-cut-over"></a>Vyjmuté uživatelem

Při prvním spuštění příkazu Robocopy budou uživatelé a aplikace pořád přistupovat k souborům na serveru NAS a můžou je měnit. Je možné, že Robocopy zpracovaly adresář, přesune se k dalšímu a pak uživatel na zdrojovém umístění přidá, změní nebo odstraní soubor, který se teď v tomto aktuálním běhu Robocopy nezpracovává. Jde o očekávané chování.

Prvním spuštěním je o přesunu hromadně změněných dat na Windows Server a do cloudu prostřednictvím Synchronizace souborů Azure. Tato první kopie může trvat dlouhou dobu, v závislosti na:

* Šířka pásma nahrávání
* rychlost místní sítě a počet, jak optimálně je počet vláken, která se v nástroji Robocopy shoduje
* počet položek (soubory a složky), které je potřeba zpracovat pomocí nástroje Robocopy a Synchronizace souborů Azure

Po dokončení počátečního spuštění znovu spusťte příkaz.

Druhý příkaz Robocopy pro stejnou sdílenou složku pak dokončí rychleji, protože potřebuje pouze přenášet změny, ke kterým došlo od posledního spuštění. Můžete spouštět opakované úlohy pro stejnou sdílenou složku.

Když považujete nepotřebné výpadky, musíte odebrat uživatelský přístup ke sdíleným složkám založeným na serveru NAS. To můžete provést všemi kroky, které uživatelům brání ve změně struktury souborů a složek a obsahu. Příkladem je Ukázat DFS-Namespace k neexistujícímu umístění nebo změnit kořenové seznamy ACL pro sdílenou složku.

Spusťte jeden poslední příkaz Robocopy. V takovém případě se zobrazí všechny změny, které mohly být vynechány.
Doba, po kterou tento poslední krok trvá, závisí na rychlosti kontroly nástroje Robocopy. Můžete odhadnout čas (který se rovná vašemu výpadku), a to měřením doby trvání předchozího běhu.

Vytvořte sdílenou složku ve složce Windows serveru a případně upravte nasazení DFS-N tak, aby odkazovalo na něj. Nezapomeňte nastavit stejné oprávnění na úrovni sdílení jako u sdílené složky SMB serveru NAS. Pokud máte server NAS připojený k doméně na podnikové úrovni, pak se identifikátory SID uživatelů automaticky shodují s tím, jak uživatelé existují ve službě Active Directory, a Robocopy kopíruje soubory a metadata s plnou věrností. Pokud jste na svém serveru NAS používali místní uživatele, musíte tyto uživatele znovu vytvořit jako místní uživatele Windows serveru a namapovat stávající nástroje Robocopy, které jste přesunuli na Windows Server, na identifikátory SID nových místních uživatelů Windows serveru.

Dokončili jste migraci sdílené složky nebo skupiny sdílených složek do společného kořenového adresáře nebo svazku. (V závislosti na mapování ze fáze 1)

Můžete zkusit spustit několik z těchto kopií paralelně. Doporučujeme, abyste v jednom okamžiku zpracovali obor jedné sdílené složky Azure.

## <a name="troubleshoot"></a>Řešení potíží

Nejpravděpodobnějším problémem, ke kterému můžete spustit, je, že příkaz Robocopy se na straně serveru Windows nezdařil s *názvem "svazek je plný"* . Vrstvení cloudu slouží jednou za hodinu k vyvádění obsahu z místního disku Windows serveru, který se synchronizuje. Jeho cílem je dosáhnout 99% volného místa na svazku.

Umožněte synchronizaci v průběhu a vrstvení cloudu uvolněte místo na disku. Můžete si všimnout, že v Průzkumníkovi souborů na vašem Windows serveru.

Pokud má Windows Server dostatek dostupné kapacity, problém vyřešíte tak, že znovu spustíte příkaz. Po dosažení této situace se nic neukončí a můžete se přesunout vpřed s jistotou. Nepohodlí spuštění příkazu je jediné, co je to v tomto důsledku.

Pokud chcete řešit potíže s Synchronizace souborů Azure problémy, podívejte se na odkaz v následující části.

## <a name="next-steps"></a>Další kroky

K dispozici je více informací o sdílených složkách a Synchronizace souborů Azure Azure. Následující články vám pomůžou pochopit pokročilé možnosti, osvědčené postupy a také obsahovat pomoc s řešením potíží. Tyto články v případě potřeby odkazují na [dokumentaci ke sdílení souborů Azure](storage-files-introduction.md) .

* [Přehled migrace](storage-files-migration-overview.md)
* [Přehled AFS](./storage-sync-files-planning.md)
* [Průvodce nasazením pro AFS](./storage-how-to-create-file-share.md)
* [Řešení potíží AFS](storage-sync-files-troubleshoot.md)
