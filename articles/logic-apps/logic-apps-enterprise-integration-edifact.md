---
title: Zprávy EDIFACT pro podnikovou integraci B2B – Azure Logic Apps | Dokumentace Microsoftu
description: Výměna zpráv EDIFACT ve formátu EDI pro podnikovou integraci B2B v Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 2257d2c8-1929-4390-b22c-f96ca8b291bc
ms.date: 07/26/2016
ms.openlocfilehash: 2da672e1f55af1e38ae0a3fa90b7ecb10d2f17c7
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128373"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Výměna zpráv EDIFACT pro podnikovou integraci B2B v Azure Logic Apps sadou Enterprise Integration Pack

Předtím, než pro Azure Logic Apps můžou vyměňovat zprávy EDIFACT, musíte vytvořit smlouvu EDIFACT a uložení této smlouvy v účtu integrace. Tady jsou kroky pro vytvoření smlouvu EDIFACT.

> [!NOTE]
> Tato stránka se vztahuje na funkce EDIFACT pro Azure Logic Apps. Další informace najdete v tématu [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Než začnete

Tady je položky, které budete potřebovat:

* [Účtu pro integraci](logic-apps-enterprise-integration-create-integration-account.md) , který již má definovaný a spojené s předplatným Azure  
* Alespoň dva [partnery](logic-apps-enterprise-integration-partners.md) , která jsou již definovány v účtu integrace

> [!NOTE]
> Když vytvoříte smlouvu, obsah zprávy, které příjmu nebo odesílání do a z partnera musí odpovídat typu smlouvy.

Poté co [vytvořit integrační účet](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a [přidání partnerů](logic-apps-enterprise-integration-partners.md), vytvoříte smlouvu EDIFACT pomocí následujících kroků.

## <a name="create-an-edifact-agreement"></a>Vytvoření smlouvy EDIFACT 

1. Přihlaste se na web [Azure Portal](http://portal.azure.com "Azure Portal"). 

2. V hlavní nabídce Azure zvolte **všechny služby**. Do vyhledávacího pole zadejte "integrace" a pak vyberte **účty pro integraci**.

   ![Vyhledejte svůj účet integrace](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Pokud **všechny služby** nezobrazí, možná budete muset nejprve rozbalte nabídku. V horní nabídce sbalený vyberte **zobrazit textové popisky**.

3. V části **účty pro integraci**, vyberte účet integrace, ve kterém chcete vytvořit smlouvu.

   ![Vyberte místo pro vytvoření smlouvu účtu integrace](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Zvolte **smlouvy**. Pokud nemáte k dispozici dlaždici smlouvy, přidejte nejprve na dlaždici.   

   ![Zvolte dlaždici "smlouvy o"](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. Na stránce smlouvy **přidat**.

   ![Zvolte "Přidat"](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. V části **přidat**, zadejte **název** pro vaši smlouvu. Pro **typ smlouvy**vyberte **EDIFACT**. Vyberte **partner s identitou hostitele**, **identita hostitele**, **partner s identitou hosta**, a **identita hosta** pro vaši smlouvu.

   ![Zadejte podrobnosti o smlouvě](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Vlastnost | Popis |
   | --- | --- |
   | Název |Název smlouvy |
   | Typ smlouvy | By měl být EDIFACT |
   | Partner s identitou hostitele |Smlouvu musí hostitelské i hostující partnera. Partner hostitele představuje organizace, který konfiguruje smlouvy. |
   | Identita hostitele |Identifikátor pro hostitele partnera |
   | Partner s identitou hosta |Smlouvu musí hostitelské i hostující partnera. Partner s identitou hosta představuje organizace, která je podnikající s partnerem hostitele. |
   | Identita hosta |Identifikátor partner s identitou hosta |
   | Nastavení příjmu |Tyto vlastnosti se vztahují na všechny zprávy přijaté službou smlouvu. |
   | Nastavení odesílání |Tyto vlastnosti se vztahují na všechny zprávy odeslané dohodou. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Nakonfigurujte, jak vaše smlouvy popisovače přijatých zpráv

Teď, když jste nastavili vlastnosti smlouvy, můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává příchozí zprávy přijaté od svého partnera prostřednictvím této smlouvy.

1. V části **přidat**vyberte **přijímat nastavení**.
Konfigurovat tyto vlastnosti závislosti na vaší smlouvě s partnerem, který vyměňuje zprávy s vámi. Popisy vlastností naleznete v tématu tabulky v této části.

   **Získat nastavení** uspořádány do těchto částí: identifikátory, potvrzení, schémata, kontrolních čísel, ověření a interní nastavení.

   ![Konfigurace "Obdrží nastavení"](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Až budete hotovi, ujistěte se, že uložte nastavení výběrem **OK**.

Smlouvy o je nyní připravena ke zpracování příchozích zpráv, které v souladu s vámi vybrané nastavení.

### <a name="identifiers"></a>Identifikátory

| Vlastnost | Popis |
| --- | --- |
| UNB6.1 (Referenční heslo příjemce) |Zadejte hodnotu mezi 1 a 14 znaky alfanumerická hodnota. |
| UNB6.2 (Referenční kvalifikátor příjemce) |Zadejte alfanumerická hodnota s minimálně jeden znak a maximálně dva znaky. |

### <a name="acknowledgments"></a>Potvrzení

| Vlastnost | Popis |
| --- | --- |
| Přijetí zprávy (CONTRL) |Zaškrtnutím tohoto políčka se vraťte do odesílatele výměny technické potvrzení (CONTRL). Potvrzení se pošle na základě nastavení odeslání smlouvy odesílatele výměny. |
| Potvrzení (CONTRL) |Zaškrtněte toto políčko, aby vrátit funkční potvrzení (CONTRL) odesílatele výměny potvrzení posílá odesílatele výměny na základě nastavení odeslání smlouvy. |

### <a name="schemas"></a>Schémata

| Vlastnost | Popis |
| --- | --- |
| UNH2.1 (TYP) |Vyberte typ sady transakcí. |
| UNH2.2 (VERZE) |Zadejte číslo verze zprávy. (Minimální, jeden znak a maximálně tři znaky). |
| UNH2.3 (VYDÁNÍ) |Zadejte číslo verze zprávy. (Minimální, jeden znak a maximálně tři znaky). |
| UNH2.5 (KÓD PŘIDRUŽENÉ PŘIŘAZENÉ) |Zadejte kód přiřazené. (Maximálně 6 znaků. Musí být alfanumerické znaky). |
| UNG2.1 (ID ODESÍLATELE APLIKACE) |Zadejte alfanumerická hodnota s minimálně jeden znak a maximálně 35 znaků. |
| UNG2.2 (KVALIFIKÁTOR KÓDU ODESÍLATELE APLIKACE) |Zadejte hodnotu alfanumerické s maximálně 4 znaky. |
| SCHÉMA |Vyberte dříve odeslaný schéma, které chcete použít z přidruženého integračního účtu. |

### <a name="control-numbers"></a>Kontrolní čísla
| Vlastnost | Popis |
| --- | --- |
| Zakázat duplicity kontrolní číslo výměny. |Zablokovat duplicitní výměn, vyberte tuto vlastnost. Pokud vybraný, akce dekódování EDIFACT kontroluje, že kontrolní číslo výměny (UNB5) pro výměnu přijatý neodpovídá kontrolní číslo výměny dříve zpracované. Pokud se zjistí shoda, není zpracován výměna. |
| Kontrolovat duplicitní UNB5 každých (dny) |Pokud jste se rozhodli zakázat duplicitní výměna kontrolních čísel, můžete zadat počet dní, kdy se má provést kontrolu tím, že má hodnotu vhodnou pro toto nastavení. |
| Zakázat duplicity kontrolních čísel skupiny |Blokování výměn s duplicitní skupiny kontrolních čísel (UNG5), vyberte tuto vlastnost. |
| Zakázat duplicity kontrolních čísel sad transakcí |Blokování výměn se duplicitní transakce sada kontrolních čísel (UNH1), vyberte tuto vlastnost. |
| Řídicí číslo potvrzení EDIFACT |K určení čísla referenční dokumentace sady transakcí pro použití v potvrzení, zadejte hodnotu pro předponu, rozsah čísel odkaz a příponu. |

### <a name="validations"></a>Ověřování

Po dokončení každého řádku ověření druhého je automaticky přidán. Pokud nezadáte žádná pravidla, ověřování pomocí "Výchozího" řádku.

| Vlastnost | Popis |
| --- | --- |
| Typ zprávy |Vyberte typ zprávy EDI. |
| Ověřování EDI |Ověřování EDI na typy dat definované schéma na EDI vlastnosti, omezení délky, prázdné datové prvky a koncové oddělovače. |
| Rozšíření ověřování |Pokud datový typ není EDI, ověřit je na požadavek na prvek dat, povolené opakování, výčty a datový element délka ověření (min/max). |
| Povolit úvodní a koncové nuly |Zachovat všechny další úvodní a koncové nuly a mezer. Neodebírat tyto znaky. |
| Oříznout úvodní a koncové nuly |Odebere úvodní a koncové nuly a znaky. |
| Zásady pro koncový oddělovač |Generovat koncové oddělovače. <p>Vyberte **nepovoluje** zakázat koncové oddělovače a oddělovače ve výměně přijaté. Pokud výměna má koncové oddělovače a oddělovače, výměna je deklarován není platný. <p>Vyberte **volitelné** tak, aby přijímal výměn s nebo bez něj koncové oddělovače a oddělovače. <p>Vyberte **povinné** při přijatý výměna musí mít koncové oddělovače a oddělovače. |

### <a name="internal-settings"></a>Interní nastavení

| Vlastnost | Popis |
| --- | --- |
| Pokud jsou povolené koncové oddělovače, vytvořit prázdné značky XML |Zaškrtněte toto políčko, aby odesílatele výměny zahrnout prázdné značky XML pro koncové oddělovače. |
| Rozdělit výměnu jako sady transakcí – pozastavit sady transakcí při chybě|Analyzuje každou transakci nastavit ve výměně do samostatného dokumentu XML použitím odpovídající obálku do sady transakcí. Pozastavit pouze sady transakcí, jejichž ověření se nezdařilo. |
| Rozdělit výměnu jako sady transakcí – pozastavit výměnu při chybě|Analyzuje každou transakci nastavit ve výměně do samostatného dokumentu XML použitím odpovídající obálky. Pozastavit celý výměnu, pokud jeden nebo více sady transakcí v výměna neúspěšné ověření. | 
| Zachovat výměnu – pozastavit sady transakcí při chybě |Výměna ponechá beze změn, vytvoří dokument XML pro celé dávkové výměnu. Pozastavit pouze sady transakcí, které neúspěšné ověření současně zpracovávat všechny ostatní sady transakcí. |
| Zachovat výměnu – pozastavit výměnu při chybě |Výměna ponechá beze změn, vytvoří dokument XML pro celé dávkové výměnu. Pozastavit celý výměnu, pokud jeden nebo více sady transakcí v výměna neúspěšné ověření. |

## <a name="configure-how-your-agreement-sends-messages"></a>Nakonfigurujte, jak vaši smlouvu odesílá zprávy

Můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává odchozí zprávy, které jste odeslali partnerům prostřednictvím této smlouvy.

1.  V části **přidat**vyberte **odeslat nastavení**.
Konfigurovat tyto vlastnosti závislosti na vaší smlouvě se vašeho partnera, který vyměňuje zprávy s vámi. Popisy vlastností naleznete v tématu tabulky v této části.

    **Nastavení odesílání** uspořádány do těchto částí: identifikátory, potvrzení, schémata, obálky, znakové sady a oddělovače, kontrolních čísel a ověření.

    ![Konfigurace "Nastavení odesílání"](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Až budete hotovi, ujistěte se, že uložte nastavení výběrem **OK**.

Nyní je připravená pro zpracování odchozích zpráv, které v souladu s vámi vybrané nastavení smlouvy.

### <a name="identifiers"></a>Identifikátory

| Vlastnost | Popis |
| --- | --- |
| UNB1.2 (verze syntaxe) |Vyberte hodnotu v rozmezí **1** a **4**. |
| UNB2.3 (Adresa zpětného směrování odesílatele) |Zadejte alfanumerická hodnota s minimálně jeden znak a maximálně 14 znaků. |
| UNB3.3 (Adresa zpětného směrování příjemce) |Zadejte alfanumerická hodnota s minimálně jeden znak a maximálně 14 znaků. |
| UNB6.1 (Referenční heslo příjemce) |Zadejte alfanumerická hodnota s minimálně jeden a maximálně 14 znaků. |
| UNB6.2 (Referenční kvalifikátor příjemce) |Zadejte alfanumerická hodnota s minimálně jeden znak a maximálně dva znaky. |
| UNB7 (Referenční ID aplikace) |Zadejte alfanumerická hodnota s minimálně jeden znak a maximálně 14 znaků |

### <a name="acknowledgment"></a>Potvrzení
| Vlastnost | Popis |
| --- | --- |
| Přijetí zprávy (CONTRL) |Toto políčko zaškrtněte, pokud hostované partnerské očekává technické potvrzení (CONTRL). Toto nastavení určuje, že hostované partnera, který odesílá zprávy, požadavků na potvrzení partner s identitou hosta. |
| Potvrzení (CONTRL) |Toto políčko zaškrtněte, pokud hostované partnerské očekává, že k přijetí funkční potvrzení (CONTRL). Toto nastavení určuje, že hostované partnera, který odesílá zprávy, požadavků na potvrzení partner s identitou hosta. |
| Vygenerovat smyčku SG1/SG4 pro přijaté sady transakcí |Pokud jste se rozhodli požádat o funkční potvrzení, vyberte toto políčko, aby generování SG1/BS4 smyček v funkční CONTRL potvrzení pro přijaté sady transakcí. |

### <a name="schemas"></a>Schémata
| Vlastnost | Popis |
| --- | --- |
| UNH2.1 (TYP) |Vyberte typ sady transakcí. |
| UNH2.2 (VERZE) |Zadejte číslo verze zprávy. |
| UNH2.3 (VYDÁNÍ) |Zadejte číslo verze zprávy. |
| SCHÉMA |Vyberte schéma používat. Schémata se nacházejí v účtu integrace. Pro přístup k vaší schémata, nejprve odkaz účtu pro integraci do aplikace logiky. |

### <a name="envelopes"></a>Obálky
| Vlastnost | Popis |
| --- | --- |
| UNB8 (Zpracování kódu priority) |Zadejte hodnotu, která není více než jeden znak abecední. |
| UNB10 (Dohoda o komunikaci) |Zadejte alfanumerická hodnota s minimálně jeden znak a maximálně 40 znaků. |
| UNB11 (Testovací indikátor) |Zaškrtněte toto políčko, že je výměna vygenerovat testovací data |
| Použít segment UNA (Nápověda k řetězci služby) |Zaškrtnutím tohoto políčka Generovat segment UNA pro výměnu k odeslání. |
| Použít segmenty UNG (Hlavička skupiny funkce) |Zaškrtnutím tohoto políčka pro vytváření segmentů seskupení v záhlaví funkční skupiny v zprávy odeslané do partner s identitou hosta. Chcete-li vytvořit segmenty UNG se používají tyto hodnoty: <p>Pro **UNG1**, zadejte alfanumerická hodnota s minimálně jeden znak a maximálně 6 znaků. <p>Pro **UNG2.1**, zadejte alfanumerická hodnota s minimálně jeden znak a maximálně 35 znaků. <p>Pro **UNG2.2**, zadejte hodnotu alfanumerické s maximálně 4 znaky. <p>Pro **UNG3.1**, zadejte alfanumerická hodnota s minimálně jeden znak a maximálně 35 znaků. <p>Pro **UNG3.2**, zadejte hodnotu alfanumerické s maximálně 4 znaky. <p>Pro **UNG6**, zadejte alfanumerická hodnota s minimálně jeden a maximálně tři znaky. <p>Pro **UNG7.1**, zadejte alfanumerická hodnota s minimálně jeden znak a maximálně tři znaky. <p>Pro **UNG7.2**, zadejte alfanumerická hodnota s minimálně jeden znak a maximálně tři znaky. <p>Pro **UNG7.3**, zadejte alfanumerická hodnota s minimálně 1 znak a maximálně 6 znaků. <p>Pro **UNG8**, zadejte alfanumerická hodnota s minimálně jeden znak a maximálně 14 znaků. |

### <a name="character-sets-and-separators"></a>Znakové sady a oddělovače

Jiné než znakové sady, můžete zadat jinou sadu oddělovačů použít u každého typu zprávy. Pokud pro danou zprávu schématu není zadána znaková sada, použije se výchozí znakovou sadu.

| Vlastnost | Popis |
| --- | --- |
| UNB1.1 (Identifikátor systému) |Vyberte EDIFACT znakové sady má být použita na odchozí výměny. |
| Schéma |Vyberte schéma z rozevíracího seznamu. Po dokončení každého řádku, se automaticky přidá nový řádek. Pro vybrané schéma vyberte sadu oddělovače, který chcete použít, podle oddělovače níže. |
| Typ vstupu |Z rozevíracího seznamu vyberte typem vstupu. |
| Oddělovač komponent |K oddělení složených datových prvků, zadejte jeden znak. |
| Oddělovač datových prvků |K oddělení jednoduché datové prvky v rámci složených datových prvků, zadejte jeden znak. |
| Ukončovací znak segmentu |Označuje konec segmentu EDI, zadejte jeden znak. |
| Přípona |Vyberte znak, který se používá s identifikátorem segmentu. Pokud určíte příponu, může být prázdný datový element ukončovací znak segmentu. Pokud ukončovací znak segmentu je prázdné, je třeba určit příponu. |

### <a name="control-numbers"></a>Kontrolní čísla
| Vlastnost | Popis |
| --- | --- |
| UNB5 (Výměnné řídicí číslo) |Zadejte předponu, rozsah hodnot pro kontrolní číslo výměny a příponu. Tyto hodnoty slouží ke generování odchozí výměny. Předpona a přípona jsou volitelné, zatímco kontrolní číslo je povinný. Kontrolní číslo je zvýšen pro každé nové zprávy. Předpona a přípona zůstávají stejné. |
| UNG5 (Řídicí číslo skupiny) |Zadejte předponu, rozsah hodnot pro kontrolní číslo výměny a příponu. Tyto hodnoty slouží ke generování kontrolní číslo skupiny. Předpona a přípona jsou volitelné, zatímco kontrolní číslo je povinný. Kontrolní číslo je zvýšen pro každou novou zprávu, dokud nebude dosaženo maximální hodnoty; Předpona a přípona zůstávají stejné. |
| UNH1 (Referenční číslo hlavičky zprávy) |Zadejte předponu, rozsah hodnot pro kontrolní číslo výměny a příponu. Tyto hodnoty slouží ke generování referenční číslo hlavičky zprávy. Předpona a přípona jsou volitelné, zatímco referenční číslo je povinný. Referenční číslo je zvýšen pro každé nové zprávy. Předpona a přípona zůstávají stejné. |

### <a name="validations"></a>Ověřování

Po dokončení každého řádku ověření druhého je automaticky přidán. Pokud nezadáte žádná pravidla, ověřování pomocí "Výchozího" řádku.

| Vlastnost | Popis |
| --- | --- |
| Typ zprávy |Vyberte typ zprávy EDI. |
| Ověřování EDI |Ověřování EDI na datové typy, jak je definováno ve vlastnosti EDI schématu, omezení délky, prázdných datových prvků a koncové oddělovače. |
| Rozšíření ověřování |Pokud datový typ není EDI, ověřit je na požadavek na prvek dat, povolené opakování, výčty a datový element délka ověření (min/max). |
| Povolit úvodní a koncové nuly |Zachovat všechny další úvodní a koncové nuly a mezer. Neodebírat tyto znaky. |
| Oříznout úvodní a koncové nuly |Odeberte úvodní a koncové nulové znaky. |
| Zásady pro koncový oddělovač |Generovat koncové oddělovače. <p>Vyberte **nepovoluje** zakázat koncové oddělovače a oddělovače v odeslané výměny. Pokud výměna má koncové oddělovače a oddělovače, výměna je deklarován není platný. <p>Vyberte **volitelné** odesílat výměn s nebo bez něj koncové oddělovače a oddělovače. <p>Vyberte **povinné** Pokud odeslané výměna musí mít koncové oddělovače a oddělovače. |

## <a name="find-your-created-agreement"></a>Najít vytvořený smlouvy

1.  Po dokončení nastavení na všechny vlastnosti vaší smlouvy **přidat** zvolte **OK** dokončit vytváření vaší smlouvě a vrátit ke svému účtu integrace.

    Smlouvy nově přidané se zobrazí ve vašich **smlouvy** seznamu.

2.  Můžete také zobrazit vaše smlouvy v přehled vašeho účtu integrace. V nabídce účtu integrace, zvolte **přehled**a pak **smlouvy** dlaždici. 

    ![Zvolte dlaždici "smlouvy o"](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="view-swagger-file"></a>Zobrazit soubor Swagger
Chcete-li zobrazit podrobnosti Swagger pro konektor EDIFACT, naleznete v tématu [EDIFACT](/connectors/edifact/).

## <a name="learn-more"></a>Další informace
* [Další informace o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "přečtěte si víc o Enterprise Integration Pack")  

