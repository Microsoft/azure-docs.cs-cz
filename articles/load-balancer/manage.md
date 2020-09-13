---
title: Nastavení Azure Load Balancer portálu
description: Začínáme se seznámení s Azure Load Balancerm nastavením portálu
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/8/2020
ms.author: allensu
ms.openlocfilehash: e1080aea12e70f4312fbee07b063d5a5cfbd1201
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596278"
---
# <a name="azure-load-balancer-portal-settings"></a>Nastavení Azure Load Balancer portálu

Informace v tomto článku vám pomůžou při vytváření Azure Load Balancer. Další informace o jednotlivých nastaveních a o tom, jaká je správná konfigurace, najdete tady.

## <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Azure Load Balancer je nástroj pro vyrovnávání zatížení sítě, který distribuuje provoz mezi instancemi virtuálních počítačů ve fondu back-end.

### <a name="basics"></a>Základy

Na kartě **základy** stránky portál vytvořit nástroj pro vyrovnávání zatížení se zobrazí následující informace:



| Nastavení |  Podrobnosti |
| ---------- | ---------- |
| Předplatné  | Vyberte předplatné. Tato volba je předplatné, ve kterém chcete nasadit nástroj pro vyrovnávání zatížení. |
| Skupina prostředků | Vyberte **vytvořit nové** a do textového pole zadejte název vaší skupiny prostředků. Pokud máte vytvořenou stávající skupinu prostředků, vyberte ji. |
| Name | Toto nastavení je název vašeho Azure Load Balancer. |
| Region (Oblast) | Vyberte oblast Azure, ve které chcete nasadit nástroj pro vyrovnávání zatížení. |
| Typ | Nástroj pro vyrovnávání zatížení má dva typy: </br> **Interní (privátní)** </br> **Veřejné (externí)**.</br> Interní nástroj pro vyrovnávání zatížení (interního nástroje) směruje provoz do členů fondu back-end přes privátní IP adresu.</br> Veřejný Nástroj pro vyrovnávání zatížení směruje požadavky od klientů přes Internet do fondu back-end.</br> Přečtěte si další informace o [typech nástroje pro vyrovnávání zatížení](components.md#frontend-ip-configuration-).|
| SKU  | Vyberte **Standard**. </br> Load Balancer má dvě SKU: **Basic** a **Standard**. </br> Základní má omezené funkce. </br> Pro produkční úlohy se doporučuje **Standard** . </br> Přečtěte si další informace o [SKU](skus.md). |

Pokud jako typ vyberete **Public** , zobrazí se následující informace:

| Nastavení |  Podrobnosti |
| ---------- | ---------- |
| Veřejná IP adresa | Vyberte **vytvořit novou** a vytvořte veřejnou IP adresu pro svůj veřejný Nástroj pro vyrovnávání zatížení. </br> Pokud máte existující veřejnou IP adresu, vyberte **použít existující**.  |
| Název veřejné IP adresy | Název veřejné IP adresy.|
| SKU veřejné IP adresy | Veřejné IP adresy mají dvě SKU: **Basic** a **Standard**. </br> Základní nepodporuje atributy Zone-odolnost a Zona. </br> Pro produkční úlohy se doporučuje **Standard** . </br> Vyrovnávání zatížení a SKU veřejné IP adresy se **musí shodovat**. |
| Přiřazení | **Statická** je automaticky vybraná pro standard. </br> Základní veřejné IP adresy mají dva typy: **Dynamická** a **statická**. </br> Dynamické veřejné IP adresy se nepřiřazují až do vytvoření. </br> Pokud se prostředek odstraní, můžou se tyto IP adresy ztratit. </br> Doporučuje se statická IP adresa. |
| Zóna dostupnosti | Chcete-li vytvořit odolný Nástroj pro vyrovnávání zatížení, vyberte **zónu – redundantní** . </br> Pokud chcete vytvořit nástroj pro vyrovnávání zatížení, vyberte konkrétní zónu z **1**, **2**nebo **3**. </br> Standardní zóny pro vyrovnávání zatížení a podpora veřejných IP adres. </br> Přečtěte si další informace o [nástroji pro vyrovnávání zatížení a zónách dostupnosti](load-balancer-standard-availability-zones.md). </br> Pro základní nelze zobrazit výběr zóny. Nástroj pro vyrovnávání zatížení úrovně Basic nepodporuje zóny. |
| Přidat veřejnou IPv6 adresu | Nástroj pro vyrovnávání zatížení podporuje adresy **IPv6** pro váš front-end. </br> Další informace o [nástroji pro vyrovnávání zatížení a protokolu IPv6](load-balancer-ipv6-overview.md)
| Předvolba směrování | Vyberte **síť Microsoft**. </br> Síť Microsoftu znamená, že provoz se směruje přes globální síť Microsoft. </br> Internet znamená, že provoz se směruje přes síť poskytovatele internetových služeb. </br> Další informace o [předvolbách směrování](../virtual-network/routing-preference-overview.md)|

:::image type="content" source="./media/manage/create-public-load-balancer-basics.png" alt-text="Vytvořte veřejnou službu Vyrovnávání zatížení." border="true":::

Pokud vyberete **interní** v typu, zobrazí se následující informace:

| Nastavení |  Podrobnosti |
| ---------- | ---------- |
| Virtuální síť | Virtuální síť, ve které chcete, aby byla interní nástroj pro vyrovnávání zatížení součástí. </br> Soukromá IP adresa front-endu, kterou jste vybrali pro interní nástroj pro vyrovnávání zatížení, bude z této virtuální sítě. |
| Přiřazení IP adresy | Vaše možnosti jsou **statické** nebo **dynamické**. </br> Statická zajišťuje, že se IP adresa nemění. Dynamická IP adresa se může změnit. |
| Zóna dostupnosti | Máte tyto možnosti: </br> **Zóna redundantní** </br> **Zóna 1** </br> **Zóna 2** </br> **Zóna 3** </br> Pokud chcete vytvořit nástroj pro vyrovnávání zatížení, který je vysoce dostupný a odolný vůči selháním zóny dostupnosti, vyberte IP adresu **redundantní v zóně** . |

:::image type="content" source="./media/manage/create-internal-load-balancer-basics.png" alt-text="Vytvořte interní nástroj pro vyrovnávání zatížení." border="true":::

## <a name="frontend-ip-configuration"></a>Konfigurace IP adresy front-endu

IP adresa vašeho Azure Load Balancer. Je to kontaktní bod pro klienty. 

Můžete mít jednu nebo mnoho konfigurací protokolu IP front-endu. Pokud jste prošli částí základy výše, už jste vytvořili front-end pro váš nástroj pro vyrovnávání zatížení. 

Pokud chcete do nástroje pro vyrovnávání zatížení Přidat konfiguraci IP adresy front-endu, v Azure Portal klikněte na nástroj pro vyrovnávání zatížení, vyberte **Konfigurace IP adresy front-endu**a pak vyberte **+ Přidat**.

| Nastavení |  Podrobnosti |
| ---------- | ---------- |
| Name | Název vaší konfigurace IP adresy front-endu. |
| Verze protokolu IP | Verze IP adresy, na kterou se má vaše front-end vymezit. </br> Load Balancer podporuje konfigurace IP adresy front-endu IPv4 i IPv6. |
| Typ IP adresy | Typ IP adresy určuje, jestli je jedna IP adresa přidružená k front-endu nebo rozsahu IP adres s použitím předpony IP adresy. </br> Pokud se potřebujete opakovaně připojit ke stejnému koncovému bodu, pomůže vám [předpona veřejných IP adres](../virtual-network/public-ip-address-prefix.md) . Předpona zajišťuje dostatek portů, které pomáhají s problémy s porty SNAT. |
| Veřejná IP adresa (nebo předpona, pokud jste vybrali předponu výše) | Pro front-endu nástroje pro vyrovnávání zatížení vyberte nebo vytvořte novou veřejnou IP adresu (nebo předponu). |

:::image type="content" source="./media/manage/frontend.png" alt-text="Vytvoří stránku konfigurace IP adresy front-endu." border="true":::

## <a name="backend-pools"></a>Back-endové fondy

Fond adres back-endu obsahuje IP adresy rozhraní virtuální sítě ve fondu back-endu. 

Pokud chcete do svého nástroje pro vyrovnávání zatížení přidat back-end fond, v Azure Portal klikněte na nástroj pro vyrovnávání zatížení, vyberte **back-endové fondy**a pak vyberte **+ Přidat**.

| Nastavení | Podrobnosti |
| ---------- |  ---------- |
| Name | Název back-end fondu. |
| Virtuální síť | Virtuální síť, ve které jsou instance back-endu. |
| Verze protokolu IP | Vaše možnosti jsou **protokolu IPv4** nebo **IPv6**. |

Virtuální počítače nebo sady škálování virtuálních počítačů můžete přidat do fondu back-end vašeho Azure Load Balancer. Nejdřív vytvořte virtuální počítače nebo sadu škálování virtuálního počítače. Pak je přidejte do nástroje pro vyrovnávání zatížení na portálu.

:::image type="content" source="./media/manage/backend.png" alt-text="Stránka vytvořit fond back-endu" border="true":::

## <a name="health-probes"></a>Sondy stavu

Sonda stavu se používá k monitorování stavu virtuálních počítačů nebo instancí back-endu. Stav sondy stavu určuje, kdy se nová připojení odesílají do instance na základě kontrol stavu. 

Pokud chcete přidat sondu stavu do nástroje pro vyrovnávání zatížení, v Azure Portal klikněte na nástroj pro vyrovnávání zatížení, vyberte **sondy stavu**a pak vyberte **+ Přidat**.

| Nastavení | Podrobnosti |
| ---------- | ---------- |
| Name | Název testu stavu. |
| Protokol | Vybraný protokol Určuje typ kontroly, který určí, zda jsou instance back-endu v dobrém stavu. </br> Máte tyto možnosti: </br> **TCP** </br> **HTTPS** </br> **HTTP** </br> Ujistěte se, že používáte správný protokol. Tento výběr bude záviset na povaze aplikace. </br> Konfigurace sondy stavu a odezvy sondy určují, které instance fondu back-end budou dostávat nové toky. </br> Sondy stavu můžete použít k detekci selhání aplikace na koncovém bodu back-endu. </br> Přečtěte si další informace o [sondách stavu](load-balancer-custom-probe-overview.md). |
| Port | Cílový port pro sondu stavu. </br> Toto nastavení je portem instance back-endu, pomocí které se sonda stavu zjišťuje stav instance. |
| Interval | Počet sekund mezi pokusy o sondy. </br> Interval určí, jak často se sonda stavu pokusí spojit s instancí back-endu. </br> Vyberete-li možnost 5, bude druhý pokus o testování proveden po 5 sekundách a tak dále. |
| Prahová hodnota pro poškozený stav | Počet po sobě jdoucích selhání sondy, ke kterým musí dojít, aby se virtuální počítač považoval za špatný.</br> Pokud vyberete 2, žádné nové toky se po dvou po sobě jdoucích selhání nenastaví na tuto instanci back-endu. |

:::image type="content" source="./media/manage/health-probe.png" alt-text="Přidejte sondu stavu." border="true":::

## <a name="load-balancing-rules"></a>Pravidla vyrovnávání zatížení

Definuje způsob distribuce příchozího provozu do všech instancí v rámci fondu back-endu. Pravidlo vyrovnávání zatížení mapuje danou konfiguraci IP adresy front-endu a port na více IP adres a portů back-endu.

Chcete-li do nástroje pro vyrovnávání zatížení přidat pravidlo nástroje pro vyrovnávání zatížení, v Azure Portal klikněte na nástroj pro vyrovnávání zatížení, vyberte možnost **pravidla vyrovnávání zatížení**a pak vyberte **+ Přidat**.
    
| Nastavení | Podrobnosti |
| ---------- | ---------- |
| Name | Název pravidla nástroje pro vyrovnávání zatížení. |
| Verze protokolu IP | Vaše možnosti jsou **protokolu IPv4** nebo **IPv6**.  |
| IP adresa front-endu | Vyberte IP adresu front-endu. </br> Front-end IP adresa vašeho nástroje pro vyrovnávání zatížení, ke kterému se má přidružit pravidlo nástroje pro vyrovnávání zatížení.|
| Protokol | Azure Load Balancer je síťový nástroj pro vyrovnávání zatížení vrstvy 4. </br> Máte tyto možnosti: **TCP** nebo **UDP**. |
| Port | Toto nastavení je port přidružený k IP adrese front-endu, pro který chcete provoz distribuovat na základě tohoto pravidla vyrovnávání zatížení. |
| Back-endový port | Toto nastavení je port instancí ve fondu back-end, do kterého chcete, aby nástroj pro vyrovnávání zatížení odesílal provoz. Toto nastavení může být stejné jako port front-end nebo jiné, pokud potřebujete flexibilitu pro vaši aplikaci. |
| Back-endový fond | Fond back-end, pro který chcete použít toto pravidlo nástroje pro vyrovnávání zatížení. |
| Sonda stavu | Sonda stavu, kterou jste vytvořili pro kontrolu stavu instancí ve fondu back-end. </br> Nové přenosy budou přijímány pouze v zdravých instancích. |
| Trvalost relace |  Máte tyto možnosti: </br> **Žádný** </br> **IP adresa klienta** </br> **IP adresa klienta a protokol**</br> </br> Udržujte provoz z klienta na stejný virtuální počítač ve fondu back-endu. Tento provoz se zachová po dobu trvání relace. </br> **Žádné** určuje, že by mohly být úspěšné požadavky ze stejného klienta zpracovány jakýmkoli virtuálním počítačem. </br> **IP adresa klienta** určuje, že po sobě jdoucí požadavky ze stejné IP adresy klienta budou zpracovávány stejným virtuálním počítačem. </br> **IP adresa klienta a protokol** zajišťují, že se po sobě jdoucí požadavky ze stejné IP adresy klienta a protokolu budou zpracovávat stejným virtuálním počítačem. </br> Přečtěte si další informace o [režimech distribuce](load-balancer-distribution-mode.md). |
| Časový limit nečinnosti (minuty) | Nechte otevřené připojení **TCP** nebo **http** , aniž by se museli spoléhat na to, že klienti odesílají zprávy Keep-Alive. |  
| Resetování protokolu TCP | Nástroj pro vyrovnávání zatížení může odesílat **resety TCP** , aby bylo možné lépe vytvořit předvídatelné chování aplikace v případě nečinnosti připojení. </br> Další informace o [resetování protokolu TCP](load-balancer-tcp-reset.md)|
| Plovoucí IP adresa | Plovoucí IP adresa je terminologie Azure, která je součástí toho, co je známo jako **přímé vrácení serveru (DSR)**. </br> DSR se skládá ze dvou částí: <br> 1. topologie toků </br> 2. schéma mapování IP adresy na úrovni platformy. </br></br> Azure Load Balancer vždy funguje v topologii signálu DSR, zda je povolena plovoucí IP adresa. </br> Tato operace znamená, že výstupní část toku je vždy správně přepsána do toku přímo zpět k původnímu zdroji. </br> Bez plovoucí IP adresy poskytuje Azure tradiční schéma mapování IP adres s vyrovnáváním zatížení, které je IP instancemi virtuálních počítačů. </br> Povolením plovoucí IP adresy změní mapování IP adres na front-end IP adresu nástroje pro vyrovnávání zatížení, aby byla umožněna další flexibilita. </br> Další informace najdete v tématu [více front-endu pro Azure Load Balancer](load-balancer-multivip-overview.md).|
| Vytvořit implicitní odchozí pravidla | Vyberte **Ne**. </br> Výchozí: **disableOutboundSnat = false**  </br> V tomto případě odchozí připojení probíhá přes stejnou front-end IP adresu. </br></br> **disableOutboundSnat = true** </br>V tomto případě jsou odchozí pravidla potřebná pro odchozí připojení. |

:::image type="content" source="./media/manage/load-balancing-rule.png" alt-text="Přidat pravidlo vyrovnávání zatížení." border="true":::

## <a name="inbound-nat-rules"></a>Příchozí pravidla NAT

Příchozí pravidlo překladu adres (NAT) přepošle příchozí provoz odeslaný do kombinace IP adresy a portu front-endu. 

Přenosy se odesílají do konkrétního virtuálního počítače nebo instance ve fondu back-endu. Předávání portů se provádí stejnou distribucí založenou na hodnotě hash jako vyrovnávání zatížení.

Pokud váš scénář vyžaduje, aby byly oddělené instance virtuálních počítačů v back-endu relace protokol RDP (Remote Desktop Protocol) (RDP) nebo Secure Shell (SSH). Do portů na stejné IP adrese front-endu lze namapovat více interních koncových bodů. 

IP adresy front-endu lze použít k vzdálené správě virtuálních počítačů bez dalšího pole s odkazem.

Pokud chcete do nástroje pro vyrovnávání zatížení přidat příchozí pravidlo NAT, v Azure Portal klikněte na nástroj pro vyrovnávání zatížení, vyberte **pravidla příchozího překladu adres (NAT)** a pak vyberte **+ Přidat**.

| Nastavení | Podrobnosti |
| ---------- | ---------- |
| Name | Název vašeho pravidla příchozího překladu adres (NAT) |
| IP adresa front-endu | Vyberte IP adresu front-endu. </br> Front-end IP adresa vašeho nástroje pro vyrovnávání zatížení, ke kterému chcete přiřadit pravidlo příchozího překladu adres (NAT). |
| Verze protokolu IP | Vaše možnosti jsou protokoly IPv4 a IPv6. |
| Služba | Typ služby, na které budete spouštět Azure Load Balancer. </br> Výběrem této položky se odpovídajícím způsobem aktualizují informace o portu. |
| Protokol | Azure Load Balancer je síťový nástroj pro vyrovnávání zatížení vrstvy 4. </br> Máte tyto možnosti: TCP nebo UDP. |
| Časový limit nečinnosti (minuty) | Nechejte připojení TCP nebo HTTP otevřené, aniž byste se museli spoléhat na to, že klienti odesílají zprávy Keep-Alive. |
| Resetování protokolu TCP | Load Balancer může odesílat resety TCP, aby lépe vytvořily předvídatelné chování aplikace při nečinnosti připojení. </br> Další informace o [resetování protokolu TCP](load-balancer-tcp-reset.md) |
| Port | Toto nastavení je port přidružený k IP adrese front-endu, pro který chcete provoz distribuovat na základě tohoto příchozího pravidla NAT. |
| Cílový virtuální počítač | Virtuální počítač, ke kterému se má toto pravidlo přidružit, do back-endu fondu. |
| Mapování portů | Toto nastavení může být na základě preference vaší aplikace výchozí nebo vlastní. |

:::image type="content" source="./media/manage/inbound-nat-rule.png" alt-text="Přidat pravidlo příchozího překladu adres (NAT)" border="true":::

## <a name="outbound-rules"></a>Pravidla odchozích přenosů

Odchozí pravidla nástroje pro vyrovnávání zatížení konfigurují odchozí SNAT pro virtuální počítače ve fondu back-endu.

Pokud chcete do svého nástroje pro vyrovnávání zatížení přidat odchozí pravidlo, v Azure Portal klikněte na nástroj pro vyrovnávání zatížení, vyberte **odchozí pravidla**a pak vyberte **+ Přidat**.

| Nastavení | Podrobnosti |
| ------- | ------ |
| Name | Název odchozího pravidla. |
| IP adresa front-endu | Vyberte IP adresu front-endu. </br> Front-end IP adresa vašeho nástroje pro vyrovnávání zatížení, ke kterému má být přidruženo odchozí pravidlo. |
| Protokol | Azure Load Balancer je síťový nástroj pro vyrovnávání zatížení vrstvy 4. </br> Máte tyto možnosti: **All**, **TCP**nebo **UDP**. |
| Časový limit nečinnosti (minuty) | Nechejte připojení **TCP** nebo **http** otevřené, aniž byste se museli spoléhat na to, že klienti odesílají zprávy Keep-Alive. |
| Resetování protokolu TCP | Nástroj pro vyrovnávání zatížení může odesílat **resety TCP** , aby bylo možné lépe vytvořit předvídatelné chování aplikace v případě nečinnosti připojení. </br> Další informace o [resetování protokolu TCP](load-balancer-tcp-reset.md) |
| Back-endový fond | Back-end fond, na kterém se má toto odchozí pravidlo použít. |

### <a name="port-allocation"></a>Přidělení portu

| Nastavení | Podrobnosti |
| ------- | ------ |
| Přidělení portu | Doporučujeme vybrat **možnost ručně počet odchozích portů**.|

### <a name="outbound-ports"></a>Odchozí porty

| Nastavení | Podrobnosti |
| ------- | ------ |
| Zvolit podle | Vybrat **porty na instanci** |
| Porty na instanci | Zadejte **10 000**. |

:::image type="content" source="./media/manage/outbound-rule.png" alt-text="Přidat příchozí odchozí pravidlo" border="true":::

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o různých pojmech a nastaveních v Azure Portal pro Azure Load Balancer.

* [Přečtěte si další informace](./load-balancer-overview.md) o Azure Load Balancer.
* [Nejčastější dotazy](./load-balancer-faqs.md) týkající se Azure Load Balancer.