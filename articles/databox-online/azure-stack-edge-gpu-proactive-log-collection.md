---
title: Pochopení proaktivní kolekce protokolů na zařízení Azure Stack Edge pro
description: Popisuje, jak se provádí proaktivní shromažďování protokolů na zařízeních Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: alkohli
ms.openlocfilehash: f79de47ec0ffad11f650054b581dbbaae030edbf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466176"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Proaktivní shromažďování protokolů na zařízení Azure Stack Edge

Můžete povolit proaktivní shromažďování protokolů na Azure Stack hraničních zařízeních na základě indikátorů stavu systému, které vám pomůžou efektivně řešit problémy se zařízením. Tento článek popisuje, co je proaktivní shromažďování protokolů, jak ho povolit a jak se zpracovávají data, když je povolená proaktivní shromažďování protokolů.
   
Informace v tomto článku se týkají Azure Stack GPU pro grafický procesor, Azure Stack Edge pro R a Azure Stack hraničních zařízení R.

## <a name="about-proactive-log-collection"></a>O proaktivní kolekci protokolů

Microsoft Customer Support and Engineering používá systémové protokoly ze zařízení Azure Stack Edge, aby efektivně identifikovaly a opravily problémy, které by mohly být během provozu. Proaktivní shromažďování protokolů je metoda, která upozorňuje společnost Microsoft na problém nebo událost (viz oddíl indikátory proaktivního protokolu pro sledované události), kterou zákazník Azure Stack hraniční zařízení zjistil. Protokoly podpory týkající se tohoto problému se automaticky odesílají na účet Azure Storage spravovaný a řízený Microsoftem. Podpora Microsoftu a technici Microsoftu prozkoumají tyto protokoly podpory a určí, co nejlepší postup vyřešit problém u zákazníka.    

> [!NOTE]
> Tyto protokoly se používají jenom pro účely ladění a poskytují zákazníkům podporu v případě problémů. 


## <a name="enabling-proactive-log-collection"></a>Povoluje se shromažďování proaktivního protokolu.

Při pokusu o aktivaci zařízení prostřednictvím místního uživatelského rozhraní můžete povolit proaktivní shromažďování protokolů. 

1. V místním webovém uživatelském rozhraní zařízení, **navštivte stránku Začínáme** .
2. Na dlaždici **Aktivace** vyberte **aktivovat**. 

    ![Místní webové uživatelské rozhraní "Podrobnosti o cloudu" Stránka 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. V podokně **Aktivace** :
    1. Zadejte **aktivační klíč** , který jste získali v [části získání aktivačního klíče pro Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

    1. Můžete povolit proaktivní shromažďování protokolů, aby Microsoft mohl shromažďovat protokoly na základě stavu zařízení. Protokoly shromážděné tímto způsobem jsou odesílány do účtu Azure Storage.
    
    1. Vyberte **Použít**.

    ![Místní webové uživatelské rozhraní "Podrobnosti o cloudu" – Stránka 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)



## <a name="proactive-log-collection-indicators"></a>Proaktivní indikátory shromažďování protokolů

Po povolení kolekce proaktivního protokolu se protokoly odesílají automaticky, když se na zařízení zjistí jedna z následujících událostí:  


|Výstraha/chyba/podmínka  |Popis  |
|---------|---------|
|AcsUnhealthyCondition     |Služby konzistentní s Azure nejsou v pořádku.         |
|IOTEdgeAgentNotRunningCondition      |Agent IoT Edge neběží.         |
|UpdateInstallFailedEvent | Nepovedlo se nainstalovat aktualizaci.        |

 
Microsoft bude dál přidávat nové události do předchozího seznamu. Pro nové události není potřeba žádný další souhlas. Podívejte se na tuto stránku, kde se dozvíte víc o nejaktuálnějších indikátorech sběru dat v aktivním protokolu.    
 

## <a name="other-log-collection-methods"></a>Další metody shromažďování protokolů

Kromě proaktivní shromažďování protokolů, které shromažďuje konkrétní protokoly týkající se zjištěného konkrétního problému, existují i další kolekce protokolů, které mohou poskytnout mnohem hlubší porozumění stavu a chování systému. Tyto další kolekce protokolů jsou obvykle spouštěny během žádosti o podporu nebo aktivované společností Microsoft na základě dat telemetrie, které zařízení poskytuje.  

## <a name="handling-data"></a>Zpracování dat

Pokud zákazník povolí proaktivní shromažďování protokolů, souhlasí s tím, že společnost Microsoft shromažďuje protokoly ze zařízení Azure Stack Edge, jak je popsáno zde. Zákazník také potvrdí a pošle odesílání a uchovávání těchto protokolů v účtu Azure Storage spravovaném a řízeným společností Microsoft.

Společnost Microsoft používá data pro řešení problémů se stavem systému a problémy. Data se nepoužívají k marketingovým, reklamním ani jiným obchodním účelům bez souhlasu zákazníka. 

Data, která Microsoft shromažďuje, se zpracovávají podle našich standardních postupů ochrany osobních údajů. Pokud se zákazník rozhodne odvolat svůj souhlas s aktivním shromažďováním protokolů, nebudou ovlivněna veškerá data shromážděná se souhlasem před zrušením.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [shromáždit balíček pro podporu](azure-stack-edge-gpu-troubleshoot.md#collect-support-package).