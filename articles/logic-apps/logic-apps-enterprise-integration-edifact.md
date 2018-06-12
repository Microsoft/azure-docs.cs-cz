---
title: EDIFACT zpráv B2B enterprise integrace - Azure Logic Apps | Microsoft Docs
description: Exchange EDIFACT zprávy ve formátu EDI B2B enterprise integraci s Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: jeconnoc
editor: ''
ms.assetid: 2257d2c8-1929-4390-b22c-f96ca8b291bc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/26/2016
ms.author: LADocs; jonfan
ms.openlocfilehash: d85db6f5be487578ceab261d2779589439bfad31
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300092"
---
# <a name="exchange-edifact-messages-for-enterprise-integration-with-logic-apps"></a>Zprávy Exchange EDIFACT pro podnikové integrace s logic apps

Před výměnou zpráv EDIFACT pro Azure Logic Apps, musíte vytvořit smlouvy EDIFACT a uložení této smlouvy ve vašem účtu integrace. Tady jsou kroky pro vytvoření EDIFACT smlouvu.

> [!NOTE]
> Tato stránka se vztahuje na funkce EDIFACT pro Azure Logic Apps. Další informace najdete v tématu [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Než začnete

Tady je položky, které budete potřebovat:

* [Integrace účet](logic-apps-enterprise-integration-create-integration-account.md) který již má definovaný a přidružené k předplatnému Azure  
* Alespoň dva [partnery](logic-apps-enterprise-integration-partners.md) , jsou již definováni ve vašem účtu integrace

> [!NOTE]
> Když vytvoříte smlouvu, obsah zprávy, které můžete přijímat nebo odesílat do a z partnera musí odpovídat typ smlouvy.

Po jste [vytvoření účtu integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a [přidat partnery](logic-apps-enterprise-integration-partners.md), EDIFACT smlouvu můžete vytvořit pomocí následujících kroků.

## <a name="create-an-edifact-agreement"></a>Vytvoření smlouvy EDIFACT 

1. Přihlaste se na web [Azure Portal](http://portal.azure.com "Azure Portal"). 

2. V hlavní nabídce Azure, vyberte **všechny služby**. Do vyhledávacího pole zadejte "integraci" a potom vyberte **účty pro integraci**.

   ![Najít váš účet integrace](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Pokud **všechny služby** nezobrazí, možná budete muset nejdřív rozbalte nabídku. V horní nabídce sbalené, vyberte **zobrazit text popisky**.

3. V části **účty pro integraci**, vyberte účet integrace, kde chcete vytvořit smlouvu.

   ![Vyberte účet integrace místo pro vytvoření této smlouvy](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Zvolte **smlouvy**. Pokud nemáte dlaždici smlouvy, přidejte nejprve dlaždici.   

   ![Vyberte že dlaždici "Smlouvy"](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. Na stránce smlouvy zvolte **přidat**.

   ![Zvolte "Přidat"](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. V části **přidat**, zadejte **název** pro vaše smlouvy. Pro **typ smlouvy**, vyberte **EDIFACT**. Vyberte **hostitele partnera**, **identitu hostitele**, **hosta partnera**, a **hosta Identity** pro vaše smlouvy.

   ![Zadejte podrobnosti o smlouvě](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Vlastnost | Popis |
   | --- | --- |
   | Název |Název smlouvy |
   | Typ smlouvy | Musí být EDIFACT |
   | Partner s identitou hostitele |Smlouvu musí hostitelské i hostované partnera. Partner hostitele představuje organizace, která nakonfiguruje smlouvu. |
   | Identita hostitele |Identifikátor pro hostitele partnera |
   | Partner s identitou hosta |Smlouvu musí hostitelské i hostované partnera. Partner hosta představuje organizace, která je spolupráci s partnery hostitele. |
   | Identita hosta |Identifikátor pro partnera hosta |
   | Nastavení příjmu |Tyto vlastnosti se vztahují na všechny zprávy přijaté službou smlouvu. |
   | Nastavení odesílání |Tyto vlastnosti se vztahují na všechny zprávy odeslané smlouvu. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Nakonfigurujte, jak vaše smlouvy popisovače přijatých zpráv

Teď, když jste nastavili vlastnosti smlouvy, můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává příchozí zprávy přijaté od svého partnera prostřednictvím této smlouvy.

1. V části **přidat**, vyberte **přijímat nastavení**.
Konfigurujte tyto vlastnosti závislosti na vaší smlouvě se partnera, výměny zpráv s vámi. Vlastnost popis najdete v tématu tabulky v této části.

   **Získat nastavení** jsou uspořádány do těchto oddílů: identifikátory, potvrzení, schémata, řízení čísla, ověřování a interní nastavení.

   ![Konfigurace "Obdrží nastavení"](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Jakmile jste hotovi, přesvědčte se, uložte nastavení tak, že zvolíte **OK**.

Nyní je připraven pro zpracování příchozích zpráv, které v souladu s vámi vybrané nastavení vaše smlouvy.

### <a name="identifiers"></a>Identifikátory

| Vlastnost | Popis |
| --- | --- |
| UNB6.1 (Referenční heslo příjemce) |Zadejte hodnotu mezi 1 a 14 znaky alfanumerické hodnotu. |
| UNB6.2 (Referenční kvalifikátor příjemce) |Zadejte hodnotu alfanumerické znaky s nejméně jeden znak a maximálně dva znaky. |

### <a name="acknowledgments"></a>Potvrzování

| Vlastnost | Popis |
| --- | --- |
| Přijetí zprávy (CONTRL) |Zaškrtnutím tohoto políčka se vraťte do odesílatele výměnu technické potvrzení (CONTRL). Potvrzení se odesílají do odesílatele výměnu na základě nastavení Odeslat smlouvy. |
| Potvrzení (CONTRL) |Zaškrtněte toto políčko vrátit funkční potvrzení (CONTRL) pro výměnu odesílatel potvrzení posílá odesílatel výměnu na základě nastavení Odeslat smlouvy. |

### <a name="schemas"></a>Schémata

| Vlastnost | Popis |
| --- | --- |
| UNH2.1 (TYP) |Vyberte typ sadu transakce. |
| UNH2.2 (VERZE) |Zadejte číslo verze zprávy. (Minimální, jeden znak, maximum, tři znaky). |
| UNH2.3 (VERZE) |Zadejte číslo verze zprávy. (Minimální, jeden znak, maximum, tři znaky). |
| UNH2.5 (PŘIDRUŽENÉ PŘIŘAZENÉ KÓD) |Zadejte kód přiřazené. (Maximální šest znaků. Musí být alfanumerické znaky). |
| UNG2.1 (ID ODESÍLATELE APLIKACE) |Zadejte hodnotu alfanumerické znaky s nejméně jeden znak a maximálně 35 znaků. |
| UNG2.2 (KVALIFIKÁTOR APLIKACE ODESÍLATELE KÓDU) |Zadejte hodnotu alfanumerické delší než 4 znaky. |
| SCHÉMA |Vyberte dříve odeslaný schéma, které chcete použít z vašeho účtu přidružené integrace. |

### <a name="control-numbers"></a>Kontrolní čísla
| Vlastnost | Popis |
| --- | --- |
| Zakáže duplikáty Interchange číslo ovládacího prvku |Chcete-li blokovat duplicitní mimoúrovňové křižovatky, vyberte tuto vlastnost. Pokud, EDIFACT dekódovat akce zkontroluje, že číslo ovládací prvek výměnu (UNB5) pro výměnu přijaté neodpovídá číslo řízení dříve zpracované výměnu. Pokud je zjištěna shoda, nebude zpracován výměnu. |
| Kontrolovat duplicitní UNB5 každých (dny) |Pokud jste se rozhodli zakázat duplicitní výměnu řízení čísla, můžete zadat počet dnů, kdy se mají provést kontrolu tím, že na odpovídající hodnotu pro toto nastavení. |
| Zakázat duplicity kontrolních čísel skupiny |Pokud chcete blokovat mimoúrovňové křižovatky s duplicitní skupině řízení čísla (UNG5), vyberte tuto vlastnost. |
| Zakázat duplicity kontrolních čísel sad transakcí |Pokud chcete blokovat mimoúrovňové křižovatky se duplicitní transakce sadu řízení čísla (UNH1), vyberte tuto vlastnost. |
| Řídicí číslo potvrzení EDIFACT |Chcete-li určit čísla transakcí sadu referenční dokumentace pro použití v potvrzení, zadejte hodnotu pro předponu, rozsah čísel odkaz a příponu. |

### <a name="validations"></a>Ověřování

Po dokončení každý řádek ověření jiné automaticky přidá. Pokud nezadáte všechna pravidla, ověření používá "Výchozího" řádku.

| Vlastnost | Popis |
| --- | --- |
| Typ zprávy |Vyberte typ zprávy EDI. |
| Ověřování EDI |Provedení ověření EDI pro datové typy podle definice schématu EDI vlastnosti, omezení délky, prázdný datové prvky a koncové oddělovače. |
| Rozšíření ověřování |Pokud datový typ není EDI, ověření se na element požadavek na data a povoleny opakování, výčty a data element délka ověření (min/max). |
| Povolit úvodní a koncové nuly |Zachování všechny další počáteční nebo koncové nula a místo znaků. Nevysunujte tyto znaky. |
| Oříznout úvodní a koncové nuly |Odeberte úvodní a koncové nuly a místo znaků. |
| Zásady pro koncový oddělovač |Generovat koncové oddělovače. <p>Vyberte **není povoleno** zakázat koncové oddělovače a oddělovače v přijatých výměnu. Pokud výměnu koncové oddělovače a oddělovačů, výměnu je deklarovaná není platný. <p>Vyberte **volitelné** tak, aby přijímal mimoúrovňové křižovatky s nebo bez koncové oddělovače a oddělovačů. <p>Vyberte **povinné** při přijaté výměnu musí mít koncové oddělovače a oddělovačů. |

### <a name="internal-settings"></a>Interní nastavení

| Vlastnost | Popis |
| --- | --- |
| Pokud jsou povolené koncové oddělovače, vytvořit prázdné značky XML |Výběrem tohoto zaškrtávacího políčka tak, aby měl odesílatel výměnu obsahovat prázdný značky XML pro koncové oddělovače. |
| Rozdělit výměnu jako sady transakcí – pozastavit sady transakcí při chybě|Analyzuje každou transakci, nastavte v výměnu do samostatného dokumentu XML s použitím příslušné obálky do sady transakce. Pozastavte pouze transakce sady, které nesplní ověření. |
| Rozdělit výměnu jako sady transakcí – pozastavit výměnu při chybě|Analyzuje každou transakci, nastavte v výměnu do samostatného dokumentu XML s použitím příslušné obálku. Celý výměnu pozastavte, pokud selže ověření se jeden nebo více sad transakce v výměnu. | 
| Zachovat výměnu – pozastavení sady transakce při chybě |Výměnu zůstanou zachovány, vytvoří dokument XML pro celý dávkové výměnu. Pozastavte pouze transakce sady, které selže ověření, můžete nadále zpracovat všechny ostatní sady transakce. |
| Zachovat výměnu – pozastavit výměnu při chybě |Výměnu zůstanou zachovány, vytvoří dokument XML pro celý dávkové výměnu. Celý výměnu pozastavte, pokud selže ověření se jeden nebo více sad transakce v výměnu. |

## <a name="configure-how-your-agreement-sends-messages"></a>Nakonfigurujte, jak vaše smlouvy odešle zprávy

Můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává odchozích zpráv, které jste odeslali partnerům prostřednictvím této smlouvy.

1.  V části **přidat**, vyberte **odeslat nastavení**.
Konfigurujte tyto vlastnosti závislosti na vaší smlouvě se svého partnera, který výměny zpráv s vámi. Vlastnost popis najdete v tématu tabulky v této části.

    **Odeslat nastavení** jsou uspořádány do těchto oddílů: identifikátory, potvrzení, schémata, obálky, znakové sady a oddělovačů, řízení čísla a ověření.

    ![Konfigurace "Odeslat nastavení"](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Jakmile jste hotovi, přesvědčte se, uložte nastavení tak, že zvolíte **OK**.

Nyní je připraven pro zpracování odchozích zpráv, které v souladu s vámi vybrané nastavení vaše smlouvy.

### <a name="identifiers"></a>Identifikátory

| Vlastnost | Popis |
| --- | --- |
| UNB1.2 (syntaxe verze) |Vyberte hodnotu mezi **1** a **4**. |
| UNB2.3 (Adresa zpětného směrování odesílatele) |Zadejte hodnotu alfanumerické znaky s nejméně jeden znak a maximálně 14 znaků. |
| UNB3.3 (Adresa zpětného směrování příjemce) |Zadejte hodnotu alfanumerické znaky s nejméně jeden znak a maximálně 14 znaků. |
| UNB6.1 (Referenční heslo příjemce) |Zadejte hodnotu alfanumerické s minimálně jeden a maximálně 14 znaků. |
| UNB6.2 (Referenční kvalifikátor příjemce) |Zadejte hodnotu alfanumerické znaky s nejméně jeden znak a maximálně dva znaky. |
| UNB7 (Referenční ID aplikace) |Zadejte hodnotu alfanumerické znaky s nejméně jeden znak a maximálně 14 znaků |

### <a name="acknowledgment"></a>Potvrzení
| Vlastnost | Popis |
| --- | --- |
| Přijetí zprávy (CONTRL) |Toto políčko zaškrtněte, pokud hostované partnera očekává technické potvrzení (CONTRL). Toto nastavení určuje, že požadavky hostované partnera, který odesílá zprávy, potvrzení z hosta partnera. |
| Potvrzení (CONTRL) |Toto políčko zaškrtněte, pokud hostované partnera očekává funkční potvrzení (CONTRL). Toto nastavení určuje, že požadavky hostované partnera, který odesílá zprávy, potvrzení z hosta partnera. |
| Vygenerovat smyčku SG1/SG4 pro přijaté sady transakcí |Pokud jste vybrali k vyžádání funkční potvrzení, zaškrtnutím tohoto políčka vynutit generování sz1/BS4 smyčky v funkční potvrzování CONTRL pro sady přijatý transakce. |

### <a name="schemas"></a>Schémata
| Vlastnost | Popis |
| --- | --- |
| UNH2.1 (TYP) |Vyberte typ sadu transakce. |
| UNH2.2 (VERZE) |Zadejte číslo verze zprávy. |
| UNH2.3 (VERZE) |Zadejte číslo verze zprávy. |
| SCHÉMA |Vyberte schéma používat. Schémata jsou umístěny ve vašem účtu integrace. Pro přístup k vaší schémat, nejprve propojte si účet integraci do aplikace logiky. |

### <a name="envelopes"></a>Obálky
| Vlastnost | Popis |
| --- | --- |
| UNB8 (Zpracování kódu priority) |Zadejte abecední hodnotu, která není více než jeden znak. |
| UNB10 (Dohoda o komunikaci) |Zadejte hodnotu alfanumerické znaky s nejméně jeden znak a maximálně 40 znaků. |
| UNB11 (Testovací indikátor) |Zaškrtnutím tohoto políčka znamenat, že výměnu generované testovací data |
| Použít segment UNA (Nápověda k řetězci služby) |Zaškrtnutím tohoto políčka Generovat UNA segmentu pro výměnu k odeslání. |
| Použít segmenty UNG (Hlavička skupiny funkce) |Zaškrtnutím tohoto políčka pro vytvoření seskupení segmentů v hlavičce funkční skupiny v zprávy odeslané do hostovaného partnera. Chcete-li vytvořit segmenty UNG budou použity následující hodnoty: <p>Pro **UNG1**, zadejte hodnotu alfanumerické znaky s nejméně jeden znak a maximálně šest znaků. <p>Pro **UNG2.1**, zadejte hodnotu alfanumerické znaky s nejméně jeden znak a maximálně 35 znaků. <p>Pro **UNG2.2**, zadejte hodnotu alfanumerické delší než 4 znaky. <p>Pro **UNG3.1**, zadejte hodnotu alfanumerické znaky s nejméně jeden znak a maximálně 35 znaků. <p>Pro **UNG3.2**, zadejte hodnotu alfanumerické delší než 4 znaky. <p>Pro **UNG6**, zadejte hodnotu alfanumerické s minimálně jeden a maximálně tři znaky. <p>Pro **UNG7.1**, zadejte hodnotu alfanumerické znaky s nejméně jeden znak a maximálně tři znaky. <p>Pro **UNG7.2**, zadejte hodnotu alfanumerické znaky s nejméně jeden znak a maximálně tři znaky. <p>Pro **UNG7.3**, zadejte hodnotu alfanumerické minimálně 1 znak a maximálně 6 znaků. <p>Pro **UNG8**, zadejte hodnotu alfanumerické znaky s nejméně jeden znak a maximálně 14 znaků. |

### <a name="character-sets-and-separators"></a>Znakové sady a oddělovače

Jiného, než znaková sada, můžete zadat jinou sadu oddělovače má být použit pro každý typ zprávy. Pokud znakovou sadu pro danou zprávou schématu není zadána, použije se výchozí znakovou sadu.

| Vlastnost | Popis |
| --- | --- |
| UNB1.1 (Identifikátor systému) |Vyberte EDIFACT znakovou sadu pro použití na odchozí výměnu. |
| Schéma |V rozevíracím seznamu vyberte schéma. Po dokončení každý řádek je automaticky přidán nový řádek. Pro vybrané schéma vyberte sadu oddělovačů, který chcete použít, na základě popisů oddělovače níže. |
| Typ vstupu |V rozevíracím seznamu vyberte typ vstupu. |
| Oddělovač komponent |Chcete-li samostatné složené datové prvky, zadejte jeden znak. |
| Oddělovač datových prvků |Chcete-li samostatné jednoduché datové elementů v rámci složené datové prvky, zadejte jeden znak. |
| Ukončovací znak segmentu |Chcete-li označení konce EDI segment, zadejte jeden znak. |
| Přípona |Vyberte znak, který se používá s identifikátor segmentu. Určíte příponu, datový prvek ukončovací segment nesmí být prázdné. Pokud segment ukončení je prázdné, je třeba určit příponu. |

### <a name="control-numbers"></a>Kontrolní čísla
| Vlastnost | Popis |
| --- | --- |
| UNB5 (Výměnné řídicí číslo) |Zadejte předponu, rozsah hodnot pro ovládací prvek číslo výměnu a příponu. Tyto hodnoty se používají ke generování odchozí výměnu. Předpona a přípona jsou volitelné, při požadované číslo ovládacího prvku. Ovládací prvek je zvýšena pro každou novou zprávu; Předpona a přípona zůstávají stejné. |
| UNG5 (Řídicí číslo skupiny) |Zadejte předponu, rozsah hodnot pro ovládací prvek číslo výměnu a příponu. Tyto hodnoty se používají ke generování řízení čísla skupiny. Předpona a přípona jsou volitelné, při požadované číslo ovládacího prvku. Ovládací prvek je zvýšena pro každé nové zprávy, dokud nebude dosaženo maximální hodnoty; Předpona a přípona zůstávají stejné. |
| UNH1 (Referenční číslo hlavičky zprávy) |Zadejte předponu, rozsah hodnot pro ovládací prvek číslo výměnu a příponu. Tyto hodnoty se používají ke generování referenční číslo záhlaví zprávy. Předpona a přípona jsou volitelné, zatímco referenční číslo se vyžaduje. Referenční číslo se zvýší, pro každou novou zprávu; Předpona a přípona zůstávají stejné. |

### <a name="validations"></a>Ověřování

Po dokončení každý řádek ověření jiné automaticky přidá. Pokud nezadáte všechna pravidla, ověření používá "Výchozího" řádku.

| Vlastnost | Popis |
| --- | --- |
| Typ zprávy |Vyberte typ zprávy EDI. |
| Ověřování EDI |Proveďte ověření EDI na datových typů, jak definované vlastnosti EDI schéma, omezení délky, prázdný datové prvky a koncové oddělovače. |
| Rozšíření ověřování |Pokud datový typ není EDI, ověření se na element požadavek na data a povoleny opakování, výčty a data element délka ověření (min/max). |
| Povolit úvodní a koncové nuly |Zachování všechny další počáteční nebo koncové nula a místo znaků. Nevysunujte tyto znaky. |
| Oříznout úvodní a koncové nuly |Odeberte počáteční nebo koncové nulový počet znaků. |
| Zásady pro koncový oddělovač |Generovat koncové oddělovače. <p>Vyberte **není povoleno** zakázat koncové oddělovače a oddělovače v odeslané výměnu. Pokud výměnu koncové oddělovače a oddělovačů, výměnu je deklarovaná není platný. <p>Vyberte **volitelné** odeslat mimoúrovňové křižovatky s nebo bez koncové oddělovače a oddělovačů. <p>Vyberte **povinné** Pokud odeslané výměnu musí mít koncové oddělovače a oddělovačů. |

## <a name="find-your-created-agreement"></a>Najít vaší vytvořené smlouvy

1.  Po dokončení nastavení na všechny vlastnosti vaše smlouvy **přidat** vyberte **OK** dokončit vytváření vaší smlouvy a vrátíte se k účtu integrace.

    Nově přidané smlouvy nyní se zobrazí v vaše **smlouvy** seznamu.

2.  Můžete také zobrazit vaše smlouvy v váš účet Přehled integrace. V nabídce váš účet integrace zvolte **přehled**, vyberte **smlouvy** dlaždici. 

    ![Vyberte že dlaždici "Smlouvy"](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="view-swagger-file"></a>Soubor Swagger zobrazení
Chcete-li zobrazit podrobnosti Swagger pro konektor EDIFACT, najdete v části [EDIFACT](/connectors/edifact/).

## <a name="learn-more"></a>Další informace
* [Další informace o integračního balíčku Enterprise](logic-apps-enterprise-integration-overview.md "Další informace o Enterprise integračního balíčku")  

