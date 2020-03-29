---
title: Správa připojení privátního koncového bodu v Azure
description: Zjistěte, jak spravovat privátní připojení koncových bodů v Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 62b24b3e2f5c1b89fa7db581ac34cf58381db2a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452965"
---
# <a name="manage-a-private-endpoint-connection"></a>Správa připojení privátního koncového bodu
Azure Private Link funguje na modelu toku volání schválení, kde příjemce služby Private Link může požádat o připojení k poskytovateli služeb pro využívání služby. Poskytovatel služeb se pak může rozhodnout, zda povolí spotřebiteli připojení či nikoli. Azure Private Link umožňuje poskytovatelům služeb spravovat připojení privátního koncového bodu na jejich prostředky. Tento článek obsahuje pokyny, jak spravovat připojení private endpoint.

![Správa soukromých koncových bodů](media/manage-private-endpoint/manage-private-endpoint.png)

Existují dvě metody schvalování připojení, ze kterých si může spotřebitel služby Private Link vybrat:
- **Automaticky**: Pokud má příjemce služby oprávnění RBAC pro prostředek poskytovatele služeb, může zvolit metodu automatického schvalování. V takovém případě, když se požadavek dostane k prostředku poskytovatele služeb, není od poskytovatele služeb vyžadována žádná akce a připojení je automaticky schváleno. 
- **Příručka**: Naopak, pokud příjemce služby nemá oprávnění RBAC pro prostředek poskytovatele služeb, může spotřebitel zvolit metodu ručního schválení. V takovém případě se požadavek na připojení zobrazí na prostředky služby jako **čekající na vyřízení**. Poskytovatel služeb musí žádost před navázáním připojení ručně schválit. V ručních případech může příjemce služby také zadat zprávu s požadavkem, který poskytuje poskytovateli služeb více kontextu. Poskytovatel služeb má následující možnosti, ze kterých si může vybrat pro všechna připojení Privátní koncový bod: **Schváleno**, **Odmítnout**, **Odebrat**.

Níže uvedená tabulka ukazuje různé akce poskytovatele služeb a výsledné stavy připojení pro soukromé koncové body.  Poskytovatel služeb může také změnit stav připojení připojení privátní koncový bod připojení později bez zásahu spotřebitele. Akce aktualizuje stav koncového bodu na straně příjemce. 


|Akce poskytovatele služeb   |Stav soukromého koncového bodu příjemce služby   |Popis   |
|---------|---------|---------|
|Žádný    |    Čekající na vyřízení     |    Připojení je vytvořeno ručně a čeká na schválení vlastníkem prostředku private link.       |
|Schválení    |  Schválené       |  Připojení bylo automaticky nebo ručně schváleno a je připraveno k použití.     |
|Odmítnout     | Rejected        | Připojení bylo odmítnuto vlastníkem prostředku soukromého propojení.        |
|Odebrat    |  Odpojen       | Připojení bylo odebráno vlastníkem prostředku soukromého propojení, soukromý koncový bod se stane informativní a měl by být odstraněn pro vyčištění.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Správa připojení privátních koncových bodů v prostředcích Azure PaaS
Portál je upřednostňovaná metoda pro správu privátní připojení koncového bodu na prostředky Azure PaaS. V současné době nemáme podporu PowerShell/CLI pro správu připojení na prostředky Azure PaaS.
1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
2. Přejděte do Centra soukromých spojnic.
3. V části **Zdroje**vyberte typ prostředku, který chcete spravovat připojení privátního koncového bodu.
4. Pro každý typ prostředku můžete zobrazit počet připojení soukromého koncového bodu, která jsou k němu přidružena. Podle potřeby můžete filtrovat prostředky.
5. Vyberte připojení privátního koncového bodu.  Pod uvedenými připojeními vyberte připojení, které chcete spravovat. 
6. Stav připojení můžete změnit výběrem z možností v horní části.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Správa připojení privátního koncového bodu ve službě Private Link vlastněné zákazníkem/partnerem

Azure PowerShell a Azure CLI jsou upřednostňované metody pro správu privátního koncového bodu připojení na Microsoft Partner Services nebo služeb vlastněných zákazníky. V současné době nemáme žádnou podporu portálu pro správu připojení ve službě Private Link.  
 
### <a name="powershell"></a>PowerShell 
  
Ke správě připojení privátního koncového bodu použijte následující příkazy prostředí PowerShell.  
#### <a name="get-private-link-connection-states"></a>Získat stavy připojení private link 
Pomocí `Get-AzPrivateLinkService` rutiny získat připojení private endpoint a jejich stavy.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Schválení připojení privátního koncového bodu 
 
Pomocí `Approve-AzPrivateEndpointConnection` rutiny schvalte připojení Private Endpoint. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Odepřít připojení soukromého koncového bodu 
 
Pomocí `Deny-AzPrivateEndpointConnection` rutiny odmítnout připojení Private Endpoint. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Odebrat připojení soukromého koncového bodu 
 
Pomocí `Remove-AzPrivateEndpointConnection` rutiny odeberte připojení Private Endpoint. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Azure CLI 
 
Slouží `az network private-link-service update` ke správě připojení private endpoint. Stav připojení je určen ```azurecli connection-status``` v parametru. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Další kroky
- [Další informace o privátním koncových bodech](private-endpoint-overview.md)
 
