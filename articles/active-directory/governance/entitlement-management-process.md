---
title: Žádost o proces & oznámení – správa oprávnění Azure AD
description: Přečtěte si o procesu žádosti o balíček přístupu a o tom, jak se e-mailová oznámení posílají ve správě nároků služby Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4ff270977449bb80f97073342dc0c726a3f2316
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128523"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Vyžádání procesu a e-mailových oznámení ve správě nároků Azure AD

Když uživatel odešle požadavek na balíček přístupu, proces začne doručovat tento požadavek na přístup. Správa nároků Azure AD odesílá e-mailová oznámení schvalovatelům a žádostem, když během procesu dojde ke klíčovým událostem. Tento článek popisuje proces žádosti a e-mailová oznámení, která jsou odeslána.

## <a name="request-process"></a>Proces požadavku

Uživatel, který potřebuje přístup k přístupu balíček můžete odeslat žádost o přístup. V závislosti na konfiguraci zásady může požadavek vyžadovat schválení. Po schválení požadavku začne proces přiřazovat uživateli přístup ke každému prostředku v balíčku přístupu. Následující diagram znázorňuje přehled procesu a různých stavů:

![Diagram schvalovacího procesu](./media/entitlement-management-process/request-process.png)

| Stav | Popis |
| --- | --- |
| Submitted (Odesláno) | Uživatel odešle žádost. |
| Čeká na schválení | Pokud zásady pro balíček přístupvyžaduje schválení, požadavek přesune na čekající schválení. |
| Platnost vypršela | Pokud žádný schvalovatel schválit žádost v rámci časového limitu žádosti o schválení, žádost vyprší. Chcete-li akci zopakovat, bude muset uživatel znovu odeslat svůj požadavek. |
| Denied | Schvalovatel odmítne požadavek. |
| Schválené | Schvalovatel schválí požadavek. |
| Poskytování | Uživateli **nebyl** přiřazen přístup ke všem prostředkům v balíčku přístupu. Pokud se jedná o externího uživatele, je možné, že uživatel ještě nezískal přístup k adresáři prostředků. Je také možné, že nepřijali výzvu k souhlasu. |
| Doručeno | Uživateli byl přiřazen přístup ke všem prostředkům v balíčku přístupu. |
| Rozšířený přístup | Pokud jsou v zásadě povolena rozšíření, uživatel přiřazení rozšířil. |
| Platnost přístupu vypršela. | Přístup uživatele k přístupovému balíčku vypršel. Chcete-li získat přístup znovu, uživatel bude muset odeslat žádost. |

## <a name="email-notifications"></a>E-mailová oznámení

Pokud jste schvalovatel, budou vám e-mailová oznámení odeslána, když potřebujete schválit žádost o přístup. Po dokončení žádosti o přístup také obdržíte oznámení. Pokud jste žadatel, budete také dostávat e-mailová oznámení, která označují stav vaší žádosti.

Následující diagramy ukazují, kdy jsou tato e-mailová oznámení odeslána schvalovatelům nebo žadateli. Odkazem na [tabulku e-mailových oznámení](entitlement-management-process.md#email-notifications-table) vyhledejte odpovídající číslo e-mailových oznámení zobrazených v diagramech.

### <a name="first-approvers-and-alternate-approvers"></a>První schvalovatelé a alternativní schvalovatelé
Následující diagram znázorňuje zkušenosti prvních schvalovatelů a alternativních schvalovatelů a e-mailová oznámení, která obdrží během procesu žádosti:

![Tok procesu prvního a alternativního schvalovatele](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Žadatelé
Následující diagram znázorňuje zkušenosti s žadateli a e-mailovými oznámeními, která obdrží během procesu žádosti:

![Tok procesu žadatele](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>Dvoustupňové schválení
Následující diagram znázorňuje zkušenosti schvalovatelů fáze 1 a fáze 2 a e-mailová oznámení, která obdrží během procesu žádosti:

![Dvoustupňový schvalovací tok](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>Tabulka e-mailových oznámení
Následující tabulka obsahuje další podrobnosti o každém z těchto e-mailových oznámení. Chcete-li spravovat tyto e-maily, můžete použít pravidla. V aplikaci Outlook můžete například vytvořit pravidla pro přesunutí e-mailů do složky, pokud předmět obsahuje slova z této tabulky:

| # | Předmět e-mailu | Při odeslání | Odesláno |
| --- | --- | --- | --- |
| 1 | Požadovaná akce: Schválení nebo zamítnutí předané žádosti do *[data]* | Tento e-mail bude odeslán alternativním schvalovatelům fáze 1 (po eskalaci žádosti), aby bylo možné provést akci. | Alternativní schvalovatelé fáze 1 |
| 2 | Požadovaná akce: Schválení nebo zamítnutí žádosti podle *[data]* | Tento e-mail bude odeslán prvnímu schvalovatelovi, pokud je eskalace zakázána, aby podnikl akci. | První schvalovatel |
| 3 | Připomenutí: Schválení nebo zamítnutí žádosti *[date]* pro *[žadatel]* | Tento e-mail s připomenutím bude odeslán prvnímu schvalovatelovi, pokud je eskalace zakázána. E-mail je požádá, aby v případě, že tak neučinili, přijali akci. | První schvalovatel |
| 4 | Schválení nebo zamítnutí žádosti podle *[času]* k *[datu]* | Tento e-mail bude odeslán prvnímu schvalovatelovi (pokud je povolena eskalace), aby mohl provést akci. | První schvalovatel |
| 5 | Akce vyžaduje připomenutí: Schválit nebo odepřít žádost *[datum]* pro *[žadatel]* | Tento e-mail s připomenutím bude odeslán prvnímu schvalovatelovi, pokud je povolena eskalace. E-mail je požádá, aby v případě, že tak neučinili, přijali akci. | První schvalovatel |
| 6 | Platnost žádosti vypršela pro *[access_package]* | Tento e-mail bude odeslán prvnímu schvalovatelovi a alternativním schvalovatelům fáze 1 po vypršení platnosti žádosti. | První schvalovatel, alternativní schvalovatelé fáze 1 |
| 7 | Žádost schválená pro *[žadatel]* *[access_package]* | Tento e-mail bude zaslán prvnímu schvalovatelovi a alternativním schvalovatelům fáze 1 po dokončení žádosti. | První schvalovatel, alternativní schvalovatelé fáze 1 |
| 8 | Žádost schválená pro *[žadatel]* *[access_package]* | Tento e-mail bude odeslán prvnímu schvalovatelovi a alternativním schvalovatelům fáze 1 dvoufázové žádosti při schválení žádosti fáze 1. | První schvalovatel, alternativní schvalovatelé fáze 1 |
| 9 | Žádost byla zamítnuta *[access_package]* | Tento e-mail bude zaslán žadateli, když je jeho žádost zamítnuta. | Requestor |
| 10 | Platnost vaší žádosti vypršela pro *[access_package]* | Tento e-mail bude zaslán žadateli na konci jednoho nebo 2-fáze žádosti. E-mail upozorní žadatele, že vypršela platnost požadavku. | Requestor |
| 11 | Požadovaná akce: Schválení nebo zamítnutí žádosti podle *[data]* | Tento e-mail bude odeslán druhému schvalovatelovi, pokud je eskalace zakázána, aby bylo možné provést akci. | Druhý schvalovatel |
| 12 | Připomenutí požadované v případě akce: Schválení nebo zamítnutí žádosti podle *[data]* | Tento e-mail s připomenutím bude odeslán druhému schvalovatelovi, pokud je eskalace zakázána. Oznámení je požádá, aby podnikli kroky, pokud tak dosud neučinili. | Druhý schvalovatel |
| 13 | Požadovaná akce: Schválení nebo zamítnutí žádosti podle *[data]* pro *[žadatel]* | Tento e-mail bude odeslán druhému schvalovatelovi, pokud je povolena eskalace, aby mohl provést akci. | Druhý schvalovatel |
| 14 | Akce vyžaduje připomenutí: Schválit nebo odepřít žádost *[datum]* pro *[žadatel]* | Tento e-mail s připomenutím bude odeslán druhému schvalovatelovi, pokud je povolena eskalace. Oznámení je požádá, aby podnikli kroky, pokud tak dosud neučinili. | Druhý schvalovatel |
| 15 | Požadovaná akce: Schválení nebo zamítnutí předané žádosti do *[data]* | Tento e-mail bude odeslán alternativním schvalovatelům fáze 2, pokud je povolena eskalace, aby bylo možné provést akci. | Fáze-2 alternativní schvalovatelé |
| 16 | Žádost schválená pro *[žadatel]* *[access_package]* | Tento e-mail bude zaslán druhému schvalovatelovi a alternativním schvalovatelům fáze 2 po schválení žádosti. | Druhý schvalovatel, alternativní schvalovatelé fáze 2 |
| 17 | Platnost žádosti vypršela pro *[access_package]* | Tento e-mail bude odeslán druhému schvalovatelovi nebo alternativním schvalovatelům po vypršení platnosti žádosti. | Druhý schvalovatel, alternativní schvalovatelé fáze 2 |
| 18 | Nyní máte přístup k *[access_package]* | Tento e-mail bude zaslán koncovým uživatelům, aby začali používat jejich přístup. | Requestor |
| 19 | Rozšíření přístupu pro *[access_package]* o *[datum]* | Tento e-mail bude odeslán koncovým uživatelům před vypršením jejich přístupu. | Requestor |
| 20 | Přístup byl ukončen pro *[access_package]* | Tento e-mail bude zaslán koncovým uživatelům po vypršení jejich přístupu. | Requestor |

### <a name="access-request-emails"></a>E-maily s žádostí o přístup

Když žadatel odešle žádost o přístup k balíčku přístupu nakonfigurované vyžadovat schválení, všichni schvalovatelé přidándo zásady obdrží e-mailové oznámení s podrobnostmi o žádosti. Podrobnosti v e-mailu zahrnují: organizaci jména žadatele a obchodní odůvodnění; a požadované datum zahájení a ukončení přístupu (je-li k dispozici). Podrobnosti budou rovněž obsahovat datum podání žádosti a dobu, kdy žádost vyprší.

E-mail obsahuje schvalovatele odkazů, na který můžete kliknout a přejít na můj přístup a schválit nebo zamítnout žádost o přístup. Zde je ukázkové e-mailové oznámení, které je odesláno prvnímu schvalovatelovi nebo druhému schvalovatelovi (pokud je povoleno dvoufázové schválení) k dokončení žádosti o přístup:

![Schválit žádost o přístup k e-mailu s balíčkem](./media/entitlement-management-shared/approver-request-email.png)

Schvalovatelé mohou také obdržet e-mail s připomenutím. E-mail požádá schvalovatele, aby o žádosti rozhodl. Zde je ukázkové e-mailové oznámení, které schvalovatel obdrží, aby jim připomněl, aby podnikli kroky:

![E-mail s žádostí o přístup k připomenutí](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>Alternativní schvalovatelé požadují e-maily

Pokud je povoleno alternativní schvalovatelé a požadavek stále čeká na vyřízení, bude předán. Alternativní schvalovatelé obdrží e-mail ke schválení nebo zamítnutí žádosti. Alternativní schvalovatele můžete povolit ve fázi 1 a 2. Zde je ukázkový e-mail oznámení alternativní schvalovatelé obdrží:

![Alternativní schvalovatelé požadují e-mail](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Schvalovatel i alternativní schvalovatelé mohou žádost schválit nebo zamítnout.

### <a name="approved-or-denied-emails"></a>Schválené nebo zamítnuté e-maily

 Když schvalovatel obdrží žádost o přístup odeslanou žadatelem, může žádost o přístup schválit nebo zamítnout. Schvalovatel musí přidat obchodní odůvodnění pro své rozhodnutí. Zde je ukázkový e-mail odeslaný schvalovatelům a alternativním schvalovatelům po schválení žádosti:

![Schválená žádost o přístup k e-mailu balíčku](./media/entitlement-management-process/approver-request-email-approved.png)

Když je schválena žádost o přístup a je zřízen jeho přístup, je žadateli odesláno e-mailové oznámení, že má nyní přístup k přístupovému balíčku. Tady je ukázkové e-mailové oznámení, které se odešle žadateli, když mu bude udělen přístup k přístupu:

![E-mail s žádostí o přístup schváleného žadatele](./media/entitlement-management-process/requestor-email-approved.png)

Pokud je žádost o přístup zamítnuta, je žadateli odesláno e-mailové oznámení. Zde je ukázkové e-mailové oznámení, které je odesláno žadateli, když je jeho žádost o přístup zamítnuta:

![Žádost o žádost zamítnuta e-mail](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>E-maily s dvoufázovým žádostem o přístup ke schválení

Pokud je povoleno dvoufázové schválení, musí žádost schválit alespoň dva schvalovatelé, jeden z každé fáze, než může žadatel získat přístup.

Během fáze 1 obdrží první schvalovatel e-mail s žádostí o přístup a rozhodne se. Pokud žádost schválí, všichni první schvalovatelé a alternativní schvalovatelé ve fázi 1 (pokud je povolena eskalace) obdrží oznámení, že fáze 1 je dokončena. Zde je ukázkový e-mail oznámení, které je odesláno po dokončení fáze 1:

![E-mail s dvoufázovým požadavkem na přístup](./media/entitlement-management-process/approver-request-email-2stage.png)

Poté, co první nebo alternativní schvalovatelé schválí žádost ve fázi 1, fáze 2 začíná. Během fáze 2 druhý schvalovatel obdrží e-mail s oznámením žádosti o přístup. Poté, co se druhý schvalovatel nebo alternativní schvalovatelé ve fázi 2 (pokud je povolena eskalace) rozhodnou o schválení nebo zamítnutí žádosti, budou e-maily s oznámením odeslány prvnímu a druhému schvalovatelům ve fázi 1 a fáze 2 a také žadateli.

### <a name="expired-access-request-emails"></a>E-maily s prošlým žádostí o přístup

Žádosti o přístup mohou vypršet, pokud žádný schvalovatel žádost neschválil nebo nezamítl. 

Jakmile požadavek dosáhne nakonfigurovaného data vypršení platnosti a vyprší jeho platnost, nemohou jej schvalovatelé schválit ani odepřít. Zde je ukázkový e-mail oznámení odeslaného všem prvnímu druhému (pokud je povoleno dvoufázové schválení) a alternativním schvalovatelům:

![E-mail schvalovatelů, jejichž platnost vypršela](./media/entitlement-management-process/approver-request-email-expired.png)

Žadateli je také odesláno e-mailové oznámení, které mu oznámí, že vypršela jejich žádost o přístup a že je třeba žádost o přístup znovu odeslat. Následující diagram znázorňuje zkušenosti s žádostí a e-mailová oznámení, která obdrží, když žádají o rozšíření přístupu:

![Žadatel rozšířit tok procesu přístupu](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

Zde je ukázkové e-mailové oznámení, které je odesláno žadateli po vypršení jejich žádosti o přístup:

![Žadatel vypršela žádost o přístup e-mail](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Další kroky

- [Požádat o přístup k přístupovému balíčku](entitlement-management-request-access.md)
- [Schválení nebo zamítnutí žádostí o přístup](entitlement-management-request-approve.md)
