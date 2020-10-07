---
title: Vyhledání ID tenanta, ID objektu a podrobností o přidružení partnerů v Azure Marketplace
description: Jak najít ID tenanta, ID objektu a podrobnosti o přidružení partnera pro ID předplatného v Azure Marketplace.
ms.service: marketplace
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/09/2020
ms.openlocfilehash: c35e42aaf5e4bd31a54f807969c3671ecc5668ab
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91814410"
---
# <a name="find-tenant-id-object-id-and-partner-association-details"></a>Vyhledání ID tenanta, ID objektu a podrobností přidružení partnera

Tento článek popisuje, jak najít ID tenanta, ID objektu a podrobnosti o přidružení partnerů spolu s příslušnými ID předplatného.

Pokud potřebujete získat snímky obrazovky těchto položek v Azure Cloud Shell, které se mají použít pro pomoc s laděním, přejděte dolů k části [vyhledání tenanta, objektu a přidružení ID partnera pro ladění](#find-ids-for-debugging).

>[!Note]
> K provedení těchto kroků má oprávnění pouze vlastník předplatného.

## <a name="find-tenant-id"></a>Najít ID tenanta

1. Přejděte na web [Azure Portal](https://ms.portal.azure.com/).
2. Vyberte **Azure Active Directory**.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-ad.png" alt-text="Ikona Azure Active Directory v Azure Portal":::

3. Vyberte **skupiny**. Vaše ID tenanta najdete v poli **informace o tenantovi** .

    :::image type="content" source="media/tenant-and-object-id/select-groups-1.png" alt-text="Ikona Azure Active Directory v Azure Portal":::

## <a name="find-subscriptions-and-roles"></a>Vyhledání předplatných a rolí

1. Přejít na Azure Portal a vybrat **Azure Active Directory** , jak je uvedeno v krocích 1 a 2 výše.
2. Vyberte **Předplatná**.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-subscriptions-1.png" alt-text="Ikona Azure Active Directory v Azure Portal":::

3. Zobrazit předplatná a role.

    :::image type="content" source="media/tenant-and-object-id/subscriptions-screen-1.png" alt-text="Ikona Azure Active Directory v Azure Portal":::

## <a name="find-partner-id"></a>Najít ID partnera

1. Přejděte na stránku předplatná, jak je popsáno v předchozí části.
2. Vyberte předplatné.
3. V části **fakturace**vyberte **informace o partnerovi**.

    :::image type="content" source="media/tenant-and-object-id/menu-partner-information.png" alt-text="Ikona Azure Active Directory v Azure Portal":::

## <a name="find-user-object-id"></a>Najít uživatele (ID objektu)

1. Přihlaste se k [portálu pro správu Office 365](https://portal.office.com/adminportal/home) pomocí účtu v požadovaném tenantovi s příslušnými právy správce.
2. V nabídce na levé straně rozbalte část centra pro **správu** a potom vyberte možnost Azure Active Directory pro spuštění konzoly správce v novém okně prohlížeče.
3. Vyberte **Uživatelé**.
4. Vyhledejte požadovaného uživatele nebo ho vyhledejte a potom vyberte název účtu, abyste si zobrazili informace o profilu uživatelského účtu.
5. ID objektu se nachází v části Identita na pravé straně.

    :::image type="content" source="media/tenant-and-object-id/azure-ad-admin-center.png" alt-text="Ikona Azure Active Directory v Azure Portal":::

6. **Přiřazení rolí** můžete najít tak, že v nabídce vlevo vyberete **řízení přístupu (IAM)** a pak na **přiřazení rolí**.

    :::image type="content" source="https://docs.microsoft.com/azure/role-based-access-control/media/role-assignments-portal/role-assignments.png" alt-text="Ikona Azure Active Directory v Azure Portal":::

## <a name="find-ids-for-debugging"></a>Najít ID pro ladění

Tato část popisuje, jak najít přidružení tenanta, objektu a partnerského ID pro účely ladění.

1. Přejděte na web [Azure Portal](https://ms.portal.azure.com/).
2. Otevřete Azure Cloud Shell výběrem ikony PowerShellu v pravém horním rohu.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-cloud-shell-1.png" alt-text="Ikona Azure Active Directory v Azure Portal":::

3. Vyberte **PowerShell**.

    :::image type="content" source="media/tenant-and-object-id/icon-powershell.png" alt-text="Ikona Azure Active Directory v Azure Portal":::

4. Zaškrtněte políčko **odběr** a vyberte, ke kterému je partner propojen, a pak **vytvořte úložiště**. Jedná se o jednorázovou akci. Pokud jste už nastavení úložiště nastavili, přejděte k dalšímu kroku.

    :::image type="content" source="media/tenant-and-object-id/create-storage-window.png" alt-text="Ikona Azure Active Directory v Azure Portal":::

5. Při vytváření (nebo již se) úložišti se otevře okno Azure Cloud Shell.

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-1.png" alt-text="Ikona Azure Active Directory v Azure Portal":::

6. V části Podrobnosti o přidružení partnerů nainstalujte rozšíření pomocí tohoto příkazu:<br>`az extension add --name managementpartner`.<br>Azure Cloud Shell si všimněte, že rozšíření je již nainstalováno:

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-2.png" alt-text="Ikona Azure Active Directory v Azure Portal":::

7. Podrobnosti o partnerovi ověřte pomocí tohoto příkazu:<br>`az managementpartner show --partner-id xxxxxx`<br>Příklad: `az managementpartner show --partner-id 4760962`.<br>Přichycení snímku obrazovky k výsledkům příkazu, který bude vypadat nějak takto:

    :::image type="content" source="media/tenant-and-object-id/partner-id-sample-screenshot.png" alt-text="Ikona Azure Active Directory v Azure Portal":::

>[!NOTE]
>Pokud snímek obrazovky vyžaduje více předplatných, použijte tento příkaz k přepínání mezi předplatnými:<br>`az account set --subscription "My Demos"`

## <a name="next-steps"></a>Další kroky

- [Podporované země a oblasti](sell-from-countries.md)