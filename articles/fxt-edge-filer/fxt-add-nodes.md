---
title: 'Kurz: Přidání uzlů do clusteru Azure FXT Edge Filer'
description: Jak přidat uzly do mezipaměti úložiště Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 6251fe8f88b7db25e3c09898540e07754d72fb0d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75551943"
---
# <a name="tutorial-add-cluster-nodes-to-an-azure-fxt-edge-filer-cluster"></a>Kurz: Přidání uzlů clusteru do clusteru Azure FXT Edge Filer

Nový cluster Azure FXT Edge Filer se vytvoří pouze s jedním uzlem. Měli byste přidat alespoň dva další uzly a povolit vysokou dostupnost před provedením jiné konfigurace. 

Tento kurz vysvětluje, jak přidat uzly clusteru a povolit funkci Vysoká dostupnost (HA). 

V tomto kurzu se dozvíte: 

> [!div class="checklist"]
> * Jak přidat uzly do clusteru FXT
> * Jak povolit HA

Kroky v tomto kurzu trvat přibližně 45 minut na dokončení.

Než začnete tento kurz, zapčtete uzly, které chcete přidat, a [nastavte jejich počáteční hesla](fxt-node-password.md). 

## <a name="1-load-the-cluster-nodes-page"></a>1. Načtení stránky Uzlů clusteru

Otevřete ovládací panel clusteru ve webovém prohlížeči a přihlaste se jako správce. (Podrobné pokyny jsou uvedeny v článku s přehledem v části [Otevřít stránky Nastavení](fxt-cluster-create.md#open-the-settings-pages).)

Ovládací panely zobrazují kartu **Řídicí panel** při otevření. 

![Řídicí panel ovládacího panelu (první karta)](media/fxt-cluster-config/dashboard-1-node.png)

Tento obrázek znázorňuje řídicí panel nově vytvořeného clusteru s jedním uzlem.

## <a name="2-locate-the-node-to-add"></a>2. Vyhledejte uzel, který chcete přidat

Chcete-li přidat uzly, klikněte na kartu **Nastavení** a v části **Cluster** zvolte stránku **Uzly FXT.**

![Karta Nastavení ovládacího panelu (druhá karta) s načtenými uzly CLUSTERu > FXT](media/fxt-cluster-config/settings-fxt-nodes.png)

Seznam **UZLy FXT - Unjoined** zobrazuje všechny nepřiřazené FXT uzly (většina datových center má jen několik. Najděte uzly FXT, které chcete přidat do clusteru.

> [!Tip] 
> Pokud nemůžete najít uzel, který chcete v seznamu **Nepřipojeno,** zkontrolujte, zda splňuje tyto požadavky:
> 
> * Je zapnutý a má [root heslo nastavit](fxt-node-password.md).
> * Je připojen k síti, ke které máte přístup. Pokud používáte sítě VLAN , musí být ve stejné síti VLAN jako cluster.
> * To může být detekováno protokolem Bonjour. 
>
>   Některá nastavení brány firewall blokují porty TCP/UDP používané společností Bonjour, což brání operačnímu systému FXT automaticky detekovat uzly.
> 
> Pokud uzel, který chcete přidat, není v seznamu, vyzkoušejte tato řešení: 
> 
> * Kliknutím na tlačítko **Ruční zjišťování** jej vyhledejte podle IP adresy.
> 
> * Ručně přiřaďte dočasné adresy IP. To je vzácné, ale může být potřeba, pokud používáte označené sítě VLAN a uzly nejsou ve správné síti nebo síť neumožňuje vlastní přiřazené adresy IP. Podle pokynů ve starší verzi tohoto dokumentu [ručně nastavte statickou adresu IP](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

Název uzlu, IP adresa, verze softwaru a stav způsobilosti jsou zobrazeny v seznamu. Sloupec **Stav** obvykle říká"Chce se připojit" nebo popisuje problém se systémem nebo hardwarem, který činí uzel nezpůsobilým pro připojení ke clusteru.

Sloupec **Akce** obsahuje tlačítka, která umožňují přidat uzel do clusteru nebo aktualizovat jeho software. Tlačítko aktualizace automaticky nainstaluje verzi softwaru, která odpovídá uzlům, které jsou již v clusteru.

Všechny uzly v clusteru musí používat stejnou verzi operačního systému, ale před přidáním uzlu není nutné aktualizovat software. Po klepnutí na tlačítko **Povolit připojení** proces spojení clusteru automaticky zkontroluje a nainstaluje software operačního systému, který odpovídá verzi v clusteru.

Další informace o možnostech na této stránce najdete v průvodci konfigurací clusteru [ **Cluster** > FXT](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) v Průvodci konfigurací clusteru.

## <a name="3-click-the-allow-to-join-button"></a>3. Klikněte na tlačítko "Povolit připojení" 

Klikněte na tlačítko **Povolit připojení *** ve sloupci **Akce** pro uzel, který chcete přidat.

Po klepnutí na tlačítko se stav uzlu může změnit, protože jeho software je aktualizován v rámci přípravy na jeho přidání do clusteru. 

Na obrázku níže je zobrazen uzel, který je v procesu připojení ke clusteru (s největší pravděpodobností je získání aktualizace operačního systému před přidáním). Ve sloupci **Akce** se nezobrazují žádná tlačítka pro uzly, které jsou právě přidávány do clusteru.

![jeden řádek tabulky uzlů s názvem uzlu, IP adresou, verzí softwaru, zprávou "Povoleno připojení" a prázdným posledním sloupcem](media/fxt-cluster-config/node-join-in-process.png)

Po několika okamžicích by se měl nový uzel zobrazit v seznamu uzlů clusteru v horní části stránky nastavení **uzlů FXT.** 

Tento postup opakujte a přidejte do clusteru další uzly. Před spuštěním jiného uzlu nemusíte čekat na dokončení připojení jednoho uzlu ke clusteru.

## <a name="enable-high-availability"></a>Povolit vysokou dostupnost

Po přidání druhého uzlu do clusteru se může na řídicím panelu ovládacího panelu zobrazit varovná zpráva, že funkce vysoké dostupnosti není nakonfigurovaná. 

Vysoká dostupnost (HA) umožňuje uzlům clusteru vzájemně kompenzovat, pokud jeden přejde dolů. Ha není ve výchozím nastavení povolena.

![Karta Řídicí panel se zprávou "Cluster má více než jeden uzel, ale ha není povolena ..." v tabulce Podmínky](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note] 
> Nepovolujte ha, dokud nebudete mít alespoň tři uzly v clusteru.

Chcete-li zapnout ha, postupujte podle tohoto postupu: 

1. Načtěte stránku **Vysoká dostupnost** v části **Cluster** na kartě **Nastavení.**

   ![Konfigurační stránka HA (Cluster > vysoká dostupnost). Zaškrtávací políčko "Povolit HA" je nahoře a tlačítko odeslat je dole.](media/fxt-cluster-config/enable-ha.png)

2. Klikněte na pole s názvem **Povolit HA** a klikněte na tlačítko **Odeslat.** 

Na **řídicím panelu** se zobrazí výstraha, která potvrzuje, že je povolena funkce HA.

![Tabulka řídicího panelu zobrazující zprávu "HA je nyní plně nakonfigurována"](media/fxt-cluster-config/ha-configured-alert.png)


## <a name="next-steps"></a>Další kroky

Po přidání všech uzlů v clusteru pokračujte v instalaci konfigurací dlouhodobého úložiště clusteru.

> [!div class="nextstepaction"]
> [Přidání back-endového úložiště a nastavení virtuálního oboru názvů](fxt-add-storage.md)