---
title: Připojení k síti Microsoft Azure FXT hrany vyfiltrovat a napájení
description: Jak zapojení síťové porty a připojit power Azure FXT hrany Filer hardwaru
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 444835a94559a352bfd749cfa1cb2cd8c3a39373
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450307"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Kurz: Zkontrolujte připojení k síti a napájení Azure FXT hrany Filer uzlu

Tento kurz vás naučí, jak zapojení síťová připojení pro uzlu Azure FXT hrany Filer hardwaru.

V tomto kurzu se dozvíte: 

> [!div class="checklist"]
> * Jak zvolit typ síťového kabelu pro vaše prostředí
> * Jak se připojit k síti datového centra do Azure FXT hrany Filer uzlu
> * Jak směrovat kabely přes arm správy kabel (CMA)
> * Jak se připojit power racked zařízení a zapnout ho

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu musí být nainstalován Filer Azure FXT hrany v racku standardní zařízení. CMA musí být nainstalován na uzlu vyfiltrovat. 

## <a name="identify-ports"></a>Identifikace portů

Identifikujte různé porty na pozadí vaší Azure FXT hrany vyfiltrovat. 
 
![Zadní poskytuje zařízení](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Zapojení kabeláže zařízení

* Připojit porty RJ-45 ke zdroji sítě datového centra, jak je popsáno v [síťovým portům](#network-ports).  
* Připojte se bezpečně [iDRAC port](#idrac-port) k oddělení síti, pomocí zabezpečeného serveru DHCP. 
* Použití portů USB a VGA port pro připojení k uzlu pro počáteční nastavení klávesnice a monitor. Je třeba spustit na uzel a [nastavit počáteční heslo](fxt-node-password.md) aktivovat uzel v jiné porty. Čtení [nastavení počátečního hesla](fxt-node-password.md) podrobnosti. 

Tento článek také popisuje, jak [připojit napájení ze sítě](#connect-power-cables) pro uzel. 

Tento článek také vysvětluje, jak se připojit k uzlu [sériového portu](#serial-port-only-when-necessary), ale sériového portu se používá jenom pro řešení potíží pro pokročilé. 

### <a name="network-ports"></a>Síťové porty 

Každý uzel Azure FXT hrany Filer zahrnuje následující síťové porty: 

* Šest vysokorychlostní 25GbE/10GbE duální míra data porty: 

  * Čtyři portů poskytuje dva modulu plug-in dvouportové síťové adaptéry
  * Dva porty, které poskytuje základní desky mezzanine síťový adaptér 

* Dva 1GbE porty, které poskytuje základní desky mezzanine síťový adaptér 

Porty vysokorychlostní 25GbE/10GbE data mají klece standardní SFP28 kompatibilní. Použití optické kabely, je nutné nainstalovat moduly SFP28 optické vysílač (není k dispozici).

Porty 1GbE mají standardní konektory, RJ-45.

Úplný seznam podporovaných kabely, přepínače a vysílače, naleznete [Cavium FastlinQ 41000 řady Interoperability matice](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).

Typ připojení pro použití pro váš systém závisí na prostředí vašeho datového centra.

* Připojení k síti 25GbE zapojení každý z portů vysokorychlostní dat s jednou z následujících typů kabel:

  * Optické kabely a SFP28 optické vysílač 25GbE nebo duální míra 25GbE/10GbE funkce
  * Přímé podporující 25GbE SFP28 typ připojte kabel twinaxial

* Pokud připojení k síti 10GbE, zapojení každý z portů vysokorychlostní dat s jednou z následujících akcí: 

  * Optické kabely a SFP28 optické vysílač 10GbE nebo duální míra 25GbE/10GbE funkce.
  * Přímé podporující 25GbE SFP28 typ připojte kabel twinaxial
  * Přímé podporující 10GbE SFP28 typ připojte kabel twinaxial

* 1GbE síťové porty se používají pro přenos pro správu clusteru. Zkontrolujte **použít síť mgmt 1Gb** možnosti při vytváření clusteru (popsané v [konfigurace sítě pro správu](fxt-cluster-create.md#configure-the-management-network)). Porty s standardní Cat5 nebo lepší kabel, jak je popsáno v seznamu podporovaných kabely zapojení.

  Můžete nechat 1GbE porty uncabled, pokud hodláte použít vysokorychlostní porty pro veškerý provoz. Ve výchozím nastavení 1GbE síťové porty nepoužívají, pokud je k dispozici vyšší rychlost portu data.  

### <a name="idrac-port"></a>iDRAC port  

Port s popiskem iDRAC je 1Gb připojení, která umožňuje komunikaci s řadičem vzdálený přístup používá ke správě hardwaru a monitorování. FXT software Intelligent Platform Management Interface (IPMI) používá k tomuto kontroleru pro řešení potíží a obnovení. Můžete použít předdefinované [iDRAC rozhraní](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) ke sledování hardwarových přes tento port. ve výchozím nastavení jsou povolené iDRAC a přístup k rozhraní IPMI. 

> [!Note]
> IDRAC port můžete vynechat operačního systému a pracovat přímo s hardwarem v uzlu. 

Pomocí těchto strategií zabezpečení při připojení a konfiguraci iDRAC portu:

* Porty iDRAC připojte jenom k síti, která je fyzicky oddělená od data sítě, která slouží pro přístup ke clusteru.
* Nastavení hesla správce zabezpečeného iDRAC na každém uzlu. Je nutné nastavit toto heslo k aktivaci hardwaru – postupujte podle pokynů v [nastavit hesla hardwaru](fxt-node-password.md).
* Výchozí konfiguraci portů iDRAC používá pro přiřazování IP adres DHCP a IPv4. Ujistěte se, že vaše prostředí DHCP je dobře chráněné a omezeny připojení mezi klienty a DHCP server. (Ovládací panely clusteru zahrnuje nastavení můžete změnit způsob konfigurace adresy uzlů po vytvoření clusteru.)
* Ponechte iDRAC port nastaven na "vyhrazené režim" (výchozí), což omezuje iDRAC/IPMI síťový provoz na vyhrazený port, RJ-45.

IDRAC port nevyžaduje vysokorychlostní síťové připojení.
  
### <a name="serial-port-only-when-necessary"></a>Sériový port (pouze v případě potřeby)

V některých situacích můžete zjistit Microsoft Service a podporu můžete připojit k uzlu sériového portu k diagnostice problému terminálu.  

Pro připojení konzoly:

1. Vyhledání sériového portu (COM1) na zadní Filer FXT hraniční uzel.
1. Pro připojení k terminálu nakonfigurovaný pro ANSI. přenosovou rychlostí 115 200 8N1 sériového portu pomocí kabelu null modemu.
1. Přihlaste se ke konzole a provést další kroky, podle pracovníky podpory.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Trasa kabelů v arm správy kabel (CMA)

Každý uzel Azure FXT hrany Filer součástí na starosti správu volitelné kabel. CMA zjednodušuje kabel směrování a poskytuje jednodušší přístup k zadní skříni aniž by bylo potřeba odpojit kabely. 

Postupujte podle těchto pokynů a kabely prostřednictvím CMA trasy: 

1. Pomocí zabalí tie k dispozici, pohromadě kabely podle jejich vstupní a výstupní košíky tak, aby jejich nejsou v konfliktu s sousední systémy (1).
1. S CMA v umístění služby směrování sady kabel košíky vnitřní a vnější (2).
1. Použijte předinstalovaných popruhů hook a smyčky k zabezpečení kabely (3) na jednom konci koše.
1. Postupná CMA zpět do místa na hlavním panelu (4).
1. Použití kabelu indikátor předinstalovaných stav zádi systém a zabezpečení kabel přesměrováním prostřednictvím CMA. Připojte druhém konci kabel do horního rohu vnějšího nákupní košík CMA (5). 

   > [!CAUTION]
   > Aby se zabránilo potenciální poškození vyčnívající kabely, zabezpečte všechny slack kabel indikátor stavu po směrování tento kabel prostřednictvím CMA. 

![Obrázek CMA s kabely nainstalovaná](media/cma-cabling-scan-400.png)

> [!NOTE]
>  Pokud jste nenainstalovali CMA, použít dva zavěšení a opakovat popruhů k dispozici v sadě lišty směrovat kabely zádi vašeho systému.
> 
>  1. Vnější závorky CMA vyhledejte na vnitřní stranách obou přírub stojanu.
>  2. Vytvoření balíčku kabely jemně, přijímání změn je vymazat systému konektorů pro levé a pravé straně.
>  3. Vlákno hook a smyčka popruhů prostřednictvím tooled sloty na vnější závorky CMA na každé straně systém a zabezpečení sady kabel.
> 
>     ![Směrovat bez CMA kabely](media/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>O požadavcích na IP adresu

Pro hardware do uzlů do mezipaměti Azure FXT hrany Filer hybridního úložiště IP adres spravuje software clusteru.

Každý uzel vyžaduje alespoň jednu IP adresu, ale uzel adresy se přiřazují při uzly jsou přidán či odebrán z clusteru. 

Celkový počet IP adres vyžaduje závisí na počtu uzlů, které tvoří mezipaměť. 

Nakonfigurujte rozsah IP adres pomocí ovládacích panelů softwaru po nainstalování uzly. Další informace najdete v článku [shromažďování informací o clusteru](fxt-cluster-create.md#gather-information-for-the-cluster).  

## <a name="connect-power-cables"></a>Připojit napájecích kabelů

Každý uzel Azure FXT hrany Filer používá dvě jednotky dodávku napájení (PSUs). 

> [!TIP] 
> Výhod dvou PSUs redundantní připojení k jednotka distribuční napájení (PDU) na okruh nezávislé větve každý AC napájecí kabel.  
> 
> Vám pomůže UPS power PDU pro zvláštní ochranu. 

1. Zahrnuté napájecích se připojte k PSUs ve skříni. Ujistěte se, že jsou plně sedí kabelů a PSUs. 
1. Připojte napájecích kabelů k jednotek pro distribuci napájení na rack zařízení. Pokud je to možné použijte dva samostatné napájecí zdroje pro dvě kabely. 
 
### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Zapnutí do Azure FXT hrany Filer uzlu

Napájení uzlu, stisknutím tlačítka napájení na začátku systému. Tlačítko je na pravé straně ovládací panely. 

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Napájení vypnuto uzlu Azure FXT hrany vyfiltrovat

Tlačítko napájení slouží k vypnutí systému při testování a před přidáním do clusteru. Ale po uzlu Azure FXT hrany Filer se používá jako součást clusteru, by měl použít software panel ovládacího prvku clusteru vypnout hardwaru. Čtení [jak bezpečně vypnutí hardware Azure FXT hrany Filer](fxt-power-off.md) podrobnosti. 

## <a name="next-steps"></a>Další postup

Po dokončení kabelů hardwaru, napájení na každém uzlu a inicializovat nastavením kořenového hesla. 
> [!div class="nextstepaction"]
> [Nastavení počátečního hesla](fxt-node-password.md)
