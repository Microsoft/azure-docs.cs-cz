---
title: Azure Active Directory Identity Protection oznámení | Microsoft Docs
description: Zjistěte, jak oznámení podporují vaše aktivity šetření.
services: active-directory
keywords: ochrany identit Azure active directory, cloud app discovery,. Správa aplikací, zabezpečení, rizik, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: e72cfab8886f28982e5451362704d6a7a9fe59e4
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713809"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure oznámení ochrany identit služby Active Directory

Azure AD Identity Protection odešle dva typy automatizované oznámení e-mailů vám pomůžou spravovat riziko pro uživatele a rizikových událostí:

- Ohrožených uživatelích zjistil e-mailu
- Týdenní digest e-mailu

Tento článek vám poskytne přehled obou oznámení e-mailů.


## <a name="users-at-risk-detected-email"></a>Ohrožených uživatelích zjistil e-mailu

V reakci na zjištěné účtu ohrožený, Azure AD Identity Protection vygeneruje výstrahu e-mailu s **uživatelé hrozí zjistil** jako předmět. E-mailu, zahrnuje odkaz **[uživatelé označení příznakem rizik](active-directory-reporting-security-user-at-risk.md)** sestavy. Jako osvědčený postup které byste měli prozkoumat okamžitě ohrožených uživatelích.

![Ohrožených uživatelích zjistil e-mailu](./media/active-directory-identityprotection-notifications/01.png)


### <a name="configuration"></a>Konfigurace

Jako správce můžete nastavit:

- **Úroveň rizika, která aktivuje generování této e-mailu** – ve výchozím nastavení, je úroveň rizika hodnotu "Vysoká" riziko.
- **Příjemci tohoto e-mailu** – ve výchozím nastavení, příjemci zahrnout všichni globální správci. Globální správce, můžete také přidat další globální správce, správce zabezpečení, zabezpečení čtečky jako příjemce.  


Otevřete dialogové okno související, klikněte na tlačítko **výstrahy** v **nastavení** části **Identity Protection** stránky.

![Ohrožených uživatelích zjistil e-mailu](./media/active-directory-identityprotection-notifications/05.png)


## <a name="weekly-digest-email"></a>Týdenní digest e-mailu

Týdenní digest e-mailu obsahuje souhrn nových událostí riziko.  
Obsahuje:

- Ohrožení uživatelé

- Podezřelé aktivity

- Zjištěné ohrožení zabezpečení

- Odkazy na související sestavy v Identity Protection

    ![Náprava](./media/active-directory-identityprotection-notifications/400.png "nápravy")

### <a name="configuration"></a>Konfigurace

Jako správce můžete přepnout odesílání týdenní ověřování algoritmem digest, e-mailu.

![Uživatel rizika](./media/active-directory-identityprotection-notifications/62.png "rizika uživatele")

Otevřete dialogové okno související, klikněte na tlačítko **týdenní Digest** v **nastavení** části **Identity Protection** stránky.

![Ohrožených uživatelích zjistil e-mailu](./media/active-directory-identityprotection-notifications/04.png)


## <a name="see-also"></a>Další informace najdete v tématech

- [Ochrany identit Azure Active Directory](active-directory-identityprotection.md)
