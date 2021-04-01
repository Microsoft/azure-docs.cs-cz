---
title: Kódování zpráv EDIFACT
description: Ověření EDI a generování XML pomocí kodéru zpráv EDIFACT pro Azure Logic Apps s Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: cee97fddc619840de821ebb70d32e0cab1bbe040
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "82106521"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>Kódovat zprávy EDIFACT pro Azure Logic Apps s Enterprise Integration Pack

Pomocí konektoru pro kódování zpráv EDIFACT můžete ověřit vlastnosti specifické pro EDI a partnera, vygenerovat dokument XML pro jednotlivé sady transakcí a požádat o technické potvrzení, funkční potvrzení nebo obojí.
Pokud chcete tento konektor použít, musíte ho přidat do existující aktivační události v aplikaci logiky.

## <a name="before-you-start"></a>Než začnete

Tady jsou položky, které potřebujete:

* Účet Azure; Můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free) .
* [Účet pro integraci](logic-apps-enterprise-integration-create-integration-account.md) , který je už definovaný a přidružený k vašemu předplatnému Azure. K používání konektoru zpráv EDIFACT pro kódování musíte mít účet pro integraci. 
* Alespoň dva [partneři](logic-apps-enterprise-integration-partners.md) , kteří jsou již definováni v účtu integrace
* [Smlouva EDIFACT](logic-apps-enterprise-integration-edifact.md) , která je už definovaná v účtu integrace

## <a name="encode-edifact-messages"></a>Kódování zpráv EDIFACT

> [!IMPORTANT]
> Konektor EDIFACT podporuje pouze znaky UTF-8.
> Pokud výstup obsahuje neočekávané znaky, ověřte, zda zprávy EDIFACT používají znakovou sadu UTF-8.

1. [Vytvořte aplikaci logiky](quickstart-create-first-logic-app-workflow.md).

2. Konektor zpráv EDIFACT ve službě encode neobsahuje aktivační události, takže musíte přidat Trigger pro spuštění aplikace logiky, jako je Trigger žádosti. V návrháři aplikace logiky přidejte Trigger a pak přidejte akci do aplikace logiky.

3.  Do vyhledávacího pole zadejte "EDIFACT" jako filtr. Vyberte buď možnost **KÓDOVAT EDIFACT zpráva podle názvu smlouvy** , nebo **zakódovat pro EDIFACT zprávy podle identity**.
   
    ![Hledat EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Pokud jste dříve nevytvořili žádná připojení k účtu pro integraci, budete vyzváni k vytvoření tohoto připojení nyní. Zadejte název připojení a vyberte účet pro integraci, který chcete připojit.

    ![vytvořit připojení k účtu Integration](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Jsou vyžadovány vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název připojení. |
    | Účet pro integraci * |Zadejte název účtu pro integraci. Ujistěte se, že váš účet pro integraci a aplikace logiky jsou ve stejném umístění Azure. |

5.  Až budete hotovi, vaše podrobnosti o připojení by měly vypadat podobně jako v tomto příkladu. Chcete-li dokončit vytváření připojení, klikněte na tlačítko **vytvořit**.

    ![podrobnosti připojení účtu pro integraci](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    Připojení je nyní vytvořeno.

    ![vytvořilo se připojení k účtu pro integraci.](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Kódovat zprávu EDIFACT podle názvu smlouvy

Pokud se rozhodnete ke kódování zpráv EDIFACT podle názvu smlouvy, otevřete seznam **EDIFACT smlouvy** , zadejte nebo vyberte název smlouvy EDIFACT. Zadejte zprávu XML, která se má zakódovat.

![Zadejte název smlouvy EDIFACT a zprávu XML ke kódování.](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Kódovat zprávy EDIFACT podle identit

Pokud se rozhodnete zakódovat zprávy EDIFACT podle identity, zadejte identifikátor odesilatele, kvalifikátor odesílatele, identifikátor přijímače a kvalifikátor přijímače podle konfigurace ve vaší smlouvě EDIFACT. Vyberte zprávu XML, která se má zakódovat.

![Zadejte identity pro odesílatele a přijímače, vyberte zprávu XML ke kódování.](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Podrobnosti kódování EDIFACT

Konektor pro kódování EDIFACT provádí tyto úlohy: 

* Vyřešte smlouvu tak, že porovnáte kvalifikátor odesílatele & identifikátor a kvalifikátor a identifikátor příjemce.
* Serializace výměny EDI a převádí zprávy kódované XML na sady transakcí EDI ve výměně.
* Použije záhlaví sady transakcí a segmenty přípojných vozidel.
* Vygeneruje řídicí číslo výměny, řídicí číslo skupiny a řídicí číslo sady transakcí pro každý odchozí výměnu.
* Nahradí oddělovače v datech datové části.
* Ověří vlastnosti EDI a pro konkrétní partnery.
  * Ověřování schématu datových prvků pro transakci sady pro schéma zprávy
  * Ověřování EDI bylo provedeno v datových prvcích sady transakcí.
  * Rozšířené ověřování provedeno v datových prvcích sady transakcí
* Generuje dokument XML pro každou sadu transakcí.
* Požaduje technické nebo funkční potvrzení (Pokud je nakonfigurované).
  * V rámci technického potvrzení zpráva CONTRL indikuje příjem výměny.
  * V rámci funkčního potvrzení zpráva CONTRL označuje přijetí nebo zamítnutí přijatého přenosu, skupiny nebo zprávy se seznamem chyb nebo nepodporovaných funkcí.

## <a name="view-swagger-file"></a>Zobrazit soubor Swagger
Podrobnosti o Swagger pro konektor EDIFACT najdete v tématu [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Další kroky
[Další informace o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informace o Enterprise Integration Pack") 

