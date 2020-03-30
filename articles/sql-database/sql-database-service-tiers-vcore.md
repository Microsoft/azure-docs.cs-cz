---
title: Přehled modelů virtuálních jader
description: Nákupní model virtuálních jader umožňuje nezávisle škálovat výpočetní prostředky a prostředky úložiště, odpovídat místnímu výkonu a optimalizovat cenu.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: 5fd69dcd30292630862887ab5434764ba377b396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481078"
---
# <a name="vcore-model-overview"></a>Přehled modelů virtuálních jader

Model virtuálního jádra (vCore) poskytuje několik výhod:

- Vyšší výpočetní, paměťové, vodicí a úložné limity.
- Kontrola nad generování hardwaru, aby lépe odpovídaly výpočetní a požadavky na paměť zatížení.
- Cenové slevy pro [hybridní výhody Azure (AHB)](sql-database-azure-hybrid-benefit.md) a [rezervované instance (RI)](sql-database-reserved-capacity.md).
- Větší transparentnost v hardwarových detailech, které pohánějí výpočetní výkon; usnadňuje plánování migrace z místních nasazení.

## <a name="service-tiers"></a>Úrovně služby

Možnosti úrovně služeb v modelu virtuálních jader zahrnují obecné účely, důležité obchodní záležitosti a hyperškálování. Úroveň služby obecně definuje architekturu úložiště, omezení prostoru a vstupně-věcí a možnosti kontinuity provozu související s dostupností a zotavením po havárii.

||**Obecné účely**|**Důležité obchodní**|**Hyperškálování**|
|---|---|---|---|
|Nejvhodnější pro|Většina obchodních úloh. Nabízí možnosti výpočetních prostředků a úložiště orientované na rozpočet, vyvážené a škálovatelné výpočetní prostředky a úložiště. |Nabízí obchodní aplikace nejvyšší odolnost proti selhání pomocí několika izolovaných replik a poskytuje nejvyšší výkon vstupně-v.i. na repliku databáze.|Většina obchodních úloh s vysoce škálovatelnými požadavky na úložiště a škálování pro čtení.  Nabízí vyšší odolnost proti selhání povolením konfigurace více než jedné repliky izolované databáze. |
|Úložiště|Používá vzdálené úložiště.<br/>**Jednotlivé databáze a elastické fondy zřízené výpočetní prostředky**:<br/>5 GB – 4 TB<br/>**Výpočetní výkon bez serveru**:<br/>5 GB – 3 TB<br/>**Spravovaná instance:** 32 GB – 8 TB |Používá místní úložiště SSD.<br/>**Jednotlivé databáze a elastické fondy zřízené výpočetní prostředky**:<br/>5 GB – 4 TB<br/>**Spravovaná instance**:<br/>32 GB – 4 TB |Flexibilní automatické dospívání úložiště podle potřeby. Podporuje až 100 TB úložného prostoru. Používá místní úložiště SSD pro místní vyrovnávací paměť a místní úložiště dat. Používá vzdálené úložiště Azure jako konečné dlouhodobé úložiště dat. |
|IOPS a propustnost (přibližná)|**Jednotné databáze a elastické fondy**: Viz omezení prostředků pro [jednotlivé databáze](../sql-database/sql-database-vcore-resource-limits-single-databases.md) a [elastické fondy](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md).<br/>**Spravovaná instance**: Viz [Přehled Omezení prostředků spravovaných instancí Azure SQL Database](../sql-database/sql-database-managed-instance-resource-limits.md#service-tier-characteristics).|Viz omezení prostředků pro [jednotlivé databáze](../sql-database/sql-database-vcore-resource-limits-single-databases.md) a [elastické fondy](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md).|Hyperscale je vícevrstvá architektura s ukládáním do mezipaměti na více úrovních. Efektivní vipoje a propustnost bude záviset na zatížení.|
|Dostupnost|1 replika, žádné repliky v měřítku pro čtení|3 repliky, 1 [replika v měřítku čtení](sql-database-read-scale-out.md),<br/>zónově redundantní vysoká dostupnost (HA)|1 replika pro čtení a zápis a [repliky v měřítku](sql-database-read-scale-out.md) 0-4|
|Zálohování|[Geograficky redundantní úložiště pro čtení (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dní (ve výchozím nastavení 7 dní)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dní (ve výchozím nastavení 7 dní)|Zálohy založené na snímcích ve vzdáleném úložišti Azure. Obnoví použít tyto snímky pro rychlé obnovení. Zálohy jsou okamžité a nemají vliv na výkon vstupně-videa výpočetních prostředků. Obnovení jsou rychlá a nejsou operace velikosti dat (trvá minuty spíše než hodiny nebo dny).|
|V paměti|Nepodporuje se|Podporuje se|Nepodporuje se|
|||


### <a name="choosing-a-service-tier"></a>Výběr úrovně služby

Informace o výběru úrovně služby pro konkrétní úlohu naleznete v následujících článcích:

- [Kdy zvolit úroveň služby pro všeobecné účely](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Kdy zvolit úroveň služeb Business Critical](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Kdy zvolit úroveň služby Hyperscale](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Výpočetní vrstvy

Možnosti výpočetní vrstvy v modelu virtuálních jader zahrnují zřízené a bezserverové výpočetní vrstvy.


### <a name="provisioned-compute"></a>Zřízené výpočetní prostředky

Zzřízená výpočetní vrstva poskytuje konkrétní množství výpočetních prostředků, které jsou nepřetržitě zřízené nezávisle na aktivitě pracovního vytížení a účty za částku výpočetních prostředků zřízených za pevnou cenu za hodinu.


### <a name="serverless-compute"></a>Bezserverové výpočetní prostředí

[Výpočetní úroveň bez serveru](sql-database-serverless.md) automaticky škáluje výpočetní prostředky na základě aktivity pracovního vytížení a účty za množství výpočetních prostředků použitých za sekundu.



## <a name="hardware-generations"></a>Generace hardwaru

Možnosti generování hardwaru v modelu virtuálního jádra zahrnují řady Gen 4/5, řady M (náhled) a řady Fsv2 (náhled). Generování hardwaru obecně definuje výpočetní a paměťové limity a další charakteristiky, které ovlivňují výkon úlohy.

### <a name="gen4gen5"></a>Gen4/Gen5

- Gen4/Gen5 hardware poskytuje vyvážené výpočetní a paměťové prostředky a je vhodný pro většinu databázových úloh, které nemají vyšší paměť, vyšší virtuální jádro nebo rychlejší požadavky na jedno virtuální jádro, jak je k dispozici řady Fsv2 nebo M-series.

V oblastech, kde je k dispozici Gen4/Gen5, viz [dostupnost Gen4/Gen5](#gen4gen5-1).

### <a name="fsv2-seriespreview"></a>Řada Fsv2 (náhled)

- Řada Fsv2 je hardwarová možnost optimalizovaná pro výpočetní výkon, která poskytuje nízkou latenci procesoru a vysokou rychlost hodin pro nejnáročnější úlohy procesoru.
- V závislosti na zatížení fsv2-series může přinést vyšší výkon procesoru na virtuální jádro než Gen5 a velikost 72 virtuálních jader může poskytnout vyšší výkon procesoru za nižší náklady než 80 virtuálních jader na Gen5. 
- Fsv2 poskytuje méně paměti a tempdb na virtuální jádro než jiný hardware, takže úlohy citlivé na tato omezení mohou chtít zvážit gen5 nebo řadu M. místo toho.  

Řada Fsv2 je podporována pouze ve vrstvě Pro obecné účely.  Informace o oblastech, kde je k dispozici řada Fsv2, naleznete v [tématu dostupnost řady Fsv2](#fsv2-series).


### <a name="m-seriespreview"></a>Řada M (náhled)

- Řada M je hardwarová možnost optimalizovaná pro paměť pro úlohy vyžadující více paměti a vyšší výpočetní limity, než poskytuje Gen5.
- Řada M poskytuje 29 GB na virtuální jádro a 128 virtuálních jader, což zvyšuje limit paměti vzhledem k Gen5 o 8x na téměř 4 TB.

Řada M je podporována pouze ve vrstvě Business Critical a nepodporuje redundanci zóny.

Chcete-li povolit hardware řady M pro předplatné a oblast, je nutné otevřít žádost o podporu. Předplatné musí být typ placené nabídky včetně průběžných plateb nebo smlouvy Enterprise (EA).  Pokud je žádost o podporu schválena, pak výběr a zřizování prostředí řady M následuje stejný vzor jako pro jiné generace hardwaru. Pro oblasti, kde je k dispozici řada M, naleznete [v tématu dostupnost řady M](#m-series).


### <a name="compute-and-memory-specifications"></a>Specifikace výpočetních prostředků a paměti


|Generování hardwaru  |Služba Compute  |Memory (Paměť)  |
|:---------|:---------|:---------|
|Gen4 (Gen4)     |- Procesory Intel E5-2673 v3 (Haswell) 2,4 GHz<br>- Zřízení až 24 virtuálních jader (1 virtuální jádro = 1 fyzické jádro)  |- 7 GB na virtuální jádro<br>- Zajištění do 168 GB|
|Gen5 (Gen5)     |**Zřízené výpočetní prostředky**<br>- Procesory Intel E5-2673 v4 (Broadwell) 2,3 GHz a Intel SP-8160 (Skylake)*<br>- Zřízení až 80 virtuálních jader (1 vCore = 1 hyper-závit)<br><br>**Bezserverové výpočetní prostředí**<br>- Procesory Intel E5-2673 v4 (Broadwell) 2,3 GHz a Intel SP-8160 (Skylake)*<br>- Automatické škálování až na 16 virtuálních jader (1 vCore = 1 hyper-thread)|**Zřízené výpočetní prostředky**<br>- 5,1 GB na virtuální jádro<br>- Zajištění do 408 GB<br><br>**Bezserverové výpočetní prostředí**<br>- Automatické škálování až 24 GB na virtuální jádro<br>- Automatické škálování až do 48 GB max|
|Řada Fsv2     |- Procesory Intel Xeon Platinum 8168 (SkyLake)<br>- S trvalou všechny základní turbo taktovací rychlost 3,4 GHz a maximální jednojádrové turbo takčata rychlost 3,7 GHz.<br>- Zřízení 72 virtuálních jader (1 vCore = 1 hyper-vlákno)|- 1,9 GB na virtuální jádro<br>- Rezerva 136 GB|
|Řada M     |- Procesory Intel Xeon E7-8890 v3 2,5 GHz<br>- Zřízení 128 virtuálních jader (1 vCore = 1 hyper-vlákno)|- 29 GB na virtuální jádro<br>- Ustanovení 3,7 TB|

\*V zobrazení dynamické správy [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) se generování hardwaru pro databáze Gen5 pomocí procesorů Intel SP-8160 (Skylake) zobrazuje jako Gen6. Omezení prostředků pro všechny databáze Gen5 jsou stejné bez ohledu na typ procesoru (Broadwell nebo Skylake).

Další informace o omezení prostředků naleznete v tématu [omezení prostředků pro jednotlivé databáze (vCore)](sql-database-vcore-resource-limits-single-databases.md)nebo omezení prostředků pro [elastické fondy (vCore).](sql-database-vcore-resource-limits-elastic-pools.md)

### <a name="selecting-a-hardware-generation"></a>Výběr generace hardwaru

Na webu Azure Portal můžete vybrat generování hardwaru pro databázi NEBO fond SQL v době vytvoření nebo můžete změnit generování hardwaru existující databáze NEBO fondu SQL.

**Výběr generování hardwaru při vytváření databáze nebo fondu SQL**

Podrobné informace naleznete [v tématu Vytvoření databáze SQL](sql-database-single-database-get-started.md).

Na kartě **Základy** vyberte v části **Výpočetní + úložiště** odkaz Konfigurovat **databázi** a pak vyberte odkaz **Změnit konfiguraci:**

  ![Konfigurace databáze](media/sql-database-service-tiers-vcore/configure-sql-database.png)

Vyberte požadovanou generaci hardwaru:

  ![vybrat hardware](media/sql-database-service-tiers-vcore/select-hardware.png)


**Změna generování hardwaru existující databáze nebo fondu SQL**

Pro databázi vyberte na stránce Přehled odkaz **Cenová úroveň:**

  ![změna hardwaru](media/sql-database-service-tiers-vcore/change-hardware.png)

U fondu vyberte na stránce Přehled **možnost Konfigurovat**.

Podle pokynů změňte konfiguraci a vyberte generování hardwaru, jak je popsáno v předchozích krocích.

**Výběr generování hardwaru při vytváření spravované instance**

Podrobné informace naleznete v [tématu Vytvoření spravované instance](sql-database-managed-instance-get-started.md).

Na kartě **Základy** vyberte v části **Výpočetní + úložiště** odkaz Konfigurovat **databázi** a pak vyberte požadované generování hardwaru:

  ![konfigurace spravované instance](media/sql-database-service-tiers-vcore/configure-managed-instance.png)
  
**Změna generování hardwaru existující spravované instance**

# <a name="portal"></a>[Portál](#tab/azure-portal)

Na stránce spravované instance vyberte odkaz **Cenová úroveň** umístěný v části Nastavení.

![změna hardwaru spravované instance](media/sql-database-service-tiers-vcore/change-managed-instance-hardware.png)

Na stránce **Cenové úrovně** budete moci změnit generování hardwaru, jak je popsáno v předchozích krocích.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Použijte následující skript PowerShellu:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Další podrobnosti najdete v [příkazu Set-AzSqlInstance.](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Použijte následující příkaz příkazového příkazu příkazu k příkazu:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Pro více informací zkontrolujte [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update) příkaz.

---

### <a name="hardware-availability"></a>Dostupnost hardwaru

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a>Gen4/Gen5

Gen4 hardware je [postupně ukončován](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) a již není k dispozici pro nová nasazení. Všechny nové databáze musí být nasazeny na hardwaru Gen5.

Gen5 je k dispozici ve většině oblastí po celém světě.

#### <a name="fsv2-series"></a>Řada Fsv2

Řada Fsv2 je k dispozici v následujících oblastech: Austrálie – střed, Austrálie – střed 2, Austrálie – východ, Austrálie – jihovýchod, Brazílie – jih, Kanada – střed, Východní Asie, Východní Usa, Francie – střed, Indie – střed, Indie – západ, Korea – střed, Korea – jih, sever Evropa, Jižní Afrika Sever, Jihovýchodní Asie, Velká Británie Jih, Velká Británie – západ, Západní Evropa, Západní USA 2.


#### <a name="m-series"></a>Řada M

Řada M je k dispozici v následujících oblastech: Východní USA, Severní Evropa, Západní Evropa, Západní USA 2.
Řada M může mít také omezenou dostupnost v dalších oblastech. Můžete požádat o jinou oblast, než je uvedeno zde, ale plnění v jiné oblasti nemusí být možné.

Chcete-li povolit dostupnost řady M v předplatném, je nutné požádat o přístup [podáním nové žádosti o podporu](#create-a-support-request-to-enable-m-series).


##### <a name="create-a-support-request-to-enable-m-series"></a>Vytvořte žádost o podporu pro povolení řady M: 

1. Vyberte **Nápověda + podpora** na portálu.
2. Vyberte **nový požadavek na podporu**.

Na stránce **Základy** zadejte následující:

1. V **případě typu Problém**vyberte možnost Omezení služeb a předplatného **(kvóty).**
2. Pro **předplatné** = vyberte předplatné povolit M-series.
3. V **části Kvóta**vyberte **databázi SQL**.
4. Výběrem **možnosti Další** přejdete na stránku **Podrobnosti.**

Na stránce **Podrobnosti** zadejte následující:

1. V části **PODROBNOSTI O PROBLÉMU** vyberte odkaz **Poskytnout podrobnosti.** 
2. Pro **typ kvóty databáze SQL** vyberte řadu **M**.
3. V **části Oblast**vyberte oblast, která umožní řadu M.
    Pro oblasti, kde je k dispozici řada M, naleznete [v tématu dostupnost řady M](#m-series).

Schválené žádosti o podporu jsou obvykle splněny do 5 pracovních dnů.


## <a name="next-steps"></a>Další kroky

- Pokud chcete vytvořit databázi SQL, [přečtěte si informace o vytvoření databáze SQL pomocí portálu Azure](sql-database-single-database-get-started.md).
- Konkrétní velikosti výpočetních prostředků a možnosti velikosti úložiště, které jsou k dispozici pro jednotlivé databáze, naleznete [v tématu limity prostředků založených na virtuálních jádrech SQL Database pro jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md).
- Konkrétní výpočetní velikosti a možnosti velikosti úložiště, které jsou k dispozici pro elastické fondy, najdete v [tématu limity prostředků založených na virtuálních jádrech SQL Database pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).
- Podrobnosti o cenách najdete na [stránce s cenami databáze Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/single/).
