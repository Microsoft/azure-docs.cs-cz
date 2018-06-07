---
title: Export a odstranění dat z Machine Learning Studio - Azure | Microsoft Docs
description: V produktu data uložená pomocí Azure Machine Learning Studio je k dispozici pro export a odstranění prostřednictvím portálu Azure a také prostřednictvím ověřeného rozhraní REST API. Telemetrická data lze přistupovat prostřednictvím portálu Azure o ochraně osobních údajů. Tento článek ukazuje, jak.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: cgronlun
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.date: 05/25/2018
ms.openlocfilehash: 6317d4baba5775c1e5a4fda66de80dd8299d8fed
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654853"
---
# <a name="export-and-delete-in-product-user-data-from-machine-learning-studio"></a>Export a odstranění dat uživatele v produktu z Machine Learning Studio

Můžete odstranit nebo export dat v produktu uložené pomocí Azure Machine Learning Studio pomocí portálu Azure, rozhraní Studio, Powershellu a ověření rozhraní REST API. Tento článek vysvětluje, jak. 

Telemetrická data jsou přístupné prostřednictvím portálu Azure o ochraně osobních údajů. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-collect"></a>Jaké druhy dat uživatele Studio shromáždit?

Uživatelská data se skládá z informací o uživatelích, oprávnění pro přístup k pracovních prostorů a telemetrie záznamy interakce uživatele se službou pro tuto službu.

Existují dva druhy dat uživatele v nástroji Machine Learning Studio:
- **Osobní účet data:** ID účtu a e-mailové adresy, které jsou přidružené k účtu.
- **Data zákazníků:** dat, který jste nahráli k analýze.

## <a name="studio-account-types-and-how-data-is-stored"></a>Typy účtů Studio a způsob uložení dat

Existují tři typy účtů v nástroji Machine Learning Studio. Druh účtu, který máte Určuje, jak se data ukládají a jak můžete odstranit nebo ho exportovat.

- A **hosta prostoru** je účet zdarma, který anonymní. Musíte se zaregistrovat bez zadání pověření, například e-mailová adresa nebo heslo.
    -  Data se vyprazdňují po vypršení platnosti prostoru hosta.
    - Data zákazníků prostřednictvím uživatelského rozhraní, rozhraní API REST nebo PowerShell balíček můžete exportovat uživatele typu Host.
- A **volného prostoru** je bezplatný účet přihlášení k se společností Microsoft přihlašovací údaje - e-mailovou adresu a heslo účtu.
    - Můžete exportovat a odstranit osobní a k zákaznickým data, která se vztahují požadavky na data subjektu práva (DSR).
    - Můžete exportovat data zákazníků prostřednictvím uživatelského rozhraní, rozhraní API REST nebo PowerShell balíčku.
    - Bezplatné pracovní prostory nepoužíváte účty Azure AD, telemetrická data je možné exportovat pomocí portálu ochrany osobních údajů.
    - Pokud odstraníte pracovním prostoru, odstraníte všechna osobní data.
- A **standardní prostoru** je placený účet přístup s přihlašovací údaje.
    - Můžete exportovat a odstranit osobní a k zákaznickým data, která se vztahují DSR požadavky.
    - Můžete přistupovat k datům prostřednictvím portálu Azure o ochraně osobních údajů
    - Můžete exportovat data pro osobní a zákazníka prostřednictvím uživatelského rozhraní, rozhraní API REST nebo PowerShell balíčku
    - Vaše data na portálu Azure můžete odstranit.

## <a name="delete-workspace-data-in-studio"></a>Odstranit data prostoru v Studio 

### <a name="delete-individual-assets"></a>Odstranit jednotlivé prostředky

Uživatelé může odstranit prostředky v pracovním prostoru vyberte je a potom vyberete tlačítko Odstranit.

![Odstranění prostředků v nástroji Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Odstranit celou pracovní prostor

Uživatele můžete také odstranit jejich celého pracovního prostoru:
- Placené pracovního prostoru: odstranění prostřednictvím portálu Azure.
- Volného prostoru: použijte tlačítko Odstranit v **nastavení** podokně.

![Odstranit volného prostoru v nástroji Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-data-with-powershell"></a>Export dat Studio pomocí prostředí PowerShell
Použití prostředí PowerShell pro export všechny informace do přenosného formátu z Azure Machine Learning Studio pomocí příkazů. Informace najdete v tématu [modul prostředí PowerShell pro Azure Machine Learning](powershell-module.md) článku.

## <a name="next-steps"></a>Další postup

Dokumentace zahrnující závazků plán fakturace a webových služeb, naleznete v části [REST API služby Azure Machine Learning odkaz](https://docs.microsoft.com/en-us/rest/api/machinelearning/). 
