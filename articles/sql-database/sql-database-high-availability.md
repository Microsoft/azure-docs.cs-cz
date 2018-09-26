---
title: Vysoká dostupnost – služba Azure SQL Database | Dokumentace Microsoftu
description: Další informace o možnosti vysoké dostupnosti služby Azure SQL Database a funkcí
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, sashan
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: 9c06a028df098874a1ec12d83a362e01a5f4a711
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161892"
---
# <a name="high-availability-and-azure-sql-database"></a>Vysoká dostupnost a Azure SQL Database

Azure SQL Database je vysoce dostupné databáze platforma jako služba, která zaručuje, že je vaše databáze a spuštěné 99,99 % času, ale nemusíme se starat o údržbu a výpadky. Toto je plně spravovaný proces databázový stroj SQL serveru hostované v cloudu Azure, který zajistí, že databáze systému SQL Server je vždy upgradovat nebo opravit bez ovlivnění vašich úloh. Když instance je opravit nebo převezme služby při selhání, výpadek není obecně souborům Pokud jste [použít logiku opakování](sql-database-develop-overview.md#resiliency) ve vaší aplikaci. Pokud čas k dokončení převzetí služeb při selhání je delší než 60 sekund, by měla otevřít případ podpory. Azure SQL Database můžete rychle obnovit i v těch nejdůležitějších okolností zajistit, že vaše data budou vždy k dispozici.

Platforma Azure plně spravuje každou databází SQL Azure a garantuje ke ztrátě dat a vysoké procento dostupnosti údajů. Použití dílčích oprav, zálohování, replikace, detekce chyb, základní potenciální hardwaru, selhání softwaru nebo sítě, opravy chyb nasazení, převzetí služeb při selhání, upgrade databáze a další úlohy údržby automaticky postará Azure. Systému SQL Server technici implementovali nejznámějších postupy zajistit, že všechny operace údržby jsou dokončeny za méně než 0,01 % dobu životnosti vaší databáze. Tato architektura je navržená tak, aby potvrzených dat se nikdy ztraceny a aby byly provedeny operace údržby bez ovlivnění pracovního vytížení. Neexistují žádné časová období údržby nebo prostoje, které by měl vyžadují, abyste k zastavení úlohy a databáze je upgradovat nebo udržovat. Integrovaná vysoká dostupnost ve službě Azure SQL Database zaručuje, že databáze nebude nikdy jediným bodem selhání v softwarové architektuře.

Azure SQL Database je založené na architektuře databázový stroj SQL serveru, která je upravená pro cloudové prostředí, aby bylo možné zajistit 99,99 % dostupnost i v případě selhání infrastruktury. Existují dva modely architektury vysoké dostupnosti, které se používají ve službě Azure SQL Database (obou z nich zajišťující 99,99 % dostupnost):
- Model vrstev service/standard pro obecné účely, který je založen na oddělení výpočetního výkonu a úložiště. Tento Architektonický model spoléhá na vysokou dostupnost a spolehlivost vrstvy úložiště, ale může mít některé potenciální snížení výkonu během údržby nevyžadovala.
- Model vrstev kritické úrovně Premium/firemní službu, která je založena na cluster procesy modulu databáze. Tento Architektonický model závisí na skutečnost, že existuje, bude vždy Kvorum uzlů k dispozici databáze modul a má minimální vliv zatížení vliv na vaši sadu funkcí i během údržby nevyžadovala.

Azure upgradů a oprav základního operačního systému, ovladačů a databázový stroj SQL serveru transparentně s minimálními výpadkům pro koncové uživatele. Azure SQL Database běží na nejnovější stabilní verze databázového stroje SQL Server a operační systém Windows a většinu uživatelů by Všimněte si, že je aktualizován se provádí nepřetržitě.

## <a name="standardgeneral-purpose-availability"></a>Dostupnost/standard pro obecné účely

Standardní dostupnost odkazuje na 99,99 % smlouva SLA, která se použije na úrovních Standard a Basic/obecné účely. Zajištění vysoké dostupnosti v této architektuře modelu se dosahuje oddělením výpočetní a úložnou vrstvu a replikace dat do vrstvy úložiště.

Následující obrázek znázorňuje čtyři uzly ve standardní Architektonický model s oddělenými výpočetní a úložnou vrstvu.

![Oddělení výpočetního výkonu a úložiště](media/sql-database-managed-instance/general-purpose-service-tier.png)

V modelu standardní dostupnosti jsou dvě vrstvy:

- Bezstavové výpočetní vrstvy, který je spuštěn proces sqlserver.exe a obsahuje pouze přechodné a uložená v mezipaměti dat (například – mezipaměti plánu, fondu vyrovnávací paměti, fondu úložiště sloupce). Azure Service Fabric, který inicializuje procesu, řídí stav uzlu a provede převzetí služeb při selhání na jiné místo v případě potřeby je provozována této bezstavové uzlu serveru SQL Server.
- Stavová data vrstvy se soubory databáze (.mdf/.ldf), které jsou uložené ve službě Azure Premium Storage. Azure Storage zaručuje, že bude bez ztráty dat u všech záznamů, který je umístěn v žádném souboru databáze. Azure Storage má předdefinované datové dostupnost a redundance, který zajistí, že každý záznam v souboru protokolu nebo stránky v datovém souboru budou zachovány, i když dojde k chybě procesu serveru SQL Server.

Pokaždé, když se upgraduje operační systém nebo databázový stroj, některá část základní infrastruktury selže nebo pokud se zjistí některé kritický problém v procesu serveru Sql Server, Azure Service Fabric se přesune bezstavové procesu serveru SQL Server na jiný bezstavové výpočetní uzel. Je sada uzlů za chodu, který čeká na spuštění nové služby výpočty v případě převzetí služeb při selhání, aby se minimalizoval čas převzetí služeb při selhání. Data ve vrstvě služby Azure Storage to neovlivní a data/log soubory jsou připojeny k nově inicializované procesu serveru SQL Server. Tento postup zaručuje 99,99 % dostupnost, ale může mít některé vliv na funkčnost v případě velkého zatížení, které běží kvůli čas přechodu a fakt nový uzel SQL serveru začíná studenou mezipaměti.

## <a name="premiumbusiness-critical-availability"></a>Úrovně Premium/důležitých dostupnosti

Premium dostupnosti je povolený v úrovni Premium služby Azure SQL Database a je určeno pro intenzivní úlohy, které nemůžou tolerovat ovlivnit výkon z důvodu operace průběžnou údržbu.

V modelu úrovně premium Azure SQL database integruje výpočetní výkon a úložiště na jeden uzel. V této architektuře modelu vysoké dostupnosti se dosahuje prostřednictvím replikace výpočetního prostředí (SQL Server Database Engine proces) a úložiště (místně připojených jednotkách SSD) nasazené v 4 uzly [skupin dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) clusteru.

![Databázové stroje uzly clusteru](media/sql-database-managed-instance/business-critical-service-tier.png)

Proces databázový stroj SQL serveru a příslušná soubory mdf a ldf jsou umístěny na stejném uzlu s místně připojené úložiště SSD, které poskytuje nízkou latenci pro vaši úlohu. Vysoká dostupnost je implementováno pomocí standardní [skupin dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Každá databáze je cluster databázové uzly se jedna primární databáze, který je přístupný pro úlohu zákazníka a tři sekundární procesy kopie dat, který obsahuje. Primárního uzlu neustále nasdílí změny do sekundární uzly aby se zajistilo, že data jsou k dispozici na sekundárních replikách, pokud z nějakého důvodu dojde k chybě primárního uzlu. Převzetí služeb při selhání se zpracovává souborem databázový stroj SQL serveru – jedna sekundární replika se stane primárním uzlu a zajistit dostatek uzlů v clusteru se vytvoří sekundární repliku. Zatížení je automaticky přesměrován na nový primární uzel.

Kromě toho pro důležité obchodní informace clusteru poskytuje předdefinovaný uzel jen pro čtení, který slouží ke spouštění jen pro čtení dotazů (například sestavy), které by neměla mít vliv na výkon vaší primární úlohy. 

## <a name="zone-redundant-configuration-preview"></a>Konfigurace redundantního (preview)

Repliky nastavení kvora pro konfigurace místní úložiště jsou ve výchozím nastavení vytvoří ve stejném datacentru. Se zavedením [zóny dostupnosti Azure](../availability-zones/az-overview.md), máte možnost mají být umístěny různých replik sady kvora do různých zón dostupnosti ve stejné oblasti. Chcete-li odstranit jediný bod selhání, je aktualizační kanál, který ovládací prvek duplicitní napříč několika zónami jako tři prstence brány (gs) také. Směrování do aktualizačního kanálu konkrétní gateway řídí [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Protože redundantní konfigurace zóny nevytváří redundance další databáze, je použití zón dostupnosti (preview) v úrovních služeb úrovně Premium nebo pro důležité obchodní informace k dispozici bez dalších nákladů. Vyberte zónu databáze redundantní, můžete provádět databází Premium nebo pro důležité obchodní informace odolné mnohem větší sadě chyb, včetně výpadků katastrofickými datacenter, bez nutnosti jakkoli měnit aplikace logiky. Můžete také převést libovolný existující Premium nebo pro důležité obchodní informace databáze nebo fondy redundantní konfigurace zóny.

Vzhledem k tomu zóny redundantní kvora sady replik v různých datových centrech některé vzdálenost mezi nimi, latence sítě může prodloužit dobu potvrzení a tedy mít vliv na výkon některé úlohy OLTP. Můžete se kdykoli vrátit v konfiguraci s jednou zónou zakázáním nastavení redundance zóny. Tento proces je velikost operace s daty a je podobné aktualizace úroveň regulární služby. Na konci procesu databáze nebo fondu migrován z kanál redundantní zóny v jedné zóně kanál nebo naopak.

> [!IMPORTANT]
> Zóna redundantní databáze a elastické fondy jsou aktuálně podporuje jenom v rámci úrovně služeb Premium. Během veřejné verzi preview, zálohování a auditu záznamy se ukládají v úložišti RA-GRS a proto nemusí být automaticky dostupná i v případě výpadku celé zóny. 

Zóna redundantní verzi architektury vysoké dostupnosti je znázorněn v následujícím diagramu:
 
![Vysoká dostupnost architektura zónově redundantní](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>Horizontální navýšení kapacity pro čtení
Jak je popsáno, úrovně služeb Premium a pro důležité obchodní informace kvora sady a využít technologii AlwaysOn pro vysokou dostupnost v jedné oblasti a zóny redundantní konfigurace. Jednou z výhod technologie AlwaysOn serveru je, že tyto repliky jsou vždy transakčně konzistentní stav. Vzhledem k tomu, že repliky se stejnou velikostí jako primární výpočetní prostředky, aplikace můžou využít výhod této dodatečnou kapacitu pro obsluhu úlohy jen pro čtení bez dalších nákladů (čtení horizontální navýšení kapacity). Tímto způsobem dotazy jen pro čtení budou z hlavní úlohy čtení a zápis izolovaných a nebude mít vliv na jeho výkon. Čtení funkce úpravy rozsahu je určená pro aplikace, které zahrnují logicky oddělené úlohy jen pro čtení, jako jsou analýzy a proto může využívat tuto dodatečnou kapacitu bez připojení k primární. 

Jak používat funkci horizontální navýšení kapacity pro čtení s danou databází, je nutné explicitně aktivovat ho při vytváření databáze nebo později změnou jeho konfigurace přes PowerShell voláním [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) nebo [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) rutiny nebo přes rozhraní REST API Azure Resource Manageru pomocí [databází – vytvořit nebo aktualizovat](/rest/api/sql/databases/createorupdate) metody.

Po povolení horizontální navýšení kapacity pro čtení pro databázi aplikace propojíte databázi budete přesměrováni do repliky pro čtení a zápis nebo jen pro čtení replik databáze podle `ApplicationIntent` vlastnost nakonfigurovaný ve vaší aplikace připojovací řetězec. Informace o tom, `ApplicationIntent` vlastnost, naleznete v tématu [zadání záměru aplikace](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent). 

Pokud je zakázán horizontální navýšení kapacity pro čtení nebo nastavte vlastnost ReadScale v vrstvu Nepodporovaná služba, všechna připojení jsou směrované na repliky pro čtení i zápis, nezávisle `ApplicationIntent` vlastnost.

## <a name="conclusion"></a>Závěr
Azure SQL Database je úzce integrovaná s platformou Azure a vysoce závisí na platformě Service Fabric pro zjištění selhání a obnovení v Azure Storage blob pro ochranu dat a zóny dostupnosti pro vyšší odolnost proti chybám. Ve stejnou dobu Azure SQL database plně využívá technologii skupiny dostupnosti Always On z pole produktu SQL Server pro replikaci a převzetí služeb při selhání. Kombinací těchto technologií umožňuje aplikacím plně využít výhod modelu smíšené úložiště a podporu nejnáročnější smlouvy o úrovni služeb. 

## <a name="next-steps"></a>Další postup

- Další informace o [zóny dostupnosti Azure](../availability-zones/az-overview.md)
- Další informace o [Service Fabric](../service-fabric/service-fabric-overview.md)
- Další informace o [Azure Traffic Manageru](../traffic-manager/traffic-manager-overview.md) 
