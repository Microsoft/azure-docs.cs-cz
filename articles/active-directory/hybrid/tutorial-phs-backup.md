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
ms.date: 01/30/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 5219241de9b18008032a3eb78c6aab25a2713370
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55660404"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Kurz:  Nastavení PHS jako zálohu pro službu AD FS v Azure AD Connect

V následujícím kurzu vás provede procesem nastavení synchronizace hodnot hash hesel jako zálohování a převzetí služeb při selhání pro službu AD FS.  Tento dokument bude také ukazují, jak povolení synchronizace hodnot hash hesel jako primární ověřování, pokud má služba AD FS se nepovedlo nebo není dostupná.

## <a name="prerequisites"></a>Požadavky
V tomto kurzu staví na [kurzu: Vytvoření federace pro jedno prostředí doménové struktury AD do cloudu](tutorial-federation.md) a je na-před pokusem o tomto kurzu.  Pokud jste tento kurz nedokončili, učiňte tak před pokusem kroky v tomto dokumentu.

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

1.  Přejděte na [http://myapps.microsoft.com](http://myapps.microsoft.com)
2. Přihlaste se pomocí uživatelského účtu, který byl vytvořen v našich nového tenanta.  Budete muset přihlásit pomocí následujícího formátu: (user@domain.onmicrosoft.com). Použijte stejné heslo, které uživatel použije k přihlášení místní.</br>
![Ověření](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="next-steps"></a>Další kroky


- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Expresní nastavení](how-to-connect-install-express.md)
- [Synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md)|
