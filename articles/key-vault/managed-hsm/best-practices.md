---
title: Osvědčené postupy s využitím Azure Key Vault spravovaného HSM
description: Tento dokument popisuje některé z osvědčených postupů pro použití Key Vault
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 9ef3b19e5064c8a88bf80eebf57539be72747fe4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482513"
---
# <a name="best-practices-when-using-managed-hsm"></a>Osvědčené postupy při použití spravovaného HSM

## <a name="control-access-to-your-managed-hsm"></a>Řízení přístupu ke spravovanému modulu HSM

Spravovaný modul HSM je cloudová služba, která chrání šifrovací klíče. Vzhledem k tomu, že jsou tyto klíče citlivé a důležité pro podnikání, nezapomeňte zabezpečit přístup ke spravovaným HSM tím, že povolíte jenom autorizované aplikace a uživatele. Tento [článek](access-control.md) poskytuje přehled modelu přístupu. Vysvětluje ověřování a autorizaci a řízení přístupu na základě rolí.
- Vytvořte [Azure Active Directory skupinu zabezpečení](../../active-directory/fundamentals/active-directory-manage-groups.md) pro správce HSM (místo přiřazování role správce jednotlivcům). V případě odstranění jednotlivého účtu to zabrání možnosti "zámek správy".
- Uzamčení přístupu ke skupinám pro správu, předplatným, skupinám prostředků a spravovaným HSM – používání služby Azure RBAC k řízení přístupu ke skupinám pro správu, předplatným a skupinám prostředků
- Vytvořte přiřazení rolí podle klíčů pomocí [spravované místní RBAC](access-control.md#data-plane-and-managed-hsm-local-rbac).
- Aby se zachovalo oddělení cel, nepřiřazují více rolí stejným objektům zabezpečení. 
- K přiřazení rolí použijte přístup k objektům s nejnižšími oprávněními.
- Vytvořte vlastní definici role s přesnou sadou oprávnění.

## <a name="choose-regions-that-support-availability-zones"></a>Výběr oblastí, které podporují zóny dostupnosti

- Pokud chcete zajistit optimální vysokou dostupnost a odolnost zóny, vyberte oblasti Azure, ve kterých se podporuje [zóny dostupnosti](../../availability-zones/az-overview.md) . Tyto oblasti se v Azure Portal zobrazí jako "Doporučené oblasti".

## <a name="backup"></a>Backup

- Ujistěte se, že provádíte pravidelné zálohování modulu hardwarového zabezpečení (HSM). Zálohy se dají provádět na úrovni HSM a pro konkrétní klíče. 

## <a name="turn-on-logging"></a>Zapnout protokolování

- [Zapněte protokolování](logging.md) pro modul hardwarového zabezpečení (HSM). Nastavte také výstrahy.

## <a name="turn-on-recovery-options"></a>Zapnout možnosti obnovení

- [Obnovitelné odstranění](../general/soft-delete-overview.md) je ve výchozím nastavení zapnuté.
- Zapněte ochranu vyprázdnění, pokud chcete chránit před vynuceným odstraněním modulu HARDWAROVÉho zabezpečení, a to i po zapnutí obnovitelného odstranění.

## <a name="next-steps"></a>Další kroky

- Informace o úplném zálohování a obnovení modulu HSM najdete v tématu [úplná záloha/obnovení](backup-restore.md) .
- Informace o tom, jak pomocí Azure Monitor nakonfigurovat protokolování, najdete v tématu [MANAGED HSM Logging](logging.md) .
- Viz [Správa spravovaných klíčů HSM](key-management.md) pro správu klíčů.
- Správa přiřazení rolí najdete v tématu [spravovaná Správa rolí HSM](role-management.md) .
