---
title: Řešení potíží s funkcí Synapse Studio
description: Řešení potíží s Azure synapse Studio
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 7d91001024ad547e52fe48ee30749fee9a4fb4a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98116173"
---
# <a name="azure-synapse-studio-troubleshooting"></a>Řešení potíží s Azure synapse Studio

Tato příručka pro řešení potíží poskytuje pokyny, které poskytují informace o tom, co je potřeba při otevření lístku podpory při potížích s připojením k síti. Se správnými informacemi můžeme pravděpodobně problém vyřešit rychleji.

## <a name="serverless-sql-pool-service-connectivity-issue"></a>Problém s připojením služby fondu SQL bez serveru

### <a name="symptom-1"></a>Příznak 1

Možnost fond SQL bez serveru se v rozevíracím seznamu připojit k zobrazí šedě.

![symptom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Příznak 2

Spuštění dotazu s "fondem SQL bez serveru" vám poskytne chybovou zprávu "vytvoření připojení k serveru" se nezdařilo.

![příznak 2](media/troubleshooting-synapse-studio/symptom2.png)
 

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

> [!NOTE] 
>    Následující kroky pro řešení potíží jsou pro chrom Edge a Chrome. Můžete použít jiné prohlížeče (například FireFox) se stejným postupem řešení potíží, ale okno vývojářský nástroj může mít jiné rozložení než snímky obrazovky v tomto TSG. Pokud je to možné, nepoužívejte pro řešení potíží klasický hraniční postup, protože v určité situaci může zobrazit nepřesné informace.

Otevřete panel diagnostické informace, vyberte tlačítko "Stáhnout diagnostiku". Udržujte stažené informace pro zasílání zpráv o chybách. Místo toho můžete zkopírovat "ID relace" a připojit ho při otevření lístku podpory.

![Diagnostika – informace](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Pokud chcete začít řešit potíže, zkuste operaci, kterou jste provedli v Azure synapse studiu.

- V případě příznaku 1 vyberte tlačítko Aktualizovat napravo od rozevíracího seznamu použít databázi na kartě skript SQL a zkontrolujte, jestli se vám zobrazuje "fond SQL bez serveru".
- V případě příznaku 2 zkuste spustit dotaz znovu, abyste viděli, jestli se úspěšně spustil.

Pokud problém stále existuje, otevřete stisknutím klávesy F12 v prohlížeči Vývojářské nástroje (DevTools).

V okně Vývojářské nástroje přejděte na panel síť. V případě potřeby vyberte na panelu nástrojů v panelu síť tlačítko Vymazat.
Ujistěte se, že je zaškrtnuté políčko Zakázat mezipaměť na panelu síť.

Opakujte operaci, kterou jste provedli v Azure synapse studiu. V seznamu "síť" v "Vývojářské nástroje" mohou být zobrazeny nové položky. Poznamenejte si aktuální systémový čas, který chcete poskytnout v rámci lístku podpory.

![Síťová panel 1](media/troubleshooting-synapse-studio/network-panel.png)

Vyhledejte položku, jejíž sloupec URL odpovídá následujícímu vzoru:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Kde [*A*] je název vašeho pracovního prostoru a "-OnDemand" může být "-sqlod" a kde [*B*] by měl být název databáze, například "Master". Mělo by existovat nejvíce dvou položek se stejnou hodnotou adresy URL, ale s různými hodnotami metod; MOŽNOSTI a POST. Ověřte, zda tyto dvě položky mají ve sloupci Stav hodnotu "200" nebo "20krát", kde "x" může být jakákoli jedna číslice.

Pokud jeden z nich má jinou hodnotu než "20krát" a:

- Stav začíná znakem "(neúspěch)", buď zvětšete sloupec status (stav), nebo umístěte ukazatel myši na stavový text, aby se zobrazil celý text. Při otevření lístku podpory zahrňte text nebo snímek obrazovky.

    ![Stavový text](media/troubleshooting-synapse-studio/status-text.png)

    - Pokud vidíte ERR_NAME_NOT_RESOLVED a pracovní prostor jste vytvořili během 10 minut, počkejte 10 minut a zkuste zjistit, jestli problém stále existuje.
    - Pokud vidíte ERR_INTERNET_DISCONNECTED nebo ERR_NETWORK_CHANGED, může to znamenat, že vaše síťové připojení k počítači má problémy. Ověřte připojení k síti a zkuste operaci zopakovat.
    - Pokud vidíte ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR nebo jiné chybové kódy obsahující "SSL", může to znamenat, že vaše místní konfigurace SSL má problémy, nebo správce sítě zablokoval přístup k serveru fondu SQL bez serveru. Otevřete lístek podpory a v popisu připojte kód chyby.
    - Pokud vidíte ERR_NETWORK_ACCESS_DENIED, může být nutné, abyste se u správce zkontrolovali, jestli vaše místní zásady brány firewall zablokovaly přístup k doméně *. database.windows.net nebo vzdálenému portu 1443.
    - Volitelně zkuste stejnou operaci hned na jiném počítači nebo v síťovém prostředí, abyste si vyzkoušeli problém s konfigurací sítě v počítači.

- Stav je "40x", "50krát" nebo jiné čísla. Chcete-li zobrazit podrobnosti, vyberte položku (y). Měli byste vidět podrobnosti položky vpravo. Vyhledejte část hlavička odpovědi. potom zkontrolujte, zda existuje položka s názvem Access-Control-Allow-Origin. Pokud ano, ověřte, zda má jednu z následujících hodnot:

    - `*` (jedna hvězdička)
    - https://web.azuresynapse.net/ (nebo jinou hodnotu, na které se text v adresním řádku prohlížeče začíná)

Pokud hlavička odpovědi obsahuje jednu z výše uvedených hodnot, znamená to, že by již měly být shromážděny informace o selhání. V případě potřeby můžete otevřít lístek podpory a volitelně připojit snímek obrazovky s podrobnostmi o položce.

Pokud záhlaví nevidíte nebo záhlaví nemá jednu z výše uvedených hodnot, připojte snímek obrazovky s podrobnostmi o položce při otevření lístku.

 
![Podrobnosti položky](media/troubleshooting-synapse-studio/item-details.png)
 
Pokud výše uvedené kroky problém nevyřeší, možná budete muset otevřít lístek podpory. Při odesílání lístku podpory zahrňte na začátku tohoto průvodce "ID relace" nebo "diagnostické informace".

Při nahlášení problému můžete případně pořídit snímek obrazovky s kartou konzola v Vývojářské nástroje a připojit ji k lístku podpory. Posuňte obsah a při zachytávání celé zprávy Vezměte v případě potřeby více snímků obrazovky.

![Konzola nástrojů pro vývojáře](media/troubleshooting-synapse-studio/developer-tool-console.png)

Pokud přidáváte snímky obrazovky, zadejte čas (nebo odhadovaný časový rozsah), kdy jste pořídili snímky obrazovky. Při hledání problému nám pomůže.

Některé prohlížeče podporují zobrazování časových razítek na kartě konzola. V případě chromového okraje/Chromu otevřete dialog "nastavení" v "Vývojářské nástroje" a na kartě Předvolby zrušte "Zobrazit časová razítka".

![nastavení konzoly nástroje pro vývojáře](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![Zobrazit časové razítko](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>Další kroky
Pokud předchozí kroky nepomohly vyřešit váš problém [Vytvoření lístku podpory](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)