---
title: Monitorování možnosti – vyhrazené modulu hardwarového zabezpečení Azure | Dokumentace Microsoftu
description: Přehled vyhrazených modulu hardwarového zabezpečení Azure možnosti monitorování a sledování odpovědnosti
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 76a50c483b9246e3e2f9df97d5287f3e2fbd9897
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56104614"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Monitorování Azure vyhrazené HSM

HSM služby Azure Dedicated fyzické zařízení pro použití výhradně zákazník poskytuje kompletní správu ovládacího prvku a správu odpovědnost. Je zařízení k dispozici [Gemalto SafeNet Luna 7 HSM modelu A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).  Microsoft bude mít přístup žádný správce jednou zřízené ze strany zákazníka, nad rámec fyzické sériového portu přílohy monitorování roli. Zákazníci v důsledku toho jsou zodpovědné za typické provozní činnosti, včetně komplexní monitorování a analýzy protokolů.
Pro aplikace, které využívají moduly hardwarového zabezpečení a by měla fungovat s Gemalto pro podporu nebo konzultační pomoc plně zodpovídají zákazníci. Z důvodu rozsahu zákazníka vlastnictví provozní kontrolu není možné pro společnost Microsoft nabízí jakýkoli druh se zárukou vysoké dostupnosti pro tuto službu. Je zodpovědností zákazníka k zajištění jejich aplikace jsou správně nakonfigurované k dosažení vysoké dostupnosti. Microsoft bude sledovat a udržovat zařízení stavu a připojení k síti.

## <a name="microsoft-monitoring"></a>Monitorování Microsoft

Gemalto SafeNet zařízení používá má ve výchozím nastavení SNMP a sériového portu jako možnosti monitorování zařízení. Microsoft se používá připojení sériového portu, jako fyzický prostředek pro připojení k zařízení a získat základní telemetrie o stavu zařízení. Jedná se například stav teploty a komponenty, například napájení a fanoušky.
K dosažení tohoto cíle, společnost Microsoft používá bez oprávnění správce role "sledovat" na zařízení Gemalto. Tato role umožňuje načíst telemetrická data, ale neposkytují přístup k zařízení z hlediska správy úloh nebo žádným způsobem zobrazení informací o kryptografických. Naši zákazníci si být jistí, že zařízení je skutečně svoje vlastní, které spravujete, spravovat a používáte pro citlivé kryptografického klíče úložiště. V případě, že každý zákazník není spokojeni s minimálními přístup pro základní monitorování stavu, mají možnost vypnout monitorování účtu. Zřejmé důsledkem je, že společnost Microsoft nebude mít žádné informace, a proto žádné schopnost poskytovat všechna proaktivní oznámení o stavu zařízení vydá. V takovém případě zákazník zodpovídá za stavu zařízení.
Samotné funkce monitorování je nastavený na zařízení dotazovat každých 10 minut se získat data o stavu. Z důvodu chyby náchylné k chybám povaze sériové komunikace až po několik indikátory negativní stavu po dobu jedné hodiny bude vydána výstraha. Tato výstraha by nakonec vést k proaktivní zákaznickou komunikací upozornění problém.
V závislosti na povaze problému by se dostala vhodných akce dopad a zajistit nápravu s nízkým rizikem. Chyba napájení napájení je například hot-swap procedura se žádné výsledné manipulovat událostí, můžete to provést pomocí malý vliv a minimální riziko pro operaci. Další postupy můžou vyžadovat, aby zařízení zeroized a zrušení zřízení minimalizovat rizika zabezpečení zákazníkovi. V této situaci by zákazník zřizovat zařízení s alternativní, znovu připojit, vysokou dostupnost párování, tedy aktivuje synchronizaci zařízení. Normální provoz bude pokračovat za minimum času, s minimálním dopadem a nejnižší bezpečnostní riziko.  

## <a name="customer-monitoring"></a>Monitorování zákazníků

Návrh hodnoty vyhrazených HSM služby je ovládací prvek, který zákazník získá zařízení, zejména vzhledem k tomu, že je cloudové zařízení. V důsledku tohoto ovládacího prvku zodpovídá za ke sledování a správě stavu zařízení. Gemalto SafeNet zařízení obsahuje pokyny pro implementaci protokolu SNMP a Syslog. Zákazníci služby vyhrazené modulu hardwarového zabezpečení jsou vhodné pro použití to i v případě, že monitorování účtu Microsoft zůstane aktivní a zvažte jeho povinné Pokud, zakažte monitorování účtu Microsoft.
Buď techniku, která je k dispozici by umožnilo zákazníci mohli rozpoznat potíže a obraťte se na podporu společnosti Microsoft k zahájení pracovních odpovídajících nápravných přizpůsobit.

## <a name="next-steps"></a>Další postup

Doporučuje se, že všechny klíčové koncepty služby, jako je vysoká dostupnost a zabezpečení například jsou dobře pochopitelné i před zřizování zařízení a návrh aplikace nebo nasazení. Další témata úrovně koncept:

* [Vysoká dostupnost](high-availability.md)
* [Fyzické zabezpečení](physical-security.md)
* [Sítě](networking.md)
* [Možnosti podpory](supportability.md)
