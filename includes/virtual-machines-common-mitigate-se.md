---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 130cc66831b25621cb022eb19005c624fcd71b9e
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2018
ms.locfileid: "40105502"
---
**Poslední aktualizace dokumentů**: 14. srpna 2018 10:00 RÁNO PST.

Zveřejnění [nová třída zabezpečení CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) se označuje jako spekulativního spouštění útoků na straně kanálu má za následek dotazy z zákazníky, kteří potřebují další nejasnostem.  

Microsoft má nasazený způsoby zmírnění rizik v našich cloudových službách. Je chráněný infrastrukturu, která běží v Azure a izoluje úloh zákazníka od sebe navzájem. To znamená, že nelze potenciálním útočníkům používat stejnou infrastrukturu útoku na vaši aplikaci pomocí těchto ohrožení zabezpečení.

Azure používá [Údržba pro zachování paměti](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#memory-preserving-maintenance) pokaždé, když je to možné, chcete-li minimalizovat dopad pro zákazníka a eliminovat tak potřebu restartovat počítač. Azure bude dál využívat tyto metody při provádění aktualizací v celém systému na hostitele a ochrana našich zákazníků.

Další informace o tom, jak zabezpečení je integrované do všech oblastí Azure je k dispozici na [dokumentace ke službě Azure Security](https://docs.microsoft.com/azure/security/) lokality. 

> [!NOTE] 
> Vzhledem k tomu, že nejprve publikování tohoto dokumentu, několik variant této třídy ohrožení zabezpečení se poskytly. Microsoft nadále být výrazně investovali do ochrany našich zákazníků a poskytuje doprovodné materiály. Na této stránce se aktualizují, jak budeme dál uvolnit další opravy. 
> 
> 14. srpna 2018, odvětví zveřejněn nové spekulativního spouštění na straně kanálu chyby zabezpečení označované jako [L1 terminálu selhání](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018) (L1TF) který byl přiřazen více CVEs ([CVE-2018-3615, CVE-2018-3620 a CVE-2018-3646](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00161.html)). Toto ohrožení zabezpečení ovlivňuje procesorů Intel Core® a procesorů Intel® Xeon®. Microsoft je nasazena způsoby zmírnění rizik napříč naše cloudové služby, které posílit izolace mezi zákazníky. Přečtěte si níže další doprovodné materiály k ochraně proti L1TF a předchozí ohrožení zabezpečení ([chyby zabezpečení Spectre Variant 2 CVE-2017-5715 a Meltdown Variant 3 CVE-2017-5754](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)).
>  






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

Můžete povolit další funkce zabezpečení uvnitř virtuálního počítače nebo cloudové služby.

### <a name="windows"></a>Windows 

Cílového operačního systému musí být aktuální tyto další bezpečnostní funkce. Zatímco řada spekulativního spouštění na straně kanálu způsoby zmírnění rizik jsou ve výchozím nastavení povolené, další funkce popsané v tomto poli musí být povoleno ručně a může způsobit, že dopad na výkon. 

**Krok 1**: [obraťte se na podporu Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) vystavení aktualizace firmwaru (mikrokód) na virtuální počítače. 

**Krok 2**: podpora povolit jádra virtuální adresu stínový provoz (KVAS) a vkládání cílové větve (BTI) operačního systému. Postupujte podle pokynů v [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) povolit ochranu prostřednictvím `Session Manager` klíče registru. Je vyžadován restart. 

**Krok 3**: pro nasazení, která používají [vnořená virtualizace](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (D3 a E3 pouze): tyto pokyny platí ve virtuálním počítači, který používáte jako hostitele Hyper-V. 

1. Postupujte podle pokynů v [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) povolit ochranu prostřednictvím `MinVmVersionForCpuBasedMitigations` klíče registru.  
 
1. Nastavte typ hypervisoru Plánovač **Core** podle následujících pokynů [tady](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types). 

**Krok 4**: postupujte podle pokynů v [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) ověření ochrany jsou povolené pomocí [SpeculationControl](https://aka.ms/SpeculationControlPS) modul prostředí PowerShell. 

> [!NOTE]
> Pokud si už tento modul, je potřeba nainstalovat nejnovější verzi.
>

Všechny virtuální počítače by se zobrazit:

```
branch target injection mitigation is enabled: True

kernel VA shadow is enabled: True  

L1TFWindowsSupportEnabled: True
```


### <a name="linux"></a>Linux

<a name="linux"></a>Povolení sadu další funkce zabezpečení uvnitř vyžaduje, aby cílový operační systém plně aktuální. Ve výchozím nastavení se povolí některá zmírnění rizik. Následující část popisuje funkce, které jsou vypnuté ve výchozím nastavení a/nebo závislé na hardwarovou podporu (mikrokód). Povolení těchto funkcí může způsobit, že dopad na výkon. Referenční dokumentaci poskytovatele operačního systému o další pokyny
 
**Krok 1**: [obraťte se na podporu Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) vystavení aktualizace firmwaru (mikrokód) na virtuální počítače.
 
**Krok 2**: podpora povolit větev cílové vkládání (BTI) operačního systému pro zmírnění CVE-2017-5715 (chyby zabezpečení Spectre Variant 2) pomocí následujících dokumentaci poskytovatele operačního systému. 
 
**Krok 3**: Povolit jádra stránky tabulky izolace (KPTI) ke zmírnění CVE-2017-5754 (Meltdown Variant 3) pomocí následujících dokumentaci poskytovatele operačního systému. 
 
Další informace jsou k dispozici od poskytovatele operačního systému:  
 
- [RedHat a CentOS](https://access.redhat.com/security/vulnerabilities/speculativeexecution) 
- [SuSE](https://www.suse.com/support/kb/doc/?id=7022512) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/SpectreAndMeltdown) 


## <a name="next-steps"></a>Další postup

Další informace najdete v tématu [ochrana zákazníků Azure před chybou zabezpečení CPU](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).
