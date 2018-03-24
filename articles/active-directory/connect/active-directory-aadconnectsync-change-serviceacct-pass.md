---
title: 'Synchronizace Azure AD Connect: Změna účtu služby Azure AD Connect Sync | Microsoft Docs'
description: Tento dokument téma popisuje šifrovací klíč a jak ho ukončil po změně hesla.
services: active-directory
keywords: Účtu synchronizační služby Azure AD, heslo
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: faa6d667b514563516c23e0484437ac9572e52b7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="changing-the-azure-ad-connect-sync-service-account-password"></a>Změna hesla účtu služby synchronizace Azure AD Connect
Pokud změníte heslo účtu služby synchronizace Azure AD Connect, synchronizační služby nebude možné spustit správně opuštění šifrovací klíč a heslo účtu služby Azure AD Connect sync znovu inicializován. 

Azure AD Connect: jako součást synchronizační služby používá šifrovací klíč k uložení hesla účtů služby AD DS a Azure AD.  Tyto účty jsou zašifrované, než jsou uloženy v databázi. 

Šifrovací klíč použít zabezpečené pomocí [Windows Data Protection (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx). Rozhraní DPAPI chrání klíč šifrování pomocí **heslo účtu služby Azure AD Connect sync**. 

Pokud potřebujete změnit heslo účtu služby můžete použít postupy v [zrušení šifrovací klíč Azure AD Connect Sync](#abandoning-the-azure-ad-connect-sync-encryption-key) toho chcete dosáhnout.  Tyto postupy by měla být používána Pokud potřebujete abandon šifrovacího klíče z jakéhokoli důvodu.

## <a name="issues-that-arise-from-changing-the-password"></a>Problémy, které vznikají z Změna hesla
Existují dvě věci, které je třeba provést, pokud změníte heslo účtu služby.

Je třeba nejprve, chcete-li změnit heslo v části správce řízení služeb systému Windows.  Dokud nebude tento problém vyřešen. zobrazí se následující chyby:


- Pokud se pokusíte spustit synchronizační službu v modulu snap-in Správce řízení služeb systému Windows, se zobrazí chyba "**Windows v místním počítači nelze spustit službu Microsoft Azure AD Sync**". **Chyba. 1069: Službu se nepodařilo spustit z důvodu selhání přihlášení.** "
- V prohlížeči událostí systému Windows v protokolu událostí systému obsahuje chybu s **7038 ID události** a zpráva "**ADSync service nebylo možné se přihlásit jako se se současně nakonfigurovaným heslem z důvodu následující chyby: uživatel jméno nebo heslo není správné.** "

Druhý pro určité podmínky, pokud je aktualizovat heslo, služba synchronizace už načíst šifrovací klíč pomocí rozhraní DPAPI. Bez šifrovací klíč nemůže dešifrovat služba synchronizace hesel vyžadovaných k synchronizaci z místní AD a Azure AD.
Zobrazí se chyby, jako:

- V části správce řízení služeb systému Windows Pokud pokusu o spuštění synchronizační službu a nemůže získat šifrovací klíč, se nezdaří s chybou "** Windows se nepodařilo spustit Microsoft Azure AD Sync v místním počítači. Další informace vyhledejte v protokolu událostí systému. Pokud je služba jiného výrobce než Microsoftu, obraťte se na dodavatele služby a pročtěte si kód chyby-**-21451857952 ***. "
- V prohlížeči událostí systému Windows v protokolu událostí aplikace obsahuje chybu s **6028 ID události** a chybovou zprávou *"**šifrovacího klíče serveru nelze získat přístup.**"*

Aby se tyto chyby neobdrží, postupujte podle pokynů v [zrušení šifrovací klíč Azure AD Connect Sync](#abandoning-the-azure-ad-connect-sync-encryption-key) při změně hesla.
 
## <a name="abandoning-the-azure-ad-connect-sync-encryption-key"></a>Zrušení šifrovací klíč Azure AD Connect Sync
>[!IMPORTANT]
>Následující postupy lze použít pouze na Azure AD Connect sestavení 1.1.443.0 nebo starší.

Pomocí následujících postupů ukončil šifrovací klíč.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Co dělat, když potřebujete abandon šifrovací klíč

Pokud potřebujete abandon šifrovací klíč, použijte k tomu následující postupy.

1. [Zrušte existující šifrovací klíč](#abandon-the-existing-encryption-key)

2. [Zadejte heslo účtu služby AD DS](#provide-the-password-of-the-ad-ds-account)

3. [Znovu inicializovat heslo účtu služby Azure AD sync](#reinitialize-the-password-of-the-azure-ad-sync-account)

4. [Spusťte službu synchronizace](#start-the-synchronization-service)

#### <a name="abandon-the-existing-encryption-key"></a>Zrušte existující šifrovací klíč
Zrušte existující šifrovací klíč, že nový šifrovací klíč lze vytvořit:

1. Přihlaste se k Azure AD Connect serveru jako správce.

2. Spusťte novou relaci prostředí PowerShell.

3. Přejděte do složky: `$env:Program Files\Microsoft Azure AD Sync\bin\`

4. Spusťte příkaz: `./miiskmu.exe /a`

![Azure AD Connect Sync šifrovací klíče nástroje](media/active-directory-aadconnectsync-encryption-key/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-account"></a>Zadejte heslo účtu služby AD DS
Jak existující hesel uložených v databázi již nebude možné dešifrovat, musíte poskytnout synchronizační služby heslo účtu služby AD DS. Služba synchronizace šifruje pomocí nového klíče šifrování hesla:

1. Spusťte Synchronization Service Manager (Služba synchronizace → START).
</br>![Správce synchronizace služby](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)  
2. Přejděte na **konektory** kartě.
3. Vyberte **AD Connector.** odpovídající místní AD. Pokud máte více než jeden konektor AD, opakujte tyto kroky pro každý z nich.
4. V části **akce**, vyberte **vlastnosti**.
5. V dialogovém okně automaticky otevírané okno Vyberte **připojit k doménové struktuře služby Active Directory**:
6. Zadejte heslo účtu služby AD DS v **heslo** textové pole. Pokud neznáte jeho heslo, musí ho nastavit na hodnotu známé před provedením tohoto kroku.
7. Klikněte na tlačítko **OK** nové heslo uložte a zavřete dialogové okno automaticky otevírané okno.
![Azure AD Connect Sync šifrovací klíče nástroje](media/active-directory-aadconnectsync-encryption-key/key6.png)

#### <a name="reinitialize-the-password-of-the-azure-ad-sync-account"></a>Znovu inicializovat heslo účtu služby Azure AD sync
Pro synchronizační službu nelze přímo zadejte heslo účtu služby Azure AD. Místo toho musíte použít rutinu **přidat ADSyncAADServiceAccount** znovu inicializovat účtu služby Azure AD. Rutina resetuje heslo k účtu a je k dispozici služba synchronizace:

1. Spusťte novou relaci prostředí PowerShell na server Azure AD Connect.
2. Spusťte rutinu `Add-ADSyncAADServiceAccount`.
3. V dialogovém okně automaticky otevírané okno Zadejte přihlašovací údaje Azure AD globálního správce pro vašeho tenanta Azure AD.
![Azure AD Connect Sync šifrovací klíče nástroje](media/active-directory-aadconnectsync-encryption-key/key7.png)
4. Pokud neproběhne úspěšně, zobrazí se příkazový řádek Powershellu.

#### <a name="start-the-synchronization-service"></a>Spusťte službu synchronizace
Teď, když služba synchronizace má přístup k šifrovací klíč a všechna hesla, které potřebuje, můžete službu restartovat správce řízení služeb systému Windows:


1. Přejděte do Windows správce řízení služeb (počáteční → služeb).
2. Vyberte **Microsoft Azure AD Sync** a klikněte na tlačítko Restartovat.

## <a name="next-steps"></a>Další postup
**Témata s přehledem**

* [Synchronizace Azure AD Connect: pochopení a přizpůsobení synchronizace](active-directory-aadconnectsync-whatis.md)

* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)
