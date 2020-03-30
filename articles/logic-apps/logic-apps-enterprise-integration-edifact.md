---
title: Edifact zprávy pro integraci B2B
description: Výměna zpráv EDIFACT ve formátu EDI pro podnikovou integraci B2B v aplikacích Azure Logic Apps s podnikovou integrací
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/26/2016
ms.openlocfilehash: 3ada12a0cde122fb78815a1d3241d8acb9da2580
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651453"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Výměna zpráv EDIFACT pro podnikovou integraci B2B v aplikacích Azure Logic Apps s podnikovou integrací

Než si budete moci vyměňovat zprávy EDIFACT pro Azure Logic Apps, musíte vytvořit smlouvu EDIFACT a uložit tuto smlouvu do svého integračního účtu. Zde jsou kroky pro vytvoření smlouvy EDIFACT.

> [!NOTE]
> Tato stránka popisuje funkce EDIFACT pro Azure Logic Apps. Další informace naleznete v tématu [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Než začnete

Zde jsou položky, které potřebujete:

* [Účet integrace,](logic-apps-enterprise-integration-create-integration-account.md) který už je definovaný a přidružený k vašemu předplatnému Azure  
* Alespoň dva [partneři,](logic-apps-enterprise-integration-partners.md) kteří jsou již definováni ve vašem integračním účtu

> [!NOTE]
> Při vytváření smlouvy musí obsah zpráv, které přijímáte nebo odesíláte partnerovi, odpovídat typu smlouvy.

Po [vytvoření účtu integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a [přidání partnerů](logic-apps-enterprise-integration-partners.md)můžete vytvořit smlouvu EDIFACT pomocí následujících kroků.

## <a name="create-an-edifact-agreement"></a>Vytvoření smlouvy EDIFACT 

1. Přihlaste se k [portálu Azure](https://portal.azure.com "portál Azure"). 

2. V hlavní nabídce Azure vyberte **Všechny služby**. Do vyhledávacího pole zadejte "integrace" a pak vyberte **Integrační účty**.

   ![Najděte svůj integrační účet](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Pokud se **všechny služby** nezobrazí, bude pravděpodobně muset rozbalit nabídku jako první. V horní části sbalené nabídky vyberte **Zobrazit textové popisky**.

3. V části **Účty integrace**vyberte účet integrace, ve kterém chcete vytvořit smlouvu.

   ![Vyberte účet integrace, kde chcete vytvořit smlouvu.](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Zvolte **Smlouvy**. Pokud dlaždici Smlouvy nemáte, přidejte ji jako první.   

   ![Zvolte dlaždici "Dohody"](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. Na stránce Dohody zvolte **Přidat**.

   ![Zvolte "Přidat"](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. V části **Přidat**zadejte **název** smlouvy. Pro **typ dohody**vyberte **edifact**. Vyberte pro svou smlouvu **hostovaného partnera**, **identitu hostitele**, **partnera hosta**a **identitu hosta.**

   ![Poskytnout podrobnosti o smlouvě](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Vlastnost | Popis |
   | --- | --- |
   | Name (Název) |Název smlouvy |
   | Typ smlouvy | Měl by to být EDIFACT |
   | Hostitelský partner |Dohoda potřebuje hostitele i partnera hosta. Hostitelský partner představuje organizaci, která konfiguruje smlouvu. |
   | Identita hostitele |Identifikátor hostitelského partnera |
   | Partner pro hosty |Dohoda potřebuje hostitele i partnera hosta. Partner hosta představuje organizaci, která obchoduje s partnerem hostitele. |
   | Identita hosta |Identifikátor pro partnera hosta |
   | Přijímat nastavení |Tyto vlastnosti platí pro všechny zprávy přijaté smlouvou. |
   | Odeslat nastavení |Tyto vlastnosti platí pro všechny zprávy odeslané smlouvou. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurace způsobu, jakým vaše smlouva zpracovává přijaté zprávy

Nyní, když jste nastavili vlastnosti smlouvy, můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává příchozí zprávy přijaté od vašeho partnera prostřednictvím této smlouvy.

1. V části **Přidat**vyberte **Nastavení příjmu**.
Nakonfigurujte tyto vlastnosti na základě vaší smlouvy s partnerem, který si s vámi vyměňuje zprávy. Popisy vlastností naleznete v tabulkách v této části.

   **Nastavení příjmu** je uspořádáno do těchto sekcí: Identifikátory, Potvrzení, Schémata, Čísla ovládacích prvku, Ověřování a Interní nastavení.

   ![Konfigurace "Nastavení příjmu"](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Po dokončení nezapomeňte nastavení uložit tak, že zvolíte **OK**.

Nyní je vaše smlouva připravena zpracovávat příchozí zprávy, které odpovídají vybraným nastavením.

### <a name="identifiers"></a>Identifikátory

| Vlastnost | Popis |
| --- | --- |
| UNB6.1 (Referenční heslo příjemce) |Zadejte alfanumerickou hodnotu v rozmezí 1 až 14 znaků. |
| UNB6.2 (Kvalifikátor odkazů pro příjemce) |Zadejte alfanumerickou hodnotu s minimálně jedním znakem a maximálně dvěma znaky. |

### <a name="acknowledgments"></a>Poděkování

| Vlastnost | Popis |
| --- | --- |
| Přijetí zprávy (CONTRL) |Zaškrtnutím tohoto políčka vrátíte technické potvrzení (CONTRL) odesílateli výměny. Potvrzení je odesláno odesílateli výměny na základě nastavení odeslání pro smlouvu. |
| Potvrzení (CONTRL) |Zaškrtnutím tohoto políčka vrátíte potvrzení o funkčnosti (CONTRL) odesílateli výměny Potvrzení je odesláno odesílateli výměny na základě nastavení odeslání pro smlouvu. |

### <a name="schemas"></a>Schémata

| Vlastnost | Popis |
| --- | --- |
| UNH2.1 (TYP) |Vyberte typ sady transakcí. |
| UNH2.2 (VERZE) |Zadejte číslo verze zprávy. (Minimálně jeden znak; maximálně tři znaky). |
| UNH2.3 (UVOLNĚNÍ) |Zadejte číslo vydání zprávy. (Minimálně jeden znak; maximálně tři znaky). |
| UNH2.5 (PŘIDRUŽENÝ PŘIDĚLENÝ KÓD) |Zadejte přiřazený kód. (Maximálně šest znaků. Musí být alfanumerická). |
| UNG2.1 (ID odesílatele aplikace) |Zadejte alfanumerickou hodnotu s minimálně jedním znakem a maximálně 35 znaky. |
| UNG2.2 (KVALIFIKÁTOR KÓDU ODESÍLATELE APLIKACE) |Zadejte alfanumerickou hodnotu s maximálně čtyřmi znaky. |
| Schématu |Vyberte dříve nahrané schéma, které chcete použít z přidruženého účtu integrace. |

### <a name="control-numbers"></a>Kontrolní čísla

| Vlastnost | Popis |
| --- | --- |
| Zakázat duplikáty kontrolního čísla výměny |Chcete-li blokovat duplicitní mimoúrovňové křižovatky, vyberte tuto vlastnost. Je-li vybrána tato možnost, edifact decode action zkontroluje, zda číslo řízení výměny (UNB5) pro přijatou výměnu neodpovídá dříve zpracovanému kontrolnímu číslu výměny. Pokud je zjištěna shoda, výměna není zpracována. |
| Zkontrolujte duplicitní UNB5 každých (dní) |Pokud jste se rozhodli zakázat duplicitní čísla ovládacího prvku pro výměnu, můžete určit počet dní, kdy chcete provést kontrolu, zadáním příslušné hodnoty pro toto nastavení. |
| Zakázat duplikáty kontrolního čísla skupiny |Chcete-li blokovat mimoúrovňové křižovatky s duplicitními čísly řízení skupin (UNG5), vyberte tuto vlastnost. |
| Zakázat disiva č. transakce nastavit duplicitní kontrolní číslo |Chcete-li blokovat mimoúrovňové křižovatky s duplicitními řídicími čísly sady transakcí (UNH1), vyberte tuto vlastnost. |
| Kontrolní číslo potvrzení EDIFACT |Chcete-li určit referenční čísla sady transakcí pro použití v potvrzení, zadejte hodnotu předpony, rozsah referenčních čísel a příponu. |

### <a name="validation"></a>Ověřování

Po dokončení každého řádku ověření se automaticky přidá další řádek. Pokud nezadáte žádná pravidla, pak ověření používá řádek "Výchozí".

| Vlastnost | Popis |
| --- | --- |
| Typ zprávy |Vyberte typ zprávy EDI. |
| Ověření EDI |Proveďte ověření EDI u datových typů definovaných vlastnostmi EDI schématu, omezeními délky, prázdnými datovými prvky a koncovými oddělovači. |
| Rozšířené ověření |Pokud datový typ není EDI, ověření je na požadavek datového prvku a povolené opakování, výčty a ověření délky datového prvku (min/max). |
| Povolit prokladové/koncové nuly |Zachovejte všechny další úvodní nebo koncové znaky nuly a mezery. Neodstraňujte tyto znaky. |
| Oříznout proklatová/koncové nuly |Odstraňte úvodní nebo koncové znaky nula a mezery. |
| Zásady koncového oddělovače |Vygenerujte koncové oddělovače. <p>Výběrem **možnosti Není povoleno** zakázat koncové oddělovače a oddělovače v přijaté výměně. Pokud má výměna koncové oddělovače a oddělovače, je tato výměna prohlášena za neplatnou. <p>Vyberte **Volitelné,** chcete-li přijmout mimoúrovňové křižovatky s koncovými oddělovači a oddělovači nebo bez nich. <p>Vyberte **Povinné,** když přijatá výměna musí mít koncové oddělovače a oddělovače. |

### <a name="internal-settings"></a>Interní nastavení

| Vlastnost | Popis |
| --- | --- |
| Vytvoření prázdných značek XML, pokud jsou povoleny koncové oddělovače |Toto políčko zaškrtněte, aby odesílatel výměny obsahoval prázdné značky XML pro koncové oddělovače. |
| Rozdělit výměnu jako sady transakcí - pozastavit sady transakcí při chybě|Analyzuje každou transakci nastavenou ve výměně do samostatného dokumentu XML použitím příslušné obálky na sadu transakcí. Pozastavit pouze sady transakcí, které se nezdaří ověření. |
| Rozdělit výměnu jako sady transakcí - pozastavit výměnu při chybě|Každou transakci nastavenou ve výměně analyzuje do samostatného dokumentu XML použitím příslušné obálky. Pozastavit celou výměnu, pokud se ověření nepodaří jedné nebo více nastavovacích sad transakcí v mezibankovní mase. | 
| Zachovat výměnu - pozastavit sady transakcí při chybě |Ponechá výměnu beze změny, vytvoří dokument XML pro celou dávkovou výměnu. Pozastavte pouze sady transakcí, které se nezdaří ověření, zatímco nadále zpracovat všechny ostatní sady transakcí. |
| Zachovat výměnu - pozastavit výměnu při chybě |Ponechá výměnu beze změny, vytvoří dokument XML pro celou dávkovou výměnu. Pozastavit celou výměnu, pokud se ověření nepodaří jedné nebo více nastavovacích sad transakcí v mezibankovní mase. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurace způsobu, jakým smlouva odesílá zprávy

Můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává odchozí zprávy, které odesíláte svým partnerům prostřednictvím této smlouvy.

1.  V části **Přidat**vyberte **Odeslat nastavení**.
Nakonfigurujte tyto vlastnosti na základě vaší smlouvy s partnerem, který si s vámi vyměňuje zprávy. Popisy vlastností naleznete v tabulkách v této části.

    **Nastavení odesílání** je uspořádáno do těchto oddílů: Identifikátory, Potvrzení, Schémata, Obálky, Znakové sady a oddělovače, Čísla ovládacích prvku a Ověření.

    ![Konfigurace "Nastavení odeslání"](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Po dokončení nezapomeňte nastavení uložit tak, že zvolíte **OK**.

Nyní je vaše smlouva připravena zpracovávat odchozí zprávy, které odpovídají vybraným nastavením.

### <a name="identifiers"></a>Identifikátory

| Vlastnost | Popis |
| --- | --- |
| UNB1.2 (Syntaktická verze) |Vyberte hodnotu mezi **1** a **4**. |
| UNB2.3 (Adresa zpětného směrování odesílatele) |Zadejte alfanumerickou hodnotu s minimálně jedním znakem a maximálně 14 znaky. |
| UNB3.3 (Adresa zpětného směrování příjemce) |Zadejte alfanumerickou hodnotu s minimálně jedním znakem a maximálně 14 znaky. |
| UNB6.1 (Referenční heslo příjemce) |Zadejte alfanumerickou hodnotu s maximálně jedním a maximálně 14 znaky. |
| UNB6.2 (Kvalifikátor odkazů pro příjemce) |Zadejte alfanumerickou hodnotu s minimálně jedním znakem a maximálně dvěma znaky. |
| UNB7 (ID odkazu na aplikaci) |Zadání alfanumerické hodnoty s minimálně jedním znakem a maximálně 14 znaky |

### <a name="acknowledgment"></a>Potvrzení

| Vlastnost | Popis |
| --- | --- |
| Přijetí zprávy (CONTRL) |Toto políčko zaškrtněte, pokud hostovaný partner očekává, že obdrží technické potvrzení (CONTRL). Toto nastavení určuje, že hostovaný partner, který odesílá zprávu, požaduje potvrzení od partnera hosta. |
| Potvrzení (CONTRL) |Toto políčko zaškrtněte, pokud hostovaný partner očekává potvrzení o funkci funkčnosti (CONTRL). Toto nastavení určuje, že hostovaný partner, který odesílá zprávu, požaduje potvrzení od partnera hosta. |
| Generovat smyčku SG1/SG4 pro přijaté sady transakcí |Pokud jste se rozhodli požádat o potvrzení funkčnosti, zaškrtněte toto políčko, chcete-li vynutit generování smyček SG1/SG4 ve funkčních potvrzovacích potvrzovacích potvrzování CONTRL pro přijaté sady transakcí. |

### <a name="schemas"></a>Schémata

| Vlastnost | Popis |
| --- | --- |
| UNH2.1 (TYP) |Vyberte typ sady transakcí. |
| UNH2.2 (VERZE) |Zadejte číslo verze zprávy. |
| UNH2.3 (UVOLNĚNÍ) |Zadejte číslo vydání zprávy. |
| Schématu |Vyberte schéma, které chcete použít. Schémata jsou umístěna ve vašem účtu integrace. Chcete-li získat přístup k schématům, nejprve propojte účet integrace s aplikací Logika. |

### <a name="envelopes"></a>Obálky

| Vlastnost | Popis |
| --- | --- |
| UNB8 (Kód priority zpracování) |Zadejte abecední hodnotu, která není delší než jeden znak. |
| UNB10 (komunikační dohoda) |Zadejte alfanumerickou hodnotu s minimálně jedním znakem a maximálně 40 znaky. |
| UNB11 (testovací indikátor) |Zaškrtnutím tohoto políčka označíte, že vygenerovaná výměna jsou testovací data. |
| Použít segment UNA (Avízo řetězce služby) |Zaškrtnutím tohoto políčka vygenerujete segment UNA pro výměnu, která má být odeslána. |
| Použít segmenty UNG (záhlaví funkční skupiny) |Toto políčko zaškrtněte, chcete-li vytvořit segmenty seskupení v záhlaví funkční skupiny ve zprávách odeslaných partnerovi hosta. K vytvoření segmentů UNG se používají následující hodnoty: <p>Do **pole UNG1**zadejte alfanumerickou hodnotu s minimálně jedním znakem a maximálně šesti znaky. <p>Pro **UNG2.1**zadejte alfanumerickou hodnotu s minimálně jedním znakem a maximálně 35 znaky. <p>Do **pole UNG2.2**zadejte alfanumerickou hodnotu s maximálně čtyřmi znaky. <p>Pro **UNG3.1**zadejte alfanumerickou hodnotu s minimálně jedním znakem a maximálně 35 znaky. <p>Do **pole UNG3.2**zadejte alfanumerickou hodnotu s maximálně čtyřmi znaky. <p>Do **pole UNG6**zadejte alfanumerickou hodnotu s minimem jednoho a maximálně třemi znaky. <p>Pro **UNG7.1**zadejte alfanumerickou hodnotu s minimálně jedním znakem a maximálně třemi znaky. <p>Pro **UNG7.2**zadejte alfanumerickou hodnotu s minimálně jedním znakem a maximálně třemi znaky. <p>Pro **UNG7.3**zadejte alfanumerickou hodnotu s minimem 1 znak a maximálně 6 znaků. <p>Do **pole UNG8**zadejte alfanumerickou hodnotu s minimálně jedním znakem a maximálně 14 znaky. |

### <a name="character-sets-and-separators"></a>Znakové sady a oddělovače

Kromě znakové sady můžete zadat jinou sadu oddělovačů, která bude použita pro každý typ zprávy. Pokud znaková sada není zadána pro dané schéma zprávy, použije se výchozí znaková sada.

| Vlastnost | Popis |
| --- | --- |
| UNB1.1 (identifikátor systému) |Vyberte znakovou sadu EDIFACT, která se má použít na odchozí výměnu. |
| Schéma |V rozevíracím seznamu vyberte schéma. Po dokončení každého řádku se automaticky přidá nový řádek. U vybraného schématu vyberte sadu oddělovačů, kterou chcete použít, na základě níže uvedených popisů oddělovačů. |
| Typ vstupu |V rozevíracím seznamu vyberte typ vstupu. |
| Oddělovač komponent |Chcete-li oddělit složené datové prvky, zadejte jeden znak. |
| Oddělovač prvků dat |Chcete-li oddělit jednoduché datové prvky ve složených datových prvcích, zadejte jeden znak. |
| Segment zakončení |Chcete-li označit konec segmentu EDI, zadejte jeden znak. |
| Přípona |Vyberte znak, který se používá s identifikátorem segmentu. Pokud určíte příponu, může být datový prvek segmentu zakončení prázdný. Pokud je zakončení segmentu ponecháno prázdné, musíte určit příponu. |

### <a name="control-numbers"></a>Kontrolní čísla

| Vlastnost | Popis |
| --- | --- |
| UNB5 (Kontrolní číslo výměny) |Zadejte předponu, rozsah hodnot pro číslo ovládacího prvku výměny a příponu. Tyto hodnoty se používají ke generování odchozí výměny. Předpona a přípona jsou volitelné, zatímco je vyžadováno číslo ovládacího prvku. Číslo ovládacího prvku se zintáží pro každou novou zprávu; předpona a přípona zůstávají stejné. |
| UNG5 (kontrolní číslo skupiny) |Zadejte předponu, rozsah hodnot pro číslo ovládacího prvku výměny a příponu. Tyto hodnoty se používají ke generování čísla ovládacího prvku skupiny. Předpona a přípona jsou volitelné, zatímco je vyžadováno číslo ovládacího prvku. Číslo ovládacího prvku se zintáží pro každou novou zprávu, dokud není dosaženo maximální hodnoty; předpona a přípona zůstávají stejné. |
| UNH1 (referenční číslo záhlaví zprávy) |Zadejte předponu, rozsah hodnot pro číslo ovládacího prvku výměny a příponu. Tyto hodnoty se používají ke generování referenčního čísla záhlaví zprávy. Předpona a přípona jsou volitelné, zatímco referenční číslo je požadováno. Referenční číslo se zintáží pro každou novou zprávu; předpona a přípona zůstávají stejné. |

### <a name="validation"></a>Ověřování

Po dokončení každého řádku ověření se automaticky přidá další řádek. Pokud nezadáte žádná pravidla, pak ověření používá řádek "Výchozí".

| Vlastnost | Popis |
| --- | --- |
| Typ zprávy |Vyberte typ zprávy EDI. |
| Ověření EDI |Proveďte ověření EDI u datových typů definovaných vlastnostmi EDI schématu, omezení délky, prázdných datových prvků a koncových oddělovačů. |
| Rozšířené ověření |Pokud datový typ není EDI, ověření je na požadavek datového prvku a povolené opakování, výčty a ověření délky datového prvku (min/max). |
| Povolit prokladové/koncové nuly |Zachovejte všechny další úvodní nebo koncové znaky nuly a mezery. Neodstraňujte tyto znaky. |
| Oříznout proklatová/koncové nuly |Odstraňte úvodní nebo koncové nulové znaky. |
| Zásady koncového oddělovače |Vygenerujte koncové oddělovače. <p>Výběrem **možnosti Není povoleno** zakázat koncové oddělovače a oddělovače v odeslané výměně. Pokud má výměna koncové oddělovače a oddělovače, je tato výměna prohlášena za neplatnou. <p>Vyberte **Volitelné,** chcete-li odeslat mimoúrovňové křižovatky s koncovými oddělovači a oddělovači nebo bez nich. <p>Vyberte **Povinné,** pokud odeslaná výměna musí mít koncové oddělovače a oddělovače. |

## <a name="find-your-created-agreement"></a>Vyhledání vytvořené smlouvy

1.  Po dokončení nastavení všech vlastností smlouvy zvolte na stránce **Přidat** **možnost OK,** chcete-li dokončit vytváření smlouvy a vrátit se k účtu integrace.

    Nově přidaná smlouva se nyní zobrazí v seznamu **Smluv.**

2.  Smlouvy můžete také zobrazit v přehledu účtu integrace. V nabídce integračního účtu zvolte **Přehled**a pak vyberte dlaždici **Smlouvy.** 

    ![Zvolte dlaždici "Dohody"](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o tomto konektoru, jako jsou akce a omezení, jak je popsáno v souboru Swagger konektoru, naleznete [na referenční stránce konektoru](https://docs.microsoft.com/connectors/edifact/).

> [!NOTE]
> Pro aplikace logiky v [prostředí služby integrace (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), tento konektor je ise označené verze používá [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) místo.

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)