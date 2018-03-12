---
title: "Vysoká dostupnost – služba Azure SQL Database | Microsoft Docs"
description: "Další informace o možnosti vysokou dostupnost služby Azure SQL Database a funkcí"
keywords: 
services: sql-database
author: anosov1960
manager: jhubbard
ms.service: sql-database
ms.custom: 
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 03/07/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: a7771eebc8359a5de1c79328014f5ecc06c9673b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="high-availability-and-azure-sql-database"></a>Vysoká dostupnost a Azure SQL Database
Od zahájení nabídky PaaS databáze SQL Azure společnost Microsoft vyvinula potenciálu svým zákazníkům, které vysoké dostupnosti (HA) je součástí služby a zákazníků nemusejí fungovat, přidejte speciální logiku pro nebo rozhodnutí ohledně HA. Společnost Microsoft udržuje plnou kontrolu nad konfigurace systému HA a operace, zákazníkům nabídnout SLA. HA SLA platí pro databáze SQL v oblasti a neposkytuje ochranu v případě selhání celkový oblasti, které je faktory, které mimo Microsoft nemohla ovlivnit (například přírodní katastrofě, war, jednání teroristický útok, povstáním, government akci, nebo síťové zařízení selhání nebo externí vzhledem k datových centrech společnosti Microsoft, včetně v lokalitách zákazníka nebo mezi lokalitami zákazníka a datového centra společnosti Microsoft).

Pro zjednodušení problém prostor HA, společnost Microsoft používá následující předpoklady:
1.  Selhání hardwaru a softwaru jsou nevyhnutelné
2.  Provozní pracovníci uděláte chyby, které vést k chybám
3.  Plánované údržby operace způsobit výpadky 

Když tyto jednotlivé události jsou jen zřídka, v cloudovém měřítku, budou každý týden, pokud není každý den. 

## <a name="fault-tolerant-sql-databases"></a>Odolné proti chybám databáze SQL
Zákazníci se nejvíc zajímat odolnost vlastní databáze a jsou méně zájem o odolnost službě SQL Database jako celek. 99,99 % dostupnost služby je smysl, pokud "databáze" je součástí 0,01 % databází, které jsou vypnuté. Každé databáze musí být odolné proti chybám a zmírnění chyby by nikdy dojít ke ztrátě potvrzené transakce. 

Databáze SQL pro data, používá místní úložiště (LS) založené na přímé připojené disky nebo virtuální pevné disky a vzdálené úložiště (r) založené na objekty BLOB stránky Azure Premium Storage. 
- Místní úložiště se používá v Premium databáze a fondy, které jsou určené pro nepostradatelné OLTP aplikace s vysokou požadavky IOPS. 
- Vzdálené úložiště se používá pro úrovně služeb Basic a Standard, určený pro nároky zaměřené na konkrétní firemních procesů, které vyžadují úložiště a výpočetního výkonu nezávisle škálovat. Objekt blob jednostránkové používají pro databázi a soubory protokolu a mechanismy pro replikaci a převzetí služeb při selhání integrovanou úložiště.

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

Řešení vysoké dostupnosti v SQL Database je založeno na [Always ON](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) technologie v systému SQL Server a udělá z něj fungovat pro LS a RS databáze s minimálním rozdíly. Konfigurace, Always ON se používá pro trvalost v RS v LS je, že ji použít k zajištění dostupnosti (nízkou RTO). 

## <a name="local-storage-configuration"></a>Místní úložiště konfigurace

V této konfiguraci každou databázi do režimu online pomocí služby správy (MS) v rámci prstenec ovládacího prvku. Jedna primární replika a alespoň dvě sekundární repliky (kvora set) jsou umístěné v rámci prstenec klienta, která zahrnuje tři nezávislých fyzických subsystémů v rámci stejného datového centra. Všechny čtení a zápisy se odesílají bránou (GW) na primární repliku a zápisů asynchronně replikovány na sekundárních replikách. Databáze SQL používá schéma na základě kvora potvrzení, kde data se zapisují do primárním serverem a nejméně jedna sekundární replika před potvrzení transakce.

[Service Fabric](/azure/service-fabric/service-fabric-overview.md) převzetí služeb při selhání systému automaticky znovu vytvoří repliky jako uzly selžou a udržuje sadu kvora členství jako uzly odchylují a připojení k systému. Plánované údržby je pečlivě koordinována, aby se zabránilo kvora set směrem dolů nižší než počet minimální repliky (obvykle 2). Tento model funguje dobře u prémiových databází, ale vyžaduje redundance součástí výpočetních operací a úložiště a má za následek vyšší náklady.

## <a name="remote-storage-configuration"></a>Konfigurace vzdáleného úložiště

Pro vzdálené úložiště konfigurace (úrovně Basic a Standard) se přesně jedna kopie udržuje v vzdálené úložiště objektů blob, pomocí možnosti úložiště systémy odolnosti, redundanci a bit kroužkovitosti detekce. 

Architektura vysoké dostupnosti je znázorněno v následujícím diagramu:
 
![Architektura vysoké dostupnosti](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection-and-recovery"></a>Detekce chyb a obnovení 
Ve velkém měřítku distribuovaného systému musí systém detekce vysoce spolehlivé selhání, který může zjistit selhání spolehlivě, rychle a co nejblíže k odběrateli. Pro databázi SQL je tento systém založen na Azure Service Fabric. 

S primární replikou je okamžitě zřejmé Pokud primární replice se nezdařila a pracovní nemůže pokračovat, protože všechny čtení a zápisu dochází v primární replice nejprve. Tento proces povýšení sekundární repliky se stavem primární má cíli času obnovení (RTO) = 30 sekund a plánovaného bodu obnovení (RPO) = 0. Aby se minimalizoval vliv 30 sekund RTO, osvědčeným postupem je pokuste se znovu připojit s menší doba čekání nezdařených pokusů o připojení.

Pokud se nezdaří sekundární repliku, databáze je dolů minimální kvora sadu, s žádné k výměně za chodu. Služba fabric zahájí se proces opětovné konfigurace podobný procesu, který následuje selhání primární repliky, takže po krátké čekání na-li určit, zda je trvalé, selhání jiné sekundární repliky se vytvoří. V případech dočasné stavu out-of-service, například k selhání operačního systému nebo upgrade není novou repliku vytvořená s umožňující uzlu se nezdařilo restartovat místo toho okamžitě. 

Pro vzdálené úložiště konfigurace SQL databáze používá funkce Always ON pro převzetí služeb při selhání databáze během upgradu. K tomu, novou instanci SQL se spouštějí předem jako součást upgradu události plánované a připojí a obnoví se soubor databáze ze vzdáleného úložiště. V případě mimoprocesových nebo jiných neplánovaných událostí technologie Windows Fabric spravuje dostupnost instance a jako poslední krok obnovení připojí vzdálený databázový soubor.

## <a name="zone-redundant-configuration-preview"></a>Konfigurace redundantního zóny (preview)

Ve výchozím nastavení jsou kvora sady replik pro místní úložiště konfigurace vytváří ve stejném datovém centru. Se zavedením [Azure dostupnost zóny](/azure/availability-zones/az-overview.md), máte možnost umístit jiné repliky sady kvora do zóny různých dostupnosti ve stejné oblasti. K vyloučení jediný bod selhání, je ovládací prvek prstenec duplicitní napříč několika zón jako tři prstence brány (GW) také. Směrování do konkrétní gateway prstenec řídí [Azure Traffic Manager](/traffic-manager/traffic-manager-overview.md) (ATM). Vzhledem k tomu, že konfigurace redundantního zóny nevytvoří redundance další databáze, použijte zón dostupnosti ve vrstvě služeb Premium je k dispozici bez jakýchkoli nákladů. Tak, že vyberete databázi redundantní zóny, můžete provést prémiových databází, které jsou odolné mnohem větší sadu chyb, včetně výpadků závažné datového centra bez uložení změn logiku aplikace. Všechny existující databáze Premium nebo fond můžete také převést konfigurace redundantního zóny.

Protože zóny redundantní kvora set má repliky v různých datových centrech s některé vzdálenost mezi nimi, latence sítě vyšší může prodloužit doba potvrzení a proto mít dopad na výkon některé úlohy OLTP. Ke konfiguraci zóny jedním může vždy vrátit zakázáním nastavení redundance zóny. Tento proces je velikost operace dat a je podobná aktualizace cíle na úrovni (SLO) regulární služby. Na konci procesu je databáze nebo fondu migrovat z prstenec redundantní zóny v jedné oblasti prstenec nebo naopak.

> [!IMPORTANT]
> Ve vrstvě služeb Premium jsou podporovány pouze zóny redundantní databáze i elastické fondy. Během verzi public preview, zálohování a audit záznamy jsou uloženy v úložišti RA-GRS a proto nemusí být automaticky k dispozici v případě výpadku celou zónu. 

Zóny redundantní verzi Architektura vysoké dostupnosti je zobrazená ve následující diagram:
 
![Vysoká dostupnost architektura zónově redundantní](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="conclusion"></a>Závěr
Databáze SQL Azure je úzce integrovaná s platformou Azure a vysoce závisí na Service Fabric pro zjištění selhání a obnovení, objektů BLOB služby Azure Storage pro ochranu dat a dostupnost zóny pro vyšší odolnosti proti chybám. Ve stejnou dobu Azure SQL database plně využívá technologie Always On z pole produktu SQL Server pro replikaci a převzetí služeb při selhání. Kombinace těchto technologií umožňuje aplikacím plně výhody modelu smíšený úložiště a podporu nejnáročnější SLA. 

## <a name="next-steps"></a>Další postup

- Další informace o [Azure dostupnost zóny](/azure/availability-zones/az-overview.md)
- Další informace o [Service Fabric](/azure/service-fabric/service-fabric-overview.md)
- Další informace o [Azure Traffic Manager](/traffic-manager/traffic-manager-overview.md) 
