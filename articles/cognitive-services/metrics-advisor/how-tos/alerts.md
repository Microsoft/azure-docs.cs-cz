---
title: Konfigurace výstrah Poradce pro metriky
titleSuffix: Azure Cognitive Services
description: Jak nakonfigurovat výstrahy Poradce pro metriky pomocí háčků pro e-mail, web a Azure DevOps.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: a68622a921bb58513c7bb2154ccee81162a59c53
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936183"
---
# <a name="how-to-configure-alerts-and-get-notifications-using-a-hook"></a>Postupy: Konfigurace výstrah a získání oznámení pomocí zavěšení

Po zjištění anomálie nástrojem metriky se aktivuje upozornění na upozornění na základě nastavení výstrah, a to pomocí zavěšení. Nastavení výstrahy se dá použít s několika konfiguracemi detekce, k dispozici jsou různé parametry pro přizpůsobení pravidla upozornění.

## <a name="create-a-hook"></a>Vytvořit zavěšení

Poradce pro metriky podporuje tři různé typy zavěšení: zapojování e-mailů, Webhooku a Azure DevOps. Můžete zvolit ten, který bude fungovat pro konkrétní scénář. 

### <a name="email-hook"></a>E-mail háček

> [!Note]
> Správci prostředků Advisoru metriky musí nakonfigurovat **Nastavení e-mailu**a zadat informace související s protokolem SMTP do poradce metriky, než se můžou výstrahy na anomálii odeslat.

K vytvoření e-mailového zavěšení jsou k dispozici následující parametry: 

E-mail Hookem je kanál pro výstrahy anomálií, který se pošle e-mailovým adresám uvedeným v části **e-mail** . Odesílají se dva typy e-mailů s výstrahami: *informační kanál není k dispozici* a *sestavy incidentů* , které obsahují jednu nebo více anomálií. 

|Parametr |Popis  |
|---------|---------|
| Název | Název e-mailového zavěšení |
| E-mail na| E-mailové adresy, které by poslaly upozornění|
| Externí odkaz | Volitelné pole, které umožňuje přizpůsobené přesměrování, například při odstraňování poznámek. |
| Název vlastní výstrahy anomálií | Šablona nadpisu podporuje `${severity}` , `${alertSettingName}` , `${datafeedName}` , `${metricName}` , `${detectConfigName}` , `${timestamp}` , `${topDimension}` , `${incidentCount}` , `${anomalyCount}`

Po kliknutí na **OK**se vytvoří e-mailový háček. Můžete ji použít v jakémkoli nastavení výstrahy a získat tak výstrahy anomálií. 

### <a name="web-hook"></a>Webový Hook

> [!Note]
> * Použijte metodu **post** Request.
> * Text žádosti probíhal být podobný tomuto:  
    `{"timestamp":"2019-09-11T00:00:00Z","alertSettingGuid":"49635104-1234-4c1c-b94a-744fc920a9eb"}`
> * Při vytvoření nebo úpravě webového zavěšení se rozhraní API zavolá jako test s prázdným textem žádosti. Vaše rozhraní API musí vracet kód HTTP 200.

Webhook je vstupním bodem pro všechny informace, které jsou k dispozici ve službě Advisory metriky, a při aktivaci výstrahy volá rozhraní API poskytnuté uživatelem. Všechny výstrahy je možné odeslat prostřednictvím webového zavěšení.

Chcete-li vytvořit Webhook, budete muset přidat následující informace:

|Parametr |Popis  |
|---------|---------|
|Koncový bod     | Adresa rozhraní API, která se má volat při aktivaci výstrahy.        |
|Uživatelské jméno a heslo | Pro ověřování na adresu rozhraní API. Tuto černou položku nechte v případě, že není vyžadováno ověřování.         |
|Záhlaví     | Vlastní hlavičky v volání rozhraní API.        |

:::image type="content" source="../media/alerts/create-web-hook.png" alt-text="okno pro vytvoření webového zavěšení.":::

Při odesílání oznámení prostřednictvím Webhooku můžete pomocí následujících rozhraní API získat podrobnosti o výstraze. Ve službě API nastavte *časové razítko* a *alertSettingGuid* , které se přidávají do, pak použijte následující dotazy: 
- `query_alert_result_anomalies`
- `query_alert_result_incidents`

### <a name="azure-devops"></a>Azure DevOps

Poradce pro metriky také podporuje automatické vytvoření pracovní položky v Azure DevOps ke sledování problémů a chyb, když se zjistila kterákoli anomálie. Všechny výstrahy je možné odesílat prostřednictvím DevOpsch zavěšení Azure.

Pokud chcete vytvořit službu Azure DevOps Hook, budete muset přidat následující informace.

|Parametr |Popis  |
|---------|---------|
| Název | Název zavěšení |
| Organizace | Organizace, do které patří váš DevOps |
| Project | Konkrétní projekt v DevOps. |
| Token přístupu |  Token pro ověřování pro DevOps. | 

> [!Note]
> Musíte udělit oprávnění k zápisu, pokud chcete, aby Poradce pro metriky vytvořil pracovní položky na základě upozornění na anomálie. Po vytvoření se háky můžete použít v libovolném z nastavení výstrah. Spravujte své háky na stránce **nastavení zavěšení** .

## <a name="add-or-edit-alert-settings"></a>Přidat nebo upravit nastavení výstrah

Přejít na stránku podrobností metriky, kde najdete část **Nastavení výstrah** v levém dolním rohu stránky s podrobnostmi metrik. Obsahuje seznam všech nastavení výstrah, která se vztahují na vybranou konfiguraci zjišťování. Při vytvoření nové konfigurace detekce není k dispozici žádné nastavení výstrahy a nebudou odeslány žádné výstrahy.  
K úpravě nastavení výstrah můžete použít ikony **Přidat**, **Upravit** a **Odstranit** .

:::image type="content" source="../media/alerts/alert-setting.png" alt-text="Položka nabídky nastavení výstrah":::

Kliknutím na tlačítka **Přidat** nebo **Upravit** zobrazíte okno pro přidání nebo úpravu nastavení výstrah.

:::image type="content" source="../media/alerts/edit-alert.png" alt-text="Přidat nebo upravit nastavení výstrah":::

**Název nastavení výstrahy**: název tohoto nastavení výstrahy. Zobrazí se v nadpisu e-mailu s výstrahou.

**Zavěšení**: seznam háčků, na které se mají odesílat výstrahy.

Oddíl označený na snímku obrazovky výše je nastavení pro zjišťování konfigurace. Můžete nastavit různá nastavení výstrah pro různé konfigurace detekce. Vyberte cílovou konfiguraci pomocí třetího rozevíracího seznamu v tomto okně. 

### <a name="filter-settings"></a>Nastavení filtru 

Níže jsou uvedené nastavení filtru pro jednu konfiguraci detekce.

**Výstraha pro** má 4 možnosti pro filtrování anomálií:

* **Anomálie ve všech řadách**: všechny anomálie budou zahrnuty do výstrahy.         
* **Anomálie ve skupině řad**: filtrovat řady podle hodnot dimenzí. Nastavte konkrétní hodnoty pro některé dimenze. Anomálie budou zahrnuty do výstrahy pouze v případě, že řada odpovídá zadané hodnotě.       
* **Anomálie v oblíbených řadách**: v této výstraze bude obsažena pouze řada označená jako oblíbená.        |
* **Anomálie v horní části N všech řad**: Tento filtr je určen pro případ, že se o řadě, jejichž hodnota je v horní části n, zajímá. Podíváme se na některá časová razítka a zkontrolujeme, jestli je hodnota řady v tomto časovém razítku v horní části N. Pokud je počet "v horním n" větší než zadané číslo, bude tato anomálie obsažena v upozornění.        |

**Možnost filtrovat anomálie** je dalším filtrem s následujícími možnostmi:

- **závažnost** : anomálie bude zahrnutá jenom v případě, že závažnost anomálií spadá do zadaného rozsahu.
- **Připomenout** : dočasně zastavit výstrahy pro anomálie v dalších N bodech (tečka), když se aktivuje v rámci výstrahy.
    - **typ odložení** : když se nastaví na **řady**, aktivovaná anomálie odsadí jenom svou řadu. V případě **metriky**bude jedna aktivovaná anomálií odložit všechny řady v této metrikě.
    - **číslo odložení** : počet bodů (period), které se mají znovu připomenout.
    - **resetovat po neúspěšné** : Pokud je vybraná, aktivovaná anomálie se odsadí jenom u dalších n úspěšných anomálií. Pokud jeden z následujících datových bodů není anomálií, odložení bude od tohoto bodu resetováno. Pokud není vybraná, jedna z aktivovaných anomálií bude připomenout další n bodů (tečka), a to i v případě, že po sobě jdoucí datové body neanomálií.
- **hodnota** (volitelné): filtrovat podle hodnoty. Budou zahrnuty pouze hodnoty bodů, které splňují podmínku, anomálie. Pokud použijete odpovídající hodnotu jiné metriky, názvy dimenzí obou metrik by měly být konzistentní.

Anomálie, které nejsou filtrovány, se odešlou v upozornění.

### <a name="add-cross-metric-settings"></a>Přidat nastavení křížové metriky

Kliknutím na **+ Přidat nastavení křížové metriky** na stránce nastavení výstrahy přidejte další oddíl.

Selektor **operátoru** je logický vztah jednotlivých sekcí, aby bylo možné určit, zda odesílají výstrahy.


|Operátor  |Popis  |
|---------|---------|
|AND     | Odeslat výstrahu pouze v případě, že řada odpovídá jednotlivým částem výstrahy a všechny datové body jsou anomálie. Pokud mají metriky různé názvy dimenzí, výstraha se nikdy neaktivuje.         |
|NEBO     | Odeslat výstrahu, pokud alespoň jedna část obsahuje anomálie.         |

:::image type="content" source="../media/alerts/alert-setting-operator.png" alt-text="Operator pro více oddílů nastavení výstrahy":::

## <a name="next-steps"></a>Další kroky

- [Úprava detekce anomálií pomocí zpětné vazby](anomaly-feedback.md)
- [Diagnostikujte incident](diagnose-incident.md).
- [Konfigurace metrik a doladění konfigurace zjišťování](configure-metrics.md)