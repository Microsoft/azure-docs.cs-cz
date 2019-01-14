---
title: Azure Stack SQL prostředků poskytovatele 1.1.30.0 poznámky k verzi | Dokumentace Microsoftu
description: Další informace o novinky v nejnovější Azure Stack prostředků poskytovatele aktualizaci SQL, včetně všech známých problémů a kde ho můžete stáhnout.
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
ms.openlocfilehash: 759ace67c2adb02003103934c4d5d363470b3068
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247482"
---
# <a name="sql-resource-provider-11330-release-notes"></a>Poznámky k verzi 1.1.33.0 poskytovatele prostředků SQL

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a známých problémech ve verzi poskytovatele prostředků 1.1.33.0 SQL.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhnout poskytovatele prostředků SQL binární a pak spusťte Self-Extractor extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimální odpovídající Azure Stack sestavení. Minimální verze služby Azure Stack požadovaná k instalaci této verze poskytovatele prostředků SQL jsou uvedeny níže:

> |Minimální verze služby Azure Stack|Verze poskytovatele prostředků SQL|
> |-----|-----|
> |Verze. 1808 (1.1808.0.97)|[SQL RP verze 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Platí minimální podporované aktualizace služby Azure Stack pro Azure Stack integrované systému nebo nasadit nejnovější Azure Stack Development Kit (ASDK) před nasazením nejnovější verzi poskytovatele prostředků SQL.

## <a name="new-features-and-fixes"></a>Nové funkce a opravy
Tato verze poskytovatele prostředků Azure Stack SQL zahrnuje následující vylepšení a opravy:

### <a name="fixes"></a>Opravy
- **Rozšíření portálu poskytovatele prostředků SQL rozhodnout, že nesprávné předplatné**. Poskytovatele prostředků SQL využívá Azure Resource Manageru volání k určení prvního správce předplatného služby použití, které nemusí být *výchozí předplatné poskytovatele*. Pokud k tomu dojde, poskytovatele prostředků SQL nebude fungovat normálně. 

- **Hostitelského serveru SQL nemá v seznamu prostředí databáze.** Vytvoří uživatele databáze nemusí být uvedeny při procházení prostředků klienta pro hostitelské servery SQL.

- **Předchozí nasazení zprostředkovatele (1.1.30.0) prostředků SQL může selhat, pokud není povolený protokol TLS 1.2**. Aktualizovat poskytovatele prostředků SQL 1.1.33.0 k povolení protokolu TLS 1.2, při nasazení poskytovatele prostředků, aktualizuje se zprostředkovatel prostředků nebo otočení tajných kódů. 

- **Tajný otočení poskytovatele prostředků SQL selže**. Oprava potíží, což vede k následujícím kódem chyby při obměně tajných kódů: ` New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Známé problémy 

- **SKU SQL může trvat až hodinu, uvidí na portálu**. Může trvat až hodinu nově vytvořený skladová jednotka byla viditelná jenom pro použití při vytváření nové databáze SQL. 

    **Alternativní řešení**: Žádné.

- **Znovu použít přihlášeních SQL**. Pokus o vytvoření nové SQL přihlásit se pomocí stejné uživatelské jméno jako stávající přihlašovací údaje v rámci stejného předplatného způsobí opětovné použití stejné přihlašovací údaje a stávající heslo. 

    **Alternativní řešení**: Použít různá uživatelská jména, při vytváření nové přihlašovací údaje v rámci stejného předplatného nebo vytvářet přihlášení se stejným uživatelským jménem v rámci různých předplatných.

- **Sdílené přihlašovací údaje SQL způsobovat datové nekonzistence**. Pokud je přihlašovací jméno SQL sdílí pro více databází SQL v rámci stejného předplatného, změna hesla přihlášení způsobí nekonzistenci dat.

    **Alternativní řešení**: Vždy používejte jiné přihlašovací údaje pro různé databáze v rámci stejného předplatného.

- **Poskytovatele prostředků SQL se nepodařilo přidat naslouchací proces SQL serveru Always On**. Pokud používáte SQL Server vždy na naslouchací proces IP adresu naslouchacího procesu, poskytovatele prostředků SQL virtuálního počítače nemůže přeložit název hostitele naslouchacího procesu.

    **Alternativní řešení**: Ujistěte se, že DNS funguje správně přeložit IP adresu naslouchacího procesu na název naslouchacího procesu hostitele.

### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Známé problémy pro správce cloudu provoz služby Azure Stack
Přečtěte si dokumentaci v [zpráva k vydání verze Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Další postup
[Další informace o poskytovateli prostředků SQL](azure-stack-sql-resource-provider.md).

[Příprava na nasazení poskytovatele prostředků SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Upgrade z předchozí verze poskytovatele prostředků SQL](azure-stack-sql-resource-provider-update.md). 