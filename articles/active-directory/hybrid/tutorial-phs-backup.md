---
title: 'Kurz: Nastavení PHS jako zálohy pro službu AD FS ve službě Azure AD Connect | Dokumenty společnosti Microsoft'
description: Ukazuje, jak zapnout synchronizaci hash hesel jako zálohu a pro službu AD FS.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "64919012"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Kurz: Nastavení PHS jako zálohy pro službu AD FS ve službě Azure AD Connect

Následující kurz vás provede nastavením synchronizace hash hesel jako zálohy a převzetí služeb při selhání pro službu AD FS.  Tento dokument také ukáže, jak povolit synchronizaci hash hesel jako primární metodu ověřování, pokud služby AD FS selhaly nebo se staly nedostupnými.

>[!NOTE] 
>Přestože tyto kroky jsou obvykle prováděny v nouzových nebo výpadek situace, je doporučeno otestovat tyto kroky a ověřit postupy před výpadku dojde.

>[!NOTE]
>V případě, že nemáte přístup k serveru Azure AD Connect nebo server nemá přístup k internetu, můžete kontaktovat [podporu Microsoftu](https://support.microsoft.com/en-us/contactus/) a pomoci se změnami na straně Azure AD.

## <a name="prerequisites"></a>Požadavky
Tento kurz vychází z [kurzu: Federate jeden prostředí doménové struktury služby AD do cloudu](tutorial-federation.md) a je pro každý-požadovaný před pokusem o tento kurz.  Pokud jste tento kurz nedokončili, proveďte to před pokusem o kroky v tomto dokumentu.

>[!IMPORTANT]
>Před přechodem na PHS byste měli vytvořit zálohu prostředí AD FS.  To lze provést pomocí [nástroje rychlého obnovení služby AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool).

## <a name="enable-phs-in-azure-ad-connect"></a>Povolení phs ve službě Azure AD Connect
Prvním krokem, teď, když máme prostředí Azure AD Connect, které používá federaci, je zapnout synchronizaci hash hesel a povolit Azure AD Connect synchronizovat hash.

Udělejte toto:

1.  Poklepejte na ikonu Azure AD Connect, která byla vytvořena na ploše
2.  Klikněte na **Konfigurovat**.
3.  Na stránce Další úkoly vyberte **Možnosti synchronizace a** klepněte na tlačítko **Další**.
4.  Zadejte uživatelské jméno a heslo globálního správce.  Tento účet byl vytvořen [zde](tutorial-federation.md#create-a-global-administrator-in-azure-ad) v předchozím kurzu.
5.  Na obrazovce **Připojit adresáře** klikněte na **další**.
6.  Na obrazovce **filtrování domény a ou položky** klepněte na tlačítko **Další**.
7.  Na obrazovce **Volitelné funkce** zaškrtněte **políčko Synchronizace hodnot hash hesla** a klepněte na tlačítko **Další**.
![Výběr](media/tutorial-phs-backup/backup1.png)</br>
8.  Na obrazovce **Připraveno ke konfiguraci** klepněte na **tlačítko Konfigurovat**.
9.  Po dokončení konfigurace klepněte na tlačítko **Ukončit**.
10. A to je vše!  Skončil jsi.  Synchronizace hodnot hash hesla nyní proběhne a může být použita jako záloha, pokud službu AD FS přestane být k dispozici.

## <a name="switch-to-password-hash-synchronization"></a>Přepnutí na synchronizaci hodnot hash hesel
Nyní vám ukážeme, jak přejít na synchronizaci hodnot hash hesel. Než začnete, zvažte, za jakých podmínek byste měli provést výměnu. Neprovávejte přepínač z dočasných důvodů, jako je výpadek sítě, menší problém služby AD FS nebo problém, který se týká podmnožiny uživatelů. Pokud se rozhodnete provést přepínač, protože oprava problému bude trvat příliš dlouho, postupujte takto:

> [!IMPORTANT]
> Uvědomte si, že bude nějakou dobu trvat, než se synchronizace hesel do služby Azure AD synchronizuje.  To znamená, že dokončení synchronizace může trvat až 3 hodiny a před zahájením ověřování pomocí hodnot hash hesel.

1. Poklepejte na ikonu Azure AD Connect, která byla vytvořena na ploše
2.  Klikněte na **Konfigurovat**.
3.  Vyberte **Změnit přihlášení uživatele** a klepněte na tlačítko **Další**.
![Změnit](media/tutorial-phs-backup/backup2.png)</br>
4.  Zadejte uživatelské jméno a heslo globálního správce.  Tento účet byl vytvořen [zde](tutorial-federation.md#create-a-global-administrator-in-azure-ad) v předchozím kurzu.
5.  Na přihlašovací obrazovce **uživatele** vyberte **synchronizace hodnot hash hesla** a zadejte zaškrtnutí do pole **Nepřevádět uživatelské účty.**  
6.  Ponechte výchozí **možnost Povolit jednotné přihlašování** vybranou a klepněte na tlačítko **Další**.
7.  Na obrazovce **Povolit jednotné přihlašování** klikněte na **další**.
8.  Na obrazovce **Připraveno ke konfiguraci** klepněte na tlačítko **Konfigurovat**.
9.  Po dokončení konfigurace klepněte na tlačítko **Ukončit**.
10. Uživatelé teď můžou používat svá hesla k přihlášení ke službám Azure a Azure.

## <a name="test-signing-in-with-one-of-our-users"></a>Otestujte přihlášení s jedním z našich uživatelů

1. Přejít na[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Přihlaste se pomocí uživatelského účtu, který byl vytvořen v našem novém tenantovi.  Budete se muset přihlásit v následujícímuser@domain.onmicrosoft.comformátu: ( ). Použijte stejné heslo, které uživatel používá k místnímu přihlášení.</br>
   ![Ověřit](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>Přepnout zpět do federace
Teď vám ukážeme, jak přejít zpět na Federaci.  Chcete-li to provést, postupujte takto:

1.  Poklepejte na ikonu Azure AD Connect, která byla vytvořena na ploše
2.  Klikněte na **Konfigurovat**.
3.  Vyberte **Změnit přihlášení uživatele** a klepněte na tlačítko **Další**.
4.  Zadejte uživatelské jméno a heslo globálního správce.  Toto je účet, který byl vytvořen [zde](tutorial-federation.md#create-a-global-administrator-in-azure-ad) v předchozím kurzu.
5.  Na přihlašovací obrazovce **uživatele** vyberte **Možnost Federace se službě AD FS** a klepněte na tlačítko **Další**.  
6. Na stránce pověření správce domény zadejte uživatelské jméno a heslo contoso\Administrator a klepněte na tlačítko **Další.**
7. Na obrazovce farmy ad FS klepněte na tlačítko **Další**.
8. Na obrazovce **domény Azure AD** vyberte doménu z rozevíracího souboru a klikněte na **Další**.
9. Na obrazovce **Připraveno ke konfiguraci** klepněte na tlačítko **Konfigurovat**.
10. Po dokončení konfigurace klepněte na tlačítko **Další**.
![Konfigurace](media/tutorial-phs-backup/backup4.png)</br>
11. Na obrazovce **Ověřit připojení federace** klepněte na tlačítko **Ověřit**.  Možná budete muset nakonfigurovat záznamy DNS (přidat záznamy A a AAAA), aby to bylo úspěšně dokončeno.
![Ověřit](media/tutorial-phs-backup/backup5.png)</br>
12. Klepněte na tlačítko **Ukončit**.

## <a name="reset-the-ad-fs-and-azure-trust"></a>Obnovení vztahu důvěryhodnosti služby AD FS a Azure
Teď musíme obnovit vztah důvěryhodnosti mezi službou AD FS a Azure.

1.  Poklepejte na ikonu Azure AD Connect, která byla vytvořena na ploše
2.  Klikněte na **Konfigurovat**.
3.  Vyberte **Spravovat federaci** a klepněte na tlačítko **Další**.
4.  Vyberte **Obnovit vztah důvěryhodnosti Azure AD** a klepněte na tlačítko **Další**.
![Reset](media/tutorial-phs-backup/backup6.png)</br>
5.  Na obrazovce **Připojit k Azure AD** zadejte uživatelské jméno a heslo pro globálního správce.
6.  Na obrazovce **Připojit ke službě AD FS** zadejte uživatelské jméno a heslo contoso\Administrator a klepněte na tlačítko **Další.**
7.  Na obrazovce **Certifikáty** klepněte na tlačítko **Další**.

## <a name="test-signing-in-with-one-of-our-users"></a>Otestujte přihlášení s jedním z našich uživatelů

1.  Přejít na[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Přihlaste se pomocí uživatelského účtu, který byl vytvořen v našem novém tenantovi.  Budete se muset přihlásit v následujícímuser@domain.onmicrosoft.comformátu: ( ). Použijte stejné heslo, které uživatel používá k místnímu přihlášení.
![Ověřit](media/tutorial-password-hash-sync/verify1.png)

Nyní jste úspěšně nastavili prostředí hybridní identity, které můžete použít k testování a seznámení s tím, co Azure nabízí.

## <a name="next-steps"></a>Další kroky


- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Expresní nastavení](how-to-connect-install-express.md)
- [Synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md)
