---
title: Co je Azure FarmBeats
description: Obsahuje přehled Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 6f0a782309edc33a8a5ce661652922494ead2ec0
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667304"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Přehled Azure FarmBeats (Preview)

Azure FarmBeats je nabídka mezi podniky, která je dostupná na Azure Marketplace. Umožňuje agregaci zemědělských datových souborů napříč poskytovateli. Azure FarmBeats umožňuje vytvářet modely umělé inteligence (AI) nebo strojového učení (ML) na základě tavené datové sady. Pomocí Azure FarmBeats se zemědělské firmy mohou soustředit na základní přidanou hodnotu namísto nediferencovaného zvedání dat.

> [!NOTE]
> Azure FarmBeats je momentálně ve verzi Public Preview. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Azure FarmBeats je k dispozici bez smlouvy o úrovni služeb. Pro podporu použijte [fórum Azure FarmBeats.](https://aka.ms/FarmBeatsMSDN )

![Projekt Farm Beats](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

S náhledem Azure FarmBeats můžete:

- Vyhodnoťte stav farmy pomocí indexu vegetace a indexu vody na základě satelitních snímků.
- Získejte doporučení, kolik senzorů vlhkosti půdy použít a kam je umístit.
- Sledujte podmínky farmy vizualizací pozemních dat shromážděných senzory od různých dodavatelů.
- Získejte mapu vlhkosti půdy na základě fúze satelitních a senzorových dat.
- Získejte užitečné přehledy tím, že vytvoříte modely AI/ML nad agregované datové sady.
- Sestavte nebo rozšiřte své digitální zemědělské řešení tím, že poskytnete zdravotní poradenství pro farmy.

## <a name="datahub"></a>Datový hub

Azure FarmBeats Datahub je vrstva rozhraní API, která umožňuje agregaci, normalizaci a kontextovou situaci různých datových sad zemědělství napříč poskytovateli. Azure FarmBeats můžete použít k získání:
- Data ze **senzorů** od dvou poskytovatelů senzorů [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), [Pessl Instruments](https://metos.at/)
- **Satelitní snímky** ze satelitní mise Evropské kosmické agentury [Sentinel-2](https://sentinel.esa.int/web/sentinel/home)
- **Snímky drone** ze tří poskytovatelů snímků drone [senseFly](https://www.sensefly.com/) , [SlantRange](https://slantrange.com/) , [DJI](https://dji.com/)

Datahub je navržen jako rozšiřitelná platforma rozhraní API. Spolupracujeme s mnoha dalšími poskytovateli na integraci s Azure FarmBeats, takže máte větší výběr při vytváření řešení.

## <a name="accelerator"></a>Accelerator

Azure FarmBeats Accelerator je ukázková webová aplikace, která je postavená na Datahubu. Akcelerátor nastartuje vývoj uživatelského rozhraní a modelu. Akcelerátor Azure FarmBeats používá api Azure FarmBeats. Vizualizuje pořizovaná data senzorů jako grafy a výstupy modelů jako mapy. Pomocí akcelerátoru můžete například rychle vytvořit farmu a snadno získat mapu indexu vegetace nebo mapu umístění senzorů pro tuto farmu.

## <a name="role-based-access-control-rbac"></a>Řízení přístupu podle role (RBAC)

Správce můžete definovat pravidla přístupu pro Azure FarmBeats pomocí jedné z předdefinovaných rolí. Role určují, ke kterým oblastem aplikace má uživatel přístup a jaké akce může provádět. Existují dva druhy rolí v Azure FarmBeats – pro uživatele a pro partnery.

### <a name="user-roles"></a>User Roles

[Správce může přidávat a spravovat uživatele](manage-users-in-azure-farmbeats.md) a definovat jejich úrovně přístupu na základě dvou uživatelských rolí: Správce a Jen pro čtení.

### <a name="partner-roles"></a>Role partnerů

Správce může do Azure FarmBeats přidat více partnerů jako zprostředkovatele dat. Následující shrnuje dostupné role partnerů v FarmBeats a jejich oprávnění:

| Typ partnera    |   Akce  | Rozsah |
| ---- | -------- | -------- |
| Partner senzoru  |   Vytvořit, číst, aktualizovat <br/> <br/> Číst, aktualizovat | DeviceModel, Zařízení, SensorModel, Senzor <br/> <br/> Rozšířený typ |
| Partner pro snímky  |   Vytvořit, číst, aktualizovat <br/> <br/> Číst, aktualizovat <br/> <br/> Čtení | Scéna, SceneFile <br/> <br/> Rozšířený typ <br/> <br/> Farmě |
| Partner pro počasí* <br/> <br/>  (* Již brzy) |   Vytvořit, číst, aktualizovat <br/> <br/> Číst, aktualizovat <br/> <br/> Čtení | WeatherDataModel, WeatherDataLocation, JobType <br/> <br/> Rozšířený typ <br/> <br/> Farmě |

## <a name="resources"></a>Prostředky

Azure FarmBeats se nabízí bez dalších poplatků a platíte jenom za prostředky Azure, které používáte. Pomocí níže uvedených zdrojů se můžete dozvědět více o nabídce:

- Na našem [blogu Azure FarmBeats](https://aka.ms/farmbeatsblog)můžete být informováni o nejnovějších novinkách Azure FarmBeats .
- Vyhledejte pomoc zveřejněním dotazu na našem [fóru podpory Azure FarmBeats](https://aka.ms/farmbeatssupport).
- Poskytněte zpětnou vazbu zveřejněním nebo hlasováním o nápadu na funkci na našem [fóru pro zpětnou vazbu Azure FarmBeats](https://aka.ms/farmbeatsfeedback).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Instalace služby Azure FarmBeats](install-azure-farmbeats.md)
