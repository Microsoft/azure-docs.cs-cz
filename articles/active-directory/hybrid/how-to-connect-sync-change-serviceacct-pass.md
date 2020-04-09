---
title: 'Synchronizace služby Azure AD Connect: Změna účtu služby ADSync | Dokumenty společnosti Microsoft'
description: Tento dokument popisuje šifrovací klíč a jak jej po změně hesla opustit.
services: active-directory
keywords: Účet synchronizační služby Azure AD, heslo
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
ms.date: 05/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b2a0d0b77b6db481b13785907a1359d2bbe3e9b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984500"
---
# <a name="changing-the-adsync-service-account-password"></a>Změna hesla účtu služby ADSync
Pokud změníte heslo účtu služby ADSync, synchronizační služba nebude moci správně spustit, dokud neopustíte šifrovací klíč a znovu neinicializujete heslo účtu služby ADSync. 

Azure AD Connect, jako součást synchronizační služby používá šifrovací klíč k ukládání hesel účtu AD DS Connector a účtu služby ADSync.  Tyto účty jsou před uložením v databázi zašifrovány. 

Použitý šifrovací klíč je zabezpečen pomocí [ochrany dat systému Windows (DPAPI).](https://msdn.microsoft.com/library/ms995355.aspx) Soubor DPAPI chrání šifrovací klíč pomocí **účtu služby ADSync**. 

Pokud potřebujete změnit heslo účtu služby, můžete k tomu použít postupy v [části Opuštění šifrovacího klíče účtu služby ADSync.](#abandoning-the-adsync-service-account-encryption-key)  Tyto postupy by měly být také použity, pokud potřebujete z nějakého důvodu opustit šifrovací klíč.

## <a name="issues-that-arise-from-changing-the-password"></a>Problémy, které vznikají změnou hesla
Existují dvě věci, které je třeba udělat při změně hesla účtu služby.

Nejprve je třeba změnit heslo ve Správci řízení služeb systému Windows.  Dokud nebude tento problém vyřešen, zobrazí se následující chyby:


- Pokud se pokusíte spustit službu synchronizace ve Správci řízení služeb systému Windows, zobrazí se chyba "**Systém Windows nemohl spustit službu Synchronizace Microsoft Azure AD v místním počítači**". **Chyba 1069: Služba nebyla zahájena z důvodu selhání přihlášení.**"
- V prohlížeči událostí systému Windows obsahuje protokol systémových událostí chybu s **ID události 7038** a zpráva "**Služba ADSync se nemohla přihlásit jako u aktuálně nakonfigurovaného hesla z důvodu následující chyby: Uživatelské jméno nebo heslo je nesprávné.**"

Za druhé, za určitých podmínek, pokud je heslo aktualizováno, synchronizační služba již nemůže načíst šifrovací klíč prostřednictvím dpapi. Bez šifrovacího klíče nemůže služba synchronizace dešifrovat hesla potřebná k synchronizaci do nebo z místníslužby AD a Azure AD.
Zobrazí se chyby, jako jsou:

- Pokud se v části Správce řízení služeb systému Windows pokusíte spustit službu synchronizace a nelze ji načíst šifrovací klíč, selže s chybou "<strong>Systém Windows nemohl spustit synchronizaci Microsoft Azure AD v místním počítači. Další informace naleznete v protokolu systémových událostí. Pokud se jedná o službu jiného výrobce, obraťte se na dodavatele služby a vyhledejte kód chyby specifický pro službu -21451857952</strong>."
- V prohlížeči událostí systému Windows obsahuje protokol událostí aplikace chybu s **ID události 6028** a chybovou zprávu *"K šifrovacímu klíči serveru nelze získat přístup."*

Chcete-li zajistit, aby se nezoašily tyto chyby, postupujte při změně hesla podle pokynů [v části Opuštění šifrovacího klíče účtu služby ADSync.](#abandoning-the-adsync-service-account-encryption-key)
 
## <a name="abandoning-the-adsync-service-account-encryption-key"></a>Opuštění šifrovacího klíče účtu služby ADSync
>[!IMPORTANT]
>Následující postupy platí jenom pro Azure AD Connect sestavení 1.1.443.0 nebo starší.

Pomocí následujících postupů můžete šifrovací klíč opustit.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Co dělat, pokud potřebujete šifrovací klíč opustit

Pokud potřebujete opustit šifrovací klíč, použijte následující postupy k dosažení tohoto cíle.

1. [Zastavení synchronizační služby](#stop-the-synchronization-service)

1. [Opuštění existujícího šifrovacího klíče](#abandon-the-existing-encryption-key)

2. [Zadání hesla účtu konektoru ad DS](#provide-the-password-of-the-ad-ds-connector-account)

3. [Opětovné inicializaci hesla účtu služby ADSync](#reinitialize-the-password-of-the-adsync-service-account)

4. [Spuštění služby synchronizace](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>Zastavení synchronizační služby
Nejprve můžete službu zastavit ve Správci řízení služeb systému Windows.  Ujistěte se, že služba není spuštěna při pokusu o zastavení.  Pokud ano, počkejte, až se dokončí, a pak ji zastavte.


1. Přejděte do správce řízení služeb systému Windows (START → Služby).
2. Vyberte **Microsoft Azure AD Sync** a klikněte na Zastavit.

#### <a name="abandon-the-existing-encryption-key"></a>Opuštění existujícího šifrovacího klíče
Opusťte existující šifrovací klíč, aby bylo možné vytvořit nový šifrovací klíč:

1. Přihlaste se k serveru Azure AD Connect Jako správce.

2. Spusťte novou relaci PowerShellu.

3. Přejděte do složky:`'$env:ProgramFiles\Microsoft Azure AD Sync\bin\'`

4. Spusťte příkaz:`./miiskmu.exe /a`

![Nástroj pro šifrovací klíč synchronizace Azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-connector-account"></a>Zadání hesla účtu konektoru ad DS
Vzhledem k tomu, že existující hesla uložená v databázi již nelze dešifrovat, je třeba službě synchronizace poskytnout heslo účtu konektoru služby AD DS. Služba synchronizace šifruje hesla pomocí nového šifrovacího klíče:

1. Spusťte Správce synchronizačních služeb (START → Služba synchronizace).
</br>![Správce synchronizačních služeb](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Přejděte na kartu **Konektory.**
3. Vyberte **konektor ad,** který odpovídá vaší místní službě AD. Pokud máte více než jednu spojnici AD, opakujte pro každý z nich následující postup.
4. V části **Akce**vyberte **Vlastnosti**.
5. V rozbalovacím dialogovém okně vyberte **Připojit k doménové struktuře služby Active Directory**:
6. Do textového pole **Heslo** zadejte heslo účtu ad ds. Pokud neznáte jeho heslo, je nutné nastavit na známou hodnotu před provedením tohoto kroku.
7. Klepnutím na **tlačítko OK** uložte nové heslo a zavřete rozbalovací dialogové okno.
![Nástroj pro šifrovací klíč synchronizace Azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-adsync-service-account"></a>Opětovné inicializaci hesla účtu služby ADSync
Nelze přímo zadat heslo účtu služby Azure AD do služby synchronizace. Místo toho je nutné použít rutinu **Add-ADSyncAADServiceAccount** znovu inicializovat účet služby Azure AD. Rutina obnoví heslo účtu a zpřístupní ho synchronizační službě:

1. Spusťte novou relaci PowerShellu na serveru Azure AD Connect.
2. Spustit rutinu `Add-ADSyncAADServiceAccount`.
3. V dialogovém okně pop-up zadejte přihlašovací údaje správce Azure AD Global pro vašeho klienta Azure AD.
![Nástroj pro šifrovací klíč synchronizace Azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Pokud je úspěšná, zobrazí se příkazový řádek prostředí PowerShell.

#### <a name="start-the-synchronization-service"></a>Spuštění služby synchronizace
Nyní, když má služba synchronizace přístup k šifrovacímu klíči a všem heslům, která potřebuje, můžete službu restartovat ve Správci řízení služeb systému Windows:


1. Přejděte do správce řízení služeb systému Windows (START → Služby).
2. Vyberte **Microsoft Azure AD Sync** a klikněte na Restartovat.

## <a name="next-steps"></a>Další kroky
**Přehledná témata**

* [Synchronizace služby Azure AD Connect: Principy a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)

* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
