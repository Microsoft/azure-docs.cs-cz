---
title: Provozní model SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Provozní model SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e147e4a5f104ca4cd1a10a776c907e3f9f1d6128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616975"
---
# <a name="operations-model-and-responsibilities"></a>Provozní model a zodpovědnosti

Služba poskytovaná se službou SAP HANA v Azure (velké instance) je zarovnaná se službami Azure IaaS. Získáte instanci velké instance HANA s nainstalovaným operačním systémem, který je optimalizován pro SAP HANA. Stejně jako u virtuálních počítačů Azure IaaS je většina úkolů vypevnění operačního systému, instalace dalšího softwaru, instalace HANA, provoz operačního systému a HANA a aktualizace operačního systému a HANA vaší odpovědností. Společnost Microsoft na vás nenutí aktualizace operačního systému ani hana aktualizace.

![Povinnosti SAP HANA v Azure (velké instance)](./media/hana-overview-architecture/image2-responsibilities.png)

Jak je znázorněno na obrázku, SAP HANA v Azure (velké instance) je nabídka IaaS s více tenanty. Rozdělení odpovědnosti je z větší části na hranici infrastruktury operačního režimu. Společnost Microsoft je zodpovědná za všechny aspekty služby pod řádkem operačního systému. Nesete odpovědnost za všechny aspekty služby nad linkou. Operační hod je vaše zodpovědnost. Můžete pokračovat v používání nejaktuálnějších místních metod, které můžete použít pro dodržování předpisů, zabezpečení, správu aplikací, základ a správu operačního systému. Systémy se jeví, jako by byly ve vaší síti ve všech ohledech.

Tato služba je optimalizována pro SAP HANA, takže existují oblasti, kde je třeba pracovat se společností Microsoft používat základní možnosti infrastruktury pro dosažení nejlepších výsledků.

Následující seznam obsahuje podrobnější informace o každé vrstvě a o vašich povinnostech:

**Sítě**: Všechny interní sítě pro razítko velké instance se systémem SAP HANA. Vaše odpovědnost zahrnuje přístup k úložišti, připojení mezi instancemi (pro horizontální navýšení kapacity a další funkce), připojení k krajině a připojení k Azure, kde je aplikační vrstva SAP hostovaná ve virtuálních počítačích. Zahrnuje také připojení wan mezi datovými centry Azure pro účely zotavení po havárii replikace. Všechny sítě jsou rozděleny na oddíly klienta a mají kvalitu služby použít.

**Úložiště**: Virtualizované dělené úložiště pro všechny svazky potřebné servery SAP HANA, stejně jako pro snímky. 

**Servery**: Vyhrazené fyzické servery pro spuštění SAP HANA DBs přiřazené k klientům. Servery třídy SKU typu I jsou hardwarově abstrahovány. U těchto typů serverů je konfigurace serveru shromažďována a udržována v profilech, které lze přesunout z jednoho fyzického hardwaru na jiný fyzický hardware. Takový (ruční) přesunutí profilu operacemi lze trochu porovnat s retušováním služeb Azure. Servery skum třídy Typu II takovou možnost nenabízejí.

**SDDC**: Software pro správu, který se používá ke správě datových center jako softwarově definovaných entit. Umožňuje společnosti Microsoft sdružovat prostředky z důvodů škálování, dostupnosti a výkonu.

**O /S:** OS, který si vyberete (SUSE Linux nebo Red Hat Linux), který běží na serverech. Bitové kopie operačního systému, které jsou dodávány, byly poskytnuty jednotlivými dodavateli Linuxu společnosti Microsoft pro spuštění SAP HANA. Musíte mít předplatné s dodavatelem Linuxu pro konkrétní image optimalizovanou pro SAP HANA. Jste zodpovědní za registraci bitových kopií u dodavatele operačního softwaru. 

Od okamžiku předání společností Microsoft jste zodpovědní za jakékoli další opravy operačního systému Linux. Tato oprava obsahuje další balíčky, které mohou být nezbytné pro úspěšnou instalaci SAP HANA a které nebyly zahrnuty konkrétní ho dodavatele linuxu v jejich SAP HANA optimalizované image operačního systému. (Další informace naleznete v dokumentaci k instalaci SAP HANA a poznámkách SAP.) 

Jste zodpovědní za opravy operačního systému z důvodu poruchy nebo optimalizace operačního systému a jeho ovladačů vzhledem k konkrétnímu hardwaru serveru. Jste také zodpovědní za zabezpečení nebo funkční opravy operačního systému. 

Vaší odpovědností je také monitorování a plánování kapacity:

- Spotřeba prostředků procesoru.
- Spotřeba paměti.
- Diskové svazky související s volným místem, viopy a latencí.
- Provoz na svazku v síti mezi velkou instancí HANA a aplikační vrstvou SAP.

Základní infrastruktura velké instance HANA poskytuje funkce pro zálohování a obnovení svazku operačního systému. Za používání této funkce nesete také vy.

**Middleware**: Sap HANA Instance, především. Za správu, provoz a monitorování nesete vavku. Pomocí poskytované funkce můžete použít snímky úložiště pro účely zálohování a obnovení a zotavení po havárii. Tyto funkce jsou poskytovány infrastrukturou. Mezi vaše povinnosti patří také navrhování vysoké dostupnosti nebo zotavení po havárii s těmito možnostmi, jejich využití a monitorování k určení, zda se snímky úložiště úspěšně spouštějí.

**Data**: Vaše data spravovaná společností SAP HANA a další data, jako jsou záložní soubory umístěné na svazcích nebo sdílených složkách. Mezi vaše povinnosti patří sledování volného místa na disku a správa obsahu na svazcích. Jste také zodpovědní za sledování úspěšného spuštění záloh diskových svazků a snímků úložiště. Za úspěšné spuštění replikace dat do lokalit pro zotavení po havárii odpovídá společnost Microsoft.

**Aplikace:** Instance aplikace SAP nebo v případě aplikací, které nejsou sap, aplikační vrstva těchto aplikací. Mezi vaše povinnosti patří nasazení, správa, operace a monitorování těchto aplikací. Jste zodpovědní za plánování kapacity spotřeby prostředků procesoru, spotřeby paměti, spotřeby úložiště Azure a spotřeby šířky pásma sítě v rámci virtuálních sítí. Jste také zodpovědní za plánování kapacity pro spotřebu prostředků z virtuálních sítí do SAP HANA v Azure (velké instance).

**Sítě WAN**: Připojení, která navážete z místního prostředí na nasazení Azure pro úlohy. Všichni zákazníci s hana velké instance používat Azure ExpressRoute pro připojení. Toto připojení není součástí řešení SAP HANA v Azure (velké instance). Jste zodpovědní za nastavení tohoto připojení.

**Archiv**: Můžete chtít archivovat kopie dat pomocí vlastních metod v účtech úložiště. Archivace vyžaduje správu, dodržování předpisů, náklady a operace. Jste zodpovědní za generování archivních kopií a záloh v Azure a jejich ukládání kompatibilním způsobem.

Podívejte se na [sla pro SAP HANA v Azure (velké instance)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

**Další kroky**
- Odkazování [architektury SAP HANA (velké instance) v Azure](hana-architecture.md)
