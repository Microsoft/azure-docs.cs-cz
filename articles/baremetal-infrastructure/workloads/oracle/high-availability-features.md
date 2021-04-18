---
title: Funkce vysoké dostupnosti pro Oracle v Azure BareMetal
description: Přečtěte si o funkcích, které jsou k dispozici v BareMetal pro databázi Oracle.
ms.topic: overview
ms.subservice: workloads
ms.date: 04/16/2021
ms.openlocfilehash: b27dc4b857d553be791528cbd91aee70b2294a92
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600212"
---
# <a name="high-availability-features-for-oracle-on-azure-baremetal"></a>Funkce vysoké dostupnosti pro Oracle v Azure BareMetal

V tomto článku se podíváme na funkce vysoké dostupnosti a zotavení po havárii Oracle.

Oracle nabízí spoustu funkcí pro vytváření odolné platformy pro spouštění databází Oracle. Přestože žádná jedna funkce neposkytuje pokrytí pro každý typ selhání, kombinace technologií v vrstveném režimu vytvoří vysoce dostupný systém. K udržení dostupnosti nejsou nutné všechny funkce. Kombinování strategií vám ale nabízí nejlepší ochranu z sortimentu selhání, ke kterým dojde. 

## <a name="flashback-database"></a>Databáze Flashback

Funkce [databáze Flashback](https://docs.oracle.com/en/database/oracle/oracle-database/21/rcmrf/FLASHBACK-DATABASE.html#GUID-584AC79A-40C5-45CA-8C63-DED3BE3A4511) se nachází v Oracle Database Enterprise Edition. Databáze Flashback Převine databázi do konkrétního bodu v čase. Tato funkce se liší od [Recovery Manager (rman)](https://docs.oracle.com/en/cloud/paas/db-backup-cloud/csdbb/performing-general-restore-and-recovery-operations.html) obnovení k určitému bodu v čase, v němž se převíjí od aktuálního času, nikoli dopředné vítr po obnovení. Výsledkem je, že databáze Flashback přináší mnohem kratší dobu dokončování.
 
Tuto funkci můžete použít spolu se systémem [Oracle data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590). Databáze Flashback umožňuje správci databáze znovu vytvořit instanci databáze, která selhala, do konfigurace ochrany dat bez úplného RMAN obnovení a obnovení. Tato funkce umožňuje obnovit možnosti zotavení po havárii (a veškeré výhody pro vytváření sestav s vyšším využitím a zálohy s aktivní ochranou dat) mnohem rychleji.
 
Tuto funkci můžete použít místo časového zpoždění opětovného spuštění databáze v pohotovostním režimu. Pohotovostní databáze může být přehrávána zpět do bodu před tím, než problém vznikl.
 
Oracle Database udržuje protokoly Flashback v oblasti rychlé obnovy (v/v). Tyto protokoly jsou oddělené od protokolů opětovného provedení a vyžadují více místa v rámci rozsahu. Ve výchozím nastavení se uchovávají 24 hodin protokolu Flashback, ale toto nastavení můžete změnit podle svých požadavků.

## <a name="oracle-real-application-clusters"></a>Clustery reálné aplikace Oracle

[Cluster reálné aplikace Oracle (RAC)](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92) umožňuje, aby se několik propojených serverů zobrazovalo jako jedna databázová služba pro koncové uživatele a aplikace. Tato funkce odebere mnoho bodů selhání a představuje rozpoznané aktivní/aktivní řešení vysoké dostupnosti pro databáze Oracle.

Jak je znázorněno na následujícím obrázku od společnosti Oracle [Přehled vysoké dostupnosti a osvědčených postupů](https://docs.oracle.com/en/database/oracle/oracle-database/19/haovw/ha-features.html), je k aplikační vrstvě nabídnuta jedna databáze RAC. Aplikace se připojí k naslouchacímu procesu vyhledávání, který směruje provoz do konkrétní instance databáze. Certifikát RAC řídí přístup z více instancí a udržuje tak konzistenci dat napříč různými výpočetními uzly.

![Diagram znázorňující přehled architektury Oracle RAC.](media/oracle-high-availability/oracle-real-application-clusters.png)

Pokud dojde k chybě jedné instance, služba pokračuje na všech dalších zbývajících instancích. Každá databáze nasazená v řešení bude v konfiguraci certifikátů RAC n + 1, kde n je minimální výpočetní výkon potřebný k podpoře služby.

Služba Oracle Database Services slouží k tomu, aby umožňovala převzetí služeb při selhání mezi uzly, když instance selže transparentně. Taková selhání můžou být plánovaná nebo neplánovaná. Při práci s aplikací (rychlé události oznámení aplikací), když je instance nedostupná, je služba přesunuta do zbývajícího uzlu. Služba se přesune do uzlu zadaného v konfiguraci služby buď jako preferované, nebo k dispozici.

Další klíčovou funkcí služby Oracle Database Services je spuštění pouze služby v závislosti na její roli. Tato funkce se používá v případě, že dojde k převzetí služeb při selhání ochrany dat. Všechny vzory nasazené pomocí ochrany dat jsou potřebné k propojení databázové služby s rolí ochrany dat.

Můžete například vytvořit dvě služby, moje \_ \_ aplikace DB a moje \_ databáze \_ jako. Služba Moje \_ \_ aplikace DB se spustí, jenom když je instance databáze spuštěná s rolí Guard dat primárního. Moje \_ databáze \_ se spustí pouze v případě, že je role Guard data v \_ pohotovostním režimu. Tato konfigurace umožňuje aplikacím, aby odkazovaly na \_ službu App Service a zároveň také vytváření sestav, které se dají přesměrovat na aktivní pohotovostní režim a odkazovalo na \_ službu as.

## <a name="oracle-data-guard"></a>Oracle Data Guard

Pomocí ochrany dat můžete zachovat identickou kopii databáze na samostatném fyzickém hardwaru. V ideálním případě by měl být tento hardware geograficky odebraný z primární databáze. Ochrana dat neomezuje vzdálenost, i když vzdálenost má vliv na režimy ochrany. Zvýšená vzdálenost přidává latenci mezi lokalitami, což může způsobit, že některé možnosti (například synchronní replikace) už nebudou životaschopné.

Data Guard nabízí výhody replikace na úrovni úložiště:

- Vzhledem k tomu, že replikace pracuje s databází, se replikují pouze relevantní přenosy.
- Některé úlohy můžou generovat vysoké vstupy/výstupy v dočasných prostorech, které se v pohotovostním režimu nevyžadují a nejsou replikované.
- Ověřování replikovaných bloků probíhá v pohotovostní databázi, takže fyzické poškození primární databáze se nereplikují do pohotovostní databáze.
- Zabraňuje logickým poškozením uvnitř bloku a ztrátám při zápisu. Eliminuje také riziko chyb způsobených Správci úložiště při replikaci do úsporného režimu.
Opakování se může zpozdit na předem stanovenou dobu, takže chyby uživatelů nejsou okamžitě replikovány do úsporného režimu.

## <a name="azure-netapp-files-snapshots"></a>Snímky Azure NetApp Files

Řešení úložiště NetApp Files používané v BareMetal umožňuje vytvářet snímky svazků. Snímky umožňují rychle vrátit systém souborů k určitému bodu v čase. Technologie snímků umožňují, aby časy doby obnovení (RTO) byly zlomkem času potřebného k obnovení zálohy databáze.

Funkce snímků pro databáze Oracle je k dispozici prostřednictvím Azure NetApp SnapCenter. SnapCenter umožňuje vytvářet snímky pro zálohování, SnapVault a klonování do offline režimu umožňuje samoobslužné obnovení a další operace.

## <a name="recovery-manager"></a>Recovery Manager

Recovery Manager (RMAN) je upřednostňovaným nástrojem pro pořízení fyzických záloh databáze. RMAN spolupracuje se souborem řízení databáze (neboli centralizovaným katalogem obnovení) k ochraně různých základních součástí databáze, včetně:

- Databázové datasoubory
- Archivované protokoly opětovného provedení
- Soubory řízení databáze
- Inicializační soubory databáze (SPFile)

RMAN umožňuje vzít v úvahu horké nebo studené zálohy databáze. Tyto zálohy můžete použít k vytvoření pohotovostní databáze nebo k vytváření duplicitních databází pro klonování prostředí. RMAN má také funkci Restore Validation. Tato funkce přečte zálohovací sklad a určí, zda jej lze použít k obnovení databáze do určitého bodu v čase.

Vzhledem k tomu, že RMAN je nástroj poskytovaný Oracle, čte interní strukturu databázových souborů. Díky tomu můžete během operací zálohování a obnovení spouštět kontroly fyzických a logických poškození. Můžete také obnovit databázové datasoubory a obnovit jednotlivé datasoubory a tabulkové prostory do konkrétního bodu v čase. Jedná se o výhody, které RMAN nabízí pro snímky úložiště. Zálohování RMAN poskytuje poslední linii obrany proti plné ztrátě dat, když nemůžete použít snímky.

## <a name="next-steps"></a>Další kroky

Přečtěte si o možnostech a doporučeních pro optimalizaci ochrany a výkonu při používání Oracle v infrastruktuře BareMetal:

> [!div class="nextstepaction"]
> [Možnosti pro servery infrastruktury Oracle BareMetal](options-considerations-high-availability.md)
