---
title: Stav zřizování aplikace v karanténě | Microsoft Docs
description: Pokud jste nakonfigurovali aplikaci pro Automatické zřizování uživatelů, přečtěte si, co stav zřizování karantény znamená a jak ho vymazat.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/18/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 49590c46588ad0d0f1c1b7b095679a3c3fce96eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579497"
---
# <a name="application-provisioning-in-quarantine-status"></a>Stav zřizování aplikace v karanténě

Služba zřizování Azure AD monitoruje stav konfigurace. Také umisťuje nestavové aplikace do stavu "karanténa". Pokud je většina nebo všechna volání prováděná s cílovým systémem konzistentně neúspěšná, bude úloha zřizování označena jako v karanténě. Příkladem selhání je chyba přijatá z důvodu neplatných přihlašovacích údajů správce.

V karanténě:
- Frekvence přírůstkových cyklů se postupně zkracuje na jeden den.
- Úloha zřizování se odebere z karantény po opravách všech chyb a spustí se další cyklus synchronizace. 
- Pokud úloha zřizování zůstane v karanténě po dobu delší než čtyři týdny, úloha zřizování je zakázaná (zastaví se).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Návody zjistit, jestli je moje aplikace v karanténě?

Existují tři způsoby, jak ověřit, zda je aplikace v karanténě:
  
- V Azure Portal přejděte na **Azure Active Directory**  >  **podnikové aplikace**  >  &lt; *název* &gt;  >  **zřizování** a Projděte si indikátor průběhu pro zprávu o karanténě.   

  ![Stavový řádek znázorňující stav karantény](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- V Azure Portal přejděte do **Azure Active Directory**  >  **protokoly auditu** > filtrovat **aktivity: umístit do karantény** a zkontrolovat historii karantény. Zobrazení v indikátoru průběhu, jak je popsáno výše, ukazuje, jestli je zřizování aktuálně v karanténě. Protokoly auditu zobrazují historii karantény pro aplikaci. 

- Pomocí Microsoft Graph žádosti [Get synchronizationJob](/graph/api/synchronization-synchronizationjob-get?tabs=http&view=graph-rest-beta&preserve-view=true) programově Získejte stav úlohy zřizování:

```microsoft-graph
        GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

- Zkontrolujte si e-mail. Když se aplikace umístí do karantény, pošle se jednorázový e-mail s oznámením. Pokud se důvod karantény změní, pošle se aktualizovaný e-mail s novým důvodem pro karanténu. Pokud se vám nezobrazuje e-mail:

  - Ujistěte se, že jste v konfiguraci zřizování pro aplikaci zadali platný **e-mail s oznámením** .
  - Ujistěte se, že neexistují žádné filtrování spamu pro e-mailovou schránku s oznámením.
  - Ujistěte se, že jste nevytvořili odběr e-mailů.
  - Kontrolovat e-maily z `azure-noreply@microsoft.com`

## <a name="why-is-my-application-in-quarantine"></a>Proč je moje aplikace v karanténě?

|Description|Doporučená akce|
|---|---|
|**Problém s dodržováním předpisů SCIM:** Byla vrácena odpověď HTTP/404 nenalezena místo očekávané odpovědi HTTP/200 OK. V tomto případě služba zřizování Azure AD vytvořila požadavek na cílovou aplikaci a obdržela neočekávanou odpověď.|Podívejte se na část přihlašovací údaje správce. Podívejte se, jestli aplikace vyžaduje zadání adresy URL tenanta a jestli je adresa URL správná. Pokud se problém nezobrazuje, obraťte se na vývojáře aplikace a ujistěte se, že je jejich služba kompatibilní s SCIM. https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**Neplatné přihlašovací údaje:** Při pokusu o ověření přístupu k cílové aplikaci jsme dostali odpověď z cílové aplikace, která indikuje, že zadané přihlašovací údaje jsou neplatné.|Přejděte do části přihlašovací údaje správce uživatelského rozhraní pro konfiguraci zřizování a znovu udělte přístup s platnými přihlašovacími údaji. Pokud je aplikace v galerii, Projděte si kurz konfigurace aplikace, který už má požadované kroky.|
|**Duplicitní role:** Role importované z některých aplikací, jako je Salesforce a Zendesk, musí být jedinečné. |V Azure Portal přejděte na [manifest](../develop/reference-app-manifest.md) aplikace a odeberte duplicitní roli.|

 Microsoft Graph požadavek na získání stavu úlohy zřizování zobrazuje následující důvod pro karanténu:
- `EncounteredQuarantineException` označuje, že byly zadány neplatné přihlašovací údaje. Služba zřizování nemůže navázat spojení mezi zdrojovým systémem a cílovým systémem.
- `EncounteredEscrowProportionThreshold` označuje, že zřizování překročilo prahovou hodnotu v úschově. K tomuto stavu dochází, pokud se nezdařila více než 40% událostí zřizování. Další informace najdete v podrobnostech o prahové hodnotě v úschově níže.
- `QuarantineOnDemand` znamená, že jsme zjistili problém s vaší aplikací a ručně jste ho nastavili na karanténu.

**V úschově prahové hodnoty**

Pokud je splněná prahová hodnota v úschově, bude úloha zřizování přejít do karantény. Tato logika se může změnit, ale funguje přibližně tak, jak je popsáno níže: 

Úloha může přejít do karantény bez ohledu na počty selhání pro problémy, jako jsou přihlašovací údaje správce nebo SCIM dodržování předpisů. Obecně platí, že chyby 5 000 jsou minimem k tomu, aby bylo možné vyhodnotit, jestli se má umístit do karantény kvůli příliš mnoha chybám. Například úloha s 4 000 selháními by nepřešla do karantény. Ale úloha s 5 000 chybami by mohla aktivovat vyhodnocení. Vyhodnocení používá následující kritéria:  
- Pokud dojde k selhání více než 40% událostí zřizování nebo dojde k více než 40 000 chybám, bude úloha zřizování přejít do karantény. Chyby odkazů se nepočítají jako součást prahové hodnoty 40% nebo 40 000. Například selhání aktualizace správce nebo člena skupiny je selhání odkazu.
- Úloha, která 45 000 uživatelů nebyla úspěšně zřízena, by mohla vést k umístění do karantény, protože překračuje prahovou hodnotu 40 000.
- Úloha, ve které 30 000 uživatelů se nepodařilo zřídit a 5 000 byla úspěšná, by mohla vést k umístění do karantény, protože by překročila minimální prahovou hodnotu 40% a minimální hodnota 5 000
- Úloha s 20 000 selháními a 100 000 úspěšností by nepřešla do karantény, protože její povýšení nepřekračuje prahovou hodnotu 40% nebo je maximální počet chyb 40 000.  
- K dispozici je absolutní prahová hodnota 60 000 chyb, která je pro chyby odkazů i bez odkazů. Například 40 000 uživatelům se nepodařilo zřídit a 21 000 aktualizace správce se nezdařily. Celkový počet je 61 000 selhání a překračuje limit 60 000.


## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Návody se moje aplikace dostat do karantény?

Nejprve Vyřešte problém, který způsobil, že se aplikace umístila do karantény.

- Zkontrolujte nastavení zřizování aplikace a ujistěte se, že jste [zadali platné přihlašovací údaje správce](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Azure AD musí navázat vztah důvěryhodnosti s cílovou aplikací. Ujistěte se, že jste zadali platné přihlašovací údaje a že váš účet má potřebná oprávnění.

- Zkontrolujte [protokoly zřizování](../reports-monitoring/concept-provisioning-logs.md) a prozkoumejte, jaké chyby způsobují karanténu a řeší chybu. V části aktivita přejdete na **Azure Active Directory** &gt; **podnikové aplikace** &gt; **protokoly zřizování (Preview)** . 

Po vyřešení problému restartujte úlohu zřizování. Některé změny nastavení zřizování aplikace, jako jsou mapování atributů nebo filtry oborů, se automaticky restartují pro vás. Indikátor průběhu na stránce pro **zřizování** aplikace indikuje, kdy bylo zřizování naposledy spuštěno. Pokud potřebujete úlohu zřizování restartovat ručně, použijte jednu z následujících metod:  

- Pomocí Azure Portal restartujte úlohu zřizování. Na stránce **zřizování** aplikace vyberte **restartovat zřizování**. Tato akce plně restartuje službu zřizování, což může nějakou dobu trvat. Úplný počáteční cyklus se znovu spustí, který zruší escrows, odebere aplikaci z karantény a vymaže všechny vodoznaky. Služba potom znovu vyhodnotí všechny uživatele ve zdrojovém systému a určí, jestli jsou v oboru pro zřizování. To může být užitečné, pokud je vaše aplikace momentálně v karanténě, jak popisuje tento článek, nebo potřebujete provést změnu mapování atributů. Všimněte si, že počáteční cyklus trvá déle než typický přírůstkový cyklus z důvodu počtu objektů, které je třeba vyhodnotit. Další informace o výkonu počátečních a přírůstkových cyklů najdete [tady](application-provisioning-when-will-provisioning-finish-specific-user.md).

- [Restartujte úlohu zřizování](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true)pomocí Microsoft Graph. Budete mít plnou kontrolu nad tím, co restartujete. Můžete zvolit vymazání escrows (pro restartování čítače v úschově, který se bude přestavovat do karantény), vymazat karanténu (Pokud chcete aplikaci odebrat z karantény) nebo zrušit meze. Použijte následující žádost:
 
```microsoft-graph
        POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

Nahraďte {ID} hodnotou ID aplikace a nahraďte {jobId} [ID úlohy synchronizace](/graph/api/resources/synchronization-configure-with-directory-extension-attributes?tabs=http&view=graph-rest-beta&preserve-view=true#list-synchronization-jobs-in-the-context-of-the-service-principal).
