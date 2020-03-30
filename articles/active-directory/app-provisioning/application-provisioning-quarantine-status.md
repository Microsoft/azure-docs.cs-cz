---
title: Stav zřizování aplikací karantény | Dokumenty společnosti Microsoft
description: Když jste nakonfigurovali aplikaci pro automatické zřizování uživatelů, zjistěte, co znamená stav zřizování karantény a jak ji vymazat.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563c049bf3d1606e87db54e3b003dac987594610
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154623"
---
# <a name="application-provisioning-in-quarantine-status"></a>Zřizování aplikací ve stavu karantény

Služba zřizování Azure AD monitoruje stav vaší konfigurace a umístí nefunkční aplikace do stavu "karantény". Pokud většina nebo všechna volání proti cílovému systému konzistentně selžou z důvodu chyby, například neplatných přihlašovacích údajů správce, úloha zřizování je označena jako v karanténě.

Zatímco v karanténě, frekvence přírůstkových cyklů se postupně snižuje na jednou denně. Úloha zřizování je odebrána z karantény po odstranění všech chyb a spuštění dalšího cyklu synchronizace. Pokud úloha zřizování zůstane v karanténě déle než čtyři týdny, úloha zřizování je zakázána (zastaví spuštění).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Jak poznám, že je moje žádost v karanténě?

Existují tři způsoby, jak zkontrolovat, zda je aplikace v karanténě:
  
- Na webu Azure Portal přejděte na*název*&gt; > aplikace **Azure Active Directory** > **Enterprise** > &lt;**a** přejděte na indikátor průběhu v dolní části.  

  ![Zřizování stavového řádku zobrazujícího stav karantény](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Na webu Azure Portal přejděte do**protokolů auditu služby** **Azure Active Directory** > > **filtrovat aktivitu: Karanténa** a zkontrolujte historii karantény. Zatímco zobrazení na panelu průběhu, jak je popsáno výše ukazuje, zda zřizování je aktuálně v karanténě, protokoly auditu umožňují zobrazit historii karantény pro aplikaci. 

- Pomocí požadavku Microsoft Graph [získat synchronizační úlohu](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) programově získat stav úlohy zřizování:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Zkontrolujte si e-mail. Když je aplikace umístěna do karantény, je odeslán jednorázový e-mail s oznámením. Pokud se změní důvod karantény, bude odeslán aktualizovaný e-mail s novým důvodem karantény. Pokud e-mail nevidíte:

  - Ujistěte se, že jste zadali platný **e-mail s oznámením** v konfiguraci zřizování pro aplikaci.
  - Ujistěte se, že ve složce doručené pošty s oznámením není filtrování nevyžádané pošty.
  - Ujistěte se, že jste se neodhlásili z odběru e-mailů.

## <a name="why-is-my-application-in-quarantine"></a>Proč je moje žádost v karanténě?

Žádost microsoft graphu k získání stavu úlohy zřizování zobrazuje následující důvod karantény:

- `EncounteredQuarantineException`označuje, že byla poskytnuta neplatná pověření. Zřizovací služba není schopna navázat spojení mezi zdrojovým systémem a cílovým systémem.

- `EncounteredEscrowProportionThreshold`znamená, že zřizování překročilo prahovou hodnotu úschovy. K této podmínce dochází, když se nezdařilo více než 60 % událostí zřizování.

- `QuarantineOnDemand`znamená, že jsme zjistili problém s vaší aplikací a ručně jsme ji nastavili do karantény.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Jak dostanu svou žádost z karantény?

Nejprve vyřešte problém, který způsobil umístění aplikace do karantény.

- Zkontrolujte nastavení zřizování aplikace a ujistěte se, že jste [zadali platná pověření správce](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Azure AD musí být schopen vytvořit vztah důvěryhodnosti s cílovou aplikací. Ujistěte se, že jste zadali platná pověření a že váš účet má potřebná oprávnění.

- Zkontrolujte [protokoly zřizování](../reports-monitoring/concept-provisioning-logs.md) dále prozkoumat, jaké chyby jsou příčinou karantény a řešit chybu. Přístup protokoly zřizování na portálu Azure tak, že přejdete na **Protokoly zřizování** **azure active directory** &gt; **podnikové aplikace** &gt; (preview) v části **Aktivita.**

Po vyřešení problému restartujte úlohu zřizování. Některé změny nastavení zřizování aplikace, jako je mapování atributů nebo filtry oborů, automaticky restartují zřizování za vás. Indikátor průběhu na stránce **Zřizování** aplikace označuje, kdy bylo naposledy spuštěno zřizování. Pokud potřebujete restartovat úlohu zřizování ručně, použijte jednu z následujících metod:  

- K restartování úlohy zřizování použijte portál Azure. Na stránce **Zřizování** aplikace v části **Nastavení**vyberte **Vymazat stav a restartujte synchronizaci** a nastavte **stav zřizování** **na Zapnuto**. Tato akce plně restartuje zřizovací služby, což může nějakou dobu trvat. Úplný počáteční cyklus bude znovu spuštěn, který vymaže escrows, odstraní aplikaci z karantény a vymaže všechny vodoznaky.

- Restartování [úlohy zřizování](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)pomocí aplikace Microsoft Graph Budete mít plnou kontrolu nad tím, co restartujete. Můžete se rozhodnout vymazat escrows (restartovat čítač úschovy, který narůstá směrem k stavu karantény), vymazat karanténu (chcete-li odstranit aplikaci z karantény) nebo vymazat vodoznaky. Použijte následující žádost:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`
