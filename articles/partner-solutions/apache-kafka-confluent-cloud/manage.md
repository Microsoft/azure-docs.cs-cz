---
title: Správa partnerských řešení cloudu Azure
description: Tento článek popisuje správu cloudu s neplynulou správou v Azure Portal. Jak nastavit jednotné přihlašování, odstranit organizaci v influent a získat podporu.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/08/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f8a54096ecda4729f7070120a02be3055f933cea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99989128"
---
# <a name="manage-the-confluent-cloud-resource"></a>Správa cloudového prostředku s možností influent

Tento článek popisuje, jak spravovat vaši instanci Apache Kafka pro cloudovou službu v Azure. Ukazuje, jak nastavit jednotné přihlašování (SSO), odstranit organizaci typu influent a vytvořit žádost o podporu.

## <a name="single-sign-on"></a>Jednotné přihlašování

K implementaci jednotného přihlašování pro vaši organizaci může správce klienta importovat aplikaci Galerie. Tento krok je volitelný. Informace o importu aplikace najdete v tématu [rychlý Start: Přidání aplikace do tenanta Azure Active Directory (Azure AD)](../../active-directory/manage-apps/add-application-portal.md). Když správce klienta aplikaci naimportuje, uživatelé nemusejí explicitně souhlasit, aby povolili přístup pro cloudový portál influent.

K povolení jednotného přihlašování použijte tento postup:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Přejděte na **Přehled** vaší instance cloudového prostředku, který je v cloudu.
1. Vyberte odkaz pro **správu v cloudu s možností influent**.

   :::image type="content" source="media/sso-link.png" alt-text="Jednotné přihlašování portálu pro influent":::

1. Pokud správce tenanta neimportoval aplikaci Galerie pro souhlas jednotného přihlašování, udělte jim oprávnění a souhlas. Tento krok je potřeba jenom při prvním přístupu k odkazu pro **správu v cloudu s možností influent**.
1. Vyberte účet Azure AD pro jednotné přihlašování ke cloudovém portálu.
1. Po zadání souhlasu budete přesměrováni na cloudový portál, který máte v cloudu.

## <a name="set-up-cluster"></a>Nastavení clusteru

Informace o nastavení clusteru najdete [v tématu Vytvoření clusteru v dokumentaci k prostředí s podporou cloudového](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)prostředí.

## <a name="delete-confluent-organization"></a>Odstranit organizaci v influent

Když už nepotřebujete svůj cloudový prostředek v cloudu, odstraňte ho v cloudu Azure a ve službě Fluent Cloud.

Postup odstranění prostředků v Azure:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Vyberte **všechny prostředky** a **vyfiltrujte je podle názvu** cloudového prostředku, který má v _organizaci_ **typ prostředku** . Nebo v Azure Portal vyhledejte název prostředku.
1. V **přehledu** prostředku vyberte **Odstranit**.

    :::image type="content" source="media/delete-resources-icon.png" alt-text="Ikona odstranění prostředku":::

1. Odstranění potvrďte tak, že zadáte název prostředku a vyberete **Odstranit**.

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="Vyzvat k potvrzení odstranění prostředku":::

Pokud chcete odstranit prostředek v cloudu v cloudu, přečtěte si dokumentaci pro [cloudová prostředí s](https://docs.confluent.io/current/cloud/using/environments.html) [informacemi](https://docs.confluent.io/current/cloud/using/cloud-basics.html)o tom, co je v dokumentaci ke službě Fluent Cloud – dokumentace ke službě influent.

Cluster a všechna data v clusteru se trvale odstraní. Pokud vaše smlouva obsahuje klauzuli uchovávání dat, udržuje data po dobu určenou v [dokumentaci k podmínkám používání služby-Fluent](https://www.confluent.io/confluent-cloud-tos).

Účtuje se vám průběžné využití v době odstranění clusteru. Až se váš cluster trvale odstraní, influent vám pošle e-mailové potvrzení.

## <a name="get-support"></a>Získání podpory

Pokud chcete odeslat žádost o podporu, obraťte se na [podporu](https://support.confluent.io) , nebo odešlete žádost prostřednictvím portálu, jak je znázorněno níže.

> [!NOTE]
> Před přihlášením k portálu podpory pro influent uživatele resetujte heslo poprvé. Pokud nemáte účet se službou influent Cloud, pošlete e-mail na `cloud-support@confluent.io` Další pomoc.

Na portálu můžete buď Odeslat žádost prostřednictvím služby Azure Help and Support, nebo přímo z vaší instance Apache Kafka pro cloudovou ochranu v Azure.

Odeslání žádosti prostřednictvím služby Azure Help and Support:

1. Vyberte **Nápověda a podpora**.
1. Vyberte **vytvořit žádost o podporu**.
1. Ve formuláři vyberte pro **typ problému** možnost **Technical** . Vyberte své předplatné. V seznamu služeb vyberte v Azure možnost **influent**.

    :::image type="content" source="media/support-request-help.png" alt-text="Vytvořte žádost o podporu z pomoci.":::

Pokud chcete odeslat žádost z prostředku, postupujte podle těchto kroků:

1. V Azure Portal vyberte svou organizaci v influent.
1. V nabídce na levé straně obrazovky vyberte **Nová žádost o podporu**.
1. Pokud chcete vytvořit žádost o podporu, vyberte odkaz na **portál influent**.

    :::image type="content" source="media/support-request.png" alt-text="Vytvořte žádost o podporu z instance.":::

## <a name="next-steps"></a>Další kroky

Pomoc při řešení potíží najdete v tématu [řešení potíží s Apache Kafka pro cloudová řešení](troubleshoot.md).
