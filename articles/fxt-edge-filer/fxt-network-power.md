---
title: 'Kurz: připojení kabelů k Azure FXT Edge souborového'
description: Jak zapojovat síťové porty a připojit napájení pro Azure FXT Edge souborového hardware
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 80458d99ed6ad19c8a837f0989798d4dcc156b61
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219526"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Kurz: vytvoření síťových připojení a napájení do uzlu Azure FXT Edge souborového

V tomto kurzu se dozvíte, jak kabelové připojení k síti pro uzel FXT Edge souborového pro Azure.

V tomto kurzu se dozvíte:

> [!div class="checklist"]
>
> * Jak zvolit typ síťového kabelu pro vaše prostředí
> * Jak připojit uzel Azure FXT Edge souborového k síti datového centra
> * Postup směrování kabelů přes ARM pro správu kabelů (CMA)
> * Jak připojit napájení k zamontovanému zařízení a zapnout ho

## <a name="prerequisites"></a>Předpoklady

Před zahájením tohoto kurzu by se Azure FXT Edge souborového měl nainstalovat do racku Standard Equipment. CMA by měl být nainstalovaný na uzlu souborového.

## <a name="identify-ports"></a>Identifikace portů

Identifikujte různé porty na zadní straně Azure FXT Edge souborového.

![Zpětně zapojené zařízení](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Zapojení kabeláže zařízení

* Připojte porty RJ-45 k síťovému zdroji datového centra, jak je popsáno v části [síťové porty](#network-ports).  
* [Port iDRAC](#idrac-port) bezpečně připojte k samostatné síti pomocí zabezpečeného serveru DHCP.
* Pomocí portů USB a portu VGA připojte klávesnici a monitor k uzlu pro počáteční nastavení. Musíte spustit uzel a [nastavit počáteční heslo](fxt-node-password.md) pro aktivaci dalších portů uzlu. Podrobnosti si můžete přečíst v tématu [nastavení počátečních hesel](fxt-node-password.md) .

Tento článek také popisuje, jak [propojit](#connect-power-cables) elektrickou energii pro uzel.

Tento článek také vysvětluje, jak se připojit k [sériovému portu](#serial-port-only-when-necessary)uzlu, pokud je to nutné pro specializované řešení potíží.

### <a name="network-ports"></a>Síťové porty

Každý uzel souborového pro Azure FXT Edge zahrnuje tyto síťové porty:

* Šest vysokorychlostních 25GbE/10GbE datových portů s duální rychlostí:

  * Čtyři porty poskytované dvěma síťovými adaptéry modulu plug-in se dvěma porty
  * Dva porty poskytované Mezzanine síťovým adaptérem základní desky

* Dva porty 10 GbE LOM, které poskytuje síťový adaptér Mezzanine základní desky

Vysokorychlostní datové porty 25GbE/10GbE mají standardní klece kompatibilní s SFP28. Aby bylo možné používat optické kabely, je nutné nainstalovat moduly SFP28 optický vysílač (nejsou k dispozici).

Porty 10 GbE LOM mají standardní konektory RJ-45.

Úplný seznam podporovaných kabelů, přepínačů a vysílačů najdete v části [Cavium FastlinQ 41000 – matice interoperability](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).

Typ připojení, která se mají použít pro váš systém, závisí na vašem prostředí datového centra.

* Pokud se připojujete k síti 25GbE, připojte každý z vysokorychlostních datových portů k jednomu z následujících typů kabelů:

  * Optický kabel a SFP28 optický vysílač s možností 25GbE nebo dual rate 25GbE/10GbE
  * SFP28 typ 25GbE s přímým připojením twinaxial kabelem

* Pokud se připojujete k síti 10GbE, připojte každý z vysokorychlostních datových portů k jednomu z následujících způsobů:

  * Optický kabel a SFP28 optický vysílač s možností 10GbE nebo dual rate 25GbE/10GbE.
  * SFP28 typ 25GbE s přímým připojením twinaxial kabelem
  * SFP28 typ 10GbE s přímým připojením twinaxial kabelem

* Porty sítě 10 GbE LOM se používají pro provoz správy clusteru. Pokud chcete vytvořit fyzicky oddělenou síť pro konfiguraci clusteru (popis najdete v tématu [Konfigurace sítě pro správu](fxt-cluster-create.md#configure-the-management-network)), při vytváření clusteru **použijte možnost použít síť pro správu s GB** /s. Zapojte porty se standardním CAT5 nebo lepším kabelem, jak je popsáno v seznamu podporované kabely.

  Porty 10 GbE LOM můžete nechat nekabelované, pokud plánujete používat vysokorychlostní porty pro veškerý provoz. Ve výchozím nastavení se porty sítě 10 GbE LOM nepoužívají, pokud je k dispozici vysokorychlostní datový port.  

### <a name="idrac-port"></a>Port iDRAC  

Port označený iDRAC je připojení s 1 GB, které umožňuje komunikaci s řadičem vzdáleného přístupu, který se používá ke správě a monitorování hardwaru. FXT software používá rozhraní IPMI (Intelligent Platform Management Interface) s tímto kontrolérem k odstraňování potíží a obnovení. Pomocí integrovaného [rozhraní iDRAC](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) můžete monitorovat hardware prostřednictvím tohoto portu. přístup iDRAC a IPMI jsou ve výchozím nastavení povolené.

> [!Note]
> Port iDRAC může obejít operační systém a komunikovat přímo s hardwarem v uzlu.

Tyto strategie zabezpečení použijte při připojování a konfiguraci portu iDRAC:

* Připojte jenom porty iDRAC k síti, která je fyzicky oddělená od datové sítě, která se používá pro přístup ke clusteru.
* V každém uzlu nastavte heslo správce zabezpečení iDRAC. Je nutné nastavit toto heslo pro aktivaci hardwaru – postupujte podle pokynů v části [Nastavení hardwarových hesel](fxt-node-password.md).
* Výchozí konfigurace portu iDRAC používá pro přiřazení IP adresy DHCP a IPv4. Ujistěte se, že je prostředí DHCP správně chráněné a že je mezi klienty DHCP a serverem DHCP omezené připojení. (Ovládací panel clusteru obsahuje nastavení, která mění způsob konfigurace adresy uzlů po vytvoření clusteru.)
* Ponechte port iDRAC nastavený na "Vyhrazený režim" (výchozí), což omezí síťový provoz iDRAC/IPMI na vyhrazený port RJ-45.

Port iDRAC nevyžaduje vysokorychlostní síťové připojení.
  
### <a name="serial-port-only-when-necessary"></a>Sériový port (pouze v případě potřeby)

V některých situacích vám může služba a podpora Microsoftu sdělit, že se k sériovému portu uzlu připojí terminál, aby mohl diagnostikovat problém.

Postup připojení konzoly:

1. Vyhledejte sériový port (COM1) na zadní straně uzlu FXT Edge souborového.
1. K připojení sériového portu k terminálu nakonfigurovanému pro ANSI-115200-8N1 použijte kabel nulového modemu.
1. Přihlaste se ke konzole a proveďte další kroky podle pokynů pracovníků podpory.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Směrování kabelů v ARM pro správu kabelů (CMA)

Každý uzel souborového pro Azure FXT Edge se dodává s volitelnou ARM pro správu kabelů. CMA zjednodušuje směrování kabelů a poskytuje snadný přístup k back-skříni, aniž by bylo nutné odpojit kabely.

Při směrování kabelů prostřednictvím CMA postupujte podle těchto pokynů:

1. Pomocí zadaných kabelů můžete seskupit kabely společně při zadávání a ukončování košíků, aby nedošlo ke konfliktu se sousedními systémy (1).
1. Pomocí CMA ve službě poloha služby směrujte kabelovou sadu přes vnitřní a vnější koše (2).
1. Pomocí předinstalovaného zavěšení a popruhů cyklů na obou koncích zabezpečíte kabely (3).
1. CMA zpátky na místo na zásobníku (4).
1. Nainstalujte kabel indikátoru stavu na zadní straně systému a zapojte kabel pomocí směrování přes CMA. Druhý konec kabelu připojte k rohu vnějšího košíku CMA (5).

   > [!CAUTION]
   > Aby nedocházelo k potenciálním škodám vyčnívajících kabelů, zabezpečte jakoukoli časovou rezervu na kabelu indikátoru stavu po směrování tohoto kabelu přes CMA.

![Obrázek CMA s nainstalovanými kabely](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
> Pokud jste nenainstalovali CMA, použijte dva popruhy zavěšení a cyklů, které jsou k dispozici v sadě kolejnice, ke směrování kabelů na zadní straně systému.
>
>  1. Vyhledá vnější CMA závorky na vnitřních stranách obou přírub na racku.
>  2. Rozbalení kabelů za mírné a jejich navýšení ze systémových konektorů na levou a pravou stranu.
>  3. Rozbalí zavěšení a smyčku pomocí pruhů nástrojů na vnějších CMA závorkách na každé straně systému, aby se zabezpečily kabelové sady.
>
>     ![Kabely směrované bez CMA](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>Požadavky na IP adresu

V případě hardwarových uzlů v mezipaměti hybridního úložiště Azure FXT Edge souborového spravuje software clusteru IP adresy.

Každý uzel vyžaduje aspoň jednu IP adresu, ale adresy uzlů se přiřazují při přidávání nebo odebírání uzlů z clusteru.

Celkový počet požadovaných IP adres závisí na počtu uzlů v, které tvoří mezipaměť.

Po instalaci uzlů nakonfigurujte rozsah IP adres pomocí softwaru ovládacích panelů. Další informace najdete v článku [shromáždění informací o clusteru](fxt-cluster-create.md#gather-information-for-the-cluster).  

## <a name="connect-power-cables"></a>Připojení napájecích kabelů

Každý uzel souborového pro Azure FXT Edge používá dva jednotky napájení (PSUs).

> [!TIP]
> Pokud chcete využít výhod těchto dvou redundantních PSUs, připojte každý kabel elektrické energie k jednotce pro distribuci napájení (PDU) na nezávislém okruhu větví.
>
> K napájení jednotek PDU pro dodatečnou ochranu můžete použít UPS.

1. Připojte zahrnuté napájecí šňůry k PSUs na skříni. Ujistěte se, že jsou šňůry a PSUs plně upevněné.
1. Připojte napájecí šňůry k jednotkám distribuce napájení v racku zařízení. Pokud je to možné, použijte dva samostatné zdroje napájení pro tyto dva šňůry.

### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Zapnutí uzlu Azure FXT Edge souborového

Pokud chcete uzel zapnout, stiskněte tlačítko napájení na přední straně systému. Tlačítko je na ovládacím panelu na pravé straně.

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Vypnutí uzlu Azure FXT Edge souborového

Tlačítko napájení se dá použít k vypnutí systému během testování a před jeho přidáním do clusteru. Až se ale uzel Azure FXT Edge souborového používá jako součást clusteru, měli byste k vypnutí hardwaru použít software na ovládacím panelu clusteru. Podrobnosti najdete v článku [jak bezpečně vypnout hardware Azure FXT Edge souborového](fxt-power-off.md) .

## <a name="next-steps"></a>Další kroky

Po dokončení práce s hardwarem zapněte všechny uzly a inicializujte je nastavením jejich kořenových hesel.
> [!div class="nextstepaction"]
> [Nastavit počáteční hesla](fxt-node-password.md)
