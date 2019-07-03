---
title: Microsoft Azure FXT hrany Filer konfigurace clusteru - přidat uzly
description: Přidání uzlů do mezipaměti Azure FXT hrany souborového úložiště
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: d84b98b4ab936bbb6978144eb2e89b5e19df7069
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543204"
---
# <a name="tutorial-add-cluster-nodes"></a>Kurz: Přidat uzly clusteru 

Nový cluster Azure FXT hrany Filer se vytvoří s pouze jedním uzlem. By měl přidat aspoň dva další uzly a povolit vysokou dostupnost než přistoupíte k další konfiguraci. 

Tento kurz vysvětluje, jak přidat uzly clusteru a povolit funkci vysoká dostupnost (HA). 

V tomto kurzu se dozvíte: 

> [!div class="checklist"]
> * Přidání uzlů do clusteru FXT
> * Jak povolit vysokou dostupnost

Kroky v tomto kurzu trvat přibližně 45 minut.

Než začnete tento kurz, zapněte uzly, které chcete přidat a [počátečního hesla](fxt-node-password.md). 

## <a name="1-load-the-cluster-nodes-page"></a>1. Načtení stránky uzly clusteru

Otevřete ovládací panely clusteru ve webovém prohlížeči a přihlaste se jako správce. (Podrobné pokyny naleznete v článku s přehledem, v části [otevření stránky nastavení](fxt-cluster-create.md#open-the-settings-pages).)

Ukazuje ovládací panely **řídicí panel** kartu, když se otevře. 

![Ovládací prvek Panel řídicího panelu (první kartu)](media/fxt-cluster-config/dashboard-1-node.png)

Tento obrázek ukazuje na řídicím panelu pro nově vytvořený cluster s jeden uzel.

## <a name="2-locate-the-node-to-add"></a>2. Vyhledejte uzel, který má přidat

Přidat uzly, klikněte na tlačítko **nastavení** kartě a zvolte **FXT uzly** stránku **clusteru** oddílu.

![Nastavení ovládacího panelu kartu (druhá karta) s clusterem > FXT uzly načíst](media/fxt-cluster-config/settings-fxt-nodes.png)

**FXT uzlů – odpojen** seznamu zobrazí všechny nepřiřazené FXT uzly (většina datových center disponuje jenom pár. Najdete FXT uzly, které chcete přidat do clusteru.

> [!Tip] 
> Pokud nemůžete najít uzel chcete na **Unjoined** seznamu, zkontrolujte, že splňuje tyto požadavky:
> 
> * Zapne a byla [kořenové heslo sady](fxt-node-password.md).
> * Je připojený k síti, ke kterým může přístup. Pokud používáte sítě VLAN, musí být na stejnou síť VLAN jako cluster.
> * U protokolu Bonjour se dá zjistit. 
>
>   Některá nastavení brány firewall na blokování TCP/UDP portů používaných Bonjour, které brání automatické zjišťování uzly FXT operačního systému.
> 
> Pokud uzel, který chcete přidat, není na seznamu, vyzkoušejte tato řešení: 
> 
> * Klikněte na tlačítko **ručně zjišťovat** tlačítko Najít podle IP adresy.
> 
> * Přiřadíte ručně dočasné IP adresy. To je vzácné, ale může být nutné, pokud používáte označené sítě VLAN a nejsou ve správné síti nebo síti neumožňuje svým přiřazené IP adresy. Postupujte podle pokynů ve starší verzi tohoto dokumentu [ručně nastavili statickou IP adresu](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

Název uzlu, IP adresa, verze softwaru a způsobilosti stav se zobrazí v seznamu. Obvykle **stav** sloupec buď říká "Chce připojit k" nebo popisuje problém systému nebo hardware, který vytvoří uzel nelze připojit ke clusteru.

**Akce** sloupec obsahuje tlačítka, která umožňují přidání uzlu do clusteru nebo aktualizovat svůj software. Tlačítko Aktualizovat automaticky nainstaluje verzi softwaru, která odpovídá uzly v clusteru.

Všechny uzly v clusteru musí používat stejnou verzi operačního systému, ale není nutné aktualizace softwaru před přidáním uzlu. Po klepnutí **povolení k připojení k** tlačítko, proces připojení clusteru automaticky kontroluje a nainstaluje software operačního systému, která odpovídá verzi v clusteru.

Další informace o možnosti na této stránce najdete v článku [ **clusteru** > **FXT uzly** ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) v Průvodci konfigurace clusteru.

## <a name="3-click-the-allow-to-join-button"></a>3. Klikněte na tlačítko "Povolit chcete se připojit k" 

Klikněte na tlačítko **povolit chcete se připojit k*** tlačítko **akce** sloupec uzlu, který chcete přidat.

Po kliknutí na tlačítko uzlu stav může změnit, protože jeho software se aktualizuje při přípravě na jeho přidání do clusteru. 

Následující obrázek ukazuje uzel, který se právě připojení (pravděpodobně, že je to stále aktualizace operačního systému přidán) clusteru. Žádné tlačítek se zobrazí v **akce** sloupec uzlů, které se v současnosti se přidávají do clusteru.

![jeden řádek tabulky uzlu, zobrazuje název uzlu, IP adresa, verze softwaru, zpráva "Povolené pro připojení" a poslední sloupec je prázdný](media/fxt-cluster-config/node-join-in-process.png)

Po chvíli se nový uzel by se zobrazit v seznamu uzlů clusteru v horní části **FXT uzly** stránku nastavení. 

Opakujte tento postup pro přidání dalších uzlů do clusteru. Nemusíte čekat na jeden uzel, který dokončit před zahájením druhé připojující se ke clusteru.

## <a name="enable-high-availability"></a>Povolit vysokou dostupnost

Po přidání druhého uzlu do clusteru, zobrazí se pravděpodobně zprávu s upozorněním na ovládacím panelu, řídicí panel, který funkci vysoké dostupnosti není nakonfigurovaná. 

Vysoká dostupnost (HA) umožňuje uzly clusteru jako kompenzaci za sobě navzájem, pokud jeden ocitne mimo provoz. Ve výchozím nastavení není povoleno vysokou dostupnost.

![Karta řídicí panel se zprávou "má více než jeden uzel clusteru, ale vysokou dostupnost bez povoleného..." v tabulce podmínky](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note] 
> Nepovolujte vysokou dostupnost, dokud nebudete mít alespoň tři uzly v clusteru.

Tento postup, chcete-li na vysokou dostupnost: 

1. Zatížení **vysoké dostupnosti** stránku **clusteru** část **nastavení** kartu.

   ![Stránka Konfigurace vysokou dostupnost (clusteru > vysoká dostupnost). Tlačítka pro odeslání je v dolní části políčko "Povolit HA" je v horní části.](media/fxt-cluster-config/enable-ha.png)

2. Klikněte na poli označeném **povolit HA** a klikněte na tlačítko **odeslat** tlačítko. 

Upozornění se zobrazí na **řídicí panel** potvrďte, že je povoleno vysokou dostupnost.

![Řídicí panel Tabulka zobrazující zprávu "Vysokou DOSTUPNOSTÍ je teď plně nakonfigurované"](media/fxt-cluster-config/ha-configured-alert.png)


## <a name="next-steps"></a>Další postup

Po přidání všech uzlů v clusteru, pokračujte v instalaci podle konfigurace vašeho clusteru dlouhodobé uložení.

> [!div class="nextstepaction"]
> [Přidání back endové úložné a nastavení virtuální obor názvů](fxt-add-storage.md)