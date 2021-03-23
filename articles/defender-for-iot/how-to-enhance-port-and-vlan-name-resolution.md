---
title: Vylepšení rozlišování názvů sítě VLAN a portů
description: Přizpůsobte názvy portů a sítí VLAN na senzorech k rozšíření rozlišení zařízení.
ms.date: 12/13/2020
ms.topic: how-to
ms.openlocfilehash: de6fbe70d5a5359ad4e4c276642b9b9ed0cef00f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784165"
---
# <a name="enhance-port-vlan-and-os-resolution"></a>Vylepšení portu, sítě VLAN a rozlišení operačního systému

Můžete přizpůsobit porty a názvy sítí VLAN na senzorech a rozšířit tak rozlišení zařízení.

## <a name="customize-port-names"></a>Přizpůsobení názvů portů

Azure Defender pro IoT automaticky přiřazuje názvy k nejobecně rezervovaným portům, jako je například DHCP nebo HTTP. Můžete přizpůsobit názvy portů pro jiné porty, které Defender pro IoT detekuje. Například přiřaďte název nerezervovanému portu, protože tento port zobrazuje neobvykle vysokou aktivitu.

Tyto názvy se zobrazí v následujících případech:

  - **Skupiny zařízení** vyberete na mapě zařízení.

  - Můžete vytvářet pomůcky, které poskytují informace o portech.

### <a name="view-custom-port-names-in-the-device-map"></a>Zobrazit vlastní názvy portů v mapě zařízení

Porty, které obsahují název definovaný uživateli, se zobrazí v mapě zařízení ve skupině **Známé aplikace** .

:::image type="content" source="media/how-to-enrich-asset-information/applications-v2.png" alt-text="Snímek obrazovky s mapou zařízení, která zobrazuje skupinu známých aplikací.":::

### <a name="view-custom-port-names-in-widgets"></a>Zobrazení vlastních názvů portů v widgetech

Názvy portů, které jste definovali, se zobrazí v pomůckách, které pokrývají provoz podle portu.

:::image type="content" source="media/how-to-enrich-asset-information/traffic-v2.png" alt-text="Pokrytí provozu.":::

Definování vlastních názvů portů:

1. Vyberte **nastavení systému** a pak vyberte **standardní aliasy**.

2. Vyberte **Přidat alias portu**.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-aliases.png" alt-text="Přidat alias portu":::

3. Zadejte číslo portu, vyberte **TCP/UDP**, nebo vyberte **obojí** a přidejte název.

4. Vyberte **Uložit**.

## <a name="configure-vlan-names"></a>Konfigurace názvů sítí VLAN

Data inventáře zařízení můžete rozšířit pomocí čísel a značek sítě VLAN. Kromě obohacení dat můžete zobrazit počet zařízení na jednu síť VLAN a zobrazit šířku pásma pomocí widgetů sítě VLAN.

Podpora sítí VLAN vychází z 802.1 q (až po VLAN ID 4094).

K získání informací o síti VLAN jsou k dispozici dvě metody:

- **Automaticky zjištěno**: senzor standardně automaticky zjišťuje sítě VLAN. SÍTĚ VLAN zjištěné s přenosy se zobrazují na obrazovce konfigurace sítě VLAN, v sestavách dolování dat a v jiných sestavách, které obsahují informace o síti VLAN. Nepoužívané sítě VLAN se nezobrazí. Tyto sítě VLAN nemůžete upravit ani odstranit. 

  Do každé sítě VLAN byste měli přidat jedinečný název. Pokud název nepřidáte, zobrazí se číslo sítě VLAN ve všech umístěních, kde je síť VLAN nahlášena.

- **Ručně přidáno**: sítě VLAN můžete přidat ručně. Je nutné přidat jedinečný název každé ručně přidané sítě VLAN a tyto sítě VLAN můžete upravit nebo odstranit.

Názvy sítí VLAN můžou obsahovat až 50 znaků ASCII.

> [!NOTE]
> Názvy sítí VLAN nejsou synchronizovány mezi senzorem a konzolou pro správu. Název je nutné definovat také v konzole pro správu.  
U přepínačů Cisco přidejte následující řádek do konfigurace rozsahu: `monitor session 1 destination interface XX/XX encapsulation dot1q` . V tomto příkazu je *xx/xx* název a číslo portu.

Konfigurace názvů sítí VLAN:

1. V postranní nabídce vyberte **nastavení systému**.

2. V okně **nastavení systému** vyberte **síť VLAN**.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-vlan.png" alt-text="Pomocí nastavení systému upravte své sítě VLAN.":::

3. Vedle každého ID sítě VLAN přidejte jedinečný název.

## <a name="improve-device-operating-system-classification-data-enhancement"></a>Vylepšení klasifikace operačního systému zařízení: vylepšení dat

Senzory průběžně automaticky objevují nová zařízení a také změny dříve zjištěných zařízení, včetně typů operačních systémů.

Za určitých okolností se konflikty můžou zjistit ve zjištěných operačních systémech. K tomu může dojít například v případě, že máte verzi operačního systému, která odkazuje buď na stolní nebo serverové systémy. Pokud k tomu dojde, obdržíte oznámení s nepovinnými klasifikacemi operačních systémů.

:::image type="content" source="media/how-to-enrich-asset-information/enhance-data-screen.png" alt-text="Vylepšete data.":::

Prozkoumejte doporučení, aby bylo možné rozšířit klasifikaci operačního systému. Tato klasifikace se zobrazí v inventáři zařízení, sestavách dolování dat a dalších zobrazeních. Zajištění aktuálnosti těchto informací může zlepšit přesnost výstrah, hrozeb a sestav analýzy rizik.

Přístup k doporučením operačního systému:

1. Vyberte **nastavení systému**.
1. Vyberte **vylepšení dat**.

## <a name="next-steps"></a>Další kroky

Zobrazit rozšířené informace o zařízení v různých sestavách:

- [Prošetření detekovaných senzorů v inventáři zařízení](how-to-investigate-sensor-detections-in-a-device-inventory.md)
- [Trendy senzorů a statistiky sestav](how-to-create-trends-and-statistics-reports.md)
- [Dotazy dolování dat ze senzorů](how-to-create-data-mining-queries.md)
