---
title: Vysoká dostupnost – služba Azure SQL Database | Microsoft Docs
description: Další informace o možnosti vysokou dostupnost služby Azure SQL Database a funkcí
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 27f0c49913b424a6bd77b7cb6f7d6e97598c2157
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839805"
---
# <a name="high-availability-and-azure-sql-database"></a>Vysoká dostupnost a Azure SQL Database
Od zahájení nabídky PaaS databáze SQL Azure společnost Microsoft vyvinula potenciálu svým zákazníkům, které vysoké dostupnosti (HA) je součástí služby a zákazníků nemusejí fungovat, přidejte speciální logiku pro nebo rozhodnutí ohledně HA. Společnost Microsoft udržuje plnou kontrolu nad konfigurace systému HA a operace, zákazníkům nabídnout SLA. HA SLA platí pro databáze SQL v oblasti a neposkytuje ochranu v případě selhání celkový oblasti, které je faktory, které mimo Microsoft nemohla ovlivnit (například přírodní katastrofě, war, jednání teroristický útok, povstáním, government akci, nebo síťové zařízení selhání nebo externí vzhledem k datových centrech společnosti Microsoft, včetně v lokalitách zákazníka nebo mezi lokalitami zákazníka a datového centra společnosti Microsoft).

Pro zjednodušení problém prostor HA, společnost Microsoft používá následující předpoklady:
1.  Selhání hardwaru a softwaru jsou nevyhnutelné
2.  Provozní pracovníci uděláte chyby, které vést k chybám
3.  Plánované údržby operace způsobit výpadky 

Tyto jednotlivé události jsou jen zřídka, v cloudovém měřítku, k nim dojde, pokud každý týden ne každý den. 

## <a name="fault-tolerant-sql-databases"></a>Odolné proti chybám databáze SQL
Zákazníci se nejvíc zajímat odolnost vlastní databáze a jsou méně zájem o odolnost službě SQL Database jako celek. 99,99 % dostupnost služby je smysl, pokud "databáze" je součástí 0,01 % databází, které jsou vypnuté. Každé databáze musí být odolné proti chybám a zmírnění chyby by nikdy dojít ke ztrátě potvrzené transakce. 

Databáze SQL pro data, používá místní úložiště (LS) založené na přímé připojené disky nebo virtuální pevné disky a vzdálené úložiště (r) založené na objekty BLOB stránky Azure Premium Storage. 
- Místní úložiště se používá v Premium nebo kritické obchodní (preview) databází a elastické fondy, které jsou určené pro mise kritické aplikace OLTP s požadavky na vysokou IOPS. 
- Vzdálené úložiště se používá v Basic, Standard a obecné účely úrovně služeb, které jsou určené pro nároky zaměřené na konkrétní firemních procesů, které vyžadují úložiště a výpočetního výkonu nezávisle škálovat. Objekt blob jednostránkové používají pro databázi a soubory protokolu a mechanismy pro replikaci a převzetí služeb při selhání integrovanou úložiště.

V obou případech replikace, detekce chyb a převzetí služeb při selhání mechanismy databáze SQL jsou plně automatizované a pracovat i bez lidského zásahu. Tato architektura umožňuje potvrdit data musí být nikdy ztraceny a že odolnost dat má přednost před všechno ostatní.

Klíčové výhody:
- Zákazníci plně využívat replikované databází bez nutnosti konfigurovat nebo spravovat složitá hardwaru, softwaru, operačních systémů nebo prostředích virtualizace.
- Úplné ACID vlastnosti relačních databází je udržováno v systému.
- Převzetí služeb při selhání jsou plně automatizované bez ztráty dat. všechny potvrzené.
- Směrování připojení k primární replice dynamicky spravuje službu s žádné logiku aplikace vyžaduje.
- Vysoká úroveň automatické redundance je k dispozici bez dalších poplatků.

> [!NOTE]
> Architektura popsané vysoké dostupnosti se mohou změnit bez předchozího upozornění. 

## <a name="data-redundancy"></a>Data redundancy

Řešení vysoké dostupnosti v SQL Database je založeno na [skupin dostupnosti Always ON](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) technologie v systému SQL Server a udělá z něj fungovat pro LS a RS databáze s minimálním rozdíly. V konfiguraci LS technologie skupiny dostupnosti Always ON se používá pro trvalost při v RS se používá k zajištění dostupnosti (nízkou RTO podle aktivní geografickou replikací). 

## <a name="local-storage-configuration"></a>Místní úložiště konfigurace

V této konfiguraci každou databázi do režimu online pomocí služby správy (MS) v rámci prstenec ovládacího prvku. Jedna primární replika a alespoň dvě sekundární repliky (kvora set) jsou umístěné v rámci prstenec klienta, která zahrnuje tři nezávislých fyzických subsystémů v rámci stejného datového centra. Všechny čtení a zápisy se odesílají bránou (GW) na primární repliku a zápisů asynchronně replikovány na sekundárních replikách. Databáze SQL používá schéma na základě kvora potvrzení, kde data se zapisují do primárním serverem a nejméně jedna sekundární replika před potvrzení transakce.

[Service Fabric](../service-fabric/service-fabric-overview.md) převzetí služeb při selhání systému automaticky znovu vytvoří repliky jako uzly selžou a udržuje sadu kvora členství jako uzly odchylují a připojení k systému. Plánované údržby je pečlivě koordinována, aby se zabránilo kvora set směrem dolů nižší než počet minimální repliky (obvykle 2). Tento model funguje dobře pro Premium a kritické obchodní databáze (preview), ale vyžaduje redundance součástí výpočetních operací a úložiště a má za následek vyšší náklady.

## <a name="remote-storage-configuration"></a>Konfigurace vzdáleného úložiště

Pro vzdálené úložiště konfigurace (úrovně Basic, Standard nebo obecné účely) se přesně jedna kopie udržuje v vzdálené úložiště objektů blob, pomocí možnosti úložiště systémy odolnosti, redundanci a bit kroužkovitosti detekce. 

Architektura vysoké dostupnosti je znázorněno v následujícím diagramu:
 
![Architektura vysoké dostupnosti](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection-and-recovery"></a>Detekce chyb a obnovení 
Ve velkém měřítku distribuovaného systému musí systém detekce vysoce spolehlivé selhání, který může zjistit selhání spolehlivě, rychle a co nejblíže k odběrateli. Pro databázi SQL je tento systém založen na Azure Service Fabric. 

S primární replikou je okamžitě zřejmé Pokud primární replice se nezdařila a pracovní nemůže pokračovat, protože všechny čtení a zápisu dochází v primární replice nejprve. Tento proces povýšení sekundární repliky se stavem primární má cíli času obnovení (RTO) = 30 sekund a plánovaného bodu obnovení (RPO) = 0. Aby se minimalizoval vliv 30 sekund RTO, osvědčeným postupem je pokuste se znovu připojit s menší doba čekání nezdařených pokusů o připojení.

Pokud se nezdaří sekundární repliku, databáze je dolů minimální kvora sadu, s žádné k výměně za chodu. Služba fabric zahájí se proces opětovné konfigurace podobný procesu, který následuje selhání primární repliky, takže po krátké čekání na-li určit, zda je trvalé, selhání jiné sekundární repliky se vytvoří. V případech dočasné stavu out-of-service, například k selhání operačního systému nebo upgrade není novou repliku vytvořená s umožňující uzlu se nezdařilo restartovat místo toho okamžitě. 

Pro vzdálené úložiště konfigurace SQL databáze používá funkce Always ON pro převzetí služeb při selhání databáze během upgradu. K tomu, novou instanci SQL se spouštějí předem jako součást upgradu události plánované a připojí a obnoví se soubor databáze ze vzdáleného úložiště. V případě mimoprocesových nebo jiných neplánovaných událostí technologie Windows Fabric spravuje dostupnost instance a jako poslední krok obnovení připojí vzdálený databázový soubor.

## <a name="zone-redundant-configuration-preview"></a>Konfigurace redundantního zóny (preview)

Ve výchozím nastavení jsou kvora sady replik pro místní úložiště konfigurace vytváří ve stejném datovém centru. Se zavedením [Azure dostupnost zóny](../availability-zones/az-overview.md), máte možnost umístit jiné repliky sady kvora do zóny různých dostupnosti ve stejné oblasti. K vyloučení jediný bod selhání, je ovládací prvek prstenec duplicitní napříč několika zón jako tři prstence brány (GW) také. Směrování do konkrétní gateway prstenec řídí [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Protože konfigurace redundantního zóny nevytvoří redundance další databáze, použití dostupnosti zón v Premium nebo kritické obchodní (preview) úrovně služeb je k dispozici bez jakýchkoli nákladů. Tak, že vyberete databázi redundantní zóny, můžete provést vaší Premium nebo kritické obchodní (preview) databáze odolné vůči mnohem větší sadu chyb, včetně výpadků závažné datového centra bez uložení změn logiku aplikace. Všechny existující databáze Premium nebo kritický stav firmy nebo fondů (preview) můžete také převést konfigurace redundantního zóny.

Protože zóny redundantní kvora set má repliky v různých datových centrech s některé vzdálenost mezi nimi, latence sítě vyšší může prodloužit doba potvrzení a proto mít dopad na výkon některé úlohy OLTP. Ke konfiguraci zóny jedním může vždy vrátit zakázáním nastavení redundance zóny. Tento proces je velikost operace dat a je podobná aktualizace cíle na úrovni (SLO) regulární služby. Na konci procesu je databáze nebo fondu migrovat z prstenec redundantní zóny v jedné oblasti prstenec nebo naopak.

> [!IMPORTANT]
> Zóny redundantní databáze i elastické fondy jsou aktuálně podporuje jenom ve vrstvě služeb Premium. Během verzi public preview, zálohování a audit záznamy jsou uloženy v úložišti RA-GRS a proto nemusí být automaticky k dispozici v případě výpadku celou zónu. 

Zóny redundantní verzi Architektura vysoké dostupnosti je zobrazená ve následující diagram:
 
![Vysoká dostupnost architektura zónově redundantní](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>Přečtěte si Škálováním na více systémů
Jak je popsáno, Premium a kritické obchodní (preview) služby využívají vrstev kvora sad a technologii AlwaysON pro zajištění vysoké dostupnosti v jedné oblasti i redundantní konfigurace zóny. Jednou z výhod AlwasyON je, že repliky jsou vždycky ve stavu transakční konzistence stavu. Vzhledem k tomu, že repliky na stejnou úroveň výkonu jako primární, aplikace mohou využít výhod tuto další kapacitu pro obsluhu úlohy jen pro čtení bez jakýchkoli nákladů (čtení Škálováním na více systémů). Tímto způsobem dotazy jen pro čtení bude izolovaná od hlavní úloh pro čtení a zápis a nebude mít vliv na jeho výkon. Číst funkce škálování je určená pro aplikace, které zahrnují logicky oddělené jen pro čtení úlohy, jako jsou například analýzy a proto může využít tuto dodatečnou kapacitu bez připojení k primární. 

Pokud chcete používat funkce škálování pro čtení s danou databází, musí explicitně ji aktivujete při vytváření databáze nebo později změnou jeho konfigurace pomocí prostředí PowerShell vyvoláním [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) nebo [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) rutiny nebo prostřednictvím rozhraní REST API Azure Resource Manager [databází - vytvořit nebo aktualizovat](/rest/api/sql/databases/createorupdate) metoda.

Po povolení škálování pro čtení pro databázi aplikace připojení k databázi přesměrováni do repliky pro čtení a zápis nebo repliku jen pro čtení této databáze podle `ApplicationIntent` vlastnost nakonfigurované do aplikace připojovací řetězec. Informace o `ApplicationIntent` vlastnost, najdete v části [zadání záměru aplikace](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent). 

Pokud je zakázán pro čtení Škálováním na více systémů nebo nastavte vlastnost ReadScale v vrstvou nepodporované služby, všechna připojení jsou směrované repliky pro čtení a zápis, nezávisle `ApplicationIntent` vlastnost.  

> [!NOTE]
> Je možné aktivovat čtení Škálováním na více systémů na standardní nebo obecné účely databáze, i když ho nebude mít za následek směrování jen pro čtení určena relace na samostatné repliku. Tomu je potřeba pro podporu existující aplikace, které mezi úrovně Standard/obecné účely a kritické Premium nebo obchodní škálovat nahoru a dolů.  

Funkce pro čtení Škálováním na více systémů podporuje úrovně konzistence typu relace. Pokud relace jen pro čtení znovu připojí po připojení chyba způsobit podle nedostupnosti repliky, můžete přesměrovat na jinou repliku. Při nepravděpodobné, může být při zpracování datové sady, která je zastaralá. Podobně pokud aplikace zapisuje data pomocí relace pro čtení a zápis a okamžitě přečte pomocí relace jen pro čtení, je možné, že nová data není okamžitě viditelné.

## <a name="conclusion"></a>Závěr
Databáze SQL Azure je úzce integrovaná s platformou Azure a vysoce závisí na Service Fabric pro zjištění selhání a obnovení, objektů BLOB služby Azure Storage pro ochranu dat a dostupnost zóny pro vyšší odolnosti proti chybám. Ve stejnou dobu Azure SQL database plně využívá technologie Always On z pole produktu SQL Server pro replikaci a převzetí služeb při selhání. Kombinace těchto technologií umožňuje aplikacím plně výhody modelu smíšený úložiště a podporu nejnáročnější SLA. 

## <a name="next-steps"></a>Další postup

- Další informace o [Azure dostupnost zóny](../availability-zones/az-overview.md)
- Další informace o [Service Fabric](../service-fabric/service-fabric-overview.md)
- Další informace o [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 
