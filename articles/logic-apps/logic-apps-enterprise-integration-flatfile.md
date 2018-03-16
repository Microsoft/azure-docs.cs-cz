---
title: "Zakódování nebo dekódování plochých souborů v Azure logic apps | Microsoft Docs"
description: "Použití souboru souboru kodér nebo dekodér v podniku integrační balíček ve vašich logic apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: anneta
editor: 
ms.assetid: 82152dab-c7ad-43df-b721-596559703be8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; divswa
ms.openlocfilehash: 410f2e184a92b879d4f75b31216da783e82dc953
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="overview-of-enterprise-integration-with-flat-files"></a>Přehled integrace enterprise s plochých souborů

Můžete se ke kódování obsahu XML před jejím odesláním obchodním partnerům ve scénáři business-to-business (B2B). V aplikaci logiky můžete k tomu plochý soubor kódování konektor. Aplikace logiky, který vytvoříte může získat jeho XML obsah z různých zdrojů, včetně aktivační procedury pro požadavek HTTP, z jiné aplikace nebo dokonce od dalších [konektory](../connectors/apis-list.md). Další informace o aplikacích logiky najdete v tématu [dokumentaci aplikace logiky](logic-apps-overview.md "Další informace o aplikacích logiky").  

## <a name="create-the-flat-file-encoding-connector"></a>Vytvořit plochý soubor kódování konektoru
Postupujte podle těchto kroků přidejte plochý soubor kódování konektoru pro svou aplikaci logiky.

1. Vytvoření aplikace logiky a [ho propojit se svým účtem integrace](logic-apps-enterprise-integration-accounts.md "zjistěte, jak lze propojit účet integrace aplikace logiky"). Tento účet obsahuje schéma, které chcete použít ke kódování XML data.  
2. Přidat **požadavku - obdrží žádost HTTP při** aktivační svou aplikaci logiky.  
   ![Snímek obrazovky aktivační události a vyberte](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
3. Přidejte plochý soubor kódování akce, následujícím způsobem:
   
    a. Vyberte **plus** přihlášení.
   
    b. Vyberte **přidat akci** odkaz (se zobrazí po výběru na symbol plus).
   
    c. Do vyhledávacího pole zadejte *ploché* vyfiltrujete všechny akce, které ten, který chcete použít.
   
    d. Vyberte **ploché kódování souborů** možnost ze seznamu.   
   ![Možnost snímek z plochých kódování souborů](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
4. Na **ploché kódování souborů** dialogové okno, vyberte **obsahu** textové pole.  
   ![Snímek obrazovky obsahu textového pole](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  
5. Vyberte značky body jako obsah, který chcete kódovat. Značky body naplní pole obsahu.     
   ![Snímek obrazovky značky textu](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  
6. Vyberte **název schématu** seznam pole a zvolte schématu, kterou chcete použít ke kódování vstupní obsah.    
   ![Pole se seznamem název schématu snímek obrazovky](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  
7. Uložte svou práci.   
   ![Snímek obrazovky uložit ikonu](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

V tomto okamžiku jste dokončili nastavení kódování konektor vaše plochý soubor. V reálném světě aplikaci můžete data uložit do kódovaného-obchodní aplikaci, například služby Salesforce. Nebo můžete odeslat, aby kódovaného dat obchodních partnerů. Můžete snadno přidat akci pro odeslání výstupu kódování akce Salesforce nebo obchodního partnera, pomocí jedné jiných konektorů poskytuje.

Nyní můžete otestovat váš konektor tím, že požadavek na koncový bod HTTP a včetně obsah XML v textu požadavku.  

## <a name="create-the-flat-file-decoding-connector"></a>Vytvořit plochý soubor dekódování konektoru

> [!NOTE]
> K provedení těchto kroků, musíte mít soubor schématu již odeslány do integrace účtu.

1. Přidat **požadavku - obdrží žádost HTTP při** aktivační svou aplikaci logiky.  
   ![Snímek obrazovky aktivační události a vyberte](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
2. Přidejte plochý soubor dekódování akce, následujícím způsobem:
   
    a. Vyberte **plus** přihlášení.
   
    b. Vyberte **přidat akci** odkaz (se zobrazí po výběru na symbol plus).
   
    c. Do vyhledávacího pole zadejte *ploché* vyfiltrujete všechny akce, které ten, který chcete použít.
   
    d. Vyberte **plochý soubor dekódování** možnost ze seznamu.   
   ![Snímek obrazovky z plochých dekódování souboru možnost](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
3. Vyberte **obsahu** ovládacího prvku. Tímto se vytvoří seznam obsahu z dřívějších krocích, které můžete použít jako obsah dekódovat. Všimněte si, že *textu* z příchozí HTTP je k dispozici pro použití jako obsah dekódování se žádost. Můžete také zadat obsah pro dekódování přímo do **obsah** ovládacího prvku.     
4. Vyberte *textu* značky. Všimněte si značky body je teď ve **obsahu** ovládacího prvku.
5. Vyberte název schématu, který chcete použít k dekódování obsah. Následující snímek obrazovky ukazuje, že *OrderFile* je název vybrané schéma. Tento název schématu měl nahraný v úvahu integrace dříve.
   
   ![Dialogové okno snímek obrazovky z plochých dekódování souboru](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png)    
6. Uložte svou práci.  
   ![Snímek obrazovky uložit ikonu](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

V tomto okamžiku jste dokončili nastavení plochého souboru dekódování konektor. V reálné aplikaci můžete ukládat dekódovaná data v řádku obchodní aplikaci, například služby Salesforce. Můžete snadno přidat akci pro odeslání výstupu dekódování akce do služby Salesforce.

Nyní můžete otestovat váš konektor tím, že požadavek na koncový bod HTTP a včetně obsah XML, který chcete dekódovat v textu požadavku.  

## <a name="next-steps"></a>Další postup
* [Další informace o integračního balíčku Enterprise](logic-apps-enterprise-integration-overview.md "Další informace o Enterprise integračního balíčku").  

