---
title: Řízení infrastruktury zálohování s využitím centra zálohování
description: Naučte se řídit prostředí Azure a zajistit, aby všechny vaše prostředky vyhovovaly z perspektivy zálohování pomocí centra zálohování.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 67b0591c7d7146d162687018854365d338105d76
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98893842"
---
# <a name="govern-your-backup-estate-using-backup-center-preview"></a>Správa záloh pomocí centra zálohování (Preview)

Centrum zálohování vám pomůže řídit vaše prostředí Azure a zajistit, aby všechny vaše prostředky vyhovovaly z perspektivy zálohování. Níže jsou uvedené některé možnosti zásad správného řízení služby Backup Center:

* Zobrazení a přiřazení zásad Azure pro zálohování

* Podívejte se na dodržování předpisů vašich prostředků ve všech předdefinovaných zásadách Azure pro zálohování.

* Zobrazit všechny zdroje dat, které nebyly nakonfigurovány pro zálohování.

## <a name="supported-scenarios"></a>Podporované scénáře

* Podrobný seznam podporovaných a nepodporovaných scénářů najdete v tématu věnovaném [matrici podpory](backup-center-support-matrix.md) .

## <a name="azure-policies-for-backup"></a>Zásady Azure pro zálohování

Chcete-li zobrazit všechny [zásady Azure](../governance/policy/overview.md) , které jsou k dispozici pro zálohování, vyberte položku nabídky **zásady Azure pro zálohování** . Zobrazí se všechny předdefinované a vlastní [definice zásad Azure pro zálohování](policy-reference.md) , které jsou k dispozici pro přiřazení k vašim předplatným a skupinám prostředků.

Výběr libovolné definice vám umožní [přiřadit zásadu](../governance/policy/tutorials/create-and-manage.md#assign-a-policy) k oboru.

![Vybrat definice Azure Policy](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="backup-compliance"></a>Dodržování předpisů pro zálohování

Kliknutím na položku nabídky Kompatibilita záloh můžete zobrazit [dodržování předpisů](../governance/policy/how-to/get-compliance-data.md) vašich prostředků podle různých předdefinovaných zásad, které jste přiřadili vašemu prostředí Azure. Můžete zobrazit procentní podíl prostředků, které jsou v souladu se všemi zásadami, a také zásady, které mají jeden nebo více nekompatibilních zdrojů.

![Zobrazit dodržování předpisů pro zálohování](./media/backup-center-govern-environment/azure-policy-compliance.png)

## <a name="protectable-datasources"></a>Chránitelné zdroje dat

Výběr položky nabídky **chránitelné zdroje dat** umožňuje zobrazit všechny zdroje dat, které nejsou nakonfigurované pro zálohování. Seznam můžete filtrovat podle předplatného zdroje dat, skupiny prostředků, umístění, typu a značek. Jakmile identifikujete zdroj dat, který je třeba zálohovat, můžete kliknout pravým tlačítkem na odpovídající položku mřížky a vybrat **zálohování** a nakonfigurovat zálohování pro daný prostředek.

![Nabídka chránitelné zdroje dat](./media/backup-center-govern-environment/protectable-datasources.png)

> [!NOTE]
> Pokud jako typ zdroje dat vyberete **SQL na virtuálním počítači Azure** , v zobrazení **chránitelné zdroje dat** se zobrazí seznam všech virtuálních počítačů galerie, které nemají žádné databáze SQL, které byly nakonfigurovány pro zálohování.
> Pokud jako typ zdroje dat vyberete možnost **Azure Storage (soubory Azure)** , zobrazí se v zobrazení **chráněných zdrojů** dat seznam všech účtů úložiště (které podporují sdílené složky), které nemají žádné sdílené složky, které byly nakonfigurovány pro zálohování.


## <a name="next-steps"></a>Další kroky

* [Monitorování a provozování záloh](backup-center-monitor-operate.md)
* [Provádění akcí pomocí centra zálohování](backup-center-actions.md)
* [Získejte přehled o vašich zálohách](backup-center-obtain-insights.md)