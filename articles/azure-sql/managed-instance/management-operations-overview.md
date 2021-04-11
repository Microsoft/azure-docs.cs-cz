---
title: Přehled operací správy
titleSuffix: Azure SQL Managed Instance
description: Přečtěte si o době trvání operací správy spravované instance Azure SQL a osvědčených postupech.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: overview
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: bd66c10bb1d6316bbe90e7ba4092d79c6a43a75d
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285271"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Přehled operací správy služby Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Spravovaná instance Azure SQL poskytuje operace správy, které můžete použít k automatickému nasazení nových spravovaných instancí, aktualizaci vlastností instance a odstranění instancí, pokud už nepotřebujete.

## <a name="what-are-management-operations"></a>Co jsou operace správy?

Všechny operace správy je možné uspořádat do následujících kategorií:

- Nasazení instance (vytvoření nové instance)
- Aktualizace instance (Změna vlastností instance, jako je virtuální jádra nebo rezervované úložiště)
- Odstranění instance

Za účelem podpory [nasazení v rámci virtuálních sítí Azure](../../virtual-network/virtual-network-for-azure-services.md) a zajištění izolace a zabezpečení pro zákazníky závisí spravovaná instance SQL na [virtuálních clusterech](connectivity-architecture-overview.md#high-level-connectivity-architecture). Virtuální cluster představuje vyhrazenou sadu izolovaných virtuálních počítačů nasazených v podsíti virtuální sítě zákazníka. V podstatě každá spravovaná instance nasazená do prázdné podsítě má za následek nové sestavení virtuálních clusterů.

Další operace správy na spravovaných instancích můžou ovlivnit základní virtuální cluster. Změny, které mají vliv na základní virtuální cluster, můžou ovlivnit dobu trvání operací správy, protože nasazení dalších virtuálních počítačů přináší režii, která je potřeba vzít v úvahu při plánování nových nasazení nebo aktualizací existujících spravovaných instancí.


## <a name="duration"></a>Doba trvání

Doba provozu virtuálního clusteru se může lišit, ale obvykle má nejdelší dobu trvání. 

Níže jsou uvedené hodnoty, které obvykle můžete očekávat na základě stávajících dat telemetrie služby:

- **Vytvoření virtuálního clusteru**: vytváření je synchronní krok v operacích správy instancí. <br/> **90% dokončených operací za 4 hodiny**.
- **Změna velikosti virtuálního clusteru (rozšíření nebo zmenšení)**: rozšíření je synchronní krok, zatímco zmenšování se provádí asynchronně (bez dopadu na dobu trvání operací správy instancí). <br/>**90% rozšíření clusteru skončí za méně než 2,5 hodin**.
- **Odstranění virtuálního clusteru**: odstranění je asynchronní krok, ale dá se taky [iniciovat ručně](virtual-cluster-delete.md) v prázdném virtuálním clusteru. v takovém případě se to provede synchronně. <br/>**90% odstranění virtuálních clusterů se dokončí za 1,5 hodin**.

Kromě toho může Správa instancí zahrnovat taky jednu z operací na hostovaných databázích, což vede k delším dobu trvání:

- **Připojení souborů databáze z Azure Storage**: synchronní krok, například škálování COMPUTE (virtuální jádra), nebo úložiště v úrovni služby pro obecné účely. <br/>**90% těchto operací skončí za 5 minut**.
- **Dosazení skupiny dostupnosti Always On**: synchronní krok, například výpočetní výkon (virtuální jádra) nebo škálování úložiště v úrovni služby pro důležité obchodní informace a změna úrovně služby z Pro obecné účely na pro důležité obchodní informace (nebo naopak). Doba trvání této operace je úměrná celkové velikosti databáze a aktuální aktivitě databáze (počet aktivních transakcí). Databázová aktivita při aktualizaci instance může způsobit značnou odchylku od celkové doby trvání. <br/>**90% těchto operací se spustí v 220 GB/hod nebo vyšších**.

V následujících tabulkách najdete souhrn operací a typických celkových dob trvání na základě kategorie operace:

**Kategorie: nasazení**

|Operace  |Dlouho běžící segment  |Odhadovaná doba trvání  |
|---------|---------|---------|
|První instance v prázdné podsíti|Vytvoření virtuálního clusteru|90% dokončených operací za 4 hodiny.|
|První instance jiné generace hardwaru v neprázdné podsíti (například první instance Gen 5 v podsíti s instancemi Gen 4)|Vytvoření virtuálního clusteru<sup>1</sup>|90% dokončených operací za 4 hodiny.|
|Vytváření dalších instancí v neprázdné podsíti (druhá, třetí atd. instance)|Změna velikosti virtuálního clusteru|90% dokončených operací za 2,5 hodin.|
| | | 

<sup>1</sup> virtuální cluster je postaven na generaci hardwaru.

**Kategorie: aktualizace**

|Operace  |Dlouho běžící segment  |Odhadovaná doba trvání  |
|---------|---------|---------|
|Změna vlastnosti instance (heslo správce, přihlášení Azure AD, příznak Zvýhodněné hybridní využití Azure)|–|Až 1 minuta.|
|Horizontální navýšení kapacity úložiště instance (Pro obecné účely úroveň služeb)|Žádný dlouhodobě běžící segment<sup>1</sup>|99% dokončených operací za 5 minut.|
|Horizontální navýšení kapacity úložiště instance (Pro důležité obchodní informace úroveň služeb)|– Změna velikosti virtuálního clusteru<br>– Vysazení skupiny dostupnosti Always On|90% dokončených operací během 2,5 hodin + času k osazení všech databází (220 GB za hodinu).|
|Instance COMPUTE (virtuální jádra) pro škálování směrem nahoru a dolů (Pro obecné účely)|– Změna velikosti virtuálního clusteru<br>-Připojení souborů databáze|90% dokončených operací za 2,5 hodin.|
|Instance COMPUTE (virtuální jádra) pro škálování směrem nahoru a dolů (Pro důležité obchodní informace)|– Změna velikosti virtuálního clusteru<br>– Vysazení skupiny dostupnosti Always On|90% dokončených operací během 2,5 hodin + času k osazení všech databází (220 GB za hodinu).|
|Instance instance služby instance (Pro obecné účely až Pro důležité obchodní informace a naopak)|– Změna velikosti virtuálního clusteru<br>– Vysazení skupiny dostupnosti Always On|90% dokončených operací během 2,5 hodin + času k osazení všech databází (220 GB za hodinu).|
| | | 

<sup>1</sup> škálování pro obecné účely spravované instance úložiště na konci operace nezpůsobí převzetí služeb při selhání. V tomto případě se operace skládá z aktualizace metadat a šíření odpovědi na odeslaný požadavek.

**Kategorie: odstranit**

|Operace  |Dlouho běžící segment  |Odhadovaná doba trvání  |
|---------|---------|---------|
|Odstranění instance|Zálohování koncového protokolu pro všechny databáze|90% operací skončilo během až 1 minuty.<br>Poznámka: Pokud je odstraněna poslední instance v podsíti, tato operace provede odstranění virtuálního clusteru po uplynutí 12 hodin. <sup>1</sup>|
|Odstranění virtuálního clusteru (jako operace iniciované uživatelem)|Odstranění virtuálního clusteru|90% operací se dokončilo během až 1,5 hodin.|
| | | 

<sup>1</sup>12 hodin je aktuální konfigurace, ale v budoucnu se může změnit. Pokud potřebujete virtuální cluster odstranit dřív (například pro vydání podsítě), přečtěte si téma [odstranění podsítě po odstranění spravované instance](virtual-cluster-delete.md).

## <a name="instance-availability"></a>Dostupnost instance

Spravovaná instance SQL **je k dispozici během operací aktualizace**, s výjimkou krátkého výpadku způsobeného převzetím služeb při selhání, ke kterému dochází na konci aktualizace. Obvykle trvá až 10 sekund i v případě přerušených dlouhotrvajících transakcí díky [urychlenému obnovení databáze](../accelerated-database-recovery.md).

> [!NOTE]
> Škálování úložiště spravované instance Pro obecné účely nezpůsobí převzetí služeb při selhání na konci aktualizace.

Spravovaná instance SQL není během operací nasazení a odstraňování dostupná pro klientské aplikace.

> [!IMPORTANT]
> Nedoporučujeme škálovat výpočetní výkon nebo úložiště Azure SQL Managed instance nebo změnit úroveň služby ve stejnou dobu jako dlouhotrvající transakce (import dat, úlohy zpracování dat, opětovné sestavení indexu atd.). Převzetí služeb při selhání databáze na konci operace zruší všechny probíhající transakce. 

## <a name="management-operations-steps"></a>Kroky pro operace správy

Operace správy se skládají z několika kroků. Díky [rozhraní API operací](management-operations-monitor.md) se tyto kroky zveřejňují pro podmnožinu operací (nasazení a aktualizace). Operace nasazení se skládá ze tří kroků a operace Update se provádí ve šesti krocích. Podrobnosti o době trvání operací najdete v části [trvání operací správy](#duration) . Kroky jsou uvedeny podle pořadí provádění.

### <a name="managed-instance-deployment-steps"></a>Kroky nasazení spravované instance

|Název kroku  |Popis kroku  |
|----|---------|
|Žádost o ověření |Odeslané parametry jsou ověřeny. V případě chybné operace konfigurace dojde k chybě s chybou. |
|Změna velikosti nebo vytvoření virtuálního clusteru |V závislosti na stavu podsítě přejde virtuální cluster na vytvoření nebo změnu velikosti. |
|Nové spuštění instance SQL |Proces SQL je spuštěný na nasazeném virtuálním clusteru. |

### <a name="managed-instance-update-steps"></a>Kroky aktualizace spravované instance

|Název kroku  |Popis kroku  |
|----|---------|
|Žádost o ověření | Odeslané parametry jsou ověřeny. V případě chybné operace konfigurace dojde k chybě s chybou. |
|Změna velikosti nebo vytvoření virtuálního clusteru |V závislosti na stavu podsítě přejde virtuální cluster na vytvoření nebo změnu velikosti. |
|Nové spuštění instance SQL | Proces SQL je spuštěný na nasazeném virtuálním clusteru. |
|Osazení souborů databáze/připojení souborů databáze |V závislosti na typu operace aktualizace se provede buď databáze osazení, nebo připojení souborů databáze. |
|Příprava převzetí služeb při selhání |Po osazení dat nebo opětovném připojení souborů databáze se systém připraví na převzetí služeb při selhání. Když je všechno nastavené, převzetí služeb při selhání se provádí **s krátkodobým výpadkem**. |
|Původní vyčištění instance SQL |Odebírání starého procesu SQL z virtuálního clusteru |

> [!NOTE]
> Výsledkem instancí škálování je, že základní virtuální cluster projde procesem uvolňování nevyužité kapacity a možné defragmentace kapacity, což může ovlivnit instance, které se neúčastnily operací vytvoření a škálování. 


## <a name="management-operations-cross-impact"></a>Operace správy – mezi důsledky

Operace správy na spravované instanci může ovlivnit jiné operace správy instancí umístěných uvnitř stejného virtuálního clusteru:

- **Dlouhotrvající operace obnovení** ve virtuálním clusteru budou zablokovat operace vytvoření nebo škálování jiné instance ve stejné podsíti.<br/>**Příklad:** Pokud existuje dlouhodobě spuštěná operace obnovení a ve stejné podsíti je žádost o vytvoření nebo škálování, bude trvat déle, než se dokončí operace obnovení, než bude pokračovat.

- **Následná operace vytvoření nebo škálování instance** je blokována dříve zahájeným vytvořením instance nebo škálováním instance, která iniciovala změnu virtuálního clusteru.<br/>**Příklad:** Pokud ve stejné podsíti v rámci stejného virtuálního clusteru existuje více požadavků na vytvoření a/nebo škálování a jedna z nich zahájí změnu velikosti virtuálního clusteru, všechny požadavky, které byly odeslány 5. minut po počáteční žádosti o operaci, budou trvat déle, než se čekalo, protože tyto požadavky budou muset čekat na dokončení změny před obnovením.

- **Operace vytvoření nebo škálování odeslané v okně s pěti minutami** se dávkují a spustí paralelně.<br/>**Příklad:** Pro všechny operace odeslané v intervalu 5 minut se provede jenom jedna změna velikosti virtuálního clusteru (měří se od okamžiku provedení první žádosti o operaci). Pokud se po odeslání prvního požadavku pošle více než 5 minut, bude se čekat na dokončení změny ve virtuálním clusteru, než se spustí spuštění.

> [!IMPORTANT]
> Operace správy, které jsou zablokovány z důvodu probíhající jiné operace, budou automaticky obnoveny, jakmile budou splněny podmínky pro pokračování. Není nutná žádná akce uživatele, aby bylo možné obnovit dočasně pozastavené operace správy.

## <a name="monitoring-management-operations"></a>Monitorování operací správy

Informace o tom, jak monitorovat průběh a stav operací správy, najdete v tématu [monitorování operací správy](management-operations-monitor.md).

## <a name="canceling-management-operations"></a>Rušení operací správy

Informace o zrušení operace správy najdete v tématu [rušení operací správy](management-operations-cancel.md).


## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak vytvořit první spravovanou instanci, najdete v tématu [Průvodce rychlým startem](instance-create-quickstart.md).
- Seznam funkcí a porovnání najdete v tématu [běžné funkce SQL](../database/features-comparison.md).
- Další informace o konfiguraci virtuální sítě najdete v tématu [Konfigurace virtuální sítě spravované instance SQL](connectivity-architecture-overview.md).
- Pro rychlý Start, který vytváří spravovanou instanci a obnovuje databázi ze záložního souboru, najdete v tématu [Vytvoření spravované instance](instance-create-quickstart.md).
- Kurz týkající se použití Azure Database Migration Service k migraci najdete v tématu [migrace spravované instance SQL pomocí Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
