---
title: Matice podpory pro zotavení po havárii virtuálního počítače Azure pomocí Azure Site Recovery
description: Shrnuje podporu zotavení po havárii virtuálních počítačů Azure do sekundární oblasti pomocí Azure Site Recovery.
ms.topic: article
ms.date: 11/29/2020
ms.author: raynew
ms.openlocfilehash: 33b0a5a648acee83b55ed97a1359a9f3d60c0052
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216849"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Matice podpory pro zotavení po havárii virtuálních počítačů Azure mezi oblastmi Azure

Tento článek shrnuje podporu a předpoklady pro zotavení po havárii virtuálních počítačů Azure z jedné oblasti Azure do jiné pomocí služby [Azure Site Recovery](site-recovery-overview.md) .


## <a name="deployment-method-support"></a>Podpora metody nasazení

**Nasazení** |  **Podpora**
--- | ---
**Azure Portal** | Podporuje se.
**PowerShell** | Podporuje se. [Další informace](azure-to-azure-powershell.md)
**REST API** | Podporuje se.
**Rozhraní příkazového řádku** | Aktuálně se nepodporuje.


## <a name="resource-support"></a>Podpora prostředků

**Akce prostředku** | **Podrobnosti**
--- | ---
**Přesun trezorů mezi skupinami prostředků** | Nepodporováno
**Přesun prostředků COMPUTE/Storage/sítě mezi skupinami prostředků** | Nepodporováno<br/><br/> Pokud po replikaci virtuálního počítače přesunete virtuální počítač nebo přidružené součásti, jako je úložiště nebo síť, musíte pro tento virtuální počítač Zakázat a znovu povolit replikaci.
**Replikace virtuálních počítačů Azure z jednoho předplatného do jiného pro zotavení po havárii** | Podporováno v rámci stejného Azure Active Directory tenanta.
**Migrace virtuálních počítačů napříč oblastmi v rámci podporovaných geografických clusterů (v rámci a napříč předplatnými)** | Podporováno v rámci stejného Azure Active Directory tenanta.
**Migrace virtuálních počítačů v rámci stejné oblasti** | Nepodporováno
**Vyhrazení hostitelé Azure** | Nepodporováno

## <a name="region-support"></a>Podpora oblastí

Virtuální počítače můžete replikovat a obnovovat mezi dvěma oblastmi v rámci stejného geografického clusteru. Geografické clustery jsou definovány pro udržení latence a suverenity dat.


**Geografický cluster** | **Oblasti Azure**
-- | --
Používaný | Kanada – východ, Kanada – střed, Střed USA – jih, Středozápadní USA, Východní USA, Východní USA 2, Západní USA, Západní USA 2, Střed USA, Střed USA – sever
Evropa | Velká Británie – západ, Velká Británie – jih, Severní Evropa, Západní Evropa, Jižní Afrika – západ, Jižní Afrika – sever, Norsko – východ, Francie – střed, Švýcarsko – sever, Německo – středozápad
Asie | Jižní Indie, Střed Indie, Západní Indie, jihovýchodní Asie, Východní Asie, Japonsko – východ, Japonsko – západ, Korea – střed, Jižní Korea
Austrálie    | Austrálie – východ, Austrálie – jihovýchod, Austrálie – střed, Austrálie – střed 2
Azure Government    | US GOV) – Virginia, US GOV Iowa, US GOV Arizona, US GOV Texas, US DOD – východ, US DOD – střed
Německo    | Německo – střed, Německo – severovýchod
Čína | Čína – východ, Čína – sever, Čína North2, Čína – východ 2
Omezené oblasti rezervované pro zotavení po havárii v zemi |Švýcarsko – západ vyhrazena pro Švýcarsko – sever, Francie – jih vyhrazená pro Francii – střed, středová Spojené arabské EMIRÁTYa pro Spojené arabské emiráty sever, Norsko – západ pro zákazníky v Norsku – východ

>[!NOTE]
>
> - Pro oblast **Brazílie – jih** můžete replikovat a přenášet služby při selhání do těchto oblastí: Střed USA – jih, Středozápadní USA, Východní USA, Východní USA 2, Západní USA, Západní USA 2 a střed USA – sever.
> - Brazílie – jih se dá použít jenom jako zdrojová oblast, ze které se můžou virtuální počítače replikovat pomocí Site Recovery. Nemůže fungovat jako cílová oblast. Důvodem je příčiny potíží s latencí z důvodu zeměpisné vzdálenosti. Počítejte s tím, že Pokud převezmete služby při selhání z Brazílie – jih jako zdrojové oblasti cíle, je podporována navrácení služeb po obnovení do Brazílie – jih od cílové oblasti.
> - Můžete pracovat v oblastech, pro které máte odpovídající přístup.
> - Pokud se v oblasti, ve které chcete vytvořit trezor, nezobrazí, ujistěte se, že vaše předplatné má přístup k vytváření prostředků v této oblasti.
> - Pokud při povolování replikace nevidíte oblast v rámci geografického clusteru, ujistěte se, že vaše předplatné má oprávnění k vytváření virtuálních počítačů v této oblasti.



## <a name="cache-storage"></a>Úložiště mezipaměti

Tato tabulka shrnuje podporu účtu úložiště mezipaměti používaného Site Recovery během replikace.

**Nastavení** | **Podpora** | **Podrobnosti**
--- | --- | ---
Účty úložiště pro obecné účely v2 (horká a studená vrstva) | Podporováno | Použití GPv2 se nedoporučuje, protože náklady na transakce pro v2 jsou podstatně vyšší než V1 účty úložiště.
Premium Storage | Nepodporováno | Účty úložiště úrovně Standard se používají pro úložiště mezipaměti, které vám pomůžou optimalizovat náklady.
Azure Storage brány firewall pro virtuální sítě  | Podporováno | Pokud používáte účet úložiště mezipaměti s povolenou bránou firewall nebo cílový účet úložiště, zajistěte, abyste [povolili důvěryhodné služby Microsoftu](../storage/common/storage-network-security.md#exceptions).<br></br>Také se ujistěte, že máte povolený přístup k alespoň jedné podsíti zdrojové virtuální sítě.

Následující tabulka uvádí omezení počtu disků, které se můžou replikovat do jednoho účtu úložiště.

**Typ účtu úložiště**    |    **Četnosti = 4 MB/s na disk**    |    **Změny = 8 MB/s na disk**
---    |    ---    |    ---
Účet úložiště v1    |    disky 300    |    disky 150
Účet úložiště v2    |    disky 750    |    disky 375

Jak se zvýší průměrná doba na discích, může se snížit počet disků, které může účet úložiště podporovat. Výše uvedenou tabulku lze použít jako vodítko pro rozhodování o počtu účtů úložiště, které je třeba zřídit.

Počítejte s tím, že výše uvedená omezení jsou specifická pro Azure do Azure a zóna pro scénáře zotavení po havárii. 

## <a name="replicated-machine-operating-systems"></a>Operační systémy replikovaných počítačů

Site Recovery podporuje replikaci virtuálních počítačů Azure s operačními systémy uvedenými v této části. Všimněte si, že pokud už je počítač, který se replikuje, na jiný hlavní jádro upgradovaný (nebo se downgradem), musíte po upgradu zakázat replikaci a znovu povolit replikaci.

### <a name="windows"></a>Windows


**Operační systém** | **Podrobnosti**
--- | ---
Windows Server 2019 | Podporováno pro jádro serveru, server s desktopovým prostředím.
Windows Server 2016  | Podporován jádro serveru, server s desktopovým prostředím.
Windows Server 2012 R2 | Podporuje se.
Windows Server 2012 | Podporuje se.
Windows Server 2008 R2 s aktualizací SP1/SP2 | Podporuje se.<br/><br/> Z verze [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) rozšíření služby mobility pro virtuální počítače Azure je třeba nainstalovat aktualizaci Windows [Servicing Stack Agent (cestou nadřazené)](https://support.microsoft.com/help/4490628) a [SHA-2](https://support.microsoft.com/help/4474419) na počítače se systémem Windows Server 2008 R2 SP1/SP2.  SHA-1 se od září 2019 nepodporuje a pokud podepisování kódu SHA-2 není povolené, rozšíření agenta se nenainstaluje nebo upgraduje podle očekávání. Přečtěte si další informace o [upgradu a požadavcích SHA-2](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Podporuje se.
Windows 8.1 (x64) | Podporuje se.
Windows 8 (x64) | Podporuje se.
Windows 7 (x64) s aktualizací SP1 a vyšší | Z verze [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) rozšíření služby mobility pro virtuální počítače Azure je třeba nainstalovat aktualizaci Windows [Servicing Stack Agent (cestou nadřazené)](https://support.microsoft.com/help/4490628) a [SHA-2](https://support.microsoft.com/help/4474419) na počítače se systémem Windows 7 s aktualizací SP1.  SHA-1 se od září 2019 nepodporuje a pokud podepisování kódu SHA-2 není povolené, rozšíření agenta se nenainstaluje nebo upgraduje podle očekávání. Přečtěte si další informace o [upgradu a požadavcích SHA-2](https://aka.ms/SHA-2KB).



#### <a name="linux"></a>Linux

**Operační systém** | **Podrobnosti**
--- | ---
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6,[7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7,8, 7,9](https://support.microsoft.com/help/4564347/), [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), [8,1, 8,2](https://support.microsoft.com/help/4570609/), [8,3](https://support.microsoft.com/help/4597409/) [](https://support.microsoft.com/help/4578241/)
CentOS | 6,5, 6,6, 6,7, 6,8, 6,9, 6,10 </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7, [7,8](https://support.microsoft.com/help/4564347/), [7,9 verze pre-GA](https://support.microsoft.com/help/4578241/), verze 7,9 s opravou ga je podporovaná z 9,37 </br> 8,0, 8,1, [8,2](https://support.microsoft.com/en-us/help/4570609), [8,3](https://support.microsoft.com/help/4597409/)
Server Ubuntu 14,04 LTS | Zahrnuje podporu pro všechny 14,04. verze *x* ; [Podporované verze jádra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines); 
Server Ubuntu 16,04 LTS | Zahrnuje podporu pro všechny 16,04. verze *x* ; [Podporovaná verze jádra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Ubuntu servery s použitím ověřování a přihlášení pomocí hesla a balíčku Cloud-init ke konfiguraci cloudových virtuálních počítačů můžou mít při převzetí služeb při selhání zakázané přihlášení (v závislosti na konfiguraci cloudinit). Přihlášení založené na heslech na virtuálním počítači můžete znovu povolit tím, že resetujete heslo z nabídky nastavení > řešení potíží s >em (u virtuálního počítače, u kterého došlo k převzetí služeb při selhání v Azure Portal.
Server Ubuntu 18,04 LTS | Zahrnuje podporu pro všechny 18,04. verze *x* ; [Podporovaná verze jádra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Ubuntu servery s použitím ověřování a přihlášení pomocí hesla a balíčku Cloud-init ke konfiguraci cloudových virtuálních počítačů můžou mít při převzetí služeb při selhání zakázané přihlášení (v závislosti na konfiguraci cloudinit). Přihlášení založené na heslech na virtuálním počítači můžete znovu povolit tím, že resetujete heslo z nabídky nastavení > řešení potíží s >em (u virtuálního počítače, u kterého došlo k převzetí služeb při selhání v Azure Portal.
Server Ubuntu 20,04 LTS | Zahrnuje podporu pro všechny 20,04. verze *x* ; [Podporovaná verze jádra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | Zahrnuje podporu pro všechny 7. verze *x* – [podporované verze jádra](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | Zahrnuje podporu pro všechny 8. verze *x* – [podporované verze jádra](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 9 | Zahrnuje podporu 9,1 až 9,13. Debian 9,0 není podporován. [Podporované verze jádra](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 10 | [Podporované verze jádra](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4, SP5  [(podporované verze jádra)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15, SP1, SP2[(podporované verze jádra)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | 3<br/><br/> Upgrade replikačních počítačů z aktualizace SP3 na verzi SP4 není podporován. Pokud byl replikovaný počítač upgradován, je nutné zakázat replikaci a po upgradu znovu povolit replikaci.
SUSE Linux Enterprise Server 11 | OPRAVY
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), [7,8, 7,9](https://support.microsoft.com/help/4573888/) [, 8,0](https://support.microsoft.com/help/4597409) [, 8,1](https://support.microsoft.com/help/4573888/) [(se](https://support.microsoft.com/help/4573888/) spuštěným systémem Red Hat & kompatibilního s UEK3, UEK4, UEK5)<br/><br/>8,1 (běžící na všech jádrech UEK a jádro RedHat <= 3.10.0-1062. * jsou podporovány v [9,35](https://support.microsoft.com/help/4573888/). Podpora pro zbytek jader RedHat je k dispozici v [9,36](https://support.microsoft.com/help/4578241/).)

> [!NOTE]
> Pro verze Linux Azure Site Recovery nepodporuje přizpůsobené image operačních systémů. Podporují se jenom ty jádra, které jsou součástí distribuční verze nebo aktualizace dílčí verze distribuce.

* * Poznámka: Pokud chcete, aby se nejnovější jádra pro Linux podporovala během 15 dnů od vydání, Azure Site Recovery na nejnovější verzi agenta mobility opravenou opravou hotfix. Tato oprava je zavedená v systému mezi dvěma verzemi hlavní verze. Pokud chcete aktualizovat na nejnovější verzi agenta mobility (včetně opravy hotfix hotfix), postupujte podle kroků uvedených v [tomto článku](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure). Tato oprava se v tuto chvíli zavedla pro agenty mobility používané ve scénáři zotavení po havárii v Azure.

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Podporované verze jádra Ubuntu pro virtuální počítače Azure

**Vydaná verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
14,04 LTS | [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 3.13.0-24 – obecné pro 3.13.0-170 – obecné,<br/>3.16.0-25 – obecný pro 3.16.0-77-Generic,<br/>3.19.0-18 – obecný pro 3.19.0 – 80 – obecné,<br/>4.2.0-18 – obecný pro 4.2.0-42 – obecné,<br/>4.4.0-21 – obecné až 4.4.0 – 148 – obecné,<br/>4.15.0-1023 – Azure do 4.15.0 – 1045 – Azure |
|||
16,04 LTS | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.4.0-21 – obecné k 4.4.0 – 201 – obecné,<br/>4.8.0-34-obecné až 4.8.0 – 58 – obecné,<br/>4.10.0-14 – obecný pro 4.10.0-42 – obecné,<br/>4.11.0-13 – Obecné pro 4.11.0-14 – obecný,<br/>4.13.0-16 – obecný pro 4.13.0-45 – obecný,<br/>4.15.0-13 – Obecné pro 4.15.0 – 133 – obecné<br/>4.11.0-1009 – Azure do 4.11.0-1016-Azure<br/>4.13.0 – 1005 – Azure do 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure do 4.15.0-1106 – Azure <br/> 4.4.0-203-Generic, 4.15.0-136-Generic, 4.15.0-1108-Azure až 9,41 oprava hotfix * *|
16,04 LTS | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.4.0-21 – obecný pro 4.4.0 – 197 – obecné,<br/>4.8.0-34-obecné až 4.8.0 – 58 – obecné,<br/>4.10.0-14 – obecný pro 4.10.0-42 – obecné,<br/>4.11.0-13 – Obecné pro 4.11.0-14 – obecný,<br/>4.13.0-16 – obecný pro 4.13.0-45 – obecný,<br/>4.15.0-13 – Obecné k 4.15.0 – 128 – obecné<br/>4.11.0-1009 – Azure do 4.11.0-1016-Azure<br/>4.13.0 – 1005 – Azure do 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure do 4.15.0-1102 – Azure </br> 4.15.0-132-Generic, 4.4.0-200-Generic, 4.15.0-1106-Azure, 4.15.0-133-Generic, 4.4.0-201-Generic-9,40 Hot Fix patch * *|
16,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.4.0-21 – obecný pro 4.4.0-194 – obecné,<br/>4.8.0-34-obecné až 4.8.0 – 58 – obecné,<br/>4.10.0-14 – obecný pro 4.10.0-42 – obecné,<br/>4.11.0-13 – Obecné pro 4.11.0-14 – obecný,<br/>4.13.0-16 – obecný pro 4.13.0-45 – obecný,<br/>4.15.0-13 – Obecné k 4.15.0 – 123 – obecné<br/>4.11.0-1009 – Azure do 4.11.0-1016-Azure<br/>4.13.0 – 1005 – Azure do 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure do 4.15.0-1098 – Azure </br> 4.4.0-197-Generic, 4.15.0-126-Generic, 4.15.0-128-Generic, 4.15.0-1100-Azure, 4.15.0-1102-Azure až 9,39 Hot Fix patch * *|
16,04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.4.0-21 – obecný pro 4.4.0 – 190 – obecné,<br/>4.8.0-34-obecné až 4.8.0 – 58 – obecné,<br/>4.10.0-14 – obecný pro 4.10.0-42 – obecné,<br/>4.11.0-13 – Obecné pro 4.11.0-14 – obecný,<br/>4.13.0-16 – obecný pro 4.13.0-45 – obecný,<br/>4.15.0-13 – obecný pro 4.15.0 – 118 – obecné<br/>4.11.0-1009 – Azure do 4.11.0-1016-Azure<br/>4.13.0 – 1005 – Azure do 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure do 4.15.0-1096 – Azure </br> 4.4.0-193-Generic, 4.15.0-120-Generic, 4.15.0-122-Generic, 4.15.0-1098-Azure až 9,38 oprava hotfix * *|
16,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.4.0-21 – obecné k 4.4.0 – 189 – obecné,<br/>4.8.0-34-obecné až 4.8.0 – 58 – obecné,<br/>4.10.0-14 – obecný pro 4.10.0-42 – obecné,<br/>4.11.0-13 – Obecné pro 4.11.0-14 – obecný,<br/>4.13.0-16 – obecný pro 4.13.0-45 – obecný,<br/>4.15.0-13 – Obecné pro 4.15.0-115 – obecné<br/>4.11.0-1009 – Azure do 4.11.0-1016-Azure<br/>4.13.0 – 1005 – Azure do 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure do 4.15.0-1093 – Azure </br> 4.4.0-190-Generic, 4.15.0-117-Generic, 4.15.0-118-Generic, 4.15.0-1095-Azure, 4.15.0-1096-Azure až 9,37 oprava hotfix patch * *|
|||
18,04 LTS | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.15.0-20 – obecný pro 4.15.0-135 – obecné </br> 4.18.0-13 – Obecné pro 4.18.0-25 – obecné </br> 5.0.0-15 – obecný pro 5.0.0-65 – obecné </br> 5.3.0-19 – obecný pro 5.3.0 – 70 – obecný </br> 5.4.0-37 – obecné až 5.4.0 – 59 – obecné</br> 5.4.0-60 – obecný pro 5.4.0-65 – obecné </br> 4.15.0-1009 – Azure do 4.15.0-1106 – Azure </br> 4.18.0-1006 – Azure až 4.18.0 – 1025 – Azure </br> 5.0.0-1012 – Azure do 5.0.0-1036 – Azure </br> 5.3.0-1007 – Azure to 5.3.0-1035-Azure </br> 5.4.0-1020 – Azure do 5.4.0-1039 – Azure </br> 4.15.0-136-Generic, 5.4.0-66-Generic, 4.15.0-1108-Azure, 4.15.0-136 – obecné až 9,41 oprava hotfix patch * *|
18,04 LTS | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.15.0-20 – obecný k 4.15.0 – 129 – obecné </br> 4.18.0-13 – Obecné pro 4.18.0-25 – obecné </br> 5.0.0-15 – obecný pro 5.0.0 – 63 – obecný </br> 5.3.0-19 – Obecné k 5.3.0 – 69 – obecné </br> 5.4.0-37 – obecné až 5.4.0 – 59 – obecné</br> 4.15.0-1009 – Azure do 4.15.0-1103 – Azure </br> 4.18.0-1006 – Azure až 4.18.0 – 1025 – Azure </br> 5.0.0-1012 – Azure do 5.0.0-1036 – Azure </br> 5.3.0-1007 – Azure to 5.3.0-1035-Azure </br> 5.4.0-1020 – Azure to 5.4.0-1035-Azure </br> 4.15.0-1104-Azure, 4.15.0-130-Generic, 4.15.0-132-Generic, 5.4.0-1036-Azure, 5.4.0-60-Generic, 5.4.0-62-Generic, 4.15.0-1106-Azure, 4.15.0-64-Generic, 4.15.0-135-Generic, 5.4.0-1039-Azure, 5.4.0-64-Generic, 5.4.0-65-Generic-9,40 Hot Fix patch * *|
18,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.15.0-20 – Obecné k 4.15.0 – 123 – obecné </br> 4.18.0-13 – Obecné pro 4.18.0-25 – obecné </br> 5.0.0-15 – obecný pro 5.0.0 – 63 – obecný </br> 5.3.0-19 – Obecné k 5.3.0 – 69 – obecné </br> 5.4.0-37 – obecný pro 5.4.0-53 – obecné</br> 4.15.0-1009 – Azure do 4.15.0 – 1099 – Azure </br> 4.18.0-1006 – Azure až 4.18.0 – 1025 – Azure </br> 5.0.0-1012 – Azure do 5.0.0-1036 – Azure </br> 5.3.0-1007 – Azure to 5.3.0-1035-Azure </br> 5.4.0-1020 – Azure do 5.4.0-1031 – Azure </br> 4.15.0-124 – obecné, 5.4.0-54-Generic, 5.4.0-1032-Azure, 5.4.0-56-Generic, 4.15.0-1100-Azure, 4.15.0-126-Generic, 4.15.0-128-Generic, 5.4.0-58-Generic, 4.15.0-1102-Azure, 5.4.0-1034-Azure 9,39 hotfix Hot Fix * *|
18,04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.15.0-20 – obecný pro 4.15.0 – 118 – obecné </br> 4.18.0-13 – Obecné pro 4.18.0-25 – obecné </br> 5.0.0-15 – obecný pro 5.0.0 – 61 – obecné </br> 5.3.0-19 – obecný pro 5.3.0-67 – obecné </br> 5.4.0-37 – obecný pro 5.4.0-48 – obecné</br> 4.15.0-1009 – Azure do 4.15.0-1096 – Azure </br> 4.18.0-1006 – Azure až 4.18.0 – 1025 – Azure </br> 5.0.0-1012 – Azure do 5.0.0-1036 – Azure </br> 5.3.0-1007 – Azure to 5.3.0-1035-Azure </br> 5.4.0-1020 – Azure do 5.4.0-1026 – Azure </br> 4.15.0-121-Generic, 4.15.0-122-Generic, 5.0.0-62-Generic, 5.3.0-68-Generic, 5.4.0-51-Generic, 5.4.0-52-Generic, 4.15.0-1099-Azure, 5.4.0-1031-Azure až 9,38 Hot Fix patch * *|
18,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.15.0-20 – obecný pro 4.15.0-115 – obecné </br> 4.18.0-13 – Obecné pro 4.18.0-25 – obecné </br> 5.0.0-15 – obecný pro 5.0.0 – 60 – obecné </br> 5.3.0-19 – Obecné k 5.3.0 – 66 – obecné </br> 5.4.0-37 – obecné až 5.4.0-45 – obecné</br> 4.15.0-1009 – Azure do 4.15.0-1093 – Azure </br> 4.18.0-1006 – Azure až 4.18.0 – 1025 – Azure </br> 5.0.0-1012 – Azure do 5.0.0-1036 – Azure </br> 5.3.0-1007 – Azure to 5.3.0-1035-Azure </br> 5.4.0-1020 – Azure do 5.4.0-1023-Azure</br> 4.15.0-117-Generic, 4.15.0-118-Generic, 5.0.0-61-Generic, 5.3.0-67-Generic, 5.4.0-47-Generic, 5.4.0-48-Generic, 4.15.0-1095-Azure, 4.15.0-1096-Azure, 5.4.0-1025-Azure, 5.4.0-1026-Azure až 9,37 oprava hotfix * *|
|||
20,04 LTS |[9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 5.4.0-26 – obecný pro 5.4.0-65 </br> -Generic 5.4.0-1010-Azure to 5.4.0-1039-Azure </br> 5.8.0-29 – obecný pro 5.8.0-43 – Obecné </br> 5.4.0-66-Generic, 5.8.0-44-Generic, 5.4.0-1040-Azure až 9,41 oprava hotfix * *|
20,04 LTS |[9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 5.4.0-26 – obecný pro 5.4.0-59 </br> -Generic 5.4.0-1010-Azure to 5.4.0-1035-Azure </br> 5.8.0-29 – Obecné k 5.8.0 – 34 – obecné </br> 5.4.0-1036-Azure, 5.4.0-60-Generic, 5.4.0-62-Generic, 5.8.0-36-Generic, 5.8.0-38-Generic, 5.4.0-1039-Azure, 5.4.0-64-Generic, 5.4.0-65-Generic, 5.8.0-40-Generic, 5.8.0-41-Generic-9,40 Hot Fix patch * *|
20,04 LTS |[9,39](https://support.microsoft.com/help/4597409/) | 5.4.0-26 – obecný pro 5.4.0-53 </br> – Generic 5.4.0-1010-Azure to 5.4.0-1031-Azure </br> 5.4.0-54-Generic, 5.8.0-29-Generic, 5.4.0-1032-Azure, 5.4.0-56-Generic, 5.8.0-31-Generic, 5.8.0-33-Generic, 5.4.0-58-Generic, 5.4.0-1034-Azure až 9,39 oprava hotfix * *
20,04 LTS |[9,39](https://support.microsoft.com/help/4597409/) | 5.4.0-26 – obecný pro 5.4.0-53 </br> – Generic 5.4.0-1010-Azure to 5.4.0-1031-Azure </br> 5.4.0-54-Generic, 5.8.0-29-Generic, 5.4.0-1032-Azure, 5.4.0-56-Generic, 5.8.0-31-Generic, 5.8.0-33-Generic, 5.4.0-58-Generic, 5.4.0-1034-Azure až 9,39 oprava hotfix * *
20,04 LTS |[9.38](https://support.microsoft.com/help/4590304/) | 5.4.0-26 – obecný pro 5.4.0-48 </br> -Generic 5.4.0-1010-Azure to 5.4.0-1026-Azure </br> 5.4.0-51-Generic, 5.4.0-52-Generic, 5.8.0-23-Generic, 5.8.0-25-Generic, 5.4.0-1031-Azure až 9,38 oprava hotfix * *
20,04 LTS |[9,37](https://support.microsoft.com/help/4582666/) | 5.4.0-26 – obecný pro 5.4.0-45 </br> -Generic 5.4.0-1010-Azure to 5.4.0-1023-Azure </br> 5.4.0-47-Generic, 5.4.0-48-Generic, 5.4.0-1025-Azure, 5.4.0-1026-Azure až 9,37 oprava hotfix * *

* * Poznámka: Pokud chcete, aby se nejnovější jádra pro Linux podporovala během 15 dnů od vydání, Azure Site Recovery na nejnovější verzi agenta mobility opravenou opravou hotfix. Tato oprava je zavedená v systému mezi dvěma verzemi hlavní verze. Pokud chcete aktualizovat na nejnovější verzi agenta mobility (včetně opravy hotfix hotfix), postupujte podle kroků uvedených v [tomto článku](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure). Tato oprava se v tuto chvíli zavedla pro agenty mobility používané ve scénáři zotavení po havárii v Azure.

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Podporované verze jádra Debian pro virtuální počítače Azure

**Vydaná verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
Debian 7 | [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) , [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 3.2.0-4-amd64 až 3.2.0-6-AMD64, 3.16.0 -0. BPO. 4 – amd64 |
|||
Debian 8 | [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 3.16.0-4-amd64 až 3.16.0-11-AMD64, 4.9.0 -0. BPO. 4-amd64 na 4.9.0 -0. BPO. 11 – amd64 |
|||
Debian 9,1 | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.9.0-1-amd64 až 4.9.0-14-amd64 </br> 4.19.0 -0. BPO. 1-AMD64 pro 4.19.0 -0. BPO. 14-amd64 </br> 4.19.0 -0. BPO. 1-Cloud-amd64 až 4.19.0 -0. BPO. 14-Cloud-amd64
Debian 9,1 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.9.0-1-amd64 až 4.9.0-14-amd64 </br> 4.19.0 -0. BPO. 1-AMD64 pro 4.19.0 -0. BPO. 13 – amd64 </br> 4.19.0 -0. BPO. 1-Cloud-amd64 až 4.19.0 -0. BPO. 13-Cloud-amd64 
Debian 9,1 | [9,39](https://support.microsoft.com/help/4597409/) | 4.9.0-1-amd64 až 4.9.0-14-amd64 </br> 4.19.0 -0. BPO. 1-AMD64 pro 4.19.0 -0. BPO. 12-amd64 </br> 4.19.0 -0. BPO. 1-Cloud-amd64 až 4.19.0 -0. BPO. 12-Cloud-amd64 </br> 4.19.0 -0. BPO. 13-AMD64, 4.19.0 -0. BPO. 13-Cloud-amd64 až 9,39 Hot Fix patch * *</br> 
Debian 9,1 | [9.38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-amd64 až 4.9.0-13-amd64 </br> 4.19.0 -0. BPO. 1-AMD64 pro 4.19.0 -0. BPO. 11 – amd64 </br> 4.19.0 -0. BPO. 1-Cloud-amd64 až 4.19.0 -0. BPO. 11 – Cloud – amd64 </br> 4.9.0-14-AMD64, 4.19.0 -0. BPO. 12-AMD64, 4.19.0 -0. BPO. 12-Cloud-amd64 až 9,38 oprava hotfix * *
Debian 9,1 | [9,37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-amd64 až 4.9.0-13-AMD64, 4.19.0 -0. BPO. 6-amd64 až 4.19.0 -0. BPO. 10-AMD64, 4.19.0 -0. BPO. 6-Cloud-amd64 na 4.19.0 -0. BPO. 10-Cloud-amd64
|||
Debian 10 | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.19.0-5-amd64 až 4.19.0-14-amd64 </br> 4.19.0-6-Cloud-amd64 až 4.19.0-14-Cloud-amd64 </br> 5.8.0 -0. BPO. 2 – amd64 </br> 5.8.0 -0. BPO. 2 – Cloud – amd64
Debian 10 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.19.0-5-amd64 až 4.19.0-13-amd64 </br> 4.19.0-6-Cloud-amd64 až 4.19.0-13-Cloud-amd64 </br> 5.8.0 -0. BPO. 2 – amd64 </br> 5.8.0 -0. BPO. 2 – Cloud – amd64

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Podporované verze jádra SUSE Linux Enterprise Server 12 pro virtuální počítače Azure

**Vydaná verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | Podporují se všechny [zásoby SUSE 12 SP1, SP2, SP3, SP4 a SP5](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.4.138-4.7 – Azure na 4.4.180 – 4.31 – Azure,</br>4.12.14-6.3 – Azure na 4.12.14 – 6.43 – Azure </br> 4.12.14-16,7 – Azure do 4.12.14-16.44-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | Podporují se všechny [zásoby SUSE 12 SP1, SP2, SP3, SP4 a SP5](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.4.138-4.7 – Azure na 4.4.180 – 4.31 – Azure,</br>4.12.14-6.3 – Azure na 4.12.14 – 6.43 – Azure </br> 4.12.14-16,7 – Azure do 4.12.14-16.38-Azure </br> 4.12.14-16.41 – Azure až 9,40 oprava hotfix * *|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,39](https://support.microsoft.com/help/4597409/) | Podporují se všechny [zásoby SUSE 12 SP1, SP2, SP3, SP4 a SP5](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.4.138-4.7 – Azure na 4.4.180 – 4.31 – Azure,</br>4.12.14-6.3 – Azure na 4.12.14 – 6.43 – Azure </br> 4.12.14-16,7 – Azure do 4.12.14-16.34-Azure </br> 4.12.14-16.38 – Azure až 9,39 oprava hotfix * *|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.38](https://support.microsoft.com/help/4590304/) | Podporují se všechny [zásoby SUSE 12 SP1, SP2, SP3, SP4 a SP5](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.4.138-4.7 – Azure na 4.4.180 – 4.31 – Azure,</br>4.12.14-6.3 – Azure na 4.12.14 – 6.43 – Azure </br> 4.12.14-16,7 – Azure do 4.12.14-16.28-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,37](https://support.microsoft.com/help/4582666/) | Podporují se všechny [zásoby SUSE 12 SP1, SP2, SP3, SP4 a SP5](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.4.138-4.7 – Azure na 4.4.180 – 4.31 – Azure,</br>4.12.14-6.3 – Azure na 4.12.14 – 6.43 – Azure </br> 4.12.14-16,7 – Azure do 4.12.14-16.22-Azure </br> 4.12.14-16.25-Azure, 4.12.14-16.28-Azure až 9,37 oprava hotfix * *|

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Podporované SUSE Linux Enterprise Server 15 verzí jádra pro virtuální počítače Azure

**Vydaná verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)  | Ve výchozím nastavení se podporují všechny [jádra SUSE 15, SP1 a SP2](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.12.14-5.5 – Azure na 4.12.14 – 5.47 – Azure </br></br> 4.12.14-8,5 – Azure na 4.12.14 – 8.55 – Azure </br> 5.3.18-16 – Azure </br> 5.3.18-18.5 – Azure do 5.3.18-18.35 – Azure
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | Ve výchozím nastavení se podporují všechny [jádra SUSE 15, SP1 a SP2](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.12.14-5.5 – Azure na 4.12.14 – 5.47 – Azure </br></br> 4.12.14-8,5 – Azure na 4.12.14 – 8.58 – Azure </br> 5.3.18-16 – Azure </br> 5.3.18-18.5 – Azure do 5.3.18-18.29 – Azure </br> 5.3.18-18.32-Azure, 4.12.14-8.58-Azure až 9,40 oprava hotfix * *
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,39](https://support.microsoft.com/help/4597409/)  | Ve výchozím nastavení se podporují všechny [jádra SUSE 15, SP1 a SP2](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.12.14-5.5 – Azure na 4.12.14 – 5.47 – Azure </br></br> 4.12.14-8,5 – Azure na 4.12.14 – 8.47 – Azure </br> 5.3.18-16 – Azure </br> 5.3.18-18.5 – Azure do 5.3.18-18.21 – Azure </br> 4.12.14-8.52-Azure, 5.3.18-18.24-Azure, 4.12.14-8.55-Azure, 5.3.18-18.29-Azure až 9,39 oprava hotfix * *
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.38](https://support.microsoft.com/help/4590304/)  | Ve výchozím nastavení se podporují všechny [jádra SUSE 15, SP1 a SP2](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.12.14-5.5 – Azure na 4.12.14 – 5.47 – Azure </br></br> 4.12.14-8,5 – Azure na 4.12.14 – 8.44 – Azure </br> 5.3.18-16 – Azure </br> 5.3.18-18.5 – Azure do 5.3.18-18.18 – Azure </br> 4.12.14-8.47-Azure, 5.3.18-18.21-Azure až 9,38 oprava hotfix * *
SUSE Linux Enterprise Server 15 a 15 SP1 | [9,37](https://support.microsoft.com/help/4582666/)  | Ve výchozím nastavení se podporují všechny [jádra SUSE 15, SP1 a SP2](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.12.14-5.5 – Azure na 4.12.14 – 5.47 – Azure </br></br> 4.12.14-8,5 – Azure na 4.12.14 – 8.38 – Azure </br> 4.12.14-8.41-Azure, 4.12.14-8.44-Azure až 9,37 oprava hotfix * *


## <a name="replicated-machines---linux-file-systemguest-storage"></a>Replikované počítače – systém souborů Linux/úložiště hostů

* Souborové systémy: ext3, EXT4, XFS, BTRFS
* Správce svazků: LVM2

> [!NOTE]
> Multipath software není podporován.


## <a name="replicated-machines---compute-settings"></a>Replikované počítače – nastavení výpočtů

**Nastavení** | **Podpora** | **Podrobnosti**
--- | --- | ---
Velikost | Všechny velikosti virtuálních počítačů Azure s minimálně 2 jádry procesoru a 1 GB paměti RAM | Ověřte [velikosti virtuálních počítačů Azure](../virtual-machines/sizes.md).
Skupiny dostupnosti | Podporováno | Pokud povolíte replikaci pro virtuální počítač Azure s výchozími možnostmi, vytvoří se skupina dostupnosti automaticky na základě nastavení zdrojové oblasti. Tato nastavení můžete změnit.
Zóny dostupnosti | Podporováno |
Zvýhodněné hybridní využití (centrum) | Podporováno | Pokud má zdrojový virtuální počítač povolenou licenci na rozbočovač, používá testovací převzetí služeb při selhání nebo převzetí služeb při selhání virtuální počítač taky licenci centra.
Škálovací sady virtuálních počítačů | Nepodporováno |
Image Galerie Azure – Publikováno Microsoftem | Podporováno | Podporováno, pokud virtuální počítač běží v podporovaném operačním systému.
Image Galerie Azure – Publikováno třetí stranou | Podporováno | Podporováno, pokud virtuální počítač běží v podporovaném operačním systému.
Vlastní image – Publikováno třetí stranou | Podporováno | Podporováno, pokud virtuální počítač běží v podporovaném operačním systému.
Virtuální počítače migrované pomocí Site Recovery | Podporováno | Pokud se virtuální počítač VMware nebo fyzický počítač migruje do Azure pomocí Site Recovery, musíte odinstalovat starší verzi služby mobility spuštěnou na počítači a restartovat počítač před tím, než ho budete replikovat do jiné oblasti Azure.
Zásady Azure RBAC | Nepodporováno | Zásady řízení přístupu na základě role Azure na virtuálních počítačích se nereplikují do virtuálního počítače s podporou převzetí služeb při selhání v cílové oblasti.
Rozšíření | Nepodporováno | Rozšíření se nereplikují do virtuálního počítače s podporou převzetí služeb při selhání v cílové oblasti. Po převzetí služeb při selhání je potřeba ji nainstalovat ručně.
Skupiny umístění blízkosti | Podporováno | Virtuální počítače umístěné ve skupině umístění blízkosti se dají chránit pomocí Site Recovery.
Značky  | Podporováno | Uživatelem vygenerované značky použité na zdrojových virtuálních počítačích se přenesou do cílového virtuálního počítače po testovací převzetí služeb při selhání nebo převzetí služeb při selhání. Značky virtuálních počítačů se replikují jednou za 24 hodin, pokud jsou virtuální počítače v cílové oblasti přítomné.


## <a name="replicated-machines---disk-actions"></a>Replikované počítače – diskové akce

**Akce** | **Podrobnosti**
-- | ---
Změna velikosti disku na replikovaném virtuálním počítači | Podporováno ve zdrojovém virtuálním počítači před převzetím služeb při selhání. Není nutné zakázat nebo znovu povolit replikaci.<br/><br/> Pokud po převzetí služeb při selhání změníte zdrojový virtuální počítač, změny se nezachytí.<br/><br/> Pokud po převzetí služeb při selhání změníte velikost disku na virtuálním počítači Azure, změny nebudou zachyceny Site Recovery a navrácení služeb po obnovení do původní velikosti virtuálního počítače.
Přidání disku do replikovaného virtuálního počítače | Podporováno
Offline změny v chráněných discích | Odpojení disků a provedení úprav v režimu offline vyžaduje aktivaci úplné opětovné synchronizace.

## <a name="replicated-machines---storage"></a>Replikované počítače – úložiště

Tato tabulka shrnuje podporu pro disk s operačním systémem Azure VM, datový disk a dočasný disk.

- Abyste se vyhnuli problémům s výkonem, je důležité sledovat omezení počtu disků virtuálních počítačů a cíle pro službu [Managed disks](../virtual-machines/disks-scalability-targets.md) .
- Pokud nasadíte s výchozím nastavením, Site Recovery na základě nastavení zdroje automaticky vytvoří disky a účty úložiště.
- Pokud přizpůsobíte, ujistěte se, že dodržujete pokyny.

**Komponenta** | **Podpora** | **Podrobnosti**
--- | --- | ---
Maximální velikost disku s operačním systémem | 2048 GB | [Přečtěte si další informace](../virtual-machines/managed-disks-overview.md) o discích virtuálních počítačů.
Dočasný disk | Nepodporováno | Dočasný disk je vždy vyloučen z replikace.<br/><br/> Na dočasném disku neukládejte žádná trvalá data. [Další informace](../virtual-machines/managed-disks-overview.md).
Maximální velikost datového disku | 32 TB pro Managed disks<br></br>4 TB pro nespravované disky|
Minimální velikost datového disku | Neexistují žádná omezení pro nespravované disky. 2 GB pro spravované disky |
Maximální počet datových disků | Až 64, v souladu s podporou konkrétní velikosti virtuálního počítače Azure | [Přečtěte si další informace](../virtual-machines/sizes.md) o velikostech virtuálních počítačů.
Rychlost změny datového disku | Maximálně 20 MB/s na disk pro Premium Storage. Maximálně 2 MB/s na disk pro úložiště úrovně Standard. | Pokud je průměrná rychlost změny dat na disku nepřetržitě vyšší než maximální, replikace se nezachytí.<br/><br/>  Pokud je ale maximum překročeno zřídka, replikace může zachytit, ale mírně zpožděné body obnovení.
Datový disk – standardní účet úložiště | Podporováno |
Datový disk – účet Premium Storage | Podporováno | Pokud má virtuální počítač disky se systémem Premium a standardními účty úložiště, můžete pro každý disk vybrat jiný cílový účet úložiště, abyste měli jistotu, že máte stejnou konfiguraci úložiště v cílové oblasti.
Spravovaný disk – Standard | Podporováno v oblastech Azure, ve kterých je podpora Azure Site Recovery podporovaná. |
Spravovaný disk – Premium | Podporováno v oblastech Azure, ve kterých je podpora Azure Site Recovery podporovaná. |
SSD úrovně Standard | Podporováno |
Redundance | LRS a GRS jsou podporovány.<br/><br/> ZRS se nepodporuje.
Studené a horké úložiště | Nepodporováno | Disky virtuálních počítačů se nepodporují na studeném a horkém úložišti.
Prostory úložiště | Podporováno |
Rozhraní úložiště NVMe | Nepodporováno
Šifrování v klidovém prostředí (SSE) | Podporováno | Pro účty úložiště je výchozí nastavení SSE.
Šifrování v klidovém umístění (CMK) | Podporováno | U spravovaných disků se podporuje jak software, tak klíče HSM.
Dvojité šifrování v klidovém umístění | Podporováno | Další informace o podporovaných oblastech pro [Windows](../virtual-machines/disk-encryption.md) a [Linux](../virtual-machines/disk-encryption.md)
Azure Disk Encryption (ADE) pro operační systém Windows | Podporováno pro virtuální počítače se spravovanými disky. | Virtuální počítače používající nespravované disky se nepodporují. <br/><br/> Klíče chráněné HSM nejsou podporovány. <br/><br/> Šifrování jednotlivých svazků na jednom disku se nepodporuje. |
Azure Disk Encryption (ADE) pro Linux OS | Podporováno pro virtuální počítače se spravovanými disky. | Virtuální počítače používající nespravované disky se nepodporují. <br/><br/> Klíče chráněné HSM nejsou podporovány. <br/><br/> Šifrování jednotlivých svazků na jednom disku se nepodporuje. <br><br> Známý problém s povolením replikace [Další informace](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
Střídání klíčů SAS | Nepodporuje se | Pokud je klíč SAS pro účty úložiště otočený, musí zákazník zakázat a znovu povolit replikaci. |
Ukládání hostitelů do mezipaměti | Podporováno
Horké přidání    | Podporováno | Povolení replikace pro datový disk, který přidáte do replikovaného virtuálního počítače Azure, se podporuje pro virtuální počítače, které používají spravované disky. <br/><br/> Do virtuálního počítače Azure se dá po jednom okamžiku připojit jenom jeden disk. Paralelní přidávání více disků není podporováno. |
Odstranit disk z provozu    | Nepodporováno | Pokud na virtuálním počítači odeberete datový disk, budete muset zakázat replikaci a znovu povolit replikaci pro virtuální počítač.
Vyloučení disku | Podpora. Ke konfiguraci je nutné použít [PowerShell](azure-to-azure-exclude-disks.md) . |    Dočasné disky jsou ve výchozím nastavení vyloučené.
Prostory úložiště – přímé  | Podporováno pro body obnovení konzistentní vzhledem k selhání. Body obnovení konzistentní vzhledem k aplikacím se nepodporují. |
Souborový server se škálováním na více instancí  | Podporováno pro body obnovení konzistentní vzhledem k selhání. Body obnovení konzistentní vzhledem k aplikacím se nepodporují. |
DRBD | Disky, které jsou součástí instalace DRBD, nejsou podporovány. |
LRS | Podporováno |
GRS | Podporováno |
RA-GRS | Podporováno |
ZRS | Nepodporováno |
Studené a horké úložiště | Nepodporováno | Disky virtuálních počítačů se ve studeném a horkém úložišti nepodporují.
Azure Storage brány firewall pro virtuální sítě  | Podporováno | Pokud omezíte přístup k účtu úložiště přes virtuální síť, povolte možnost [Povolit důvěryhodné služby společnosti Microsoft](../storage/common/storage-network-security.md#exceptions).
Účty úložiště pro obecné účely v2 (horká a studená vrstva) | Podporováno | Náklady transakce se podstatně zvyšují v porovnání s účty úložiště pro obecné účely v1.
Generace 2 (spuštění UEFI) | Podporováno
NVMe disky | Nepodporováno
Sdílené disky Azure | Nepodporováno
Možnost zabezpečeného přenosu | Podporováno
Disky s povoleným akcelerátorem zápisu | Nepodporováno
Značky  | Podporováno | Uživatelem vygenerované značky se replikují každých 24 hodin.

>[!IMPORTANT]
> Abyste se vyhnuli problémům s výkonem, ujistěte se, že je pro službu [Managed disks](../virtual-machines/disks-scalability-targets.md)vhodná škálovatelnost a cíle výkonu virtuálního počítače. Pokud použijete výchozí nastavení, Site Recovery v závislosti na konfiguraci zdroje vytvoří požadované disky a účty úložiště. Pokud přizpůsobíte a vyberete vlastní nastavení, postupujte podle škálovatelnosti disku a cílů výkonu pro vaše zdrojové virtuální počítače.

## <a name="limits-and-data-change-rates"></a>Limity a míry změny dat

Následující tabulka shrnuje Site Recovery omezení.

- Tato omezení vycházejí z našich testů, ale zjevně nepokrývá všechny možné kombinace I/O aplikací.
- Skutečné výsledky se můžou lišit v závislosti na kombinaci v/v aplikace.
- Existují dvě omezení, která je potřeba zvážit, četnost změn dat na disku a změny dat virtuálních počítačů.
- Aktuální limit pro každou četnost změn dat virtuálního počítače je 54 MB/s, bez ohledu na velikost.

**Cíl úložiště** | **Průměrný počet vstupů a výstupů zdrojového disku** |**Průměrná četnost změn dat zdrojového disku** | **Celková četnost změn dat zdrojového disku za den**
---|---|---|---
Storage úrovně Standard | 8 kB    | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 8 kB    | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 16 kB | 4 MB/s |    336 GB na disk
Disk úrovně Premium P10 nebo P15 | 32 kB nebo větší | 8 MB/s | 672 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 8 kB    | 5 MB/s | 421 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 16 kB nebo větší |20 MB/s | 1684 GB na disk

## <a name="replicated-machines---networking"></a>Replikované počítače – sítě
**Nastavení** | **Podpora** | **Podrobnosti**
--- | --- | ---
NIC | Maximální podporovaný počet pro konkrétní velikost virtuálního počítače Azure | Síťové karty se vytvoří, když se virtuální počítač vytvoří během převzetí služeb při selhání.<br/><br/> Počet síťových adaptérů na VIRTUÁLNÍm počítači s podporou převzetí služeb při selhání závisí na počtu síťových adaptérů ve zdrojovém virtuálním počítači, když je replikace povolená. Pokud po povolení replikace přidáte nebo odeberete síťovou kartu, nebude to mít vliv na počet síťových adaptérů v replikovaném virtuálním počítači po převzetí služeb při selhání. <br/><br/> Pořadí síťových adaptérů po převzetí služeb při selhání není zaručené jako původní pořadí. <br/><br/> Síťové karty můžete přejmenovat v cílové oblasti na základě zásad vytváření názvů vaší organizace. Přejmenování síťových adaptérů se podporuje pomocí prostředí PowerShell.
Internetový nástroj pro vyrovnávání zatížení | Nepodporováno | Můžete nastavit služby Vyrovnávání zatížení pro veřejné/internetové sítě v primární oblasti. Nástroje pro vyrovnávání zatížení veřejné/Internet však nejsou podporovány Azure Site Recovery v oblasti zotavení po havárii.
Interní nástroj pro vyrovnávání zatížení | Podporováno | Přidružte předem nakonfigurovaný Nástroj pro vyrovnávání zatížení pomocí skriptu Azure Automation v plánu obnovení.
Veřejná IP adresa | Podporováno | Přidružte existující veřejnou IP adresu k síťovému rozhraní. Případně vytvořte veřejnou IP adresu a přidružte ji k síťovému rozhraní pomocí skriptu Azure Automation v plánu obnovení.
NSG na síťové kartě | Podporováno | Přidružte NSG k síťovému rozhraní pomocí skriptu Azure Automation v plánu obnovení.
NSG v podsíti | Podporováno | Přidružte NSG k podsíti pomocí skriptu Azure Automation v plánu obnovení.
Rezervovaná (statická) IP adresa | Podporováno | Pokud má síťová karta ve zdrojovém virtuálním počítači statickou IP adresu a cílová podsíť má stejnou IP adresu, je přiřazená k virtuálnímu počítači, u kterého došlo k převzetí služeb při selhání.<br/><br/> Pokud cílová podsíť nemá k dispozici stejnou IP adresu, jedna z dostupných IP adres v podsíti je vyhrazena pro virtuální počítač.<br/><br/> Můžete také zadat pevnou IP adresu a podsíť v nastavení **replikované položky**  >  **Nastavení**  >  **výpočetních a síťových**  >  **síťových rozhraní**.
Dynamická IP adresa | Podporováno | Pokud má síťová karta ve zdroji dynamické přidělování IP adres, síťové rozhraní ve virtuálním počítači převzetí služeb při selhání je ve výchozím nastavení také dynamické.<br/><br/> V případě potřeby ho můžete v případě potřeby upravit na pevně stanovenou IP adresu.
Několik IP adres | Nepodporováno | Při převzetí služeb při selhání virtuálního počítače, který má síťové rozhraní s více IP adresami, zůstane zachována pouze primární IP adresa síťového adaptéru ve zdrojové oblasti. Pokud chcete přiřadit více IP adres, můžete přidat virtuální počítače do [plánu obnovení](recovery-plan-overview.md) a připojit skript pro přiřazení dalších IP adres k plánu, nebo můžete změnu provést ručně nebo pomocí skriptu po převzetí služeb při selhání.
Traffic Manager     | Podporováno | Můžete předem nakonfigurovat Traffic Manager tak, aby se provoz směroval do koncového bodu ve zdrojové oblasti v pravidelných intervalech, a v případě převzetí služeb při selhání do koncového bodu v cílové oblasti.
Azure DNS | Podporováno |
Vlastní DNS    | Podporováno |
Neověřený proxy server | Podporováno | [Další informace](./azure-to-azure-about-networking.md)
Ověřený proxy server | Nepodporováno | Pokud virtuální počítač používá pro odchozí připojení ověřený proxy server, nedá se replikovat pomocí Azure Site Recovery.
Připojení VPN typu Site-to-site k místnímu prostředí<br/><br/>(s ExpressRoute nebo bez něj)| Podporováno | Ujistěte se, že jsou udr a skupin zabezpečení sítě nakonfigurované tak, aby se provoz Site Recovery nesměroval do místního prostředí. [Další informace](./azure-to-azure-about-networking.md)
Připojení virtuální sítě k virtuální síti    | Podporováno | [Další informace](./azure-to-azure-about-networking.md)
Koncové body služby pro virtuální síť | Podporováno | Pokud omezíte přístup k virtuální síti k účtům úložiště, ujistěte se, že důvěryhodné služby Microsoftu mají povolený přístup k účtu úložiště.
Urychlení sítě | Podporováno | Na zdrojovém virtuálním počítači musí být povolené urychlené síťové služby. [Další informace](azure-vm-disaster-recovery-with-accelerated-networking.md).
Síťové zařízení Palo Alto | Nepodporováno | U zařízení třetích stran se v rámci virtuálního počítače často ukládají omezení poskytovatele. Azure Site Recovery potřebuje k dispozici agenta, rozšíření a odchozí připojení. Zařízení ale neumožňuje konfigurovat žádné odchozí aktivity uvnitř virtuálního počítače.
IPv6  | Nepodporováno | Současně se nepodporují i smíšené konfigurace, které zahrnují IPv4 i IPv6. Před jakoukoli operací Site Recovery Prosím uvolněte podsíť rozsahu IPv6.
Přístup k Site Recovery službě přes soukromé odkazy | Podporováno | [Další informace](azure-to-azure-how-to-enable-replication-private-endpoints.md)
Značky  | Podporováno | Uživatelem vygenerované značky na síťových kartách se replikují každých 24 hodin.



## <a name="next-steps"></a>Další kroky

- Přečtěte si [pokyny k síti](./azure-to-azure-about-networking.md)  pro replikaci virtuálních počítačů Azure.
- Nasazení zotavení po havárii [replikací virtuálních počítačů Azure](./azure-to-azure-quickstart.md).
