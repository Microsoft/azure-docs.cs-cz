---
title: 'Preview: důvěryhodné spuštění virtuálních počítačů Azure'
description: Přečtěte si o důvěryhodných spuštěních virtuálních počítačů Azure.
author: khyewei
ms.author: khwei
ms.service: virtual-machines
ms.subservice: security
ms.topic: conceptual
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: template-concept; references_regions
ms.openlocfilehash: bc1afa72a0eebd2bb467616237641222b790923c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679381"
---
# <a name="trusted-launch-for-azure-virtual-machines-preview"></a>Důvěryhodné spuštění virtuálních počítačů Azure (Preview)

Azure nabízí možnost důvěryhodného spuštění jako bezproblémového způsobu, jak zvýšit zabezpečení virtuálních počítačů [generace 2](generation-2.md) . Důvěryhodné spuštění chrání před technikami pokročilých a trvalých útoků. Důvěryhodné spuštění se skládá z několika koordinovaných technologií infrastruktury, které je možné povolit nezávisle. Každá technologie poskytuje další vrstvu obrany proti sofistikovaným hrozbám.

> [!IMPORTANT]
> Důvěryhodné spuštění vyžaduje vytvoření nových virtuálních počítačů. U stávajících virtuálních počítačů, které se původně vytvořily bez nich, nemůžete povolit důvěryhodné spuštění.
>
> Důvěryhodné spuštění je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. 
>
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="benefits"></a>Výhody 

- Bezpečně nasaďte virtuální počítače pomocí ověřených spouštěcích zavaděčů, jader operačních systémů a ovladačů.
- Bezpečně chránit klíče, certifikáty a tajné klíče ve virtuálních počítačích.
- Získejte přehledy o integritě celého spouštěcího řetězu a jeho spolehlivost.
- Zajistěte, aby byly úlohy důvěryhodné a ověřitelné.

## <a name="public-preview-limitations"></a>Omezení veřejné verze Preview

**Podpora velikosti**: všechny velikosti virtuálních počítačů [2. generace](generation-2.md) , s výjimkou:

- HBv3 
- Řada Lsv2 
- Řada M 
- Řada Mv2 
- Řada NDv4 
- Řada NVv4

**Podpora operačního systému**:
- RedHat Enterprise Linux 8,3
- SUSE 15 SP2
- Ubuntu 20,04 LTS
- Ubuntu 18.04 LTS
- Windows Server 2019
- Windows Server 2016
- Windows 10 Pro
- Windows 10 Enterprise

**Oblasti**: 
- Středojižní USA
- Severní Evropa

**Ceny**: žádné další poplatky za stávající ceny virtuálních počítačů.

**V této verzi Preview nejsou podporovány následující funkce**:
- Backup
- Azure Site Recovery
- Sdílená galerie obrázků
- Dočasný disk s operačním systémem
- Sdílený disk
- Spravovaná image
- Azure Dedicated Host 

## <a name="secure-boot"></a>Zabezpečené spuštění

V kořenovém adresáři důvěryhodného spuštění je zabezpečené spouštění virtuálního počítače. Tento režim, který je implementován v firmwaru platformy, chrání před instalací sad rootkit a spouštěcích sad založených na malwaru. Zabezpečené spouštění systému funguje, aby bylo zajištěno, že mohou spustit pouze podepsané operační systémy a ovladače. Pro softwarový zásobník na vašem VIRTUÁLNÍm počítači vytvoří kořen vztahu důvěryhodnosti. Pokud je povolené zabezpečené spouštění, musí být všechny součásti spouštění operačního systému (zaváděcí zavaděč, jádro, ovladače jádra) podepsané důvěryhodnými vydavateli. Jak Windows, tak výběr distribuce pro Linux podporují zabezpečené spouštění. Pokud zabezpečené spuštění nedokáže ověřit, jestli je image podepsaná důvěryhodným vydavatelem, virtuální počítač se nebude moct spustit. Další informace najdete v tématu [Zabezpečené spouštění](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-secure-boot).

## <a name="vtpm"></a>vTPM

Důvěryhodné spuštění také zavádí vTPM pro virtuální počítače Azure. Toto je virtualizovaná verze čipu TPM (hardware [Trusted Platform Module](/windows/security/information-protection/tpm/trusted-platform-module-overview)), která je kompatibilní se specifikací TPM 2.0. Slouží jako vyhrazené zabezpečené úložiště pro klíče a měření. Důvěryhodné spuštění poskytuje VIRTUÁLNÍmu počítači svou vlastní vyhrazenou instanci TPM, která běží v zabezpečeném prostředí mimo dosah libovolného virtuálního počítače. VTPM umožňuje [ověření](/windows/security/information-protection/tpm/tpm-fundamentals#measured-boot-with-support-for-attestation) pomocí měření celého spouštěcího ŘETĚZu virtuálního počítače (UEFI, OS, System a Drivers). 

Důvěryhodné spuštění používá vTPM k provádění vzdáleného ověřování pomocí cloudu. Používá se k kontrolám stavu platformy a k rozhodování na základě vztahu důvěryhodnosti. Při kontrole stavu může důvěryhodné spuštění kryptograficky ověřit, že se virtuální počítač spustil správně. Pokud tento proces selže, může to být způsobeno tím, že na VIRTUÁLNÍm počítači běží neoprávněná součást, Azure Security Center bude vydávat výstrahy integrity. Výstrahy obsahují podrobnosti o tom, které součásti se nepodařilo úspěšně projít kontroly integrity.

## <a name="virtualization-based-security"></a>Zabezpečení založené na virtualizaci

[Zabezpečení založené na virtualizaci](/windows-hardware/design/device-experiences/oem-vbs) (VBS) používá hypervisor k vytvoření zabezpečené a izolované oblasti paměti. Systém Windows používá tyto oblasti ke spouštění různých řešení zabezpečení se zvýšenou ochranou před ohrožením zabezpečení a zneužití škodlivých útoků. Důvěryhodné spuštění umožňuje povolit integritu kódu hypervisoru (HYPERVISOREM HVCI) a ochranu Credential Guard v programu Windows Defender.

HYPERVISOREM HVCI je výkonné řešení, které chrání procesy Windows v režimu jádra proti vkládání a spouštění škodlivého nebo neověřeného kódu. Před spuštěním kontroluje ovladače režimu jádra a binární soubory a zabraňuje načtení nepodepsaných souborů do paměti. To zajistí, že takový spustitelný kód nelze změnit, jakmile je povoleno načtení. Další informace o příkazech VBS a HYPERVISOREM HVCI naleznete v tématu [Security Based Security (VBS) and hypervisored Code integrity (hypervisorem hvci)](https://techcommunity.microsoft.com/t5/windows-insider-program/virtualization-based-security-vbs-and-hypervisor-enforced-code/m-p/240571).

Pomocí důvěryhodných spuštění a VBS můžete povolit ochranu Credential Guard v programu Windows Defender. Tato funkce izoluje a chrání tajné kódy, aby k nim měli přístup jenom privilegovaný systémový software. Pomáhá zabránit neoprávněnému přístupu k tajným klíčům a útokům na krádeži přihlašovacích údajů, jako jsou útoky pass-the-hash (PtH). Další informace najdete v tématu [Credential Guard](https://docs.microsoft.com/windows/security/identity-protection/credential-guard/credential-guard).


## <a name="security-center-integration"></a>Integrace Security Center

Důvěryhodné spuštění je integrované s Azure Security Center, aby se zajistilo, že jsou vaše virtuální počítače správně nakonfigurované. Azure Security Center průběžně vyhodnocuje kompatibilní virtuální počítače a vydá relevantní doporučení.

- **Doporučení pro povolení zabezpečeného spouštění** – toto doporučení platí jenom pro virtuální počítače, které podporují důvěryhodné spouštění. Azure Security Center identifikuje virtuální počítače, které můžou povolit zabezpečené spouštění, ale je zakázané. Bude vydávat doporučení s nízkou závažností, aby ji bylo možné povolit.
- **Doporučení pro povolení vTPM** – Pokud je ve vašem virtuálním počítači vTPM povolený, Azure Security Center ho můžou použít k provádění ověření identity hostů a identifikaci pokročilých vzorů hrozeb. Pokud Azure Security Center identifikuje virtuální počítače, které podporují důvěryhodné spouštění a vTPM zakázané, vystaví se doporučení s nízkou závažností, aby ji bylo možné povolit. 
- **Vyhodnocení stavu ověření identity** – Pokud je ve vašem virtuálním počítači povolený vTPM, může rozšíření Azure Security Center vzdáleně ověřit, jestli se váš virtuální počítač spouští v pořádku. To se označuje jako vzdálené ověření identity. Azure Security Center vydá problémy s posouzením, které indikuje stav vzdáleného ověření identity.

## <a name="azure-defender-integration"></a>Integrace se službou Azure Defender

Pokud jsou vaše virtuální počítače správně nastavené pomocí důvěryhodného spuštění, může vám Azure Defender zjistit problémy s dobrým stavem virtuálních počítačů a upozorňovat na ně.

- **Upozornění na selhání ověřování virtuálních počítačů** – Azure Defender bude na vašich virtuálních počítačích pravidelně provádět ověření identity. K tomu dojde také po spuštění virtuálního počítače. Pokud se ověření identity nezdaří, aktivuje se upozornění střední závažnosti.
    Ověření identity virtuálního počítače může selhat z následujících důvodů:
    - Ověřené informace, které zahrnují protokol spuštění, se odchylují od důvěryhodného směrného plánu. To může znamenat, že byly načteny nedůvěryhodné moduly, a operační systém může být ohrožen.
    - Nepovedlo se ověřit nabídku ověření identity, aby vznikla z vTPM ověřeného virtuálního počítače. To může znamenat, že je k dispozici malware a že může zachytit provoz do vTPM.
    - Rozšíření ověření identity na virtuálním počítači neodpovídá. To může znamenat útok DOS malwarem nebo správcem operačního systému.

    > [!NOTE]
    >  Tato výstraha je k dispozici pro virtuální počítače s povoleným vTPM a nainstalované rozšíření pro ověření identity. Aby bylo možné předat ověření identity, musí být zabezpečené spouštění zapnuté. Ověření identity se nezdaří, pokud je zakázané zabezpečené spouštění. Pokud je nutné zakázat zabezpečené spouštění, můžete toto upozornění potlačit, abyste se vyhnuli falešně pozitivním výsledkům.

- **Výstraha pro nedůvěryhodný modul jádra pro Linux** – pro důvěryhodné spuštění se zapnutým zabezpečeným spouštěním je možné, že se virtuální počítač spustí i v případě, že se ověření ovladače jádra nezdařilo a je zakázáno načítání. Pokud k tomu dojde, bude Azure Defender vystavovat upozornění s nízkou závažností. I když neexistuje žádná bezprostřední hrozba, protože nebyl načten nedůvěryhodný ovladač, tyto události by se měly prozkoumat. Zvažte použití těchto zdrojů:
    - Který ovladač jádra selhal? Jsem s tímto ovladačem obeznámen a očekává se, že se načte?
    - Je to přesně ta verze ovladače, kterou očekávám? Jsou binární soubory ovladače nedotčeny? Pokud se jedná o ovladač třetí strany, dodavatel předá testy dodržování předpisů pro operační systém, aby se zaregistroval?


## <a name="faq"></a>Časté otázky

Nejčastější dotazy týkající se důvěryhodného spuštění

### <a name="why-should-i-use-trusted-launch-what-does-trusted-launch-guard-against"></a>Proč mám použít důvěryhodné spuštění? K čemu je důvěryhodné spuštění ochrany?

Důvěryhodné spouštění Guard proti spouštěcím kitsm, správcovským programům a malwaru na úrovni jádra. Tyto sofistikované typy malwaru běží v režimu jádra a zůstávají od uživatelů skryté. Například:
- Rootkit – sady pro firmware: tyto sady přepisují firmware systému BIOS virtuálního počítače, takže se správcovská sada může spustit před operačním systémem. 
- Spouštěcí sady: tyto sady nahrazují spouštěcí program pro operační systém, takže virtuální počítač načte spouštěcí sadu před operačním systémem.
- Správcovské sady jádra: tyto sady nahrazují část jádra operačního systému, aby se mohla správcovská sada spustit automaticky při načtení operačního systému.
- Sady rootkit ovladačů: tyto sady předstírat jako jeden z důvěryhodných ovladačů, které operační systém používá ke komunikaci s komponentami virtuálního počítače.

### <a name="what-are-the-differences-between-secure-boot-and-measured-boot"></a>Jaké jsou rozdíly mezi zabezpečeným spouštěním a změřeným spouštěním?

V řetězci zabezpečeného spouštění zkontroluje každý krok spouštěcího procesu kryptografický podpis následujících kroků. Například BIOS zkontroluje podpis zavaděče a zavaděč zkontroluje podpisy všech objektů jádra, které načte, a tak dále. Pokud dojde k ohrožení bezpečnosti některého z objektů, signatura se neshoduje a virtuální počítač se nespustí. Další informace najdete v tématu [Zabezpečené spouštění](/windows-hardware/design/device-experiences/oem-secure-boot). Měřené spouštění neukončí proces spouštění, měří nebo vypočítá hodnotu hash dalších objektů v řetězu a ukládá hodnoty hash do registrů konfigurace platforem (Registry) na vTPM. Ke sledování integrity spouštění se používají měřené spouštěcí záznamy.

### <a name="what-happens-when-an-integrity-fault-is-detected"></a>Co se stane, když se zjistí chyba integrity?

Pro pokročilé hrozby se monitoruje důvěryhodné spuštění virtuálních počítačů Azure. Pokud jsou tyto hrozby zjištěny, aktivuje se výstraha. Výstrahy jsou k dispozici pouze na [úrovni Standard](/azure/security-center/security-center-pricing) Azure Security Center.
Azure Security Center pravidelně provádí ověření identity. Pokud se ověření identity nezdaří, aktivuje se upozornění střední závažnosti. Ověření důvěryhodného spuštění se může zdařit z následujících důvodů: 
- Ověřené informace, které obsahují protokol TCB (Trusted Computing Base), se odchylují od důvěryhodného směrného plánu (například při povolení zabezpečeného spouštění). To může znamenat, že byly načteny nedůvěryhodné moduly a operační systém může být ohrožen.
- Nepovedlo se ověřit nabídku ověření identity, aby vznikla z vTPM ověřeného virtuálního počítače. To může znamenat, že je k dispozici malware a že může zachytit provoz do čipu TPM. 
- Rozšíření ověření identity na virtuálním počítači neodpovídá. To může znamenat útok DOS malwarem nebo správcem operačního systému.

  
### <a name="how-does-trusted-launch-compared-to-hyper-v-shielded-vm"></a>Jak se důvěryhodné spuštění porovnává s chráněným virtuálním počítačem Hyper-V?
Chráněný virtuální počítač Hyper-V je momentálně dostupný jenom pro Hyper-V. [Virtuální počítač s ochranou Hyper-V](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms) se obvykle nasazuje společně s chráněnými prostředky infrastruktury. Chráněné prostředky infrastruktury se skládají ze služby strážce hostitele (HGS), jednoho nebo více chráněných hostitelů a sady chráněných virtuálních počítačů. Virtuální počítače s ochranou Hyper-V jsou určené pro použití v prostředcích infrastruktury, ve kterých se musí data a stav virtuálního počítače chránit od správců prostředků infrastruktury i z nedůvěryhodného softwaru, který by mohl běžet na hostitelích Hyper-V. Důvěryhodné spuštění na druhé straně se dá nasadit jako samostatný virtuální počítač nebo sada škálování virtuálních počítačů v Azure bez dalšího nasazení a správy služby HGS. Všechny funkce pro důvěryhodné spuštění lze povolit jednoduchou změnou v kódu nasazení nebo zaškrtnutím políčka na Azure Portal.  

### <a name="how-can-i-convert-existing-vms-to-trusted-launch"></a>Jak můžu převést stávající virtuální počítače na důvěryhodné spuštění?
U virtuálního počítače 2. generace je cesta migrace pro převod na důvěryhodné spuštění zaměřená na obecnou dostupnost (GA).

## <a name="next-steps"></a>Další kroky

[Pomocí portálu nasaďte důvěryhodný spouštěcí virtuální počítač](trusted-launch-portal.md).
