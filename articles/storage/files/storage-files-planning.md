---
title: Plánování nasazení souborů Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, co je třeba vzít v úvahu při plánování nasazení souborů Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 76a96d36387f55889b65f16ea1ca6ec07359c377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502428"
---
# <a name="planning-for-an-azure-files-deployment"></a>Plánování nasazení služby Soubory Azure
[Soubory Azure](storage-files-introduction.md) lze nasadit dvěma hlavními způsoby: přímým připojením sdílených složek Azure bez serveru nebo ukládáním sdílených složek Azure místně pomocí Azure File Sync. Kterou možnost nasazení zvolíte, změní věci, které je třeba zvážit při plánování nasazení. 

- **Přímé připojení sdílené složky Azure**: Vzhledem k tomu, že soubory Azure poskytují přístup s smb, můžete připojit sdílené složky Azure místně nebo v cloudu pomocí standardního klienta SMB dostupného ve Windows, macOS a Linuxu. Vzhledem k tomu, že sdílené složky Azure jsou bez serveru, nasazení pro produkční scénáře nevyžaduje správu souborového serveru nebo zařízení NAS. To znamená, že nemusíte používat softwarové opravy ani vyměňovat fyzické disky. 

- **Sdílení souborů Azure v mezipaměti místně pomocí Azure File Sync**: Azure File Sync umožňuje centralizovat sdílené složky vaší organizace v souborech Azure a současně zachovat flexibilitu, výkon a kompatibilitu místního souborového serveru. Azure File Sync transformuje místní (nebo cloudový) Windows Server do rychlé mezipaměti sdílené složky Azure. 

Tento článek řeší především aspekty nasazení pro nasazení sdílené složky Azure k přímému připojení místního nebo cloudového klienta. Pokud chcete naplánovat nasazení synchronizace souborů Azure, přečtěte si [informace o plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md).

## <a name="management-concepts"></a>Koncepce řízení
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Při nasazování sdílených složek Azure do účtů úložiště doporučujeme:

- Jenom nasazování sdílených složek Azure do účtů úložiště s jinými sdílenými složkami souborů Azure. I když účty úložiště GPv2 umožňují mít účty úložiště pro smíšené účely, protože prostředky úložiště, jako jsou sdílené složky Azure a kontejnery objektů blob, sdílejí limity účtu úložiště, směšování prostředků dohromady může ztížit řešení potíží problémy s výkonem později. 

- Při nasazování sdílených složek Azure je třeba věnovat pozornost omezením viops účtu úložiště. V ideálním případě byste mapovat sdílení souborů 1:1 s účty úložiště, ale to nemusí být vždy možné z důvodu různých omezení a omezení, a to jak z vaší organizace, tak z Azure. Pokud není možné mít pouze jednu sdílenou složku nasazenou v jednom účtu úložiště, zvažte, které sdílené složky budou vysoce aktivní a které sdílené složky budou méně aktivní, aby se zajistilo, že nejžhavější sdílené složky nebudou vloženy do stejného účtu úložiště společně.

- Nasadit pouze Účty GPv2 a FileStorage a upgradovat GPv1 a klasické účty úložiště, když je najdete ve vašem prostředí. 

## <a name="identity"></a>Identita
Chcete-li získat přístup ke sdílené složce Azure, musí být uživatel sdílené složky ověřen a musí mít oprávnění k přístupu ke sdílené položce. To se provádí na základě identity uživatele, který přistupuje ke sdílené složce. Soubory Azure se integrují se třemi hlavními poskytovateli identit:
- **Služba Active Directory vlastněná zákazníkem** (preview): Účty úložiště Azure lze připojit k službě Windows Server Active Directory vlastněné zákazníkem, stejně jako souborový server Windows Serveru nebo zařízení NAS. Váš řadič domény služby Active Directory lze nasadit místně, ve virtuálním počítači Azure nebo dokonce jako virtuální počítač v jiném poskytovateli cloudu; Azure Files je agnostik, kde je hostovaný řadič domény. Jakmile je účet úložiště připojen k doméně, může koncový uživatel připojit sdílenou složku s uživatelským účtem, se kterým se přihlásil ke svému počítači. Ověřování pomocí služby AD používá ověřovací protokol Kerberos.
- **Azure Active Directory Domain Services (Azure AD DS):** Azure AD DS poskytuje řadič domény Active Directory spravovaný společností Microsoft, který se dá použít pro prostředky Azure. Doména spojující váš účet úložiště s Azure AD DS poskytuje podobné výhody pro doménu, která se k němu připojuje ke službě Active Directory vlastněné zákazníkem. Tato možnost nasazení je nejužitečnější pro scénáře výtahu a směny aplikací, které vyžadují oprávnění založené na službě AD. Vzhledem k tomu, že Azure AD DS poskytuje ověřování na základě služby AD, tato možnost také používá ověřovací protokol Kerberos.
- **Klíč účtu úložiště Azure**: Sdílené složky Azure se dají taky namontovat pomocí klíče účtu úložiště Azure. Chcete-li připojit sdílení souborů tímto způsobem, název účtu úložiště se používá jako uživatelské jméno a klíč účtu úložiště se používá jako heslo. Použití klíče účtu úložiště k připojení sdílené složky Azure je efektivně operace správce, protože připojená sdílená složka bude mít úplná oprávnění ke všem souborům a složkám ve sdílené složce, i když mají seznamy ACL. Při použití klíče účtu úložiště k připojení přes SMB se používá ověřovací protokol NTLMv2.

Pro zákazníky, kteří migrují z místních souborových serverů nebo vytvářejí nové sdílené složky v Azure Files, které mají chovat jako souborové servery Windows nebo zařízení NAS, je doporučená možnost domény spojující váš účet úložiště se **službou Active Directory vlastněnou zákazníkem.** Další informace o připojení účtu úložiště k službě Active Directory vlastněné zákazníkem najdete v [tématu Přehled služby Azure Files Active Directory](storage-files-active-directory-overview.md).

Pokud máte v úmyslu použít klíč účtu úložiště pro přístup ke sdíleným položkám souborů Azure, doporučujeme použít koncové body služby, jak je popsáno v části [Sítě.](#networking)

## <a name="networking"></a>Síťové služby
Sdílené složky Azure jsou přístupné odkudkoli prostřednictvím veřejného koncového bodu účtu úložiště. To znamená, že ověřené požadavky, jako jsou požadavky autorizované přihlašovací identitou uživatele, mohou bezpečně pocházet z Azure nebo mimo něj. V mnoha zákaznických prostředích se nezdaří počáteční připojení sdílené složky Azure na vaší místní pracovní stanici, i když připojení z virtuálních počítačů Azure jsou úspěšná. Důvodem je to, že mnoho organizací a poskytovatelů internetových služeb (ISP) blokuje port, který smb používá ke komunikaci, port 445. Souhrn poskytovatelů internetových služeb, kteří umožňují nebo neumožňují přístup z portu 445, najdete na webu [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Chcete-li odblokovat přístup ke sdílené složce Azure, máte dvě hlavní možnosti:

- Odblokujte port 445 pro místní síť vaší organizace. Sdílené složky Azure lze přistupovat pouze externě prostřednictvím veřejného koncového bodu pomocí protokolů bezpečné ho internetu, jako je SMB 3.0 a FileREST API. Toto je nejjednodušší způsob, jak získat přístup ke sdílené složce Azure z místního prostředí, protože nevyžaduje pokročilou konfiguraci sítě nad rámec změny pravidel odchozího portu vaší organizace, ale doporučujeme odebrat starší a zastaralé verze SMB protokolu, konkrétně SMB 1.0. Informace o tom naleznete v [tématech Zabezpečení systému Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server) a [Zabezpečení Linuxu](storage-how-to-use-files-linux.md#securing-linux).

- Přístup ke sdíleným položkům Azure prostřednictvím připojení ExpressRoute nebo VPN. Při přístupu ke sdílené složce Azure prostřednictvím síťového tunelového propojení můžete připojit sdílenou složku Azure jako místní sdílenou složku, protože provoz SMB neprochází hranicemi vaší organizace.   

I když z technického hlediska je mnohem jednodušší připojit sdílené složky Azure prostřednictvím veřejného koncového bodu, očekáváme, že většina zákazníků se rozhodne připojit své sdílené složky Azure přes připojení ExpressRoute nebo VPN. Chcete-li to provést, budete muset nakonfigurovat následující pro vaše prostředí:  

- **Tunelové propojení sítě pomocí ExpressRoute, Site-to-Site, nebo Point-to-Site VPN:** Tunelové propojení do virtuální sítě umožňuje přístup ke sdíleným položkám Azure z místního prostředí, i když je port 445 blokovaný.
- **Privátní koncové body**: Privátní koncové body poskytují vašemu účtu úložiště vyhrazenou IP adresu z adresního prostoru virtuální sítě. To umožňuje tunelové propojení sítě bez nutnosti otevírat místní sítě až do všech rozsahů IP adres vlastněných clustery úložišť Azure. 
- **Předávání DNS**: Nakonfigurujte místní SLUŽBU DNS tak, aby `storageaccount.file.core.windows.net` přelapojila název vašeho účtu úložiště (tj. pro oblasti veřejného cloudu), který chcete přeložit na IP adresu vašich privátních koncových bodů.

Pokud chcete naplánovat síť přidruženou k nasazení sdílené složky Azure, přečtěte si [část Aspekty sítě Azure Files](storage-files-networking-overview.md).

## <a name="encryption"></a>Šifrování
Azure Files podporuje dva různé typy šifrování: šifrování při přenosu, které se vztahuje k šifrování používanému při připojení/přístupu ke sdílené složce Azure, a šifrování v klidovém stavu, které souvisí s tím, jak jsou data šifrována, když jsou uložena na disku. 

### <a name="encryption-in-transit"></a>Šifrování během přenosu
Ve výchozím nastavení mají všechny účty úložiště Azure povolené šifrování při přenosu. To znamená, že když připojíte sdílenou složku přes SMB nebo k ní přistupujete přes protokol FileREST (například prostřednictvím portálu Azure, PowerShellu/CLI nebo sad Azure SDK), soubory Azure povolí připojení jenom v případě, že se vytvoří s SMB 3.0+ se šifrováním nebo HTTPS. Klienti, kteří nepodporují SMB 3.0 nebo klienti, kteří podporují šifrování SMB 3.0, ale ne SMB, nebudou moci připojit sdílenou složku Azure, pokud je povoleno šifrování při přenosu. Další informace o tom, které operační systémy podporují smb 3.0 s šifrováním, naleznete v naší podrobné dokumentaci pro [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)a [Linux](storage-how-to-use-files-linux.md). Všechny aktuální verze powershellových, cli a sad SDK podporují protokol HTTPS.  

Šifrování můžete zakázat při přenosu pro účet úložiště Azure. Pokud je šifrování zakázáno, soubory Azure také povolí SMB 2.1, SMB 3.0 bez šifrování a nešifrovaná volání rozhraní API FileREST přes protokol HTTP. Primárním důvodem pro zakázání šifrování při přenosu je podpora starší aplikace, která musí být spuštěna ve starším operačním systému, jako je například windows server 2008 R2 nebo starší distribuce Linuxu. Soubory Azure povolují jenom připojení SMB 2.1 ve stejné oblasti Azure jako sdílená složka Azure; Klient SMB 2.1 mimo oblast Azure sdílené složky Azure, jako je například místní nebo v jiné oblasti Azure, nebude mít přístup ke sdílené složce.

Důrazně doporučujeme zajistit, aby bylo povoleno šifrování dat při přenosu.

Další informace o šifrování při přenosu najdete v [tématu vyžadování zabezpečeného přenosu v úložišti Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Vrstvy úložiště
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

Obecně platí, že funkce Azure Files a interoperabilita s jinými službami jsou stejné mezi sdílenými položkami souborů premium a standardními sdílenými složkami, existují však několik důležitých rozdílů:
- **Model fakturace**
    - Sdílené složky Premium se účtují pomocí zřízeného fakturačního modelu, což znamená, že platíte za kolik úložiště, které zřídíte, spíše než kolik úložiště skutečně požadujete. 
    - Standardní sdílené složky se účtují pomocí modelu průběžných plateb, který zahrnuje základní náklady na úložiště za to, kolik úložiště skutečně spotřebováváte, a pak další transakční náklady na základě způsobu použití sdílené položky. Se standardními sdílenými složkami se vaše faktura zvýší, pokud použijete (čtení/zápis/připojení) sdílené složky Azure.
- **Možnosti redundance**
    - Sdílené složky Premium jsou k dispozici pouze pro místně redundantní (LRS) a zónové redundantní (ZRS) úložiště. 
    - Standardní sdílené složky jsou k dispozici pro místně redundantní, zónově redundantní, geograficky redundantní (GRS) a geograficky zónové redundantní (GZRS).
- **Maximální velikost sdílené složky**
    - Sdílené složky Premium mohou být zřízeny až pro 100 TiB bez jakékoli další práce.
    - Ve výchozím nastavení mohou standardní sdílené složky zahrnovat pouze 5 TiB, ačkoli limit sdílení lze zvýšit na 100 TiB tím, že se přihlásíte do příznaku funkce účtu úložiště *velké sdílené složky.* Standardní sdílené složky mohou mít k dispozici pouze 100 TiB pro místně redundantní nebo zónově redundantní účty úložiště. Další informace o zvětšení velikosti sdílené složky naleznete v tématu [Povolení a vytvoření velkých sdílených složek](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share)souborů .
- **Regionální dostupnost**
    - Sdílené složky premium nejsou k dispozici ve všech oblastech a zónová redundantní podpora je k dispozici v menší podmnožině oblastí. Pokud chcete zjistit, jestli jsou akcie prémiových souborů momentálně dostupné ve vaší oblasti, podívejte se na stránku [produktů dostupných podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=storage) pro Azure. Informace o tom, které oblasti podporují ZRS, najdete v [tématu Azure Dostupnost zóny podpory podle oblasti](../../availability-zones/az-overview.md#services-support-by-region). Chcete-li nám pomoci upřednostnit nové oblasti a funkce prémiové úrovně, vyplňte tento [průzkum](https://aka.ms/pfsfeedback).
    - Standardní sdílené složky jsou k dispozici ve všech oblastech Azure.
- Azure Kubernetes Service (AKS) podporuje sdílené složky premium ve verzi 1.13 a novějších.

Jakmile je sdílená složka vytvořena jako prémiová nebo standardní sdílená složka, nelze ji automaticky převést na druhou úroveň. Pokud chcete přepnout na druhou vrstvu, musíte vytvořit novou sdílenou složku v této vrstvě a ručně zkopírovat data z původní sdílené složky do nové sdílené složky, kterou jste vytvořili. K provedení `robocopy` této `rsync` kopie doporučujeme používat windows nebo macOS a Linux.

### <a name="understanding-provisioning-for-premium-file-shares"></a>Principy zřizování pro sdílené složky s prémiovým souborem
Sdílené složky Premium jsou zřízeny na základě pevného gib/iops/propustnost. Pro každý gib zřízené, bude akcie vydána jeden VOPS a 0,1 MiB/s propustnost až do maximální limity na akcii. Minimální povolené zřizování je 100 GiB s min VOPS/propustnost.

Na základě nejlepší úsilí, všechny akcie může prasknout až tři VOPS na GiB zřízeného úložiště po dobu 60 minut nebo déle v závislosti na velikosti sdílené složky. Nové akcie začínají plným objemovým úvěrem založeným na zřízené kapacitě.

Akcie musí být zřízeny v 1 přírůstcích GiB. Minimální velikost je 100 GiB, další velikost je 101 GiB a tak dále.

> [!TIP]
> Základní VOPS = 1 * zřízená GiB. (Až do max 100 000 VOPS).
>
> Limit roztržení = 3 * základní iOPs. (Až do max 100 000 VOPS).
>
> rychlost odchozího výstupu = 60 MiB/s + 0,06 * zřízená GiB
>
> rychlost příchozího přenosu = 40 MiB/s + 0,04 * zřízená GiB

Zřízená velikost sdílené složky je určena kvótou sdílené složky. Kvótu sdílení lze kdykoli zvýšit, ale lze ji snížit až po 24 hodinách od posledního zvýšení. Po 24 hodinách čekání bez zvýšení kvóty můžete kvótu sdílení snížit tolikrát, kolikrát chcete, dokud ji znovu nezvýšíte. Změny škálování IOPS/Propustnost budou účinné během několika minut po změně velikosti.

Velikost zřízeného sdíleného podílu je možné zmenšit pod použitou gib. Pokud tak učiníte, neztratíte data, ale stále se vám bude účtovat použitá velikost a obdržíte výkon (viposlužby podle směrného plánu, propustnost a shluky VOPS) zřízené sdílené složky, nikoli velikost použitá.

Následující tabulka ilustruje několik příkladů těchto vzorců pro zřízené velikosti sdílené složky:

|Kapacita (GiB) | IOP Směrného plánu | VAOP SPU | Výstup (MiB/s) | Příchozí přenos (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Až 300     | 66   | 44   |
|500         | 500     | Až 1 500   | 90   | 60   |
|1,024       | 1,024   | Až 3 072   | 122   | 81   |
|5,120       | 5,120   | Až 15 360  | 368   | 245   |
|10,240      | 10,240  | Až 30 720  | 675 | 450   |
|33,792      | 33,792  | Až 100 000 | 2,088 | 1,392   |
|51,200      | 51,200  | Až 100 000 | 3,132 | 2,088   |
|102,400     | 100 000 | Až 100 000 | 6,204 | 4,136   |

> [!NOTE]
> Výkon sdílených složek podléhá omezením sítě počítače, dostupné šířce pásma sítě, velikosti vi, paralelismu a mnoha dalším faktorům. Například na základě interního testování s 8 velikostmi IO pro čtení a zápis kiB může jeden virtuální počítač Windows, *Standard F16s_v2*, připojený k prémiovému sdílení souborů přes SMB dosáhnout 20 kB čtení IOPS a 15K zápis vstupně-sad. S 512 MiB velikosti vstupně-výstupních vstupně-výstupních. Chcete-li dosáhnout maximálního výkonu škálování, rozprostřete zatížení mezi více virtuálních počítačích. Některé běžné problémy s výkonem a řešení naleznete v [příručce pro řešení potíží.](storage-troubleshooting-files-performance.md)

#### <a name="bursting"></a>Prasknutí
Prémiové sdílené složky mohou prasknout své iOPS až trojnásobně. Roztržení je automatizované a funguje na základě kreditního systému. Bursting funguje na základě nejlepšího úsilí a limit shluků není zárukou, sdílené složky mohou být až *do* limitu.

Kredity se hromadí v segmentu shluků vždy, když je provoz pro sdílenou složku nižší než základní vipony. Například 100 GiB podíl má 100 vipos směrného plánu. Pokud skutečný provoz na sdílené složce byl 40 VOPS pro konkrétní interval 1 sekundy, pak 60 nevyužitých VOPS jsou připsány do intervalu burst. Tyto kredity budou použity později, kdy operace překročí základní iOP.

> [!TIP]
> Velikost prasklého kbelíku = základní iops * 2 * 3600.

Kdykoli akcie překročí základní viposlužby a má kredity v kbelíku s praskla, praskne. Akcie mohou nadále prasknout tak dlouho, dokud kredity zůstávají, i když akcie menší než 50 TiB zůstanou na limitu roztržení až na hodinu. Akcie větší než 50 TiB mohou technicky překročit tento hodinový limit, až dvě hodiny, ale to je založeno na počtu naběhlých kreditů. Každý vod mimo základní vipos spotřebovává jeden kredit a po spotřebování všech kreditů by se podíl vrátil do výchozího viporu.

Akciové kredity mají tři stavy:

- Nabíhání, pokud sdílená složka používá méně než základní vipony.
- Klesá, když je sdílená složka prasknutí.
- Zbývající konstanta, pokud nejsou k dispozici žádné kredity nebo vipos směrného plánu.

Nové sdílené složky začínají plným počtem kreditů v jeho prasklém kbelíku. Burst kredity nebudou časově rozlišeny, pokud vipo služby sdílení klesne pod základní vipos z důvodu omezení serverem.

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Povolit standardní sdílené složky span až 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Regionální dostupnost
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundance
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migrace
V mnoha případech nebudete vytvářet novou čistou sdílenou složku pro vaši organizaci, ale místo toho migrovat existující sdílenou složku z místního souborového serveru nebo zařízení NAS do souborů Azure. Existuje mnoho nástrojů, které poskytuje společnost Microsoft i třetí strany, aby se migrace na sdílenou složku, ale mohou být zhruba rozděleny do dvou kategorií:

- **Nástroje, které udržují atributy systému souborů, jako jsou akly a časová razítka**:
    - **[Azure File Sync](storage-sync-files-planning.md)**: Azure File Sync se dá použít jako metodu ingestování dat do sdílené složky Azure, i když požadované koncové nasazení není k udržení místní přítomnosti. Azure File Sync se dá nainstalovat na stávající nasazení Windows Serveru 2012 R2, Windows Server 2016 a Windows Server 2019. Výhodou použití Azure File Sync jako mechanismu ingestování je, že koncoví uživatelé mohou nadále používat existující sdílenou složku na místě; přestřihnutí do sdílené složky Azure může dojít po dokončení nahrávání všech dat na pozadí.
    - **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy je známý kopírovací nástroj, který je dodáván s Windows a Windows Server. Robocopy lze použít k přenosu dat do souborů Azure připojením sdílené složky místně a pak pomocí připojené umístění jako cíl v příkazu Robocopy.

- **Nástroje, které neudržují atributy systému souborů**:
    - **Data Box**: Data Box poskytuje offline mechanismus přenosu dat pro fyzická data dodávat data do Azure. Tato metoda je určena ke zvýšení propustnosti a uložení šířky pásma, ale v současné době nepodporuje atributy systému souborů, jako jsou časová razítka a akl.
    - **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: AzCopy je nástroj příkazového řádku určený pro kopírování dat do a z Azure Files, stejně jako úložiště objektů Blob Azure pomocí jednoduchých příkazů s optimálním výkonem.

## <a name="next-steps"></a>Další kroky
* [Plánování nasazení synchronizace souborů Azure](storage-sync-files-planning.md)
* [Nasazení souborů Azure](storage-files-deployment-guide.md)
* [Nasazení synchronizace souborů Azure](storage-sync-files-deployment-guide.md)
