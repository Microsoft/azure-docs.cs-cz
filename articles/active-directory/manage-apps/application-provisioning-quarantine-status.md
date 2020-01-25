---
title: Stav zřizování aplikace v karanténě | Microsoft Docs
description: Pokud jste nakonfigurovali aplikaci pro Automatické zřizování uživatelů, přečtěte si, co stav zřizování karantény znamená a jak ho vymazat.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 933217b2ad86535d45f6674165ee162f263a8cd7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712173"
---
# <a name="application-provisioning-in-quarantine-status"></a>Stav zřizování aplikace v karanténě

Služba zřizování Azure AD monitoruje stav vaší konfigurace a umisťuje nestavové aplikace do karantény. Pokud se většina nebo všechna volání prováděná na cílovém systému konzistentně nedaří kvůli chybě, například neplatné přihlašovací údaje správce, je úloha zřizování označena jako v karanténě.

V karanténě se frekvence přírůstkových cyklů postupně zkracuje na jeden den. Úloha zřizování se odebere z karantény po opravách všech chyb a spustí se další cyklus synchronizace. Pokud úloha zřizování zůstane v karanténě po dobu delší než čtyři týdny, úloha zřizování je zakázaná (zastaví se).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Návody zjistit, jestli je moje aplikace v karanténě?

Existují tři způsoby, jak ověřit, zda je aplikace v karanténě:
  
- V Azure Portal přejděte na **Azure Active Directory** > **podnikové aplikace** > &lt;*název aplikace*&gt; > **zřizování** a posuňte se k indikátoru průběhu v dolní části.  

  ![Stavový řádek znázorňující stav karantény](media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Pomocí Microsoft Graph žádosti [Get synchronizationJob](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) programově Získejte stav úlohy zřizování:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Podívejte se na e-mail. Když se aplikace umístí do karantény, pošle se jednorázový e-mail s oznámením. Pokud se důvod karantény změní, pošle se aktualizovaný e-mail s novým důvodem pro karanténu. Pokud se vám nezobrazuje e-mail:

  - Ujistěte se, že jste v konfiguraci zřizování pro aplikaci zadali platný **e-mail s oznámením** .
  - Ujistěte se, že pro e-mailovou schránku oznámení není žádné filtrování spamu.
  - Ujistěte se, že jste odhlásili odběr e-mailů.

## <a name="why-is-my-application-in-quarantine"></a>Proč je moje aplikace v karanténě?

Microsoft Graph požadavek na získání stavu úlohy zřizování zobrazuje následující důvod pro karanténu:

- `EncounteredQuarantineException` označuje, že byly zadány neplatné přihlašovací údaje. Služba zřizování nemůže navázat spojení mezi zdrojovým systémem a cílovým systémem.

- `EncounteredEscrowProportionThreshold` označuje, že zřizování překročilo prahovou hodnotu v úschově. K tomuto stavu dochází, pokud se nezdařila více než 60% událostí zřizování.

- `QuarantineOnDemand` znamená, že jsme zjistili problém s vaší aplikací a ručně jste ho nastavili na karanténu.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Návody se moje aplikace dostat do karantény?

Nejprve Vyřešte problém, který způsobil, že se aplikace umístila do karantény.

- Zkontrolujte nastavení zřizování aplikace a ujistěte se, že jste [zadali platné přihlašovací údaje správce](configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Azure AD musí být schopný vytvořit vztah důvěryhodnosti s cílovou aplikací. Ujistěte se, že jste zadali platné přihlašovací údaje a že váš účet má potřebná oprávnění.

- Zkontrolujte [protokoly zřizování](../reports-monitoring/concept-provisioning-logs.md) a prozkoumejte, jaké chyby způsobují karanténu a řeší chybu. Přístup k protokolům zřizování v Azure Portal získáte tak, že v části **aktivita** přejdeme na **Azure Active Directory** &gt; **podnikové aplikace** &gt; **protokoly zřizování (Preview)** .

Po vyřešení problému restartujte úlohu zřizování. Některé změny nastavení zřizování aplikace, jako jsou mapování atributů nebo filtry oborů, se automaticky restartují pro vás. Indikátor průběhu na stránce pro **zřizování** aplikace indikuje, kdy bylo zřizování naposledy spuštěno. Pokud potřebujete úlohu zřizování restartovat ručně, použijte jednu z následujících metod:  

- Pomocí Azure Portal restartujte úlohu zřizování. Na stránce **zřizování** aplikace v části **Nastavení**vyberte **Vymazat stav a znovu spusťte synchronizaci** a nastavte **stav zřizování** **na zapnuto**. Tato akce plně restartuje službu zřizování, což může nějakou dobu trvat. Úplný počáteční cyklus se znovu spustí, který zruší escrows, odebere aplikaci z karantény a vymaže všechny vodoznaky.

- [Restartujte úlohu zřizování](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)pomocí Microsoft Graph. Budete mít plnou kontrolu nad tím, co restartujete. Můžete zvolit vymazání escrows (pro restartování čítače v úschově, který se bude přestavovat do karantény), vymazat karanténu (Pokud chcete aplikaci odebrat z karantény) nebo zrušit meze. Použijte následující požadavek:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`