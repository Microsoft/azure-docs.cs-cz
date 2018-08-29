---
title: X12 zprávy pro podnikovou integraci B2B – Azure Logic Apps | Dokumentace Microsoftu
description: Výměna X12 zpráv EDI formát pro podnikovou integraci B2B v Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.date: 01/31/2017
ms.openlocfilehash: c4ee56f4ddcccb1fc4ddd84aa1c1b16dea9754d9
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123953"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Výměna X12 zpráv pro podnikovou integraci B2B v Azure Logic Apps sadou Enterprise Integration Pack

Před výměnou X12 zprávy pro Azure Logic Apps, je nutné vytvořit x X12 smlouvy a uložení této smlouvy v účtu integrace. Tady jsou kroky pro vytvoření x X12 smlouvy.

> [!NOTE]
> Tato funkce se vztahuje X12 stránky pro Azure Logic Apps. Další informace najdete v tématu [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Než začnete

Tady je položky, které budete potřebovat:

* [Účtu pro integraci](logic-apps-enterprise-integration-create-integration-account.md) , který již má definovaný a spojené s předplatným Azure
* Alespoň dva [partneři](../logic-apps/logic-apps-enterprise-integration-partners.md) , které jsou definovány v účtu integrace a nakonfigurovanou X12 identifikátor v části **obchodní identity**    
* Požadovaný [schématu](../logic-apps/logic-apps-enterprise-integration-schemas.md) , který nahrajete do účtu pro integraci

Poté co [vytvořit integrační účet](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [přidání partnerů](logic-apps-enterprise-integration-partners.md)a mít [schématu](../logic-apps/logic-apps-enterprise-integration-schemas.md) , kterou chcete použít, můžete vytvořit x X12 smlouvu pomocí následujících kroků.

## <a name="create-an-x12-agreement"></a>Vytvoření x X12 smlouvy

1. Přihlaste se na web [Azure Portal](http://portal.azure.com "Azure Portal"). 

2. V hlavní nabídce Azure zvolte **všechny služby**. Do vyhledávacího pole zadejte "integrace" a pak vyberte **účty pro integraci**.  

   ![Vyhledejte svůj účet integrace](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Pokud **všechny služby** nezobrazí, možná budete muset nejprve rozbalte nabídku. V horní nabídce sbalený vyberte **nabídky Zobrazit**.

3. V části **účty pro integraci**, vyberte účet integrace, ve které chcete přidat smlouvu.

   ![Vyberte místo pro vytvoření smlouvu účtu integrace](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Vyberte **přehled**a pak **smlouvy** dlaždici. Pokud nemáte k dispozici dlaždici smlouvy, přidejte nejprve na dlaždici. 

   ![Zvolte dlaždici "smlouvy o"](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. V části **smlouvy**, zvolte **přidat**.

   ![Zvolte "Přidat"](./media/logic-apps-enterprise-integration-as2/agreement-2.png)     

6. V části **přidat**, zadejte **název** pro vaši smlouvu. Typ smlouvy, vyberte **X12**. Vyberte **partner s identitou hostitele**, **identita hostitele**, **partner s identitou hosta**, a **identita hosta** pro vaši smlouvu. Vlastnost podrobnosti najdete v tabulce v tomto kroku.

    ![Zadejte podrobnosti o smlouvě](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Vlastnost | Popis |
    | --- | --- |
    | Název |Název smlouvy |
    | Typ smlouvy | By měl být X12 |
    | Partner s identitou hostitele |Smlouvu musí hostitelské i hostující partnera. Partner hostitele představuje organizace, který konfiguruje smlouvy. |
    | Identita hostitele |Identifikátor pro hostitele partnera |
    | Partner s identitou hosta |Smlouvu musí hostitelské i hostující partnera. Partner s identitou hosta představuje organizace, která je podnikající s partnerem hostitele. |
    | Identita hosta |Identifikátor partner s identitou hosta |
    | Nastavení příjmu |Tyto vlastnosti se vztahují na všechny zprávy přijaté službou smlouvu. |
    | Nastavení odesílání |Tyto vlastnosti se vztahují na všechny zprávy odeslané dohodou. |  

  > [!NOTE]
  > Rozlišení X12 smlouvy závisí na odpovídající kvalifikátor odesílatele a identifikátor a kvalifikátoru pro příjemce a identifikátor definovaný v partnera a příchozí zprávy. Pokud tyto hodnoty změnit svého partnera, aktualizujte příliš smlouvy.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Nakonfigurujte, jak vaše smlouvy popisovače přijatých zpráv

Teď, když jste nastavili vlastnosti smlouvy, můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává příchozí zprávy přijaté od svého partnera prostřednictvím této smlouvy.

1.  V části **přidat**vyberte **přijímat nastavení**.
Konfigurovat tyto vlastnosti závislosti na vaší smlouvě s partnerem, který vyměňuje zprávy s vámi. Popisy vlastností naleznete v tématu tabulky v této části.

    **Získat nastavení** uspořádány do těchto částí: identifikátory, potvrzení, schémata, obálky, kontrolních čísel, ověření a interní nastavení.

2. Až budete hotovi, ujistěte se, že uložte nastavení výběrem **OK**.

Smlouvy o je nyní připravena ke zpracování příchozích zpráv, které v souladu s vámi vybrané nastavení.

### <a name="identifiers"></a>Identifikátory

![Nastavit vlastnosti identifikátoru](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Vlastnost | Popis |
| --- | --- |
| ISA1 (Kvalifikátor autorizace) |Z rozevíracího seznamu vyberte hodnotu kvalifikátor autorizace. |
| ISA2 |Volitelné. Zadejte informace o autorizaci. Pokud je hodnota, kterou jste zadali pro ISA1 než 00, zadejte alespoň jeden alfanumerický znak a maximálně 10. |
| ISA3 (Kvalifikátor zabezpečení) |Z rozevíracího seznamu vyberte hodnotu kvalifikátor zabezpečení. |
| ISA4 |Volitelné. Zadejte hodnotu informace o zabezpečení. Pokud je hodnota, kterou jste zadali pro ISA3 než 00, zadejte alespoň jeden alfanumerický znak a maximálně 10. |

### <a name="acknowledgment"></a>Potvrzení

![Nastavit vlastnosti potvrzení](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Vlastnost | Popis |
| --- | --- |
| Očekává se TA1 |Vrátí technické potvrzení odesílatele výměny |
| Očekává se FA |Vrátí funkční potvrzení odesílatele výměny. Potom vyberte, jestli chcete potvrzování 997 nebo 999 založené na verzi schématu |
| Zahrnout smyčku AK2/IK2 smyčky |Umožňuje generování smyčku AK2 smyček v funkční potvrzení pro přijaté sady transakcí |

### <a name="schemas"></a>Schémata

Vyberte schéma pro každý typ transakce (ST1) a u aplikace odesílatele (GS2). Kanál receive zpětně přeloží příchozí zpráva porovnáním hodnot pro ST1 a GS2 v příchozí zprávě pomocí hodnoty, které tady nastavíte a schéma příchozí zprávy se schématem, že se že tady nastavíte.

![Vyberte schéma](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Vlastnost | Popis |
| --- | --- |
| Verze |Vyberte X12 verze |
| Typ transakce (ST01) |Vyberte typ transakce |
| Aplikace odesílatele (GS02) |Vyberte aplikace odesílatele |
| Schéma |Vyberte soubor schématu, které chcete použít. Schémata jsou přidány do účtu pro integraci. |

> [!NOTE]
> Nakonfigurujte požadované [schématu](../logic-apps/logic-apps-enterprise-integration-schemas.md) , který se nahraje do vaší [účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Obálky

![Zadejte oddělovač sady transakcí: Zvolte Standardní identifikátor nebo oddělovač opakování](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Vlastnost | Popis |
| --- | --- |
| Využití ISA11 |Určuje oddělovač, který chcete použít v sadě transakcí: <p>Vyberte **standardní identifikátor** pro účely desítkovém zápisu tečkou (.), namísto desítkový zápis příchozí dokumentu v EDI přijímat kanálu. <p>Vyberte **oddělovač opakování** zadat oddělovač pro opakované výskyty jednoduchý datový prvek nebo opakované datovou strukturu. Obvykle se například stříšky (^) používá jako oddělovač opakování. HIPAA schémat můžete použít pouze ikonu kosočtverce. |

### <a name="control-numbers"></a>Kontrolní čísla

![Vyberte způsob zpracování duplicity kontrolních čísel](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Vlastnost | Popis |
| --- | --- |
| Zakázat duplicity kontrolní číslo výměny. |Zablokovat duplicitní výměn. Kontrolní číslo výměny (ISA13) vyhledá kontrolní číslo výměny přijaté. Pokud se zjistí shoda, nebude zpracování příjmu kanálu výměna. Můžete zadat počet dní pro provedení kontroly tím, že hodnota pro *zkontrolovat duplicity ISA13 každých (dny)*. |
| Zakázat duplicity kontrolních čísel skupiny |Blok výměn s duplicitní skupiny kontrolních čísel. |
| Zakázat duplicity kontrolních čísel sad transakcí |Blok výměn se duplicitní transakce sada kontrolních čísel. |

### <a name="validations"></a>Ověřování

![Nastavení vlastností ověřování pro přijaté zprávy](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Po dokončení každého řádku ověření druhého je automaticky přidán. Pokud nezadáte žádná pravidla, ověřování pomocí "Výchozího" řádku.

| Vlastnost | Popis |
| --- | --- |
| Typ zprávy |Vyberte typ zprávy EDI. |
| Ověřování EDI |Ověřování EDI na typy dat definované schéma na EDI vlastnosti, omezení délky, prázdné datové prvky a koncové oddělovače. |
| Rozšíření ověřování |Pokud datový typ není EDI, ověřit je na požadavek na prvek dat, povolené opakování, výčty a datový element délka ověření (min/max). |
| Povolit úvodní a koncové nuly |Zachovat všechny další úvodní a koncové nuly a mezer. Neodebírat tyto znaky. |
| Oříznout úvodní a koncové nuly |Odebere úvodní a koncové nuly a znaky. |
| Zásady pro koncový oddělovač |Generovat koncové oddělovače. <p>Vyberte **nepovoluje** zakázat koncové oddělovače a oddělovače ve výměně přijaté. Pokud výměna má koncové oddělovače a oddělovače, výměna je deklarován není platný. <p>Vyberte **volitelné** tak, aby přijímal výměn s nebo bez něj koncové oddělovače a oddělovače. <p>Vyberte **povinné** při výměna musí mít koncové oddělovače a oddělovače. |

### <a name="internal-settings"></a>Interní nastavení

![Vybrat interní nastavení](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Vlastnost | Popis |
| --- | --- |
| Převést implicitní desítkový formát "Nn" základní 10 číselnou hodnotu |Převede číslo EDI, který je zadaný ve formátu "Nn" na číselnou hodnotu základu 10 |
| Pokud jsou povolené koncové oddělovače, vytvořit prázdné značky XML |Zaškrtněte toto políčko, aby odesílatele výměny zahrnout prázdné značky XML pro koncové oddělovače. |
| Rozdělit výměnu jako sady transakcí – pozastavit sady transakcí při chybě|Analyzuje každou transakci nastavit ve výměně do samostatného dokumentu XML použitím odpovídající obálku do sady transakcí. Pozastaví jenom transakce, pokud ověření selže. |
| Rozdělit výměnu jako sady transakcí – pozastavit výměnu při chybě|Analyzuje každou transakci nastavit ve výměně do samostatného dokumentu XML použitím odpovídající obálky. Pozastaví celé výměně, když jeden nebo více sady transakcí v výměna neúspěšné ověření. | 
| Zachovat výměnu – pozastavit sady transakcí při chybě |Výměna ponechá beze změn, vytvoří dokument XML pro celé dávkové výměnu. Pozastaví jenom sady transakcí, které nesplní ověřování současně zpracovávat všechny ostatní sady transakcí. |
| Zachovat výměnu – pozastavit výměnu při chybě |Výměna ponechá beze změn, vytvoří dokument XML pro celé dávkové výměnu. Celé výměně pozastaví, když jeden nebo více sady transakcí v výměna dojde k selhání ověřování. |

## <a name="configure-how-your-agreement-sends-messages"></a>Nakonfigurujte, jak vaši smlouvu odesílá zprávy

Můžete nakonfigurovat, jak tato smlouva identifikuje a zpracovává odchozí zprávy, které odešlete váš partner prostřednictvím této smlouvy.

1.  V části **přidat**vyberte **odeslat nastavení**.
Konfigurovat tyto vlastnosti závislosti na vaší smlouvě se vašeho partnera, který vyměňuje zprávy s vámi. Popisy vlastností naleznete v tématu tabulky v této části.

    **Nastavení odesílání** uspořádány do těchto částí: identifikátory, potvrzení, schémata, obálky, znakové sady a oddělovače, kontrolních čísel a ověření.

2. Až budete hotovi, ujistěte se, že uložte nastavení výběrem **OK**.

Nyní je připravená pro zpracování odchozích zpráv, které v souladu s vámi vybrané nastavení smlouvy.

### <a name="identifiers"></a>Identifikátory

![Nastavit vlastnosti identifikátoru](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Vlastnost | Popis |
| --- | --- |
| Kvalifikátor autorizace (ISA1) |Z rozevíracího seznamu vyberte hodnotu kvalifikátor autorizace. |
| ISA2 |Zadejte informace o autorizaci. Pokud je tato hodnota než 00, pak zadejte minimálně jeden alfanumerický znak a maximálně 10. |
| Kvalifikátor zabezpečení (ISA3) |Z rozevíracího seznamu vyberte hodnotu kvalifikátor zabezpečení. |
| ISA4 |Zadejte hodnotu informace o zabezpečení. Pokud je tato hodnota než 00 pro hodnotu (ISA4) textového pole pak zadejte minimálně jednu hodnotu alfanumerické znaky a maximálně 10. |

### <a name="acknowledgment"></a>Potvrzení

![Nastavit vlastnosti potvrzení](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Vlastnost | Popis |
| --- | --- |
| Očekává se TA1 |Technické potvrzení (TA1) vrátíte odesílatele výměny. Toto nastavení určuje, že požadavky hostitele partnera, který odesílá zprávy potvrzení od partnera hostovaného ve smlouvě. Tato potvrzení se očekává partner hostitele na základě nastavení přijímat smlouvy. |
| Očekává se FA |Funkční potvrzení (IM) vrátíte odesílatele výměny. Vyberte, jestli chcete 997 nebo 999 potvrzení, na základě verze schématu, které pracujete. Tato potvrzení se očekává partner hostitele na základě nastavení přijímat smlouvy. |
| Verze FA |Vyberte verzi DM |

### <a name="schemas"></a>Schémata

![Výběr schématu se má použít](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Vlastnost | Popis |
| --- | --- |
| Verze |Vyberte X12 verze |
| Typ transakce (ST01) |Vyberte typ transakce |
| SCHÉMA |Vyberte schéma používat. Schémata se nacházejí v účtu integrace. Pokud nejprve vyberte schéma automaticky konfiguruje verze a transakce typu  |

> [!NOTE]
> Nakonfigurujte požadované [schématu](../logic-apps/logic-apps-enterprise-integration-schemas.md) , který se nahraje do vaší [účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Obálky

![Zadejte oddělovač sady transakcí: Zvolte Standardní identifikátor nebo oddělovač opakování](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Vlastnost | Popis |
| --- | --- |
| Využití ISA11 |Určuje oddělovač, který chcete použít v sadě transakcí: <p>Vyberte **standardní identifikátor** pro účely desítkovém zápisu tečkou (.), namísto desítkový zápis příchozí dokumentu v EDI přijímat kanálu. <p>Vyberte **oddělovač opakování** zadat oddělovač pro opakované výskyty jednoduchý datový prvek nebo opakované datovou strukturu. Obvykle se například stříšky (^) používá jako oddělovač opakování. HIPAA schémat můžete použít pouze ikonu kosočtverce. |

### <a name="control-numbers"></a>Kontrolní čísla

![Zadejte vlastnosti pro kontrolní číslo](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Vlastnost | Popis |
| --- | --- |
| Kontrolní číslo verze (ISA12) |Vyberte verzi X12 standard |
| Indikátor využití (ISA15) |Vyberte místní výměny.  Hodnoty jsou informace, produkčních dat nebo testovací data |
| Schéma |Generuje GS a ST segmenty pro výměnu X12 kódování, která odesílá do kanálu odeslat |
| GS1 |Volitelný parametr, vyberte hodnotu pro funkční kód z rozevíracího seznamu |
| GS2 |Volitelné, aplikace odesílatele |
| GS3 |Volitelné, aplikace příjemce |
| GS4 |Volitelné, vyberte CCYYMMDD nebo RRMMDD |
| GS5 |Volitelné, vyberte HHMM, HHMMSS nebo HHMMSSdd |
| GS7 |Volitelný parametr, vyberte hodnotu pro příslušné agentury z rozevíracího seznamu |
| GS8 |Volitelné, verzi dokumentu |
| (ISA13) kontrolní číslo výměny |Vyžaduje, zadejte rozsah hodnot pro kontrolní číslo výměny. Zadejte číselnou hodnotu s minimálně 1 a maximálně 999999999 |
| Kontrolní číslo skupiny (GS06) |Vyžaduje, zadejte rozsah čísel pro kontrolní číslo skupiny. Zadejte číselnou hodnotu s minimálně 1 a maximálně 999999999 |
| Kontrolní číslo sady transakcí (ST02) |Vyžaduje, zadejte rozsah čísel pro transakce nastavit kontrolní číslo. Zadejte rozsah číselných hodnot s minimálně 1 a maximálně 999999999 |
| Předvolba |Volitelné, určené pro rozsah čísla ovládací prvek sady transakcí používaných pro potvrzení. Zadejte číselnou hodnotu pro střední dvě pole a alfanumerická hodnota (v případě potřeby) pro pole předpon a přípon. Střední pole jsou povinné a obsahovat minimální a maximální hodnoty pro kontrolní číslo |
| Přípona |Volitelné, určené pro rozsah čísla ovládací prvek sady transakcí používaných pro potvrzení. Zadejte číselnou hodnotu pro střední dvě pole a alfanumerická hodnota (v případě potřeby) pro pole předpon a přípon. Střední pole jsou povinné a obsahovat minimální a maximální hodnoty pro kontrolní číslo |

### <a name="character-sets-and-separators"></a>Znakové sady a oddělovače

Jiné než znakové sady, můžete zadat jinou sadu oddělovačů pro každý typ zprávy. Pokud pro danou zprávu schématu není zadána znaková sada, použije se výchozí znakovou sadu.

![Zadejte oddělovače pro typy zpráv](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Vlastnost | Popis |
| --- | --- |
| Znakové sady, který se má použít |Ověření vlastnosti, vyberte X12 znakovou sadu. Možnosti jsou Basic, Extended a UTF8. |
| Schéma |Vyberte schéma z rozevíracího seznamu. Po dokončení každého řádku, se automaticky přidá nový řádek. Pro vybrané schéma vyberte sadu oddělovače, který chcete použít, podle oddělovače níže. |
| Typ vstupu |Z rozevíracího seznamu vyberte typem vstupu. |
| Oddělovač komponent |K oddělení složených datových prvků, zadejte jeden znak. |
| Oddělovač datových prvků |K oddělení jednoduché datové prvky v rámci složených datových prvků, zadejte jeden znak. |
| Náhradní znak |Zadejte náhradní znak použitý k nahrazení všechny znaky oddělovačů v datové části při generování X12 odchozí zprávy. |
| Ukončovací znak segmentu |Označuje konec segmentu EDI, zadejte jeden znak. |
| Přípona |Vyberte znak, který se používá s identifikátorem segmentu. Pokud určíte příponu, může být prázdný datový element ukončovací znak segmentu. Pokud ukončovací znak segmentu je prázdné, je třeba určit příponu. |

> [!TIP]
> Zadejte hodnoty speciální znak, upravit smlouvu jako dokumenty JSON a zadejte hodnotu ASCII speciální znak.

### <a name="validation"></a>Ověření

![Nastavení vlastností ověřování pro odesílání zpráv](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Po dokončení každého řádku ověření druhého je automaticky přidán. Pokud nezadáte žádná pravidla, ověřování pomocí "Výchozího" řádku.

| Vlastnost | Popis |
| --- | --- |
| Typ zprávy |Vyberte typ zprávy EDI. |
| Ověřování EDI |Ověřování EDI na typy dat definované schéma na EDI vlastnosti, omezení délky, prázdné datové prvky a koncové oddělovače. |
| Rozšíření ověřování |Pokud datový typ není EDI, ověřit je na požadavek na prvek dat, povolené opakování, výčty a datový element délka ověření (min/max). |
| Povolit úvodní a koncové nuly |Zachovat všechny další úvodní a koncové nuly a mezer. Neodebírat tyto znaky. |
| Oříznout úvodní a koncové nuly |Odeberte úvodní a koncové nulové znaky. |
| Zásady pro koncový oddělovač |Generovat koncové oddělovače. <p>Vyberte **nepovoluje** zakázat koncové oddělovače a oddělovače v odeslané výměny. Pokud výměna má koncové oddělovače a oddělovače, výměna je deklarován není platný. <p>Vyberte **volitelné** odesílat výměn s nebo bez něj koncové oddělovače a oddělovače. <p>Vyberte **povinné** Pokud odeslané výměna musí mít koncové oddělovače a oddělovače. |

## <a name="find-your-created-agreement"></a>Najít vytvořený smlouvy

1.  Po dokončení nastavení na všechny vlastnosti vaší smlouvy **přidat** zvolte **OK** dokončit vytváření vaší smlouvě a vrátit ke svému účtu integrace.

    Smlouvy nově přidané se zobrazí ve vašich **smlouvy** seznamu.

2.  Můžete také zobrazit vaše smlouvy v přehled vašeho účtu integrace. V nabídce účtu integrace, zvolte **přehled**a pak **smlouvy** dlaždici.

    ![Zvolte dlaždici "smlouvy o"](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="view-the-swagger"></a>Zobrazení swaggeru
Zobrazit [swagger podrobnosti](/connectors/x12/). 

## <a name="learn-more"></a>Další informace
* [Další informace o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "přečtěte si víc o Enterprise Integration Pack")  

