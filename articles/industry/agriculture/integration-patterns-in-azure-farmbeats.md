---
title: Architektura Azure FarmBeats
description: Popisuje architekturu služby Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: ebc1cdc7c762117851072037624f11e9fa98b6f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182600"
---
# <a name="integration-patterns"></a>Vzory integrace

Azure FarmBeats je nabídka pro firmy, která je dostupná v Azure Marketplace. FarmBeats umožňuje agregaci datových sad pro zemědělství mezi poskytovateli a generování užitečných přehledů vytvořením umělých inteligentních funkcí (AI) nebo Machine Learning (ML) tím, že tyto sady odmítne.

![Beats farmy projektu](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Následující části popisují model integrace pro Azure FarmBeats.

## <a name="why-integrate-with-azure-farmbeats"></a>Proč integrovat s Azure FarmBeats?

Tato část se zaměřuje na partnery, kteří chtějí integrovat své datové systémy (jako jsou senzory, DRONY zachraňují životy a povětrnostní stanice) do Azure FarmBeats.

Azure FarmBeats je rozšiřitelná nabídka, která umožňuje zemědělským podnikům přidat své různé historické datové sady v reálném čase do jediné platformy. Azure FarmBeats pomáhá zemědělským podnikům normalizovat, dát a agregovat jeho data v kontextu farmy.

Když se stanete datovým partnerem s Azure FarmBeats, můžete si otevřít své systémy a rozšířit si jejich nabídky na více zákazníků. Azure FarmBeats poskytuje rozšiřitelnou vrstvu rozhraní API nazvanou DataHub, která pomáhá přijímat data ze zařízení systematicky a do standardizovaného schématu.

Jakmile budou data dostupná v rámci instance Azure FarmBeats vaší zákazníky, můžou vaši zákazníci sestavovat bohatší analýzy a nástroje nad vašimi daty.

## <a name="next-steps"></a>Další kroky

Další informace o integraci dat senzorů najdete v tématu Integrace [dat senzorů](sensor-partner-integration-in-azure-farmbeats.md) a pro integraci [partnerů v obrazovém rámečku.](imagery-partner-integration-in-azure-farmbeats.md)
