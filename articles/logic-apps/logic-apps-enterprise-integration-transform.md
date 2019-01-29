---
title: Transformace XML mezi formáty – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvoření mapy, které provádějí převod mezi formáty v Azure Logic Apps sadou Enterprise Integration Pack XML nebo transformací
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: add01429-21bc-4bab-8b23-bc76ba7d0bde
ms.date: 07/08/2016
ms.openlocfilehash: 4ebd96613378bbd907beb5109343a2427b1300b0
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095664"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Vytváření map, které transformují XML mezi formáty v Azure Logic Apps sadou Enterprise Integration Pack

Enterprise integration transformace konektor převádí data z jednoho formátu do jiného formátu. Například může mít příchozí zprávy, která obsahuje aktuální datum ve formátu YearMonthDay. Transformace můžete opakovaně formátovat datum ve formátu MonthDayYear.

## <a name="what-does-a-transform-do"></a>Co dělá transformace?
Transformace, která se také označuje jako mapu, se skládá ze zdroje XML schématu (vstup) a cíl XML schématu (výstup). Různé integrované funkce můžete použít k manipulaci nebo určit data, včetně manipulace s řetězci, podmíněného přiřazení, aritmetických výrazů, datum čas formátování a dokonce konstruktorech cyklů.

## <a name="how-to-create-a-transform"></a>Postup vytvoření transformace?
Pomocí sady Visual Studio můžete vytvořit transformace/map [Enterprise Integration SDK](https://aka.ms/vsmapsandschemas). Po dokončení vytváření a testování transformací, která nahrajete transformací, která se ke svému účtu integrace. 

## <a name="how-to-use-a-transform"></a>Použití transformace
Po transformaci nebo namapovat nahrajete do účtu pro integraci, můžete k vytvoření aplikace logiky. Pokaždé, když se aplikace logiky se aktivuje (a není nutné transformovat vstupní obsah), spuštění aplikace logiky transformace.

**Tady je postup používání transformace**:

### <a name="prerequisites"></a>Požadavky

* Vytvoření účtu pro integraci a přidání mapy  

Teď, když jste postaral o požadavky, je čas vytvořit aplikaci logiky:  

1. Vytvoření aplikace logiky a [propojit ho se svým účtem integrace](../logic-apps/logic-apps-enterprise-integration-accounts.md "se naučíte, jak propojit účet integrace aplikace logiky") , která obsahuje mapu.
2. Přidat **žádosti** trigger aplikace logiky  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Přidat **transformace XML** akce tak, že vyberete první **přidat akci**   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Zadejte slovo *transformace* do vyhledávacího pole filtrovat všechny akce, které ten, který chcete použít  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Vyberte **transformace XML** akce   
6. Přidání souboru XML **obsahu** , který je transformace. Můžete použít libovolná data XML se zobrazí v požadavku HTTP jako **obsahu**. V tomto příkladu vyberte tělo požadavku HTTP, který aktivuje aplikace logiky.

   > [!NOTE]
   > Ujistěte se, že obsah **transformace XML** je XML. Pokud obsah není ve formátu XML nebo je s kódováním base64, musíte zadat výraz, který zpracovává obsah. Například můžete použít [funkce](logic-apps-workflow-definition-language.md#functions), třeba ```@base64ToBinary``` pro dekódování obsahu nebo ```@xml``` pro zpracování obsahu ve formátu XML.
 

7. Vyberte název **MAPY** , kterou chcete použít k provedení transformace. Na mapě již musí být v účtu integrace. V jednom z předchozích kroků dáte už váš přístup k aplikaci logiky k, která obsahuje mapu účtu integrace.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Uložte si práci  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

V tomto okamžiku budete mít nastavení mapy. V reálné aplikaci můžete uložit Transformovaná data do aplikace LOB jsou například služby SalesForce. Můžete snadno jako akci, aby odesílal výstup transformace do Salesforce. 

Teď můžete otestovat vaši transformace tak, že požadavek na koncový bod HTTP.  


## <a name="features-and-use-cases"></a>Funkce a případy použití
* Transformace vytvořeny v objektu map lze jednoduché, jako je například kopírování název a adresu z jednoho dokumentu do jiného. Nebo můžete vytvářet složitější transformace pomocí operace out-of-the-box mapy.  
* Více operace mapování nebo funkce jsou snadno dostupné, včetně řetězců, čas funkce data a tak dále.  
* Vám pomůžou s přímým přístupem data kopírování mezi schémata. V mapování součástí sady SDK je to snadné – stačí kreslení čáry, která se připojuje elementy ve schématu zdroje s jejich protějšky v cílové schéma.  
* Při vytváření mapy, můžete zobrazit grafická reprezentace mapy, která znázorňuje vztahy a odkazy, které vytvoříte.
* Pomocí funkce mapy testu přidat ukázková zpráva XML. Jediným kliknutím můžete testovat mapu, kterou jste vytvořili a zobrazí se vygenerovaný výstup.  
* Nahrání existující mapování  
* Zahrnuje podporu pro formát XML.

## <a name="advanced-features"></a>Pokročilé funkce

### <a name="reference-assembly-or-custom-code-from-maps"></a>Odkaz na sestavení nebo vlastního kódu z mapy 
Akce transformace také podporuje mapy nebo transformuje s odkazem na externí sestavení. Tato možnost umožňuje volání vlastního kódu .NET přímo z XSLT mapy. Tady jsou požadavky na sestavení se používá v rámci služby maps.

* Na mapě a sestavení odkazované z mapování musí být [nahráli do účtu pro integraci](./logic-apps-enterprise-integration-maps.md). 

  > [!NOTE]
  > Mapy a sestavení nutných k odeslání v určitém pořadí. Než nahrajete mapa, který odkazuje na sestavení, musíte nahrát sestavení.

* Mapování musí také mít tyto atributy a oddíl CDATA, který obsahuje volání do kódu sestavení:

    * **název** je název vlastního sestavení.
    * **obor názvů** je obor názvů v sestavení, která obsahuje vlastní kód.

  Tento příklad ukazuje mapu, která odkazuje na sestavení s názvem "XslUtilitiesLib" a volání `circumreference` metoda ze sestavení.

  ```xml
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
  ```


### <a name="byte-order-mark"></a>Značka pořadí bajtů
Ve výchozím nastavení spustí odpověď od transformace značky pořadí bajtů (BOM). Tato funkce se zpřístupní pouze při práci v editoru kódu zobrazení. Chcete-li tuto funkci zakázat, zadejte `disableByteOrderMark` pro `transformOptions` vlastnost:

```json
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
```





## <a name="learn-more"></a>Další informace
* [Další informace o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "přečtěte si víc o Enterprise Integration Pack")  
* [Další informace o mapování](../logic-apps/logic-apps-enterprise-integration-maps.md "přečtěte si víc o podnikové integrace map")  

