---
title: Pochopení proaktivní kolekce protokolů na zařízení Azure Stack Edge pro
description: Popisuje, jak se provádí proaktivní shromažďování protokolů na zařízeních Azure Stack Edge pro a jejich zakázání.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: alkohli
ms.openlocfilehash: bdd44bf81e34b60ee648b71c6dc3bde5a96d3deb
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443008"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Proaktivní shromažďování protokolů na zařízení Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Proaktivní shromažďování protokolů shromažďuje indikátory stavu systému na Azure Stack hraničních zařízeních, které vám pomůžou efektivně řešit problémy se zařízením. Ve výchozím nastavení je povoleno shromažďování proaktivního protokolu. Tento článek popisuje, co je zaznamenáno, jak Microsoft zpracovává data a jak zakázat nebo povolit shromažďování dat v aktivním protokolu. 

Informace v tomto článku se týkají Azure Stack GPU pro grafický procesor, Azure Stack Edge pro R a Azure Stack hraničních zařízení R.

## <a name="about-proactive-log-collection"></a>O proaktivní kolekci protokolů

Microsoft Customer Support and Engineering používá systémové protokoly ze zařízení Azure Stack Edge, aby efektivně identifikovaly a opravily problémy, které by mohly být během provozu. Proaktivní shromažďování protokolů je metoda, která upozorňuje společnost Microsoft na to, že zařízení Azure Stackho okraje zákazníka zjistilo problém nebo událost. Viz [proaktivní indikátory shromažďování protokolů](#proactive-log-collection-indicators) pro události, které jsou sledovány. Protokoly podpory týkající se tohoto problému se automaticky odesílají do účtu Azure Storage, který Microsoft spravuje a řídí. Podpora Microsoftu a technici Microsoftu prozkoumají tyto protokoly podpory a určí, co nejlepší postup vyřešit problém u zákazníka.

> [!NOTE]
> Tyto protokoly se používají pouze pro účely ladění a poskytují zákazníkům podporu v případě problémů.


## <a name="enabling-proactive-log-collection"></a>Povoluje se shromažďování proaktivního protokolu.

Ve výchozím nastavení je povoleno shromažďování proaktivního protokolu. Při pokusu o aktivaci zařízení prostřednictvím místního uživatelského rozhraní můžete zakázat proaktivní shromažďování protokolů. 

1. V místním webovém uživatelském rozhraní zařízení, **navštivte stránku Začínáme** .

2. Na dlaždici **Aktivace** vyberte **aktivovat**. 

    ![Místní webové uživatelské rozhraní "Podrobnosti o cloudu" Stránka 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)

3. V podokně **Aktivace** :

   1. Zadejte **aktivační klíč** , který jste získali v [části získání aktivačního klíče pro Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

      Po aktivaci je ve výchozím nastavení zapnuté proaktivní shromažďování protokolů, které Microsoftu umožňuje shromažďovat protokoly na základě stavu zařízení. Tyto protokoly se nahrají na účet Azure Storage. 

      Pokud chcete zastavit shromažďování protokolů od Microsoftu, můžete zakázat proaktivní shromažďování protokolů.

   1. Pokud chcete na zařízení zakázat shromažďování proaktivního protokolu, vyberte **Zakázat**.

   1. Vyberte **aktivovat**.

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

Kromě proaktivní shromažďování protokolů, které shromažďuje konkrétní protokoly týkající se zjištěného konkrétního problému, můžou jiné kolekce protokolů získat mnohem hlubší porozumění stavu a chování systému. Tyto další protokoly se obvykle můžou shromažďovat během žádosti o podporu nebo aktivované Microsoftem na základě dat telemetrie ze zařízení.

## <a name="handling-data"></a>Zpracování dat

Když je zapnuté proaktivní shromažďování protokolů, zákazník souhlasí s tím, že společnost Microsoft shromažďuje protokoly ze zařízení Azure Stack Edge, jak je popsáno zde. Zákazník také potvrdí a pošle odesílání a uchovávání těchto protokolů v účtu Azure Storage spravovaném a řízeným společností Microsoft.

Společnost Microsoft používá data pro řešení problémů se stavem systému a problémy. Data se nepoužívají k marketingovým, reklamním ani jiným obchodním účelům bez souhlasu zákazníka. 

Data, která Microsoft shromažďuje, se zpracovávají podle našich standardních postupů ochrany osobních údajů. Pokud se zákazník rozhodne odvolat svůj souhlas s aktivním shromažďováním protokolů, nebudou ovlivněna veškerá data shromážděná se souhlasem před zrušením.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [shromáždit balíček pro podporu](azure-stack-edge-gpu-troubleshoot.md#collect-support-package).