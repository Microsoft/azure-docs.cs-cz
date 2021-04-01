---
title: Práce s mapami ve vnitřních mapách v Azure Maps Creator (Preview)
description: Tento článek představuje koncepty, které se vztahují na služby Azure Maps Creator (Preview).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4ab00317e71f832bb677c4c7587e2356a37cb7a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96903560"
---
# <a name="creator-preview-for-indoor-maps"></a>Autor (Preview) pro mapy vnitřních verzí


> [!IMPORTANT]
> Služby Azure Maps Creator jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


V tomto článku se seznámíte s koncepty a nástroji, které se týkají Azure Maps Creator. Doporučujeme, abyste si tento článek přečetli dřív, než začnete používat rozhraní API pro Azure Maps Creator a sadu SDK.

Tvůrce můžete použít k vývoji aplikací s funkcemi mapy založenými na vnitřních datech mapy. Tento článek popisuje proces nahrávání, převádění, vytváření a používání dat map. Celý pracovní postup je znázorněn v následujícím diagramu.

![Pracovní postup tvůrce dat mapy](./media/creator-indoor-maps/workflow.png)

## <a name="create-azure-maps-creator-preview"></a>Vytvořit tvůrce Azure Maps (Preview) 

Chcete-li používat službu Creator Services (Preview), je třeba vytvořit Azure Maps tvůrce v účtu Azure Maps. Informace o tom, jak vytvořit tvůrce Azure Maps v Azure Maps, najdete v tématu [správa Azure Maps Creator](how-to-manage-creator.md).

## <a name="upload-a-drawing-package"></a>Nahrání balíčku pro kreslení

Creator (Preview) shromažďuje data vnitřních map tím, že převede nahraný balíček pro kreslení. Balíček pro kreslení představuje vytvořené nebo přemodelované zařízení. Informace o požadavcích na sbalení balíčku najdete v tématu [požadavky na balíčky pro vykreslování](drawing-requirements.md).

Pomocí [rozhraní API pro nahrávání Azure maps dat (Preview)](/rest/api/maps/data/uploadpreview) nahrajte balíček pro kreslení.  Po úspěšném nahrání načte rozhraní API pro nahrání dat identifikátor dat uživatele ( `udid` ). `udid`V dalším kroku se použije k převedení nahraného balíčku na data mapy interiéru.

## <a name="convert-a-drawing-package"></a>Převod balíčku pro kreslení

[Služba Azure Maps Conversion](/rest/api/maps/conversion) převede nahraný balíček vykreslování na data mapy vnitřních dat. Služba konverze také ověřuje balíček. Problémy s ověřením jsou klasifikovány do dvou typů: chyby a upozornění. Pokud jsou zjištěny nějaké chyby, proces převodu se nezdařil. By měla být zjištěna upozornění, převod bude úspěšný. Doporučuje se ale zkontrolovat a vyřešit všechna upozornění. Upozornění znamená, že část převodu byla ignorována nebo automaticky opravena. Selhání při řešení problémů by mohlo způsobit chyby v těchto procesech. Další informace najdete v tématu [Upozornění a chyby při vykreslování balíčku](drawing-conversion-error-codes.md).

Pokud dojde k chybě, služba převodu poskytuje odkaz na Azure Maps vykreslení samostatné webové aplikace [Vizualizér chyb](drawing-error-visualizer.md) . K prozkoumání [Upozornění a chyb](drawing-conversion-error-codes.md) , ke kterým došlo během procesu převodu, můžete použít Vizualizér chyb při vykreslování. Až chyby opravíte, můžete se pokusit o nahrání a převod balíčku.

## <a name="create-indoor-map-data"></a>Vytvoření dat vnitřních map

Azure Maps Creator (Preview) poskytuje tři služby:

* [Služba DataSet](/rest/api/maps/dataset/createpreview)
Pomocí služby DataSet Vytvořte datovou sadu z převedených dat balíčku vykreslování.
* [Služba TILESET](/rest/api/maps/tileset/createpreview)
Použijte službu TILESET k vytvoření vektorové reprezentace datové sady. Aplikace mohou používat TILESET k prezentaci vizuálního zobrazení na základě dlaždice datové sady.
* [Stavová služba funkcí](/rest/api/maps/featurestate). Použijte službu stavu funkce pro podporu stylů dynamické mapy. Styl dynamické mapy umožňuje aplikacím odrážet události v reálném čase na prostorech poskytovaných systémem IoT.

### <a name="datasets"></a>Datové sady

Datová sada je kolekce funkcí mapy vnitřních verzí. Funkce mapy vnitřních funkcí představuje zařízení definovaná v převedeném balíčku pro kreslení. Po vytvoření datové sady pomocí [služby DataSet](/rest/api/maps/dataset/createpreview)můžete vytvořit libovolný počet [tilesets](#tilesets) nebo [funkcí statesets](#feature-statesets).

[Služba DataSet](/rest/api/maps/dataset/createpreview) umožňuje vývojářům kdykoli přidat nebo odebrat zařízení pro existující datovou sadu. Další informace o tom, jak aktualizovat existující datovou sadu pomocí rozhraní API, najdete v tématu možnosti připojení ve [službě DataSet](/rest/api/maps/dataset/createpreview). Příklad, jak aktualizovat datovou sadu, najdete v tématu [Údržba dat](#data-maintenance).

### <a name="tilesets"></a>Tilesets

TILESET je kolekce vektorových dat, která představuje sadu jednotných dlaždic mřížky. Vývojáři mohou pomocí [služby TILESET](/rest/api/maps/tileset/createpreview) vytvořit tilesets z datové sady.

Chcete-li odrážet různé fáze obsahu, můžete vytvořit více tilesets ze stejné datové sady. Můžete například vytvořit jeden TILESET s nábytekem a zařízením a další TILESET bez nábytku a vybavení.  Můžete se rozhodnout, že vygenerujete jeden TILESET s nejnovějšími aktualizacemi dat a jednou bez nejnovějších aktualizací dat.

Kromě vektorových dat poskytuje TILESET metadata pro optimalizaci vykreslování mapy. Například metadata TILESET obsahují minimální a maximální úroveň přiblížení pro TILESET. Metadata také poskytují ohraničující rámeček definující geografickou velikost TILESET. Ohraničovací rámeček umožňuje aplikaci programově nastavit správný středový bod. Další informace o metadatech TILESET najdete v tématu [rozhraní API pro seznam TILESET](/rest/api/maps/tileset/listpreview).

Po vytvoření je TILESET může získat [Služba vykreslování v2](#render-v2-service).

Pokud se TILESET stane zastaralým a již není užitečný, můžete TILESET odstranit. Další informace o tom, jak odstranit tilesets, najdete v tématu  [Údržba dat](#data-maintenance).

>[!NOTE]
>TILESET je nezávislý na datové sadě, ze které byla vytvořena. Pokud vytvoříte tilesets z datové sady a následně aktualizujete tuto datovou sadu, tilesets se neaktualizuje. Aby se projevily změny v datové sadě, je nutné vytvořit nové tilesets. Podobně pokud odstraníte TILESET, datová sada nebude ovlivněna.

### <a name="feature-statesets"></a>Statesets funkcí

Funkce statesets jsou kolekce dynamických vlastností (*stavů*) přiřazených k funkcím datové sady, jako jsou místnosti nebo vybavení. Příkladem *stavu* může být teplota nebo obsazení. Každý *stav* je dvojice klíč/hodnota obsahující název vlastnosti, hodnotu a časové razítko poslední aktualizace.

[Služba stavu funkce](/rest/api/maps/featurestate/createstatesetpreview) umožňuje vytvořit a spravovat funkci stateset pro datovou sadu. Stateset je definován jedním nebo více *stavy*. Každá funkce, jako je například místnost, může mít k sobě připojen jeden *stav* .

Hodnota každého *stavu* ve stateset může být aktualizována nebo načtena zařízeními IoT nebo jinými aplikacemi.  Například při použití [rozhraní API aktualizace stavu funkce](/rest/api/maps/featurestate/updatestatespreview)zařízení, která měří obsazení místa, můžou systematicky zveřejnit stavovou změnu místnosti.

Aplikace může používat funkci stateset k dynamickému vykreslování funkcí v zařízení podle jejich aktuálního stavu a odpovídajícího stylu mapy. Další informace o používání funkcí statesets k funkcím stylu v mapě vykreslování naleznete v tématu [vnitřní webová sada SDK](#indoor-maps-module).

>[!NOTE]
>Podobně jako tilesets, změna datové sady nemá vliv na stávající funkce stateset a odstranění funkce stateset nebude mít žádný vliv na datovou sadu, ke které je připojená.

## <a name="using-indoor-maps"></a>Použití map vnitřníchy

### <a name="render-v2-service"></a>Služba vykreslování v2

Služba Azure Maps [Render v2 – získat rozhraní API dlaždice mapy (Preview)](/rest/api/maps/renderv2/getmaptilepreview) , které podporuje tvůrce (Preview) tilesets.

Služba vykreslování v2 – rozhraní API dlaždice získat stav mapy umožňuje aplikacím požadovat tilesets. Tilesets je pak možné integrovat do mapového ovládacího prvku nebo sady SDK. Příklad mapového ovládacího prvku, který používá službu vykreslování v2, najdete v tématu [modul vnitřních map](#indoor-maps-module).

### <a name="web-feature-service-api"></a>Rozhraní API služby webové funkce

K datovým sadám se dá zadat dotaz pomocí [rozhraní API služby webové funkce (WFS)](/rest/api/maps/wfs). WFS se řídí [funkcemi rozhraní API pro Open Geospatial Consortium](http://docs.opengeospatial.org/DRAFTS/17-069r1.html). Rozhraní WFS API umožňuje dotazovat se na funkce v rámci samotné datové sady. Můžete například použít WFS k vyhledání všech místností pro setkání střední velikosti daného zařízení a úrovně podlahy.

### <a name="indoor-maps-module"></a>Modul interiérových map

[Azure Maps webová sada SDK](./index.yml) obsahuje modul vnitřních map. Tento modul nabízí rozšířené funkce knihovny Azure Maps *ovládací prvek mapa* . Modul mapy Vnitřníchy vykresluje vnitřní mapy vytvořené v tvůrci (Preview). Integruje widgety, jako je *Výběr podlaží*, což pomáhá uživatelům vizualizovat různá podlaží.

Modul mapy vnitřních souborů umožňuje vytvářet webové aplikace, které integrují data ze vnitřních map s ostatními [Azure Maps službami](./index.yml). Nejběžnější instalace aplikací můžou zahrnovat přidání znalostí do vnitřních map z jiných map, jako jsou silniční, satelitní a klimatické a tranzitní.

Modul mapy Vnitřníchy podporuje také dynamické styly mapy. Podrobný návod, jak implementovat dynamické stylování funkcí stateset v aplikaci, najdete v tématu [použití modulu mapy vnitřníchy](how-to-use-indoor-module.md) .

### <a name="azure-maps-integration"></a>Integrace Azure Maps

Jak začnete vyvíjet řešení pro mapy vnitřních prostorů, můžete zjistit způsoby, jak integrovat existující možnosti Azure Maps. Například scénáře sledování prostředků nebo zabezpečení by mohly být implementovány pomocí [Azure Maps rozhraní API pro geografickou](/rest/api/maps/spatial/postgeofence) práci s nástroji Creator interiér Maps. Rozhraní API geografické oblasti se dá použít k určení toho, jestli pracovní proces zadá nebo opustí konkrétní vnitřní oblasti. Další informace o tom, jak se připojit Azure Maps pomocí telemetrie IoT, [najdete tady](tutorial-iot-hub-maps.md).

### <a name="data-maintenance"></a>Údržba dat

 Rozhraní API pro Azure Maps tvůrce (Preview), Update a DELETE umožňuje zobrazit, aktualizovat a odstranit vaše datové sady, tilesets a funkce statesets.

>[!NOTE]
>Kdykoli provedete kontrolu seznamu položek a rozhodnete se je odstranit, musíte vzít v úvahu dopad tohoto odstranění na všechny závislé rozhraní API nebo aplikace. Například pokud byste měli odstranit TILESET, který aktuálně používá aplikace, pomocí [rozhraní vykreslování v2 rozhraní API pro zobrazení dlaždice mapy](/rest/api/maps/renderv2/getmaptilepreview), odstranění tohoto TILESET by vedlo k selhání aplikace při vykreslování tohoto TILESET.

### <a name="example-updating-a-dataset"></a>Příklad: aktualizace datové sady

Následující příklad ukazuje, jak aktualizovat datovou sadu, vytvořit novou TILESET a odstranit starou TILESET.

1. Při nahrávání a převodu nového balíčku pro kreslení použijte postup uvedený v části [nahrání balíčku](#upload-a-drawing-package) pro kreslení a [převod částí balíčku pro kreslení](#convert-a-drawing-package) .

2. Pomocí [datové sady vytvořte rozhraní API](/rest/api/maps/dataset/createpreview) pro připojení převedených dat do existující datové sady.

3. Pomocí [rozhraní TILESET Create API](/rest/api/maps/tileset/createpreview) Vygenerujte nový TILESET z aktualizované datové sady. Uložte novou tilesetId pro krok 4.

4. Aktualizujte identifikátor TILESET ve vaší aplikaci, aby se povolila vizualizace aktualizované datové sady areálu. Pokud se stará TILESET už nepoužívá, můžete ji odstranit.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: vytvoření mapy vnitřních verzí pro tvůrce (Preview)](tutorial-creator-indoor-maps.md)