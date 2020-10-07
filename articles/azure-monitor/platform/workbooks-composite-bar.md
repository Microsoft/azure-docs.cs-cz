---
title: Vykreslovací modul vykreslování kompozitního panelu Azure Monitor sešitu
description: Přečtěte si o všech vizualizacích zobrazovacího panelu odblokování Azure Monitor sešitu.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 9/04/2020
ms.author: lagayhar
ms.openlocfilehash: 80846ecb1ad48d9f8ba49f0025772b4e131c23e2
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776353"
---
# <a name="composite-bar-renderer"></a>Vykreslovací modul kompozitních pruhů

Sešit umožňuje vykreslování dat pomocí složeného pruhu, pruhu vytvořeného z více pruhů.

Následující obrázek ukazuje složený pruh pro stav databáze představující, kolik serverů je online, offline a ve stavu obnovování.

![Snímek obrazovky složeného pruhu pro stav databáze](./media/workbooks-composite-bar/database-status.png)

Vykreslování složených pruhů je podporováno pro vizualizace mřížky, dlaždic a grafů.

## <a name="adding-composite-bar-renderer"></a>Přidávání zobrazovacího panelu kompozitních pruhů

1. Přepněte sešit do režimu úprav výběrem možnosti *Upravit* položku panelu nástrojů.
2. Vyberte *Přidat* a pak *Přidat dotaz*.
3. Nastavte *zdroj dat* na JSON a *vizualizaci* na Grid.
4. Přidejte následující data JSON.

```json
[
    {"sub":"X", "server": "A", "online": 20, "recovering": 3, "offline": 4, "total": 27},
    {"sub":"X", "server": "B", "online": 15, "recovering": 8, "offline": 5, "total": 28},
    {"sub":"Y", "server": "C", "online": 25, "recovering": 4, "offline": 5, "total": 34},
    {"sub":"Y", "server": "D", "online": 18, "recovering": 6, "offline": 9, "total": 33}
]
```

5. Spustit dotaz.
6. Vyberte **Nastavení sloupce** a otevřete nastavení.
7. Vyberte možnost celkem ze *sloupců* a zvolte možnost složený pruh pro *Renderer sloupců*.
8. V části *Nastavení složeného panelu*nastavte následující nastavení.

| Název sloupce | Color        |
|-------------|--------------|
| online      | Green        |
| obnovení  | Žlutý       |
| stav     | Červená (světlá) |

9. Přidat popisek:`["online"] of ["total"] are healthy`
10. V nastavení sloupce pro online, offline a obnovování můžete nastavit vykreslování sloupců na skryté (volitelné).
11. Vyberte *popisky* v horní části a aktualizujte popisek sloupce celkem jako stav databáze (volitelné).
12. Vybrat při **použití**

Nastavení složeného panelu bude vypadat jako na následujícím snímku obrazovky:

![Snímek obrazovky s nastavením sloupce složených pruhů s výše popsaným nastavením](./media/workbooks-composite-bar/composite-bar-settings.png)

Složený pruh s výše uvedeným nastavením:

![Snímek ze složeného panelu](./media/workbooks-composite-bar/composite-bar.png)

## <a name="composite-bar-settings"></a>Nastavení složeného panelu

Vyberte název sloupce a odpovídající barvu pro vykreslení tohoto sloupce v této barvě jako součást složeného panelu. Můžete vložit, odstranit a přesunout řádky nahoru a dolů.

### <a name="label"></a>Popisek

Složený pruhový popisek se zobrazí v horní části složené čáry. Můžete použít kombinaci statického textu, sloupců a parametru.  Pokud je popisek prázdný, zobrazí se jako popisek hodnota aktuální sloupce. Pokud v předchozím příkladu ponecháme pole popisku černou, zobrazí se hodnota celkový počet sloupců.

Odkazuje na sloupce pomocí `["columnName"]` .

Informace o parametrech naleznete v tématu `{paramName}` .

Název sloupce i název parametru rozlišují velká a malá písmena. Popisek můžete také označit propojením tak, že vyberete vytvořit tuto položku jako odkaz a pak přidáte nastavení propojení.

### <a name="aggregation"></a>Agregace

Agregace jsou užitečné pro vizualizace/seskupení pomocí vizualizací. Data pro sloupec pro řádek skupiny jsou určena agregací nastaveným pro daný sloupec. Existují tři typy agregací, které lze použít pro složené pruhy: žádné, Sum a Inherit.

Přidání nastavení seskupit podle:

1. V nastavení sloupce přejdete do sloupce, do kterého chcete přidat nastavení.
2. V části *strom/seskupení podle nastavení* pod položkou *typ stromu*vyberte **Seskupit podle** .
3. Vyberte pole, podle kterého se má seskupit.

![Snímek obrazovky s nastavením Group by](./media/workbooks-composite-bar/group-by-settings.png)

#### <a name="none"></a>Žádné

Žádná agregace znamená, že pro řádky skupiny nebudou zobrazeny žádné výsledky pro tento sloupec.

![Snímek obrazovky složené čáry s agregací None](./media/workbooks-composite-bar/none.png)

#### <a name="sum"></a>Součet

Pokud je agregace nastavena jako součet, sloupec na řádku skupiny zobrazí složený pruh pomocí součtu sloupců použitých k vykreslení. Popisek bude také používat součet sloupců, na které se odkazuje.

V příkladu pod online, offline a obnovování všech je nastavená maximální agregovaná hodnota agregace a agregace sloupce Total je suma.

![Snímek obrazovky složeného pruhu s agregací Sum](./media/workbooks-composite-bar/sum.png)

#### <a name="inherit"></a>Přenesení

Pokud je agregace nastavená jako dědění, pak sloupec na řádku skupiny zobrazí složený pruh pomocí agregace nastavené uživateli pro sloupce použité k vykreslení. Sloupce použité v popisku používají také agregaci nastavenou uživatelem. Pokud je aktuální objekt pro vykreslování sloupce složený pruh a v popisku je refereed (jako "Total" v příkladu výše), použije se jako agregace pro tento sloupec suma.

V následujícím příkladu je k dispozici, že online, offline a obnovení všech mají pro sebe maximální agregovanou hodnotu a agregace pro sloupec Total je děděna.

![Snímek obrazovky složeného pruhu s děděnou agregací](./media/workbooks-composite-bar/inherit.png)

## <a name="sorting"></a>Řazení

U vizualizací mřížky funguje řazení řádků pro sloupec se složeným nástrojem pro vykreslování kompozitních pruhů na základě hodnoty, která je součtem sloupců používaných k dynamickému vykreslení počítače kompozitního panelu. V předchozích příkladech je hodnota použitá pro řazení součtem online, obnovování a offline sloupců pro tento konkrétní řádek.

## <a name="tiles-visualization"></a>Vizualizace dlaždic

1. Vyberte **Přidat** a *Přidat dotaz*.
2. Změňte zdroj dat na JSON a zadejte data z [předchozího příkladu](#adding-composite-bar-renderer).
3. Změna vizualizace na *dlaždice*
4. Spustit dotaz.
5. Vyberte **nastavení dlaždice**.
6. Vyberte položku *vlevo* v poli dlaždice.
7. V části *Nastavení pole*zadejte následující nastavení.
    1. Použijte sloupec: Server.
    2. Zobrazovací jednotka sloupce: "text".
8. V polích dlaždic vyberte *dolní* .
9. V části *Nastavení pole*zadejte následující nastavení.
    1. Použít sloupec: Total.
    2. Vykreslovací modul sloupců: "složený pruh".
    3. V části nastavení složeného panelu zadejte nastavte následující nastavení.

    | Název sloupce | Color        |
    |-------------|--------------|
    | online      | Green        |
    | obnovení  | Žlutý       |
    | stav     | Červená (světlá) |

    4. Přidat popisek: `["online"] of ["total"] are healthy` .
10. Vyberte **Použít**.

Nastavení složeného panelu pro dlaždice:

![Snímek obrazovky s nastavením dlaždice složených pruhů s výše popsaným nastavením](./media/workbooks-composite-bar/tiles-settings.png)

Složené zobrazení pruhů pro dlaždice s výše uvedenými nastaveními bude vypadat takto:

![Snímek obrazovky složených pruhových dlaždic](./media/workbooks-composite-bar/composite-bar-tiles.png)

## <a name="graphs-visualization"></a>Vizualizace grafů

Chcete-li pro vizualizaci grafu vytvořit kompozitní zobrazovací panely (typy clusterů podregistru), postupujte podle následujících pokynů.

1. Vyberte **Přidat** a *Přidat dotaz*.
2. Změňte zdroj dat na JSON a zadejte data z [předchozího příkladu](#adding-composite-bar-renderer).
3. Změna vizualizace na *grafy*
4. Spustit dotaz.
5. Vyberte **nastavení grafu**.
6. Vyberte *Vycentrovat obsah* v nastavení formát uzlu.
7. V části *Nastavení pole*zadejte následující nastavení.
    1. Použít sloupec: Total.
    2. Vykreslovací modul sloupců: "složený pruh".
    3. V části *Nastavení složeného panelu*zadejte následující nastavení.

    |Název sloupce  |     Color    |
    |-------------|--------------|
    | online      | Green        |
    | obnovení  | Žlutý       |
    | stav     | Červená (světlá) |

   4. Přidat popisek: `["online"] of ["total"] are healthy` .
9. V části *nastavení rozložení*zadejte níže uvedená nastavení.
    1. Typ grafu: **clustery podregistru**.
    2. ID uzlu vyberte: Server.
    3. Seskupit podle pole: "none".
    4. Velikost uzlu: 100.
    5. Okraj mezi šestiúhelníky: 5.
    6. Typ barevného vybarvení: **žádné**.
1. Vyberte **Použít**.
    
Nastavení složeného panelu pro grafy:

![Snímek obrazovky s nastavením kompozitního pruhového grafu s výše popsaným nastavením](./media/workbooks-composite-bar/graphs-settings.png)

Složené zobrazení pruhů pro graf s výše uvedenými nastaveními bude vypadat takto:

![Snímek obrazovky složených pruhových grafů s clustery podregistru](./media/workbooks-composite-bar/composite-bar-graphs.png)

## <a name="next-steps"></a>Další kroky

* [Nasaďte](workbooks-automate.md) sešit pomocí Azure Resource Manager.
* [Řízení](workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu.
