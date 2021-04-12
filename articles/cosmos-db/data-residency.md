---
title: Postup splnění požadavků na zaplnění dat v Azure Cosmos DB
description: Naučte se plnit požadavky na umístění dat v Azure Cosmos DB, aby vaše data a zálohy zůstaly v jedné oblasti.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: c5f8a4361774368e3934d1e2b16c8311876f5caa
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491861"
---
# <a name="how-to-meet-data-residency-requirements-in-azure-cosmos-db"></a>Postup splnění požadavků na zaplnění dat v Azure Cosmos DB

V Azure Cosmos DB můžete nakonfigurovat data a zálohy tak, aby zůstaly v jedné oblasti, aby splňovaly[ požadavky na sídlo.](https://azure.microsoft.com/en-us/global-infrastructure/data-residency/)

## <a name="residency-requirements-for-data"></a>Požadavky na sídlo pro data

V Azure Cosmos DB musíte explicitně nakonfigurovat replikaci dat mezi oblastmi. Naučte se konfigurovat geografickou replikaci pomocí [Azure Portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account) [Azure CLI](scripts/cli/common/regions.md). Aby se splnily požadavky na umístění dat, můžete vytvořit zásadu Azure, která umožňuje určitým oblastem zabránit replikaci dat do nežádoucích oblastí.

## <a name="residency-requirements-for-backups"></a>Požadavky na sídlo pro zálohy

**Zálohy průběžného režimu**: tyto zálohy jsou ve výchozím nastavení rezidentní, protože jsou uložené buď místně redundantní, nebo v zóně redundantního úložiště. Další informace najdete v článku [průběžné zálohování](continuous-backup-restore-portal.md) .

**Zálohy periodického režimu**: ve výchozím nastavení se zálohy účtů periodického režimu budou ukládat v geograficky redundantním úložišti. Pro režimy periodického zálohování můžete datovou redundanci nakonfigurovat na úrovni účtu. Pro úložiště zálohování existují tři možnosti redundance. Jedná se o místní redundanci, redundanci zóny nebo geografickou redundanci. Další informace najdete v tématu Postup [Konfigurace redundance zálohy](configure-periodic-backup-restore.md#configure-backup-interval-retention) pomocí portálu.

## <a name="use-azure-policy-to-enforce-the-residency-requirements"></a>Použití Azure Policy k vymáhání požadavků na dosazení

Pokud máte požadavky na umístění dat, které vyžadují, abyste zachovali všechna data v jedné oblasti Azure, můžete vynutit redundantní nebo místně redundantní zálohy pro váš účet pomocí Azure Policy.  Můžete také vymáhat zásadu, že účty Azure Cosmos DB nejsou geograficky replikovány do jiných oblastí.

Azure Policy je služba, kterou můžete použít k vytváření, přiřazování a správě zásad, které používají pravidla pro prostředky Azure. Azure Policy vám pomůže zajistit, aby tyto prostředky vyhovovaly vašim firemním standardům a smlouvám o úrovni služeb. Další informace najdete v tématu použití [Azure Policy](policy.md) k implementaci zásad správného řízení a řízení prostředků Azure Cosmos DB.

## <a name="next-steps"></a>Další kroky

* Konfigurace a Správa pravidelného zálohování pomocí [Azure Portal](configure-periodic-backup-restore.md)
* Nakonfigurujte a spravujte průběžné zálohování pomocí [Azure Portal](continuous-backup-restore-portal.md), [PowerShellu](continuous-backup-restore-powershell.md), rozhraní příkazového [řádku](continuous-backup-restore-command-line.md)nebo [Azure Resource Manager](continuous-backup-restore-template.md).
