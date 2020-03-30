---
title: Dekódování zpráv X12
description: Ověření EDI a generování potvrzení pomocí dekodéru zpráv X12 v aplikacích Azure Logic Apps s podnikovou integrací Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 918516a5629f8570d54c641ffc29f2367937266f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792371"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Dekódování zpráv X12 v aplikacích Azure Logic Apps pomocí sady Enterprise Integration Pack

Pomocí konektoru zpráv Decode X12 můžete obálku ověřit proti smlouvě s obchodním partnerem, ověřit vlastnosti EDI a specifické pro partnera, rozdělit mezizměny na sady transakcí nebo zachovat celé mimoúrovňové křižovatky a generovat potvrzení pro zpracované transakce. Chcete-li použít tento konektor, musíte přidat konektor do existující aktivační události v aplikaci logiky.

## <a name="before-you-start"></a>Než začnete

Zde jsou položky, které potřebujete:

* Účet Azure; můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* [Účet integrace,](logic-apps-enterprise-integration-create-integration-account.md) který už je definovaný a přidružený k vašemu předplatnému Azure. Chcete-li použít konektor zprávy Decode X12, musíte mít účet integrace.
* Alespoň dva [partneři,](logic-apps-enterprise-integration-partners.md) kteří jsou již definováni ve vašem integračním účtu
* [Smlouva X12,](logic-apps-enterprise-integration-x12.md) která je již definována ve vašem integračním účtu

## <a name="decode-x12-messages"></a>Dekódování zpráv X12

1. [Vytvořte aplikaci logiky](quickstart-create-first-logic-app-workflow.md).

2. Konektor zprávy Decode X12 nemá aktivační události, takže je nutné přidat aktivační událost pro spuštění aplikace logiky, jako je aktivační událost požadavku. V Návrháři aplikace logiky přidejte aktivační událost a pak přidejte akci do aplikace logiky.

3.  Do vyhledávacího pole zadejte pro filtr "x12". Vyberte **X12 - Dekódovat zprávu X12**.
   
    ![Hledat "x12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Pokud jste dříve nevytvořili žádná připojení ke svému účtu integrace, budete vyzváni k vytvoření tohoto připojení nyní. Pojmenujte připojení a vyberte účet integrace, který chcete připojit. 

    ![Poskytnutí podrobností o připojení účtu integrace](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Jsou vyžadovány vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název připojení. |
    | Integrační účet * |Zadejte název účtu integrace. Ujistěte se, že váš účet integrace a aplikace logiky jsou ve stejném umístění Azure. |

5.  Po dokončení by měly vypadat podobně jako v tomto příkladu podrobnosti o připojení. Chcete-li dokončit vytváření připojení, zvolte **Vytvořit**.
   
    ![podrobnosti o připojení účtu integrace](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Po vytvoření připojení, jak je znázorněno v tomto příkladu, vyberte zprávu o plochém souboru X12, kterou chcete dekódovat.

    ![vytvořeno připojení účtu integrace](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Například:

    ![Vyberte zprávu o plochém souboru X12 pro dekódování.](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > Skutečný obsah zprávy nebo datové části pro pole zpráv, dobré nebo špatné, je base64 kódován. Proto je nutné zadat výraz, který zpracovává tento obsah.
   > Zde je příklad, který zpracovává obsah jako XML, který můžete zadat v zobrazení kódu nebo pomocí tvůrce výrazů v návrháři.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Příklad obsahu](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>X12 Dekódovat detaily

Konektor x12 dekódování provádí tyto úkoly:

* Ověří obálku proti dohodě obchodního partnera.
* Ověří EDI a vlastnosti specifické pro partnera.
  * Edi strukturální validace a rozšířené ověření schématu
  * Validace struktury výměnné obálky.
  * Ověření schématu obálky proti schématu ovládacího prvku.
  * Ověření schématu datových prvků sady transakcí proti schématu zprávy.
  * Ověření EDI provedené u datových prvků sady transakcí 
* Ověří, zda kontrolní čísla sady mezibankovních, skupinových a transakčních sad nejsou duplicitní.
  * Zkontroluje kontrolní číslo výměny oproti dříve přijatým mimoúrovňové křižovatkám.
  * Zkontroluje kontrolní číslo skupiny proti jiným kontrolním číslům skupiny ve výměně.
  * Zkontroluje číslo ovládacího prvku sady transakcí oproti jiným řídicím číslům sady transakcí v této skupině.
* Rozdělí výměnu na sady transakcí nebo zachová celou výměnu:
  * Rozdělit výměnu jako sady transakcí - pozastavit sady transakcí na chybu: Rozdělí mezibankovní na sady transakcí a analyzuje každou sadu transakcí. 
  Akce Decode X12 vydezinuje `badMessages`pouze ty sady transakcí, které `goodMessages`neuspějí ověření na a výstupy zbývajících transakcí nastaví na .
  * Rozdělit výměnu jako sady transakcí - pozastavit výměnu při chybě: Rozdělí výměnu na sady transakcí a analyzuje každou sadu transakcí. 
  Pokud jedna nebo více sad transakcí v mezibankovní majek nezdaří ověření, X12 `badMessages`Decode akce výstupy všechny sady transakcí v této výměně na .
  * Zachovat výměnu - pozastavit sady transakcí při chybě: Zachovat výměnu a zpracovat celou dávkovou výměnu. 
  Akce Decode X12 vydezinuje `badMessages`pouze ty sady transakcí, které `goodMessages`neuspějí ověření na a výstupy zbývajících transakcí nastaví na .
  * Zachovat výměnu - pozastavit výměnu při chybě: Zachovat výměnu a zpracovat celou dávkovou výměnu. 
  Pokud jedna nebo více sad transakcí v mezibankovní majek nezdaří ověření, X12 `badMessages`Decode akce výstupy všechny sady transakcí v této výměně na . 
* Generuje technické a/nebo funkční potvrzení (pokud je nakonfigurováno).
  * Technické potvrzení generuje v důsledku ověření záhlaví. Technické potvrzení hlásí stav zpracování výměnné hlavičky a přípojného vozidla příjemcem adresy.
  * Funkční potvrzení generuje v důsledku ověření těla. Funkční potvrzení hlásí každou chybu, ke které došlo při zpracování přijatého dokumentu

## <a name="view-the-swagger"></a>Zobrazit naparování
Podívejte se na [podrobnosti o chvástání](/connectors/x12/). 

## <a name="next-steps"></a>Další kroky
[Další informace o balíčku Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Další informace o balíčku Enterprise Integration Pack") 

