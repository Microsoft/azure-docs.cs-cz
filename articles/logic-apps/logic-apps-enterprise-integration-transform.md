---
title: Převést data XML s transformací - Azure Logic Apps | Microsoft Docs
description: Vytvořit transformací nebo mapps převést data XML mezi formáty v logiku aplikace pomocí sady SDK integrace Enterprise
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: jeconnoc
editor: cgronlun
ms.assetid: add01429-21bc-4bab-8b23-bc76ba7d0bde
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 1621843d58954aa05a572f43fdab0fb16097332e
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299449"
---
# <a name="enterprise-integration-with-xml-transforms"></a>Enterprise integrace s transformace XML
## <a name="overview"></a>Přehled
Konektor Enterprise integrace transformace převádí data z jednoho formátu do jiného formátu. Například můžete mít příchozí zprávy obsahující aktuální datum ve formátu YearMonthDay. Transformace můžete přeformátujte datum ve formátu MonthDayYear.

## <a name="what-does-a-transform-do"></a>Transformace k čemu slouží?
Transformace, která je také označována jako mapu, se skládá z schéma XML pro zdroj (vstup) a cíl XML schema (výstup). Různé integrované funkce můžete použít k manipulaci nebo řízení dat, včetně manipulace s řetězci, podmíněného přiřazení, aritmetických výrazech, datum čas formátování a i opakování ve smyčce konstrukce.

## <a name="how-to-create-a-transform"></a>Postup vytvoření transformace?
Transformace nebo mapu můžete vytvořit pomocí sady Visual Studio [Enterprise integrace SDK](https://aka.ms/vsmapsandschemas). Po dokončení vytváření a testování pro transformaci nahrajete do účtu integrace pro transformaci. 

## <a name="how-to-use-a-transform"></a>Postup použití transformace
Po odeslání transformace nebo mapy ke svému účtu integrace, můžete k vytvoření aplikace logiky. Aplikace logiky běží vaše transformace vždy, když se aktivuje aplikaci logiky (a vstupní obsah, který potřebuje k transformaci).

**Tady jsou kroky při použití transformace**:

### <a name="prerequisites"></a>Požadavky

* Vytvoření účtu integrace a přidejte do ní mapy  

Teď, když jste postaráno, požadované součásti, je čas vytvořit svou aplikaci logiky:  

1. Vytvoření aplikace logiky a [ho propojit se svým účtem integrace](../logic-apps/logic-apps-enterprise-integration-accounts.md "zjistěte, jak lze propojit účet integrace aplikace logiky") obsahující mapy.
2. Přidat **požadavku** aktivační události do aplikace logiky  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Přidat **transformace XML** akce první výběrem **přidat akci**   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Zadejte slovo *transformace* do vyhledávacího pole vyfiltrujete všechny akce, které ten, který chcete použít  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Vyberte **transformace XML** akce   
6. Přidání souboru XML **obsahu** , můžete transformace. Můžete použít libovolná data XML, který se zobrazí v požadavku HTTP, jako **obsahu**. V tomto příkladu vyberte textu požadavku HTTP, který aktivoval aplikaci logiky.

   > [!NOTE]
   > Ujistěte se, že obsah **transformace XML** formátu XML. Pokud obsah není v kódu XML nebo je kódováním base64, musíte zadat výraz, který zpracovává obsah. Například můžete použít [funkce](logic-apps-workflow-definition-language.md#functions), například ```@base64ToBinary``` pro dekódování obsah nebo ```@xml``` pro zpracování obsahu ve formátu XML.
 

7. Vyberte název **MAPY** , kterou chcete použít k provedení transformace. Mapy již musí být v účtu integrace. V předchozím kroku dáte již logiku aplikace přístup k vašemu účtu integrace, která obsahuje vaše mapy.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Uložte si práci  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

V tomto okamžiku jste dokončili nastavení mapy. V reálné aplikaci můžete ukládat Transformovaná data obchodní aplikace, například služby SalesForce. Můžete snadno jako akce pro odeslání výstupu transformace do služby Salesforce. 

Nyní můžete otestovat váš transformace tak, že požadavek na koncový bod HTTP.  


## <a name="features-and-use-cases"></a>Funkce a případy použití
* Transformace vytvořené v mapu může být jednoduchý, jako je například kopírování názvu a adresy z jednoho dokumentu do jiného. Nebo můžete vytvořit složitější transformace pomocí operace, které se na pole mapy.  
* Více mapy operace nebo funkce jsou snadno dostupné, včetně řetězce, datum časové funkce a tak dále.  
* Můžete to udělat kopii přímá dat mezi schémat. V Mapper zahrnutý v sadě SDK to je jednoduché, kreslení čáry propojující se svými protějšky ve schématu cílové elementy ve schématu zdroje.  
* Při vytváření mapy, zobrazí se grafické zobrazení mapy, které ukazují vztahy a odkazy, které vytvoříte.
* Použijte funkci Test mapy přidat ukázkovou zprávu XML. S jediným kliknutím můžete otestovat mapu, kterou jste vytvořili a najdete v části generovaný výstup.  
* Nahrát existující mapy  
* Zahrnuje podporu pro formát XML.

## <a name="advanced-features"></a>Pokročilé funkce

### <a name="reference-assembly-or-custom-code-from-maps"></a>Odkaz na sestavení nebo vlastní kód z mapy 
Akce transformace také podporuje mapy nebo transformuje nese odkaz na externí sestavení. Tato funkce umožňuje volání vlastní kód .NET přímo z XSLT mapy. Tady jsou požadavky pro použití sestavení v rámci služby maps.

* Mapy a sestavení na něj odkazovat z mapy, musí být [integrace účet nahraje](./logic-apps-enterprise-integration-maps.md). 

  > [!NOTE]
  > Mapy a sestavení nutných k odeslání v určitém pořadí. Před nahráním mapu, která odkazuje na sestavení, je potřeba načíst sestavení.

* Mapa musí rovněž mít tyto atributy a CDATA oddíl, který obsahuje volání kódu sestavení:

    * **název** je název vlastního sestavení.
    * **obor názvů** je obor názvů ve vaší sestavení, která obsahuje vlastní kód.

  Tento příklad ukazuje mapu, která odkazuje na sestavení s názvem "XslUtilitiesLib" a volání `circumreference` metoda ze sestavení.

  ````xml
  <?xml version="1.0" encoding="UTF-8"?>
  <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
  <msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
  </msxsl:script>
  <xsl:template match="data">
     <circles>
        <xsl:for-each select="circle">
            <circle>
                <xsl:copy-of select="node()"/>
                    <circumference>
                        <xsl:value-of select="user:circumference(radius)"/>
                    </circumference>
            </circle>
        </xsl:for-each>
     </circles>
    </xsl:template>
    </xsl:stylesheet>
  ````


### <a name="byte-order-mark"></a>Značka pořadí bajtů
Ve výchozím nastavení spustí odpověď z transformace značky pořadí bajtů (BOM). Tuto funkci můžete přistupovat pouze při práci v editoru kódu zobrazení. Chcete-li zakázat tuto funkci, zadejte `disableByteOrderMark` pro `transformOptions` vlastnost:

````json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
````





## <a name="learn-more"></a>Další informace
* [Další informace o integračního balíčku Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Další informace o Enterprise integračního balíčku")  
* [Další informace o mapování](../logic-apps/logic-apps-enterprise-integration-maps.md "Další informace o enterprise integrace mapy")  

