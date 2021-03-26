---
title: Architektura nasazení – vyhrazený modul HARDWAROVÉho zabezpečení Azure | Microsoft Docs
description: Základní požadavky na návrh při použití vyhrazeného modulu HARDWAROVÉho zabezpečení Azure v rámci architektury aplikace
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: c454b2e4df7a9ce5fadd33386e5bb413b503c6e4
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608421"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Architektura nasazení Azure Dedicated HSM

Vyhrazený modul HSM Azure poskytuje úložiště kryptografických klíčů v Azure. Splňuje přísné požadavky na zabezpečení. Zákazníci budou využívat vyhrazený modul HSM v Azure, pokud:

* Musí splňovat certifikaci [FIPS 140-2 Level-3](https://csrc.nist.gov/publications/detail/fips/140/2/final) .
* Vyžadovat, aby měli exkluzivní přístup k modulu HARDWAROVÉho zabezpečení
* měla by mít úplnou kontrolu nad svými zařízeními.

HSM jsou distribuované napříč datovými centry Microsoftu a dají se snadno zřídit jako pár zařízení jako základ pro vysoce dostupné řešení. Můžou být nasazené i v různých oblastech řešení odolného proti havárii. Oblasti s dostupným vyhrazeným modulem HSM je aktuálně možné zkontrolovat pomocí [stránky produkty podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=azure-dedicated-hsm). 

* East US
* USA – východ 2
* USA – západ
* Západní USA 2
* Středojižní USA
* Southeast Asia
* Východní Asie
* Indie – střed
* Indie – jih
* Japonsko – východ
* Japonsko – západ
* Severní Evropa
* West Europe
* Spojené království – jih
* Spojené království – západ
* Střední Kanada
* Kanada – východ
* Austrálie – východ
* Austrálie – jihovýchod
* Švýcarsko – sever
* Švýcarsko – západ
* USA (Gov) – Virginia
* USA (Gov) – Texas

Každá z těchto oblastí má racky HSM nasazené buď v obou nezávislých datových centrech, nebo alespoň ve dvou nezávislých zónách dostupnosti. Jižní Východní Asie má tři zóny dostupnosti a Východní USA 2 má dvě. V rámci Evropy, Asie a Severní Amerika jsou celkem dvacet tři oblasti, které nabízejí vyhrazenou službu HSM. Další informace o oblastech Azure najdete v informacích o oficiálních  [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).
Některé faktory návrhu pro jakékoli vyhrazené řešení založené na HSM jsou umístění/latence, vysoká dostupnost a podpora pro jiné distribuované aplikace.

## <a name="device-location"></a>Device location (Umístění zařízení)

Optimální umístění zařízení HSM je nejbližší blízkosti aplikací provádějících kryptografické operace. Latence v oblasti se očekává jako jednociferné číslo milisekund. Latence mezi oblastmi může být 5 až 10krát vyšší než tato.

## <a name="high-availability"></a>Vysoká dostupnost

Aby zákazník dosáhl vysoké dostupnosti, musí v oblasti, která je nakonfigurovaná pomocí Thales softwaru jako dvojice vysoké dostupnosti, používat dvě zařízení HSM. Tento typ nasazení zajišťuje dostupnost klíčů v případě, že dojde k potížím v jednom zařízení, které brání zpracování klíčových operací. Také významně snižuje riziko při provádění údržby break/opravit, jako je například náhrada zdroje napájení. Je důležité, aby se v rámci návrhu zohlednila jakákoli druh selhání regionální úrovně. Selhání regionální úrovně může nastat, pokud dojde k přirozeným katastrofám, jako je hurikány, zaplavení nebo zemětřesení. Tyto typy událostí by se měly zmírnit zřizováním zařízení HSM v jiné oblasti. Zařízení nasazená v jiné oblasti se můžou párovat společně prostřednictvím konfigurace softwaru Thales. To znamená, že minimální nasazení pro vysoce dostupné a odolné navýšení po havárii je čtyři zařízení HSM ve dvou oblastech. Místní redundanci a redundanci napříč oblastmi se dají použít jako základ pro přidání jakýchkoli dalších nasazení zařízení HSM za účelem podpory latence, kapacity nebo splnění jiných požadavků na konkrétní aplikace.

## <a name="distributed-application-support"></a>Podpora distribuované aplikace

Vyhrazená zařízení HSM se obvykle nasazují v podpoře aplikací, které potřebují provádět operace klíčeového úložiště a načítání klíčů. Vyhrazená zařízení HSM mají 10 oddílů pro podporu nezávislé aplikace. Umístění zařízení by mělo být založené na holistický zobrazení všech aplikací, které potřebují službu používat.

## <a name="next-steps"></a>Další kroky

Po určení architektury nasazení bude služba Thales obsahovat většinu konfiguračních aktivit pro implementaci této architektury. To zahrnuje konfiguraci zařízení i scénáře integrace aplikací. Další informace najdete na portálu [zákaznická podpora Thales](https://supportportal.thalesgroup.com/csm) a na webu Průvodce správou a konfigurací. Partnerský web Microsoftu má řadu průvodců integrací.
Doporučuje se, aby všechny klíčové koncepty služby, jako je vysoká dostupnost a zabezpečení, byly dobře srozumitelné před zřizováním nebo návrhem a nasazením aplikací.
Další témata o úrovni konceptu:

* [Vysoká dostupnost](high-availability.md)
* [Fyzické zabezpečení](physical-security.md)
* [Sítě](networking.md)
* [Možnosti podpory](supportability.md)
* [Monitorování](monitoring.md)
