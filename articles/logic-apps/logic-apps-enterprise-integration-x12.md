---
title: Zprávy X12 pro integraci B2B
description: Zprávy Exchange X12 ve formátu EDI pro podnikovou integraci B2B v aplikacích Azure Logic Apps s podnikovou integrací
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/31/2017
ms.openlocfilehash: cbf0a1f033ddafc68debab8de26dff29d73cc98e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651470"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Zprávy Exchange X12 pro podnikovou integraci B2B v aplikacích Azure Logic Apps s podnikovou integrací

Než si budete moci vyměňovat zprávy X12 pro Azure Logic Apps, musíte vytvořit smlouvu X12 a uložit tuto smlouvu do svého integračního účtu. Zde jsou kroky, jak vytvořit smlouvu X12.

> [!NOTE]
> Tato stránka popisuje funkce X12 pro Azure Logic Apps. Další informace naleznete v tématu [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Než začnete

Zde jsou položky, které potřebujete:

* [Účet integrace,](logic-apps-enterprise-integration-create-integration-account.md) který už je definovaný a přidružený k vašemu předplatnému Azure
* Nejméně dva [partneři,](../logic-apps/logic-apps-enterprise-integration-partners.md) kteří jsou definováni ve vašem integračním účtu a konfigurováni s identifikátorem X12 v části **Obchodní identity**    
* Požadované [schéma,](../logic-apps/logic-apps-enterprise-integration-schemas.md) které můžete nahrát do účtu integrace

Po [vytvoření účtu integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [přidejte partnery](logic-apps-enterprise-integration-partners.md)a máte [schéma,](../logic-apps/logic-apps-enterprise-integration-schemas.md) které chcete použít, můžete vytvořit smlouvu X12 pomocí následujících kroků.

## <a name="create-an-x12-agreement"></a>Vytvoření smlouvy X12

1. Přihlaste se k [portálu Azure](https://portal.azure.com "portál Azure"). 

2. V hlavní nabídce Azure vyberte **Všechny služby**. 
   Do vyhledávacího pole zadejte "integrace" a pak vyberte **Integrační účty**.  

   ![Najděte svůj integrační účet](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Pokud se **všechny služby** nezobrazí, bude pravděpodobně muset rozbalit nabídku jako první. V horní části sbalené nabídky vyberte **Zobrazit nabídku**.

3. V části **Účty integrace**vyberte účet integrace, do kterého chcete smlouvu přidat.

   ![Vyberte účet integrace, kde chcete vytvořit smlouvu.](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Vyberte **Přehled**a pak vyberte dlaždici **Smlouvy.** 
   Pokud dlaždici Smlouvy nemáte, přidejte ji jako první. 

   ![Zvolte dlaždici "Dohody"](./media/logic-apps-enterprise-integration-x12/agreement-1.png)

5. V části **Smlouvy**zvolte **Přidat**.

   ![Zvolte "Přidat"](./media/logic-apps-enterprise-integration-x12/agreement-2.png)     

6. V části **Přidat**zadejte **název** smlouvy. 
   Pro typ smlouvy vyberte **X12**. 
   Vyberte pro svou smlouvu **hostovaného partnera**, **identitu hostitele**, **partnera hosta**a **identitu hosta.** 
   Další podrobnosti o vlastnostech naleznete v tabulce v tomto kroku.

    ![Poskytnout podrobnosti o smlouvě](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Vlastnost | Popis |
    | --- | --- |
    | Name (Název) |Název smlouvy |
    | Typ smlouvy | By měl být X12 |
    | Hostitelský partner |Dohoda potřebuje hostitele i partnera hosta. Hostitelský partner představuje organizaci, která konfiguruje smlouvu. |
    | Identita hostitele |Identifikátor hostitelského partnera |
    | Partner pro hosty |Dohoda potřebuje hostitele i partnera hosta. Partner hosta představuje organizaci, která obchoduje s partnerem hostitele. |
    | Identita hosta |Identifikátor pro partnera hosta |
    | Přijímat nastavení |Tyto vlastnosti platí pro všechny zprávy přijaté smlouvou. |
    | Odeslat nastavení |Tyto vlastnosti platí pro všechny zprávy odeslané smlouvou. |  

   > [!NOTE]
   > Řešení smlouvy X12 závisí na porovnání kvalifikátoru a identifikátoru odesílatele a kvalifikátoru a identifikátoru příjemce definovaného v partnerském a příchozí zprávě. Pokud se tyto hodnoty změní pro vašeho partnera, aktualizujte také smlouvu.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurace způsobu, jakým vaše smlouva zpracovává přijaté zprávy

Nyní, když jste nastavili vlastnosti smlouvy, můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává příchozí zprávy přijaté od vašeho partnera prostřednictvím této smlouvy.

1.  V části **Přidat**vyberte **Nastavení příjmu**.
Nakonfigurujte tyto vlastnosti na základě vaší smlouvy s partnerem, který si s vámi vyměňuje zprávy. Popisy vlastností naleznete v tabulkách v této části.

    **Nastavení příjmu** je uspořádáno do těchto sekcí: Identifikátory, Potvrzení, Schémata, Obálky, Kontrolní čísla, Ověření a Interní nastavení.

2. Po dokončení nezapomeňte nastavení uložit tak, že zvolíte **OK**.

Nyní je vaše smlouva připravena zpracovávat příchozí zprávy, které odpovídají vybraným nastavením.

### <a name="identifiers"></a>Identifikátory

![Nastavení vlastností identifikátoru](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Vlastnost | Popis |
| --- | --- |
| ISA1 (kvalifikátor autorizace) |V rozevíracím seznamu vyberte hodnotu kvalifikátoru autorizace. |
| ISA2 |Nepovinný parametr. Zadejte hodnotu Informace o autorizaci. Pokud je hodnota zadaná pro ISA1 jiná než 00, zadejte minimálně jeden alfanumerický znak a maximálně 10. |
| ISA3 (kvalifikátor zabezpečení) |V rozevíracím seznamu vyberte hodnotu kvalifikátoru zabezpečení. |
| ISA4 |Nepovinný parametr. Zadejte hodnotu Informace o zabezpečení. Pokud je hodnota zadaná pro ISA3 jiná než 00, zadejte minimálně jeden alfanumerický znak a maximálně 10. |

### <a name="acknowledgment"></a>Potvrzení

![Nastavit vlastnosti potvrzení](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Vlastnost | Popis |
| --- | --- |
| TA1 očekáváno |Vrátí technické potvrzení odesílateli výměny. |
| DM bylo očekáváno. |Vrátí funkční potvrzení odesílateli výměny. Pak vyberte, zda chcete potvrzení 997 nebo 999 na základě verze schématu |
| Zahrnout smyčku AK2/IK2 |Umožňuje generování smyček AK2 ve funkčních potvrzeních pro přijaté sady transakcí. |

### <a name="schemas"></a>Schémata

Vyberte schéma pro každý typ transakce (ST1) a sender aplikace (GS2). Kanál pro příjem rozebírá příchozí zprávu porovnáním hodnot ST1 a GS2 v příchozí zprávě s hodnotami, které zde nastavíte, a schématu příchozí zprávy se zde nastaveným schématem.

![Vybrat schéma](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Vlastnost | Popis |
| --- | --- |
| Version |Výběr verze X12 |
| Typ transakce (ST01) |Vyberte typ transakce |
| Aplikace odesílatele (GS02) |Výběr aplikace odesílatele |
| Schéma |Vyberte soubor schématu, který chcete použít. Schémata jsou přidány do vašeho účtu integrace. |

> [!NOTE]
> Nakonfigurujte požadované [schéma,](../logic-apps/logic-apps-enterprise-integration-schemas.md) které je odesláno do vašeho [účtu integrace](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Obálky

![Určení oddělovače v sadě transakcí: zvolte Standardní identifikátor nebo Oddělovač opakování.](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Vlastnost | Popis |
| --- | --- |
| Použití ISA11 |Určuje oddělovač, který má být používán v sadě transakcí: <p>Vyberte **Standardní identifikátor,** chcete-li použít tečku (.) pro desítkovou notaci, nikoli desetinnou notaci příchozího dokladu v kanálu příjmu EDI. <p>Vyberte **Oddělovač opakování,** chcete-li určit oddělovač pro opakované výskyty jednoduchého datového prvku nebo opakované datové struktury. Například obvykle se jako oddělovač opakování používá karát (^). Pro schémata HIPAA můžete použít pouze karát. |

### <a name="control-numbers"></a>Kontrolní čísla

![Vyberte způsob zpracování duplicitních čísel ovládacích prvku.](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Vlastnost | Popis |
| --- | --- |
| Zakázat duplikáty kontrolního čísla výměny |Blokovat duplicitní mimoúrovňové křižovatky. Zkontroluje kontrolní číslo výměny (ISA13) pro přijaté číslo řízení mezibankovní. Pokud je zjištěna shoda, kanál příjmu nezpracovává výměnu. Počet dní pro provedení kontroly můžete zadat zadáním hodnoty *pro check for duplicate ISA13 každý (dny)*. |
| Zakázat duplikáty kontrolního čísla skupiny |Blokovat mimoúrovňové křižovatky s duplicitními kontrolními čísly skupin. |
| Zakázat disiva č. transakce nastavit duplicitní kontrolní číslo |Blokovat mimoúrovňové křižovatky s duplicitními řídicími čísly sady transakcí. |

### <a name="validation"></a>Ověřování

![Nastavení vlastností ověření pro přijaté zprávy](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Po dokončení každého řádku ověření se automaticky přidá další řádek. Pokud nezadáte žádná pravidla, pak ověření používá řádek "Výchozí".

| Vlastnost | Popis |
| --- | --- |
| Typ zprávy |Vyberte typ zprávy EDI. |
| Ověření EDI |Proveďte ověření EDI u datových typů definovaných vlastnostmi EDI schématu, omezeními délky, prázdnými datovými prvky a koncovými oddělovači. |
| Rozšířené ověření |Pokud datový typ není EDI, ověření je na požadavek datového prvku a povolené opakování, výčty a ověření délky datového prvku (min/max). |
| Povolit prokladové/koncové nuly |Zachovejte všechny další úvodní nebo koncové znaky nuly a mezery. Neodstraňujte tyto znaky. |
| Oříznout proklatová/koncové nuly |Odstraňte úvodní nebo koncové znaky nula a mezery. |
| Zásady koncového oddělovače |Vygenerujte koncové oddělovače. <p>Výběrem **možnosti Není povoleno** zakázat koncové oddělovače a oddělovače v přijaté výměně. Pokud má výměna koncové oddělovače a oddělovače, je tato výměna prohlášena za neplatnou. <p>Vyberte **Volitelné,** chcete-li přijmout mimoúrovňové křižovatky s koncovými oddělovači a oddělovači nebo bez nich. <p>Vyberte **Povinné,** když výměna musí mít koncové oddělovače a oddělovače. |

### <a name="internal-settings"></a>Interní nastavení

![Výběr interního nastavení](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Vlastnost | Popis |
| --- | --- |
| Převést implicitní desítkový formát "Nn" na základní číselnou hodnotu 10 |Převede číslo EDI určené ve formátu Nn na číselnou hodnotu base-10. |
| Vytvoření prázdných značek XML, pokud jsou povoleny koncové oddělovače |Toto políčko zaškrtněte, aby odesílatel výměny obsahoval prázdné značky XML pro koncové oddělovače. |
| Rozdělit výměnu jako sady transakcí - pozastavit sady transakcí při chybě|Analyzuje každou transakci nastavenou ve výměně do samostatného dokumentu XML použitím příslušné obálky na sadu transakcí. Pozastaví pouze transakce, kde se ověření nezdaří. |
| Rozdělit výměnu jako sady transakcí - pozastavit výměnu při chybě|Každou transakci nastavenou ve výměně analyzuje do samostatného dokumentu XML použitím příslušné obálky. Pozastaví celou výměnu, pokud se ověření nepodaří jedné nebo více nastavovacích sad transakcí v mezibankovní majek. | 
| Zachovat výměnu - pozastavit sady transakcí při chybě |Ponechá výměnu beze změny, vytvoří dokument XML pro celou dávkovou výměnu. Pozastaví pouze sady transakcí, které se nezdaří ověření, zatímco nadále zpracovává všechny ostatní sady transakcí. |
| Zachovat výměnu - pozastavit výměnu při chybě |Ponechá výměnu beze změny, vytvoří dokument XML pro celou dávkovou výměnu. Pozastaví celou výměnu, pokud se ověření nepodaří jednu nebo více nastavovacích sad transakcí v mezibankovní mase. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurace způsobu, jakým smlouva odesílá zprávy

Můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává odchozí zprávy, které odesíláte partnerovi prostřednictvím této smlouvy.

1.  V části **Přidat**vyberte **Odeslat nastavení**.
Nakonfigurujte tyto vlastnosti na základě vaší smlouvy s partnerem, který si s vámi vyměňuje zprávy. Popisy vlastností naleznete v tabulkách v této části.

    **Nastavení odesílání** je uspořádáno do těchto oddílů: Identifikátory, Potvrzení, Schémata, Obálky, Znakové sady a oddělovače, Čísla ovládacích prvku a Ověřování.

2. Po dokončení nezapomeňte nastavení uložit tak, že zvolíte **OK**.

Nyní je vaše smlouva připravena zpracovávat odchozí zprávy, které odpovídají vybraným nastavením.

### <a name="identifiers"></a>Identifikátory

![Nastavení vlastností identifikátoru](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Vlastnost | Popis |
| --- | --- |
| Kvalifikátor autorizace (ISA1) |V rozevíracím seznamu vyberte hodnotu kvalifikátoru autorizace. |
| ISA2 |Zadejte hodnotu Informace o autorizaci. Pokud je tato hodnota jiná než 00, zadejte minimálně jeden alfanumerický znak a maximálně 10. |
| Kvalifikátor zabezpečení (ISA3) |V rozevíracím seznamu vyberte hodnotu kvalifikátoru zabezpečení. |
| ISA4 |Zadejte hodnotu Informace o zabezpečení. Pokud je tato hodnota jiná než 00, zadejte do textového pole Hodnota (ISA4) minimálně jednu alfanumerickou hodnotu a maximálně 10. |

### <a name="acknowledgment"></a>Potvrzení

![Nastavit vlastnosti potvrzení](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Vlastnost | Popis |
| --- | --- |
| TA1 očekáváno |Vraťte odesílateli výměny technické potvrzení (TA1). Toto nastavení určuje, že hostitelský partner, který odesílá zprávu, požaduje potvrzení od partnera hosta ve smlouvě. Tato potvrzení jsou očekávána hostitelským partnerem na základě nastavení příjmu smlouvy. |
| DM bylo očekáváno. |Vraťte odesílateli výměny funkční potvrzení (DM). Vyberte, zda chcete potvrzení 997 nebo 999 na základě verzí schématu, se kterými pracujete. Tato potvrzení jsou očekávána hostitelským partnerem na základě nastavení příjmu smlouvy. |
| Verze DM |Výběr verze DM |

### <a name="schemas"></a>Schémata

![Vyberte schéma, které chcete použít.](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Vlastnost | Popis |
| --- | --- |
| Version |Výběr verze X12 |
| Typ transakce (ST01) |Vyberte typ transakce |
| Schématu |Vyberte schéma, které chcete použít. Schémata jsou umístěna ve vašem účtu integrace. Pokud nejprve vyberete schéma, automaticky nakonfiguruje verzi a typ transakce.  |

> [!NOTE]
> Nakonfigurujte požadované [schéma,](../logic-apps/logic-apps-enterprise-integration-schemas.md) které je odesláno do vašeho [účtu integrace](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Obálky

![Určení oddělovače v sadě transakcí: zvolte Standardní identifikátor nebo Oddělovač opakování.](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Vlastnost | Popis |
| --- | --- |
| Použití ISA11 |Určuje oddělovač, který má být používán v sadě transakcí: <p>Vyberte **Standardní identifikátor,** chcete-li použít tečku (.) pro desítkovou notaci, nikoli desetinnou notaci příchozího dokladu v kanálu příjmu EDI. <p>Vyberte **Oddělovač opakování,** chcete-li určit oddělovač pro opakované výskyty jednoduchého datového prvku nebo opakované datové struktury. Například obvykle se jako oddělovač opakování používá karát (^). Pro schémata HIPAA můžete použít pouze karát. |

### <a name="control-numbers"></a>Kontrolní čísla

![Určení vlastností čísla ovládacího prvku](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Vlastnost | Popis |
| --- | --- |
| Číslo verze ovládacího prvku (ISA12) |Vyberte verzi standardu X12 |
| Indikátor použití (ISA15) |Vyberte kontext výměny.  Hodnoty jsou informace, výrobní data nebo testovací data |
| Schéma |Generuje segmenty GS a ST pro výměnu kódovanou x12, kterou odešle do kanálu odeslání. |
| GS1 |Volitelné, vyberte hodnotu funkčního kódu z rozevíracího seznamu |
| GS2 |Volitelné, odesílatel aplikace |
| GS3 |Volitelné, přijímač aplikace |
| GS4 |Volitelné, vyberte CCYYMMDDnebo YYMMDD |
| GS5 |Volitelné, vyberte HHMM, HHMMSS nebo HHMMSSdd |
| GS7 |Volitelně vyberte hodnotu pro odpovědnou agenturu z rozevíracího seznamu. |
| GS8 |Volitelná verze dokumentu |
| Číslo řízení výměny (ISA13) |Povinné, zadejte rozsah hodnot pro číslo ovládacího prvku výměny. Zadejte číselnou hodnotu s minimem 1 a maximálně 999999999. |
| Kontrolní číslo skupiny (GS06) |Povinné, zadejte rozsah čísel pro číslo ovládacího prvku skupiny. Zadejte číselnou hodnotu s minimem 1 a maximálně 999999999. |
| Kontrolní číslo sady transakcí (ST02) |Povinné, zadejte rozsah čísel pro číslo ovládacího prvku nastavit transakce. Zadejte rozsah číselných hodnot s minimem 1 a maximálně 9999999999. |
| Předpona |Volitelné, určené pro rozsah kontrolních čísel sady transakcí použitých v potvrzení. Zadejte číselnou hodnotu pro dvě prostřední pole a alfanumerickou hodnotu (v případě potřeby) pro pole předpony a přípony. Střední pole jsou povinná a obsahují minimální a maximální hodnoty pro číslo ovládacího prvku. |
| Přípona |Volitelné, určené pro rozsah kontrolních čísel sady transakcí použitých v potvrzení. Zadejte číselnou hodnotu pro dvě prostřední pole a alfanumerickou hodnotu (v případě potřeby) pro pole předpony a přípony. Střední pole jsou povinná a obsahují minimální a maximální hodnoty pro číslo ovládacího prvku. |

### <a name="character-sets-and-separators"></a>Znakové sady a oddělovače

Kromě znakové sady můžete pro každý typ zprávy zadat jinou sadu oddělovačů. Pokud znaková sada není zadána pro dané schéma zprávy, použije se výchozí znaková sada.

![Určení oddělovačů pro typy zpráv](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Vlastnost | Popis |
| --- | --- |
| Znaková sada, která má být použita |Chcete-li ověřit vlastnosti, vyberte znakovou sadu X12. Možnosti jsou Základní, Extended a UTF8. |
| Schéma |V rozevíracím seznamu vyberte schéma. Po dokončení každého řádku se automaticky přidá nový řádek. U vybraného schématu vyberte sadu oddělovačů, kterou chcete použít, na základě níže uvedených popisů oddělovačů. |
| Typ vstupu |V rozevíracím seznamu vyberte typ vstupu. |
| Oddělovač komponent |Chcete-li oddělit složené datové prvky, zadejte jeden znak. |
| Oddělovač prvků dat |Chcete-li oddělit jednoduché datové prvky ve složených datových prvcích, zadejte jeden znak. |
| Náhradní znak |Zadejte náhradní znak používaný k nahrazení všech oddělovacích znaků v datech datové části při generování odchozí zprávy X12. |
| Segment zakončení |Chcete-li označit konec segmentu EDI, zadejte jeden znak. |
| Přípona |Vyberte znak, který se používá s identifikátorem segmentu. Pokud určíte příponu, může být datový prvek segmentu zakončení prázdný. Pokud je zakončení segmentu ponecháno prázdné, musíte určit příponu. |

> [!TIP]
> Chcete-li zadat speciální hodnoty znaků, upravte smlouvu jako JSON a zadejte hodnotu ASCII pro speciální znak.

### <a name="validation"></a>Ověřování

![Nastavení vlastností ověření pro odesílání zpráv](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Po dokončení každého řádku ověření se automaticky přidá další řádek. Pokud nezadáte žádná pravidla, pak ověření používá řádek "Výchozí".

| Vlastnost | Popis |
| --- | --- |
| Typ zprávy |Vyberte typ zprávy EDI. |
| Ověření EDI |Proveďte ověření EDI u datových typů definovaných vlastnostmi EDI schématu, omezeními délky, prázdnými datovými prvky a koncovými oddělovači. |
| Rozšířené ověření |Pokud datový typ není EDI, ověření je na požadavek datového prvku a povolené opakování, výčty a ověření délky datového prvku (min/max). |
| Povolit prokladové/koncové nuly |Zachovejte všechny další úvodní nebo koncové znaky nuly a mezery. Neodstraňujte tyto znaky. |
| Oříznout proklatová/koncové nuly |Odstraňte úvodní nebo koncové nulové znaky. |
| Zásady koncového oddělovače |Vygenerujte koncové oddělovače. <p>Výběrem **možnosti Není povoleno** zakázat koncové oddělovače a oddělovače v odeslané výměně. Pokud má výměna koncové oddělovače a oddělovače, je tato výměna prohlášena za neplatnou. <p>Vyberte **Volitelné,** chcete-li odeslat mimoúrovňové křižovatky s koncovými oddělovači a oddělovači nebo bez nich. <p>Vyberte **Povinné,** pokud odeslaná výměna musí mít koncové oddělovače a oddělovače. |

## <a name="find-your-created-agreement"></a>Vyhledání vytvořené smlouvy

1.  Po dokončení nastavení všech vlastností smlouvy zvolte na stránce **Přidat** **možnost OK,** chcete-li dokončit vytváření smlouvy a vrátit se k účtu integrace.

    Nově přidaná smlouva se nyní zobrazí v seznamu **Smluv.**

2.  Smlouvy můžete také zobrazit v přehledu účtu integrace. V nabídce integračního účtu zvolte **Přehled**a pak vyberte dlaždici **Smlouvy.**

    ![Zvolte dlaždici "Dohody"](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o tomto konektoru, jako jsou akce a omezení, jak je popsáno v souboru Swagger konektoru, naleznete [na referenční stránce konektoru](https://docs.microsoft.com/connectors/x12/). 

> [!NOTE]
> Pro aplikace logiky v [prostředí služby integrace (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), tento konektor je ise označené verze používá [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) místo.

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)