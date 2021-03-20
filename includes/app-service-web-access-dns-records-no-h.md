---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 2f490a5b12484a91e963d068810b292d7761521a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95986532"
---
> [!NOTE]
> K nakonfigurování vlastního názvu DNS pro Azure App Service můžete použít Azure DNS. Další informace najdete v tématu popisujícím [použití Azure DNS k určení nastavení vlastní domény pro službu Azure](../articles/dns/dns-custom-domain.md#app-service-web-apps).
>
>

1. Přihlaste se k webu vašeho poskytovatele domény.

1. Vyhledejte stránku pro správu záznamů DNS. Každý poskytovatel domén má vlastní rozhraní pro správu záznamů DNS, takže se obraťte na dokumentaci poskytovatele. Hledejte oblasti webu označené jako **Domain Name** (Název domény), **DNS** nebo **Name Server Management** (Správa názvových serverů).

   Často můžete najít stránku záznamy DNS zobrazením informací o účtu a pak vyhledat odkaz, jako je **moje doména**. Přejít na tuto stránku a vyhledat odkaz s názvem, jako je **soubor zóny**, **záznamy DNS** nebo **Pokročilá konfigurace**.

   Následující snímek obrazovky obsahuje příklad stránky záznamů DNS:

   ![Snímek obrazovky, který zobrazuje ukázkovou stránku záznamů DNS.](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Na ukázkovém snímku obrazovky vyberte **Přidat** a vytvořte záznam. Někteří poskytovatelé nabízejí různé odkazy pro přidání různých typů záznamů. Opět se obraťte na dokumentaci poskytovatele.

> [!NOTE]
> U určitých poskytovatelů, jako je například GoDaddy, se změny záznamů DNS neprojeví, dokud nevyberete samostatný odkaz **Save Changes** (Uložit změny).
