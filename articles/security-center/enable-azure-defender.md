---
title: Povolit ochranu úloh v integrovaném Azure Security Center
description: Naučte se, jak povolit Azure Defenderu pro rozšiřování ochrany Azure Security Center do hybridních i cloudových prostředků.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/24/2021
ms.openlocfilehash: 6496b18c8c22cbbd4fd3344736a4b38b7a998890
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102108844"
---
# <a name="quickstart-enable-azure-defender"></a>Rychlý Start: povolení Azure Defenderu

Další informace o výhodách Azure Defenderu najdete v tématu [Security Center bezplatné vs Azure Defender](security-center-pricing.md).

## <a name="prerequisites"></a>Požadavky

Pro účely Security Center rychlých startů a kurzů musíte povolit Azure Defender. 

Můžete chránit celé předplatné Azure pomocí Azure Defenderu a všechny prostředky v rámci tohoto předplatného zdědí všechny jeho ochrany.

K dispozici je bezplatná 30denní zkušební verze. Podrobnosti o cenách v měně zvolené a podle vaší oblasti najdete v tématu [Security Center ceny](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-azure-defender-from-the-azure-portal"></a>Povolit Azure Defender z Azure Portal

Pokud chcete povolit všechny Security Center funkce, včetně funkcí ochrany před hrozbami, musíte v předplatném, které obsahuje příslušné úlohy, povolit Azure Defender. Povolením na úrovni pracovního prostoru nepovolíte pro prostředky Azure přístup k virtuálnímu počítači za běhu, adaptivní řízení aplikací a zjišťování sítě. K dispozici jsou navíc jenom plány Azure Defenderu na úrovni pracovního prostoru, které jsou v Azure Defenderu pro servery a Azure Defender pro SQL servery na počítačích.

- **Azure Defender můžete povolit pro účty úložiště** buď na úrovni předplatného, nebo na úrovni prostředků.
- **Azure Defender pro SQL** můžete povolit buď na úrovni předplatného, nebo na úrovni prostředků.
- Ochranu před hrozbami pro **Azure Database for MariaDB/MySQL/PostgreSQL** můžete povolit jenom na úrovni prostředků.

### <a name="to-enable-azure-defender-on-your-subscriptions-and-workspaces"></a>Povolení Azure Defenderu pro vaše předplatná a pracovní prostory:

- Povolení služby Azure Defender v jednom předplatném:

    1. V hlavní nabídce Security Center vyberte **cenové & nastavení**.
    1. Vyberte předplatné nebo pracovní prostor, který chcete chránit.
    1. Pro upgrade vyberte **Azure Defender** .
    1. Vyberte **Uložit**.

    > [!TIP]
    > Všimnete si, že každý plán v Azure Defenderu se účtuje samostatně a dá se individuálně nastavit na hodnotu Zapnuto nebo vypnuto. Můžete například chtít vypnout Azure Defender pro App Service v předplatných, která nemají přiřazený plán Azure App Service. 

    :::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="Stránka s cenami Security Center na portálu":::

- Povolení Azure Defenderu pro několik předplatných nebo pracovních prostorů:

    1. Z bočního panelu Security Center vyberte **Začínáme**.

        Karta **upgrade** obsahuje seznam předplatných a pracovních prostorů, které mají nárok na registraci.

        :::image type="content" source="./media/enable-azure-defender/get-started-upgrade-tab.png" alt-text="Karta upgrade stránky Začínáme"::: 

    1. V seznamu **Vybrat odběry a pracovní prostory, pro které chcete povolit Azure Defender v** seznamu vyberte předplatné a pracovní prostory, které chcete upgradovat, a vyberte **upgradovat** , aby se povolilo Azure Defender.

       - Pokud vyberete předplatná a pracovní prostory, které nemají nárok na zkušební verzi, další krok se upgraduje a začnou platit poplatky.
       - Pokud vyberete pracovní prostor, který má nárok na bezplatnou zkušební verzi, bude další krok začínat zkušební verzí.

        :::image type="content" source="./media/enable-azure-defender/upgrade-selected-workspaces-and-subscriptions.png" alt-text="Upgradovat všechny vybrané pracovní prostory a odběry z úvodní stránky":::


## <a name="next-steps"></a>Další kroky

Teď, když jste povolili Azure Defender, povolte automatické shromažďování dat potřebnými agenty a rozšířeními popsanými v tématu [Automatické zřizování agentů a rozšíření z Azure Security Center](security-center-enable-data-collection.md).