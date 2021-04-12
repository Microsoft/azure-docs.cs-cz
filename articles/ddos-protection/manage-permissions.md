---
title: Azure DDoS Protection plánu oprávnění
description: Naučte se spravovat oprávnění v plánu ochrany.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: df53062c7c897493a47d88ea2873f9710b9825bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99806252"
---
# <a name="manage-ddos-protection-plans-permissions-and-restrictions"></a>Správa plánů DDoS Protection: oprávnění a omezení

Plán DDoS Protection funguje napříč oblastmi a předplatnými. Stejný plán může být propojený s virtuálními sítěmi z jiných předplatných v různých oblastech v rámci vašeho tenanta. K předplatnému, ke kterému je přiřazen plán, se účtují měsíční periodická faktura za plán, jakož i poplatky za překročení limitu pro případ, že počet chráněných veřejných IP adres překračuje 100. Další informace o cenách DDoS najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="prerequisites"></a>Požadavky

- Než budete moct dokončit kroky v tomto kurzu, musíte nejdřív vytvořit [Plán Standard Protection pro Azure DDoS](manage-ddos-protection.md).

## <a name="permissions"></a>Oprávnění

Aby bylo možné pracovat s plány ochrany DDoS Protection, musí být váš účet přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, která je přiřazena k příslušným akcím uvedeným v následující tabulce:

| Akce                                            | Name                                     |
| ---------                                         | -------------                            |
| Microsoft. Network/ddosProtectionPlans/Read        | Přečíst plán DDoS Protection              |
| Microsoft. Network/ddosProtectionPlans/Write       | Vytvořit nebo aktualizovat plán DDoS Protection  |
| Microsoft. Network/ddosProtectionPlans/DELETE      | Odstranění plánu DDoS Protection            |
| Microsoft. Network/ddosProtectionPlans/JOIN/Action | Připojit se k plánu DDoS Protection              |

Aby bylo možné povolit DDoS ochranu pro virtuální síť, musí mít váš účet také přiřazeny příslušné [akce pro virtuální sítě](../virtual-network/manage-virtual-network.md#permissions).

## <a name="azure-policy"></a>Azure Policy

Pro většinu organizací není nutné vytvářet více než jeden plán. Plán nelze přesunout mezi předplatnými. Pokud chcete změnit předplatné, ve kterém je plán, je nutné odstranit stávající plán a vytvořit nový.

Pro zákazníky, kteří mají různá předplatná, a kteří chtějí zajistit, aby byl v rámci svého tenanta nasazený jeden plán pro řízení nákladů, můžete použít Azure Policy k [omezení vytváření Azure DDoS Protectionch standardních plánů](https://aka.ms/ddosrestrictplan). Tato zásada zablokuje vytváření jakýchkoli plánů DDoS, pokud předplatné předtím neoznačilo výjimku. V této zásadě se zobrazí také seznam všech předplatných, která mají nasazený plán DDoS, ale neměly by se jim označit jako nedodržující předpisy.


## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak zobrazit a nakonfigurovat telemetrii pro svůj plán DDoS Protection, přejděte k kurzům.

> [!div class="nextstepaction"]
> [Zobrazení a konfigurace telemetrie ochrany před útoky DDoS](telemetry.md)
