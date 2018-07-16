---
title: Aplikace Microsoft Authenticator pro mobilní telefony – Azure AD | Dokumentace Microsoftu
description: Zjistěte, jak upgradovat na nejnovější verzi Azure Authenticator.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/17/2017
ms.author: lizross
ms.reviewer: librown
ms.custom: H1Hack27Feb2017, end-user
ms.openlocfilehash: 8241dcaaf5623a22f4fc485f021766276472fb51
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059820"
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Začínáme s aplikací Microsoft Authenticator
Aplikace Microsoft Authenticator poskytuje další úroveň zabezpečení ve vaší pracovní nebo školní účet (třeba bsimon@contoso.com) nebo účtu Microsoft (třeba bsimon@outlook.com).

Aplikace funguje v jednom ze dvou způsobů:

* **Oznámení**. Aplikace můžou pomoct zabránit neoprávněnému přístupu k účtům a zastavte podvodné transakce doručením (push) oznámení na tablet nebo smartphone. Jednoduše oznámení zobrazte a pokud je legitimní, vyberte **ověřte**. V opačném případě můžete vybrat **Odepřít**.
* **Ověřovací kód**. Aplikace slouží jako softwarový token k vygenerování kódu ověřování OAuth. Po zadání uživatelského jména a hesla, zadejte kód aplikace k dispozici na přihlašovací obrazovku. Ověřovací kód, který obsahuje druhou podobu ověřování.

Aplikace Microsoft Authenticator nahrazuje aplikace Azure Authenticator. Aplikace Azure Authenticator stále funguje, ale pokud se rozhodnete přejít na novou aplikaci Microsoft Authenticator, tento článek vám může pomoci.  

## <a name="opt-in-for-two-step-verification"></a>Vyjádřit výslovný souhlas pro dvoustupňové ověřování.

Aplikace Microsoft Authenticator nefunguje samostatně. Nakonfigurujte každý z vašich účtů s výzvou k zadání druhé metody ověřování po přihlášení pomocí uživatelského jména a hesla.

Pro pracovní nebo školní účet nebudete mít obvykle vybrat tuto funkci sami. Místo toho správce zabezpečení vyjádřit výslovný souhlas vaším jménem a potom upozorní vás, abyste zaregistrovat metody ověřování pro váš účet. Pokud pro vás platí tento scénář, další informace najdete v [Azure Multi-Factor Authentication význam pro mě](multi-factor-authentication-end-user.md).

Pro osobní účet budete muset nastavit dvoustupňové ověřování pro sebe. Pokud máte účet Microsoft, jsou k dispozici v těchto kroků [o dvoustupňovém ověřování](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

Můžete použít také aplikaci Microsoft Authenticator s účty jiných společností než Microsoft. Funkci se zavolat něco jiného než dvoustupňové ověřování, ale by měl být schopen najít v části zabezpečení nebo nastavení přihlášení.

## <a name="install-the-app"></a>Nainstalujte si aplikaci
Aplikace Microsoft Authenticator je k dispozici pro [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) a [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

## <a name="add-accounts-to-the-app"></a>Přidat účty do aplikace
Pro každý účet, který chcete přidat do aplikace Microsoft Authenticator použijte jednu z následujících postupů:

### <a name="add-a-personal-microsoft-account-to-the-app"></a>Přidejte do aplikace osobní účet Microsoft

Pro osobní účet Microsoft (ten, který používáte pro přihlášení k Outlook.com, Xbox, Skype, atd.) vše, co musíte udělat, je přihlásit ke svému účtu v aplikaci Microsoft Authenticator.

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>Přidat pracovní nebo školní účet do aplikace pomocí skener kódů QR
1. Přejděte na obrazovku nastavení ověření zabezpečení.  Informace o tom, jak získat na této obrazovce najdete v tématu [změnou nastavení zabezpečení](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).
2. Zaškrtněte políčko vedle položky **aplikace Authenticator** vyberte **konfigurovat**.

    ![Tlačítko Konfigurovat v dialogovém okně Nastavení ověření zabezpečení](./media/microsoft-authenticator-app-how-to/azureauthe.png)

    Tím se zobrazí obrazovky pomocí kódu QR v něm.

    ![Obrazovka, která obsahuje kód QR](./media/microsoft-authenticator-app-how-to/barcode2.png)
3. Otevřete aplikaci Microsoft Authenticator. Na **účty** obrazovky, vyberte **+** a pak určete, že chcete přidat pracovní nebo školní účet.
4. Pomocí fotoaparátu/kamery naskenovat kód QR a pak vyberte **provádí** zavřete obrazovky kód QR.

    Pokud vaše kamera nepracuje správně, můžete si [QR kód a adresu URL zadat ručně](#add-an-account-to-the-app-manually).

5. Pokud aplikace zobrazí šestimístný kód pod ním název svého účtu, pak budete hotovi.

    ![obrazovka s účty](./media/microsoft-authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Ručně přidat účet do aplikace
1. Přejděte na obrazovku nastavení ověření zabezpečení.  Informace o tom, jak získat na této obrazovce najdete v tématu [změnou nastavení zabezpečení](multi-factor-authentication-end-user-manage-settings.md).
2. Vyberte **nakonfigurovat**.

    ![Tlačítko Konfigurovat v dialogovém okně Nastavení ověření zabezpečení](./media/microsoft-authenticator-app-how-to/azureauthe.png)

    Tím se zobrazí obrazovky pomocí kódu QR v něm.  Všimněte si kód a adresu URL.

    ![Obrazovka, která poskytuje QR kód a adresu URL](./media/microsoft-authenticator-app-how-to/barcode2.png)
3. Otevřete aplikaci Microsoft Authenticator. Na **účty** obrazovky, vyberte **+** a pak určete, že chcete přidat pracovní nebo školní účet.

4. Skener, vyberte **zadat kód ručně**.

    ![Obrazovka pro skenování kódu QR](./media/microsoft-authenticator-app-how-to/scan2.png)
5. Zadejte kód a adresu URL do příslušných polí v aplikaci a pak vyberte **Dokončit**.

    ![Obrazovka pro zadání kód a adresu URL](./media/microsoft-authenticator-app-how-to/manual.png)

6. Pokud aplikace zobrazí šestimístný kód pod ním název svého účtu, pak budete hotovi.

    ![obrazovka s účty](./media/microsoft-authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>Přidat účet do aplikace pomocí funkce rozpoznávání obličeje nebo otisků prstů vašeho zařízení
Vaše organizace může vyžadovat kód PIN k dokončení výzvy ověřování. Aplikace Microsoft Authenticator můžete použít funkce rozpoznávání obličeje nebo otisků prstů vašeho zařízení místo kódu PIN. Chcete-li toto nastavení na první ověření v aplikaci, se zobrazí možnost Touch ID použít (pro iOS) nebo otisků prstů identifikace místo. 

Nastavení Touch ID pro aplikaci Microsoft Authenticator, budete muset provést challenge normální ověření pomocí kódu PIN. Microsoft Authenticator se automaticky nastaví pro zařízení, která podporují Touch ID. 

![Ověření nastavení Touch ID](./media/microsoft-authenticator-app-how-to/touchid1.png)

Od bodu vpřed, když je nutné ověřit vaše přihlášení, vyberte přijatý nabízené oznámení a kontrolovat váš otisk prstu místo zadávání PINU.

![Nabízené oznámení](./media/microsoft-authenticator-app-how-to/touchid2.png)

## <a name="use-the-app-when-you-sign-in"></a>Použití aplikace při přihlášení

Jakmile se váš účet je přidán do aplikace, můžete být vyzváni provést ověření testů, ujistěte se, že všechno, co byla správně nakonfigurovaná. Potom budete hotovi! Nemusíte dělat nic dalšího až při příštím přihlášení.

Pokud jste se rozhodli použít ověřovací kódy v aplikaci, můžete začít naleznete na domovské stránce. Mohou změnit každých 30 sekund, takže máte vždycky nový kód, v případě potřeby. Ale není nutné provádět s nimi až po přihlášení a výzva k zadání ověřovacího kódu.  
