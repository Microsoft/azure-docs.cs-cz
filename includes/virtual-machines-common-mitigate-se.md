---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/14/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: ba41f6cce5233491020a0b42f4fd40dac060be57
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65815472"
---
**Poslední aktualizace dokumentů**: 14 května 2019 10:00 RÁNO PST.

Zveřejnění [nová třída zabezpečení CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) se označuje jako spekulativního spouštění útoků na straně kanálu má za následek dotazy z zákazníky, kteří potřebují další nejasnostem.  

Microsoft má nasazený způsoby zmírnění rizik v našich cloudových službách. Je chráněný infrastrukturu, která běží v Azure a izoluje úloh zákazníka od sebe navzájem. To znamená, že nelze potenciálním útočníkům používat stejnou infrastrukturu útoku na vaši aplikaci pomocí těchto ohrožení zabezpečení.

Azure používá [Údržba pro zachování paměti](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-not-requiring-a-reboot) pokaždé, když je to možné, chcete-li minimalizovat dopad pro zákazníka a eliminovat tak potřebu restartovat počítač. Azure bude dál využívat tyto metody při provádění aktualizací v celém systému na hostitele a ochrana našich zákazníků.

Další informace o tom, jak zabezpečení je integrované do všech oblastí Azure je k dispozici na [dokumentace ke službě Azure Security](https://docs.microsoft.com/azure/security/) lokality. 

> [!NOTE] 
> Vzhledem k tomu, že nejprve publikování tohoto dokumentu, několik variant této třídy ohrožení zabezpečení se poskytly. Microsoft nadále být výrazně investovali do ochrany našich zákazníků a poskytuje doprovodné materiály. Na této stránce se aktualizují, jak budeme dál uvolnit další opravy. 
> 
> 14. května 2019 [Intel zveřejněn](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00233.html) novou sadu spekulativního spouštění na straně kanálu chyby zabezpečení označované jako mikroarchitektury vzorkování dat (MDS naleznete pokyny Microsoftu ohledně zabezpečení [ADV190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)), který více CVEs bylo přiřazeno: 
> - CVE-2019-11091 - mikroarchitektury Data vzorkování paměti Uncacheable (MDSUM)
> - CVE-2018-12126 - Store mikroarchitektury vyrovnávací paměti pro Data vzorkování (MSBDS) 
> - CVE-2018-12127 - mikroarchitektury zatížení Port dat vzorkování (MLPDS)
> - CVE-2018-12130 - mikroarchitektury výplně vyrovnávací paměti pro Data vzorkování (MFBDS)
>
> Toto ohrožení zabezpečení ovlivňuje procesorů Intel Core® a procesorů Intel® Xeon®.  Microsoft Azure vydala aktualizací operačního systému a nasazení nové mikrokód, protože je k dispozici společností Intel, v rámci naší vozového parku ochrana našich zákazníků tyto nové zranitelná.   Azure úzce spolupracuje s technologií Intel k testování a validaci nové mikrokód před jejich oficiálním vydáním na platformě. 
>
> **Zákazníci, kteří spuštění nedůvěryhodného kódu v rámci svých virtuálních počítačů** potřeba provést akci na ochranu proti těmto ohrožením zabezpečení, přečtěte si téma níže pro další Rady týkající se všechna spekulativního spouštění na straně kanálu ohrožení zabezpečení (ADV poradci Microsoftu [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018), a [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Ostatní zákazníci by měla vyhodnotit tyto slabých míst z Defense v hloubce perspektivy a zvážit důsledky zabezpečení a výkonu svých zvolené konfigurace.



## <a name="keeping-your-operating-systems-up-to-date"></a>Průběžná operačních systémů

Aktualizace operačního systému se vyžadují k izolaci aplikací spuštěných v Azure od ostatních zákazníků Azure, je vždy vhodné aktualizování svého softwaru. Nejnovější zabezpečení kumulativní pro Windows obsahují způsoby zmírnění rizik pro několik spekulativního spouštění ohrožení zabezpečení na straně kanálu. Podobně distribuce Linuxu vydali několik aktualizací, které tyto nedostatky zabezpečení řeší. Tady jsou naše doporučené akce aktualizovat operační systém:

| Nabídka | Doporučená akce  |
|----------|---------------------|
| Azure Cloud Services  | Povolit [automatické aktualizace](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) nebo zajistit, aby běžely nejnovější hostovaného operačního systému. |
| Linuxové virtuální počítače Azure | Instalovat aktualizace z poskytovatele operačního systému. Další informace najdete v tématu [Linux](#linux) dále v tomto dokumentu. |
| Windows Azure Virtual Machines  | Nainstalujte nejnovější aktualizace zabezpečení.
| Další služby Azure PaaS | Neexistuje žádná akce potřebné pro zákazníky, kteří používají tyto služby. Azure automaticky udržuje vaše verze operačního systému aktuální. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Další pokyny, pokud používáte nedůvěryhodného kódu 

Zákazníci, kteří nepovolujte nedůvěryhodným uživatelům k vykonání libovolného kódu může chtít implementujte některé další funkce zabezpečení ve své službě Azure Virtual Machines nebo Cloud Services. Tyto funkce Ochrana před vektory zpřístupnění uvnitř procesu, které popisují několik chyb zabezpečení spekulativního spouštění.

Ukázkové scénáře, ve kterém se doporučuje další funkce zabezpečení:

- Můžete povolit kód, kterým nedůvěřujete ke spuštění uvnitř virtuálního počítače.  
    - *Například jeden z vašich zákazníků k nahrání do binárního souboru nebo skriptu, který jste pak provést v rámci vaší aplikace povolíte*. 
- Umožníte uživatelům, kterým nedůvěřujete pro přihlášení k virtuálnímu počítači pomocí nízké privilegované účty.   
    - *Například můžete povolit uživatele s nízkými oprávněními k přihlášení do jednoho z vašich virtuálních počítačů pomocí vzdálené plochy nebo SSH*.  
- Nedůvěryhodným uživatelům povolíte přístup k virtuálním počítačům prostřednictvím vnořená virtualizace.  
    - *Například ovládací prvek hostitele Hyper-V, ale virtuální počítače přidělit nedůvěryhodným uživatelům*. 

Zákazníci, kteří neimplementují scénáře zahrnující nedůvěryhodný kód není potřeba tyto další bezpečnostní funkce. 

## <a name="enabling-additional-security"></a>Umožňuje zvýšit zabezpečení 

Pokud používáte nedůvěryhodný kód, můžete povolit další funkce zabezpečení uvnitř virtuálního počítače nebo cloudové služby. Paralelně Ujistěte se, že je váš operační systém aktuální. k povolení funkcí zabezpečení uvnitř virtuálního počítače nebo cloudové služby

### <a name="windows"></a>Windows 

Cílového operačního systému musí být aktuální tyto další bezpečnostní funkce. Zatímco řada spekulativního spouštění na straně kanálu způsoby zmírnění rizik jsou ve výchozím nastavení povolené, další funkce popsané v tomto poli musí být povoleno ručně a může způsobit, že dopad na výkon. 


**Krok 1: Zakázat hyperthreadingem na virtuálním počítači** – zákazníci, kteří používají nedůvěryhodný kód na s vysokým počtem podprocesů, virtuální počítač bude nutné zakázat hyperthreadingem nebo přesunout na velikost virtuálního počítače – do s vysokým počtem podprocesů. Zkontrolujte, jestli váš virtuální počítač má povoleným hyperthreadingem, najdete níže uvedený skript příkazového řádku Windows z v rámci virtuálního počítače.

Typ `wmic` zadat interaktivní rozhraní. Zadejte níže zobrazíte velikost fyzických a logických procesorů na virtuálním počítači.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Pokud je počet logických procesorů větší než fyzických procesorů (jader), je povolen hyperthreading.  Pokud používáte virtuální počítač, s vysokým počtem podprocesů, [obraťte se na podporu Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) zobrazíte hyperthreadingem zakázán.  Jakmile hyperthreadingem je zakázaná, **podpora bude vyžadovat úplné restartování virtuálního počítače**. 


**Krok 2**: Paralelní ke kroku 1, postupujte podle pokynů v [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) ověření ochrany jsou povolené pomocí [SpeculationControl](https://aka.ms/SpeculationControlPS) modul prostředí PowerShell.

> [!NOTE]
> Pokud si už tento modul, je potřeba nainstalovat nejnovější verzi.
>


Výstup skriptu prostředí PowerShell by měly mít následujících hodnot pro ověření povolené nabízí další ochranu před těmito chybami:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
```

Pokud se zobrazí výstup `MDS mitigation is enabled: False`, prosím [obraťte se na podporu Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) možnosti k dispozici omezení rizik.



**Krok 3**: Pokud chcete povolit jádra virtuální adresu stínový provoz (KVAS) a podporu vkládání cílové větve (BTI) operačního systému, postupujte podle pokynů v [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) povolit ochranu pomocí `Session Manager` klíče registru. Je vyžadován restart.


**Krok 4**: Pro nasazení, která používají [vnořená virtualizace](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (D3 a E3 pouze): Tyto pokyny platí ve virtuálním počítači, který používáte jako hostitele Hyper-V.

1.  Postupujte podle pokynů v [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) povolit ochranu pomocí `MinVmVersionForCpuBasedMitigations` klíče registru.
2.  Nastavte typ hypervisoru Plánovač `Core` podle následujících pokynů [tady](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types).


### <a name="linux"></a>Linux

<a name="linux"></a>Povolení sadu další funkce zabezpečení uvnitř vyžaduje, aby cílový operační systém plně aktuální. Ve výchozím nastavení se povolí některá zmírnění rizik. Následující část popisuje funkce, které jsou vypnuté ve výchozím nastavení a/nebo závislé na hardwarovou podporu (mikrokód). Povolení těchto funkcí může způsobit, že dopad na výkon. Referenční dokumentaci poskytovatele operačního systému o další pokyny


**Krok 1: Zakázat hyperthreadingem na virtuálním počítači** – zákazníci, kteří používají nedůvěryhodný kód na s vysokým počtem podprocesů, virtuální počítač bude nutné zakázat hyperthreadingem nebo přesunout do virtuálního počítače – do s vysokým počtem podprocesů.  Chcete-li zkontrolovat, zda jsou spuštěny s vysokým počtem podprocesů virtuálního počítače, spusťte `lscpu` v virtuálního počítače s Linuxem. 

Pokud `Thread(s) per core = 2`, pak je povolen hyperthreading. 

Pokud `Thread(s) per core = 1`, pak hyperthreadingem byla zakázána. 

 
Ukázka výstupu pro virtuální počítač s povoleným hyperthreadingem: 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0,2,4,6
Off-line CPU(s) list:  1,3,5,7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

Pokud používáte virtuální počítač, s vysokým počtem podprocesů, [obraťte se na podporu Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) zobrazíte hyperthreadingem zakázán.  Jakmile hyperthreadingem je zakázaná, **podpora bude vyžadovat úplné restartování virtuálního počítače**.


**Krok 2**: Cílem je zmírnit nebezpečí některý níže ohrožení zabezpečení na straně kanálu spekulativního spouštění, najdete v dokumentaci poskytovatele operačního systému:   
 
- [RedHat a CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 

## <a name="next-steps"></a>Další postup

Tento článek obsahuje pokyny, které pod spekulativního spouštění na straně kanálu útoků, které ovlivňují mnoho moderní procesorů:

[Chyby zabezpečení Spectre Meltdown](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715 - vkládání cílové větve (BTI)  
- CVE-2017-5754 – izolace tabulky stránky jádra (KPTI)
- CVE-2018-3639 – nepoužívat spekulativního Store (KPTI) 
 
[L1 Chyby terminálu (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615 – rozšíření Guard Software Intel (Intel SGX)
- CVE-2018-3620 – operační systémy (OS) a systém správy režimu (SMM)
- CVE-2018-3646 – ovlivňuje Virtual Machine Manager (VMM)

[Vzorkování dat mikroarchitektury](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE-2019-11091 - mikroarchitektury Data vzorkování paměti Uncacheable (MDSUM)
- CVE-2018-12126 - Store mikroarchitektury vyrovnávací paměti pro Data vzorkování (MSBDS)
- CVE-2018-12127 - mikroarchitektury zatížení Port dat vzorkování (MLPDS)
- CVE-2018-12130 - mikroarchitektury výplně vyrovnávací paměti pro Data vzorkování (MFBDS)








