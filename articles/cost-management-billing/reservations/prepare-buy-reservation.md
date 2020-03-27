---
title: Příprava k nákupu rezervace Azure
description: Seznamte se s důležitými body ještě před nákupem rezervace Azure.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: 1f5ca2d43356eab98cffe8414c00d97e5744739a
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235656"
---
# <a name="prepare-to-buy-a-reservation"></a>Příprava k nákupu rezervace

Rezervace Azure umožňují šetřit peníze tím, že potvrzují závazek využívání řady prostředků Azure v rámci plánů na jeden nebo tři roky. Než se rozhodnete pro koupi rezervace, nezapomeňte si projít následující oddíly a připravit se na tento nákup.

## <a name="who-can-buy-a-reservation"></a>Kdo si může koupit rezervaci

Pokud si chcete koupit plán, musíte mít roli vlastníka předplatného v předplatném se smlouvou Enterprise (MS-AZR-0017P nebo MS-AZR-0148P), předplatném s průběžnými platbami (MS-AZR-0003P nebo MS-AZR-0023P) nebo předplatném se smlouvou se zákazníkem Microsoftu. Poskytovatelé cloudových řešení můžou nakupovat rezervace Azure prostřednictvím webu Azure Portal nebo  [Partnerského centra](/partner-center/azure-reservations) .

Zákazníci se smlouvou Enterprise (EA) můžou omezit nákupy na správce EA tak, že na portálu EA Portal zakážou možnost **Přidat rezervované instance**. Aby správce EA mohl zakoupit rezervaci, musí být vlastníkem alespoň jednoho předplatného EA. Tato možnost je užitečná pro podniky, které chtějí, aby rezervace pro různá nákladová centra pořizoval centralizovaný tým. Po zakoupení můžou centralizované týmy přidat k rezervacím vlastníky nákladových center. Vlastníci potom vymezí rozsah rezervace na svoje předplatná. Centrální tým nemusí umožnit přístup vlastníka předplatného tam, kde se rezervace pořizuje.

Sleva za rezervaci se vztahuje jenom na prostředky přidružené k předplatným zakoupeným prostřednictvím plánů Enterprise, Cloud Solution Provider (CSP), plánů se smlouvou se zákazníkem Microsoftu a individuálních plánů s průběžnými platbami.

## <a name="scope-reservations"></a>Vymezení rozsahů rezervací

Rozsah rezervace můžete vymezit na předplatné nebo na skupiny prostředků. Nastavením rozsahu rezervace se určí, kde se úspory získané rezervací uplatní. Pokud vymezíte rozsah rezervace na skupinu prostředků, uplatní se slevy za rezervaci jenom u této skupiny prostředků, ne u celého předplatného.

### <a name="reservation-scoping-options"></a>Možnosti vymezení rozsahů rezervací

Při vymezování rozsahu rezervace máte v závislosti na vašich potřebách k dispozici tři možnosti:

- Rozsah **Jedna skupina prostředků**: Sleva za rezervaci se uplatní jenom u odpovídajících prostředků ve vybrané skupině prostředků.
- Rozsah **Jedno předplatné**: Sleva za rezervaci se uplatní u odpovídajících prostředků ve vybraném předplatném.
- Rozsah **Sdílený**: Sleva za rezervaci se uplatní u odpovídajících prostředků v oprávněných předplatných v kontextu fakturace. U zákazníků se smlouvou Enterprise je kontextem fakturace příslušná registrace. U zákazníků se smlouvou se zákazníkem Microsoftu je rozsahem fakturace příslušný fakturační profil. U individuálních předplatných se sazbami pro průběžné platby jsou rozsahem fakturace všechna oprávněná předplatná vytvořená správcem účtu.

Při uplatňování slev za používání Azure zpracovává rezervace v tomto pořadí:

1. Rezervace s rozsahem na jednu skupinu prostředků
2. Rezervace s rozsahem na jedno předplatné
3. Rezervace se sdíleným rozsahem

Jedna skupina prostředků může získat slevy za rezervaci z více rezervací v závislosti na tom, jak vymezíte rozsahy vašich rezervací.

Po zakoupení rezervace máte možnost rozsah aktualizovat. Provedete to tak, že přejdete k rezervaci, kliknete na **Konfigurace** a změníte rozsah této rezervace. Změna rozsahu rezervace není komerční transakcí. Období vaší rezervace se nezmění. Další informace o aktualizaci rozsahu najdete v tématu [Aktualizace rozsahu po zakoupení rezervace](manage-reserved-vm-instance.md#change-the-reservation-scope).

![Příklad znázorňující změnu rozsahu rezervace](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="purchase-reservations"></a>Nákup rezervací

Rezervace si můžete koupit prostřednictvím webu Azure Portal, rozhraní API, PowerShellu nebo CLI. Přečtěte si následující články, ve kterých se dozvíte, kdy jste připravení koupit rezervace:

- [App Service](prepay-app-service-isolated-stamp.md)
- [Azure Cache for Redis](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Databáze Cosmos](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Data Explorer](../../data-explorer/pricing-reserved-capacity.md)
- [Disk Storage](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Dedicated Host](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [Softwarové plány](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Storage](../../storage/blobs/storage-blob-reserved-capacity.md)
- [SQL Database](../../sql-database/sql-database-reserved-capacity.md)
- [SQL Data Warehouse](prepay-sql-data-warehouse-charges.md)
- [Virtual Machines](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

## <a name="next-steps"></a>Další kroky

- [Správa rezervací prostředků Azure](manage-reserved-vm-instance.md)
