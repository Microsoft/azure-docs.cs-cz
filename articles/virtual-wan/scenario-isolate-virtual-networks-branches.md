---
title: 'Scénář: vlastní izolace pro virtuální sítě a větve'
titleSuffix: Azure Virtual WAN
description: Scénáře pro směrování – zabrání vybraným virtuální sítě a větvím, aby se mohly vzájemně narazit.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: wellee
ms.openlocfilehash: e8e5a5a1b9325f40fdd51133155a0daffaa55a7b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99408479"
---
# <a name="scenario-custom-isolation-for-virtual-networks-and-branches"></a>Scénář: vlastní izolace pro virtuální sítě a větve

Při práci s směrováním virtuálního rozbočovače WAN je k dispozici několik scénářů, které jsou v pořádku. Ve vlastním scénáři izolace pro obě virtuální sítě (virtuální sítě) i větve je cílem zabránit konkrétní sadě virtuální sítě v tom, aby dosáhli jiné sady virtuální sítě. Větve (VPN/ER/User VPN) mají povolený přístup jenom k určitým sadám virtuální sítě.

Zavádíme také další požadavek, který Azure Firewall by měl kontrolovat větev na virtuální síť a provoz mezi virtuálními sítěmi, ale **ne**  síťový provoz virtuální sítě.  

Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Návrh

Aby bylo možné zjistit, kolik směrovacích tabulek bude potřeba, můžete vytvořit matici připojení. V tomto scénáři bude vypadat podobně jako v následujícím příkladu, kde každá buňka představuje, zda zdroj (řádek) může komunikovat s cílem (sloupec):

| Z | Do:| *Modrý virtuální sítě* | *Červené virtuální sítě* | *Červené větve*| *Modré větve*| 
|---|---|---|---|---|---|
| **Modrý virtuální sítě** |   &#8594;|   Direct     |           |   |  AzFW|
| **Červené virtuální sítě**  |   &#8594;|              |   Direct  |  AzFW  | 
| **Červené větve**   |   &#8594;|   |   AzFW  |  Direct | Direct
| **Modré větve**| &#8594;| AzFW  |   |Direct   | Direct

Každá z buněk v předchozí tabulce popisuje, jestli připojení k virtuální síti WAN (strana "od", záhlaví řádků) komunikuje s cílem (strana "do" toku, záhlaví sloupců jsou kurzívou). **Přímý** zahrnuje přenos toků přímo přes virtuální síť WAN, zatímco **AzFW** znamená, že se provoz před předáním do cílového umístění kontroluje Azure firewall. Prázdná položka znamená, že tok je v nastavení blokovaný.

V takovém případě se dvě směrovací tabulky pro virtuální sítě vyžadují k dosažení cíle izolace virtuální sítě bez Azure Firewall v cestě. Tyto tabulky tras budeme volat **RT_BLUE** a **RT_RED**.

Kromě toho musí být větve vždy přidruženy k  **výchozí** směrovací tabulce. Aby se zajistilo, že provoz do a z větví Azure Firewall, přidáme ve **výchozím nastavení** statické trasy **RT_RED** a **RT_BLUE** směrovacích tabulek, které odkazují na provoz do Azure firewall, a nastavíte Síťová pravidla, která povolují požadovaný provoz. Také zajišťujeme, **aby se větve nerozšířily** na **RT_BLUE** a **RT_RED**.

V důsledku toho je to konečný návrh:

* Modré virtuální sítě:
  * Přidružená směrovací tabulka: **RT_BLUE**
  * Rozšiřování do směrovacích tabulek: **RT_BLUE**
* Červené virtuální sítě:
  * Přidružená směrovací tabulka: **RT_RED**
  * Rozšiřování do směrovacích tabulek: **RT_RED** 
* Zřizování
  * Přidružená tabulka směrování: **výchozí**
  * Rozšiřování do směrovacích tabulek: **výchozí**
* Statické trasy:
    * **Výchozí směrovací tabulka**: Virtual Network adresní prostory s dalším segmentem směrování Azure firewall
    * **RT_RED**: 0.0.0.0/0 s dalším segmentem směrování Azure firewall
    * **RT_BLUE**: 0.0.0.0/0 s dalším segmentem směrování Azure firewall
* Síťová pravidla brány firewall:
    * **Povolení** **předpony zdroje** pravidla: adresa modrý předpona adresy **cíle**: modré předpony virtuální sítě 
    * **Povolení**  **předpony zdroje** pravidla: Předpona adresy červené větve předpony **cíle**: červená předpony virtuální sítě

> [!NOTE]
> Vzhledem k tomu, že jsou všechny větve nutné přidružit k výchozí směrovací tabulce a rozšířit na stejnou sadu směrovacích tabulek, budou mít všechny větve stejný profil připojení. Jinými slovy: koncept červeného/modrého pro virtuální sítě nelze použít na větve. Abychom ale mohli dosáhnout vlastního směrování pro větve, můžeme přesměrovat provoz z větví na Azure Firewall.

> [!NOTE]
> Azure Firewall ve výchozím nastavení zakazuje provoz v modelu s nulovým vztahem důvěryhodnosti. Pokud neexistuje žádné explicitní pravidlo **Povolení** , které by odpovídalo kontrolovanému paketu, Azure firewall tento paket vyřadí.

Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).



## <a name="workflow"></a><a name="architecture"></a>Pracovní postup

Na **obrázku 1** jsou modré a červené virtuální sítě a větve, které mají přístup k modrému nebo červenému virtuální sítě.

* Modrou připojená virtuální sítě se může vzájemně navázat a můžou se dostat ke všem modrým připojením (VPN/ER/P2S). V diagramu je modrá větev lokalitou VPN typu Site-to-site.
* Virtuální sítě připojené k červeným připojením se můžou vzájemně navázat a můžou se dostat ke všem připojením s červenými odbočkami (VPN/ER/P2S). V diagramu je červená větev uživatele sítě VPN typu Point-to-site.

Při nastavování směrování Vezměte v úvahu následující kroky.

1. Vytvořte dvě vlastní směrovací tabulky v Azure Portal **RT_BLUE** a **RT_RED** , abyste mohli přizpůsobit provoz těmto virtuální sítě.
2. V poli směrovací tabulka **RT_BLUE** použijte následující nastavení, abyste se ujistili, že se modré virtuální sítě učí předpony adres všech ostatních modrých virtuální sítě.:
   * **Asociace**: vyberte všechny modré virtuální sítě.
   * **Šíření**: vyberte všechny modré virtuální sítě.
3. Opakujte stejný postup pro **RT_RED** v tabulce směrování pro červenou virtuální sítě.
4. Zřídí Azure Firewall ve virtuální síti WAN. Další informace o Azure Firewall ve virtuálním centru WAN najdete v tématu [konfigurace Azure firewall ve virtuálním centru sítě WAN](howto-firewall.md).
5. Přidejte statickou trasu do **výchozí** směrovací tabulky virtuálního rozbočovače, který směruje veškerý provoz určený pro adresní prostory virtuální sítě (modrou i červeně) na Azure firewall. Tento krok zajistí, že se všechny pakety z větví budou posílat na Azure Firewall pro kontrolu.
    * Příklad: **předpona cíle**: 10.0.0.0/24 **Další segment směrování**: Azure firewall
    >[!NOTE]
    > Tento krok se dá udělat taky přes Správce brány firewall tak, že vyberete možnost zabezpečený privátní provoz. Tato akce přidá trasu pro všechny privátní IP adresy RFC1918 použitelné pro virtuální sítě a větve. Budete muset ručně přidat do všech větví nebo virtuálních sítí, které nedodržují předpisy RFC1918. 

6. Přidejte statickou trasu, která **RT_RED** a **RT_BLUE** směruje veškerý provoz do Azure firewall. Tento krok zajistí, že virtuální sítě nebude moci přistupovat k větvím přímo. Tento krok nelze provést pomocí Správce brány firewall, protože tyto virtuální sítě nejsou přidruženy k výchozí směrovací tabulce.
    * Příklad: **předpona cíle**: 0.0.0.0/0 **Další segment směrování**: Azure firewall

    > [!NOTE]
    > Směrování se provádí pomocí nejdelší shody předpony (v systému). V důsledku **toho nebudou statické** trasy 0.0.0.0/0 upřednostňovány nad přesnými předponami, které existují v **BLUE_RT** a **RED_RT**. V důsledku toho se Azure Firewall nekontrolují provoz uvnitř virtuální sítě.

Výsledkem bude, že se změní konfigurace směrování, jak je vidět na následujícím obrázku.

**Obrázek 1** 
 [ ![ Obrázek 1 ](./media/routing-scenarios/custom-branch-vnet/custom-branch.png)](./media/routing-scenarios/custom-branch-vnet/custom-branch.png#lightbox)

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
