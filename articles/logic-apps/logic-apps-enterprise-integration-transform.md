---
title: Transformovat XML mezi formáty
description: Vytvořte transformace nebo mapy, které převádějí XML mezi formáty v Azure Logic Apps pomocí Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 038c1d4c0f0b5ffd7b9aabea2de32e3a44e3b221
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654128"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Vytváření map transformujících XML mezi formáty v Azure Logic Apps s využitím rozšíření Enterprise Integration Pack

Konektor pro transformace podnikové integrace převádí data z jednoho formátu do jiného. Například můžete mít příchozí zprávu, která obsahuje aktuální datum ve formátu rok-měsíc-den. Pomocí transformace můžete toto datum převést do formátu měsíc-den-rok.

## <a name="what-does-a-transform-do"></a>Co dělá transformace?
Transformace, která se také označuje jako mapa, se skládá ze zdrojového schématu XML (Input) a cílového schématu XML (výstup). Můžete použít různé integrované funkce pro usnadnění manipulace s daty nebo jejich řízení, včetně manipulace s řetězci, podmíněného přiřazení, aritmetických výrazů, formátovacích modulů data a času a dokonce i konstrukcí smyček.

## <a name="how-to-create-a-transform"></a>Jak vytvořit transformaci?
Můžete vytvořit transformaci nebo mapu pomocí sady Visual Studio [Podniková integrace SDK](https://aka.ms/vsmapsandschemas). Až dokončíte vytváření a testování transformace, nahrajete transformaci do účtu pro integraci. 

## <a name="how-to-use-a-transform"></a>Jak použít transformaci
Po nahrání transformace nebo mapy do účtu pro integraci ji můžete použít k vytvoření aplikace logiky. Aplikace logiky spouští vaše transformace vždy, když se aktivuje aplikace logiky (a vstupní obsah, který je nutné transformovat).

Následující **postup použijte k transformaci**:

### <a name="prerequisites"></a>Předpoklady

* Vytvořit účet pro integraci a přidat k němu mapu  

Teď, když jste se seznámili s požadavky, je čas vytvořit aplikaci logiky:  

1. Vytvořte aplikaci logiky a [propojte ji s účtem pro integraci](./logic-apps-enterprise-integration-create-integration-account.md "Naučte se propojit účet pro integraci s aplikací logiky.") , který obsahuje mapu.
2. Přidání triggeru **žádosti** do aplikace logiky  
   ![Snímek obrazovky s rozevíracím seznamem zobrazit rozhraní API spravovaná Microsoftem s vybraným triggerem žádosti Rozevírací seznam je v aplikaci logiky vytvořené pomocí sady Visual Studio Enterprise Integration SDK.](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Přidejte akci **transformace XML** , a to tak, že nejprve vyberete **přidat akci** .   
   ![Snímek obrazovky s vybraným tlačítkem přidat akci na obrazovce triggeru žádosti](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Pokud chcete filtrovat všechny akce, které chcete použít, zadejte do vyhledávacího pole *transformaci* slova.  
   ![Snímek obrazovky ukazující, jak vyhledat akci transformace XML v rozevíracím seznamu zobrazit rozhraní API spravované Microsoftem, aby se mohla přidat do triggeru žádosti](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Vybrat akci **transformace XML**   
6. Přidejte **obsah** XML, který transformujete. Jako **obsah** můžete použít jakákoli data XML, která obdržíte v požadavku HTTP. V tomto příkladu vyberte tělo požadavku HTTP, který aktivoval aplikaci logiky.

   > [!NOTE]
   > Ujistěte se, že obsah pro **transformaci XML** je XML. Pokud obsah není ve formátu XML nebo je kódovaný v kódování Base64, je nutné zadat výraz, který zpracovává obsah. Například můžete použít [funkce](logic-apps-workflow-definition-language.md#functions), například ```@base64ToBinary``` pro dekódování obsahu nebo ```@xml``` pro zpracování obsahu jako XML.
 

7. Vyberte název **mapy** , kterou chcete použít k provedení transformace. Tato mapa již musí být v účtu pro integraci. V předchozím kroku už máte aplikaci logiky přístup k vašemu účtu pro integraci, který obsahuje vaši mapu.      
   ![Snímek obrazovky zobrazující obsah a pole mapy na obrazovce transformace XML pro aktivační událost požadavku](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Uložení práce  
    ![Snímek obrazovky zobrazující tlačítko Uložit v Návrháři Logic Apps.](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

V tuto chvíli jste dokončili nastavování mapy. V reálné aplikaci budete možná chtít ukládat transformovaná data do obchodní aplikace, jako je SalesForce. K odeslání výstupu transformace do Salesforce můžete snadno použít akci. 

Tuto transformaci teď můžete testovat tím, že vytvoříte požadavek na koncový bod HTTP.  


## <a name="features-and-use-cases"></a>Funkce a případy použití
* Transformace vytvořená v mapě může být jednoduchá, jako je například kopírování názvu a adresy z jednoho dokumentu na jiný. Nebo můžete vytvořit složitější transformace pomocí připravených operací mapování.  
* K dispozici je více operací nebo funkcí mapování, včetně řetězců, funkcí data a času a tak dále.  
* Mezi schématy můžete vytvořit přímou datovou kopii. V mapovači obsaženém v sadě SDK je to jednoduché jako vykreslení čáry, která spojuje prvky ve zdrojovém schématu se svými protějšky v cílovém schématu.  
* Při vytváření mapy zobrazíte grafické znázornění mapy, které zobrazuje všechny vztahy a odkazy, které vytvoříte.
* Pomocí funkce test map přidejte ukázkovou zprávu XML. Jediným kliknutím můžete otestovat mapu, kterou jste vytvořili, a zobrazit vygenerovaný výstup.  
* Nahrát existující mapy  
* Zahrnuje podporu formátu XML.

## <a name="advanced-features"></a>Pokročilé funkce

### <a name="reference-assembly-or-custom-code-from-maps"></a>Odkazování na sestavení nebo vlastní kód z map 
Akce Transform podporuje také mapy nebo transformace s odkazem na externí sestavení. Tato schopnost umožňuje volat vlastní kód .NET přímo z map XSLT. Tady jsou požadavky na použití sestavení v mapách.

* Mapa a sestavení, na které odkazuje mapa, musí být [nahrány do účtu pro integraci](./logic-apps-enterprise-integration-maps.md). 

  > [!NOTE]
  > Mapování a sestavení musí být nahrány v určitém pořadí. Sestavení je nutné nahrát předtím, než nahrajete mapu, která odkazuje na sestavení.

* Mapa musí mít také tyto atributy a oddíl CDATA, který obsahuje volání kódu sestavení:

    * **název** je název vlastního sestavení.
    * **obor názvů** je obor názvů ve vašem sestavení, které obsahuje vlastní kód.

  Tento příklad ukazuje mapu, která odkazuje na sestavení s názvem "XslUtilitiesLib" a volá `circumreference` metodu ze sestavení.

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


### <a name="byte-order-mark"></a>Znak pořadí bajtů
Ve výchozím nastavení se odpověď z transformace začíná znakem pořadí bajtů (BOM). K této funkci můžete přistupovat pouze při práci v editoru zobrazení kódu. Chcete-li tuto funkci zakázat, zadejte `disableByteOrderMark` pro `transformOptions` vlastnost:

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
* [Další informace o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informace o Enterprise Integration Pack")  
* [Další informace o mapách](../logic-apps/logic-apps-enterprise-integration-maps.md "Další informace o službě Enterprise Integration Maps")  