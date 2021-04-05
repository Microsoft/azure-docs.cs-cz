---
title: Pochopení sestav Insights v Azure dosah
description: Tento článek vysvětluje, jaké přehledy jsou v Azure dosah.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: d841fa336b2702a02f3215f97a6403217986d7e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96553057"
---
# <a name="understand-insights-in-azure-purview"></a>Principy přehledů v Azure Purview

Tento článek poskytuje přehled funkce Insights v Azure dosah.

Přehledy jsou jedním z klíčových pilířů dosah. Tato funkce poskytuje zákazníkům, jediné podokno se skleněným zobrazením do svého katalogu a dále se zaměřuje na poskytování konkrétních přehledů správcům zdrojů dat, obchodním uživatelům, datům Stewards, datovým úředníkům a správcům zabezpečení. V současné době má dosah následující sestavy Insights, které budou zákazníkům dostupné ve verzi Public Preview.

## <a name="asset-insights"></a>Přehledy prostředků

Tato sestava poskytuje pohled na oči z pohledu na vaši datovou plochu a její distribuci podle typu zdroje, podle klasifikace a velikosti souboru jako některé z dimenzí. Tato sestava se zakládá na různé typy uživatelů, kteří mohou spravovat účet dosah a spouštět kontroly nebo obchodní uživatele, kteří mají zájem o tom, kolik prostředků existuje s určitou klasifikací v rámci své organizace na základě jejich údajů. 

Tato sestava poskytuje široké přehledy prostřednictvím grafů a klíčových ukazatelů výkonu a vyšších hlubokých podrobně do konkrétních anomálií, jako jsou nesprávně umístěné soubory. Tato sestava také podporuje ucelené prostředí pro zákazníky, kde zákazník může zobrazit počet prostředků s určitou klasifikací, může vyhodnotit informace podle typů zdrojů a hlavních složek a může také zobrazit seznam prostředků pro další šetření.

## <a name="scan-insights"></a>Vyhledat přehledy

Tato sestava umožňuje správcům pochopit celkový stav kontrol – počet úspěšných, počet neúspěšných, Počet zrušených. Tato sestava obsahuje aktualizaci stavu pro kontroly, které byly spuštěny v účtu dosah během období posledních sedmi dnů nebo posledních 30 dnů.

Tato sestava umožňuje správcům také hluboko podrobně a prozkoumat, které kontroly se nezdařily a na jaké konkrétní typy zdrojů. Aby bylo možné uživatele prozkoumat, sestava jim pomůže přejít na stránku historie kontroly v rámci zkušeností "zdrojů".

## <a name="glossary-insights"></a>Glosář přehledů

Tato sestava dává obchodním uživatelům a datům Stewards zprávu o stavu v glosáři. Uživatelé mohou zobrazit tuto sestavu, aby porozuměli distribuci pojmů Glosář podle stavu, zjistili, kolik termínů glosáře je připojeno k assetům a kolik ještě není připojeno k žádnému assetu. Obchodní uživatelé si také můžou přečíst informace o úplnosti pojmů glosáře. 

Tato sestava shrnuje nejdůležitější položky, které musí firemní uživatel nebo data Steward zaměřit na vytvoření kompletního a použitelného glosáře pro svou organizaci. Uživatelé můžou také přejít do Glosářového prostředí z části "Glosář Insights", aby provedli změny konkrétního termínu glosáře.

## <a name="classification-insights"></a>Přehledy klasifikace

Tato sestava poskytuje podrobné informace o tom, kde se nacházejí klasifikovaná data, klasifikace nalezené během kontroly a podrobné informace o klasifikovaných souborech. Umožňuje správcům zabezpečení pochopit typy informací, které se nacházejí v nemovitosti jejich organizace. 

Ve službě Azure dosah se klasifikace podobají značkám předmětu a používají se k označení a identifikaci obsahu určitého typu v datovém majetku.

Pomocí sestavy Přehled klasifikace můžete identifikovat obsah s konkrétní klasifikací a pochopit požadované akce, jako je například přidání dalšího zabezpečení do úložišť nebo přesunutí obsahu do bezpečnějšího umístění.

Další informace najdete v tématu [Přehled klasifikace vašich dat z Azure dosah](classification-insights.md).

## <a name="sensitivity-labeling-insights"></a>Přehledy označování citlivosti

Tato sestava uvádí podrobnosti o popisech citlivosti zjištěných během kontroly a také podrobnější informace o označených souborech. Umožňuje správcům zabezpečení zajistit zabezpečení informací, které se nacházejí v nemovitosti jejich organizace. 

V Azure dosah se popisky citlivosti používají k identifikaci kategorií typů klasifikace a také zásad zabezpečení skupiny, které chcete použít u jednotlivých kategorií.

Pomocí sestavy přehledy popisů Identifikujte popisky citlivosti nalezené v obsahu a rozumíte požadovaným akcím, jako je například Správa přístupu ke konkrétním úložištím nebo souborům.

Další informace najdete v tématu věnovaném [citlivosti na popis vašich dat v Azure dosah](sensitivity-insights.md).

## <a name="file-extension-insights"></a>Přehledy přípon souborů

Tato sestava poskytuje podrobné informace o příponách souborů nebo typech souborů nalezených během kontroly a také k podrobnému procházení souborů. 

Pomocí sestavy přehledy přípon souborů můžete pochopit, kolik souborů v každém případě máte, kde jsou tyto soubory a jestli jsou scannable pro citlivá data.

Další informace najdete v tématu věnovaném [rozšíření souborů přehled o vašich datech z Azure dosah](file-extension-insights.md).

## <a name="next-steps"></a>Další kroky

* [Glosář přehledů](glossary-insights.md)
* [Vyhledat přehledy](scan-insights.md)
* [Přehledy klasifikace](./classification-insights.md)