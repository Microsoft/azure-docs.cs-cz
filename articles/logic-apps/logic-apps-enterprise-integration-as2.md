---
title: AS2 zpráv B2B enterprise integrace - Azure Logic Apps | Microsoft Docs
description: Výměna AS2 zpráv B2B enterprise integraci s Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: jeconnoc
editor: ''
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 8984b76c68ebd562ce9e5af9bded859b38d498da
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298235"
---
# <a name="exchange-as2-messages-for-enterprise-integration-with-logic-apps"></a>Výměna zpráv AS2 pro podnikové integrace s logic apps

Před výměnou zpráv AS2 pro Azure Logic Apps, musíte vytvořit smlouvy AS2 a uložení této smlouvy ve vašem účtu integrace. Tady jsou kroky pro vytvoření smlouvy AS2.

## <a name="before-you-start"></a>Než začnete

Tady je položky, které budete potřebovat:

* [Integrace účet](../logic-apps/logic-apps-enterprise-integration-accounts.md) který již má definovaný a přidružené k předplatnému Azure
* Alespoň dva [partnery](logic-apps-enterprise-integration-partners.md) které již jsou definované ve vašem účtu integrace a nakonfigurované kvalifikátor AS2 pod **obchodní identit**

> [!NOTE]
> Když vytvoříte smlouvu, obsah v souboru smlouvy musí odpovídat typ smlouvy.    

Po jste [vytvoření účtu integrace](../logic-apps/logic-apps-enterprise-integration-accounts.md) a [přidat partnery](logic-apps-enterprise-integration-partners.md), AS2 smlouvu můžete vytvořit pomocí následujících kroků.

## <a name="create-an-as2-agreement"></a>Vytvoření smlouvy AS2

1.  Přihlaste se na web [Azure Portal](http://portal.azure.com "Azure Portal").  

2. V hlavní nabídce Azure, vyberte **všechny služby**. Do vyhledávacího pole zadejte "integraci" a potom vyberte **účty pro integraci**.

   ![Najít váš účet integrace](./media/logic-apps-enterprise-integration-as2/overview-1.png)

   > [!TIP]
   > Pokud nevidíte **všechny služby**, možná budete muset nejdřív rozbalte nabídku. V horní nabídce sbalené, vyberte **zobrazit text popisky**.

3. V části **účty pro integraci**, vyberte účet integrace, kde chcete vytvořit smlouvu.

   ![Vyberte účet, integrace místo pro vytvoření této smlouvy](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Vyberte **smlouvy** dlaždici. Pokud nemáte dlaždici smlouvy, přidejte nejprve dlaždici.

    ![Vyberte že dlaždici "Smlouvy"](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. V části **smlouvy**, zvolte **přidat**.

    ![Zvolte "Přidat"](./media/logic-apps-enterprise-integration-as2/agreement-2.png)

6. V části **přidat**, zadejte **název** pro vaše smlouvy. Pro **typ smlouvy**, vyberte **AS2**. Vyberte **hostitele partnera**, **identitu hostitele**, **hosta partnera**, a **hosta Identity** pro vaše smlouvy.

    ![Zadejte podrobnosti o smlouvě](./media/logic-apps-enterprise-integration-as2/agreement-3.png)  

    | Vlastnost | Popis |
    | --- | --- |
    | Název |Název smlouvy |
    | Typ smlouvy | Musí být AS2 |
    | Partner s identitou hostitele |Smlouvu musí hostitelské i hostované partnera. Partner hostitele představuje organizace, která nakonfiguruje smlouvu. |
    | Identita hostitele |Identifikátor pro hostitele partnera |
    | Partner s identitou hosta |Smlouvu musí hostitelské i hostované partnera. Partner hosta představuje organizace, která je spolupráci s partnery hostitele. |
    | Identita hosta |Identifikátor pro partnera hosta |
    | Nastavení příjmu |Tyto vlastnosti se vztahují na všechny zprávy přijaté službou smlouvu. |
    | Nastavení odesílání |Tyto vlastnosti se vztahují na všechny zprávy odeslané smlouvu. |

## <a name="configure-how-your-agreement-handles-received-messages"></a>Nakonfigurujte, jak vaše smlouvy popisovače přijatých zpráv

Teď, když jste nastavili vlastnosti smlouvy, můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává příchozí zprávy přijaté od svého partnera prostřednictvím této smlouvy.

1.  V části **přidat**, vyberte **přijímat nastavení**.
Konfigurujte tyto vlastnosti závislosti na vaší smlouvě se partnera, výměny zpráv s vámi. Popisy vlastností najdete v tabulce v této části.

    ![Konfigurace "Obdrží nastavení"](./media/logic-apps-enterprise-integration-as2/agreement-4.png)

2. Volitelně můžete přepsat vlastnosti příchozí zprávy výběrem **přepsat vlastnosti zprávy**.

3. Pokud chcete vyžadovat všechny příchozí zprávy podepsat, vyberte **zpráva by měla být podepsána**. Z **certifikát** vyberte existující [hosta partnera veřejný certifikát](../logic-apps/logic-apps-enterprise-integration-certificates.md) pro ověření podpisu zprávy. Nebo vytvořte certifikát, pokud nemáte.

4.  Pokud chcete vyžadovat šifrování všechny příchozí zprávy, vyberte **zpráva by měla šifrovat**. Z **certifikát** vyberte existující [privátní certifikát hostitele partnera](../logic-apps/logic-apps-enterprise-integration-certificates.md) pro dešifrování příchozí zprávy. Nebo vytvořte certifikát, pokud nemáte.

5. Chcete-li vyžadovat zpráv, které mají komprimovat, vyberte **zpráva by měla být komprimované**.

6. Odeslat oznámení dispozice synchronní zprávy (MDN) pro přijatých zpráv, vyberte **odeslat MDN**.

7. Chcete-li odeslat podepsaný MDNs přijaté zprávy, vyberte **odeslat podepsaný MDN**.

8. Chcete-li odeslat asynchronní MDNs přijaté zprávy, vyberte **odesílat asynchronní MDN**.

9. Jakmile jste hotovi, přesvědčte se, uložte nastavení tak, že zvolíte **OK**.

Nyní je připraven pro zpracování příchozích zpráv, které v souladu s vámi vybrané nastavení vaše smlouvy.

| Vlastnost | Popis |
| --- | --- |
| Přepsat vlastnosti zprávy |Označuje, že je možné přepsat vlastnosti v přijatých zpráv. |
| Zprávu je nutné podepsat. |Vyžaduje zpráv, které mají být digitálně podepsané. Nakonfigurujte hosta partnera veřejný certifikát pro ověření podpisu.  |
| Zprávu je nutné zašifrovat. |Vyžaduje šifrování zprávy. Bez šifrované zprávy budou odmítnuty. Nakonfigurujte privátní certifikát hostitele partnera pro dešifrování zprávy.  |
| Zprávu je nutné zkomprimovat. |Vyžaduje zpráv, které mají být komprimovány. Non komprimovanou zprávy budou odmítnuty. |
| Text zprávy MDN |Výchozí zprávu dispozice oznámení (MDN) k odeslání do odesílatele zprávy. |
| Odeslat zprávu MDN |Vyžaduje MDNs k odeslání. |
| Odeslat podepsanou zprávu MDN |Vyžaduje MDNs k podepsání. |
| Algoritmus MIC |Vyberte algoritmus použitý k podepisování zpráv. |
| Odeslat asynchronní zprávu MDN | Vyžaduje asynchronně odesílání zpráv. |
| zprostředkovatele identity | Zadejte adresu URL, kam má posílat MDNs. |

## <a name="configure-how-your-agreement-sends-messages"></a>Nakonfigurujte, jak vaše smlouvy odešle zprávy

Můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává odchozích zpráv, které jste odeslali partnerům prostřednictvím této smlouvy.

1.  V části **přidat**, vyberte **odeslat nastavení**.
Konfigurujte tyto vlastnosti závislosti na vaší smlouvě se partnera, výměny zpráv s vámi. Popisy vlastností najdete v tabulce v této části.

    ![Nastavit vlastnosti "Odeslat nastavení"](./media/logic-apps-enterprise-integration-as2/agreement-51.png)

2. Chcete-li odeslat podepsaný zprávy do svého partnera, vyberte **povolit podepisování zpráv**. K podepisování zpráv, v **povinná kontrola úrovně Důvěryhodnosti algoritmus** seznamu, vyberte *privátní certifikát hostitele partnera povinná kontrola úrovně Důvěryhodnosti algoritmus*. A v **certifikát** vyberte existující [privátní certifikát hostitele partnera](../logic-apps/logic-apps-enterprise-integration-certificates.md).

3. K odesílání šifrovaných zpráv do partnera, vyberte **povolit šifrování zpráv**. Pro šifrování zprávy, v **šifrovací algoritmus** seznamu, vyberte *hosta partnera veřejný certifikát algoritmus*.
A v **certifikát** vyberte existující [hosta partnera veřejný certifikát](../logic-apps/logic-apps-enterprise-integration-certificates.md).

4. Chcete-li komprimovat zprávy, vyberte **povolit kompresi zpráva**.

5. Chcete-li unfold hlavičku HTTP content-type na jeden řádek, vyberte **hlavičky Unfold HTTP**.

6. Pokud chcete získat synchronní MDNs pro odeslané zprávy, vyberte **požadavku MDN**.

7. Pokud chcete získat podepsaný MDNs pro odeslané zprávy, vyberte **požadavek podepsané MDN**.

8. Pokud chcete získat asynchronní MDNs pro odeslané zprávy, vyberte **žádosti o asynchronní MDN**. Pokud vyberete tuto možnost, zadejte adresu URL, kam můžete odesílat MDNs.

9. Pokud chcete vyžadovat neodvolatelnost příjmu, vyberte **povolit NRR**.  

10. Můžete nastavit, formát algoritmus, který se použije v povinná kontrola úrovně Důvěryhodnosti nebo podepisování v hlavičkách odchozí zprávy AS2 nebo MDN **algoritmus SHA2 formátu**.  

11. Jakmile jste hotovi, přesvědčte se, uložte nastavení tak, že zvolíte **OK**.

Nyní je připraven pro zpracování odchozích zpráv, které v souladu s vámi vybrané nastavení vaše smlouvy.

| Vlastnost | Popis |
| --- | --- |
| Povolit podepisování zpráv |Vyžaduje všechny zprávy odeslané z smlouvy k podepsání. |
| Algoritmus MIC |Algoritmus použitý k podepisování zpráv. Nakonfiguruje privátní certifikát hostitele partnera algoritmus povinná kontrola úrovně Důvěryhodnosti pro podepisování zpráv. |
| Certifikát |Vyberte certifikát, který chcete použít k podepisování zpráv. Nakonfiguruje privátní certifikát hostitele partnera k podepisování zpráv. |
| Povolit šifrování zpráv |Vyžaduje šifrování všechny zprávy odeslané z této smlouvy. Nakonfiguruje algoritmus hosta partnera veřejný certifikát pro šifrování zprávy. |
| Šifrovací algoritmus |Šifrovací algoritmus, který chcete použít pro šifrování zpráv. Nakonfiguruje hosta partnera veřejný certifikát pro šifrování zprávy. |
| Certifikát |Certifikát, který se má použít k šifrování zpráv. Nakonfiguruje hosta partnera privátní certifikát pro šifrování zprávy. |
| Povolit kompresi zpráv |Vyžaduje komprese všechny zprávy odeslané z této smlouvy. |
| Rozbalit hlavičky HTTP |Umístí hlavičku HTTP content-type na jeden řádek. |
| Požadovat zprávy MDN |Vyžaduje MDN pro všechny zprávy odeslané z této smlouvy. |
| Požadovat podepsané zprávy MDN |Vyžaduje všechny MDNs, které se odesílají do této smlouvy k podepsání. |
| Požadovat asynchronní zprávy MDN |Vyžaduje asynchronní MDNs k odeslání do této smlouvy. |
| zprostředkovatele identity |Zadejte adresu URL, kam má posílat MDNs. |
| Povolit NRR |Vyžaduje neodvolatelnost příjmu (NRR), komunikace atribut, který poskytuje důkaz, tato data byla přijata, jak je řešit. |
| Formát algoritmu SHA2 |Vyberte formát algoritmus pro použití v povinná kontrola úrovně Důvěryhodnosti nebo podepisování v hlavičkách odchozí MDN nebo AS2 zpráv |

## <a name="find-your-created-agreement"></a>Najít vaší vytvořené smlouvy

1. Po dokončení nastavení na všechny vlastnosti vaše smlouvy **přidat** vyberte **OK** dokončit vytváření vaší smlouvy a vrátíte se k účtu integrace.

    Nově přidané smlouvy nyní se zobrazí v vaše **smlouvy** seznamu.

2. Můžete také zobrazit vaše smlouvy v váš účet Přehled integrace. V nabídce váš účet integrace zvolte **přehled**, vyberte **smlouvy** dlaždici. 

   ![Vyberte že dlaždici "Smlouvy" Chcete-li zobrazit všechny smlouvy](./media/logic-apps-enterprise-integration-as2/agreement-6.png)

## <a name="view-the-swagger"></a>Zobrazení swagger
Najdete v článku [swagger podrobnosti](/connectors/as2/). 

## <a name="next-steps"></a>Další postup
* [Další informace o integračního balíčku Enterprise](logic-apps-enterprise-integration-overview.md "Další informace o Enterprise integračního balíčku")  
