---
title: Migrace místní služby NAS do sdílených složek Azure
description: Naučte se migrovat soubory z umístění místního síťového připojení (NAS) do sdílených složek Azure pomocí Azure DataBox.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 666e9f01d090acf29b8013470ed0264cd83f6d47
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2021
ms.locfileid: "106293630"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-azure-file-shares"></a>Použití DataBox k migraci ze síťového připojeného úložiště (NAS) do sdílených složek Azure

Tento článek migrace je jedním z několika součástí, které zahrnují klíčová slova NAS a Azure DataBox. Podívejte se, jestli tento článek platí pro váš scénář:

> [!div class="checklist"]
> * Zdroj dat: úložiště připojené k síti (NAS)
> * Trasa migrace: NAS &rArr; Databox &rArr; Shared File sdílená složka Azure
> * Místní ukládání souborů do mezipaměti: Ne, konečným cílem je používat sdílené složky Azure přímo v cloudu. Neexistuje žádný plán, který by bylo možné použít Synchronizace souborů Azure.

Pokud je váš scénář jiný, podívejte se do [tabulky Průvodce migrací](storage-files-migration-overview.md#migration-guides).

Tento článek vás provede úplnými konfiguracemi pro plánování, nasazování a vytváření sítí potřebných k migraci ze zařízení NAS do funkčních sdílených složek Azure. Tato příručka používá Azure DataBox pro přenos hromadných dat (offline přenos dat).

## <a name="migration-goals"></a>Cíle migrace

Cílem je přesunout sdílené složky, které máte na zařízení NAS, do Azure a stát, že se stanou nativními sdílenými složkami Azure, můžete použít bez nutnosti použití Windows serveru. Tato migrace se musí udělat způsobem, který zaručuje integritu produkčních dat a dostupnosti během migrace. Ta ta vyžaduje udržení minimálního výpadku, aby bylo možné se přizpůsobit nebo jen mírně překročit pravidelná časová období údržby.

## <a name="migration-overview"></a>Přehled migrace

Proces migrace se skládá z několika fází. Budete potřebovat nasadit účty a sdílené složky Azure Storage, nakonfigurovat sítě, migrovat je pomocí Azure DataBox, zachytit změny pomocí nástroje Robocopy a nakonec vyjmout uživatele do nově vytvořených sdílených složek Azure. V následujících oddílech jsou podrobně popsány fáze procesu migrace.

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

## <a name="phase-4-provision-a-temporary-windows-server"></a>Fáze 4: zřízení dočasného Windows serveru

Až budete čekat na doručení Azure DataBox, můžete již nasadit jeden nebo více serverů Windows, které budete potřebovat ke spuštění úloh Robocopy. 

- Prvním použitím těchto serverů bude zkopírování souborů do DataBox.
- Druhé použití těchto serverů bude zachytit změny, ke kterým došlo na zařízení NAS, zatímco služba DataBox byla v přenosu. Tento přístup trvá minimálně na straně zdroje.

Rychlost fungování úloh nástroje Robocopy závisí hlavně na těchto faktorech:

* IOPS na zdrojovém a cílovém úložišti
* dostupná šířka pásma sítě mezi nimi </br> Další podrobnosti najdete v části Poradce při potížích: [požadavky na IOPS a šířku pásma](#iops-and-bandwidth-considerations) .
* možnost rychlého zpracování souborů a složek v oboru názvů </br> Další podrobnosti najdete v části řešení potíží: [rychlost zpracování](#processing-speed) .
* počet změn mezi spuštěním nástroje Robocopy </br> Další podrobnosti najdete v části řešení potíží: [Vyhněte se zbytečné práci](#avoid-unnecessary-work) .

Při rozhodování o velikosti paměti RAM a počtu vláken, které poskytnete vašim dočasným systémům Windows Server, je důležité mít na paměti odkazované podrobnosti.

## <a name="phase-5-preparing-to-use-azure-file-shares"></a>Fáze 5: Příprava na používání sdílených složek Azure

Pokud chcete ušetřit čas, měli byste v této fázi pokračovat, až budete čekat, až DataBox přijde. S informacemi v této fázi budete moct rozhodnout, jak budou mít vaše servery a uživatelé v Azure a mimo Azure povolené využívat sdílené složky Azure. Nejzávažnější rozhodnutí jsou:

- **Síťové služby:** Povolte směrování provozu protokolu SMB v sítích.
- **Ověřování:** Nakonfigurujte účty úložiště Azure pro ověřování protokolem Kerberos. AdConnect a připojení k doméně vašemu účtu úložiště umožní vašim aplikacím a uživatelům používat pro ověřování svoji identitu AD.
- **Autorizace:** Seznamy řízení přístupu (ACL) na úrovni sdílené složky pro každou sdílenou složku Azure umožní uživatelům a skupinám služby AD přístup k dané sdílené složce a v rámci sdílené složky Azure. nativní seznamy řízení přístupu NTFS se převezmou. Autorizace na základě seznamů řízení přístupu k souborům a složkám pak funguje jako u místních sdílených složek SMB.
- **Provozní kontinuita:** Integrace sdílených složek Azure do stávajícího prostředí často zahrnuje zachování stávajících adres sdílené složky. Pokud již nepoužíváte obory názvů DFS, zvažte, že ve vašem prostředí máte možnost vytvořit. Budete moct dál sdílet adresy uživatelů a skriptů, a to beze změny. Systém souborů DFS-N použijete jako směrovací službu oboru názvů pro protokol SMB tím, že po migraci přesměruje DFS-Namespace cíle na sdílené složky Azure.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Toto video je průvodce a ukázkou, jak bezpečně vystavit sdílené složky Azure přímo pro informační pracovníky a aplikace v pěti jednoduchých krocích.</br>
        Video odkazuje na vyhrazenou dokumentaci pro některá témata:

* [Přehled identity](storage-files-active-directory-overview.md)
* [Jak připojit k doméně účet úložiště](storage-files-identity-auth-active-directory-enable.md)
* [Přehled sítí pro sdílené složky Azure](storage-files-networking-overview.md)
* [Postup konfigurace veřejných a privátních koncových bodů](storage-files-networking-endpoints.md)
* [Jak nakonfigurovat S2S VPN](storage-files-configure-s2s-vpn.md)
* [Jak nakonfigurovat Windows P2S VPN](storage-files-configure-p2s-vpn-windows.md)
* [Jak nakonfigurovat P2S síť VPN pro Linux](storage-files-configure-p2s-vpn-linux.md)
* [Jak nakonfigurovat předávání DNS](storage-files-networking-dns.md)
* [Konfigurace DFS – N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

## <a name="phase-6-copy-files-onto-your-databox"></a>Fáze 6: zkopírování souborů do DataBox

Až přijde vaše DataBox, musíte si nastavit DataBox v rámci pohledu na zařízení NAS. Postupujte podle pokynů k instalaci pro typ DataBox, který jste objednali.

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

## <a name="phase-7-catch-up-robocopy-from-your-nas"></a>Fáze 7: zachytávání nástroje Robocopy z vašeho serveru NAS

Jakmile DataBox oznámí, že se všechny soubory a složky umístily do plánovaných sdílených složek Azure, můžete v této fázi pokračovat.
Zachycená služba Robocopy je nutná pouze v případě, že se data na serveru NAS od spuštění kopírování DataBox mohla změnit. V některých případech, kdy pro účely archivace používáte sdílenou složku, může být možné zastavit změny sdílené složky na serveru NAS, dokud se migrace nedokončí. Je také možné, že budete mít možnost využívat své obchodní požadavky tím, že během migrace nastavíte sdílené složky NAS jen pro čtení.

V případech, kdy potřebujete, aby během migrace bylo sdílení pro čtení a zápis, a může dojít pouze k výpadku malého okna, je důležité provést tento krok pro zachytávání, aby bylo možné provést převzetí služeb při selhání přístupu uživatele přímo do sdílené složky Azure.

V tomto kroku spustíte úlohy nástroje Robocopy, abyste mohli zachytit sdílené složky cloudu s nejnovějšími změnami na serveru NAS od doby, kdy jste své sdílené složky rozpracovali do DataBox.
V závislosti na množství změn, ke kterým došlo na sdílených složkách na serveru NAS, může tato složka Robocopy rychle skončit nebo nějakou dobu trvat.

Spusťte první místní kopii do cílové složky Windows serveru:

1. Identifikujte první umístění na vašem zařízení NAS.
1. Identifikujte stejnou sdílenou složku Azure.
1. Připojte sdílenou složku Azure jako místní síťovou jednotku na dočasném Windows serveru.
1. Spusťte kopii pomocí příkazu Robocopy, jak je popsáno

### <a name="mounting-an-azure-file-share"></a>Připojení sdílené složky Azure

Než budete moct použít Robocopy, je potřeba, aby byla sdílená složka Azure přístupná přes protokol SMB. Nejjednodušší způsob je připojit sdílenou složku jako místní síťovou jednotku k serveru Windows, který plánujete pomocí nástroje pro Robocopy. 

> [!IMPORTANT]
> Než budete moct úspěšně připojit sdílenou složku Azure k místnímu Windows serveru, musíte mít hotovou fázi: Příprava na používání sdílených složek Azure.

Až budete připraveni, přečtěte si [článek použití sdílené složky Azure s postupem Windows](storage-how-to-use-files-windows.md) a připojení sdílené složky Azure, pro kterou chcete spustit systém souborů Robocopy pro zápis na serveru.

### <a name="robocopy"></a>RoboCopy

Následující příkaz Robocopy zkopíruje pouze rozdíly (aktualizované soubory a složky) z úložiště NAS do sdílené složky Azure. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

> [!TIP]
> [V části věnované řešení potíží se dozvíte](#troubleshoot) , jestli má systém Robocopy dopad na vaše produkční prostředí, oznamuje spoustu chyb nebo nepostupuje podle očekávání.

### <a name="user-cut-over"></a>Vyjmuté uživatelem

Při prvním spuštění příkazu Robocopy budou uživatelé a aplikace pořád přistupovat k souborům na serveru NAS a můžou je měnit. Je možné, že Robocopy zpracovaly adresář, přesune se k dalšímu a pak uživatel na zdrojovém umístění přidá, změní nebo odstraní soubor, který se teď v tomto aktuálním běhu Robocopy nezpracovává. Jde o očekávané chování.

Prvním spuštěním je o přesunu hromadně změněných dat do sdílené složky Azure. Tato první kopie může chvíli trvat. Další informace o tom, co může ovlivnit rychlost Robocopy, najdete v [části věnované řešení potíží](#troubleshoot) .

Po dokončení počátečního spuštění znovu spusťte příkaz.

Druhý příkaz Robocopy pro stejnou sdílenou složku pak dokončí rychleji, protože potřebuje pouze přenášet změny, ke kterým došlo od posledního spuštění. Můžete spouštět opakované úlohy pro stejnou sdílenou složku.

Když považujete nepotřebné výpadky, musíte odebrat uživatelský přístup ke sdíleným složkám založeným na serveru NAS. To můžete provést všemi kroky, které uživatelům brání ve změně struktury souborů a složek a obsahu. Příkladem je Ukázat DFS-Namespace k neexistujícímu umístění nebo změnit kořenové seznamy ACL pro sdílenou složku.

Spusťte jeden poslední příkaz Robocopy. V takovém případě se zobrazí všechny změny, které mohly být vynechány.
Doba, po kterou tento poslední krok trvá, závisí na rychlosti kontroly nástroje Robocopy. Můžete odhadnout čas (který se rovná vašemu výpadku), a to měřením doby trvání předchozího běhu.

Vytvořte sdílenou složku ve složce Windows serveru a případně upravte nasazení DFS-N tak, aby odkazovalo na něj. Nezapomeňte nastavit stejné oprávnění na úrovni sdílení jako u sdílené složky SMB serveru NAS. Pokud máte server NAS připojený k doméně na podnikové úrovni, pak se identifikátory SID uživatelů automaticky shodují s tím, jak uživatelé existují ve službě Active Directory, a Robocopy kopíruje soubory a metadata s plnou věrností. Pokud jste na svém serveru NAS používali místní uživatele, musíte tyto uživatele znovu vytvořit jako místní uživatele Windows serveru a namapovat stávající nástroje Robocopy, které jste přesunuli na Windows Server, na identifikátory SID nových místních uživatelů Windows serveru.

Dokončili jste migraci sdílené složky nebo skupiny sdílených složek do společného kořenového adresáře nebo svazku. (V závislosti na mapování ze fáze 1)

Můžete zkusit spustit několik z těchto kopií paralelně. Doporučujeme, abyste v jednom okamžiku zpracovali obor jedné sdílené složky Azure.

## <a name="troubleshoot"></a>Řešení potíží

Rychlost a rychlost úspěšnosti daného spuštění nástroje Robocopy budou záviset na několika faktorech:

* IOPS na zdrojovém a cílovém úložišti
* dostupná šířka pásma sítě mezi nimi
* možnost rychlého zpracování souborů a složek v oboru názvů
* počet změn mezi spuštěním nástroje Robocopy


### <a name="iops-and-bandwidth-considerations"></a>Požadavky na IOPS a šířku pásma

V této kategorii je potřeba zvážit možnosti **zdroje** (vašeho serveru NAS), **cíle** (Azure Databox a novější sdílená složka Azure) a **sítě** , která je propojuje. Maximální možná propustnost závisí na nejpomalejších těchto třech součástech. Standardní DataBox se dodává se dvěma síťovými rozhraními 10 GB/s. V závislosti na vašem serveru NAS je možné, že se to bude shodovat. Ujistěte se, že je vaše síťová infrastruktura nakonfigurovaná tak, aby podporovala optimální přenosové rychlosti a jejich nejlepší možnosti.

> [!CAUTION]
> Kopírování co nejrychlejší je často nejužitečnější, zvažte použití vaší místní sítě a zařízení NAS pro jiné, často důležité úkoly.

Pokud existuje riziko, že migrace může monopolizovat dostupné prostředky, nemusí se co nejrychleji kopírovat co nejrychlejší.

* Vezměte v úvahu, kdy je nejlepší ve vašem prostředí spouštět migrace: během dne, mimo špičku nebo během víkendu.
* Zvažte také síťové technologie QoS na serveru Windows, abyste omezili rychlost nástroje Robocopy, a tedy dopad na NAS a síť.
* Vyhněte se zbytečné práci na nástrojích pro migraci.

RobCopy má taky možnost vkládat prodlevy mezi pakety zadáním `/IPG:n` přepínače, který `n` se měří v milisekundách mezi pakety Robocopy. Použití tohoto přepínače vám může pomoci zabránit monopolizationí prostředků v vstupně-výstupních zařízeních s omezeným počtem zařízení NAS a vysoce využitých síťových propojení. 

`/IPG:n` nedá se použít pro přesné síťové omezení pro určité MB/s. Místo toho použijte Windows Server Network QoS. Robocopy zcela spoléhá na protokol SMB pro všechny sítě, a proto nemá schopnost ovlivnit propustnost sítě, ale může zpomalit její využití. 

Podobná řádková podobnost se vztahuje na IOPS zjištěné na serveru NAS. Velikost clusteru na svazku serveru NAS, velikost paketů a pole dalších faktorů ovlivňují pozorovaný IOPS. Zavedení mezipaketového zpoždění je často nejjednodušší způsob, jak řídit zatížení na serveru NAS. Otestujte více hodnot, například z přibližně 20 milisekund (n = 20) až násobků, které vám umožní zjistit, kolik prodlevy umožňuje obsluhovat vaše jiné požadavky a přitom udržuje rychlost nástroje Robocopy na maximum pro vaše omezení.

### <a name="processing-speed"></a>Rychlost zpracování

Robocopy projde obor názvů, na který se odkazuje, a vyhodnotí každý soubor a složku pro kopírování. Každý soubor se vyhodnotí během počátečního kopírování, jako je třeba kopírování z místní sítě do DataBox, a dokonce i během zachytávání kopií přes připojení WAN ke sdílené složce Azure.

Často jsme standardně posuzujete šířku pásma jako nejúčinnější faktor migrace – a to může být pravdivé. Možnost vytvoření výčtu oboru názvů může mít vliv na celkovou dobu kopírování ještě více pro větší obory názvů s menšími soubory. Vezměte v úvahu, že kopírování 1 TiB malých souborů bude trvat mnohem déle než kopírování 1 TiB méně, ale větší soubory bylo uděleno, že jsou všechny ostatní proměnné stejné.

Příčinou tohoto rozdílu je výpočetní výkon potřebný k procházení oboru názvů. Robocopy podporuje kopie s více vlákny prostřednictvím `/MT:n` parametru, který n představuje počet vláken procesoru. Takže při zřizování počítače speciálně pro Robocopy zvažte počet jader procesoru a jejich vztah k počtu vláken, která poskytují. Nejběžnější jsou dvě vlákna na jádro. Počet jader a vláken počítače je důležitým datovým bodem pro rozhodování o tom, jaké hodnoty násobného vlákna `/MT:n` byste měli zadat. Zvažte také, kolik úloh nástroje Robocopy máte v plánu paralelně na daném počítači.

Další vlákna budou kopírovat náš 1Tib příklad malých souborů podstatně rychleji než méně vláken. Ve stejnou chvíli existuje snížení návratnosti investic do našich 1Tib větších souborů. Budou pořád kopírovat rychleji více vláken, která přiřadíte, ale získáte větší pravděpodobně šířku pásma sítě nebo vstupně-výstupní operace.

### <a name="avoid-unnecessary-work"></a>Vyhnout se zbytečné práci

Vyhněte se velkým změnám v oboru názvů. To zahrnuje přesun souborů mezi adresáři, změny vlastností ve velkém měřítku nebo změnu oprávnění (seznamy ACL pro systém souborů NTFS), protože mají často vliv na změnu v případě, že se změní seznamy ACL pro složku, které se blíží kořenu sdílené složky. Důsledky můžou být:

* Rozšířená doba běhu úlohy nástroje Robocopy v důsledku změny, kterou je třeba aktualizovat u každého souboru a složky ovlivněného seznamem ACL
* efektivita použití DataBox v prvním místě se může snížit, když se struktury složek změní po zkopírování souborů do DataBox. Úloha Robocopy nebude moct přejít zpátky do oboru názvů a místo toho bude muset soubory přenášet do sdílené složky Azure a znovu nahrávat soubory do nové struktury složek do Azure.

Dalším důležitým aspektem je použití nástroje Robocopy efektivně. Když použijete doporučený skript Robocopy, vytvoříte a uložíte soubor protokolu pro chyby. Může dojít k chybám kopírování – to je normální. Tyto chyby často potřebují spustit více zaokrouhlení kopírovacího nástroje, jako je například Robocopy. Počáteční spuštění, řekněme od serveru NAS až po DataBox, a jeden nebo více doplňků s přepínačem/MIR pro zachycení a opakování souborů, které nebyly zkopírovány.

Měli byste být připravení spustit více hodnot Robocopy proti danému oboru názvů. Po sobě jdoucí běhy dokončí rychleji, protože jejich kopírování je méně, ale omezuje se tím rychlost zpracování tohoto oboru názvů. Když spustíte více zaoblení, můžete každou kulatou hodnotu zrychlit tak, že se příkaz Robocopy nedoporučuje při prvním pokusu zkopírovat vše. Tyto přepínače Robocopy můžou mít výrazný rozdíl:

* `/R:n` n = jak často se pokusíte zkopírovat soubor, který selhal a 
* `/W:n` n = kolik sekund se má čekat mezi opakovanými pokusy

`/R:5 /W:5` je rozumné nastavení, které můžete upravit na své míru. V tomto příkladu se neúspěšný soubor znovu pokusí o pět sekund, přičemž doba čekání mezi opakovanými pokusy proběhne znovu. Pokud se soubor stále nedaří zkopírovat, pokusí se znovu provést další úlohy Robocopy a často soubory, které selhaly, protože se používají, nebo kvůli problémům s časovým limitem může být někdy zkopírován tímto způsobem.


## <a name="next-steps"></a>Další kroky

K dispozici je více informací o sdílených složkách Azure. Následující články vám pomůžou pochopit pokročilé možnosti, osvědčené postupy a také obsahovat pomoc s řešením potíží. Tyto články v případě potřeby odkazují na [dokumentaci ke sdílení souborů Azure](storage-files-introduction.md) .

* [Přehled migrace](storage-files-migration-overview.md)
* [Monitorování, diagnostika a řešení problémů s Microsoft Azure Storage](../common/storage-monitoring-diagnosing-troubleshooting.md)
* [Aspekty sítí pro přímý přístup](storage-files-networking-overview.md)
* [Zálohování: snímky sdílené složky Azure](storage-snapshots-files.md)
