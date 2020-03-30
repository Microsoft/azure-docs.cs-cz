---
title: Kódování nebo dekódování plochých souborů
description: Kódování nebo dekódování plochých souborů pro podnikovou integraci pomocí Azure Logic Apps a Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 2d6182ba01507c2fb361628e01bb52e1ea821f44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152648"
---
# <a name="encode-or-decode-flat-files-with-azure-logic-apps-and-enterprise-integration-pack"></a>Kódování nebo dekódování plochých souborů pomocí azure logic apps a sady Enterprise Integration Pack

Obsah XML můžete před odesláním obchodnímu partnerovi zakódovat v případě business-to-business (B2B). V aplikaci logiky, můžete použít konektor pro kódování plochý soubor k tomu. Aplikace logiky, kterou vytvoříte můžete získat jeho obsah XML z různých zdrojů, včetně z aktivační události požadavku HTTP, z jiné aplikace nebo dokonce z jednoho z mnoha [konektorů](../connectors/apis-list.md). Další informace o aplikacích logiky najdete v [dokumentaci k aplikacím logiky](logic-apps-overview.md "Další informace o aplikacích Logika").  

## <a name="create-the-flat-file-encoding-connector"></a>Vytvoření konektoru pro kódování plochého souboru
Podle následujících kroků přidejte do aplikace logiky konektor pro kódování plochých souborů.

1. Vytvořte aplikaci logiky a [propojte ji s účtem integrace](logic-apps-enterprise-integration-accounts.md "Naučte se propojit účet integrace s aplikací Logika"). Tento účet obsahuje schéma, které použijete ke kódování dat XML.  

1. V Návrháři aplikace logiky přidejte při **přijetí požadavku HTTP** aktivační událost do aplikace logiky.

1. Přidejte akci kódování plochého souboru takto:

   a. Vyberte znaménko **plus.**

   b. Vyberte odkaz **Přidat akci** (zobrazí se po výběru znaménku plus).

   c. Do vyhledávacího pole zadejte *Plochý,* chcete-li filtrovat všechny akce na tu, kterou chcete použít.

   d. Ze seznamu vyberte volbu **Kódování plochého souboru.**   

      ![Snímek obrazovky s možností kódování plochého souboru](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. V dialogovém okně **Kódování plochého souboru** vyberte textové pole **Obsah.**  

   ![Snímek obrazovky s textovým polem Obsah](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  

1. Vyberte značku těla jako obsah, který chcete zakódovat. Značka tělo naplní pole obsahu.     

   ![Snímek obrazovky se značkou tělo](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  

1. Vyberte seznam **Název schématu** a vyberte schéma, které chcete použít ke kódování vstupního obsahu.    

   ![Snímek obrazovky se seznamem Název schématu](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  

1. Uložte svou práci.

   ![Snímek obrazovky s ikonou Uložit](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

V tomto okamžiku jste dokončili nastavení konektoru pro kódování plochých souborů. V aplikaci reálného světa můžete chtít uložit kódovaná data v obchodní aplikaci, jako je například Salesforce. Nebo můžete poslat zakódovaná data obchodnímu partnerovi. Můžete snadno přidat akci k odeslání výstupu akce kódování salesforce nebo obchodnímu partnerovi pomocí některého z dalších konektorů.

Nyní můžete otestovat konektor tím, že požadavek na koncový bod HTTP a včetně obsahu XML v těle požadavku.  

## <a name="create-the-flat-file-decoding-connector"></a>Vytvoření konektoru pro dekódování plochých souborů

> [!NOTE]
> Chcete-li provést tyto kroky, musíte mít soubor schématu již odeslány do účtu integrace.

1. V Návrháři aplikace logiky přidejte při **přijetí požadavku HTTP** aktivační událost do aplikace logiky.

1. Přidejte akci dekódování plochého souboru takto:

   a. Vyberte znaménko **plus.**

   b. Vyberte odkaz **Přidat akci** (zobrazí se po výběru znaménku plus).

   c. Do vyhledávacího pole zadejte *Plochý,* chcete-li filtrovat všechny akce na tu, kterou chcete použít.

   d. V seznamu vyberte volbu **Dekódování plochého souboru.**   

      ![Snímek obrazovky s možností Dekódování plochého souboru](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. Vyberte ovládací **prvek Obsah.** Tím se vytvoří seznam obsahu z předchozích kroků, které můžete použít jako obsah k dekódování. Všimněte si, že *text* z příchozí ho požadavku HTTP je k dispozici pro použití jako obsah k dekódování. Můžete také zadat obsah, který chcete dekódovat přímo do ovládacího **prvku Obsah.**     

1. Vyberte značku *Tělo.* Všimněte si, že značka tělo je nyní v ovládacím **prvku Obsah.**

1. Vyberte název schématu, které chcete použít k dekódování obsahu. Následující snímek obrazovky ukazuje, že *OrderFile* je vybraný název schématu. Tento název schématu byl dříve nahrán do účtu integrace.

   ![Snímek obrazovky s dialogovým oknem Dekódování plochého souboru](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png) 

1. Uložte svou práci.  

   ![Snímek obrazovky s ikonou Uložit](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

V tomto okamžiku jste dokončili nastavení konektoru pro dekódování plochého souboru. V aplikaci reálného světa můžete chtít uložit dekódovaná data v obchodní aplikaci, jako je Salesforce. Můžete snadno přidat akci pro odeslání výstupu akce dekódování do salesforce.

Nyní můžete otestovat konektor tak, že požadavek na koncový bod HTTP a včetně obsahu XML, který chcete dekódovat v těle požadavku.  

## <a name="next-steps"></a>Další kroky
* [Další informace o podnikové mašaletu .](logic-apps-enterprise-integration-overview.md "Další informace o balíčku Enterprise Integration Pack")  

