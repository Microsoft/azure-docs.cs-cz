---
title: Ukázka podrobného plánu Cílová zóna migrace CAF – přehled
description: Přehled a architektura přechodu na cloud pro Azure (CAF) pro ukázkový podrobný plán Cílová zóna migrace.
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: 9cbf1c1a4ac4cf7a58ed27ec05cc77997da1366d
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472195"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>Přehled architektury přechodu na cloud pro Azure od Microsoftu pro ukázkový podrobný plán Cílová zóna migrace

Ukázkový podrobný plán Cílová zóna migrace architektury přechodu na cloud pro Azure od Microsoftu (CAF) nasadí sadu prostředků infrastruktury, které vám pomůžou nastavit migraci vaší první úlohy a spravovat cloudová aktiva v souladu s CAF.

Ukázkový podrobný plán [Základy CAF](../caf-foundation/index.md) na tento podrobný plán navazuje a rozšiřuje ho.

## <a name="architecture"></a>Architektura

Ukázkový podrobný plán Cílová zóna migrace CAF nasadí do Azure základní prostředky infrastruktury, které organizace můžou využít k přípravě svých předplatných pro migraci virtuálních počítačů. Pomáhá také zavést ovládací prvky zásad správného řízení, které jsou nezbytné pro správu jejich cloudových aktiv. Tato ukázka nasadí a vynutí prostředky, zásady a šablony, které organizacím umožní, aby s Azure mohli bez obav začít.

:::image type="complex" source="../../media/caf-blueprints/caf-migration-landing-zone-architecture.png" alt-text="Cílová zóna migrace CAF: Obrázek popisuje, co se nainstaluje v rámci pokynů CAF pro počáteční cílovou zónu." border="false":::
   Popisuje architekturu Azure, která se zajistí nasazením podrobného plánu Migrace CAF.  Dá se použít pro předplatné se skupinami prostředků, jehož součástí je virtuální síť Azure, účet úložiště pro ukládání protokolů a analytiky protokolů nakonfigurované pro uložení v účtu úložiště.  Ukazuje také nakonfigurovanou službu Azure Key Vault a vytvořené počáteční nastavení služby Azure Migrate.  Všechny tyto základní infrastruktury jsou dostupné pomocí Azure Active Directory.     
:::image-end:::

Toto prostředí je tvořené několika službami Azure, které se využívají k zajištění zabezpečených a plně monitorovaných zásad správného řízení na podnikové úrovni. Toto prostředí tvoří:

- Instance služby [Azure Key Vault](../../../../key-vault/general/overview.md), která slouží k hostování tajných kódů používaných pro certifikáty, klíče a tajné kódy nasazené v prostředí sdílených služeb.
- Nasazení služby [Log Analytics](../../../../azure-monitor/overview.md) pro zajištění, že od okamžiku, kdy zahájíte migraci, se všechny akce a služby připojují k centrálnímu umístění.
- Nasazení služby [Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md) poskytující izolovanou síť a podsítě pro váš virtuální počítač.
- Nasazení [projektu Azure Migrate](../../../../migrate/migrate-services-overview.md) pro zjišťování a hodnocení. Přidáváme nástroje pro vyhodnocování serverů, migraci serverů, vyhodnocení databází a migraci databází.  


Všechny tyto prvky dodržují prověřené postupy publikované v článku zaměřeném na [Centrum architektury Azure – referenční architektury](/azure/architecture/reference-architectures/).

> [!NOTE]
> Podrobný plán Migrace CAF vymezuje cílovou zónu pro vaše úlohy. Nad rámec této základní architektury je stále potřeba provést posouzení a migraci vašich virtuálních počítačů a databází.

Další informace najdete v tématu [Architektura přechodu na cloud pro Azure od Microsoftu – migrace](/azure/architecture/cloud-adoption/migrate/).

## <a name="next-steps"></a>Další kroky

Prošli jste si přehled a architekturu ukázkového podrobného plánu Cílová zóna migrace CAF.

> [!div class="nextstepaction"]
> [Podrobný plán Cílová zóna migrace CAF – kroky nasazení](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
