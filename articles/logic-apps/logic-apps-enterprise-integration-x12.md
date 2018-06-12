---
title: X12 zpráv B2B enterprise integrace - Azure Logic Apps | Microsoft Docs
description: Exchange X12 zprávy ve formátu EDI B2B enterprise integraci s Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: b269a5171dcb8867f7ea40215d6b8a75d5d918bc
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299075"
---
# <a name="exchange-x12-messages-for-enterprise-integration-with-logic-apps"></a>Exchange X12 zprávy pro podnikové integrace s logic apps

Před výměnou X12 zprávy pro Azure Logic Apps, musíte vytvořit X12 smlouvy a uložit v účtu integrace této smlouvy. Tady jsou kroky pro vytvoření X12 smlouvy.

> [!NOTE]
> Tato funkce zahrnuje X12 stránky pro Azure Logic Apps. Další informace najdete v tématu [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Než začnete

Tady je položky, které budete potřebovat:

* [Integrace účet](logic-apps-enterprise-integration-create-integration-account.md) který již má definovaný a přidružené k předplatnému Azure
* Alespoň dva [partnery](../logic-apps/logic-apps-enterprise-integration-partners.md) které jsou definované ve vašem účtu integrace a nakonfigurované X12 identifikátor pod **obchodní identit**    
* Požadovanou [schématu](../logic-apps/logic-apps-enterprise-integration-schemas.md) lze odeslat ke svému účtu integrace

Po jste [vytvoření účtu integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [přidat partnery](logic-apps-enterprise-integration-partners.md)a mít [schématu](../logic-apps/logic-apps-enterprise-integration-schemas.md) , kterou chcete použít, můžete vytvořit X12 smlouvy pomocí následujících kroků.

## <a name="create-an-x12-agreement"></a>Vytvoření X12 smlouvy

1. Přihlaste se na web [Azure Portal](http://portal.azure.com "Azure Portal"). 

2. Z hlavní nabídky Azure, vyberte **všechny služby**. Do vyhledávacího pole zadejte "integraci" a potom vyberte **účty pro integraci**.  

   ![Najít váš účet integrace](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Pokud **všechny služby** nezobrazí, možná budete muset nejdřív rozbalte nabídku. V horní nabídce sbalené, vyberte **nabídky Zobrazit**.

3. V části **účty pro integraci**, vyberte účet integrace, ve které chcete přidat smlouvu.

   ![Vyberte účet integrace místo pro vytvoření této smlouvy](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Vyberte **přehled**, vyberte **smlouvy** dlaždici. Pokud nemáte dlaždici smlouvy, přidejte nejprve dlaždici. 

   ![Vyberte že dlaždici "Smlouvy"](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. V části **smlouvy**, zvolte **přidat**.

   ![Zvolte "Přidat"](./media/logic-apps-enterprise-integration-as2/agreement-2.png)     

6. V části **přidat**, zadejte **název** pro vaše smlouvy. Pro typ smlouvy, vyberte **X12**. Vyberte **hostitele partnera**, **identitu hostitele**, **hosta partnera**, a **hosta Identity** pro vaše smlouvy. Další podrobnosti vlastnost najdete v tabulce v tomto kroku.

    ![Zadejte podrobnosti o smlouvě](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Vlastnost | Popis |
    | --- | --- |
    | Název |Název smlouvy |
    | Typ smlouvy | Musí být X12 |
    | Partner s identitou hostitele |Smlouvu musí hostitelské i hostované partnera. Partner hostitele představuje organizace, která nakonfiguruje smlouvu. |
    | Identita hostitele |Identifikátor pro hostitele partnera |
    | Partner s identitou hosta |Smlouvu musí hostitelské i hostované partnera. Partner hosta představuje organizace, která je spolupráci s partnery hostitele. |
    | Identita hosta |Identifikátor pro partnera hosta |
    | Nastavení příjmu |Tyto vlastnosti se vztahují na všechny zprávy přijaté službou smlouvu. |
    | Nastavení odesílání |Tyto vlastnosti se vztahují na všechny zprávy odeslané smlouvu. |  

  > [!NOTE]
  > Rozlišení X12 smlouvy závisí na odpovídající kvalifikátor odesílatele a identifikátor a kvalifikátor příjemce a identifikátor definovaný v partnera a příchozí zprávy. Pokud tyto hodnoty změnit pro svého partnera, aktualizujte příliš smlouvu.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Nakonfigurujte, jak vaše smlouvy popisovače přijatých zpráv

Teď, když jste nastavili vlastnosti smlouvy, můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává příchozí zprávy přijaté od svého partnera prostřednictvím této smlouvy.

1.  V části **přidat**, vyberte **přijímat nastavení**.
Konfigurujte tyto vlastnosti závislosti na vaší smlouvě se partnera, výměny zpráv s vámi. Vlastnost popis najdete v tématu tabulky v této části.

    **Získat nastavení** jsou uspořádány do těchto oddílů: identifikátory, potvrzení, schémata, obálky, řízení čísla, ověření a interní nastavení.

2. Jakmile jste hotovi, přesvědčte se, uložte nastavení tak, že zvolíte **OK**.

Nyní je připraven pro zpracování příchozích zpráv, které v souladu s vámi vybrané nastavení vaše smlouvy.

### <a name="identifiers"></a>Identifikátory

![Nastavit identifikátor vlastnosti](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Vlastnost | Popis |
| --- | --- |
| ISA1 (Kvalifikátor autorizace) |V rozevíracím seznamu vyberte hodnoty kvalifikátor autorizace. |
| ISA2 |Volitelné. Zadejte hodnotu informace autorizace. Pokud je hodnota, které jste zadali pro ISA1 než 00, zadejte minimálně jeden alfanumerický znak a maximálně 10. |
| ISA3 (Kvalifikátor zabezpečení) |V rozevíracím seznamu vyberte hodnotu kvalifikátor zabezpečení. |
| ISA4 |Volitelné. Zadejte hodnotu informace o zabezpečení. Pokud je hodnota, které jste zadali pro ISA3 než 00, zadejte minimálně jeden alfanumerický znak a maximálně 10. |

### <a name="acknowledgment"></a>Potvrzení

![Nastavit vlastnosti potvrzení](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Vlastnost | Popis |
| --- | --- |
| TA1 očekávání |Vrátí potvrzení o technické odesílateli výměnu |
| FA očekávání |Vrátí potvrzení o funkční odesílateli výměnu. Poté vyberte, zda chcete 997 nebo 999 potvrzování, podle verze schématu |
| Zahrnout AK2/IK2 smyčky |Umožňuje generování AK2 smyčky v funkční potvrzování pro sady přijatý transakce |

### <a name="schemas"></a>Schémata

Vyberte schéma pro každý typ transakce (ST1) a aplikace Sender (GS2). Kanál receive provede zpětný překlad příchozí zpráva porovnáním hodnot pro ST1 a GS2 příchozí zprávy s hodnotami, které tady nastavíte a schéma příchozí zpráva se schématem, že tady nastavíte.

![Vyberte možnost schématu](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Vlastnost | Popis |
| --- | --- |
| Verze |Vyberte X12 verze |
| Typ transakce (ST01) |Vyberte typ transakce |
| Aplikace odesílatele (GS02) |Vyberte aplikace sender |
| Schéma |Vyberte soubor schématu, které chcete použít. Schémata jsou přidána k vašemu účtu integrace. |

> [!NOTE]
> Nakonfigurujte požadované [schématu](../logic-apps/logic-apps-enterprise-integration-schemas.md) odeslání do vaší [integrace účet](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Obálky

![Určete oddělovač v sadě transakce: Zvolte Standardní identifikátor nebo opakování oddělovače](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Vlastnost | Popis |
| --- | --- |
| Využití ISA11 |Určuje oddělovač, který má používat v sadě transakce: <p>Vyberte **standardní identifikátor** Pokud chcete použít pro zápisem tečkou (.), namísto desítkové notaci příchozí dokumentu v EDI přijímat kanálu. <p>Vyberte **opakování oddělovače** k určení oddělovač pro opakované výskyty jednoduché datového elementu nebo opakovaných datová struktura. Obvykle se například karátová (^) používá jako oddělovač opakování. U HIPAA schémat můžete použít pouze stříšky. |

### <a name="control-numbers"></a>Kontrolní čísla

![Vyberte způsob zpracování číslo duplikáty ovládací prvek](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Vlastnost | Popis |
| --- | --- |
| Zakáže duplikáty Interchange číslo ovládacího prvku |Blokovat duplicitní mimoúrovňové křižovatky. Počet přijatých výměnu řízení zkontroluje číslo řízení výměnu (ISA13). Pokud je zjištěna shoda, není proces kanálu receive výměnu. Můžete zadat počet dní pro provedení kontroly tím, že hodnota *kontrolovat duplicitní ISA13 každých (dny)*. |
| Zakázat duplicity kontrolních čísel skupiny |Blok interchanges s duplicitní skupině řízení čísla. |
| Zakázat duplicity kontrolních čísel sad transakcí |Blok interchanges s čísly verzí sady se duplicitní transakce. |

### <a name="validations"></a>Ověřování

![Nastavit vlastnosti ověření pro přijatých zpráv](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Po dokončení každý řádek ověření jiné automaticky přidá. Pokud nezadáte všechna pravidla, ověření používá "Výchozího" řádku.

| Vlastnost | Popis |
| --- | --- |
| Typ zprávy |Vyberte typ zprávy EDI. |
| Ověřování EDI |Provedení ověření EDI pro datové typy podle definice schématu EDI vlastnosti, omezení délky, prázdný datové prvky a koncové oddělovače. |
| Rozšíření ověřování |Pokud datový typ není EDI, ověření se na element požadavek na data a povoleny opakování, výčty a data element délka ověření (min/max). |
| Povolit úvodní a koncové nuly |Zachování všechny další počáteční nebo koncové nula a místo znaků. Nevysunujte tyto znaky. |
| Oříznout úvodní a koncové nuly |Odeberte úvodní a koncové nuly a místo znaků. |
| Zásady pro koncový oddělovač |Generovat koncové oddělovače. <p>Vyberte **není povoleno** zakázat koncové oddělovače a oddělovače v přijatých výměnu. Pokud výměnu koncové oddělovače a oddělovačů, výměnu je deklarovaná není platný. <p>Vyberte **volitelné** tak, aby přijímal mimoúrovňové křižovatky s nebo bez koncové oddělovače a oddělovačů. <p>Vyberte **povinné** při výměnu musí mít koncové oddělovače a oddělovačů. |

### <a name="internal-settings"></a>Interní nastavení

![Vyberte interní nastavení](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Vlastnost | Popis |
| --- | --- |
| Převést předpokládané formátu desetinného čísla "Nn" základní 10 číselná hodnota |Převede představuje počet EDI, která je zadána ve formátu "Nn" do základu 10 číselná hodnota |
| Pokud jsou povolené koncové oddělovače, vytvořit prázdné značky XML |Výběrem tohoto zaškrtávacího políčka tak, aby měl odesílatel výměnu obsahovat prázdný značky XML pro koncové oddělovače. |
| Rozdělit výměnu jako sady transakcí – pozastavit sady transakcí při chybě|Analyzuje každou transakci, nastavte v výměnu do samostatného dokumentu XML s použitím příslušné obálky do sady transakce. Pozastaví pouze transakce, kde se ověřování nezdaří. |
| Rozdělit výměnu jako sady transakcí – pozastavit výměnu při chybě|Analyzuje každou transakci, nastavte v výměnu do samostatného dokumentu XML s použitím příslušné obálku. Celý výměnu pozastaví, pokud selže ověření se jeden nebo více sad transakce v výměnu. | 
| Zachovat výměnu – pozastavení sady transakce při chybě |Výměnu zůstanou zachovány, vytvoří dokument XML pro celý dávkové výměnu. Pozastaví pouze transakce sady, které selže ověření, můžete nadále zpracovat všechny ostatní sady transakce. |
| Zachovat výměnu – pozastavit výměnu při chybě |Výměnu zůstanou zachovány, vytvoří dokument XML pro celý dávkové výměnu. Celý výměnu pozastaví, pokud selže ověření se jeden nebo více sad transakce v výměnu. |

## <a name="configure-how-your-agreement-sends-messages"></a>Nakonfigurujte, jak vaše smlouvy odešle zprávy

Můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává odchozích zpráv, které odesílají do svého partnera prostřednictvím této smlouvy.

1.  V části **přidat**, vyberte **odeslat nastavení**.
Konfigurujte tyto vlastnosti závislosti na vaší smlouvě se svého partnera, který výměny zpráv s vámi. Vlastnost popis najdete v tématu tabulky v této části.

    **Odeslat nastavení** jsou uspořádány do těchto oddílů: identifikátory, potvrzení, schémata, obálky, znakové sady a oddělovačů, řízení čísla a ověření.

2. Jakmile jste hotovi, přesvědčte se, uložte nastavení tak, že zvolíte **OK**.

Nyní je připraven pro zpracování odchozích zpráv, které v souladu s vámi vybrané nastavení vaše smlouvy.

### <a name="identifiers"></a>Identifikátory

![Nastavit identifikátor vlastnosti](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Vlastnost | Popis |
| --- | --- |
| Kvalifikátor autorizace (ISA1) |V rozevíracím seznamu vyberte hodnoty kvalifikátor autorizace. |
| ISA2 |Zadejte hodnotu informace autorizace. Pokud tato hodnota je než 00, zadejte minimálně jeden alfanumerický znak a maximálně 10. |
| Kvalifikátor zabezpečení (ISA3) |V rozevíracím seznamu vyberte hodnotu kvalifikátor zabezpečení. |
| ISA4 |Zadejte hodnotu informace o zabezpečení. Pokud je tato hodnota než 00 pro textové pole hodnota (ISA4), zadejte minimálně jednu hodnotu alfanumerické znaky a maximálně 10. |

### <a name="acknowledgment"></a>Potvrzení

![Nastavit vlastnosti potvrzení](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Vlastnost | Popis |
| --- | --- |
| TA1 očekávání |Vrátí technické potvrzení (TA1) pro výměnu odesílatele. Toto nastavení určuje, že partnera hostitele, který odesílá zprávy požadavků na potvrzení partnerovi hosta smlouvy. Tato potvrzení by se měl partner poskytující prostředky hostitele na základě nastavení přijmout smlouvy. |
| FA očekávání |Vrátí funkční potvrzení (IM) pro výměnu odesílatele. Vyberte, zda chcete 997 nebo 999 potvrzení, podle verze schématu, které pracujete. Tato potvrzení by se měl partner poskytující prostředky hostitele na základě nastavení přijmout smlouvy. |
| Verze FA |Vyberte verzi DM |

### <a name="schemas"></a>Schémata

![Vyberte možnost schématu používat](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Vlastnost | Popis |
| --- | --- |
| Verze |Vyberte X12 verze |
| Typ transakce (ST01) |Vyberte typ transakce |
| SCHÉMA |Vyberte schéma používat. Schémata jsou umístěny ve vašem účtu integrace. Pokud vyberete schématu nejprve, automaticky konfiguruje verze a transakce typu  |

> [!NOTE]
> Nakonfigurujte požadované [schématu](../logic-apps/logic-apps-enterprise-integration-schemas.md) odeslání do vaší [integrace účet](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Obálky

![Určete oddělovač v sadě transakce: Zvolte Standardní identifikátor nebo opakování oddělovače](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Vlastnost | Popis |
| --- | --- |
| Využití ISA11 |Určuje oddělovač, který má používat v sadě transakce: <p>Vyberte **standardní identifikátor** Pokud chcete použít pro zápisem tečkou (.), namísto desítkové notaci příchozí dokumentu v EDI přijímat kanálu. <p>Vyberte **opakování oddělovače** k určení oddělovač pro opakované výskyty jednoduché datového elementu nebo opakovaných datová struktura. Obvykle se například karátová (^) používá jako oddělovač opakování. U HIPAA schémat můžete použít pouze stříšky. |

### <a name="control-numbers"></a>Kontrolní čísla

![Zadejte číslo řízení vlastnosti](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Vlastnost | Popis |
| --- | --- |
| Kontrolní číslo verze (ISA12) |Vyberte verzi standardní X12 |
| Použití ukazatele (ISA15) |Vyberte kontextu výměnu.  Hodnoty jsou informace, provozními daty, nebo testovacích dat |
| Schéma |Generuje GS a ST segmenty pro výměnu kódováním X12, který odešle do kanálu odesílání |
| GS1 |Volitelné, vyberte hodnotu pro funkční kód z rozevíracího seznamu |
| GS2 |Volitelné, odesílatel aplikace |
| GS3 |Volitelné, aplikace příjemce |
| GS4 |Volitelné, vyberte CCYYMMDD nebo RRMMDD |
| GS5 |Volitelné, vyberte hh: mm, HHMMSS nebo HHMMSSdd |
| GS7 |Volitelné, vyberte hodnotu pro příslušné agentury z rozevíracího seznamu |
| GS8 |Volitelné, verzi dokumentu |
| Interchange číslo ovládací prvek (ISA13) |Vyžaduje, zadejte rozsah hodnot pro ovládací prvek číslo výměnu. Zadejte číselnou hodnotu minimálně 1 a maximálně 999999999 |
| Číslo skupiny ovládací prvek (GS06) |Vyžaduje, zadejte rozsah čísel pro ovládací prvek čísla skupiny. Zadejte číselnou hodnotu minimálně 1 a maximálně 999999999 |
| Transakce nastavit počet ovládací prvek (ST02) |Vyžaduje, zadejte rozsah čísel pro číslo nastavit řízení transakce. Zadejte rozsah číselné hodnoty minimálně 1 a maximálně 999999999 |
| Předvolba |Volitelné, určené pro rozsah čísla řízení sadu transakcí použít v potvrzení. Zadejte číselnou hodnotu pro střední dvě pole a alfanumerické hodnoty (v případě potřeby) pro pole předponu a příponu. Střední pole jsou povinné a obsahovat minimální a maximální hodnoty pro číslo ovládacího prvku |
| Přípona |Volitelné, určené pro rozsah čísla řízení sadu transakcí používány potvrzení. Zadejte číselnou hodnotu pro střední dvě pole a alfanumerické hodnotu (v případě potřeby) pro pole předponu a příponu. Střední pole jsou povinné a obsahovat minimální a maximální hodnoty pro číslo ovládacího prvku |

### <a name="character-sets-and-separators"></a>Znakové sady a oddělovače

Jiného, než znaková sada, můžete u každého typu zprávy zadejte jinou sadu oddělovače. Pokud znakovou sadu pro danou zprávou schématu není zadána, použije se výchozí znakovou sadu.

![Zadejte oddělovače pro typ zprávy](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Vlastnost | Popis |
| --- | --- |
| Znakové sady, který se má použít |Ověření vlastnosti, vyberte X12 znakovou sadu. Možnosti jsou Basic, rozšířené a UTF8. |
| Schéma |V rozevíracím seznamu vyberte schéma. Po dokončení každý řádek je automaticky přidán nový řádek. Pro vybrané schéma vyberte sadu oddělovačů, který chcete použít, na základě popisů oddělovače níže. |
| Typ vstupu |V rozevíracím seznamu vyberte typ vstupu. |
| Oddělovač komponent |Chcete-li samostatné složené datové prvky, zadejte jeden znak. |
| Oddělovač datových prvků |Chcete-li samostatné jednoduché datové elementů v rámci složené datové prvky, zadejte jeden znak. |
| Nahrazení používá znak |Zadejte znak, kterým používá k nahrazení všech znaků oddělujících v datové části dat při generování odchozí X12 zprávy. |
| Ukončovací znak segmentu |Chcete-li označení konce EDI segment, zadejte jeden znak. |
| Přípona |Vyberte znak, který se používá s identifikátor segmentu. Určíte příponu, datový prvek ukončovací segment nesmí být prázdné. Pokud segment ukončení je prázdné, je třeba určit příponu. |

> [!TIP]
> Zadat speciální znak hodnoty, upravit smlouvu jako JSON a zadejte hodnotu ASCII pro speciální znak.

### <a name="validation"></a>Ověření

![Nastavit vlastnosti ověření pro zasílání zpráv](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Po dokončení každý řádek ověření jiné automaticky přidá. Pokud nezadáte všechna pravidla, ověření používá "Výchozího" řádku.

| Vlastnost | Popis |
| --- | --- |
| Typ zprávy |Vyberte typ zprávy EDI. |
| Ověřování EDI |Provedení ověření EDI pro datové typy podle definice schématu EDI vlastnosti, omezení délky, prázdný datové prvky a koncové oddělovače. |
| Rozšíření ověřování |Pokud datový typ není EDI, ověření se na element požadavek na data a povoleny opakování, výčty a data element délka ověření (min/max). |
| Povolit úvodní a koncové nuly |Zachování všechny další počáteční nebo koncové nula a místo znaků. Nevysunujte tyto znaky. |
| Oříznout úvodní a koncové nuly |Odeberte počáteční nebo koncové nulový počet znaků. |
| Zásady pro koncový oddělovač |Generovat koncové oddělovače. <p>Vyberte **není povoleno** zakázat koncové oddělovače a oddělovače v odeslané výměnu. Pokud výměnu koncové oddělovače a oddělovačů, výměnu je deklarovaná není platný. <p>Vyberte **volitelné** odeslat mimoúrovňové křižovatky s nebo bez koncové oddělovače a oddělovačů. <p>Vyberte **povinné** Pokud odeslané výměnu musí mít koncové oddělovače a oddělovačů. |

## <a name="find-your-created-agreement"></a>Najít vaší vytvořené smlouvy

1.  Po dokončení nastavení na všechny vlastnosti vaše smlouvy **přidat** vyberte **OK** dokončit vytváření vaší smlouvy a vrátíte se k účtu integrace.

    Nově přidané smlouvy nyní se zobrazí v vaše **smlouvy** seznamu.

2.  Můžete také zobrazit vaše smlouvy v váš účet Přehled integrace. V nabídce váš účet integrace zvolte **přehled**, vyberte **smlouvy** dlaždici.

    ![Vyberte že dlaždici "Smlouvy"](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="view-the-swagger"></a>Zobrazení swagger
Najdete v článku [swagger podrobnosti](/connectors/x12/). 

## <a name="learn-more"></a>Další informace
* [Další informace o integračního balíčku Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Další informace o Enterprise integračního balíčku")  

