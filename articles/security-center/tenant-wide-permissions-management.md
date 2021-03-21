---
title: Udělení a vyžádání oprávnění na úrovni tenanta v Azure Security Center
description: Naučte se spravovat oprávnění tenanta v Azure Security Center
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: 0a24546579df020dcb7c7a9b01ee3d181226d2df
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617484"
---
# <a name="grant-and-request-tenant-wide-visibility"></a>Udělení a vyžádání viditelnosti v úrovni tenanta

Uživatel s rolí Azure Active Directory (AD) **globálního správce** může mít odpovědnost v rámci tenanta, ale nemá oprávnění Azure zobrazovat informace o celé organizaci v Azure Security Center. Vyžaduje se zvýšení oprávnění, protože přiřazení role Azure AD neuděluje přístup k prostředkům Azure. 

## <a name="grant-tenant-wide-permissions-to-yourself"></a>Udělení oprávnění na úrovni tenanta sobě

Přiřazení oprávnění na úrovni tenanta:

1. Pokud vaše organizace spravuje přístup k prostředkům s [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)nebo jakýmkoli jiným nástrojem PIM, musí být globální role správce pro uživatele aktivní podle níže uvedeného postupu.

1. Jako globální uživatel správce bez přiřazení do kořenové skupiny pro správu tenanta otevřete stránku **přehled** Security Center a v proužku vyberte odkaz **viditelnost v rámci tenanta** . 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Povolit oprávnění na úrovni tenanta v Azure Security Center":::

1. Vyberte novou roli Azure, která se má přiřadit. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="Formulář pro definování oprávnění na úrovni tenanta, která se mají přiřadit uživateli":::

    > [!TIP]
    > Obecně platí, že role správce zabezpečení je nutná k uplatnění zásad na kořenové úrovni, zatímco čtečka zabezpečení bude stačit k poskytování viditelnosti na úrovni tenanta. Další informace o oprávněních udělených těmito rolemi najdete v [popisu předdefinované role správce zabezpečení](../role-based-access-control/built-in-roles.md#security-admin) nebo v [popisu předdefinované role čtenáře zabezpečení](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > Rozdíly mezi těmito rolemi, které jsou specifické pro Security Center, najdete v tabulce v části [role a povolené akce](security-center-permissions.md#roles-and-allowed-actions).

    Zobrazení v celé organizaci se dosahuje přidělením rolí na úrovni kořenové skupiny pro správu tenanta.  

1. Odhlaste se od Azure Portal a pak se znovu přihlaste.

1. Jakmile budete mít zvýšený přístup, otevřete nebo aktualizujte Azure Security Center a ověřte si, že máte přehled o všech předplatných v rámci vašeho tenanta Azure AD. 

Výše uvedený jednoduchý proces provede pro vás automaticky několik operací:

1. Oprávnění uživatele jsou dočasně zvýšena.
1. Pomocí nových oprávnění se uživatel přiřadí k požadované roli Azure RBAC pro kořenovou skupinu pro správu.
1. Oprávnění se zvýšenými oprávněními se odeberou.

Další podrobnosti o procesu zvýšení oprávnění služby Azure AD najdete v tématu [zvýšení přístupu ke správě všech předplatných Azure a skupin pro správu](../role-based-access-control/elevate-access-global-admin.md).


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>Požádat o oprávnění na úrovni tenanta, pokud není dostatečná

Pokud se přihlásíte k Security Center a zobrazí se informační zpráva s oznámením, že vaše zobrazení je omezené, můžete kliknout na tlačítko pro odeslání žádosti globálnímu správci vaší organizace. V žádosti můžete zahrnout roli, kterou chcete přiřadit, a globální správce provede rozhodnutí o tom, která role se má udělit. 

Jedná se o rozhodnutí globálního správce, jestli chcete tyto žádosti přijmout nebo odmítnout. 

> [!IMPORTANT]
> Jednu žádost můžete odeslat jenom každých 7 dní.

Chcete-li požádat o zvýšená oprávnění od globálního správce:

1. Z Azure Portal otevřete Azure Security Center.

1. Pokud se zobrazí nápis "zobrazují se omezené informace". vyberte ji.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Banner s informující uživatele, který může požádat o oprávnění na úrovni tenanta.":::

1. Ve formuláři podrobný požadavek vyberte požadovanou roli a odůvodnění, proč potřebná oprávnění potřebujete.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="Stránka podrobností pro vyžádání oprávnění na úrovni tenanta od globálního správce Azure":::

1. Vyberte **požádat o přístup**.

    Globálnímu správci se pošle e-mail. E-mail obsahuje odkaz na Security Center, kde může žádost schválit nebo odmítnout.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="E-mail globálnímu správci pro nová oprávnění":::

    Poté, co globální správce vybere **kontrolu žádosti** a dokončí proces, rozhodnutí se odešle e-mailem žádajícímu uživateli. 

## <a name="next-steps"></a>Další kroky

Další informace o oprávněních Security Center najdete na následující související stránce:

- [Oprávnění ve službě Azure Security Center](security-center-permissions.md)