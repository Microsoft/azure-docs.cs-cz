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
ms.openlocfilehash: 88d6207bf44d1ec04fde4f74ac9295412ea88fbc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87824609"
---
# <a name="permissions"></a>Oprávnění

## <a name="how-do-permissions-work-in-azure-security-center"></a>Jak fungují oprávnění v Azure Security Center?

Azure Security Center používá [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md), které poskytuje [předdefinované role](../role-based-access-control/built-in-roles.md) , které je možné přiřadit uživatelům, skupinám a službám v Azure.

Security Center posuzuje konfiguraci vašich prostředků pro identifikaci problémů a ohrožení zabezpečení. V Security Center se zobrazí informace týkající se prostředku pouze v případě, že máte přiřazenou roli vlastníka, přispěvatele nebo čtenáře pro předplatné nebo skupinu prostředků, do které prostředek patří.

Další informace o rolích a povolených akcích v Security Center najdete v tématu věnovaném [oprávněním v Azure Security Center](security-center-permissions.md) .



## <a name="who-can-modify-a-security-policy"></a>Kdo může upravit zásady zabezpečení?

Pokud chcete upravit zásady zabezpečení, musíte být správcem zabezpečení nebo vlastníkem nebo přispěvatelem daného předplatného.

Informace o tom, jak nakonfigurovat zásady zabezpečení, najdete [v tématu Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md).