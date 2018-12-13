---
title: Architektura nasazení – vyhrazené modulu hardwarového zabezpečení Azure | Dokumentace Microsoftu
description: Aspekty návrhu základní při používání vyhrazeného modulu hardwarového zabezpečení Azure jako součást architektury aplikace
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 913be9d1ee6ce92d71f5f0c7eda46a618483ce8d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078289"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Architektura služby Azure vyhrazené nasazení modulu hardwarového zabezpečení

Azure vyhrazené HSM nabízí kryptografického klíče úložiště v Azure. Splňuje požadavky na přísné zásady zabezpečení. Zákazníci se benefitovat z použití vyhrazené modulu hardwarového zabezpečení Azure, pokud jsou:

* Musí splňovat FIPS 140-2 Level 3 certifikace
* Vyžadovat, aby měli exkluzivní přístup k modulu hardwarového zabezpečení
* by měl mít úplnou kontrolu nad svými zařízeními

Moduly hardwarového zabezpečení jsou distribuovány napříč datovými centry společnosti Microsoft a je možné snadno zřídit jako pár zařízení jako základ vysoce dostupné řešení. Může být také nasadit napříč oblastmi odolné řešení po havárii. Aktuálně jsou oblasti s vyhrazené modulu hardwarového zabezpečení k dispozici:

* USA – východ
* Východní USA 2
* USA – západ
* Střed USA – jih
* Jihovýchodní Asie
* Východní Asie
* Severní Evropa
* Západní Evropa

Každý z těchto oblastí má stojany modulu hardwarového zabezpečení, které jsou nasazené ve dvou nezávislých datových center nebo aspoň dva nezávislé dostupnost zóny. Jihovýchodní Asie má tři zóny dostupnosti a východní USA 2 má dvě. Je tam dohromady osm oblastí Evropa, Asie a USA, který nabízí služba Dedicated modulu hardwarového zabezpečení. Další informace o oblastech Azure, najdete v oficiální [informace o oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).
Některé faktory návrhu pro jakékoli řešení založené na vyhrazené modulu hardwarového zabezpečení jsou umístění/latenci, vysokou dostupnost a podporu dalších distribuované aplikace.

## <a name="device-location"></a>Device location (Umístění zařízení)

Optimální umístění zařízení HSM je v nejbližším aplikace provádí kryptografické operace. V oblasti latence má být řádu milisekund. Může být k 10 5krát vyšší než toto latence mezi různými oblastmi.

## <a name="high-availability"></a>Vysoká dostupnost

Abyste dosáhli vysoké dostupnosti, musíte použít zákazník dvě zařízení HSM v oblasti, které jsou nakonfigurované pomocí softwaru Gemalto jako společně s vysokou dostupností. Tento typ nasazení zajišťuje dostupnost klíče, pokud je jedno zařízení dojde k potížím znemožňuje zpracování klíčové operace. Při provádění údržby přerušení nebo opravy, jako je například power dodavatelského nahrazení také významně snižuje riziko. Je důležité návrh pro jakýkoli druh selhání v oblasti úrovně. Regionální úrovni selhání může dojít, když jsou přírodní pohromy, jako je například hurikány, záplavy nebo zemětřesení. Tyto typy událostí, které by měl zmírnit tím, že zajistíte zařízením hardwarového zabezpečení v jiné oblasti. Zařízení nasazený v jiné oblasti může spárovat společně prostřednictvím Gemalto softwarové konfigurace. To znamená, že minimální nasazení s vysokou dostupností a po havárii odolné řešení se čtyřmi zařízením hardwarového zabezpečení ve dvou oblastech. Místní redundanci a redundance napříč oblastmi může sloužit jako základ pro přidání dalšího nasazení zařízení HSM podporovat latence, kapacity nebo splňovat další požadavky specifické pro aplikaci.

## <a name="distributed-application-support"></a>Distribuované aplikace podpory

Vyhrazená zařízení HSM se obvykle nasazují podporu aplikací, které potřebují k provedení operace načtení klíčů a klíče úložiště. Vyhrazená zařízení HSM mít 10 oddíly pro podporu nezávislé aplikace. Umístění zařízení by měla vycházet z komplexní pohled na všechny aplikace, které potřebují používat službu.

## <a name="next-steps"></a>Další postup

Jakmile je určena architektura nasazení, většina aktivit konfigurace pro implementaci této architektury poskytovaný Gemalto. Jedná se o konfiguraci zařízení, stejně jako aplikace scénáře integrace. Další informace získáte pomocí [Gemalto zákaznickou podporu](https://supportportal.gemalto.com/csm/) portál a stažení příručky pro správu a konfiguraci. Partner společnosti Microsoft má řadu různých integračních příručkách.
Doporučuje se, že všechny klíčové koncepty služby, jako je vysoká dostupnost a zabezpečení například jsou dobře pochopitelné i před zřizování zařízení nebo aplikace návrhu a nasazení.
Další témata úrovně koncept:

* [Vysoká dostupnost](high-availability.md)
* [Fyzické zabezpečení](physical-security.md)
* [Sítě](networking.md)
* [Možnosti podpory](supportability.md)
* [Monitorování](monitoring.md)
