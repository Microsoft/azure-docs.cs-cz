---
title: Jak vytvořit bezplatného tenanta Azure Active Directoryho vývojáře
description: V tomto článku se dozvíte, jak vytvořit vývojářský účet.
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 1e8bb59c09622a39dad680940ff34e643ee0cc3b
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222737"
---
# <a name="how-to-create-a-free-azure-active-directory-developer-tenant"></a>Jak vytvořit bezplatného tenanta Azure Active Directoryho vývojáře

> [!IMPORTANT]
> Azure Active Directory ověřitelných přihlašovacích údajů je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Ve verzi Preview je vyžadována licence P2. 

Existují dva jednoduché způsoby, jak vytvořit bezplatná Azure Active Directory s zkušební licencí P2, abyste mohli nainstalovat službu pro vystavitele ověřitelných přihlašovacích údajů a můžete testovat vytváření a ověřování ověřitelných přihlašovacích údajů:

- [Připojte](https://aka.ms/o365devprogram) se k bezplatnému Microsoft 365 vývojářskému programu a získejte bezplatné izolované prostory, nástroje a další prostředky, jako je Azure Active Directory s licencemi P2. Nakonfigurované uživatele, skupiny, poštovní schránky atd.
- Vytvořte nového [tenanta](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) a aktivujte [bezplatnou zkušební](https://azure.microsoft.com/trial/get-started-active-directory/) verzi Azure AD Premium P1 nebo P2 v novém tenantovi.

Pokud se rozhodnete zaregistrovat bezplatný program pro vývojáře Microsoft 365, musíte postupovat podle několika snadných kroků:

1. Klikněte na tlačítko **připojit** se na obrazovce.

2. Přihlaste se pomocí nového účtu Microsoft nebo použijte existující (pracovní) účet, který už máte.

3. Na stránce registrace vyberte oblast, zadejte název společnosti a před kliknutím na další klikněte na tlačítko **Další**.

4. Klikněte na **nastavit předplatné**. Určete oblast, ve které chcete vytvořit nového tenanta, vytvořte uživatelské jméno, doménu a zadejte heslo. Tím se vytvoří nový tenant a první správce tenanta.

5. Zadejte informace o zabezpečení potřebné k ochraně účtu správce nového tenanta. Tím se pro tento účet nastaví ověřování MFA.


V tuto chvíli jste vytvořili tenanta s 25 uživateli s uživatelskými licencemi. Mezi licence E5 patří licence Azure AD P2. Volitelně můžete přidat ukázkové datové sady s uživateli, skupinami, poštou a SharePointem, které vám pomůžou s testováním ve vývojovém prostředí. U ověřené služby pro vystavování přihlašovacích údajů nejsou vyžadovány.

Pro usnadnění práce byste mohli přidat vlastní pracovní účet jako [hosta](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) do nově vytvořeného tenanta a použít tento účet ke správě tenanta. Pokud chcete, aby účet Guest mohl spravovat ověřitelnou přihlašovací službu, musíte tomuto uživateli přiřadit roli globální správce.

## <a name="next-steps"></a>Další kroky

Teď, když máte vývojářský účet, si můžete vyzkoušet náš [první kurz](get-started-verifiable-credentials.md) , kde se dozvíte víc o ověřitelných přihlašovacích údajích.