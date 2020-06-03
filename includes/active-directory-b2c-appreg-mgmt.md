---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 11728c62202b15f0052017609ac384e8ef259211
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298910"
---
#### <a name="app-registrations"></a>[Registrace aplikací](#tab/app-reg-ga/)

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací**a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *managementapp1*.
1. Vyberte **účty jenom v tomto organizačním adresáři**.
1. V části **oprávnění**zrušte zaškrtnutí políčka *udělit souhlas správce udělit oprávnění OpenID a offline_access* .
1. Vyberte **Zaregistrovat**.
1. Poznamenejte si **ID aplikace (klienta)** , které se zobrazí na stránce s přehledem aplikace. Tuto hodnotu použijete v pozdějším kroku.