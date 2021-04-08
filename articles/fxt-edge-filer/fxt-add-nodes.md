---
title: 'Kurz: Přidání uzlů do clusteru Azure FXT Edge souborového'
description: Naučte se přidávat uzly clusteru do mezipaměti úložiště Azure FXT Edge souborového a povolit funkci vysoké dostupnosti (HA).
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 85ad78eeb095b427b1a6334f57c351e926022dff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021873"
---
# <a name="tutorial-add-cluster-nodes-to-an-azure-fxt-edge-filer-cluster"></a>Kurz: Přidání uzlů clusteru do clusteru Azure FXT Edge souborového

Vytvoří se nový cluster Azure FXT Edge souborového jenom s jedním uzlem. Před provedením další konfigurace byste měli přidat aspoň dva další uzly a povolit vysokou dostupnost.

V tomto kurzu se dozvíte, jak přidat uzly clusteru a povolit funkci vysoké dostupnosti (HA).

V tomto kurzu se dozvíte:

> [!div class="checklist"]
>
> * Postup přidání uzlů do clusteru FXT
> * Jak povolit HA

Dokončení kroků v tomto kurzu trvá přibližně 45 minut.

Než začnete s tímto kurzem, zapněte uzly, které chcete přidat, a [nastavte jejich počáteční hesla](fxt-node-password.md).

## <a name="1-load-the-cluster-nodes-page"></a>1. načtení stránky uzlů clusteru

Ve webovém prohlížeči otevřete ovládací panely clusteru a přihlaste se jako správce. (Podrobné pokyny najdete v článku Přehled v části [otevření stránek nastavení](fxt-cluster-create.md#open-the-settings-pages).)

Ovládací panel při otevření zobrazí kartu **řídicí panel** . 

![Řídicí panel (první karta)](media/fxt-cluster-config/dashboard-1-node.png)

Tento obrázek ukazuje řídicí panel nově vytvořeného clusteru s jedním uzlem.

## <a name="2-locate-the-node-to-add"></a>2. Vyhledejte uzel, který chcete přidat.

Chcete-li přidat uzly, klikněte na kartu **Nastavení** a vyberte stránku **uzly FXT** v části **cluster** .

![Karta nastavení ovládacího panelu (druhá karta) s načtenými uzly clusteru > FXT](media/fxt-cluster-config/settings-fxt-nodes.png)

**Uzel FXT – nepřipojený** seznam zobrazuje všechny nepřiřazené FXT uzly (většina datových Center má jenom pár. Vyhledejte uzly FXT, které chcete přidat do clusteru.

> [!Tip]
> Pokud v seznamu **nepřipojený** požadovaný uzel nemůžete najít, ověřte, že splňuje tyto požadavky:
>
> * Je zapnutý a má [nastavené kořenové heslo](fxt-node-password.md).
> * Je připojen k síti, ke které máte přístup. Pokud používáte sítě VLAN, musí se nacházet ve stejné síti VLAN jako cluster.
> * Dá se zjistit pomocí protokolu Bonjour.
>
>   Některá nastavení brány firewall blokují porty TCP/UDP používané službou Bonjour, což brání operačnímu systému FXT v automatickém zjišťování uzlů.
>
> Pokud uzel, který chcete přidat, není v seznamu, zkuste Tato řešení:
>
> * Klikněte na tlačítko **Ruční zjišťování** a vyhledejte ho podle IP adresy.
>
> * Ručně přiřaďte dočasné IP adresy. Tato situace je vzácná, ale může být nutná, pokud používáte tagované sítě VLAN a uzly nejsou ve správné síti nebo vaše síť nepovoluje IP adresy přiřazené svým držitelem. Podle pokynů ve starší verzi tohoto dokumentu [nastavte STATICKOU IP adresu ručně](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

V seznamu se zobrazí název uzlu, IP adresa, verze softwaru a stav způsobilosti. Sloupec status ( **stav** ) zpravidla říká "chce se připojit" nebo popisuje problém se systémem nebo hardwarem, který způsobí, že se uzel neoprávněně připojí ke clusteru.

Sloupec **Actions** obsahuje tlačítka, která umožňují přidat uzel do clusteru nebo aktualizovat jeho software. Tlačítko Aktualizovat automaticky nainstaluje verzi softwaru, která odpovídá uzlům, které jsou již v clusteru.

Všechny uzly v clusteru musí používat stejnou verzi operačního systému, ale před přidáním uzlu nemusíte tento software aktualizovat. Po kliknutí na tlačítko **pro připojení** se proces připojení clusteru automaticky zkontroluje a nainstaluje software operačního systému, který odpovídá verzi v clusteru.

Další informace o možnostech na této stránce najdete v části [ **cluster**  >  **FXT Nodes**](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) v Průvodci konfigurací clusteru.

## <a name="3-click-the-allow-to-join-button"></a>3. klikněte na tlačítko "udělit připojení".

Klikněte na tlačítko **pro připojení** _ ve sloupci _ *Akce** pro uzel, který chcete přidat.

Po kliknutí na tlačítko se stav uzlu může změnit, protože jeho software se aktualizuje při přípravě na jeho přidání do clusteru.

Následující obrázek ukazuje uzel, který se v procesu připojení ke clusteru (pravděpodobně ho před přidáním aktualizuje na operační systém). Ve sloupci **Actions** se nezobrazí žádná tlačítka pro uzly, které se v procesu přidávají do clusteru.

![jeden řádek tabulky uzlů, ve kterém se zobrazuje název uzlu, IP adresa, verze softwaru, zpráva s povoleným připojením a prázdný poslední sloupec](media/fxt-cluster-config/node-join-in-process.png)

Po chvíli by se měl nový uzel zobrazit v seznamu uzlů clusteru v horní části stránky nastavení **uzlů FXT** .

Opakujte tento postup pro přidání dalších uzlů do clusteru. Než začnete s nasazením jiného uzlu, nemusíte čekat na dokončení připojení ke clusteru.

## <a name="enable-high-availability"></a>Povolit vysokou dostupnost

Po přidání druhého uzlu do clusteru se na řídicím panelu ovládacích panelů může zobrazit zpráva s upozorněním, že funkce vysoké dostupnosti není nakonfigurovaná.

Vysoká dostupnost (HA) umožňuje uzlům clusteru kompenzovat každou druhou dobu, pokud jedna dojde k výpadku. HA není ve výchozím nastavení povolená.

![Karta řídicího panelu se zprávou "cluster má více než jeden uzel, ale možnost HA není povolena..." v tabulce podmínky](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note]
> Nepovolujte HA, dokud nebudete mít aspoň tři uzly v clusteru.

Pomocí tohoto postupu zapněte HA:

1. V části **cluster** na kartě **Nastavení** načtěte stránku **vysoké dostupnosti** .

   ![Konfigurační stránka HA (vysoká dostupnost clusteru >). Zaškrtávací políčko "Povolit HA" je v horní části a tlačítko Odeslat je v dolní části.](media/fxt-cluster-config/enable-ha.png)

2. Klikněte na pole s popiskem **Povolit ha** a klikněte na tlačítko **Odeslat** .

Na **řídicím panelu** se zobrazí výstraha, která potvrdí, že je možnost ha povolena.

![Tabulka řídicích panelů ukazující zprávu "HA je nyní plně nakonfigurovaná"](media/fxt-cluster-config/ha-configured-alert.png)

## <a name="next-steps"></a>Další kroky

Po přidání všech uzlů v clusteru pokračujte v instalaci nakonfigurováním dlouhodobého úložiště clusteru.

> [!div class="nextstepaction"]
> [Přidání back-endu úložiště a nastavení virtuálního oboru názvů](fxt-add-storage.md)