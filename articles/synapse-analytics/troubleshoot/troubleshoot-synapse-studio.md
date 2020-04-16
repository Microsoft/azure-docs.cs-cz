---
title: Poradce při potížích s Azure Synapse Studio (preview)
description: Poradce při potížích s Azure Synapse Studio
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ede38317762e5aac5562c9bb3494713c3edacc69
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431316"
---
# <a name="azure-synapse-studio-preview-troubleshooting"></a>Řešení potíží s Azure Synapse Studio (preview)

Tento průvodce potížemi poskytuje pokyny, jaké informace mají poskytnout při otevírání lístku podpory o problémech s připojením k síti. S příslušnými informacemi můžeme problém vyřešit rychleji.

## <a name="sql-on-demand-preview-service-connectivity-issue"></a>Problém s připojením služby SQL na vyžádání (preview)

### <a name="symptom-1"></a>Příznak 1

Možnost "SQL on-demand" je šedě v rozevíracím souboru "Připojit k".

![příznak1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Příznak 2

Spuštění dotazu s "SQL na vyžádání" vám zobrazí chybová zpráva "Nepodařilo se navázat připojení k serveru".

![symptom2](media/troubleshooting-synapse-studio/symptom2.png)

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

> [!NOTE] 
>    Následující kroky řešení potíží jsou pro Chromium Edge a Chrome. Můžete použít jiné prohlížeče (například FireFox) se stejnými kroky řešení potíží, ale okno "Developer Tool" může mít jiné rozložení než snímky obrazovky v tomto TSG. Pokud je to možné, NEPOUŽÍVEJTE klasické Edge pro řešení potíží, protože to může zobrazit nepřesné informace v určité situaci.

Otevřete panel "Diagnostické informace", vyberte tlačítko "Stáhnout diagnostiku". Uchovávejte stažené informace pro zasílání zpráv o chybách. Místo toho můžete zkopírovat "ID relace" a připojit jej při otevření lístku podpory.

![diagnostické informace](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Chcete-li zahájit řešení potíží, opakujte operaci, kterou jste provedli v Azure Synapse Studio.

- Pro příznak 1 vyberte tlačítko "Aktualizovat" vpravo od rozbalovacího souboru "Použít databázi" na kartě "Skript SQL" a zkontrolujte, zda můžete vidět "SQL na vyžádání".
- Pro příznak 2 zkuste znovu spustit dotaz a zjistěte, zda se úspěšně spustí.

Pokud problém přetrvává, stisknutím klávesy F12 v prohlížeči otevřete "Developer Tools" (DevTools).

V okně "Nástroje pro vývojáře" přepněte na panel "Síť". V případě potřeby vyberte tlačítko "Vymazat" na panelu nástrojů v panelu "Síť".
Ujistěte se, že je zaškrtnuto políčko Zakázat mezipaměť v panelu Síť.

Opakujte operaci, kterou jste provedli v Azure Synapse Studio. Nové položky se mohou zobrazit v seznamu "Síť" v "Vývojářské nástroje". Poznamenejte si aktuální systémový čas, který chcete poskytnout v lístku podpory.

![síťový panel](media/troubleshooting-synapse-studio/network-panel.png)

Vyhledejte položku, jejíž sloupec URL odpovídá následujícímu vzoru:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Kde [*A*] je název pracovního prostoru a "-ondemand" může být "-sqlod" a kde [*B*] by měl být název databáze, například "master". Měly by existovat maximálně dvě položky se stejnou hodnotou adresy URL, ale s různými hodnotami metody; MOŽNOSTI A POST. Zkontrolujte, zda tyto dvě položky mají "200" nebo "20x" ve sloupci stavu, kde "x" může být libovolné jednociferné.

Pokud jeden z nich má něco jiného než "20x" a:

- stav začíná "(se nezdařilo)", buď rozšířit sloupec "Stav" nebo najeďte myší na stavový text, abyste viděli celý text. Při otevření lístku podpory uveďte text a/nebo snímek obrazovky.

    ![stavový text](media/troubleshooting-synapse-studio/status-text.png)

    - Pokud se zobrazí ERR_NAME_NOT_RESOLVED a vytvořili jste pracovní prostor do 10 minut, počkejte 10 minut a zkuste znovu zjistit, zda problém stále existuje.
    - Pokud vidíte ERR_INTERNET_DISCONNECTED nebo ERR_NETWORK_CHANGED, může to znamenat, že máte problémy s připojením k počítači. Zkontrolujte připojení k síti a opakujte operaci.
    - Pokud se zobrazí ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR nebo jiné kódy chyb obsahující "SSL", může to znamenat, že místní konfigurace SSL má problémy nebo správce sítě zablokoval přístup k serveru NA vyžádání SQL. Otevřete lístek podpory a připojte kód chyby v popisu.
    - Pokud se zobrazí ERR_NETWORK_ACCESS_DENIED, bude pravděpodobně nutné se poradit se správcem, zda místní zásady brány firewall zablokovaly přístup k doméně *.database.windows.net nebo vzdálenému portu 1443.
    - Volitelně vyzkoušejte stejnou operaci okamžitě v jiném počítači nebo síťovém prostředí, abyste vyloučili problém s konfigurací sítě v počítači.

- "40x", "50x" nebo jiná čísla, vyberte na položkách, abyste viděli podrobnosti. Podrobnosti o položce byste měli vidět vpravo. Vyhledejte oddíl "Záhlaví odpovědi". potom zkontrolujte, zda položka s názvem "access-control-allow-origin" existuje. Pokud ano, zkontrolujte, zda má jednu z následujících hodnot:

    - `*`(jedna hvězdička)
    - https://web.azuresynapse.net/(nebo jinou hodnotu, kterou text v adresním řádku prohlížeče začíná)

Pokud hlavička odpovědi obsahuje jednu z výše uvedených hodnot, znamená to, že jsme již měli shromáždit informace o selhání. V případě potřeby můžete otevřít lístek podpory a volitelně připojit snímek obrazovky s podrobnostmi o položce.

Pokud záhlaví nevidíte nebo záhlaví nemá jednu z výše uvedených hodnot, připojte při otevření lístku snímek obrazovky s podrobnostmi o položce.

![podrobnosti položky](media/troubleshooting-synapse-studio/item-details.png)

Pokud výše uvedené kroky problém nevyřeší, bude pravděpodobně nutné otevřít lístek podpory. Při odesílání lístku podpory uveďte na začátku této příručky "ID relace" nebo "Diagnostické informace".

Při nahlašování problému můžete volitelně pořídit snímek obrazovky s kartou "Konzole" v části Nástroje pro vývojáře a připojit jej k lístku podpory. Posuňte obsah a v případě potřeby pořiďte více než jeden snímek obrazovky, abyste zachytili celou zprávu.

![vývojář-nástroj-konzole](media/troubleshooting-synapse-studio/developer-tool-console.png)

Pokud připojujete snímky obrazovky, uveďte čas (nebo odhadovaný časový rozsah) toho, kdy jste snímky obrazovky pořídili. To nám pomůže při pohledu na problém.

Některé prohlížeče podporují zobrazení časových razítek na kartě "Konzola". V části Chromium Edge/Chrome otevřete dialogové okno "Nastavení" v části Nástroje pro vývojáře a v záložce Předvolby zaškrtněte políčko Zobrazit časová razítka.

![nastavení vývojář-nástroj-konzole](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![show-time-razítko](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>Další kroky
Pokud předchozí kroky nepomohou vyřešit [problém, vytvořte lístek podpory](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
