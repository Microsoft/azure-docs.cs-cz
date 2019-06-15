---
title: Azure Active Directory Identity Protection upozornění | Dokumentace Microsoftu
description: Zjistěte, jak oznámení podporu aktivit šetření.
services: active-directory
keywords: Azure active directory identity protection, zjišťování cloudových aplikací, Správa aplikací, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0065ec03695ee977133ae2ec43aafba7d5bfff78
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65784345"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Oznámení služby Azure Active Directory Identity Protection

Služba Azure AD Identity Protection se odešle dva druhy automatizované oznámení e-mailů vám pomohou při správě uživatelského rizika a rizikových událostí:

- Ohrožení uživatelé zjistil e-mailu
- Týdenní přehled e-mailu

Tento článek poskytuje přehled o obou oznámení e-mailů.


## <a name="users-at-risk-detected-email"></a>Ohrožení uživatelé zjistil e-mailu

V reakci na účet zjištěná ohrožení, Azure AD Identity Protection generuje upozornění e-mailu s **uživatelé na zjištěné riziko** jako předmět. E-mailu obsahuje odkaz **[uživatelé označení příznakem rizika](../reports-monitoring/concept-user-at-risk.md)** sestavy. Jako osvědčený postup byste měli prozkoumat okamžitě ohrožení uživatelé.

Konfigurace pro toto upozornění můžete zadat, na jaké úroveň rizika uživatele má vygenerování výstrahy. E-mailu se vygeneruje, když úroveň rizika uživatele dosáhne jste zadali; ale neobdržíte žádné nové uživatele na zjištěné riziko e-mailové výstrahy pro tohoto uživatele po přecházejí na tuto úroveň rizika uživatele. Například pokud nastavíte zásady tak, aby upozornění na střední uživatelského rizika a John přesune na střední riziko uživatelů, zobrazí se uživateli v e-mailu zjištěné riziko pro Jan. Druhý uživatelé na zjištěné riziko upozornění ale neobdržíte Pokud John pak přesune do vysoce rizikových nebo má další rizikové události.

![Ohrožení uživatelé zjistil e-mailu](./media/notifications/01.png)


### <a name="configuration"></a>Konfigurace

Jako správce můžete nastavit:

- **Úroveň rizika pro uživatele, která aktivuje generování tohoto e-mailu** – ve výchozím nastavení, je k riziku "Vysoká" nastavit úroveň rizika.
- **Příjemci tohoto e-mailu** – ve výchozím nastavení, příjemci zahrnují všichni globální správci. Globální správci můžete také přidat další globální správci, správci zabezpečení, čtenáři zabezpečení jako příjemce.  


Související dialogového okna, klikněte na tlačítko **výstrahy** v **nastavení** část **Identity Protection** stránky.

![Ohrožení uživatelé zjistil e-mailu](./media/notifications/05.png)


## <a name="weekly-digest-email"></a>Týdenní přehled e-mailu

Týdenní přehled e-mailu obsahuje souhrn nové rizikové události.  
Zahrnuje:

- Ohrožení uživatelé

- Podezřelé aktivity

- Zjištěná ohrožení zabezpečení

- Odkazy na související sestavy ve službě Identity Protection

    ![Náprava](./media/notifications/400.png "nápravy")

### <a name="configuration"></a>Konfigurace

Jako správce můžete přepnout, odesílání týdenní e-mailů digest.

![Riziko uživatele](./media/notifications/62.png "rizika uživatele")

Související dialogového okna, klikněte na tlačítko **týdenní souhrn** v **nastavení** část **Identity Protection** stránky.

![Ohrožení uživatelé zjistil e-mailu](./media/notifications/04.png)


## <a name="see-also"></a>Další informace najdete v tématech

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
