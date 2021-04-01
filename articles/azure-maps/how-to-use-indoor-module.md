---
title: Použití modulu Azure Mapsch vnitřních map se službou Microsoft Creator Services (Preview)
description: Naučte se, jak používat modul mapy Vnitřníchy Microsoft Azure map pro vykreslování map vložením knihoven JavaScript modulu.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: e527cf5fa6a7caaeaf56ea19d684dd0830d5ca8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708675"
---
# <a name="use-the-azure-maps-indoor-maps-module"></a>Použití modulu Azure Mapsch vnitřních map

> [!IMPORTANT]
> Služby Azure Maps Creator jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sada Azure Maps Web SDK obsahuje modul *Azure Maps interiéru* . Modul  *vnitřních Azure Maps* umožňuje vykreslovat mapy vnitřních verzí vytvořené v rámci služby Azure Maps Creator Services (Preview). 

## <a name="prerequisites"></a>Požadavky

1. [Vytvořit účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Vytvoření prostředku Creator (Preview)](how-to-manage-creator.md)
3. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného.
4. Získejte `tilesetId` a a `statesetId` doplněním [kurzu vytváření vnitřních map](tutorial-creator-indoor-maps.md).
 Tyto identifikátory budete muset použít k vykreslování vnitřních map pomocí modulu Azure Mapsch vnitřních map.

## <a name="embed-the-indoor-maps-module"></a>Vložení modulu mapy Vnitřníchy

Modul *vnitřního Azure Maps* můžete nainstalovat a vložit jedním ze dvou způsobů.

Pokud chcete použít globálně hostovanou verzi Content Delivery Network Azure *Azure Maps modulu vnitřního* prostředí, odkazujte na následující odkazy na šablonu JavaScriptu a stylů v `<head>` elementu souboru HTML:

```html
<link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
<script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
```

 Nebo si můžete stáhnout Azure Maps modul *vnitřního* softwaru. Modul *vnitřních Azure Maps* obsahuje klientskou knihovnu pro přístup ke službě Azure Maps Services. Pomocí následujících kroků nainstalujete a načtete modul pro *vnitřní* prostředí do své webové aplikace.  
  
  1. Nainstalujte [balíček Azure-Maps-interiér](https://www.npmjs.com/package/azure-maps-indoor).
  
      ```powershell
      >npm install azure-maps-indoor
      ```

  2. Odkaz na *Azure Maps modul vnitřních* souborů a šablonu stylů v `<head>` prvku souboru HTML:

      ```html
      <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-indoor.min.css" type="text/css" />
      <script src="node_modules/azure-maps-drawing-tools/dist/atlas-indoor.min.js"></script>
      ```

## <a name="instantiate-the-map-object"></a>Vytvoření instance objektu map

Nejprve vytvořte *objekt mapy*. *Objekt map* se použije v dalším kroku pro vytvoření instance objektu *správce vnitřních* objektů.  Následující kód ukazuje, jak vytvořit instanci *objektu mapy*:

```javascript
const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";

const map = new atlas.Map("map-id", {
  //use your facility's location
  center: [-122.13203, 47.63645],
  //or, you can use bounds: [# west, # south, # east, # north] and replace # with your map's bounds
  style: "blank",
  view: 'Auto',
  authOptions: {
      authType: 'subscriptionKey',
      subscriptionKey: subscriptionKey
  },
  zoom: 19,
});
```

## <a name="instantiate-the-indoor-manager"></a>Vytvoření instance Správce vnitřních

Chcete-li načíst vnitřní tilesets a styl mapy dlaždic, je nutné vytvořit instanci *správce vnitřních* verzí. Vytvořte instanci *správce vnitřních* objektů tak, že poskytnete *objekt map* a odpovídající `tilesetId` . Pokud chcete podporovat [styl dynamické mapy](indoor-map-dynamic-styling.md), musíte předat `statesetId` . V `statesetId` názvu proměnné se rozlišují malá a velká písmena. Váš kód by měl vypadat jako JavaScript níže.

```javascript
const tilesetId = "<tilesetId>";
const statesetId = "<statesetId>";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: tilesetId,
    statesetId: statesetId // Optional
});
```

Chcete-li povolit cyklické dotazování na data stavu, je nutné zadat `statesetId` volání a `indoorManager.setDynamicStyling(true)` . Data o stavu cyklického dotazování umožňují dynamicky aktualizovat stav dynamických vlastností nebo *stavů*. Například funkce, jako je například místnost, může mít zavolanou dynamickou vlastnost (*stav*) `occupancy` . Vaše aplikace se může chtít dotázat na všechny změny *stavu* , aby odrážely změnu ve vizuální mapě. Následující kód ukazuje, jak povolit cyklické dotazování stavu:

```javascript
const tilesetId = "<tilesetId>";
const statesetId = "<statesetId>";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: tilesetId,
    statesetId: statesetId // Optional
});

if (statesetId.length > 0) {
    indoorManager.setDynamicStyling(true);
}
```

## <a name="indoor-level-picker-control"></a>Ovládací prvek Výběr úrovně vnitřníchy

 Ovládací prvek *pro výběr na úrovni interiéru* umožňuje změnit úroveň vykreslené mapy. Volitelnou inicializaci ovládacího prvku *pro výběr na úrovni interiéru* můžete provést pomocí *správce vnitřních* verzí. Zde je kód pro inicializaci výběru ovládacího prvku úrovně:

```javascript
const levelControl = new atlas.control.LevelControl({ position: "top-right" });
indoorManager.setOptions({ levelControl });
```

## <a name="indoor-events"></a>Vnitřní události

 Modul *vnitřních Azure Maps* podporuje události *objektu map* . Naslouchací procesy událostí *objektu mapy* jsou vyvolány, když došlo ke změně úrovně nebo zařízení. Pokud chcete spustit kód po změně úrovně nebo zařízení, umístěte kód do naslouchacího procesu události. Následující kód ukazuje, jak lze do *objektu map* přidat naslouchací procesy událostí.

```javascript
map.events.add("levelchanged", indoorManager, (eventData) => {

  //code that you want to run after a level has been changed
  console.log("The level has changed: ", eventData);
});

map.events.add("facilitychanged", indoorManager, (eventData) => {

  //code that you want to run after a facility has been changed
  console.log("The facility has changed: ", eventData);
});
```

`eventData`Proměnná uchovává informace o úrovni nebo zařízení, které vyvolalo `levelchanged` `facilitychanged` událost nebo, v uvedeném pořadí. Když dojde ke změně úrovně, bude `eventData` objekt obsahovat `facilityId` metadata, nová `levelNumber` a další metadata. Když se zařízení změní, `eventData` objekt bude obsahovat nové `facilityId` , novou `levelNumber` a další metadata.

## <a name="example-use-the-indoor-maps-module"></a>Příklad: použití modulu mapy Vnitřníchy

V tomto příkladu se dozvíte, jak ve webové aplikaci použít modul *vnitřníchy Azure Maps* . I když je v oboru omezený příklad, zahrnuje základní informace o tom, co potřebujete, abyste mohli začít používat modul *Azure Maps vnitřních* . Úplný kód HTML je pod tímto postupem.

1. Pomocí [Možnosti](#embed-the-indoor-maps-module) Azure Content Delivery Network nainstalujte modul *Azure Maps interiéru* .

2. Vytvořit nový soubor HTML

3. V záhlaví HTML, odkazujte na Azure Maps a styly šablon stylů v modulu *interiéru* .

4. Inicializuje *objekt mapy*. *Objekt map* podporuje následující možnosti:
    - `Subscription key` je váš Azure Maps primární klíč předplatného.
    - `center` definuje zeměpisnou šířku a délku pro umístění vašeho centra mapy na vnitřních místech. Zadejte hodnotu pro, `center` Pokud nechcete zadat hodnotu pro `bounds` . Formát by měl vypadat takto `center` : [-122,13315, 47,63637].
    - `bounds` je nejmenší pravoúhlý tvar, který obklopuje data mapy TILESET. Nastavte hodnotu pro `bounds` , pokud nechcete nastavit hodnotu pro `center` . Mapu vazeb můžete najít voláním [rozhraní API pro seznam TILESET](/rest/api/maps/tileset/listpreview). Rozhraní TILESET vypíše rozhraní API `bbox` , které můžete analyzovat a přiřadit k `bounds` . Formát by měl vypadat takto `bounds` : [# západ, # jih, # východ, # sever].
    - `style` umožňuje nastavit barvu pozadí. Chcete-li zobrazit bílé pozadí, definujte `style` hodnotu "prázdné".
    - `zoom` umožňuje zadat minimální a maximální úroveň přiblížení pro mapu.

5. Pak vytvořte modul *správce vnitřních* . Přiřaďte *Azure Maps interiér* `tilesetId` a volitelně přidejte `statesetId` .

6. Vytvořte instanci ovládacího prvku *pro výběr na úrovni interiéru* .

7. Přidejte naslouchací procesy událostí *objektu mapy* .  

Váš soubor by teď měl vypadat podobně jako v následujícím formátu HTML.

  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, user-scalable=no" />
      <title>Indoor Maps App</title>
      
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>

      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
        
      <style>
        html,
        body {
          width: 100%;
          height: 100%;
          padding: 0;
          margin: 0;
        }

        #map-id {
          width: 100%;
          height: 100%;
        }
      </style>
    </head>

    <body>
      <div id="map-id"></div>
      <script>
        const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";
        const tilesetId = "<your tilesetId>";
        const statesetId = "<your statesetId>";

        const map = new atlas.Map("map-id", {
          //use your facility's location
          center: [-122.13315, 47.63637],
          //or, you can use bounds: [# west, # south, # east, # north] and replace # with your Map bounds
          style: "blank",
          view: 'Auto',
          authOptions: { 
              authType: 'subscriptionKey',
              subscriptionKey: subscriptionKey
          },
          zoom: 19,
        });

        const levelControl = new atlas.control.LevelControl({
          position: "top-right",
        });

        const indoorManager = new atlas.indoor.IndoorManager(map, {
          levelControl: levelControl, //level picker
          tilesetId: tilesetId,
          statesetId: statesetId // Optional
        });

        if (statesetId.length > 0) {
          indoorManager.setDynamicStyling(true);
        }

        map.events.add("levelchanged", indoorManager, (eventData) => {
          //put code that runs after a level has been changed
          console.log("The level has changed:", eventData);
        });

        map.events.add("facilitychanged", indoorManager, (eventData) => {
          //put code that runs after a facility has been changed
          console.log("The facility has changed:", eventData);
        });
      </script>
    </body>
  </html>
  ```

Pokud chcete zobrazit mapu vnitřních souborů, načtěte ji do webového prohlížeče. Měl by vypadat podobně jako na obrázku níže. Pokud kliknete na funkci stairwell, *Výběr úrovně* se zobrazí v pravém horním rohu.

  ![Obrázek mapy interiéru](media/how-to-use-indoor-module/indoor-map-graphic.png)

[Viz Živá ukázka](https://azuremapscodesamples.azurewebsites.net/?sample=Creator%20indoor%20maps)

## <a name="next-steps"></a>Další kroky

Přečtěte si o rozhraních API, která souvisí s modulem *Azure Maps vnitřních* :

> [!div class="nextstepaction"]
> [Požadavky balíčku pro kreslení](drawing-requirements.md)

>[!div class="nextstepaction"]
> [Autor (Preview) pro mapy vnitřních verzí](creator-indoor-maps.md)

Další informace o tom, jak přidat další data do mapy:

> [!div class="nextstepaction"]
> [Dynamické stylování map v interiéru](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](/samples/browse/?products=azure-maps)