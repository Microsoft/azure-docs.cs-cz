---
title: Podpora zón dostupnosti pro App Service prostředí
description: Naučte se, jak nasadit prostředí App Service, aby vaše aplikace byly redundantní.
author: ccompy
ms.assetid: 24e3e7eb-c160-49ff-8d46-e947818ef186
ms.topic: article
ms.date: 07/15/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 383b5bb5c7295fe54efda883e47b9b2338286de5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624721"
---
# <a name="availability-zone-support-for-app-service-environments"></a>Podpora zón dostupnosti pro App Service prostředí

App Service prostředí (pomocného mechanismu) se dají nasadit do Zóny dostupnosti (AZ).  Zákazníci můžou nasadit interní nástroj pro vyrovnávání zatížení (interního nástroje) služby ASE do konkrétní AZ v oblasti Azure. Pokud připnete připnutí interního nástroje pomocného mechanismu na konkrétní AZ, prostředky využívané interního nástroje pomocným mechanismem připnuté buď na zadaný AZ, nebo nasazené v zóně redundantním způsobem.  

INTERNÍHO nástroje pomocného modulu, který je explicitně nasazený do AZ, se považuje za prostředek oblasti, protože interního nástroje pomocného mechanismu je připnuté ke konkrétní zóně. K zadané zóně budou připnuté následující závislosti interního nástroje pomocného mechanismu:

- IP adresa interního nástroje pro vyrovnávání zatížení
- výpočetní prostředky, které služba pomocného mechanismu používá ke správě a spouštění webových aplikací

Vzdálené úložiště souborů pro webové aplikace nasazené v oblasti interního nástroje pomocného mechanismu služby používá zónu redundantního úložiště (ZRS).

Pokud se nenásledují kroky popsané v tomto článku, interního nástroje služby ASE se automaticky nesadí v rámci Zona. Externí pomocný objekt pro pořízení nelze připnout pomocí veřejné IP adresy do konkrétní zóny dostupnosti. 

Oblast interního nástroje služby ASE lze vytvořit v kterékoli z následujících oblastí:

- Austrálie – východ
- Střední Kanada
- Střední USA
- East US
- USA – východ 2
- Východní USA 2 (EUAP)
- Francie – střed 
- Japonsko – východ
- Severní Evropa
- West Europe
- Southeast Asia
- Spojené království – jih
- Západní USA 2

Aplikace nasazené v oblasti interního nástroje pomocného mechanismu pro práci budou dál spouštět a obsluhovat přenosy v tomto pomocném modulu pro zpracování dat i v případě výpadku jiných zón ve stejné oblasti.  Je možné, že chování mimo běhu, včetně; škálování plánu služby Application Service, vytváření aplikací, konfigurace aplikací a publikování aplikací může mít i nadále vliv na výpadek v jiných zónách dostupnosti. Nasazení oblasti interního nástroje připnuté do zóny pouze zajišťuje nepřetržitou dobu provozu již nasazených aplikací.

## <a name="how-to-deploy-an-app-service-environment-in-an-availability-zone"></a>Postup nasazení App Service Environment v zóně dostupnosti ##

Oblast interního nástroje služby ASE je nutné vytvořit pomocí šablon ARM. Jakmile se interního nástroje pomocného mechanismu služby vytvoří prostřednictvím šablony ARM, dá se zobrazit a interagovat pomocí Azure Portal a CLI.  Šablona ARM je nutná jenom pro počáteční vytváření interního nástroje pomocného mechanismu.

Jediná změna potřebná pro zadání oblasti interního nástroje pomocného mechanismu řízení pro oblast je nová vlastnost ***Zone** _. Vlastnost *_zonees_** by měla být nastavená na hodnotu "1", "2" nebo "3" v závislosti na logické zóně dostupnosti, ke které by měl být PŘIpnuté interního nástroje pomocného mechanismu řízení.

Příklad fragmentu šablony ARM dole ukazuje vlastnost nové ***zóny*** , která určuje, že se má PŘIpnutí interního nástroje připnout ke zóně 2.

```
   "resources": [
      {
         "type": "Microsoft.Web/hostingEnvironments",
         "kind": "ASEV2",
         "name": "yourASENameHere",
         "apiVersion": "2015-08-01",
         "location": "your location here",
         "zones": [
            "2"
         ],
         "properties": {
         "name": "yourASENameHere",
         "location": "your location here",
         "ipSslAddressCount": 0,
         "internalLoadBalancingMode": "3",
         "dnsSuffix": "contoso-internal.com",
         "virtualNetwork": {
             "Id": "/subscriptions/your-subscription-id-here/resourceGroups/your-resource-group-here/providers/Microsoft.Network/virtualNetworks/your-vnet-name-here",
             "Subnet": "yourSubnetNameHere"
          }
         }
      }
    ]
```

Aby bylo možné zónu aplikace redundantní, je nutné nasadit dvě oblasti interního nástroje služby ase. Dvě oblasti interního nástroje služby ASE musí být v samostatných zónách dostupnosti. Pak budete muset své aplikace nasadit do všech interního nástroje služby ase. Po vytvoření vašich aplikací je potřeba nakonfigurovat řešení vyrovnávání zatížení. Doporučeným řešením je nasadit [zónu redundantní Application Gateway](../../application-gateway/application-gateway-autoscaling-zone-redundant.md) , která je v oblasti Zona interního nástroje služby ase. 

## <a name="in-region-data-residency"></a>V oblasti data zasídlí ##

INTERNÍHO nástroje služby ASE nasazená v zóně dostupnosti bude ukládat jenom zákaznická data v rámci oblasti, ve které se nasadila oblast interního nástroje pomocného programu. Obsah souboru webu i nastavení poskytnuté zákazníky a tajné klíče uložené v App Service zůstávají v oblasti, kde je nasazená oblast interního nástroje pomocného mechanismu řízení.

Zákazníci zajišťují, aby data v jedné oblasti byla zavedena podle kroků uvedených výše v části Jak nasadit App Service Environment v zóně dostupnosti. Když nakonfigurujete App Service Environment podle těchto kroků, App Service Environment nasazená v zóně dostupnosti splňuje požadavky v oblasti dat, včetně těch, které jsou uvedené v [Centrum zabezpečení Azure](https://azuredatacentermap.azurewebsites.net/).

Zákazníci můžou ověřit, jestli je App Service Environment správně nakonfigurovaný tak, aby ukládala data v jedné oblasti pomocí následujících kroků: 

1. Pomocí [Průzkumník prostředků](https://resources.azure.com)přejděte k prostředku ARM pro App Service Environment.  Služby ASE jsou uvedena v části *poskytovatelé/Microsoft. Web/hostingEnvironments*.
2. Pokud vlastnost *Zones* existuje v zobrazení syntaxe JSON pro ARM a obsahuje jedno ohodnocené pole JSON s hodnotou "1", "2" nebo "3", pak je pomocného zonally nasazený a zákaznická data zůstanou ve stejné oblasti.
2. Pokud vlastnost *zóny* neexistuje nebo vlastnost nemá platnou hodnotu zóny, jak je uvedeno výše, pak není zonally nasazený a zákaznická data se neukládají výhradně ve stejné oblasti.
