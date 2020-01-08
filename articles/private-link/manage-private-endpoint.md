---
title: Správa připojení privátního koncového bodu v Azure
description: Naučte se spravovat připojení privátních koncových bodů v Azure.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 62b24b3e2f5c1b89fa7db581ac34cf58381db2a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452965"
---
# <a name="manage-a-private-endpoint-connection"></a>Správa připojení privátního koncového bodu
Privátní propojení Azure pracuje na modelu toku volání schválení, kde příjemce služby privátního propojení může požádat o připojení k poskytovateli služeb za účelem využívání služby. Poskytovatel služeb se pak může rozhodnout, jestli chcete, aby se příjemce mohl připojit nebo ne. Privátní propojení Azure umožňuje poskytovatelům služeb spravovat připojení privátního koncového bodu na svých prostředcích. Tento článek poskytuje informace o tom, jak spravovat připojení privátního koncového bodu.

![Správa privátních koncových bodů](media/manage-private-endpoint/manage-private-endpoint.png)

Existují dvě metody schvalování připojení, ze kterých může příjemce služby privátního propojení vybírat:
- **Automaticky**: Pokud má příjemce služby oprávnění RBAC u prostředku poskytovatele služeb, může vybrat metodu automatického schvalování. V takovém případě, když požadavek dosáhne prostředku poskytovatele služeb, není od poskytovatele služeb vyžadována žádná akce a připojení je automaticky schváleno. 
- **Ruční**: v opačném případě, pokud nemá příjemce služby oprávnění RBAC u prostředku poskytovatele služeb, může příjemce zvolit metodu ručního schválení. V takovém případě se žádost o připojení zobrazí v části prostředky služby jako **nevyřízená**. Aby bylo možné navázat spojení, musí poskytovatel služeb požadavek ručně schválit. V manuálních případech může příjemce služby také určit zprávu s žádostí o poskytnutí kontextu poskytovateli služeb. Poskytovatel služeb nabízí následující možnosti, ze kterých si můžete vybrat pro všechna připojení privátních koncových bodů: **schválení**, **zamítnutí**, **Odebrání**.

V níže uvedené tabulce jsou uvedeny různé akce poskytovatele služeb a výsledné stavy připojení pro soukromé koncové body.  Poskytovatel služeb může také později změnit stav připojení privátního koncového bodu bez zásahu uživatele. Akce aktualizuje stav koncového bodu na straně spotřebitele. 


|Akce poskytovatele služeb   |Stav privátního koncového bodu příjemce služby   |Popis   |
|---------|---------|---------|
|Žádné    |    Čekající na vyřízení     |    Připojení je vytvořeno ručně a čeká na schválení vlastníkem prostředku privátního odkazu.       |
|Schválení    |  Schválené       |  Připojení bylo automaticky nebo ručně schváleno a je připraveno k použití.     |
|Odmítnout     | Odmítnuto        | Připojení bylo odmítnuto vlastníkem prostředku privátního odkazu.        |
|Odebrat    |  Odpojení       | Připojení bylo odebráno vlastníkem prostředku privátního propojení, soukromý koncový bod bude informativní a měl by být odstraněn pro vyčištění.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Správa připojení privátního koncového bodu na PaaSch prostředcích Azure
Portál je upřednostňovanou metodou správy připojení privátních koncových bodů v prostředcích Azure PaaS. V současné době neposkytujeme podporu PowerShellu/CLI pro správu připojení k prostředkům Azure PaaS.
1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
2. Přejděte do centra privátních odkazů.
3. V části **prostředky**vyberte typ prostředku, pro který chcete spravovat připojení privátního koncového bodu.
4. Pro každý typ prostředku můžete zobrazit počet připojení privátního koncového bodu, která jsou k němu přidružená. Prostředky můžete podle potřeby filtrovat.
5. Vyberte připojení privátního koncového bodu.  V seznamu připojení vyberte připojení, které chcete spravovat. 
6. Stav připojení můžete změnit výběrem z možností v horní části.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Správa připojení privátního koncového bodu u zákazníka nebo partnera, který je vlastníkem privátního propojení

Azure PowerShell a Azure CLI jsou preferované metody pro správu připojení privátních koncových bodů k partnerským službám Microsoftu nebo službám vlastněných zákazníky. V současné době nemáme žádná podpora portálu pro správu připojení ve službě privátních odkazů.  
 
### <a name="powershell"></a>PowerShell 
  
Ke správě připojení privátních koncových bodů použijte následující příkazy PowerShellu.  
#### <a name="get-private-link-connection-states"></a>Získat stavy připojení privátního propojení 
Pomocí rutiny `Get-AzPrivateLinkService` můžete získat připojení privátního koncového bodu a jejich stavy.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Schválení připojení privátního koncového bodu 
 
K schválení připojení privátního koncového bodu použijte rutinu `Approve-AzPrivateEndpointConnection`. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Odepřít připojení privátního koncového bodu 
 
Pomocí rutiny `Deny-AzPrivateEndpointConnection` zamítnout připojení privátního koncového bodu. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Odebrat připojení privátního koncového bodu 
 
Pomocí rutiny `Remove-AzPrivateEndpointConnection` odeberte připojení privátního koncového bodu. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Azure CLI 
 
Pro správu privátních připojení koncových bodů použijte `az network private-link-service update`. Stav připojení je zadán v parametru ```azurecli connection-status```. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Další kroky
- [Informace o privátních koncových bodech](private-endpoint-overview.md)
 
