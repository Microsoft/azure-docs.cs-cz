---
title: Pomocí Instalační služby MSI v prostředí cloudu Azure | Microsoft Docs
description: Ověřování kódu pomocí Instalační služby MSI v prostředí cloudu Azure
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: juluk
ms.openlocfilehash: 99577faf7328dc773a9da5f7c1227aa63600aa0a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31517440"
---
# <a name="use-msi-in-azure-cloud-shell"></a>Pomocí Instalační služby MSI v prostředí cloudu Azure

Prostředí Azure Cloud podporuje autorizace s spravovat identity služby (MSI). Využívat to načíst tokeny přístupu ke zabezpečeně komunikovat se službami Azure.

## <a name="about-managed-service-identity-msi"></a>O identitě spravované služby (MSI)
Běžné výzvu, při vytváření cloudové aplikace je bezpečně Správa přihlašovacích údajů, musí být v kódu ověřování pro cloudové služby. V prostředí cloudu budete muset ověřit načtení z Key Vault pro přihlašovací údaje, které může být nutné skript.

Identita spravované služby (MSI) usnadňuje řešení tohoto problému jednodušší tím, že služby Azure automaticky spravované identity v Azure Active Directory (Azure AD). Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault, bez nutnosti všechny přihlašovací údaje ve vašem kódu.

## <a name="acquire-access-token-in-cloud-shell"></a>Získání tokenu přístupu v prostředí cloudu

Spuštěním následujících příkazů pro nastavení přístupový token MSI jako proměnné prostředí, `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Zpracování vypršení platnosti tokenu

Subsystém místní MSI tokeny ukládá do mezipaměti. Proto můžete volat ho tak často, jak se vám líbí a volání na přenosu do služby Azure AD výsledků pouze, pokud:
- dojde k neúspěšnému přístupu do mezipaměti z důvodu žádné token v mezipaměti
- vypršela platnost tokenu

Pokud jste do mezipaměti tokenu ve vašem kódu, byste měli být připravení zpracovávat scénáře, kde prostředek udává, že vypršela platnost tokenu.

Ke zpracování tokenu chyb, navštivte [MSI stránky o kulmy MSI přístupové tokeny](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

## <a name="next-steps"></a>Další postup
[Další informace o MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Získání přístupových tokenů z virtuálních počítačů MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
