---
title: Architektura nasazení – azure dedicated hsm | Dokumenty společnosti Microsoft
description: Základní aspekty návrhu při použití azure dedicated hsm jako součást architektury aplikace
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 89e3bf95a6b048e5e97cfb151ef9302b70eac1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048553"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Architektura nasazení Azure Dedicated HSM

Vyhrazený modul hardwarového zabezpečení Azure poskytuje úložiště kryptografických klíčů v Azure. Splňuje přísné bezpečnostní požadavky. Zákazníci budou mít prospěch z používání vyhrazeného modulu hardwarového zabezpečení Azure, pokud:

* Musí splňovat certifikaci FIPS 140-2 Level 3
* Vyžadovat, aby měli výhradní přístup k přístupu k hst
* měli mít úplnou kontrolu nad svými zařízeními

Soubory zabezpečení zabezpečení jsou distribuovány v datových centrech společnosti Microsoft a lze je snadno zřídit jako dvojici zařízení jako základ vysoce dostupného řešení. Mohou být také nasazeny napříč regiony za řešením odolným proti katastrofám. Regiony s vyhrazeným modulu hardwarového zabezpečení, které jsou v současné době k dispozici, jsou:

* USA – východ
* USA – východ 2
* USA – západ
* USA – středojih
* Jihovýchodní Asie
* Východní Asie
* Indie – střed
* Indie – jih
* Japonsko – východ
* Japonsko – západ
* Severní Evropa
* Západní Evropa
* Spojené království – jih
* Spojené království – západ
* Střední Kanada
* Kanada – východ
* Austrálie – východ
* Austrálie – jihovýchod

Každá z těchto oblastí má řadiče domény nasazené ve dvou nezávislých datových centrech nebo alespoň ve dvou nezávislých zónách dostupnosti. Jihovýchodní Asie má tři zóny dostupnosti a východní USA 2 má dvě. Existuje celkem osm oblastí v Evropě, Asii a USA, které nabízejí vyhrazené služby HSM. Další informace o oblastech Azure najdete v oficiálních [informacích o oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).
Některé faktory návrhu pro jakékoli vyhrazené řešení založené na modulu hardwarového zabezpečení jsou umístění nebo latence, vysoká dostupnost a podpora pro jiné distribuované aplikace.

## <a name="device-location"></a>Device location (Umístění zařízení)

Optimální umístění zařízení HSM je v nejbližší blízkosti aplikací provádějících kryptografické operace. V oblasti latence se očekává, že jednociferné milisekundy. Latence mezi oblastmi může být 5 až 10krát vyšší než tato.

## <a name="high-availability"></a>Vysoká dostupnost

Aby bylo dosaženo vysoké dostupnosti, musí zákazník používat dvě zařízení hsm v oblasti, které jsou nakonfigurovány pomocí softwaru Gemalto jako dvojice s vysokou dostupností. Tento typ nasazení zajišťuje dostupnost klíčů, pokud jedno zařízení dojde k problému, který mu brání ve zpracování operací klíče. To také výrazně snižuje riziko při provádění break / fix údržby, jako je výměna napájecího zdroje. Je důležité, aby návrh zohledňoval jakýkoli druh selhání na regionální úrovni. K selháním na regionální úrovni může dojít při přírodních katastrofách, jako jsou hurikány, záplavy nebo zemětřesení. Tyto typy událostí by měly být zmírněny zřizováním zařízení hsm v jiné oblasti. Zařízení nasazená v jiné oblasti mohou být spárována pomocí softwarové konfigurace Gemalto. To znamená, že minimální nasazení pro vysoce dostupné a odolné řešení pro katastrofy jsou čtyři zařízení hsm ve dvou oblastech. Místní redundance a redundance napříč oblastmi lze použít jako směrný plán pro přidání dalších nasazení zařízení s modulu hesm pro podporu latence, kapacity nebo ke splnění jiných požadavků specifických pro aplikaci.

## <a name="distributed-application-support"></a>Podpora distribuovaných aplikací

Vyhrazená zařízení hardwarového zabezpečení se obvykle nasazují na podporu aplikací, které potřebují provádět operace úložiště klíčů a načítání klíčů. Vyhrazená zařízení hardwarového zabezpečení mají 10 oddílů pro nezávislou podporu aplikací. Umístění zařízení by mělo být založeno na ucelené zobrazení všech aplikací, které potřebují službu používat.

## <a name="next-steps"></a>Další kroky

Jakmile je určena architektura nasazení, většina konfiguračních aktivit k implementaci této architektury bude poskytována společností Gemalto. To zahrnuje konfiguraci zařízení, stejně jako scénáře integrace aplikací. Další informace získáte na portálu [zákaznické podpory Gemalto](https://supportportal.gemalto.com/csm/) a v příručkách ke správě a konfiguraci. Partnerský web společnosti Microsoft má řadu integračních příruček.
Doporučuje se, aby všechny klíčové koncepty služby, jako je například vysoká dostupnost a zabezpečení, byly dobře pochopeny před zřizováním zařízení nebo návrhem a nasazením aplikací.
Další témata na úrovni konceptu:

* [Vysoká dostupnost](high-availability.md)
* [Fyzické zabezpečení](physical-security.md)
* [Síťové služby](networking.md)
* [Možnosti podpory](supportability.md)
* [Sledování](monitoring.md)
