---
title: Co je nového v Azure Active Directory ve službě Microsoft 365 Government – Azure Active Directory | Dokumentace Microsoftu
description: Přečtěte si o některých změnách Azure Active Directory (Azure AD) v Microsoftu 365 Government Cloudová instance, což může ovlivnit.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b077c7b5efbad2add971d42ff31938b56f6bc33
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258891"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Co je nového v Azure Active Directory ve službě Microsoft 365 Government

Provedli jsme některé změny do služby Azure Active Directory (Azure AD) v Microsoftu 365 Government Cloudová instance, která se vztahuje na zákazníky, kteří používají tyto služby:

- Microsoft Azure Government

- Microsoft 365 Government – GCC vysoká

- Microsoft 365 Government – DoD

Tento článek se nevztahuje na Microsoft 365 Government – GCC zákazníků.

## <a name="changes-to-the-initial-domain-name"></a>Změny bude počáteční název domény

Během vaší organizace počáteční registraci si uživatelé pro online služby Microsoftu 365 Government, byly muset zvolte název domény vaší organizace, `<your-domain-name>.onmicrosoft.com`. Pokud už máte názvu domény pomocí přípony .com, nic se změní.

Nicméně, pokud se zaregistrujete pro novou službu Microsoft 365 Government, budete vyzváni zvolit použití názvu domény `.us` příponu. Ano, bude `<your-domain-name>.onmicrosoft.us`.

>[!Note]
>Tato změna se nevztahuje na všechny zákazníky, kteří spravují poskytovatelé cloudových služeb (CSP).

## <a name="changes-to-portal-access"></a>Změny pro přístup k portálu

Aktualizovali jsme portál koncové body pro Microsoft Azure Government, Microsoft 365 Government – vysoká GCC a Microsoft 365 Government – amerického ministerstva obrany, jak je znázorněno [koncový bod tabulky mapování](#endpoint-mapping).

Zákazníci by mohla dříve přihlásit pomocí Azure po celém světě (portal.azure.com) a Office 365 (portal.office.com) portálů. S touto aktualizací se zákazníci musí nyní přihlaste konkrétní Microsoft Azure Government, Microsoft 365 Government – vysoká GCC a Microsoft 365 Government – DoD portály.

## <a name="endpoint-mapping"></a>Mapování na koncový bod

V následující tabulce jsou uvedeny koncových bodů pro všechny zákazníky:

| Name | Podrobnosti o koncovém bodu |
|------|------------------|
| Portály |Microsoft Azure Government: https://portal.azure.us<p>Microsoft 365 Government – GCC vysokou: https://portal.office365.us<p>Microsoft 365 Government – DoD: https://portal.apps.mil |
| Koncového bodu autority Azure Active Directory | https://login.microsoftonline.us |
| Azure Active Directory Graph API | https://graph.windows.net |
| Microsoft Graph API pro Microsoft 365 Government - GCC vysoká | https://graph.microsoft.us |
| Microsoft Graph API pro Microsoft 365 Government – ministerstva obrany USA | https://dod-graph.microsoft.us |
| Koncové body služeb Azure Government | Podrobnosti najdete v tématu [– Příručka pro vývojáře Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Microsoft 365 Government - GCC vysokou koncových bodů | Podrobnosti najdete v tématu [Office 365 US Koncové body vysoké GCC státní správy](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 Government – ministerstva obrany USA | Podrobnosti najdete v tématu [Office 365 US Koncové body Government ministerstva obrany USA](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Další postup

Další informace najdete v těchto článcích:

- [Co je Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Aktualizace koncového bodu autority AAD Azure Government](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Microsoft Graph koncových bodů v cloudu pro státní správu USA](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 U.S. Government GCC vysoké a ministerstva obrany USA](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)