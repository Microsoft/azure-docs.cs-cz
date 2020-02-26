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
ms.openlocfilehash: 66a260fbb03f770ee98ec29a5f5e15e3d7dd1310
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599389"
---
# <a name="permissions"></a>Oprávnění

## <a name="how-do-permissions-work-in-azure-security-center"></a>Jak fungují oprávnění v Azure Security Center?

Azure Security Center používá [řízení přístupu na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md). To poskytuje [předdefinované role](../role-based-access-control/built-in-roles.md), které se dají v Azure přiřadit uživatelům, skupinám a službám.

Security Center posuzuje konfiguraci vašich prostředků identifikovat problémy se zabezpečením a ohrožení zabezpečení. Ve službě Security Center zobrazí jenom informace související s prostředku, když máte přiřazenou roli vlastník, Přispěvatel nebo Čtenář pro předplatné nebo skupinu prostředků, které prostředek patří.

Další informace o rolích a povolených akcích v Security Center najdete v tématu věnovaném [oprávněním v Azure Security Center](security-center-permissions.md) .



## <a name="who-can-modify-a-security-policy"></a>Kdo může upravit zásady zabezpečení?

Pokud chcete upravit nějakou zásadu zabezpečení, musíte být správce zabezpečení nebo vlastníka nebo přispěvatele daného předplatného.

Informace o tom, jak nakonfigurovat zásady zabezpečení, najdete [v tématu Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md).