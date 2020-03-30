---
title: Podpora – azure dedicated hsm | Dokumenty společnosti Microsoft
description: Možnosti podpory a oblasti odpovědnosti za azure dedicated hsm v různých scénářích
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881016"
---
# <a name="azure-dedicated-hsm-supportability"></a>Podpora azure dedicated hsm

Služba Azure Dedicated HSM Service poskytuje fyzické zařízení pro použití jediným zákazníkem s úplnou odpovědností za správu a správu. K dispozici je dostupný přístroj [Gemalto SafeNet Luna 7 HSM model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Společnost Microsoft nebude mít po zřízení zákazníkem žádný přístup pro správu, kromě fyzické přílohy sériového portu jako role monitorování.  Bez přístupu nemůže mít společnost Microsoft žádné průběžné povinnosti údržby na úrovni softwaru nebo správy systému. V důsledku toho jsou zákazníci zodpovědní za typické provozní činnosti.
Zákazníci jsou plně zodpovědní za aplikace, které používají skryté funkce dohledu, a měli by spolupracovat se společností Gemalto na podpoře nebo poradenství. Vzhledem k rozsahu vlastnictví provozní hygieny zákazníkem není možné, aby společnost Microsoft nabídla pro tuto službu jakoukoli záruku vysoké dostupnosti. Je odpovědností zákazníka zajistit, aby jejich aplikace byly správně nakonfigurovány tak, aby bylo dosaženo vysoké dostupnosti. Společnost Microsoft bude monitorovat a udržovat stav zařízení a připojení k síti.

## <a name="getting-support"></a>Získání podpory

Zákaznická podpora pro vyhrazený modul hardwarového zabezpečení je společným úsilím společností Microsoft a Gemalto. Všechny problémy s hardwarem nebo s cestou k síti budou řešeny společností Microsoft a společnost Gemalto bude řešit cokoli, co má co do činění se skutečným modulem hardwarového zabezpečení, jako je konfigurace, software, firmware a vývoj aplikací. Tento model podpory zajišťuje nejrychlejší cestu k co nejefektivnější podpoře. V případě pochybností o konkrétním problému vzdejte u společnosti Microsoft žádost o podporu a my zajistíme, abyste byli odpovídajícím způsobem přesměrováni. Společnost Microsoft zůstane zapojena do všech scénářů podpory a bude usilovat o nejlepší prostředí podpory pro naše zákazníky.

## <a name="gemalto-support"></a>Podpora pro Gemalto

Zákazníci, kteří využívají vyhrazenou službu hardwarového zabezpečení, se kvalifikují pro podporu od společnosti Gemalto podle plánu podpory Plus. To vyžaduje pouze registrační proces pomocí portálu podpory Gemalto. V rámci počátečního zapojení společnosti Microsoft o získání přístupu k vyhrazené službě hardwarového zabezpečení bude poskytnuto id zákazníka a pokyny. Mechanismus pro získání podpory od společnosti Gemalto je prostřednictvím [portálu zákaznické podpory](https://supportportal.gemalto.com/csm/).
Klíčovým bodem je, že Gemalto bude poskytovat veškerý software a dokumentaci potřebnou k používání hsm (například software pro přístup ke klientům a sady SDK) prostřednictvím stahování na portálu zákaznické podpory.

### <a name="software-components"></a>Softwarové komponenty

V konfiguraci zařízení HSM se používají různé softwarové komponenty:

* Klientský software
* Sada SDK
* Nástroje

### <a name="guidance"></a>Doprovodné materiály

Gemalto zpřístupňuje administraci a konfigurační pokyny prostřednictvím [portálu zákaznické podpory](https://supportportal.gemalto.com/csm/). Po přihlášení pomocí platného ID zákazníka jsou tyto dokumenty k dispozici ke stažení. Gemalto také poskytuje řadu integračních průvodců, které pomáhají zákazníkům s různými scénáři a softwarovými integracemi. Další informace naleznete na [partnerském webu Gemalto pro společnost Microsoft](https://safenet.gemalto.com/partners/microsoft/).

### <a name="support"></a>Podpora

Jakýkoli problém nebo otázka na úrovni softwaru týkající se používání modulů hardwarového zabezpečení jako součásti vyhrazené služby hardwarového zabezpečení by měly být adresovány přímo podpoře společnosti Gemalto. Všechny softwarové součásti uvedené výše a všechny vlastní konfigurace hsm, který je post-provisioning, budou řešeny Gemalto. Další informace naleznete na [portálu zákaznické podpory Gemalto](https://supportportal.gemalto.com/csm/).

### <a name="consulting-services"></a>Konzultační služby

Pro jakoukoli pomoc při návrhu, vývoji a nasazení vlastních aplikací, které používají hsm, obraťte se na zástupce účtu Gemalto.

## <a name="microsoft-support"></a>Podpora Microsoftu

Společnost Microsoft zajistí, aby fyzická zařízení s připojením k vzdušnému zabezpečení byla přístupná k síti a v provozním stavu pro výhradní použití jednoho zákazníka. Zákazníci jsou zodpovědní za konfiguraci, správu a správu zařízení. Mezi povinnosti společnosti Microsoft patří:

* Ujistěte se, že zařízení má napájení a chlazení
* Udržování provozního stavu objektu zabezpečení zabezpečení (například scénáře přerušení/opravy)
* Zařízení je přístupné po síti.

Společnosti Microsoft by měly být hlášeny následující problémy:

* Selhání součásti
* Úplné selhání zařízení
* Problémy s přístupem k síti
* Problémy zřizování a zrušení zřizování.

Společnost Microsoft má fyzický přístup k sériovému portu k zařízení prostřednictvím role monitorování (tj. nikoli role pro správu), která umožňuje základní telemetrii stavu.  To společnosti Microsoft umožní poskytovat zákazníkovi proaktivní oznámení o problémech, pokud se zákazník nerozhodne toto oprávnění omezit. 

### <a name="provisioning-and-decommissioning"></a>Zajišťování a vyřazování z provozu

Poté, co má zákazník schválenou registraci pro vyhrazenou službu hardwarového zabezpečení, bude moct vytvářet prostředky modulu hardwarového zabezpečení (v současné době prostřednictvím prostředí PowerShell nebo rozhraní příkazového řádku a nikoli portálu Azure). Prostředek prochází procesem přidělení, který mapuje fyzické zařízení v zadané oblasti, na předdefinovanou virtuální síť (VNet) zákazníka. Jakmile je zákazník viditelný ve virtuální síti, může k zařízení přistupovat a dále ho nakonfigurovat podle požadavků. Zákazníci přistupují ke svým vyhrazeným modulům hardwarového zabezpečení pomocí klientského softwaru a nástrojů Gemalto. Proces vytváření prostředků je podporován společností Microsoft. Vlastní proces konfigurace a dále jsou podporovány Gemalto. (viz podpora Gemalto výše). Po dokončení používání hsmm, musí být reset (nebo nulování) zajistit žádné trvalost dat. Proces resetování zařízení odebere všechny vlastní konfigurace a data. Microsoft navrátí zařízení a vrátí jej do fondu v nedotčeném stavu. To znamená, že když je zařízení vráceno do fondu neexistuje žádný důkaz o předchozí aktivity zákazníka. 

### <a name="hardware-issues"></a>Problémy s hardwarem

Zařízení HSM má redundantní a vyměnitelné napájecí zdroje a ventilátory.  Odstranění jednotky ventilátoru však stále způsobí tamper událost. Dojde-li k selhání součásti, použije společnost Microsoft nejvhodnější proces k řešení problému na úrovni komponent způsobem, který způsobuje minimální přerušení a nejnižší riziko pro dostupnost služeb zákazníkům.
Jakékoli vážnější selhání zařízení bude mít za následek, že zařízení bude nahrazeno novým zařízením z volného fondu. Zákazník jednoduše zahrne nové zařízení do stávajícího páru HA, aby se synchronizoval a vrátil do plného provozního stavu. V selhání zařízení budou jeho datová ložiska odebrána a skartována na místě v datovém centru. Pouze podvozek bude vrácen do Gemalto k recyklaci.


### <a name="networking-issues"></a>Problémy se sítí

Pokud se zákazníci sejdou s problémy s přístupem k síti k zařízení s hsm, měli by kontaktovat podporu společnosti Microsoft. Jednoduchý test pro přístup k síti je použití SSH pro připojení k zařízení HSM. Pokud se to nezdaří, obraťte se na podporu společnosti Microsoft.

## <a name="service-level-expectations-for-support"></a>Očekávání podpory na úrovni služeb

Úrovně služeb podpory Microsoftu najdete v [plánu podpory Azure](https://azure.microsoft.com/support/plans/).
Úrovně služeb podpory Gemalto naleznete v části [Gemalto Support Essentials](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Další kroky

Doporučuje se, aby klíčové koncepty, jako je vysoká dostupnost a zabezpečení, byly dobře pochopeny před zřizováním zařízení a návrhem nebo nasazením aplikací.

* [Architektura nasazení](deployment-architecture.md)
* [Vysoká dostupnost](high-availability.md)
* [Fyzické zabezpečení](physical-security.md)
* [Síťové služby](networking.md)

