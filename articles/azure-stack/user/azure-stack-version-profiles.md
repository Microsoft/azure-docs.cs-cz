---
title: Správa profilů verzí API ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o profilů verzí API ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 4977e96e215aa58873146fe57203bbd895df551e
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55769823"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Správa profilů verzí API ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Profily rozhraní API zadejte poskytovatel prostředků Azure a verze rozhraní API pro koncové body Azure REST. Můžete vytvořit vlastní klientů v různých jazycích pomocí profilů rozhraní API. Každý klient používá profil API kontaktovat poskytovatele prostředků správné a verzi rozhraní API pro službu Azure Stack.

Můžete vytvořit aplikaci pro práci s poskytovateli prostředků Azure bez nutnosti řazení přesně, která verze každého rozhraní API poskytovatele prostředků jsou kompatibilní s Azure Stack. Pouze zarovnání aplikace do profilu; Sada SDK se vrátí na správnou verzi rozhraní API.

Toto téma vám pomůže:

 - Vysvětlení profilů rozhraní API pro Azure Stack.
 - Zjistěte, jak můžete pomocí profilů rozhraní API pro vývoj vašeho řešení.
 - Najdete v článku, kde najdete pokyny týkající se kód.

## <a name="summary-of-api-profiles"></a>Přehled rozhraní API profily

- Profily rozhraní API se používá k reprezentování sadu poskytovatele prostředků Azure a jejich verzí rozhraní API.
- Rozhraní API profily byly vytvořeny pro vám pomůže vytvářet šablony napříč několika cloudy Azure. Profily poskytují rozhraní kompatibilní a stabilní.
- Profily jsou vydávány čtyřikrát za rok.
- Používají se tři zásady vytváření názvů profilu:
    - **nejnovější**  
        Obsahuje nejnovější verze rozhraní API v globální Azure.
    - **yyyy-mm-dd-hybrid**  
    Dvakrát ročně vydání této verze se zaměřuje na konzistenci a stabilitu napříč několika cloudy. Tento profil, zaměřuje optimální kompatibility služby Azure Stack.
    - **rrrr mm-dd-profile** optimální stabilitu a nejnovější funkce.

## <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Azure API profily a kompatibility služby Azure Stack

Nejnovější rozhraní API služby Azure profily nejsou kompatibilní s Azure Stack. Tyto zásady vytváření názvů můžete použít k identifikaci profily, které chcete použít pro svá řešení Azure Stack.

**nejnovější**  
Tento profil má nejaktuálnější verzi API součástí globální Azure, které nebudou fungovat ve službě Azure Stack. **Nejnovější** má největší počet nejnovější změny. Profil, který nabízí jste si poznamenali stabilitu a kompatibilitu s jinými cloudy. Pokud se pokoušíte použít nejnovější verze rozhraní API **nejnovější** tedy profil, který byste měli použít.

**Yyyy-mm-dd-hybrid**  
Tento profil je vydána v březnu a září každý rok. Má optimální stabilitu a kompatibility s různými cloudy. **Rrrr mm-dd hybridní** je určená k cílení na global Azure a Azure Stack. Verze rozhraní API služby Azure uvedené v tomto profilu budou stejné jako ty, které jsou uvedeny ve službě Azure Stack. Tento profil slouží k vývoji kódu pro hybridní Cloudová řešení.

**yyyy-mm-dd-profile**  
Tento profil je vydané pro globální Azure v červen a prosinec. Pomocí služby Azure Stack; nefunguje obvykle bude existovat mnoho rozbíjející změny. I když vyrovnává optimální stabilitu a nejnovější funkce, rozdíl mezi **nejnovější** a tento profil, který je **nejnovější** vždy se skládá z nejnovější verze rozhraní API, bez ohledu na to, kdy byla rozhraní API všeobecně dostupné. Například pokud nová verze rozhraní API pro rozhraní API pro Compute zítra tuto verzi rozhraní API je uvedena v **nejnovější**, ale ne v **rrrr mm-dd-profile** profilu, protože tento profil už existuje. **rrrr mm-dd-profile** zahrnuje nejnovější verze vydané před dne nebo před dne.

## <a name="azure-resource-manager-api-profiles"></a>Azure profily rozhraní API Resource Manageru

Azure Stack nepoužívá nejnovější verzi rozhraní API verze v globální Azure. Když vytvoříte řešení, musí najít verzi rozhraní API pro každý poskytovatel prostředků Azure, která je kompatibilní s Azure Stack.

Spíše než výzkum každý poskytovatel prostředků a konkrétní verzi služby Azure Stack, můžete použít profil rozhraní API. Profil, který určuje sadu poskytovatelů prostředků a verze rozhraní API. Sady SDK nebo vytvořené pomocí sady SDK, nástroj se vrátí k cíli `api-version` zadaný v profilu. Pomocí profilů rozhraní API můžete určit verzi profilu, která se vztahuje na celý šablonu a za běhu, vybere správnou verzi prostředku Azure Resource Manageru.

Profily rozhraní API můžete pracovat s nástroji, které používají Azure Resource Manageru, jako je PowerShell, rozhraní příkazového řádku Azure, k dispozici v sadě SDK a sady Microsoft Visual Studio code. Nástroje a sady SDK můžete použít profily ke čtení, kterou verzi moduly a knihovny, které chcete zahrnout při sestavování aplikace.

Například, pokud použijete k vytvoření účtu úložiště pomocí prostředí PowerShell **Microsoft.Storage** poskytovatele prostředků, která podporuje **verze api-version** 2016-03-30 a virtuální počítač pomocí  **Microsoft.Compute** poskytovatele prostředků s **verze api-version** 2015-12-01, musí vyhledat která podporuje modulu prostředí PowerShell 2016-03-30 pro úložiště a které modul podporuje 2015-02-01 pro výpočetní prostředky a pak Nainstalujte je. Místo toho můžete použít profil. Použijte rutinu `Install-Profile <profilename>`, a načte správnou verzi modulů prostředí PowerShell.

Podobně když pomocí sady Python SDK k sestavení aplikace založené na Pythonu, můžete zadat profilu. Sada SDK načte správné moduly pro poskytovatele prostředků, které jste zadali ve skriptu.

Jako vývojář můžou soustředit na psaní vašeho řešení. Místo prozkoumáváním údajů, které verze rozhraní API, poskytovatel prostředků a cloudu fungují společně, můžete použít profil a vědět, že váš kód funguje ve všech cloudech, které podporují tento profil.

## <a name="api-profile-code-samples"></a>Ukázky kódu profil rozhraní API

Můžete najít ukázky kódu pro usnadnění integrace vašich řešení s upřednostňovaného jazyka pomocí služby Azure Stack s použitím profilů. V současné době můžete najít pokyny a ukázky pro následující jazyky:

- **.NET** profilu rozhraní .NET API můžete použít k získání nejnovější, většina stabilní verzi každého typu prostředku v balíčku poskytovatele prostředků. Další informace najdete v tématu [profilů verzí použití rozhraní API s využitím .NET v Azure stacku](azure-stack-version-profiles-net.md).
- **PowerShell**  
Můžete použít **AzureRM.Bootstrapper** modulu, které jsou dostupné v galerii prostředí PowerShell zobrazíte rutiny prostředí PowerShell, který je vyžadována pro práci pomocí profilů verzí API. Informace najdete v tématu [profilů verzí API pomocí prostředí PowerShell pro](azure-stack-version-profiles-powershell.md).
- **Azure CLI**  
Můžete aktualizovat konfiguraci vašeho prostředí použít profil pro konkrétní verze rozhraní API Azure Stack. Informace najdete v tématu [profilů verzí API pomocí Azure CLI](azure-stack-version-profiles-azurecli2.md).
- **Go**  
Profil je v sadě Go SDK kombinaci různých typů prostředků s různými verzemi z různých služeb. Profily jsou k dispozici v části profily nebo cestu, jejich verze **rrrr-MM-DD** formátu. Informace najdete v tématu [profilů verzí API použijte pro Go](azure-stack-version-profiles-go.md).
- **Ruby**  
Ruby SDK pro Azure Resource Manageru zásobníku poskytuje nástroje, které vám pomůžou vytvářet a spravovat infrastrukturu. Poskytovatelé prostředků v sadě SDK patří výpočetní prostředky, virtuální sítě a úložiště dat pomocí jazyka Ruby. Informace najdete v tématu [profilů verzí použití rozhraní API pomocí Ruby](azure-stack-version-profiles-ruby.md)
- **Python**  
Python SDK podporuje profilů verzí API cílit na různé cloudové platformy, jako je Azure Stack a globální Azure. Profily rozhraní API můžete použít k vytváření řešení pro hybridní cloud. Informace najdete v tématu [profilů verzí použití rozhraní API s využitím Pythonu](azure-stack-version-profiles-python.md)

## <a name="next-steps"></a>Další postup

* [Instalace PowerShellu pro Azure Stack](azure-stack-powershell-install.md)
* [Konfigurace prostředí PowerShell uživatele Azure stacku](azure-stack-powershell-configure-user.md)
* [Přečtěte si podrobnosti o verze rozhraní API poskytovatele prostředků podporuje profily](azure-stack-profiles-azure-resource-manager-versions.md).
