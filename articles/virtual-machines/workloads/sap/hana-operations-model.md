---
title: Provozní model SAP Hana v Azure (velké instance) | Dokumentace Microsoftu
description: Provozní model SAP Hana v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 36a648e2d46cce96a8ff663f45ccf45326898a84
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417002"
---
# <a name="operations-model-and-responsibilities"></a>Provozní model a zodpovědnosti

Služby, opatřeného SAP HANA v Azure (velké instance) je v souladu s služby Azure IaaS. Můžete získat instanci velká Instance HANA s nainstalovaným operačním systémem, která je optimalizovaná pro SAP HANA. Stejně jako u virtuálních počítačů Azure IaaS, většinu úloh, posílení zabezpečení operačního systému, instalace dalšího softwaru, instalaci HANA, provozní operačního systému a HANA a aktualizaci operačního systému a HANA je vaší odpovědností. Microsoft nebude vynucení aktualizací operačního systému nebo HANA na vás.

![Odpovědnosti SAP Hana v Azure (velké instance)](./media/hana-overview-architecture/image2-responsibilities.png)

Jak je znázorněno na diagramu, SAP HANA v Azure (velké instance) je více tenanty, které nabízejí IaaS. Ve většině případů je dělení zodpovědnosti na hranici infrastruktury operačního systému. Microsoft je zodpovědná za všechny aspekty služby pod řádkem operačního systému. Zodpovídáte za všechny aspekty služby nad řádku. Operační systém je na vás. Můžete nadále používat nejaktuálnější místních metod, které může využívat pro dodržování předpisů, zabezpečení, Správa aplikací, základ a operačního systému správy. V systémech zobrazují, jakoby se ve vaší síti všechny vztahující.

Tato služba je optimalizovaná pro SAP HANA, existují oblasti, kde potřebujete pro práci s Microsoft používat základní funkce infrastruktury pro dosažení co nejlepších výsledků.

Následující seznam obsahuje další podrobnosti na jednotlivých vrstev a vaše odpovědnosti:

**Sítě**: Všechny interní sítě pro razítko velká Instance spouštějí SAP HANA. Vaší zodpovědností zahrnuje přístup k úložišti, spojení mezi instancí (pro horizontální navýšení kapacity a další funkce), připojení na šířku a připojení k Azure, kde se hostuje aplikační vrstvě SAP na virtuálních počítačích. Zahrnuje také sítě WAN připojení mezi datovými centry Azure pro replikaci pro účely zotavení po havárii. Všechny sítě dělí podle tenanta a mít kvality služeb, které jsou použity.

**Úložiště**: Virtualizovaného rozdělit na oddíly úložiště pro všechny svazky, které jsou vyžadované servery SAP HANA, a také pro snímky. 

**Servery**: Vyhrazených fyzických serverů ke spuštění databáze SAP HANA přiřazen klientům. Servery sady s typem třídy můžu skladových položek jsou abstrahované hardwaru. Konfigurace serveru s těmito typy serverů se shromažďují a udržuje na profilů, které lze přesunout z jednoho fyzického hardwaru do jiného fyzického hardwaru. Takové (ruční) přesun profilu operacemi je možné porovnat trochu s opravou služeb Azure. Servery třídy SKU typu II nenabízí takovou schopnost.

**SDDC**: Software pro správu, který se používá pro správu datového centra jako softwarově definovaných entit. Umožňuje Microsoftu do fondu zdrojů pro škálování, dostupnost a kvůli výkonu.

**O/S**: Operační systém zvolíte (SUSE Linux nebo Red Hat Linux), na kterém běží na serverech. Pro spuštění SAP HANA bylo zadáno Image operačních systémů, které jsou součástí jednotlivých dodavatel Linuxových společnosti Microsoft. Musíte mít předplatné s dodavatelem Linux pro konkrétní image optimalizovaných pro SAP HANA. Zodpovídáte za registraci bitové kopie operačního systému dodavatele. 

Z bodu předání společností Microsoft zodpovídáte za všechny další opravy operačního systému Linux. Tato oprava obsahuje další balíčky, který může být nezbytné pro úspěšnou instalaci SAP HANA, která nejsou zahrnuta konkrétní dodavatel Linuxových v jejich SAP HANA optimalizované bitové kopie operačního systému. (Další informace najdete v tématu SAP HANA dokumentaci k instalaci a poznámkách.) 

Zodpovídáte za opravy operačního systému z důvodu selhání nebo optimalizace operačního systému a jeho ovladače vzhledem ke konkrétní serverového hardwaru. Můžete také zodpovídají za zabezpečení nebo funkční opravy operačního systému. 

Vaše odpovědnosti také zahrnuje monitorování a plánování kapacity:

- Spotřeba prostředků procesoru.
- Využití paměti.
- Správa pro diskové svazky související s volného místa, vstupně-výstupních operací a latenci.
- Objem síťového provozu mezi velká Instance HANA a aplikační vrstvě SAP.

Základní infrastruktury velká Instance HANA poskytuje funkce pro zálohování a obnovení svazek s operačním systémem. Pomocí této funkce je také vaší povinností.

**Middleware**: SAP HANA Instance primárně. Správa, operace a monitorování jsou vaší odpovědností. Zadaná funkce můžete používat úložiště snímků pro účely obnovení zálohování a obnovení a po havárii. Tyto možnosti jsou k dispozici v infrastruktuře. Vaše odpovědnosti také zahrnovat návrh vysoké dostupnosti nebo zotavení po havárii s těmito možnostmi monitorování a jejich využitím k určení, zda snímků úložiště byl úspěšně proveden.

**Data**: Vaše data spravovaná přes SAP HANA a další data, jako je zálohování sdílených složek soubory jsou umístěné v svazků nebo souborů. Vaše odpovědnosti patří monitorování volného místa na disku a správu obsahu ve svazcích. Také zodpovídáte za monitorování úspěšné provedení zálohy diskové svazky a snímků úložiště. Úspěšné spuštění replikace dat do lokality pro zotavení po havárii zodpovídá Microsoft.

**Aplikace:** Instance aplikace SAP nebo v případě aplikací mimo systém SAP, aplikační vrstvu těchto aplikací. Vaše odpovědnosti patří nasazení, správu, operací a monitorování těchto aplikací. Zodpovídáte za spotřebu prostředků procesoru, využití paměti, spotřebu úložiště Azure a využití šířky pásma sítě v rámci virtuálních sítí plánování kapacity. Můžete také zodpovídají za plánování kapacity pro spotřebu prostředků z virtuálních sítí k SAP HANA v Azure (velké instance).

**Sítě WAN**: Připojení, které vytvoříte v místním nasazení Azure pro úlohy. Všichni zákazníci s velká Instance HANA pomocí Azure ExpressRoute pro připojení. Toto připojení, které nejsou součástí SAP Hana v Azure (velké instance) řešení. Zodpovídáte za nastavení tohoto připojení.

**Archiv**: Pravděpodobně budete chtít archivovat kopie dat pomocí vlastní metody v účtech úložiště. Archivace vyžaduje správu, dodržování předpisů, náklady a provoz. Zodpovídáte za generování archivu kopií a záloh v Azure a ukládat je na vyhovující způsobem.

Zobrazit [smlouva SLA pro SAP HANA v Azure (velké instance)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

**Další kroky**
- Přečtěte si [architektura SAP HANA (velké instance) v Azure](hana-architecture.md)
