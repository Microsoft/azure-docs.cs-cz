---
title: Stav zřizování aplikace v karanténě | Microsoft Docs
description: Pokud jste nakonfigurovali aplikaci pro Automatické zřizování uživatelů, přečtěte si, co stav zřizování karantény znamená a jak ho vymazat.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/24/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 6a716aef65cc81c5558a214c1ee5f93180810977
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91266679"
---
# <a name="application-provisioning-in-quarantine-status"></a>Stav zřizování aplikace v karanténě

Služba zřizování Azure AD monitoruje stav vaší konfigurace a umisťuje nestavové aplikace do karantény. Pokud se většina nebo všechna volání prováděná na cílovém systému konzistentně nedaří kvůli chybě, například neplatné přihlašovací údaje správce, je úloha zřizování označena jako v karanténě.

V karanténě se frekvence přírůstkových cyklů postupně zkracuje na jeden den. Úloha zřizování se odebere z karantény po opravách všech chyb a spustí se další cyklus synchronizace. Pokud úloha zřizování zůstane v karanténě po dobu delší než čtyři týdny, úloha zřizování je zakázaná (zastaví se).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Návody zjistit, jestli je moje aplikace v karanténě?

Existují tři způsoby, jak ověřit, zda je aplikace v karanténě:
  
- V Azure Portal přejděte na **Azure Active Directory**  >  **podnikové aplikace**  >  &lt; *název* &gt;  >  **zřizování** a Projděte si indikátor průběhu pro zprávu o karanténě.   

  ![Stavový řádek znázorňující stav karantény](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- V Azure Portal přejděte do **Azure Active Directory**  >  **protokoly auditu** > filtrovat **aktivity: umístit do karantény** a zkontrolovat historii karantény. Zobrazení v indikátoru průběhu, jak je popsáno výše, ukazuje, jestli je zřizování aktuálně v karanténě, a protokoly auditu vám umožní zobrazit historii karantény pro aplikaci. 

- Pomocí Microsoft Graph žádosti [Get synchronizationJob](/graph/api/synchronization-synchronizationjob-get?tabs=http&view=graph-rest-beta) programově Získejte stav úlohy zřizování:

```microsoft-graph
        GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

- Zkontrolujte si e-mail. Když se aplikace umístí do karantény, pošle se jednorázový e-mail s oznámením. Pokud se důvod karantény změní, pošle se aktualizovaný e-mail s novým důvodem pro karanténu. Pokud se vám nezobrazuje e-mail:

  - Ujistěte se, že jste v konfiguraci zřizování pro aplikaci zadali platný **e-mail s oznámením** .
  - Ujistěte se, že pro e-mailovou schránku oznámení není žádné filtrování spamu.
  - Ujistěte se, že jste odhlásili odběr e-mailů.
  - Kontrolovat e-maily z azure-noreply@microsoft.com

## <a name="why-is-my-application-in-quarantine"></a>Proč je moje aplikace v karanténě?

|Popis|Doporučená akce|
|---|---|
|**Problém s dodržováním předpisů SCIM:** Byla vrácena odpověď HTTP/404 nenalezena místo očekávané odpovědi HTTP/200 OK. V tomto případě služba zřizování Azure AD odeslala žádost do cílové aplikace a obdržela neočekávanou odpověď.|Zkontrolujte část přihlašovací údaje správce, abyste viděli, jestli aplikace vyžaduje zadání adresy URL tenanta, a ujistěte se, že je adresa URL správná. Pokud se problém nezobrazuje, obraťte se prosím na vývojáře aplikace, aby se zajistilo, že je jejich služba kompatibilní s SCIM. https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**Neplatné přihlašovací údaje:** Při pokusu o autorizaci přístupu k cílové aplikaci jsme dostali odpověď z cílové aplikace, která indikuje, že zadané přihlašovací údaje jsou neplatné.|Přejděte do části přihlašovací údaje správce uživatelského rozhraní pro konfiguraci zřizování a znovu udělte přístup s platnými přihlašovacími údaji. Pokud je aplikace v galerii, Projděte si kurz konfigurace aplikace pro všechny další požadované kroky.|
|**Duplicitní role:** Role importované z některých aplikací, jako je Salesforce a Zendesk, musí být jedinečné. |V Azure Portal přejděte na [manifest](../develop/reference-app-manifest.md) aplikace a odeberte duplicitní roli.|

 Microsoft Graph požadavek na získání stavu úlohy zřizování zobrazuje následující důvod pro karanténu:

- `EncounteredQuarantineException` označuje, že byly zadány neplatné přihlašovací údaje. Služba zřizování nemůže navázat spojení mezi zdrojovým systémem a cílovým systémem.

- `EncounteredEscrowProportionThreshold` označuje, že zřizování překročilo prahovou hodnotu v úschově. K tomuto stavu dochází, pokud se nezdařila více než 60% událostí zřizování.

- `QuarantineOnDemand` znamená, že jsme zjistili problém s vaší aplikací a ručně jste ho nastavili na karanténu.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Návody se moje aplikace dostat do karantény?

Nejprve Vyřešte problém, který způsobil, že se aplikace umístila do karantény.

- Zkontrolujte nastavení zřizování aplikace a ujistěte se, že jste [zadali platné přihlašovací údaje správce](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Azure AD musí být schopný vytvořit vztah důvěryhodnosti s cílovou aplikací. Ujistěte se, že jste zadali platné přihlašovací údaje a že váš účet má potřebná oprávnění.

- Zkontrolujte [protokoly zřizování](../reports-monitoring/concept-provisioning-logs.md) a prozkoumejte, jaké chyby způsobují karanténu a řeší chybu. Přístup k protokolům zřizování v Azure Portal získáte tak, že **Azure Active Directory** &gt; v části aktivita zařadíte **Enterprise Apps** &gt; **protokoly zřizování (Preview) Azure Active Directory Enterprise Apps (Preview)** . **Activity**

Po vyřešení problému restartujte úlohu zřizování. Některé změny nastavení zřizování aplikace, jako jsou mapování atributů nebo filtry oborů, se automaticky restartují pro vás. Indikátor průběhu na stránce pro **zřizování** aplikace indikuje, kdy bylo zřizování naposledy spuštěno. Pokud potřebujete úlohu zřizování restartovat ručně, použijte jednu z následujících metod:  

- Pomocí Azure Portal restartujte úlohu zřizování. Na stránce **zřizování** aplikace v části **Nastavení**vyberte **Vymazat stav a znovu spusťte synchronizaci** a nastavte **stav zřizování** **na zapnuto**. Tato akce plně restartuje službu zřizování, což může nějakou dobu trvat. Úplný počáteční cyklus se znovu spustí, který zruší escrows, odebere aplikaci z karantény a vymaže všechny vodoznaky.

- [Restartujte úlohu zřizování](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta)pomocí Microsoft Graph. Budete mít plnou kontrolu nad tím, co restartujete. Můžete zvolit vymazání escrows (pro restartování čítače v úschově, který se bude přestavovat do karantény), vymazat karanténu (Pokud chcete aplikaci odebrat z karantény) nebo zrušit meze. Použijte následující žádost:
 
```microsoft-graph
        POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

Nahraďte {ID} hodnotou ID aplikace a nahraďte {jobId} [ID úlohy synchronizace](/graph/api/resources/synchronization-configure-with-directory-extension-attributes?tabs=http&view=graph-rest-beta#list-synchronization-jobs-in-the-context-of-the-service-principal).
