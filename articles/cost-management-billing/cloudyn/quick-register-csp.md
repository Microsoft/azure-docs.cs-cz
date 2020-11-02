---
title: Registrace u Cloudyn v Azure s použitím informací o partnerovi CSP
description: Přečtěte si podrobnosti o procesu registrace, který partneři používají k onboardingu zákazníků na portálu Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 95d8a425314ad1c968915085b35ca89aa3f62711
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543317"
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>Registrace do partnerského programu CSP a zobrazení informací o nákladech

Jako partner CSP a registrovaný uživatel Cloudynu si můžete v Cloudynu zobrazit a analyzovat útratu v cloudu. [Azure Cost Management mají přirozeně k dispozici přímí partneři](../costs/get-started-partners.md), jejichž zákazníci přešli na Smlouvu se zákazníkem Microsoftu a mají koupený plán Azure.

Registrací získáte přístup k portálu Cloudyn. Tento rychlý start podrobně popisuje proces registrace nezbytný k vytvoření zkušebního předplatného a přihlášení k portálu Cloudyn.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Konfigurace přístupu nepřímých poskytovatelů CSP v Cloudyn

Rozhraní API partnerského centra je ve výchozím nastavení přístupné pouze pro přímé poskytovatele CSP. Přímý poskytovatel CSP však může nakonfigurovat přístup pro své nepřímé zákazníky nebo partnery CSP pomocí skupin entit v Cloudyn.

Pokud chcete povolit přístup pro nepřímé partnery nebo zákazníky CSP, provedením následujících kroků segmentujte data nepřímých poskytovatelů CSP pomocí skupin entit Cloudyn. Potom skupinám entit přiřaďte odpovídající uživatelská oprávnění.

1. Vytvořte skupinu entit s použitím informací v části [Vytvoření entit](tutorial-user-access.md#create-and-manage-entities).
2. Postupujte podle kroků v článku [Assigning subscriptions to Cost Entities](https://www.youtube.com/watch?v=d9uTWSdoQYo) (Přiřazování předplatných k entitám nákladů). Přidružte účet nepřímého zákazníka CSP a příslušná předplatná Azure k entitě, kterou jste vytvořili dříve.
3. Postupujte podle kroků v části [Vytvoření uživatele s přístupem správce](tutorial-user-access.md#create-a-user-with-admin-access) a vytvořte uživatelský účet s přístupem správce. Potom se ujistěte, že má uživatelský účet přístup ke konkrétním entitám, které jste vytvořili dříve pro nepřímý účet.

Nepřímí partneři CSP se k portálu Cloudyn přihlašují pomocí účtů, které jste pro ně vytvořili.


[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili své informace o CSP k registraci u Cloudyn. Také jste se přihlásili k portálu Cloudyn a začali jste zobrazovat informace o nákladech. Další informace o Cloudyn najdete v kurzu pro Cloudyn.

> [!div class="nextstepaction"]
> [Kontrola využití a nákladů](tutorial-review-usage.md)