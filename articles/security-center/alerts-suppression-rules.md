---
title: Pomocí pravidel pro potlačení výstrah můžete potlačit falešně pozitivní nebo jiné nepotřebné výstrahy zabezpečení v Azure Security Center.
description: Tento článek vysvětluje, jak používat pravidla potlačení Azure Security Center ke skrytí upozornění na nechtěné zabezpečení.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 05/04/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: 5a5d6a96bd73a67c2611e7f334eba3daeda031ba
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007697"
---
# <a name="suppressing-alerts-from-azure-security-centers-threat-protection"></a>Potlačení výstrah z ochrany před hrozbami Azure Security Center

Tato stránka vysvětluje, jak můžete pomocí pravidel potlačení výstrah potlačit falešně pozitivní nebo jiné nechtěné výstrahy zabezpečení v Azure Security Center.

## <a name="availability"></a>Dostupnost

- Stav verze: verze **Preview**
- Požadované role: Správce zabezpečení a vlastník můžou pravidla vytvořit nebo odstranit. Čtenáři zabezpečení a čtenáři můžou zobrazit pravidla.
- Cloudy: vše (globální, národní, státní správa a Svrchovan)


## <a name="introduction-to-suppression-rules"></a>Seznámení s pravidly potlačení

Součásti ochrany před hrozbami pro Azure Security Center zjišťují hrozby v jakékoli oblasti prostředí a generují výstrahy zabezpečení.

Pokud není jedna výstraha zajímavá nebo nerelevantní, můžete ji ručně zavřít. Případně můžete pomocí funkce pravidla potlačení automaticky v budoucnu odstranit podobné výstrahy. Obvykle byste pravidlo potlačení používali k těmto akcím:

- potlačit výstrahy, které jste identifikovali jako falešně pozitivní

- potlačit výstrahy, které se spouštějí příliš často, aby byly užitečné

Pravidla potlačení definují kritéria, pro která se mají automaticky odkládat výstrahy.

> [!CAUTION]
> Potlačení výstrah zabezpečení snižuje ochranu před hrozbami Security Center. Měli byste pečlivě zkontrolovat potenciální dopad na jakékoli pravidlo potlačení a monitorovat ho v průběhu času.

[![Stránka Azure Security Center výstrahy zabezpečení s možnostmi potlačení výstrah](media/alerts-suppression-rules/alerts-screen-with-options.png)](media/alerts-suppression-rules/alerts-screen-with-options.png#lightbox)

## <a name="creating-a-suppression-rule"></a>Vytvoření pravidla potlačení

Existuje několik způsobů, jak můžete vytvořit pravidla pro potlačení upozornění na nechtěné zabezpečení:

- Chcete-li potlačit výstrahy na úrovni skupiny pro správu, použijte Azure Policy

- Chcete-li potlačit výstrahy na úrovni předplatného, můžete použít Azure Portal nebo REST API, jak je vysvětleno níže.

Pravidla potlačení můžou odpustit jenom výstrahy, které už se aktivovaly ve vybraných předplatných.

Postup vytvoření pravidla přímo v Azure Portal:

1. Stránka výstrahy zabezpečení Security Center:

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

1. Zadejte podrobnosti pravidla:

    - **Název** – název pravidla. Názvy pravidel musí začínat písmenem nebo číslicí, být mezi 2 a 50 znaky a nesmí obsahovat žádné symboly jiné než spojovníky (-) nebo podtržítka (_). 
    - **Stav** -povoleno nebo zakázáno.
    - **Důvod** – vyberte jeden z předdefinovaných důvodů nebo ' jiné ', pokud nevyhovují vašim potřebám.
    - **Datum vypršení platnosti** – datum a čas ukončení pravidla. Pravidla mohou běžet až po dobu šesti měsíců.

1. Volitelně můžete otestovat pravidlo pomocí tlačítka **simulovat** a zjistit, kolik výstrah by bylo vykázalo, pokud bylo toto pravidlo aktivní.

1. Uložte pravidlo. 

## <a name="editing-suppression-rules"></a>Úprava pravidel potlačení

Pokud chcete upravit pravidla, která jste vytvořili, použijte stránku pravidla potlačení.

1. Na stránce výstrahy zabezpečení Security Center v horní části stránky vyberte odkaz **pravidla potlačení** .

1. Na stránce Pravidla potlačení se otevře seznam všech dostupných pravidel podle aktuálně vybraných předplatných. 

    [![Seznam pravidel potlačení](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. Chcete-li upravit jedno pravidlo, otevřete nabídku se třemi tečkami (...) pro pravidlo a vyberte **Upravit**.

1. Proveďte potřebné změny a vyberte **použít**. 

## <a name="deleting-suppression-rules"></a>Odstraňují se pravidla potlačení.

Pokud chcete odstranit jedno nebo více pravidel, která jste vytvořili, použijte stránku pravidla potlačení.

1. Na stránce výstrahy zabezpečení Security Center v horní části stránky vyberte odkaz **pravidla potlačení** .

1. Na stránce Pravidla potlačení se otevře seznam všech dostupných pravidel podle aktuálně vybraných předplatných. 

1. Pokud chcete odstranit jedno pravidlo, otevřete nabídku se třemi tečkami (...) pro pravidlo a vyberte **Odstranit**.

1. Pokud chcete odstranit víc pravidel, zaškrtněte políčka pro pravidla, která se mají odstranit, a vyberte **Odstranit**.

    ![Odstranění jednoho nebo více pravidel potlačení](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="viewing-alerts-that-have-been-suppressed"></a>Zobrazení výstrah, které byly potlačeny

Budou se vygenerovat i výstrahy, které odpovídají povoleným pravidlům potlačení, ale jejich **stav se nastaví**na zastaveno. Můžete zobrazit stav v Azure Portal, nebo máte přístup k výstrahám zabezpečení Security Center. 

> [!TIP]
> Pro potlačené výstrahy [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) nevytvoří incidenty. U jiných systémů Siem můžete filtrovat potlačitelné výstrahy pomocí stavu výstrahy (zastaveno).

Pomocí filtru Security Center můžete zobrazit výstrahy, které vaše pravidla vystavila.

* Na stránce výstrahy zabezpečení Security Center otevřete možnosti filtru a vyberte možnost **zavřeno**.  

   [![Zobrazení odeslaných výstrah](media/alerts-suppression-rules/view-dismissed-alerts.png)](media/alerts-suppression-rules/view-dismissed-alerts.png#lightbox)


## <a name="using-the-api-to-create-and-manage-suppression-rules"></a>Vytvoření a Správa pravidel potlačení pomocí rozhraní API

Pravidla pro potlačení výstrah můžete vytvořit, zobrazit nebo odstranit pomocí REST API Security Center. 

Příslušné metody HTTP pro pravidla potlačení v REST API jsou:

- **Put**: pro vytvoření nebo aktualizaci pravidla potlačení v zadaném předplatném.

- **Získat**:

    - K vypsání všech pravidel nakonfigurovaných pro zadané předplatné. Tato metoda vrací pole příslušných pravidel.

    - Získat podrobnosti o konkrétním pravidle v zadaném předplatném. Tato metoda vrací jedno pravidlo potlačení.

    - Chcete-li simulovat dopad pravidla potlačení, stále ve fázi návrhu. Toto volání identifikuje, který z vašich stávajících výstrah by byl vypnutý, pokud bylo pravidlo aktivní.

- **Odstranit**: odstraní stávající pravidlo (ale nemění stav výstrah, které mu už zavřelo).

Úplné podrobnosti a příklady použití najdete v [dokumentaci k rozhraní API](https://docs.microsoft.com/api/securitycenter/). 


## <a name="next-steps"></a>Další kroky

Tento článek popisuje pravidla potlačení v Azure Security Center, která automaticky odpustit nechtěné výstrahy.

Další informace o výstrahách zabezpečení v Azure Security Center najdete na následujících stránkách:

- [Výstrahy zabezpečení a dezaktivační řetěz záměru](alerts-reference.md) – referenční příručka pro výstrahy zabezpečení, které se mohou zobrazit v modulu ochrany před internetovými útoky Azure Security Center.
- [Ochrana před hrozbami v Azure Security Center](threat-protection.md) – popis mnoha aspektů prostředí monitorovaných modulem ochrany před internetovými útoky v Azure Security Center.