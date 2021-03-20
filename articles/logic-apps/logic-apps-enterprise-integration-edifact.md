---
title: Zprávy EDIFACT pro integraci B2B
description: Zprávy Exchange EDIFACT ve formátu EDI pro integraci B2B Enterprise v Azure Logic Apps s Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: b0df55e59bd519a816c4022f2434edfcd4460780
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006519"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Výměna zpráv EDIFACT pro podnikovou integraci B2B v Azure Logic Apps s využitím rozšíření Enterprise Integration Pack

Než budete moct EDIFACT zprávy pro Azure Logic Apps, musíte si vytvořit EDIFACT smlouvu a uložit ji do svého účtu integrace. Tady je postup, jak vytvořit smlouvu EDIFACT.

> [!NOTE]
> Tato stránka obsahuje funkce EDIFACT pro Azure Logic Apps. Další informace najdete v tématu [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Než začnete

Tady jsou položky, které potřebujete:

* [Účet pro integraci](logic-apps-enterprise-integration-create-integration-account.md) , který je už definovaný a přidružený k vašemu předplatnému Azure  
* Alespoň dva [partneři](logic-apps-enterprise-integration-partners.md) , kteří jsou již definováni v účtu integrace

> [!NOTE]
> Při vytváření smlouvy se musí obsah ve zprávách, které obdržíte nebo odeslat, partnerovi a partnerovi shodovat s typem smlouvy.

Po [Vytvoření účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a [Přidání partnerů](logic-apps-enterprise-integration-partners.md)můžete vytvořit EDIFACT dohodu pomocí následujících kroků.

## <a name="create-an-edifact-agreement"></a>Vytvoření smlouvy EDIFACT 

1. Přihlaste se na [Azure Portal](https://portal.azure.com "portál Azure"). 

2. V hlavní nabídce Azure vyberte **všechny služby**. Do vyhledávacího pole zadejte "Integration" a pak vyberte **účty pro integraci**.

   ![Najít účet pro integraci](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Pokud se nezobrazí **všechny služby** , může být nutné nejprve rozbalit nabídku. V horní části sbalené nabídky vyberte **zobrazit textové popisky**.

3. V části **účty pro integraci** vyberte účet pro integraci, ve kterém chcete vytvořit smlouvu.

   ![Vyberte účet pro integraci, kde se má smlouva vytvořit.](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Vyberte možnost **smlouvy**. Pokud nemáte dlaždici smlouvy, nejdřív přidejte dlaždici.   

   ![Vybrat dlaždici smlouvy](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. Na stránce smlouvy klikněte na tlačítko **Přidat**.

   ![Zvolit přidat](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. V části **Přidat** zadejte **název** vaší smlouvy. Jako **typ smlouvy** vyberte **EDIFACT**. Vyberte **hostitele hostitele**, **identitu hostitele**, **partnera hosta** a **identitu hosta** pro vaši smlouvu.

   ![Zadání podrobností o smlouvě](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Vlastnost | Popis |
   | --- | --- |
   | Název |Název smlouvy |
   | Typ smlouvy | By měl být EDIFACT |
   | Partner hostitele |Smlouva potřebuje hostitele i partnera hosta. Partner hostitele představuje organizaci, která konfiguruje smlouvu. |
   | Hostitelská identita |Identifikátor hostitelského partnera |
   | Partner hosta |Smlouva potřebuje hostitele i partnera hosta. Partner hosta představuje organizaci, která provádí podnikání s hostitelským partnerem. |
   | Identita hosta |Identifikátor hostovaného partnera |
   | Nastavení příjmu |Tyto vlastnosti se vztahují na všechny zprávy přijaté smlouvou. |
   | Nastavení odesílání |Tyto vlastnosti se vztahují na všechny zprávy odesílané smlouvou. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurace způsobu, jakým vaše smlouva zpracovává přijaté zprávy

Teď, když jste nastavili vlastnosti smlouvy, můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává příchozí zprávy přijaté od vašeho partnera prostřednictvím této smlouvy.

> [!IMPORTANT]
> Konektor EDIFACT podporuje pouze znaky UTF-8.
> Pokud výstup obsahuje neočekávané znaky, ověřte, zda zprávy EDIFACT používají znakovou sadu UTF-8.

1. V části **Přidat** vyberte **Nastavení příjmu**.
Tyto vlastnosti můžete nakonfigurovat na základě vaší smlouvy s partnerem, který s vámi vyměňuje zprávy. Popis vlastností najdete v tabulkách v této části.

   **Nastavení příjmu** je uspořádáno do těchto částí: identifikátory, potvrzení, schémata, řídicí čísla, ověřování a interní nastavení.

   ![Konfigurovat nastavení příjmu](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Až to budete mít, nezapomeňte nastavení uložit tak, že kliknete na **OK**.

Vaše smlouva je teď připravená na zpracování příchozích zpráv, které odpovídají vybraným nastavením.

### <a name="identifiers"></a>Identifikátory

| Vlastnost | Popis |
| --- | --- |
| UNB 6.1 (referenční heslo příjemce) |Zadejte alfanumerický údaj o rozsahu od 1 do 14 znaků. |
| UNB 6.2 (kvalifikátor odkazu na příjemce) |Zadejte alfanumerický údaj s minimálně jedním znakem a maximálně dvěma znaky. |

### <a name="acknowledgments"></a>Poděkování

| Vlastnost | Popis |
| --- | --- |
| Přijetí zprávy (CONTRL) |Zaškrtnutím tohoto políčka vrátíte odesilateli výměny technické (CONTRL) potvrzení. Potvrzení se pošle odesílateli služby Interchange na základě nastavení odeslání smlouvy. |
| Potvrzení (CONTRL) |Zaškrtněte toto políčko, pokud chcete vrátit funkční (CONTRL) potvrzení odesilateli výměny, pošle se potvrzení odesílateli výměny dat na základě nastavení odeslání smlouvy. |

### <a name="schemas"></a>Schémata

| Vlastnost | Popis |
| --- | --- |
| UNH 2.1 (TYP) |Vyberte typ sady transakcí. |
| UNH 2.2 (VERZE) |Zadejte číslo verze zprávy. (Minimálně, jeden znak; maximum, tři znaky). |
| UNH 2.3 (VERZE) |Zadejte číslo verze zprávy. (Minimálně, jeden znak; maximum, tři znaky). |
| UNH 2.5 (PŘIDRUŽENÝ PŘIŘAZENÝ KÓD) |Zadejte přiřazený kód. (Maximálně šest znaků. Musí být alfanumerické). |
| UNG 2.1 (ID ODESÍLATELE APLIKACE) |Zadejte alfanumerický údaj s minimálně jedním znakem a maximálně 35 znaků. |
| UNG 2.2 (KVALIFIKÁTOR KÓDU ODESÍLATELE APLIKACE) |Zadejte alfanumerický údaj, který má maximálně čtyři znaky. |
| XSD |Vyberte dříve nahrané schéma, které chcete použít z přidruženého integračního účtu. |

### <a name="control-numbers"></a>Řídicí čísla

| Vlastnost | Popis |
| --- | --- |
| Zakázat duplicity kontrolního čísla výměny |Chcete-li zablokovat duplicitní změny, vyberte tuto vlastnost. Pokud je toto políčko zaškrtnuté, akce deEDIFACT dekódování kontroluje, zda se kontrolní číslo výměny (UNB5) pro přijatý mezibankovní neshoduje s dříve zpracovaným kontrolním číslem výměny. Pokud se zjistí shoda, nebude se výměna zpracovávat. |
| Kontrolovat duplicitní UNB5 každých (dny) |Pokud se rozhodnete zakázat duplicitní řídicí čísla výměny, můžete zadat počet dní, kdy se má kontrola provést, a to tak, že pro toto nastavení udělíte odpovídající hodnotu. |
| Zakázat duplicity kontrolních čísel skupin |Pokud chcete zablokovat změny s duplicitními čísly řízení skupiny (UNG5), vyberte tuto vlastnost. |
| Zakázat duplicity kontrolního čísla sady transakcí |Chcete-li blokovat změny s duplicitními čísly řízení sady transakcí (UNH1), vyberte tuto vlastnost. |
| Kontrolní číslo potvrzení EDIFACT |Chcete-li určit referenční čísla sady transakcí pro použití v potvrzení, zadejte hodnotu předpony, rozsah referenčních čísel a příponu. |

### <a name="validation"></a>Ověřování

Po dokončení každého ověřovacího řádku se přidá další automaticky. Pokud nezadáte žádná pravidla, pak ověřování použije řádek default (výchozí).

| Vlastnost | Popis |
| --- | --- |
| Typ zprávy |Vyberte typ zprávy EDI. |
| Ověřování EDI |Proveďte ověření EDI u datových typů, které jsou definovány vlastnostmi EDI schématu, omezením délky, prázdnými datovými prvky a koncovými oddělovači. |
| Rozšířené ověřování |Pokud datový typ není EDI, ověřování je na základě požadavku datového prvku a povoleného opakování, výčtů a ověřování délky datových prvků (min/max). |
| Povolení počátečních a koncových nul |Zachovejte jakékoli další úvodní nebo koncové znaky nula a mezer. Tyto znaky neodstraňujte. |
| Oříznout úvodní a koncové nuly |Odstraní úvodní nebo koncové znaky nula a mezer. |
| Zásady koncového oddělovače |Vygeneruje oddělovače na konci. <p>Pokud chcete zakázat koncové oddělovače a oddělovače v přijatém přenosu, vyberte **Nepovoleno** . V případě, že výměna obsahuje koncové oddělovače a oddělovače, je výměna deklarována jako neplatná. <p>Vyberte možnost **volitelné** , pokud chcete přijmout změny s koncovými oddělovači a oddělovači nebo bez nich. <p>Vyberte možnost **povinná** , pokud je povolený mezibankovní čas na konci a oddělovače. |

### <a name="internal-settings"></a>Interní nastavení

| Vlastnost | Popis |
| --- | --- |
| Pokud jsou povolené koncové oddělovače, vytvoří se prázdné značky XML. |Zaškrtněte toto políčko, pokud chcete, aby odesílatel výměny zahrnoval prázdné značky XML pro koncové oddělovače. |
| Rozdělit výměnu jako sady transakcí – pozastavit sady transakcí při chybě|Analyzuje každou transakci nastavenou při výměně do samostatného dokumentu XML použitím příslušné obálky pro sadu transakcí. Pozastaví pouze sady transakcí, jejichž ověření se nezdaří. |
| Rozdělit výměnu jako sady transakcí – při chybě pozastavit výměnu|Analyzuje každou transakci nastavenou při výměně do samostatného dokumentu XML použitím příslušné obálky. Pokud jedna nebo více transakcí v rámci výměny selže, pozastavíte celý výměnu. | 
| Zachovat výměnu – pozastavit sady transakcí při chybě |Ponechá výměnu beze změn, vytvoří dokument XML pro celý vydaný hromadnou výměnu. Pozastaví pouze sady transakcí, které selžou při ověřování, a přitom pokračuje ve zpracování všech ostatních sad transakcí. |
| Zachovat výměnu – pozastavit výměnu při chybě |Ponechá výměnu beze změn, vytvoří dokument XML pro celý vydaný hromadnou výměnu. Pokud jedna nebo více transakcí v rámci výměny selže, pozastavíte celý výměnu. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurace způsobu, jakým vaše smlouva posílá zprávy

Můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává odchozí zprávy odeslané vašim partnerům prostřednictvím této smlouvy.

1.  V části **Přidat** vyberte **Odeslat nastavení**.
Tyto vlastnosti můžete nakonfigurovat na základě vaší smlouvy s vaším partnerem, který vám s vámi vyměňuje zprávy. Popis vlastností najdete v tabulkách v této části.

    **Nastavení odesílání** je uspořádáno do těchto částí: identifikátory, potvrzení, schémata, obálky, znakové sady a oddělovače, řídicí čísla a ověření.

    ![Konfigurovat nastavení Odeslat](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Až to budete mít, nezapomeňte nastavení uložit tak, že kliknete na **OK**.

Vaše smlouva je teď připravená na zpracování odchozích zpráv, které odpovídají vybraným nastavením.

### <a name="identifiers"></a>Identifikátory

| Vlastnost | Popis |
| --- | --- |
| UNB 1.2 (verze syntaxe) |Vyberte hodnotu v rozmezí od **1** do **4**. |
| UNB 2.3 (adresa zpětného směrování odesilatele) |Zadejte alfanumerický údaj s minimálně jedním znakem a maximálně 14 znaků. |
| UNB 3.3 (adresa zpětného směrování příjemců) |Zadejte alfanumerický údaj s minimálně jedním znakem a maximálně 14 znaků. |
| UNB 6.1 (referenční heslo příjemce) |Zadejte alfanumerický údaj s minimální hodnotou jednoho a maximálně 14 znaků. |
| UNB 6.2 (kvalifikátor odkazu na příjemce) |Zadejte alfanumerický údaj s minimálně jedním znakem a maximálně dvěma znaky. |
| UNB7 (referenční ID aplikace) |Zadejte alfanumerický údaj s minimálně jedním znakem a maximálně 14 znaků. |

### <a name="acknowledgment"></a>Potvrzení

| Vlastnost | Popis |
| --- | --- |
| Přijetí zprávy (CONTRL) |Zaškrtněte toto políčko, pokud hostovaný partner očekává, že obdrží potvrzení Technical (CONTRL). Toto nastavení určuje, že hostovaný partner, který posílá zprávu, požádá o potvrzení od hostovaného partnera. |
| Potvrzení (CONTRL) |Toto políčko zaškrtněte, pokud hostovaný partner očekává, že bude dostávat funkční (CONTRL) potvrzení. Toto nastavení určuje, že hostovaný partner, který posílá zprávu, požádá o potvrzení od hostovaného partnera. |
| Generovat smyčku sz1/SG4 pro akceptované sady transakcí |Pokud se rozhodnete, že požadujete funkční potvrzení, zaškrtněte toto políčko, pokud chcete vynutit generování smyček sz1/SG4 ve funkčních potvrzeních CONTRL pro přijaté sady transakcí. |

### <a name="schemas"></a>Schémata

| Vlastnost | Popis |
| --- | --- |
| UNH 2.1 (TYP) |Vyberte typ sady transakcí. |
| UNH 2.2 (VERZE) |Zadejte číslo verze zprávy. |
| UNH 2.3 (VERZE) |Zadejte číslo verze zprávy. |
| XSD |Vyberte schéma, které chcete použít. Schémata se nacházejí v účtu pro integraci. Pokud chcete získat přístup ke schématům, nejprve propojte účet pro integraci s vaší aplikací logiky. |

### <a name="envelopes"></a>Obálek

| Vlastnost | Popis |
| --- | --- |
| UNB8 (kód priority zpracování) |Zadejte abecední hodnotu, která není delší než jeden znak. |
| UNB10 (komunikační smlouva) |Zadejte alfanumerický údaj s minimálně jedním znakem a maximálně 40 znaků. |
| UNB11 (indikátor testu) |Zaškrtnutím tohoto políčka označíte, že vygenerovaná výměna je testovací data. |
| Použít segment UNA (avízo řetězce služby) |Zaškrtnutím tohoto políčka vygenerujete UNA segment, který bude odeslán pro výměnu. |
| Použít segmenty UNG (záhlaví skupiny funkcí) |Toto políčko zaškrtněte, pokud chcete vytvořit segmenty seskupení v hlavičce funkční skupiny v zprávách odesílaných hostovanému partnerovi. K vytvoření segmentů UNG se použijí následující hodnoty: <p>V případě **UNG1** zadejte alfanumerickou hodnotu s minimálním počtem jednoho znaku a maximálně šest znaků. <p>V případě **Ung 2.1** zadejte alfanumerickou hodnotu s minimálně jedním znakem a maximálně 35 znaků. <p>Pro **Ung 2.2** zadejte alfanumerickou hodnotu, která má maximálně čtyři znaky. <p>Pro **Ung 3.1** zadejte alfanumerickou hodnotu s minimálně jedním znakem a maximálně 35 znaků. <p>Pro **Ung 3.2** zadejte alfanumerickou hodnotu, která má maximálně čtyři znaky. <p>Pro **UNG6** zadejte alfanumerický údaj s minimálním a maximálním počtem tří znaků. <p>V případě **Ung 7.1** zadejte alfanumerický údaj s minimálním počtem jednoho znaku a maximálně tři znaky. <p>V případě **Ung 7.2** zadejte alfanumerický údaj s minimálním počtem jednoho znaku a maximálně tři znaky. <p>Pro **Ung 7.3** zadejte alfanumerický údaj o minimálním 1 znaku a maximálně 6 znaků. <p>V případě **UNG8** zadejte alfanumerický údaj s minimálním počtem jednoho znaku a maximálně 14 znaků. |

### <a name="character-sets-and-separators"></a>Znakové sady a oddělovače

Kromě znakové sady můžete zadat jinou sadu oddělovačů, které se mají použít pro každý typ zprávy. Pokud není pro dané schéma zprávy zadána znaková sada, je použita výchozí znaková sada.

| Vlastnost | Popis |
| --- | --- |
| UNB 1.1 (identifikátor systému) |Vyberte znakovou sadu EDIFACT, která se má použít pro odchozí výměnu. |
| Schéma |V rozevíracím seznamu vyberte schéma. Po dokončení každého řádku se automaticky přidá nový řádek. Pro vybrané schéma vyberte v závislosti na níže uvedeném oddělovači sadu oddělovačů, kterou chcete použít. |
| Typ vstupu |V rozevíracím seznamu vyberte typ vstupu. |
| Oddělovač komponent |Chcete-li oddělit složené datové prvky, zadejte jeden znak. |
| Oddělovač datových prvků |Chcete-li oddělit jednoduché datové prvky v rámci složených datových elementů, zadejte jeden znak. |
| Ukončovací znak segmentu |Pro indikaci konce segmentu EDI zadejte jeden znak. |
| Auditování |Vyberte znak, který se používá s identifikátorem segmentu. Pokud určíte příponu, pak může být datový prvek koncového znaku segmentu prázdný. Pokud je ukončovací znak segmentu prázdný, je nutné určit příponu. |

### <a name="control-numbers"></a>Řídicí čísla

| Vlastnost | Popis |
| --- | --- |
| UNB5 (řídící číslo výměny) |Zadejte předponu, rozsah hodnot pro kontrolní číslo výměny a příponu. Tyto hodnoty se používají ke generování odchozího výměny. Předpona a přípona jsou volitelné, zatímco číslo kontrolního čísla je povinné. Řídicí číslo se zvyšuje pro každou novou zprávu. Předpona a přípona zůstávají stejné. |
| UNG5 (kontrolní číslo skupiny) |Zadejte předponu, rozsah hodnot pro kontrolní číslo výměny a příponu. Tyto hodnoty se používají ke generování kontrolního čísla skupiny. Předpona a přípona jsou volitelné, zatímco číslo kontrolního čísla je povinné. Řídicí číslo se zvyšuje pro každou novou zprávu, dokud není dosaženo maximální hodnoty; Předpona a přípona zůstávají stejné. |
| UNH1 (referenční číslo záhlaví zprávy) |Zadejte předponu, rozsah hodnot pro kontrolní číslo výměny a příponu. Tyto hodnoty se používají ke generování referenčního čísla záhlaví zprávy. Předpona a přípona jsou volitelné, zatímco číslo odkazu je povinné. Referenční číslo se zvyšuje pro každou novou zprávu. Předpona a přípona zůstávají stejné. |

### <a name="validation"></a>Ověřování

Po dokončení každého ověřovacího řádku se přidá další automaticky. Pokud nezadáte žádná pravidla, pak ověřování použije řádek default (výchozí).

| Vlastnost | Popis |
| --- | --- |
| Typ zprávy |Vyberte typ zprávy EDI. |
| Ověřování EDI |Proveďte ověření EDI u datových typů, jak jsou definovány vlastnostmi EDI schématu, omezení délky, prázdných datových prvků a koncových oddělovačů. |
| Rozšířené ověřování |Pokud datový typ není EDI, ověřování je na základě požadavku datového prvku a povoleného opakování, výčtů a ověřování délky datových prvků (min/max). |
| Povolení počátečních a koncových nul |Zachovejte jakékoli další úvodní nebo koncové znaky nula a mezer. Tyto znaky neodstraňujte. |
| Oříznout úvodní a koncové nuly |Odstraní úvodní nebo koncové znaky nula. |
| Zásady koncového oddělovače |Vygeneruje oddělovače na konci. <p>Pokud chcete zakázat koncové oddělovače a oddělovače v odeslaném přenosu, vyberte **Nepovoleno** . V případě, že výměna obsahuje koncové oddělovače a oddělovače, je výměna deklarována jako neplatná. <p>Vyberte možnost **volitelné** , pokud chcete odesílat změny s koncovými oddělovači a oddělovači nebo bez nich. <p>Vyberte možnost **povinná** , pokud je nutné zasílat odesílateli koncové oddělovače a oddělovače. |

## <a name="find-your-created-agreement"></a>Najít vytvořenou smlouvu

1.  Po dokončení nastavení všech vlastností smlouvy na stránce **Přidat** kliknutím na **tlačítko OK** dokončete vytváření smlouvy a vraťte se k účtu pro integraci.

    Nově přidaná smlouva se teď zobrazí v seznamu **smluv** .

2.  Vaše smlouvy si můžete prohlédnout také v přehledu účtu pro integraci. V nabídce účtu pro integraci zvolte **Přehled** a pak vyberte dlaždici **smlouvy** . 

    ![Snímek obrazovky, který zobrazuje dlaždici smlouvy](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o této spojnici, jako jsou akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/edifact/).

> [!NOTE]
> Pro Logic Apps v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Tato verze konektoru ISE-Label používá [omezení zpráv B2B pro ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
