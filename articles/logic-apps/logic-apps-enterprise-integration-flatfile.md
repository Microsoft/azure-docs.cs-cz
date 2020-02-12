---
title: Kódování a dekódování plochých souborů
description: Kódování a dekódování plochých souborů pro podnikovou integraci s Azure Logic Apps a Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 2d6182ba01507c2fb361628e01bb52e1ea821f44
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152648"
---
# <a name="encode-or-decode-flat-files-with-azure-logic-apps-and-enterprise-integration-pack"></a>Kódování a dekódování plochých souborů pomocí Azure Logic Apps a Enterprise Integration Pack

Obsah XML možná budete chtít kódovat předtím, než ho odešlete obchodnímu partnerovi ve scénáři B2B (Business-to-Business). V aplikaci logiky můžete to provést pomocí konektoru pro kódování plochého souboru. Aplikace logiky, kterou vytvoříte, může získat obsah XML z nejrůznějších zdrojů, včetně z triggeru požadavku HTTP, z jiné aplikace nebo dokonce z jednoho z mnoha [konektorů](../connectors/apis-list.md). Další informace o Logic Apps najdete v dokumentaci k [Logic Apps](logic-apps-overview.md "Další informace o Logic Apps").  

## <a name="create-the-flat-file-encoding-connector"></a>Vytvoření konektoru pro kódování plochého souboru
Pomocí těchto kroků můžete do aplikace logiky přidat konektor pro kódování plochých souborů.

1. Vytvořte aplikaci logiky a [propojte ji s vaším účtem pro integraci](logic-apps-enterprise-integration-accounts.md "Naučte se propojit účet pro integraci s aplikací logiky."). Tento účet obsahuje schéma, které použijete ke kódování dat XML.  

1. V návrháři aplikace logiky přidejte **při přijetí požadavku HTTP** do vaší aplikace logiky aktivační událost.

1. Akci kódování plochého souboru přidejte následujícím způsobem:

   a. Vyberte znaménko **plus** .

   b. Vyberte odkaz **přidat akci** (zobrazí se po výběru znaménka plus).

   c. Do vyhledávacího pole zadejte *ploché* , chcete-li filtrovat všechny akce na tu, kterou chcete použít.

   d. V seznamu vyberte možnost **kódování plochého souboru** .   

      ![Snímek obrazovky s možností kódování plochého souboru](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. V dialogovém okně **kódování plochého souboru** vyberte textové pole **obsah** .  

   ![Snímek obrazovky s obsahem – textové pole](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  

1. Vyberte značku těla jako obsah, který chcete kódovat. Značka text naplní pole obsah.     

   ![Snímek obrazovky se značkou textu](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  

1. Vyberte seznam **název schématu** a zvolte schéma, které chcete použít ke kódování vstupního obsahu.    

   ![Snímek obrazovky se seznamem názvů schémat](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  

1. Uložte svou práci.

   ![Snímek obrazovky s ikonou Uložit](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

V tuto chvíli jste dokončili nastavování konektoru pro kódování nestrukturovaného souboru. V reálné aplikaci můžete ukládat zakódovaná data do obchodní aplikace, jako je například Salesforce. Případně můžete tato kódovaná data odeslat obchodnímu partnerovi. Můžete snadno přidat akci, která odešle výstup akce kódování do Salesforce nebo vašemu obchodnímu partnerovi, a to pomocí některého z dalších zadaných konektorů.

Nyní můžete konektor otestovat tak, že vytvoříte požadavek na koncový bod HTTP a zahrnete obsah XML do těla žádosti.  

## <a name="create-the-flat-file-decoding-connector"></a>Vytvoření konektoru DEKÓDOVÁNÍ plochého souboru

> [!NOTE]
> Chcete-li provést tento postup, musíte mít již nahraný soubor schématu do účtu pro integraci.

1. V návrháři aplikace logiky přidejte **při přijetí požadavku HTTP** do vaší aplikace logiky aktivační událost.

1. Přidejte akci dekódování plochého souboru následujícím způsobem:

   a. Vyberte znaménko **plus** .

   b. Vyberte odkaz **přidat akci** (zobrazí se po výběru znaménka plus).

   c. Do vyhledávacího pole zadejte *ploché* , chcete-li filtrovat všechny akce na tu, kterou chcete použít.

   d. V seznamu vyberte možnost **dekódování nestrukturovaného souboru** .   

      ![Snímek obrazovky s možností dekódování plochého souboru](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. Vyberte ovládací prvek **obsahu** . Tím se vytvoří seznam obsahu ze starších kroků, které můžete použít jako obsah k dekódování. Všimněte si, že *tělo* příchozího požadavku HTTP je k dispozici pro použití jako obsah k dekódování. Můžete také zadat obsah k dekódování přímo do ovládacího prvku **obsahu** .     

1. Vyberte značku *těla* . Všimněte si, že značka textu je nyní v ovládacím prvku **Content** .

1. Vyberte název schématu, který chcete použít k dekódování obsahu. Na následujícím snímku obrazovky vidíte, že *OrderFile* je vybraný název schématu. Tento název schématu se předtím nahrál do účtu pro integraci.

   ![Snímek obrazovky dialogového okna dekódování plochého souboru](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png) 

1. Uložte svou práci.  

   ![Snímek obrazovky s ikonou Uložit](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

V tuto chvíli jste dokončili nastavování konektoru DEKÓDOVÁNÍ nestrukturovaného souboru. V reálné aplikaci můžete data dekódování ukládat do obchodní aplikace, jako je třeba Salesforce. Můžete snadno přidat akci, která odešle výstup akce dekódování do Salesforce.

Nyní můžete konektor otestovat tak, že vytvoříte požadavek na koncový bod HTTP a zahrnete do těla žádosti obsah XML, který chcete dekódovat.  

## <a name="next-steps"></a>Další kroky
* [Přečtěte si další informace o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informace o Enterprise Integration Pack").  

