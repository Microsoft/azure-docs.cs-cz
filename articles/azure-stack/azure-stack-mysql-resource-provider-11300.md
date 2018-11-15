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
ms.date: 11/14/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 6354212eb95fbefb217dd5339613d050da55f4ba
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688125"
---
# <a name="mysql-resource-provider-11300--release-notes"></a>Poznámky k verzi poskytovatele 1.1.30.0 prostředků MySQL

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a známé problémy v MySQL verze zprostředkovatele prostředků 1.1.30.0.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhnout poskytovatele prostředků MySQL binární a pak spusťte Self-Extractor extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimální odpovídající Azure Stack sestavení. Minimální verze služby Azure Stack požadovaná k instalaci této verze poskytovatele prostředků MySQL jsou uvedeny níže:

> |Minimální verze služby Azure Stack|Verze poskytovatele prostředků MySQL|
> |-----|-----|
> |Aktualizace Azure Stack. 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Platí minimální podporované aktualizace služby Azure Stack pro Azure Stack integrované systému nebo nasadit nejnovější Azure Stack Development Kit (ASDK) před nasazením nejnovější verzi poskytovatele prostředků MySQL.

## <a name="new-features-and-fixes"></a>Nové funkce a opravy
Tato verze poskytovatele prostředků Azure Stack MySQL zahrnuje následující vylepšení a opravy:

- **Telemetrická data povolená pro nasazení poskytovatele prostředků MySQL**. Nasazení poskytovatele prostředků MySQL je povoleno shromažďování telemetrie. Shromažďovat telemetrii zahrnuje nasazení poskytovatele prostředků, spuštění a zastavení časy, ukončete stavové zprávy o ukončení a podrobnosti o chybě (pokud existuje).

- **Aktualizace šifrování TLS 1.2**. Povoleno jen 1.2 podpora TLS pro prostředek zprostředkovatele komunikace s interní komponenty služby Azure Stack. 

### <a name="fixes"></a>Opravy

- **Kompatibilita Azure Stack Powershellu poskytovatele prostředků MySQL**. Poskytovatele prostředků MySQL byla aktualizována na pracovním profilem Azure Stack 2018-03-01hybridní prostředí PowerShell a k zajištění kompatibility se AzureRM 1.3.0 a novější.

- **MySQL přihlášení změnit heslo okno**. Opravili jsme problém, kde heslo nelze změnit, v okně změnit heslo. Upozornění na změnu odstraněny odkazy z hesla.

## <a name="known-issues"></a>Známé problémy 

- **SKU MySQL může trvat až hodinu, uvidí na portálu**. Může trvat až hodinu nově vytvořený skladová jednotka byla viditelná jenom pro použití při vytváření nových databází MySQL. 

    **Alternativní řešení**: žádné.

- **Znovu použít přihlašovací údaje MySQL**. Při pokusu o vytvoření nové databáze MySQL přihlášení pomocí stejné uživatelské jméno jako stávající přihlašovací údaje v rámci stejného předplatného způsobí opětovné použití stejné přihlašovací údaje a stávající heslo. 

    **Alternativní řešení**: použít různá uživatelská jména, při vytváření nové přihlašovací údaje v rámci stejného předplatného nebo vytvářet přihlášení se stejným uživatelským jménem v rámci různých předplatných.


### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Známé problémy pro správce cloudu provoz služby Azure Stack
Přečtěte si dokumentaci v [zpráva k vydání verze Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Další postup
[Další informace o poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider.md).

[Příprava na nasazení poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Upgrade z předchozí verze poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-update.md). 