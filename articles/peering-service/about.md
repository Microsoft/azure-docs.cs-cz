---
title: Přehled služby Azure peering
description: Další informace o službě Azure peering Service – přehled
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 198ba23920179e71e095e498ee2173d7f0111d42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026725"
---
# <a name="azure-peering-service-overview"></a>Přehled služby Azure peering

Azure peering Service je síťová služba, která vylepšuje připojení zákazníků ke cloudovým službám Microsoftu, jako jsou Microsoft 365, Dynamics 365, služby software jako služba (SaaS), Azure nebo jakékoli služby Microsoftu přístupné prostřednictvím veřejného Internetu. Společnost Microsoft spolupracuje s poskytovateli poskytovatelů internetových služeb (ISP), internetovými partnery (IXPs) a softwarově definovanými poskytovateli cloudových propojení (SDCI) po celém světě, aby poskytovala spolehlivé a vysoce náročné veřejné připojení s optimálním směrováním od zákazníka k síti Microsoftu.

Se službou peering Service můžou zákazníci vybrat dobře připojeného poskytovatele služby partnera v dané oblasti. Veřejné připojení je optimalizované pro zajištění vysoké spolehlivosti a minimální latence z cloudových služeb až po umístění koncového uživatele.

![Distribuované připojení ke cloudu Microsoftu](./media/peering-service-about/peering-service-what.png)

Zákazníci mohou také pro telemetrii služby partnerských vztahů, jako jsou míry latence uživatelů, využívat k síti Microsoftu, monitorování směrování protokolu BGP a výstrahám proti nevracení a napadení, registraci připojení ke službě peering Service v Azure Portal. 

Pokud chcete používat službu peering Service, nemusíte zákazníky registrovat u Microsoftu. Jediným požadavkem je kontaktovat [partnera služby partnerského vztahu](location-partners.md) za účelem získání služby. Pokud se chcete přihlásit k telemetrie služby peering Service, musí se zákazníci zaregistrovat v Azure Portal.

Pokyny k registraci služby peering Service najdete v tématu [Registrace služby partnerského vztahu pomocí Azure Portal](azure-portal.md). 

> [!NOTE]
> Tento článek je určený pro architekty sítě, které se účtují v podnikovém připojení ke cloudu a na internetu.


## <a name="what-is-peering-service"></a>Co je Peering Service?

Služba partnerského vztahu je:

- Služba IP, která používá veřejný Internet. 
- Platforma pro spolupráci s poskytovateli služeb a službou přidanou hodnotou, která je určená k zajištění optimálního a spolehlivého směrování zákazníkovi prostřednictvím partnerů poskytovatele služeb do cloudu Microsoftu přes veřejnou síť.

Peering Service není produkt privátního připojení, jako je Azure ExpressRoute nebo produkt VPN.

> [!NOTE]
> Další informace o ExpressRoute najdete v [dokumentaci k ExpressRoute](../expressroute/index.yml).
>

## <a name="background"></a>Pozadí

Microsoft 365, Dynamics 365 a jakékoli jiné služby Microsoft SaaS Services se hostují v několika datových centrech Microsoftu a dají se k nim dostat z libovolného zeměpisného umístění. V globální síti Microsoft se nachází umístění Microsoft Edge Point-of-prezence (PoP) po celém světě, kde se může připojit k koncovému uživateli přes jejich poskytovatelé služeb. 

Poskytovatelé služeb Microsoftu a partnerů zajišťují, aby přenosy pro předpony registrované s připojením služby peering Service vstoupily do nejbližšího umístění PoP Microsoft Edge v globální síti Microsoftu a ukončili je. Microsoft zajistí, aby se provoz síťových přenosů z předpon zaregistrovaných u připojení ke službě peering Service měl nejbližších umístění PoP Microsoft Edge v globální síti Microsoftu.

![Připojení k síti a veřejné síti Microsoftu](./media/peering-service-about/peering-service-background-final.png)

> [!NOTE]
> Další informace o globální síti Microsoft najdete v tématu [globální síť Microsoft](../networking/microsoft-global-network.md).
>

## <a name="why-use-peering-service"></a>Proč používat službu peering Service?

Firmy, které hledají přístup k Internetu, nebo berou v úvahách o architektuře SD-WAN nebo s vysokým využitím služeb Microsoft SaaS Services, vyžadují robustní a vysoké možnosti připojení k Internetu. Zákazníci můžou tento přechod provést pomocí služby peering Service. Poskytovatelé Microsoftu a služeb se společně dodávají k spolehlivému veřejnému připojení zaměřenému na výkon cloudu Microsoftu. Tady jsou uvedené některé klíčové funkce zákazníka:

- Nejlepší veřejné směrování přes Internet, které Microsoft Azure Cloud Services pro zajištění optimálního výkonu a spolehlivosti.
- Možnost výběru preferovaného poskytovatele služeb pro připojení ke cloudu Microsoftu.
- Provozní přehledy, jako je generování sestav latence a monitorování předpon.
- Optimální směrování sítě (jako směrování) od Microsoft cloudu.
- Analýzy tras a statistiky: události pro ([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) směrovat anomálie (detekce nevracení a napadení) a neoptimální směrování.

### <a name="robust-reliable-peering"></a>Robustní a spolehlivý partnerský vztah

Služba peering Service používá dva typy redundance:

- **Místní redundance**

   Poskytovatelé Microsoftu a služeb se v různých umístěních PoP přes Microsoft Edge vzájemně vzájemně přidávají ke službě partnerského vztahu V každém umístění musí propojení podporovat převzetí služeb při selhání ve dvou směrovačích.

   Každé umístění partnerského vztahu je zřízeno s redundantními a různými partnerskými odkazy.

- **Geografická redundance**

   Společnost Microsoft spolupojuje s poskytovateli služeb ve více umístěních Metro, aby v případě, že jeden z hraničních uzlů má snížený výkon, směrují přenosy do Microsoftu prostřednictvím alternativních lokalit. Microsoft směruje provoz do své globální sítě pomocí zásad směrování na základě SDN pro zajištění optimálního výkonu.

    Tento typ redundance používá nejkratší cestu směrování, kdykoli pro koncového uživatele zvolíte nejbližšího Microsoft Edge, který zajistí, že zákazník bude mít jednu síť směrování (jako směrování) od Microsoftu.

   ![Geografická redundance](./media/peering-service-about/peering-service-geo-shortest.png)

### <a name="optimal-routing"></a>Optimální směrování

Upřednostňovaná je následující technika směrování:

-  **Směrování studených brambor**

   Technika směrování studených brambor definovaných v softwaru nabízí kontrolu nad síťovými přenosy, které pocházejí z cloudu Microsoftu. Zajišťuje, aby provoz zůstával na vysokou kapacitu, nízkou latenci a vysoce spolehlivou globální síť Microsoftu, dokud není co nejblíže cíli.
   
   Směrování, které nepoužívá techniku studených brambor, se označuje jako směrování za horkou bramborový. Díky směrování za provozu, provoz pocházející z cloudu Microsoftu pak přechází přes Internet.

   ![Směrování studených brambor](./media/peering-service-about/peering-service-cold-potato.png)

### <a name="monitoring-platform"></a>Monitorovací platforma

   Monitorování služby je nabízeno k analýze provozu a směrování zákazníků a nabízí následující možnosti: 

-  **Detekce anomálií trasy protokolu BGP Internetu**
          
   Tato služba se používá ke zjišťování a upozorňování na události anomálií trasy, jako je například napadení tras pro předpony zákazníků.

-  **Latence zákazníka**

   Tato služba monitoruje výkon směrování mezi umístěním zákazníka a Microsoftem. 
   
   Výkon směrování se měří tak, že se ověří doba odezvy z klienta, aby se dostalo na PoP Microsoft Edge. Zákazníci si můžou sestavy latence zobrazit v různých geografických umístěních.

   Monitorování zachycuje události v případě snížení úrovně služby.

   ![Monitorovací platforma pro službu partnerského vztahu](media/peering-service-about/peering-service-latency-report.png)

### <a name="traffic-protection"></a>Ochrana provozu

Směrování probíhá pouze přes upřednostňovanou cestu, která je definována při registraci zákazníka u služby partnerského vztahu.

Společnost Microsoft garantuje přenos provozu prostřednictvím upřednostňovaných cest i v případě, že je zjištěna škodlivá aktivita.

Anomálie tras protokolu BGP jsou hlášeny ve Azure Portal, pokud nějaké existují.

## <a name="next-steps"></a>Další kroky

- Další informace o připojeních ke službě peering Service najdete v tématu [připojení ke službě peering Service](connection.md).
- Další informace o telemetrie připojení ke službě peering Service najdete v tématu [telemetrie připojení ke službě peering Service](connection-telemetry.md).
- Pokud chcete najít partnera poskytovatele služeb, přečtěte si téma [partneři a umístění služby partnerského vztahu](location-partners.md).
- Pokud chcete připojit připojení ke službě peering Service, přečtěte si téma připojování [modelu služby peering](onboarding-model.md)Service.
- Pokud chcete připojení zaregistrovat pomocí Azure Portal, přečtěte si téma [registrace připojení ke službě peering Service pomocí Azure Portal](azure-portal.md).
- Měření telemetrie najdete v tématu [měření telemetrie připojení](measure-connection-telemetry.md).