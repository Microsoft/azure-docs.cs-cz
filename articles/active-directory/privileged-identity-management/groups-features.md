---
title: Správa privilegovaných skupin Azure AD v Privileged Identity Management (PIM) | Microsoft Docs
description: Jak spravovat členy a vlastníky privilegovaných skupin přístupu v Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 759781fd61cd42d05f2823d390e99d128dd2fcac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512388"
---
# <a name="management-capabilities-for-privileged-access-azure-ad-groups-preview"></a>Možnosti správy pro privilegovaný přístup skupiny Azure AD (Preview)

V Privileged Identity Management (PIM) teď můžete přiřadit nárok na členství nebo vlastnictví privilegovaných skupin přístupu. Od této verze Preview můžete přiřadit předdefinované role Azure Active Directory (Azure AD) skupinám cloudů a použít PIM ke správě členů skupin a nároků a aktivace vlastníků. Další informace o skupinách přiřazení rolí v Azure AD najdete v tématu [použití skupin cloudu ke správě přiřazení rolí v Azure Active Directory (Preview)](../roles/groups-concept.md).

>[!Important]
> Pokud chcete přiřadit privilegovaný přístup k roli pro přístup pro správu k Exchangi, zabezpečení a centru dodržování předpisů nebo SharePointu, použijte **role a** prostředí portálu Azure AD, které se nepoužívají v prostředí Privileged Access Groups, aby uživatel nebo skupina nezpůsobili aktivaci do skupiny.

## <a name="require-different-policies-for-each-role-assignable-group"></a>Vyžadovat pro každou skupinu přiřazenou roli jiné zásady

Některé organizace používají nástroje, jako je spolupráce v rámci Azure AD Business-to-Business (B2B), k pozvání svých partnerů jako hostů ke své organizaci Azure AD. Místo jedné zásady za běhu pro všechna přiřazení privilegované role můžete vytvořit dvě různé privilegované přístupové skupiny s vlastními zásadami. U důvěryhodných zaměstnanců můžete vyhovět méně přísným požadavkům a přísnější požadavky jako pracovní postup schvalování pro vaše partnery při žádosti o aktivaci do přiřazené skupiny.

## <a name="activate-multiple-role-assignments-in-a-single-request"></a>Aktivace více přiřazení rolí v jednom požadavku

Ve verzi Preview skupin Privileged Access Groups můžete správcům pro konkrétní úlohy poskytnout rychlý přístup k více rolím s jednou žádostí za běhu. Například správci Office úrovně 3 můžou potřebovat přístup za běhu k rolím Správce Exchange, správcům aplikací Office, správcům týmů a správcům hledání, aby mohli každý den důkladně prozkoumat incidenty. Dřív by to vyžadovalo čtyři po sobě jdoucí požadavky, což je proces, který nějakou dobu trvá. Místo toho můžete vytvořit skupinu s možností přiřazení s názvem "role 3 Office Admins", přiřadit ji ke každé ze čtyř výše zmíněných rolí (nebo libovolné předdefinované role Azure AD) a povolit pro privilegovaný přístup v části aktivita skupiny. Po povolení privilegovaného přístupu můžete nakonfigurovat nastavení za běhu pro členy skupiny a přiřadit správce a vlastníky jako oprávněné. Když se správci zvyšují do skupiny, stanou se členy všech čtyř rolí Azure AD.

## <a name="extend-and-renew-group-assignments"></a>Prodloužit a prodloužit přiřazení skupin

Po nastavení vlastního vlastníka nebo přiřazení členů s časovým limitem se může zobrazit první otázka, co se stane, když přiřazení vyprší? V této nové verzi poskytujeme dvě možnosti pro tento scénář:

- Extended – když se přiřazení role blíží k vypršení platnosti, může uživatel použít Privileged Identity Management k vyžádání rozšíření pro přiřazení role.
- Obnovit – Pokud již vypršela platnost přiřazení role, uživatel může použít Privileged Identity Management k vyžádání obnovení přiřazení role.

Akce iniciované uživatelem vyžadují schválení od správce globálního správce nebo privilegované role. Správci už nebudou muset být v podnikání v rámci správy těchto vypršení platnosti. Může pouze počkat na rozšíření nebo žádosti o obnovení a schválit je, pokud je žádost platná.

## <a name="next-steps"></a>Další kroky

- [Přiřazení vlastníka nebo člena skupiny privilegovaného přístupu](groups-assign-member-owner.md)
- [Schválení nebo zamítnutí žádostí o aktivaci pro členy a vlastníky skupiny privilegovaný přístup](groups-approval-workflow.md)
