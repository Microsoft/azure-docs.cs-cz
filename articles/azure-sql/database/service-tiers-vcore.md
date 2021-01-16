---
title: Přehled modelu nákupu vCore
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Model nákupu vCore vám umožňuje nezávisle škálovat výpočetní prostředky a prostředky úložiště, odpovídat místnímu výkonu a optimalizovat ceny pro Azure SQL Database a Azure SQL Managed instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 01/15/2021
ms.openlocfilehash: 6589f451d4db8f2ed77ce70a2bdfa9d76927c1e2
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251212"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>Přehled modelu vCore – Azure SQL Database a Azure SQL Managed instance 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Model nákupu Virtual Core (vCore), který používá Azure SQL Database a Azure SQL Managed instance, nabízí několik výhod:

- Vyšší omezení výpočetní kapacity, paměti, vstupně-výstupních operací a úložiště.
- Řízení vytváření hardwaru pro lepší porovnání požadavků na výpočetní a paměťové úlohy.
- Cenové slevy pro [zvýhodněné hybridní využití Azure (AHB)](../azure-hybrid-benefit.md) a [REZERVOVANOU instanci (ri)](reserved-capacity-overview.md).
- Větší transparentnost v podrobnostech o hardwaru, které vynucuje výpočetní výkon, který usnadňuje plánování migrace z místních nasazení.

## <a name="service-tiers"></a>Úrovně služby

Mezi možnosti vrstvy služeb v modelu vCore patří Pro obecné účely, Pro důležité obchodní informace a měřítko. Vrstva služeb obecně definuje architekturu úložiště, omezení místa a vstupně-výstupních operací a možnosti provozní kontinuity související s dostupností a zotavením po havárii.

|-|**Obecné použití**|**Pro důležité obchodní informace**|**Hyperškálování**|
|---|---|---|---|
|Nejvhodnější pro|Většina obchodních úloh. Nabízí uživatelsky orientované, vyvážené a škálovatelné možnosti výpočtů a úložiště. |Nabízí podnikovým aplikacím nejvyšší odolnost proti chybám pomocí několika izolovaných replik a poskytuje nejvyšší výkon vstupně-výstupních operací na jednu repliku databáze.|Většina obchodních úloh s vysokou škálovatelností úložiště a požadavky na škálování pro čtení.  Nabízí vyšší odolnost proti chybám tím, že umožňuje konfiguraci více než jedné repliky izolované databáze. |
|Úložiště|Používá vzdálené úložiště.<br/>**SQL Database zřízené výpočetní** prostředky:<br/>5 GB – 4 TB<br/>**Výpočetní** prostředí bez serveru:<br/>5 GB – 3 TB<br/>**Spravovaná instance SQL**: 32 GB až 8 TB |Používá místní úložiště SSD.<br/>**SQL Database zřízené výpočetní** prostředky:<br/>5 GB – 4 TB<br/>**Spravovaná instance SQL**:<br/>32 GB AŽ 4 TB |Flexibilní autogrow úložiště podle potřeby. Podporuje až 100 TB úložiště. Používá místní úložiště SSD pro místní mezipaměť fondu vyrovnávací paměti a místní úložiště dat. Používá vzdálené úložiště Azure jako konečné dlouhodobé úložiště dat. |
|IOPS a propustnost (přibližná)|**SQL Database**: Přečtěte si o omezeních prostředků pro izolované [databáze](resource-limits-vcore-single-databases.md) a [elastické fondy](resource-limits-vcore-elastic-pools.md).<br/>**Spravovaná instance SQL**: Přečtěte si téma [Přehled omezení prostředků spravované instance Azure SQL](../managed-instance/resource-limits.md#service-tier-characteristics).|Viz omezení prostředků pro izolované [databáze](resource-limits-vcore-single-databases.md) a [elastické fondy](resource-limits-vcore-elastic-pools.md).|Škálovatelná architektura je Vícevrstvá architektura s ukládáním do mezipaměti na více úrovních. Platnost IOPS a propustnosti budou záviset na zatížení.|
|Dostupnost|1 replika, žádné repliky na úrovni čtení|3 repliky, 1 [replika pro čtení a škálování](read-scale-out.md)<br/>zóna – redundantní vysoká dostupnost (HA)|1 replika pro čtení i zápis a 0-4 replik v režimu [čtení a škálování](read-scale-out.md)|
|Zálohování|[Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)](../../storage/common/geo-redundant-design.md), 7-35 dní (ve výchozím nastavení 7 dnů)|[RA-GRS](../..//storage/common/geo-redundant-design.md), 7-35 dní (ve výchozím nastavení 7 dnů)|Zálohování na základě snímků ve vzdáleném úložišti Azure. Obnoví použití těchto snímků pro rychlé obnovení. Zálohy jsou okamžité a neovlivňují výkon vstupně-výstupních operací ve výpočetním prostředí. Obnovení je rychlé a nejedná se o datovou operaci (trvá to jen v minutách).|
|V paměti|Nepodporováno|Podporováno|Nepodporováno|
|||


### <a name="choosing-a-service-tier"></a>Výběr úrovně služby

Informace o výběru úrovně služby pro konkrétní úlohu najdete v následujících článcích:

- [Kdy zvolit úroveň služby Pro obecné účely](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Kdy zvolit úroveň služby Pro důležité obchodní informace](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Kdy zvolit úroveň služby na úrovni služeb](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Výpočetní úrovně

Mezi možnosti výpočetní vrstvy v modelu vCore patří zřízené a výpočetní úrovně bez serveru.


### <a name="provisioned-compute"></a>Zřízené výpočetní prostředky

Zřízená výpočetní vrstva poskytuje určité množství výpočetních prostředků, které se průběžně zřídí nezávisle na aktivitě úloh a účtuje se na množství výpočetní služby za pevnou cenu za hodinu.


### <a name="serverless-compute"></a>Bezserverové výpočetní prostředí

[Výpočetní vrstva bez serveru](serverless-tier-overview.md) automaticky škáluje výpočetní prostředky na základě aktivity úloh a účtuje množství výpočetní služby za sekundu.



## <a name="hardware-generations"></a>Generace hardwaru

Možnosti generování hardwaru v modelu vCore zahrnují obecné 4/5, M-Series, Fsv2-Series a DC-Series. Generování hardwaru obecně definuje omezení výpočetních hodnot a paměti a další vlastnosti, které mají vliv na výkon úlohy.

### <a name="gen4gen5"></a>COMPUTE GEN4 –/Gen5

- COMPUTE GEN4 –/Gen5 hardware poskytuje vyvážené výpočetní a paměťové prostředky a je vhodný pro většinu databázových úloh, které nemají vyšší nároky na paměť, vyšší vCore nebo rychlejší jednoduché požadavky vCore, jak poskytují řady Fsv2-Series nebo M-Series.

Oblasti, ve kterých je COMPUTE GEN4 –/Gen5 k dispozici, najdete v tématu [dostupnost COMPUTE GEN4 –/Gen5](#gen4gen5-1).

### <a name="fsv2-series"></a>Řada Fsv2

- Fsv2-Series je hardwarově optimalizovaná možnost hardwaru, která poskytuje nízkou latenci procesoru a vysokou rychlost práce pro nejvíce náročné úlohy procesoru.
- V závislosti na zatížení může Fsv2-Series doručovat větší výkon procesoru na vCore než Gen5 a velikost vCore 72 může poskytovat větší výkon procesoru pro méně nákladů než 80 virtuální jádra v Gen5. 
- Fsv2 poskytuje méně paměti a databázi tempdb na vCore než jiný hardware, takže zatížení citlivá na tato omezení mohou místo toho brát v úvahu Gen5 nebo M-Series.  

Fsv2-Series se podporuje jenom na úrovni Pro obecné účely. Oblasti, ve kterých je Fsv2-Series k dispozici, najdete v tématu [dostupnost Fsv2-Series](#fsv2-series-1).

### <a name="m-series"></a>Řada M

- Řada M-Series je hardwarově optimalizovaná možnost hardwaru pro úlohy, které přidávají větší nároky na paměť a vyšší výpočetní limity než služba Gen5.
- Řada M-Series poskytuje 29 GB na vCore a až 128 virtuální jádra, což zvyšuje limit paměti vzhledem k Gen5, 8rychlostní až skoro 4 TB.

Řada M-Series je podporována pouze v Pro důležité obchodní informace vrstvě a nepodporuje redundanci zóny.  V oblastech, kde je dostupná řada M-Series, najdete informace v tématu [dostupnost řady m-Series](#m-series-1).

#### <a name="azure-offer-types-supported-by-m-series"></a>Typy nabídek Azure podporované v řadě M-Series

Pro přístup k řadě M-Series musí být předplatné placená typ nabídky, včetně průběžných plateb nebo smlouva Enterprise (EA).  Úplný seznam typů nabídek Azure podporovaných v řadě M-Series najdete v tématu [aktuální nabídky bez omezení útraty](https://azure.microsoft.com/support/legal/offer-details).

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="dc-series"></a>Řada DC

> [!NOTE]
> DC-Series je aktuálně ve **verzi Public Preview**.

- Hardware DC-Series používá procesory Intel s technologií Intel SGX (software Guard Extension).
- Pro [Always Encrypted s zabezpečeným enclaves](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves), která není podporovaná pro jiné konfigurace hardwaru, se vyžaduje DC-Series.
- DC-Series jsou navržené pro úlohy, které zpracovávají citlivá data a možnosti zpracování důvěrných dotazů, které poskytuje Always Encrypted s využitím zabezpečených enclaves.
- Hardware DC-Series poskytuje vyvážené výpočetní a paměťové prostředky.

DC-Series se podporují jenom pro zřízené výpočetní prostředky (bez serveru se nepodporuje) a nepodporují redundanci zóny. Oblasti, ve kterých je k dispozici DC-Series, najdete v tématu [dostupnost řadiče domény](#dc-series-1).

#### <a name="azure-offer-types-supported-by-dc-series"></a>Typy nabídek Azure podporované řadičem DC-Series

Aby bylo možné získat přístup k řadiči domény, musí být odběr placeného typu nabídky, včetně průběžných plateb nebo smlouva Enterprise (EA).  Úplný seznam typů nabídek Azure, které podporuje DC-Series, najdete v tématu [aktuální nabídky bez omezení útraty](https://azure.microsoft.com/support/legal/offer-details).

### <a name="compute-and-memory-specifications"></a>Specifikace výpočtů a paměti


|Generování hardwaru  |Výpočetní prostředky  |Memory (Paměť)  |
|:---------|:---------|:---------|
|COMPUTE GEN4 –     |– Procesory Intel® E5-2673 V3 (Haswell) 2,4 GHz<br>-Zřídit až 24 virtuální jádra (1 vCore = 1 fyzický jádro)  |– 7 GB na vCore<br>– Zřídit až 168 GB|
|Gen5     |**Zřízené výpočetní prostředky**<br>– Intel® E5-2673 v4 (Broadwell) 2,3-GHz, Intel® SP-8160 (Skylake) \* a intel® 8272CL (Cascade Lake) 2,5 GHz \*<br>-Zřídit až 80 virtuální jádra (1 vCore = 1 Hyper-thread)<br><br>**Bezserverové výpočetní prostředí**<br>-Intel® E5-2673 v4 (Broadwell) 2,3-GHz a Intel® SP-8160 (Skylake) * procesory<br>– Automatické škálování až do 40 virtuální jádra (1 vCore = 1 Hyper-thread)|**Zřízené výpočetní prostředky**<br>-5,1 GB na vCore<br>– Zřídit až 408 GB<br><br>**Bezserverové výpočetní prostředí**<br>– Automatické škálování až na 24 GB na vCore<br>– Automatické škálování až do 120 GB max.|
|Řada Fsv2     |– Procesory Intel® 8168 (Skylake)<br>– S trvalou frekvencí 3,4 GHz a maximální jednotnou rychlostí Turbo 3,7 GHz v jádře.<br>-Zřídit až 72 virtuální jádra (1 vCore = 1 Hyper-thread)|-1,9 GB na vCore<br>– Zřídit až 136 GB|
|Řada M     |– Intel® E7-8890 V3 2,5 GHz a Intel® 8280M 2,7 GHz (Cascade Lake) procesory<br>-Zřídit až 128 virtuální jádra (1 vCore = 1 Hyper-thread)|– 29 GB na vCore<br>– Zřídit až 3,7 TB|
|Řada DC     | – Procesory Intel XEON E-2288G<br>– S rozšířením Intel software Guard (Intel SGX))<br>-Zřídit až 8 virtuální jádra (1 vCore = 1 fyzický jádro) | 4,5 GB na vCore |

\* V zobrazení dynamické správy [Sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) se generování hardwaru pro databáze používající procesory Intel® SP-8160 (Skylake) zobrazuje jako Gen6, zatímco generování hardwaru pro databáze pomocí technologie Intel® 8272CL (Cascade Lake) se zobrazuje jako Gen7. Omezení prostředků pro všechny databáze Gen5 jsou stejná bez ohledu na typ procesoru (Broadwell, Skylake nebo Cascade Lake).

Další informace o omezeních prostředků najdete v tématech [omezení prostředků pro izolované databáze (Vcore)](resource-limits-vcore-single-databases.md)nebo [omezení prostředků pro elastické fondy (Vcore)](resource-limits-vcore-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Výběr hardwarového generování

V Azure Portal můžete vybrat generování hardwaru pro databázi nebo fond v SQL Database v době vytváření, nebo můžete změnit vygenerování hardwaru existující databáze nebo fondu.

**Výběr generování hardwaru při vytváření SQL Database nebo fondu**

Podrobné informace najdete v tématu [vytvoření SQL Database](single-database-create-quickstart.md).

Na kartě **základy** vyberte odkaz **Konfigurovat databázi** v části **COMPUTE + úložiště** a pak vyberte odkaz **změnit konfiguraci** :

  ![Konfigurace databáze](./media/service-tiers-vcore/configure-sql-database.png)

Vyberte požadovanou generaci hardwaru:

  ![vybrat hardware](./media/service-tiers-vcore/select-hardware.png)


**Změna hardwarového generování stávajícího SQL Database nebo fondu**

V případě databáze klikněte na stránce Přehled na odkaz **cenová úroveň** :

  ![Změna hardwaru](./media/service-tiers-vcore/change-hardware.png)

U fondu na stránce Přehled vyberte **Konfigurovat**.

Použijte postup změny konfigurace a vyberte generaci hardwaru, jak je popsáno v předchozích krocích.

**Výběr generování hardwaru při vytváření spravované instance SQL**

Podrobné informace najdete v tématu [Vytvoření spravované instance SQL](../managed-instance/instance-create-quickstart.md).

Na kartě **základy** vyberte odkaz **Konfigurovat databázi** v části **COMPUTE + úložiště** a pak vyberte požadované generování hardwaru:

  ![Konfigurace spravované instance SQL](./media/service-tiers-vcore/configure-managed-instance.png)
  
**Změna hardwarového generování existující spravované instance SQL**

# <a name="the-azure-portal"></a>[Azure Portal](#tab/azure-portal)

Na stránce spravovaná instance SQL vyberte odkaz **cenová úroveň** umístěný v části nastavení.

![Změna hardwaru spravované instance SQL](./media/service-tiers-vcore/change-managed-instance-hardware.png)

Na stránce cenová úroveň budete moci změnit generování hardwaru, jak je popsáno v předchozích krocích.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Použijte následující skript PowerShellu:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Další podrobnosti najdete v příkazu [set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) .

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Použijte následující příkaz CLI:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Další podrobnosti najdete v příkazu [AZ SQL mi Update](/cli/azure/sql/mi#az-sql-mi-update) Command.

---

### <a name="hardware-availability"></a>Dostupnost hardwaru

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a> COMPUTE GEN4 –/Gen5

COMPUTE GEN4 – hardware se [rozchází a už není k](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) dispozici pro nová nasazení. Všechny nové databáze musí být nasazeny na Gen5 hardwaru.

Gen5 je k dispozici ve většině oblastí po celém světě.

#### <a name="fsv2-series"></a>Řada Fsv2

Fsv2-Series je k dispozici v následujících oblastech: Austrálie – střed, Austrálie – střed, Austrálie – jihovýchod, Brazílie – jih, Kanada – Východní Asie střed, USA – východ, Francie – střed, Indie – střed, Korea – střed, Korea – Severní Evropa jih, USA – střed, USA – sever, jihovýchodní Asie, Velká Británie – jih, Velká Británie – západ, Západní Evropa, západ USA 2.


#### <a name="m-series"></a>Řada M

Řada M-Series je k dispozici v následujících oblastech: Východní USA, Severní Evropa, Západní Evropa Západní USA 2.
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

#### <a name="dc-series"></a>Řada DC

> [!NOTE]
> DC-Series je aktuálně ve **verzi Public Preview**.

DC-Series jsou k dispozici v následujících oblastech: Kanada – střed, Kanada – východ, Východní USA, Severní Evropa, Velká Británie – jih, Západní Evropa Západní USA.

Pokud v aktuálně nepodporované oblasti potřebujete DC-Series, [odešlete lístek podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) podle pokynů v tématu [kvóta žádostí se zvýší na Azure SQL Database a SQL Managed instance](quota-increase-request.md).

## <a name="next-steps"></a>Další kroky

Informace o tom, jak začít, najdete v těchto tématech: 
- [Vytvoření SQL Database pomocí Azure Portal](single-database-create-quickstart.md)
- [Vytvoření spravované instance SQL pomocí Azure Portal](../managed-instance/instance-create-quickstart.md)

Podrobnosti o cenách najdete na [stránce s cenami Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/).

Podrobnosti o specifických úložištích a velikostech úložiště dostupných v úrovních služeb pro obecné účely a důležité pro firmy najdete v těchto tématech:

- [omezení prostředků založené na Vcore pro Azure SQL Database](resource-limits-vcore-single-databases.md).
- [omezení prostředků založené na Vcore pro fond Azure SQL Database](resource-limits-vcore-elastic-pools.md).
- [omezení prostředků založené na Vcore pro spravovanou instanci Azure SQL](../managed-instance/resource-limits.md)