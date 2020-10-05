---
title: Běžné scénáře s důvěrnými výpočetními procesy Azure a případy použití
description: Pochopte, jak ve svém scénáři používat důvěrný výpočetní výkon.
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.topic: overview
ms.date: 9/22/2020
ms.author: jencook
ms.openlocfilehash: e1280ac90032869616830ccb931b367ff22f8bfb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91000936"
---
# <a name="common-scenarios-for-azure-confidential-computing"></a>Běžné scénáře pro důvěrný výpočetní výkon Azure

Tento článek poskytuje přehled několika běžných scénářů pro důvěrný výpočetní výkon Azure. Doporučení v tomto článku slouží jako výchozí bod při vývoji aplikace s využitím důvěrných výpočetních služeb a platforem. 

Po přečtení tohoto článku budete moci zodpovědět následující otázky:

- Jaké jsou některé scénáře týkající se důvěrného výpočetního prostředí Azure?
- Jaké jsou výhody používání důvěrného výpočetního prostředí Azure pro scénáře s více stranami?
- Jaké jsou výhody používání důvěrného výpočetního prostředí Azure v blockchain síti?


## <a name="secure-multi-party-computation"></a>Zabezpečení výpočtu více stran
Důvěrné výpočetní prostředí Azure umožňuje zpracovávat data z více zdrojů bez odhalení vstupních dat jiným stranám. Tento typ zabezpečeného výpočtu umožňuje spoustu scénářů, jako je například praní proti podvodným penězům, odhalování podvodů a bezpečná analýza dat zdravotnictví.

Více zdrojů může nahrávat svá data do jednoho enklávy ve virtuálním počítači. Jedna strana oznamuje, že enklávy provede výpočet nebo zpracování dat. Žádné strany (ne ani ta, která provádí analýzu), můžou ve skutečnosti vidět data jiných stran, která se nahrála do enklávy. 

V zabezpečeném výpočetním prostředí s více stranami přenáší zašifrovaná data do enklávy, enklávy dešifruje data pomocí klíče, provede analýzu, získá výsledek a vrátí šifrovaný výsledek, který může strana dešifrovat pomocí určeného klíče. 

**Chránit použitá data**: 
- Použijte virtuální počítač DCsv2-Series v Azure s povolenou podporou Intel SGX. Tyto virtuální počítače jsou povolené pomocí prostředí pro důvěryhodné spuštění (TEEs), které zabezpečují a izolují části dat a kódu vaší aplikace.
- Pomocí sady SDK s podporou enklávy můžete vytvořit enklávy uvnitř virtuálního počítače. V rámci enklávy nebudou data vystavena žádnému, ani poskytovateli virtuálního počítače. Data v enklávy budou šifrována hardwarovou podporou.
    - Můžete například použít [sadu OE SDK](https://github.com/openenclave/openenclave) pro zpracování na straně serveru. 

**Ochrana dat během přenosů** 
- Použití ověřeného kanálu TLS jako zabezpečeného kanálu pro zajištění zabezpečení dat při přenosu
- Klient zajišťuje, aby byla data posílána pouze na stejný server, který je chráněn enklávy. 

**Ochrana neaktivních uložených dat**
- Používejte chráněná a zabezpečená úložiště dat k zajištění zabezpečení dat v klidovém režimu. 

### <a name="anti-money-laundering"></a>Praní proti špinavým penězům
V tomto příkladu zabezpečeného zpracování více stran sdílí několik bank mezi sebou data bez odhalení osobních údajů svých zákazníků. V kombinaci s citlivými datovými sadami bank běžely dohodnuté analýzy. Analýza agregované datové sady může zjistit pohyb peněz jedním uživatelem mezi více bankami, aniž by bankům přistupovali k datům druhé strany.

V rámci důvěrného výpočetního prostředí můžou tyto finanční instituce zvýšit míru odhalování podvodů, řešit scénáře boje proti podvodům, snižovat falešně pozitivní výsledky a pokračovat ve učení s většími datovými sadami. 

### <a name="drug-development-in-healthcare"></a>Vývoj drog v lékařské péči
Partnerská zařízení pro vzdělávání přispívají k tomu, že poskytují sady dat o soukromých stavech a pomáhají modelovat ML. Každé zařízení může zobrazit jenom svou vlastní datovou sadu. Žádný jiný prostředek nebo dokonce poskytovatel cloudu může zobrazit model dat nebo školení. 

![Analýza stavu pacienta](./media/use-cases-scenarios/patient-data.png)

Všechna zařízení využívají vyškolený model. Vytvořením modelu s více daty byl model přesnější. Každé zařízení, které přispívá k výuce modelu, ho může použít a získat užitečné výsledky. 

## <a name="blockchain"></a>Blockchain

Blockchain síť je decentralizovaná síť uzlů. Tyto uzly jsou spuštěné a udržované operátory nebo validátory, které chtějí zajistit integritu a dosahovat konsensu na stav sítě. Samotné uzly jsou replikami v hlavní knize a používají se ke sledování transakcí blockchain. Každý uzel má úplnou kopii historie transakcí, která zajišťuje integritu a dostupnost v distribuované síti.

Blockchain technologie postavené na důvěrných computingech můžou používat hardwarové ochrany osobních údajů, které umožňují utajení dat a zabezpečení výpočtů. V některých případech je celá hlavní kniha zašifrovaná, aby se zajistil přístup k datům. V některých případech se může stát, že se k samotné transakci dojde v rámci modulu COMPUTE uvnitř uzlu enklávy v rámci uzlu.

### <a name="confidential-consortium-framework-ccf"></a>Důvěrné rozhraní Consortium (CCF)
[CCF](https://www.microsoft.com/research/project/confidential-consortium-framework/) je jedním z příkladů distribuovaných blockchain rozhraní postaveného na důvěrných výpočetních computingech Azure. V spearheaded podle Microsoft Research toto rozhraní využívá sílu prostředí Trusted Execution (TEEs) k vytvoření sítě vzdálených enclaves pro ověření identity. Uzly můžou běžet na virtuálních počítačích Azure ([DCsv2-Series](confidential-computing-enclaves.md)) a využívat infrastrukturu enklávy. Pomocí protokolů ověření identity můžou uživatelé blockchain ověřit integritu jednoho uzlu CCF a efektivně ověřit celou síť. 

![Síť uzlů](./media/use-cases-scenarios/ccf.png)

V CCF je decentralizovaná účetní kniha tvořena zaznamenanými změnami úložiště hodnot klíčů, které jsou replikovány napříč všemi síťovými uzly. Každý z těchto uzlů spouští transakční modul, který mohou aktivovat uživatelé blockchain přes TLS. Po aktivaci koncového bodu jste provedete uložení klíč-hodnota. Předtím, než se zašifrovaná změna zaznamená do decentralizované hlavní knihy, je nutné, aby se na základě konsensu dosáhlo určitého počtu uzlů. 

## <a name="next-steps"></a>Další kroky
[Nasaďte](quick-create-marketplace.md) virtuální počítač s DCsv2-Series.


