---
title: Přehled Azure Maps | Microsoft Docs
description: Seznámení s Azure Maps
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: overview
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 4882eadb8db5137d6fcf75c6d80c34ae050d3a6d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193947"
---
# <a name="an-introduction-to-azure-maps"></a>Seznámení s Azure Maps
Azure Maps je portfolio geoprostorových služeb, mezi které patří rozhraní API pro mapy, vyhledávání, trasy, provoz a časová pásma. Portfolio služeb umožňuje používat známé nástroje k rychlému vývoji a škálování řešení, která integrují informace o poloze do vašich řešení v Azure. Azure Maps poskytuje vývojářům ze všech odvětví výkonné geoprostorové funkce společně s čerstvými mapovými daty, která jsou nezbytná k zajištění geografického kontextu pro webové a mobilní aplikace. Azure Maps představuje sadu rozhraní REST API doprovázenou webovým ovládáním v JavaScriptu pro zajištění snadného, flexibilního a přenositelného vývoje napříč různými médii. 

Následující video představuje Azure Maps:

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Azure Maps se skládá z pěti primárních služeb pro podporu aplikací v Azure, které vyžadují geografický kontext. Ke každé ze služeb je k dispozici podrobný popis.

Služba **Render Service** je určená vývojářům, kteří potřebují vytvářet webové a mobilní mapové aplikace. Služba nabízí buď vysoce kvalitní rastrové podklady v 19 úrovních přiblížení, nebo plně přizpůsobitelné vektorové obrazy.

![Azure Maps Map.png](media/about-azure-maps/Introduction_Map.png)

Služba **Route Service** obsahuje robustní výpočty na základě reálné geometrie a infrastruktury a trasy pro různé způsoby dopravy. Služba umožňuje vývojářům vypočítat trasy v několika různých režimech, např. auto, nákladní vůz, kolo nebo chůze. Služba také dokáže zohlednit zadání, jako je aktuální provoz, omezení hmotnosti nebo doprava nebezpečných látek.

![Azure Maps Route.png](media/about-azure-maps/Introduction_Route.png)

Služba **Search Service** umožňuje vývojářům vyhledávat adresy, místa a výpisy firem podle názvu nebo kategorie a jiných zeměpisných údajů. Vyhledávací služba může také [reverzně geokódovat](https://en.wikipedia.org/wiki/Reverse_geocoding) adresy a křižovatky na základě zeměpisných souřadnic. 

![Azure Maps Search.png](media/about-azure-maps/Introduction_Search.png)

Služba **Time Zone Service** umožňuje získávat aktuální, historické i budoucí informace související s časovými pásmy buď podle zeměpisné šířky a délky, nebo [ID organizace IANA](http://www.iana.org/). Služba časových pásem také umožňuje převod ID časových pásem Microsoft Windows na ID organizace IANA, zjištění rozdílu časového pásma od UTC a získání aktuálního času v zadaném časovém pásmu. Typická odpověď JSON na dotaz na službu Time Zone Service vypadá jako v následující ukázce:

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

**Traffic Service** je sada webových služeb určená pro vývojáře, kteří ve svých webových nebo mobilních aplikacích potřebují informace o provozu. Služba nabízí dva datové typy:
* Aktuální provoz – v reálném čase zjištěné rychlosti a doby průjezdu pro všechny klíčové silnice sítě. 
* Dopravní nehody – přesné informace o zablokované dopravě a o nehodách na silniční síti.

![Provoz v Azure Maps](media/about-azure-maps/Introduction_Traffic.png)

Služby Azure Maps jsou navržené pro mobilní aplikace a je možné na nich založit i víceplatformní aplikace, protože jejich programovací model je agnostický a podporuje výstup JSON prostřednictvím rozhraní REST API. Kromě toho Azure Maps nabízí pohodlný mapový ovládací prvek v JavaScriptu s jednoduchým programovacím modelem pro rychlý a snadný vývoj webových i mobilních aplikací. 

Azure Maps používá schéma ověřování na základě klíčů, takže přístup ke službám je otázkou přechodu na web [Azure Portal](http://portal.azure.com) a vytvoření účtu Azure Maps. V účtu budou pro vás předem vygenerované dva klíče. Při zahájení integrace těchto polohových služeb do vaší aplikace můžete v požadavcích na službu Azure Maps použít kterýkoli z obou klíčů.

## <a name="unsupported-regions"></a>Nepodporované oblasti
Rozhraní Azure Maps API v současné době není dostupné v několika zemích. Zkontrolujte aktuální IP adresu a ověřte, že umístění této IP adresy není v jedné z nepodporovaných zemích uvedených níže:

* Argentina
* Čína
* Indie
* Maroko
* Pákistán
* Jižní Korea

## <a name="relationship-with-bing-maps"></a>Vztah ke službě Mapy Bing
Mapy popsané v tomto dokumentu se liší od těch, které poskytuje služba Mapy Bing. Přestože tyto dvě služby sdílejí velmi podobné funkce, jsou rozdílné a vzájemně nesouvisejí. Tato služba Azure nemá vliv na nabídku ani plán produktu Mapy Bing.

Cílem společnosti Microsoft je poskytnout komunitě vývojářů s ohledem na služby zjišťování polohy možnost volby. Následující tabulka obsahuje pokyny pro vývojáře, kteří se rozhodují, kterou službu využít: 

| Scénář | Azure Maps použijte, když… | Mapy Bing použijte, když… |
| ------------- | ------------- | ------------- |
| Vývojové prostředí | Vytváříte v jiných službách Azure nebo s nimi koordinujete vývoj. | Využíváte cloud třetí strany nebo jiné vývojové prostředí. |
| Vývojová fáze  | Služba Azure Maps je optimalizovaná pro vývoj testování v počáteční fázi a testování konceptu. | Pro produkční prostředí se vyžaduje smlouva SLA na podnikové úrovni. |
| Cenové možnosti | Postačují předběžné cenové možnosti pro vývojáře. | Vyžadují se individuální ceny na podnikové úrovni. |
| Prostředí pro případy použití | Vyžaduje se využití ve vozidle. | Nevyžaduje se využití ve vozidle. |
| Geografické pokrytí | Nevyžaduje se pokrytí pro Indii, Čínu, Japonsko a Jižní Koreu. | Pokrytí pro Indii, Čínu, Japonsko a Jižní Koreu se vyžaduje. |
| Obsah map | Postačují standardní mapy povrchu. | Vyžadují se satelitní mapy, letecké mapy a obrázky ulic. |
| Základní zdroj map | Upřednostňují se mapovací data TomTom. | Upřednostňují se mapovací data HERE. |

Zaregistrujte si [účet Azure Maps ještě dnes](http://aka.ms/azurelbsportal).

## <a name="next-steps"></a>Další kroky

Teď máte přehled o Azure Maps. Dalším krokem je vyzkoušení ukázkové aplikace, která službu předvádí.

> [!div class="nextstepaction"]
> [Spuštění ukázky interaktivního hledání v mapách](quick-demo-map-app.md)

