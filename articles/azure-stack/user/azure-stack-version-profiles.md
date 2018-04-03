---
title: Správa profilů verze rozhraní API v Azure zásobníku | Microsoft Docs
description: Další informace o profilech verze rozhraní API v Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 8A336052-8520-41D2-AF6F-0CCE23F727B4
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 452ed1de0588b380747edaa44dd0cc3805c51392
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Správa profilů verze rozhraní API v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Profily rozhraní API zadejte poskytovatel prostředků Azure a verze rozhraní API pro koncové body Azure REST. Můžete vytvořit vlastní klientů v různých jazycích pomocí profilů rozhraní API. Každý klient používá rozhraní API profil kontaktovat poskytovatele prostředků vpravo a verzi rozhraní API pro Azure zásobníku. 

Můžete vytvořit aplikaci pro práci s zprostředkovatelé prostředků Azure, aniž by bylo nutné vyřešit přesně, jaká verze každý poskytovatel prostředků rozhraní API je kompatibilní s Azure zásobníku. Právě zarovnat vaší aplikace do profilu; Sada SDK se vrátí do správné verze API-Version.


Toto téma vám pomůže:
 - Porozumět profily rozhraní API pro Azure zásobníku.
 - Jak můžete profily rozhraní API pro vývoj řešení.
 - Kde hledat kód konkrétní pokyny.

## <a name="summary-of-api-profiles"></a>Souhrn rozhraní API profilů

- Profily rozhraní API se používají k vyjádření sadu zprostředkovatelé prostředků Azure a jejich verze rozhraní API.
- Profily rozhraní API byly vytvořeny pro vývojáře k vytvoření šablony napříč několika Cloudech Azure. Jsou navrženy splňují vaše potřebu rozhraní kompatibilní a stabilní.
- Profily jsou vydávány čtyřikrát za rok.
- Jsou tři profil zásady vytváření názvů:
    - **latest**  
        Nejnovější verze rozhraní API vydané v Azure.
    - **yyyy-mm-dd-hybrid**  
    Vydání na pololetní cadence, tato verze zaměřuje na konzistenci a stabilitu napříč více cloudy.
    - **yyyy-mm-dd-profile**  
    Je umístěna mezi optimální stability a nejnovější funkce.

## <a name="azure-resource-manager-api-profiles"></a>Azure Resource Manager API profily

Azure zásobníku nepoužívá nejnovější verzi z verzí rozhraní API v globální Azure nalezen. Při vytváření vlastní řešení, budete muset najít verze rozhraní API pro každý poskytovatel prostředků v Azure, který je kompatibilní s Azure zásobníku.

Spíše než výzkum každý poskytovatel prostředků a konkrétní verzi podporovanou serverem zásobník Azure, můžete použít profil aplikace API. Profil určuje sadu zprostředkovatelé prostředků a verze rozhraní API. Sady SDK nebo nástroj vytvořené pomocí sady SDK, se vrátí k cílové verze rozhraní api-zadaná v profilu. Pomocí profilů rozhraní API můžete zadat verze profilu, který se vztahuje na šablonu celý a v době běhu Azure Resource Manager vybere správnou verzi prostředku.

Profily rozhraní API pracovat s nástroji, které používají Azure Resource Manager, například prostředí PowerShell, rozhraní příkazového řádku Azure, kódu, které jsou součástí sady SDK a sadu Microsoft Visual Studio. Nástroje a sady SDK můžete použít profily číst kterou verzi modulů a knihovny, které chcete zahrnout při vytváření aplikace.

Například, pokud účet pomocí prostředí PowerShell k vytvoření úložiště pomocí **Microsoft.Storage** poskytovatele prostředků, který podporuje rozhraní api-version 2016-03-30 a virtuální počítač pomocí rozhraní api verze 2015-12-01 zprostředkovateli prostředků Microsoft.Compute. , potřebovali byste k vyhledání který podporuje modul prostředí PowerShell 2016-03-30 pro úložiště a které modul podporuje 2015-02-01 pro výpočet a instalovat je. Místo toho můžete k profilu. Použijte rutinu ** instalace profilu * profilename *** a prostředí PowerShell načte správnou verzi modulů.

Podobně pokud používáte Python SDK k vytvoření aplikace na základě Python, můžete zadat profil. Sada SDK načte správné moduly pro poskytovatele prostředků, které jste zadali ve vašem skriptu.

Jako vývojář můžete se zaměřit na zápis vašeho řešení. Místo analýza, které verze api Version, poskytovatel prostředků a které cloudové funguje společně, můžete použít profil a vědět, že váš kód bude fungovat přes všechny cloudy, které podporují tento profil.

## <a name="api-profile-code-samples"></a>Ukázky kódu rozhraní API profilu

Můžete najít ukázky kódu můžete integrovat řešení preferovaný jazyk zásobníkem Azure s použitím profilů. V současné době najdete pokyny a ukázky pro následující jazyky:

- **PowerShell**  
Můžete použít **AzureRM.Bootstrapper** modulu, které jsou k dispozici prostřednictvím Galerie prostředí PowerShell získat rutiny prostředí PowerShell, které jsou nutné k práci pomocí profilů verze rozhraní API.  
Informace najdete v tématu [profily verze rozhraní API pomocí prostředí PowerShell pro](azure-stack-version-profiles-powershell.md).
- **Azure CLI 2.0**  
Můžete aktualizovat konfiguraci prostředí použít profil pro konkrétní verzi rozhraní API zásobník Azure.  
Informace v tématu [profily verze pomocí rozhraní API pro Azure CLI 2.0](azure-stack-version-profiles-azurecli2.md).
- **GO**  
V sadě SDK přejděte profil je kombinaci různých typů prostředků s různými verzemi z jiné služby. profily jsou k dispozici v části profily nebo cestu s jejich verze v **rrrr-MM-DD** formátu.  
Informace v tématu [profily verze pomocí rozhraní API pro přejděte](azure-stack-version-profiles-go.md).

## <a name="next-steps"></a>Další postup
* [Instalace PowerShellu pro Azure Stack](azure-stack-powershell-install.md)
* [Konfigurace prostředí PowerShell Azure zásobník uživatele](azure-stack-powershell-configure-user.md)
* [Přečtěte si podrobnosti o verze API poskytovatele prostředků podporuje profily](azure-stack-profiles-azure-resource-manager-versions.md).
