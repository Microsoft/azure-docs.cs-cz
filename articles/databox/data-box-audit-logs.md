---
title: Protokoly auditu pro Azure Data Box, Azure Data Box Heavy události | Microsoft Docs
description: V této části najdete popis úplných protokolů auditu pro Data Box, které se shromažďují v různých fázích Azure Data Box a Azure Data Box Heavy jejich pořadí.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 50dbe9ab649a708fb36b1c9e4fe89bccadc7ea90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86209430"
---
# <a name="audit-logs-for-your-azure-data-box-and-azure-data-box-heavy"></a>Protokoly auditu pro váš Azure Data Box a Azure Data Box Heavy

Protokoly jsou neměnné, záznamy s časovým razítkem diskrétních událostí, ke kterým došlo v průběhu času. Protokoly obsahují informace o diagnostice, auditování a zabezpečení ze zařízení.  

Data Box nebo Data Box Heavy objednávka prochází pomocí následujících kroků v průběhu její činnosti: pořadí, nastavení, kopírování dat, vrácení, nahrání do Azure a ověření a mazání dat. U každého z těchto kroků jsou auditovány a protokolovány všechny události.

Tento článek obsahuje informace o protokolech auditu Data Box, včetně typů protokolů a shromažďovaných informací, jakož i umístění protokolů. 

Informace v tomto článku se vztahují na Data Box i Data Box Heavy. V následujících částech se všechny odkazy na Data Box vztahují také na Data Box Heavy. V tomto článku se nevztahují protokoly shromážděné ze služby Data Box spuštěné v Azure. 


## <a name="about-audit-logs"></a>O protokolech auditu 

V Data Box jsou shromažďovány následující protokoly:

- **Systémové protokoly** – data box se jedná o zařízení se systémem Windows, jsou protokolovány všechny události hardwaru, softwaru a systému. V protokolech auditu systému se shromáždí a nahlásí sada těchto událostí. 

- Data Box **zabezpečení** je zařízení se systémem Windows, jsou protokolovány všechny události zabezpečení. V protokolech auditu zabezpečení se shromáždí a nahlásí sada těchto událostí. 

- **Aplikace** – tyto protokoly jsou specifické jenom pro data box. Tyto protokoly obsahují všechny události generované v zařízení v reakci na spuštěné služby Data Box.

Jednotlivé protokoly jsou popsány v následující části.

### <a name="system-logs"></a>Systémové protokoly

Následující identifikátory událostí systémového protokolu se shromažďují jako protokoly auditu systému na vašich Data Box:

|Název zprostředkovatele událostí     |Shromážděno ID události   |Popis události   |
|-------------------|----------|----------------|
|Microsoft-Windows-kernel-General|12  |Čas UTC, kdy byl operační systém restartován.   |
|                                |13  |Čas UTC, kdy byl operační systém vypnut. |
|    |                              |
|Microsoft-Windows-kernel-Power  |41  |Systém byl restartován bez čistého vypnutí.| 
|    |                              |
|Microsoft-Windows-BitLocker-Driver|Vše|    |

### <a name="security-logs"></a>Protokoly zabezpečení

Následující identifikátory událostí protokolu zabezpečení se shromažďují jako protokoly auditu zabezpečení v Data Box:

|Název zprostředkovatele událostí                   |Shromážděno ID události    |Popis události       |
|--------------------------------------|------------|----------|
|Auditování zabezpečení systému Microsoft Windows   |4624        |Úspěšné přihlášení |
|                                      |4625        |Přihlášení k účtu se nezdařilo. Neznámé uživatelské jméno nebo špatné heslo. |
|                                     

### <a name="application-logs"></a>Protokoly aplikací

Následující identifikátory událostí protokolu aplikace se shromažďují jako součást protokolů auditu balíčku v Data Box.     

- **Microsoft-Azure-Databox-OOBE-audit** – obsahuje události, ke kterým dochází v místním uživatelském rozhraní. 
- **Microsoft-Azure-Databox-Revision-audit** -obsahuje události týkající se opětovného zřízení data Boxho zařízení. Opětovné zřízení Data Box nastane, když se zařízení resetuje prostřednictvím místního uživatelského rozhraní. Tuto možnost vyberte, když chcete vymazat zkopírovaná data odebráním stávajících sdílených složek a opětovným vytvořením sdílených složek v rámci opětovného zřízení nebo resetování zařízení.
- **Microsoft-Azure-Databox-HcsMgmt-audit** -obsahuje události, které souvisejí pouze s krokem **Příprava na dodání** , před odesláním zařízení zpět do datacentra Azure. 
- **Microsoft-Azure-Databox-IfxAudit** -obsahuje zprávy zaznamenávané různými entitami produktu o úlohách, protokoly, které označují další informace o tom, co se děje v některých tocích.

Tady je tabulka shrnující různé zprostředkovatele událostí a odpovídající ID událostí, která se shromažďují v jednotlivých případech.

|Název zprostředkovatele událostí    |ID události    | Poznámky |
|-----------------|-----------------|-------------------|
|Microsoft-Azure-DataBox-OOBE-audit |4624        |Úspěšné přihlášení|
|                                      |4625        |Přihlášení k účtu se nezdařilo. Neznámé uživatelské jméno nebo špatné heslo.|
|                                     |4634        |Odhlášení události|
|                                   |  | |
|Microsoft-Azure-DataBox-Revision-audit    |65001       |Úspěšná rezřízení události|
|                                                  |65002       |Nepovedlo se znovu zřídit událost.|
|                                                  |                 |         |
|Microsoft-Azure-DataBox-HcsMgmt-audit        |65003       |Příprava k odeslání událost stavu NotStarted, InProgress, Failed, Canceled, Succeeded, ScanCompletedWithIssues, SucceededWithWarnings          |
|                                                  |                 |     |
|Microsoft-Azure-DataBox-IfxAudit    |Vše |Všechny události jsou v kódu protokolovány pomocí rozhraní API protokolu auditu. |

Tady je příklad protokolu auditu IFX (Instrumentation Framework):

|     Úloha/úloha/rozhraní API                              |     Protokolované události                                                                                                              | 
|-----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
|     Vyčištění                                   |     Události týkající se spuštění, dokončení nebo selhání úlohy čištění jsou protokolovány. |                                              
|     Příprava zařízení pro expedici zákazníka    |     Události související s zahájením, dokončením nebo selháním úlohy pro přípravu zařízení k expedici jsou protokolovány. |
|     Zřídit                                 |     Události související s zahájením, dokončením nebo selháním úlohy zřizování zařízení jsou protokolovány.|
|     Úloha auditu balíčku                       |     Události související s zahájením, dokončením nebo selháním úlohy balíčku auditu, které vytvářejí řetěz protokolů o úschově, jsou protokolovány.|
|     Přepsání disku                          |     Nepovedlo se přepsat disk v protokolu.|
|     Povolit nebo zakázat vzdálené prostředí PowerShell     |     Události související s povolením nebo zakázáním vzdáleného prostředí PowerShell v zařízení jsou protokolovány. |
|     Podrobnosti získání fáze instalace               |     Události související s instalací softwaru v zařízení v jednotlivých fázích jsou protokolovány v datacentru Azure.|
|     Odemknout nebo zamknout svazek BitLockeru           |     Události jsou protokolovány, aby označovaly stav nástroje BitLocker pro svazek *basevolume* a *hcsdata* .|
|     Upravení disku                              |     Události související s selháním fyzických disků, které nebylo možné vymazat, a události, které jsou úspěšně smazány všemi fyzickými disky v zařízení, jsou protokolovány. |
|     Povolit nebo zakázat místního uživatele               |     Události související s povolením nebo zakázáním místních uživatelských účtů pro StorSimpleAdmin a PodSupportAdminUser se zaznamenávají do protokolu.| 
|     Resetování hesla                          |     Události související s úspěšným nebo neúspěšným resetováním hesla pro místního uživatele StorSimpleAdmin jsou protokolovány. |


Kromě protokolů auditu IFX jsou také shromažďovány řetězce protokolů auditu pro Data Box. Tyto protokoly nelze zobrazit v reálném čase, ale pouze po dokončení úlohy a data jsou vymazána z Data Box disků. Tyto protokoly obsahují podmnožinu informací obsažených v protokolech auditu IFX.

Další informace o řetězci protokolů auditu pro úschovu najdete v tématu [získání řetězu protokolů pro úschovu po vymazání dat](data-box-logs.md#get-chain-of-custody-logs-after-data-erasure).

<!-- write a few lines about order history and link out to the detailed section on order history-->

## <a name="access-audit-logs"></a>Přístup k protokolům auditu

Tyto protokoly se ukládají v Azure a nedají se získat přímo. Pokud potřebujete získat přístup k těmto protokolům, zasouborte lístek podpory. Další informace najdete v tématu [kontakt podpora Microsoftu](data-box-disk-contact-microsoft-support.md). 

Po podání lístku podpory se Microsoft stáhne a poskytne vám přístup k těmto protokolům.


## <a name="next-steps"></a>Další kroky

- Naučte se [řešit problémy s data box a data box Heavy](data-box-troubleshoot.md).
