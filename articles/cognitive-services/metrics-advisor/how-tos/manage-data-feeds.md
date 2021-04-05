---
title: Správa datových kanálů v poradci metrik
titleSuffix: Azure Cognitive Services
description: Naučte se spravovat datové kanály, které jste přidali do poradce metrik.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: mbullwin
ms.openlocfilehash: fb6eaf44967732d3a41ea92b0896540a40f694e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96184718"
---
# <a name="how-to-manage-your-data-feeds"></a>Postupy: Správa datových kanálů

Naučte se spravovat datové kanály připojitelné do služby Advisory metriky. Tento článek vás provede správou datových kanálů v poradci metriky.

## <a name="edit-a-data-feed"></a>Úprava datového kanálu

> [!NOTE]
> Po vytvoření datového kanálu nelze změnit následující podrobnosti. 
> * ID datového kanálu
> * Čas vytvoření
> * Dimenze
> * Typ zdroje
> * Členitost

Pouze správce datového kanálu smí provádět změny. 

Pozastavení nebo opětovné aktivace datového kanálu:

1. Na stránce seznam datového kanálu klikněte na operaci, kterou chcete provést na datovém kanálu.

2. Na stránce s podrobnostmi datového kanálu klikněte na tlačítko **stav** přepínač.

Postup odstranění datového kanálu: 

1. Na stránce se seznamem datového kanálu klikněte v informačním kanálu na **Odstranit** .

2. Na stránce s podrobnostmi datového kanálu klikněte na **Odstranit**.

Když změníte čas zahájení, budete muset schéma znovu ověřit. Můžete ji změnit pomocí **Upravit parametry**.

##  <a name="backfill-your-data-feed"></a>Zpětná výplň datového kanálu

Vyberte tlačítko  **zpět** pro aktivaci okamžitého příjmu na časové razítko, abyste opravili neúspěšnou příjem dat nebo přepsat stávající data.
- Čas spuštění je včetně.
- Čas ukončení je exkluzivní.
- Detekce anomálií se znovu aktivuje jenom na vybraném rozsahu.

:::image type="content" source="../media/datafeeds/backfill-datafeed.png" alt-text="Datový kanál pro naplnění dat":::

## <a name="manage-permission-of-a-data-feed"></a>Správa oprávnění datového kanálu

Přístup k pracovnímu prostoru je řízen prostředkem metrika metrik, který používá Azure Active Directory pro ověřování. Pro data metriky se použije jiná vrstva řízení oprávnění.

Poradce pro metriky umožňuje udělit oprávnění různým skupinám uživatelů v různých datových kanálech. Existují dva typy rolí: 

- Správce: kdo má úplná oprávnění ke správě datového kanálu, včetně možnosti upravit a odstranit.
- Viewer: kdo má přístup k zobrazení datového kanálu jen pro čtení.
 

## <a name="advanced-settings"></a>Rozšířená nastavení

Při vytváření nového datového kanálu je k dispozici několik volitelných upřesňujících nastavení, která je možné upravit na stránce s podrobnostmi o datovém kanálu.

### <a name="ingestion-options"></a>Možnosti ingestování

* **Časový posun** příjmu: ve výchozím nastavení jsou data přijímána podle zadané členitosti. Například metrika s *denním* časovým razítkem bude ingestovat jeden den po jeho časovém razítku. Posun můžete použít k prodlevě času *příjmu s kladným* číslem nebo k jeho posunutí se *záporným* číslem.

* **Maximální souběžnost**: Tento parametr nastavte, pokud váš zdroj dat podporuje omezené souběžnosti. V opačném případě ponechte výchozí nastavení.

* **Ukončit opakování po**: Pokud se příjem dat nezdařil, bude automaticky opakován během období. Začátek období je čas, kdy došlo k prvnímu příjmu dat. Délka období je definována podle členitosti. Pokud necháte výchozí hodnotu (-1), hodnota se určí podle členitosti.
    
    | Členitost       | Ukončit akci po           |
    | :------------ | :--------------- |
    | Denně, Custom (>= 1 den), týdně, měsíčně, ročně     | 7 dní          |
    | Každou hodinu, vlastní (< 1 den)       | 72 hodin |

* **Minimální interval opakování**: při pokusu o přijetí dat ze zdroje můžete zadat minimální interval. Pokud necháte výchozí hodnotu (-1), interval opakování se určí podle členitosti.
    
    | Členitost       | Minimální interval opakování           |
    | :------------ | :--------------- |
    | Denně, Custom (>= 1 den), týdně, měsíčně     | 30 minut          |
    | Každou hodinu, vlastní (< 1 den)      | 10 minut |
    | Ročně | 1 den          |
 
### <a name="fill-gap-when-detecting"></a>Při detekci vyplnit mezeru: 

> [!NOTE]
> Toto nastavení nebude mít vliv na váš zdroj dat a nebude mít vliv na datové grafy zobrazené na portálu. Automatické vyplňování probíhá pouze při detekci anomálií.

Některé časové řady nejsou průběžné. V případě chybějících datových bodů použije Poradce pro metriky zadanou hodnotu k jejich vyplnění před detekcí anomálií pro lepší přesnost.
Možnosti: 

* Použití hodnoty z předchozího skutečného datového bodu. To se používá ve výchozím nastavení.
* Použití konkrétní hodnoty.

### <a name="action-link-template"></a>Šablona odkazu na akci: 

Šablony odkazů na akce slouží k předdefinování akcí HTTP URL, které se skládají ze zástupných symbolů `%datafeed` , `%metric` ,, `%timestamp` `%detect_config` a `%tagset` . Tuto šablonu můžete použít k přesměrování z anomálie nebo incidentu na konkrétní adresu URL pro přechod k podrobnostem.

:::image type="content" source="../media/action-link-template.png" alt-text="Šablona odkazu na akci" lightbox="../media/action-link-template.png":::

Jakmile vyplníte odkaz akce, klikněte na **odkaz přejít na akci** v možnosti akce v seznamu incidentu a na místní nabídku stromu incidentů. Zástupné symboly v šabloně odkazu akce nahraďte odpovídajícími hodnotami anomálie nebo incidentu.

| Zástupný symbol | Příklady | Komentář |
| ---------- | -------- | ------- |
| `%datafeed` | - | ID datového kanálu |
| `%metric` | - | ID metriky |
| `%detect_config` | - | Zjistit ID konfigurace |
| `%timestamp` | - | Časové razítko anomálie nebo koncového času trvalého incidentu |
| `%tagset` | `%tagset`, <br> `[%tagset.get("Dim1")]`, <br> `[ %tagset.get("Dim1", "filterVal")]` | Hodnoty dimenze anomálie nebo nejvyšší anomálie incidentu.   <br> `filterVal`Slouží k odfiltrování vyhovujících hodnot v hranatých závorkách.   |

4.6

* Pokud je šablona odkazu akce `https://action-link/metric/%metric?detectConfigId=%detect_config` ,
  * Odkaz akce `https://action-link/metric/1234?detectConfigId=2345` by přešel na anomálie nebo incidenty v rámci metriky `1234` a zjišťování konfigurace `2345` .

* Pokud je šablona odkazu akce `https://action-link?[Dim1=%tagset.get('Dim1','')&][Dim2=%tagset.get('Dim2','')]` , 
    * Odkaz akce by byl v `https://action-link?Dim1=Val1&Dim2=Val2` době, kdy je anomálie `{ "Dim1": "Val1", "Dim2": "Val2" }` . 
    * Odkaz akce by byl v `https://action-link?Dim2=Val2` době, kdy je anomálie `{ "Dim1": "", "Dim2": "Val2" } ` , protože `[Dim1=***&]` se pro hodnotu dimenze v prázdném řetězci přeskočí. 

* Pokud je šablona odkazu akce `https://action-link?filter=[Name/Dim1 eq '%tagset.get('Dim1','')' and ][Name/Dim2 eq '%tagset.get('Dim2','')']` , 
    * Odkaz akce by byl v `https://action-link?filter=Name/Dim1 eq 'Val1' and Name/Dim2 eq 'Val2'` případě anomálií `{ "Dim1": "Val1", "Dim2": "Val2" }` , 
    * Odkaz na akci by byl v případě, že se `https://action-link?filter=Name/Dim2 eq 'Val2'` anomálie `{ "Dim1": "", "Dim2": "Val2" }` `[Name/Dim1 eq '***' and ]` vynechala pro prázdný řetězec hodnoty dimenze. 
   
### <a name="data-feed-not-available-alert-settings"></a>Nastavení upozornění "datový kanál není k dispozici"

Datový kanál se považuje za nedostupný, pokud se ze zdroje neuchovávají žádná data během období odkladu určeného v době, kdy se spustí ingestování datového kanálu. V tomto případě se aktivuje výstraha.

Chcete-li nakonfigurovat výstrahu, je třeba nejprve [vytvořit zavěšení](alerts.md#create-a-hook) . Výstrahy budou odesílány prostřednictvím nakonfigurovaného zavěšení.

* **Období odkladu**: nastavení období odkladu se používá k určení, kdy se má odeslat výstraha, pokud se ingestují žádné datové body. Referenční bod je čas první ingestování. Pokud dojde k chybě ingestování, Poradce při metrikách bude pokračovat v pravidelných intervalech určených členitosti. Pokud i nadále neproběhne po uplynutí období odkladu, bude odeslána výstraha.

* **Automatické odložení**: Pokud je tato možnost nastavená na hodnotu nula, každé časové razítko, *které není k dispozici* , vyvolá výstrahu. Pokud je zadáno nastavení jiné než nula, nebudou se po prvním časovém razítku, které *není k dispozici* , spouštět nepřetržitá časová razítka, a to podle zadaného nastavení.

## <a name="next-steps"></a>Další kroky
- [Konfigurace metrik a doladění konfigurace zjišťování](configure-metrics.md)
- [Úprava detekce anomálií pomocí zpětné vazby](anomaly-feedback.md)
- [Diagnostikujte incident](diagnose-incident.md).
