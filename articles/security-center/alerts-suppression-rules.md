---
title: Pomocí pravidel pro potlačení výstrah můžete potlačit falešně pozitivní nebo jiné nepotřebné výstrahy zabezpečení v Azure Security Center.
description: Tento článek vysvětluje, jak používat pravidla potlačení Azure Security Center ke skrytí upozornění na nechtěné zabezpečení.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 09/10/2020
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: 12cc2de6cd1a8e9ddf40b358c94b720b8b2cf594
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920439"
---
# <a name="suppress-alerts-from-azure-defender"></a>Potlačit výstrahy z Azure Defenderu

Tato stránka vysvětluje, jak můžete pomocí pravidel potlačení výstrah potlačit falešně pozitivní nebo jiné nechtěné výstrahy zabezpečení z Azure Defenderu.

## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Obecná dostupnost (GA)|
|Stanov|Free<br>(Většina výstrah zabezpečení je dostupná jenom v Azure Defenderu.)|
|Požadované role a oprávnění:|**Správce zabezpečení** a **vlastník** můžou pravidla vytvářet a odstraňovat.<br>**Čtenáři zabezpečení** a **čtenáři** můžou zobrazit pravidla.|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![Yes](./media/icons/yes-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||


## <a name="what-are-suppression-rules"></a>Jaká jsou pravidla pro potlačení?

Různé plány Azure Defenderu zjišťují hrozby v libovolné oblasti prostředí a generují výstrahy zabezpečení.

Pokud není jedna výstraha zajímavá nebo nerelevantní, můžete ji ručně zavřít. Případně můžete pomocí funkce pravidla potlačení automaticky v budoucnu odstranit podobné výstrahy. Obvykle byste pravidlo potlačení používali k těmto akcím:

- Potlačit výstrahy, které jste identifikovali jako falešně pozitivní

- Potlačit výstrahy, které se spouštějí příliš často, aby byly užitečné

Pravidla potlačení definují kritéria, pro která se mají automaticky odkládat výstrahy.

> [!CAUTION]
> Potlačení výstrah zabezpečení snižuje efektivitu ochrany před hrozbami v Azure Defenderu. Měli byste pečlivě zkontrolovat potenciální dopad na jakékoli pravidlo potlačení a monitorovat ho v průběhu času.

:::image type="content" source="./media/alerts-suppression-rules/create-suppression-rule.gif" alt-text="Vytvořit pravidlo pro potlačení výstrah":::

## <a name="create-a-suppression-rule"></a>Vytvoření pravidla potlačení

Pravidla pro potlačení nechtěných upozornění zabezpečení můžete vytvořit několika způsoby:

- Pokud chcete potlačit upozornění na úrovni skupiny pro správu, použijte službu Azure Policy.
- Pokud chcete potlačit upozornění na úrovni předplatného, můžete použít Azure Portal nebo rozhraní REST API, jak je vysvětleno níže.

Pravidla potlačení můžou odpustit jenom výstrahy, které už se aktivovaly ve vybraných předplatných.

Postup vytvoření pravidla přímo v Azure Portal:

1. V horní části stránky Upozornění zabezpečení služby Security Center

    - Vyhledejte konkrétní výstrahu, kterou už nechcete vidět, a z nabídky se třemi tečkami (...) pro výstrahu vyberte **vytvořit pravidlo potlačení**:

        [![* * Vytvořit pravidlo potlačení * * možnost](media/alerts-suppression-rules/auto-dismiss-future-option.png)](media/alerts-suppression-rules/auto-dismiss-future-option.png#lightbox)

    - Případně vyberte odkaz **pravidla potlačení** v horní části stránky a na stránce Pravidla potlačení vyberte **vytvořit nové pravidlo potlačení**:

        ![Tlačítko vytvořit nové pravidlo potlačení * *](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. V podokně nové pravidlo potlačení zadejte podrobnosti nového pravidla.
    - Vaše pravidlo může oznámení zavřít na **všech prostředcích** , takže v budoucnu už nebudete mít žádné výstrahy.     
    - Vaše pravidlo může upozornění **na konkrétní kritéria** zavřít – Pokud se týká konkrétní IP adresy, názvu procesu, uživatelského účtu, prostředku Azure nebo umístění.

    > [!TIP]
    > Pokud jste otevřeli stránku Nová pravidla z konkrétní výstrahy, výstraha a předplatné se automaticky nakonfigurují v novém pravidle. Pokud jste použili odkaz **vytvořit nové pravidlo potlačení** , vybrané odběry budou odpovídat aktuálnímu filtru na portálu.

    [![Podokno Vytvoření pravidla potlačení](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)
1. Zadejte podrobnosti o pravidle:
    - **Název** – název pravidla. Názvy pravidel musí začínat písmenem nebo číslicí, mít 2 až 50 znaků a nesmí obsahovat žádné jiné symboly než spojovníky (-) a podtržítka (_). 
    - **Stav** -povoleno nebo zakázáno.
    - **Důvod** – vyberte jeden z předdefinovaných důvodů nebo ' jiné ', pokud nevyhovují vašim potřebám.
    - **Datum vypršení platnosti** – datum a čas ukončení pravidla. Pravidla můžou platit až šest měsíců.
1. Volitelně můžete otestovat pravidlo pomocí tlačítka **simulovat** a zjistit, kolik výstrah by bylo vykázalo, pokud bylo toto pravidlo aktivní.
1. Uložte pravidlo. 


## <a name="edit-a-suppression-rules"></a>Úprava pravidel potlačení

Pokud chcete upravit pravidla, která jste vytvořili, použijte stránku pravidla potlačení.

1. Na stránce výstrahy zabezpečení Security Center v horní části stránky vyberte odkaz **pravidla potlačení** .
1. Otevře se stránka pravidla potlačení se všemi pravidly pro vybraná předplatná.

    [![Seznam pravidel potlačení](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. Chcete-li upravit jedno pravidlo, otevřete nabídku se třemi tečkami (...) pro pravidlo a vyberte **Upravit**.
1. Proveďte potřebné změny a vyberte **použít**. 

## <a name="delete-a-suppression-rule"></a>Odstranění pravidla potlačení

Pokud chcete odstranit jedno nebo více pravidel, která jste vytvořili, použijte stránku pravidla potlačení.

1. Na stránce výstrahy zabezpečení Security Center v horní části stránky vyberte odkaz **pravidla potlačení** .
1. Otevře se stránka pravidla potlačení se všemi pravidly pro vybraná předplatná.
1. Pokud chcete odstranit jedno pravidlo, otevřete nabídku se třemi tečkami (...) pro pravidlo a vyberte **Odstranit**.
1. Pokud chcete odstranit víc pravidel, zaškrtněte políčka pro pravidla, která se mají odstranit, a vyberte **Odstranit**.
    ![Odstranění jednoho nebo více pravidel potlačení](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="view-suppressed-alerts"></a>Zobrazit potlačené výstrahy

Budou se vygenerovat i výstrahy, které odpovídají povoleným pravidlům potlačení, ale jejich **stav se nastaví** na zastaveno. Můžete zobrazit stav v Azure Portal, nebo máte přístup k výstrahám zabezpečení Security Center. 

> [!TIP]
> Pro potlačené výstrahy [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) nevytvoří incidenty. U jiných systémů Siem můžete filtrovat potlačitelné výstrahy pomocí stavu výstrahy (zastaveno).

Pomocí filtru Security Center můžete zobrazit výstrahy, které vaše pravidla vystavila.

* Na stránce výstrahy zabezpečení Security Center otevřete možnosti filtru a vyberte možnost **zavřeno**.  

   [![Zobrazení odeslaných výstrah](media/alerts-suppression-rules/view-dismissed-alerts.png)](media/alerts-suppression-rules/view-dismissed-alerts.png#lightbox)


## <a name="create-and-manage-suppression-rules-with-the-api"></a>Vytváření a Správa pravidel potlačení pomocí rozhraní API

Pravidla pro potlačení výstrah můžete vytvořit, zobrazit nebo odstranit pomocí REST API Security Center. 

Příslušné metody HTTP pro pravidla potlačení v REST API jsou:

- **Put**: pro vytvoření nebo aktualizaci pravidla potlačení v zadaném předplatném.

- **Získat**:

    - K vypsání všech pravidel nakonfigurovaných pro zadané předplatné. Tato metoda vrací pole příslušných pravidel.

    - Získat podrobnosti o konkrétním pravidle v zadaném předplatném. Tato metoda vrací jedno pravidlo potlačení.

    - Chcete-li simulovat dopad pravidla potlačení, stále ve fázi návrhu. Toto volání identifikuje, který z vašich stávajících výstrah by byl vypnutý, pokud bylo pravidlo aktivní.

- **Odstranit**: odstraní stávající pravidlo (ale nemění stav výstrah, které mu už zavřelo).

Úplné podrobnosti a příklady použití najdete v [dokumentaci k rozhraní API](/rest/api/securitycenter/). 


## <a name="next-steps"></a>Další kroky

Tento článek popisuje pravidla potlačení v Azure Security Center, která automaticky odpustit nechtěné výstrahy.

Další informace o výstrahách zabezpečení v Azure Defenderu najdete na následujících stránkách:

- [Výstrahy zabezpečení a dezaktivační řetěz záměru](alerts-reference.md) – referenční příručka k výstrahám zabezpečení, které můžete získat od Azure Defenderu.