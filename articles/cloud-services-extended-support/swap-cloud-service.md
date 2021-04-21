---
title: Přepnutí nebo přepínání nasazení v Azure Cloud Services (Rozšířená podpora)
description: Přečtěte si, jak přepínat mezi nasazeními v Azure Cloud Services (Rozšířená podpora).
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: f5e01075ffb460c7ddd70b40a6b19f7ea70dd776
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748809"
---
# <a name="swap-or-switch-deployments-in-azure-cloud-services-extended-support"></a>Přepnutí nebo přepínání nasazení v Azure Cloud Services (Rozšířená podpora)

V Azure Cloud Services můžete přepínat mezi dvěma nezávislými nasazeními cloudových služeb (Rozšířená podpora). Na rozdíl od Azure Cloud Services (Classic) nepoužívá model Azure Resource Manager v Azure Cloud Services (Rozšířená podpora) sloty nasazení. V Azure Cloud Services (Rozšířená podpora), když nasadíte novou verzi cloudové služby, můžete vytvořit "vyměnitelné" cloudovou službu pomocí existující cloudové služby ve službě Azure Cloud Services (Rozšířená podpora).

Po záměně nasazení můžete pomocí nového nasazení cloudové služby připravit novou verzi a otestovat ji. V důsledku toho swap propaguje novou cloudovou službu, která je připravená na produkční verzi.

> [!NOTE]
> Nemůžete přepínat mezi nasazením Azure Cloud Services (Classic) a nasazením Azure Cloud Services (Rozšířená podpora).

Pokud nasadíte druhou dvojici cloudových služeb, musíte provést výměnu cloudové služby s jinou cloudovou službou.

Nasazení můžete prohodit pomocí šablony Azure Resource Manager (šablona ARM), Azure Portal nebo REST API.

## <a name="arm-template"></a>Šablona ARM

Pokud používáte metodu nasazení šablony ARM, chcete-li zajistit, aby cloudové služby byly vyměněny, nastavte v `SwappableCloudService` `networkProfile` `cloudServices` objektu vlastnost na ID spárované cloudové služby:

```json
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
        }
```

## <a name="azure-portal"></a>portál Azure

Postup při prohození nasazení v Azure Portal:

1. V nabídce portál vyberte možnost **Cloud Services (Rozšířená podpora)** nebo **řídicí panel**.
1. Vyberte cloudovou službu, kterou chcete aktualizovat.
1. V části **Přehled** cloudové služby vyberte **swap**:

   :::image type="content" source="media/swap-cloud-service-portal-swap.png" alt-text="Snímek obrazovky zobrazující kartu swap pro cloudovou službu.":::

1. V podokně potvrzení prohození ověřte informace o nasazení a pak výběrem **OK** zahodíte nasazení:

   :::image type="content" source="media/swap-cloud-service-portal-confirm.png" alt-text="Snímek obrazovky, který ukazuje potvrzení informací o zahození nasazení.":::

Nasazení se rychle mění, protože jediná věc, kterou mění, je virtuální IP adresa pro nasazenou cloudovou službu.

Pokud chcete ušetřit náklady na výpočetní výkon, můžete odstranit jednu z cloudových služeb (určených jako přípravného prostředí pro nasazení aplikace), až ověříte, že vaše vyměněná cloudová služba funguje podle očekávání.

## <a name="rest-api"></a>Rozhraní REST API

Pokud chcete použít REST API k přepnutí na nové nasazení cloudových služeb ve službě Azure Cloud Services (Rozšířená podpora), použijte následující příkaz a konfiguraci JSON:

```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```

```json
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
}
```

## <a name="common-questions-about-swapping-deployments"></a>Běžné dotazy týkající se odkládacích nasazení

Přečtěte si tyto odpovědi na nejčastější dotazy týkající se swapů nasazení v Azure Cloud Services (Rozšířená podpora).

### <a name="what-are-the-prerequisites-for-swapping-to-a-new-cloud-services-deployment"></a>Jaké jsou požadavky na záměnu při nasazení nového Cloud Services?

U úspěšného prohození nasazení v Azure Cloud Services je nutné splňovat dva klíčové předpoklady (Rozšířená podpora):

* Pokud chcete pro jednu z vyměnitelné cloudové služby použít statickou nebo rezervovanou IP adresu, musí druhá cloudová služba taky používat rezervovanou IP adresu. V opačném případě se výměna nezdařila.
* Aby se prohození úspěšně provedlo, musí být spuštěné všechny instance vašich rolí. Pokud chcete zjistit stav svých instancí, v Azure Portal, vyhledejte v části **Přehled** nově nasazené cloudové služby nebo použijte `Get-AzRole` příkaz ve Windows PowerShellu.

Aktualizace operačního systému hosta a operace opravy služby můžou způsobit selhání přepnutí nasazení. Další informace najdete v tématu [řešení potíží s nasazením cloudové služby](../cloud-services/cloud-services-troubleshoot-deployment-problems.md).

### <a name="can-i-make-a-vip-swap-in-parallel-with-another-mutating-operation"></a>Je možné provést prohození virtuálních IP adres paralelně s jinou další operací?

No. Swap VIP je změna jenom pro síť, která se musí dokončit před zahájením jakékoli jiné výpočetní operace v cloudové službě. Spouští se operace aktualizace, odstranění nebo automatického škálování pro cloudovou službu, zatímco probíhá prohození VIP nebo Aktivace swap VIP, zatímco probíhá jiná výpočetní operace, ale cloudová služba je ve stavu neopravitelné chyby.

### <a name="does-a-swap-incur-downtime-for-my-application-and-how-should-i-handle-it"></a>Účtuje se v aplikaci výpadky a jak ji mám pokládat?

Výměna cloudových služeb je obvykle rychlá, protože je jenom změnou konfigurace v nástroji pro vyrovnávání zatížení Azure. V některých případech může prohození trvat 10 nebo více sekund a výsledkem jsou chyby přechodného připojení. Chcete-li omezit účinek swapu na uživatele, zvažte implementaci logiky opakování klienta.

## <a name="next-steps"></a>Další kroky 

* Přečtěte si [požadavky nasazení](deploy-prerequisite.md) pro Azure Cloud Services (Rozšířená podpora).
* Přečtěte si [Nejčastější dotazy](faq.md) k Azure Cloud Services (Rozšířená podpora).
* Nasaďte cloudovou službu Azure Cloud Services (Rozšířená podpora) pomocí jedné z následujících možností:
  * [Azure Portal](deploy-portal.md)
  * [PowerShell](deploy-powershell.md)
  * [Šablona ARM](deploy-template.md)
  * [Visual Studio](deploy-visual-studio.md)
