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
ms.openlocfilehash: a2d9ece119003c341f49ee03d735d5636b179a32
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259883"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Povolení víceklientské aplikace s Azure digitální dvojče

Vývojáři, kteří používají Azure digitální dvojče obvykle mají být sestaveny víceklientské aplikace. A *víceklientské aplikaci* je zřízená jednu instanci, která podporuje více zákazníků. Každý zákazník má své vlastní nezávislý datový a oprávnění.

Tento dokument podrobně popisuje, jak vytvořit digitální dvojče Azure víceklientskou aplikaci, která podporuje několik tenantů Azure Active Directory (Azure AD) a zákazníky.

## <a name="scenario-summary"></a>Zpráva scénáře

V tomto scénáři zvažte pro vývojáře D a C: zákazníka

- Pro vývojáře D má předplatné Azure s tenantem Azure AD.
- Pro vývojáře D nasadí instanci digitální dvojče Azure do svého předplatného Azure.
- Uživatelé v rámci tenanta Azure AD pro vývojáře D můžete získat tokeny pro službu Azure digitální dvojče, protože Azure AD vytvořit instanční objekt v tenantovi Azure AD pro vývojáře D služby.
- Pro vývojáře D nyní vytvoří mobilní aplikace, která se integruje přímo s rozhraním API správy Dvojčat digitální Azure.
- Pro vývojáře D dovoluje zákazníka C mobilních aplikací.
- Zákazník C musí autorizaci k používání Management API digitální Dvojčat Azure v rámci aplikace D pro vývojáře.

  > [!IMPORTANT]
  > - Když se zákazník C přihlásí do aplikace D pro vývojáře, aplikace nemůže získat tokeny uživatele C zákazníka ke komunikaci s rozhraní API pro správu.
  > - Azure AD vyvolá chybu, což znamená, že digitální dvojče Azure nebyl rozpoznán v adresáři C zákazníka.

## <a name="solution"></a>Řešení

K vyřešení předchozího scénáře, jsou následující akce potřebné k vytvoření instančního objektu služby v rámci tenanta Azure AD zákazníka C digitální dvojče Azure:

- Pokud zákazník C ještě nemá předplatné Azure s tenantem Azure AD:

  - Musíte získat správce tenanta Azure AD zákazníka jazyka C [předplatné Azure s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - Správce tenanta Azure AD zákazníka jazyka C je pak nutné [propojte svého tenanta pomocí nového předplatného](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

- Na [webu Azure portal](https://portal.azure.com), zákazník C správce tenanta Azure AD má následující kroky:

  1. Otevřít **předplatná**.
  1. Vyberte předplatné, který má tenanta Azure AD, který se má použít v aplikaci pro vývojáře D.
  1. Vyberte **poskytovatelů prostředků**.
  1. Vyhledejte **Microsoft.IoTSpaces**.
  1. Vyberte **Zaregistrovat**.
  
## <a name="next-steps"></a>Další postup

Další informace o tom, jak pomocí Azure digitální dvojče uživatelsky definovaných funkcí, [Azure digitální dvojče UDF](how-to-user-defined-functions.md).

Další informace o použití řízení přístupu na základě rolí pro dalšího zabezpečení aplikace pomocí přiřazení rolí, přečtěte si téma [řízení přístupu Azure na základě rolí digitální dvojče](security-create-manage-role-assignments.md).
