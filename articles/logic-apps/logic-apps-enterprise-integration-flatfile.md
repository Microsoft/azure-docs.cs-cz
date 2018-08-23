---
title: Zakódování nebo dekódování plochých souborů v Azure logic apps | Dokumentace Microsoftu
description: Použití souboru kodér a dekodér v ve službě logic apps Enterprise Integration Pack
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: jeconnoc
editor: ''
ms.assetid: 82152dab-c7ad-43df-b721-596559703be8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; divswa
ms.openlocfilehash: b13e8da04c984456027f152f5af63cfa6604ddc4
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42054411"
---
# <a name="overview-of-enterprise-integration-with-flat-files"></a>Přehled podniková integrace pomocí plochých souborů

Můžete chtít kódování obsahu XML před odesláním do obchodního partnera ve scénáři business-to-business (B2B). V aplikaci logiky můžete k tomu plochého souboru kódování konektoru. Aplikace logiky, kterou vytvoříte můžete získat jeho XML obsahu z nejrůznějších zdrojů, včetně triggeru požadavku HTTP, z jiné aplikace nebo dokonce od mnoho [konektory](../connectors/apis-list.md). Další informace o logic apps, najdete v článku [dokumentace k logic apps](logic-apps-overview.md "Další informace o Logic apps").  

## <a name="create-the-flat-file-encoding-connector"></a>Vytvoření konektoru kódování plochého souboru
Postupujte podle těchto kroků přidejte plochého souboru kódování konektor do aplikace logiky.

1. Vytvoření aplikace logiky a [propojit ho se svým účtem integrace](logic-apps-enterprise-integration-accounts.md "se naučíte, jak propojit účet integrace aplikace logiky"). Tento účet obsahuje schéma, které budete používat ke kódování dat XML.  
1. Přidat **požadavek – přijetí požadavku HTTP při** trigger aplikace logiky.  
   ![Snímek obrazovky aktivační události vyberte](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
1. Přidejte akci, následujícím způsobem kódování plochého souboru:
   
    a. Vyberte **plus** přihlašování.
   
    b. Vyberte **přidat akci** odkaz (zobrazí se po výběru na symbol plus).
   
    c. Do vyhledávacího pole zadejte *plochý* filtrovat všechny akce, které ten, který chcete použít.
   
    d. Vyberte **kódování plochého souboru** možnost ze seznamu.   
   ![Snímek obrazovky z plochého kódování souboru možnost](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
1. Na **kódování plochého souboru** dialogové okno, vyberte **obsahu** textového pole.  
   ![Snímek obrazovky obsahu textového pole](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  
1. Vyberte značky body jako obsah, který chcete kódovat. Do značky body naplní pole obsahu.     
   ![Snímek obrazovky značky body](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  
1. Vyberte **Schemaname** seznam a zvolte možnost schématu, kterou chcete použít ke kódování vstupní obsah.    
   ![Název schématu snímek obrazovky pole se seznamem](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  
1. Uložte svou práci.   
   ![Snímek obrazovky uložit ikonu](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

V tomto okamžiku budete mít nastavení konektoru kódování plochých souborů. V reálné aplikaci můžete data uložit do kódovaný – obchodní aplikace, jako je například Salesforce. Nebo můžete odeslat, kódovaných dat do obchodních partnerů. Můžete snadno přidat akce pro odeslání výstupu kódování akce k Salesforce, a váš obchodní partner, pomocí některého z jiných konektorů k dispozici.

Teď můžete svůj konektor otestovat požadavku na koncový bod protokolu HTTP a včetně obsah XML v textu požadavku.  

## <a name="create-the-flat-file-decoding-connector"></a>Vytvoření konektoru dekódování plochého souboru

> [!NOTE]
> Pokud chcete dokončit tento postup, musíte mít schéma soubor již nahráli do integračního účtu.

1. Přidat **požadavek – přijetí požadavku HTTP při** trigger aplikace logiky.  
   ![Snímek obrazovky aktivační události vyberte](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
1. Přidejte akci, následujícím způsobem dekódování plochého souboru:
   
    a. Vyberte **plus** přihlašování.
   
    b. Vyberte **přidat akci** odkaz (zobrazí se po výběru na symbol plus).
   
    c. Do vyhledávacího pole zadejte *plochý* filtrovat všechny akce, které ten, který chcete použít.
   
    d. Vyberte **dekódování plochého souboru** možnost ze seznamu.   
   ![Možnost snímek obrazovky z plochého souboru – dekódování](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
1. Vyberte **obsahu** ovládacího prvku. To vytváří seznam obsahu z dřívějších krocích, které můžete použít jako obsah pro dekódování. Všimněte si, že *tělo* z příchozí HTTP je požadavek dostupné pro použití jako obsah pro dekódování. Můžete také zadat obsah pro dekódování přímo do **obsah** ovládacího prvku.     
1. Vyberte *tělo* značky. Všimněte si, že do značky body je teď v **obsahu** ovládacího prvku.
1. Vyberte název schématu, který chcete použít k dekódování obsahu. Následující snímek obrazovky ukazuje, že *OrderFile* je název vybraného schématu. Tento název schématu dříve měli byla nahrána do účtu integrace.
   
   ![Dialogové okno snímek obrazovky z plochého souboru – dekódování](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png)    
1. Uložte svou práci.  
   ![Snímek obrazovky uložit ikonu](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

V tomto okamžiku budete mít nastavení plochého souboru dekódování konektoru. V reálné aplikaci můžete data uložit do dekódovaný – obchodní aplikace, jako je například Salesforce. Můžete snadno přidat akce pro odeslání výstupu dekódování akce k Salesforce.

Teď můžete svůj konektor otestovat tím, že požadavek na koncový bod protokolu HTTP a včetně obsahu XML, který chcete dekódovat v textu požadavku.  

## <a name="next-steps"></a>Další postup
* [Další informace o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "přečtěte si víc o Enterprise Integration Pack").  

