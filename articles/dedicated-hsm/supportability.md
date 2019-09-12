---
title: Možnosti podpory – vyhrazené Azure HSM | Dokumentace Microsoftu
description: Možnosti podpory a oblasti zodpovědnosti pro vyhrazené modulu hardwarového zabezpečení Azure v různých scénářích
services: dedicated-hsm
author: johndaw
manager: rkarlin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/27/2019
ms.author: mbaldwin
ms.openlocfilehash: d83d688707baf6098d63dfde9b4181eb04fb9729
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881016"
---
# <a name="azure-dedicated-hsm-supportability"></a>Možnosti podpory Azure vyhrazených HSM

HSM služby Azure Dedicated fyzické zařízení pro použití výhradně zákazník poskytuje kompletní správu ovládacího prvku a správu odpovědnost. Je zařízení k dispozici [Gemalto SafeNet Luna 7 HSM modelu A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Microsoft bude mít přístup žádný správce jednou zřízené ze strany zákazníka, nad rámec fyzické sériového portu přílohy monitorování roli.  Bez přístupu Microsoft může mít žádné probíhající softwaru úrovně údržby nebo systém správy odpovědnosti. V důsledku toho zodpovědností zákazníků typické provozní činnosti.
Zákazníci odpovídají plně pro aplikace, které využívají moduly hardwarového zabezpečení a by měla fungovat s Gemalto podpory nebo na základě consulting o pomoc. Z důvodu rozsahu zákazníka vlastnictví provozní kontrolu není možné pro společnost Microsoft nabízí jakýkoli druh se zárukou vysoké dostupnosti pro tuto službu. Je zodpovědností zákazníka k zajištění jejich aplikace jsou správně nakonfigurované k dosažení vysoké dostupnosti. Microsoft bude sledovat a udržovat zařízení stavu a připojení k síti.

## <a name="getting-support"></a>Získání podpory

Zákaznická podpora pro vyhrazený modul hardwarového zabezpečení (HSM) je společným úsilím mezi Microsoftem a identita Gemalto. Všechny problémy s hardwarem nebo problémy se síťovou cestou budou řešeny společností Microsoft a cokoli k tomu, aby se skutečný modul hardwarového zabezpečení (HSM), jako je konfigurace, software, firmware a vývoj aplikací, vyřešila identita Gemalto. Tento model podpory zajišťuje nejrychlejší směrování na nejúčinnější podporu. Pokud máte pochybnosti s konkrétním problémem, vyvolejte žádost o podporu u Microsoftu a my vám zajistíme, aby bylo správně směrováno. Společnost Microsoft se bude zabývat všemi scénáři podpory a usiluje o to, aby naši zákazníci měli nejlepší možnosti podpory.

## <a name="gemalto-support"></a>Podpora Gemalto

Zákazníci, kteří používají vyhrazenou službu HSM, mají nárok na podporu od identita Gemalto podle plánu jejich plus (support). To pouze vyžaduje proces registrace pomocí portálu podpory identita Gemalto. ID zákazníka a pokyny budou k dispozici v rámci počáteční zapojení do společnosti Microsoft za účelem získání přístupu k vyhrazené službě HSM. Mechanismus pro získání podpory od Gemalto je prostřednictvím jejich [zákaznického portálu služeb podpory](https://supportportal.gemalto.com/csm/).
Klíčovým bodem je, že identita Gemalto poskytne veškerý software a dokumentaci potřebné k používání modulu HSM (například software pro klientský přístup a sady SDK) prostřednictvím stažení na portálu zákaznické podpory.

### <a name="software-components"></a>Softwarové komponenty

V konfiguraci modulu hardwarového zabezpečení zařízení se používají různé softwarové součásti:

* Klientský software
* Sada SDK
* Nástroje

### <a name="guidance"></a>Doprovodné materiály

Gemalto je k dispozici správu a konfiguraci pokyny prostřednictvím [zákaznického portálu služeb podpory](https://supportportal.gemalto.com/csm/). Po přihlášení pomocí ID zákazníka platné tyto dokumenty jsou k dispozici ke stažení. Gemalto také obsahuje sadu integračních příručkách k zákazníkům usnadní různé scénáře a integrace softwaru. Další informace najdete v tématu [Gemalto web partnera Microsoftu](https://safenet.gemalto.com/partners/microsoft/).

### <a name="support"></a>Podpora

Mělo by se řešit jakékoli dotaz ve vztahu k pomocí moduly hardwarového zabezpečení jako součást služby vyhrazené HSM nebo software úrovně problém k Gemalto podporu přímo. Všechny softwarové komponenty, které jsou uvedené výše a jakékoli vlastní konfigurace modulu hardwarového zabezpečení, která je po zřízení, bude řešit Gemalto. Další informace najdete v tématu [Gemalto zákaznického portálu služeb podpory](https://supportportal.gemalto.com/csm/).

### <a name="consulting-services"></a>Konzultační služby

Pro všechny pomoc při navrhování, vývoj a nasazení vlastních aplikací, které používají modul hardwarového zabezpečení obraťte se na váš obchodní zástupce Gemalto.

## <a name="microsoft-support"></a>Podpora Microsoftu

Společnost Microsoft zajistí, aby fyzická zařízení HSM byla přístupná k síti a v provozním stavu pro výhradní použití jednoho zákazníka. Zákazníci zodpovídají za konfiguraci, správu a správu zařízení. Odpovědnosti Microsoftu patří:

* Zařízení musí napájení a chladicí
* Údržba provozního stavu modulu hardwarového zabezpečení (například scénáře řešení potíží)
* Zařízení je dostupný v síti.

Společnosti Microsoft by se měly hlásit problémy, jako je následující:

* Chyb na úrovni součástí
* Selhání celé zařízení
* Potíže s přístupem k síti
* Problémy zřizování a zrušení zřizování.

Microsoft má fyzické sériového portu přístup k zařízení pomocí sledování role (tedy ne pro správu role), která umožňuje základní stav telemetrie.  To vám umožní Microsoftu poskytují proaktivní oznámení o problémy zákazníkovi, pokud zákazník rozhodne omezit toto oprávnění. 

### <a name="provisioning-and-decommissioning"></a>Zřizování a vyřazování z provozu

Poté, co zákazník má schválené registrace pro službu vyhrazené modulu hardwarového zabezpečení, bude moct vytvářet prostředky modulu hardwarového zabezpečení (aktuálně prostřednictvím Powershellu nebo rozhraní příkazového řádku a ne na webu Azure portal). Prostředek prochází o přidělení proces, který se mapuje fyzické zařízení v zadané oblasti, na základě předem definované virtuální síť (VNet). Jakmile viditelné ve virtuální síti, může zákazník přístupu k zařízení a nakonfigurovat dále podle požadavků. Zákazníkům přístup k jejich dedikovaných modulů hardwarového zabezpečení pomocí Gemalto klientský software a nástroje. Společnost Microsoft podporuje proces vytváření prostředků. Vlastní konfigurace pro zpracování a novější podporuje Gemalto. (viz Gemalto podporují výše). Když zákazník dokončí, pomocí modulu hardwarového zabezpečení, se musí být resetování (nebo zeroized) k zajištění žádné trvalosti dat. Postup resetování zařízení odebere všechny vlastní konfigurace a data. Microsoft zruší přidělení zařízení a vrátí ji do fondu ve pristine stavu. To znamená, že když se zařízení vrátí do fondu, že není žádná doklad o předchozí aktivitě zákazníka. 

### <a name="hardware-issues"></a>Problémy s hardwarem

Zařízení HSM má redundantní a nahraditelné napájení a ventilátor jednotek.  Odebrání jednotky ventilátoru ale pořád způsobí událost manipulace. Pokud dojde k selhání součásti, Microsoft použije nejvhodnější k vyřešení problému úrovni komponenty způsobem, který způsobí, že nejnižší rizika na dostupnost služby našich zákazníků a minimálním přerušením.
Toto zařízení teď nahrazuje čerstvé jedním z volného fondu způsobí žádné závažnější selhání zařízení. Zákazník jednoduše obsahuje nové zařízení v existující pár HA pro ni k synchronizaci a vraťte se do plné provozní stav. Selhání zařízení bude mít svá data s zařízení odebrán a skartovány na webu v datovém centru. Pouze skříni se vrátí do Gemalto recyklace.


### <a name="networking-issues"></a>Problémy sítě

Pokud zákazníci síťové potíže s přístupem k modulu hardwarového zabezpečení zařízení, měli byste kontaktovat podporu Microsoftu. Jednoduchý test pro síťový přístup je pomocí SSH se připojte k zařízení HSM. Pokud to nepomůže, obraťte se na podporu Microsoftu.

## <a name="service-level-expectations-for-support"></a>Očekávání úrovni služby pro podporu

Úrovně služeb podpory společnosti Microsoft, najdete [plán podpory Azure](https://azure.microsoft.com/support/plans/).
Úrovně služeb pro podporu Gemalto, najdete [Gemalto podporují Essentials](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Další postup

Doporučuje se, že klíčových konceptů, jako je vysoká dostupnost a zabezpečení jsou dobře pochopitelné i před zřizování zařízení a návrh aplikace nebo nasazení.

* [Architektura nasazení](deployment-architecture.md)
* [Vysoká dostupnost](high-availability.md)
* [Fyzické zabezpečení](physical-security.md)
* [Sítě](networking.md)

