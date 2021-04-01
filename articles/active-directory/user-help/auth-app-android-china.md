---
title: Microsoft Authenticator dostupnost a omezení pro Android v Číně | Microsoft Docs
description: Další informace o tom, jak získat aplikaci Microsoft Authenticator – dostupnost v Číně
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/27/2021
ms.author: curtand
ms.openlocfilehash: d499417414e560bdaa3fcd0a12255946aca413d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99054834"
---
# <a name="microsoft-authenticator-for-android-in-the-public-cloud-in-china"></a>Microsoft Authenticator pro Android ve veřejném cloudu v Číně

Aplikace Microsoft Authenticator pro Android je dostupná ke stažení v Číně. Obchod Google Play není k dispozici v Číně, takže se aplikace musí stáhnout z jiných čínských Marketplace aplikací. Aplikace Microsoft Authenticator pro Android je aktuálně dostupná v následujících úložištích v Číně:

- [Lenovo](https://www.lenovomm.com/appdetail/com.azure.authenticator/20197724)
- [Huawei](https://appgallery.cloud.huawei.com/uowap/index.html#/detailApp/C100262999?source=appshare&subsource=C100262999&shareTo=weixin&locale=zh_CN)
- [Samsung Galaxy Store](http://apps.samsung.com/appquery/appDetail.as?appId=com.azure.authenticator)

Nejaktuálnější sestavení aplikace je ve Obchod Google Play, ale aktualizujeme aplikaci ve všech ostatních obchodech s aplikacemi hned, jak můžeme. Vzhledem k tomu, že není k dispozici žádný vlastní balíček aplikace pro Android (APK) do žádného App Storu, může být aplikace bez problémů aktualizována z jednoho z následujících umístění:

- Úložiště, ze kterého se stáhlo
- Obchod Google Play, pokud uživatel přejíždí oblasti

## <a name="limitations"></a>Omezení

Aplikace Microsoft Authenticator pro Android používá Firebase cloudový systém zasílání zpráv od společnosti Google a Služby Google Play k přijímání nabízených oznámení. Protože žádná služba není k dispozici v Číně, existují určitá omezení funkcí aplikace:

- Registrace aplikace ověřovatele jako metody Multi-Factor Authentication (MFA) pomocí nabízených oznámení nefunguje.

- [Přihlášení telefonem](../authentication/howto-authentication-sms-signin.md) se nedá nastavit. Vyžaduje, aby uživatel aplikaci nastavil jako metodu MFA pomocí nabízených oznámení, která aktuálně nefungují.

Pokud uživatel dřív spravoval, aby v aplikaci nastavil přihlášení k telefonu nebo ověřování pomocí služby Multi-Factor Authentication, může v aplikaci provést ruční kontrolu žádostí o oznámení a použít je k ověření identity.

## <a name="multi-factor-authentication-workaround"></a>Alternativní řešení Multi-Factor Authentication

Místo používání nabízených oznámení pro službu Multi-Factor Authentication můžou uživatelé [nastavit svou ověřovací aplikaci tak, aby na svém zařízení přijímala ověřovací kódy](multi-factor-authentication-setup-auth-app.md#set-up-the-microsoft-authenticator-app-to-use-verification-codes) , které můžou použít pro MFA k ověření jejich identity. Tyto ověřovací kódy jsou platné po dobu 30 sekund a jejich použití musí správci povolit, aby mohli provádět ověřování pomocí ověřovacích kódů One-Time (Time-to-based Password).

## <a name="availability"></a>Dostupnost

Microsoft Authenticator funkce | Dostupnost v Číně
------------------------------- | ---------------------
Registrace MFA pomocí nabízených oznámení | No
Již existující účet MFA ověřování identity pomocí nabízených oznámení | No
Již existující účet MFA, který provádí ruční kontrolu oznámení | Yes
Registrace MFA/ověřování pouze pomocí ověřovacích kódů TOTP/ověřování | Yes
Registrace přihlášení k telefonu | No
Existující přihlášení k telefonu pomocí nabízených oznámení | No
Stávající ověřování přihlášení k telefonu pomocí ruční kontroly žádostí o ověření | Yes
Podpora ověřovací aplikace pro osobní účty Microsoft | No

## <a name="next-steps"></a>Další kroky

- [Stažení a instalace aplikace Microsoft Authenticator](user-help-auth-app-download-install.md)
