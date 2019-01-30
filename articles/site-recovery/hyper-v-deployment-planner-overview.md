---
title: Informace o Azure Site Recovery Deployment Planner pro zotavení po havárii virtuálních počítačů Hyper-V do Azure | Dokumentace Microsoftu
description: Přečtěte si o zotavení po havárii Azure Site Recovery nasazení Planneru Hyper-V do Azure.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: nisoneji
ms.openlocfilehash: 06e3139ffa958637721aae7e912b34070d307757
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207390"
---
# <a name="about-the-azure-site-recovery-deployment-planner-for-hyper-v-disaster-recovery-to-azure"></a>Informace o Azure Site Recovery Deployment Planner pro zotavení po havárii Hyper-V do Azure

Tento článek představuje uživatelskou příručku k Plánovači nasazení služby Azure Site Recovery pro produkční nasazení Hyper-V do Azure.

Než začnete chránit jakékoli virtuální počítače Hyper-V pomocí Site Recovery, přidělte dostatečnou šířku pásma v závislosti na vaší denní frekvenci změn dat, abyste dosáhli požadovaného cíle bodu obnovení (RPO) a přidělili dostatečně velký volný prostor úložiště na každém svazku místního úložiště Hyper-V.

Také je nutné vytvořit správný typ a počet cílových účtů služby Azure Storage. Vytvoříte účty služby Storage úrovně Standard nebo Premium, které budou zohledňovat nárůst počtu vašich zdrojových produkčních serverů způsobený zvyšováním využití v průběhu času. Typ úložiště zvolíte pro každý virtuální počítač na základě charakteristik úloh, jako je počet vstupně-výstupních operací (IOPS) čtení a zápisu za sekundu nebo četnost změn dat, a omezení Azure Site Recovery. 

Plánovač nasazení služby Azure Site Recovery je nástroj příkazového řádku pro scénáře zotavení po havárii z Hyper-V do Azure i z VMware do Azure. Pomocí tohoto nástroje můžete vzdáleně profilovat virtuální počítače Hyper-V přítomné na více hostitelích Hyper-V (bez jakéhokoli dopadu na produkční prostředí) a porozumět tak požadavkům na šířku pásma a úložiště Azure pro úspěšnou replikaci a testovací převzetí služeb při selhání / převzetí služeb při selhání. Nástroj můžete spustit místně bez nutnosti instalace jakýchkoli komponent Azure Site Recovery. K získání výsledků přesně dosažené propustnosti ale doporučujeme spustit plánovač na Windows Serveru, který má stejnou hardwarovou konfiguraci jako jeden ze serverů Hyper-V, které budete používat k povolení ochrany pomocí zotavení po havárii do Azure. 

Nástroj poskytuje následující podrobnosti:

**Posouzení kompatibility**

* Vyhodnocení způsobilosti virtuálního počítače na základě počtu disků, velikosti disků, počtu vstupně-výstupních operací za sekundu (IOPS), četnosti změn a několika charakteristik virtuálních počítačů

**Srovnání šířky pásma sítě a posouzení cíle bodu obnovení**

* Odhadovaná šířka pásma sítě potřebná pro rozdílovou replikaci
* Propustnost z místního prostředí do Azure, které Azure Site Recovery může dosáhnout
* Cíl bodu obnovení, kterého je možné dosáhnout pro danou šířku pásma
* Dopad na požadovaný cíl bodu obnovení při zřízení menší šířky pásma.

    
**Požadavky na infrastrukturu Azure**

* Požadovaný typ úložiště (účet služby Storage úrovně Standard nebo Premium) pro každý virtuální počítač
* Celkový počet účtů služby Storage úrovně Standard a Premium, které se mají nastavit pro replikaci
* Návrhy pojmenování účtů úložiště na základě pokynů pro Azure Storage
* Umístění jednotlivých virtuálních počítačů v účtech úložiště
* Počet jader systému Azure, která se mají zřídit před testovacím převzetím služeb při selhání nebo převzetím služeb při selhání v rámci předplatného
* Doporučená velikost virtuálního počítače Azure pro každý místní virtuální počítač

**Požadavky na místní infrastrukturu**
* Vyžadovaný volný prostor úložiště na každém svazku úložiště Hyper-V k zajištění úspěšné počáteční replikace a rozdílové replikace, které zajistí, že replikace virtuálních počítačů nebude způsobovat nežádoucí výpadky pro produkční aplikace
* Maximální frekvence kopírování, která se má nastavit pro replikaci Hyper-V

**Pokyny k rozdělení počáteční replikace do dávek** 
* Počet dávek virtuálních počítačů použitých k ochraně
* Seznam virtuálních počítačů v každé dávce
* Pořadí, ve kterém mají být jednotlivé dávky chráněné
* Odhadovaný čas dokončení počáteční replikace každé dávky

**Odhadované náklady na zotavení po havárii do Azure**
* Odhadované celkové náklady na zotavení po havárii do Azure: náklady na licence pro Azure Site Recovery, výpočty, úložiště a síť
* Podrobná analýza nákladů na virtuální počítač



>[!IMPORTANT]
>
>Protože se využití časem bude pravděpodobně zvyšovat, všechny předchozí výpočty jsou provedeny s předpokladem 30% faktoru růstu v charakteristikách úloh a používají hodnoty 95. percentilu všech metrik profilace (počet vstupně-výstupních operací čtení a zápisu za sekundu [R/W IOPS], četnost změn atd.). Oba tyto elementy (faktor růstu a výpočet percentilu) je možné konfigurovat. Další informace o faktoru růstu najdete v části Aspekty faktoru růstu. Další informace o hodnotě percentilu najdete v části Hodnota percentilu používaná k výpočtu.
>

## <a name="support-matrix"></a>Matice podpory

| | **Z VMware do Azure** |**Z Hyper-V do Azure**|**Z Azure do Azure**|**Z Hyper-V do sekundární lokality**|**Z VMware do sekundární lokality**
--|--|--|--|--|--
Podporované scénáře |Ano|Ano|Ne|Ano*|Ne
Podporovaná verze | vCenter 6.5, 6.0 nebo 5.5| Windows Server 2016, Windows Server 2012 R2 | Není k dispozici |Windows Server 2016, Windows Server 2012 R2|Není k dispozici
Podporovaná konfigurace|vCenter, ESXi| Cluster Hyper-V, hostitel Hyper-V|Není k dispozici|Cluster Hyper-V, hostitel Hyper-V|Není k dispozici|
Počet serverů, které jde profilovat, na spuštěnou instanci Plánovače nasazení služby Azure Site Recovery |Jeden (virtuální počítače, které patří k jednomu vCenter Serveru nebo jednomu serveru ESXi, jde profilovat najednou)|Více (virtuální počítače napříč více hostiteli nebo hostitelskými clustery jde profilovt najednou)| Není k dispozici |Více (virtuální počítače napříč více hostiteli nebo hostitelskými clustery jde profilovt najednou)| Není k dispozici

*Nástroj je primárně určen pro scénář zotavení po havárii z Hyper-V do Azure. Pro zotavení po havárii z Hyper-V na sekundární server ho jde použít pouze k pochopení doporučení na straně zdroje, jako je požadovaná šířka pásma, požadovaný prázdný prostor úložiště na každém ze zdrojových serverů Hyper-V a počet dávek počáteční replikace a definice dávek.  Ignorujte doporučení Azure a náklady ze sestavy. Také pro zotavení po havárii z Hyper-V na sekundární server nejde použít operaci Zjištění propustnosti.

## <a name="prerequisites"></a>Požadavky
Nástroj má pro Hyper-V tři hlavní fáze: získání seznamu virtuálních počítačů, profilace a generování sestav. Existuje také čtvrtá možnost – výpočet pouze propustnosti. V následující tabulce jsou uvedeny požadavky na server, na kterém se musí provést jednotlivé fáze:

| Požadavek na server | Popis |
|---|---|
|Získání seznamu virtuálních počítačů, profilace a měření propustnosti |<ul><li>Operační systém: Microsoft Windows Server 2016 nebo Microsoft Windows Server 2012 R2 </li><li>Konfigurace počítače: 8 virtuálních CPU, 16 GB paměti RAM, 300 GB HDD</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[Microsoft Visual C++ Redistributable for Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Internetový přístup k Azure z tohoto serveru</li><li>Účet služby Azure Storage</li><li>Přístup správce na server</li><li>Volné místo na disku alespoň 100 GB (za předpokladu 1 000 virtuálních počítačů, každý průměrně se 3 disky a profilovaný po dobu 30 dnů)</li><li>Virtuální počítač, ze kterého spouštíte nástroj Plánovač nasazení služby Azure Site Recovery, musí být přidaný do seznamu TrustedHosts všech serverů Hyper-V.</li><li>Všechny servery Hyper-V určených k profilaci musí být přidány do seznamu TrustedHosts klientského virtuálního počítače z ve kterém je nástroj spuštěn. [Další informace o přidání serverů do seznamu TrustedHosts](#steps-to-add-servers-into-trustedhosts-list) </li><li> Nástroj by měl být spuštěný pomocí oprávnění pro správu z PowerShellu nebo konzoly příkazového řádku na klientovi.</ul></ul>|
| Generování sestav | Počítač s Windows nebo Windows Server s aplikací Microsoft Excel 2013 nebo novější |
| Uživatelská oprávnění | Účet správce pro přístup ke clusteru Hyper-V / hostiteli Hyper-V během operací získání seznamu virtuálních počítačů a profilace<br>Všichni hostitelé, pro které je potřeba provést profilaci, by měli mít účet správce domény se stejnými přihlašovacími údaji, tj. uživatelským jménem a heslem.
 |

## <a name="steps-to-add-servers-into-trustedhosts-list"></a>Postup přidání serverů do seznamu TrustedHosts
1.  Virtuální počítač, ze kterého se má nástroj nasadit, by měl mít všechny profilované hostitele ve svém seznamu TrustedHosts. Pokud chcete přidat klienta do seznamu Trustedhosts, spusťte na virtuálním počítači následující příkaz z PowerShellu se zvýšenými oprávněními. Virtuální počítač může být Windows Server 2012 R2 nebo Windows Server 2016. 

            set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate

1.  Každý hostitel Hyper-V, který potřebuje profilaci, musí splňovat následující:

    a. V seznamu TrustedHosts musí být uvedený virtuální počítač, na kterém se bude nástroj spouštět. Na hostiteli Hyper-V spusťte z PowerShellu se zvýšenými oprávněními následující příkaz.

            set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate

    b. Je povolená vzdálená komunikace PowerShellu.

            Enable-PSRemoting -Force

## <a name="download-and-extract-the-deployment-planner-tool"></a>Stažení a rozbalení nástroje plánovače nasazení

1.  Stáhněte si nejnovější verzi nástroje [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner).
Nástroje je zabalený ve složce .zip. Stejný nástroj podporuje scénáře zotavení po havárii jak z VMware do Azure, tak i z Hyper-V do Azure. Tento nástroj můžete použít i pro scénář zotavení po havárii z Hyper-V do sekundární lokality, ale ignorujte doporučení infrastruktury Azure ze sestavy.

1.  Zkopírujte složku .zip na Windows Server, ze kterého chcete nástroj spustit. Nástroj můžete spustit na Windows Serveru 2012 R2 nebo Windows Serveru 2016. Server musí mít přístup k síti, aby se připojil ke clusteru Hyper-V nebo hostiteli Hyper-V, kde jsou profilované virtuální počítače. Doporučujeme, abyste měli stejnou hardwarovou konfiguraci virtuálního počítače, kde chcete nástroj spustit, jako toho serveru Hyper-V, který chcete chránit. Taková konfigurace zajistí, že dosažená propustnost, kterou nástroj hlásí, bude odpovídat skutečné propustnosti, které může Azure Site Recovery dosáhnout během replikace. Výpočet propustnosti závisí na dostupné šířce pásma sítě na serveru a na konfiguraci hardwaru (CPU, úložiště atd.) serveru. Propustnost je počítána ze serveru, na kterém je nástroj spuštěný, do Azure. Pokud se konfigurace hardwaru tohoto serveru liší od serveru Hyper-V, dosažená propustnost, kterou nástroj hlásí, nebude přesná.
Doporučená konfigurace virtuálního počítače: 8 vCPUs, 16 GB RAM, 300 GB HDD.

1.  Rozbalte složku .zip.
Složka obsahuje několik souborů a podsložek. Spustitelný soubor je ASRDeploymentPlanner.exe v nadřazené složce.

Příklad: Zkopírujte soubor .zip na jednotku E:\ a rozbalte jej. E:\ASR Deployment Planner_v2.3.zip

E:\ASR Deployment Planner_v2.3\ASRDeploymentPlanner.exe

### <a name="updating-to-the-latest-version-of-deployment-planner"></a>Aktualizace na nejnovější verzi plánovače nasazení
Pokud máte předchozí verzi plánovače nasazení, proveďte jednu z následujících akcí:
 * Pokud nejnovější verze neobsahuje opravu profilace a ve vaší stávající verzi Deployment Planneru již profilace probíhá, pokračujte v ní.
 * Pokud nejnovější verze obsahuje opravu profilace, doporučujeme zastavit profilaci ve stávající verzi a znovu ji spustit v nové verzi.


  >[!NOTE]
  >
  >Při spuštění profilace v nové verzi předejte stejnou cestu k výstupnímu adresáři, aby nástroj připojil data profilu k existujícím souborům. K vygenerování sestavy se použije úplná sada dat profilace. Pokud předáte jiný výstupní adresář, vytvoří se nové soubory a stará data profilace se k vygenerování sestavy nepoužijí.
  >
  >Každý nový Deployment Planner je kumulativní aktualizací souboru .zip. Nemusíte kopírovat nejnovější soubory do předchozí složky. Můžete vytvořit a použít novou složku.

## <a name="version-history"></a>Historie verzí
Nejnovější verze nástroje plánovače nasazení ASR je 2.3.
Opravy přidané v jednotlivých aktualizacích najdete na stránce [historie verzí Plánovače nasazení ASR](https://social.technet.microsoft.com/wiki/contents/articles/51049.asr-deployment-planner-version-history.aspx).


## <a name="next-steps"></a>Další postup
* [Spuštění plánovače nasazení](site-recovery-hyper-v-deployment-planner-run.md)
