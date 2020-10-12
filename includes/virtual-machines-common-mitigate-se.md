---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/12/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 6668d9753d0b93ab907d37cdeff8315f488cff7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "73935889"
---
**Poslední aktualizace dokumentu**: 12. listopadu 2019 10:00 AM PST.

Zveřejnění [nové třídy chyb zabezpečení procesoru](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) , známé jako spekulativní útoky na straně služby, má za následek otázky od zákazníků, kteří hledají větší přehlednost.  

Společnost Microsoft nasadila zmírnění hrozeb napříč všemi našimi Cloud Services. Je chráněná infrastruktura, která spouští Azure a izoluje úlohy zákazníka od sebe navzájem. To znamená, že potenciální útočník používající stejnou infrastrukturu nemůže vaše aplikace napadnout pomocí těchto ohrožení zabezpečení.

Azure používá [zachovávání údržby paměti](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot) , kdykoli je to možné, k minimalizaci dopadu na zákazníky a eliminaci nutnosti restartování. Azure bude tyto metody používat při provádění aktualizací v hostiteli a ochraně našich zákazníků.

Další informace o integraci zabezpečení do všech aspektů Azure najdete na webu [dokumentace k zabezpečení Azure](https://docs.microsoft.com/azure/security/) . 

> [!NOTE] 
> Od prvního publikování tohoto dokumentu jsme zavřeli několik variant této třídy ohrožení zabezpečení. Společnost Microsoft bude i nadále silně investovat do ochrany našich zákazníků a poskytování pokynů. Tato stránka bude aktualizována, protože budeme dál vydávat další opravy. 
> 
> 12. listopadu 2019 [publikovalo společnost intel](https://software.intel.com/security-software-guidance/insights/deep-dive-intel-transactional-synchronization-extensions-intel-tsx-asynchronous-abort) ® technický poradce týkající se chyby zabezpečení TSX (Intel®) transakce asynchronního přerušení transakcí (TAA), která je přiřazena [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135). Tato chyba zabezpečení má vliv na procesory Intel® Core® a procesory Intel® Xeon®.  Microsoft Azure vydali aktualizace operačního systému a nasazují se nové vyžádal povolení mikrokódu, protože je k dispozici prostřednictvím technologie Intel, v rámci našeho loďstva k ochraně našich zákazníků před těmito novými chybami zabezpečení.   Azure úzce spolupracuje s Intel a testuje a ověřuje nové vyžádal povolení mikrokódu před jeho oficiálním vydáním na platformě. 
>
> **Zákazníci, kteří spouštějí nedůvěryhodný kód v rámci svého virtuálního počítače** , musí provést opatření k ochraně proti těmto chybám zabezpečení. Níže najdete další pokyny ke všem spekulativním chybám zabezpečení na straně klienta ( [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)advisory, [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)a [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Jiní zákazníci by měli vyhodnotit tyto chyby zabezpečení z hlediska obrany v hloubkové perspektivě a vzít v úvahu dopad na zabezpečení a výkon zvolené konfigurace.
> 



## <a name="keeping-your-operating-systems-up-to-date"></a>Udržování aktuálnosti operačních systémů

Přestože aktualizace operačního systému není nutná k izolaci vašich aplikací běžících na Azure od jiných zákazníků Azure, je vždy osvědčeným postupem udržet software v aktuálním stavu. Nejnovější kumulativní aktualizace pro Windows obsahují zmírňující rizika pro několik spekulativních ohrožení zabezpečení kanálu na straně služby. Podobně distribuce systému Linux vydaly několik aktualizací, které řeší tyto chyby zabezpečení. Tady jsou doporučené akce pro aktualizaci operačního systému:

| Nabídka | Doporučená akce  |
|----------|---------------------|
| Azure Cloud Services  | Povolte [Automatické aktualizace](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) nebo se ujistěte, že používáte nejnovější hostovaný operační systém. |
| Linux Virtual Machines Azure | Nainstalujte aktualizace od svého poskytovatele operačního systému. Další informace najdete v části [Linux](#linux) dále v tomto dokumentu. |
| Windows Virtual Machines Azure  | Nainstalujte nejnovější kumulativní zabezpečení.
| Další služby Azure PaaS | Pro zákazníky, kteří tyto služby nepoužívají, není nutná žádná akce. Azure automaticky udržuje verze operačních systémů v aktuálním stavu. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Další pokyny, pokud používáte nedůvěryhodný kód 

Zákazníci, kteří umožňují nedůvěryhodným uživatelům spustit libovolný kód, můžou chtít implementovat některé další funkce zabezpečení v rámci svých Virtual Machines nebo Cloud Services Azure. Tyto funkce jsou chráněny proti vektorům zpřístupnění uvnitř procesu, které popisují několik spekulativních ohrožení zabezpečení za běhu.

Příklady scénářů, ve kterých jsou doporučené další funkce zabezpečení:

- Povolíte kód, kterému nedůvěřujete, aby se spouštěl ve vašem VIRTUÁLNÍm počítači.  
    - *Například umožníte jednomu z vašich zákazníků nahrát binární soubor nebo skript, který potom spustíte v rámci aplikace*. 
- Uživatelům, kterým nedůvěřujete, umožníte přihlášení k VIRTUÁLNÍmu počítači pomocí účtů s nízkými oprávněními.   
    - *Můžete například uživateli s nízkými oprávněními přihlašovat se k některému z vašich virtuálních počítačů pomocí vzdálené plochy nebo SSH*.  
- Povolíte nedůvěryhodným uživatelům přístup k virtuálním počítačům implementovaným prostřednictvím vnořené virtualizace.  
    - *Například řídíte hostitele Hyper-V, ale přidělíte virtuálním počítačům nedůvěryhodným uživatelům*. 

Zákazníci, kteří neimplementují scénář zahrnující nedůvěryhodný kód, nemusí tyto další funkce zabezpečení povolit. 

## <a name="enabling-additional-security"></a>Povolení dalšího zabezpečení 

Pokud používáte nedůvěryhodný kód, můžete ve svém VIRTUÁLNÍm počítači nebo v cloudové službě povolit další funkce zabezpečení. Paralelně zajistěte, aby byl váš operační systém v aktuálním stavu povolený pro funkce zabezpečení ve vašem VIRTUÁLNÍm počítači nebo cloudové službě.

### <a name="windows"></a>Windows 

Aby bylo možné tyto další funkce zabezpečení povolit, musí být cílový operační systém aktuální. I když jsou ve výchozím nastavení povolené spousty zmírňování kanálu na straně spuštění, musí být všechny další popsané funkce povolené ručně a můžou způsobit dopad na výkon. 


**Krok 1: zakázání prostředí Hyper-Threading na virtuálním** počítači – zákazníci, kteří spouštějí nedůvěryhodný kód na virtuálním počítači s vlákny s technologií Hyper-thread, budou muset zakázat technologii Hyper-Threading nebo přejít na velikost virtuálního počítače bez technologie Hyper-thread. Odkaz na [Tento dokument](https://docs.microsoft.com/azure/virtual-machines/windows/acu) pro seznam velikostí virtuálních počítačů s vlákny typu Hyper (kde poměr VCPU až Core je 2:1). Pokud chcete zjistit, jestli má virtuální počítač povolený technologii Hyper-Threading, přečtěte si níže uvedený skript pomocí příkazového řádku Windows v rámci virtuálního počítače.

Zadejte, `wmic` Chcete-li zadat interaktivní rozhraní. Potom zadejte níže pro zobrazení množství fyzických a logických procesorů na virtuálním počítači.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Pokud je počet logických procesorů větší než fyzický procesor (jádra), je technologie Hyper-Threading povolena.  Pokud používáte virtuální počítač s vlákny s technologií Hyper-thread, [Požádejte prosím podporu Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) o zakázání prostředí Hyper-Threading.  Když je technologie Hyper-Threading zakázaná, **bude podpora vyžadovat úplné restartování virtuálního počítače**. Informace o tom, proč se počet jader virtuálních počítačů snížil, najdete v [části základní počet](#core-count) .


**Krok 2**: paralelně s krokem 1 postupujte podle pokynů v části [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) a ověřte, jestli jsou povolené ochrany pomocí modulu [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShellu.

> [!NOTE]
> Pokud jste tento modul stáhli dřív, budete muset nainstalovat nejnovější verzi.
>


Výstup skriptu PowerShellu by měl mít níže uvedené hodnoty, aby bylo možné ověřit povolené ochrany proti těmto chybám zabezpečení:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
Windows OS support for TAA mitigation is enabled: True
```

Pokud se zobrazí výstup `MDS mitigation is enabled: False` , [Požádejte prosím podporu Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) o dostupné možnosti zmírnění.



**Krok 3**: Pokud chcete povolit podporu pro vytváření stínových adres (KVAS) a BTI (větvení Target) pro operační systém, postupujte podle pokynů v [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) pro povolení ochrany pomocí `Session Manager` klíčů registru. Vyžaduje se restartování.


**Krok 4**: u nasazení, která používají [vnořené virtualizaci](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (jenom D3 a E3): tyto pokyny platí v rámci virtuálního počítače, který používáte jako hostitele Hyper-V.

1.  Pokud chcete povolit ochranu pomocí klíčů registru, postupujte podle pokynů v [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) `MinVmVersionForCpuBasedMitigations` .
2.  Nastavte typ plánovače hypervisoru na `Core` podle pokynů uvedených [tady](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types).


### <a name="linux"></a>Linux

<a name="linux"></a>Povolení sady dalších funkcí zabezpečení v rámci vyžaduje, aby byl cílový operační systém plně aktuální. Ve výchozím nastavení budou povolena některá zmírnění rizik. Následující část popisuje funkce, které jsou ve výchozím nastavení vypnuté nebo závislé na hardwarové podpoře (vyžádal povolení mikrokódu). Povolení těchto funkcí může způsobit dopad na výkon. Další pokyny najdete v dokumentaci od poskytovatele operačního systému.


**Krok 1: zakázání prostředí Hyper-Threading na virtuálním** počítači – zákazníci, kteří spouštějí nedůvěryhodný kód na virtuálním počítači s vlákny s technologií Hyper-thread, budou muset zakázat technologii Hyper-Threading nebo přejít na virtuální počítač bez technologie Hyper-thread.  Odkaz na [Tento dokument](https://docs.microsoft.com/azure/virtual-machines/linux/acu) pro seznam velikostí virtuálních počítačů s vlákny typu Hyper (kde poměr VCPU až Core je 2:1). Pokud chcete zjistit, jestli používáte virtuální počítač s vlákny s technologií Hyper- `lscpu` v, spusťte příkaz na virtuálním počítači se systémem Linux. 

Pokud je `Thread(s) per core = 2` , je povolený Hyper-Threading. 

Pokud `Thread(s) per core = 1` je, je Hyper-Threading zakázaný. 

 
Ukázkový výstup pro virtuální počítač s povolenou technologií Hyper-Threading: 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

Pokud používáte virtuální počítač s vlákny s technologií Hyper-thread, [Požádejte prosím podporu Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) o zakázání prostředí Hyper-Threading.  Když je technologie Hyper-Threading zakázaná, **bude podpora vyžadovat úplné restartování virtuálního počítače**. Informace o tom, proč se počet jader virtuálních počítačů snížil, najdete v [části základní počet](#core-count) .



**Krok 2**: Chcete-li zmírnit některou z níže uvedených ohrožení zabezpečení na straně služby spekulativního prostředí, přečtěte si dokumentaci svého poskytovatele operačního systému:   
 
- [RedHat a CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>Počet jader

Když se vytvoří virtuální počítač s vlákny s technologií Hyper-thread, Azure přidělí 2 vlákna na jádro – Tato metoda se nazývá vCPU. Když je technologie Hyper-Threading zakázaná, Azure odebere vlákno a rozsvítí jednotlivé procesory s jedním vláknem (fyzické jádra). Poměr vCPU k procesoru je 2:1, takže když je technologie Hyper-Threading zakázaná, bude se počet PROCESORů ve virtuálním počítači jevit jako polovinu. Například D8_v3 virtuální počítač je virtuální počítač s vlákny s technologií Hyper-v, který běží na 8 vCPU (2 vlákna na jádro x 4 jádra).  Když je technologie Hyper-Threading zakázaná, CPU se přeruší na 4 fyzické jádra s 1 vláknem na jádro. 

## <a name="next-steps"></a>Další kroky

Tento článek poskytuje pokyny k následujícím postupům spekulativních útoků na straně vykonávání, které mají vliv na mnoho moderních procesorů:

[Spectre Meltdown](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715 – vkládání cíle větve (BTI)  
- CVE-2017-5754 – izolace tabulky stránky jádra (KPTI)
- CVE-2018-3639 – spekulativní obcházení obchodu (KPTI) 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) – informace o jádrech systému Windows – varianta Spectre varianty 1
 
[Chyba terminálu L1 (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615 – rozšíření Intel software Guard (Intel SGX)
- CVE-2018-3620-operační systémy (OS) a režim správy systému (SMM)
- CVE-2018-3646 – dopady Virtual Machine Manager (VMM)

[Vzorkování dat mikroarchitektury](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE-2019-11091 – vzorkování dat s neukládatelné do mezipaměti v mikroarchitektuře (MDSUM)
- CVE-2018-12126-vzorkování dat vyrovnávací paměti úložiště mikroarchitektury (MSBDS)
- CVE-2018-12127-vzorkování dat portu pro zatížení mikroarchitekturou (MLPDS)
- CVE-2018-12130-vzorkování dat vyrovnávací paměti pro doplňování mikroarchitektury (MFBDS)

Asynchronní přerušení transakce v rozšířeních transakční synchronizace (Intel® TSX):  
- [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135) – asynchronní přerušení transakce TSX (TAA)








