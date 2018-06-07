---
title: Připojení k Dynamics 365 - Azure Logic Apps | Microsoft Docs
description: Vytvořit a spravovat záznamy s Dynamics 365 (online) rozhraní API REST a Azure Logic Apps
author: Mattp123
manager: cfowler
ms.author: matp
ms.date: 02/10/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 015f587257603330eab9840edf661ba9675f4ba8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34609891"
---
# <a name="connect-to-dynamics-365-from-logic-app-workflows"></a>Připojit k aplikaci Dynamics 365 z pracovní postupy aplikace logiky

S Logic Apps můžete připojit k aplikaci Dynamics 365 (online) a vytvořit toky užitečné firmy, které vytvořit záznamy, aktualizovat položky nebo vrátí seznam záznamů. Tento konektor Dynamics 365 můžete:

* Sestavení vaší firmy toku na základě dat, které můžete získat z Dynamics 365 (online).
* Pomocí akcí, které se odpověď a pak proveďte výstup k dispozici pro další akce. Při aktualizaci položky v Dynamics 365 (online), můžete odeslat e-mailu pomocí Office 365.

Toto téma ukazuje, jak vytvořit aplikaci logiky, která vytvoří úlohu v Dynamics 365 vždy, když nové zájemce je vytvořen v Dynamics 365.

## <a name="prerequisites"></a>Požadavky
* Účet Azure.
* Účet Dynamics 365 (online).

## <a name="create-a-task-when-a-new-lead-is-created-in-dynamics-365"></a>Vytvoření úlohy při vytváření nového zájemce v Dynamics 365

1.  [Přihlaste se k Azure](https://portal.azure.com).

2.  Do pole vyhledávání systému Azure, zadejte `Logic apps`, a stiskněte klávesu ENTER.

      ![Najít Logic Apps](./media/connectors-create-api-crmonline/find-logic-apps.png)

3.  V části **Logic apps**, klikněte na tlačítko **přidat**.

      ![Přidat LogicApp](./media/connectors-create-api-crmonline/add-logic-app.png)

4.  Chcete-li vytvořit aplikaci logiky, proveďte **název**, **předplatné**, **skupiny prostředků**, a **umístění** pole a pak klikněte na tlačítko **vytvořit**.

5.  Vyberte nové aplikace logiky. Když se zobrazí **nasazení bylo úspěšné** oznámení, klikněte na tlačítko **aktualizovat**.

6.  V části **nástroje pro vývoj**, klikněte na tlačítko **návrhář aplikace na základě logiky**. V seznamu šablon klikněte na **prázdné aplikace logiky**.

7.  Do vyhledávacího pole zadejte `Dynamics 365`. Vyberte ze seznamu aktivační události Dynamics 365 **Dynamics 365 – když se vytvoří záznam**.

8.  Pokud se zobrazí výzva k přihlášení k aplikaci Dynamics 365, udělejte to teď.

9.  V podrobnostech aktivační události zadejte následující informace:

  * **Název organizace**. Vyberte, které chcete aplikaci logiky pro naslouchání na instanci Dynamics 365.

  * **Název entity**. Vyberte typ entity, která chcete pro naslouchání na. Tato událost funguje jako aktivační událost a spusťte aplikaci logiky. 
  V tomto návodu **vede** je vybrána.

  * **Jak často chcete zkontrolovat položky?** Tyto hodnoty nastaveny, jak často aplikaci logiky kontrolovat aktualizace související se aktivační událost. Výchozí nastavení je ke kontrole aktualizací každé tři minuty.

    * **Frekvence**. Vyberte sekund, minut, hodin nebo dnů.

    * **Interval**. Zadejte počet sekund, minut, hodin nebo dnů, které chcete předat před další kontroly.

      ![Podrobnosti o logiku aktivační událostí aplikace](./media/connectors-create-api-crmonline/trigger-details.png)

10. Klikněte na tlačítko **nový krok**a potom klikněte na **přidat akci**.

11. Do vyhledávacího pole zadejte `Dynamics 365`. Vyberte ze seznamu akce **Dynamics 365 – vytvořit nový záznam**.

12. Zadejte následující informace:

    * **Název organizace**. Vyberte instanci Dynamics 365 místo postup pro vytvoření záznamu. 
    Všimněte si, že nemusí být na stejnou instanci, kde je aktivována událost z této instance.

    * **Název entity**. Vyberte entity, který chcete vytvořit záznam, když je aktivována událost. 
    V tomto návodu **úlohy** je vybrána.

13. Kliknutím na tlačítko ve **subjektu** pole, které se zobrazí. Dynamické obsahu seznamu, které se zobrazí můžete si vybrat jednu z těchto polí:

    * **Příjmení**. Příjmení zájemce výběru v tomto poli vloží do pole Předmět pro úlohu, když je vytvořen záznam úloh.
    * **Téma**. Výběrem toto pole vloží pole tématu zájemce do poli pro předmět pro úlohy, když je vytvořen záznam úloh. 
    Klikněte na tlačítko **tématu** to přidat **subjektu** pole.

      ![Logiku aplikace vytvoří nové podrobnosti záznamu](./media/connectors-create-api-crmonline/create-record-details.png)

14. Na panelu nástrojů návrháře aplikace logiky, klikněte na tlačítko **Uložit**.

    ![Návrhář aplikace na základě nástrojů Logika uložit](./media/connectors-create-api-crmonline/designer-toolbar-save.png)

15. Chcete-li spustit aplikaci logiky, klikněte na tlačítko **spustit**.

    ![Návrhář aplikace na základě nástrojů Logika uložit](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

16. Teď vytvořte záznam realizace v 365 Dynamics pro prodej a v tématu vaše toku v akci!

## <a name="set-advanced-options-for-a-logic-app-step"></a>Upřesnit možnosti pro krok aplikace logiky

Určete, jak se k filtrování dat v kroku aplikaci logiky, klikněte na tlačítko **zobrazit rozšířené možnosti** v tomto kroku přidáte filtru nebo pořadí dotazem.

Například můžete použít dotaz filter získat pouze aktivní účty a pořadí podle názvu účtu. K provedení této úlohy, zadejte dotaz filtru OData `statuscode eq 1`a vyberte **název účtu** ze seznamu dynamického obsahu. Další informace: [MSDN: $filter](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_1) a [$orderby](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_2).

![Aplikace logiky rozšířené možnosti](./media/connectors-create-api-crmonline/advanced-options.png)

### <a name="best-practices-when-using-advanced-options"></a>Osvědčené postupy při použití rozšířené možnosti

Při přidejte hodnotu do pole, musí odpovídat typ pole, ať už zadejte hodnotu, nebo vyberte hodnotu ze seznamu dynamického obsahu.

Typ pole  |Způsob použití  |Kde najít  |Název  |Typ dat  
---------|---------|---------|---------|---------
Textová pole|Textová pole vyžadují jeden řádek textu nebo dynamický obsah, který je pole typu text. Mezi příklady patří pole kategorie a dílčí kategorie.|Nastavení > Přizpůsobení > Přizpůsobení systému > entity > úlohy > pole |category |Jeden řádek textu        
Pole celé číslo | Některá pole vyžadují celé číslo nebo dynamický obsah, který je pole typu integer. Mezi příklady patří dokončeno % a doby trvání. |Nastavení > Přizpůsobení > Přizpůsobení systému > entity > úlohy > pole |Procento dokončení |Celé číslo         
Datum pole | Některá pole vyžadují datum ve formátu mm/dd/rrrr nebo dynamický obsah, který je pole typu datum. Příklady jsou vytvořené na počáteční datum, skutečné zahájení poslední na dobu uchování, skutečný konec a datum splatnosti. | Nastavení > Přizpůsobení > Přizpůsobení systému > entity > úlohy > pole |createdon |Datum a čas
Zadejte pole, které vyžadují záznamu ID i vyhledávání |Některá pole, které odkazují na jiný záznam entity vyžadují ID záznamu a typ vyhledávání. |Nastavení > Přizpůsobení > Přizpůsobení systému > entity > účet > pole  | ID účtu  | Primární klíč

### <a name="more-examples-of-fields-that-require-both-a-record-id-and-lookup-type"></a>Zadejte další příklady pole, které vyžadují ID záznamu a vyhledávání
Rozšíření v předchozí tabulce, zde jsou další příklady pole, která není pracovat s hodnotami vybraný ze seznamu dynamického obsahu. Místo toho tato pole vyžadují obě záznamu ID a vyhledávací typ zadali do pole v PowerApps.  
* Vlastník a typ vlastníka. V poli vlastník musí být platné ID záznamu uživateli nebo týmu Typ vlastník musí být buď **systemusers** nebo **týmy**.
* Zákazníka a typ odběratele. Pole musí být platný účet nebo se obraťte na ID záznamu. Typ vlastník musí být buď **účty** nebo **kontakty**.
* A typu. Pole týká musí být platný záznam ID, jako je například účet nebo se obraťte na ID záznamu. Typ ohledně musí být typ vyhledávání na záznam, jako například **účty** nebo **kontakty**.

Následující příklad akce vytvoření úloh přidá záznam klienta, která odpovídá na ID záznamu přidáním do pole týkající úlohy.

![Tok recordId a typ účtu](./media/connectors-create-api-crmonline/recordid-type-account.png)

Tento příklad také přiřadí úlohu pro konkrétního uživatele na základě ID uživatele záznamu.

![Tok recordId a typ účtu](./media/connectors-create-api-crmonline/recordid-type-user.png)

ID záznamu najdete v následující části: *najít ID záznamu*

## <a name="find-the-record-id"></a>Najít ID záznamu

1. Otevřete záznam, jako je například záznam klienta.

2. Na panelu nástrojů Akce klikněte na tlačítko **Pop Out** ![záznam zobrazovat](./media/connectors-create-api-crmonline/popout-record.png).
Případně, na panelu nástrojů Akce, pokud chcete zkopírovat úplnou adresu URL do výchozí program e-mailu, kliknutím na tlačítko **odkazu A e-MAILU**.

   ID záznamu se zobrazí mezi % 7b a %7 d kódování znaků adresy URL.

   ![Tok recordId a typ účtu](./media/connectors-create-api-crmonline/recordid.png)

## <a name="troubleshooting"></a>Řešení potíží
Chcete-li vyřešit vadný krok v aplikaci logiky, zobrazte podrobnosti o stavu události.

1. V části **Logic Apps**, vyberte svou aplikaci logiky a pak klikněte na tlačítko **přehled**. 

   V oblasti souhrnu se zobrazí a poskytuje stav spuštění pro aplikaci logiky. 

   ![Stav spuštění aplikace logiky](./media/connectors-create-api-crmonline/tshoot1.png)

2. Chcete-li zobrazit další informace o všech došlo k chybě spuštění, klikněte na možnost neúspěšné události. Chcete-li rozšířit vadný krok, klikněte na tento krok.

   ![Rozbalte vadný krok](./media/connectors-create-api-crmonline/tshoot2.png)

   Podrobnosti krok zobrazí a mohou pomoci při řešení příčiny selhání.

   ![Krok podrobnosti neúspěšné](./media/connectors-create-api-crmonline/tshoot3.png)

Další informace o odstraňování potíží s aplikací logiky najdete v tématu [diagnostikování selhání aplikace logiky](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Zobrazit všechny aktivační události a akce definované v swagger a také zobrazit žádné limity v [connector – podrobnosti](/connectors/crm/). 

## <a name="next-steps"></a>Další postup
Prozkoumejte dalších dostupných konektorů v Logic Apps v našem [rozhraní API seznamu](apis-list.md).
