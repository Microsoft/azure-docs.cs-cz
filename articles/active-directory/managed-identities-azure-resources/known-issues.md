---
title: Známé problémy se spravovanými identitami – Azure Active Directory
description: Známé problémy se spravovanými identitami pro prostředky Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 8d2b6323a15595e57e7e89c6a83f9f718422e1d7
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226918"
---
# <a name="known-issues-with-managed-identities"></a>Známé problémy se spravovanými identitami

Tento článek pojednává o několika problémech týkajících se spravovaných identit a o tom, jak je vyřešit. Běžné dotazy týkající se spravovaných identit jsou popsány v článku [Nejčastější dotazy](managed-identities-faq.md) .
## <a name="vm-fails-to-start-after-being-moved"></a>Po přesunu se virtuální počítač nepodařilo spustit. 

Pokud přesunete virtuální počítač v běžícím stavu ze skupiny prostředků nebo předplatného, během přesunu se i nadále spustí. Pokud se ale virtuální počítač zastaví a restartuje, po přesunutí se jeho spuštění nezdaří. K tomuto problému dochází, protože virtuální počítač neaktualizuje odkaz na spravované identity pro prostředky prostředků Azure a i nadále odkazuje na něj ve staré skupině prostředků.

**Alternativní řešení** 
 
Aktivujte na virtuálním počítači aktualizaci, aby mohla získat správné hodnoty pro spravované identity prostředků Azure. Změnou vlastnosti virtuálního počítače můžete aktualizovat odkaz na spravované identity pro identitu prostředků Azure. Můžete například nastavit novou hodnotu značky na virtuálním počítači pomocí následujícího příkazu:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Tento příkaz nastaví novou značku "fixVM" s hodnotou 1 na virtuálním počítači. 
 
Nastavením této vlastnosti se virtuální počítač aktualizuje se správnými spravovanými identitami pro identifikátor URI prostředku prostředků Azure a pak byste měli být schopni virtuální počítač spustit. 
 
Po spuštění virtuálního počítače se dá značku odebrat pomocí následujícího příkazu:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="transferring-a-subscription-between-azure-ad-directories"></a>Převod předplatného mezi adresáři služby Azure AD

Spravované identity se při přesunu nebo přenosu předplatného do jiného adresáře neaktualizují. V důsledku toho budou přerušeny všechny existující spravované identity přiřazené systémem nebo uživatelem. 

Alternativní řešení pro spravované identity v předplatném, které se přesunulo do jiného adresáře:

 - Pro spravované identity přiřazené systémem: zakažte a znovu povolte. 
 - U spravovaných identit přiřazených uživateli: Odstraňte, znovu vytvořte a připojte je znovu k potřebným prostředkům (například virtuálním počítačům).

Další informace najdete v tématu věnovaném [převodu předplatných Azure do jiného adresáře Azure AD](../../role-based-access-control/transfer-subscription.md).


## <a name="next-steps"></a>Další kroky

V našem článku najdete seznam [služeb, které podporují spravované identity](services-support-managed-identities.md) a [Nejčastější dotazy](managed-identities-faq.md) .
