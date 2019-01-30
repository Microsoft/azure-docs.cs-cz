---
title: Azure Stack MySQL prostředků poskytovatele 1.1.30.0 poznámky k verzi | Dokumentace Microsoftu
description: Další informace o novinky v nejnovější Azure Stack MySQL prostředků poskytovatele aktualizaci, včetně všech známých problémů a kde ho můžete stáhnout.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 7f44e8c2c4587ecfdb3bd5eb4304789674da96f3
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252000"
---
# <a name="mysql-resource-provider-11330--release-notes"></a>Poznámky k verzi poskytovatele 1.1.33.0 prostředků MySQL

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a známé problémy v MySQL verze zprostředkovatele prostředků 1.1.33.0.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhnout poskytovatele prostředků MySQL binární a pak spusťte Self-Extractor extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimální odpovídající Azure Stack sestavení. Minimální verze služby Azure Stack požadovaná k instalaci této verze poskytovatele prostředků MySQL jsou uvedeny níže:

> |Minimální verze služby Azure Stack|Verze poskytovatele prostředků MySQL|
> |-----|-----|
> |Verze. 1808 (1.1808.0.97)|[Poskytovatele prostředků MySQL verze 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Platí minimální podporované aktualizace služby Azure Stack pro Azure Stack integrované systému nebo nasadit nejnovější Azure Stack Development Kit (ASDK) před nasazením nejnovější verzi poskytovatele prostředků MySQL.

## <a name="new-features-and-fixes"></a>Nové funkce a opravy
Tato verze poskytovatele prostředků Azure Stack MySQL zahrnuje následující vylepšení a opravy:

### <a name="fixes"></a>Opravy
- **Rozšíření portálu poskytovatele prostředků MySQL rozhodnout, že nesprávné předplatné**. Poskytovatele prostředků MySQL pomocí Azure Resource Manageru volání k určení prvního správce předplatného služby použití, které nemusí být *výchozí předplatné poskytovatele*. Pokud k tomu dojde, poskytovatele prostředků MySQL normálně nefunguje. 

- **MySQL, který neobsahuje hostitelský server hostitelem databází.** Vytvoří uživatele databáze nemusí být uvedeny při procházení prostředků klienta pro hostitelské servery MySQL.

- **Předchozí nasazení zprostředkovatele (1.1.30.0) prostředků MySQL může selhat, pokud není povolený protokol TLS 1.2**. Aktualizovat poskytovatele prostředků MySQL 1.1.33.0 k povolení protokolu TLS 1.2, při nasazení poskytovatele prostředků, aktualizuje se zprostředkovatel prostředků nebo otočení tajných kódů. 

- **Selhání tajných kódů otočení poskytovatele prostředků MySQL**. Oprava potíží, což vede k následujícím kódem chyby při obměně tajných kódů: ` New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Známé problémy 

- **SKU MySQL může trvat až hodinu, uvidí na portálu**. Může trvat až hodinu nově vytvořený skladová jednotka byla viditelná jenom pro použití při vytváření nových databází MySQL. 

    **Alternativní řešení**: Žádné.

- **Znovu použít přihlašovací údaje MySQL**. Při pokusu o vytvoření nové databáze MySQL přihlášení pomocí stejné uživatelské jméno jako stávající přihlašovací údaje v rámci stejného předplatného způsobí opětovné použití stejné přihlašovací údaje a stávající heslo. 

    **Alternativní řešení**: Použít různá uživatelská jména, při vytváření nové přihlašovací údaje v rámci stejného předplatného nebo vytvářet přihlášení se stejným uživatelským jménem v rámci různých předplatných.

- **Sdílené přihlašovací údaje MySQL způsobovat datové nekonzistence**. Pokud přihlášení MySQL je sdílený více databází MySQL v rámci stejného předplatného, změna hesla přihlášení způsobí nekonzistenci dat.

    **Alternativní řešení**: Vždy používejte jiné přihlašovací údaje pro různé databáze v rámci stejného předplatného.


### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Známé problémy pro správce cloudu provoz služby Azure Stack
Přečtěte si dokumentaci v [zpráva k vydání verze Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Další postup
[Další informace o poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider.md).

[Příprava na nasazení poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Upgrade z předchozí verze poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-update.md). 