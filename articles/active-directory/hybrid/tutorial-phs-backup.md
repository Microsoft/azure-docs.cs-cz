---
title: 'Kurz:  Nastavení PHS jako zálohu pro službu AD FS v Azure AD Connect | Dokumentace Microsoftu'
description: Ukazuje, jak zapnout synchronizace hodnot hash hesel na zálohu a pro službu AD FS.
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
ms.openlocfilehash: 3e5ad7badfa44a006fd7e71d3b0e42ee95ac698d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919012"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Kurz:  Nastavení PHS jako zálohu pro službu AD FS v Azure AD Connect

V následujícím kurzu vás provede procesem nastavení synchronizace hodnot hash hesel jako zálohování a převzetí služeb při selhání pro službu AD FS.  Tento dokument bude také ukazují, jak povolení synchronizace hodnot hash hesel jako primární ověřování, pokud má služba AD FS se nepovedlo nebo není dostupná.

>[!NOTE] 
>I když tyto kroky jsou obvykle provést během mimořádné události nebo situace výpadek, doporučujeme tyto kroky testu a ověření vašich procedur předtím, než dojde k výpadku.

>[!NOTE]
>V případě, že máte přístup k serveru služby Azure AD Connect nebo server nemá přístup k Internetu, můžete kontaktovat [Microsoft Support](https://support.microsoft.com/en-us/contactus/) jako pomoc s změny na straně služby Azure AD.

## <a name="prerequisites"></a>Požadavky
V tomto kurzu staví na [kurzu: Vytvoření federace pro jedno prostředí doménové struktury AD do cloudu](tutorial-federation.md) a je na-před pokusem o tomto kurzu.  Pokud jste tento kurz nedokončili, učiňte tak před pokusem kroky v tomto dokumentu.

>[!IMPORTANT]
>Před přepnutím do PHS byste měli vytvořit zálohu prostředí služby AD FS.  To lze provést pomocí [AD FS rychlé obnovení nástroj](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool).

## <a name="enable-phs-in-azure-ad-connect"></a>Povolit PHS ve službě Azure AD Connect
Prvním krokem, když teď máme prostředí Azure AD Connect, která používá federation, je pro zapnutí synchronizace hodnot hash hesel a povolení služby Azure AD Connect pro synchronizaci hodnoty hash.

Udělejte toto:

1.  Dvakrát klikněte na ikonu služby Azure AD Connect, který byl vytvořen v klientských počítačích
2.  Klikněte na **Konfigurovat**.
3.  Na stránce další úlohy, vyberte **přizpůsobit možnosti synchronizace** a klikněte na tlačítko **Další**.
4.  Zadejte uživatelské jméno a heslo pro globálního správce.  Tento účet byl vytvořen [tady](tutorial-federation.md#create-a-global-administrator-in-azure-ad) v předchozím kurzu.
5.  Na **připojení k adresářům** obrazovce, klikněte na tlačítko **Další**.
6.  Na **domény a organizační jednotky filtrování** obrazovce, klikněte na tlačítko **Další**.
7.  Na **volitelné funkce** obrazovky, zkontrolujte **synchronizaci hodnot hash hesel** a klikněte na tlačítko **Další**.
![Výběr](media/tutorial-phs-backup/backup1.png)</br>
8.  Na **připraveno ke konfiguraci** obrazovku kliknutím **konfigurovat**.
9.  Po dokončení konfigurace klikněte na tlačítko **ukončovací**.
10. A to je vše!  Dokončení.  Synchronizace hodnot hash hesel teď dojde a může sloužit jako zálohu, pokud služba AD FS přestane být k dispozici.

## <a name="switch-to-password-hash-synchronization"></a>Přepnout na synchronizaci hodnot hash hesel
Teď vám ukážeme, jak přepnout na synchronizaci hodnot hash hesel. Než začnete, vezměte v úvahu za jakých podmínek by vám ulehčili přechod. Nenastavujte přepínač dočasné z důvodů, jako je výpadek sítě, podverze služby AD FS problém nebo problém, který má vliv na některé uživatele. Pokud se rozhodnete ulehčili přechod, protože opravuje problém bude trvat příliš dlouho, postupujte takto:

> [!IMPORTANT]
> Mějte na paměti, že bude trvat nějakou dobu heslo hodnoty hash na synchronizovat se službou Azure AD.  To znamená, že může trvat až 3 hodiny pro synchronizace k dokončení a před zahájením ověřování pomocí hodnot hash hesel.

1. Dvakrát klikněte na ikonu služby Azure AD Connect, který byl vytvořen v klientských počítačích
2.  Klikněte na **Konfigurovat**.
3.  Vyberte **změnit přihlášení uživatele** a klikněte na tlačítko **Další**.
![Změna](media/tutorial-phs-backup/backup2.png)</br>
4.  Zadejte uživatelské jméno a heslo pro globálního správce.  Tento účet byl vytvořen [tady](tutorial-federation.md#create-a-global-administrator-in-azure-ad) v předchozím kurzu.
5.  Na **přihlášení uživatele** obrazovky, vyberte **synchronizaci hodnot Hash hesel** a zatržítko **nepřevádět uživatelské účty** pole.  
6.  Ponechte výchozí nastavení **povolit jednotné přihlašování** vybraný a klikněte na tlačítko **Další**.
7.  Na **povolit jednotné přihlašování** obrazovku kliknutím **Další**.
8.  Na **připraveno ke konfiguraci** obrazovce, klikněte na tlačítko **konfigurovat**.
9.  Po dokončení konfigurace klikněte na tlačítko **ukončovací**.
10. Uživatelé mohou nyní používat jejich hesla k přihlášení k Azure a Azure services.

## <a name="test-signing-in-with-one-of-our-users"></a>Test přihlášení pomocí jednoho z našich uživatelů

1. Přejděte na [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Přihlaste se pomocí uživatelského účtu, který byl vytvořen v našich nového tenanta.  Budete muset přihlásit pomocí následujícího formátu: (user@domain.onmicrosoft.com). Použijte stejné heslo, které uživatel použije k přihlášení místní.</br>
   ![Ověření](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>Přepněte zpátky na federace
Teď vám ukážeme, jak chcete přejít zpátky k federace.  Chcete-li to provést, postupujte takto:

1.  Dvakrát klikněte na ikonu služby Azure AD Connect, který byl vytvořen v klientských počítačích
2.  Klikněte na **Konfigurovat**.
3.  Vyberte **změnit přihlášení uživatele** a klikněte na tlačítko **Další**.
4.  Zadejte uživatelské jméno a heslo pro globálního správce.  Jedná se o účet, který byl vytvořen [tady](tutorial-federation.md#create-a-global-administrator-in-azure-ad) v předchozím kurzu.
5.  Na **přihlášení uživatele** obrazovky, vyberte **federace se službou AD FS** a klikněte na tlačítko **Další**.  
6. Na stránce přihlašovací údaje správce domény, zadejte contoso\Administrator uživatelské jméno a heslo a klikněte na tlačítko **Další.**
7. Na obrazovce pro farmu služby AD FS, klikněte na tlačítko **Další**.
8. Na **Azure AD domain** obrazovky, vyberte doménu z rozevíracího seznamu a klikněte na tlačítko **Další**.
9. Na **připraveno ke konfiguraci** obrazovce, klikněte na tlačítko **konfigurovat**.
10. Po dokončení konfigurace klikněte na tlačítko **Další**.
![Konfigurace](media/tutorial-phs-backup/backup4.png)</br>
11. Na **ověřit připojení federace** obrazovce, klikněte na tlačítko **ověřte**.  Budete muset nakonfigurovat záznamy DNS (přidat záznamy A a AAAA) pro tuto úspěšně dokončit.
![Ověření](media/tutorial-phs-backup/backup5.png)</br>
12. Klikněte na tlačítko **ukončovací**.

## <a name="reset-the-ad-fs-and-azure-trust"></a>Resetování služby AD FS a Azure vztah důvěryhodnosti
Nyní potřebujeme resetovat vztah důvěryhodnosti mezi AD FS a Azure.

1.  Dvakrát klikněte na ikonu služby Azure AD Connect, který byl vytvořen v klientských počítačích
2.  Klikněte na **Konfigurovat**.
3.  Vyberte **Správa federace** a klikněte na tlačítko **Další**.
4.  Vyberte **resetování služby Azure AD vztah důvěryhodnosti** a klikněte na tlačítko **Další**.
![Resetovat](media/tutorial-phs-backup/backup6.png)</br>
5.  Na **připojit ke službě Azure AD** obrazovce zadejte uživatelské jméno a heslo pro globálního správce.
6.  Na **připojit k AD FS** obrazovce zadejte contoso\Administrator uživatelské jméno a heslo a klikněte na tlačítko **Další.**
7.  Na **certifikáty** obrazovce, klikněte na tlačítko **Další**.

## <a name="test-signing-in-with-one-of-our-users"></a>Test přihlášení pomocí jednoho z našich uživatelů

1.  Přejděte na [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Přihlaste se pomocí uživatelského účtu, který byl vytvořen v našich nového tenanta.  Budete muset přihlásit pomocí následujícího formátu: (user@domain.onmicrosoft.com). Použijte stejné heslo, které uživatel použije k přihlášení místní.
![Ověření](media/tutorial-password-hash-sync/verify1.png)

Máte teď úspěšně nastavení hybridního prostředí identit, můžete použít k testování a seznamte se s co Azure může nabídnout.

## <a name="next-steps"></a>Další postup


- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Expresní nastavení](how-to-connect-install-express.md)
- [Synchronizace hodnoty hash hesel](how-to-connect-password-hash-synchronization.md)
