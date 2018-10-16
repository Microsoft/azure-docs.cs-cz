---
title: Povolení víceklientské aplikace s Azure digitální dvojče | Dokumentace Microsoftu
description: Pochopení způsobu, jak zaregistrovat digitální dvojče Azure vašich zákazníků tenantů Azure Active Directory
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: mavoge
ms.openlocfilehash: e465ab95b670268f8ef31472259783fce8f24b36
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324097"
---
# <a name="how-to-enable-multitenant-applications-with-azure-digital-twins"></a>Povolení víceklientské aplikace s Azure digitální dvojče

Vývojáři na platformě Azure digitální dvojče pravděpodobně chtít vytváření víceklientských aplikací. A *víceklientské aplikaci* je jedna zřízené instance použít pro podporu více zákazníků každého s vlastní nezávislý datový a oprávnění.

Tento dokument podrobně popisuje, jak vytvořit digitální dvojče Azure víceklientskou aplikaci podporující několik tenantů Azure Active Directory (AD) a zákazníky.

## <a name="scenario-summary"></a>Zpráva scénáře

V tomto scénáři zvažte pro vývojáře D a C: zákazníka

- Pro vývojáře D má předplatné Azure s tenantem Azure AD.
- Pro vývojáře D s nasazeným digitální dvojče instance do svého předplatného Azure.
- Uživatelé v rámci tenanta Azure AD pro vývojáře D můžete získat tokeny pro službu digitální dvojče od Azure AD má vytvoření služby instanční objekt v tenantovi Azure AD pro vývojáře D.
- Mobilní aplikace, která se integruje přímo s rozhraním API pro správu digitálních Dvojčat vytvoří nyní Developer D.
- Pro vývojáře D pak umožní C zákazníka pomocí mobilních aplikací.
- Nyní C zákazník muset mít autorizaci k používání rozhraní API pro správu digitálních Dvojčat v rámci aplikace D pro vývojáře.

  > [!IMPORTANT]
  > - Když se zákazník C přihlásí do aplikace D pro vývojáře, aplikace nebude možné získat tokeny uživatele C zákazníka ke komunikaci s rozhraní API pro správu.
  > - Azure AD pak vyvolá chybu s informacemi, že digitální dvojče nebyl rozpoznán v adresáři C zákazníka.

## <a name="solution"></a>Řešení

K řešení výše uvedeného scénáře, je nutné tyto akce vytvořit digitální dvojče instanční objekt v rámci tenanta Azure AD zákazníka C:

- Pokud zákazník C ještě nemá předplatné Azure s tenantem Azure AD:

  - Správce Tenanta Azure AD zákazníka jazyka C bude nutné získat [předplatné Azure s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - Správce Tenanta Azure AD zákazníka jazyka C, pak bude mít k [propojte svého tenanta pomocí nového předplatného](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

- Z [webu Azure Portal](https://portal.azure.com), pak by měl zákazník C správce Tenanta Azure AD:
  1. Otevřít **předplatná**.
  1. Vyberte předplatné, který má tenanta Azure AD, který se má použít v aplikaci pro vývojáře D.
  1. Vyberte **poskytovatelů prostředků**.
  1. Vyhledejte **Microsoft.IoTSpaces**.
  1. Klikněte na tlačítko **zaregistrovat**.
  
## <a name="next-steps"></a>Další postup

Další informace o použití uživatelem definovaných funkcí s Azure digitální dvojče, [Azure digitální dvojče UDF](how-to-user-defined-functions.md).

Další informace o použití řízení přístupu na základě rolí pro dalšího zabezpečení aplikace pomocí přiřazení rolí, přečtěte si téma [řízení přístupu na základě rolí digitální dvojče](security-create-manage-role-assignments.md).
