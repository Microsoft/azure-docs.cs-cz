---
title: Podpora – vyhrazený modul HARDWAROVÉho zabezpečení Azure | Microsoft Docs
description: Možnosti podpory a oblasti zodpovědnosti za vyhrazený modul HARDWAROVÉho zabezpečení Azure v různých scénářích
services: dedicated-hsm
author: johndaw
manager: rkarlin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: cd949bdb7c489478df6a16d6dccd0bf358637604
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606925"
---
# <a name="azure-dedicated-hsm-supportability"></a>Podpora modulu HSM vyhrazeného pro Azure

Vyhrazená služba HSM v Azure poskytuje fyzické zařízení pro použití výhradně pro zákazníky s úplnou zodpovědností řízení a správou. K dispozici je zařízení [Thales Luna 7 model HSM A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms). Společnost Microsoft nebude mít po zřízení prostřednictvím fyzického připojení sériového portu jako role monitorování žádný přístup správce.  Bez přístupu může společnost Microsoft mít netrvalou zodpovědnost na úrovni softwaru ani správu systému. Výsledkem je, že zákazníci zodpovídají za běžné provozní činnosti.
Zákazníci jsou plně odpovědní za aplikace, které používají HSM, a měly by spolupracovat s Thales pro podporu nebo pomoc na základě konzultací. Vzhledem k rozsahu vlastnictví provozu zákazníka není možné, že společnost Microsoft nabízí pro tuto službu žádný druh záruky vysoké dostupnosti. Je zodpovědností zákazníka, aby bylo zajištěno, že jejich aplikace jsou správně nakonfigurovány tak, aby dosáhly vysoké dostupnosti. Microsoft bude monitorovat a udržovat stav zařízení a připojení k síti.

## <a name="getting-support"></a>Získání podpory

Zákaznická podpora pro vyhrazený modul hardwarového zabezpečení (HSM) je společným úsilím mezi Microsoftem a Thales. Všechny problémy s hardwarem nebo problémy se síťovou cestou budou řešeny společností Microsoft a cokoli k tomu, aby se skutečný modul hardwarového zabezpečení (HSM), jako je konfigurace, software, firmware a vývoj aplikací, vyřešila Thales. Tento model podpory zajišťuje nejrychlejší směrování na nejúčinnější podporu. Pokud máte pochybnosti s konkrétním problémem, vyvolejte žádost o podporu u Microsoftu a my vám zajistíme, aby bylo správně směrováno. Společnost Microsoft se bude zabývat všemi scénáři podpory a usiluje o to, aby naši zákazníci měli nejlepší možnosti podpory.

## <a name="thales-support"></a>Podpora Thales

Zákazníci, kteří používají vyhrazenou službu HSM, mají nárok na podporu od Thales podle plánu jejich plus (support). To pouze vyžaduje proces registrace pomocí portálu podpory Thales. ID zákazníka a pokyny budou k dispozici v rámci počáteční zapojení do společnosti Microsoft za účelem získání přístupu k vyhrazené službě HSM. Mechanismus pro získání podpory od Thales je prostřednictvím [portálu zákaznické podpory](https://supportportal.thalesgroup.com/csm).
Klíčovým bodem je, že Thales poskytne veškerý software a dokumentaci potřebné k používání modulu HSM (například software pro klientský přístup a sady SDK) prostřednictvím stažení na portálu zákaznické podpory.

### <a name="software-components"></a>Softwarové komponenty

V konfiguraci zařízení HSM se používají různé softwarové komponenty:

* Klientský software
* Sada SDK
* nástroje

### <a name="guidance"></a>Pokyny

Thales zpřístupňuje k dispozici pokyny pro správu a konfiguraci prostřednictvím [portálu zákaznické podpory Thales](https://supportportal.thalesgroup.com/csm). Po přihlášení pomocí platného ID zákazníka jsou tyto dokumenty k dispozici ke stažení. Thales také poskytuje řadu integračních průvodců, které zákazníkům pomůžou s různými scénáři a integrací softwaru. Další informace najdete na [partnerském webu Thales pro Microsoft](https://cpl.thalesgroup.com/partners/overview).

### <a name="support"></a>Podpora

Všechny problémy s úrovní softwaru nebo otázky související s používáním HSM jako součást vyhrazené služby HSM by se měly adresovat přímo podpoře Thales. Všechny softwarové součásti uvedené výše a všechny vlastní konfigurace HSM, které jsou po zřízení, budou řešeny nástrojem Thales. Další informace najdete na [portálu zákaznická podpora Thales](https://supportportal.thalesgroup.com/csm).

### <a name="consulting-services"></a>Konzultační služby

Pokud potřebujete pomoc při návrhu, vývoji a nasazení vlastních aplikací, které používají modul HARDWAROVÉho zabezpečení, obraťte se na svého zástupce účtu Thales.

## <a name="microsoft-support"></a>Podpora Microsoftu

Společnost Microsoft zajistí, aby fyzická zařízení HSM byla přístupná k síti a v provozním stavu pro výhradní použití jednoho zákazníka. Zákazníci zodpovídají za konfiguraci, správu a správu zařízení. Mezi zodpovědnosti Microsoftu patří:

* Ujistěte se, že zařízení má napájení a chlazení.
* Údržba provozního stavu modulu HARDWAROVÉho zabezpečení (například scénáře přerušení a opravy)
* Zařízení je přístupné přes síť.

Společnosti Microsoft by měly být hlášeny problémy, jako například následující:

* Selhání součásti
* Úplné selhání zařízení
* Problémy s přístupem k síti
* Problémy zřizování a rušení zřizování.

Microsoft má fyzický portový přístup k zařízení prostřednictvím role monitorování (která je role bez oprávnění správce), která umožňuje základní telemetrii stavu.  Tím umožníte, aby společnost Microsoft poskytovala proaktivní oznámení o problémech zákazníka, pokud se zákazník nerozhodne omezit toto oprávnění. 

### <a name="provisioning-and-decommissioning"></a>Zřizování a vyřazení z provozu

Poté, co zákazník obdrží schválenou registraci pro vyhrazenou službu HSM, bude moci vytvářet prostředky HSM (aktuálně prostřednictvím PowerShellu nebo rozhraní příkazového řádku, nikoli Azure Portal). Prostředek prochází procesem přidělení, který mapuje fyzické zařízení v určité oblasti na předem definovanou virtuální síť zákazníka. Po zobrazení ve virtuální síti může zákazník získat přístup k zařízení a nakonfigurovat ho dál podle požadavků. Zákazníci mají přístup ke svým vyhrazeným HSM pomocí klientského softwaru a nástrojů Thales. Microsoft podporuje proces vytváření prostředků. Thales podporuje vlastní konfigurační proces a novější. (viz Podpora Thales výše). Pokud zákazník dokončil používání modulu hardwarového zabezpečení (HSM), musí se resetovat (nebo se mu vyhodnotit nula), aby se zajistila žádná trvalá data. Proces resetování zařízení odebere veškerou vlastní konfiguraci a data. Microsoft zruší přidělení zařízení a vrátí ho do fondu ve stavu pristine. To znamená, že když se zařízení vrátí do fondu, neexistují žádné legitimace předchozí aktivity zákazníka. 

### <a name="hardware-issues"></a>Problémy s hardwarem

Zařízení HSM má redundantní a nahraditelný zdroj napájení a jednotky ventilátorů.  Odebrání jednotky ventilátoru ale pořád způsobí událost manipulace. Pokud dojde k selhání součásti, společnost Microsoft použije nejvhodnější proces k vyřešení problému na úrovni komponenty způsobem, který způsobí minimální přerušení a nejnižší riziko pro dostupnost služeb zákazníkům.
Veškerá závažná chyba zařízení bude mít za následek, že se zařízení nahradí novým zařízením z bezplatného fondu. Zákazník obsahuje jenom nové zařízení v existující dvojici HA, aby se mohlo synchronizovat a vrátit se do úplného provozního stavu. Zařízení, která selhala, budou mít svá data, která mají zařízení odebrána a skartována v datovém centru v lokalitě. 

### <a name="networking-issues"></a>Problémy se sítí

Pokud se zákazníkům v zařízení HSM setkávají problémy s přístupem k síti, měli by se obrátit na podporu Microsoftu. Jednoduchý test přístupu k síti je použití SSH pro připojení k zařízení HSM. Pokud se to nepovede, obraťte se na podporu Microsoftu.

## <a name="service-level-expectations-for-support"></a>Očekávání na úrovni služby pro podporu

Úroveň služeb podpory společnosti Microsoft najdete v [plánu podpory Azure](https://azure.microsoft.com/support/plans/).
Úroveň služeb Thales support najdete v tématu [základy podpory Thales](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Další kroky

Doporučuje se, aby klíčové koncepty, jako je vysoká dostupnost a zabezpečení, byly dobře pochopitelné před zřizováním a navrhováním aplikací a jejich nasazením.

* [Architektura nasazení](deployment-architecture.md)
* [Vysoká dostupnost](high-availability.md)
* [Fyzické zabezpečení](physical-security.md)
* [Sítě](networking.md)

