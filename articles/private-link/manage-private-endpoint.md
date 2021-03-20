---
title: Správa připojení privátního koncového bodu v Azure
description: Naučte se spravovat připojení privátních koncových bodů v Azure.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 2987cd1ff8c678f7079e13e8b9bc657817c066f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95531368"
---
# <a name="manage-a-private-endpoint-connection"></a>Správa připojení privátního koncového bodu
Privátní propojení Azure pracuje na modelu toku volání schválení, kde příjemce služby privátního propojení může požádat o připojení k poskytovateli služeb za účelem využívání služby. Poskytovatel služeb se pak může rozhodnout, jestli chcete, aby se příjemce mohl připojit nebo ne. Privátní propojení Azure umožňuje poskytovatelům služeb spravovat připojení privátního koncového bodu na svých prostředcích. Tento článek poskytuje informace o tom, jak spravovat připojení privátního koncového bodu.

![Správa privátních koncových bodů](media/manage-private-endpoint/manage-private-endpoint.png)

Existují dvě metody schvalování připojení, ze kterých může příjemce služby privátního propojení vybírat:
- **Automaticky**: Pokud má uživatel služby oprávnění Azure RBAC u prostředku poskytovatele služeb, může příjemce zvolit metodu automatického schvalování. V takovém případě, když požadavek dosáhne prostředku poskytovatele služeb, není od poskytovatele služeb vyžadována žádná akce a připojení je automaticky schváleno. 
- **Ruční**: v opačném případě, pokud nemá uživatel služby oprávnění Azure RBAC u prostředku poskytovatele služeb, může příjemce zvolit metodu ručního schválení. V takovém případě se žádost o připojení zobrazí v části prostředky služby jako **nevyřízená**. Aby bylo možné navázat spojení, musí poskytovatel služeb požadavek ručně schválit. V manuálních případech může příjemce služby také určit zprávu s žádostí o poskytnutí kontextu poskytovateli služeb. Poskytovatel služeb nabízí následující možnosti, ze kterých si můžete vybrat pro všechna připojení privátních koncových bodů: **schválení**, **zamítnutí**, **Odebrání**.

V níže uvedené tabulce jsou uvedeny různé akce poskytovatele služeb a výsledné stavy připojení pro soukromé koncové body.  Poskytovatel služeb může také později změnit stav připojení privátního koncového bodu bez zásahu uživatele. Akce aktualizuje stav koncového bodu na straně spotřebitele. 


|Akce poskytovatele služeb   |Stav privátního koncového bodu příjemce služby   |Description   |
|---------|---------|---------|
|Žádná    |    Čekající     |    Připojení je vytvořeno ručně a čeká na schválení vlastníkem prostředku privátního odkazu.       |
|Schválení    |  Schválené       |  Připojení bylo automaticky nebo ručně schváleno a je připraveno k použití.     |
|Odmítnout     | Zamítnuto        | Připojení bylo odmítnuto vlastníkem prostředku privátního odkazu.        |
|Odebrat    |  Odpojeno       | Připojení bylo odebráno vlastníkem prostředku privátního propojení, soukromý koncový bod bude informativní a měl by být odstraněn pro vyčištění.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Správa připojení privátních koncových bodů v prostředcích Azure PaaS
Portál je upřednostňovanou metodou správy připojení privátních koncových bodů v prostředcích Azure PaaS. V současné době neposkytujeme podporu PowerShellu/CLI pro správu připojení k prostředkům Azure PaaS.
1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
2. Přejděte do centra privátních odkazů.
3. V části **prostředky** vyberte typ prostředku, pro který chcete spravovat připojení privátního koncového bodu.
4. Pro každý typ prostředku můžete zobrazit počet připojení privátního koncového bodu, která jsou k němu přidružená. Prostředky můžete podle potřeby filtrovat.
5. Vyberte připojení privátního koncového bodu.  V seznamu připojení vyberte připojení, které chcete spravovat. 
6. Stav připojení můžete změnit výběrem z možností v horní části.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Správa připojení privátního koncového bodu u zákazníka nebo partnera, který je vlastníkem privátního propojení

Azure PowerShell a Azure CLI jsou preferované metody pro správu připojení privátních koncových bodů k partnerským službám Microsoftu nebo službám vlastněných zákazníky. V současné době nemáme žádná podpora portálu pro správu připojení ve službě privátních odkazů.  
 
### <a name="powershell"></a>PowerShell 
  
Ke správě připojení privátních koncových bodů použijte následující příkazy PowerShellu.  
#### <a name="get-private-link-connection-states"></a>Získat stavy připojení privátního propojení 
Pomocí `Get-AzPrivateLinkService` rutiny můžete získat připojení privátního koncového bodu a jejich stavy.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Schválení připojení privátního koncového bodu 
 
Pomocí `Approve-AzPrivateEndpointConnection` rutiny schválíte připojení privátního koncového bodu. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Odepřít připojení privátního koncového bodu 
 
Pomocí `Deny-AzPrivateEndpointConnection` rutiny odmítnete připojení privátního koncového bodu. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Odebrat připojení privátního koncového bodu 
 
Pomocí `Remove-AzPrivateEndpointConnection` rutiny odeberte připojení privátního koncového bodu. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Azure CLI 
 
Používá `az network private-link-service update` se ke správě připojení privátního koncového bodu. Stav připojení je zadán v ```azurecli connection-status``` parametru. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Další kroky
- [Informace o privátních koncových bodech](private-endpoint-overview.md)
 
