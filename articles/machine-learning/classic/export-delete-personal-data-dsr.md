---
title: 'ML Studio (Classic): Export & odstranění dat – Azure'
description: Data v produktu uložená v Azure Machine Learning Studio (Classic) jsou k dispozici pro export a odstranění prostřednictvím Azure Portal a také prostřednictvím ověřených rozhraní REST API. K datům telemetrie se dá dostat prostřednictvím portálu ochrany osobních údajů Azure. V tomto článku se dozvíte, jak.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: 6bd86dc855e5a312550abe050603aeaf45b075ee
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362346"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio-classic"></a>Exportovat a odstranit data uživatelů v produktu z Azure Machine Learning Studio (Classic)

**platí pro:** ![ Platí pro. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ neplatí pro.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  




Můžete odstranit nebo exportovat data v produktu uložená v Azure Machine Learning Studio (Classic) pomocí Azure Portal, rozhraní studia (Classic), PowerShellu a ověřených rozhraní REST API. V tomto článku se dozvíte, jak. 

K datům telemetrie se dá dostat prostřednictvím portálu ochrany osobních údajů Azure. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>Jaké druhy uživatelských dat má Studio (Classic) shromažďovat?

Pro tuto službu se uživatelská data skládají z informací o uživatelích autorizovaných pro přístup k pracovním prostorům a k záznamům o interakcích uživatelů pomocí služby.

V Machine Learning Studio jsou dva druhy uživatelských dat (Classic):
- **Data osobního účtu:** ID účtu a e-mailové adresy přidružené k účtu.
- **Zákaznická data:** Data, která jste nahráli k analýze

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>Typy účtů studia (Classic) a způsob ukládání dat

Existují tři druhy účtů v Machine Learning Studio (Classic). Druh účtu, který určíte, jak jsou data uložená a jak je můžete odstranit nebo exportovat.

- **Pracovní prostor hosta** je bezplatný anonymní účet. Zaregistrujte se bez zadání přihlašovacích údajů, jako je e-mailová adresa nebo heslo.
    -  Po vypršení platnosti pracovního prostoru hosta se data vyprázdní.
    - Uživatelé typu Host můžou exportovat zákaznická data prostřednictvím uživatelského rozhraní, rozhraní REST API nebo balíčku PowerShellu.
- **Bezplatný pracovní prostor** je bezplatný účet, ke kterému se přihlašujete pomocí účet Microsoft přihlašovacích údajů – e-mailová adresa a heslo.
    - Můžete exportovat a odstranit osobní údaje a zákaznická data, která se vztahují na žádosti o práva subjektu údajů (DSR).
    - Zákaznická data můžete exportovat prostřednictvím uživatelského rozhraní, rozhraní REST API nebo balíčku PowerShellu.
    - U bezplatných pracovních prostorů, které nepoužívají účty Azure AD, se telemetrie dá exportovat pomocí portálu ochrany osobních údajů.
    - Při odstraňování pracovního prostoru odstraníte všechna osobní zákaznická data.
- **Standardní pracovní prostor** je placený účet, ke kterému přistupujete pomocí přihlašovacích údajů pro přihlášení.
    - Můžete exportovat a odstranit osobní údaje a zákaznická data, která se vztahují na požadavky na DSR.
    - K datům můžete přistupovat prostřednictvím portálu ochrany osobních údajů Azure.
    - Osobní údaje a zákaznická data můžete exportovat prostřednictvím uživatelského rozhraní, rozhraní REST API nebo balíčku PowerShellu.
    - Data můžete z Azure Portal odstranit.

## <a name="delete-workspace-data-in-studio-classic"></a><a name="delete"></a>Odstranění dat pracovního prostoru v studiu (klasické) 

### <a name="delete-individual-assets"></a>Odstranění jednotlivých assetů

Uživatelé mohou odstranit prostředky v pracovním prostoru, a to tak, že je vyberete a pak vyberete tlačítko Odstranit.

![Odstranění prostředků v Machine Learning Studio (klasické)](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Odstranit celý pracovní prostor

Uživatelé můžou také odstranit celý pracovní prostor:
- Placený pracovní prostor: odstranění prostřednictvím Azure Portal.
- Bezplatný pracovní prostor: použijte tlačítko Odstranit v podokně **Nastavení** .

![Odstranění bezplatného pracovního prostoru v Machine Learning Studio (Classic)](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>Export dat studia (Classic) pomocí PowerShellu
Použijte PowerShell k exportu všech informací do přenosného formátu z Azure Machine Learning Studio (Classic) pomocí příkazů. Informace najdete v článku [modul PowerShellu pro Azure Machine Learning Studio (Classic)](powershell-module.md) .

## <a name="next-steps"></a>Další kroky

Dokumentaci týkající se fakturace za webové služby a plán závazku najdete v tématu [Azure Machine Learning Studio (Classic) REST API Reference](https://docs.microsoft.com/rest/api/machinelearning/). 
