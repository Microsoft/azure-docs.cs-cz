---
title: Použití spravovaných identit pro prostředky v Azure Cloud Shellu
description: Ověření kódu pomocí MSI v Prostředí Azure Cloud Shell
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/14/2018
ms.openlocfilehash: a5d49a16324a5a97f4a0507f9abf47ea602ea072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72328712"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Použití spravovaných identit pro prostředky Azure v Azure Cloud Shellu

Azure Cloud Shell podporuje autorizaci se spravovanými identitami pro prostředky Azure. Využijte to k načtení přístupových tokenů k bezpečné komunikaci se službami Azure.

## <a name="about-managed-identities-for-azure-resources"></a>Informace o spravovaných identitách pro prostředky Azure
Běžnou výzvou při vytváření cloudových aplikací je, jak bezpečně spravovat přihlašovací údaje, které musí být ve vašem kódu pro ověřování cloudových služeb. V prostředí Cloud Shell může být nutné ověřit načítání z trezoru klíčů pro pověření, které může skript potřebovat.

Spravované identity pro prostředky Azure zjednodušují řešení tohoto problému tím, že poskytují službám Azure automaticky spravovanou identitu ve službě Azure Active Directory (Azure AD). Tuto identitu můžete použít k ověření pro jakoukoli službu, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje.

## <a name="acquire-access-token-in-cloud-shell"></a>Získání přístupového tokenu v prostředí Cloud Shell

Proveďte následující příkazy a nastavte přístupový token `access_token`MSI jako proměnnou prostředí .
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Zpracování vypršení platnosti tokenu

Místní podsystém MSI ukládá tokeny. Proto můžete volat tak často, jak chcete, a on-the-wire volání výsledků Azure AD pouze v případě, že:
- dojde k chybě mezipaměti z důvodu žádného tokenu v mezipaměti
- platnost tokenu vypršela

Pokud uložíte token do mezipaměti ve vašem kódu, měli byste být připraveni na zpracování scénářů, kde prostředek označuje, že platnost tokenu vypršela.

Chcete-li zpracovat chyby tokenů, navštivte [stránku MSI o curlingu přístupových tokenů MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

## <a name="next-steps"></a>Další kroky
[Další informace o MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Získání přístupových tokenů z virtuálních měn MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
