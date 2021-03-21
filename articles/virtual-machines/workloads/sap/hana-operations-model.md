---
title: Provozní model SAP HANA v Azure (velké instance) | Microsoft Docs
description: Provozní model SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9660540c60c6b3a734dc507a6056af9f1f8d6dc9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101675560"
---
# <a name="operations-model-and-responsibilities"></a>Provozní model a zodpovědnosti

Služba poskytovaná pomocí SAP HANA v Azure (velké instance) se zarovnává s Azure IaaS Services. Získáte instanci velké instance HANA s nainstalovaným operačním systémem, který je optimalizován pro SAP HANA. Stejně jako u virtuálních počítačů Azure IaaS je většina úloh posílení zabezpečení operačního systému, instalace dalšího softwaru, instalace HANA, provozu operačního systému a HANA a aktualizace operačního systému a HANA je vaší zodpovědností. Microsoft nenutí aktualizace operačního systému ani aktualizace pro HANA.

![Zodpovědnosti SAP HANA v Azure (velké instance)](./media/hana-overview-architecture/image2-responsibilities.png)

Jak je znázorněno v diagramu, SAP HANA v Azure (velké instance) je IaaS nabídka pro více tenantů. Ve většině případů je rozdělení zodpovědnosti na hranici infrastruktury operačního systému. Společnost Microsoft zodpovídá za všechny aspekty služby pod řádkem operačního systému. Zodpovídáte za všechny aspekty služby nad řádkem. Operační systém je vaší zodpovědností. Můžete dál používat většinu současných místních metod, které můžete využít k zajištění dodržování předpisů, zabezpečení, správy aplikací, zabezpečení a správy OS. Systémy se zobrazí jako v případě, že jsou ve vaší síti ve všech ohledech.

Tato služba je optimalizovaná pro SAP HANA, takže existují oblasti, ve kterých potřebujete spolupracovat s Microsoftem, abyste mohli používat základní možnosti infrastruktury pro dosažení nejlepších výsledků.

Následující seznam poskytuje další podrobnosti o každé z vrstev a jejich zodpovědnosti:

**Sítě**: všechny interní sítě pro označení velkých instancí, na kterých běží SAP HANA. Vaše zodpovědnost zahrnuje přístup k úložišti, připojení mezi instancemi (pro škálování a další funkce), připojení k krajině a připojení k Azure, kde je aplikační vrstva SAP hostovaná na virtuálních počítačích. Zahrnuje taky připojení WAN mezi datovými centry Azure a replikací pro účely zotavení po havárii. Všechny sítě jsou rozdělené do oddílů v rámci tenanta a jsou použité kvalitní služby.

**Storage**: virtualizované dělené úložiště pro všechny svazky, které potřebuje servery SAP Hana a také pro snímky. 

**Servery**: vyhrazené fyzické servery spouštějí SAP HANA databáze přiřazené klientům. Servery typu třída SKU jsou hardware abstraktní. U těchto typů serverů je konfigurace serveru shromažďována a udržována v profilech, které lze přesunout z jednoho fyzického hardwaru na jiný fyzický hardware. Takovým (ručním) Přesunutí profilu podle operací se dá porovnat s bitem Azure Service retušovací. Servery SKU třídy Type II nenabízejí takovou schopnost.

**SDDC**: software pro správu, který se používá ke správě datových center jako entit definovaných softwarem. Umožňuje Microsoftu sdružování prostředků do fondů pro účely škálování, dostupnosti a výkonu.

**O/S**: zvolený operační systém (SUSE Linux nebo Red Hat Linux), který běží na serverech. Bitové kopie operačního systému, které jste zadali, poskytl jednotliví dodavatel pro Linux Microsoftu pro spouštění SAP HANA. Pro konkrétní SAP HANA optimalizované bitové kopie musíte mít předplatné s dodavatelem systému Linux. Zodpovídáte za registraci imagí s dodavatelem operačního systému. 

Od předají od Microsoftu zodpovídáte za všechny další opravy operačního systému Linux. Tato oprava zahrnuje další balíčky, které mohou být nezbytné k úspěšné instalaci SAP HANA a které nebyly součástí konkrétního dodavatele pro Linux ve svých SAP HANA optimalizovaných bitových kopiích operačního systému. (Další informace najdete v dokumentaci k instalaci SAP na HANA a poznámky ke službě SAP.) 

Zodpovídáte za opravy operačního systému kvůli nefunkčnosti nebo optimalizaci operačního systému a jeho ovladačů vzhledem k určitému hardwaru serveru. Zodpovídáte také za zabezpečení nebo funkční opravy operačního systému. 

Vaše zodpovědnost zahrnuje i plánování monitorování a kapacity:

- Spotřeba prostředků procesoru.
- Spotřeba paměti.
- Diskové svazky související s volným místem, IOPS a latencí.
- Přenos síťových svazků mezi rozsáhlou instancí HANA a aplikační vrstvou SAP.

Základní infrastruktura velké instance HANA poskytuje funkce pro zálohování a obnovení svazku s operačním systémem. Používání této funkce je zároveň zodpovědností.

**Middleware**: instance SAP HANA, primárně. Vaše zodpovědnost za správu, provoz a monitorování. Pomocí poskytované funkce můžete používat snímky úložiště pro účely zálohování a obnovení a zotavení po havárii. Tyto možnosti poskytuje infrastruktura. Mezi vaše zodpovědnosti patří i návrh vysoké dostupnosti nebo zotavení po havárii s těmito možnostmi, jejich využití a monitorování, aby bylo možné zjistit, jestli se snímky úložiště úspěšně provedly.

**Data**: data spravovaná pomocí SAP Hana a další data, jako jsou například soubory zálohy umístěné ve svazcích nebo sdílených složkách souborů. Mezi vaše zodpovědnosti patří monitorování volného místa na disku a Správa obsahu na svazcích. Zodpovídáte také za monitorování úspěšného provedení zálohování svazků disku a snímků úložiště. Úspěšné spuštění replikace dat do lokalit pro zotavení po havárii je zodpovědností společnosti Microsoft.

**Aplikace:** Instance aplikace SAP nebo, v případě aplikací pro jiné než SAP, aplikační vrstva těchto aplikací. Mezi vaše zodpovědnosti patří nasazení, Správa, operace a monitorování těchto aplikací. Zodpovídáte za plánování kapacity spotřeby prostředků procesoru, spotřeby paměti, Azure Storage spotřeby a využití šířky pásma sítě v rámci virtuálních sítí. Zodpovídáte také za plánování kapacity pro využití prostředků z virtuálních sítí, která se SAP HANA v Azure (velké instance).

**WAN**: připojení, která jste navázali z místního nasazení do Azure, pro úlohy. Všichni zákazníci s velkou instancí HANA používají pro připojení Azure ExpressRoute. Toto připojení není součástí SAP HANA v řešení Azure (velké instance). Zodpovídáte za nastavení tohoto připojení.

**Archivace**: možná budete chtít archivovat kopie dat pomocí vlastních metod v účtech úložiště. Archivace vyžaduje správu, dodržování předpisů, náklady a operace. Zodpovídáte za generování archivovaných kopií a záloh v Azure a jejich uložení v souladu s vyhovujícím způsobem.

Podívejte se na [smlouvu SLA pro SAP HANA v Azure (velké instance)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

**Další kroky**
- Informace [o architektuře SAP Hana (velké instance) v Azure](hana-architecture.md)
