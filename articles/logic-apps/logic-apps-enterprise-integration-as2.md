---
title: Zprávy AS2 pro podnikovou integraci B2B – Azure Logic Apps | Dokumentace Microsoftu
description: Výměna zpráv AS2 pro podnikovou integraci B2B v Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.date: 06/08/2017
ms.openlocfilehash: 2604cdd6bf758858328c2d30fc4cde535f0a7148
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124658"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Výměna zpráv AS2 pro podnikovou integraci B2B v Azure Logic Apps sadou Enterprise Integration Pack

Předtím, než pro Azure Logic Apps můžou vyměňovat zprávy AS2, musíte vytvořit smlouvy AS2 a uložení této smlouvy v účtu integrace. Tady jsou kroky pro vytvoření smlouvy AS2.

## <a name="before-you-start"></a>Než začnete

Tady je položky, které budete potřebovat:

* [Účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-accounts.md) , který již má definovaný a spojené s předplatným Azure
* Alespoň dva [partneři](logic-apps-enterprise-integration-partners.md) , které jsou již definovány v účtu integrace a nakonfigurovanou kvalifikátor AS2 v rámci **obchodní identity**

> [!NOTE]
> Když vytvoříte smlouvu, obsah v souboru smlouvy musí odpovídat typ smlouvy.    

Poté co [vytvořit integrační účet](../logic-apps/logic-apps-enterprise-integration-accounts.md) a [přidání partnerů](logic-apps-enterprise-integration-partners.md), smlouvy AS2 můžete vytvořit pomocí následujících kroků.

## <a name="create-an-as2-agreement"></a>Vytvoření smlouvy AS2

1.  Přihlaste se na web [Azure Portal](http://portal.azure.com "Azure Portal").  

2. V hlavní nabídce Azure zvolte **všechny služby**. Do vyhledávacího pole zadejte "integrace" a pak vyberte **účty pro integraci**.

   ![Vyhledejte svůj účet integrace](./media/logic-apps-enterprise-integration-as2/overview-1.png)

   > [!TIP]
   > Pokud nevidíte **všechny služby**, možná budete muset nejprve rozbalte nabídku. V horní nabídce sbalený vyberte **zobrazit textové popisky**.

3. V části **účty pro integraci**, vyberte účet integrace, ve kterém chcete vytvořit smlouvu.

   ![Vyberte místo pro vytvoření smlouvu účtu integrace](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Zvolte **smlouvy** dlaždici. Pokud nemáte k dispozici dlaždici smlouvy, přidejte nejprve na dlaždici.

    ![Zvolte dlaždici "smlouvy o"](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. V části **smlouvy**, zvolte **přidat**.

    ![Zvolte "Přidat"](./media/logic-apps-enterprise-integration-as2/agreement-2.png)

6. V části **přidat**, zadejte **název** pro vaši smlouvu. Pro **typ smlouvy**vyberte **AS2**. Vyberte **partner s identitou hostitele**, **identita hostitele**, **partner s identitou hosta**, a **identita hosta** pro vaši smlouvu.

    ![Zadejte podrobnosti o smlouvě](./media/logic-apps-enterprise-integration-as2/agreement-3.png)  

    | Vlastnost | Popis |
    | --- | --- |
    | Název |Název smlouvy |
    | Typ smlouvy | By měl být AS2 |
    | Partner s identitou hostitele |Smlouvu musí hostitelské i hostující partnera. Partner hostitele představuje organizace, který konfiguruje smlouvy. |
    | Identita hostitele |Identifikátor pro hostitele partnera |
    | Partner s identitou hosta |Smlouvu musí hostitelské i hostující partnera. Partner s identitou hosta představuje organizace, která je podnikající s partnerem hostitele. |
    | Identita hosta |Identifikátor partner s identitou hosta |
    | Nastavení příjmu |Tyto vlastnosti se vztahují na všechny zprávy přijaté službou smlouvu. |
    | Nastavení odesílání |Tyto vlastnosti se vztahují na všechny zprávy odeslané dohodou. |

## <a name="configure-how-your-agreement-handles-received-messages"></a>Nakonfigurujte, jak vaše smlouvy popisovače přijatých zpráv

Teď, když jste nastavili vlastnosti smlouvy, můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává příchozí zprávy přijaté od svého partnera prostřednictvím této smlouvy.

1.  V části **přidat**vyberte **přijímat nastavení**.
Konfigurovat tyto vlastnosti závislosti na vaší smlouvě s partnerem, který vyměňuje zprávy s vámi. Popisy vlastností najdete v tabulce v této části.

    ![Konfigurace "Obdrží nastavení"](./media/logic-apps-enterprise-integration-as2/agreement-4.png)

2. Volitelně můžete přepsat vlastnosti příchozí zprávy tak, že vyberete **přepsat vlastnosti zprávy**.

3. Chcete-li vyžadovat všechny příchozí zprávy podepsané, vyberte **zprávu je nutné podepsat**. Z **certifikát** seznamu, vyberte existující [veřejný certifikát partnera hosta](../logic-apps/logic-apps-enterprise-integration-certificates.md) pro ověření podpisu na zprávy. Nebo vytvořte certifikát, pokud ho nemáte.

4.  Chcete-li vyžadovat všechny příchozí zprávy šifrování, vyberte **zprávu je nutné zašifrovat**. Z **certifikát** seznamu, vyberte existující [privátní certifikát hostitele partnera](../logic-apps/logic-apps-enterprise-integration-certificates.md) pro dešifrování příchozí zprávy. Nebo vytvořte certifikát, pokud ho nemáte.

5. Požadovat zprávy, aby se komprimoval, vyberte **zprávu je nutné zkomprimovat**.

6. Chcete-li odeslat oznámení dispozice synchronní zprávy (MDN) pro přijaté zprávy, vyberte **odesílat zprávy MDN**.

7. Chcete-li odeslat podepsaný něho pro přijaté zprávy, vyberte **odeslat podepsanou zprávu MDN**.

8. Pokud chcete odesílat asynchronní něho pro přijaté zprávy, vyberte **odesílat asynchronní zprávy MDN**.

9. Až budete hotovi, ujistěte se, že uložte nastavení výběrem **OK**.

Smlouvy o je nyní připravena ke zpracování příchozích zpráv, které v souladu s vámi vybrané nastavení.

| Vlastnost | Popis |
| --- | --- |
| Přepsat vlastnosti zprávy |Označuje, že se dá přepsat vlastnosti v přijaté zprávy. |
| Zprávu je nutné podepsat. |Vyžaduje zprávy digitálně podepsané. Nakonfigurujte partnerské hosta veřejný certifikát pro ověření podpisu.  |
| Zprávu je nutné zašifrovat. |Vyžaduje šifrování zpráv. Zprávy šifrované bez odmítají. Nakonfigurujte privátní certifikát hostitele partnera pro dešifrování zprávy.  |
| Zprávu je nutné zkomprimovat. |Vyžaduje zprávy, aby se komprimoval. Bez komprimované zprávy jsou odmítnuta. |
| Text zprávy MDN |Výchozí zprávu dispozice oznámení (MDN) k odeslání do odesílatele zprávy. |
| Odeslat zprávu MDN |Vyžaduje něho k odeslání. |
| Odeslat podepsanou zprávu MDN |Vyžaduje něho podepsat. |
| Algoritmus MIC |Vyberte algoritmus použitý k podepsání zprávy. |
| Odeslat asynchronní zprávu MDN | Vyžaduje zpráv k odeslání asynchronně. |
| zprostředkovatele identity | Zadejte adresu URL, kam má odesílat něho. |

## <a name="configure-how-your-agreement-sends-messages"></a>Nakonfigurujte, jak vaši smlouvu odesílá zprávy

Můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává odchozí zprávy, které jste odeslali partnerům prostřednictvím této smlouvy.

1.  V části **přidat**vyberte **odeslat nastavení**.
Konfigurovat tyto vlastnosti závislosti na vaší smlouvě s partnerem, který vyměňuje zprávy s vámi. Popisy vlastností najdete v tabulce v této části.

    ![Nastavte "Odeslat" nastavení](./media/logic-apps-enterprise-integration-as2/agreement-51.png)

2. Chcete-li odeslat podepsané zprávy svého partnera, vyberte **povolit podepisování zpráv**. Pro podepisování zpráv, v **algoritmus MIC** seznamu, vyberte *privátní certifikát hostitele partnera algoritmus MIC*. A **certifikát** seznamu, vyberte existující [privátní certifikát hostitele partnera](../logic-apps/logic-apps-enterprise-integration-certificates.md).

3. K odesílání šifrovaných zpráv partnerovi, vyberte **povolit šifrování zpráv**. Pro šifrování zpráv, v **šifrovací algoritmus** seznamu, vyberte *algoritmus veřejný certifikát partnera hosta*.
A **certifikát** seznamu, vyberte existující [veřejný certifikát partnera hosta](../logic-apps/logic-apps-enterprise-integration-certificates.md).

4. Komprimují se zpráva, vyberte **povolit kompresi zpráv**.

5. Chcete-li rozbalit hlavičku HTTP content-type na jeden řádek, vyberte **hlavičky protokolu HTTP při rozvinutí**.

6. Chcete-li přijímat synchronní něho pro odeslané zprávy, vyberte **požadovat zprávy MDN**.

7. Pro příjem podepsaný něho pro odeslané zprávy, vyberte **požadovat podepsané zprávy MDN**.

8. Pokud chcete přijímat asynchronní něho pro odeslané zprávy, vyberte **požadovat asynchronní zprávy MDN**. Pokud vyberete tuto možnost, zadejte adresu URL, kam můžete odeslat něho.

9. Chcete-li vyžadovat neodvolatelnost příjmu, vyberte **povolit NRR**.  

10. Chcete-li určit formát algoritmus MIC nebo podepisování v odchozích hlavičkách MDN nebo zprávy AS2, vyberte **formát algoritmu SHA2**.  

11. Až budete hotovi, ujistěte se, že uložte nastavení výběrem **OK**.

Nyní je připravená pro zpracování odchozích zpráv, které v souladu s vámi vybrané nastavení smlouvy.

| Vlastnost | Popis |
| --- | --- |
| Povolit podepisování zpráv |Vyžaduje všechny zprávy odeslané z dohody podepsat. |
| Algoritmus MIC |Algoritmus použitý k podepsání zprávy. Nakonfiguruje privátní certifikát hostitele partnera algoritmus MIC k podepsání zprávy. |
| Certifikát |Vyberte certifikát, který chcete použít pro podepisování zpráv. Nakonfiguruje hostitele partnera privátní certifikát pro podpis zprávy. |
| Povolit šifrování zpráv |Vyžaduje šifrování všechny zprávy odeslané z této smlouvy. Nakonfiguruje algoritmus hosta partnera veřejný certifikát pro šifrování zprávy. |
| Šifrovací algoritmus |Šifrovací algoritmus pro šifrování zpráv. Nakonfiguruje hosta partnera veřejný certifikát pro šifrování zprávy. |
| Certifikát |Certifikát, který se má použít k šifrování zpráv. Konfiguruje partnerské hostovaného privátního certifikátu pro šifrování zprávy. |
| Povolit kompresi zpráv |Vyžaduje komprese všechny zprávy odeslané z této smlouvy. |
| Rozbalit hlavičky HTTP |Umístí hlavičku HTTP content-type na jednom řádku. |
| Požadovat zprávy MDN |Vyžaduje zprávy MDN. pro všechny zprávy odeslané z této smlouvy. |
| Požadovat podepsané zprávy MDN |Vyžaduje všechny něho, které se odesílají na tuto smlouvu podepsat. |
| Požadovat asynchronní zprávy MDN |Vyžaduje asynchronní něho k odeslání do této smlouvy. |
| zprostředkovatele identity |Zadejte adresu URL, kam má odesílat něho. |
| Povolit NRR |Vyžaduje neodvolatelnost příjmu (NRR) komunikaci atribut, který poskytuje důkazy, který uživateli přišel data, jak je řešit. |
| Formát algoritmu SHA2 |Vyberte formát algoritmus MIC nebo podepisování v odchozích hlavičkách MDN nebo zprávy AS2 |

## <a name="find-your-created-agreement"></a>Najít vytvořený smlouvy

1. Po dokončení nastavení na všechny vlastnosti vaší smlouvy **přidat** zvolte **OK** dokončit vytváření vaší smlouvě a vrátit ke svému účtu integrace.

    Smlouvy nově přidané se zobrazí ve vašich **smlouvy** seznamu.

2. Můžete také zobrazit vaše smlouvy v přehled vašeho účtu integrace. V nabídce účtu integrace, zvolte **přehled**a pak **smlouvy** dlaždici. 

   ![Zvolte dlaždici "smlouvy o" Chcete-li zobrazit všechny smlouvy](./media/logic-apps-enterprise-integration-as2/agreement-6.png)

## <a name="view-the-swagger"></a>Zobrazení swaggeru
Zobrazit [swagger podrobnosti](/connectors/as2/). 

## <a name="next-steps"></a>Další postup
* [Další informace o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "přečtěte si víc o Enterprise Integration Pack")  
