---
title: 'Azure AD Connect synchronizace: Změna účtu služby ADSync | Microsoft Docs'
description: V tomto tématu najdete popis šifrovacího klíče a jeho opuštění po změně hesla.
services: active-directory
keywords: Účet služby Azure AD Sync, heslo
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4dcc7ed6076c3bac723d709f50f1b3ab2ce8f58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996555"
---
# <a name="changing-the-adsync-service-account-password"></a>Změna hesla účtu služby ADSync
Změníte-li heslo účtu služby ADSync, synchronizační služba nebude moci správně spustit, dokud neodstraníte šifrovací klíč a znovu znovu spustíte heslo účtu služby ADSync. 

Azure AD Connect jako součást synchronizačních služeb používá šifrovací klíč k ukládání hesel účtu služby služba AD DS Connector a účtu služby ADSync.  Tyto účty se šifrují předtím, než se uloží do databáze. 

Použitý šifrovací klíč je zabezpečený pomocí nástroje [Windows Data Protection (DPAPI)](/previous-versions/ms995355(v=msdn.10)). DPAPI chrání šifrovací klíč pomocí **účtu služby AdSync**. 

Pokud potřebujete změnit heslo účtu služby, můžete k tomu použít postupy [přenechání šifrovacího klíče účtu služby AdSync](#abandoning-the-adsync-service-account-encryption-key) .  Tyto postupy by se měly používat i v případě, že z nějakého důvodu potřebujete opustit šifrovací klíč.

## <a name="issues-that-arise-from-changing-the-password"></a>Problémy, které vznikají při změně hesla
Při změně hesla účtu služby je třeba provést dvě věci.

Nejprve je třeba změnit heslo pod správcem řízení služeb systému Windows.  Dokud se tento problém nevyřeší, zobrazí se následující chyby:


- Pokud se pokusíte spustit synchronizační službu ve Správci řízení služeb systému Windows, zobrazí se chyba "**Windows nemůže spustit službu Microsoft Azure AD Sync v místním počítači**". **Chyba 1069: služba nebyla spuštěna z důvodu neúspěšného přihlášení.**"
- V části Windows Prohlížeč událostí protokol událostí systému obsahuje chybu s **ID události 7038** a zprávou "**Služba AdSync se nemohla přihlásit jako s aktuálně nakonfigurovaným heslem z důvodu následující chyby: uživatelské jméno nebo heslo není správné.**"

Za druhé za určitých podmínek platí, že pokud se heslo aktualizuje, synchronizační služba už nebude moct získat šifrovací klíč přes DPAPI. Bez šifrovacího klíče nemůže synchronizační služba dešifrovat hesla potřebná k synchronizaci do/z místních služeb AD a Azure AD.
Zobrazí se chyby, jako například:

- V části Správce řízení služeb systému Windows, pokud se pokusíte spustit synchronizační službu a nemůže získat šifrovací klíč, dojde k chybě "<strong>Windows nemůže spustit synchronizaci Microsoft Azure AD v místním počítači. Další informace najdete v protokolu událostí systému. Pokud se jedná o službu, která není od Microsoftu, obraťte se na dodavatele služby a podívejte se na kód chyby specifický pro službu – 21451857952</strong>.
- V části Windows Prohlížeč událostí obsahuje protokol událostí aplikace chybu s **ID události 6028** a chybovou zprávou *"přístup k šifrovacímu klíči serveru nelze provést."*

Chcete-li zajistit, že tyto chyby neobdržíte, postupujte podle pokynů v části [opuštění šifrovacího klíče účtu služby AdSync](#abandoning-the-adsync-service-account-encryption-key) při změně hesla.
 
## <a name="abandoning-the-adsync-service-account-encryption-key"></a>Přenechání šifrovacího klíče účtu služby ADSync
>[!IMPORTANT]
>Následující postupy platí jenom pro Azure AD Connect Build verze 1.1.443.0 nebo starší. Tato funkce se nedá použít pro novější verze Azure AD Connect, protože přenechání šifrovacího klíče se při změně hesla účtu služby AD Sync zpracovává sám o sobě, takže v novějších verzích se nevyžadují následující kroky.   

K opuštění šifrovacího klíče použijte následující postupy.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Jak postupovat, pokud potřebujete opustit šifrovací klíč

Pokud potřebujete opustit šifrovací klíč, použijte k tomu následující postupy.

1. [Zastavení synchronizační služby](#stop-the-synchronization-service)

1. [Opustit existující šifrovací klíč](#abandon-the-existing-encryption-key)

2. [Zadejte heslo účtu konektoru služba AD DS.](#provide-the-password-of-the-ad-ds-connector-account)

3. [Znovu inicializovat heslo účtu služby ADSync](#reinitialize-the-password-of-the-adsync-service-account)

4. [Spuštění synchronizační služby](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>Zastavení synchronizační služby
Nejdřív můžete zastavit službu ve Správci řízení služeb systému Windows.  Ujistěte se, že při pokusu o zastavení není služba spuštěná.  Pokud je, počkejte, než se dokončí, a pak ho zastavte.


1. Přejít na správce řízení služeb systému Windows (START → Services).
2. Vyberte **Microsoft Azure AD synchronizovat** a klikněte na zastavit.

#### <a name="abandon-the-existing-encryption-key"></a>Opustit existující šifrovací klíč
Stávající šifrovací klíč přenecháte, aby bylo možné vytvořit nový šifrovací klíč:

1. Přihlaste se k serveru Azure AD Connect jako správce.

2. Spusťte novou relaci PowerShellu.

3. Přejít do složky: `'$env:ProgramFiles\Microsoft Azure AD Sync\bin\'`

4. Spusťte příkaz: `./miiskmu.exe /a`

![Snímek obrazovky, který po spuštění příkazu zobrazuje PowerShell](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-connector-account"></a>Zadejte heslo účtu konektoru služba AD DS.
Vzhledem k tomu, že stávající hesla uložená v databázi již nelze dešifrovat, je nutné zadat synchronizační službu s heslem účtu konektoru služba AD DS. Synchronizační služba šifruje hesla pomocí nového šifrovacího klíče:

1. Spusťte Synchronization Service Manager (spusťte synchronizační službu →).
</br>![Synchronizovat Service Manager](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Přejít na kartu **konektory** .
3. Vyberte **konektor služby Active** Directory, který odpovídá vaší místní službě AD. Pokud máte více než jeden konektor služby AD, opakujte následující postup pro každý z nich.
4. V části **Akce** vyberte **vlastnosti**.
5. V místním dialogovém okně vyberte **připojit k doménové struktuře služby Active Directory**:
6. Do textového pole **heslo** zadejte heslo účtu služba AD DS. Pokud heslo neznáte, musíte ho před provedením tohoto kroku nastavit na známou hodnotu.
7. Kliknutím na tlačítko **OK** uložte nové heslo a zavřete automaticky otevírané okno.
![Snímek obrazovky zobrazující stránku připojit k doménové struktuře služby Active Directory v okně Vlastnosti.](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-adsync-service-account"></a>Znovu inicializovat heslo účtu služby ADSync
Do synchronizační služby nemůžete přímo zadat heslo k účtu služby Azure AD. Místo toho je třeba použít rutinu **Add-ADSyncAADServiceAccount** k opětovné inicializaci účtu služby Azure AD. Rutina obnoví heslo účtu a zpřístupní ho službě synchronizace:

1. Spusťte novou relaci PowerShellu na Azure AD Connectovém serveru.
2. Spusťte rutinu `Add-ADSyncAADServiceAccount` .
3. V místním dialogovém okně zadejte přihlašovací údaje globálního správce Azure AD pro vašeho tenanta Azure AD.
![Nástroj Azure AD Connect Sync šifrovací klíč](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Pokud je to úspěšné, zobrazí se příkazový řádek PowerShellu.

#### <a name="start-the-synchronization-service"></a>Spuštění synchronizační služby
Teď, když synchronizační služba má přístup k šifrovacímu klíči a všem potřebným heslům, můžete službu restartovat ve Správci řízení služeb systému Windows:


1. Přejít na správce řízení služeb systému Windows (START → Services).
2. Vyberte **Microsoft Azure AD synchronizovat** a klikněte na restartovat.

## <a name="next-steps"></a>Další kroky
**Témata s přehledem**

* [Azure AD Connect synchronizace: pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)

* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
