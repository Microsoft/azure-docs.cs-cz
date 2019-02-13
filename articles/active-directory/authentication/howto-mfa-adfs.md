---
title: Zabezpečení cloudových prostředků s Azure MFA a AD FS | Dokumentace Microsoftu
description: Toto je stránka vícefaktorového ověřování Azure, která popisuje, jak začít s vícefaktorovým ověřováním Azure a službami AD FS v cloudu.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89559c1c42255bf1050cbb96f38e6d7394c21103
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191399"
---
# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>Zabezpečení cloudových prostředků s Azure Multi-Factor Authentication a AD FS

Pokud je vaše organizace federovaná pomocí služby Azure Active Directory, k zabezpečení prostředků, které využívá Azure AD, použijte ověřování Azure Multi-Factor Authentication nebo Active Directory Federation Services (AD FS). Následující postupy použijte k zabezpečení prostředků Azure Active Directory s ověřováním Azure Multi-Factor Authentication nebo Active Directory Federation Services.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Zabezpečení prostředků Azure AD pomocí služby AD FS

K zabezpečení cloudových prostředků nastavte pravidlo deklarace identity tak, aby služba Active Directory Federation Services vyslala deklaraci identity multipleauthn, když uživatel úspěšně provede dvoustupňové ověření. Tato deklarace identity se předá Azure AD. Postupujte takto:

1. Otevřete správu služby AD FS.
2. Na levé straně vyberte **Vztahy důvěryhodnosti předávající strany**.
3. Klikněte pravým tlačítkem na **Platforma identit Microsoft Office 365** a vyberte **Upravit pravidla deklarací identity**.

   ![Cloud](./media/howto-mfa-adfs/trustedip1.png)

4. V pravidlech transformace vystavení klikněte na **Přidat pravidlo**.

   ![Cloud](./media/howto-mfa-adfs/trustedip2.png)

5. V Průvodci přidáním pravidla – deklarace identity transformace vyberte v rozevíracím seznamu **Předávat nebo filtrovat příchozí deklarace** a klikněte na **Další**.

   ![Cloud](./media/howto-mfa-adfs/trustedip3.png)

6. Pojmenujte pravidlo. 
7. Jako typ příchozí deklarace identity vyberte **Odkazy na metody ověřování**.
8. Vyberte **Předávat všechny hodnoty deklarací identity**.
    ![Průvodce přidáním pravidla deklarace identity transformace](./media/howto-mfa-adfs/configurewizard.png)
9. Klikněte na **Dokončit**. Uzavřete konzolu pro správu služby AD FS.

## <a name="trusted-ips-for-federated-users"></a>Důvěryhodné IP adresy pro federované uživatele

Důvěryhodné IP adresy umožňují správcům obejít dvoustupňové ověření pro konkrétní IP adres nebo pro federované uživatele, kteří mají požadavky pocházejících z vlastního intranetu. V následujících tématech se věnujeme tomu, jak nakonfigurovat důvěryhodné IP adresy ověřování Azure Multi-Factor Authentication s federovanými uživateli a obejít dvoustupňové ověření, když požadavek pochází z intranetu federovaného uživatele. Toho dosáhnete pomocí konfigurace služby AD FS pro používání průchodu nebo filtru příchozí šablony deklarace identity pomocí typu deklarace identity uvnitř podnikové sítě.

Tento příklad používá Office 365 pro naše trusty přijímající strany.

### <a name="configure-the-ad-fs-claims-rules"></a>Konfigurace pravidel deklarací identity služby AD FS

První věc, kterou je potřeba udělat, je konfigurace deklarací identity služby AD FS. Vytvořte dvě pravidla deklarace identity: jedno pro typ deklarace identity uvnitř podnikové sítě a druhé pro zachování přihlášení uživatelů.

1. Otevřete správu služby AD FS.
2. Na levé straně vyberte **Vztahy důvěryhodnosti předávající strany**.
3. Klikněte pravým tlačítkem na **Platforma identit Microsoft Office 365** a vyberte **Upravit pravidla deklarací identity…**
   ![Cloud](./media/howto-mfa-adfs/trustedip1.png)
4. V pravidlech transformace vystavení klikněte na **Přidat pravidlo.**
   ![Cloud](./media/howto-mfa-adfs/trustedip2.png)
5. V Průvodci přidáním pravidla – deklarace identity transformace vyberte v rozevíracím seznamu **Předávat nebo filtrovat příchozí deklarace** a klikněte na **Další**.
   ![Cloud](./media/howto-mfa-adfs/trustedip3.png)
6. Do pole vedle názvu pravidla deklarace identity zadejte název pravidla. Příklad: InsideCorpNet.
7. V rozevíracím seznamu vedle Typ příchozí deklarace vyberte **Uvnitř podnikové sítě**.
   ![Cloud](./media/howto-mfa-adfs/trustedip4.png)
8. Klikněte na **Dokončit**.
9. V pravidlech transformace vystavení klikněte na **Přidat pravidlo**.
10. V Průvodci přidáním pravidla – deklarace identity transformace vyberte **Odesílat deklarace pomocí vlastního pravidla** v rozevíracím seznamu a klikněte na **Další**.
11. Do pole pod Název pravidla deklarace napište *Nechat uživatele přihlášené*.
12. Do pole Vlastní pravidlo zadejte:

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Cloud](./media/howto-mfa-adfs/trustedip5.png)
13. Klikněte na **Dokončit**.
14. Klikněte na tlačítko **Použít**.
15. Klikněte na tlačítko **OK**.
16. Zavřete správu služby AD FS.

### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>Konfigurovat důvěryhodné IP adresy ověřování Azure Multi-Factor Authentication s federovanými uživateli

Teď, když máme vytvořené deklarace identity, můžeme nakonfigurovat důvěryhodné IP adresy.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **Azure Active Directory** > **Podmíněný přístup** > **Pojmenovaná umístění**.
3. V okně **Podmíněný přístup – pojmenovaná umístění** vyberte **Konfigurovat důvěryhodné IP adresy MFA**.

   ![Azure AD – Podmíněný přístup – Pojmenovaná umístění – Konfigurovat důvěryhodné IP adresy MFA](./media/howto-mfa-adfs/trustedip6.png)

4. Na stránce Nastavení služby v části **Důvěryhodné IP adresy** vyberte **Pro žádosti od federovaných uživatelů v mém intranetu přeskočit vícefaktorové ověřování**.  
5. Klikněte na **Uložit**.

A to je vše! Od tohoto okamžiku musí federovaní uživatelé služeb Office 365 využívat MFA pouze pokud deklarace identity pochází z oblasti mimo podnikového intranetu.
