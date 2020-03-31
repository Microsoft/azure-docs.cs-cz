---
title: Transformace XML mezi formáty
description: Vytváření transformací nebo map, které převádějí XML mezi formáty v logicových aplikacích Azure pomocí sady Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 500769a39ba7658b35c1abb80101f6234170c941
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792394"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Vytvářejte mapy, které transformují XML mezi formáty v Logic Apps Azure pomocí sady Enterprise Integration Pack

Spojnice Transformace podnikové integrace převádí data z jednoho formátu do jiného formátu. Můžete mít například příchozí zprávu, která obsahuje aktuální datum ve formátu YearMonthDay. Transformaci můžete použít k přeformátování data, které má být ve formátu MonthDayYear.

## <a name="what-does-a-transform-do"></a>K čemu transformace dělá?
Transformace, která je také známá jako mapa, se skládá ze schématu XML zdroje (vstup) a cílového schématu XML (výstup). Můžete použít různé vestavěné funkce, které pomáhají manipulovat nebo řídit data, včetně manipulace s řetězci, podmíněných přiřazení, aritmetické výrazy, datum čas formatters a dokonce i opakování konstrukce.

## <a name="how-to-create-a-transform"></a>Jak vytvořit transformaci?
Transformovat nebo mapovat pomocí sady Visual Studio [Enterprise Integration SDK](https://aka.ms/vsmapsandschemas). Po dokončení vytváření a testování transformace nahrajete transformaci do účtu integrace. 

## <a name="how-to-use-a-transform"></a>Jak používat transformaci
Po nahrání transformace/mapy do účtu integrace ji můžete použít k vytvoření aplikace logiky. Aplikace Logika spustí transformace vždy, když se aktivuje aplikace logiky (a je vstupní obsah, který je třeba transformovat).

**Zde jsou kroky pro použití transformace**:

### <a name="prerequisites"></a>Požadavky

* Vytvoření účtu integrace a přidání mapy  

Teď, když jste se postarali o předpoklady, je čas vytvořit aplikaci Logika:  

1. Vytvořte aplikaci logiky a [propojte ji s účtem integrace,](../logic-apps/logic-apps-enterprise-integration-accounts.md "Naučte se propojit účet integrace s aplikací Logika") který obsahuje mapu.
2. Přidání aktivační **události požadavku** do aplikace Logika  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Přidání akce **Transformovat XML** tak, že nejprve vyberete **Přidat akci**   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Zadejte *do* vyhledávacího pole transformaci slov a vyfiltrujte všechny akce na tu, kterou chcete použít.  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Výběr akce **Transformace XML**   
6. Přidejte **obsah** XML, který transformujete. Jako **OBSAH**můžete použít všechna data XML, která obdržíte v požadavku HTTP . V tomto příkladu vyberte text požadavku HTTP, který spustil aplikaci Logika.

   > [!NOTE]
   > Ujistěte se, že obsah **transformačního XML** je XML. Pokud obsah není ve formátu XML nebo je kódován base64, musíte zadat výraz, který obsah zpracovává. Můžete například použít [funkce](logic-apps-workflow-definition-language.md#functions) ```@base64ToBinary``` , například ```@xml``` pro dekódování obsahu nebo pro zpracování obsahu jako XML.
 

7. Vyberte název **map,** který chcete použít k provedení transformace. Mapa již musí být ve vašem integračním účtu. V dřívějším kroku jste už aplikaci Logika udělili přístup k účtu integrace, který obsahuje vaši mapu.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Uložení práce  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

V tomto okamžiku jste dokončili nastavení mapy. V aplikaci reálného světa můžete chtít uložit transformovaná data v obchodní aplikaci, jako je SalesForce. Můžete snadno jako akce odeslat výstup transformace Salesforce. 

Nyní můžete otestovat transformaci tím, že požadavek na koncový bod HTTP.  


## <a name="features-and-use-cases"></a>Funkce a případy použití
* Transformace vytvořená v mapě může být jednoduchá, například kopírování názvu a adresy z jednoho dokumentu do druhého. Nebo můžete vytvořit složitější transformace pomocí operací mapy out-of-the-box.  
* Více mapové operace nebo funkce jsou snadno dostupné, včetně řetězců, funkce čas data a tak dále.  
* Mezi schématy můžete provést přímou kopii dat. V mapovači zahrnuté v sadě SDK je to stejně jednoduché jako kreslení čáry, která spojuje prvky ve zdrojovém schématu s jejich protějšky v cílovém schématu.  
* Při vytváření mapy zobrazíte grafické znázornění mapy, která zobrazuje všechny vztahy a odkazy, které vytvoříte.
* Pomocí funkce Testovací mapa můžete přidat ukázkovou zprávu XML. Jednoduchým kliknutím můžete otestovat mapu, kterou jste vytvořili, a zobrazit generovaný výstup.  
* Nahrání existujících map  
* Zahrnuje podporu formátu XML.

## <a name="advanced-features"></a>Pokročilé funkce

### <a name="reference-assembly-or-custom-code-from-maps"></a>Referenční sestavení nebo vlastní kód z map 
Akce transformace také podporuje mapy nebo transformace s odkazem na externí sestavení. Tato funkce umožňuje volání vlastního kódu .NET přímo z map XSLT. Zde jsou předpoklady pro použití sestavení v mapách.

* Mapu a sestavení odkazované z mapy je třeba [nahrát do integračního účtu](./logic-apps-enterprise-integration-maps.md). 

  > [!NOTE]
  > Mapa a sestavení musí být nahrány v určitém pořadí. Před nahráním mapy, která odkazuje na sestavení, je nutné nahrát sestavení.

* Mapa musí mít také tyto atributy a oddíl CDATA, který obsahuje volání kódu sestavení:

    * **název** je vlastní název sestavení.
    * **obor názvů** je obor názvů v sestavení, který obsahuje vlastní kód.

  Tento příklad ukazuje mapu, která odkazuje na sestavení s názvem "XslUtilitiesLib" a volá metodu `circumreference` ze sestavení.

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


### <a name="byte-order-mark"></a>Značka objednávky bajtů
Ve výchozím nastavení začíná odpověď z transformace znakem objednavace (BOM). K této funkci můžete přistupovat pouze při práci v editoru zobrazení kódu. Chcete-li tuto funkci `disableByteOrderMark` zakázat, zadejte `transformOptions` vlastnost:

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
* [Další informace o balíčku Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Další informace o balíčku Enterprise Integration Pack")  
* [Další informace o mapách](../logic-apps/logic-apps-enterprise-integration-maps.md "Další informace o mapách podnikové integrace")  

