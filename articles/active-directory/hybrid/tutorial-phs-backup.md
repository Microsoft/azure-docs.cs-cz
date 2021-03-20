---
title: 'Kurz: nastavení KOSMETICE jako zálohy pro AD FS v Azure AD Connect | Microsoft Docs'
description: Ukazuje, jak zapnout synchronizaci hodnot hash hesel jako zálohu a pro AD FS.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d9f59906c566d80344891c0796a85b0a4972e68
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91313089"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Kurz: nastavení KOSMETICE jako zálohy pro AD FS v Azure AD Connect

Následující kurz vás provede nastavením synchronizace hodnot hash hesel jako zálohy a převzetí služeb při selhání pro AD FS.  Tento dokument také demonstruje, jak povolit synchronizaci hodnot hash hesel jako primární metodu ověřování, pokud se AD FS nezdařila nebo není k dispozici.

>[!NOTE] 
>I když se tyto kroky obvykle provádějí při mimořádných událostech nebo při výpadkech, doporučujeme, abyste tyto kroky otestovali a ověřili své procedury před výpadkem.

>[!NOTE]
>V případě, že nemáte přístup k serveru Azure AD Connect nebo server nemá přístup k Internetu, můžete kontaktovat [Podpora Microsoftu](https://support.microsoft.com/en-us/contactus/) a pomoct se změnami na straně Azure AD.

## <a name="prerequisites"></a>Předpoklady
Tento kurz sestaví na tomto [kurzu: federovat jedno prostředí doménové struktury AD do cloudu](tutorial-federation.md) a před pokusem o provedení tohoto kurzu je v jednotlivých požadavcích.  Pokud jste tento kurz nedokončili, udělejte to ještě před provedením kroků v tomto dokumentu.

>[!IMPORTANT]
>Před přechodem na KOSMETICE byste měli vytvořit zálohu AD FSho prostředí.  To lze provést pomocí [nástroje AD FS Rapid Restore Tool](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool).

## <a name="enable-phs-in-azure-ad-connect"></a>Povolit KOSMETICE v Azure AD Connect
První krok, teď když máme Azure AD Connect prostředí, které používá federaci, je zapnout synchronizaci hodnot hash hesel a povolit Azure AD Connect synchronizaci hodnot hash.

Postupujte následovně:

1.  Poklikejte na ikonu Azure AD Connect, která se vytvořila na ploše.
2.  Klikněte na **Konfigurovat**.
3.  Na stránce další úlohy vyberte možnost **přizpůsobit možnosti synchronizace** a klikněte na tlačítko **Další**.
4.  Zadejte uživatelské jméno a heslo pro globálního správce.  Tento účet [se vytvořil v](tutorial-federation.md#create-a-global-administrator-in-azure-ad) předchozím kurzu.
5.  Na obrazovce **připojit adresáře** klikněte na **Další**.
6.  Na obrazovce **filtrování domény a organizační jednotky** klikněte na **Další**.
7.  Na obrazovce **volitelné funkce** ověřte **synchronizaci hodnot hash hesel** a klikněte na **Další**.
![Výběr](media/tutorial-phs-backup/backup1.png)</br>
8.  Na obrazovce **připraveno ke konfiguraci** klikněte na **Konfigurovat**.
9.  Po dokončení konfigurace klikněte na **ukončit**.
10. A to je vše!  Jste hotovi.  Synchronizace hodnot hash hesel se teď projeví a dá se použít jako záloha, pokud AD FS nebude k dispozici.

## <a name="switch-to-password-hash-synchronization"></a>Přepnout na synchronizaci hodnot hash hesel
Teď vám ukážeme, jak přepnout na synchronizaci hodnot hash hesel. Než začnete, zvažte, za jakých podmínek byste měli přepínač udělat. Neprovádějte přepínač pro dočasné důvody, jako je výpadek sítě, menší problém AD FS, nebo problém, který má vliv na podmnožinu vašich uživatelů. Pokud se rozhodnete tento přepínač provést, protože oprava problému bude trvat moc dlouho, udělejte toto:

> [!IMPORTANT]
> Počítejte s tím, že pro synchronizaci hodnot hash hesel do služby Azure AD bude trvat nějakou dobu.  To znamená, že může trvat až 3 hodiny, než se synchronizace dokončí, a předtím, než budete moct začít s ověřováním pomocí hodnot hash hesel.

1. Poklikejte na ikonu Azure AD Connect, která se vytvořila na ploše.
2.  Klikněte na **Konfigurovat**.
3.  Vyberte možnost **změnit přihlášení uživatele** a klikněte na tlačítko **Další**.
![Mění](media/tutorial-phs-backup/backup2.png)</br>
4.  Zadejte uživatelské jméno a heslo pro globálního správce.  Tento účet [se vytvořil v](tutorial-federation.md#create-a-global-administrator-in-azure-ad) předchozím kurzu.
5.  Na obrazovce **přihlášení uživatele** vyberte **synchronizace hodnot hash hesel** a zaškrtněte políčko **nepřevádět uživatelské účty** .  
6.  Ponechte vybrané políčko **Povolit jednotné přihlašování** a klikněte na **Další**.
7.  Na obrazovce **Povolit jednotné přihlašování** klikněte na **Další**.
8.  Na obrazovce **připraveno ke konfiguraci** klikněte na **Konfigurovat**.
9.  Po dokončení konfigurace klikněte na **ukončit**.
10. Uživatelé teď můžou používat svá hesla pro přihlášení k Azure a službám Azure.

## <a name="test-signing-in-with-one-of-our-users"></a>Vyzkoušejte si přihlašování jedním z našich uživatelů

1. Přejděte na [https://myapps.microsoft.com](https://myapps.microsoft.com).
2. Přihlaste se pomocí uživatelského účtu vytvořeného v našem novém tenantovi.  Budete se muset přihlásit pomocí následujícího formátu: ( user@domain.onmicrosoft.com ). Použijte stejné heslo, které uživatel používá k místnímu přihlášení.</br>
   ![Snímek obrazovky, který zobrazuje úspěšnou zprávu při testování přihlášení. ](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>Přepnout zpátky na federaci
Teď vám ukážeme, jak přejít zpátky k Federaci.  Uděláte to takto:

1.  Poklikejte na ikonu Azure AD Connect, která se vytvořila na ploše.
2.  Klikněte na **Konfigurovat**.
3.  Vyberte možnost **změnit přihlášení uživatele** a klikněte na tlačítko **Další**.
4.  Zadejte uživatelské jméno a heslo pro globálního správce.  Jedná se o účet, který jste [vytvořili v](tutorial-federation.md#create-a-global-administrator-in-azure-ad) předchozím kurzu.
5.  Na obrazovce **přihlášení uživatele** vyberte možnost **federace s AD FS** a klikněte na tlačítko **Další**.  
6. Na stránce pověření správce domény zadejte uživatelské jméno a heslo contoso\Administrator a klikněte na **Další.**
7. Na obrazovce AD FS farmy klikněte na **Další**.
8. Na obrazovce **doména Azure AD** vyberte v rozevíracím seznamu doménu a klikněte na **Další**.
9. Na obrazovce **připraveno ke konfiguraci** klikněte na **Konfigurovat**.
10. Po dokončení konfigurace klikněte na **Další**.
![Konfigurace](media/tutorial-phs-backup/backup4.png)</br>
11. Na obrazovce **ověřit připojení federace** klikněte na **ověřit**.  Možná budete muset nakonfigurovat záznamy DNS (přidat záznamy a a AAAA), aby se tato konfigurace úspěšně dokončila.
![Snímek obrazovky zobrazující obrazovku ověřit připojení federace a tlačítko ověřit](media/tutorial-phs-backup/backup5.png)</br>
12. Klikněte na tlačítko **konec**.

## <a name="reset-the-ad-fs-and-azure-trust"></a>Resetování AD FS a vztahu důvěryhodnosti Azure
Teď je potřeba resetovat vztah důvěryhodnosti mezi AD FS a Azure.

1.  Poklikejte na ikonu Azure AD Connect, která se vytvořila na ploše.
2.  Klikněte na **Konfigurovat**.
3.  Vyberte **Spravovat federaci** a klikněte na **Další**.
4.  Vyberte **resetovat vztah důvěryhodnosti služby Azure AD** a klikněte na **Další**.
![Reset](media/tutorial-phs-backup/backup6.png)</br>
5.  Na obrazovce **připojení ke službě Azure AD** zadejte uživatelské jméno a heslo pro globálního správce.
6.  Na obrazovce **připojit k AD FS** zadejte uživatelské jméno a heslo CONTOSO\Administrator a klikněte na **Další.**
7.  Na obrazovce **certifikáty** klikněte na **Další**.

## <a name="test-signing-in-with-a-user"></a>Testování přihlášení pomocí uživatele

1.  Přejděte na [https://myapps.microsoft.com](https://myapps.microsoft.com).
2. Přihlaste se pomocí uživatelského účtu vytvořeného v našem novém tenantovi.  Budete se muset přihlásit pomocí následujícího formátu: ( user@domain.onmicrosoft.com ). Použijte stejné heslo, které uživatel používá k místnímu přihlášení.
![Ověření](media/tutorial-password-hash-sync/verify1.png)

Nyní jste úspěšně nastavili hybridní prostředí identity, které můžete použít k otestování a seznámení s tím, co Azure nabízí.

## <a name="next-steps"></a>Další kroky


- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Expresní nastavení](how-to-connect-install-express.md)
- [Synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md)