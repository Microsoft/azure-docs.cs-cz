---
title: Dekódování zpráv EDIFACT
description: Ověřte EDI a generujte potvrzení pomocí dekodéru zpráv EDIFACT pro Azure Logic Apps s Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: b36641677dbf36402c7f578b9b1887c52f441afd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322474"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Dekódovat zprávy EDIFACT pro Azure Logic Apps s Enterprise Integration Pack

Pomocí konektoru pro dekódování zpráv EDIFACT můžete ověřit vlastnosti specifické pro EDI a partnera, rozdělit výměnu na sady transakcí nebo zachovat celé výměny a generovat potvrzení pro zpracované transakce. Pokud chcete tento konektor použít, musíte ho přidat do existujícího triggeru v aplikaci logiky.

## <a name="before-you-start"></a>Než začnete

Tady jsou položky, které potřebujete:

* Účet Azure; Můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free) .
* [Účet pro integraci](logic-apps-enterprise-integration-create-integration-account.md) , který je už definovaný a přidružený k vašemu předplatnému Azure. Abyste mohli použít dekódovaný konektor zpráv EDIFACT, musíte mít účet pro integraci. 
* Alespoň dva [partneři](logic-apps-enterprise-integration-partners.md) , kteří jsou již definováni v účtu integrace
* [Smlouva EDIFACT](logic-apps-enterprise-integration-edifact.md) , která je už definovaná v účtu integrace

## <a name="decode-edifact-messages"></a>Dekódování zpráv EDIFACT

> [!IMPORTANT]
> Konektor EDIFACT podporuje pouze znaky UTF-8.
> Pokud výstup obsahuje neočekávané znaky, ověřte, zda zprávy EDIFACT používají znakovou sadu UTF-8. 

1. [Vytvořte aplikaci logiky](quickstart-create-first-logic-app-workflow.md).

2. Dekódovaný konektor zpráv EDIFACT neobsahuje triggery, takže musíte přidat Trigger pro spuštění aplikace logiky, jako je Trigger žádosti. V návrháři aplikace logiky přidejte Trigger a pak přidejte akci do aplikace logiky.

3. Do vyhledávacího pole zadejte "EDIFACT" jako filtr. Vyberte **dekódovat zprávu EDIFACT**.
   
    ![Hledat EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Pokud jste dříve nevytvořili žádná připojení k účtu pro integraci, budete vyzváni k vytvoření tohoto připojení nyní. Zadejte název připojení a vyberte účet pro integraci, který chcete připojit.
   
    ![vytvořit účet pro integraci](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Jsou vyžadovány vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název připojení. |
    | Účet pro integraci * |Zadejte název účtu pro integraci. Ujistěte se, že váš účet pro integraci a aplikace logiky jsou ve stejném umístění Azure. |

4. Až skončíte s vytvářením připojení, klikněte na **vytvořit**. Podrobnosti o připojení by měly vypadat podobně jako v tomto příkladu:

    ![Podrobnosti účtu pro integraci](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Po vytvoření připojení, jak je znázorněno v tomto příkladu, vyberte zprávu EDIFACT Flat File, která se má dekódovat.

    ![vytvořilo se připojení k účtu pro integraci.](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Například:

    ![Pro dekódování vybrat zprávu plochý soubor EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Podrobnosti o dekodéru EDIFACT

Dekódovaný EDIFACT konektor provádí tyto úlohy: 

* Ověří obálku proti smlouvě s obchodním partnerem.
* Vyhodnotí smlouvu porovnáním kvalifikátoru odesílatele & identifikátoru a kvalifikátoru přijímače & identifikátoru.
* Rozdělí výměnu na více transakcí, pokud má výměna více než jednu transakci na základě konfigurace nastavení přijetí smlouvy.
* Rozloží výměnu.
* Ověřuje EDI a vlastnosti specifické pro partnerský server, včetně:
  * Ověření struktury obálky výměny
  * Ověřování schématu obálky proti schématu ovládacího prvku
  * Ověřování schématu datových elementů transakce-sady podle schématu zprávy
  * Ověřování EDI provedené v datových prvcích sady transakcí
* Ověřuje, že čísla řízení výměny, skupiny a sady transakcí nejsou duplicitní (pokud jsou nakonfigurovaná). 
  * Kontroluje kontrolní číslo výměny proti dříve přijatým změnám. 
  * Kontroluje číslo řízení skupiny proti jiným řídicím číslům skupiny ve výměně. 
  * Kontroluje kontrolní číslo sady transakcí s jinými čísly řízení sady transakcí v této skupině.
* Rozdělí výměnu na sady transakcí nebo zachová celý výměnu:
  * Rozdělit výměnu jako sady transakcí – pozastavit sady transakcí při chybě: rozdělí výměnu na sady transakcí a analyzuje každou sadu transakcí. 
  Akce dekódování EDIFACT výstupuje pouze ty sady transakcí, které selžou při ověřování `badMessages` , a výstup zbývajících transakcí nastaví na `goodMessages` .
  * Rozdělit výměnu jako sady transakcí – pozastavit výměnu při chybě: rozděluje výměnu do sad transakcí a analyzuje každou sadu transakcí. 
  Pokud jedna nebo více transakcí v rámci výměny selže, akce dekódování EDIFACT vypíše všechny sady transakcí v tomto přenosu do `badMessages` .
  * Zachovat výměnu – pozastavit sady transakcí při chybě: zachovejte výměnu a zpracujte celý dávkový vydaný výměnný. 
  Akce dekódování EDIFACT výstupuje pouze ty sady transakcí, které selžou při ověřování `badMessages` , a výstup zbývajících transakcí nastaví na `goodMessages` .
  * Zachovat výměnu – pozastavit výměnu při chybě: zachovejte výměnu a zpracujte celý dávkový výměnný. 
  Pokud jedna nebo více transakcí v rámci výměny selže, akce dekódování EDIFACT vypíše všechny sady transakcí v tomto přenosu do `badMessages` .
* Generuje technický (ovládací) nebo funkční potvrzení (Pokud je nakonfigurováno).
  * Technické potvrzení nebo potvrzení CONTRLu hlásí výsledky syntaktické kontroly kompletního přijímaného přenosu.
  * Funkční potvrzení potvrdí přijetí nebo odmítnutí přijatého výměny nebo skupiny.

## <a name="view-swagger-file"></a>Zobrazit soubor Swagger
Podrobnosti o Swagger pro konektor EDIFACT najdete v tématu [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Další kroky
[Další informace o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informace o Enterprise Integration Pack") 

