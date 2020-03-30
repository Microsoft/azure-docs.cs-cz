---
title: Nejčastější dotazy centra zabezpečení Azure – dotazy týkající se oprávnění
description: Tyto nejčastější dotazy odpovídají na otázky týkající se oprávnění v Azure Security Center, produktu, který vám pomůže předcházet hrozbám, zjišťovat je a reagovat na ně.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599389"
---
# <a name="permissions"></a>Oprávnění

## <a name="how-do-permissions-work-in-azure-security-center"></a>Jak fungují oprávnění v Azure Security Center?

Azure Security Center používá [řízení přístupu na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md). To poskytuje [předdefinované role](../role-based-access-control/built-in-roles.md), které se dají v Azure přiřadit uživatelům, skupinám a službám.

Security Center vyhodnocuje konfiguraci prostředků k identifikaci problémů se zabezpečením a slabými místy. V Centru zabezpečení se informace související s prostředkem zobrazují pouze v případě, že je přiřazena role vlastníka, přispěvatele nebo čtenáře pro předplatné nebo skupinu prostředků, do které prostředek patří.

Další informace o rolích a povolených akcích v Centru zabezpečení najdete v tématu [Oprávnění v Centru zabezpečení Azure.](security-center-permissions.md)



## <a name="who-can-modify-a-security-policy"></a>Kdo může změnit zásady zabezpečení?

Chcete-li změnit zásady zabezpečení, musíte být správcem zabezpečení nebo vlastníkem nebo přispěvatelem tohoto předplatného.

Informace o konfiguraci zásad zabezpečení najdete v tématu [Nastavení zásad zabezpečení v Centru zabezpečení Azure](tutorial-security-policy.md).