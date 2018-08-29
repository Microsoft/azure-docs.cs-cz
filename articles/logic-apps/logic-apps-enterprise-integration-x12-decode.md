---
title: Dekódování X12 zprávy – Azure Logic Apps | Dokumentace Microsoftu
description: Ověřování EDI a generování potvrzení s X12 dekodér zpráv ve službě Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.date: 01/27/2017
ms.openlocfilehash: e3d2a458c2cece5e3f01fdb9e3d403b3fb78dd2b
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43121641"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Dekódování X12 zpráv ve službě Azure Logic Apps sadou Enterprise Integration Pack

S konektorem dekódování X12 zpráva můžete ověřovat obálky proti smlouvy s obchodním partnerem, ověření EDI a vlastnosti specifické pro partnera, rozdělit výměn do sad transakcí nebo zachovat celý výměn a generovat potvrzení pro zpracování transakcí. Pokud chcete použít tento konektor, musí přidání konektoru do existující aktivační událost ve své aplikaci logiky.

## <a name="before-you-start"></a>Než začnete

Tady je položky, které budete potřebovat:

* Účet Azure. můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* [Účtu pro integraci](logic-apps-enterprise-integration-create-integration-account.md) , který již má definovaný a spojené s předplatným Azure. Musíte mít účtu pro integraci k používání konektoru X12 dekódování zprávy.
* Alespoň dva [partnery](logic-apps-enterprise-integration-partners.md) , která jsou již definovány v účtu integrace
* [X12 smlouvy](logic-apps-enterprise-integration-x12.md) , která je již definována v účtu integrace

## <a name="decode-x12-messages"></a>Dekódování X12 zprávy

1. [Vytvoření aplikace logiky](quickstart-create-first-logic-app-workflow.md).

2. Konektor dekódování X12 zpráva nemá aktivačních událostí, proto musíte přidat aktivační událost pro spuštění aplikace logiky jako trigger požadavku. V návrháři aplikace logiky přidávat aktivační události a pak přidat akci do aplikace logiky.

3.  Do vyhledávacího pole zadejte "x12" filtr. Vyberte **X12 – dekódování X12 zpráva**.
   
    ![Vyhledejte "x12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Pokud jste nevytvořili dříve všechna připojení k účtu pro integraci, budete vyzváni k vytvoření tohoto připojení. Název připojení a vyberte účet pro integraci, kterou chcete připojit. 

    ![Zadat podrobnosti připojení účtu integrace](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Vyžadují se vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název vašeho připojení. |
    | Účet integrace * |Zadejte název účtu pro integraci. Ujistěte se, že integrační účet a logiku aplikace jsou ve stejném umístění Azure. |

5.  Až budete hotovi, vaše podrobnosti připojení by měl vypadat podobně jako tento příklad. Chcete-li dokončit vytváření připojení, zvolte **vytvořit**.
   
    ![Podrobnosti připojení účtu integrace](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Po připojení se vytvoří, jak je znázorněno v tomto příkladu, vyberte X12 zpráva plochého souboru k dekódování.

    ![Vytvoření připojení k účtu integrace](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Příklad:

    ![Vyberte X12 zpráva plochého souboru k dekódování](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > Obsah zprávy skutečné nebo datovou část zprávy pole dobré a špatné, je kódování base64. Ano je nutné zadat výraz, který zpracuje tento obsah.
   > Tady je příklad, který zpracovává obsah ve formátu XML, které můžete zadat v kódu zobrazit nebo pomocí Tvůrce výrazů v návrháři.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Příklad obsahu](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>X12 dekódování podrobnosti

X12 dekódování connector provádí tyto úlohy:

* Ověří obálky proti obchodování smlouvu pro partnery
* Ověří EDI a vlastnosti specifické pro partnera
  * Strukturální ověřování EDI a rozšířeného schématu ověřování
  * Ověření struktury obálky výměny.
  * Ověření schématu obálky ovládacího prvku schématu.
  * Ověření schématu datové sady transakcí prvky proti schématu zprávy.
  * Ověřování EDI provedla sada transakcí datových prvků 
* Ověřuje, že kontrolní čísla sady výměny, skupiny a transakce nejsou duplicitní položky
  * Ověří proti dříve přijaté výměn kontrolní číslo výměny.
  * Ověří proti dalších kontrolních čísel skupiny v výměna kontrolní číslo skupiny.
  * Ověří, že kontrolní číslo sady transakcí pro ostatní transakce sada kontrolních čísel v této skupině.
* Rozdělí výměna do sady transakcí nebo zachová celý výměny:
  * Rozdělit výměnu jako sady transakcí – pozastavit sady transakcí při chybě: Nastaví výměny rozdělí do transakce a Parsuje každá sada transakcí. 
  X12 dekódování akce vypíše pouze ty transakce sad, které neúspěšné ověření na `badMessages`a nastaví zbývající transakce na výstupy `goodMessages`.
  * Rozdělit výměnu jako sady transakcí – pozastavit výměnu při chybě: Nastaví výměny rozdělí do transakce a Parsuje každá sada transakcí. 
  Pokud je jeden nebo více transakcí nastaví v výměna neúspěšné ověření, X12 dekódování akce vypíše všechny transakce nastaví v této výměny na `badMessages`.
  * Zachovat výměnu – pozastavit sady transakcí při chybě: zachování výměna a celé výměně dávkové zpracování. 
  X12 dekódování akce vypíše pouze ty transakce sad, které neúspěšné ověření na `badMessages`a nastaví zbývající transakce na výstupy `goodMessages`.
  * Zachovat výměnu – pozastavit výměnu při chybě: zachování výměna a celé výměně dávkové zpracování. 
  Pokud je jeden nebo více transakcí nastaví v výměna neúspěšné ověření, X12 dekódování akce vypíše všechny transakce nastaví v této výměny na `badMessages`. 
* Generuje technické a/nebo funkční potvrzení (je-li konfigurováno).
  * Technické potvrzení vygeneruje jako výsledek ověření záhlaví. Technické potvrzení hlásí stav zpracování výměny záhlaví a koncová část adresy příjemce.
  * Funkční potvrzení vygeneruje jako výsledek ověření textu. Funkční potvrzení sestavy každou došlo k chybě při zpracování přijatých dokumentu

## <a name="view-the-swagger"></a>Zobrazení swaggeru
Zobrazit [swagger podrobnosti](/connectors/x12/). 

## <a name="next-steps"></a>Další postup
[Další informace o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "přečtěte si víc o Enterprise Integration Pack") 

