---
title: Kódování zpráv EDIFACT
description: Ověření EDI a generování XML pomocí kodéru zpráv EDIFACT pro aplikace Azure Logic Apps s podnikovou integrací Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 257cbd0b1a68ddd2b16235e6f8dec5d5b0eb10e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790656"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>Kódování zpráv EDIFACT pro aplikace Azure Logic Apps pomocí sady Enterprise Integration Pack

Pomocí konektoru zpráv Encode EDIFACT můžete ověřit vlastnosti EDI a specifické pro partnera, vygenerovat dokument XML pro každou sadu transakcí a požádat o technické potvrzení, funkční potvrzení nebo obojí.
Chcete-li použít tento konektor, musíte přidat konektor do existující aktivační události v aplikaci logiky.

## <a name="before-you-start"></a>Než začnete

Zde jsou položky, které potřebujete:

* Účet Azure; můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* [Účet integrace,](logic-apps-enterprise-integration-create-integration-account.md) který už je definovaný a přidružený k vašemu předplatnému Azure. Chcete-li použít konektor zprávy Encode EDIFACT, musíte mít účet integrace. 
* Alespoň dva [partneři,](logic-apps-enterprise-integration-partners.md) kteří jsou již definováni ve vašem integračním účtu
* [Dohoda EDIFACT,](logic-apps-enterprise-integration-edifact.md) která je již definována ve vašem integračním účtu

## <a name="encode-edifact-messages"></a>Kódování zpráv EDIFACT

1. [Vytvořte aplikaci logiky](quickstart-create-first-logic-app-workflow.md).

2. Konektor zprávy Encode EDIFACT nemá aktivační události, takže je nutné přidat aktivační událost pro spuštění aplikace logiky, jako je aktivační událost požadavku. V Návrháři aplikace logiky přidejte aktivační událost a pak přidejte akci do aplikace logiky.

3.  Do vyhledávacího pole zadejte jako filtr "EDIFACT". Vyberte buď **Ecode EDIFACT Message podle názvu smlouvy** nebo **Encode do EDIFACT zprávy podle identit**.
   
    ![hledat EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Pokud jste dříve nevytvořili žádná připojení ke svému účtu integrace, budete vyzváni k vytvoření tohoto připojení nyní. Pojmenujte připojení a vyberte účet integrace, který chcete připojit.

    ![vytvoření připojení integračního účtu](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Jsou vyžadovány vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název připojení. |
    | Integrační účet * |Zadejte název účtu integrace. Ujistěte se, že váš účet integrace a aplikace logiky jsou ve stejném umístění Azure. |

5.  Po dokončení by měly vypadat podobně jako v tomto příkladu podrobnosti o připojení. Chcete-li dokončit vytváření připojení, zvolte **Vytvořit**.

    ![podrobnosti o připojení účtu integrace](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    Vaše připojení je nyní vytvořeno.

    ![vytvořeno připojení účtu integrace](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Zakódovat zprávu EDIFACT podle názvu smlouvy

Pokud jste se rozhodli kódovat zprávy EDIFACT podle názvu smlouvy, otevřete seznam **Název smlouvy EDIFACT,** zadejte nebo vyberte název smlouvy EDIFACT. Zadejte zprávu XML, kterou chcete zakódovat.

![Zadejte název smlouvy EDIFACT a zprávu XML pro kódování.](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Kódování zprávy EDIFACT podle identit

Pokud se rozhodnete kódovat zprávy EDIFACT podle identit, zadejte identifikátor odesílatele, kvalifikátor odesílatele, identifikátor příjemce a kvalifikátor příjemce, jak je nakonfigurován o smlouvě EDIFACT. Vyberte zprávu XML, kterou chcete zakódovat.

![Poskytněte identity odesílateli a příjemci, vyberte zprávu XML, kterou chcete kódovat](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>EDIFACT Kódovat detaily

Konektor Encode EDIFACT provádí tyto úkoly: 

* Vyřešte smlouvu tak, že se kvalifikátor odesílatele & identifikátor a kvalifikátor příjemce a identifikátor
* Serializuje výměnu EDI a převádí zprávy kódované XML do sad transakcí EDI v výměně.
* Použije segmenty záhlaví a segmenty přívěsu sady transakcí.
* Generuje kontrolní číslo výměny, číslo řízení skupiny a řídicí číslo sady transakcí pro každou odchozí výměnu.
* Nahrazuje oddělovače v datech datové části.
* Ověří EDI a vlastnosti specifické pro partnera.
  * Ověření schématu datových prvků sady transakcí proti schématu zprávy.
  * Ověření EDI provedené u datových prvků sady transakcí.
  * Rozšířené ověření provedené u datových prvků sady transakcí
* Generuje dokument XML pro každou sadu transakcí.
* Požaduje technické a/nebo funkční potvrzení (pokud je nakonfigurováno).
  * Jako technické potvrzení zpráva CONTRL označuje přijetí výměny.
  * Jako funkční potvrzení zpráva CONTRL označuje přijetí nebo odmítnutí přijaté výměny, skupiny nebo zprávy se seznamem chyb nebo nepodporovaných funkcí

## <a name="view-swagger-file"></a>Zobrazit soubor Swagger
Podrobnosti s naparováním pro konektor EDIFACT naleznete v tématu [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Další kroky
[Další informace o balíčku Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Další informace o balíčku Enterprise Integration Pack") 

