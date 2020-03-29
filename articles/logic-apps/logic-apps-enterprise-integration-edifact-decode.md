---
title: Dekódování zpráv EDIFACT
description: Ověření EDI a generování potvrzení pomocí dekodéru zpráv EDIFACT pro aplikace Azure Logic Apps s balíčkem Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 78c0d20c0f32a6d63d134e958b30d38fe11fcc5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790663"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Dekódování zpráv EDIFACT pro aplikace Azure Logic Apps pomocí sady Enterprise Integration Pack

Pomocí konektoru zpráv Decode EDIFACT můžete ověřit vlastnosti EDI a specifické pro partnera, rozdělit mezizměny na sady transakcí nebo zachovat celé mimoúrovňové křižovatky a generovat potvrzení pro zpracované transakce. Chcete-li použít tento konektor, musíte přidat konektor do existující aktivační události v aplikaci logiky.

## <a name="before-you-start"></a>Než začnete

Zde jsou položky, které potřebujete:

* Účet Azure; můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* [Účet integrace,](logic-apps-enterprise-integration-create-integration-account.md) který už je definovaný a přidružený k vašemu předplatnému Azure. Chcete-li použít konektor zprávy Decode EDIFACT, musíte mít účet integrace. 
* Alespoň dva [partneři,](logic-apps-enterprise-integration-partners.md) kteří jsou již definováni ve vašem integračním účtu
* [Dohoda EDIFACT,](logic-apps-enterprise-integration-edifact.md) která je již definována ve vašem integračním účtu

## <a name="decode-edifact-messages"></a>Dekódování zpráv EDIFACT

1. [Vytvořte aplikaci logiky](quickstart-create-first-logic-app-workflow.md).

2. Konektor zprávy Decode EDIFACT nemá aktivační události, takže je nutné přidat aktivační událost pro spuštění aplikace logiky, jako je aktivační událost požadavku. V Návrháři aplikace logiky přidejte aktivační událost a pak přidejte akci do aplikace logiky.

3. Do vyhledávacího pole zadejte jako filtr "EDIFACT". Vyberte **dekódovat zprávu EDIFACT**.
   
    ![hledat EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Pokud jste dříve nevytvořili žádná připojení ke svému účtu integrace, budete vyzváni k vytvoření tohoto připojení nyní. Pojmenujte připojení a vyberte účet integrace, který chcete připojit.
   
    ![vytvořit účet integrace](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Jsou vyžadovány vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název připojení. |
    | Integrační účet * |Zadejte název účtu integrace. Ujistěte se, že váš účet integrace a aplikace logiky jsou ve stejném umístění Azure. |

4. Až dokončíte vytváření připojení, zvolte **Vytvořit**. Podrobnosti o připojení by měly vypadat podobně jako v tomto příkladu:

    ![podrobnosti o integračním účtu](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Po vytvoření připojení, jak je znázorněno v tomto příkladu, vyberte zprávu edifact plochý soubor dekódovat.

    ![vytvořeno připojení účtu integrace](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Například:

    ![Vyberte zprávu edifact plochý soubor pro dekódování](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Detaily dekodéru EDIFACT

Decode EDIFACT konektor provádí tyto úkoly: 

* Ověří obálku proti dohodě obchodního partnera.
* Řeší dohodu porovnáním kvalifikátoru odesílatele & identifikátor u identifikátoru a identifikátoru kvalifikátoru příjemce &.
* Rozdělí výměnu na více transakcí, pokud má výměna více než jednu transakci na základě konfigurace nastavení příjmu smlouvy.
* Demontuje výměnu.
* Ověří EDI a vlastnosti specifické pro partnera, včetně:
  * Validace struktury výměnných obálek
  * Ověření schématu obálky proti schématu ovládacího prvku
  * Ověření schématu datových prvků sady transakcí proti schématu zprávy
  * Ověření EDI provedené u datových prvků sady transakcí
* Ověří, zda kontrolní čísla sady mezibankovních, skupinových a transakčních sad nejsou duplicitní (pokud jsou nakonfigurována). 
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
* Generuje technické (řízení) a/nebo funkční potvrzení (pokud je nakonfigurováno).
  * Technické potvrzení nebo CONTRL ACK hlásí výsledky syntaktické kontroly úplné přijaté výměny.
  * Funkční potvrzení potvrzuje přijetí nebo odmítnutí přijaté výměny nebo skupiny

## <a name="view-swagger-file"></a>Zobrazit soubor Swagger
Podrobnosti s naparováním pro konektor EDIFACT naleznete v tématu [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Další kroky
[Další informace o balíčku Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Další informace o balíčku Enterprise Integration Pack") 

