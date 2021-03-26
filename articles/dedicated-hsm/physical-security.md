---
title: Fyzické zabezpečení HSM – vyhrazený modul HSM v Azure | Microsoft Docs
description: Informace o fyzickém zabezpečení zařízení s vyhrazeným HSM v Azure v datových centrech
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 8437d12075a148a3e8062f62e195bc019d89a85b
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606959"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Vyhrazené fyzické zabezpečení HSM Azure

Vyhrazený modul HARDWAROVÉho zabezpečení Azure vám pomůže splnit pokročilé požadavky na zabezpečení pro úložiště klíčů. Spravuje se po celém životním cyklu přísné postupy zabezpečení, které vyhovují potřebám zákazníků.

## <a name="security-through-procurement"></a>Zabezpečení prostřednictvím nákupů

Microsoft se řídí zabezpečeným procesem zadávání. Spravujeme řetěz úschovy a zajišťují, že konkrétní zařízení, které obchází a dodává, je zařízení přicházející v našich datových centrech. Tato zařízení jsou v serializovaných obalech a kontejnerech plastických neoprávněných událostí. Jsou uloženy v zabezpečené oblasti úložiště, dokud neproběhne v galerii dat v datovém centru.  Stojany, které obsahují zařízení HSM, se považují za vysoký dopad na firmu (HBI). Zařízení jsou zamčená a v rámci kontroly videa jsou neustále vpřed a zpět.

## <a name="security-through-deployment"></a>Zabezpečení prostřednictvím nasazení

HSM se instalují do racků společně s přidruženými síťovými součástmi. Po instalaci je potřeba nakonfigurovat, aby byly dostupné v rámci vyhrazené služby HSM Azure. Tuto aktivitu konfigurace provádí zaměstnanci Microsoftu, kteří prošli kontrolou na pozadí. Správa "just in time" (JIT) slouží k omezení přístupu jenom na ty správné zaměstnance a jenom na dobu, kdy je potřeba přístup. Používané postupy a systémy také zajistí, že se protokolují všechny aktivity související se zařízeními HSM.

## <a name="security-in-operations"></a>Zabezpečení v provozu

HSM jsou hardwarová zařízení (skutečný modul HARDWAROVÉho zabezpečení v rámci zařízení), aby mohlo dojít k problémům na úrovni součástí. K potenciálním problémům patří mimo jiné ventilátory a chyby napájení. Tento typ události bude vyžadovat, aby aktivity údržby nebo přerušení/opravy nahradily jakékoli odkládací součásti.

### <a name="component-replacement"></a>Výměna součásti

Po zřízení zařízení a v části Správa zákazníků jsou jediné součásti, které by se nahradily zdrojem napájení Hot-swap. Tato součást je mimo hranice zabezpečení a nezpůsobuje událost manipulace. Systém lístků se používá k autorizaci Microsoft inženýra za účelem přístupu k zadnímu stojanu HBI. Při zpracování lístku se vydá dočasný fyzický klíč. Tento klíč dává inženýrovi přístup k zařízení a umožňuje přepínat ovlivněné součásti. Pokud se zařízení nepřiřazuje zákazníkovi a tím se minimalizuje riziko zabezpečení a dostupnosti, bude se provádět jakýkoli jiný přístup (tj. způsobila neoprávněná událost).  

### <a name="device-replacement"></a>Nahrazení zařízení

V případě celkového selhání zařízení je následován proces podobný tomu, který se používá při selhání komponenty. Pokud zákazník nemůže zařízení zeroizeovat, nebo je v neznámém stavu, data zařízení se odeberou a umístí do přihrádky zničení v racku. Zařízení umístěná v přihrádce budou zničena řízeným a zabezpečeným způsobem. Žádná data, která zařízení nenesou z racku HBI, ponechají v datacentru Microsoftu.

### <a name="other-rack-access-activities"></a>Jiné aktivity přístupu do racku

Pokud má inženýr Microsoftu přístup k stojanu, který používají zařízení HSM (například údržba síťových zařízení), budou se standardní bezpečnostní postupy používat k získání přístupu k HBI zabezpečenému stojanu. Veškerý přístup bude pod dohledem videí. Zařízení HSM se ověřují na [úrovni FIPS 140-2 úrovně 3](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) , takže případný neoprávněný přístup k zařízením HSM se uživateli pošle jako signál a data se odeberou.

## <a name="logical-level-security-considerations"></a>Požadavky na zabezpečení logické úrovně

HSM se zřídí pro virtuální síť vytvořenou zákazníkem v rámci privátního adresního prostoru IP adres zákazníka.  Tato konfigurace poskytuje cennou izolaci na úrovni logické sítě a zajišťuje přístup pouze pro zákazníka. To znamená, že všechny ovládací prvky zabezpečení logické úrovně jsou zodpovědností zákazníka.

## <a name="next-steps"></a>Další kroky

Doporučuje se, aby všechny klíčové koncepty služby, jako je například vysoká dostupnost a zabezpečení a podpora, byly dobře pochopitelné před zřizováním zařízení, návrhem aplikací nebo nasazením.

* [Vysoká dostupnost](high-availability.md)
* [Sítě](networking.md)
* [Možnosti podpory](supportability.md)
* [Monitorování](monitoring.md)
* [Architektura nasazení](deployment-architecture.md)
