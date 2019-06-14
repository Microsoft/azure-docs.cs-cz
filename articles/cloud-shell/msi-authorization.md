---
title: Použití spravované identity pro prostředky Azure ve službě Azure Cloud Shell | Dokumentace Microsoftu
description: Ověřování kódu pomocí Instalační služby MSI ve službě Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: damaerte
ms.openlocfilehash: 7cadaaf67f9c6923ee9e9eb2596941aa8e1f0c9b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60199478"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Použití spravované identity pro prostředky Azure ve službě Azure Cloud Shell

Azure Cloud Shell podporuje autorizace pomocí spravované identity pro prostředky Azure. Využijte tuto hodnotu na získání přístupových tokenů zabezpečeně komunikovat se službami Azure.

## <a name="about-managed-identities-for-azure-resources"></a>Informace o spravovaných identitách pro prostředky Azure
Běžné challenge vytváření cloudových aplikací po tom, jak bezpečně spravovat přihlašovací údaje, které musí být ve vašem kódu za účelem ověřování totožnosti ke cloudovým službám. Ve službě Cloud Shell, budete muset ověřit načtení ze služby Key Vault pro přihlašovací údaj, který možná bude nutné skript.

Spravované identity pro prostředky Azure díky řešení tohoto problému jednodušší tím, že automaticky spravovanou identitu služby Azure ve službě Azure Active Directory (Azure AD). Tuto identitu můžete použít k ověření pro jakoukoli službu, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje.

## <a name="acquire-access-token-in-cloud-shell"></a>Získání přístupového tokenu ve službě Cloud Shell

Spusťte následující příkazy pro nastavení tokenu přístupu MSI jako proměnnou prostředí `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Zpracování vypršení platnosti tokenu

Místní subsystému MSI ukládá do mezipaměti tokenů. Proto můžete volat ho tak často, jak se vám líbí a výsledky volání rozhraní na přenosu do služby Azure AD pouze v případě:
- z důvodu žádný token v mezipaměti dojde k neúspěšnému přístupu do mezipaměti
- platnost tokenu vypršela

Pokud mezipaměť tokenu v kódu, byste měli být připraveni zpracování scénářů, ve kterém prostředek označuje, že platnost tokenu vypršela.

Zpracování chyb, které token, najdete v tématu [MSI stránky o kulmy MSI přístupové tokeny](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

## <a name="next-steps"></a>Další postup
[Další informace o MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Získání přístupových tokenů z MSI virtuálních počítačů](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
