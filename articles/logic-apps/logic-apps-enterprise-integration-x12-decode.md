---
title: Dekódovat zprávy X12
description: Ověřte EDI a generujte potvrzení pomocí dekodéru zpráv X12 v Azure Logic Apps s Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 918516a5629f8570d54c641ffc29f2367937266f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "74792371"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Dekódování zpráv X12 v Azure Logic Apps pomocí Enterprise Integration Packu

Pomocí konektoru pro dekódování zpráv X12 můžete ověřit obálku vůči smlouvě s obchodním partnerem, ověřit vlastnosti specifické pro EDI a partnera, rozdělit výměnu na sady transakcí nebo zachovat celé výměny a generovat potvrzení pro zpracované transakce. Pokud chcete tento konektor použít, musíte ho přidat do existující aktivační události v aplikaci logiky.

## <a name="before-you-start"></a>Než začnete

Tady jsou položky, které potřebujete:

* Účet Azure; Můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free) .
* [Účet pro integraci](logic-apps-enterprise-integration-create-integration-account.md) , který je už definovaný a přidružený k vašemu předplatnému Azure. Abyste mohli použít dekódovaný konektor zpráv X12, musíte mít účet pro integraci.
* Alespoň dva [partneři](logic-apps-enterprise-integration-partners.md) , kteří jsou již definováni v účtu integrace
* [Smlouva X12](logic-apps-enterprise-integration-x12.md) , která je už definovaná v účtu integrace

## <a name="decode-x12-messages"></a>Dekódovat zprávy X12

1. [Vytvořte aplikaci logiky](quickstart-create-first-logic-app-workflow.md).

2. Dekódovaný konektor zpráv X12 neobsahuje triggery, takže musíte přidat Trigger pro spuštění aplikace logiky, jako je Trigger žádosti. V návrháři aplikace logiky přidejte Trigger a pak přidejte akci do aplikace logiky.

3.  Do vyhledávacího pole zadejte "X12" pro svůj filtr. Vyberte **X12-dekódovat X12 zprávu**.
   
    ![Vyhledejte "X12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Pokud jste dříve nevytvořili žádná připojení k účtu pro integraci, budete vyzváni k vytvoření tohoto připojení nyní. Zadejte název připojení a vyberte účet pro integraci, který chcete připojit. 

    ![Zadat podrobnosti připojení účtu pro integraci](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Jsou vyžadovány vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název připojení. |
    | Účet pro integraci * |Zadejte název účtu pro integraci. Ujistěte se, že váš účet pro integraci a aplikace logiky jsou ve stejném umístění Azure. |

5.  Až budete hotovi, vaše podrobnosti o připojení by měly vypadat podobně jako v tomto příkladu. Chcete-li dokončit vytváření připojení, klikněte na tlačítko **vytvořit**.
   
    ![podrobnosti připojení účtu pro integraci](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Po vytvoření připojení, jak je znázorněno v tomto příkladu, vyberte zprávu X12 Flat File, která se má dekódovat.

    ![vytvořilo se připojení k účtu pro integraci.](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Například:

    ![Pro dekódování vybrat zprávu plochý soubor X12](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > Skutečný nebo chybný obsah zprávy nebo datové části pro pole zpráv je kódovaný v kódování Base64. Proto je nutné zadat výraz, který tento obsah zpracovává.
   > Tady je příklad, který zpracovává obsah jako XML, který můžete zadat v zobrazení kódu nebo pomocí Tvůrce výrazů v návrháři.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Příklad obsahu](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>Podrobnosti dekódování X12

Konektor dekódování X12 provádí tyto úlohy:

* Ověří obálku proti smlouvě s obchodním partnerem.
* Ověří vlastnosti EDI a pro konkrétní partnery.
  * Strukturální ověřování EDI a rozšířené ověřování schématu
  * Ověření struktury obálky výměny.
  * Ověřování schématu obálky na základě schématu ovládacího prvku
  * Ověřování schématu datových prvků pro transakci sady pro schéma zprávy
  * Ověřování EDI provedené v datových prvcích sady transakcí 
* Ověřuje, že čísla pro výměnu, skupinu a řízení sady transakcí nejsou duplicitní.
  * Kontroluje kontrolní číslo výměny proti dříve přijatým změnám.
  * Kontroluje číslo řízení skupiny proti jiným řídicím číslům skupiny ve výměně.
  * Kontroluje kontrolní číslo sady transakcí s jinými čísly řízení sady transakcí v této skupině.
* Rozdělí výměnu na sady transakcí nebo zachová celý výměnu:
  * Rozdělit výměnu jako sady transakcí – pozastavit sady transakcí při chybě: rozdělí výměnu na sady transakcí a analyzuje každou sadu transakcí. 
  Akce dekódování X12 výstupuje pouze ty sady transakcí, které selžou při ověřování `badMessages` , a výstup zbývajících transakcí nastaví na `goodMessages` .
  * Rozdělit výměnu jako sady transakcí – pozastavit výměnu při chybě: rozděluje výměnu do sad transakcí a analyzuje každou sadu transakcí. 
  Pokud jedna nebo více transakcí v rámci výměny selže, akce dekódování X12 vypíše všechny sady transakcí v tomto přenosu do `badMessages` .
  * Zachovat výměnu – pozastavit sady transakcí při chybě: zachovejte výměnu a zpracujte celý dávkový vydaný výměnný. 
  Akce dekódování X12 výstupuje pouze ty sady transakcí, které selžou při ověřování `badMessages` , a výstup zbývajících transakcí nastaví na `goodMessages` .
  * Zachovat výměnu – pozastavit výměnu při chybě: zachovejte výměnu a zpracujte celý dávkový výměnný. 
  Pokud jedna nebo více transakcí v rámci výměny selže, akce dekódování X12 vypíše všechny sady transakcí v tomto přenosu do `badMessages` . 
* Generuje technické nebo funkční potvrzení (Pokud je nakonfigurováno).
  * Technické potvrzení vygeneruje výsledek ověřování hlaviček. Technické potvrzení oznamuje stav zpracování záhlaví a přípojného vozidla pro přijímač adres.
  * Funkční potvrzení generuje výsledek ověření těla. Funkční potvrzení oznamuje každou chybu, ke které došlo při zpracování přijatého dokumentu.

## <a name="view-the-swagger"></a>Zobrazit Swagger
Podívejte se na [Podrobnosti Swagger](/connectors/x12/). 

## <a name="next-steps"></a>Další kroky
[Další informace o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informace o Enterprise Integration Pack") 

