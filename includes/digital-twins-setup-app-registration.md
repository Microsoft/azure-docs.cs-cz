---
author: baanders
description: zahrnutý soubor pro krok přístupových oprávnění v nastavení digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 18aa039977beeff3f6b16249822fbd98370ca404
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408257"
---
Po nastavení instance digitálního vlákna Azure je běžné pracovat s touto instancí prostřednictvím klientské aplikace. Aby bylo možné vytvořit funkční aplikaci klienta, bude nutné zajistit, aby klientská aplikace byla schopna ověřit se pomocí digitálních vláken Azure. K tomu je potřeba nastavit [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) **registraci aplikace** pro použití vaší klientské aplikace.

V této registraci aplikace nakonfigurujete přístupová oprávnění k [rozhraním API pro digitální vlákna Azure](../articles/digital-twins/how-to-use-apis-sdks.md). Později se klientská aplikace ověří proti registraci aplikace a v důsledku toho se jim přidělí nakonfigurovaná přístupová oprávnění k rozhraním API.

>[!TIP]
> Jako vlastník předplatného můžete chtít nastavit novou registraci aplikace pro každou novou instanci digitálních vláken Azure *nebo* to udělat jenom jednou a vytvořit jedinou registraci aplikace, která se bude sdílet mezi všemi instancemi digitálních vláken Azure v předplatném.

### <a name="create-the-registration"></a>Vytvořit registraci