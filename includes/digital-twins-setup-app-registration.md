---
author: baanders
description: zahrnutý soubor pro krok přístupových oprávnění v nastavení digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 45f55e6c18d7cc551a6b96504ad2ce2ec3f84d86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009637"
---
Po nastavení instance digitálního vlákna Azure je běžné pracovat s touto instancí prostřednictvím klientské aplikace. Aby bylo možné vytvořit funkční aplikaci klienta, bude nutné zajistit, aby klientská aplikace byla schopna ověřit se pomocí digitálních vláken Azure. K tomu je potřeba nastavit [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) **registraci aplikace** pro použití vaší klientské aplikace.

V této registraci aplikace nakonfigurujete přístupová oprávnění k [rozhraním API pro digitální vlákna Azure](../articles/digital-twins/how-to-use-apis-sdks.md). Později se klientská aplikace ověří proti registraci aplikace a v důsledku toho se jim přidělí nakonfigurovaná přístupová oprávnění k rozhraním API.

>[!TIP]
> Jako vlastník nebo správce předplatného můžete chtít nastavit novou registraci aplikace pro každou novou instanci služby Azure Digital provázání *nebo* to udělat jenom jednou a vytvořit jedinou registraci aplikace, která se bude sdílet mezi všemi instancemi digitálních vláken Azure v předplatném.

### <a name="create-the-registration"></a>Vytvořit registraci