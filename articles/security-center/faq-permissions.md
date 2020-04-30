---
title: Nejčastější dotazy k Azure Security Center – otázky týkající se oprávnění
description: Tato Nejčastější dotazy vám odpoví na dotazy týkající se oprávnění v Azure Security Center, produktu, který pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 89b3233966ac53d6c384ca6bd5ac30322fbb0b02
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82106589"
---
# <a name="permissions"></a>Oprávnění

## <a name="how-do-permissions-work-in-azure-security-center"></a>Jak fungují oprávnění v Azure Security Center?

Azure Security Center používá [řízení přístupu na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md). To poskytuje [předdefinované role](../role-based-access-control/built-in-roles.md), které se dají v Azure přiřadit uživatelům, skupinám a službám.

Security Center posuzuje konfiguraci vašich prostředků pro identifikaci problémů a ohrožení zabezpečení. V Security Center se zobrazí informace týkající se prostředku pouze v případě, že máte přiřazenou roli vlastníka, přispěvatele nebo čtenáře pro předplatné nebo skupinu prostředků, do které prostředek patří.

Další informace o rolích a povolených akcích v Security Center najdete v tématu věnovaném [oprávněním v Azure Security Center](security-center-permissions.md) .



## <a name="who-can-modify-a-security-policy"></a>Kdo může upravit zásady zabezpečení?

Pokud chcete upravit zásady zabezpečení, musíte být správcem zabezpečení nebo vlastníkem nebo přispěvatelem daného předplatného.

Informace o tom, jak nakonfigurovat zásady zabezpečení, najdete [v tématu Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md).