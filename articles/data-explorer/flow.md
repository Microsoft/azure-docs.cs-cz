---
title: Konektor toku průzkumníka dat Microsoft Azure (preview)
description: Přečtěte si o použití konektoru Microsoft Flow k vytvoření toků automaticky naplánovaných nebo aktivovaných úloh.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 7745888dcaa1324d4a9d956e93d0504c8da8c026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501768"
---
# <a name="microsoft-flow-connector-preview"></a>Konektor Microsoft Flow (náhled)

Konektor toku Průzkumníka dat Azure umožňuje Průzkumníku dat Azure používat [funkce Toku Microsoft Power Automate](https://flow.microsoft.com/) k automatickému spouštění dotazů a příkazů Kusto jako součásti naplánované nebo aktivované úlohy.

Mezi běžné scénáře použití patří:

* Odesílání denních sestav obsahujících tabulky a grafy
* Nastavení oznámení na základě výsledků dotazu
* Plánování řídicích příkazů v clusterech
* Export a import dat mezi Azure Data Explorera a dalšími databázemi 

Další informace naleznete v [příkladech použití konektoru Microsoft Flow](flow-usage.md).

##  <a name="log-in"></a>Přihlášení 

1. Přihlaste se k [programu Microsoft Power Automate](https://flow.microsoft.com/).

1. Při prvním připojení budete vyzváni k přihlášení.

1. Vyberte **Sign in** (Přihlásit se) a zadejte své přihlašovací údaje.

![Dialogové okno Přihlásit se](./media/flow/flow-signin.png)

## <a name="authentication"></a>Ověřování

Můžete se ověřit pomocí pověření uživatele nebo aplikace AAD.

> [!Note]
> Ujistěte se, že vaše aplikace je [aplikace AAD](https://docs.microsoft.com/azure/kusto/management/access-control/how-to-provision-aad-app) a je oprávněna spouštět dotazy v clusteru.

1. Vyberte tři tečky v pravém horním rohu ![konektoru Microsoft Flow: Přidání připojení](./media/flow/flow-addconnection.png)

1. Vyberte **Přidat nové připojení** a pak vyberte Připojit pomocí **instančního objektu**.
![Dialogové okno Přihlásit se](./media/flow/flow-signin.png)

1. Zadejte požadované informace:
    * Název připojení: Popisný a smysluplný název nového připojení
    * ID klienta: ID vaší aplikace
    * Tajný klíč klienta: Váš klíč aplikace
    * Tenant: ID adresáře AAD, ve kterém jste aplikaci vytvořili. Například ID klienta Microsoftu je: 72f988bf-86f1-41af-91ab-2d7cd011db47

![Ověřování aplikací](./media/flow/flow-appauth.png)

Po dokončení ověřování uvidíte, že tok používá nově přidané připojení.

![Dokončené ověřování aplikací](./media/flow/flow-appauthcomplete.png)

Od této chvíle bude tento tok spuštěn pomocí těchto přihlašovacích údajů aplikace.

## <a name="find-the-azure-kusto-connector"></a>Najít konektor Azure Kusto

Chcete-li použít konektor Microsoft Flow, musíte nejprve přidat aktivační událost. Aktivační událost lze definovat na základě opakovaného časového období nebo jako odpověď na předchozí akci toku.

1. [Vytvořte nový tok](https://flow.microsoft.com/manage/flows/new) nebo na domovské stránce vyberte akci **Moje toky** a pak vyberte **+ Nový**.

    ![Vytvoření nového toku](./media/flow/flow-newflow.png)

1. Přidat prázdné naplánované z.

    ![Nový plánovaný tok](./media/flow/flow-scheduled-from-blank.png)

1. Zadejte požadované informace na stránce Sestavení naplánovaného toku.
    ![Vytvoření naplánovaného toku](./media/flow/flow-build-scheduled-flow.png)
1. Vyberte **Vytvořit**.
1. Vyberte **+ Nový krok**.
1. Do vyhledávacího pole zadejte "Kusto".

    ![Vybrat akce toku](./media/flow/flow-actions.png)

1. Vyberte **Průzkumník dat Azure**.

## <a name="flow-actions"></a>Akce toku

Když otevřete konektor Průzkumníka dat Azure, existují tři možné akce, které můžete přidat do toku.

Tato část popisuje možnosti a parametry pro každou akci Microsoft Flow.

![Akce Toku Průzkumníka dat Azure](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>Spuštění příkazu řízení a vizualizace výsledků

Pomocí příkazu Spustit ovládací prvek a vizualizaci výsledků spusťte [příkaz ovládacího prvku](https://docs.microsoft.com/azure/kusto/management/index).

1. Zadejte adresu URL clusteru. Například https://clusterName.eastus.kusto.windows.net.
1. Zadejte název databáze.
1. Zadejte příkaz ovládacího prvku:
    * Výběr dynamického obsahu z aplikací a konektorů používaných v toku
    * Přidání výrazu pro přístup k hodnotám, jejich převod a porovnání
1. Chcete-li výsledky této akce odeslat e-mailem jako tabulku nebo graf, zadejte typ grafu, který může být následující:
    * Tabulka HTML
    * Výsečový graf
    * Časový graf
    * Pruhový graf

![Spustit příkaz řízení toku](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> Do pole *Název clusteru* zadejte adresu URL clusteru.

### <a name="run-query-and-list-results"></a>Spuštění výsledků dotazu a seznamu

> [!Note]
> Pokud dotaz začíná tečkou (což znamená, že se jedná o [řídicí příkaz),](https://docs.microsoft.com/azure/kusto/management/index)použijte [příkaz Spustit ovládací prvek a vizualizovat výsledky](#run-control-command-and-visualize-results)

Tato akce odešle dotaz do clusteru Kusto. Akce, které jsou přidány později iterate přes každý řádek výsledků dotazu.

Následující příklad spustí dotaz každou minutu a odešle e-mail na základě výsledků dotazu. Dotaz zkontroluje počet řádků v databázi a odešle e-mail pouze v případě, že počet řádků je větší než 0. 

![Spustit výsledky seznamu dotazů](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> Pokud sloupec obsahuje několik řádků, spojnice bude spuštěna pro každý řádek ve sloupci.

### <a name="run-query-and-visualize-results"></a>Spuštění dotazu a vizualizace výsledků
        
> [!Note]
> Pokud dotaz začíná tečkou (což znamená, že se jedná o [řídicí příkaz),](https://docs.microsoft.com/azure/kusto/management/index)použijte [příkaz Spustit ovládací prvek a vizualizovat výsledky](#run-control-command-and-visualize-results)
        
Pomocí akce Spustit dotaz a vizualizaci výsledků vizualizujte výsledek kusto dotazu jako tabulku nebo graf. Tento tok můžete například použít k přijímání denních sestav ICM e-mailem. 
    
V tomto příkladu jsou výsledky dotazu vráceny jako tabulka HTML.
            
![Spuštění dotazu a vizualizace výsledků](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> Do pole *Název clusteru* zadejte adresu URL clusteru.

## <a name="email-kusto-query-results"></a>E-mail kusto dotaz výsledky

Můžete zahrnout krok do libovolného toku pro odesílání zpráv e-mailem na libovolnou e-mailovou adresu. 

1. Výběrem **možnosti + Nový krok** přidáte do toku nový krok.
1. Do vyhledávacího pole zadejte Office 365 a vyberte **Office 365 Outlook**.
1. Vyberte **Odeslat e-mail**.
1. Zadejte e-mailovou adresu, na kterou chcete e-mailovou sestavu odeslat.
1. Zadejte předmět e-mailu.
1. V poli *Tělo* vyberte z pole Dynamický obsah **položku Tělo**.
1. Vyberte **Zobrazit pokročilé možnosti**.
1. V poli *Název přílohy -1* vyberte **Název přílohy**.
1. V poli *Obsah příloh* vyberte **Obsah přílohy**.
1. V případě potřeby nastavte úroveň důležitosti.
1. Vyberte **Uložit**.

![Odeslat e-mail](./media/flow/flow-sendemail.png)

## <a name="check-if-your-flow-succeeded"></a>Zkontrolujte, zda byl váš tok úspěšný

Chcete-li zkontrolovat, zda je váš tok úspěšný, podívejte se na historii spuštění toku:
1. Přejděte na [domovskou stránku Microsoft Flow](https://flow.microsoft.com/).
1. V hlavní nabídce vyberte [Možnost Moje toky](https://flow.microsoft.com/manage/flows).
    ![Moje stránka Toky](./media/flow/flow-myflows.png)
1. Na řádku toku, který chcete prozkoumat, vyberte ikonu další příkazy a potom **spusťte historii**.

    ![Spustit nabídku historie](./media/flow//flow-runhistory.png)

    Všechny tokběží jsou uvedeny s časem zahájení, doba trvání a stav.
    ![Spustit stránku s výsledky historie](./media/flow/flow-runhistoryresults.png)

    Podrobné informace o toku na stránce [Moje toky](https://flow.microsoft.com/manage/flows) vyberte tok, který chcete prozkoumat.

    ![Spustit stránku úplných výsledků historie](./media/flow/flow-fulldetails.png) 

Chcete-li zjistit, proč se spuštění nezdařilo, vyberte čas spuštění. Zobrazí se tok a krok toku, který selhal, je označen červeným vykřičníkem. Rozbalte neúspěšný krok a zobrazte jeho podrobnosti. Pravé podokno obsahuje informace o selhání, takže ji můžete řešit.

![Chyba toku](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>Výjimky časového výpadku

Tok může selhat a vrátit výjimku "RequestTimeout", pokud běží déle než sedm minut.

Přečtěte si další informace o [omezeních Microsoft Flow](#limitations).
    
Stejný dotaz může úspěšně spustit v Průzkumníku dat Azure, kde čas není omezen a lze změnit.
            
Výjimka "RequestTimeout" je zobrazena na obrázku níže:
    
![Chyba výjimky časového omezení požadavku toku](./media/flow/flow-requesttimeout.png)
    
Chcete-li vyřešit problém s časovým limitem, pokuste se zefektivnit dotaz tak, aby byl rychlejší, nebo jej oddělte na bloky. Každý blok dat lze spustit v jiné části dotazu.

Další informace naleznete v [doporučených postupech aplikace Query](https://docs.microsoft.com/azure/kusto/query/best-practices).

## <a name="limitations"></a>Omezení

* Výsledky vrácené klientovi jsou omezeny na 500 000 záznamů. Celková paměť pro tyto záznamy nesmí překročit 64 MB a sedm minut spuštění.
* Konektor nepodporuje operátory [vidlicí](https://docs.microsoft.com/azure/kusto/query/forkoperator) a [omezující fazety.](https://docs.microsoft.com/azure/kusto/query/facetoperator)
* Tok funguje nejlépe na Microsoft Edge a Chrome.

## <a name="next-steps"></a>Další kroky

Další informace o [konektoru aplikace Microsoft Azure Explorer Logic App,](https://docs.microsoft.com/azure/kusto/tools/logicapps) což je další způsob, jak spustit kusto dotazy a příkazy automaticky jako součást naplánované nebo aktivované úlohy.
