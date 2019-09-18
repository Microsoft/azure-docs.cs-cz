---
title: Přidat nativní klientskou aplikaci – Azure Active Directory B2C | Microsoft Docs
description: Naučte se, jak do tenanta Active Directory B2C přidat nativní klientskou aplikaci.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6fc953d5c6109fbc6eacbd946ecd112db4639fa5
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064595"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>Přidání nativní klientské aplikace do tenanta Azure Active Directory B2C

Aby mohla aplikace komunikovat s Azure Active Directory B2C, musí být ve vašem tenantovi zaregistrované prostředky nativního klienta.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikace**a pak vyberte **Přidat**.
2. Zadejte název aplikace. Například *nativeapp1*.
3. Pro **Zahrnout webovou aplikaci nebo webové rozhraní API**vyberte **ne**.
4. V případě **Zahrnout nativního klienta**vyberte **Ano**.
5. Pro **identifikátor URI přesměrování**zadejte platný identifikátor URI přesměrování s vlastním schématem. Pro výběr identifikátoru URI přesměrování existují dva důležité informace:

    - **Jedinečné** – schéma identifikátoru URI pro přesměrování by mělo být pro každou aplikaci jedinečné. V příkladu `com.onmicrosoft.contoso.appname://redirect/path` `com.onmicrosoft.contoso.appname` je schéma. Tento vzor by měl následovat. Pokud dvě aplikace sdílejí stejné schéma, uživateli je dána možnost zvolit aplikaci. Pokud uživatel vytvoří nesprávnou volbu, přihlášení se nezdařilo.
    - **Dokončit** – identifikátor URI pro přesměrování musí mít schéma a cestu. Cesta musí po doméně obsahovat alespoň jedno lomítko. Například `//contoso/` funguje a `//contoso` neúspěch. Ujistěte se, že identifikátor URI pro přesměrování neobsahuje speciální znaky, například podtržítka.

6. Klikněte na možnost **Vytvořit**.
7. Na stránce Vlastnosti Poznamenejte ID aplikace, které použijete při konfiguraci nativní klientské aplikace.
