---
title: Práce s mapou zařízení senzorů
description: Mapa zařízení poskytuje grafické znázornění zjištěných síťových zařízení. Pomocí mapy můžete analyzovat a spravovat informace o zařízení, síťové řezy a generovat sestavy.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/7/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: edd1438a665e4917d5dd4cdcfba08d9cee01d3bb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100523834"
---
# <a name="investigate-sensor-detections-in-the-device-map"></a>Prozkoumat detekci senzorů v mapě zařízení

Mapa zařízení poskytuje grafické znázornění zjištěných síťových zařízení. Použijte mapování na:

  - Načtěte, analyzujte a spravujte informace o zařízení.

  - Analyzujte síťové řezy, například konkrétní skupiny pro důležité nebo Purdue vrstvy.

  - Generování sestav, například export podrobností a souhrnů zařízení.

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Snímek obrazovky s mapou zařízení":::

Přístup k mapě:

  - Na hlavní obrazovce konzoly vyberte **Mapa zařízení** .

## <a name="map-search-and-layout-tools"></a>Nástroje pro vyhledávání a rozložení mapy

Následující nástroje slouží k práci na mapě.

Vaše role uživatele určuje, které nástroje jsou k dispozici v okně Mapa zařízení. Podrobnosti o rolích uživatelů najdete v tématu věnovaném [Vytvoření a správě uživatelů](how-to-create-and-manage-users.md) .

| Symbol | Description |
|---|---|
| :::image type="icon" source="media/how-to-work-with-maps/search-bar-icon-v2.png" border="false":::| Hledání podle IP adresy nebo adresy MAC pro konkrétní zařízení. Do textového pole zadejte adresu IP nebo adresu MAC. Mapa zobrazí zařízení, které jste hledali pomocí zařízení, která jsou k němu připojená. |
| Zvýraznění skupin a filtry <br /> :::image type="content" source="media/how-to-work-with-maps/group-highlight-and-filters-v2.png" alt-text="Snímek obrazovky s zvýrazněním a filtry skupiny"::: | Filtrovat nebo zvýraznit mapu na základě výchozích a vlastních skupin zařízení. |
| :::image type="icon" source="media/how-to-work-with-maps/collapse-view-icon.png" border="false"::: | Sbalí zobrazení, umožní povolit zobrazení s fokusem na zařízeních a seskupovat zařízení IT.  |
| :::image type="icon" source="media/how-to-work-with-maps/device-management-icon.png" border="false"::: | Udržujte aktuální uspořádání zařízení v mapě. Například pokud přetáhnete zařízení do nových umístění na mapě, zařízení zůstanou v těchto umístěních, když se ukončí mapa. |
| :::image type="icon" source="media/how-to-work-with-maps/fit-to-screen-icon.png" border="false"::: | Přizpůsobit na obrazovku |
| :::image type="icon" source="media/how-to-work-with-maps/layer-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/layouts-icon-v2.png" border="false"::: | -Zobrazit vrstvu Purdue identifikovanou pro toto zařízení, včetně automatického řízení procesů, dohledu a podniku <br /> -Zobrazení připojení mezi zařízeními.|
| :::image type="icon" source="media/how-to-work-with-maps/broadcast-icon.png" border="false"::: | Zobrazit nebo skrýt mezi všesměrovým vysíláním a vícesměrovým vysíláním |
| :::image type="icon" source="media/how-to-work-with-maps/time-icon.png" border="false"::: | Vyfiltrujte zařízení na mapě podle doby poslední komunikace s ostatními zařízeními. |
| :::image type="icon" source="media/how-to-work-with-maps/notifications-icon.png" alt-text="připomenutí" border="false"::: | Zobrazit oznámení o zařízení. Například pokud byla zjištěna nová IP adresa pro zařízení pomocí existující adresy MAC |
| :::image type="icon" source="media/how-to-work-with-maps/export-import.png" alt-text="Export" border="false"::: | Exportujte/importujte informace o zařízení. |
| :::image type="icon" source="media/how-to-work-with-maps/properties-icon.png" alt-text="vlastnosti" border="false"::: | Zobrazí základní vlastnosti zařízení pro vybraná zařízení. |
| :::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" alt-text="Přiblížení" border="false"::: nebo :::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png" alt-text="oddálení" border="false"::: | Přiblížení nebo oddálení zařízení v mapě. |

## <a name="view-ot-elements-only"></a>Zobrazit pouze elementy OT

Ve výchozím nastavení se zařízení automaticky agreguje podle podsítě, takže zobrazení mapy je zaměřeno na sítě v síti a ICS. Prezentace prvků IT sítě je sbalena na minimum, což snižuje celkový počet zařízení prezentovaných na mapě a poskytuje jasný přehled o síťových prvcích OT a ICS.

Každá podsíť je prezentována jako jediná entita na mapě zařízení, včetně interaktivní sbalení a rozšiřování možností, jak se podívat na podrobnosti o podsíti IT a zpátky.

Následující obrázek ukazuje sbalenou podsíť IT s 27 prvky IT sítě.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="sbalená podsíť IT s 27 prvky IT sítě":::

Povolení možnosti sbalení sítí IT:

- V okně **nastavení systému** se ujistěte, že je povolená možnost seskupování sítí IT.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="Okno nastavení systému":::

Rozšíření podsítě IT:

1. Chcete-li rozlišovat mezi IT a sítěmi sítě, vyberte z obrazovky nastavení systému možnost **podsítě**.

   > [!NOTE]
   > Doporučujeme pojmenovat každou podsíť pomocí smysluplných názvů, které uživatel může snadno identifikovat, aby bylo možné rozlišovat mezi nimi a sítěmi.

   :::image type="content" source="media/how-to-work-with-maps/subnet-list.png" alt-text="Konfigurace podsítí":::

2. V okně **Upravit konfiguraci podsítí** zrušte zaškrtnutí políčka **podsíť ICS** pro každou podsíť, kterou chcete definovat jako podsíť IT. Podsítě IT se v mapě zařízení zobrazí jako sbalená s oznámeními pro zařízení ICS, jako je například kontroler nebo PLC v IT sítích.

   :::image type="content" source="media/how-to-work-with-maps/edit-config.png" alt-text="Upravit konfiguraci podsítí":::

3. Pokud chcete síť IT na mapě rozšířit, klikněte na ni pravým tlačítkem myši a vyberte **rozbalte síť**.

   :::image type="content" source="media/how-to-work-with-maps/expand-network.png" alt-text="Rozbalíte zobrazení vaší sítě.":::

4. Zobrazí se potvrzovací okno s informacemi o tom, že změnu rozložení nelze znovu provést.

5. Vyberte **OK**. Prvky podsítě IT se zobrazí na mapě.

   :::image type="content" source="media/how-to-work-with-maps/fixed-map.png" alt-text="OK":::

Sbalení podsítě IT:

1.  V levém podokně vyberte **zařízení**.

2. V okně zařízení vyberte ikonu Sbalit. Číslo v červené barvě označuje, kolik rozbalených podsítí IT se aktuálně zobrazuje na mapě.

   :::image type="content" source="media/how-to-work-with-maps/devices-notifications.png" alt-text="Okno zařízení":::

3. Vyberte podsítě, které chcete sbalit, nebo vyberte **Sbalit vše**. Vybraná podsíť se na mapě zobrazí jako sbalená.

   :::image type="content" source="media/how-to-work-with-maps/close-all-subnets.png" alt-text="Sbalit vše":::

Ikona sbalení je aktualizována aktualizovaným počtem rozbalených podsítí IT.

## <a name="view-or-highlight-device-groups"></a>Zobrazení nebo zvýraznění skupin zařízení

Zobrazení mapy můžete přizpůsobit na základě skupin zařízení. Například skupiny zařízení přidružené k určitému protokolu typu, sítě VLAN nebo podsíti. K dispozici jsou předdefinované skupiny a je možné vytvořit vlastní skupiny.

Zobrazit skupiny podle:

  - **Zvýrazňování:** Zvýrazněte zařízení, která patří do určité skupiny modře.

  - **Filtrování:** Zobrazí jenom zařízení, která patří do konkrétní skupiny.

:::image type="content" source="media/how-to-work-with-maps/port-standard.png" alt-text="Standardní zobrazení portu":::

K dispozici jsou následující předdefinované skupiny:

| Název skupiny | Description |
|--|--|
| **Známé aplikace** | Zařízení, která používají rezervované porty, jako je například TCP.  |
| **porty, které nejsou standardní (výchozí)** | Zařízení, která používají nestandardní porty nebo porty, kterým nebyl přiřazen alias. |
| **Protokoly OT (výchozí)** | Zařízení, která zpracovávají známá provozu. |
| **Autorizace (výchozí)** | Zařízení zjištěná v síti během procesu učení nebo byla oficiálně schválena v síti. |
| **Filtry inventáře zařízení** | Zařízení seskupená podle filtrů se ukládají do tabulky inventáře zařízení. |
| **Intervaly dotazování** | Zařízení seskupená podle intervalů cyklického dotazování. Intervaly dotazování se generují automaticky podle cyklických kanálů neboli teček. Například 15,0 sekund, 3,0 sekund, 1,5 sekund nebo libovolný interval. Tyto informace vám pomůžou zjistit, jestli se systémy dotazují příliš rychle nebo pomalu. |
| **Plánování** | Technické stanice a programovací počítače. |
| **Podsítě** | Zařízení, která patří do určité podsítě. |
| **REŽIM** | Zařízení přidružená k určitému ID sítě VLAN |
| **Připojení mezi podsítěmi** | Zařízení, která komunikují z jedné podsítě do jiné podsítě. |
| **Připnuté výstrahy** | Zařízení, ke kterým uživatel připnul výstrahu. |
| **Simulace vektoru útoku** | V sestavách vektoru útoku byly zjištěna zranitelná zařízení. Pokud chcete zobrazit tato zařízení na mapě, při generování vektoru útoku zaškrtněte políčko **Zobrazit v mapě zařízení** . :::image type="content" source="media/how-to-work-with-maps/add-attack-v2.png" alt-text="Přidejte simulace vektoru útoku":::. |
| **Poslední výskyt** | Zařízení seskupená podle časového rámce, které byly naposledy viděli, například: jedna hodina, šest hodin, jeden den, 7 dní. |
| **Není ve službě Active Directory** | Všechna zařízení, která nejsou sami PLC, která nekomunikují se službou Active Directory. |

Zvýraznění nebo filtrování zařízení:

1. V postranní nabídce vyberte **Mapa zařízení** .

2. Vyberte ikonu filtru. :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="Nabídka":::

3. V podokně skupiny vyberte skupinu, do které chcete zvýraznit nebo filtrovat zařízení.

4. Vyberte možnost **Zvýraznit** nebo **filtrovat**. Přepněte stejný výběr pro odebrání zvýraznění nebo filtrování.

## <a name="define-custom-groups"></a>Definovat vlastní skupiny

Kromě zobrazení předdefinovaných skupin můžete definovat vlastní skupiny. Skupiny se zobrazí v sestavách mapa zařízení, inventář zařízení a dolování dat.

> [!NOTE]
> Můžete také vytvořit skupiny z inventáře zařízení.

Vytvoření skupiny:

1. V postranní nabídce vyberte **zařízení** . Zobrazí se mapa zařízení.

1. Vyberte :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="Nastavení skupiny"::: , aby se zobrazila nastavení skupin.

1. Vyberte :::image type="content" source="media/how-to-work-with-maps/create-group-v2.png" alt-text="skupiny"::: , chcete-li vytvořit novou vlastní skupinu.

:::image type="content" source="media/how-to-work-with-maps/custom-group-v2.png" alt-text="Vytvoření obrazovky s vlastní skupinou":::

1. Přidejte název skupiny, použijte až 30 znaků.

1. Vyberte relevantní zařízení, a to následujícím způsobem:

   - Přidejte zařízení z této nabídky tak, že je vyberete ze seznamu (vyberte na tlačítko se šipkou).<br /> Nebo: <br /> 
   - Do této nabídky přidejte zařízení tak, že je zkopírujete z vybrané skupiny (vyberte tlačítko se šipkou).

1. Vyberte **Přidat skupinu** a přidejte do vlastních skupin existující skupiny.

### <a name="add-devices-to-a-custom-group"></a>Přidání zařízení do vlastní skupiny

Zařízení můžete přidat do vlastní skupiny nebo vytvořit novou vlastní skupinu a zařízení.

1. Pravým tlačítkem myši klikněte na zařízení na mapě.

1. Vyberte **Přidat do skupiny**.

1. Do pole Skupina zadejte název skupiny a vyberte +. Zobrazí se nová skupina. Pokud již skupina existuje, bude přidána do existující vlastní skupiny.

   :::image type="content" source="media/how-to-work-with-maps/groups-section-v2.png" alt-text="Název skupiny":::

1. Do skupiny přidejte zařízení opakováním kroků 1-3.

## <a name="map-zoom-views"></a>Zobrazení přiblížení mapy

Práce s zobrazeními map vám pomůžou urychlit forenzní při analýze velkých sítí.

Můžou se zobrazit tři zobrazení podrobností o zařízení:

  - [Pohled z ptačí perspektivy](#birds-eye-view)

  - [Typ zařízení a zobrazení připojení](#device-type-and-connection-view)

  - [Podrobné zobrazení](#detailed-view)

### <a name="birds-eye-view"></a>Pohled z ptačí perspektivy

Toto zobrazení poskytuje podrobné zobrazení zařízení, která představují následující:

  - Červené tečky indikují zařízení s výstrahami

  - Tečky označených hvězdičkou označují zařízení označená jako důležitá.

  - Černé tečky označují zařízení bez výstrah.

:::image type="content" source="media/how-to-work-with-maps/colored-dots-v2.png" alt-text="Zobrazení ptačí perspektivy":::

### <a name="device-type-and-connection-view"></a>Typ zařízení a zobrazení připojení 

Toto zobrazení prezentuje zařízení reprezentovaná jako ikony na mapě, aby bylo možné zvýrazňovat zařízení s výstrahami, typy zařízení a připojenými zařízeními.

  - Zařízení s výstrahami se zobrazují s červeným kroužkem.

  - Zařízení bez výstrahy se zobrazují s šedým kroužkem.

  - Zařízení zobrazená jako hvězdička byla označena jako důležitá.

Ikona typ zařízení se zobrazuje u připojených zařízení.

:::image type="content" source="media/how-to-work-with-maps/colored-rings.png" alt-text="zobrazení připojení":::

### <a name="detailed-view"></a>Podrobné zobrazení 

Podrobné zobrazení prezentuje zařízení a popisky a indikátory zařízení s následujícími informacemi:

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Podrobné zobrazení":::

### <a name="control-the-zoom-view"></a>Řízení zobrazení lupy

Zobrazené zobrazení mapy závisí na úrovni přiblížení mapy. Přepínání mezi zobrazeními map se provádí změnou úrovní přiblížení.

:::image type="content" source="media/how-to-work-with-maps/zoom-in-out.png" alt-text="Řízení zobrazení lupy":::

### <a name="enable-simplified-zoom-views"></a>Povolit zjednodušená zobrazení lupy

Správci, kteří chtějí analytikům zabezpečení a uživatelům typu RO přístup k pohledům na oči a zařízení a připojení typu, by měli povolit možnost zjednodušené zobrazení.

Chcete-li povolit zjednodušená zobrazení mapy:

  - Vyberte **nastavení systému** a pak přepněte možnost **zjednodušené zobrazení mapy** .

:::image type="content" source="media/how-to-work-with-maps/simplify-view-v2.png" alt-text="Jednodušší zobrazení mapy":::

## <a name="learn-more-about-devices"></a>Další informace o zařízeních

K dispozici je rozsáhlá škála nástrojů pro další informace o zařízeních tvoří mapu zařízení:

- [Popisky a indikátory zařízení](#device-labels-and-indicators)

- [Rychlá zobrazení zařízení](#device-quick-views)

- [Zobrazení a Správa vlastností zařízení](#view-and-manage-device-properties)

- [Zobrazit typy zařízení](#view-device-types)

- [Propojovacího rozhraní](#backplane-properties)

- [Zobrazit časovou osu událostí pro zařízení](#view-a-timeline-of-events-for-the-device)

- [Analyzovat podrobnosti a změny programování](#analyze-programming-details-and-changes)

### <a name="device-labels-and-indicators"></a>Popisky a indikátory zařízení

Na zařízeních na mapě se můžou zobrazit tyto popisky a indikátory:

| Popisek zařízení | Description |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/host-v2.png" alt-text="Název hostitele IP"::: | Název hostitele IP adresy a IP adresa nebo adresy podsítí |
| :::image type="content" source="media/how-to-work-with-maps/amount-alerts-v2.png" alt-text="Počet výstrah"::: | Počet výstrah přidružených k zařízení |
| :::image type="icon" source="media/how-to-work-with-maps/type-v2.png" border="false"::: | Ikona typu zařízení, například úložiště, PLC nebo historian. |
| :::image type="content" source="media/how-to-work-with-maps/grouped-v2.png" alt-text="seskupená zařízení"::: | Počet zařízení seskupených v podsíti v síti IT. V tomto příkladu 8. |
| :::image type="content" source="media/how-to-work-with-maps/not-authorized-v2.png" alt-text="období učení zařízení"::: | Zařízení, které bylo zjištěno po období učení a nebylo autorizováno jako síťové zařízení. |
| Plná čára | Logické připojení mezi zařízeními |
| :::image type="content" source="media/how-to-work-with-maps/new-v2.png" alt-text="Nové zařízení"::: | Nové zařízení zjištěné po dokončení učení. |

### <a name="device-quick-views"></a>Rychlá zobrazení zařízení

Přístup k vlastnostem zařízení a připojením z mapy.

Otevřete nabídku rychlé vlastnosti:

  - V nabídce :::image type="content" source="media/how-to-work-with-maps/properties.png" alt-text="rychlá vlastnost vyberte":::nabídku rychlé vlastnosti.

#### <a name="quick-device-properties"></a>Vlastnosti rychlého zařízení

Vyberte zařízení nebo více zařízení, když je otevřená obrazovka rychlé vlastnosti, aby se zobrazila světla těchto zařízení:

:::image type="content" source="media/how-to-work-with-maps/device-information.png" alt-text="Vlastnosti rychlého zařízení":::

#### <a name="quick-connection-properties"></a>Vlastnosti rychlého připojení

Vyberte připojení, když je otevřená obrazovka rychlé vlastnosti, aby se zobrazily protokoly, které se používají v tomto připojení a kdy se naposledy zobrazily:

:::image type="content" source="media/how-to-work-with-maps/connection-details-v2.png" alt-text="Vlastnosti rychlého připojení":::

## <a name="view-and-manage-device-properties"></a>Zobrazení a Správa vlastností zařízení

Správců zařízení můžete zobrazit pro každé zařízení zobrazené na mapě. Například název zařízení, typ nebo operační systém nebo firmware nebo dodavatel.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Zobrazení a Správa vlastností zařízení":::

Následující informace lze aktualizovat ručně. Informace, které jsou zadány ručně, přepíšou informace zjištěné programem Defender pro IoT.

  - Název

  - Typ

  - Operační systém

  - Purdue vrstva

  - Description

| Položka | Popis |
|--|--|
| Základní informace | Základní informace, které jsou potřeba. |
| Name | Název zařízení <br /> Ve výchozím nastavení senzor zjistí název zařízení, jak je definován v síti. Například název definovaný na serveru DNS. <br /> Pokud se žádné takové názvy nedefinovaly, zobrazí se v tomto poli IP adresa zařízení. <br /> Název zařízení můžete změnit ručně. Poskytněte svým zařízením smysluplné názvy, které odpovídají jejich funkcím. |
| Typ | Typ zařízení zjištěný senzorem. <br /> Další informace najdete v tématu [zobrazení typů zařízení](#view-device-types). |
| Dodavatel | Dodavatel zařízení. To je určeno úvodními znaky adresy MAC zařízení. Toto pole je jen ke čtení. |
| Operační systém | SYSTÉM zařízení zjistil senzor. |
| Purdue vrstva | Purdue vrstva určená senzorem pro toto zařízení, včetně: <br /> – Automaticky <br /> – Řízení procesů <br /> – Dohlížecí <br /> – Enterprise |
| Description | Textové pole zdarma <br /> Přidejte další informace o zařízení. |
| Atributy | Jakékoli další informace, které o zařízení byly zjištěny během období učení a nepatří do jiných kategorií, se zobrazí v části atributy. <br /> Informace jsou typu RO. |
| Nastavení | Můžete ručně změnit nastavení zařízení, aby nedocházelo k falešně pozitivním hodnotám: <br /> - **Autorizované zařízení**: během období učení se všechna zařízení zjištěná v síti identifikují jako Autorizovaná zařízení. Jakmile se zařízení zjistí po období učení, ve výchozím nastavení se zobrazí jako neoprávněné zařízení. Tuto definici můžete změnit ručně. <br /> - **Známé jako skener**: tuto možnost povolte, pokud víte, že se jedná o zařízení se označuje jako skener a že se k němu nemusíte upozorňovat. <br /> - **Programové zařízení**: tuto možnost povolte, pokud víte, že toto zařízení je známé jako programové zařízení a slouží k provádění změn v programování. Rozpoznání jako programovací zařízení zabrání upozorněním na změny programování pocházející z tohoto prostředku. |
| Vlastní skupiny | Vlastní skupiny v mapě zařízení, ve kterých se toto zařízení účastní |
| Stav | Stav zabezpečení a autorizace zařízení: <br /> – Stav není, `Secured` Pokud nejsou k dispozici žádná upozornění. <br /> – Pokud se zobrazí výstrahy týkající se zařízení, zobrazí se počet výstrah. <br /> – Stav `Unauthorized` se zobrazí pro zařízení, která se přidala do sítě po období učení. Zařízení můžete ručně definovat jako `Authorized Device` v nastavení. <br /> – Pro případ, že je adresa tohoto zařízení definovaná jako Dynamická adresa, `DHCP` přidá se do stavu. |


| Síť | Description |
|--|--|
| Rozhraní | Rozhraní zařízení. Pole typu RO. |
| Protokoly | Protokoly používané zařízením Pole typu RO. |
| Firmware | Pokud jsou k dispozici informace o replánech, informace o firmwaru nebudou zobrazeny. |
| Adresa | IP adresa zařízení. |
| Sér | Sériové číslo zařízení. |
| Adresa modulu | Model zařízení a číslo slotu nebo ID. |
| Modelování | Číslo modelu zařízení. |
| Verze firmwaru | Číslo verze firmwaru. |

Postup zobrazení informací o zařízení:

1. V postranní nabídce vyberte **zařízení** .

2. Klikněte pravým tlačítkem na zařízení a vyberte **Zobrazit vlastnosti**. Zobrazí se okno Vlastnosti zařízení.

3. V dolní části okna vyberte požadovaná výstraha, kde najdete podrobné informace o výstrahách pro toto zařízení.

### <a name="view-device-types"></a>Zobrazit typy zařízení

Typ zařízení se senzorem automaticky identifikuje během procesu zjišťování zařízení. Typ můžete změnit ručně.

:::image type="content" source="media/how-to-work-with-maps/type-of-device.png" alt-text="Zobrazit typy zařízení":::

Následující tabulka uvádí všechny typy v systému:

| Kategorie | Typ zařízení |
|--|--|
| HOSTITELSKÝ | Technická stanice <br /> PLC <br />Historian <br />HMI <br />IED <br />Řadič DCS <br />RTU <br />Systém pro průmyslové balení <br />Průmyslový rozsah <br />Průmyslový robot <br />Slot <br />Měřič <br />Proměnná hustota disku  <br />Kontroler robota <br />Servo jednotka <br />Pneumatické zařízení <br />Marquee |
| IT | Řadič domény <br />Server DB <br />Pracovní stanice <br />Server <br />Terminálová stanice <br />Storage <br />Inteligentní telefon <br />Tablet <br />Záložní server |
| IoT | Kamera IP <br />Tiskárna  <br />Děrovací hodiny <br />ATM <br />Inteligentní TV <br />Herní konzola <br />HRY <br />Ovládací panel dveří <br />VENTIL <br />Termostat <br />Požární alarm <br />Inteligentní světlo <br />Inteligentní přepínač <br />Detektor požáru <br />IP telefon <br />Výstražný systém <br />Alarm Siren <br />Detektor pohybu <br />Hodnocení <br />Senzor vlhkosti <br />Skener čárových kódů <br />Nepřerušitelný zdroj napájení <br />Systém čítače lidí <br />Intercom <br />Turnstile |
| Síť | Bezdrátový přístupový bod <br />Směrovač <br />Přepínač <br />Brána firewall <br />VPN Gateway <br />Server NTP <br />WiFi – ananas <br />Fyzické umístění <br />Vstupně-výstupní adaptér <br /> Převaděč protokolu |

Postup zobrazení informací o zařízení:

1.  V postranní nabídce vyberte **zařízení** .

2. Klikněte pravým tlačítkem na zařízení a vyberte **Zobrazit vlastnosti**. Zobrazí se okno Vlastnosti zařízení.

3. Pokud chcete zobrazit podrobné informace o výstrahách pro toto zařízení, vyberte na požadované výstraze.

### <a name="backplane-properties"></a>Vlastnosti pro replánování

Pokud PLC obsahuje více modulů oddělených na racky a sloty, můžou se tyto vlastnosti lišit mezi kartami modulů. Pokud je třeba IP adresa a adresa MAC stejné, firmware se může lišit.

Možnost Možnosti nového plánu můžete použít ke kontrole několika řadičů/karet a jejich vnořených zařízení jako jedné entity s nejrůznějšími definicemi. Každá patice v zobrazení pro naplánování představuje základní zařízení – zařízení, která byla za něj zjištěna.

:::image type="content" source="media/how-to-work-with-maps/backplane-image-v2.png" alt-text="Vlastnosti pro replánování":::

:::image type="content" source="media/how-to-work-with-maps/backplane-details-v2.png" alt-text="Naplánování vlastností zařízení":::

Replánování může obsahovat až 30 karet Controller a až 30 jednotek stojanu. Celkový počet zařízení zahrnutých do několika úrovní může být až 200 zařízení.

Podokno replánování se zobrazuje v okno Vlastnosti zařízení při zjištění podrobností o plánu.

Každý slot se zobrazí s počtem základních zařízení a ikonou, která zobrazuje typ modulu.

| Ikona | Typ modulu |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/power.png" alt-text="Zdroj napájení"::: | Zdroj napájení |
| :::image type="content" source="media/how-to-work-with-maps/analog.png" alt-text="Analogové vstupně-výstupní operace"::: | Analogové vstupně-výstupní operace |
| :::image type="content" source="media/how-to-work-with-maps/comms.png" alt-text="Komunikační adaptér"::: | Komunikační adaptér |
| :::image type="content" source="media/how-to-work-with-maps/digital.png" alt-text="Digitální I/O"::: | Digitální I/O |
| :::image type="content" source="media/how-to-work-with-maps/computer-processor.png" alt-text="Procesor"::: | Procesor |
| :::image type="content" source="media/how-to-work-with-maps/HMI-icon.png" alt-text="HMI"::: | HMI |
| :::image type="content" source="media/how-to-work-with-maps/average.png" alt-text="Obecné"::: | Obecné |

Po výběru slotu se zobrazí podrobnosti o patici:

:::image type="content" source="media/how-to-work-with-maps/slot-selection-v2.png" alt-text="vybrat slot":::

Pokud chcete zobrazit základní zařízení za slotem, vyberte **Zobrazit na mapě**. Slot se zobrazí v mapě zařízení se všemi podkladovým modulem a zařízeními, která jsou k němu připojená.

:::image type="content" source="media/how-to-work-with-maps/map-appearance-v2.png" alt-text="ZOBRAZIT NA MAPĚ":::

## <a name="view-a-timeline-of-events-for-the-device"></a>Zobrazit časovou osu událostí pro zařízení

Zobrazit časovou osu událostí přidružených k zařízení.

Zobrazení časové osy:

1. Pravým tlačítkem myši klikněte na zařízení z mapy.

2. Vyberte **Zobrazit události**. Otevře se okno Časová osa události s informacemi o událostech zjištěných pro vybrané zařízení.

Podrobnosti najdete v části [Časová osa události](#event-timeline) .

## <a name="analyze-programming-details-and-changes"></a>Analyzovat podrobnosti a změny programování

Vylepšete forenzní zobrazením událostí programování prováděných v síťových zařízeních a analýzou změn kódu. Tyto informace vám pomohou odhalit podezřelé programové aktivity, například:

  - Lidská chyba: inženýr naprogramuje špatné zařízení.

  - Automatizace programování je poškozena: programování je nesprávně provedeno z důvodu chyby automatizace.

  - Napadené systémy: neautorizovaných uživatelů přihlášených k programovacímu zařízení.

Můžete zobrazit programové zařízení a procházejte různými změnami programování, které na něm provedla jiná zařízení.

Zobrazení kódu, který byl přidán, změněn, odebrán nebo znovu načten programovacím zařízením. Vyhledejte programové změny na základě typů souborů, dat nebo časů zájmu.

### <a name="when-to-review-programming-activity"></a>Kdy zkontrolovat aktivitu programování 

Možná budete muset zkontrolovat aktivitu programování:

  - Po zobrazení výstrahy týkající se neautorizovaného programování

  - Po plánované aktualizaci řadičů

  - Pokud proces nebo počítač nepracuje správně (Pokud chcete zjistit, kdo provedl poslední aktualizaci a kdy)

:::image type="content" source="media/how-to-work-with-maps/differences.png" alt-text="Programový protokol změn":::

Další možnosti vám umožní:

  - Označte události, které mají zájem, s hvězdičkou.

  - Stáhněte soubor *. txt s aktuálním kódem.

### <a name="about-authorized-vs-unauthorized-programming-events"></a>O událostech autorizovaných a neautorizovaných programování 

Neoprávněné programové události jsou prováděny zařízeními, která se nedozvěděla nebo ručně definovala jako programování zařízení. Autorizované události programování se provádějí pomocí zařízení, která byla vyřešena nebo manuálně definována jako programová zařízení.

Okno Analýza programování zobrazuje jak autorizované, tak neautorizované události programování.

### <a name="accessing-programming-details-and-changes"></a>Přístup k podrobnostem o programování a změnám

Přístup k oknu analýza programování z:

- [Časová osa události](#event-timeline)

- [Neautorizované výstrahy programování](#unauthorized-programming-alerts)

### <a name="event-timeline"></a>Časová osa události

Pomocí časové osy události můžete zobrazit časovou osu událostí, ve kterých byly zjištěny změny v programování.

:::image type="content" source="media/how-to-work-with-maps/timeline.png" alt-text="Zobrazení časové osy události":::

### <a name="unauthorized-programming-alerts"></a>Neautorizované výstrahy programování

Výstrahy se aktivují, když Neautorizovaná programovací zařízení provádějí aktivity programování.

:::image type="content" source="media/how-to-work-with-maps/unauthorized.png" alt-text="Neautorizované výstrahy programování":::

> [!NOTE]
> Můžete si také prohlédnout základní informace o programování v okno Vlastnosti zařízení a inventáři zařízení.

### <a name="working-in-the-programming-timeline-window"></a>Práce v okně programovací časová osa

Tato část popisuje, jak zobrazit programové soubory a porovnat verze. Vyhledá konkrétní soubory odeslané do naprogramovaného zařízení. Hledat soubory na základě:

  - Date (Datum)

  - Typ souboru

:::image type="content" source="media/how-to-work-with-maps/timeline-view.png" alt-text="okno programovací časová osa":::

|Typ programovací časové osy | Description |
|--|--|
| Naprogramované zařízení | Obsahuje podrobné informace o zařízení, které bylo naprogramováno, včetně názvu hostitele a souboru. |
| Nedávné události | 50 zobrazí nejnovější události, které senzor zjistil. <br />Pokud chcete událost zvýraznit, najeďte na ni myší a klikněte na starou. :::image type="icon" source="media/how-to-work-with-maps/star.png" border="false"::: <br /> Můžete zobrazit poslední 50 události. |
| Soubory | Zobrazí soubory zjištěné pro zvolené datum a velikost souboru v naprogramovaném zařízení. <br /> Ve výchozím nastavení je maximální počet souborů, které jsou k dispozici pro zobrazení na zařízení, 300. <br /> Ve výchozím nastavení je maximální velikost souboru pro každý soubor 15 MB. |
| Stav souboru :::image type="icon" source="media/how-to-work-with-maps/status-v2.png" border="false"::: | Jmenovky souborů udávají stav souboru v zařízení, včetně: <br /> **Přidáno**: soubor se přidal do koncového bodu pro vybrané datum nebo čas. <br /> **Aktualizováno**: soubor se aktualizoval podle vybraného data nebo času. <br /> **Odstraněno**: Tento soubor se odebral. <br /> **Bez popisku**: soubor nebyl změněn.   |
| Programovací zařízení | Zařízení, které provedlo programové změny. V jednom programovém zařízení bylo možné provádět změny v programování více zařízení. Zobrazí se název hostitele, datum nebo čas změny a přihlášeného uživatele. |
| :::image type="icon" source="media/how-to-work-with-maps/current.png" border="false"::: | Zobrazí aktuální soubor nainstalovaný v naprogramovaném zařízení. |
| :::image type="icon" source="media/how-to-work-with-maps/download-text.png" border="false"::: | Stáhněte si textový soubor zobrazeného kódu. |
| :::image type="icon" source="media/how-to-work-with-maps/compare.png" border="false"::: | Porovná aktuální soubor se souborem zjištěným ve vybraném datu. |

### <a name="choose-a-file-to-review"></a>Vyberte soubor, který chcete zkontrolovat.

Tato část popisuje, jak zvolit soubor, který se má zkontrolovat.

Chcete-li zvolit soubor, který chcete zkontrolovat:

1. Vybrat událost z podokna **nedávné události**

2. Vyberte souborový formulář v podokně soubor. Soubor se zobrazí v aktuálním podokně.

:::image type="content" source="media/how-to-work-with-maps/choose-file.png" alt-text="Vyberte soubor, se kterým chcete pracovat.":::

### <a name="compare-files"></a>Porovnat soubory

Tato část popisuje, jak porovnat programové soubory.

Pro porovnání:

1. Vyberte událost z podokna nedávné události.

2. V podokně soubor vyberte soubor. Soubor se zobrazí v aktuálním podokně. Tento soubor můžete porovnat s jinými soubory.

3. Vyberte indikátor porovnání.

   :::image type="content" source="media/how-to-work-with-maps/compare.png" alt-text="Indikátor porovnání":::

   V okně se zobrazí všechna data, která se ve vybraném souboru zjistila v naprogramovaném zařízení. Soubor mohl být v programovém zařízení aktualizován více programovacími zařízeními.

   Počet zjištěných rozdílů se zobrazí v pravém horním rohu okna. Možná se budete muset posunout dolů, aby se zobrazily rozdíly.

   :::image type="content" source="media/how-to-work-with-maps/scroll.png" alt-text="Posuňte se dolů k výběru.":::

   Číslo se počítá sousedícími řádky změněného textu. Pokud se například změnila osm po sobě jdoucích řádků kódu (odstraní, aktualizuje nebo přidal), bude se počítat jako jeden rozdíl.

   :::image type="content" source="media/how-to-work-with-maps/program-timeline.png" alt-text="Zobrazení časové osy programu.":::

4. Vyberte datum. V okně se zobrazí soubor zjištěný u vybraného data.

5. Soubor vybraný z podokna nedávné události/soubory se vždycky zobrazuje na pravé straně.

### <a name="device-programming-information-other-locations"></a>Informace o programování zařízení: jiná umístění

Kromě kontroly podrobností na časové ose programování můžete získat přístup k programovým informacím v okno Vlastnosti zařízení a inventáři zařízení.

| Typ zařízení | Description |
|--|--|
| Vlastnosti zařízení | Okno vlastností zařízení poskytuje informace o poslední události programování zjištěné na device\.. :::image type="content" source="media/how-to-work-with-maps/information-from-device-v2.png" alt-text="Vlastnosti vašeho zařízení"::: |
| Inventář zařízení | Inventář zařízení indikuje, jestli je zařízení programový device\.. :::image type="content" source="media/how-to-work-with-maps/inventory-v2.png" alt-text="Inventář zařízení"::: |

## <a name="manage-device-information-from-the-map"></a>Správa informací o zařízení z mapy

Senzor neaktualizuje nebo neovlivní zařízení přímo v síti. Změny, které jste udělali, mají vliv jenom na to, jak zařízení analyzuje.

### <a name="delete-devices"></a>Odstranit zařízení

Pokud se zjištěné informace netýkají, je vhodné zařízení odstranit. Třeba

  - Partnerská strana v technické pracovní stanici se k provedení aktualizací konfigurace dočasná připojuje. Po dokončení úlohy se zařízení odebere.

  - V důsledku změn v síti už některá zařízení nejsou připojená.

Pokud zařízení neodstraníte, snímač ho bude nadále monitorovat. Po 60 dnech se zobrazí oznámení, které doporučuje, abyste ho odstranili.

Může se zobrazit výstraha oznamující, že zařízení nereaguje, pokud se k němu pokusí přistupovat jiné zařízení. V takovém případě může být vaše síť nesprávně nakonfigurovaná.

Zařízení se odebere z mapy zařízení, inventáře zařízení a sestav dolování dat. Další informace, například: informace uložené v pomůckách budou zachovány.

Aby se zařízení odstranilo aspoň 10 minut, musí být zařízení neaktivní.

Odstranění zařízení z mapy zařízení:

1. V postranní nabídce vyberte **zařízení** .

2. Klikněte pravým tlačítkem na zařízení a vyberte **Odstranit**.

### <a name="merge-devices"></a>Sloučit zařízení

Za určitých okolností může být nutné sloučit zařízení. To může být nutné v případě, že senzor zjistil samostatné síťové entity, které jsou přidruženy k jednomu jedinečnému zařízení. Třeba

  - PLC se čtyřmi síťovými kartami.

  - Přenosný počítač s Wi-Fi a fyzickou kartou.
  
  - Pracovní stanice se dvěma nebo více síťovými kartami.

Při slučování dáte pokyn snímači ke kombinování vlastností zařízení dvou zařízení do jednoho. Když to uděláte, budou se sestavy okno Vlastnosti zařízení a snímače aktualizovat o nové podrobnosti vlastnosti zařízení.

Pokud například sloučíte dvě zařízení, každá s IP adresou, budou se obě IP adresy zobrazovat jako samostatná rozhraní okno Vlastnosti zařízení. Je možné sloučit pouze ověřená zařízení.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="okno Vlastnosti zařízení":::

Časová osa události prezentuje událost sloučení.

:::image type="content" source="media/how-to-work-with-maps/events-time.png" alt-text="Časová osa události se sloučenými událostmi.":::

Sloučení zařízení nelze vrátit zpět. Pokud jste omylem sloučili dvě zařízení, odstraňte zařízení a počkejte, než se senzor znovu nenajde.

Sloučení zařízení:

1. Vyberte dvě zařízení (Shift-Click) a pak na jednu z nich klikněte pravým tlačítkem.

2. Vyberte **Sloučit** , aby se zařízení mohla sloučit. Dokončení sloučení může trvat až 2 minuty.

3. V dialogovém okně nastavit atributy slučovacího zařízení vyberte název zařízení.

   :::image type="content" source="media/how-to-work-with-maps/name-the-device-v2.png" alt-text="Dialogové okno atributy":::

4. Vyberte **Uložit**.

### <a name="authorize-and-unauthorize-devices"></a>Autorizace a Neautorizovaná zařízení

Během období učení se všechna zařízení zjištěná v síti identifikují jako Autorizovaná zařízení. **Autorizovaný** popisek se na těchto zařízeních na mapě zařízení nezobrazí.

Jakmile se zařízení zjistí po období učení, zobrazí se jako neautorizované zařízení. Kromě toho, že se na mapě zobrazují neoprávněná zařízení, můžete je zobrazit i v inventáři zařízení.

:::image type="content" source="media/how-to-work-with-maps/inventory-icon.png" alt-text="Inventář zařízení":::

**Nové zařízení vs. Neautorizováno**

Nová zařízení zjištěná po období učení se zobrazí s `New` `Unauthorized` popiskem a.

Pokud zařízení na mapě přesunete nebo ručně změníte vlastnosti zařízení, `New` Popisek se odebere z ikony zařízení.

#### <a name="unauthorized-devices---attack-vectors-and-risk-assessment-reports"></a>Neoprávněná zařízení – vektory útoku a sestavy vyhodnocení rizik

Neoprávněná zařízení jsou součástí sestav hodnocení rizik a vektorů útoků.

- **Sestavy vektorů útoku:** Zařízení označená jako neoprávněná jsou ve vektoru útoku vyřešena jako podezřelá neoprávněná zařízení, která by mohla být hrozbou pro síť.

   :::image type="content" source="media/how-to-work-with-maps/attack-vector-reports.png" alt-text="Zobrazení vektorové sestavy útoku":::

- **Sestavy posouzení rizik:** Zařízení označená jako neoprávněná jsou:

  - Identifikováno v sestavách posouzení rizik

Ruční autorizace nebo zrušení autorizace zařízení:

1. Pravým tlačítkem myši klikněte na zařízení na mapě a vyberte zrušit **autorizaci** .

### <a name="mark-devices-as-important"></a>Označení zařízení jako důležitých

Významná síťová zařízení můžete označit jako důležité, například firemní důležité servery. Tato zařízení jsou označená hvězdičkou na mapě. Hvězdička se liší podle úrovně přiblížení mapy.

:::image type="icon" source="media/how-to-work-with-maps/star-one.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-two.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-3.png" border="false":::

### <a name="important-devices---attack-vectors-and-risk-assessment-reports"></a>Důležitá zařízení – vektory útoku a sestavy hodnocení rizik

Při generování sestav posouzení rizik a vektorů útoku se vypočítají důležitá zařízení.

  - Vektorové sestavy, které jsou označené jako důležité, se ve vektoru útoku vyřeší jako cíle útoku. 

  - Sestavy posouzení rizik: zařízení označená jako důležitá se počítají při poskytování skóre zabezpečení v sestavě posouzení rizik.

## <a name="generate-activity-reports-from-the-map"></a>Generování sestav aktivit z mapy

Vygeneruje sestavu aktivity pro vybrané zařízení za 1, 6, 12 nebo 24 hodin. K dispozici jsou následující informace:

  - Kategorie: základní informace o zjišťování na základě scénářů provozu.

  - Zdrojová a cílová zařízení

  - Data: Další informace byly vyřazeny.

  - Čas a datum posledního vypohledu.

Sestavu můžete uložit jako soubor aplikace Microsoft Excel nebo Word.

:::image type="content" source="media/how-to-work-with-maps/historical-information.png" alt-text="Poslední aktivita":::

Vygenerování sestavy aktivity pro zařízení:

1. Pravým tlačítkem myši klikněte na zařízení z mapy.

2. Vyberte sestavu aktivity.

   :::image type="content" source="media/how-to-work-with-maps/activity-report.png" alt-text="Zobrazit sestavu aktivity.":::

## <a name="generate-attack-vector-reports-from-the-map"></a>Generování sestav vektorů útoku z mapy

Simulujte sestavu vektoru útoku a zjistěte, jestli je zařízení na mapě, které jste vybrali, zranitelné cílem útoku.

Vektorové sestavy útoku poskytují grafické znázornění řetězce ohrožení zabezpečení zneužití zařízení. Tato ohrožení zabezpečení můžou útočníkovi umožnit přístup k klíčovým síťovým zařízením. Simulátor vektoru útoku vypočítá vektory útoku v reálném čase a analyzuje všechny vektory útoku na konkrétní cíl.

Zobrazení zařízení v sestavách útoku na útoky:

1. Pravým tlačítkem myši klikněte na zařízení z mapy.

2. Vyberte **simulovat vektory útoku**. Dialogové okno způsob útoku se otevře se zařízením, které vyberete jako cíl útoku.

   :::image type="content" source="media/how-to-work-with-maps/simulation.png" alt-text="Přidat simulaci vektoru útoku":::

3. Do dialogového okna přidejte zbývající parametry a vyberte **Přidat simulaci**.

## <a name="export-device-information-from-the-map"></a>Export informací o zařízení z mapy

Exportujte následující informace o zařízení z mapy.

  - Podrobnosti o zařízení (Microsoft Excel)

  - Souhrn zařízení (Microsoft Excel)

  - Soubor aplikace Word se skupinami (Microsoft Word)

Export:

1. Vyberte z mapy ikonu pro export.

1. Vyberte možnost exportu.

## <a name="see-also"></a>Viz také

[Prozkoumat detekci senzorů v inventáři zařízení](how-to-investigate-sensor-detections-in-a-device-inventory.md)
