---
title: Prohození a přepínání mezi dvěma Cloud Services Azure (Rozšířená podpora)
description: Prohození a přepínání mezi dvěma Cloud Services Azure (Rozšířená podpora)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: 6f96656af9afd9874cc6273a9cea9ed43e8c69cc
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294277"
---
# <a name="swapswitch-between-two-azure-cloud-services-extended-support"></a>Prohození a přepínání mezi dvěma Cloud Services Azure (Rozšířená podpora)
Díky Cloud Services (rozšířené podpoře) můžete proměňovat mezi dvěma nezávislými nasazeními cloudových služeb. Na rozdíl od služby Cloud Services (Classic) neexistuje koncept slotů s modelem Azure Resource Manager. Pokud se rozhodnete nasadit novou verzi cloudové služby (rozšířenou podporu), můžete ji nastavit jako "swape" s jinou existující cloudovou službou (rozšířenou podporu), která vám umožní připravit a otestovat nové vydání pomocí tohoto nasazení. Cloudovou službu je možné prohodit s jinou cloudovou službou, která je dostupná jenom v době nasazení druhé cloudové služby (páru). Při použití metody nasazení založené na šablonách ARM se to dělá nastavením vlastnosti SwappableCloudService v profilu sítě objektu cloudové služby na ID spárované cloudové služby. 

```
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
```
> [!Note] 
> Nemůžete přepínat mezi cloudovou službou (klasickou) a cloudovou službou (Rozšířená podpora).

Pomocí **swapu** můžete přepínat adresy URL, na které jsou řešeny dvě cloudové služby, a to v důsledku zvýšení úrovně nové cloudové služby (připravené) na produkční verzi.
Nasazení můžete měnit na stránce Cloud Services nebo na řídicím panelu.

1. V [Azure Portal](https://portal.azure.com)Vyberte cloudovou službu, kterou chcete aktualizovat. Tento krok otevře okno instance cloudové služby.
2. V okně vyberte **odkládací** 
    :::image type="content" source="media/swap-cloud-service-1.png" alt-text="obrázek. zobrazí se možnost přepnutí do cloudové služby"::: .
   
3. Otevře se následující výzva k potvrzení.
   
   :::image type="content" source="media/swap-cloud-service-2.png" alt-text="Obrázek ukazuje záměna cloudové služby":::
   
4. Po ověření informací o nasazení vyberte OK a Proměňte nasazení.
Prohození proběhne rychle, protože jediná věc, kterou měníte, jsou virtuální IP adresy (VIP) pro obě cloudové služby.

Pokud chcete ušetřit náklady na výpočetní výkon, můžete odstranit jednu z cloudových služeb (určených jako přípravného prostředí pro nasazení aplikace), až ověříte, že vaše vyměněná cloudová služba funguje podle očekávání.

Rozhraní REST API k provedení příkazu prohození mezi dvěma službami Cloud Services rozšířená nasazení podpory najdete níže:
```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```
```
{
  "frontendIPConfigurations": [
    {
    "id": "#LBFE1#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP2#"
    }
      }
    },
   {
    "id": "#LBFE2#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP1#"
     }
       }
    }
  ]
 }
```
## <a name="common-questions-about-swapping-deployments"></a>Běžné dotazy týkající se odkládacích nasazení

### <a name="what-are-the-prerequisites-for-swapping-between-two-cloud-services"></a>Jaké jsou požadavky pro záměnu dvou cloudových služeb?
Existují dva klíčové předpoklady pro úspěšné prohození cloudové služby (Rozšířená podpora):
* Pokud chcete pro jednu z vyměnitelné cloudové služby použít statickou/vyhrazenou IP adresu, musí druhá cloudová služba také používat vyhrazenou IP adresu. V opačném případě se výměna nezdařila.
* Než budete moct provést prohození, musí být spuštěné všechny instance vašich rolí. Stav vašich instancí můžete zjistit v okně Přehled Azure Portal. Případně můžete použít příkaz Get-AzRole ve Windows PowerShellu.

Aktualizace operačního systému hosta a operace opravy služby můžou způsobit, že se zahození nasazení nezdaří. Další informace najdete v tématu řešení problémů s nasazením cloudové služby.

### <a name="can-i-perform-a-vip-swap-in-parallel-with-another-mutating-operation"></a>Můžu provést prohození VIP paralelně s jinou další operací?
No. Prohození VIP je pouze změna sítě, která se musí dokončit před provedením jakékoli jiné výpočetní operace v cloudových službách. Provádí se operace aktualizace, odstranění nebo automatického škálování v cloudových službách, zatímco probíhá prohození VIP nebo Aktivace prohození VIP, zatímco probíhá jiná výpočetní operace, takže cloudová služba zůstane v nepožadovaném stavu, ze kterého může být obnovení možná. 

### <a name="does-a-swap-incur-downtime-for-my-application-how-should-i-handle-it"></a>Účtuje se u své aplikace výpadky? Jak ho mám pokládat?
Jak je popsáno v předchozí části, je výměna cloudové služby obvykle rychlá, protože se jedná jenom o změnu konfigurace v nástroji Azure Load Balancer. V některých případech může trvat 10 nebo více sekund a způsobit selhání při přechodném připojení. Pokud chcete omezit dopad na vaše zákazníky, zvažte implementaci logiky opakování klienta.

## <a name="next-steps"></a>Další kroky 
- Přečtěte si [požadavky na nasazení](deploy-prerequisite.md) pro Cloud Services (Rozšířená podpora).
- Přečtěte si [Nejčastější dotazy](faq.md) k Cloud Services (Rozšířená podpora).
- Nasaďte cloudovou službu (rozšířenou podporu) pomocí [Azure Portal](deploy-portal.md), [PowerShellu](deploy-powershell.md), [šablony](deploy-template.md) nebo sady [Visual Studio](deploy-visual-studio.md).
