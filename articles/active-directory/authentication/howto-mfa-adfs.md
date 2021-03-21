---
title: Zabezpečení prostředků pomocí Azure AD MFA a ADFS-Azure Active Directory
description: Toto je stránka Multi-Factor Authentication služby Azure AD, která popisuje, jak začít s Azure AD MFA a AD FS v cloudu.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b42f920726d4b3046ab0c292e1090f5217e8b1f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96743237"
---
# <a name="securing-cloud-resources-with-azure-ad-multi-factor-authentication-and-ad-fs"></a>Zabezpečení cloudových prostředků pomocí Multi-Factor Authentication Azure AD a AD FS

Pokud je vaše organizace federované pomocí Azure Active Directory, použijte Azure AD Multi-Factor Authentication nebo Active Directory Federation Services (AD FS) (AD FS) k zabezpečení prostředků, ke kterým přistupovala služba Azure AD. Následující postupy použijte k zabezpečení Azure Active Directorych prostředků pomocí Azure AD Multi-Factor Authentication nebo Active Directory Federation Services (AD FS).

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Zabezpečení prostředků Azure AD pomocí služby AD FS

K zabezpečení cloudových prostředků nastavte pravidlo deklarace identity tak, aby služba Active Directory Federation Services vyslala deklaraci identity multipleauthn, když uživatel úspěšně provede dvoustupňové ověření. Tato deklarace identity se předá Azure AD. Postupujte takto:

1. Otevřete správu služby AD FS.
2. Na levé **straně vyberte vztahy důvěryhodnosti předávající strany**.
3. Klikněte pravým tlačítkem na **Platforma identit Microsoft Office 365** a vyberte **Upravit pravidla deklarací identity**.

   ![Konzola ADFS – vztahy důvěryhodnosti předávající strany](./media/howto-mfa-adfs/trustedip1.png)

4. V pravidlech transformace vystavení klikněte na **Přidat pravidlo**.

   ![Úprava pravidel transformace vystavení](./media/howto-mfa-adfs/trustedip2.png)

5. V Průvodci přidáním pravidla – deklarace identity transformace vyberte v rozevíracím seznamu **Předávat nebo filtrovat příchozí deklarace** a klikněte na **Další**.

   ![Snímek obrazovky se zobrazí Průvodce přidáním pravidla deklarace identity, kde vyberete šablonu pravidla deklarace identity.](./media/howto-mfa-adfs/trustedip3.png)

6. Pojmenujte pravidlo. 
7. Jako typ příchozí deklarace identity vyberte **Odkazy na metody ověřování**.
8. Vyberte **Předávat všechny hodnoty deklarací identity**.
    ![Snímek obrazovky s průvodcem přidat transformační pravidlo deklarace identity, kde můžete vybrat možnost předat všechny hodnoty deklarace identity.](./media/howto-mfa-adfs/configurewizard.png)
9. Klikněte na **Finish** (Dokončit). Uzavřete konzolu pro správu služby AD FS.

## <a name="trusted-ips-for-federated-users"></a>Důvěryhodné IP adresy pro federované uživatele

Důvěryhodné IP adresy umožňují správcům obejít dvoustupňové ověření pro konkrétní IP adres nebo pro federované uživatele, kteří mají požadavky pocházejících z vlastního intranetu. Následující části popisují, jak nakonfigurovat důvěryhodné IP adresy Azure AD Multi-Factor Authentication důvěryhodných uživatelů a obejít dvoustupňové ověřování, když požadavek pochází z intranetu federovaného uživatele. Toho dosáhnete pomocí konfigurace služby AD FS pro používání průchodu nebo filtru příchozí šablony deklarace identity pomocí typu deklarace identity uvnitř podnikové sítě.

Tento příklad používá Microsoft 365 pro naše vztahy důvěryhodnosti předávající strany.

### <a name="configure-the-ad-fs-claims-rules"></a>Konfigurace pravidel deklarací identity služby AD FS

První věc, kterou je potřeba udělat, je konfigurace deklarací identity služby AD FS. Vytvořte dvě pravidla deklarace identity: jedno pro typ deklarace identity uvnitř podnikové sítě a druhé pro zachování přihlášení uživatelů.

1. Otevřete správu služby AD FS.
2. Na levé **straně vyberte vztahy důvěryhodnosti předávající strany**.
3. Klikněte pravým tlačítkem na **systém Microsoft Office 365 Identity Platform** a vyberte **upravit pravidla deklarací identity...** 
    ![ Konzola ADFS – úprava pravidel deklarací identity](./media/howto-mfa-adfs/trustedip1.png)
4. V pravidla transformace vystavení klikněte na **Přidat pravidlo.** 
    ![ Přidání pravidla deklarace identity](./media/howto-mfa-adfs/trustedip2.png)
5. V Průvodci přidáním pravidla – deklarace identity transformace vyberte v rozevíracím seznamu **Předávat nebo filtrovat příchozí deklarace** a klikněte na **Další**.
   ![Snímek obrazovky s průvodcem pro přidání transformačního pravidla deklarace, kde můžete vybrat možnost předávat nebo filtrovat příchozí deklarace identity.](./media/howto-mfa-adfs/trustedip3.png)
6. Do pole vedle názvu pravidla deklarace identity zadejte název pravidla. Příklad: InsideCorpNet.
7. V rozevíracím seznamu vedle Typ příchozí deklarace vyberte **Uvnitř podnikové sítě**.
   ![Přidávání v rámci deklarace identity podnikové sítě](./media/howto-mfa-adfs/trustedip4.png)
8. Klikněte na **Finish** (Dokončit).
9. V pravidlech transformace vystavení klikněte na **Přidat pravidlo**.
10. V Průvodci přidáním pravidla – deklarace identity transformace vyberte **Odesílat deklarace pomocí vlastního pravidla** v rozevíracím seznamu a klikněte na **Další**.
11. Do pole pod Název pravidla deklarace napište *Nechat uživatele přihlášené*.
12. Do pole Vlastní pravidlo zadejte:

```ad-fs-claim-rule
        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Create custom claim to keep users signed in](./media/howto-mfa-adfs/trustedip5.png)
```

13. Klikněte na **Finish** (Dokončit).
14. Klikněte na **Použít**.
15. Klikněte na **OK**.
16. Zavřete správu služby AD FS.

### <a name="configure-azure-ad-multi-factor-authentication-trusted-ips-with-federated-users"></a>Konfigurace důvěryhodných IP adres Multi-Factor Authentication Azure AD s federovanými uživateli

Teď, když máme vytvořené deklarace identity, můžeme nakonfigurovat důvěryhodné IP adresy.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Vyberte **Azure Active Directory**  >  **zabezpečení**  >  **podmíněný přístup**  >  **pojmenované umístění**.
3. V okně **podmíněný přístup – pojmenovaná umístění** vyberte **Konfigurovat důvěryhodné IP adresy MFA** .

   ![Podmíněný přístup služby Azure AD konfigurace důvěryhodných IP adres MFA](./media/howto-mfa-adfs/trustedip6.png)

4. Na stránce Nastavení služby v části **Důvěryhodné IP adresy** vyberte **Pro žádosti od federovaných uživatelů v mém intranetu přeskočit vícefaktorové ověřování**.  
5. Klikněte na **Uložit**.

A to je vše! V tuto chvíli by měli uživatelé federované Microsoft 365 používat MFA jenom v případě, že deklarace identity pochází mimo podnikový intranet.
