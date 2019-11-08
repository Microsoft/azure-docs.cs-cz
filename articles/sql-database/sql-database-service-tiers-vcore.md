---
title: Přehled modelu vCore
description: Model nákupu vCore vám umožňuje nezávisle škálovat výpočetní prostředky a prostředky úložiště, odpovídat místnímu výkonu a optimalizovat ceny.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/04/2019
ms.openlocfilehash: b9de02bf0836727ac88b78194641238621e87a79
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821065"
---
# <a name="vcore-model-overview"></a>Přehled modelu vCore

Model Virtual Core (vCore) poskytuje několik výhod:

- Vyšší omezení výpočetních prostředků, paměti, vstupně-výstupních operací a úložiště.
- Řízení vytváření hardwaru pro lepší porovnání požadavků na výpočetní a paměťové úlohy.
- Cenové slevy pro [zvýhodněné hybridní využití Azure (AHB)](sql-database-azure-hybrid-benefit.md) a [REZERVOVANOU instanci (ri)](sql-database-reserved-capacity.md).
- Větší transparentnost v podrobnostech o hardwaru, které vysílaly výpočetní výkon; usnadňuje plánování migrací z místních nasazení.

## <a name="service-tiers"></a>Úrovně služby

Mezi možnosti vrstvy služeb v modelu vCore patří Pro obecné účely, Pro důležité obchodní informace a měřítko. Vrstva služeb obecně definuje architekturu úložiště, omezení místa a vstupně-výstupních operací a možnosti provozní kontinuity související s dostupností a zotavením po havárii.

||**Obecné účely**|**Důležité pro podnikání**|**Hyperškálovatelný**|
|---|---|---|---|
|Nejvhodnější pro|Většina obchodních úloh. Nabízí uživatelsky orientované, vyvážené a škálovatelné možnosti výpočtů a úložiště. |Nabízí podnikovým aplikacím nejvyšší odolnost proti chybám pomocí několika izolovaných replik a poskytuje nejvyšší výkon vstupně-výstupních operací na jednu repliku databáze.|Většina obchodních úloh s vysokou škálovatelností úložiště a požadavky na škálování pro čtení.  Nabízí vyšší odolnost proti chybám tím, že umožňuje konfiguraci více než jedné repliky izolované databáze. |
|Storage|Používá vzdálené úložiště.<br/>Izolovaná **databáze a elastický fond zřízený COMPUTE**:<br/>5 GB – 4 TB<br/>**Výpočetní**prostředí bez serveru:<br/>5 GB – 3 TB<br/>**Spravovaná instance**: 32 GB až 8 TB |Používá místní úložiště SSD.<br/>Izolovaná **databáze a elastický fond zřízený COMPUTE**:<br/>5 GB – 8 TB<br/>**Spravovaná instance**:<br/>32 GB AŽ 4 TB |Flexibilní autogrow úložiště podle potřeby. Podporuje až 100 TB úložiště. Používá místní úložiště SSD pro místní mezipaměť fondu vyrovnávací paměti a místní úložiště dat. Používá vzdálené úložiště Azure jako konečné dlouhodobé úložiště dat. |
|Propustnost vstupně-výstupních operací (přibližná)|Izolovaná **databáze a elastický fond**: 500 vstupně-výstupních operací na vCore až 40000 maximálních IOPS.<br/>**Spravovaná instance**: závisí na [velikosti souboru](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes).|5000 vstupně-výstupních operací na vCore až 320 000 maximální IOPS|Škálovatelná architektura je Vícevrstvá architektura s ukládáním do mezipaměti na více úrovních. Platnost IOPs bude záviset na zatížení.|
|Dostupnost|1 replika, žádné repliky na úrovni čtení|3 repliky, 1 [replika pro čtení a škálování](sql-database-read-scale-out.md)<br/>zóna – redundantní vysoká dostupnost (HA)|1 replika pro čtení i zápis a 0-4 replik v režimu [čtení a škálování](sql-database-read-scale-out.md)|
|Zálohování|[Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dní (ve výchozím nastavení 7 dnů)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dní (ve výchozím nastavení 7 dnů)|Zálohování na základě snímků ve vzdáleném úložišti Azure. Obnoví použití těchto snímků pro rychlé obnovení. Zálohy jsou okamžité a neovlivňují výkon vstupně-výstupních operací ve výpočetním prostředí. Obnovení je rychlé a nejedná se o datovou operaci (trvá to jen v minutách).|
|V paměti|Nepodporuje se|Podporuje se|Nepodporuje se|
|||


### <a name="choosing-a-service-tier"></a>Výběr úrovně služby

Informace o výběru úrovně služby pro konkrétní úlohu najdete v následujících článcích:

- [Kdy zvolit úroveň služby pro obecné účely](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Kdy zvolit úroveň služby Pro důležité obchodní informace](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Kdy zvolit úroveň služby na úrovni služeb](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Výpočetní úrovně

Mezi možnosti výpočetní vrstvy v modelu vCore patří zřízené a výpočetní úrovně bez serveru.


### <a name="provisioned-compute"></a>Zřízené výpočetní prostředky

Zřízená výpočetní vrstva poskytuje určité množství výpočetních prostředků, které se průběžně zřídí nezávisle na aktivitě úloh a účtuje se na množství výpočetní služby za pevnou cenu za hodinu.


### <a name="serverless-compute"></a>Bezserverové výpočetní prostředí

[Výpočetní vrstva bez serveru](sql-database-serverless.md) automaticky škáluje výpočetní prostředky na základě aktivity úloh a účtuje množství výpočetní služby za sekundu.



## <a name="hardware-generations"></a>Generace hardwaru

Mezi možnosti generování hardwaru v modelu vCore patří obecné 4/5, M-Series (Preview) a Fsv2-Series (Preview). Generování hardwaru obecně definuje omezení výpočetních hodnot a paměti a další vlastnosti, které mají vliv na výkon úlohy.

### <a name="gen4gen5"></a>COMPUTE GEN4 –/Gen5

- COMPUTE GEN4 –/Gen5 hardware poskytuje vyvážené výpočetní a paměťové prostředky a je vhodný pro většinu databázových úloh, které nemají vyšší nároky na paměť, vyšší vCore nebo rychlejší jednoduché požadavky vCore, jak poskytují řady Fsv2-Series nebo M-Series.

Oblasti, ve kterých je COMPUTE GEN4 –/Gen5 k dispozici, najdete v tématu [dostupnost COMPUTE GEN4 –/Gen5](#gen4gen5-1).

### <a name="fsv2-seriespreview"></a>Fsv2-Series (Preview)

- Fsv2-Series je hardwarově optimalizovaná možnost hardwaru, která poskytuje nízkou latenci procesoru a vysokou rychlost práce pro nejvíce náročné úlohy procesoru.
- V závislosti na zatížení může Fsv2-Series doručovat větší výkon procesoru na vCore než Gen5 a velikost vCore 72 může poskytovat větší výkon procesoru pro méně nákladů než 80 virtuální jádra v Gen5. 
- Fsv2 poskytuje méně paměti a databázi tempdb na vCore než jiný hardware, takže zatížení citlivá na tato omezení mohou místo toho brát v úvahu Gen5 nebo M-Series.  

Oblasti, ve kterých je Fsv2-Series k dispozici, najdete v tématu [dostupnost Fsv2-Series](#fsv2-series).


### <a name="m-seriespreview"></a>Řada M-Series (Preview)

- Řada M-Series je hardwarově optimalizovaná možnost hardwaru pro úlohy, které přidávají větší nároky na paměť a vyšší výpočetní limity než služba Gen5.
- Řada M-Series poskytuje 29 GB na vCore a 128 virtuální jádra, což zvyšuje limit paměti relativní vzhledem k Gen5, 8rychlostní až skoro 4 TB.

Pokud chcete povolit hardware řady M-Series pro předplatné a oblast, je nutné otevřít žádost o podporu. Pokud je žádost o podporu schválená, možnosti výběru a zřizování řady M-Series se řídí stejným vzorem jako u ostatních generací hardwaru. V oblastech, kde je dostupná řada M-Series, najdete informace v tématu [dostupnost řady m-Series](#m-series).


### <a name="compute-and-memory-specifications"></a>Specifikace výpočtů a paměti


|Generování hardwaru  |Compute  |Memory (Paměť)  |
|:---------|:---------|:---------|
|COMPUTE GEN4 –     |– Procesory Intel E5-2673 V3 (Haswell) 2,4 GHz<br>-Zřídit až 24 virtuální jádra (1 vCore = 1 fyzický jádro)  |– 7 GB na vCore<br>– Zřídit až 168 GB|
|Gen5     |**Zřízené výpočetní prostředky**<br>– Procesory Intel E5-2673 v4 (Broadwell) 2,3 GHz<br>-Zřídit až 80 virtuální jádra (1 vCore = 1 Hyper-thread)<br><br>**Výpočetní prostředí bez serveru**<br>– Procesory Intel E5-2673 v4 (Broadwell) 2,3 GHz<br>– Automatické škálování až na 16 virtuální jádra (1 vCore = 1 Hyper-thread)|**Zřízené výpočetní prostředky**<br>-5,1 GB na vCore<br>– Zřídit až 408 GB<br><br>**Výpočetní prostředí bez serveru**<br>– Automatické škálování až na 24 GB na vCore<br>– Automatické škálování až do 48 GB max.|
|Fsv2-series     |– Procesory Intel Xeon Platinum 8168 (SkyLake)<br>– S trvalou frekvencí 3,4 GHz a maximální jednotnou rychlostí Turbo 3,7 GHz v jádře.<br>-Zřídit 72 virtuální jádra (1 vCore = 1 = 1 Hyper-thread)|-1,9 GB na vCore<br>-Zřizování 136 GB|
|M-Series     |– Procesory Intel Xeon E7-8890 V3 2,5 GHz<br>-Zřídit 128 virtuální jádra (1 vCore = 1 = 1 Hyper-thread)|– 29 GB na vCore<br>-Zřizování 3,7 TB|


Další informace o omezeních prostředků najdete v tématech [omezení prostředků pro izolované databáze (Vcore)](sql-database-vcore-resource-limits-single-databases.md)nebo [omezení prostředků pro elastické fondy (Vcore)](sql-database-vcore-resource-limits-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Výběr hardwarového generování

V Azure Portal můžete vybrat generování hardwaru pro databázi nebo fond SQL v době vytváření, nebo můžete změnit vygenerování hardwaru existující databáze nebo fondu SQL.

**Výběr generování hardwaru při vytváření databáze nebo fondu SQL**

Podrobné informace najdete v tématu [Vytvoření databáze SQL](sql-database-single-database-get-started.md).

Na kartě **základy** vyberte odkaz **Konfigurovat databázi** v části **COMPUTE + úložiště** a pak vyberte odkaz **změnit konfiguraci** :

  ![Konfigurace databáze](media/sql-database-service-tiers-vcore/configure-sql-database.png)

Vyberte požadovanou generaci hardwaru:

  ![vybrat hardware](media/sql-database-service-tiers-vcore/select-hardware.png)


**Změna hardwarového generování existující databáze nebo fondu SQL**

V případě databáze klikněte na stránce Přehled na odkaz **cenová úroveň** :

  ![Změna hardwaru](media/sql-database-service-tiers-vcore/change-hardware.png)

U fondu na stránce Přehled vyberte **Konfigurovat**.

Použijte postup změny konfigurace a vyberte generaci hardwaru, jak je popsáno v předchozích krocích.

### <a name="hardware-availability"></a>Dostupnost hardwaru

#### <a name="gen4gen5"></a>COMPUTE GEN4 –/Gen5

Nové databáze COMPUTE GEN4 – již nejsou podporovány v oblastech Austrálie – východ a Brazílie – jih. 

Gen5 je k dispozici ve většině oblastí po celém světě.

#### <a name="fsv2-series"></a>Fsv2-series

Fsv2-Series je k dispozici v následujících oblastech: Austrálie – střed, Austrálie – střed, Austrálie – jihovýchod, Brazílie – jih, Kanada – střed Východní Asie, USA – střed, Severní Korea, Indie – střed, Indie – západ, Indie – jih, USA – sever Evropa, Jižní Afrika, USA – sever, jihovýchodní Asie, Velká Británie – jih, Velká Británie – západ, Západní Evropa, západ USA 2.


#### <a name="m-series"></a>M-Series

Řada M-Series je k dispozici v následujících oblastech: Východní USA, Severní Evropa, Západní Evropa Západní USA 2.
Řada M-Series může mít také omezené dostupnosti v dalších oblastech. Můžete si vyžádat jinou oblast, než je zde uvedeno, ale nemusí být splněna v jiné oblasti.

Pokud chcete povolit dostupnost řady M-Series v rámci předplatného, musíte požádat o přístup k [nové žádosti o podporu](#create-a-support-request-to-enable-m-series).


##### <a name="create-a-support-request-to-enable-m-series"></a>Vytvoření žádosti o podporu pro povolení řady M-Series: 

1. Na portálu vyberte **help + podpora** .
2. Vyberte **Nová žádost o podporu**.

Na stránce **základy** zadejte následující:

1. Jako **typ problému**vyberte **omezení služby a předplatné (kvóty)** .
2. Pro **předplatné** = vyberte předplatné, abyste mohli povolit řadu M-Series.
3. Jako **typ kvóty**vyberte **SQL Database**.
4. Kliknutím na tlačítko **Další** přejdete na stránku s **podrobnostmi** .

Na stránce **Podrobnosti** zadejte následující:

5. V části **Podrobnosti o problému** vyberte odkaz **poskytnout podrobnosti** . 
6. U **SQL Database typ kvóty** vyberte **M-Series**.
7. V poli **oblast**vyberte oblast, do které chcete povolit řadu M-Series.
    V oblastech, kde je dostupná řada M-Series, najdete informace v tématu [dostupnost řady m-Series](#m-series).

Schválené žádosti o podporu jsou obvykle splněné do 5 pracovních dnů.


## <a name="next-steps"></a>Další kroky

- Chcete-li vytvořit databázi SQL, přečtěte si téma [Vytvoření databáze SQL pomocí Azure Portal](sql-database-single-database-get-started.md).
- Konkrétní velikosti a možnosti velikosti úložiště, které jsou dostupné pro jednotlivé databáze, najdete v tématu [SQL Database omezení prostředků na základě Vcore pro jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md).
- Konkrétní velikosti a možnosti velikosti úložiště dostupné pro elastické fondy najdete v tématu [SQL Database omezení prostředků na základě Vcore pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).
- Podrobnosti o cenách najdete na [stránce s cenami Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/).
