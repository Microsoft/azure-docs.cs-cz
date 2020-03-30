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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73935889"
---
**Poslední aktualizace dokumentu**: 12 Listopad 2019 10:00 AM PST.

Zveřejnění nové [třídy chyb zabezpečení procesoru](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) známé jako spekulativní spuštění útoků na straně kanálu má za následek otázky od zákazníků, kteří hledají větší jasnost.  

Společnost Microsoft nasadila skutečnosti snižující závažnost rizika ve všech našich cloudových službách. Infrastruktura, která spouští Azure a izoluje úlohy zákazníků od sebe navzájem je chráněna. To znamená, že potenciální útočník, který používá stejnou infrastrukturu, nemůže napadnout vaši aplikaci pomocí těchto chyb zabezpečení.

Azure používá [údržbu,](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot) kdykoli je to možné, aby minimalizoval dopad na zákazníka a eliminoval potřebu restartování. Azure bude i nadále využívat tyto metody při provádění celosystémových aktualizací hostitele a ochraně našich zákazníků.

Další informace o tom, jak je zabezpečení integrované do všech aspektů Azure, jsou dostupné na webu [dokumentace zabezpečení Azure.](https://docs.microsoft.com/azure/security/) 

> [!NOTE] 
> Od prvního publikování tohoto dokumentu bylo zveřejněno několik variant této třídy ohrožení zabezpečení. Společnost Microsoft je i nadále silně investována do ochrany našich zákazníků a poskytování poradenství. Tato stránka bude aktualizována, jak budeme pokračovat ve vydávání dalších oprav. 
> 
> 12. listopadu 2019 [společnost Intel publikovala](https://software.intel.com/security-software-guidance/insights/deep-dive-intel-transactional-synchronization-extensions-intel-tsx-asynchronous-abort) technické poradenství týkající se chyby zabezpečení asynchronního přerušení transakcí (Intel® TSX) (Intel® TSX), kterému je přiřazeno [cve-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135). Tato chyba zabezpečení ovlivňuje procesory Intel® Core® a procesory Intel® Xeon®.  Microsoft Azure vydal aktualizace operačního systému a nasazuje nový mikrokód, jak je k dispozici společností Intel, v celém našem vozovém parku, aby chránil naše zákazníky před těmito novými chybami zabezpečení.   Azure úzce spolupracuje se společností Intel na testování a ověřování nového mikrokódu před jeho oficiálním vydáním na platformě. 
>
> **Zákazníci, kteří používají nedůvěryhodný kód v rámci svého virtuálního počítači,** musí podniknout kroky k ochraně proti těmto chybám zabezpečení tím, že si přečtou níže další pokyny ke všem chybám zabezpečení spekulativního spuštění na straně kanálu (Microsoft Advisories ADV [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)a [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Ostatní zákazníci by měli vyhodnotit tyto chyby zabezpečení z hlediska ochrany do hloubky a zvážit důsledky zabezpečení a výkonu jejich zvolené konfigurace.
> 



## <a name="keeping-your-operating-systems-up-to-date"></a>Udržování vašich operačních systémů v aktuálním stavu

Zatímco aktualizace operačního systému není nutné izolovat vaše aplikace spuštěné v Azure od jiných zákazníků Azure, je vždy osvědčeným postupem, aby váš software aktuální. Nejnovější kumulativní zabezpečení pro systém Windows obsahují skutečnosti snižující závažnost rizika pro několik chyb zabezpečení kanálu spekulativní spuštění straně kanálu. Podobně linuxové distribuce vydaly několik aktualizací k řešení těchto chyb zabezpečení. Zde jsou naše doporučené akce pro aktualizaci operačního systému:

| Nabídka | Doporučená akce  |
|----------|---------------------|
| Azure Cloud Services  | Povolte [automatickou aktualizaci](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) nebo se ujistěte, že používáte nejnovější hostovaný operační systém. |
| Virtuální počítače Azure Linux | Nainstalujte aktualizace od poskytovatele operačního systému. Další informace naleznete v [tématu Linux](#linux) dále v tomto dokumentu. |
| Virtuální počítače Azure Windows  | Nainstalujte nejnovější kumulativní zabezpečení.
| Další služby Azure PaaS | Pro zákazníky, kteří tyto služby využívají, není nutná žádná akce. Azure automaticky udržuje verze operačního systému aktuální. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Další pokyny, pokud používáte nedůvěryhodný kód 

Zákazníci, kteří nedůvěryhodným uživatelům povolí spouštět libovolný kód, mohou chtít implementovat některé další funkce zabezpečení v rámci svých virtuálních počítačů Azure nebo cloudových služeb. Tyto funkce chrání před vektory zpřístupnění uvnitř procesu, které popisují několik chyb zabezpečení spekulativního spuštění.

Příklady scénářů, ve kterých jsou doporučeny další funkce zabezpečení:

- Povolíte spuštění kódu, kterému nedůvěřujete, uvnitř virtuálního počítače.  
    - *Například povolíte jednomu ze zákazníků nahrát binární nebo skript, který pak spustíte v rámci aplikace*. 
- Povolíte uživatelům, kterým nedůvěřujete, aby se k virtuálnímu počítači přihlašovali pomocí účtů s nízkými oprávněními.   
    - *Například povolíte uživateli s nízkými oprávněními přihlásit se k jednomu z vašich virtuálních počítačů pomocí vzdálené plochy nebo SSH*.  
- Umožňujete nedůvěryhodným uživatelům přístup k virtuálním počítačům implementovaným prostřednictvím vnořené virtualizace.  
    - *Například můžete řídit hostitele Hyper-V, ale přidělit virtuální chod nedůvěryhodným uživatelům*. 

Zákazníci, kteří neimplementují scénář zahrnující nedůvěryhodný kód, nemusí povolit tyto další funkce zabezpečení. 

## <a name="enabling-additional-security"></a>Povolení dalšího zabezpečení 

Pokud používáte nedůvěryhodný kód, můžete v rámci virtuálního počítače nebo cloudové služby povolit další funkce zabezpečení. Současně zajistěte, aby byl váš operační systém aktuální, aby bylo možné povolit funkce zabezpečení uvnitř virtuálního počítače nebo cloudové služby.

### <a name="windows"></a>Windows 

Aby bylo možné tyto další funkce zabezpečení povolit, musí být cílový operační systém aktuální. Zatímco mnoho spekulativní spuštění straně kanálu skutečnosti snižující závažnost rizika jsou povoleny ve výchozím nastavení, další funkce popsané zde musí být povolena ručně a může způsobit dopad na výkon. 


**Krok 1: Zakázat hyper-threading na virtuálním počítači** – zákazníci s nedůvěryhodný kód na virtuálním počítači s hypervlákny bude muset zakázat hyper-threading nebo přejít na velikost virtuálního počítače bez hyper-threaded. Odkaz [na tento dokument](https://docs.microsoft.com/azure/virtual-machines/windows/acu) pro seznam velikostí virtuálních virtuálních zařízení s hyper vlákny (kde poměr virtuálního procesoru k jádru je 2:1). Chcete-li zkontrolovat, zda je povoleno hyper-threading, naleznete níže uvedený skript pomocí příkazového řádku systému Windows z v rámci virtuálního počítače.

Zadejte `wmic` pro vstup do interaktivního rozhraní. Pak zadejte níže zobrazíte množství fyzické a logické procesory na virtuálním počítači.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Pokud je počet logických procesorů větší než fyzické procesory (jádra), je povoleno hyper-threading.  Pokud používáte virtuální počítač s hypervlákny, [obraťte se](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) na podporu Azure a zjistěte, jestli je hyper-threading zakázán.  Po zakázání hyper-threadingu **bude podpora vyžadovat úplné restartování virtuálního počítače**. Přečtěte si prosím [počet jádra,](#core-count) abyste pochopili, proč se počet jádra virtuálního počítače snížil.


**Krok 2**: Souběžně s krokem 1 postupujte podle pokynů v [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) a ověřte, zda jsou pomocí modulu [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell povoleny ochrany.

> [!NOTE]
> Pokud jste tento modul stáhli dříve, budete muset nainstalovat nejnovější verzi.
>


Výstup skriptu prostředí PowerShell by měl mít níže uvedené hodnoty k ověření povolené ochrany proti těmto chybám zabezpečení:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
Windows OS support for TAA mitigation is enabled: True
```

Pokud se `MDS mitigation is enabled: False`výstup zobrazí , [obraťte se](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) na podporu Azure pro dostupné možnosti zmírnění.



**Krok 3**: Chcete-li povolit podporu operačního systému Stínování virtuálních adres jádra (KVAS) a injektáže `Session Manager` větev (BTI), postupujte podle pokynů v [kb4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) a povolte ochranu pomocí klíčů registru. Je nutné restartovat počítač.


**Krok 4**: Pro nasazení, která používají [vnořené virtualizace](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (D3 a E3 jenom): Tyto pokyny platí uvnitř virtuálního počítače, který používáte jako hostitele Hyper-V.

1.  Podle pokynů [kKB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) povolte ochranu `MinVmVersionForCpuBasedMitigations` pomocí klíčů registru.
2.  Nastavte typ plánovače hypervisoru podle `Core` pokynů [zde](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types).


### <a name="linux"></a>Linux

<a name="linux"></a>Povolení sady dalších funkcí zabezpečení uvnitř vyžaduje, aby byl cílový operační systém plně aktuální. Některé skutečnosti snižující závažnost rizika budou ve výchozím nastavení povoleny. Následující část popisuje funkce, které jsou ve výchozím nastavení vypnuté a/nebo závislé na hardwarové podpoře (mikrokódu). Povolení těchto funkcí může způsobit dopad na výkon. Další pokyny naleznete v dokumentaci k poskytovateli operačního systému.


**Krok 1: Zakázat hyper-threading na virtuálním počítači** – zákazníci s nedůvěryhodný kód na virtuálním počítači s hypervlákny bude muset zakázat hyper-threading nebo přejít na virtuální počítač bez hyper-threaded.  Odkaz [na tento dokument](https://docs.microsoft.com/azure/virtual-machines/linux/acu) pro seznam velikostí virtuálních virtuálních zařízení s hyper vlákny (kde poměr virtuálního procesoru k jádru je 2:1). Chcete-li zkontrolovat, zda používáte virtuální počítač `lscpu` s hypervlákny, spusťte příkaz ve virtuálním počítači s Linuxem. 

Pokud `Thread(s) per core = 2`bylo povoleno hyper-threading. 

Pokud `Thread(s) per core = 1`bylo zakázáno hyper-threading. 

 
Ukázkový výstup pro virtuální virtuální připojení s povoleným hypervlákna: 

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

Pokud používáte virtuální počítač s hypervlákny, [obraťte se](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) na podporu Azure a zjistěte, jestli je hyper-threading zakázán.  Po zakázání hyper-threadingu **bude podpora vyžadovat úplné restartování virtuálního počítače**. Přečtěte si prosím [počet jádra,](#core-count) abyste pochopili, proč se počet jádra virtuálního počítače snížil.



**Krok 2**: Chcete-li zmírnit některé z níže uvedených chyb zabezpečení spekulativní spuštění straně kanálu, naleznete v dokumentaci poskytovatele operačního systému:   
 
- [Redhat a CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>Počet jádra

Když se vytvoří virtuální počítač s hypervlákny, Azure přidělí 2 vlákna na jádro – ty se nazývají virtuální procesory. Když je hyper-threading zakázán, Azure odebere vlákno a zobrazí se jednovláknová jádra (fyzická jádra). Poměr virtuálního procesoru k procesoru je 2:1, takže jakmile je hyper-threading zakázán, počet procesorů ve virtuálním počítače se bude zdát, že se snížil o polovinu. Například virtuální počítač D8_v3 je virtuální počítač s velkým vláknem běžící na 8 virtuálních procesorech (2 vlákna na jádro x 4 jádra).  Pokud je hyper-threading zakázán, procesory klesnou na 4 fyzická jádra s 1 vláknem na jádro. 

## <a name="next-steps"></a>Další kroky

Tento článek obsahuje pokyny pro níže spekulativní provádění útoky na straně kanálu, které ovlivňují mnoho moderních procesorů:

[Spectre Meltdown](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715 - Vstřikování cílů pobočky (BTI)  
- CVE-2017-5754 - Izolace stránkových tabulek jádra (KPTI)
- CVE-2018-3639 – Spekulativní obchvat obchodu (KPTI) 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) – Informace o jádru Windows – varianta varianty Spectre 1
 
[Porucha terminálu L1 (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615 - Rozšíření Intel Software Guard (Intel SGX)
- CVE-2018-3620 - Operační systémy (OS) a režim správy systému (SMM)
- CVE-2018-3646 – dopady na správce virtuálních strojů (VMM)

[Vzorkování dat mikroarchitektury](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE-2019-11091 - Mikroarchitektonické vzorkování dat necacheable paměti (MDSUM)
- CVE-2018-12126 - Vzorkování dat vyrovnávací paměti úložiště mikroarchitektury (MSBDS)
- CVE-2018-12127 - Mikroarchitektonické vzorkování dat zátěžového portu (MLPDS)
- CVE-2018-12130 - Mikroarchitektonické vzorkování vyrovnávací paměti výplně (MFBDS)

Asynchronní přerušení transakční synchronizace (Intel® TSX):  
- [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135) – TSX transakční asynchronní přerušení (TAA)








