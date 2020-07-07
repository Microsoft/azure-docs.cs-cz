---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 66d3397fae24ee2546dae4eb5d7c9d188f9ede99
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "78944119"
---
> [!NOTE]
> K nakonfigurování vlastního názvu DNS pro Azure App Service můžete použít Azure DNS. Další informace najdete v tématu popisujícím [použití Azure DNS k určení nastavení vlastní domény pro službu Azure](../articles/dns/dns-custom-domain.md#app-service-web-apps).
>
>

Přihlaste se k webu vašeho poskytovatele domény.

Vyhledejte stránku pro správu záznamů DNS. Každý poskytovatel domén má vlastní rozhraní pro správu záznamů DNS, takže se obraťte na dokumentaci poskytovatele. Hledejte oblasti webu označené jako **Domain Name** (Název domény), **DNS** nebo **Name Server Management** (Správa názvových serverů). 

Často můžete stránku záznamů DNS najít tak, že zobrazíte informace o vašem účtu a vyhledáte odkaz jako **My domains** (Moje domény). Přejděte na příslušnou stránku a vyhledejte odkaz pojmenovaný podobně jako **Zone file** (Soubor zóny), **DNS Records** (Záznamy DNS) nebo **Advanced configuration** (Pokročilá konfigurace).

Následující snímek obrazovky obsahuje příklad stránky záznamů DNS:

![Příklad stránky záznamů DNS](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

Na příkladu snímku obrazovky vytvoříte nový záznam výběrem možnosti **Add** (Přidat). Někteří poskytovatelé nabízejí různé odkazy pro přidání různých typů záznamů. Opět se obraťte na dokumentaci poskytovatele.

> [!NOTE]
> U určitých poskytovatelů, jako je například GoDaddy, se změny záznamů DNS neprojeví, dokud nevyberete samostatný odkaz **Save Changes** (Uložit změny). 
