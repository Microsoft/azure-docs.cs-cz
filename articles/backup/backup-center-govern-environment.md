---
title: Správa záloh pomocí centra zálohování
description: Naučte se řídit prostředí Azure a zajistit, aby všechny vaše prostředky vyhovovaly z perspektivy zálohování pomocí centra zálohování.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 009ee461b0372a3fb73ffb3b0ee5151b77bd8ef8
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995765"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>Správa záloh pomocí centra zálohování

Centrum zálohování vám pomůže řídit vaše prostředí Azure a zajistit, aby všechny vaše prostředky vyhovovaly z perspektivy zálohování. Níže jsou uvedené některé možnosti zásad správného řízení služby Backup Center:

* Zobrazení a přiřazení zásad Azure pro zálohování

* Zobrazit všechny zdroje dat, které nebyly nakonfigurovány pro zálohování.

## <a name="supported-scenarios"></a>Podporované scénáře

* Podrobný seznam podporovaných a nepodporovaných scénářů najdete v tématu věnovaném [matrici podpory](backup-center-support-matrix.md) .

## <a name="azure-policies-for-backup"></a>Zásady Azure pro zálohování

Chcete-li zobrazit všechny [zásady Azure](https://docs.microsoft.com/azure/governance/policy/overview) , které jsou k dispozici pro zálohování, vyberte položku nabídky **zásady Azure pro zálohování** . Zobrazí se všechny předdefinované a vlastní [definice zásad Azure pro zálohování](policy-reference.md) , které jsou k dispozici pro přiřazení k vašim předplatným a skupinám prostředků.

Výběr libovolné definice vám umožní [přiřadit zásadu](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage#assign-a-policy) k oboru.

![Vybrat definice Azure Policy](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="protectable-datasources"></a>Chránitelné zdroje dat

Výběr položky nabídky **chránitelné zdroje dat** umožňuje zobrazit všechny zdroje dat, které nejsou nakonfigurované pro zálohování. Seznam můžete filtrovat podle předplatného zdroje dat, skupiny prostředků, umístění, typu a značek. Jakmile identifikujete zdroj dat, který je třeba zálohovat, můžete kliknout pravým tlačítkem na odpovídající položku mřížky a vybrat **zálohování** a nakonfigurovat zálohování pro daný prostředek.

![Nabídka chránitelné zdroje dat](./media/backup-center-govern-environment/protectable-datasources.png)

## <a name="next-steps"></a>Další kroky

* [Monitorování a provozování záloh](backup-center-monitor-operate.md)
* [Provádění akcí pomocí centra zálohování](backup-center-actions.md)
* [Získejte přehled o vašich zálohách](backup-center-obtain-insights.md)
