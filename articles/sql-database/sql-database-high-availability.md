---
title: Vysoká dostupnost – služba Azure SQL Database | Microsoft Docs
description: Další informace o možnosti vysokou dostupnost služby Azure SQL Database a funkcí
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: jovanpop
ms.reviewer: carlrab, sashan
ms.openlocfilehash: a9874681d59d193fc3c3d0fd4271e2a6a0fb0dc6
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060379"
---
# <a name="high-availability-and-azure-sql-database"></a>Vysoká dostupnost a Azure SQL Database

Databáze SQL Azure je vysoce dostupné databáze platforma jako služba, která zaručí, že vaše databáze je nahoru a spuštěné 99,99 % času, bez starostí o údržbě a výpadky způsobené nástrojem. Toto je plně spravovaná procesu databázového stroje SQL Server hostované v cloudu Azure, která zajišťuje, že vaše databáze systému SQL Server vždy upgradovat opravit aniž by to ovlivnilo vaše úlohy. Databáze SQL Azure můžete rychle obnovit i v případech nejdůležitější zajistit, aby vaše data byla vždy k dispozici.

Platformy Azure plně spravuje každou databází SQL Azure a zaručí nedošlo ke ztrátě dat a vysoký podíl data dostupnosti. Azure automaticky zpracovává opravy, zálohování, replikace, detekce chyb, potenciální základní hardware, selhání softwaru nebo sítě, nasazení oprav chyb, převzetí služeb při selhání, upgrade databáze a další úlohy údržby. Technici systému SQL Server byla implementována nejznámějších postupů, zajistíte, že všechny operace údržby jsou dokončeny za méně než 0,01 % doba života vaší databáze. Tato architektura umožňuje potvrdit data musí být nikdy ztraceny a aby byly provedeny operace údržby bez ovlivnění zatížení. Neexistují žádné časová období údržby nebo výpadky způsobené nástrojem, které by měl vyžadují, abyste zastavit úlohy, zatímco databázi upgradovat nebo zachována. Integrovanou vysokou dostupnost ve službě Azure SQL Database zaručí, že databáze nebude nikdy jediný bod selhání v architektuře vašeho softwaru.

Existují dva modely vysokou dostupnost, které jsou použity v Azure SQL:

- Standard nebo obecné účely model, který poskytuje 99,99 % dostupnost, ale některé potenciální snížení výkonu během údržby aktivity.
- Premium nebo obchodní kritické model, který poskytuje také obsahuje 99,99 % dostupnost s dopadem na výkon minimální velikosti pracovní zátěže i během údržby aktivity.

Azure upgraduje a transparentně opravy základního operačního systému, ovladačů a databázového stroje SQL Server s minimální dobou nižší pro koncové uživatele. Databáze SQL Azure běží na nejnovější stabilní verze databázového stroje SQL Server a operačního systému Windows a většina uživatelů by Všimněte si, že jsou neustále provést upgrady.

## <a name="standard-availability"></a>Standardní dostupnosti

Odkazuje standardní dostupnost SLA 99,99 %, která se použije na úrovních Standard nebo Basic nebo obecné účely. Dostupnost se dosahuje oddělení vrstvy výpočetního prostředí a úložiště. V modelu standardní dostupnosti máme dvě vrstvy:

- Bezstavové výpočetní vrstvy, který je spuštěn proces sqlserver.exe a obsahuje data pouze přechodný a uložené v mezipaměti (například – mezipaměti plánu, fondu vyrovnávací paměti, fondu úložiště sloupců). Tento uzel bezstavové systému SQL Server je provozována společností Azure Service Fabric, která inicializuje proces, který řídí stav uzlu a v případě potřeby provede převzetí služeb při selhání na jiné místo.
- Stavová data vrstva se soubory databáze (.mdf/.ldf), které jsou uložené v Azure Premium Storage disky. Úložiště Azure zaručuje, že bude nedošlo ke ztrátě dat, které je umístěn v žádném souboru databáze. Úložiště Azure má integrovanou data dostupnosti nebo redundance, která zajišťuje, že každý záznam v souboru protokolu nebo stránky v datovém souboru se zachovají i v případě, že dojde k chybě procesů serveru SQL Server.

Vždy, když se upgraduje databázový stroj nebo operačního systému, nebo pokud některé důležité problém je zjištěn během procesů serveru Sql Server, Azure Service Fabric přesune do jiného uzlu bezstavové výpočetní bezstavové procesů serveru SQL Server. Neovlivní data ve vrstvě úložiště Azure a soubory dat a protokolování jsou připojené k nově inicializovaného procesů serveru SQL Server. Doba očekávané převzetí služeb při selhání může být měří v sekundách. Tento postup zaručuje 99,99 % dostupnost, ale může mít některé ovlivňuje výkon v případě velkého zatížení, systémem z důvodu čas přechodu do a fakt, na novém uzlu systému SQL Server začíná cold mezipaměti.

## <a name="premium-availability"></a>Premium dostupnosti

Dostupnost Premium je povolené v úrovni Premium databáze SQL Azure a je určená pro náročné úlohy, které nemůžou tolerovat dopady výkonu z důvodu údržby probíhající operace.

V modelu premium Azure SQL database integruje výpočetního prostředí a úložiště na jeden uzel. Proces databázového stroje SQL Server a základní mdf/ldf soubory jsou umístěny na stejném uzlu s místně připojené úložiště SSD poskytuje s nízkou latencí pro velikosti pracovní zátěže.

Vysoká dostupnost je implementovaná pomocí standardní [skupin dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Ke každé databázi je cluster databázové uzly se několik sekundární procesy obsahující kopií dat a jeden primární databázi, která je přístupná pro úloh zákazníka. Primárním uzlu, který doručí neustále změny na sekundární uzly aby se zajistilo, že data jsou k dispozici na sekundárních replikách, pokud z nějakého důvodu dojde k chybě primárního uzlu. Převzetí služeb při selhání se zpracovává souborem databázového stroje SQL Server – jedna sekundární replika se stane primárním uzlu a vytvoření nové sekundární repliky zajistit dostatek uzlů v clusteru. Úlohy se automaticky přesměruje na nový primární uzel. Převzetí služeb při selhání čas se měří v milisekundách a nové primární instance je okamžitě připraveni pokračovat obsluhovat požadavky.

## <a name="zone-redundant-configuration-preview"></a>Konfigurace redundantního zóny (preview)

Ve výchozím nastavení jsou kvora sady replik pro místní úložiště konfigurace vytváří ve stejném datovém centru. Se zavedením [Azure dostupnost zóny](../availability-zones/az-overview.md), máte možnost umístit jiné repliky sady kvora do zóny různých dostupnosti ve stejné oblasti. K vyloučení jediný bod selhání, je ovládací prvek prstenec duplicitní napříč několika zón jako tři prstence brány (GW) také. Směrování do konkrétní gateway prstenec řídí [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Protože konfigurace redundantního zóny nevytvoří redundance další databáze, použití dostupnosti zón v Premium nebo kritické obchodní (preview) úrovně služeb je k dispozici bez jakýchkoli nákladů. Tak, že vyberete databázi redundantní zóny, můžete provést vaší Premium nebo kritické obchodní (preview) databáze odolné vůči mnohem větší sadu chyb, včetně výpadků závažné datového centra bez uložení změn logiku aplikace. Všechny existující databáze Premium nebo kritický stav firmy nebo fondů (preview) můžete také převést konfigurace redundantního zóny.

Protože zóny redundantní kvora set má repliky v různých datových centrech s některé vzdálenost mezi nimi, latence sítě vyšší může prodloužit doba potvrzení a proto mít dopad na výkon některé úlohy OLTP. Ke konfiguraci zóny jedním může vždy vrátit zakázáním nastavení redundance zóny. Tento proces je velikost operace dat a je podobná aktualizace cíle na úrovni (SLO) regulární služby. Na konci procesu je databáze nebo fondu migrovat z prstenec redundantní zóny v jedné oblasti prstenec nebo naopak.

> [!IMPORTANT]
> Zóny redundantní databáze i elastické fondy jsou aktuálně podporuje jenom ve vrstvě služeb Premium. Během verzi public preview, zálohování a audit záznamy jsou uloženy v úložišti RA-GRS a proto nemusí být automaticky k dispozici v případě výpadku celou zónu. 

Zóny redundantní verzi Architektura vysoké dostupnosti je zobrazená ve následující diagram:
 
![Vysoká dostupnost architektura zónově redundantní](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>Přečtěte si Škálováním na více systémů
Jak je popsáno, Premium a kritické obchodní (preview) služby využívají vrstev kvora sad a technologie Always On pro zajištění vysoké dostupnosti v jedné oblasti i redundantní konfigurace zóny. Jednou z výhod technologie AlwaysOn serveru je, že repliky jsou vždycky ve stavu transakční konzistence stavu. Vzhledem k tomu, že repliky na stejnou úroveň výkonu jako primární, aplikace mohou využít výhod tuto další kapacitu pro obsluhu úlohy jen pro čtení bez jakýchkoli nákladů (čtení Škálováním na více systémů). Tímto způsobem dotazy jen pro čtení bude izolovaná od hlavní úloh pro čtení a zápis a nebude mít vliv na jeho výkon. Číst funkce škálování je určená pro aplikace, které zahrnují logicky oddělené jen pro čtení úlohy, jako jsou například analýzy a proto může využít tuto dodatečnou kapacitu bez připojení k primární. 

Pokud chcete používat funkce škálování pro čtení s danou databází, musí explicitně ji aktivujete při vytváření databáze nebo později změnou jeho konfigurace pomocí prostředí PowerShell vyvoláním [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) nebo [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) rutiny nebo prostřednictvím rozhraní REST API Azure Resource Manager [databází - vytvořit nebo aktualizovat](/rest/api/sql/databases/createorupdate) metoda.

Po povolení škálování pro čtení pro databázi aplikace připojení k databázi přesměrováni do repliky pro čtení a zápis nebo repliku jen pro čtení této databáze podle `ApplicationIntent` vlastnost nakonfigurované do aplikace připojovací řetězec. Informace o `ApplicationIntent` vlastnost, najdete v části [zadání záměru aplikace](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent). 

Pokud je zakázán pro čtení Škálováním na více systémů nebo nastavte vlastnost ReadScale v vrstvou nepodporované služby, všechna připojení jsou směrované repliky pro čtení a zápis, nezávisle `ApplicationIntent` vlastnost.  

> [!NOTE]
> Je možné aktivovat čtení Škálováním na více systémů na standardní nebo obecné účely databáze, i když ho nebude mít za následek směrování jen pro čtení určena relace na samostatné repliku. Tomu je potřeba pro podporu existující aplikace, které mezi úrovně Standard/obecné účely a kritické Premium nebo obchodní škálovat nahoru a dolů.  

Funkce pro čtení Škálováním na více systémů podporuje úrovně konzistence typu relace. Pokud relace jen pro čtení znovu připojí po připojení chyba způsobit podle nedostupnosti repliky, můžete přesměrovat na jinou repliku. Při nepravděpodobné, může být při zpracování datové sady, která je zastaralá. Podobně pokud aplikace zapisuje data pomocí relace pro čtení a zápis a okamžitě přečte pomocí relace jen pro čtení, je možné, že nová data není okamžitě viditelné.

## <a name="conclusion"></a>Závěr
Databáze SQL Azure je úzce integrovaná s platformou Azure a vysoce závisí na Service Fabric pro zjištění selhání a obnovení, objektů BLOB služby Azure Storage pro ochranu dat a dostupnost zóny pro vyšší odolnosti proti chybám. Ve stejnou dobu Azure SQL database plně využívá technologie vždy na skupiny dostupnosti z pole produktu SQL Server pro replikaci a převzetí služeb při selhání. Kombinace těchto technologií umožňuje aplikacím plně výhody modelu smíšený úložiště a podporu nejnáročnější SLA. 

## <a name="next-steps"></a>Další postup

- Další informace o [Azure dostupnost zóny](../availability-zones/az-overview.md)
- Další informace o [Service Fabric](../service-fabric/service-fabric-overview.md)
- Další informace o [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 
