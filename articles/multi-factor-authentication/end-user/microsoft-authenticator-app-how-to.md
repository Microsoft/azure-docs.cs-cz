---
title: Aplikace Microsoft Authenticator pro mobilních telefonů | Microsoft Docs
description: Naučte se upgradovat na nejnovější verzi Azure Authenticator.
services: multi-factor-authentication
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: H1Hack27Feb2017, end-user
ms.openlocfilehash: 1532054a9463d710685d3f865d2e26ee7ff5014f
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2018
ms.locfileid: "28200993"
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Začínáme s aplikací Microsoft Authenticator
Aplikace Microsoft Authenticator poskytuje další úroveň zabezpečení v pracovní nebo školní účet (například bsimon@contoso.com) nebo účtu Microsoft (například bsimon@outlook.com).

Aplikace funguje v jednom ze dvou způsobů:

* **Oznámení**. Aplikace může pomoci zabránit neoprávněnému přístupu k účtům a zastavit podvodné transakce vynucením oznámení tablet nebo smartphone. Jednoduše oznámení zobrazte a pokud je oprávněné, vyberte **ověřte**. Jinak, můžete vybrat **Odepřít**.
* **Ověřovací kód**. Aplikace můžete používat jako softwarový token pro generování k ověření kódu OAuth. Po zadání uživatelského jména a hesla, je třeba zadat kód poskytovaném aplikací na obrazovce přihlášení. Ověřovací kód poskytuje druhou podobu ověřování.

Aplikace Microsoft Authenticator nahrazuje aplikaci Azure Authenticator. Aplikace Azure Authenticator pořád funguje, ale pokud se rozhodnete přesunout do nové aplikace Microsoft Authenticator, tento článek vám může pomoci.  

## <a name="opt-in-for-two-step-verification"></a>Vyjádřit výslovný souhlas pro dvoustupňové ověření

Aplikace Microsoft Authenticator nefunguje samostatně. Konfigurovat účty a vyzvat vás k druhé metody ověřování, jakmile se přihlásíte pomocí uživatelského jména a hesla.

Pro pracovní nebo školní účet neobdržíte obvykle vybrat tuto funkci pro sami. Místo toho správce zabezpečení vyjádřit výslovný souhlas vaším jménem a potom upozorní vás, abyste zaregistrovat metody ověření pro váš účet. Pokud pro vás platí tento scénář, další informace v [co Azure Multi-Factor Authentication znamená pro mě nejlepší](multi-factor-authentication-end-user.md).

Pro osobní účet budete muset nastavit dvoustupňové ověřování pro sebe. Pokud máte účet Microsoft, tyto kroky jsou k dispozici v [o dvoustupňovém ověřování](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

Můžete taky Microsoft Authenticator s účty jiných společností než Microsoft. Tato funkce se volání něco jiného než dvoustupňové ověření, ale musí být schopen najít v části zabezpečení nebo nastavení přihlášení.

## <a name="install-the-app"></a>Nainstalujte aplikaci
Je k dispozici pro aplikaci Microsoft Authenticator [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594), a [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

## <a name="add-accounts-to-the-app"></a>Přidejte účty do aplikace
U každého účtu, který chcete přidat do aplikace Microsoft Authenticator použijte jednu z následujících postupů:

### <a name="add-a-personal-microsoft-account-to-the-app"></a>Přidat do aplikace osobní účet Microsoft

Osobní účet Microsoft (jeden, který používáte pro přihlášení k Outlook.com, Xbox, Skype, atd.) všechny, které musíte udělat je, přihlaste se ke svému účtu v aplikaci Microsoft Authenticator.

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>Přidat pracovní nebo školní účet do aplikace pomocí skeneru kód QR
1. Přejděte na obrazovku nastavení ověření zabezpečení.  Informace o tom, jak získat na tuto obrazovku najdete v tématu [Změna nastavení zabezpečení](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).
2. Zaškrtněte políčko vedle **ověřovací aplikaci** vyberte **konfigurace**.

    ![Tlačítka konfigurovat na obrazovce nastavení ověření zabezpečení](./media/authenticator-app-how-to/azureauthe.png)

    Otevře obrazovky s kód QR na něm.

    ![Obrazovka, která poskytuje kód QR](./media/authenticator-app-how-to/barcode2.png)
3. Otevřete aplikaci Microsoft Authenticator. Na **účty** obrazovku, vyberte  **+** a potom zadejte, zda chcete přidat pracovní nebo školní účet.
4. Použít fotoaparát naskenujte kód QR, a potom vyberte **provádí** zavřete obrazovce kód QR.

    Pokud svůj fotoaparát nepracuje správně, můžete [ručně zadejte kód QR a adresa URL](#add-an-account-to-the-app-manually).

5. Pokud aplikace zobrazuje název účtu s šestimístný kód pod ním, jste hotovi.

    ![Účty obrazovky](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Ručně přidat účet do aplikace
1. Přejděte na obrazovku nastavení ověření zabezpečení.  Informace o tom, jak získat na tuto obrazovku najdete v tématu [Změna nastavení zabezpečení](multi-factor-authentication-end-user-manage-settings.md).
2. Vyberte **konfigurace**.

    ![Tlačítka konfigurovat na obrazovce nastavení ověření zabezpečení](./media/authenticator-app-how-to/azureauthe.png)

    Otevře obrazovky s kód QR na něm.  Všimněte si kód a adresu URL.

    ![Obrazovky, který poskytuje kód QR a adresy URL](./media/authenticator-app-how-to/barcode2.png)
3. Otevřete aplikaci Microsoft Authenticator. Na **účty** obrazovku, vyberte  **+** a potom zadejte, zda chcete přidat pracovní nebo školní účet.

4. V skener, vyberte **ručně zadejte kód**.

    ![Obrazovka pro skenování kód QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)
5. Zadejte kód a adresu URL do příslušných polí v aplikaci a potom vyberte **Dokončit**.

    ![Obrazovka pro zadání kód a adresu URL](./media/authenticator-app-how-to/manual.png)

6. Pokud aplikace zobrazuje název účtu s šestimístný kód pod ním, jste hotovi.

    ![Účty obrazovky](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>Přidání účtu do aplikace pomocí otisku prstu nebo funkce rozpoznávání obličeje vašeho zařízení
Vaše organizace může vyžadovat kód PIN k dokončení výzvy ověřování. Aplikace Microsoft Authenticator můžete použít vaše zařízení otisk prstu nebo funkce rozpoznávání obličeje místo kódu PIN. Chcete-li toto nastavení na první ověřování v aplikaci, se zobrazí možnost použít Touch ID (pro iOS) nebo místo toho otisků identifikace. 

Pro nastavení pro Microsoft Authenticator Touch ID, musíte dokončit výzvy normální ověření pomocí kódu PIN. Microsoft Authenticator bude automaticky nastavena pro zařízení, která podporují Touch ID. 

![Ověření instalace Touch ID](./media/authenticator-app-how-to/touchid1.png)

Z tohoto bodu dál, když budete vyzváni k ověření vaše přihlášení, vyberte přijaté nabízených oznámení a kontrolovat otisk prstu místo zadávání svůj PIN kód.

![Nabízené oznámení](./media/authenticator-app-how-to/touchid2.png)

## <a name="use-the-app-when-you-sign-in"></a>Použití aplikace při přihlášení

Jakmile váš účet je přidán do aplikace, zobrazí se výzva provedete test ověření a ujistěte se, že všechno, co byla nakonfigurována správně. Potom je vše. Nemusíte dělat žádné další kroky až po příštím přihlášení.

Pokud jste se rozhodli použít ověřovací kódy v aplikaci, můžete začít neuvidíte na domovské stránce. Se změní každých 30 sekund, takže máte vždy nový kód, když budete potřebovat. Ale nemusíte dělat nic s nimi, dokud se přihlásit a zobrazí se výzva k zadání ověřovací kód.  
