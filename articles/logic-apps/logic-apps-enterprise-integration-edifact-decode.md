---
title: Dekódování zprávy EDIFACT – Azure Logic Apps | Dokumentace Microsoftu
description: Ověřování EDI a generování potvrzení s dekodér zprávy EDIFACT pro Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 0e61501d-21a2-4419-8c6c-88724d346e81
ms.date: 01/27/2017
ms.openlocfilehash: b101922d15a3f90c29eff51c223d2ea7dc30ddf2
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125348"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Dekódování zprávy EDIFACT pro Azure Logic Apps sadou Enterprise Integration Pack

Zpráva konektoru dekódování EDIFACT můžete ověřit EDI a vlastnosti specifické pro partnera, rozdělit výměn do sad transakcí nebo zachovat celý výměn a generovat potvrzení pro zpracování transakcí. Pokud chcete použít tento konektor, musí přidání konektoru do existující aktivační událost ve své aplikaci logiky.

## <a name="before-you-start"></a>Než začnete

Tady je položky, které budete potřebovat:

* Účet Azure. můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* [Účtu pro integraci](logic-apps-enterprise-integration-create-integration-account.md) , který již má definovaný a spojené s předplatným Azure. Integrační účet pro použití konektoru zpráva dekódování EDIFACT musí mít. 
* Alespoň dva [partnery](logic-apps-enterprise-integration-partners.md) , která jsou již definovány v účtu integrace
* [Smlouvy EDIFACT](logic-apps-enterprise-integration-edifact.md) , která je již definována v účtu integrace

## <a name="decode-edifact-messages"></a>Dekódování zprávy EDIFACT

1. [Vytvoření aplikace logiky](quickstart-create-first-logic-app-workflow.md).

2. Dekódování EDIFACT zpráva konektoru nemá aktivačních událostí, proto musíte přidat aktivační událost pro spuštění aplikace logiky jako aktivační událost požadavek. V návrháři aplikace logiky přidávat aktivační události a pak přidat akci do aplikace logiky.

3. Do vyhledávacího pole zadejte jako filtr "EDIFACT". Vyberte **dekódovat zprávu EDIFACT**.
   
    ![hledání EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Pokud jste nevytvořili dříve všechna připojení k účtu pro integraci, budete vyzváni k vytvoření tohoto připojení. Název připojení a vyberte účet pro integraci, kterou chcete připojit.
   
    ![Vytvoření účtu pro integraci](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Vyžadují se vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název vašeho připojení. |
    | Účet integrace * |Zadejte název účtu pro integraci. Ujistěte se, že integrační účet a logiku aplikace jsou ve stejném umístění Azure. |

4. Až to budete mít k dokončení vytváření připojení, zvolit **vytvořit**. Podrobnosti připojení by měla vypadat podobně jako v tomto příkladu:

    ![Podrobnosti o účtu integrace](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Po vytvoření připojení, jak je znázorněno v tomto příkladu, vyberte zpráva plochého souboru EDIFACT k dekódování.

    ![Vytvoření připojení k účtu integrace](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Příklad:

    ![Vyberte zpráva plochého souboru EDIFACT k dekódování](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Podrobnosti dekodér EDIFACT

Dekódování EDIFACT connector provádí tyto úlohy: 

* Ověří obálky proti obchodování partnerskou smlouvu.
* Vyhodnotí smlouvu to provede spárováním odpovídajících kvalifikátor odesílatele a identifikátoru a kvalifikátoru pro příjemce a identifikátor.
* Rozdělí křižovatky do více transakcí při výměna má více než jednu transakci podle dohody přijímat konfigurační nastavení.
* Zpětně přeloží výměna.
* Ověří EDI a vlastnosti specifické pro partnerů, včetně:
  * Ověření struktury obálky výměny
  * Ověření schématu obálky ovládacího prvku schématu
  * Ověření schématu datové sady transakcí prvky proti schématu zprávy
  * Ověřování EDI provedla sada transakcí datových prvků
* Ověřuje, že kontrolních čísel sady výměny, skupiny a transakce nejsou duplikáty (je-li konfigurováno) 
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
* Generuje technické (řízení) nebo funkční potvrzení (je-li konfigurováno).
  * Technické potvrzení nebo CONTRL ACK hlásí výsledky syntaktické kontrolu dokončení přijatý výměny.
  * Funkční potvrzení uznává následně přijímal nebo odmítal přijatý výměny nebo skupiny

## <a name="view-swagger-file"></a>Zobrazit soubor Swagger
Chcete-li zobrazit podrobnosti Swagger pro konektor EDIFACT, naleznete v tématu [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Další postup
[Další informace o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "přečtěte si víc o Enterprise Integration Pack") 

