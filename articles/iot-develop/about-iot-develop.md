---
title: Seznámení s vývojem zařízení a aplikací v Azure IoT
description: Naučte se používat Azure IoT k provádění integrovaných vývojových zařízení a vytváření cloudových aplikací s podporou zařízení.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: overview
ms.date: 01/11/2021
ms.openlocfilehash: dd4e53eebe6593db457798f009d3d05ddcbd77b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654891"
---
# <a name="what-is-azure-iot-device-and-application-development"></a>Co je vývoj aplikací a zařízení Azure IoT?

Azure IoT je kolekce služeb spravovaných a platforem, které se připojují, monitorují a ovládají vaše zařízení IoT. Azure IoT nabízí vývojářům komplexní sadu možností. Mezi vaše možnosti patří platformy zařízení, podpora cloudových služeb, sady SDK a nástroje pro vytváření cloudových aplikací s podporou zařízení.

Tento článek popisuje několik klíčových doporučení pro vývojáře, kteří začali používat Azure IoT. Tyto koncepty vás budou orientovat jako vývojář zařízení IoT, a to na možnosti Azure IoT a jak začít. Konkrétně článek přehleduje tyto koncepty:
- [Principy rolí vývoje zařízení](#device-development-roles)
- [Volba hardwaru](#choosing-your-hardware)
- [Výběr sady SDK](#choosing-an-sdk)
- [Výběr možností připojení](#selecting-connection-options)

## <a name="device-development-roles"></a>Role vývoje zařízení
Tento článek pojednává o dvou běžných rolích, které můžete sledovat mezi vývojáři zařízení. Jak je zde použito, role je kolekce souvisejících vývojářských úloh. Je užitečné pochopit, na jaký typ role vývoje aktuálně pracujete. Vaše role má vliv na mnoho možností vývoje, které provedete.

* **Vývoj aplikací pro zařízení:** Se zarovnává s moderními postupy pro vývoj, zaměřuje se na mnohé jazyky s vyšším pořadím a provádí se v běžném operačním systému, jako je Windows nebo Linux.

* **Vývoj integrovaných zařízení:** Popisuje vývoj zaměřený na zařízení s omezeným použitím prostředků. Omezené zařízení se často použije ke snížení nákladů na jednotku, spotřeby energie nebo velikosti zařízení. Tato zařízení mají přímou kontrolu nad hardwarovou platformou, na které se spouštějí.

### <a name="device-application-development"></a>Vývoj aplikací pro zařízení
Vývojáři aplikací pro zařízení přizpůsobují existující zařízení pro připojení ke cloudu a jejich integraci do svých řešení IoT. Tato zařízení můžou podporovat jazyky s vyšším pořadím, jako je C# nebo Python, a často podporují robustní operační systémy pro obecné účely, jako je Windows nebo Linux. Mezi běžná cílová zařízení patří počítače, kontejnery, Malinová Pisa a mobilní zařízení. 

Místo vývoje omezených zařízení se tito vývojáři zaměřují na povolení konkrétního scénáře IoT vyžadovaného jejich cloudovým řešením. Někteří tito vývojáři budou také fungovat na omezených zařízeních pro své cloudové řešení. Pro vývojáře, kteří pracují s omezenými zařízeními, najdete níže v části cesta pro [vývoj integrovaných zařízení](#embedded-device-development) .

> [!TIP]
> Pokud chcete začít, podívejte se na sady [SDK neomezená zařízení](about-iot-sdks.md#unconstrained-device-sdks) .

### <a name="embedded-device-development"></a>Vývoj integrovaných zařízení
Vložené cíle vývoje omezila zařízení, která mají omezené množství paměti a zpracování. Omezená zařízení omezují, co je možné dosáhnout v porovnání s tradiční vývojovou platformou.

Vestavěná zařízení obvykle používají operační systém v reálném čase (RTO) nebo vůbec žádný operační systém. Vestavěná zařízení mají plnou kontrolu nad hardwarem z důvodu nedostatku operačního systému pro obecné účely. Tato skutečnost usnadňuje integrovaným zařízením správné možnosti pro systémy v reálném čase.

Aktuální vložené sady SDK cílí na jazyk **C** . Vložené sady SDK buď neposkytují žádný operační systém, ani podporu Azure RTO. Jsou navržené s ohledem na vložené cíle. Mezi faktory týkající se návrhu patří nutnost minimálního množství paměti a návrh, který není přidělující paměť.

Pokud vaše zařízení dokáže spustit obecný operační systém, doporučujeme vám postupovat podle cesty pro [vývoj aplikací pro zařízení](#device-application-development) . Poskytuje bohatou sadu možností vývoje.

> [!TIP]
> Pokud chcete začít, podívejte se na sady [SDK omezeného zařízení](about-iot-sdks.md#constrained-device-sdks) .

## <a name="choosing-your-hardware"></a>Volba hardwaru
Zařízení Azure IoT jsou základními stavebními bloky řešení IoT a zodpovídá za dodržování a interakci s jejich prostředími. Existuje mnoho různých typů zařízení IoT a je užitečné pochopit druhy zařízení, která existují, a to, jak můžou ovlivnit proces vývoje.

Další informace o rozdílech mezi typy zařízení, která jsou popsaná v tomto článku, najdete v [tématu o typech zařízení IoT](concepts-iot-device-types.md).

## <a name="choosing-an-sdk"></a>Výběr sady SDK
Jako vývojář zařízení Azure IoT máte k dispozici různé sady SDK pro zařízení a sady SDK pro služby Azure, které vám pomůžou vytvářet cloudové aplikace s podporou zařízení. Sady SDK zjednodušují vaše úsilí při vývoji a zjednoduší složitost připojení a správy zařízení. 

Jak je uvedeno v části [role pro vývoj zařízení](#device-development-roles) , existují tři druhy sad IoT SDK pro vývoj zařízení:
- Sady SDK integrovaných zařízení (pro omezená zařízení)
- Sady SDK pro zařízení (pro používání jazyků vyšších objednávek pro připojení stávajících zařízení k aplikacím IoT)
- Sady SDK pro služby (pro vytváření řešení Azure IoT, které připojují zařízení ke službám)

Další informace o výběru zařízení nebo sady SDK služby Azure IoT najdete v tématu [Přehled sad SDK pro zařízení Azure IoT](about-iot-sdks.md).

## <a name="selecting-connection-options"></a>Výběr možností připojení
Důležitým krokem v procesu vývoje je výběr sady možností, které budete používat pro připojení a správu zařízení. Je potřeba vzít v úvahu dvě důležité aspekty:
- Výběr aplikační platformy IoT pro hostování zařízení. V případě Azure IoT to znamená zvolit IoT Hub nebo IoT Central.
- Výběr vývojářských nástrojů vám umožní připojit, spravovat a monitorovat zařízení.

Další informace o výběru platformy a nástrojů aplikace najdete v tématu [Přehled: možnosti připojení pro vývojáře zařízení Azure IoT](concepts-overview-connection-options.md).

## <a name="next-steps"></a>Další kroky
Vyberte jednu z následujících řad pro rychlý Start, která je pro vaši vývojovou roli nejrelevantnější. Tyto články ukazují základy vytváření aplikací Azure IoT pro hostování zařízení, používání sady SDK, připojení zařízení a odesílání telemetrie.  
- Pro vývoj aplikací pro zařízení:  [rychlý Start: odeslání telemetrie ze zařízení do Azure IoT Central](quickstart-send-telemetry-python.md)
- Pro vývoj integrovaných zařízení: [Začínáme s vývojem zařízení Azure IoT Embedded](quickstart-device-development.md)
