---
title: Zprávy X12 pro integraci B2B
description: Zprávy Exchange X12 ve formátu EDI pro integraci B2B Enterprise v Azure Logic Apps s Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/31/2017
ms.openlocfilehash: 12a1cd3c170fd7444362d1eabba1541cefb37d1a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115546"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Zprávy Exchange X12 pro integraci B2B Enterprise v Azure Logic Apps s využitím Enterprise Integration Pack

Než budete moct X12 zprávy pro Azure Logic Apps, musíte si vytvořit X12 smlouvu a uložit ji do svého účtu integrace. Tady je postup, jak vytvořit smlouvu X12.

> [!NOTE]
> Tato stránka obsahuje funkce X12 pro Azure Logic Apps. Další informace najdete v tématu [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Než začnete

Tady jsou položky, které potřebujete:

* [Účet pro integraci](logic-apps-enterprise-integration-create-integration-account.md) , který je už definovaný a přidružený k vašemu předplatnému Azure
* Aspoň dva [partneři](../logic-apps/logic-apps-enterprise-integration-partners.md) , kteří jsou definováni v účtu integrace a nakonfigurovali pomocí identifikátoru X12 v rámci **obchodních identit**    
* Požadované [schéma](../logic-apps/logic-apps-enterprise-integration-schemas.md) , které můžete nahrát na účet pro integraci

Až [vytvoříte účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [přidáte partnery](logic-apps-enterprise-integration-partners.md)a máte [schéma](../logic-apps/logic-apps-enterprise-integration-schemas.md) , které chcete použít, můžete vytvořit X12 smlouvu pomocí následujících kroků.

## <a name="create-an-x12-agreement"></a>Vytvoření smlouvy X12

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com "portál Azure"). 

2. V hlavní nabídce Azure vyberte **všechny služby**. 
   Do vyhledávacího pole zadejte "Integration" a pak vyberte **účty pro integraci**.  

   ![Najít účet pro integraci](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Pokud se nezobrazí **všechny služby** , může být nutné nejprve rozbalit nabídku. V horní části sbalené nabídky vyberte možnost **Zobrazit nabídku**.

3. V části **účty pro integraci**vyberte účet pro integraci, na který chcete přidat smlouvu.

   ![Vyberte účet pro integraci, kde se má smlouva vytvořit.](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Vyberte **Přehled**a potom vyberte dlaždici **smlouvy** . 
   Pokud nemáte dlaždici smlouvy, nejdřív přidejte dlaždici. 

   ![Vybrat dlaždici smlouvy](./media/logic-apps-enterprise-integration-x12/agreement-1.png)

5. V části **smlouvy**klikněte na možnost **Přidat**.

   ![Zvolit přidat](./media/logic-apps-enterprise-integration-x12/agreement-2.png)     

6. V části **Přidat**zadejte **název** vaší smlouvy. 
   Jako typ smlouvy vyberte **X12**. 
   Vyberte **hostitele hostitele**, **identitu hostitele**, **partnera hosta**a **identitu hosta** pro vaši smlouvu. 
   Další podrobnosti o vlastnostech najdete v tabulce v tomto kroku.

    ![Zadání podrobností o smlouvě](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Vlastnost | Popis |
    | --- | --- |
    | Název |Název smlouvy |
    | Typ smlouvy | By měl být X12 |
    | Partner hostitele |Smlouva potřebuje hostitele i partnera hosta. Partner hostitele představuje organizaci, která konfiguruje smlouvu. |
    | Hostitelská identita |Identifikátor hostitelského partnera |
    | Partner hosta |Smlouva potřebuje hostitele i partnera hosta. Partner hosta představuje organizaci, která provádí podnikání s hostitelským partnerem. |
    | Identita hosta |Identifikátor hostovaného partnera |
    | Nastavení příjmu |Tyto vlastnosti se vztahují na všechny zprávy přijaté smlouvou. |
    | Nastavení odesílání |Tyto vlastnosti se vztahují na všechny zprávy odesílané smlouvou. |  

   > [!NOTE]
   > Řešení smlouvy X12 závisí na porovnání kvalifikátoru a identifikátoru odesílatele a kvalifikátoru a identifikátoru přijímače definovaného v partnerské a příchozí zprávě. Pokud se tyto hodnoty pro vašeho partnera změní, aktualizujte také smlouvu.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurace způsobu, jakým vaše smlouva zpracovává přijaté zprávy

Teď, když jste nastavili vlastnosti smlouvy, můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává příchozí zprávy přijaté od vašeho partnera prostřednictvím této smlouvy.

1.  V části **Přidat**vyberte **Nastavení příjmu**.
Tyto vlastnosti můžete nakonfigurovat na základě vaší smlouvy s partnerem, který s vámi vyměňuje zprávy. Popis vlastností najdete v tabulkách v této části.

    **Nastavení příjmu** je uspořádáno do těchto částí: identifikátory, potvrzení, schémata, obálky, čísla ovládacích prvků, ověřování a interní nastavení.

2. Až to budete mít, nezapomeňte nastavení uložit tak, že kliknete na **OK**.

Vaše smlouva je teď připravená na zpracování příchozích zpráv, které odpovídají vybraným nastavením.

### <a name="identifiers"></a>Identifikátory

![Nastavit vlastnosti identifikátoru](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Vlastnost | Popis |
| --- | --- |
| ISA1 (kvalifikátor autorizace) |V rozevíracím seznamu vyberte hodnotu kvalifikátor autorizace. |
| ISA2 |Nepovinný parametr. Zadejte hodnotu informace o autorizaci. Pokud je hodnota, kterou jste zadali pro ISA1, jiná než 00, zadejte minimálně jeden alfanumerický znak a maximálně 10. |
| ISA3 (kvalifikátor zabezpečení) |V rozevíracím seznamu vyberte hodnotu kvalifikátoru zabezpečení. |
| ISA4 |Nepovinný parametr. Zadejte hodnotu informace o zabezpečení. Pokud je hodnota, kterou jste zadali pro ISA3, jiná než 00, zadejte minimálně jeden alfanumerický znak a maximálně 10. |

### <a name="acknowledgment"></a>Potvrzení

![Nastavit vlastnosti potvrzení](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Vlastnost | Popis |
| --- | --- |
| Očekáván TA1 |Vrátí technické potvrzení odesílateli výměny dat. |
| Očekává se FA. |Vrátí funkční potvrzení odesílateli výměny. Pak vyberte, jestli chcete potvrzení 997 nebo 999, podle verze schématu. |
| Zahrnout smyčku AK2/IK2 |Povoluje generování smyček AK2 ve funkčních potvrzeních pro přijaté sady transakcí. |

### <a name="schemas"></a>Schémata

Vyberte schéma pro každý typ transakce (ST1) a aplikace odesílatele (GS2 úrovně). Kanál pro příjem rozloží příchozí zprávy porovnáním hodnot pro ST1 a GS2 úrovně v příchozí zprávě s hodnotami, které jste zde nastavili, a schématem příchozí zprávy se schématem, které jste nastavili.

![Vybrat schéma](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Vlastnost | Popis |
| --- | --- |
| Version |Vybrat verzi X12 |
| Typ transakce (ST01) |Vyberte typ transakce. |
| Aplikace odesílatele (GS02) |Výběr aplikace odesílatele |
| Schéma |Vyberte soubor schématu, který chcete použít. Do účtu pro integraci se přidají schémata. |

> [!NOTE]
> Nakonfigurujte požadované [schéma](../logic-apps/logic-apps-enterprise-integration-schemas.md) , které se nahraje na [účet pro integraci](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Obálek

![Určete oddělovač v sadě transakcí: zvolte standardní identifikátor nebo oddělovač opakování.](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Vlastnost | Popis |
| --- | --- |
| Využití ISA11 |Určuje oddělovač, který se má použít v sadě transakcí: <p>Vyberte **standardní identifikátor** pro použití tečky (.) pro Desítkový zápis místo desítkového zápisu příchozího dokumentu v kanálu pro příjem EDI. <p>Vyberte **oddělovač opakování** , chcete-li určit oddělovač pro opakované výskyty jednoduchého datového elementu nebo opakované datové struktury. Například obvykle se jako oddělovač opakování používá kosočtverce (^). Pro schémata HIPAA můžete použít jenom kosočtverce. |

### <a name="control-numbers"></a>Řídicí čísla

![Vyberte, jak se mají zpracovat duplicity kontrolních čísel.](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Vlastnost | Popis |
| --- | --- |
| Zakázat duplicity kontrolního čísla výměny |Zablokuje duplicitní změny. Kontroluje číslo řízení výměny (ISA13) pro přijaté číslo řízení výměny. Pokud se zjistí shoda, kanál pro příjem nezpracovává výměnu. Můžete zadat počet dní, po které se má provést kontrolu, a to zadáním hodnoty pro *kontrolu duplicitních ISA13 každých (dnů)*. |
| Zakázat duplicity kontrolních čísel skupin |Blokové změny s duplicitními čísly řízení skupiny. |
| Zakázat duplicity kontrolního čísla sady transakcí |Blokové změny s duplicitními čísly řízení sady transakcí. |

### <a name="validation"></a>Ověřování

![Nastavení vlastností ověření pro přijaté zprávy](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Po dokončení každého ověřovacího řádku se přidá další automaticky. Pokud nezadáte žádná pravidla, pak ověřování použije řádek default (výchozí).

| Vlastnost | Popis |
| --- | --- |
| Typ zprávy |Vyberte typ zprávy EDI. |
| Ověřování EDI |Proveďte ověření EDI u datových typů, které jsou definovány vlastnostmi EDI schématu, omezením délky, prázdnými datovými prvky a koncovými oddělovači. |
| Rozšířené ověřování |Pokud datový typ není EDI, ověřování je na základě požadavku datového prvku a povoleného opakování, výčtů a ověřování délky datových prvků (min/max). |
| Povolení počátečních a koncových nul |Zachovejte jakékoli další úvodní nebo koncové znaky nula a mezer. Tyto znaky neodstraňujte. |
| Oříznout úvodní a koncové nuly |Odstraní úvodní nebo koncové znaky nula a mezer. |
| Zásady koncového oddělovače |Vygeneruje oddělovače na konci. <p>Pokud chcete zakázat koncové oddělovače a oddělovače v přijatém přenosu, vyberte **Nepovoleno** . V případě, že výměna obsahuje koncové oddělovače a oddělovače, je výměna deklarována jako neplatná. <p>Vyberte možnost **volitelné** , pokud chcete přijmout změny s koncovými oddělovači a oddělovači nebo bez nich. <p>Vyberte možnost **povinná** , pokud musí mít výměna koncové oddělovače a oddělovače. |

### <a name="internal-settings"></a>Interní nastavení

![Vybrat interní nastavení](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Vlastnost | Popis |
| --- | --- |
| Převést implicitní desítkový formát "NN" na základní 10 číselnou hodnotu |Převede číslo EDI zadané ve formátu "NN" na číselnou hodnotu o základu 10. |
| Pokud jsou povolené koncové oddělovače, vytvoří se prázdné značky XML. |Zaškrtněte toto políčko, pokud chcete, aby odesílatel výměny zahrnoval prázdné značky XML pro koncové oddělovače. |
| Rozdělit výměnu jako sady transakcí – pozastavit sady transakcí při chybě|Analyzuje každou transakci nastavenou při výměně do samostatného dokumentu XML použitím příslušné obálky pro sadu transakcí. Pozastavuje jenom transakce, kde se ověření nepovede. |
| Rozdělit výměnu jako sady transakcí – při chybě pozastavit výměnu|Analyzuje každou transakci nastavenou při výměně do samostatného dokumentu XML použitím příslušné obálky. Pozastaví celý výměnu, pokud jedna nebo více transakcí v rámci výměny selže. | 
| Zachovat výměnu – pozastavit sady transakcí při chybě |Ponechá výměnu beze změn, vytvoří dokument XML pro celý vydaný hromadnou výměnu. Pozastavuje pouze sady transakcí, které selžou při ověřování, a přitom pokračuje ve zpracování všech ostatních sad transakcí. |
| Zachovat výměnu – pozastavit výměnu při chybě |Ponechá výměnu beze změn, vytvoří dokument XML pro celý vydaný hromadnou výměnu. Pozastaví celý výměnu, pokud jedna nebo více transakcí v rámci výměny selže. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurace způsobu, jakým vaše smlouva posílá zprávy

Pomocí této smlouvy můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává odchozí zprávy odeslané vašemu partnerovi.

1.  V části **Přidat**vyberte **Odeslat nastavení**.
Tyto vlastnosti můžete nakonfigurovat na základě vaší smlouvy s vaším partnerem, který vám s vámi vyměňuje zprávy. Popis vlastností najdete v tabulkách v této části.

    **Nastavení odesílání** je uspořádáno do těchto částí: identifikátory, potvrzení, schémata, obálky, znakové sady a oddělovače, řídicí čísla a ověřování.

2. Až to budete mít, nezapomeňte nastavení uložit tak, že kliknete na **OK**.

Vaše smlouva je teď připravená na zpracování odchozích zpráv, které odpovídají vybraným nastavením.

### <a name="identifiers"></a>Identifikátory

![Nastavit vlastnosti identifikátoru](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Vlastnost | Popis |
| --- | --- |
| Kvalifikátor autorizace (ISA1) |V rozevíracím seznamu vyberte hodnotu kvalifikátor autorizace. |
| ISA2 |Zadejte hodnotu informace o autorizaci. Pokud je tato hodnota jiná než 00, zadejte minimálně jeden alfanumerický znak a maximálně 10 znaků. |
| Kvalifikátor zabezpečení (ISA3) |V rozevíracím seznamu vyberte hodnotu kvalifikátoru zabezpečení. |
| ISA4 |Zadejte hodnotu informace o zabezpečení. Pokud je tato hodnota jiná než 00, pro textové pole hodnota (ISA4) zadejte minimálně jednu alfanumerickou hodnotu a maximálně 10. |

### <a name="acknowledgment"></a>Potvrzení

![Nastavit vlastnosti potvrzení](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Vlastnost | Popis |
| --- | --- |
| Očekáván TA1 |Vrátí technické potvrzení (TA1) odesílateli výměny. Toto nastavení určuje, že hostitel hostující partner, který posílá zprávu, požádá o potvrzení od hostovaného partnera v rámci smlouvy. Tato potvrzení jsou očekávána hostitelským partnerem na základě nastavení příjmu smlouvy. |
| Očekává se FA. |Vrátí funkční potvrzení (IM) odesílateli výměny. V závislosti na verzích schématu, se kterými pracujete, vyberte, jestli chcete potvrdit 997 nebo 999. Tato potvrzení jsou očekávána hostitelským partnerem na základě nastavení příjmu smlouvy. |
| Verze im |Vyberte verzi im. |

### <a name="schemas"></a>Schémata

![Vyberte schéma, které se má použít.](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Vlastnost | Popis |
| --- | --- |
| Version |Vybrat verzi X12 |
| Typ transakce (ST01) |Vyberte typ transakce. |
| XSD |Vyberte schéma, které chcete použít. Schémata se nacházejí v účtu pro integraci. Pokud nejprve vyberete schéma, bude automaticky nakonfiguruje typ verze a transakce.  |

> [!NOTE]
> Nakonfigurujte požadované [schéma](../logic-apps/logic-apps-enterprise-integration-schemas.md) , které se nahraje na [účet pro integraci](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Obálek

![Určete oddělovač v sadě transakcí: zvolte standardní identifikátor nebo oddělovač opakování.](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Vlastnost | Popis |
| --- | --- |
| Využití ISA11 |Určuje oddělovač, který se má použít v sadě transakcí: <p>Vyberte **standardní identifikátor** pro použití tečky (.) pro Desítkový zápis místo desítkového zápisu příchozího dokumentu v kanálu pro příjem EDI. <p>Vyberte **oddělovač opakování** , chcete-li určit oddělovač pro opakované výskyty jednoduchého datového elementu nebo opakované datové struktury. Například obvykle se jako oddělovač opakování používá kosočtverce (^). Pro schémata HIPAA můžete použít jenom kosočtverce. |

### <a name="control-numbers"></a>Řídicí čísla

![Určení vlastností řídicího čísla](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Vlastnost | Popis |
| --- | --- |
| Číslo verze ovládacího prvku (ISA12) |Vyberte verzi X12 Standard. |
| Indikátor využití (ISA15) |Vyberte kontext výměny.  Hodnoty jsou informace, produkční data nebo testovací data. |
| Schéma |Vygeneruje segmenty GS a ST pro X12 kódovaný pro kódování, které posílá do kanálu pro odeslání. |
| GS1 |Volitelné, v rozevíracím seznamu vyberte hodnotu pro funkční kód. |
| GS2 úrovně |Volitelné, odesílatel aplikace |
| GS3 úrovně |Volitelné, příjemce aplikace |
| GS4 úrovně |Volitelné, vyberte CCYYMMDD nebo rrmmdd. |
| GS5 |Volitelné, vyberte HHMM, HHMMSS nebo HHMMSSdd |
| GS7 |Volitelné, v rozevíracím seznamu vyberte hodnotu zodpovědného subjektu. |
| GS8 |Volitelná verze dokumentu |
| Kontrolní číslo výměny (ISA13) |Požadováno, zadejte rozsah hodnot pro kontrolní číslo výměny. Zadejte číselnou hodnotu s minimální hodnotou 1 a maximálně 999999999. |
| Řídicí číslo skupiny (GS06) |Požadováno, zadejte rozsah čísel pro kontrolní číslo skupiny. Zadejte číselnou hodnotu s minimální hodnotou 1 a maximálně 999999999. |
| Kontrolní číslo sady transakcí (ST02) |Požadováno, zadejte rozsah čísel pro kontrolní číslo sady transakcí. Zadejte rozsah číselných hodnot s minimální hodnotou 1 a maximálně 999999999. |
| Předpona |Volitelné, určené pro rozsah řídicích čísel sady transakcí použitých v potvrzení. Do polí předpona a přípona zadejte číselnou hodnotu pro střední dvě pole a alfanumerický údaj (Pokud je to potřeba). Prostřední pole jsou povinná a obsahují minimální a maximální hodnoty pro kontrolní číslo. |
| Auditování |Volitelné, určené pro rozsah řídicích čísel sady transakcí použitých v potvrzení. Do polí předpona a přípona zadejte číselnou hodnotu pro střední dvě pole a alfanumerický údaj (Pokud je to potřeba). Prostřední pole jsou povinná a obsahují minimální a maximální hodnoty pro kontrolní číslo. |

### <a name="character-sets-and-separators"></a>Znakové sady a oddělovače

Kromě znakové sady můžete pro každý typ zprávy zadat jinou sadu oddělovačů. Pokud není pro dané schéma zprávy zadána znaková sada, je použita výchozí znaková sada.

![Zadat oddělovače pro typy zpráv](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Vlastnost | Popis |
| --- | --- |
| Znaková sada, která se má použít |Chcete-li ověřit vlastnosti, vyberte znakovou sadu X12. Možnosti jsou základní, rozšířené a UTF8. |
| Schéma |V rozevíracím seznamu vyberte schéma. Po dokončení každého řádku se automaticky přidá nový řádek. Pro vybrané schéma vyberte v závislosti na níže uvedeném oddělovači sadu oddělovačů, kterou chcete použít. |
| Typ vstupu |V rozevíracím seznamu vyberte typ vstupu. |
| Oddělovač komponent |Chcete-li oddělit složené datové prvky, zadejte jeden znak. |
| Oddělovač datových prvků |Chcete-li oddělit jednoduché datové prvky v rámci složených datových elementů, zadejte jeden znak. |
| Znak nahrazení |Zadejte náhradní znak, který se používá k nahrazení všech znaků oddělovače v datech datové části při generování odchozí zprávy X12. |
| Ukončovací znak segmentu |Pro indikaci konce segmentu EDI zadejte jeden znak. |
| Auditování |Vyberte znak, který se používá s identifikátorem segmentu. Pokud určíte příponu, pak může být datový prvek koncového znaku segmentu prázdný. Pokud je ukončovací znak segmentu prázdný, je nutné určit příponu. |

> [!TIP]
> Chcete-li zadat speciální hodnoty znaků, upravte smlouvu jako JSON a zadejte hodnotu ASCII pro speciální znak.

### <a name="validation"></a>Ověřování

![Nastavení vlastností ověřování pro odesílání zpráv](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Po dokončení každého ověřovacího řádku se přidá další automaticky. Pokud nezadáte žádná pravidla, pak ověřování použije řádek default (výchozí).

| Vlastnost | Popis |
| --- | --- |
| Typ zprávy |Vyberte typ zprávy EDI. |
| Ověřování EDI |Proveďte ověření EDI u datových typů, které jsou definovány vlastnostmi EDI schématu, omezením délky, prázdnými datovými prvky a koncovými oddělovači. |
| Rozšířené ověřování |Pokud datový typ není EDI, ověřování je na základě požadavku datového prvku a povoleného opakování, výčtů a ověřování délky datových prvků (min/max). |
| Povolení počátečních a koncových nul |Zachovejte jakékoli další úvodní nebo koncové znaky nula a mezer. Tyto znaky neodstraňujte. |
| Oříznout úvodní a koncové nuly |Odstraní úvodní nebo koncové znaky nula. |
| Zásady koncového oddělovače |Vygeneruje oddělovače na konci. <p>Pokud chcete zakázat koncové oddělovače a oddělovače v odeslaném přenosu, vyberte **Nepovoleno** . V případě, že výměna obsahuje koncové oddělovače a oddělovače, je výměna deklarována jako neplatná. <p>Vyberte možnost **volitelné** , pokud chcete odesílat změny s koncovými oddělovači a oddělovači nebo bez nich. <p>Vyberte možnost **povinná** , pokud je nutné zasílat odesílateli koncové oddělovače a oddělovače. |

## <a name="find-your-created-agreement"></a>Najít vytvořenou smlouvu

1.  Po dokončení nastavení všech vlastností smlouvy na stránce **Přidat** kliknutím na **tlačítko OK** dokončete vytváření smlouvy a vraťte se k účtu pro integraci.

    Nově přidaná smlouva se teď zobrazí v seznamu **smluv** .

2.  Vaše smlouvy si můžete prohlédnout také v přehledu účtu pro integraci. V nabídce účtu pro integraci zvolte **Přehled**a pak vyberte dlaždici **smlouvy** .

    ![Vybrat dlaždici smlouvy](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o této spojnici, jako jsou akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](https://docs.microsoft.com/connectors/x12/). 

> [!NOTE]
> Pro Logic Apps v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Tato verze konektoru ISE-Label používá [omezení zpráv B2B pro ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
