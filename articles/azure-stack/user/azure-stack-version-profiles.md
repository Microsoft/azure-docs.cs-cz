---
title: Správa profilů verze rozhraní API v Azure zásobníku | Microsoft Docs
description: Další informace o profilech verze rozhraní API v Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: adbe88a44ac38868a68a6845c328ef4cf7fba60c
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604433"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Správa profilů verze rozhraní API v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Profily rozhraní API zadejte poskytovatel prostředků Azure a verze rozhraní API pro koncové body Azure REST. Můžete vytvořit vlastní klientů v různých jazycích pomocí profilů rozhraní API. Každý klient používá rozhraní API profil kontaktovat zprostředkovatele správný zdroj a verzi rozhraní API pro Azure zásobníku.

Můžete vytvořit aplikaci pro práci s zprostředkovatelé prostředků Azure, aniž by bylo nutné vyřešit přesně, jaká verze každý poskytovatel prostředků rozhraní API je kompatibilní s Azure zásobníku. Právě zarovnat vaší aplikace do profilu; Sada SDK přejde na správnou verzi rozhraní API.

Toto téma vám pomůže:

 - Porozumět profily rozhraní API pro Azure zásobníku.
 - Zjistěte, jak pomocí profilů rozhraní API pro vývoj řešení.
 - Zjistit, kde najít informace specifické pro kód.

## <a name="summary-of-api-profiles"></a>Souhrn rozhraní API profilů

- Profily rozhraní API se používají k vyjádření sadu zprostředkovatelé prostředků Azure a jejich verze rozhraní API.
- Profily rozhraní API byly vytvořeny pro vytvoření šablony napříč více Azure cloudy. Profily jsou navrženy pro splňují vaše potřeby pro rozhraní kompatibilní a stabilní.
- Profily jsou vydávány čtyřikrát za rok.
- Používají se tři profil zásady vytváření názvů:
    - **Nejnovější**  
        Obsahuje nejnovější verze rozhraní API vydané v globální Azure.
    - **yyyy-mm-dd-hybrid**  
    Vydání na pololetní cadence, tato verze se zaměřuje na konzistenci a stabilitu napříč více cloudy. Tento profil cílí optimální kompatibility zásobník Azure.
    - **rrrr mm-dd profil** nachází mezi optimální stability a nejnovější funkce.

### <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Azure profily rozhraní API a kompatibility Azure zásobníku

Nejnovější profily rozhraní API služby Azure nejsou kompatibilní s Azure zásobníku. Tyto zásady vytváření názvů slouží k identifikaci profily, které chcete použít pro vaše řešení Azure zásobníku.

**Nejnovější**  
Tento profil je aktuální verze rozhraní API v globální Azure, která nebude fungovat v Azure zásobníku nalezen. **Nejnovější** má největší počet nejnovější změny. Profil převádí z produkce stability a kompatibilitu s ostatních cloudů. Pokud se pokoušíte použít aktuální verze rozhraní API, **nejnovější** tedy profil, měli byste použít.

**Rrrr mm-dd hybridní**  
Tento profil je vydána v březnu a v září každý rok. Tento profil má optimální stability a kompatibilitu s různými cloudy. **Rrrr mm-dd hybridní** slouží k cílové globální Azure a Azure zásobníku. Verze rozhraní API služby Azure, uvedené v tomto profilu budou stejné jako ty, které jsou uvedeny v zásobníku Azure. Můžete vyvíjet kódu pro hybridní cloudové řešení pro tento profil.

**yyyy-mm-dd-profile**  
Tento profil je vydané pro globální Azure v červnu a dne. Tento profil nebude fungovat s Azure zásobníku; obvykle bude mnoho nejnovější změny. I když se nachází mezi optimální stability a nejnovější funkce rozdíl mezi **nejnovější** a tento profil je, že **nejnovější** bude vždy sestávat z nejnovější verze rozhraní API, bez ohledu na to, kdy rozhraní API byla vydána. Například pokud nová verze rozhraní API se vytvoří pro rozhraní API výpočetní zítra, že verze rozhraní API bude uvedena v **nejnovější**, ale ne v **rrrr mm-dd profil** vzhledem k tomu, že tento profil již existuje.  **rrrr mm-dd profil** popisuje aktuální verze vydané před června nebo před prosinec.

## <a name="azure-resource-manager-api-profiles"></a>Azure Resource Manager API profily

Azure zásobníku nepoužívá nejnovější verzi rozhraní API verze najít v globální Azure. Při vytváření řešení, budete muset najít verze rozhraní API pro každý poskytovatel prostředků Azure, který je kompatibilní s Azure zásobníku.

Spíše než výzkum každý poskytovatel prostředků a konkrétní verzi podporovanou serverem zásobník Azure, můžete použít profil aplikace API. Profil určuje sadu zprostředkovatelé prostředků a verze rozhraní API. Sady SDK nebo nástroj vytvořené pomocí sady SDK, se vrátí k cílové verze rozhraní api-zadaná v profilu. Pomocí profilů rozhraní API můžete zadat verze profilu, který se vztahuje na šablonu celý a v době běhu Azure Resource Manager vybere správnou verzi prostředku.

Profily rozhraní API pracovat s nástroji, které používají Azure Resource Manager, například prostředí PowerShell, rozhraní příkazového řádku Azure, kódu, které jsou součástí sady SDK a sadu Microsoft Visual Studio. Nástroje a sady SDK můžete použít profily číst kterou verzi modulů a knihovny, které chcete zahrnout při vytváření aplikace.

Například, pokud účet pomocí prostředí PowerShell k vytvoření úložiště pomocí **Microsoft.Storage** poskytovatele prostředků, který podporuje rozhraní api-version 2016-03-30 a virtuální počítač pomocí rozhraní api verze 2015-12-01 zprostředkovateli prostředků Microsoft.Compute. , potřebovali byste k vyhledání který podporuje modul prostředí PowerShell 2016-03-30 pro úložiště a které modul podporuje 2015-02-01 pro výpočet a instalovat je. Místo toho můžete k profilu. Použijte rutinu ** instalace profilu * profilename *** a prostředí PowerShell načte správnou verzi modulů.

Podobně pokud používáte Python SDK k vytvoření aplikace na základě Python, můžete zadat profil. Sada SDK načte správné moduly pro poskytovatele prostředků, které jste zadali ve vašem skriptu.

Jako vývojář můžete se zaměřit na zápis vašeho řešení. Místo analýza, které verze api Version, poskytovatel prostředků a které cloudové funguje společně, můžete použít profil a vědět, že váš kód bude fungovat přes všechny cloudy, které podporují tento profil.

## <a name="api-profile-code-samples"></a>Ukázky kódu rozhraní API profilu

Můžete najít ukázky kódu můžete integrovat řešení preferovaný jazyk zásobníkem Azure s použitím profilů. V současné době najdete pokyny a ukázky pro následující jazyky:

- **PowerShell**  
Můžete použít **AzureRM.Bootstrapper** modulu, které jsou k dispozici prostřednictvím Galerie prostředí PowerShell získat rutiny prostředí PowerShell, které jsou nutné k práci pomocí profilů verze rozhraní API. Informace najdete v tématu [profily verze rozhraní API pomocí prostředí PowerShell pro](azure-stack-version-profiles-powershell.md).
- **Azure CLI 2.0**  
Můžete aktualizovat konfiguraci prostředí použít profil pro konkrétní verzi rozhraní API zásobník Azure. Informace najdete v tématu [profily verze pomocí rozhraní API pro Azure CLI 2.0](azure-stack-version-profiles-azurecli2.md).
- **GO**  
V sadě SDK přejděte profil je kombinaci různých typů prostředků s různými verzemi z jiné služby. profily jsou k dispozici v části profily nebo cestu s jejich verze v **rrrr-MM-DD** formátu. Informace najdete v tématu [profily verze pomocí rozhraní API pro přejděte](azure-stack-version-profiles-go.md).
- **Ruby**  
Ruby SDK pro Azure Resource Manager zásobníku poskytuje nástroje, které vám pomohou vytvářet a spravovat infrastrukturu. Zprostředkovatelé prostředků v sadě SDK zahrnují výpočty, virtuální sítě a úložiště s Ruby jazyk. Informace najdete v tématu [profily verze rozhraní API pro použití s Ruby](azure-stack-version-profiles-ruby.md)
- **Python**  
- Python SDK podporuje profily verze rozhraní API pro platformu jiný cloud, jako je například Azure zásobníku a globální Azure. Pomocí profilů rozhraní API pro vytváření řešení pro hybridní cloud. Informace najdete v tématu [pomocí rozhraní API verze profily s Pythonem](azure-stack-version-profiles-python.md)

## <a name="next-steps"></a>Další postup

* [Instalace PowerShellu pro Azure Stack](azure-stack-powershell-install.md)
* [Konfigurace prostředí PowerShell Azure zásobník uživatele](azure-stack-powershell-configure-user.md)
* [Přečtěte si podrobnosti o verze API poskytovatele prostředků podporuje profily](azure-stack-profiles-azure-resource-manager-versions.md).