---
title: 'Kurz: Připojení kabelů k azure FXT edge fileru'
description: Jak kabelovat síťové porty a připojit napájení hardwaru Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 1e3c60fd955bd899955c46b7670acd3f088d0f86
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239781"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Kurz: Navazují síťová připojení a napájejí se do uzlu Azure FXT Edge Filer

Tento kurz vás naučí, jak kabel připojení k síti pro uzel hardwaru Azure FXT Edge Filer.

V tomto kurzu se dozvíte: 

> [!div class="checklist"]
> * Jak si vybrat typ síťového kabelu pro vaše prostředí
> * Jak připojit uzel Azure FXT Edge Filer k síti datového centra
> * Jak směrovat kabely přes rameno pro vedení kabelů (CMA)
> * Jak připojit napájení k rackovanému zařízení a zapnout jej

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu by měl být Azure FXT Edge Filer nainstalován do standardního racku vybavení. CMA by měla být nainstalována na filer uzlu. 

## <a name="identify-ports"></a>Identifikace portů

Identifikujte různé porty na zadní straně vašeho Azure FXT Edge Filer. 
 
![Zadní část kabelového zařízení](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Zapojení kabeláže zařízení

* Připojte porty RJ-45 ke zdroji sítě datového centra, jak je popsáno v [síťovém portu](#network-ports).  
* Bezpečně připojte [port iDRAC](#idrac-port) k samostatné síti se zabezpečeným serverem DHCP. 
* Pomocí portů USB a portu VGA připojte klávesnici a monitor k uzlu pro počáteční nastavení. Je nutné spustit uzel a [nastavit počáteční heslo](fxt-node-password.md) pro aktivaci ostatních portů uzlu. Podrobnosti načláneknete [na článek Nastavení počátečních hesel.](fxt-node-password.md) 

Tento článek také popisuje, jak [připojit napájení střídavého proudu](#connect-power-cables) pro uzel. 

Tento článek také vysvětluje, jak se připojit k [sériovému portu](#serial-port-only-when-necessary)uzlu , v případě potřeby pro specializované řešení potíží. 

### <a name="network-ports"></a>Síťové porty 

Každý uzel Azure FXT Edge Filer obsahuje následující síťové porty: 

* Šest vysokorychlostních dvourychlostních datových portů 25GbE/10GbE: 

  * Čtyři porty poskytované dvěma síťovými adaptéry pro plug-in se dvěma porty
  * Dva porty poskytované síťovým adaptérem mezzanine základní desky 

* Dva porty 1GbE poskytované síťovým adaptérem mezzanine základní desky 

Vysokorychlostní datové porty 25GbE/10GbE mají standardní klece kompatibilní s SFP28. Chcete-li používat optické kabely, musíte nainstalovat moduly optického vysílače SFP28 (nejsou k dispozici).

Porty 1GbE mají standardní konektory RJ-45.

Úplný seznam podporovaných kabelů, přepínačů a vysílačů naleznete v [matici interoperability řady Cavium FastlinQ 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).

Typ připojení, která se mají pro váš systém používat, závisí na prostředí datového centra.

* Pokud se připojujete k síti 25GbE, zadejte kabel pro každý z vysokorychlostních datových portů jedním z následujících typů kabelů:

  * Optický kabel a optický vysílač SFP28 s možností 25GbE nebo duální 25GbE/10GbE
  * SFP28 typ 25GbE-schopný přímé připojení twinaxial kabel

* Pokud se připojujete k síti 10GbE, zadejte kabel na každý z vysokorychlostních datových portů jedním z následujících: 

  * Optický kabel a optický vysílač SFP28 s možností 10GbE nebo dvousouhed 25GbE/10GbE.
  * SFP28 typ 25GbE-schopný přímé připojení twinaxial kabel
  * SFP28 typ 10GbE-schopný přímý připojit twinaxial kabel

* Síťové porty 1GbE se používají pro provoz správy clusteru. Chcete-li vytvořit fyzicky samostatnou síť pro konfiguraci clusteru , zaškrtněte možnost **Použít síť Mgmt 1 Gb** při vytváření clusteru (popsané v části Konfigurace sítě pro [správu).](fxt-cluster-create.md#configure-the-management-network) Kabelporty se standardním Cat5 nebo lepší kabel, jak je popsáno v seznamu podporovaných kabelů.

  Porty 1GbE můžete ponechat bez kabelů, pokud plánujete použít vysokorychlostní porty pro veškerý provoz. Ve výchozím nastavení se síťové porty 1GbE nepoužívají, pokud je k dispozici datový port s vyšší rychlostí.  

### <a name="idrac-port"></a>port iDRAC  

Port s označením iDRAC je 1Gb připojení, které umožňuje komunikaci s řadičem vzdáleného přístupu používaným pro správu a monitorování hardwaru. Fxt software používá rozhraní IPMI (Intelligent Platform Management Interface) s tímto ovladačem pro řešení problémů a obnovu. Integrované [rozhraní iDRAC](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) můžete použít ke sledování hardwaru prostřednictvím tohoto portu. přístup iDRAC a IPMI je ve výchozím nastavení povolen. 

> [!Note]
> Port iDRAC může obejít operační systém a komunikovat přímo s hardwarem v uzlu. 

Při připojování a konfiguraci portu iDRAC používejte tyto strategie zabezpečení:

* Připojte pouze porty iDRAC k síti, která je fyzicky oddělena od datové sítě používané pro přístup ke clusteru.
* Nastavte zabezpečené heslo správce iDRAC na každém uzlu. Chcete-li aktivovat hardware , musíte nastavit toto heslo – postupujte podle pokynů v části [Nastavit hardwarová hesla](fxt-node-password.md).
* Výchozí konfigurace portu iDRAC používá pro přiřazení adres IP server DHCP a IPv4. Zkontrolujte, zda je prostředí DHCP dobře chráněno a zda jsou omezena připojení mezi klienty DHCP a serverem DHCP. (Ovládací panel clusteru obsahuje nastavení pro změnu metody konfigurace adresy uzlů po vytvoření clusteru.)
* Port iDRAC ponechejte nastavený na "vyhrazený režim" (výchozí), který omezuje síťový provoz iDRAC/IPMI na vyhrazený port RJ-45.

Port iDRAC nevyžaduje vysokorychlostní síťové připojení.
  
### <a name="serial-port-only-when-necessary"></a>Sériový port (pouze v případě potřeby)

V některých situacích vám služba a podpora společnosti Microsoft může sdělit, abyste se připojili k terminálu k sériovému portu uzlu, abyste mohli diagnostikovat problém.  

Připojení konzoly:

1. Vyhledejte sériový port (COM1) na zadní straně uzlu FXT Edge Filer.
1. Pomocí kabelu nulového modemu připojte sériový port k terminálu nakonfigurovanému pro ANSI-115200-8N1.
1. Přihlaste se ke konzoli a podniknidalší kroky podle pokynů pracovníka podpory.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Směrové kabely v rameni pro vedení kabelů (CMA)

Každý uzel Azure FXT Edge Filer je dodáván s volitelným ramenem pro správu kabelů. CMA zjednodušuje vedení kabelů a poskytuje snadnější přístup k zadní části šasi bez nutnosti odpojovat kabely. 

Podle těchto pokynů pro směrování kabelů přes CMA: 

1. Pomocí zavazovacích obalů přispojujte kabely při vstupu do košů a vytoužejte je tak, aby nezasahovaly do sousedních systémů (1).
1. S CMA v servisní poloze veďte kabelový svazek přes vnitřní a vnější koše (2).
1. Kabely (3) používejte předinstalované popruhy na háček a smyčky na obou koncích košů.
1. Namisky (4) nahouťcma zpět na místo.
1. Nainstalujte kabel indikátoru stavu na zadní straně systému a zajistěte jej jeho nasměrováním přes CMA. Druhý konec kabelu připevněte k rohu vnějšího koše CMA (5). 

   > [!CAUTION]
   > Abyste zabránili možnému poškození vyčnívajícími kabely, zajistěte po nasměrování tohoto kabelu cma jakoukoli rezervu ve kabelu indikátoru stavu. 

![Ilustrace CMA s nainstalovanými kabely](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
>  Pokud jste cma nenainstalovali, použijte dva háky a smyčky, které jsou k dispozici v železniční soupravě, a vysměrujte kabely na zadní straně systému.
> 
>  1. Umístěte vnější cma držáky na vnitřní straně obou přírub racku.
>  2. Kabely jemně svěšte a vytáhněte je z konektorů systému na levou a pravou stranu.
>  3. Provlékněte hákové a smyčkové popruhy otvory na vnějších konzolách CMA na každé straně systému, abyste zajistili kabelové svazky.
> 
>     ![Kabely směrované bez CMA](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>Požadavky na IP adresu

U hardwarových uzlů v mezipaměti hybridního úložiště Azure FXT Edge Filer jsou IP adresy spravované softwarem clusteru.

Každý uzel vyžaduje alespoň jednu adresu IP, ale adresy uzlů jsou přiřazeny při přidání uzlů do clusteru nebo z něj. 

Celkový počet požadovaných IP adres závisí na počtu uzlů v mezipaměti. 

Po instalaci uzlů nakonfigurujte rozsah adres IP pomocí softwaru Ovládacích panelů. Další informace najdete v části [Shromáždit informace pro cluster](fxt-cluster-create.md#gather-information-for-the-cluster).  

## <a name="connect-power-cables"></a>Připojení napájecích kabelů

Každý uzel Azure FXT Edge Filer používá dvě napájecí zdroje (PSU). 

> [!TIP] 
> Chcete-li využít výhod dvou redundantních napájecích klíčů, připojte každý napájecí kabel k napájecí jednotce (PDU) na nezávislém větevovém obvodu.  
> 
> Ups můžete použít k napájení PDU pro extra ochranu. 

1. Připojte přiložené napájecí kabely k napájecím silám v šasi. Ujistěte se, že kabely a napájecí lyže jsou plně usazeny. 
1. Připojte napájecí kabely k napájecím jednotkám na stojanu zařízení. Pokud je to možné, použijte pro oba kabely dva samostatné zdroje energie. 
 
### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Zapnutí uzlu Azure FXT Edge Filer

Chcete-li uzel zapnout, stiskněte tlačítko napájení na přední straně systému. Tlačítko je na pravém ovládacím panelu. 

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Vypnutí uzlu Azure FXT Edge Filer

Tlačítko napájení lze použít k vypnutí systému během testování a před jeho přidáním do clusteru. Však po Azure FXT Edge Filer uzel se používá jako součást clusteru, měli byste použít software ovládacího panelu clusteru vypnout hardware. Podrobnosti načláneknou na článek [Jak bezpečně vypnout hardware Azure FXT Edge Filer.](fxt-power-off.md) 

## <a name="next-steps"></a>Další kroky

Po dokončení kabeláže hardwaru zapnite každý uzel a inicializujte je nastavením jejich kořenových hesel. 
> [!div class="nextstepaction"]
> [Nastavení počátečních hesel](fxt-node-password.md)
