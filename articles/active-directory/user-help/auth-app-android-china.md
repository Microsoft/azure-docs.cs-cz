---
title: Microsoft Authenticator dostupnosti a omezení Foe Androidu v Číně | Microsoft Docs
description: Další informace o tom, jak získat aplikaci Microsoft Authenticator – dostupnost v Číně
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 05/06/2020
ms.author: curtand
ms.openlocfilehash: 3ba1b41859a543ab5e0393c5715dc49d452ea73c
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891834"
---
# <a name="microsoft-authenticator-for-android-in-the-public-cloud-in-china"></a>Microsoft Authenticator pro Android ve veřejném cloudu v Číně

Aplikace Microsoft Authenticator pro Android je dostupná ke stažení v Číně. Obchod Google Play není k dispozici v Číně, takže se aplikace musí stáhnout z jiných čínských Marketplace aplikací. Aplikace Microsoft Authenticator pro Android je aktuálně dostupná v následujících úložištích v Číně:

- [Baidu](https://shouji.baidu.com/software/26638379.html)
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

Místo používání nabízených oznámení pro službu Multi-Factor Authentication můžou uživatelé nastavit MFA pro příjem ověřovacích kódů na svém zařízení, které můžou použít k ověření své identity. Tyto ověřovací kódy jsou platné po dobu 30 sekund a jejich použití vyžaduje, aby si správci povolili, aby mohli provádět ověřování pomocí ověřovacích kódů s jednorázovým heslem (TOTP).

## <a name="availability"></a>Dostupnost

Microsoft Authenticator funkce | Dostupnost v Číně
------------------------------- | ---------------------
Registrace MFA pomocí nabízených oznámení | No
Již existující účet MFA ověřování identity pomocí nabízených oznámení | No
Již existující účet MFA, který provádí ruční kontrolu oznámení | Ano
Registrace MFA/ověřování pouze pomocí ověřovacích kódů TOTP/ověřování | Ano
Registrace přihlášení k telefonu | No
Existující přihlášení k telefonu pomocí nabízených oznámení | No
Stávající ověřování přihlášení k telefonu pomocí ruční kontroly žádostí o ověření | Ano

## <a name="next-steps"></a>Další kroky

- [Stažení a instalace aplikace Microsoft Authenticator](user-help-auth-app-download-install.md)
