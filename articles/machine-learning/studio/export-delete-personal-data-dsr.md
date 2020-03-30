---
title: Export a odstranění dat
titleSuffix: ML Studio (classic) - Azure
description: Data v produktu uložená v Azure Machine Learning Studio (klasické) je k dispozici pro export a odstranění prostřednictvím portálu Azure a také prostřednictvím ověřených rozhraní API REST. K telemetrickým datům se dá přistupovat prostřednictvím portálu ochrany osobních údajů Azure. Tento článek vám ukáže, jak na to.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: c380d10d0c68794ec3810cea25341d68bb41400d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251684"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio-classic"></a>Export a odstranění uživatelských dat v produktu z Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Data v produktu uložená v Azure Machine Learning Studio (klasické) můžete odstranit nebo exportovat pomocí portálu Azure, rozhraní Studio (klasické), PowerShellu a ověřených rozhraní REST API. Tento článek vám řekne, jak na to. 

K telemetrickým datům se dá přistupovat prostřednictvím portálu ochrany osobních údajů Azure. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>Jaké druhy uživatelských dat studio (klasické) shromažďuje?

Pro tuto službu se uživatelská data skládají z informací o uživatelích oprávněných k přístupu k pracovním prostorům a telemetrických záznamů interakcí uživatelů se službou.

Ve machine learningovém studiu (klasické) existují dva druhy uživatelských dat:
- **Údaje o osobním účtu:** ID účtu a e-mailové adresy přidružené k účtu.
- **Údaje o zákaznících:** Data, která jste nahráli k analýze.

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>Typy účtů Studio (klasické) a způsob ukládání dat

Existují tři druhy účtů v Machine Learning Studio (klasické). Typ účtu, který máte, určuje, jak jsou data uložena a jak je můžete odstranit nebo exportovat.

- Pracovní **prostor pro hosty** je bezplatný anonymní účet. Zaregistrujete se bez zadání přihlašovacích údajů, jako je e-mailová adresa nebo heslo.
    -  Data jsou po vypršení platnosti pracovního prostoru hosta vymazána.
    - Uživatelé typu Host mohou exportovat zákaznická data prostřednictvím uživatelského prostředí, místních api nebo balíčku prostředí PowerShell.
- **Volný pracovní prostor** je bezplatný účet, ke kterým se přihlásíte pomocí přihlašovacích údajů k účtu Microsoft – e-mailové adresy a hesla.
    - Můžete exportovat a odstranit osobní a zákaznická data, která podléhají požadavkům na práva subjektu údajů (DSR).
    - Data zákazníků můžete exportovat prostřednictvím ui, rest API nebo balíčku PowerShellu.
    - Pro volné pracovní prostory, které nepoužívají účty Azure AD, telemetrie lze exportovat pomocí portálu ochrany osobních údajů.
    - Když pracovní prostor odstraníte, odstraníte všechna osobní zákaznická data.
- **Standardní pracovní prostor** je placený účet, ke kterým máte přístup pomocí přihlašovacích údajů.
    - Můžete exportovat a odstranit osobní a zákaznická data, která podléhají požadavkům DSR.
    - Přístup k datům můžete získat prostřednictvím portálu ochrany osobních údajů Azure
    - Osobní a zákaznická data můžete exportovat prostřednictvím ui, rest API nebo balíčku PowerShellu.
    - Svá data můžete odstranit na webu Azure Portal.

## <a name="delete-workspace-data-in-studio-classic"></a><a name="delete"></a>Odstranění dat pracovního prostoru ve studiu (klasické) 

### <a name="delete-individual-assets"></a>Odstranění jednotlivých datových zdrojů

Uživatelé mohou odstranit datové zdroje v pracovním prostoru tak, že je vyberou a pak vyberou tlačítko pro odstranění.

![Odstranění datových zdrojů ve studiu Strojového učení (klasické)](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Odstranění celého pracovního prostoru

Uživatelé mohou také odstranit celý pracovní prostor:
- Placený pracovní prostor: Odstranit prostřednictvím portálu Azure.
- Volná pracovní plocha: Použijte tlačítko Odstranit v podokně **Nastavení.**

![Odstranění volného pracovního prostoru ve studiu Machine Learning Studio (klasické)](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>Exportovat studiová (klasická) data pomocí PowerShellu
Pomocí PowerShellu můžete exportovat všechny informace do přenosného formátu z Azure Machine Learning Studio (klasické) pomocí příkazů. Další informace najdete v [článku modul PowerShell pro Azure Machine Learning Studio (klasické).](powershell-module.md)

## <a name="next-steps"></a>Další kroky

Dokumentace týkající se webových služeb a fakturace plánu závazků najdete v [tématu Azure Machine Learning Studio (klasický) REST API reference](https://docs.microsoft.com/rest/api/machinelearning/). 
