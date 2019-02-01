---
title: 'Synchronizace Azure AD Connect:  Změna účtu služby Azure AD Connect Sync | Dokumentace Microsoftu'
description: Tento dokument téma popisuje šifrovací klíč a o tom, která může opustit po změně hesla.
services: active-directory
keywords: Účet služby Azure AD sync, heslo
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 4c12675f28664da07aeebf8302b196d372c1f1d2
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55495407"
---
# <a name="changing-the-azure-ad-connect-sync-service-account-password"></a>Změna hesla účtu služby synchronizace Azure AD Connect
Pokud změníte heslo účtu služby Azure AD Connect sync, synchronizační služba nebude možné start správně opuštěných šifrovacího klíče a znovu inicializovat heslo účtu služby Azure AD Connect sync. 

Azure AD Connect, jako součást synchronizační služby používá šifrovací klíč k uložení hesla účtů služby AD DS a Azure AD.  Tyto účty se zašifrují předtím, než jsou uloženy v databázi. 

Šifrovací klíč, který je zabezpečený pomocí [Windows Data Protection (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx). Rozhraní DPAPI chrání klíč šifrování pomocí **hesla účtu synchronizační služby Azure AD Connect**. 

Pokud je třeba změnit heslo účtu služby můžete použít postupy uvedené v [nastavuje Azure AD Connect Sync šifrovací klíč](#abandoning-the-azure-ad-connect-sync-encryption-key) jak toho dosáhnout.  Tyto postupy by měla sloužit také pokud potřebujete spustit metodu Abandon šifrovacího klíče z jakéhokoli důvodu.

## <a name="issues-that-arise-from-changing-the-password"></a>Problémy, které jsou vyvolány změnu hesla
Existují dvě věci, které je potřeba udělat, když změníte heslo účtu služby.

Je třeba nejprve, chcete-li změnit heslo v rámci správce řízení služeb Windows.  Dokud tento problém nebude vyřešen uvidíte následující chyby:


- Pokud se pokusíte spustit synchronizační službu ve správci řízení služeb Windows, se zobrazí chyba "**Windows nelze spustit službu Microsoft Azure AD Sync na místním počítači**". **Chyby 1069: Službu se nepodařilo spustit z důvodu selhání přihlášení.** "
- V prohlížeči událostí pro Windows, protokolu událostí systému obsahuje chybu s **7038 ID události** a zpráva "**ADSync service nebylo možné se přihlásit jako se se současně nakonfigurovaným heslem kvůli následující chybě: Uživatelské jméno nebo heslo není správné.** "

Za druhé za určitých podmínek, pokud se heslo aktualizuje, synchronizační služba už načíst šifrovací klíč pomocí rozhraní DPAPI. Bez šifrovací klíč synchronizační služba nemůže dešifrovat hesla, třeba synchronizovat z místní AD a Azure AD.
Chyby se zobrazí jako například:

- V části správce řízení služeb Windows Pokud se pokusíte spustit synchronizační službu a nemůže získat šifrovací klíč, selže s chybou "<strong>Windows nelze spustit Microsoft Azure AD Sync na místním počítači. Další informace najdete v protokolu událostí systému. Pokud je to služba jiného subjektu než Microsoft, obraťte se na dodavatele služby a získáte kódu chyby specifické pro služby-21451857952</strong>. "
- V prohlížeči událostí pro Windows, protokolu událostí aplikace obsahuje chybu s **6028 ID události** chybové zprávy a *"**šifrovacího klíče serveru nelze přistupovat.**"*

Aby se tyto chyby nezobrazí, postupujte podle pokynů v [nastavuje Azure AD Connect Sync šifrovací klíč](#abandoning-the-azure-ad-connect-sync-encryption-key) při změně hesla.
 
## <a name="abandoning-the-azure-ad-connect-sync-encryption-key"></a>Nastavuje Azure AD Connect Sync šifrovacího klíče
>[!IMPORTANT]
>Následující postupy platí jenom pro Azure AD Connect sestavení 1.1.443.0 nebo starší.

Použijte následující postupy k opuštění šifrovací klíč.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Co dělat, když potřebujete spustit metodu Abandon šifrovacího klíče

Pokud potřebujete spustit metodu Abandon šifrovací klíč, použijte následující postupy, jak toho dosáhnout.

1. [Zastavit službu synchronizace](#stop-the-synchronization-service)

1. [Opustit stávající šifrovací klíč](#abandon-the-existing-encryption-key)

2. [Zadejte heslo účtu služby AD DS](#provide-the-password-of-the-ad-ds-account)

3. [Proveďte novou inicializaci hesla účtu synchronizační služby Azure AD](#reinitialize-the-password-of-the-azure-ad-sync-account)

4. [Spustit synchronizační službu](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>Zastavit službu synchronizace
Nejprve můžete zastavit službu ve správci řízení služeb Windows.  Ujistěte se, že služba není spuštěná při pokusu o zastavení.  Pokud se jedná, počkejte, až se dokončí a potom ji zastavit.


1. Přejděte do Windows správce řízení služeb (spuštění → služeb).
2. Vyberte **Microsoft Azure AD Sync** a klikněte na Zastavit.

#### <a name="abandon-the-existing-encryption-key"></a>Opustit stávající šifrovací klíč
Opustit stávající šifrovací klíč tak, že nový šifrovací klíč je vytvořit:

1. Přihlaste se k Azure AD Connect serveru jako správce.

2. Spusťte novou relaci Powershellu.

3. Přejděte do složky: `$env:Program Files\Microsoft Azure AD Sync\bin\`

4. Spusťte příkaz: `./miiskmu.exe /a`

![Azure AD Connect Sync šifrovací klíče nástroje](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-account"></a>Zadejte heslo účtu služby AD DS
Stávající hesla uložená v databázi je už nebude možné dešifrovat, je potřeba poskytnout službě synchronizace heslo účtu služby AD DS. Synchronizační služba šifruje hesla pomocí nového šifrovacího klíče:

1. Spusťte Synchronization Service Manager (Služba synchronizace → START).
</br>![Správce synchronizace služby](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Přejděte **konektory** kartu.
3. Vyberte **AD Connector** , který odpovídá místní AD. Pokud máte více než jeden konektor služby AD, opakujte následující kroky pro každý z nich.
4. V části **akce**vyberte **vlastnosti**.
5. V místním dialogovém okně vyberte **připojit k doménové struktuře služby Active Directory**:
6. Zadejte heslo účtu služby AD DS v **heslo** textového pole. Pokud si nejste jisti své heslo, musí ho nastavit na hodnotu známé před provedením tohoto kroku.
7. Klikněte na tlačítko **OK** nové heslo uložte a zavřete dialogové okno místní.
![Azure AD Connect Sync šifrovací klíče nástroje](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-azure-ad-sync-account"></a>Proveďte novou inicializaci hesla účtu synchronizační služby Azure AD
Službě synchronizace nelze přímo zadat heslo účtu služby Azure AD. Místo toho budete muset použít rutiny **přidat ADSyncAADServiceAccount** opětovnou inicializaci účtu služby Azure AD. Rutina resetuje heslo k účtu a zpřístupňuje je službě synchronizace:

1. Spusťte novou relaci prostředí PowerShell na serveru služby Azure AD Connect.
2. Spusťte rutinu `Add-ADSyncAADServiceAccount`.
3. V místním dialogovém okně zadejte přihlašovací údaje globálního správce Azure AD pro vašeho tenanta Azure AD.
![Azure AD Connect Sync šifrovací klíče nástroje](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Pokud je úspěšná, zobrazí se příkazový řádek Powershellu.

#### <a name="start-the-synchronization-service"></a>Spustit synchronizační službu
Teď, když synchronizační služba má přístup k šifrování klíče a všechna hesla, které potřebuje, je restartovat službu ve správci řízení služeb Windows:


1. Přejděte do Windows správce řízení služeb (spuštění → služeb).
2. Vyberte **Microsoft Azure AD Sync** a klikněte na tlačítko Restartovat.

## <a name="next-steps"></a>Další postup
**Témata s přehledem**

* [Synchronizace Azure AD Connect: Pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)

* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
