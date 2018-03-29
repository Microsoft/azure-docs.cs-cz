---
title: Nejčastější dotazy k Azure provoz analytics | Microsoft Docs
description: Získejte odpovědi na některé nejčastější dotazy o analýzy provozu.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: 01d5150bff8642a1a3fe9b7ac063923916f191c0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Analýza provozu nejčastější dotazy

1.  Jaké jsou požadavky používat Analýza provozu?

    Analýza provozu vyžaduje následující požadavky:

    - Sledovací proces sítě povolené předplatné
    - Tok protokolů NSG pro skupiny Nsg, kterou chcete sledovat povoleno
    - Účet úložiště Azure pro uložení nezpracovaná flog protokoly
    - Pracovní prostor analýzy protokolů (OMS) s oprávnění ke čtení a zápisu
    - Musí váš účet přiřazenou na poskytovateli Microsoft.Network následující akce:

        - Microsoft.Network/applicationGateways/read
        - Microsoft.Network/connections/read
        - Microsoft.Network/loadBalancers/read 
        - Microsoft.Network/localNetworkGateways/read 
        - Microsoft.Network/networkInterfaces/read 
        - Microsoft.Network/networkSecurityGroups/read 
        - Microsoft.Network/publicIPAddresses/read
        - Microsoft.Network/routeTables/read
        - Microsoft.Network/virtualNetworkGateways/read 
        - Microsoft.Network/virtualNetworks/read

2.  Které oblasti jsou k dispozici v provozu analytics?

    Ve verzi preview, můžete použít pro skupiny Nsg v některém z následujících Analýza provozu **podporované oblasti**: – Západ střední USA, Východ USA, Východ USA 2, – Sever střední USA, Jižní střední USA, střed USA, západ USA, západní USA 2, západní Evropa, Severní Evropa , Spojené království – Západ, Spojené království – Jih, Austrálie – východ a Austrálie – jihovýchod. Pracovní prostor analýzy protokolů musí existovat v – Západ střední USA, Východ USA, západní Evropa, Austrálie – jihovýchod nebo oblasti Spojené království – jih.

3.  Můžete povolit toku skupin Nsg protokoly pro nacházet v různých oblastech než pracovní prostor OMS?

    Ano

4.  Lze nakonfigurovat více skupin Nsg v rámci jednoho pracovního prostoru?

    Ano

5.  Můžete použít stávající pracovní prostor OMS?

    Ano, pokud vyberete existujícímu pracovnímu prostoru, ujistěte se, že byla migrována do nového dotazu jazyka. Pokud nechcete, aby k upgradu prostoru; budete muset vytvořit nový. Další informace o nový jazyk dotazů najdete v tématu [Azure Log Analytics upgradovat na nové hledání protokolu](../log-analytics/log-analytics-log-search-upgrade.md).

6.  Můžete být svůj účet úložiště Azure v rámci jednoho předplatného a pracovní prostor OMS být v jiném předplatném?

    Ano

7.  Můžete uložit nezpracovaná protokoly v jiný účet úložiště v jiném předplatném.

    Ne. Nezpracovaná protokoly můžete uložit ve všech podporou skupina NSG pro tok protokoly účtu úložiště, ale účet úložiště a raw protokoly musí být ve stejném předplatném, oblasti.

8.  Pokud se zobrazí chybu "Nebyl nalezen" při konfiguraci Skupina NSG pro analýzu provoz, jak mohu vyřešit?

    Vyberte podporované oblasti uvedené v otázku 2. Pokud vyberete oblast není podporován, obdržíte chybu "Nebyl nalezen".

9.  V rámci toku protokolů NSG, zobrazuje stav NSG jako "Nepodařilo se načíst", jak postupovat?

    Zprostředkovatel Microsoft.Insights musí být zaregistrován pro tok protokolování správně fungovat. Pokud si nejste jisti, zda je poskytovatel Microsoft.Insights registrovaný nebo není pro vaše předplatné, nahraďte *xxxxx-xxxxx-xxxxxx-xxxx* na následující příkaz a potom spusťte následující příkazy z prostředí PowerShell:

    ```powershell-interactive
    **Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
    **Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
    ```

10. Nakonfigurovali I řešení. Proč nejsou zobrazeny nic na řídicím panelu?

    Řídicí panel může trvat až 30 minut se zobrazí při prvním. Řešení musí nejprve agregace dostatek dat pro její odvodit smysluplné přehledy, než všechny sestavy jsou generovány. 

11.  Pokud se zobrazí následující zpráva: "jsme nenašli žádná data v tomto pracovním prostoru pro vybraný časový interval. Zkuste změnit časový interval nebo vyberte jiný pracovní prostor", jak ho lze vyřešit?

        Vyzkoušejte následující možnosti:
        - Zkuste změnit časový interval v horním panelu.
        - V horním panelu vyberte jiný pracovní prostor analýzy protokolů
        - Zkuste přístup k provozu Analytics po 30 minutách, pokud bylo nedávno povolené
    
        Pokud problémy potrvají, vyšší riziko z hlediska v [fórum hlas uživatele](https://feedback.azure.com/forums/217313-networking?category_id=195844).

12.  Pokud se zobrazí následující zpráva: "1) analýza vaše skupina NSG toku protokoly poprvé. Tento proces může trvat 20-30 minut. Zkontrolujte zpět po určité době. 2) Pokud předchozí krok nefunguje a pracovní prostor je pod volné SKU a potom zkontrolujte vašeho pracovního prostoru využití k ověření přes kvótu, jinak postupujte nejčastější dotazy o další informace", jak lze vyřešit ji?

        Může dojít k chybě z následujících důvodů:
        - Analýza provozu může byl nedávno povolen a může být agregování dostatek dat pro její odvodit smysluplné přehledy, než lze generovat žádné sestavy. V takovém případě zkuste to znovu za 30 minut
        - Pracovní prostor OMS je pod volné SKU a nichž nebyla dodržena omezení kvóty. V takovém případě budete muset použít pracovního prostoru v SKU s větší kapacitou.
    
        Pokud problémy potrvají, vyšší riziko z hlediska v [fórum hlas uživatele](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
13.  Pokud se zobrazí následující zpráva: "vypadá máme zdroje dat (topologie) a žádné informace toky. Mezitím, kliknutím sem můžete zobrazit data prostředků a odkazovat na nejčastější dotazy pro další informace", jak ho vyřešit?

        Informace o zdroji se zobrazuje na řídicím panelu; však nejsou žádné související toku statistiky. Data k dispozici z důvodu toky žádná komunikace mezi prostředky. Počkejte 60 minut a znovu zkontrolovat stav. Pokud jste si jisti, že neexistuje toky komunikace mezi zdroje a poté vyšší riziko z hlediska v [fórum hlas uživatele](https://feedback.azure.com/forums/217313-networking?category_id=195844).

14.  Jak je cenově Analýza provozu?

        Analýza provozu je – měření podle objemu rozšíření snížené protokoly a ukládání rozšířené protokolů v pracovním prostoru analýzy protokolů. Zatímco ve verzi preview, není účtován Analýza provozu rozšíření snížené protokoly, ale uchovávání dat v pracovním prostoru podléhá fakturace publikované tempem. Tato odpověď bude aktualizován, jakmile ceny pro provoz analytics je k dispozici.

15.  Jak můžete přejít pomocí klávesnice v zobrazení mapy geograficky?

        Tato stránka geografické mapy obsahuje dvě hlavní části:
    
        - **Hlavička**: Hlavička umístěny v horní části geografické mapy poskytuje schopnost vyberte filtry distribuce přenosů prostřednictvím tlačítka jako Deployment/No nasazení nebo aktivní nebo neaktivní nebo provoz Analytics povoleno nebo provoz Analytics není povolena nebo provozu z škodlivý přenos zemích nebo Benign nebo škodlivé nebo povolené a informace legendy. Na výběr definované tlačítka se na mapě, například pokud uživatel vybere "Aktivní" tlačítko filtru v informační zprávě, pak mapy označuje "Aktivní" datových center ve vašem nasazení použije příslušný filtr.
        - **Mapa**: v části The mapy umístit pod Banner informující o zobrazují distribuce přenosů mezi datovými centry Azure a zemích.
    
        **Navigace klávesnice na hlavičku**
    
        - Ve výchozím nastavení je výběr na stránce geografické mapy Banner informující o filtr tlačítko "Azure řadiče domény".
        - Přejděte na další tlačítko filtry, můžete použít `Tab` nebo `Right arrow` klíč přesunout na další. Přesun zpět, použijte buď `Shift+Tab` nebo `Left arrow` klíč. Předat dál navigační směr přednost zleva doprava, následuje horní dolů.
        - Stiskněte `Enter` nebo `Down` klávesy ŠIPKA použít vybraný filtr. Na základě výběru filtru a nasazení, jsou vyznačené jeden nebo více uzlů části mapy.
            - Přepínat mezi **Banner** a **mapy**, stiskněte klávesu `Ctrl+F6`.
        
        **Navigace na mapě pomocí klávesnice**
    
        - Jakmile vybraný libovolný filtr na informační zprávě a stisknutí `Ctrl+F6`, se aktivuje jeden zvýrazněný uzel (**datové centrum Azure** nebo **země nebo oblast**) v zobrazení mapy.
        - Přejděte do dalších uzlů zvýrazněných v mapě, můžete použít `Tab` nebo `Right arrow` klíče pro posunování vpřed a `Shift+Tab` nebo `Left arrow` klíče pro zpětné pohyb.
        - Chcete-li vybrat všechny zvýrazněný uzel v mapě, použijte `Enter` nebo `Down arrow` klíč.
        - Na výběr takové uzlů se aktivuje **informace nástroj pole** pro uzel. Ve výchozím nastavení, se aktivuje tlačítko uzavřené **informace nástroj pole**. Dále přejděte v **pole** zobrazit, použijte `Right` a `Left arrow` klíče přesunout vpřed a zpět, v uvedeném pořadí. Stisknutím `Enter` má stejný účinek jako výběr cílených tlačítka na **informace nástroj pole**.
        - Stisknutím `Tab` při zaměřuje se na **informace nástroj pole**, aktivuje koncové body na stejné kontinentě jako vybraný uzel. Můžete použít `Right` a `Left arrow` klíče můžete procházet pomocí těchto koncových bodů.
        - Přejděte na další koncové body nebo přes kontinenty až clusteru toku, použijte `Tab` pro posunování vpřed a `Shift+Tab` pro zpětné pohyb.
        - Jakmile je zaměřená na `Continent clusters`, použijte `Enter` nebo `Down` klávesy se šipkami zvýrazněte koncové body uvnitř obsah přenášel clusteru. Procházet koncových bodů a tlačítko Zavřít na pole informace obsah přenášel clusteru, použijte buď `Right` nebo `Left arrow` klíč pro přesun vpřed a zpět, v uvedeném pořadí. Na libovolný koncový bod, můžete použít `Shift+L` přepnutí na řádek připojení z vybraného uzlu ke koncovému bodu. Stisknutím `Shift+L` můžete znovu přejde na vybraný koncový bod.
        
        V jakékoli fázi:
    
        - `ESC` Sbalí rozšířený výběr.
        - `UP Arrow` Klíč provede stejnou akci, jako `ESC`. `Down arrow` Klíč provede stejnou akci, jako `Enter`.
        - Použití `Shift+Plus` v, a `Shift+Minus` zvětšit.
