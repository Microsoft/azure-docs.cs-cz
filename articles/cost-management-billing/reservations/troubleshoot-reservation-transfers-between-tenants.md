---
title: Řešení potíží s přenosy rezervací Azure mezi klienty
description: Tento článek pomáhá vlastníkům rezervací přenést objednávku rezervace z jednoho Azure Active Directoryho tenanta do jiného.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 02/24/2021
ms.openlocfilehash: 79473d57cc7504e7e6ef4ef68ba0cee74203f62b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055831"
---
# <a name="troubleshoot-reservation-transfers-between-tenants"></a>Řešení potíží s přenosy rezervací mezi klienty

Tento článek pomáhá vlastníkům rezervací přenést objednávku rezervace z jednoho Azure Active Directoryho tenanta do jiného. Když změníte adresář objednávky rezervace, odebere se veškerý přístup Azure RBAC k objednávce rezervace a závislým rezervacím. Po změně budete mít přístup jenom vy. Změnou adresáře se nezmění vlastnictví fakturace pro objednávku rezervace. Adresář je změněn pro nadřazenou objednávku rezervace a závislé rezervace.

Výměna a zrušení rezervace není potřeba k přenosu mezi klienty.

Po převedení rezervace do jiného tenanta můžete také přidat další vlastníky k rezervaci. Další informace najdete v tématu [kdo může ve výchozím nastavení spravovat rezervaci](view-reservations.md#who-can-manage-a-reservation-by-default).

Při přenosu objednávky rezervace se s ní přenesou všechny rezervace v této objednávce.

## <a name="transfer-a-reservation"></a>Přenos rezervace

Pomocí následujících kroků přeneste objednávku rezervace a závislé rezervace na jiného tenanta.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Pokud nejste správcem fakturace, ale jste vlastníkem rezervace, přejděte k **rezervacím** a potom přejděte ke kroku 6.
1. Přejděte na **Cost Management a fakturace**.
    - Pokud jste správce služby EA, vyberte v levé nabídce položku **obory fakturace** a potom v seznamu rozsahů fakturace vyberte jednu.
    - Pokud jste vlastníkem fakturačního profilu, který je zákazníkem Microsoftu, vyberte v levé nabídce možnost **profily fakturace**. V seznamu profilů fakturace vyberte jednu.
1. V nabídce vlevo vyberte **transakce rezervací**. Zobrazí se seznam transakcí rezervace.
1. Banner v horní části stránky čtení *nyní může spravovat rezervace správci fakturace. Kliknutím sem můžete spravovat rezervace.* Vyberte banner.
1. Zobrazí se úplný seznam rezervací pro registraci EA nebo profil pro fakturaci.
1. Vyberte rezervaci, kterou chcete přenést.
1. V části podrobnosti rezervace vyberte ID objednávky rezervace.
1. V části pořadí rezervací vyberte **změnit adresář**.
1. V podokně změnit adresář vyberte adresář služby Azure AD, na který chcete přenést rezervaci, a pak vyberte **Potvrdit**.

## <a name="next-steps"></a>Další kroky

- Další informace o rezervacích najdete v tématu [Co jsou rezervace Azure?](save-compute-costs-reservations.md).